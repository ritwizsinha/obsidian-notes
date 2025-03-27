
1. [Maximum Deletions on a String](https://leetcode.com/problems/maximum-deletions-on-a-string/)
2. Read masters theorem

### Topological Sort
Dp can be applied after we topological sort the states. If there is a cycle then no dp can be applied.


### Top down over bottom up
Sometimes top down calculates the states needed for the answer and is faster than the bottom up approach which requires going through every possible state.

Top down is easier in the case where we are trying to find the max profit for choosing non intersecting intervals.

Unless you are well aware about the bottom up don't do it.