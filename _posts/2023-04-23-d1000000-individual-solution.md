---
title: D1000000
date: 2023-04-24
---

# OVERVIEW 
In this document, we explore the problem of finding the longest straight that can be formed using a collection of N dice. The reader will be introduced an algorithm that efficiently solves this problem and a detailed explanation of how the algorithm works and why it is suitable for solving the problem at hand. 

# CONTEXT
This problem involves choosing dice from a collection of N dice, where the i-th die has Si sides showing integers 1 through Si. A straight of length ℓ starting at x is the list of integers x,x+1,…,x+(ℓ−1). The goal is to choose some of the dice (possibly all) and pick one number from each to form a straight. The problem asks for the longest straight that can be formed in this way. 
Let's examine a test case to better understand this, for this test case we will consider 10 dice with the following number of sides 10 10 7 6 7 4 4 5 7 4, it is possible to form the straight 1,2,3,4,5,6,7,8,9 by discarding one d4 and using the d4⁠'s, d5, and d6 to get 1 through 4; the d7⁠'s to get 5 through 7; and the d10⁠'s to get 8 and 9. There is no way to form a straight of length 10, so this is the best that can be done.

# SOLUTION
In my opinion, this problem was one of the easiest to solve. While I did require help from my partner to understand the problem and how to solve it, and this allowed me to quickly grasp the given algorithm without spending time figuring it out on my own. After my partner explained the problem to me, we discovered that the analysis section of the Google Code Jam problem instructions included the algorithm needed to find the longest straight. The algorithm proposed in the problem's instructions is:
```
maximum_straight_length(S):
  sort(S)
  length = 0
  for si in S:
    if si > length: length += 1
  return length
```
Sort the list S containing the number of sides for each die in ascending order. Sorting ensures that the algorithm can find the longest straight by considering the dice with fewer sides first. Iterate through each element Si in the sorted list S. For each die with Si sides and check if Si is greater than the current length. If it is, increment length by 1. This condition ensures that the algorithm can extend the straight by one integer. Since the dice are sorted, it guarantees that the current die can contribute to the longest straight without breaking the consecutive integer sequence. I implemented the algorithm in Kotlin and added a code segment to read user input.