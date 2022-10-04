---
template: post
title: Storing Currency Values and Float Precision
slug: Preserve precision with Numeric values
socialImage: /media/michal-matlon-4apmfdvo32q-unsplash.jpg
draft: false
date: 2022-03-23T15:50:10.187Z
description: When I was a junior developer, I was going through a database
  schema and noticed that currency values are stored in Integers and that each
  value is multiplied by 100. This was an intriguing approach, and I began to
  investigate. It was fascinating back then because I thought we could use a
  type like a Float to store this without having to use multiplication or
  division hackle. This article will explain why we tend to avoid Float and
  Double in favor of cleaner solutions. This is an important approach to think
  about. So, let's get started.
category: Software Engineering
tags:
  - SoftwareEngineering
  - Java
  - MySQL
  - Database
---
Before getting started with the article, let me iterate the content. This article's main objective is to discuss how currency values can be stored in Databases (MySQL). My other main focus is to explain the inaccuracy with float values. Now that we have straightened out the scope let's get started with the much-needed introduction.
Long ago, when I was a junior developer, I was going through a DB schema, and noted currency values are stored in Integers, and every value is multiplied by 100. This was a fascinating approach, and I started digging. It was fascinating back then because I thought we could use something like Float type to store this without multiplication or division hackle. This article is about that; I will tell you why we tend to move away from Float and Double and look into cleaner solutions. This is a fundamental approach to consider. Okay, let's dive into it.
The requirement is pretty straightforward. We need to store currency value in our database or a numeric value where precision matters. The approaches or solutions provided below use MySQL, and coding is made through Java, but I hope the fundamentals will make sense to you nonetheless. One other small note: Some relational databases support a Money type like Postgress, but we will focus only on MySQL to keep this simple.
Let's evaluate the data types we can consider to store currency values:

1. [Float / Double](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html)
2. [Decimal / Numeric](https://dev.mysql.com/doc/refman/8.0/en/fixed-point-types.html)
3. [Bigint / Integer](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html)
4. [Varchar / String ](https://dev.mysql.com/doc/refman/8.0/en/char.html)

Alright, we have our leading solutions listed down. To be clear, I listed them not by a specific data type. As you can see, consider Float and Double, while those are two different data types (Double stores double-precision floating-point number values), which brings to the table under this topic is pretty straightforward. Here comes the exciting stuff.

## Float / Double

If precision is one of your requirements, this may be a no; why the word maybe? Not always; I'll explain that. However, it's recommended to avoid this data type to save currency values. Why float and decimal are not precise, as you might believe?
Let's look into this small code snippet,

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

Computers use binary numbers because they're faster at dealing with those and because for most calculations, a tiny error in most cases can be ignored. One other important thing to be mentioned is it's not due to binary. Base 10 suffers the same for an instant can we accurately represent a number like (1/3)? You have to round to something like 0.33, and still, you don't expect 0.33 + 0.33 + 0.33 to add up to 1, right?

Okay, now to the explanation, I'm just going to copy-paste the answer, which explains the whole thing flawlessly.

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

So let's sum it up. In short, the result of a floating-point calculation must often be rounded to fit back into its finite representation. This rounding error is the characteristic feature of floating-point computation. Suppose you are looking to do a certain level of complex multiplication. This will affect your calculation flow and final values unless taken care of (it's not only in multiplication, other arithmetic operations will have the same effect, but on a lesser scale for obvious reasons). 

## Decimal / Numeric

   This is one of the better ways or solves a general need of saving currency values without any loss. 
   It's pretty straightforward.
   decimal(15,2)
   15 is the precision (total length of value including decimal places), and 2 is the number of digits after the decimal point; of course, you can define length and precision to fit your requirements. Suppose your application needs to handle money values up to a trillion. In that case, this should work: 13,2 and If you need to comply with GAAP (Generally Accepted Accounting Principles), then use 4 for precision like 13,4.

## BigInt / Integer

   Here is another approach, storing as an integer. The only takeaway is you have to do a calculation. Why? Because there are no decimal places, you will have to store the values by multiplying with 100 or 1000, depending on the precision you are aiming for. The signed range of integer (INT) is from -2147483648 to 2147483647, and the unsigned range is from 0 to 4294967295. You can specify signed or unsigned int in the column definition. While for Bigint, the signed range is -9223372036854775808 to 9223372036854775807, and the unsigned range takes a positive value. The range of unsigned is 0 to 18446744073709551615. You can find more details on MySQL Documentation. This will be sufficient for general level business applications to store currency values but take away is imposing an extra burden on handling fractional values. Not recommended, but it depends on your requirements and feasibly, which applies to every solution I have listed here.

## VARCHAR

   I'm just going to throw this out to the list just for the sake of it, You can use VARCHAR to store exact representations, but one significant takeaway apart from the obvious is it takes more bytes to store a number a string. And any arithmetic you do on the value will convert it to a number anyway.

   Alright, we discussed the float precision issue. We discussed the currency data types. The objective is done. Have a great day all.



### References

1. IEEE 754 — Wikipedia, <https://en.wikipedia.org/wiki/IEEE_754>
2. Floating-point arithmetic — Wikipedia, <https://en.wikipedia.org/wiki/Floating-point_arithmetic#Representable_numbers.2C_conversion_and_rounding>
3. DECIMAL Data Type Characteristics — MySQL, <https://dev.mysql.com/doc/refman/8.0/en/precision-math-decimal-characteristics.html>