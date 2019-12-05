---
title: "Daily Coding Problem #07"
date: 2019-12-05
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #07"
mathjax: "true"
---

## The problem
The [Daily Coding Problem](https://www.dailycodingproblem.com/) #7 is the following:

>Given the mapping a = 1, b = 2, ... z = 26, and an encoded message, count the number of ways it can be decoded.
{: .text-justify}

>For example, the message '111' would give 3, since it could be decoded as 'aaa', 'ka', and 'ak'.
{: .text-justify}

>You can assume that the messages are decodable. For example, '001' is not allowed.
{: .text-justify}

## The definition of the problem

At the first sight this problem looks like one that can be solved by applying simple recursive algorithm. However, a more optimal solution would involve dynamic programming. Let's take a look how to derive a dynamic programming solution form the recursive relationship and then apply it to the problem at hand.
{: .text-justify}

The most recognizable example of the recursive problem would be the Fibonacci sequence. Thus, we shall use it as our example and drive the dynamic programming solution from the recursive definition.
{: .text-justify}

The Fibonacci sequence is defined as $f(x) = f(x-1) + f(x-2)$ where $f(0)=0$ and $f(1)=1$. If we are to return $n^{th}$ element of the Fibonacci sequence recursively the Python code might look as following.
{: .text-justify}

```python
def recursive_fib(n):
    if n == 0:
        return 0
    if n == 1:
        return 1
    return recursive_fib(n-1) + recursive_fib(n-2)
```

While the solution is extremely simple and elegant it has a drawback of poor performance. The algorithm has a time complexity of $O(n^2)$, but memory complexity of just $O(n)$. Thus, it is not feasible in practical applications as it starts to blow up with $n \approx 40$.  
{: .text-justify}

In order to make the recursive approach more universal we will trade off some memory complexity for reduced time complexity. In order not to recompute the sub-problems each time we will store their results. Then we can reference to them when we need the result again. That would be main theme of the dynamic programming concept.
{: .text-justify}

Thus, the solution of getting $n^{th}$ element of the Fibonacci sequence using the dynamic programming can look as following.
{: .text-justify}

```python
def dp_fibonacci(n):
    temp = [0] * (n+1)

    temp[0] = 0
    temp[1] = 1

    for i in range(2, n+1):
        temp[i] = temp[i-1] + temp[i-2]
    return temp[n]
```

The idea is to reserve a part of memory to store the solutions of sub-problems and then loop to fill up the list. This algorithm has a time complexity of $O(n)$ and keeps the memory complexity of $O(n)$. Thus, it is linear both in regards to time and space complexity.
{: .text-justify}

## The solution
### Python
The Python solution is presented below. The main idea is to leverage the concept of dynamic programming. The solution starts with declaration of the list that will hold the number of solutions for each of the sub-problems. We assume that for the message with no characters there is one way to decode it (as an empty message) and a message with one character can be decoded in only one way.
{: .text-justify}

Next, we are looping all of the sub-problems, looking only at the two last characters in the message. If the last digit of the message is different than zero we set the number of ways the message can be decoded the same as the message with one character less. This part of the code leverages the fact that all messages are decodable, i.e. cannot start with 0. Then, if the two last digits are within $(9, 27)$ range, as the max number that can be decoded is 26, we add the number of solutions from the message that had two characters less. Finally the $n^{th}$ int in an array is returned as the result of calculation. 
{: .text-justify}

```python
def decode(message):
    message_len = len(message)

    number_of_solutions = [0] * (message_len + 1)
    number_of_solutions[0] = 1
    number_of_solutions[1] = 1

    for i in range(2, message_len+1):
        last_digit = int(message[i-1])
        two_last_digits = int(message[(i-2):i])
        # If the last digit is greater than zero, 
        # then the number of solutions stays the same
        if (last_digit > 0):
            number_of_solutions[i] = number_of_solutions[i - 1]

        # If the two last digits are in between 9 and 27, 
        # this results in an additional batch of solutions.
        # Thus, a number of the solutions from two characters before is added.
        if ((two_last_digits > 9) & (two_last_digits < 27)):  
            number_of_solutions[i] += number_of_solutions[i - 2]  

    return number_of_solutions[-1]
```

### C++
The C++ solution does not have any significant implementation differences from the one done in Python.
{: .text-justify}

```cpp
#include <iostream>
#include <string>

int decode(std::string message)
{
  const int message_len = message.length();

  int number_of_solutions[message_len];
  number_of_solutions[0] = 1;
  number_of_solutions[1] = 1;

   for(int i = 2; i <= message_len+1 ; i++)
   {
     int last_digit =  std::atoi(message.substr(message_len - 1).c_str());
     int two_last_digits = std::atoi(message.substr(message_len - 2).c_str());

     if (last_digit > 0)
     {
       number_of_solutions[i] = number_of_solutions[i - 1];
     }

     if ((two_last_digits > 9) & (two_last_digits < 27))
     {
      number_of_solutions[i] += number_of_solutions[i - 2];
     }
   }
   return number_of_solutions[message_len];
}

int main() 
{
  std::string test_message = "111";
  int result = decode(test_message);

  std::cout << "The input message was " << test_message << " the number of ways to decode it is " << result << ".\n"; 

  return 0;
}

```
The result of the script is following:
{: .text-justify}
```
The input message was 111 the number of ways to decode it is 3.
```

And as always, feel free to leave comments or suggestions below.
{: .text-justify}