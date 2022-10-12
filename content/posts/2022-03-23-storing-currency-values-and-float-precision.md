---
template: post
title: Storing Currency Values and Float Precision
slug: Preserve precision with Numeric values
socialImage: /media/michal-matlon-4apmfdvo32q-unsplash.jpg
draft: false
date: 2022-03-23T15:50:10.187Z
description: When I was a junior developer, I was looking through a database
  schema and noticed that currency values are stored in Integers and that each
  value is multiplied by 100. This was an intriguing approach, and I began to
  look into it. It was fascinating back then because I thought we could use a
  type like a Float to store this without having to use multiplication or
  division hackle. This article will explain why we avoid Float and Double in
  favor of cleaner solutions. This is an important strategy to consider. So,
  let's get started.
category: Software Engineering
tags:
  - SoftwareEngineering
  - Java
  - MySQL
  - Database
---
Let me iterate the content before we begin. The main goal of this article is to discuss how currency values can be stored in databases (MySQL). My secondary goal is to explain the inaccuracy with float values. Now that we've clarified the scope, let's get started
The requirement is straightforward. In our database, we must store currency values or numeric values where precision is critical. The approaches or solutions provided below use MySQL, and the coding is done in Java, but I hope the fundamentals are clear to you. Another quick note: Some relational databases, such as Postgres, support a Money type, but we'll stick to MySQL to keep things simple.
Let's look at the different data types that can be used to store currency values:

1. [Float / Double](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html)
2. [Decimal / Numeric](https://dev.mysql.com/doc/refman/8.0/en/fixed-point-types.html)
3. [Bigint / Integer](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html)
4. [Varchar / String ](https://dev.mysql.com/doc/refman/8.0/en/char.html)

Okay, we've compiled a list of our top solutions. To be clear, I did not list them by data type. As you can see, Float and Double are two distinct data types (Double stores double-precision floating-point number values), but what they bring to the table under this topic is fairly straightforward. Now comes the exciting part.

## Float / Double

If precision is a requirement, this may be a no; why maybe?  let me explain. It is advised to avoid using this data type to save currency values. Why are float and double not as precise as you might think?
Let's take a look at this small code snippet.

```java
class HelloWorld {
    public static void main(String\[] args) {
        double total = 0.2;
        System.out.println("Initial Value : " + total);
        for (int i = 0; i < 10; i++) {
            total += 0.2;
        }
        System.out.println("Total Value : " + total);
    }
}
```

We increment out the initial value ten times with addition, and we get an output like the following:

```java
Initial Value : 0.2
Total Value : 2.1999999999999997
```

We expected 2 but got 2.1999999999999997 . Now we have an issue in our hands; if we chop the value, we get 2.1 or if we rounded it off, we get 2.2 Either way, we have a close to 0.1 loss of precision (or loss of significance)
Okay, why does that happen? Before I point you down to the solution, something you need to know is
Floats were according to[ IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) always binary

The term "float" refers to the decimal point 'floats'. For instance, the following are all different exponents with the same whole number:

1. 10.25 is 1025 x 10<sup>(-2)</sup>
2. 0.15 is 15 x 10<sup>(-2)</sup>

But I said floats were binary, right? Yes, computers think in binary. So it's something like

1. 10.25 is 164 x 2<sup>(-4)</sup> which is 10.25
2. 0.15 is 168884986026394 x 2<sup>(-50)</sup> which is close to 0.15

Computers use binary numbers because they are faster at dealing with them and because a small error can usually be ignored in most calculations. Another important point to note is that it is not due to binary. For example, can we accurately represent a number like (1/3) in Base 10? You have to round to something like 0.33, and you don't expect 0.33 + 0.33 + 0.33 to equal 1.

Okay, now for the explanation. I'm just going to copy-paste the answer, which perfectly explains everything.

Extracted from [Wikipedia](https://en.wikipedia.org/wiki/Floating-point_arithmetic#Representable_numbers.2C_conversion_and_rounding):

> Whether or not a rational number has a terminating expansion depends on the base. For example, in base-10 the number 1/2 has a terminating expansion (0.5) while the number 1/3 does not (0.333…). In base-2 only rationals with denominators that are powers of 2 (such as 1/2 or 3/16) are terminating. Any rational with a denominator that has a prime factor other than 2 will have an infinite binary expansion. This means that numbers that appear to be short and exact when written in decimal format may need to be approximated when converted to binary floating-point. For example, the decimal number 0.1 is not representable in binary floating-point of any finite precision; the exact binary representation would have a "1100" sequence continuing endlessly:
>
>  e = −4; s = 1100110011001100110011001100110011…,
>
>  where, as previously, s is the significand and e is the exponent. When rounded to 24 bits this becomes
>
>  e = −4; s = 110011001100110011001101,
>
>  which is actually 0.100000001490116119384765625 in decimal.

   Float uses 24-bit for its "mantissa", which holds all the significant digits. This means it has about seven digits of precision (as 2^(24) is about 16 million), and Double uses 53-bit for its "mantissa", so it can hold about 16 digits accurately.

To summarise, the result of a floating-point calculation is frequently rounded in order to fit back into its finite representation. This rounding error is a defining characteristic of floating-point computation. Assume you want to perform a certain level of complex multiplication,  This will have an impact on your calculation flow and final values if not addressed (this is not limited to multiplication; other arithmetic operations will have the same effect, although on a smaller scale for obvious reasons).

## Decimal / Numeric

This is one of the better ways to save currency values without incurring any losses. It's not difficult to understand. Decimal(15,2) 15 is the precision (total length of value including decimal places), and 2 is the number of digits after the decimal point; of course, length and precision can be defined to meet your needs. Assume your application must handle money values up to a trillion dollars. In that case, the following should work: 13,2 and If you must adhere to GAAP (Generally Accepted Accounting Principles), use 4 for precision, such as 13,4.

## BigInt / Integer

Another method is to store it as an integer. The only takeaway is that you must perform a calculation. Why? Because there are no decimal places, you must store the values by multiplying by 100 or 1000, depending on the level of precision desired. Integer (INT) has a signed range of -2147483648 to 2147483647 and an unsigned range of 0 to 4294967295. In the column definition, you can specify whether the int is signed or unsigned. The signed range for Bigint is -9223372036854775808 to 9223372036854775807, and the unsigned range is positive. Unsigned has a value range of 0 to 18446744073709551615. More information is available in the MySQL Documentation. This is sufficient for general-purpose business applications to store currency values, but it imposes an additional burden when dealing with fractional values. Not recommended, but it depends on your needs and feasibility, which applies to all of the solutions I've listed here.

## VARCHAR

I'm just going to add this to the list for the sake of completeness: You can use VARCHAR to store exact representations, but one important takeaway aside from the obvious is that it takes more bytes to store a number as a string. And any arithmetic on the value will always convert it to a number.

Okay, we talked about the float precision issue. We talked about the various currency data types. The goal has been met. Everyone have a wonderful day.

### References

1. IEEE 754 — Wikipedia, <https://en.wikipedia.org/wiki/IEEE_754>
2. Floating-point arithmetic — Wikipedia, <https://en.wikipedia.org/wiki/Floating-point_arithmetic#Representable_numbers.2C_conversion_and_rounding>
3. DECIMAL Data Type Characteristics — MySQL, <https://dev.mysql.com/doc/refman/8.0/en/precision-math-decimal-characteristics.html>