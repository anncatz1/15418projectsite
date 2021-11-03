# Exploring Concurrent Union-Find
## Project Proposal
Members: Annie Xu, Henry Liu

URL: [anncatz1.github.io/15418projectsite](https://anncatz1.github.io/15418projectsite/)

### SUMMARY
We will implement 4 versions of the Union-Find (Disjoint Set Union) data structure:
Serial
Coarse-Grained Locking
Fine-Grained Locking
Lock-Free

We will compare the performances of these implementations against a variety of test inputs and usage scenarios to demonstrate the application of and motivation behind Lock-Free Union-Find. 

### BACKGROUND
Union-Find is a tree-based data structure that allows for the efficient querying of connected components within an undirected graph. A connected component is a group of nodes such that every node can reach every other node by traversing a series of edges.

Union-Find uses two basic operations: 
Find, which determines which component a node is in
Union: which joins two connected components into a single component. 

In a naive implementation of Union-Find, the worst case time complexity of both Union and Find are O(N) with N being the number of nodes in the graph.
There exists several measures to improve the amortized complexities of these operations:
Union by Rank/Size: when uniting components, connect the smaller one to the larger one to avoid degenerate tree structures 
Path Compression: intermediate links along a path from a node to the root of its component are extraneous. We can directly link the nodes to their roots as we find them. 
Path Splitting:
Path Halving:

Through the use of these techniques, we can improve the time complexity of both operations to scale with the inverse ackermann function: O(α(N)), (which is nearly constant).

Many famous algorithms depend on an efficient implementation of Union-Find. There are several interesting applications of Union-Find, which we hope to reveal:
Cycle Detection in Undirected Graphs
Kruskal’s Algorithm for Minimum-Spanning-Tree
Tarjan’s Offline Algorithm for Lowest Common Ancestor
Network Connectivity

### THE CHALLENGE
Describe why the problem is challenging. What aspects of the problem might make it difficult to parallelize? In other words, what do you hope to learn by doing the project? 

• Describe the workload: what are the dependencies, what are its memory access characteristics? (is there locality? is there a high communication to computation ratio?), is there divergent execution? 
Memory access and synchronization costs will be the most likely reason for poor performance, given the simple nature of most algorithms that use union-find. We want to limit the total amount of memory used and accessed per operation. Additionally, reducing the cache misses was deemed to be the most critical factor for performance of existing algorithms. The computation cost for graphs is fairly small compared to the cost of memory traversals. Contention is fairly limited for most scenarios. 
• Describe constraints: What are the properties of the system that make mapping the workload to it challenging? 

### RESOURCES
We have several research papers that analyze different algorithms for different concurrency methods for union find. The first paper “In Search of the Fastest Concurrent Union-Find Algorithm” by Alistarh, Federov, Koval describes optimizing sequential implementations using different linking strategies between nodes (to determine the parent of each node) and various methods of path compaction. In addition, they provide pseudo-code for concurrent union find with Boruvka’s algorithm using priority ranking and path compression. We will utilize this pseudo-code as a starting point to write our code. In addition, the other papers describe lock-free implementations with several different algorithms - they include pseudo-code as well. 

“In Search of the Fastest Concurrent Union-Find Algorithm” by Alistarh, Federov, Koval: https://drops.dagstuhl.de/opus/volltexte/2020/11801/pdf/LIPIcs-OPODIS-2019-15.pdf.

“A Lock-Free Implementation of Union-Find” by Maroš Tkáčik: https://is.muni.cz/th/kpdej/BakalarkaDigital.pdf

“Wait-free Parallel Algorithms for the Union–Find Problem” by Anderson and Woll: 
https://dl.acm.org/doi/pdf/10.1145/103418.103458?casa_token=lOG4EgnOJ7UAAAAA:AVK_CpoiZc8kYaXNPY9hXl3faTqzyNOKxASFH1wfMOVMbYYE-Vpxecxfc6eg1dNTsZSCx9OJCwsf

“An Optimized Union-Find Algorithm for Connected Components Labeling Using GPUs”
https://arxiv.org/pdf/1708.08180.pdf

### GOALS AND DELIVERABLES
Plan to achieve: We plan to implement different versions of union-find and run enough performance measurements on different test cases in order to find the fastest algorithm/implementations. We will also try to discover whether it is worthwhile to use the lock-free version of union-find given the trade-offs in performance.
We plan to write at least 1 version for sequential, coarse-grained locking, fine-grained locking, and lock-free union-find in order to compare performance between them. If we have time, we can explore multiple different algorithms and optimizations. 
Hope to achieve: Concurrent Union-find with delete, CUDA union-find

For the poster session, we will show performance speedup graphs.
We are hoping to learn which of the four methods (lock free, coarse-grain locking, fine-grain locking, and sequential) are the best for concurrent performance. In addition, we hope to learn even more about using very different algorithms/implementations and optimizations of union-find to see which is the best. 

### PLATFORM CHOICE
We will implement the 4 main versions, and the testing code all in C++. Also, we will use CUDA to write a GPU-based union find, if we get around to it.

### SCHEDULE: 

Week 1: Oct 31 - Nov 6
- **Due: Proposal due Nov 3rd**
- Come up with test cases 
- Test sequential on the test cases
- Implement coarse-grained and test 

Week 2: Nov 7 - Nov 13
- Do Fine-grained 

Week 3: Nov 14 - Nov 20
- Do Lock-free

Week 4: Nov 21 - Nov 27
- **Due: Project Checkpoint Nov 22**
- Finish testing 

Week 5: Nov 28 - Dec 4
- Write paper

Week 6: Dec 5 - Dec 11
- **Due: Final project report Dec 9**
- **Due: Poster session Dec 10**
