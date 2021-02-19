---
layout: post
title:      "Data Structures and Algorithms: Recursion and Memoization"
date:       2021-02-19 00:43:03 +0000
permalink:  data_structures_and_algorithms_recursion_and_memoization
---


**Recursion**
Recursion in programming simply means writing functions that call themselves. If you find that a problem is easily solved once a subproblem is solved, then recursion is often useful. (This technique is often known as *divide and conquer*). An example is the factorial function, defined as the product of integers in sequence from 1 to n. Mathematically the formula is n!=n·(n−1)·(n−2)...3·2·1. Recursively, factorial can be defined as n!=n·(n−1)! For a recursive solution to be effective, there must be a base case, from which the rest of the recursive calls can be evaluated. In the case of factorial, n! = 1 when n = 0. Then 1! = 1* 0! =1* 1=1, 2! = 2* 1! =2* 1 = 2, 3 = 3 * 2! = 3 * 2 = 6, etc. If you do not define a base case, the function will never stop running.

Here is an example in Python. Python will successively call fact(n) until reaching the base case, then all the recursive calls can be resolved:

```
def fact(n):
    if n == 0:
        return 1
    
    else:
        return n * fact(n-1)
```

The following function sums all the integers from 0 to that integer, with the base case of returning 0 when n=0.
```
def rec_sum(n):
    
    if n == 0:
        return 0
    else:
        return n + rec_sum(n - 1)
```

This function sums all the individual digits of an integer. The base case is that n = n when n < 10. For other cases, you want the last digit plus the sum of rest of the digits.
```
def sum_func(n):
    if n < 10:
        return n
    else:
        return n % 10 + sum_func(int(n / 10))
```

**Memoization**
Memoization is an additional technique that is often used with recursion. In some recursive solutions, the method is called repeatedly to calculate a result. With memoizaiton, the results can be stored in a memo instead of calculating them again and again unnecessarily. Memoization can speed up a recursive method and reduce its computational complexity in terms of time, although it adds to the space complexity in order to store the memo.

For instance, the factorial method could be written like this:
```
factorial_memo = {}

def factorial(k):
    
    if k < 2: 
        return 1
    
    if not k in factorial_memo:
        factorial_memo[k] = k * factorial(k-1)
        
    return factorial_memo[k]
```

Whenever the factorial method is called, the factorials of all values up to k will be stored for future use in factorial_memo, and there will be no need to recalculate them if factorial is called again in the program. This saves a lot of time, at the cost of a little bit of space in memory to store the

**References**
[Udemy on Recursion](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/3179674#content)
[Wikipedia on Memoization](http://en.wikipedia.org/wiki/Memoization)
[Udemy on Memoization](https://www.udemy.com/course/python-for-data-structures-algorithms-and-interviews/learn/lecture/4048046#content)

