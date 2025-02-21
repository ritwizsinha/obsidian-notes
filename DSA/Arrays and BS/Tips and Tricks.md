
1. In array problems sometimes, the iterations which seems O(N^2) reduce down to O(N) even with nested loops. Sometimes iterating through all possible outputs amounts to iterating through all items in an array which makes it linear.
2. We can convert two sets containing common elements to 3 disjoint sets containing no common elements. So if we have a set {1,2,4,5, 3} and {2,3,8,9,10} -> {1,4,5} and {8,9,10} and {2,3}
3. Removing x number of elements from a container is same as taking n - x elements from the container.
4. We can get the latest most frequent element using a frequency map and a stack in O(1) time. The operations allowed are push and pop
5. Explore divide and conquer approaces in questions.
6. In array questions considering every element as the part of the solution, for example sometimes the left or the right bound is very helpful.
7. Code to get the left and right smaller at once
	```cpp
        vector<int> leftSmaller(n, -1), rightSmaller(n, -1);
        for(int i = 0; i < n; i++) {
            while(!s.empty() and heights[s.top()] > heights[i]) {
                rightSmaller[s.top()] = i;
                s.pop();
            }
            if (!s.empty()) leftSmaller[i] = s.top();
            s.push(i);
        }
    
```

1. Complexity of log(m * n) is same as complexity of logm + logn
2. To create a hash for a big datastructure we can always create a string as the key
3. Median is the point in the array such that it takes minimum number of operations to convert the remaining elements to the median if the operations are single increment and decrement. So the solution to the problem of finding the minimum number of operations to make all elements in a vector equal is the median in the array.


### Sliding Window
4. [Minimum Number of Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/)


### GCD and Median Trick
5. [Make K-Subarray Sums Equal](https://leetcode.com/problems/make-k-subarray-sums-equal/)



### Hoare's Algorithm for finding the kth largest, smallest, most frequent, least frequent elements.

Probabilistic algorithm with average complexity of O(N) but worst of O(N^2) where the worst case is negligible. Finding the median is an O(N) operation.

**Always remember a reduction in complexity of computation from n to n / r to n / r^2 is of complexity O(N)**




### Line Sweep
1. [Car Pooling](https://leetcode.com/problems/car-pooling/)
2. [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)
There are 2 kinds of line sweep. 
3. One is doing the sweep over the whole solution space. This is done using the array and is generally O(Max element in the array)
4. Second is sorting and jumping over a bunch of points and only considering points in the array given.
5. Extending the concept of line sweep in problems like meeting rooms and calendars and car pooling, one possible way of solving is treat start of every segment as an event with +x passengers or items and end of every event as -x of the passengers or events. Sorting all of them we can then linearly iterate and get the maximum capacity. The negative events cancel the affect of the passenger addition.

Always remember this case in events problems

------------------ A
------ B  ------- C


#### Interval Questions
1. [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
2. [Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)

**Why Sorting with endTimes Works**  
In many of the problem above we do sorting with endTime , this is an important concept to understand,  
if you sort with endTime and then check other intervals you can easily find non-overlapping intervals like this, here prev is previous interval.

```
if(intervals[i][0] < intervals[prev][1])
```

Reason is if a new interval start is before previous end time that means a sure overlap.  
While on the other hand if we sort by startTime, we dont know when this interval gonna end, there will be overlaps.  
Here are some additional points to consider:

**Sorting by end time is a greedy algorithm**. This means that it makes the best possible choice at each step, without considering the future. As a result, it is usually more efficient than sorting by start time.  
**Sorting by start time is a dynamic programming algorithm**. This means that it makes a choice at each step, based on the choices that it has made in the past. As a result, it is usually more robust to errors in the input data.  We can either sort by start time and run a dp from the end or sort by end time and run a dp from the start.


##### Custom Comparator in Upper bound
In the upper bound function if we are doing a Binary search in a multidimensional vector, we don't need to provide other dimension default values, and instead can have a comparator.
```cpp
```cpp
[&](const int &target, const vector<int>&a) -> bool {
                return target < a[1];
            }
```
```

Like this. This comparator returns the first element greater than target.