## Blockchain  

>[!defeinitions]
>A distributed ledger that can record transactions in an immutable, verifiable and permanent way.


19/11/2026


>[!definition] Dependability
>Dependability is the ability of a system to deliver a service that can justifiably be trusted, i.e. the ability to avoid service failures that are more frequent than is acceptable. 

The characteristics of dependability are:
1. **Availability:** readiness of correct service: the system is operational and accessible when required;
2. **Reliability:** continuity of correct service;
3. **Safety:** no catastrophic consequences,
4. **Integrity:** no system alteration;
5. **Maintainability:** possibility to modify and repair;
6. **Robustness:** keep performing well even if the environment changes.

We are going to proof theorem and lemma of analytical solution, but about what? 

- **Liveness**: something good eventually happens. In blockchains, this means that valid transactions submitted by honest users are eventually included and committed.
- **Safety**: nothing bad ever happens. In blockchains, this means that honest nodes never commit conflicting blocks at the same height, (i.e. they can never disagree).

>[!Note]
>Safety guarantees that conflicting decisions cannot both become final; finality tells us when a particular decision can be considered irreversible.

![[DDS part 2-1795087669538.webp]]

Maintainability: smart contract, hard fork 

#### Nakamoto consensus
**Assumptions**:
- **Longest chain wins:** A node adopts the longest proof-of-work (PoW) chain to its knowledge and attempts to mine a new block that extend this longest chain.
- **Disseminate blocks:** Upon adopting a new longest chain, either through mining or by receiving from others, a node broadcasts the newly acquired blocks.
2. **k-confirmation commit**. A node commits a block if it is buried at least k blocks deep in the longest chain adopted by that node.

##### Poissan process
Blocks are produced according to a **memoryless** Poissan process with arrival rate $\alpha$. Over any time interval $t$, the expected total number of honest blocks mined is $\alpha t$.
Poissan process -> exponential inter arrival time between two consecutive blocks.
$P[T>\Delta] = \exp^{-\alpha\Delta}$ 

![[DDS part 2-1795088187797.webp]]

In bitcoin $\rightarrow$ 1 block every 10 min, in ethereum $\rightarrow$ 1 block every 13 sec

>[!Definitions] Tailgaters
>A node mined before the time $\Delta$ necessary to receive updates. 

![[DDS part 2-1795088359957.webp]]

Tailgater $T$ and non-tailgater $N$ both had time to receive the propagation of $L$ (> $\Delta$) and are consequently linked to $L$. $T$ is mined in a time < $\Delta$ from $N$ and consequently cannot hear the propagation of $N$. $T$ and $N$ represent two distinc legit storis (<u>fork</u>)

>[!Lemma]
>Non-tailgaters do not have the same height.

###### What fraction of honest blocks are tailgaters vs non-tailgaters?
