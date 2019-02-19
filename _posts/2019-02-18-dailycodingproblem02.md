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

The lists $$K_i$$ are created by removing the i-th element from the list $$L$$. Now with problem defined one may proceed to an example. Let's assume $$L=\{3, 2, 1\}$$, then the output we expect would be $$A = \{2 * 1, 3 * 1, 2 * 3\} = \{2, 3, 6\}$$. Now we may proceed to the actual implementation.
{: .text-justify}

The Python code is given below. The function takes the list of integers as the input and initializes the empty list of the same length. Then iterating over the input list elements it creates sub-list with the i-th element excluded and multiplies all of the sub-list elements. The result is placed in the i-th place of the output list. The solution is tested by a couple of cases and each time it gives the desired result.
{: .text-justify}

```python
def calculate_product_of_all_elements_except_ith(input_list):
    output_list = [0] * len(input_list)
    for i in range(0, len(input_list)):
        multiplication_temp = 1
        for j in (input_list[:i] + input_list[i+1 :]):
            multiplication_temp *= j 
        output_list[i] = multiplication_temp
    return output_list

def test_solution(test_dictionary):
    for key, value in test_dictionary.items():
        results = calculate_product_of_all_elements_except_ith(value)
        print("Test name:", key, "\nThe input numbers are:", value,
        "\nThe results are:", results)

if __name__ == "__main__":
    test_dictionary = {"Positive integer test":[1, 2, 3],
                    "Negative integer test":[1, -2, 3],
                    "Same value test": [2, 2, 2],
                    "Zero test": [0, 0, 0]}
    test_solution(test_dictionary)
```

Please feel free to leave comments or suggestions below.