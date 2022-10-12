---
template: post
title: Immutability in Elixir
slug: Data immutability in Elixir Programming
socialImage: /media/download.jpg
draft: false
date: 2022-10-09T13:20:29.408Z
description: Objects are not supported in Elixir because it is a functional
  language. Furthermore, the concept of immutability is heavily emphasized
  within the Elixir language. Instead of altering data, transformations are used
  in Elixir.
category: Software Engineering
tags:
  - Elixir
---
Why is data immutable? What are we attempting to solve by making data immutable? It enables n processes to safely access the same data at the same time. It makes concurrency easy to handle because no process can change the original data. Any transformation of the original data will result in the creation of new data.

Let's have a look at an example.

```
var mutableArray = ["Element 01", "Element 02", "Element 03", "Element 04", "Element 05"];

console.log("Initial array : " + mutableArray)
mutableArray.shift()
console.log("Array after shift action : " + mutableArray)
```

 output:

```
"Initial array : Element 01,Element 02,Element 03,Element 04,Element 05"
"Array after shift action : Element 02,Element 03,Element 04,Element 05"
```

You do not need to grasp Javascript syntax. It is more important that you grasp the distinctions between mutable and immutable languages than the specifics of this example.

We're assigning 5 strings to the variable mutableArray above. Then we execute the mutableArray's shift method, which retrieves the first value in the array while also removing that string from the array. The array now has only four elements.

Now let's look at some Elixir code:

```
immutable_array = ["Element 01", "Element 02", "Element 03", "Element 04", "Element 05"]

IO.puts(["Initial array : ", Enum.join(immutable_array, ", ")])  
Enum.slice(immutable_array, 4, 2)
IO.puts(["Array after slice action : ", Enum.join(immutable_array, ", ")])  
```

o﻿utput:

```
Initial array : Element 01, Element 02, Element 03, Element 04, Element 05
Array after slice action : Element 01, Element 02, Element 03, Element 04, Element 05
```

In contrast, immutable data has no side effects, no matter what you call an array with 5 elements, it will always be an array with 5 elements. Simply put, it prevent values in a certain memory area from changing. Never. Until the variable is trash collected or no longer exists. 

If you want to learn more about the topic, Id recommend [Are We There Yet presentation by Rich Hickey](https://youtu.be/ScEPu1cs4l0)

Happy Coding!