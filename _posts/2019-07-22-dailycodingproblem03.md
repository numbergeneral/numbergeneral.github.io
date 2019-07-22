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

The information above will bring us to the intuitive definition of a node using recursion. We may denote each node in a tree as a triple $(V, L, R)$, where $V$ is the value of current node and $L$ and $R$ are children nodes. Children node can be a node or an empty set. 
{: .text-justify}

Thus, our serialization and deserialization functions will be recursive. Recursive methods are usually frowned upon, but as any tool in the programmers toolbox they can be used in the right context. To serialize we would read the values of the node and values of its children nodes. If the children node is empty the recursion stops on this path. The values will be converted to a list of strings. It would be possible to convert the list into the single string but it would not bring much insight into the problem and would obscure the core of the issue.   
{: .text-justify}

To deserialize we would revert the logic of serialize function and create objects based on the list of strings. Thus, reverting the process and getting the binary tree object as a result.  
{: .text-justify}

The actual implementation does not need a high degree of abstraction so it will also serve as an example. 
{: .text-justify}

## The solution
### Python
The Python solution will follow the logic presented above. I am starting with implementing the Node class proposed in the task description. Next, the serialize and deserialize functions are defined. The serialize function takes a node as an input. It assigns each of the node values to temporary variables. The children nodes are recursively assigned to temporary variables by the serialize function. If the node on given path is empty the recursion ends. Finally nodes are assigned to the list of strings. 
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


### C++

The C++ solution is similar to the presented Python solution. There are no significant implementation differences.
{: .text-justify}

```cpp

```

Please feel free to leave comments or suggestions below.
{: .text-justify}
