---
title: "Daily Coding Problem #03"
date: 2019-07-22
tags: [programming, daily coding problem]
excerpt: "My solution to the Daily Coding Problem #03"
mathjax: "true"
---

## The problem
The [Daily Coding Problem](https://www.dailycodingproblem.com/) #03 is the following:

> Given the root to a binary tree, implement serialize(root), which serializes the tree into a string, and deserialize(s), which deserializes the string back into the tree.

>For example, given the following Node class:
```python
class Node: 
    def __init__(self, val, left=None, right=None): 
        self.val = val 
        self.left = left 
        self.right = right 
```
>The following test should pass:
```python
node = Node('root', Node('left', Node('left.left')), Node('right')) 
assert deserialize(serialize(node)).left.left.val == 'left.left'
```
{: .text-justify}

## The definition of the problem

First thing would be to refresh some definitions related to the binary trees. Following the [Wikipedia definition](https://en.wikipedia.org/wiki/Binary_tree) a binary tree itself is a tree data structure in which each node has at most two children, which are referred to as the left child and the right child. This distinguishes binary tree from other trees as their nodes may have unlimited number of children. The topmost node of the tree is called root. Nodes with no children are called leafs or external nodes.
{: .text-justify}

The information above will bring us to the intuitive definition of a node using recursion. We may denote each node in a tree as a triple $$(V, L, R)$$, where $$V$$ is the value of current node and $$L$$ and $$R$$ are children nodes. Children node can be a node or an empty set. 
{: .text-justify}

Thus, our serialization and deserialization functions will be recursive. Recursive methods are usually frowned upon, but as any tool in the programmers toolbox they can be used in the right context. To serialize we would read the values of the node and values of its children nodes. If the children node is empty the recursion stops on this path. The values will be converted to a single string.   
{: .text-justify}

To deserialize we would revert the logic of serialize function and create objects based on the string. Thus, reverting the process and getting the binary tree object as a result.
{: .text-justify}

## The solution
### Python Prototype
The Python solution will follow the logic presented above. I am starting with implementation of the prototype that follows closely the problem but it's not very Python specific. 
{: .text-justify}

I will be using the Node class definition proposed in the task description. Next, the serialize and deserialize functions are defined. The serialize function takes a node as an input. It assigns each of the node values to temporary variables. The children nodes are recursively assigned to temporary variables by the serialize function. If the node on given path is empty the recursion ends. Finally nodes are assigned to the list of strings. 
{: .text-justify}

Deserialization method follows the same logic. The values of the string list are taken one by one and assigned to the respective node class elements. The children nodes are assigned recursively and recursion ends when it encounters an empty node.
{: .text-justify}

```python
class Node: 
    def __init__(self, val, left=None, right=None): 
        self.val = val 
        self.left = left 
        self.right = right

def serialize(node):
    val = node.val
    if node.left:
        left = serialize(node.left)
    else:
        left = None
    if node.right:
        right = serialize(node.right)
    else:
        right = None
    serialized = [val, left, right]
    return serialized

def deserialize(serialized_node):
        val = serialized_node[0]
        if serialized_node[1]:
            left = deserialize(serialized_node[1])
        else:
            left = None
        if serialized_node[2]:
            right = deserialize(serialized_node[2])
        else:
            right = None
        return Node(val, left, right)

tree = Node('root', Node('left', Node('left.left')), Node('right'))
assert deserialize(serialize(tree)).left.left.val=="left.left"
```

### Direct Python Solution
The knowledge of Python built-in functions can make the solution shorter and much more elegant. First, there is a method specific for tasks like serialization. The build-in function 'repr' is designed for returning a printable representation of a given object. Thus, we can overload it in our Node class. By recurrence it will generate a single string representing a binary tree. It's worth noticing details of implementation of this rather simple function. I am keeping all of the parenthesis so the output would look exactly like a code for Python definition of this tree. 
{: .text-justify}

To revert the string back into object I will use a function 'eval' that evaluates a string as a fragment of Python code. Thus, by leveraging built-in Python functions we may deliver the whole solution of the problem using only a couple lines of code.
{: .text-justify}

``` Python
class Node: 
    def __init__(self, val, left=None, right=None): 
        self.val = val 
        self.left = left 
        self.right = right

    def __repr__(self):
        return ('Node(' + repr(self.val) + ', ' + repr(self.left)
                +   ', ' + repr(self.right) + ")")

tree = Node('root', Node('left', Node('left.left')), Node('right'))
serialized_tree = repr(tree)
deserialized_tree = eval(serialized_tree)
assert deserialized_tree.left.left.val == "left.left"
```

### C++

The C++ solution is much more verbose and presents a couple of differences in comparison with Python solution. First of all both C++ classes and structs cannot contain instances of itself. It will result in infinite recursive call to the constructor, thus resulting in object of infinite size. However, they can contain pointers to their type. Thus I will create a simple node struct that contains a sting as its value and pointers to the child nodes. 
{: .text-justify}

The serialization function follows the same logic as the one from Python prototype. It takes the Node object as an input and through recursion returns a string representation of the tree.       
{: .text-justify}

The deserialization function is split into two parts. First function splits the string by commas and saves the result in the vector of strings. The second function is the one that reads the elements from the vector and based on their value (if the string is a value of the node or empty node) recursively creates new nodes and assigns their values.

```cpp
#include <iostream>
#include <sstream>
#include <string>
#include <vector>

struct Node{
  std::string value; 
  Node *left;
  Node *right;
};

Node new_node(std::string value)
{
  Node temp_node = {value};
  return temp_node;
}

std::string serialize(Node tree){
    std::string val;
    std::string left;
    std::string right;
    val = tree.value;

    if(tree.left) {left = serialize(*(tree.left));}
    else {left = "-";}

    if(tree.right){right = serialize(*(tree.right));}
    else {right = "-";}

    std::string res =  val + "," + left + "," + right;
    return res;
}

void deserialize_helper(std::vector<std::string>& res, Node** tree)
{
    if(!res.empty() && res.front() != "-")
    {
      *tree = new Node;
      (*tree) -> value = res.front();
      res.erase(res.begin());
      
      deserialize_helper(res, &((*tree) -> left));
      deserialize_helper(res, &((*tree) -> right));
    }

    else if(!res.empty() && res.front() == "-")
    {
      res.erase(res.begin());
    }
}

Node deserialize(std::string serialized_tree)
{
  Node* result_node;
  std::istringstream iss (serialized_tree);
  std::string token;
  std::vector<std::string> res;

  while(std::getline(iss, token, ','))
  {
    res.push_back(token);
  }

  deserialize_helper(res, &result_node);

  return *result_node;
}

int main() {
  Node left_left  = new_node("left_left");
  Node left       = new_node("left");
  left.left       = &left_left;
  Node right      = new_node("right");
  Node root       = new_node("root");
  root.left       = &left;
  root.right      = &right;

  std::string result = serialize(root);
  std::cout << "Serialized: \t\t"<< result << "\n";

  Node deserialized;
  deserialized = deserialize(result);
  std::cout << "Deserialized: \t\t"<< serialize(deserialized)<< "\n";

  bool assert; 
  assert = (root.left->left->value == deserialized.left->left->value);

  std::cout <<"The bool comparison is: " << assert << "\n"; 

  return 0;
}
```
The output of this C++ script is following:
{: .text-justify}
```
Serialized:         root,left,left_left,-,-,-,right,-,-
Deserialized:       root,left,left_left,-,-,-,right,-,-
The bool comparison is: 1
```
And as always, feel free to leave comments or suggestions below.
{: .text-justify}
