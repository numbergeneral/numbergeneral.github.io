---
title: "Daily Coding Problem #04"
date: 2019-03-01
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #04"
mathjax: "true"
---

## The problem

> Given an array of integers, find the first missing positive integer in linear time and constant space. In other words, find the lowest positive integer that does not exist in the array. The array can contain duplicates and negative numbers as well.
>For example, the input [3, 4, -1, 1] should give 2. The input [1, 2, 0] should give 3.
>You can modify the input array in-place. 
{: .text-justify}

## The definition of the problem

First let's define the in-place algorithm. It is one that operates directly on its input and changes it, instead of creating and returning a new object. It does mean that it does not create the new copy of the input. I will bend this rule slightly in the problem definition, but the implementation will have only the in-place input modification.   
{: .text-justify}

We have an integer array $$L$$ of length $$n$$ such that $$L = \{l_1, l_2, \dots , l_n\}$$. We need to find the first missing positive integer in this array. Since we are interested only in positive numbers we may create an ordered array $$L' = \{a \in \mathbb{N}: a > 0 \wedge a \in L\ \wedge a_{i} > a_{j} \text{ for } i > j\}$$ of length $$m$$. If the resulting array $$L'$$ is empty we need to return $$1$$ since [zero is neither positive nor negative](https://en.wikipedia.org/wiki/0). In other case we end up with an array of natural numbers $$L'$$ of length $$m<n$$. Then we can define a function from $$L'$$ to $$\mathbb{N}$$ as 
{: .text-justify}

$$ 
f = \begin{cases}
  a_{i}+1, & \text{if } a_{i+1} - a_{i} \neq 1, i \in \{1, \dots, m-1\}\\
  a_{m}+1, & \text{otherwise} .
\end{cases}
$$

Finally to get the lowest natural number not in $$L'$$ would be $$\operatorname{min}(f(L'))$$.
{: .text-justify}

Before the actual implementation let's go through the example. Let's take the array given in the problem definition, thus $$L=\{3, 4, -1, 1\}$$. Then we proceed by creating array $$L'=\{1, 3, 4\}$$. Since the array is not empty we will apply the function $$f$$ to it $$f(L') = \{2, 5, 5\}$$. The solution to the problem is the minimum of the resulting array.
{: .text-justify}

## The solution
### Python
The Python solution will follow the logic presented above, but I will apply some optimization to the algorithm. I'll start with ordering the input list. Then in order to speed up execution I will check if the last element of the list is lesser or equal to zero. If that is the case the program will return 1. Otherwise it will loop the list and apply function defined above as $$f$$. To speed up execution again if the difference of two neighboring elements is different than one the script returns the value of lesser element incremented by one. If the loop will come to an end the maximum value from the list incremented by one is returned. 
{: .text-justify}

```python
def find_first_missing_integer(input_list):
    input_list.sort()
    # If the last element of the sorted list is lesser
    # or equal to zero return 1 
    if input_list[-1] <= 0:
        return 1
    for i, val in enumerate(input_list[:-1]):
        if val >= 0:
            if val - input_list[i+1] != -1:
                return val + 1
    return input_list[-1] + 1
```

With the allowed array in-place modification the implementation after sorting is $$O(n)$$ in time complexity and $$O(1)$$ in space complexity.
{: .text-justify}

### C++

The C++ solution is similar to the presented Python solution. There are no significant implementation differences.
{: .text-justify}

```cpp
#include <vector>
#include <algorithm>

using namespace std;

int find_first_missing_integer(vector<int> input_vector);

int find_first_missing_integer(vector<int> input_vector)
{
    sort(input_vector.begin(), input_vector.end());
    int vector_length = input_vector.size()-1;
    
    if(input_vector[vector_length] <= 0)
    {
        return 1;
    }
    for(int i = 0; i < vector_length; i++)
    {
        if (input_vector[i] >= 0 &&
            input_vector[i+1] - input_vector[i] != 1)
            {
                return input_vector[i] + 1;
            }
    }
    return input_vector[vector_length] + 1;
}
```

