---
layout: post
title: "[data structure] unordered_set and set; "
description: "when to use unordered_set or set"
comments: true
keywords: "set, unordered_set, algoithm"
---


Unordered sets have to pay for their O(1) average access time in a few ways:
For instance, hash tables are "O(n)" at worst case. O(1) is the average case. Trees are "O(log n)" at worst.
People tend to overlook the fact that hashtables have O(1) average-case access time, meaning they can occasionally have big delays.

 red-black trees, an advanced self balancing tree.

 unordered_set, We need single element access i.e. no traversal.


1. set uses less memory than unordered_set to store the same number of elements.
   1. unordered used the linkedlist
2. For a small number of elements, lookups in a set might be faster than lookups in an unordered_set.
3. Even though many operations are faster in the average case for unordered_set, they are often guaranteed to have better worst case complexities for set (for example insert).
4. That set sorts the elements is useful if you want to access them in order.
5. You can lexicographically compare different sets with <, <=, > and >=. unordered_sets are not required to support these operations.

[[".",".","#",".",".",".",".","#"],
 [".","B",".",".",".",".",".","#"],
 [".",".","S",".",".",".",".","."],
 [".","#",".",".",".",".",".","."],
 [".",".",".",".",".",".",".","."],
 [".",".",".","T",".",".",".","."],
 [".",".",".",".",".",".",".","#"],
 [".","#",".",".",".",".",".","."]]
