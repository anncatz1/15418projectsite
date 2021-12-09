# Exploring Concurrent Union-Find
Members: Annie Xu, Henry Liu

[Proposal](https://anncatz1.github.io/15418projectsite/proposal)

[Milestone Report](https://anncatz1.github.io/15418projectsite/midreport)

[Final Report](https://anncatz1.github.io/15418projectsite/finalreport)

### Project Summary
We will implement 3 versions of the Union-Find (Disjoint Set Union) data structure:
1. Serial
2. Coarse-Grained Locking
3. Lock-Free

We will compare the performances of these implementations against a variety of test inputs and usage scenarios to demonstrate the application of and motivation behind Lock-Free Union-Find. 

### BACKGROUND: 
Union-Find is a tree-based data structure that allows for the efficient querying of connected components within an undirected graph. A connected component is a group of nodes such that every node can reach every other node by traversing a series of edges.

Union-Find uses two basic operations: 
- Find: determines which component a node is in
- Union: joins two connected components into a single component. 

In a naive implementation of Union-Find, the worst case time complexity of both Union and Find are O(N) with N being the number of nodes in the graph. There exists several measures to improve the amortized complexities of these operations:
- Union by Rank/Size: when uniting components, connect the smaller one to the larger one to avoid degenerate tree structures 
- Path Compression: intermediate links along a path from a node to the root of its component are extraneous. We can directly link the nodes to their roots as we find them. 
- Path Splitting: updates the parent links during the traversal to the root by updating the parent link to point to the grandparent of the current node. This compresses the paths by 1.

Through the use of these techniques, we can improve the time complexity of both operations to scale with the inverse ackermann function: O(α(N)), (which is nearly constant).

Many famous algorithms depend on an efficient implementation of Union-Find. There are several interesting applications of Union-Find which we hope to delve into, including:
- Cycle Detection in Undirected Graphs
- Kruskal’s Algorithm for Minimum-Spanning-Tree
- Tarjan’s Offline Algorithm for Lowest Common Ancestor
- Network Connectivity
