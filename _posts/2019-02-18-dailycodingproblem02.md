---
title: "Daily Coding Problem #02"
date: 2019-02-18
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #02"
mathjax: "true"
---

The Daily Coding Problem #02 is the following:
{: .text-justify}
>Given an array of integers, return a new array such that each element at index i of the new array is the product of all the numbers in the original array except the one at i.
>For example, if our input was [1, 2, 3, 4, 5], the expected output would be [120, 60, 40, 30, 24]. If our input was [3, 2, 1], the expected output would be [2, 3, 6].
>Follow-up: what if you can't use division?
{: .text-justify}

With the division allowed the definition of the problem is quite straightforward. We have a list of integers of length $$n$$ $$L = \{l_1, l_2, ..., l_n\}$$. The output of the program is the list of integers that are products of all elements except for the element at the i-th place. If we define the output list as $$A$$ this can be written as:
{: .text-justify}

$$ A = \{\dfrac{\prod_{i=1}^{n} l_i}{l_1}, \dfrac{\prod_{i=1}^{n} l_i}{l_2}, ... , \dfrac{\prod_{i=1}^{n} l_i}{l_n}\}$$

But since there are extra points for not using the division we may redefine the problem by excluding the i-th element from the original list. Again assuming that out output list is denoted as $$A$$ it can be written down as:
{: .text-justify}

$$ A = \{\prod_{i=1}^{n-1} K_1, \prod_{i=1}^{n-1} K_2, ... , \prod_{i=1}^{n-1} K_n\} $$ 

$$ K_i = \{l_1, l_2, ... , l_{i-1}, l_{i+1}, ... , l_n \} $$

The lists $$K_i$$ are created by removing the i-th element from the list $$L$$. Now with problem defined one may proceed to the implementation. 
{: .text-justify}