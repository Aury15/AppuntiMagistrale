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

## Blockchain

A dependable distributed system can be:
- **Centralized**: there is a single central node on which all the other peripheral nodes depend. If the central node fails, the entire system collapses;
- **Decentralized:** There are multiple intermediate-level nodes, each of which manages its own subset of peripheral nodes. Control is divided among multiple entities, reducing dependance on a single central point;
- **Distributed:** All nodes are connected to each other in a peer-to-peer network (or mesh), where there is no central authority or single coordinator and each node collaborates to achieve a common goal. This is the key model for ensuring high fault tolerance and reliability in modern distributed systems.
![[Lezione 1-1793616007202.webp]]

>[!defintions] Consensus
>A **consensus** algorithm allows a group of distributed nodes to agree on a common value or state, even when some nodes fails or behave maliciously. It must satisfy:
>	- **Agreement**: all honest nodes agree on the same value;
>	- **Validity**: the decided value satisfies the protocol rules;
>	- **Termination**: every honest node reaches a decision.


![[Lezione 1-1793616407264.webp]]

A transaction is made of several phases; it is atomic when all its phases are executed as a single unit. 

>[!definitions] Ledger of transactions
>The **ledger of transactions** is the record of all the transactions made. It is easier to keep updated and synchronised in a centralised architecture.

>[!Definitions] Block chain
>A **blockchain** is a distributed ledger that records transactions in an immutable, verifiable and permanent way.

If two irreconcilable transactions happen, only the first one recorded is considered legitimate.
![[Lezione 1-1793616554037.webp]]

#### How a transaction gets into a blockchain

![[Lezione 1-1793616647445.webp]]

## Modelling distributed system

A distributed system pursues its goal by running a distributed algorithm, i.e. a piece of software that takes care of communication, coordination and resource sharing. The road to a distributed abstraction has three steps: 
1. **System model**: describe the relevant elements in an abstract way, identify their intrinsic properties and characterize their interactions;
2. **Distributed abstraction**: design a protocol that captures a recurring interaction pattern of distributed applications;
3. **Implementation and deployment of the algorithm**, possibly as part of the middleware. This step is system dependent (language, architectural pattern) and belongs to Software Engineering and Lab.

Abstraction are important because they capture properties common to a large and significant range of systems, help to distinguish the fundamental from the accessory, and prevent designers from reinventing the same solution for slight variants of the same problem. An *architectural style* (components with well-defined required and provided interfaces, their connections, the data exchanged, their configuration) is treated in Software Engineering. Here components are abstracted by their specification and interfaces, and we focus on distributed abstractions and their algorithmic aspects.

#### Composition model

Protocols are written in pseudo.code that reflects a reactive computing model: components of the same process communicate by exchanging events and an algorithm is a set of event handlers that react to incoming events and possibly trigger new ones.

$$
\begin{aligned}
&\textbf{upon event } \langle c_1, \text{Event}_1 \mid \text{att}_1, \text{att}_2, \dots \rangle \textbf{ do} \\
&\quad \text{do something;}   \quad  \quad \quad \quad \quad                 // send-some-event                \\ 
&\quad \textbf{trigger } \langle c_2, \text{Event}_2 \mid \text{att}_1, \text{att}_2, \dots \rangle; \\
&\textbf{upon condition do}  \quad  \quad \quad \quad \quad                 // an-internal-event   \\
&\quad \text{do something;}
\end{aligned}
$$


