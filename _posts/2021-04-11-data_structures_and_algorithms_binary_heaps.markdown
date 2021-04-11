---
layout: post
title:      "Data Structures and Algorithms: Binary Heaps "
date:       2021-04-11 16:57:26 +0000
permalink:  data_structures_and_algorithms_binary_heaps
---


In this post, I will continue my discussion of trees with the specific examples of binary heaps, which can be used to implement a priority queue.

**Binary heaps**
Binary heaps[1] are used to implement a variation on the queue data structure called the *priority queue*. A queue, if you recall, is a linear data structure organized on the basis of "first in, first out". Items in a queue are enqueued, added to the queue, from the back, and dequeued, removed from the queue, from the front. In a priority queue, instead of being sorted by when they are added, items are sorted in order of priority. The highest priority items go to the front, regardless of when they entered the queue. A binary queue can be implemented with the binary heap structure, in which items can be enqueued and dequeued in O(log n),due to the tree structure. (You might think an array would be much slower, but actually you can construct and do operations on an array such that it becomes equivalent to a binary tree. More on that later). Binary heaps come in two variations, a *min heap* with the smallest key at the front, and a *max heap* with the largest key at the front.

Binary heaps are complete, meaning that  each level is filled except the bottom one, in which the nodes are filled left to right. This ensures that the tree is balanced, with approximately the same number of elements to the left and right. A min heap is arranged so the the smallest value is at the root. In each case, the value of parent nodes are less than or equal to their children. 

**Adding and removing elements**
In order to add an element ("enqueue"), you add the element to the first open space at the bottom of the tree, compare with its parent, and then swap with the parent until it is in the correct place in the tree. This operation restores the heap property that the element is greater than its parent, without disturbing the ordered relationships that already exist

In order to extract the minimum element("dequeue"), you replace the root with the last element, then compare the new root with its children and swap with the smaller child until everything is in the right place. Both of these operations have worst-case complexity of O(log n). 

Binary heaps can be represented in list form, indexed with the zero element being zero, then the root being index 1, its children being 2 and 3, and so on going down each level of the tree, then going left to right. Then you can traverse the tree by halving or doubling the index

**Implementing a binary heap with Python lists**

Here is a simple class for a binary heap with an init method. It simply stores the elements in an array (Python list) and keeps track of the current size of the array.
```
class BinHeap:
    def __init__(self):
        self.heapList = [0]
        self.currentSize = 0
```

To insert into the array you need both an insert method and a percUp ("percolate up") method:
```

    def percUp(self,i):
        
        while i // 2 > 0:
            
            if self.heapList[i] < self.heapList[i // 2]:
                
            
                tmp = self.heapList[i // 2]
                self.heapList[i // 2] = self.heapList[i]
                self.heapList[i] = tmp
            i = i // 2

    def insert(self,k):
        
        self.heapList.append(k)
        self.currentSize = self.currentSize + 1
        self.percUp(self.currentSize)
```
The insert method first of all appends the new value to the end of the heapList, an efficient operation with lists, and increments the count. Then it positions the element in the correct place, checking whether it is less than its parent and halving the index each time until it is not less than its parent, i.e. in the correct position . (It uses the // floor division or integer division method, to get an integer value for the index.)

In order to find and delete the minimum element, you can use the delMin method as follows, in conjunction with percDown ("percolate down"):

```
  def delMin(self):
        retval = self.heapList[1]
        self.heapList[1] = self.heapList[self.currentSize]
        self.currentSize = self.currentSize - 1
        self.heapList.pop()
        self.percDown(1)
        return retval
				
	def percDown(self,i):
        
        while (i * 2) <= self.currentSize:
            
            mc = self.minChild(i)
            if self.heapList[i] > self.heapList[mc]:
                
                tmp = self.heapList[i]
                self.heapList[i] = self.heapList[mc]
                self.heapList[mc] = tmp
            i = mc
```
The minimum element is simply the root element (at index 1 of the list) but after removing it you have to restore the heap to proper order. Since there needs to be a root element, it is set to the last element of the list. Then percDown is called, which successively moves the element down the tree, by doubling its index, until it is in the proper place.


**References**
[Wikipedia article on binary heaps](http://en.wikipedia.org/wiki/Binary_heap)
[Udemy course Python for data structures and algorithms](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/3179656#content)

1. One side note about heaps: it is possible that you may have encountered the term "heap" in the context of memory management. (I have, in the context of my random reading about programming languages, and investigation of errors and log files). Heap in this context has nothing to do with the data structure in question. "Stack" is another term that has a different meaning in the context of memory management. See en.wikipedia.org/wiki/Memory_management.
