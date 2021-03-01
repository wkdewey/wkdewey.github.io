---
layout: post
title:      "Data Structures and Algorithms: Trees"
date:       2021-03-01 21:24:43 +0000
permalink:  data_structures_and_algorithms_trees
---


Trees are a very common data structure that is important for new programmers to master. Examples you may have run across, whether or not you know that they are trees, including the following:
1. Outlines of books and papers
2. Biological classification (Kingdoms, phylum, class, order, family...down to species and subspecies)
3. HTML elements and DOM nodes
4. The file system on a computer, with disk drives, directories, and subdirectories
5. URL's of websites, with a domain and series of directories
6. the organizational chart of a company (CEO, vice presidents, managers, etc.)

**Definition of a tree**
Trees in computer science are upside down compared with an ordinary tree a root at the top and branches and leaves that spread downward. They are defined as a series of *nodes*, connected in parent-child relationships, by *edges* or the connections between nodes. Each node is unique, and all children of one node are independent of those of other nodes. The top node with no parents is the *root* and its value is often known as the *key*. *Sibling* nodes have incoming edges from the same node, or parent. A *subtree* consists of a parent node and all descendant nodes. A *leaf node* is one that has no children  It is possible to move a section of the tree (a subtree) to a different position without affecting the others. A *path* is an ordered list of nodes connected by edges. A tree can be defined recursively, as consisting (if it is not empty) of a root and zero or more subtrees. A *binary tree* is one in which each node has at most two children, designated as left and right.
**Implementing trees**
There are two basic ways you can implement binary trees in Python (or similar languages like Ruby and JavaScript): as an array of arrays, or using object-oriented methods.
*Array-based implementation*
In the first method, the tree consists of an array of arrays, or in python terminology a list of lists. Each nested list represents a subtree. The full implementation looks like this:
```
def BinaryTree(r):
    return [r, [], []]

def insertLeft(root,newBranch):
    t = root.pop(1)
    if len(t) > 1:
        root.insert(1,[newBranch,t,[]])
    else:
        root.insert(1,[newBranch, [], []])
    return root

def insertRight(root,newBranch):
    t = root.pop(2)
    if len(t) > 1:
        root.insert(2,[newBranch,[],t])
    else:
        root.insert(2,[newBranch,[],[]])
    return root

def getRootVal(root):
    return root[0]

def setRootVal(root,newVal):
    root[0] = newVal

def getLeftChild(root):
    return root[1]

def getRightChild(root):
    return root[2]
```
Considering the first method BinaryTree: The variable r represents the value of root node, the first list [] represents the left child (empty at first), the second list [] represents the right child (ditto). Then the insertLeft and insertRight methods take in a root and newBranch (a tree, represented by the same list structure) and inserts it. It moves the existing left or right branch (if any) accordingly. getRootVal, getLeftChild, getRightChild get the root, left branch, and right branch, respectively, using the indexes 0, 1, or 2. Although this is a simple implementation, the deeply nested lists could get quite unwieldy.

*Object-oriented implementation*
This implementation is easier to understand than the above, provided you are familiar with object oriented programming:
```
class BinaryTree(object):
    def __init__(self,rootObj):
        self.key = rootObj
        self.leftChild = None
        self.rightChild = None

    def insertLeft(self,newNode):
        if self.leftChild == None:
            self.leftChild = BinaryTree(newNode)
        else:
            t = BinaryTree(newNode)
            t.leftChild = self.leftChild
            self.leftChild = t

    def insertRight(self,newNode):
        if self.rightChild == None:
            self.rightChild = BinaryTree(newNode)
        else:
            t = BinaryTree(newNode)
            t.rightChild = self.rightChild
            self.rightChild = t


    def getRightChild(self):
        return self.rightChild

    def getLeftChild(self):
        return self.leftChild

    def setRootVal(self,obj):
        self.key = obj

    def getRootVal(self):
        return self.key
```
When initializing, the rootObj (can be a value of any type) is set as the key, and left and right children are initialized with the value None to mark that they are empty. Each leftChild and rightChild, if not empty, is itself an object of the class BinaryTree. The insertLeft and insertRight methods work much like they do above, but instead of inserting into the list, the newNode is initialized as a new BinaryTree object. In order to return the root values or left or right children, you can simply returnself.key ,self.rightChild, or self.leftChild.

**Tree traversal**
To *traverse* a tree means to visit each node in a defined order. The *preorder* traversal first visits the root, then recursively performs a preorder traversal on the left node, then recursively preforms a preorder traversal on the right node. In the case of a book, divided into chapters, sections, and subsections, the preorder traversal would traverse the book into reading order. if you have a BinaryTree class, you could write the following:
```
def preorder(tree):
    if tree:
		    print(tree.getRootVal())
				preorder(tree.getLeftChild())
				preorder(tree.getRightChild())
```

The inorder and postorder traversals are similar to preorder, they just change when they visit the root node. The inorder traversal recursively performs an inorder traversal on the left node, visits the root node, then recursively performs an inorder traversal on the right node. The postorder traversal recursively performs an postorder traversal on the left node, performs a postorder traversal on the right node, then visits the root node.

**References**
[Python for Data STructures and Algorithms: Section 16: Trees](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/3179654#content)



