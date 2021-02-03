---
layout: post
title:      "Basic Data Structures and Algorithms: Linked Lists"
date:       2021-02-03 20:58:40 +0000
permalink:  basic_data_structures_and_algorithms_linked_lists
---


If you are a new web developer, especially if you are a graduate of a bootcamp like Flatiron School, it is important to study data structures and algorithms. An obvious reason  is that they are needed for technical interviews. But more importantly, learning data structures and algorithms gives you experience in problem solving, and also a better understanding of computer science. A foundation in some CS concepts allows you to understand how the computer  represents, stores, and operates on your data. It will also give you the practical benefit of knowing what code will run efficiently, and what will become impossibly slow (or take up too much memory) as data sets grow.

**Linked Lists**
The linked list is one data structure that is not natively implemented in languages like JavaScript or Python (unlike arrays and the related stack and queue data structures). However, it is a simple structure to implement yourself.

In a *singly linked list*, each element (or node) of a linked list consists simply of a data value and a pointer to the next element. There is an identifer "head" that indicates the first element of the list, and the last element will have a pointer point to null as next_node.  In order to traverse the list, you can start at the head and visit each next_node pointer in turn. To add a node to the front, change "head" to the new node, and set its next_node pointer to the original head. To add a node at any other point, just set its next_node to the desired node, and change the previous node's next_node to the new node.

Here is a simple implementation of singly linked list with a node class in Python:
```
class Node(object):
    
    def __init__(self,value):
        
        self.value = value
        self.next_node = None
```
References to "head" can be made as needed in the rest of your code. For instance, the following code reverses a linked list, given its head:

```
def reverse(head):
    current = head
    last = None
    while current.nextnode:
        print(current.value, current.nextnode)
        next = current.nextnode
        current.nextnode = last
        last = current
        current = next
        print(current.value, current.nextnode)
    current.nextnode = last
    return current
```

A *doubly linked list* is the same as a singly linked list, with the addition of a reference to the previous node. This enables easy insertion or deletion of nodes at any point in the list, by changing the values of next_node and previous_node. Compared with a singly linked list, it is less costly to add and remove nodes toward  the end of the list.

```
class DoublyLinkedListNode(object):
    
    def __init__(self,value):
        
        self.value = value
        self.next_node = None
        self.prev_node = None
```

What is the advantage of a linked list? Arrays must be given a fixed size in advance, and expanded if they become too large. (This is handled automatically by high-level languages like Python). In a linked list there is no need to allocate space in this way as you add elements, it only takes up as much space as it needs. It is also less costly to add nodes at or near the beginning of the list, an operation that is linear with the length of an array, but only as costly as the desired index with a linked list.

**References**

[Udemy course: Python for Data Structures and Algorithms](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/3179640#content)
