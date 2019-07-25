---
title: "Daily Coding Problem #05"
date: 2019-07-25
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #05"
mathjax: "true"
---

## The problem
The [Daily Coding Problem](https://www.dailycodingproblem.com/) #05 is the following:

>cons(a, b) constructs a pair, and car(pair) and cdr(pair) returns the first and last element of that pair. For example, car(cons(3, 4)) returns 3, and cdr(cons(3, 4)) returns 4.
>Given this implementation of cons:
{: .text-justify}

```python
def cons(a, b):
   def pair(f):
       return f(a, b)
   return pair
```

>Implement car and cdr.
{: .text-justify}

## The definition of the problem

This problem does not have a strict mathematical definition. The clue to solving it is to notice what exactly 'cons' returns. The function 'cons' takes two (let's assume) integers and returns function 'pair'. Function 'pair' in turn takes function as an input and returns the output of its input function applied with parameters 'a' and 'b'. So in order to solve this problem we need create a function that will take a function as a parameter and return output of passing a function to input function.    
{: .text-justify}

## The solution
### Python
The Python solution is very short and straightforward. I will use the ready definition of 'cons'. The 'car' function takes the function as an input and returns the output of input function with some other function as parameter. The function we need is a function that out of two arguments will return first. The 'car' and 'cdr' functions share the same logic with only difference of which parameter they return.
{: .text-justify}

```python
def cons(a, b):
   def pair(f):
       return f(a, b)
   return pair

def car(cons):    
    return cons(lambda a, b : a)

def cdr(cons):   
    return cons(lambda a, b : b)
```

### C++
In case of this problem the C++ solution was more intuitive to me, as working with functions pointers is more common in C++. I have changed the 'cons' function to C++ implementation by making 'pair' function a lambda. The part requiring the most focus was getting the input and output types right. The implementation of 'car' and 'cdr' follows the same logic. The 'car' function takes pointer to the 'cons' function as an output and passes the 'get_a' lambda into it. Where 'get_a' is a simple function that returns first of the two given integers.
{: .text-justify}

```cpp
#include <iostream>
#include <vector>
#include <functional>

std::function<int (int(*f)(int, int))> cons(int a, int b)
{
  auto pair = [a, b] (int(*f)(int, int)) -> int
  {
     return (*f)(a, b);
  };
  return pair;
}

int car(std::function<int (int(*f)(int, int))> cons){
  auto get_a = [](int a, int b) -> int
  {
    return a;
  };
  return cons(*get_a);
}

int cdr(std::function<int (int(*f)(int, int))> cons){
  auto get_b = [](int a, int b) -> int
  {
    return b;
  };
  return cons(*get_b);
}

int main() {
  std::function<int (int(*f)(int, int))> cons_v = cons(1, 2);
  int a = car(cons_v);
  int b = cdr(cons_v);
  
  std::cout << "The value of a is: " << a << "\n";
  std::cout << "The value of b is: " << b << "\n";
}
```
The result of the script is following:
{: .text-justify}

```
The value of a is: 1
The value of b is: 2
```

And as always, feel free to leave comments or suggestions below.
{: .text-justify}
