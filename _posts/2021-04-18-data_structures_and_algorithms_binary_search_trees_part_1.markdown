---
layout: post
title:      "Data Structures and Algorithms: Binary Search Trees, Part 1"
date:       2021-04-18 18:05:53 +0000
permalink:  data_structures_and_algorithms_binary_search_trees_part_1
---


Binary search trees are a common use of the *tree* data structure. *Binary search*, as you may recall, is an efficient way to search through data for a desired value. It takes logarithmic O(log n) time instead of slower linear O(n) time. It sometimes called the "dictionary method": comparing what you are searching for, perhaps a word like "binary", with the midpoint of a sorted data set, say "N" in the dictionary (note that a dictionary is sorted in alphabetical order).  Then one uses the comparison to determine what half of the data set contains what you are looking for ("A-N"), and repeats the process until you have sufficiently narrowed it down. This process saves you, or the computer, from having to look through every element.

A *binary search tree* uses a *binary tree*--one in which each parent node has exactly two children--as an efficient data structure for organizing and searching data.

**Definition of a Binary Search Tree**

The defining property of a binary search tree, known as the *bst property* is that keys less than the parent are found in the left subtree, and keys greater than the parent are found in the right subtree. This statement only applies to the children of the parent and their descendants, not the whole tree. The whole tree may well appear disorganized at first glance. However, the bst property is sufficient to be able to insert and find keys easily. The keys are what is used to order the tree, and they may be associated with other data.

**Constructing a binary search tree**

If you insert the nodes [70, 31, 93, 94, 14, 23, 73] in order into a binary search tree, the tree will look like this:
![](https://wkdewey.files.wordpress.com/2021/04/binary-search-tree.png?resize=438%2C438:)

Despite the apparent disorder, all keys less than the root are in the left subtree, and all keys greater than the root and in the right subtree. 
1. 70 is the root because it is the first key. 
2. 31 is the left child because it is less than 70, and 93 is the right child because it is greater than 80. 
3. 94 is greater than 70, and also greater than 93, so it is the right child of 93. 
4. 14 is less than 70, and also less than 31, so it is the left child of 93. 
5. 23 is less than 31 so must be in its left subtree (already filled by 14), and it becomes the right child of 23. 

Subsequent nodes are placed in a similar recursive manner.

**Implementing the binary search tree**

One implementation of the binary search tree uses two classes, TreeNode and BinarySearchTree. TreeNode is implemented similarly to a linked list, with nodes and references to linked nodes. The basic form of the BinarySearchTree class keeps track of the root and the size property:

```
classBinarySearchTree:

    def __init__(self):
        self.root = None
        self.size = 0

    def length(self):
        return self.size

    def __len__(self):
        return self.size
```

TreeNode keeps track of the key, payload (the value associated with a given key), left and right children, and parent node:

```
class TreeNode:

    def __init__(self,key,val,left=None,right=None,parent=None):
        self.key = key
        self.payload = val
        self.leftChild = left
        self.rightChild = right
        self.parent = parent
```

It also has a number of helper methods that return information about the tree.

In order to insert a node into the tree, BinarySearchTree has the following method:

```
def put(self,key,val):
        if self.root:
            self._put(key,val,self.root)
        else:
            self.root = TreeNode(key,val)
        self.size = self.size + 1

    def _put(self,key,val,currentNode):
        if key < currentNode.key:
            if currentNode.hasLeftChild():
                   self._put(key,val,currentNode.leftChild)
            else:
                   currentNode.leftChild = TreeNode(key,val,parent=currentNode)
        else:
            if currentNode.hasRightChild():
                   self._put(key,val,currentNode.rightChild)
            else:
                   currentNode.rightChild = TreeNode(key,val,parent=currentNode)
```

The underscore is a Python convention for a private method, and in this case it is a method separated out from the main method to make recursion simple. 

The *put* method checks to see if there is already a root node. If not, it creates a TreeNode and assigns it to the root property of the BinarySearchTree. Otherwise it calls the private *_put* method recursively to insert the node into the proper place in the tree. It compares the key with the currentNode (this will be set to self.root when first called) and recursively calls *_put* on the leftChild if the key is  less than the currentNode or rightChild if it is greater. If there is no child, the desired spot for inserting has been found, and a new TreeNode instance is placed in that location.

The above  code makes use of the following simple helper methods from TreeNode:

```

	def hasLeftChild(self):
			return self.leftChild

	def hasRightChild(self):
			return self.rightChild
```

The *get* method, to find and return a value within the tree, follows a similar recursive pattern.
```
def get(self,key):
        if self.root:
            res = self._get(key,self.root)
            if res:
                
                return res.payload
            else:
                return None
        else:
            return None

    def _get(self,key,currentNode):
        
        if not currentNode:
            return None
        elif currentNode.key == key:
            return currentNode
        elif key < currentNode.key:
            return self._get(key,currentNode.leftChild)
        else:
            return self._get(key,currentNode.rightChild)

```
The get method first tests if the tree has a root, otherwise it returns None (the key was not found). Then it calls the recursive private *_get* method to search the tree. It returns the payload of the key that was found, or if the key is not found, it again returns None. The *_get* method checks the desired key against the current node, starting with the root. It then either returns the key if it matches, or recursively calls *_get* on either the left or right child. If it reaches a nonexistent node, that means the key is not found and it returns None.

Deleting a node is more complicated because it may require the tree to be reconstructed. Since this method is more complicated, I will save it for a future post


**References**
[Binary Search Trees from Udemy's course Python for Data Structures and Algorithms](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/3179662#content)
[Jupyter notebook on BinarySearchTree from the above course](https://nbviewer.jupyter.org/github/jmportilla/Python-for-Algorithms--Data-Structures--and-Interviews/blob/master/Trees/Binary%20Search%20Trees.ipynb)

