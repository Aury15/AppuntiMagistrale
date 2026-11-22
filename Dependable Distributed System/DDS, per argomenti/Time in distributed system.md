---
cssclasses:
  - dds-notes
---
In a distributed system the processes run on different nodes interconnected by mean of a network (LAN, WLAN...). This processes can cooperate to complete a computation and communicate only through message-based communications. Many applications require a perfect ordering between events and synchronization to terminate correctly. **So time is a critical factor for distributed system.** 
We want to measure the time, but why? because many algorithm depends on time and we have many problems related to time: 
1. **Data consistency**: Ensure that different replicas of a database reflect updates in the correct order;
2. **Authentication**: Validate tokens and time-based certificates;
3. **Double processing avoidance**: Prevent the same operation from being performed twice on different nodes.
To solve these problem ordering events is fundamental.