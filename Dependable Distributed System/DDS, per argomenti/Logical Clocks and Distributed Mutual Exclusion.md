---
cssclasses:
  - dds-notes
---
Synchronizing physical clocks in distributed systems is complex, and accuracy depends heavily on estimating network communication delays, which are often difficult to calculate precisely. <mark style="background:#fff88f">The key insight</mark>: In many distributed applications, it is not essential to know the exact time an event occurred, but it is essential to determine with certainty the order in which events occurred. **Goal**: Find a reliable way to order events without having to rely on physical clock synchronization.
