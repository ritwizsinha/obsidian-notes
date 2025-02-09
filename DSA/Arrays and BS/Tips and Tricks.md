
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