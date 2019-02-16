---
title: "Daily Coding Problem #01"
date: 2019-02-16
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #01"
mathjax: "true"
---

Looking for an opportunity to improve my coding skills I have encountered the website called [Daily Coding Problem](https://www.dailycodingproblem.com/). After signing up, you will receive a daily email with a coding question asked by the big tech companies during the interviews. I will try to include the solutions to the questions in at least two different languages and comment on implementation differences.{: .text-justify}

The first problem in the series is following:
> Given a list of numbers and a number k, return whether any two numbers from the list add up to k.
> For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17.
> Bonus: Can you do this in one pass?

Let's define the problem. We have some integer number $$k$$ and the list of integer numbers of length $$n$$ such that $$ L = \{l_1, l_2, ..., l_n \}.$$ The number $$k$$ can be written as a sum of two numbers $$a$$ and $$b$$ such that $$k = a + b$$. Thus, if we make a set $$A =\{a : a = k - l_i\}, i \in (1, 2, ... , n)$$ the problem will have the solution if the the intersection of the set $$L$$ and the set $$A$$ is not empty.{: .text-justify}

The implementation in Python can be coded in one loop. We start by defining the empty list for the subtraction results. Then loop elements in the input list and for each element save the subtraction result. After that, we check if the number we are currently iterating is within the list of subtraction results. If that is true then function returns true. If after the whole list has been iterated and none of the elements in the subtraction list is equal to any element in the input list the function returns false.{: .text-justify}

```python
def number_can_be_created_from_two_list_values(input_list, number):
    subtraction_list = []
    
    for i in range(0, len(input_list)):
        subtraction_list.append(number - input_list[i]) 
        if input_list[i] in subtraction_list:
            print("Two numbers from the list add up to a given number.")  
            return True

    print("Any two numbers from the list don't add up to a given number.")
    return False

def test_solution(number, test_dictionary):
    print("The tested number is: ", number)
    for key, value in test_dictionary.items():
        print("Test name:", key, "\nThe numbers are:", value)
        number_can_be_created_from_two_list_values(value, number)

if __name__ == "__main__":
    number = 17
    test_dictionary = {"Positive integer test":[10, 15, 3, 21, 44, 981, 7],
                       "Negative integer test":[10, 5, 12, -27],
                       "Same value test": [17, 17, 17, 17],
                       "Zero test": [0, 0, 0, 0, 0]}
    test_solution(number, test_dictionary)
```

The solution in C++ is very similar. The only difference is that there is no ready function to check if the element is within the vector. Thus, I have implemented another loop that loops values in the subtraction vector and checks if the current iteration element is equal to one of them.

```cpp
#include <vector>
#include <iostream>

using namespace std;

// define the function that will return true or false given the ints in the 
// list input and a value k
bool AnyTwoNumbersInVectorAddUpToNumber(int number, vector<int> input_vector);

int main()
{
    cout << "Daily Coding Problem 01 \n";

    // Initialize the test vector along with number  
    vector<int> input_vector{1, 15, 3, 6, 17, 0, 0, 0};
    int k_value = 17;

    // Print the test value and the numbers in the vector 
    cout << "The value to test is: " << k_value << "\n";
    cout << "The input list is: ";
    for(int i = 0; i < input_vector.size(); i++)
    {
        cout << input_vector[i] << " ";
    }

    // Run the function
    bool number_adds_up_in_the_list = AnyTwoNumbersInVectorAddUpToNumber(
        k_value, input_vector);
    
    // Print out the result
    if (number_adds_up_in_the_list)
    {
        cout << "\nThe number can be created from the two numbers in the list\n";
    }
    else
    {
        cout << "\nThe number cannot be created from any two numbers in the list\n";
    }
return 0;
}

bool AnyTwoNumbersInVectorAddUpToNumber(int number, vector<int> input_vector)
{
    // Initialize the empty vector for substraction values
    vector<int> subctraction_vector;
    // Loop for elements in the input vector and push back substraction values
    for(int i = 0; i < input_vector.size(); i++)
    {
        subctraction_vector.push_back(number - input_vector[i]); 
        // Loop the substraction_vector values
        // Return true if the i-th value of the input vector is equal to element
        // of the substraction_vector
        for(int j = 0; j < subctraction_vector.size(); j++)
        {
            if (input_vector[i] == subctraction_vector[j])
            {
                return true;
            }
        }    
    }
        return false;
}
```

The Python implementation is equipped with the set of tests. I have tested four scenarios: a list of positive integers, a list with negaitve integers, a list of the same values and a list of zeroes. All of them give the expected results.

Please feel free to leave comments or suggestions below. 