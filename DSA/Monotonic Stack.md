
#### Finding out a[j] - j < a[i] - i

If we want to find out an index j such that for each a[i] this condition holds, this can be done using a stack. In this situation a stack can be used as follows. Assuming three indexes j, i and k. We will prove correctness of using the stack. The stack for an index i removes all the elements between j and i such that a[l] - l >= a[i] - i. Finally we get to a[j] - j < a[i] - i. Now we encounter a[k] - k. If it is smaller than a[i] - i that means it is also smaller than all the elements it removed from the stack. This is similar to finding the previous smaller element.


### Important Problems
1. [Maximum Score of a Good Subarray](https://leetcode.com/problems/maximum-score-of-a-good-subarray/)
2. 