---
layout: default
title: "Milestone Report"
permalink: /midreport/
---
# Exploring Concurrent Union-Find
## Milestone Report
Members: Annie Xu, Henry Liu

[Home](https://anncatz1.github.io/15418projectsite/)

[Proposal](https://anncatz1.github.io/15418projectsite/proposal)

### Work Completed Currently

We have written code for the sequential version, the coarse-grain locking version, and lock-free version of union-find. We have done extensive research on these different versions and different algorithm changes to these versions we can make that we will intend to implement in code to explore performance differences later. We are currently testing these different versions of union-find to see how performance differs especially across our different kinds of test cases. 

### Goals
I believe that we can produce our deliverables, except we have changed our plan to not include a fine-grain version for union-find because it doesn’t quite make sense for union-find. We are currently working on a working version of lock-free union-find, and we have all our testing material ready and are currently working on comparing the different performances of the sequential/coarse-grain/lock-free versions. We definitely plan to complete this for the poster session. Additionally, we are optimistic that we can finish this and move onto looking at different algorithmic versions/optimizations of lock-free/coarse-grain locking - these are the “nice to haves”. These involve different methods of path compression, using rank by size or not, etc. We do not believe we can complete the stretch goals we identified in the proposal anymore given the time constraints. We also were unable to work for a good portion of last week since one of our members was sick. 

### Poster Session

We will show graphs comparing the different versions of union-find and their performance on different kinds of test cases. We will include how we implemented these different versions. 

### Issues
We are a little worried about completing the coding work on time given how quickly the end of the semester is approaching but otherwise we don’t see any issues. 
