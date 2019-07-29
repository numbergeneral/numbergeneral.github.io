---
title: "Daily Coding Problem #06"
date: 2019-07-27
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #06"
mathjax: "true"
---

## The problem
The [Daily Coding Problem](https://www.dailycodingproblem.com/) #06 is the following:

>An XOR linked list is a more memory efficient doubly linked list. Instead of each node holding next and prev fields, it holds a field named both, which is an XOR of the next node and the previous node. Implement an XOR linked list; it has an add(element) which adds the element to the end, and a get(index) which returns the node at index.
{: .text-justify}

>If using a language that has no pointers (such as Python), you can assume you have access to get_pointer and dereference_pointer functions that converts between nodes and memory addresses.
{: .text-justify}

## The definition of the problem 
To define this problem well we need to gain insight on the bitwise XOR operation. First of all let's take a look at the XOR operation itself. 
{: .text-justify}

<center>

| A | B | XOR |
|---|:-:|:---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |  

</center>

By looking at the truth table we can observe than XOR returns true only in case if and only if one of the values is true and other is false. Our goal is to use this operation on the pointers to code and decode two addresses within one element. So we just make the XOR operation digit by digit, for example XOR(1100, 0101) = 1001.
{: .text-justify}

In order to use XOR in our problem we would need also some of its properties. We need to obtain an address of a node by applying XOR to the addresses of previous and next nodes. XOR undo itself if applied twice, for example A = A XOR B XOR B. Knowing that, we may use this property for list traversal. Namely, we are in the Node B and we want to move to node C. We know the address of previous node A and the value of XOR(A,C) stored in the Node B. Thus, we can do the following operation XOR(A, XOR(A,C)) = C. This way we get the address of C and we can repeat the process to traverse to the next node. 
{: .text-justify}

From the paragraph above me may already draw some conclusions about pros and cons of the XOR linked lists. For the price of saving memory we are restricted to travel the list only from start to end or from end to start. We cannot jump at the random node and start traversing as we would not know the address of the previous nodes necessary to obtain the address of next node. 
{: .text-justify}

Let's go through an example first. We have an empty list and will add two nodes. To make things simpler let's assume that addresses are 6 digit long. We start with an empty list, so the first node will have neither predecessor nor successor and will point to null in both cases.
{: .text-justify}

<center>

| NULL | NULL | Node_0->xpn = XOR(NULL, NULL) |
|---|:---:|:---:|
| 000000 | 000000 | 000000 |  

</center>  
  
Now let's add a new node. We need to make sure that the old node points to NULL and the new node while the new node points to old node and NULL. So the new node link should look following:
{: .text-justify}
<center>

| *Node_0 | NULL | Node_1->xpn = XOR(\*Node_0, NULL) |
|---|:---:|:---:|
| 010101 | 000000 | 010101 |

</center>  
  
And now we also should update the link for the old node:
{: .text-justify}
<center>

| Node_0->xpn | NULL | *next = XOR(Node_0->xpn, NULL) |
|---|:---:|:---:|
| 000000 | 000000 | 000000 |
  
| *Node_1 | *next | Node_0->xpn = XOR(\*Node_1, \*next) |
|---|:----:|:---:|
| 111001 | 000000 | 111001 |

</center>  
  
Now let's add the final node. We will repeat operation from the last step: 
{: .text-justify}
<center>

| *Node_1 | NULL | Node_2->xpn = XOR(\*Node_1, NULL) |
|---|:----:|:---:|
| 111001 | 000000 | 111001 |

</center>  
  
And now we also should update the link for the old node:
{: .text-justify}
<center>

| Node_1->xpn | NULL | *next = XOR(Node_1->xpn, NULL) |
|---|:----:|:---:|
| 010101 | 000000 | 010101 |
  
| *Node_2 | *next | Node_1->xpn = XOR(\*Node_2, \*next) |
|---|:----:|:---:|
| 001101 | 010101 | 011000 |

</center>
  
So finally we have:
{: .text-justify}
<center>

|| Node_0 | Node_1 | Node_2 |
|---|---|:----:|:---:|
|address| 010101 | 111001 | 001101 |
|xpn| 111001 | 011000 | 111001 |

</center>
  
Now let's traverse the list. To do so we need an address of the head node, then we take XOR of the previous node and the xnp value of the current node.
{: .text-justify}
<center>

| *Node_0 | *Node_1 = XOR(NULL, Node_0->xnp) | *Node_2 = XOR(\*Node_0, Node_1->xnp) |
|---|:----:|:---:|
| 010101 | 111001 | 001101 |

</center>
  
## The solution
### C++
Since this problem is specific to the programming languages with native pointers I will omit the Python solution and move straight to C++.
{: .text-justify}

The solution follows closely the logic presented in the previous section. First we need a Node class that will hold some value (assume integer) and the XORed address of previous and next nodes (thus name xpn - Xor Previous Next). The class does have a constructor and a static method for running XOR operation on two node pointers.
{: .text-justify}

The next class is a XOR linked list. Head and tail of the list are kept for traversing the list, and tail is also helpful for appending an element to the list. The class does have a method to add a node at the end of the list, to get the node at the given index and to print the whole list. The adding of the node follows the logic from the problem definition. Getting node and printing list is simply a traverse of the list.
{: .text-justify}

```cpp
#include <iostream>
#include <vector>

class Node
{
  public:
  int value;
  Node * xpn;

  Node(int new_value)
  {
  value = new_value;
  }

  static Node * XOR (Node *pervious, Node *next)
  {
  return (Node*)((uintptr_t)(pervious) ^ (uintptr_t)(next));
  }
};

class XOR_LL
{
  public:
  Node* head = NULL;
  Node* tail = NULL;
  int length = 0;

  XOR_LL(Node* head_node)
  {
    head = head_node;
    tail = head_node;
    length += 1;
  }

  void add(int value)
  {
    Node *new_node = new Node(value);

    new_node->xpn = Node::XOR(tail, NULL);
  
  if (tail != NULL)  
    { 
        Node* next = Node::XOR(tail->xpn, NULL);  
        tail->xpn = Node::XOR(new_node, next);  
    } 
    length += 1;
    tail = new_node;
  }

  Node* get(int index)
  {
    Node *curr = head;  
    Node *prev = NULL;  
    Node *next = NULL;  

    for(int i = 0; i < index; i++) 
    {  
        next = Node::XOR(prev, curr->xpn);  
  
        prev = curr;  
        curr = next;  
    }  
    return curr;
  }

  void print_list()
  {
    Node *curr = head;  
    Node *prev = NULL;  
    Node *next = NULL;  
  
    std::cout << "The XOR linked list contains: ";  
  
    while (curr != NULL)  
    {  
        std::cout << curr->value <<" ";  
  
        next = Node::XOR(prev, curr->xpn);  
  
        prev = curr;  
        curr = next;  
    }
    std::cout << "\n"; 
  }
};

int main() {
    // Create the XOR linked list
    Node *head = new Node(1);
    XOR_LL xor_ll(head);
    xor_ll.add(2);
    xor_ll.add(3);
    xor_ll.add(4);
    xor_ll.add(5);

    // Print the list
    xor_ll.print_list();

    // Get the node at the index
    // Indexing starts at 0
    int index = 2; 
    Node* indexed_node = xor_ll.get(index);
    std::cout << "The value of xor_ll at index " << index << " is " << indexed_node->value << "\n"; 

  return 0;
}
```
The result of the script is following:
{: .text-justify}

```
The XOR linked list contains: 1 2 3 4 5
The value of xor_ll at index 2 is 3
```

And as always, feel free to leave comments or suggestions below.
{: .text-justify}
