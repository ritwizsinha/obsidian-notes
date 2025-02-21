#### O(1) Insertion deletion and get random element in O(1).
The thing I missed here is the trick of removing elements from a vector in O(1) which is swapping the element we need to remove with the last element and then pop_back

#### Insertion in O(1) and get random element in O(1).
We don't even need to maintain a vector or list in this case. What we can do is maintain a count of elements and store a variable random_value. When ran for the first time it will store the first value. After that it will replace the value with a probability of (1 / total_number_of_elements). This would then give an equal probability of any of the value seen so far given all values are unique.

### Using Linked List
Cpp has the item list which supports insertion and deletion in O(1). We don't get random access but front and end access is very fast.
