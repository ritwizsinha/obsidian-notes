
### Backtracking
Most Backtracking solutions get optimized, when we move from iterating through all possiblities from a state in O(n) or O(n^2) to just the next possible state.
1. [1718. Construct the Lexicographically Largest Valid Sequence](https://leetcode.com/problems/construct-the-lexicographically-largest-valid-sequence/)

### Casework
1. [Can Place Flowers](https://leetcode.com/problems/can-place-flowers/)
2. [Long Pressed Name](https://leetcode.com/problems/long-pressed-name/)
3. [Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)
4. [Decode String](https://leetcode.com/problems/decode-string/)
5. [Serialize and Deserialize N-ary Tree](https://leetcode.com/problems/serialize-and-deserialize-n-ary-tree/)
6. [Next Permutation](https://leetcode.com/problems/next-permutation/)
7. [Roman to Integer](https://leetcode.com/problems/roman-to-integer/)
8. [Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)
9. [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)
10.  [Exclusive Time of Functions](https://leetcode.com/problems/exclusive-time-of-functions/) 


### Kth Selection and QuickSeelct
1. https://leetcode.com/problems/k-closest-points-to-origin/description/?envType=company&envId=linkedin&favoriteSlug=linkedin-all
2. [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

### Intricacies of DFS
3. [Find Leaves of Binary Tree](https://leetcode.com/problems/find-leaves-of-binary-tree/)


### Useful Data Structure
4. [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/). A structure which can calculate the max and min occuring strings and their frequency in O(1)
5. [716. Max Stack](https://leetcode.com/problems/max-stack/)
	A structure to get the most recent element in O(1) time and maintain removal and addition in log(N) time.
3.[Insert Delete GetRandom O(1) - Duplicates allowed](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed/)  Insert/Delete/GetRandom O(1).



### Iterating over all possible states bitmask
6. [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)


### Binary Search
1. [Kth Smallest Product of Two Sorted Arrays](https://leetcode.com/problems/kth-smallest-product-of-two-sorted-arrays/)
2. [House Robber IV](https://leetcode.com/problems/house-robber-iv/)
3. [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)


### Tips
1. In Bipartite, no need for a visited array, the color array can serve as the visited array
2. Remember the logarithm trick for overflowing products.
3. If a Binary Tree gives a sorted inorder, it doesn't mean that it is a BST. Eg
	1. 10 5  20 null null 15
4. For x and y as coordinates and if we need to cache that we can do to_string(x) + '-' + to_string(y)
5. All kth distance problems can be solved using quick select in O(N) time instead of priority queue which takes O(Nlogk)
6. Read up on the documentation of list
7. The comparator logic for a priority queue is inverted as to normal logic. So if a < b gets a forward in a array, for creating a min heap we have to do a > b.
8. Revise Djikstra


#### BST and Linked List
1. Casework  [Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)
2. [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
3. [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
4.  [Reorder List](https://leetcode.com/problems/reorder-list/)
5. [Two Sum IV - Input is a BST](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/)
6. [Same Tree](https://leetcode.com/problems/same-tree/)
7. [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
8.  [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
9. [Closest Leaf in a Binary Tree](https://leetcode.com/problems/closest-leaf-in-a-binary-tree/)
10. [Binary Tree Upside Down](https://leetcode.com/problems/binary-tree-upside-down/)
11. [Rotate List](https://leetcode.com/problems/rotate-list/)
12. [Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

#### Time complexity
1. [Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)
2. [Accounts Merge](https://leetcode.com/problems/accounts-merge/)
3. [Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)
4. [H-Index](https://leetcode.com/problems/h-index/) 
5. [Combination Sum](https://leetcode.com/problems/combination-sum/)
6. [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
7.  [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
8. [Number of Islands](https://leetcode.com/problems/number-of-islands/)
9. [Word Ladder](https://leetcode.com/problems/word-ladder/)
10. [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)



#### BFS
1. [Map of Highest Peak](https://leetcode.com/problems/map-of-highest-peak/)
2. [Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)
3. [Minimum Knight Moves](https://leetcode.com/problems/minimum-knight-moves/)
### Stack ka Pachra
1. [Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/)


#### KMP and Rabin Karp and Menacher
1. [ind the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)
2.  [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

#### Backtracking
1. [Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)
2. [Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)       
3. [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)


### Learn
1. [Implement Rand10() Using Rand7()](https://leetcode.com/problems/implement-rand10-using-rand7/)
2. [Bulb Switcher](https://leetcode.com/problems/bulb-switcher/)
3. [Number of Divisible Triplet Sums](https://leetcode.com/problems/number-of-divisible-triplet-sums/)
4. [Robot Bounded In Circle](https://leetcode.com/problems/robot-bounded-in-circle/)
5. [Can I Win](https://leetcode.com/problems/can-i-win/)
6. [Kth Smallest Product of Two Sorted Arrays](https://leetcode.com/problems/kth-smallest-product-of-two-sorted-arrays/) 
7. [Sort Transformed Array](https://leetcode.com/problems/sort-transformed-array/)
8. [Valid Number](https://leetcode.com/problems/valid-number/)


#### Data Structure
1. [Min Stack](https://leetcode.com/problems/min-stack/)
2. 

### Interview Experience

#### Design
1. We have catalog events coming at Google scale. We need to enhance the catalog by adding extra fields and use this data for analytics. Design a system for this 3
2. Design a distributed job scheduler. You are given an API to fetch machines for running jobs. Design a system that schedules jobs, executes them, and triggers alerts 2
3. _Design Top K system.  
4. Metrics monitoring system