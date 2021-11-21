# Exploring Concurrent Union-Find
Members: Annie Xu, Henry Liu

[Proposal](https://anncatz1.github.io/15418projectsite/proposal)

[Milestone Report](https://anncatz1.github.io/15418projectsite/midreport)

### Project Summary
We will implement 3 versions of the Union-Find (Disjoint Set Union) data structure:
1. Serial
2. Coarse-Grained Locking
3. Lock-Free

We will compare the performances of these implementations against a variety of test inputs and usage scenarios to demonstrate the application of and motivation behind Lock-Free Union-Find. 

### Schedule 

Week 1: Oct 31 - Nov 6
- **Due: Proposal due Nov 3rd**
- Implement sequential, come up with test case ideas 

Week 2: Nov 7 - Nov 13
- Create test case repository
- Implement Coarse-grain locking version

Week 3: Nov 14 - Nov 20
- Implement Lock-free (draft)

Week 4 First Half: Nov 21 - Nov 24
- **Due: Project Checkpoint Nov 22**
- Test sequential/coarse-grain on the test cases (Henry)
- Check correctness of lock-free version (Annie)

Week 4 Second Half: Nov 24 - Nov 27
- Test lock-free version (Henry)
- Decide what other versions of locking/lock-free we will test (different path compression techniques, rank by size) (Both)
- Write first draft of paper (Annie)

Week 5 First Half: Nov 28 - Dec 1
- Finish writing other algorithm versions of union-find (Both)

Week 5 Second Half: Dec 1 - Dec 4
- Testing other algorithm versions of union-find (Henry)
- Work on paper (Annie/Both)

Week 6 First Half: Dec 5 - Dec 11
- Finish paper (Annie/Both)
- Make draft of poster (Henry)

Week 6 Second Half: Dec 5 - Dec 11
- Finish paper and poster (Both)
- **Due: Final project report Dec 9**
- **Due: Poster session Dec 10**
