---
layout: default
title: "Final Report"
permalink: /finalreport/
filename: finalReport.md
---
# Exploring Concurrent Union-Find
Members: Annie Xu, Henry Liu

[Home](https://anncatz1.github.io/15418projectsite/)

[Proposal](https://anncatz1.github.io/15418projectsite/proposal)

[Milestone Report](https://anncatz1.github.io/15418projectsite/midreport)

## Final Paper

### ABSTRACT: 

We implemented five versions of the Union-Find (Disjoint Set Union) data structure: serial, coarse-grained locking, fine-grained locking, lock-free, and software transactional memory. We compared the performance of these implementations against a variety of test inputs and problem tasks to demonstrate the application of and motivation behind Lock-Free Union-Find. We produced performance graphs of different problem sizes of the three problem tasks we used. 

### BACKGROUND: 

Union-Find is a tree-based data structure that allows for the efficient querying of connected components within an undirected graph. A connected component is a set of nodes such that every node can reach every other node by traversing a series of edges.
Union-Find consists of two basic operations. find(), which determines which component a node is in, and unite(), which joins two connected components into a single component. Additionally, some implementations support fast querying of remaining components, deletion of nodes, and various other tricks.
In a naive implementation of Union-Find, the worst case time complexity of both union() and find() are O(N) with N being the number of nodes in the graph. However, there exists a variety of methods to vastly improve on these bounds, including union-by-rank, path compression, and more. 
Our goal is to produce a fast, correct, and thread-safe implementation of Union-Find and test its limits against a suite of tasks.

#### *Data Structure*
Our general data structure involves trees to represent the connected components, while the root of each tree acts as the representative of the identity for the component. To perform a find(), we will traverse upwards through the tree to track down the root of the component. To unite() a pair of sets, we attach the root of one set to the root of the other, resulting in a larger merged tree. The trees may be implemented via a parent array, with -1’s to indicate roots.

#### *Optimizations*
In order to support some of the efficiency measures down the line, it’s also crucial to keep track of the rank of each set, or the height of the trees. Traditionally, this is accomplished with a separate array. However, we have made a small optimization here in the memory representation – because we only need the ranks of full trees and not subtrees, we can store the ranks within our parent array at each root as negative integers to differentiate it from the positive indices at other nodes. By using a single array to maintain this information, we can reduce the number of total memory accesses and improve the locality of our data structures. The main axis of optimization is in controlling our tree structure.

**Union-by-rank:** always connect shorter trees to taller ones. Since the time complexity of find() and union() are bounded by the height of the trees, it follows that we should take measures to control the growth in height of our trees. By connecting smaller trees to taller ones, we have not created any taller trees in this union event! Union-by-rank allows us to avoid degenerate tree structures and facilitates a fast average traversal on find() operations.

**Union-by-size:** this method performs a similar comparison over the number of nodes in a set as opposed to the height of the trees. In this case, however, maintaining the updated sizes will require additional memory operations on each unite(). In contrast, union-by-rank only updates when the two sets are of equal height. This results in a visible performance improvement over union-by-size.

- **Path compaction** is another important heuristic. It’s easy to recognize that the intermediate links along a path from a node to the root of its component are extraneous. If we can shorten the path between the nodes and roots, our find() operation will be much faster. 
- **Path Compression** involves directly linking the nodes to their roots as we find them. 
- **Path Splitting** involves updating the parent links during the traversal to the root by updating the parent link to point to the grandparent of the current node. This compresses the paths by one. Path Halving is similar to path splitting, however, each node is directly linked to its own grandparent. This means we’ll only traverse and update half of the elements as we go from the node to the root. 
- **Path compaction** ensures O(log n) amortized cost of Find while linking by priority ensures O(log n) worst-case cost. Through the use of both linking by priority and path compaction techniques, we can improve the time complexity of union-find to scale with the inverse ackermann function: O(α(N)), (which is nearly constant).

#### *Parallelism*
In terms of the parallelism for Union-Find, we ideally will be able to operate on any independent components in parallel (so two groups of independent connected components should be able to unite simultaneously without issue). Without any path compaction optimizations, we can do all the find operations in parallel as they are simply reads. If we implement path compaction, we must take care that when we update the links between nodes updates are not lost. Additionally, if we use path compaction along with priority linking by rank, we need to be able to be sure to simultaneously update the rank of a node along with changing the link to the parent. We cannot allow two different operations to occur on the same node where one changes the parent and a different one changes the rank, thus breaking correctness. For uniting, we also need to ensure the modifying of one root to point to the other occurs atomically. 
Memory accesses and synchronization costs are the most likely reasons for poor performance, given the simple nature of most algorithms that use union-find. The cost of computation for graphs is very small compared to the cost of memory traversals. Reducing cache misses is the most critical factor for performance of existing algorithms. We also want to limit the total amount of memory accessed per operation. Additionally, since graphs are so large, there is not a lot of locality in the memory accessed. We can increase locality with the methods of path compression. 
We will examine how use of atomic operations plays into things as well. Contention over accesses to the same nodes is fairly limited for most graph problems, so there will be less probability of having to retry CAS operations to access data. 

### *Tasks*

We tested our data structures on the following 3 tasks:

Task: COUNT

Description: find # of connected components in graph

Input:
N M     <-- N nodes, M edges
a1 b1   <-- edge from a1 to b1
... 
aM bM

Output: single int, # of connected components

---------------------------------------------------------------

Task: QUERIES

Description: answer connectivity queries in a graph

Input:
N M Q   <-- N nodes, M edges, Q queries
a1 b1   <-- edge from a1 to b1
...
aM bM
x1 y1   <-- query: is x1 connected to y1?
...
xQ yQ

Output: bool[Q], answers to all queries

---------------------------------------------------------------

Task: WORMSORT

Description: maximize width of smallest wormhole cows must use
in order to sort themselves. Full problem statement available:
http://www.usaco.org/index.php?page=viewproblem2&cpid=992

Input:
N M   	  <-- N cows, M wormholes
p1 ... pN   <-- initial permutation
a1 b1 w1    <-- wormhole from a1 to b1, width w1
...
aM bM wM

Output: single int, largest minimum width of wormhole to sort

### APPROACH:

We touched on a portion of the C++ standard library’s parallelism support in class, but didn’t have a chance to experiment with it. So, we decided to implement the entire project in pure C++, heavily utilizing the <atomic>, <mutex> and <thread> headers. We modeled our base code off of the paper “In Search of the Fastest Concurrent Union-Find Algorithm” by Alistarh, Federov, Koval. We performed testing on various machines, but our final results are based on a 16-in MacBook Pro 2019, with 2.6 GHz 6-Core Intel Core i7, with 12 hardware contexts.
As above, we represent our union-find data structures with a vector<int>. Each node is represented at an index of the array, and the element at that index represents the parents of the node. If the node is a root of the tree, then we do not have a parent and instead record the rank of that set as a negative number. When we use union-by-rank, the (absolute value) of the number in the “parent” array for any root node is the rank at the root (or height of the tree). 

#### *Sequential*
	
We use the sequential data structure as a reference to verify the correctness of our later implementations. Since it’s sequential, we can easily keep track of how many components remain with a single integer. It uses union-by-rank and path compression:

#### *Coarse-Grained Locking*
	
In our coarse-grained locking algorithm, we keep a single mutex for the entire data structure, and we simply wrap each mutating function with an std::lock_guard, which ensures that the mutex is released when the guard goes out of scope. The algorithms are otherwise identical to the sequential implementation. 

#### *Fine-Grained Locking*

We obtained the basis for a fine-grain locking algorithm from the paper “Multicore Programming in the Face of Metamorphosis: Union-Find as an Example” by Igor Berman. This uses an “optimistic” locking approach which uses fine grained locks to coordinate any mutating operations, but there are also non-mutating operations that traverse the data structure and are lock-free. There is one lock per component i.e. per tree. The lock is at the root of the tree. When we merge multiple components together, the locks at the leaves and inner nodes become inactive and only the lock at the root remains in service. Thus, when we want to unite, we obtain the locks of the roots of the two node’s components before we mutate the trees. To avoid deadlock, we ensure that the locks are acquired in the same order (by memory address) each time. It is possible that in between traversing to the roots and obtaining the locks, other threads have done unites on those components and changed a root to be an inner node, so we must be sure that they are still roots after we obtain the lock for correctness. Once we obtain the locks on the roots, we do the same uniting steps as before, and then release the two locks on the roots. 
	
Here, find() is implemented as a lock-free function. This is so we can improve the speed of doing any finds while also maintaining correct concurrency. While the node is not a root, we use a CompareAndSwap primitive for path-halving. We do a CAS on the parent of the node, and we replace the node’s parent with the node’s grandparent. We then set the node equal to its grandparent. Due to this change, we now store A as a vector<atomic<int>> which supports the atomic CAS operations.

#### *Lock-Free*

For our find function, we take as input a node and we want to find the root. We check at the beginning of the function if the parent of the node is negative, which means it's a root, and then we return the node and its rank if that is true. We also implemented the immediate parent check optimization. This is due to the fact that it is likely that two elements that are being united have the same parent later on in the algorithm after the paths have been compressed a lot. This optimization checks whether the parent of u and v are equal in the beginning of the find operation and returns node and rank if true. This allows us to skip the more time-consuming operations if this is true. After this, we recursively call find on the parent so we go up the tree by one. We always return both the root and rank at once so they stay consistent. We also include path compression. This was described above, but again we use a CAS (in order to ensure that two threads are not concurrently updating the node) to update the parent of the original input node to point to the root. This enables faster finds in the future. 

The sameSet function is the same idea as before, it just adds a check on if the root returned from the find is actually still a root - this will check if other threads have united either of the two nodes’ components in the midst of this function. If it’s not still a root, then we return false and we try the finds again to find the correct root.
	
In our unite function, we do two finds on the two input nodes which give us the roots and the ranks of the roots. We check if they’re already in the same component, and if not, then we check which node’s rank is higher. If rank of u is smaller than rank of v, this means we want to make v the master root of the united component and we use a CAS in order to change u’s parent to be v. If rank of u is bigger than rank of v, then we do the opposite. If the ranks are equal, we then try to unite based on the index of the elements. If u < v, then we want to put u into v’s structure (u’s parent will point to v) and vice versa. Our array will return a rank if the node is a root, and we use a CAS on this number to ensure that the rank of the node is still consistent. If it is, then we change this array so that it is v instead of a rank (parent of u is now v). We do a second CAS to change the rank of v (decrementing it to “increase” the height since it’s negative). The CAS checks on whether the rank of v is still consistent. If the first CAS fails, we do not do the second CAS and will retry in the while loop. Here again, we store A as a vector<atomic<int>> to support CAS operations.
	
#### *Software Transactional Memory*

This variant was a bonus element. C++ has some built-in support for software transactional memory via the -fgnu-tm flag. To implement the data structure, we simply wrapped each mutating function in a synchronized block. Unfortunately, this support in C++ is still experimental and unoptimized, and our preliminary testing showed that though it was correct, its performance was noticeably worse than naive coarse-grained locking. As a result, we have excluded it from the rest of our results.

#### Task: COUNT
	
This is a very simple task. We can simply fire up all our threads and interleave them through the edges array. For the sequential and coarse-grained variants, we can easily keep track of the remaining component count with an integer. For the lock-free and fine-grained variants, there are ways to optimize the count (such as running a parallel scan over the parent array) however the bulk of the computation is within the union operations, and optimizing the count would not yield much performance gain. Instead, we count the number of negative values in the parent array.

#### Task: QUERIES
	
Here, we can parallelize over all of the edge unions, then synchronize. Afterwards, we can parallelize over the queries. Again, this was a simpler task aimed at ensuring correctness across a large number of queries.

#### Task: WORMSORT
	
The first important reduction is that we can interpret the scenario as a graph. Wormholes are weighted edges that connect nodes. Then, to tell if the cows can be sorted, we can check if the cow at each position is connected to its correct position. The second key is that the answer is monotonic: if we can sort the cows with min width X, we must be able to sort the cows with any Y < X. Thus, we can perform a sequential binary search over the answer, and a parallel verification of each guess. We can initially sort the wormholes by weight. Then, to verify a guess g, we can unite all edges with weights >= g and see if the sorting condition above holds true. If so, we can narrow our search space lower, and if not, we know to look higher. 

### RESULTS: 

We produced graphs of the performance on three different problem sizes for each of our three tasks and included change in performance as thread count changes. As expected, coarse-grained performed the worst out of the three (coarse-grained, fine-grained, lock-free). This would be due to essentially serializing the operations one can perform on the data structure as only one thread can operate at a time. Coarse-grain also suffers from the synchronization time of obtaining and releasing locks. Fine-grained performed better on average than coarse-grained, and lock-free performed the best out of the three. Comparing the performance of the sequential algorithm, we actually found that sequential was better than both fine-grained and coarse-grained locking. We reasoned that the overhead of taking and releasing locks was very high and there may be high contention for the fine-grained locking scheme. The lock-free implementation, however, achieved up to 3x speedup over sequential on certain tasks, indicating that using a lock-free implementation for concurrency is beneficial. 

As we can see from the three different problem sizes, if the problem is too small there is no benefit to using more thread counts at all. For the medium and big problem sizes, we see a general trend for fine-grained and lock-free that the performance improves as thread count increases. However, for coarse-grained, the performance worsens as thread count increases. This is due to the fact that only one thread can operate at a time on the union-find structure so with more threads there’s just more threads waiting to operate and no performance benefit. For some of the tasks/problem sizes, we see that using 12 threads is a little worse than the sweet spot of using 8-10 threads for both fine-grained/lock-free. This may be due to our problems not being big enough to really see the benefit using more and more thread counts. 

#### Count Problem Sizes
- Small: Vertices: 10, Edges: 5
- Medium: Vertices: 72910, Edges: 291620
- Big: Vertices: 337510, Edges: 1350020
	
As we can see here, the performance on the very small graph just gets worse as thread count increases. On the biggest graph, we see as thread count goes up, coarse-grained has worse performance, fine-grained has better performance, and lock-free also has slightly better performance but not as much difference. On medium size graphs, the same occurs, except for lock-free we get worse performance on 12 count threads.  

#### Wormsort Problem Sizes 
- Small: Cows: 4, Wormholes: 4
- MediumSmall: Cows: 500, Wormholes: 1000
- MediumBig: Cows: 10000, Wormholes: 100000
- Big: Cows: 50000, Wormholes: 100000
	
Again, performance on the very small problem gets worse as thread count increases. On the biggest size problem, we see fine-grained and coarse-grained have similar performance on 2 threads, but as thread count goes up, coarse-grained gets worse, while fine-grained gets only slightly better. Lock-free also only has slightly better performance. On the medium-big problem, the same occurs, except coarse-grained has a drastically different performance decrease. On the medium-small graph, performance is wildly erratic. 

#### Queries Problem Sizes 
- Small: Vertices:  10, Edges: 5, Queries: 5
- Medium: Vertices: 51210, Edges: 204820, Queries: 102410
- Big: Vertices: 337510, Edges: 1350020, Queries: 675010
	
For the queries problem, performance on the very small problem jumps around slightly. It seems the best performance for all methods is 4 threads, with 6 threads as the second best and 2 and 4 threads are relatively similar. It creates a kind of U-shape. This is likely due to more threads providing slight performance benefit with likely low contention but too many threads are ineffective as there’s too much contention, and two threads not giving enough parallelism. On the biggest size problem, we see as thread count goes up, coarse-grained has increasingly worse performance, and fine-grained and lock-free have increasingly better performance. On the medium size problem, the same occurs, except coarse-grained has more of a sharp decline in performance at 6 threads.  

### REFERENCES: 
Alistarh, D., Fedorov, A., & Koval, N. (2019). In Search of the Fastest Concurrent Union-Find Algorithm. arXiv preprint arXiv:1911.06347.
Anderson, R. J., & Woll, H. (1991, January). Wait-free parallel algorithms for the union-find problem. In Proceedings of the twenty-third annual ACM symposium on Theory of computing (pp. 370-380).
Berman, I. (2010). Multicore programming in the face of metamorphosis: Union-find as an example. University of Tel-Aviv.
