

#### Ignoring disconnected components
In a graph problem always account for the fact that there could be multiple graphs which are disconnected from each other.


### Finding the max path length from a node using BFS without filling up the queue much
```cpp
```cpp
    int getLongestShortestPath(vector<vector<int>> &adjList, int srcNode) {
        // Initialize a queue for BFS and a visited array
        int n = adjList.size();
        queue<int> nodesQueue;
        vector<bool> visited(n, false);

        nodesQueue.push(srcNode);
        visited[srcNode] = true;
        int distance = 0;

        // Perform BFS layer by layer
        while (!nodesQueue.empty()) {
            // Process all nodes in the current layer
            int numOfNodesInLayer = nodesQueue.size();
            for (int i = 0; i < numOfNodesInLayer; i++) {
                int currentNode = nodesQueue.front();
                nodesQueue.pop();

                // Visit all unvisited neighbors of the current node
                for (int neighbor : adjList[currentNode]) {
                    if (visited[neighbor]) continue;
                    visited[neighbor] = true;
                    nodesQueue.push(neighbor);
                }
            }
            // Increment the distance for each layer
            distance++;
        }
        // Return the total distance (longest shortest path)
        return distance;
    }
```

While pushing to the queue we peel layer by layer thus once we process one layer completely we only process the other one. This ensures that the number of elements pushed to the queue max is the width of a particular level. This is useful method of level order traversal in the array. This also allows us to replace a queue with a vector or array.

### Finding all paths with the minimum path length

Backtracking + BFS
Create a graph using the nodes built from the BFS and then backtrack to create all the paths. The BFS graph ensures that whenever we reach the target word we 

### BFS through each node.
BFS through each node would be O(N) *  O(N + M) in complexity not M^2. Be careful.


#### Going through multiple cycles
DFS is great for detecting cycles. It might be best even to detect the number of cycles but to detect the shortest cycle it would fail as dfs can ignore a shorter cycle for a longer one and cover the same number of nodes.


#### Time complexity of exploring all paths from 0 to n-1.
Using induction the time complexity for exploring all paths is O(2^N) * O(N)



### BFS/DFS for djikstra
If we want to find the shortest path using BFS, we can do that using bfs from src and then not marking the nodes as visited because they can be visited again from preferrably a shorter path. Thus the time complexity of such an algorithm would be O(N + E*maxRelaxation(node)). This is useful in solving problems where we want to find the minimum distance in k nodes or earlier.
Here the complexity is bounded by K ie O(N + E.K). For each new node we iterate the edge again if the distance we get now is smaller than the distance previously explored.


### Convert Multiplication to Log
Just what the title says.


### Kth smallest element in BST
For frequent updates and deletes we can maintain a doubly linked list, or we can use an order statistic tree

### Topo Sort
The BFS method is simpler in terms of complex problems of topo sort compared to DFS.


### DSU
1. [Evaluate Division](https://leetcode.com/problems/evaluate-division/)


