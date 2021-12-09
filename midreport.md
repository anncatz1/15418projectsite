---
layout: default
title: "Milestone Report"
permalink: /midreport/
---
# Exploring Concurrent Union-Find
Members: Annie Xu, Henry Liu

[Home](https://anncatz1.github.io/15418projectsite/)

[Proposal](https://anncatz1.github.io/15418projectsite/proposal)

[Final Report](https://anncatz1.github.io/15418projectsite/finalreport)

## Milestone Report

### Work Completed Currently

We have written code for the sequential version, the coarse-grain locking version, and lock-free version of union-find. We have done extensive research on these different versions and different algorithm changes to these versions we can make that we will intend to implement in code to explore performance differences later. We are currently testing these different versions of union-find to see how performance differs especially across our different kinds of test cases. 

### Goals
I believe that we can produce our deliverables, except we have changed our plan to not include a fine-grain version for union-find because it doesn’t quite make sense for union-find. We are currently working on a working version of lock-free union-find, and we have all our testing material ready and are currently working on comparing the different performances of the sequential/coarse-grain/lock-free versions. We definitely plan to complete this for the poster session. Additionally, we are optimistic that we can finish this and move onto looking at different algorithmic versions/optimizations of lock-free/coarse-grain locking - these are the “nice to haves”. These involve different methods of path compression, using rank by size or not, etc. We do not believe we can complete the stretch goals we identified in the proposal anymore given the time constraints. We also were unable to work for a good portion of last week since one of our members was sick. 

### Poster Session

We will show graphs comparing the different versions of union-find and their performance on different kinds of test cases. We will include how we implemented these different versions. 

### Issues
We are a little worried about completing the coding work on time given how quickly the end of the semester is approaching but otherwise we don’t see any issues. 

### New Schedule

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
