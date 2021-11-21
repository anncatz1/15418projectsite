# Exploring Concurrent Union-Find
Members: Annie Xu, Henry Liu

[Proposal](https://anncatz1.github.io/15418projectsite/proposal)

[Milestone Report](https://anncatz1.github.io/15418projectsite/midreport)

### SUMMARY OF PROJECT
We will implement 4 versions of the Union-Find (Disjoint Set Union) data structure:
1. Serial
2. Coarse-Grained Locking
3. Fine-Grained Locking
4. Lock-Free

We will compare the performances of these implementations against a variety of test inputs and usage scenarios to demonstrate the application of and motivation behind Lock-Free Union-Find. 

### SCHEDULE: 

Week 1: Oct 31 - Nov 6
- **Due: Proposal due Nov 3rd**
- Implement sequential, come up with test case ideas 

Week 2: Nov 7 - Nov 13
- Create test case repository
- Implement Coarse-grain locking version

Week 3: Nov 14 - Nov 20
- Implement Lock-free (draft)
- Test sequential/coarse-grain on the test cases

Week 4: Nov 21 - Nov 27
- **Due: Project Checkpoint Nov 22**
- Make sure lock-free is correct, finish testing lock-free/other vers

Week 5: Nov 28 - Dec 4
- Write paper

Week 6: Dec 5 - Dec 11
- Finish paper, make poster
- **Due: Final project report Dec 9**
- **Due: Poster session Dec 10**
