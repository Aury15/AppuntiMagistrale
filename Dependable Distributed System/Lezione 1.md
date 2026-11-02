## Distributed system 

>[!Definitions] Distributed system
>A distributed system is a piece of software that ensures that a collection of indipendant computers appears to its users as a single coherent system.

Usually a distributed system needs to complete a task, or goal, for which more than half ( $\frac{2}{3}$) of the entities need to agree.

Distributed system are useful:
- To increase performance, coping with the increasing demand of users in both processing power and data storage, and reducing latency;
- To build dependable services, coping with failures.

A distributed system is characterized by: 
1. **A collection of autonomous computing elements.**
	1. *Challenges*: the absence of a global clock, group membership (open vs closed) and the overlay networks (structured vs unstructured)
2. **A single coherent system**, where *coherent* means that it behaves according to the expectations of its users. 
	1. *Challenge*: failures.

##