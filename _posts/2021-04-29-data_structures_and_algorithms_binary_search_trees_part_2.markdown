---
layout: post
title:      "Data Structures and Algorithms: Binary Search Trees, Part 2"
date:       2021-04-29 23:00:02 +0000
permalink:  data_structures_and_algorithms_binary_search_trees_part_2
---


In the previous post, I discussed how to implement binary search trees, and described how to insert a node and retrieve it from thre tree. In this post, I describe how to delete a node,
and also how to iterate through a binary search tree.

**Deleting a node--find the node**

The first task in deleting a node is to find it. This can be done by the methods we implemented earlier, the *get* method in conjunction with the private *\_get* method that recursively searches the tree.
```
class BinarySearchTree
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
If the tree has a single node, we just remove the root node. If the code never finds the node to be returned, it returns an error.
```
def delete(self,key):
        
        if self.size > 1:
            
            nodeToRemove = self._get(key,self.root)
            if nodeToRemove:
                self.remove(nodeToRemove)
                self.size = self.size-1
            else:
                raise KeyError('Error, key not in tree')
        elif self.size == 1 and self.root.key == key:
            self.root = None
            self.size = self.size - 1
        else:
            raise KeyError('Error, key not in tree')
```
In each case, if the node is found, the size of the tree is subtracted by one. If the element is the ronly node, the root is set to None, otherwise the *remove* method is called.

There are three cases to consider when removing a node from a tree:
1. the node has no children
2. the node has one child
3. the node has two children

**If the node has no children**

This can be determined with the *isLeaf* method (which simply checks if the node has a rightChild or leftChild attribute). In this case, all you need to do is delete the node setting the parent's reference to the node to None:
```
if currentNode.isLeaf(): #leaf
            if currentNode == currentNode.parent.leftChild:
                currentNode.parent.leftChild = None
            else:
                currentNode.parent.rightChild = None
```
**if the node has one child**

In this case, the tree will need to be reconstituted, but this can be done simply by putting the child in the place of its parent. 
```
else: # this node has one child
            if currentNode.hasLeftChild():
                if currentNode.isLeftChild():
                    currentNode.leftChild.parent = currentNode.parent
                    currentNode.parent.leftChild = currentNode.leftChild
                elif currentNode.isRightChild():
                    currentNode.leftChild.parent = currentNode.parent
                    currentNode.parent.rightChild = currentNode.leftChild
                else:
                
                    currentNode.replaceNodeData(currentNode.leftChild.key,
                                    currentNode.leftChild.payload,
                                    currentNode.leftChild.leftChild,
                                    currentNode.leftChild.rightChild)
            else:
                
                if currentNode.isLeftChild():
                    currentNode.rightChild.parent = currentNode.parent
                    currentNode.parent.leftChild = currentNode.rightChild
                elif currentNode.isRightChild():
                    currentNode.rightChild.parent = currentNode.parent
                    currentNode.parent.rightChild = currentNode.rightChild
                else:
                    currentNode.replaceNodeData(currentNode.rightChild.key,
                                    currentNode.rightChild.payload,
                                    currentNode.rightChild.leftChild,
                                    currentNode.rightChild.rightChild)
```
The code determines whether the currentNode (the one we are trying to delete) has a left child or right child, then whether it is a left child or right child of its parent. It then changes the references so that its child and its parent refer to each other as parent and child, cutting out the current node. If the current node is the root, then *replaceNodeData* makes the child node the root.

**If the node has two children**

In this case, you cannot "promote" one of the children as in the previous case. However, you can find a *successor* node that can replace the node you are deleting. What node will allow the tree to maintain the binary search tree properties (lesser keys on the left subtree, greater keys on the right)? The node with the next largest key to the one we're deleting.

It will have at most one (right) child, because there are no intermediate nodes between the successor and the deleted nodes. Having identified the successor, you can delete it like we did above for the node with one child. Then you can insert the successor in the proper place in the tree, where you are deleting the node.
```
elif currentNode.hasBothChildren():
            
            succ = currentNode.findSuccessor()
            succ.spliceOut()
            currentNode.key = succ.key
            currentNode.payload = succ.payload
```
This case uses the following helper methods:
*findSuccessor* finds the successor element to the node by searching the right, then left sides of the tree.
```
def findSuccessor(self):
        
        succ = None
        if self.hasRightChild():
            succ = self.rightChild.findMin()
        else:
            if self.parent:
                
                if self.isLeftChild():
                    
                    succ = self.parent
                else:
                    self.parent.rightChild = None
                    succ = self.parent.findSuccessor()
                    self.parent.rightChild = self
        return succ
```
*findMin* is called by the above method to find the correct right child. It goes through all the left children until it finds the correct node.
```
    def findMin(self):
        
        current = self
        while current.hasLeftChild():
            current = current.leftChild
        return current
```
*spliceOut* is used to remove the successor, adjusting the relationships accordingly:
```
    def spliceOut(self):
        if self.isLeaf():
            if self.isLeftChild():
                
                self.parent.leftChild = None
            else:
                self.parent.rightChild = None
        elif self.hasAnyChildren():
            if self.hasLeftChild():
                
                if self.isLeftChild():
                    
                    self.parent.leftChild = self.leftChild
                else:
                    
                    self.parent.rightChild = self.leftChild
                    self.leftChild.parent = self.parent
        else:
                    
            if self.isLeftChild():
                        
                self.parent.leftChild = self.rightChild
            else:
                self.parent.rightChild = self.rightChild
                self.rightChild.parent = self.parent
```
**Iterating through a binary tree**

Previously, I discussed how to traverse a binary tree. The inorder traversal of a binary search tree will go through each of the nodes in numeric order. An iterator works like a traversal, but will only return one node at a time. If defined using the \__iter\__ method, you can access the iterator with Python's "for...in" syntax for loops.
```
def __iter__(self):
    if self:
		    if self.hasLeftChild():
				    for elem in self.leftChild():
						    yield elem
				yield self.key
				if self.
```
Note that this method is recursive, because the for...in loop makes use of iter. The *yield* operator returns a single element, the next in the iteration sequence, whenever it is called. As with an inorder traversal, the code recursively tranverses the left children, the root node, and then the right children.

**References**

[Udemy course: Python for Data Structures, Algorithms, and Interviews](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/4460706#overview)

[Jupyter notebook from above course](https://nbviewer.jupyter.org/github/jmportilla/Python-for-Algorithms--Data-Structures--and-Interviews/blob/master/Trees/Binary%20Search%20Trees.ipynb)
