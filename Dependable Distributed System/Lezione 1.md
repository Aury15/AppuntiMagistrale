## Distributed system 

>[!Definitions] Distributed system
>A distributed system is a piece of software that ensures that a collection of indipendant computers appears to its users as a single coherent system.

Usually a distributed system needs to complete a task, or goal, for which more than half ( $\frac{2}{3}$) of the entities need to agree.

![[Lezione 1-1793615269064.webp|706]]

Distributed system are useful:
- To increase performance, coping with the increasing demand of users in both processing power and data storage, and reducing latency;
- To build dependable services, coping with failures.

A distributed system is characterized by: 
1. **A collection of autonomous computing elements.**
	1. *Challenges*: the absence of a global clock, group membership (open vs closed) and the overlay networks (structured vs unstructured)
2. **A single coherent system**, where *coherent* means that it behaves according to the expectations of its users. 
	1. *Challenge*: failures.

## Dependability 

>[!definitions] Dependability
>Dependability is the ability of a system to deliver a service that can justifiably be trusted, i.e. the ability to avoid service failures that are more frequent than is acceptable. 

The characteristics of dependability are:
1. **Availability:** ready to be used;
2. **Reliability:** continuity of correct service;
3. **Safety:** no catastrophic consequences,
4. **Integrity:** no system alteration;
5. **Maintainability:** possibility to modify and repair;
6. **Robustness:** keep performing well even if the environment changes.

$$\text{Fault} \xrightarrow{\text{causes}} \text{error} \xrightarrow{\text{generates}} \text{failure}$$
## Dependable distributed systems

The characteristics and challenges of these systems are:
1. **Heterogeneity** [not defined]; 
2. **Openness**: the capability of a system to be extended and re-implemented; 
3. **Security**: confidentiality, integrity and availability; 
4. **Scalability**: the system keeps running with adequate performance even if the number of resources and/or users grows by orders of magnitude; 
5. **Fault tolerance**; 
6. **Concurrency**: multiple access to shared resources, coordination and synchronisation;
7. **Transparency**.