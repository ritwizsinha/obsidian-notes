1. [Prefix and Suffix Search](https://leetcode.com/problems/prefix-and-suffix-search/)
   Finding prefix and suffix simultaneously
2. [Maximum Strong Pair XOR II](https://leetcode.com/problems/maximum-strong-pair-xor-ii/)







### Learnings
1. For finding prefixes and suffixes we can basically create a concatenation like suf + '{' + pref.
2. Maximum Xor can be found in an array of two elements using the XOR Trie.
3. Any equations of the form |nums[i] - nums[j]| <= min(nums[i], nums[j]) can be rewritten in the form of a sliding window or binary search.