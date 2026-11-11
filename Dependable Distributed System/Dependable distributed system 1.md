---
cssclasses:
  - dds-notes
---
## Distributed system 

>[!definition] Distributed system
>A distributed system is a piece of software that ensures that a collection of indipendant computers appears to its users as a single coherent system.

Usually a distributed system needs to complete a task, or goal, for which more than half ($\frac{2}{3}$) of the entities need to agree.

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

>[!definition] Dependability
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

>[!definition] Consensus
>A **consensus** algorithm allows a group of distributed nodes to agree on a common value or state, even when some nodes fails or behave maliciously. It must satisfy:
>	- **Agreement**: all honest nodes agree on the same value;
>	- **Validity**: the decided value satisfies the protocol rules;
>	- **Termination**: every honest node reaches a decision.


![[Lezione 1-1793616407264.webp]]

A transaction is made of several phases; it is atomic when all its phases are executed as a single unit. 

>[!definition] Ledger of transactions
>The **ledger of transactions** is the record of all the transactions made. It is easier to keep updated and synchronised in a centralised architecture.

>[!definition] Blockchain
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
&\quad \text{do something;} \quad // \text{send-some-event} \\ 
&\quad \textbf{trigger } \langle c_2, \text{Event}_2 \mid \text{att}_1, \text{att}_2, \dots \rangle; \\
&\textbf{upon condition do} \quad // \text{an-internal-event} \\
&\quad \text{do something;}
\end{aligned}
$$


##### Event-Driven Algorithm Components
- **`upon event ⟨ component, EventName | attributes ⟩`**: This acts as an event handler. It triggers the execution of the subsequent code block whenever a specific event is received or intercepted by the component.
- **`component` (e.g., $c_1, rbc$)**: Identifies the specific module, layer, or instance (such as a communication abstraction or broadcast instance) responsible for handling or generating the event.
- **`EventName` (e.g., Broadcast, Deliver, Send)**: Specifies the type of action or message event being processed (e.g., initiating a broadcast or delivering a payload).
- **`attributes` (e.g., $m, p, \text{att}_1$)**: Represents the parameters or payload carried by the event, such as messages, process identifiers, or state variables.
- **`trigger ⟨ ... ⟩`**: Used to emit or dispatch a new event toward another component, layer, or communication channel.
- **`upon condition`**: Evaluates an internal boolean guard or local system state condition, triggering execution immediately when the condition becomes true.


Events are of three kinds, and form the programming interface between layers:
- **Request**: used by a component c1 to request a service from c2 (e.g. disseminate a message in a group); 
- **Confirmation**: used by a component to confirm the completion of a request; 
- **Indication**: used by a component c1 to deliver information to c2 (e.g. the notification of a message delivery).

![[Lezione 1-1793618151965.webp|575x310]]
##### Example: job handler and layering 

$$
\begin{aligned}
&\textbf{Component: } \text{JobHandler, instance } jh. \\
&\textbf{Request:} \\
&\quad \langle jh, \text{Submit} \mid \text{job} \rangle : \text{requests a job to be processed.} \\
&\textbf{Indication:} \\
&\quad \langle jh, \text{Confirm} \mid \text{job} \rangle : \text{confirms that the job has been (or will be) processed.} \\
&\textbf{Property JH1 (Guaranteed response):} \\
&\quad \text{every submitted job is eventually confirmed.}
\end{aligned}
$$

Two implementations satisfy this specification. In the *synchronous* one the job is processed before being confirmed; in the *asynchronous* one it is only stored in a buffer, which is why the confirmation says “has been or will be” processed.

###### Algorithm: synchronous job handler
$$
\begin{aligned}
&\textbf{Algorithm: } \text{synchronous job handler} \\
&\textbf{Upon event } \langle jh, \text{Submit} \mid \text{job} \rangle \textbf{ do} \\
&\quad \text{process(job);} \\
&\quad \text{trigger } \langle jh, \text{Confirm} \mid \text{job} \rangle; \\
\end{aligned}
$$
- **Event Trigger (`Submit`):** When a job submission request arrives via $\langle jh, \text{Submit} \mid \text{job} \rangle$, the handler handles it straight away.
- **Execution Flow:**
    - It executes the task synchronously: `process(job);`
    - Once processing is fully completed, it triggers the confirmation event: `trigger` $\langle jh, \text{Confirm} \mid \text{job} \rangle$;
- **Characteristics:** It is **blocking**. The system processes the request sequentially and remains tied up until the job is completely executed before sending a confirmation back.
###### Algorithm: asynchronous job handler
$$
\begin{aligned}
&\textbf{Algorithm: } \text{asynchronous job handler} \\
&\textbf{Upon event } \langle jh, \text{Init} \rangle \textbf{ do} \\
&\quad \text{buffer} := \emptyset; \\
&\textbf{Upon event } \langle jh, \text{Submit} \mid \text{job} \rangle \textbf{ do} \\
&\quad \text{buffer} := \text{buffer} \cup \{\text{job}\}; \\
&\quad \text{trigger } \langle jh, \text{Confirm} \mid \text{job} \rangle; \\
&\textbf{Upon } \text{buffer} \neq \emptyset \textbf{ do} \\
&\quad \text{job} := \text{selectjob}(\text{buffer}); \\
&\quad \text{process(job);} \\
&\quad \text{buffer} := \text{buffer} \setminus \{\text{job}\};
\end{aligned}
$$
- **Initialization (`Init`):**
    - Upon initialization, it creates an empty data structure to store incoming tasks: `buffer := ∅;`
- **Receiving Requests (`Submit`):**
    - When a job arrives, instead of processing it right away, it appends the job to the buffer (`buffer := buffer ∪ {job}`) and immediately triggers a confirmation ($\langle jh, \text{Confirm} \mid \text{job} \rangle$). This decouples request acceptance from actual execution.
- **Background Processing (`upon buffer ≠ ∅`):**
    - A continuous internal guard monitors the buffer. Whenever it is not empty, the system selects a job (`selectjob(buffer)`), processes it, and removes it from the queue (`buffer := buffer \ {job}`).   
- **Characteristics:** It is **non-blocking**. The system accepts and confirms requests instantly while queuing them up for background processing.
###### Layering
$$
\begin{aligned}
&\textbf{Module: } \\
&\quad \textbf{Name: } \text{TransformationHandler, \textbf{instance} } th. \\
&\textbf{Events:} \\
&\quad \textbf{Request:} \langle th, \text{Submit} \mid \text{job} \rangle : \text{Submits a job for transformation and for processing.} \\
&\quad \textbf{Indication:} \langle th, \text{Confirm} \mid \text{job} \rangle : \text{Confirms that the given job has been (or will be) transformed and processed.} \\
&\quad \textbf{Indication:} \langle th, \text{Error} \mid \text{job} \rangle : \text{Indicates that the transformation of the given job failed.} \\
&\textbf{Properties:} \\
&\quad \textbf{TH1: } \text{Guaranteed response: Every submitted job is eventually confirmed or its transformation fails.} \\
&\quad \textbf{TH2: } \text{Soundness: A submitted job whose transformation fails is not processed.}
\end{aligned}
$$
###### Algorithm: Job-Transformation by Buffering
$$
\begin{aligned}
&\textbf{Algorithm: } \text{Job-Transformation by Buffering} \\
&\textbf{Implements:} \text{ TransformationHandler, instance } th. \\
&\textbf{Uses:} \text{ JobHandler, instance } jh. \\[0.8em]
&\textbf{Upon event } \langle th, \text{Init} \rangle \textbf{ do} \\
&\quad \text{top} := 1; \quad \text{bottom} := 1; \\
&\quad \text{handling} := \text{FALSE}; \quad \text{buffer} := [\bot]^M; \\[0.8em]
&\textbf{Upon event } \langle th, \text{Submit} \mid \text{job} \rangle \textbf{ do} \\
&\quad \textbf{if } \text{bottom} + M = \text{top} \textbf{ then} \\
&\quad \quad \text{trigger } \langle th, \text{Error} \mid \text{job} \rangle; \\
&\quad \textbf{else} \\
&\quad \quad \text{buffer}[\text{top} \bmod M + 1] := \text{job}; \\
&\quad \quad \text{top} := \text{top} + 1; \\
&\quad \quad \text{trigger } \langle th, \text{Confirm} \mid \text{job} \rangle; \\[0.8em]
&\textbf{Upon } \text{bottom} < \text{top} \land \text{handling} = \text{FALSE} \textbf{ do} \\
&\quad \text{job} := \text{buffer}[\text{bottom} \bmod M + 1]; \\
&\quad \text{bottom} := \text{bottom} + 1; \\
&\quad \text{handling} := \text{TRUE}; \\
&\quad \text{trigger } \langle jh, \text{Submit} \mid \text{job} \rangle; \\[0.8em]
&\textbf{Upon event } \langle th, \text{Confirm} \mid \text{job} \rangle \textbf{ do} \\
&\quad \text{handling} := \text{FALSE};
\end{aligned}
$$
This algorithm maintains a buffer of size $M$ to temporarily store incoming jobs and send them one at a time to the underlying JobHandler ($jh$):
- **Initialization (`Init`)**
    - The `top` and `bottom` indices are set to $1$, pointing to the insertion and extraction positions, respectively.
    - `handling := FALSE` indicates that the system is currently not processing any jobs.
    - `buffer := [\bot]^M` initializes an empty circular buffer of size $M$ filled with null elements ($\bot$).
- **Receiving the Request (`Submit`)**
    - When a new job arrives via the event $\langle th, \text{Submit} \mid \text{job} \rangle$, the algorithm checks whether the buffer is full by evaluating the overflow condition $\text{bottom} + M = \text{top}$.
	    - **If the buffer is full:** It rejects the request by triggering the error event $\langle th, \text{Error} \mid \text{job} \rangle$.
	    - **If the buffer is not full:** It stores the job at the correct position using the modulo operator ($\text{top} \bmod M + 1$), increments the `top` index, and immediately confirms the submission by triggering $\langle th, \text{Confirm} \mid \text{job} \rangle$.
- **Asynchronous Sending to the Lower Level ($\text{bottom} < \text{top} \land \text{handling} = \text{FALSE}$)**
    - This internal guard continuously monitors whether there are pending jobs in the buffer ($\text{bottom} < \text{top}$) and whether the system is free ($\text{handling} = \text{FALSE}$).
    - **When both conditions are verified:** It retrieves the next job, increments the `bottom` index, sets `handling := TRUE` to prevent simultaneous transmissions, and forwards the job to the lower-level handler via `trigger` $\langle jh, \text{Submit} \mid \text{job} \rangle$.
- **Handling Confirmation from the Lower Level (`jh, Confirm`)**
    - Upon receiving the completion signal $\langle jh, \text{Confirm} \mid \text{job} \rangle$ from the lower-level handler ($jh$), the `handling` flag is reset to `FALSE`, allowing the system to process the next job from the buffer.

#### Processes, messages and algorithms

$\Pi$ denotes the **set of processes**. Unless stated otherwise it is static and every process knows the identities of all the others. Sometimes a function $rank$ : $\Pi \to \{1, \dots, N\}$ gives each process a unique index; in the code, $\texttt{self}$ is the name of the process executing it.
**Processes** communicate by *exchanging messages over links*. Messages are uniquely identified (e.g. by a sequence number or local clock together with the process identifier), hence all messages ever exchanged by an algorithm are unique. 

>[!definition] Distributed algorithm
>A **distributed algorithm** is a distributed collection of automata, one per process. The automaton regulates how the process executes its computation steps, i.e. how it reacts to a message; every process runs the same automaton. An execution is a sequence of steps executed by the processes.

>[!definition] Safety and liveness
>A **safety** property states that the algorithm does not do anything wrong: it can be violated at some time $t$ and never be satisfied again after $t$ (equivalently, if it is violated in an execution $E$, there is a partial execution $E'$ of $E$ such that it is violated in every extension of $E'$ ). A **liveness** property ensures that eventually something good happens: for any time t there is some hope that it can be satisfied at some $t' \ge t$.

#### Failure models 
$$
\begin{array}{l|l}
\textbf{Failure} & \textbf{Behaviour} \\ \hline
\text{Crash} & \text{the process stops its execution} \\
\text{Omission} & \text{the process does not send (or receive) a message that it should,} \\
& \text{according to its algorithm} \\
\text{Crash-recovery} & \text{the process crashes and stops sending messages, but might} \\
& \text{recover later} \\
\text{Eavesdropping} & \text{the process leaks information obtained in an algorithm to an} \\
& \text{outside entity} \\
\text{Arbitrary (Byzantine)} & \text{the process may deviate arbitrarily from its algorithm}
\end{array}
$$

**Crash-stop**. The process crashes at time $t$ and never recovers. A process is *faulty* if it crashes at some time during the execution, and *correct* if it never crashes and executes an infinite number of steps.

>[!definition] Fault tolerance, resilience
>**Fault tolerance** means designing an algorithm that works despite faults, by assuming that at most $f$ of the $N$ processes are faulty. The relation between $f$ and $N$ is called **resilience**; an upper bound $f$ means that any number of processes up to $f$ may fail. *Crash-stop* does not preclude recovery: a recovered process is simply no longer part of the system, and the algorithm must not rely on it to make progress.

**Crash-recovery**. A process is *faulty* if it crashes and never recovers, or if it keeps crashing and recovering infinitely often; otherwise it is correct, even if it crashed and recovered a finite number of times.
A characteristic of this model is *amnesia*: a crashed process loses its internal state, so after recovering it might send messages that contradict those sent before the crash. To cope with this, every process is assumed to have a stable storage (a log), accessed through $\text{store}()$ and $\text{retrieve}()$, and to be aware upon recovery that it crashed and recovered.

![[Dependable distributed system-1793621296255.webp|637]]

The second one crashes and recovers infinitely often so it is faulty. The third one is correct and the fourth one crashes and recovers finitely so it is correct.

#### Timing assumptions

| Model                 | Assumption                                                                                                                              |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Synchronous           | known upper bounds on (i) the time to execute a basic step, (ii) message transit time, (iii) the drift of local clocks w.r.t. real time |
| Asynchronous          | no known bound on processes or links                                                                                                    |
| Partially synchronous | there exists a time t after which the synchrony bounds hold                                                                             |

**Synchronous systems**: enable timed failure detection, measuring transit delays, time-based coordination, worst-case performance guarantees and synchronised clocks. The major problem is the coverage of the synchrony assumption: it is hard to build a system in which the bounds hold with high probability. 
**Asynchronous systems**: even without physical clocks, the passage of time can be measured through the transmission and delivery of messages: time is defined with respect to communication. This is *logical time*, and the resulting notion of clock is a logical clock.
**Partial (eventual) synchrony**: distributed systems are usually synchronous most of the time, with periods of bounded asynchrony. This is captured by eventual synchrony: there is an unknown time t after which the system becomes synchronous. It does not mean that the whole system (hardware, software, network) is synchronous forever after t, nor that the system starts asynchronous and later becomes synchronous. What we need is a period of synchrony long enough for the algorithm to terminate.

![[Dependable distributed system-1793621710737.webp|739]]
## Abstracting communication

A link represents the network components of the distributed system: every pair of processes is connected by a bidirectional link. The physical topology (fully connected, bus, ring, switched network, ...) may be complex, in which case a routing algorithm realizes the abstraction. Under the crash-failure assumption we consider three links: **fair-loss, stubborn and perfect**.
The system model is the following: two processes (sender and receiver); messages can be lost and take an unpredictable time to arrive; processes can crash; the time taken by a process to execute an operation is bounded (the bound may be unknown).


>[!note] Remark
> Deliver is different from receive. A message is received at a port of the network and stored in a buffer; then an algorithm runs to make sure that the properties of the required link abstraction hold, and only afterwards is the message delivered.

###### Fair-loss links
Messages can get lost, but if a message is continuously re-transmitted, under certain conditions it is delivered.
###### Stubborn links
They guarantee continuous re-transmission of messages until they are delivered, so the same message can be received multiple times.
###### Perfect links
They ensure that messages are delivered reliably, without duplication and without creating non-existent messages.
## Time in distributed systems

In a distributed system the processes run on different nodes interconnected by mean of a network (LAN, WLAN...). This processes cooperate to complete a computation and communicate only through message-based communications. Many applications require a perfect ordering between events and synchronization to terminate correctly. **So time is a critical factor for distributed system.** 

#### Why time is so important? 
We want to measure the time, but why? because many algorithm depends on time and we have many problems related to time: 
1. **Data consistency**: Ensure that different replicas of a database reflect updates in the correct order;
2. **Authentication**: Validate tokens and time-based certificates;
3. **Double processing avoidance**: Prevent the same operation from being performed twice on different nodes.
To solve these problem ordering events is fundamental.

#### System model
A distributed system is composed by a set $\Pi = \{p_1, \dots, p_n\}$ of $n$ processes. Each *process* runs on a single mono-processor machine with no shared memory. Each process $p_i$ has a *state* $s_i$ that is changed by the actions it takes during algorithm execution. The state $s_i$ includes all the values of variables maintained by $p_i$. Each process can communicate with other processes only by exchanging messages. 
Each process generates a sequence of events:
- Internal event, an event that transforms the process state;
- External event. send/receive message;
- $e_i^k$, $k$-th event generated by $p_i$.

The evolution of the computation can be represented with a space-time diagram:

![[Dependable distributed system-1793719418826.webp|694x331]]

We denote with $\rightarrow_i$ the ordering relationship between two events in the same process $p_i$: $e \rightarrow_i e' \iff e \text{ happened before } e' \text{ in } p_i$. 

>[!definition] Local history
>Sequence of events produced by a process: $\text{history}(p_1) = h_1 = \langle e_1^1, e_1^2, e_1^3, e_1^4, e_1^5, e_1^6 \rangle$ 

>[!definition] Partial local history
>Prefix of local history $h_1^m = e_1^1 \dots e_1^m$ 

>[!definition] Global history
>Set containing every local history: $H = \bigcup_{i=1}^n h_i$

The first (and bigger) circle is the local history of $p_1$, the second circle is a partial local history of $h_3^3$  
![[Dependable distributed system-1793730759525.webp|650]]
#### Time-stamping and physical clocks
The naive solution to build a global history is *time-stamping*: every process labels each event with the value of its own physical clock. Events of the same process are always ordered this way, **but for processes on different machines the network and processing delays make it impossible to realise a common clock shared by all of them**. What can be done is to synchronize the physical clocks, through suitable algorithms, up to a certain accuracy (synchronization error), and to timestamp events with the synchronized local clock. 
A **physical clock** is a counting register incremented at every tick of an oscillator (hardware clock $H_i(t)$). The operating system reads the hardware $H_i(t)$ and produces a software clock $C_i(t) = \alpha H_i(t) + \beta$ which approximates the real time $t$ at  process $p_i$.
*Example*: $C_i(t)$ may be implemented by a 64-bit word, representing nanoseconds that have elapsed at time t.
Generally this clock is not completely accurate: it can be different from the real time $t$, it can be different at any process due to the precision of the approximation. 
$C_i$ can be used such as timestamp for event produced by $p_i$. To distinguish two events, its resolution (the time between two consecutive increments) must satisfy $T_{\text{resolution}} < T$  between the two events.
##### Parameters affecting the Clock Synchronization accuracy
We have two different parameter that affect the clock synchronization accuracy:
- **Skew**: the difference in time between two clocks $\to |C_i(t) - C_j(t)|$;
- **Drift rate**: the gradual misalignment of once synchronized clocks caused by the slight inaccuracies of the time keeping-mechanism. Difference for time unit with respect to an ideal clock. 
The standard of time is the **UTC** $\rightarrow$ *U*niversal *T*ime *C*oordinated) is the base of any international measure. Based on international atomic time: 1 sec = time a cesium atom needs for 9192631770 state transitions.

We have two type of synchronization:
1. **External Synchronization**: processes synchronize their clock $C_i$ with an authoritative external source $S$. Let $D > 0$ be the synchronization bound and $S$ be the source of UTC. Clocks $C_i$ for $i = 1, \dots, N$ are **externally synchronized** with a time source $S$ (UTC) if for each time interval $I$: $|S(t) - C_i(t)| < D$ for $i = 1, 2, \dots, N$ and for real time $t$ in $I$.  We say that clocks $C_i$ are accurate within the bound of $D$.
2. **Internal Synchronization**: all the processes synchronize their clocks $C_i$ between them. Let $D > 0$ be the synchronization  bound and let $C_i$ and $C_j$ the clocks at processes $p_i$ and $p_j$ respectively. Clocks are **internally synchronized** in a time interval $I$: $|C_i(t) - C_j(t)| < D$ for $i, j = 1, 2, \dots, N$ and for all time $t$ in $I$. We say that clocks $C_i, C_j$ agree within the bound of $D$.

Internally synchronized clocks are not necessarily externally synchronized: they may drift collectively from the external source. Conversely, processes externally synchronized within D are internally synchronized within 2D (by the triangle inequality).

#### Correct clock 
A *hardware clock* $H$ is **correct** if its drift rate is within a limited bound of $\rho > 0$, respecting: $1 - \rho \leq \frac{dC}{dt} \leq 1 + \rho$. 
In the presence of a correct hardware clock $H$ we can measure a time interval $[t, t']$ ($\forall t' > t$) introducing only a limited error. So we can quantify and limit the error: $(1 - \rho)(t' - t) \leq H(t') - H(t) \leq (1 + \rho)(t' - t)$. On the left of the formula we have the lower limit, which describes the case when the hardware clock is slow: the interval of time measured by the hardware will be shorter than the real time, but the formula guarantees that it cannot be less than $(1 - \rho)(t' - t)$. 
![[Dependable distributed system-1793740789023.webp|505]]

>[!important] A hardware clock is considered correct if its speed deviation respects this band between $(1 - \rho)$ and $(1 + \rho)$. Thanks to this inequality, we know with mathematical certainty that, measuring an interval with a clock with a certain drift $\rho$, the introduced error will remain confined within very precise and predictable limits.!

Software clocks have to be **monotone**: $t' > t \implies C(t') > C(t)$. The monotonic property can be guaranteed by choosing opportune $\alpha$ and $\beta$. (Note that $\alpha$ and $\beta$ can be functions of time). 

**Clock failure**:
- *Crash failure*: clock simply stops;
- *Arbitrary failure*: clock behaves arbitrarily (e.g. Y2K bug: the day after the 31/1/1999
becomes 1/1/1900 rather than 1/1/2000)

#### Synchronization algorithm based on time server

##### Christian's algorithm (external synchronization)

- **Operation**: A process $p$ sends a time request (ask the current time) through a message $m_r$  to a time server $S$ (connected to a UTC source) and receives the timestamp $t$ via $m_t$. 
![[Dependable distributed system-1793782188573.webp|616]]
- **Timing**: The process sets its clock to $t + \frac{\text{RTT}}{2}$, where $\text{RTT}$ (Round Trip Time) is the total round-trip time for the message. 
- **Accuracy**: The maximum estimated error is $\pm\left(\frac{\text{RTT}}{2} - \min\right)$, where $\min$ is the minimum estimated transmission time on the network.
- **Limitations**: Represents a single point of failure; requires the use of redundant server clusters and authentication mechanisms. A time server can crash and can be attacked. There could exist periods in which the synchronization is not possible $\rightarrow$ ask to multiple servers at the same time. 
![[Dependable distributed system-1793740918279.webp]]
##### Berkeley Algorithm (Internal Synchronization) 
- **Structure**: Master-slave architecture. 
- **Algorithm steps**: 
	1. The master $p_m$ periodically polls the slaves, sending its own timestamp $t_1$ and receiving the nodes' local timestamps $t_2$ in return. 
	2. The master calculates the differences $\Delta$ ($\Delta = \frac{t_1 + t_3}{2} - t_2$ where $t_3$ is the local clock when the master receive the timestamps $t_2$ in return) between its own clock and that of each process. 
	3. The master discards the values of faulty processes (whose difference exceeds a threshold $\gamma$). 
	4. It calculates the average of the valid values $\Delta p_i$  and sends each process the algebraic correction to apply using $\text{Adj}_{p_i} = \text{avg} - \Delta p_i$. 
- **Slowdown management**: To avoid jumping back in time (violating causality and monotonicity), excess clocks are not set to a past value, but are slowed down by temporarily hiding hardware clock interrupts.
![[Dependable distributed system-1793782696302.webp]]
![[Dependable distributed system-1793782931443.webp]]
- **Accuracy**: the protocol accuracy depends on the maximum round-trip time. The master does not consider clock values associated to RTT grater than the maximum one. 
- **Fault** **tolerance**:
	- If the master crashes, another master is elected (in an unknown time);
	- It is tolerant to arbitrary behavior (eg. slaves that send wrong values);
	- Master process consider a certain number of clock values and these values do not differ between them over a certain threshold;
>[!note] What does slowing down a clock mean? 
>Imagine that the average calculated by the master determines that the correct clock is 12:00, but a slave (having a faulty or fast clock) reads 12:05. It's not possible to directly set a passed value to the slave clocks. If we told the slave to suddenly "jump" from 12:05 to 12:00, a logical disaster would ensue: 
>	- The *monotonicity* of time would be violated (time must always advance, never go backward: $t' > t \implies C(t') > C(t)$). 
>	- The *cause-effect order* (causality) would be violated: events that logically happened earlier could end up with a later timestamp than events that happened later, creating massive conflicts in logs and databases.
>**Solution**: slow down the clock! Since we can't make the clock "jump" back in time, the only legitimate solution is to slow down the slave's clock speed until the rest of the system "*reach it*". Computers update their software clocks using periodic hardware interrupts (electronic signals repeated at regular intervals, e.g., every millisecond, that tell the operating system to add a "tick" to the time counter). To slow down the clock, the operating system hides (ignores) a certain number of these interrupts. The system calculates how many specific interrupts to skip based on the amount of accumulated delay and the original tick rate. <u>Practical example:</u> If the counter normally ticks 1000 times per second, we decide to ignore some of these signals. This way, for a certain period, time on that machine will run slower than normal, allowing the average time calculated by the master to "catch up" with it without leaping into the past.

#### Network time protocol (NTP)
NTP is a standard de facto for **external clock synchronization** of distributed system on
Internet. It synchronizes clients with UTC. NTP employs several security mechanisms (e.g. mechanisms for authentication of time references) usually they are not required in a local area network. Based on a remote reading procedure like Christian’s Algorithm, NTP specification adds to the basic algorithm mechanisms for clustering, filtering and evaluating data quality in order to minimize the synchronization. 
<mark style="background:#fff88f">Stratum Hierarchy:</mark> 
1. **Stratum 1** (Primary servers): Connected directly to the primary UTC time sources.
2. **Stratum 2** (Secondary servers): Synchronized hierarchically with the primary servers.
3. **Stratum 3** *and above*: Synchronization subnets for end-user computers.
![[Dependable distributed system-1793783886141.webp]]

The NTP hierarchy is reconfigurable in the presence of faults: 
- Primary server that loses its connection with UTC-signal can become a secondary server;
- Secondary server that loses its connection with a primary server (e.g. a crash of the primary server) can contact and connect itself to another primary.
###### NTP Synchronizations models
- **Multicast**: server periodically send its actual time to its leaves in the LAN. Leaves set their time using the received time assuming a certain delay. *It is used in quick LANs but it shows a low accuracy.*
- **Procedure call**: server replies to request with its actual timestamp (like Christian's algorithm). *High accuracy and it is useful when it is not available hw multicast.*
- **Symmetrical**: used to synchronize between pairs of time servers using messages containing timing information. *Only used in high level of hierarchy*.

#### Time in asynchronous system 
In an asynchronous distributed system, global **physical time is not directly observable** in a deterministic way due to the unpredictability of network communication delays. Clocks can only be synchronized with a certain degree of probability, and an accuracy bound can only be calculated by knowing rigorously the upper and lower limits of the transmission delays.


## Logical Clocks and Distributed Mutual Exclusion
Synchronizing physical clocks in distributed systems is complex, and accuracy depends heavily on estimating network communication delays, which are often difficult to calculate precisely. <mark style="background:#fff88f">The key insight</mark>: In many distributed applications, it is not essential to know the exact time an event occurred, but it is essential to determine with certainty the order in which events occurred. **Goal**: Find a reliable way to order events without having to rely on physical clock synchronization.

#### Happened-before relation 

![[Remark]]

Formal definition ($e \rightarrow e'$): Two events $e$ and $e'$ are related by the happened-before relation if one of the following conditions is true:
1. **Same process**: $\exists p_i | e \rightarrow_i e'$  
	- $e$ and $e'$ occur in the same process $p_i$ and $e$ precedes $e'$ in time.
2. **Communication**: $\exists m | e = send(m) \text{ and } e' = deliver(m)$ 
	- $e$ corresponds to the sending of a message ($\text{send}(m)$) by one process and $e'$ corresponds to the reception/delivery of the same message ($\text{deliver}(m)$) by another process ($\text{send}(m) \rightarrow \text{deliver}(m)$). 
3. **Transitivity**: $\exists e, e', e'' | (e \rightarrow e') \land (e'' \rightarrow e')$
	- There exists an intermediate event $e''$ such that $e \rightarrow e''$ and $e'' \rightarrow e'$.
![[Dependable distributed system-1793785270199.webp]]
Happened-before relation imposes a partial order over events of the execution history: 
- If two events $e_i$ and $e_j$ are not linked by any cause-effect relationship, they are said to be **concurrent** ($e_i \parallel e_j$).
- In a distributed system, for any pair of events only one of three conditions is always true:
	1. $e_i \rightarrow e_j$;
	2. $e_j \rightarrow e_i$;
	3. $e_i \parallel e_j$.
![[Dependable distributed system-1793785445872.webp]]
#### Logical clock and scalar logical clock
The Logical Clock, introduced by Lamport, is a software counter that monotonically
increases its value. A logical clock $L_i$ can be used to timestamp events: 
$ts_e = L_i(e)$ is the “logical” timestamp assigned by a process $p_i$ to events e using its current logical clock with a property: if $e \rightarrow e' \text{ then } ts_e < ts_{e'}$ .
<mark style="background:#fff88f">Implementation Rules</mark>: 
- Each process $p_i$ initializes its local clock to zero ($L_i = 0$);
- The process increments $L_i$ by $1$ each time it generates a local event (send or receive);
- **Sending**: When $p_i$ sends a message $m$, it appends the current timestamp value $ts = L_i$; 
- **Receiving**: When $p_i$ receives a message with a timestamp $ts$, it updates its clock by calculating the maximum and incrementing it: $L_i = \max(ts, L_i) + 1$ (before generating the receive event).

<mark style="background:#affad1">Example: </mark>

![[Dependable distributed system-1793786065529.webp]]
$e_i^j$ is $j-th$ event of process $p_i$, $L_i$ is the logical clock of $p_i$. 
- **System Overview:** The diagram illustrates Lamport's Logical Clocks in a distributed system consisting of three processes ($p_1$, $p_2$, and $p_3$).
- **Initial State:** All processes start with their logical clocks set to zero ($L_1 = 0$, $L_2 = 0$, $L_3 = 0$).
- **Local Events & Internal Increments:** As time progresses locally within each process, events occur and local counters increment sequentially (e.g., $L_3$ goes from $1$ to $4$).
- **Message Passing & Synchronization (Blue Arrows):** When a message is sent from one process to another, it carries the sender's current timestamp. Upon reception, the receiving process updates its own clock by taking the maximum between its current local clock and the incoming timestamp, then adding $1$ ($\max(\text{local}, \text{msg}) + 1$).

$e_1^1 \to e_1^2$ and timestamps reflect this property
$e_1^1 \parallel e_3^1$ and respective timestamps have the same value
$e_1^2 \parallel e_3^1$ but respective timestamps have different values

###### Properties and Limitations: 
- **Guaranteed Property**: If $e \rightarrow e'$, then their timestamp satisfies the inequality $ts_e < ts_{e'}$.
- **Critical Bound**: The implication is one-way. The converse is not true: if $ts_e < ts_{e'}$, we cannot deduce with certainty that $e \rightarrow e'$. With scalar clocks, it is not possible to determine whether two events are causally related or concurrent ($e \parallel e\'$).
#### Vector clock 
*Vector Clock* for a set of N processes is composed by an array of N integer counters.
Each process $p_i$ maintains a Vector Clock $V_i$ and timestamps events by mean of its Vector Clock. Similarly to scalar clock, Vector Clock is attached to message $m$, but in this case the timestamp will be an integer vector. Vector Clock allows nodes to order events in happens-before just looking at their timestamps:
		- <u>Scalar clocks</u>: $e \rightarrow e' \implies L(e) < L(e')$;
		- <u>Vector clocks</u>: $e \rightarrow e' \iff L(e) < L(e')$.
###### Definition and Implementation:
- In a system with $N$ processes, each process $p_i$ maintains a vector of integers $V_i$ of size $N$. 
- $V_i[i]$ tracks the number of events autonomously generated by $p_i$.
- $V_i[j]$ (with $i \neq j$) represents the updated knowledge that $p_i$ has about the events generated by $p_j$. 
- Generating an event: $p_i$ increments its component in the vector: $V_i[i] = V_i[i] + 1$.
- **Sending**: The message is stamped by attaching the entire vector $ts = V_i$. 
- **Receiving**: The receiving process updates each component of its vector by maximizing the received vector: $V_i[j] = \max(ts[j], V_i[j])$ for each $j = 1 \dots N$, and then increments its component.

![[Dependable distributed system-1793787749252.webp]]
A Vector Clock $V_i$:
- $V_i[i]$ represents the number of events produced by $p_i$.
- $V_i[j]$ with $i \neq j$ represents the number of events generated by $p_j$ that $p_i$ knows.
##### Properties of Vector Clocks
* **Equality ($V = V'$):** $V = V' \iff V[j] = V'[j] \quad \forall j = 1 \dots N$
* **Less than or equal ($V \le V'$):** $V \le V' \iff V[j] \le V'[j] \quad \forall j = 1 \dots N$
* **Causal precedence ($V < V'$):** The event associated with $V$ happened before the event associated with $V'$ if and only if: $V < V' \iff (V \le V') \land (V \neq V')$
  Equivalently, expressed in components:
  $\forall i = 1 \dots N, \quad V'[i] \ge V[i]$
  $\exists i \in \{1 \dots N\} \mid V'[i] > V[i]$

![[Dependable distributed system-1793788692222.webp|656]]
Differently from Scalar Clock, Vector Clock allows to determine if two events are concurrent or related by a happened-before relation.

#### Distributed mutual exclusion

Let us consider a set of processes $\Pi = \{p_1, \dots, p_n\}$ and a set of resources $R = \{r_1, \dots, r_m\}$. The system is asynchronous, the process will be always correct and can communicate by exchanging messages on top of perfect point to point links. 
![[Dependable distributed system-1793788795390.webp|603]]
Processes need to access resources exclusively and we need to design a distributed
abstraction that allows them to coordinate to get access to resources.
Distributed Mutual Exclusion is a fundamental problem in distributed systems. Imagine having a set of processes distributed across different machines connected via a network (with no shared memory) that need access to a shared resource (called a Critical Section or CS, such as a file to write, a database, or a printer). The mutual exclusion problem involves designing a coordination mechanism that allows processes to access it in a safe and orderly manner.
###### Events
- `request()`: it issues a request to enter into the critical section;
- `ok()`: it notifies the process that it can now access the critical section;
- `release()`: it is invoked to leave the critical section and to allow someone else to enter.
###### Properties 
- **Mutual exclusion:** at any time $t$, at most one process $p$ is running the critical section;
- **No-deadlock:** there always exists a process $p$ able to enter the critical section;
- **No-starvation:** every `request()` and `release()` operation eventually terminate. 

![[Dependable distributed system-1793789585449.webp|566]]
#### Lamport's algorithm
The Lamport Algorithm for Distributed Mutual Exclusion is one of the most important classical methods in distributed systems for regulating access to a shared resource (Critical Section, or CS) using scalar logical timestamps.
In this algorithm, each process $p_i$ in the system maintains two fundamental structures:
- A *local logical clock counter* ($ck$).
- A *local request queue* ($Q$) in which all requests for access to the Critical Section (both its own and those of other processes) are stored in chronological order.
##### The Phases of the Algorithm
**Phase 1: The Request**
- When a process $p_i$ wishes to access the Critical Section, it increments its logical clock and sends a `REQUEST` message (containing the timestamp $t$ and its identifier $id$) to **all** other processes in the system.
- Simultaneously, $p_i$ inserts its request into its local queue $Q$.

**Phase 2: Reception and Response (Ack)**
- When any other process $p_j$ receives the `REQUEST` message from $p_i$:
  - Inserts $p_i$'s request into its local queue $Q$.
  - Responds by immediately sending an acknowledgement (**ACK**) message to $p_i$. (Note: The ACK travels with its own next logical timestamp.)

**Phase 3: Entering the Critical Section**
A process $p_i$ can enter the Critical Section **only if** three conditions are simultaneously true:
1. Its request is at the top of its queue $Q$ (meaning it has the **smallest timestamp** of all pending requests).
2. $p_i$ has already received an ACK message from _all_ other processes in the system.
3. The timestamps of received ACKs are strictly greater than the timestamp of its own request.
_Why is this rule necessary?_ Because the arrival of an ACK from another node guarantees $p_i$ that that node has seen its request and has recorded a higher logical clock; therefore, no other node will be able to insert a request with a timestamp "in the past" that should have taken precedence.

**Phase 4: The Release**
- Once the work within the Critical Section is complete, process $p_i$:
  - Sends a `RELEASE` message to all other processes.
  - Removes its request from its local $Q$ queue.
- When other processes receive the `RELEASE` message from $p_i$, they also remove $p_i$'s request from their local $Q$ queues. This refreshes the queues and frees the resource for the next process in the queue.

*PICTURES OF THE EXAMPLE*

###### Safety proof
Let's assume, for the sake of argument, that both $p_i$ and $p_j$ manage to enter the Critical Section.
According to the rules of Lamport's algorithm, for a process to enter the CS, two things must be simultaneously true:
- The process must have received an ACK from _all_ other nodes in the system.
- Its request must have the absolute smallest timestamp within the local queue ($Q$).
If we assume that $p_i$ sent a request with a timestamp of $t_i$ and $p_j$ with a timestamp of $t_j$:
- One of the two must necessarily have the temporally smallest timestamp than the other (assume $t_i < t_j$).
- Since logical timestamps create a total order (thanks to scalar counters and process IDs in case of ties), the queues of all nodes will reflect exactly the same priority order.
- Consequently, $p_j$ will never be able to consider its own timestamp $t_j$ as "the smallest" if the timestamp of $p_i$ ($t_i$) which arrived first is in the queue, or $p_j$ has not yet received the ACK from all nodes because it is blocked by the priority of $p_i$.
###### Properties of the algorithm
- **Fairness (Absence of Starvation):** Requests are fulfilled strictly in the chronological order established by logical timestamps (_happened-before_ relationship). First come, first served.
- **Fairness (Mutual Exclusion):** Two processes are guaranteed never to enter the CS at the same time, precisely because the combination of ordered timestamps and universal ACKs creates a global and consistent view of the queues on all nodes.
- **Message Cost:** For each single access to the Critical Section, the Lamport algorithm requires a fixed total of **$3(N - 1)$ messages**, divided as follows:
- $N - 1$ `REQUEST` messages sent to all other nodes.
- $N - 1$ `ACK` response messages.
- $N - 1$ `RELEASE` messages.

#### Ricart-Agrawala's algorithm
The *Ricart-Agrawala algorithm* is an evolution and optimization of the Lamport algorithm to solve the Distributed Mutual Exclusion problem. While the Lamport algorithm requires sending `REQUEST`, `ACK`, and `RELEASE` messages separately (for a total of $3(N-1)$ messages), Ricart-Agrawala merges and reduces message exchange by leveraging dynamic responses called `REPLYs`.

Each process maintains the following information about its state:
- **`State`**: The current state of the process, which can be:
  - `NCS` (Non Critical Section): The process is not interested in the resource (initial state).
  - `Requesting`: The process is requesting entry into the Critical Section.
  - `CS`: The process is currently within the Critical Section.
- **`num`**: The local logical clock counter (scalar).
- **`Last_Req`**: The timestamp of the last request sent by the process.
- **`#replies`**: A counter that tracks permissions (`REPLY`) received from other nodes (initially 0).
- **`Q`**: A queue of pending requests (requests received from others that have been decided not to respond to yet).

###### The Phases of the Algorithm
**Phase A: When a process wants to enter the Critical Section**
1. Process $p_i$ changes its state to `Requesting`, increments its logical clock, and sets `num`.
2. It sends a `REQUEST(num, i)` message (containing its timestamp and ID) to **all** other $N-1$ processes in the system.
3. It **waits** patiently until it receives permission from all other nodes, that is, until the `#replies` counter reaches the value $N-1$.
4. Once it has obtained all permissions, it enters the Critical Section (`State = CS`).

**Phase B: When a process $p_j$ receives a `REQUEST(t, i)` request from $p_i$**
When a node $p_j$ receives a request from another node $p_i$, it must decide whether to respond immediately or postpone the response, comparing priorities (based on timestamp and process ID: the lowest `{timestamp, id}` pair takes precedence):
- **Conditions for postponing the response (deferring the `REPLY`):** Process $p_j$ **does not** respond immediately if it is in one of these two situations:
1. $p_j$ is currently in the Critical Section (`State == CS`).
2. $p_j$ is in the `Requesting` state **and** has a higher priority than $p_i$ (i.e., its `Last_Req` timestamp/ID is less than that of $p_i$: `{Last_Req_j, j} < {t, i}`).
_In this case_, $p_j$ puts $p_i$'s request into its local pending queue `Q`.
- **When to respond immediately:** If $p_j$ is not interested in the resource (`State == NCS`) or has a lower priority than $p_i$, then it **immediately sends a `REPLY` message** to $p_i$.

**Phase C: When the process leaves the Critical Section (Release)**
Once it has finished its work in the Critical Section, process $p_i$:
1. It scrolls through its local queue `Q` for requests it had queued and sends a `REPLY` message to each of those processes.
2. It empties the `Q` queue, returns the state to `NCS`, and resets the `#replies` counter.

*for the example -> DDS lecture5B*

## Coordinator, Token and Quorum Algorithms for Mutual Exclusion

We keep the same setting as before: a set of processes $\Pi$, a single resource, an asynchronous system, correct processes and perfect point-to-point links. After the logical clock-based algorithms, this lecture presents the other three approaches: coordinator-based, token-based and quorum-based.
*Basic idea:* There exist a special process, called coordinator, that collects requests and grant permission to enter into the critical section. 

###### Process code
$$
\begin{aligned}
&\textbf{Init:} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{coordinator} := \text{getCoordinatorId()}; \\
& \\
&\textbf{upon event } \text{request}() \textbf{ do} \\
&\quad \text{state} := \text{waiting}; \\
&\quad \text{trigger } \text{pp2pSend}(\text{REQ}, i) \text{ to coordinator}; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{GRANT\_CS}) \textbf{ do} \\
&\quad \text{state} := \text{CS}; \\
&\quad \text{trigger } \text{ok}(); \\
& \\
&\textbf{upon event } \text{release}() \textbf{ do} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{trigger } \text{pp2pSend}(\text{REL}, i) \text{ to coordinator};
\end{aligned}
$$

This is the pseudocode that runs on every generic process $p_i$ that needs access to a shared resource (Critical Section).
- **`Init` (Initialization):**
	- `state = idle`: Initially, the process is in an "idle" state (it does not need the resource).
	- `coordinator = getCoordinatorId()`: The process locates and stores the identifier of the node acting as coordinator in the system.

- **`upon event request()` (When the application requests the resource):**
	- `state = waiting`: The process changes its state to "waiting".
	- `trigger pp2pSend(REQ, i) to coordinator`: Sends a point-to-point `REQ` request message (containing its ID $i$) to the coordinator.

- **`upon event pp2pDeliver(GRANT_CS)` (When permission arrives from the coordinator):**
	- `state = CS`: The process enters the Critical Section state (can use the resource).
	- `trigger ok()`: Notifies the local application that it is now safe to proceed.

- **`upon event release()` (When the application has finished using the resource):**
	- `state = idle`: The process returns to the idle state.
	- `trigger pp2pSend(REL, i) to coordinator`: Sends a `REL` release message to the coordinator to notify it that it has freed the resource.
###### Coordinator code
$$
\begin{aligned}
&\textbf{Init:} \\
&\quad \text{pending} := \emptyset; \\
&\quad \text{current\_in\_CS} := \bot; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{REQ}, j) \text{ from } p_j \textbf{ do} \\
&\quad \text{pending} := \text{pending} \cup \{p_j\}; \\
& \\
&\textbf{when } \text{pending} \neq \emptyset \land \text{current\_in\_CS} = \bot \text{ do} \\
&\quad \text{candidate} := \text{select\_process}(\text{pending}); \\
&\quad \text{pending} := \text{pending} \setminus \{\text{candidate}\}; \\
&\quad \text{current\_in\_CS} := \text{candidate}; \\
&\quad \text{trigger } \text{pp2pSend}(\text{GRANT\_CS}) \text{ to candidate}; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{REL}, j) \text{ from } p_j \text{ do} \\
&\quad \textbf{if } \text{current\_in\_CS} = j \text{ then} \\
&\quad \quad \text{current\_in\_CS} := \bot;
\end{aligned}
$$

This is the code executed exclusively by the process elected as coordinator, which acts as a central "traffic policeman" for dispatching permissions.

- **`Init` (Initialization):**
	- `pending = ∅`: Initializes a queue or empty pool to collect incoming requests from various nodes.
	- `current_in_CS = ⊥`: A variable that keeps track of which process is currently in the Critical Section (initially empty/null, denoted by $\bot$).

- **`upon event pp2pDeliver(REQ, j) from pj` (When the coordinator receives a request):**
	- `pending = pending ∪ {p_j}`: Takes the requesting process $p_j$ and adds it to the pool of pending requests.

- **The `when pending ≠ ∅ and current_in_CS = ⊥` control block (The scheduling logic):**
	- This check is executed continuously (or triggered on an event-by-event basis): if there are pending requests (`pending` is not empty) **and** the resource is currently free (`current_in_CS = ⊥`), the coordinator performs three actions:
		1. `candidate = select_process(pending)`: Selects a process from the waiting list (e.g., in FIFO order).
		2. `pending = pending \ candidate`: Removes the selected process from the waiting list.
		3. `current_in_CS = candidate`: Assigns the resource to the candidate.
		4. `trigger pp2pSend(GRANT_CS) to candidate`: Sends the `GRANT_CS` authorization message directly to that process.

- **`upon event pp2pDeliver(REL, j) from pj` (When the coordinator receives the release):**
	- `if current_in_CS = j`: Verifies that the process that was using the resource is actually releasing it.
	- `current_in_CS = ⊥`: Frees the resource by setting it to $\bot$. (This immediately unlocks the `when` lock described above, allowing the coordinator to serve the next process in the queue.)

<mark style="background:#d3f8b6">Example:</mark>
![[Dependable distributed system-1793809731647.webp]]![[Dependable distributed system-1793809735721.webp]]![[Dependable distributed system-1793809737748.webp]]![[Dependable distributed system-1793809741189.webp]]![[Dependable distributed system-1793809747758.webp]]![[Dependable distributed system-1793809783788.webp]]![[Dependable distributed system-1793809798764.webp]]![[Dependable distributed system-1793809811798.webp]]![[Dependable distributed system-1793809819636.webp]]

###### Pro/Cons
- **Performance**: Entering the CS requires exactly **2 messages** (1 `REQ` and 1 `GRANT`), equivalent to a single RTT (Round Trip Time). Releasing the resource requires **1** `REL` message.
- **Pros**: Extremely lightweight and easy to implement protocol.
- **Cons**:
- Scalability issues if traffic increases on the coordinator.
- **Single Point of Failure (SPOF)**: If the coordinator crashes, the entire mutual exclusion system crashes.

#### Token-based Algorithm on Logical Ring
The processes are organized in a logical ring topology (overlay network) using existing point-to-point communication channels. A single special message called a Token continuously circulates along the ring: only those who possess it are authorized to enter the Critical Section.

**How it works**
1. **Ring construction**: Each process $p_i$ stores the name of the next process in a local variable (`next = p_{(i+1) \bmod N}`).
2. **Initialization**: A single token is created (usually generated by an initial node like $p_0$) and inserted into the ring.
3. **Request handling**:
- When a process wants to access the CS, it enters the `waiting` state and **waits to receive the token**.
- When the token arrives, if the process was waiting, it enters the CS (notifying `ok()`). When it is finished (`release()`), it passes the token to the next node (`next`).
- If the process receives the token but is _not_ interested in the CS, it simply **immediately** forwards it to the next process in the ring.

![[Dependable distributed system-1793810509841.webp]]

1. We construct an overlay (i.e., a logical network) as a ring exploiting existing point-to-point communication channels. The ring is obtained by:
   - Storing in a local variable the name of the next process in the ring;
   - Allowing communication only with the next node.

![[Dependable distributed system-1793810667840.webp|681]]

$$
\begin{aligned}
&\textbf{Init:} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{next} := p_{(i+1) \bmod N};
\end{aligned}
$$

2. A token is created and propagated in the ring during the initialization phase (i.e., it is assigned to a process of the system). <mark style="background:#fff88f">WARNING: The token must be unique to guarantee mutual exclusion</mark>. Only one process (selected through a deterministic function) can create the token during the init.

![[Dependable distributed system-1793810978086.webp|673]]
$$
\begin{aligned}
&\textbf{Init} \\
&\quad \textbf{state} := \text{idle} \\
&\quad \textbf{next} := p_{(i+1) \bmod n} \\
&\quad \textbf{if } \text{self} = p_0 \textbf{ then} \\
&\quad \quad \textbf{trigger } \text{pp2pSend}(\text{TOKEN}) \textbf{ to } \textbf{next}
\end{aligned}
$$
![[Dependable distributed system-1793811185758.webp]]

$$
\begin{aligned}
&\textbf{upon event } \text{request}() \textbf{ do} \\
&\quad \text{state} := \text{waiting}; \\
&\textbf{upon event } \text{pp2pDeliver}(\text{TOKEN}) \textbf{ do} \\
&\quad \textbf{if } \text{state} = \text{waiting} \text{ then} \\
&\quad \quad \text{state} := \text{CS}; \\
&\quad \quad \text{trigger } \text{ok}(); \\
&\quad \textbf{else} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next};; \\
&\textbf{upon event } \text{release}() \textbf{ do} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next};
\end{aligned}
$$

**So we obtain**

$$
\begin{aligned}
&\textbf{Init:} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{next} := p_{(i+1) \bmod N}; \\
&\quad \textbf{if } \text{self} = p_0 \textbf{ then} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next};; \\
&\textbf{upon event } \text{request}() \textbf{ do} \\
&\quad \text{state} := \text{waiting}; \\
&\textbf{upon event } \text{pp2pDeliver}(\text{TOKEN}) \textbf{ do} \\
&\quad \textbf{if } \text{state} = \text{waiting} \textbf{ then} \\
&\quad \quad \text{state} := \text{CS}; \\
&\quad \quad \text{trigger } \text{ok}(); \\
&\quad \textbf{else} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next};; \\
&\textbf{upon event } \text{release}() \textbf{ do} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next};;
\end{aligned}
$$


- **Main disadvantage**: The token continues to cycle incessantly, consuming network resources and bandwidth even when no process is interested in accessing the CS.
- **Latency**: The waiting time (delay) varies from 0 (the token arrives immediately) to $N$ messages if the token has just been passed and must complete the entire loop before becoming available again.
#### Quorum-based Algorithm – Maekawa’s voting algorithm

Instead of asking permission from _all_ nodes (as in Lamport) or from a single coordinator, a process entering the CS must get acknowledgement (vote) only from a **specific subset of processes**, called the **Quorum or Voting Set ($V_i$)**.

**The Properties of Voting Sets ($V_i$)**

To ensure mutual exclusion and correct operation, voting sets must adhere to strict geometric/mathematical rules:
1. $p_i \in V_i$ (each process is part of its own voting set).
2. The *intersection* between any two sets is never empty: $V_i \cap V_j \neq \emptyset$. This ensures that there is always at least one "arbiter node" in common between two processes claiming to enter the CS, preventing conflicts.
3. All sets have the same size $|V_i| = K$ (fair load principle).
4. Each process $p_j$ belongs to the same number of sets $M$ (fair responsibility principle).

$$
\begin{aligned}
&\textbf{Init:} \\
&\quad \text{state} := \text{released}; \\
&\quad \text{voted} := \text{false}; \\
&\quad V_i := \text{get\_voting\_set}(i); \\
&\quad \text{replies} := \emptyset; \\
&\quad \text{pending} := \emptyset; \\
& \\
&\textbf{upon event } \text{request}() \textbf{ do} \\
&\quad \text{state} := \text{wanted}; \\
&\quad \textbf{for each } p_j \in (V_i \setminus \{p_i\}) \textbf{ do} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{REQ}, i) \text{ to } p_j; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{REQ}, j) \textbf{ do} \\
&\quad \textbf{if } \text{state} = \text{held} \lor \text{voted} = \text{true} \textbf{ then} \\
&\quad \quad \text{pending} := \text{pending} \cup \{p_j\}; \\
&\quad \textbf{else} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{ACK}, i) \text{ to } p_j; \\
&\quad \quad \text{voted} := \text{true}; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{ACK}, j) \textbf{ do} \\
&\quad \text{replies} := \text{replies} \cup \{p_j\}; \\
& \\
&\textbf{when } |\text{replies}| = |V_i| - 1 \textbf{ do} \\
&\quad \text{state} := \text{held}; \\
& \\
&\textbf{upon event } \text{release}() \textbf{ do} \\
&\quad \text{state} := \text{released}; \\
&\quad \text{replies} := \emptyset; \\
&\quad \textbf{for each } p_j \in (V_i \setminus \{p_i\}) \textbf{ do} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{REL}, i) \text{ to } p_j; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{REL}, j) \textbf{ do} \\
&\quad \textbf{if } |\text{pending}| > 0 \textbf{ then} \\
&\quad \quad \text{candidate} := \text{select\_next}(\text{pending}); \\
&\quad \quad \text{pending} := \text{pending} \setminus \{\text{candidate}\}; \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{ACK}, i) \text{ to candidate}; \\
&\quad \quad \text{voted} := \text{true}; \\
&\quad \textbf{else} \\
&\quad \quad \text{voted} := \text{false};
\end{aligned}
$$

Maekawa showed that to minimize the number of messages and balance the load:
$$
K \approx \sqrt{N} \quad \text{and} \quad M = K
$$
A rule of thumb for constructing the groups ($V_i$) is to arrange the $N$ processes in a matrix $\sqrt{N} \times \sqrt{N}$ and define the voting set $V_i$ as **the union of the row and column** in which the process $p_i$ is located.

<mark style="background:#d3f8b6">Example: </mark>
Let us consider a system composed by N = 4 processes. $M = K \approx \sqrt{4} = 2$  
Let's place processes in the matrix and compute $V_i$ 

| Process id | V               |
| ---------- | --------------- |
| 1          | $p_1, p_2, p_3$ |
| 2          | $p_1, p_2, p_4$ |
| 3          | $p_1, p_3, p_4$ |
| 4          | $p_2, p_3, p_4$ |
![[Dependable distributed system-1793812092141.webp]]

>[!warning] 
>Maekawa's algorithm is not deadlock-free!
>If two processes ($p_1$ and $p_4$) simultaneously request the resource and block each other by obtaining partial votes from the common nodes without being able to complete their quorum, the system becomes stuck in a structural deadlock.

## Failure Detection and Leader Election

#### Failure detection
A **Failure Detector** is an oracle providing information about the failure state of a process. Failure detector is generally described by two properties: 
- **Accuracy**:  the ability to avoid mistakes in the detection;
- **Completeness**: informally is ability to detect all failures.
![[Dependable distributed system-1793871598316.webp|644]]
###### Failure detectors classification
Combining the different levels of Accuracy and Completeness, detectors are divided into classes:

- **Completeness**:
- _Strong_: Sooner or later, every process that crashes is permanently suspected by _all_ healthy processes.
- _Weak_: Sooner or later, every process that crashes is permanently suspected by _at least_ one healthy process.

- **Accuracy**:
- _Strong_: Healthy processes are **never** suspected.
- _Weak_: There is at least one healthy process that is never suspected.

- **Eventual Accuracy ($\diamond$)**: Accuracy is not required immediately, but applies "after a certain time" (_eventually_):
- **Eventual Strong Accuracy** ($\diamond S$): There is a time after which no correct process is suspected by another correct process.


### The Class Matrix:

- **P (Perfect Failure Detector)**: Strong completeness + Strong accuracy (requires a synchronous system).
- **S (Strong Failure Detector)**: Strong completeness + Weak accuracy.
- **Q (Quasi-perfect Failure Detector)**: Weak completeness + Strong accuracy.
- **W (Weak Failure Detector)**: Weak completeness + Weak accuracy.
- The diamond versions ($\diamond P, \diamond S, \diamond Q, \diamond W$) indicate that the accuracy properties hold only partially (typical of partial synchrony).
![[Dependable distributed system-1793871629365.webp|607]]
###### Perfect failure detector
They operate in synchronous systems with crash-stop failures. Using its own clock and the bound of the synchrony model, a process can infer if another process is crashed. 
- _PFD1 (Strong Completeness)_: Every process that crashes is permanently detected by every correct process.
- _PFD2 (Strong Accuracy)_: If one process $p$ is detected by any process, then $p$ has crashed. 
$$
\begin{aligned}
&\textbf{Algorithm 2.5: } \text{Exclude on Timeout} \\
&\textbf{Implements:} \text{ PerfectFailureDetector, instance } \mathcal{P}. \\
&\textbf{Uses:} \text{ PerfectPointToPointLinks, instance } pl. \\
& \\
&\textbf{upon event } \langle \mathcal{P}, \text{Init} \rangle \textbf{ do} \\
&\quad \text{alive} := \Pi; \\
&\quad \text{detected} := \emptyset; \\
&\quad \text{starttimer}(\Delta); \\
& \\
&\textbf{upon event } \langle \text{Timeout} \rangle \textbf{ do} \\
&\quad \textbf{forall } p \in \Pi \textbf{ do} \\
&\quad \quad \textbf{if } (p \notin \text{alive}) \land (p \notin \text{detected}) \textbf{ then} \\
&\quad \quad \quad \text{detected} := \text{detected} \cup \{p\}; \\
&\quad \quad \quad \textbf{trigger } \langle \mathcal{P}, \text{Crash} \mid p \rangle; \\
&\quad \quad \textbf{trigger } \langle pl, \text{Send} \mid p, [\text{HEARTBEATREQUEST}] \rangle; \\
&\quad \text{alive} := \emptyset; \\
&\quad \text{starttimer}(\Delta); \\
& \\
&\textbf{upon event } \langle pl, \text{Deliver} \mid q, [\text{HEARTBEATREQUEST}] \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle pl, \text{Send} \mid q, [\text{HEARTBEATREPLY}] \rangle; \\
& \\
&\textbf{upon event } \langle pl, \text{Deliver} \mid p, [\text{HEARTBEATREPLY}] \rangle \textbf{ do} \\
&\quad \text{alive} := \text{alive} \cup \{p\};
\end{aligned}
$$
This algorithm implements a **Perfect Failure Detector** ($\mathcal{P}$) using point-to-point communication channels ($pl$). Its purpose is to monitor processes in the system and detect crashes with absolute accuracy and completeness.
* **Initialization (`Init`)**: 
  - `alive := Π`: Initially assumes all processes in the system are alive.
  - `detected := ∅`: No process is marked as crashed yet.
  - `starttimer(Δ)`: Starts a periodic heartbeat timer with a interval $\Delta$.
* **Timeout Handling (`Timeout`)**: 
  - The algorithm periodically checks every process $p \in \Pi$.
  - If a process $p$ is neither in the `alive` set nor already marked in `detected`, it means $p$ failed to send a heartbeat within the last interval. The detector marks it as crashed (`detected := detected ∪ {p}`), triggers a crash indication ($\langle \mathcal{P}, \text{Crash} \mid p \rangle$), and sends a heartbeat request to check its status.
  - It then resets `alive := ∅` for the next round and restarts the timer.
* **Responding to Heartbeats (`HEARTBEATREQUEST` / `HEARTBEATREPLY`)**: 
  - When a process receives a `HEARTBEATREQUEST`, it immediately replies with a `HEARTBEATREPLY`.
  - When a process receives a `HEARTBEATREPLY` from $p$, it adds $p$ back to the `alive` set (`alive := alive ∪ {p}`), confirming that $p$ is still operational.

#### Eventually perfect failure detectors 
It operates under conditions of **partial synchrony**. Before an unknown time $t$, the system behaves asynchronously (the detector can make errors and incorrectly suspect live nodes). After time $t$, the system becomes synchronous and the suspicions become accurate.
- _EPFD1 (Strong Completeness)_: Every crashed process is permanently suspected.
- _EPFD2 (Eventual Strong Accuracy)_: After time $t$, no valid process is suspected.
To avoid false alarms due to the variability of the asynchronous network, the algorithm adopts an incremental timeout: each time a node is incorrectly suspected of being alive due to a timeout that is too short, the delay value is increased ($\text{delay} = \text{delay} + \Delta$), ensuring convergence to accuracy when the network stabilizes.

$$
\begin{aligned}
&\textbf{Algorithm 2.7: } \text{Increasing Timeout} \\
&\textbf{Implements:} \text{ EventuallyPerfectFailureDetector, instance } \diamond\mathcal{P}. \\
&\textbf{Uses:} \text{ PerfectPointToPointLinks, instance } pl. \\
& \\
&\textbf{upon event } \langle \diamond\mathcal{P}, \text{Init} \rangle \textbf{ do} \\
&\quad \text{alive} := \Pi; \\
&\quad \text{suspected} := \emptyset; \\
&\quad \text{delay} := \Delta; \\
&\quad \text{starttimer}(\text{delay}); \\
& \\
&\textbf{upon event } \langle \text{Timeout} \rangle \textbf{ do} \\
&\quad \textbf{if } \text{alive} \cap \text{suspected} \neq \emptyset \text{ then} \\
&\quad \quad \text{delay} := \text{delay} + \Delta; \\
&\quad \textbf{forall } p \in \Pi \text{ do} \\
&\quad \quad \textbf{if } (p \notin \text{alive}) \land (p \notin \text{suspected}) \text{ then} \\
&\quad \quad \quad \text{suspected} := \text{suspected} \cup \{p\}; \\
&\quad \quad \quad \textbf{trigger } \langle \diamond\mathcal{P}, \text{Suspect} \mid p \rangle; \\
&\quad \quad \textbf{else if } (p \in \text{alive}) \land (p \in \text{suspected}) \text{ then} \\
&\quad \quad \quad \text{suspected} := \text{suspected} \setminus \{p\}; \\
&\quad \quad \quad \textbf{trigger } \langle \diamond\mathcal{P}, \text{Restore} \mid p \rangle; \\
&\quad \quad \textbf{trigger } \langle pl, \text{Send} \mid p, [\text{HEARTBEATREQUEST}] \rangle; \\
&\quad \text{alive} := \emptyset; \\
&\quad \text{starttimer}(\text{delay}); \\
& \\
&\textbf{upon event } \langle pl, \text{Deliver} \mid q, [\text{HEARTBEATREQUEST}] \rangle \text{ do} \\
&\quad \textbf{trigger } \langle pl, \text{Send} \mid q, [\text{HEARTBEATREPLY}] \rangle; \\
& \\
&\textbf{upon event } \langle pl, \text{Deliver} \mid p, [\text{HEARTBEATREPLY}] \rangle \textbf{ do} \\
&\quad \text{alive} := \text{alive} \cup \{p\};
\end{aligned}
$$

This algorithm implements an **Eventually Perfect Failure Detector ($\diamond\mathcal{P}$)**. Unlike a _perfect_ failure detector (which makes no mistakes), an eventually perfect failure detector can make temporary mistakes during periods of asynchrony (such as suspecting a process that is still alive), but guarantees that it eventually becomes accurate once the system reaches periods of synchrony.

1. **Initialization** (`Init`)
- `alive := Π`: Initially, the algorithm assumes that all processes in the system set ($\Pi$) are alive.
- `suspected := ∅`: No process is initially suspected of having crashed.
- `delay := Δ`: Sets an initial timeout duration.
- `starttimer(delay) `: Starts the timer using the current delay value.
    
2. **Timeout Handling** (`Timeout`)
This is the core mechanism that makes the algorithm adaptive:
- **Checking for past mistakes (`alive \cap suspected \neq \emptyset`)**: If a process that was previously suspected of crashing successfully replies during the current interval (meaning it is actually in the `alive` set), it implies the timeout interval was too short for network conditions. Consequently, **the delay is increased** (`delay := delay + Δ`).
- **Updating Suspicions (`forall p \in \Pi`)**:
    - If a process $p$ is neither in `alive` nor already in `suspected`, it is added to the `suspected` set, and a `Suspect` indication is triggered (`trigger` < $\diamond\mathcal{P}$, $\text{Suspect} \mid p >$).
    - If a process was previously suspected but has now responded (meaning it is back in `alive`), it is removed from the `suspected` set, and a `Restore` indication is triggered (`trigger`$< \diamond\mathcal{P}, \text{Restore} \mid p >$).    
- **Broadcasting Heartbeats (`HEARTBEATREQUEST`)**: The process sends a new heartbeat request to all nodes, resets the `alive` set to empty (`alive := ∅`), and restarts the timer with the updated (or maintained) `delay`.

 2. **Message Delivery** (`Deliver`)
- When a node receives a `HEARTBEATREQUEST`, it immediately responds with a `HEARTBEATREPLY`.
- When a monitoring node receives a `HEARTBEATREPLY` from $p$, it adds $p$ back into the `alive` set (`alive := alive ∪ {p}`).

###### Correctness 
 1. **Strong Completeness**
	- **What this means:** _"If a process crashes, it stops sending messages. Consequently, the process will be suspected by every healthy process, and no process will change its mind again."
	- **Explanation:** This property is **always true** from the beginning (it doesn't need the word "eventually"). If a node truly dies, it will no longer respond to heartbeat checks, so sooner or later all healthy nodes will notice its absence and permanently add it to the suspect list.

 2. **Eventual Strong Accuracy**
	- **What this means:** _"After time $T$, the system becomes synchronous (i.e., messages between healthy processes arrive within a guaranteed maximum time). If $p$ was suspected in error by $q$, then $q$ will revise its suspicion."
	- **Why is it "eventual"?** This is where the temporal aspect of $\diamond \mathcal{P}$ comes into play.
		- _Before time $T$_ (when the network is asynchronous and unpredictable), the system can make mistakes: a live node can be mistakenly suspected simply because the network delayed a message or because the timeout was set too short.
		- _After time $T$, the magic of **eventually** comes true_: the network stabilizes and becomes synchronous. Messages travel within a certain time limit. If $q$ had wrongly suspected a live node $p$, as soon as it receives a real message from $p$, it will realize its error and **revise its suspicion**.

#### Leader election 
In distributed systems, continuously monitoring the state of all nodes can be cumbersome. Often, to coordinate actions (e.g., in consensus or transactional protocols), it's enough to know that **at least one process is alive** and acting as the coordinator (_leader_).
- **LE1 (Eventual Detection)**: Either there are no valid processes, or a valid process is eventually elected as the leader.
- **LE2 (Accuracy)**: If a process is the leader, all previously elected leaders have certainly failed.
**Monarchical Implementation ("Monarchical Leader Election")**: Uses a Perfect Failure Detector ($\mathcal{P}$). Each node maintains a list of suspected processes and chooses as the leader the alive process with the highest identifier (or rank) among the unsuspected ones ($\text{maxrank}(\Pi \setminus \text{suspected})$).

$$
\begin{aligned}
&\textbf{Algorithm 2.6: } \text{Monarchical Leader Election} \\
&\textbf{Implements:} \text{ LeaderElection, instance } le. \\
&\textbf{Uses:} \text{ PerfectFailureDetector, instance } \mathcal{P}. \\
& \\
&\textbf{upon event } \langle le, \text{Init} \rangle \textbf{ do} \\
&\quad \text{suspected} := \emptyset; \\
&\quad \text{leader} := \bot; \\
& \\
&\textbf{upon event } \langle \mathcal{P}, \text{Crash} \mid p \rangle \textbf{ do} \\
&\quad \text{suspected} := \text{suspected} \cup \{p\}; \\
& \\
&\textbf{upon } \text{leader} \neq \text{maxrank}(\Pi \setminus \text{suspected}) \textbf{ do} \\
&\quad \text{leader} := \text{maxrank}(\Pi \setminus \text{suspected}); \\
&\quad \textbf{trigger } \langle le, \text{Leader} \mid \text{leader} \rangle;
\end{aligned}
$$
This algorithm implements a **Monarchical Leader Election** mechanism (`le`), which elects a unique leader among a set of processes $\Pi$ using a Perfect Failure Detector ($\mathcal{P}$). The core idea is that processes are ranked (e.g., by their identifier or priority), and the correct process with the highest rank is always chosen as the leader.
 1. **Initialization** (`Init`)
- `suspected` $:= \emptyset$: Initially, no processes are suspected of having crashed.
- `leader` $:= \bot$: The leader variable is undefined ($\bot$) at startup.
 1. **Handling Process Crashes** $\langle \mathcal{P}, \text{Crash} \mid p \rangle$
- When the underlying Perfect Failure Detector ($\mathcal{P}$) detects that a process $p$ has crashed, it triggers an event.
- The algorithm catches this event and adds $p$ to the `suspected` set (`suspected := suspected \cup \{p\}`).
 1. **Continuous Guard** ($\text{leader} \neq \text{maxrank}(\Pi \setminus \text{suspected})$)
- This is a reactive guard condition (`upon ... do`) that constantly monitors the set of active (non-suspected) processes: $\Pi \setminus \text{suspected}$.
- **Selecting the Leader**: It computes `maxrank(...)`, which identifies the process with the highest rank among the currently alive processes.
- **Updating and Notifying**: If the current `leader` differs from this newly calculated highest-rank process, the variable is updated (`leader := maxrank(...)`), and a notification is broadcasted via `trigger` $\langle le, \text{Leader} \mid \text{leader} \rangle$ to inform the system of the new leader.

![[Dependable distributed system-1793871318324.webp]]
#### Eventual leader election $\Omega$

In asynchronous or partially synchronous systems, it is not possible to guarantee a single, error-free leader from the outset. Therefore, the oracle $\Omega$ (_Eventual Leader Detector_) is introduced:
- _ELD1 (Eventual Accuracy)_: There is a time after which all correct processes place their trust (_trust_) in the same correct process.
- *ELD2 (Eventual Agreement)*: There is a time after which no two correct processes choose different correct leaders.

**Important note**: $\Omega$ does not guarantee immediate stability; leaders can temporarily change before converging on the final election (_stabilization_). 
There always exists at least one correct process, otherwise $\Omega$ cannot be built.
**Crash-Recovery Implementation**: When considering systems with crash-recovery failures (nodes can restart), epoch-based algorithms (generation/restart numbers) and fair-loss links are used to make nodes exchange heartbeats associated with the active epoch, ultimately electing the leader with the correct epoch and rank.

$$
\begin{aligned}
&\textbf{Algorithm 2.8: } \text{Monarchical Eventual Leader Detection} \\
&\textbf{Implements:} \text{ EventualLeaderDetector, instance } \Omega. \\
&\textbf{Uses:} \text{ EventuallyPerfectFailureDetector, instance } \diamond\mathcal{P}. \\
& \\
&\textbf{upon event } \langle \Omega, \text{Init} \rangle \textbf{ do} \\
&\quad \text{suspected} := \emptyset; \\
&\quad \text{leader} := \bot; \\
& \\
&\textbf{upon event } \langle \diamond\mathcal{P}, \text{Suspect} \mid p \rangle \textbf{ do} \\
&\quad \text{suspected} := \text{suspected} \cup \{p\}; \\
& \\
&\textbf{upon event } \langle \diamond\mathcal{P}, \text{Restore} \mid p \rangle \textbf{ do} \\
&\quad \text{suspected} := \text{suspected} \setminus \{p\}; \\
& \\
&\textbf{upon } \text{leader} \neq \text{maxrank}(\Pi \setminus \text{suspected}) \textbf{ do} \\
&\quad \text{leader} := \text{maxrank}(\Pi \setminus \text{suspected}); \\
&\quad \textbf{trigger } \langle \Omega, \text{Trust} \mid \text{leader} \rangle;
\end{aligned}
$$

 1. **Initialization** (`Init`)
- `suspected := ∅`: Initially, no processes are suspected of having crashed.
- `leader := \bot`: The local leader variable is undefined ($\bot$) at startup.

 2. **Handling Failure Detector Events** (`Suspect` / `Restore`)
- **`upon event` $\langle \diamond\mathcal{P}, \text{Suspect} \mid p \rangle$**: When the underlying Eventually Perfect Failure Detector ($\diamond\mathcal{P}$) suspects that a process $p$ has crashed, the algorithm adds $p$ to the `suspected` set (`suspected := suspected \cup \{p\}`).
- **`upon event` $\langle \diamond\mathcal{P}, \text{Restore} \mid p \rangle$**: If the failure detector later realizes it made a mistake and restores $p$, the algorithm removes $p$ from the `suspected` set (`suspected := suspected \setminus \{p\}`).

 3. **Continuous Guard and Trust** ($\text{leader} \neq \text{maxrank}(\Pi \setminus \text{suspected})$)
- This reactive guard continuously monitors the set of currently trusted (non-suspected) processes: $\Pi \setminus \text{suspected}$.
- **Selecting the New Leader**: It computes `maxrank(...)` to find the process with the highest rank among the active processes.
- **Updating and Trusting**: If the calculated highest-rank process differs from the current `leader`, the local variable is updated, and a notification is sent out via `trigger` $\langle \Omega, \text{Trust} \mid \text{leader} \rangle$ to signal that the process now trusts this new leader.

![[Dependable distributed system-1793871932811.webp]]

$$
\begin{aligned}
&\textbf{Algorithm 2.9: } \text{Elect Lower Epoch} \\
&\textbf{Implements: } \text{EventualLeaderDetector, instance } \Omega \\
&\textbf{Uses: } \text{FairLossPointToPointLinks, instance } fll \\[6pt]
&\textbf{upon event } \langle \Omega, \text{Init} \rangle \text{ do} \\
&\quad \text{epoch} := 0; \\
&\quad \text{store}(\text{epoch}); \\
&\quad \text{candidates} := \emptyset; \\
&\quad \text{trigger } \langle \Omega, \text{Recovery} \rangle; \\[6pt]
&\textbf{upon event } \langle \Omega, \text{Recovery} \rangle \text{ do} \\
&\quad \text{leader} := \text{maxrank}(\Pi); \\
&\quad \text{trigger } \langle \Omega, \text{Trust} \mid \text{leader} \rangle; \\
&\quad \text{delay} := \Delta; \\
&\quad \text{retrieve}(\text{epoch}); \\
&\quad \text{epoch} := \text{epoch} + 1; \\
&\quad \text{store}(\text{epoch}); \\
&\quad \textbf{forall } p \in \Pi \text{ do} \\
&\qquad \text{trigger } \langle fll, \text{Send} \mid p, [\text{HEARTBEAT}, \text{epoch}] \rangle; \\
&\quad \text{candidates} := \emptyset; \\
&\quad \text{starttimer}(\text{delay}); \\
&\textbf{upon event } \langle \text{Timeout} \rangle \text{ do} \\
&\quad \text{newleader} := \text{select}(\text{candidates}); \\
&\quad \textbf{if } \text{newleader} \neq \text{leader} \textbf{ then}\\
&\qquad \text{delay} := \text{delay} + \Delta; \\
&\qquad \text{leader} := \text{newleader};\\
&\qquad \text{trigger } \langle \Omega, \text{Trust} \mid \text{leader} \rangle;\\
&\quad \textbf{forall } p \in \Pi \text{ do} \\
&\qquad \text{trigger } \langle fll, \text{Send} \mid p, [\text{HEARTBEAT}, \text{epoch}] \rangle;\\
&\quad \text{candidates} := \emptyset; \\
&\quad \text{starttimer}(\text{delay}); \\[6pt]
&\textbf{upon event } \langle fll, \text{Deliver} \mid q, [\text{HEARTBEAT}, ep] \rangle \text{ do} \\
&\quad \textbf{if exists } (s, e) \in \text{candidates} \text{ such that } s = q \land e < ep \textbf{ then} \\
&\qquad \text{candidates} := \text{candidates} \setminus \{(q, e)\}; \\
&\quad \text{candidates} := \text{candidates} \cup (q, ep);
\end{aligned}
$$
![[Dependable distributed system-1793872972616.webp]]![[Dependable distributed system-1793872976318.webp]]

## Broadcast communications

Until now we studied how a process can communicate with another process, but now we want to study how a process can communicate with an entire group of processes. This type of communication is called *broadcast communications*. So, now, we want that a process can communicate with a group of processes ensuring certain properties even when some processes may fail.
![[Dependable distributed system-1793873296104.webp]]

Let's remember some useful things for this chapter:
- System model: the model that describes the system and his characteristics;
- A distributed system is composed by many processes that communicate over a network. Processes can perform operations independently and do not necessarily share the same memory.
- When we define a communication abstraction, we need to specify which properties it must respect:
	- *Safety*;
	- *Liveness*.
- Until now we studied point-to-point communication abstractions like:
	- *Fair-loss* links;
	- *Stubborn* links;
	- *Perfect* links (reliable delivery, no duplication, no creation).
- The following were also addressed:
	- *Physical clocks*: physical clocks used to measure time.
	- *Logical clocks*: logical clocks used to establish an ordering of events.
	- *Failure Detector* (FD): component that allows you to suspect that a process has failed.
	- *Leader Election*: procedure that allows processes to elect a leader.

For a communication broadcast we can immagine a system with three processes: $\Pi = \{p_1, p_2, p_3\}$, if $p_1$ want to send a message $m$ to everyone,  it must make sure that it is received by $p_1, p_2, p_3$. But we can ave no failures and crash failures so we reach the goal $\rightarrow$ <mark style="background:rgba(5, 117, 197, 0.2)">How can we ensure that processes have a consistent view of the messages received, even in the presence of failures?</mark>

#### Best effort broadcast (BEB)

Idea: When a process wants to send a message, the system tries to deliver it to all processes.
The two main operations are:
- `BEB_Broadcast(msg)`: Used by a process to broadcast a message to everyone.
- `BEB_Deliver(msg)`: Used when a process receives and delivers the message to the application.
BEB has three properties:
1. **BEB1**: *Validity*, if a correct process broadcast a message $m$, then every correct process eventually delivers $m$;
2. **BEB2**: *No duplication*, no message is delivered more than once;
3. **BEB3**: *No creation*, if a process delivers a message $m$ with sender $s$, then $m$ was previously broadcast by process $s$. 

**System model**:
- Asynchronous system;
- Perfect links;
- Crash failures.
The important thing is that BEB doesn't use an inter-process re-transmission mechanism to recover messages that the sender failed to send before a crash. It simply sends the message to everyone via Perfect Links.
$$
\begin{aligned}
&\textbf{Algorithm 3.1: } \text{Basic Broadcast} \\
&\textbf{Implements:} \text{ BestEffortBroadcast, instance } beb. \\
&\textbf{Uses:} \text{ PerfectPointToPointLinks, instance } pl. \\[0.8em]
&\textbf{upon event } \langle beb, \text{Broadcast} \mid m \rangle \textbf{ do} \\
&\quad \textbf{forall } q \in \Pi \textbf{ do} \\
&\quad \quad \textbf{trigger } \langle pl, \text{Send} \mid q, m \rangle; \\[0.8em]
&\textbf{upon event } \langle pl, \text{Deliver} \mid p, m \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle beb, \text{Deliver} \mid p, m \rangle;
\end{aligned}
$$
###### Why BEB is correct?

The validity ensure that if a correct process broadcast a message $m$ then all correct process eventually delivery $m$. Why? because the sender process send the message at all the processes using perfect links. If the sender is correct then can complete the delivery to everyone. Furthermore, Perfect Links guarantee delivery to the correct recipients. Therefore, validity derives directly from the reliable delivery of Perfect Links and from the fact that the sender sends the message to every process.
The no-duplication ensure that a message can be delivered at most one time by the same process. We know that the message have unique identifier so the different messages must be distinguishable.
The no-creation assure that it must not be possible to deliver a message that no process has ever sent.

![[Dependable distributed system-1793876650116.webp]]

So we can notice: 
1. BEB guarantees message delivery as long as the sender doesn't fail. If the sender is correct, it successfully sends the message to all processes, and thanks to Perfect Links, the correct recipients receive it.
2. If the sender fails, the processes may disagree about whether the message was delivered or not.

#### Reliable broadcast (RB)
To overcome the BEB limitation we introduce **Reliable Broadcast**. **RB** retains the same properties as BEB, but adds a key property: **Agreement**. 
**Agreement**: If a correct process delivers a message ($m$), then all correct processes eventually deliver ($m$). If even one correct process has delivered $m$, then all other correct processes must eventually deliver it. It doesn't matter if the original sender subsequently fails. <mark style="background:#d3f8b6">This is a fundamental difference from BEB.</mark>
![[Dependable distributed system 1-1793896507581.webp]]
We have: $[ p_1, p_2, p_3 ]$
Process $p_1$ sends $m_1$, but fails before completing the sending.
With BEB:
- $p_2$ delivers $m_1$;
- $p_3$ does not deliver $m_1$.
This behavior is permitted by BEB, but not by RB.
Why? Because $p_2$ is a correct process and has delivered $m_1$. Consequently, according to Agreement, $p_3$, if correct, must also deliver it eventually. The **Agreement** property does not mean that all processes must deliver every message transmitted by a failing process. It means that, once a correct process has delivered a message, no other correct process can remain without delivering it.

>[!warning]
>**BEB** ensures the reliability of communication when the sender is correct. **RB** also ensures agreement between the correct processes on message delivery.

###### Lazy reliable broadcast
To understand why it is necessary, we must first remember the problem that RB is designed to solve: if a sender fails after sending a message to only some processes, we must ensure that the other correct processes can still receive it.
Consider a set of three processes:
$$\Pi = \{p_1, p_2, p_3\}$$

Suppose $p_1$ broadcasts a message $m_1$:
- $p_1$ succeeds in sending the message to $p_2$, but **crashes** before it can transmit it to $p_3$.
- With the **Best-Effort Broadcast (BEB)** abstraction:
  - $p_2$ receives and delivers $m_1$.
  - $p_3$ will never receive $m_1$.

> [!warning] Agreement Violation
>   This situation violates the **Agreement** property of *Reliable Broadcast*: if a correct process ($p_2$) delivers a message, then all correct processes (and thus also $p_3$) must eventually deliver it. To honor the **Agreement**, process $p_2$, after receiving the message, must cooperate by forwarding $m_1$ to other processes to fill the gap left by the failed sender. 

##### Role of the Perfect Failure Detector ($\mathcal{P}$)

The key question is: *when should a process intervene to retransmit?* In a synchronous system, we rely on the oracle of the **Perfect Failure Detector** ($\mathcal{P}$), characterized by two formal properties:

1. **Strong Completeness**:
   $$\forall p \in \Pi, \quad p \text{ crash} \implies \lozenge (\forall q \in \text{Correct}, \, q \text{ suspects } p)$$
   Every process that fails is sooner or later permanently suspected (detected) by all correct processes.

2. **Strong Accuracy**:
   $$\forall p \in \text{Correct}, \quad \forall q \in \Pi, \quad q \text{ never suspects } p$$
   No correct process is ever wrongly suspected.

> [!info] Intuition
> When $\mathcal{P}$ notifies the event $\langle \mathcal{P}, \text{Crash} \mid p \rangle$, there is mathematical certainty that $p$ has indeed crashed, avoiding unnecessary or premature retransmissions. This deterministic guarantee is achievable in the **synchronous model**, where the upper bounds on transmission and computation times are known a priori.

$$
\begin{aligned}
&\textbf{Algorithm 3.2: } \text{Lazy Reliable Broadcast} \\
&\textbf{Implements:} \text{ ReliableBroadcast, instance } rb. \\
&\textbf{Uses:} \\
&\quad \text{BestEffortBroadcast, instance } beb; \\
&\quad \text{PerfectFailureDetector, instance } \mathcal{P}. \\[0.8em]
&\textbf{upon event } \langle rb, \text{Init} \rangle \textbf{ do} \\
&\quad \text{correct} := \Pi; \\
&\quad \text{from}[p] := [\emptyset]^N; \\[0.8em]
&\textbf{upon event } \langle rb, \text{Broadcast} \mid m \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{Data}, \textit{self}, m] \rangle; \\[0.8em]
&\textbf{upon event } \langle beb, \text{Deliver} \mid p, [\text{Data}, s, m] \rangle \textbf{ do} \\
&\quad \textbf{if } m \notin \text{from}[s] \textbf{ then} \\
&\quad \quad \textbf{trigger } \langle rb, \text{Deliver} \mid s, m \rangle; \\
&\quad \quad \text{from}[s] := \text{from}[s] \cup \{m\}; \\
&\quad \quad \textbf{if } s \notin \text{correct} \textbf{ then} \\
&\quad \quad \quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{Data}, s, m] \rangle; \\[0.8em]
&\textbf{upon event } \langle \mathcal{P}, \text{Crash} \mid p \rangle \textbf{ do} \\
&\quad \text{correct} := \text{correct} \setminus \{p\}; \\
&\quad \textbf{forall } m \in \text{from}[p] \textbf{ do} \\
&\quad \quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{Data}, p, m] \rangle;
\end{aligned}
$$
The **Lazy Reliable Broadcast** algorithm implements a reliable broadcast abstraction ($rb$) using a **Best-Effort Broadcast** ($beb$) protocol and a **Perfect Failure Detector** ($\mathcal{P}$).
Its key characteristic is "laziness": instead of eagerly re-broadcasting every message to all processes immediately (which would consume high network bandwidth), processes only re-broadcast a message if its original sender is suspected or detected to have crashed. This ensures **Reliable Broadcast** properties (Validity, Agreement, Integrity) with lower message overhead under normal, crash-free conditions.

 **Components and State Variables**:
- **`correct`**: A set initialized to $\Pi$ (all processes in the system). It tracks which processes are currently believed to be correct (not crashed).
- **`from[p]`**: An array/dictionary mapping each process $p$ to a set of messages received from $p$. This prevents duplicate deliveries and keeps track of what has been heard from whom.
- **`beb` (Best-Effort Broadcast)**: A lower-level communication primitive that guarantees message delivery if the sender does not crash, but does not guarantee atomic agreement across all correct processes on its own.
- **$\mathcal{P}$ (Perfect Failure Detector)**: A failure detector module that outputs permanent crash suspicions (never makes mistakes).
**Detailed Event Breakdown**
 1. **Initialization**: When the initialization event $\langle rb, \text{Init} \rangle$ occurs, the system sets the set of correct processes to all nodes by letting $correct := \Pi$, and initializes the message-tracking structure with empty sets for each process by setting $\text{from}[p] := [\emptyset]^N$.
 2. **Initiating a Broadcast**: When a process wants to broadcast a message $m$ via $\langle rb, \text{Broadcast} \mid m \rangle$, it triggers a best-effort broadcast by encapsulating the message with its own identifier, activating the event $\langle beb, \text{Broadcast} \mid [\text{Data}, \textit{self}, m] \rangle$. In this way, the message is sent only once without immediately forcing others to repeat it.
 3. **Receiving a Message**: When a process receives a message via $\langle beb, \text{Deliver} \mid p, [\text{Data}, s, m] \rangle$, it performs a check to see if the message is new by checking the condition $m \notin from[s]$. If the condition is true:
	1. The process locally delivers the message by triggering $\langle rb, \text{Deliver} \mid s, m \rangle$.
    2. It updates the history by adding the message with $\text{from}[s] := \text{from}[s] \cup \{m\}$.
    3. It checks whether the original sender is considered incorrect by checking the condition $s \notin correct$. If the sender turns out to have crashed, the current process takes responsibility for forwarding the message by triggering $langle beb, \text{Broadcast} \mid [\text{Data}, s, m] \rangle$.
4.  **Handling Process Crashes**: When the failure detector signals the crash of a process through the event $\langle \mathcal{P}, \text{Crash} \mid p \rangle$: 
	1. The process is removed from the set of healthy nodes by updating $\text{correct} := \text{correct} \setminus \{p\}$.
	2. The system loops through all messages previously received from that process by executing $\forall m \in \text{from}[p]$ and re-broadcasting them via $\langle beb, \text{Broadcast} \mid [\text{Data}, p, m] \rangle$. This guarantees that if $p$ crashed mid-transmission, its pending messages are recovered and propagated to all remaining correct processes.

![[Dependable distributed system-1793878585352.webp|749]]
Consider the following sequence: $p_1$ performs an RB Broadcast of $m_1$, the message reaches $p_2$, subsequently $p_1$ performs an RB Broadcast of $m_2$, and before everyone has received the messages, $p_1$ crashes. Let us observe what happens. For $m_1$: $p_2$ has received $m_1$ and delivered it via RB; when $p_1$'s crash is detected, $p_2$ knows that $p_1$ originated $m_1$, and consequently, it retransmits $m_1$ via BEB, allowing other processes that had received $m_1$ to also participate in its dissemination. For $m_2$: the message was originated by $p_1$, but not all processes had received it yet; however, if at least one correct process had already received it, this process can retransmit it after the crash. The lesson to understand is that any process that has received the message becomes responsible for its further dissemination when the sender fails.

![[Dependable distributed system-1793878592836.webp|754]]![[Dependable distributed system-1793878600980.webp|761]]
The idea behind the algorithm is:
- Normally, a process does not retransmit messages it receives.
- However, if it discovers that the original processing of a message has failed, it re-transmits the messages that process had already sent.
Therefore, re-transmission occurs only when necessary. Hence the name Lazy.

###### Performance of lazy RB algorithm
- <u>Best case</u>: 1 BEB message per one RB message;
- <u>Worst case</u>: n-1 BEB message per one RB (this is the case with n-1 failures).

#### Reliable broadcast implementation in asynchronous system


This algorithm is called ""Eager reliable broadcast". It doesn't use a failure detector, this is important because we are in an asynchronous system. The logic of this algorithm is very easy: every process that receive a message for the first time re-send this message (immediately) to all the processes. So we don't have to wait that a process fail and we don't have to know if a process is failed or either. This is where the name Eager comes from, meaning "immediate".
**<mark style="background:rgba(5, 117, 197, 0.2)">Why does Eager RB guarantee Agreement?</mark>** Let's look at an example.
We have three processes: $[ p_1,p_2,p_3$]
Suppose $p_1$ sends $m_1$ to $p_2$, but fails before reaching $p_3$.
With BEB, $p_2$ receives $m_1$. With Eager RB, however, $p_2$ doesn't just deliver it: it immediately retransmits it to everyone. So $p_3$ can receive $m_1$ even if $p_1$ has failed.
**This is exactly the behavior we need to guarantee Agreement.**
$$
\begin{aligned}
&\textbf{Algorithm 3.3: } \text{Eager Reliable Broadcast} \\
&\textbf{Implements:} \text{ ReliableBroadcast, instance } rb. \\
&\textbf{Uses:} \text{ BestEffortBroadcast, instance } beb. \\[0.8em]
&\textbf{upon event } \langle rb, \text{Init} \rangle \textbf{ do} \\
&\quad \text{delivered} := \emptyset; \\[0.8em]
&\textbf{upon event } \langle rb, \text{Broadcast} \mid m \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{DATA}, \text{self}, m] \rangle; \\[0.8em]
&\textbf{upon event } \langle beb, \text{Deliver} \mid p, [\text{DATA}, s, m] \rangle \textbf{ do} \\
&\quad \textbf{if } m \notin \text{delivered} \textbf{ then} \\
&\quad \quad \text{delivered} := \text{delivered} \cup \{m\}; \\
&\quad \quad \textbf{trigger } \langle rb, \text{Deliver} \mid s, m \rangle; \\
&\quad \quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{DATA}, s, m] \rangle;
\end{aligned}
$$
###### Performance of Eager RB algorithm
<u>Best case = worst case</u> Why? because every process that receive the message for the first time re-transmits it. In a system with n processes, we have: 
- An initial diffusion from the sender;
- A diffusion from every other process that receive the message for the first time.
So the total number of broadcast operations is $n$. 
This feature incurs a higher communication cost than Lazy RB when there are no failures. However, we don't need a Failure Detector.

| Charcteristic      | Lazy RB             | Eager RB                 |
| ------------------ | ------------------- | ------------------------ |
| System             | Sync                | Async                    |
| Failure Detector   | Perfect FD          | Not necessary            |
| Re-transmit        | Only when necessary | At every first receive   |
| Best case          | 1 BEB per RB        | \(n\) BEB per RB         |
| Worst case         | \(n-1\) BEB per RB  | \(n\) BEB per RB         |
| Communication cost | Depend on failure   | Consistent with failures |

![[Dependable distributed system-1793890533236.webp|574]]
#### Uniform reliable broadcast (URB)
The agreement property only considers processes that don't fail. But what happens if a process delivers a message and then fails? Regular reliable broadcast does not require that all correct processes deliver that message.
<mark style="background:#affad1"> Example</mark>
Consider three processes: $[p_1,p_2,p_3$]
Suppose:
1. $p_1$ broadcasts $m$.
2. $p_2$ receives and delivers $m$.
3. $p_2$ fails.
4. $p_1$ and $p_3$ have not delivered $m$.
The Agreement property of RB is not violated! Why? Because the process that delivered the message, $p_2$, failed.
Agreement requires propagation only if the message was delivered by a correct process. Therefore, other correct processes may never receive that message. But from an application perspective, this can be a problem: one process has already performed an operation that the others are unaware of.

URB introduces a stronger property called **Uniform Agreement**: If any process, whether correct or failing, delivers a message $m$, then all correct processes must eventually deliver $m$.
URB maintains the properties of BEB and RB, but replaces Agreement with Uniform Agreement.

>[!important] Difference between RB and URB
RB considers delivery made by a correct process. URB considers delivery made by any process, correct or faulty. For this reason, Uniform Agreement is a stronger property than Agreement.


![[Dependable distributed system 1-1793891442619.webp|696]]![[Dependable distributed system 1-1793891478751.webp|785]]
The goal is to verify which behaviors are allowed by the three abstractions. The yellow message represents the message whose delivery is being analyzed. The slide highlights two situations:
1. **First situation**:
The yellow message was sent by \(p_1\). In this case, the situation can respect BEB but not necessarily RB. If \(p_1\) fails after sending the message to only a few processes, BEB allows the processes to have received different information. RB, however, requires Agreement if a correct process delivered the message.

2. **Second situation**:
The slide shows that, if the process that delivered the message is destined to fail, the situation can respect RB but not URB. This is because RB does not enforce Agreement when the message was delivered only by one process that subsequently fails. URB, however, requires all correct processes to deliver the message.

**The criteria to be applied to the examples is always the same**:
- *BEB*: checks Validity, No Duplication, and No Creation.
- *RB*: also checks Agreement.
- *URB*: Uniform Agreement verification, which also considers faulty processes.

###### Implementation in a synchronous system
$$
\begin{aligned}
&\textbf{Algorithm 3.4: } \text{All-Ack Uniform Reliable Broadcast.  } \\
&\textbf{Implements:} \text{ UniformReliableBroadcast, instance } urb. \\
&\textbf{Uses:} \text{ BestEffortBroadcast, instance } beb. \\
&\quad \text{PerfectFailureDetector, instance } \mathcal{P}. \\[0.8em]
&\textbf{upon event } \langle urb, \text{Init} \rangle \textbf{ do} \\
&\quad \text{delivered} := \emptyset; \\
&\quad \text{pending} := \emptyset; \\
&\quad \text{correct} := \Pi; \\
&\quad \textbf{forall } m \textbf{ do } \text{ack}[m] := \emptyset; \\[0.8em]
&\textbf{upon event } \langle urb, \text{Broadcast} \mid m \rangle \textbf{ do} \\
&\quad \text{pending} := \text{pending} \cup \{(\text{self}, m)\}; \\
&\quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{DATA}, \text{self}, m] \rangle; \\[0.8em]
&\textbf{upon event } \langle beb, \text{Deliver} \mid p, [\text{DATA}, s, m] \rangle \textbf{ do} \\
&\quad \text{ack}[m] := \text{ack}[m] \cup \{p\}; \\
&\quad \textbf{if } (s, m) \notin \text{pending} \textbf{ then} \\
&\quad \quad \text{pending} := \text{pending} \cup \{(s, m)\}; \\
&\quad \quad \textbf{trigger } \langle beb, \text{Broadcast} \mid [\text{DATA}, s, m] \rangle; \\[0.8em]
&\textbf{upon event } \langle \mathcal{P}, \text{Crash} \mid p \rangle \textbf{ do} \\
&\quad \text{correct} := \text{correct} \setminus \{p\}; \\[0.8em]
&\textbf{function } \text{candeliver}(m) \textbf{ returns } \text{Boolean} \textbf{ is} \\
&\quad \textbf{return } (\text{correct} \subseteq \text{ack}[m]); \\[0.8em]
&\textbf{upon exists } (s, m) \in \text{pending} \textbf{ such that } \text{candeliver}(m) \land m \notin \text{delivered} \textbf{ do} \\
&\quad \text{delivered} := \text{delivered} \cup \{m\}; \\
&\quad \textbf{trigger } \langle urb, \text{Deliver} \mid s, m \rangle;
\end{aligned}
$$
 **Overview & Dependencies**
- **Implements:** `UniformReliableBroadcast`, instance `urb`. This guarantees that if _any_ process delivers a message (even if the sender crashes), _all_ correct processes eventually deliver it.
- **Uses:**
    - `BestEffortBroadcast`, instance `beb` (to disseminate messages across the network).
    - `PerfectFailureDetector`, instance $\mathcal{P}$ (to accurately track which processes are still alive and which have crashed).

**State Variables** (`Init`)
When the algorithm starts, the local state is initialized as follows:
- $\text{delivered} := \emptyset$: Stores messages that have already been delivered to the application layer.
- $\text{pending} := \emptyset$: Tracks messages that have been broadcast or received but are waiting for the safety condition to be met before final delivery.
- $\text{correct} := \Pi$: The set of all processes in the system ($\Pi$) currently presumed to be correct (non-faulty).
- $\text{ack}[m] := \emptyset$: For each message $m$, a set recording which processes have acknowledged or relayed it.

 **Core Events & Logic**
- **Initiating a Broadcast:**
    $$\textbf{upon event } \langle urb, \text{Broadcast} \mid m \rangle \text{ do}$$
    - Adds $(\text{self}, m)$ to the local `pending` set.
    - Triggers a Best-Effort Broadcast (`beb`) to send the data payload `[DATA, self, m]` to all processes.
        
- **Receiving via Best-Effort Broadcast:**
    $$\textbf{upon event } \langle beb, \text{Deliver} \mid p, [\text{DATA}, s, m] \rangle \text{ do}$$
    - Adds the sender $p$ to the acknowledgment set for message $m$ ($\text{ack}[m] := \text{ack}[m] \cup \{p\}$).
    - If the message-sender pair $(s, m)$ is not yet in `pending`, it adds it and immediately relays it using `beb` to ensure network-wide propagation.
        
- **Handling Process Crashes:**
    $$\textbf{upon event } \langle \mathcal{P}, \text{Crash} \mid p \rangle \text{ do}$$
    - When the failure detector $\mathcal{P}$ suspects/detects that process $p$ has crashed, $p$ is removed from the local `correct` set ($\text{correct} := \text{correct} \setminus \{p\}$). This ensures the algorithm stops waiting for acknowledgments from dead processes.
        
- **Check for Delivery Readiness:**
    $$\textbf{function } \text{candeliver}(m) \text{ returns } \text{Boolean} \text{ is}$$
    - Returns `true` if and only if every process currently in the `correct` set has acknowledged message $m$ ($\text{correct} \subseteq \text{ack}[m])$.
        
- **Delivering the Message:**
    $$\textbf{upon exists } (s, m) \in \text{pending} \text{ such that } \text{candeliver}(m) \land m \notin \text{delivered} \text{ do}$$
    - Once all correct nodes acknowledge a pending message and it hasn't been delivered yet:
        - Adds $m$ to the `delivered` set ($\text{delivered} := \text{delivered} \cup \{m\}$).
        - Triggers the upper-layer delivery event $\langle urb, \text{Deliver} \mid s, m \rangle$.

###### Implementation in asynchronous system
In an asynchronous system, we can't know for sure whether a process has failed or is simply very slow. Therefore, we can't use a Perfect Failure Detector in the same way. So we put a rule: 
>[!Warning]
>We need to assume a majority of correct processes.
Supponiamo che il sistema contenga \(n\) processi.

The majority of processes is correct when: $\boxed{ \#Correct>\frac{n}{2} }$
For <mark style="background:#affad1">example</mark>, if we have five processes: $\Pi=\{p_1,p_2,p_3,p_4,p_5\}$
the majority requires at least three correct processes. Therefore:
- 5 correct processes: majority respected;
- 4 correct processes: majority respected;
- 3 correct processes: majority respected;
- 2 correct processes: majority not respected.
![[Dependable distributed system 1-1793892399517.webp|601]]

A majority allows for a large enough set of correct processes to ensure that the confirmations collected don't come exclusively from processes destined to fail. The idea is to use a quorum, i.e., a minimum number of processes that must confirm receipt of the message.

| Implementation | Hypothesis                 |
| -------------- | -------------------------- |
| Sync system    | Perfect Failure Detector   |
| Async system   | Correct processes majority |

>[!Question] 
>Can we devise a uniform reliable broadcast algorithm for a partially synchronous system (using an eventually perfect failure detector) but without the assumption of a majority of correct processes?

## Probabilistic broadcast

So far, we've studied algorithms that attempt to guarantee reliability properties. The problem is that these guarantees can be very costly when the process group becomes very large. Probabilistic Broadcast follows a different philosophy: It does not guarantee delivery of every message to every process, but it does attempt to guarantee a very high probability of delivery.

**Properties**: 
1. PB1: *Probabilistic validity*, there is a positive value $\epsilon$ such that when a correct process broadcasts a message $m$, the probability that every correct process eventually delivers $m$ is at least $1- \epsilon$ ;
2. PB2: *No duplication*, no message is delivered more than once;
3. PB3: *No creation*: if a process delivers a message $m$ with sender $s$, then $m$ was previously broadcast by process $s$.

The system is not completely reliable. Unlike RB and URB, we cannot deterministically guarantee that all correct processes will deliver every message.
This solution is particularly interesting when the group is:
- Very large.
- Dynamic, meaning processes can join and leave the group.
Imagine a network consisting of thousands or millions of devices. Sending a message to everyone and waiting for individual confirmation from each can become extremely expensive. To achieve high reliability, RBs and URBs may require re-transmission and acknowledgement mechanisms. As the number of processes increases, the number of ACKs also increases.
The issue, therefore, is scalability.
#### ACK Implosion

In traditional reliable broadcast protocols, when a process receives a message, it may send an acknowledgment (ACK) to the original sender to confirm that the message has been received.
However, when the number of processes in the system becomes very large, this mechanism can create a scalability problem known as **ACK implosion**.
Suppose a process broadcasts a message to $N$ processes. If every receiver sends an acknowledgment directly to the original sender, that sender may receive a very large number of ACK messages almost simultaneously.
This can overload the sender and its communication resources.

> [!important] ACK Implosion  
> ACK implosion occurs when a large number of acknowledgment messages are sent to the same process, potentially overwhelming it and creating a communication bottleneck.

#### ACK Tree
One possible solution is to organize acknowledgments using a tree structure. Instead of sending every ACK directly to the original sender, processes are organized hierarchically:
- The leaves of the tree represent processes that receive the broadcast message.
- Each intermediate node collects acknowledgments from its children.
- An intermediate node sends an aggregated acknowledgment to its parent.
- Eventually, the root receives the aggregated information.
For <mark style="background:#affad1">example</mark>, in a binary tree, each intermediate node can collect acknowledgments from two children and forward the result to its parent. This reduces the number of acknowledgment messages that the original sender must process directly.
However, this solution introduces some disadvantages:
- **Additional latency:** acknowledgments must travel through multiple levels of the tree.
- **Tree maintenance:** the tree must be maintained as processes join or leave the system.
- **Failure management:** if an intermediate node fails, the acknowledgment structure may need to be reorganized.
The ACK Tree reduces the load on the original sender, but it introduces additional complexity.

#### Probabilistic broadcast
The idea is to use **gossip-based dissemination**: instead of contacting every process directly, each process forwards the message to a limited number of randomly selected processes.
This reduces communication overhead and makes the protocol more scalable.

The Probabilistic Broadcast abstraction provides two operations:
- `Pb_Broadcast(m)`: used by a process to broadcast message $m$.
- `Pb_Deliver(m)`: used to notify a process that message $m$ has been delivered

##### Gossip Dissemination

Probabilistic Broadcast commonly relies on a technique called **gossip dissemination**. The main idea is that each process forwards a received message to a limited number of randomly selected processes. Instead of sending a message to every process in the system, each process selects only $k$ targets. The dissemination proceeds in multiple rounds.
###### How Gossip Dissemination Works

Suppose a process wants to broadcast a message:
1. The original process sends the message to $k$ randomly selected processes.
2. Each process that receives the message forwards it to another $k$ randomly selected processes.
3. This process continues for a limited number of rounds.
4. Eventually, the message spreads through a large portion of the system.

The two important parameters are:
- $k$: the number of randomly selected processes contacted by each process.
- $r$: the maximum number of dissemination rounds.

Increasing $k$ or $r$ generally increases the probability that the message reaches all processes, but also increases communication overhead.

###### Why Random Selection?
Random selection allows the protocol to avoid maintaining a complete list of all processes that must receive the message. It also makes dissemination more scalable in large and dynamic distributed systems. However, random selection means that the same process may be contacted by multiple senders.
Therefore, the number of distinct processes reached is not necessarily:

$$  
1+k+k^2+\cdots+k^r  
$$

This expression would represent the number of processes in a tree without overlapping recipients. In gossip dissemination, different processes may select the same targets, so the actual number of distinct processes reached depends on the random choices.

> [!example] Simple Example  
> Suppose each process forwards a message to $k=2$ randomly selected processes.
> 
> - In the first round, the original sender contacts two processes.
>     
> - In the second round, those two processes each contact two additional processes.
>     
> - In later rounds, the message continues spreading.
>     
> 
> Some processes may receive the same message from multiple senders. The protocol must therefore prevent duplicate deliveries.

#### Eager Probabilistic Broadcast

The lecture presents an implementation of Probabilistic Broadcast based on gossip dissemination.
The algorithm uses:
- `pb`: the Probabilistic Broadcast abstraction.
- `fl`: Fair-Loss Point-to-Point Links.
- `delivered`: a local set containing all messages already delivered by the process.

The algorithm uses the following parameters:
- $k$: number of randomly selected targets.
- $r$: number of remaining gossip rounds.
- $s$: original sender of the message.
- $p$: process from which the current message was received.

##### Local State
Each process maintains a set:
$$  
delivered := {}  
$$
This set stores the messages that the process has already delivered. It is used to ensure that a message is delivered only once.
##### Gossip Procedure
The procedure `gossip(msg)` sends the message to $k$ randomly selected processes.
Conceptually:
```text
procedure gossip(msg):
    for each t in picktargets(k):
        trigger fl.Send(t, msg)
```
The function `picktargets(k)` returns $k$ distinct processes selected randomly, excluding the process itself.
##### When a Process Broadcasts a Message
When a process invokes:
```text
upon pb.Broadcast(m):
```
the algorithm performs the following operations:
1. Adds $m$ to the local `delivered` set.
2. Delivers the message locally by triggering `pb.Deliver(self, m)`.
3. Starts gossip dissemination by forwarding the message to randomly selected processes.

The message sent through the point-to-point links has the following structure:
```text
[GOSSIP, self, m, R]
```
Here:
- `GOSSIP` identifies the type of message.
- `self` identifies the original sender.
- `m` is the broadcast message.
- `R` is the initial number of gossip rounds.
The original sender delivers its own message immediately and then starts the dissemination process.
##### When a Process Receives a Gossip Message
When a process receives a gossip message:
```text
upon fl.Deliver(p, [GOSSIP, s, m, r]):
```
it checks whether the message $m$ has already been delivered.

**Case 1: The message has not been delivered yet**
If:

$$  
m \notin delivered  
$$
the process:
1. Adds $m$ to its `delivered` set.
2. Delivers the message by triggering `pb.Deliver(s, m)`.
3. Checks whether there are still gossip rounds remaining.

If:
$$  
r > 1  
$$
the process forwards the message again using:
```text
gossip([GOSSIP, s, m, r-1])
```
The number of remaining rounds is therefore decreased by one.

**Case 2: The message has already been delivered**
If:
$$  
m \in delivered  
$$
the process ignores the message.
It does not deliver it again and does not forward it again. This prevents duplicate deliveries and avoids unnecessary forwarding.


#### Summary: Probabilistic Broadcast vs. Traditional Reliable Broadcast

| Aspect             | Traditional Reliable Broadcast              | Probabilistic Broadcast                             |
| ------------------ | ------------------------------------------- | --------------------------------------------------- |
| Delivery guarantee | Deterministic properties                    | Probabilistic guarantee                             |
| Agreement          | Guaranteed according to the abstraction     | Not deterministically guaranteed                    |
| Dissemination      | Depends on the reliable broadcast algorithm | Randomized gossip                                   |
| Acknowledgments    | May require significant communication       | Does not require acknowledgments from every process |
| Scalability        | Can be affected by communication overhead   | Designed to scale in large systems                  |
| Main limitation    | Communication and coordination costs        | A small probability of incomplete dissemination     |

## Consensus
<p align="justify">
In the consensus problem a group of processes must agree on a value that has been proposed by one of them. Its an abstraction of a class of problems where processes start with their opinion and then converge on one of them. Its a fundamental problem. 
</p>
We have a set of initial value $\in$ {0, 1} and every process has to decide the same value in {0,1}, based on the initial proposals.

**Initial values**:

| $p_1$ | $p_2$ | $p_3$ | $p_4$ | $p_5$ | $p_6$ |
| ----- | ----- | ----- | ----- | ----- | ----- |
| 0     | 1     | 0     | 1     | 0     | 0     |
**Consensus Values**: (decided with a consensus protocol)

| $p_1$ | $p_2$ | $p_3$ | $p_4$ | $p_5$ | $p_6$ |
| ----- | ----- | ----- | ----- | ----- | ----- |
| 1     | 1     | 1     | 1     | x     | 1     |
This problem has four properties:
1. **C1**: *Termination*, every correct process eventually decides some value;
2. **C2**: *Validity*, if a process decides v, then v was proposed by some process;
3. **C3**: *Integrity*, no process decides twice;
4. **C4**: *Agreement*, no two correct processes decide differently.

>[!Important] FLP impossibility result
>No (deterministic) algorithm can guarantee to reach consensus in an asynchronous system, even if only one process may fail by crashing. 

#### A consensus implementation in synchronous systems: flooding consensus
**Basic idea**: the processes exchange their values and, when all the proposals of the correct processes are available, one value can be chosen. 
**Problem**: because of failures some values can be lost, so processes could choose among different sets.
**Solution**: a value is selected only when no failure happens during a round of communication.
Imagine we have $N$ processes, each with its own proposal. For <mark style="background:#d3f8b6">example</mark>: $p_1: 5 \qquad p_2: 3 \qquad p_3: 7$. We want all correct processes to decide the same value. The idea of flooding consensus is that:
1. Every process communicate its own proposal to everyone;
2. The processes collect the proposals received;
3. When they have enough information, they apply a deterministic function to choose the value to decide.
The rule for chose is "choose the min value of the proposal received", so in our example the chosen value is "3". 

##### Example 1 — nessun guasto
:p1 = 5, p2 = 3, p3 = 

| Round    | p1 send | p2 send | p3 send | Values p1 | Values p2 | Values p3 |
| -------- | ------- | ------- | ------- | --------- | --------- | --------- |
| 0 (init) | —       | —       | —       | {5}       | {3}       | {7}       |
| 1        | {5}     | {3}     | {7}     | {3,5,7}   | {3,5,7}   | {3,5,7}   |
| 2        | {3,5,7} | {3,5,7} | {3,5,7} | {3,5,7}   | {3,5,7}   | {3,5,7}   |
**min{3,5,7} = 3**
##### Esempio 2 
**p2 sends 3 only to p1 and then crashes**: the message for p3 is not sent. This is legal in crash-stop (a process can crash at any time).

|Round|p1 invia|p2 invia|p3 invia|Values p1|Values p2|Values p3|
|---|---|---|---|---|---|---|
|0|—|—|—|{5}|{3}|{7}|
|1|{5}|{3} _solo a p1_|{7}|**{3,5,7}**|💥 crashato|**{5,7}**|
|2|{3,5,7}|—|{5,7}|{3,5,7}|—|{3,5,7}|
- **After round 1**: p1 knows 3, p3 does not. If a decision were made here, p1 would decide **3** and p3 would decide **5** → **disagreement**, breach of agreement.
- **After round 2**: p1 passes on the 3 he received, p3 acquires it. Both have {3,5,7} → both decide **3**. ✔

$$\begin{aligned} &\textbf{Algorithm 5.1: } \text{Flooding Consensus} \\ &\textbf{Implements: } \text{Consensus, instance } c; \quad \textbf{Uses: } \text{BestEffort Broadcast, instance } beb; \text{ PerfectFD, instance } P \\ &\textbf{upon event } (c, \text{Init}) \text{ do} \\ &\quad \text{correct} := \Pi; \quad \text{round} := 1; \quad \text{decision} := \bot; \\ &\quad \text{receivedfrom} := [\emptyset]^N; \quad \text{proposals} := [\emptyset]^N; \quad \text{receivedfrom}[0] := \Pi; \\ &\textbf{upon event } (P, \text{Crash} \mid p) \text{ do} \\ &\quad \text{correct} := \text{correct} \setminus \{p\}; \\ &\textbf{upon event } (c, \text{Propose} \mid v) \text{ do} \\ &\quad \text{proposals}[1] := \text{proposals}[1] \cup \{v\}; \\ &\quad \text{trigger } (beb, \text{Broadcast} \mid [\text{PROPOSAL}, 1, \text{proposals}[1]]); \\ &\textbf{upon event } (beb, \text{Deliver} \mid p, [\text{PROPOSAL}, r, ps]) \text{ do} \\ &\quad \text{receivedfrom}[r] := \text{receivedfrom}[r] \cup \{p\}; \\ &\quad \text{proposals}[r] := \text{proposals}[r] \cup ps; \\ &\textbf{upon } \text{correct} \subseteq \text{receivedfrom}[\text{round}] \land \text{decision} = \bot \text{ do} \\ &\quad \text{if } \text{receivedfrom}[\text{round}] = \text{receivedfrom}[\text{round} - 1] \text{ then} \\ &\qquad \text{decision} := \min(\text{proposals}[\text{round}]); \\ &\qquad \text{trigger } (beb, \text{Broadcast} \mid [\text{DECIDED}, \text{decision}]); \\ &\qquad \text{trigger } (c, \text{Decide} \mid \text{decision}); \\ &\quad \text{else} \\ &\qquad \text{trigger } (beb, \text{Broadcast} \mid [\text{PROPOSAL}, \text{round} + 1, \text{proposals}[\text{round}]]); \\ &\qquad \text{round} := \text{round} + 1; \\ &\textbf{upon event } (beb, \text{Deliver} \mid p, [\text{DECIDED}, v]) \text{ such that } p \in \text{correct} \land \text{decision} = \bot \text{ do} \\ &\quad \text{decision} := v; \\ &\quad \text{trigger } (beb, \text{Broadcast} \mid [\text{DECIDED}, \text{decision}]); \\ &\quad \text{trigger } (c, \text{Decide} \mid \text{decision}). \end{aligned}$$

##### Correctness
 <u>Validity</u> and <u>integrity</u> follow from the properties of the communication channels. <u>Termination</u>: at most after N rounds the processes decide (there are at most N −1 crashes, so some round has no new crash). <u>Agreement</u>: correct processes apply the same deterministic function to the same set of values.

##### Performance
- **Best case:** one communication round;
- **Worst case**: $N^2$ messages exchanged for each communication step and at most $N$ rounds $\rightarrow N^3$ messages

#### Uniform consensus specification
In the consensus the agreement property establish that: No correct process decides differently from another correct process. The key constraint here is the word **correct**. <mark style="background:#b1ffff">What happens if a process breaks down (crashes) shortly after making a decision</mark>, perhaps deciding on a value $v_1$, while the other correct processes decide on $v_2$? In regular consensus, the property is technically respected if the deviating process crashes and is no longer "correct," but in real distributed systems this creates serious consistency problems. So this algorithm introduces the **Uniform agreement:** <u>No process (whether it is correct or has crashed) can decide a different value than another.</u>

$$\begin{aligned} &\textbf{Algorithm 5.3: } \text{Flooding Uniform Consensus} \\ &\textbf{Implements: } \text{UniformConsensus, instance } uc; \quad \textbf{Uses: } \text{BestEffort Broadcast, instance } beb; \text{ PerfectFD, instance } P \\ &\textbf{upon event } (uc, \text{Init}) \text{ do} \\ &\quad \text{correct} := \Pi; \quad \text{round} := 1; \quad \text{decision} := \bot; \quad \text{proposalset} := \emptyset; \quad \text{receivedfrom} := \emptyset; \\ &\textbf{upon event } (P, \text{Crash} \mid p) \text{ do} \\ &\quad \text{correct} := \text{correct} \setminus \{p\}; \\ &\textbf{upon event } (uc, \text{Propose} \mid v) \text{ do} \\ &\quad \text{proposalset} := \text{proposalset} \cup \{v\}; \\ &\quad \text{trigger } (beb, \text{Broadcast} \mid [\text{PROPOSAL}, 1, \text{proposalset}]); \\ &\textbf{upon event } (beb, \text{Deliver} \mid p, [\text{PROPOSAL}, r, ps]) \text{ such that } r = \text{round} \text{ do} \\ &\quad \text{receivedfrom} := \text{receivedfrom} \cup \{p\}; \\ &\quad \text{proposalset} := \text{proposalset} \cup ps; \\ &\textbf{upon } \text{correct} \subseteq \text{receivedfrom} \land \text{decision} = \bot \text{ do} \\ &\quad \text{if } \text{round} = N \text{ then} \\ &\qquad \text{decision} := \min(\text{proposalset}); \\ &\qquad \text{trigger } (uc, \text{Decide} \mid \text{decision}); \\ &\quad \text{else} \\ &\qquad \text{round} := \text{round} + 1; \quad \text{receivedfrom} := \emptyset; \\ &\qquad \text{trigger } (beb, \text{Broadcast} \mid [\text{PROPOSAL}, \text{round}, \text{proposalset}]). \end{aligned}$$
This algorithm implements uniform consensus in synchronous systems using a flooding strategy combined with a perfect failure detector ($P$) and best-effort broadcast ($beb$).
##### 1. Initializaton (`Init`)
Upon initialization:
- The `correct` set of processes is initialized to the full set $\Pi$.
- The `round` counter starts at $1$, `decision` is set to $\bot$ (undecided), `proposalset` is empty, and `receivedfrom` is empty.
##### 2. Failure Detection (`Crash`)
- When the perfect failure detector $P$ reports that a process $p$ has crashed, $p$ is removed from the `correct` set (`correct := correct \setminus \{p\}`).
- Thanks to the strong completeness property of the failure detector, no correct process waits indefinitely for a message from a crashed process, because the failed process is eventually removed from the `correct` set.
##### 3. Proposing a Value (`Propose`)
- When a value $v$ is proposed to the uniform consensus instance ($uc$), it is added to the local `proposalset`.
- The process then triggers a best-effort broadcast (`beb`) to send the proposal set for round $1$ to all processes.
##### 4. Delivering Messages (`Deliver`)
- Upon delivering a proposal message `[PROPOSAL, r, ps]` from process $p$ such that the round matches the current round ($r = \text{round}$):
    - The sender $p$ is added to `receivedfrom` (`receivedfrom := receivedfrom \cup \{p\}`).
    - The received proposal elements (`ps`) are merged into the local `proposalset` (`proposalset := proposalset \cup ps`).
##### 5. Round Progression and Decision (`upon correct ⊆ receivedfrom`)
When a process has heard from all currently correct processes (`correct ⊆ receivedfrom`) and has not yet decided (`decision = \bot`):
- **If `round = N` (Final Round):** The process computes the deterministic minimum of its accumulated proposal set (`decision := min(proposalset)`), triggers the decision event (`uc, Decide | decision`), and terminates. Uniform agreement is guaranteed because all processes that reach round $N$ share the exact same set of values in their `proposalset`.
- **If `round < N` (Intermediate Round):** The process increments the round counter (`round := round + 1`), resets `receivedfrom` to empty, and broadcasts the updated `proposalset` for the next round via best-effort broadcast.
###### Correctness & Performance Summary
- **Validity and Integrity:** Follow directly from the algorithm logic and the properties of best-effort broadcast.
- **Termination:** Ensured because all correct processes eventually reach round $N$ and decide in that round.
- **Uniform Agreement:** Holds because all processes reaching round $N$ possess the identical set of values in their `proposalset`.
- **Performance:** Requires $N$ communication steps and $O(N^3)$ messages for all correct processes to decide.

## Paxos 

>[!FLP] FLP
>In an asynchronous system, it is not possible to always guarantee that consensus will be achieved if even a single process can crash. 

In an asynchronous system we cannot assume that processes have a predictable speed or that messages arrive within a maximum time. <u>The silence of a process does not prove that that process has failed.</u>

What does FLP claim?
Imagine three processes that must decide on a common value:
- $p_1$ proposes $A$;
- $p_2$ proposes $B$;
- $p_3$ proposes $C$.
We would like all correct processes to reach a decision, choosing one of the proposed values. FLP demonstrates that, in a completely asynchronous system, if even a single crash is possible, we cannot design an algorithm that guarantees termination in every possible execution. **Caution**: This does not mean that consensus is always impossible to achieve. It means that we cannot guarantee that it will be achieved in all executions allowed by the asynchronous model. We can still guarantee <u>safety properties</u>, such as that two processes do not decide on different values. **The problem is ensuring that sooner or later someone decides.**

The paxos family of algorithms was introduced to provide a viable solution to consensus in asynchronous settings. The goal is to allow multiple processes to agree on a value, even in the presence of arbitrary delays and crashes. 

###### Properties of paxos's family
- **Safety** is always guaranteed.The protocol must not produce contradictory decisions, even when the network behaves badly.
- **Liveness** is guaranteed only when the system behaves well for sufficiently long periods: in other words, when network conditions allow messages to flow and processes to complete the protocol phases. This property is connected to the partial synchrony.
- **Only a value that has been proposed may be chosen**;
- **Only a single value is chosen**;
- **A process never learns that a value has been chosen unless it actually has been**: A process must have a concrete basis for knowing that a decision has been reached. It cannot simply assume that a value has been chosen because it received an isolated message. 

>[!important] Remember
Paxos does not contradict the FLP result. FLP says that we cannot guarantee termination in every possible asynchronous execution. Paxos guarantees safety and can progress when favorable conditions are met, such as when a proposer can communicate stably with a majority of acceptors.

##### System model
<u>The processes</u>:
- They can have arbitrary execution speeds;
- They can fail by interrupting their execution (crashing);
- They can restart after a crash.
<u>The messages</u>: 
- They can take an arbitrarily long time to be delivered;
- They can be duplicated;
- They can be lost;
- They are not corrupted.

>[!Observation] 
>Since all agents may fail after a value is chosen and then restart, a solution is impossible unless some information can be remembered by an agent that has failed and restarted.

#### The three roles of Paxos

1. **Proposers**: are processes that can propose a value;
	1. For <mark style="background:#affad1">example</mark>, we could have:
			- proposer $P_1$ proposing the value $v_1$;
			- proposer $P_2$ proposing the value $v_2$;
			- proposer $P_3$ proposing the value $v_3$.
	The proposer cannot directly impose its choice: it must participate in the protocol and obtain the necessary approval from the acceptors.
2. **Acceptors**: are the processes that participate in value selection. They receive proposals and can accept them. They are the heart of Paxos' security: the protocol must ensure that accepted proposals never lead to the choice of two different values; 
3. **Learners**: passively assist to the decision and obtain the final decided value. They are not responsible for proposing or accepting the value: they observe the decisions reached by the acceptors. For <mark style="background:#affad1">example</mark>, when a learner receives confirmation that a majority of acceptors have accepted a proposal, it can conclude that the corresponding value has been chosen. 

The easiest way to solve the problem is to have a single acceptor:
-  A proposer sends a proposal to the acceptor;
-  The acceptor choses the first proposed value it receives and informs everybody about its choice.

**But there's a fundamental problem: what happens if the only acceptor crashes?**
If the acceptor fails before communicating its decision, the system can no longer progress. There is no other acceptor that can replace it. Therefore, we need multiple acceptors so that the protocol can continue even if some of them fail.

**The problem of having multiple acceptors**:

Each proposer sends its proposal to a set of acceptors. However, messages do not necessarily arrive in the same order to all of them. 
For <mark style="background:#affad1">example</mark>, suppose we have three proposers:

$$
P_1 \rightarrow v_1
$$

$$
P_2 \rightarrow v_2
$$

$$
P_3 \rightarrow v_3
$$

and three acceptors:
$$
A_1,\ A_2,\ A_3
$$
<u></u>
It could happen that:
- $A_1$ receives $v_1$, $v_2$, and $v_3$;
- $A_2$ receives $v_2$ and $v_1$;
- $A_3$ receives only $v_3$.
The problem is obvious: each acceptor can receive a different set of proposals and can receive them in a different order. <mark style="background:#b1ffff">How can we ensure that acceptors don't choose different values?</mark> This is the central question that led to the construction of Paxos.

#### Evolution of rules
To prevent acceptors from choosing different values, strict rules and invariants are introduced.
>[!Initial Rule]
>**Initial rule:** An acceptor can accept at most one value, and a value is chosen when a majority of acceptors accept it. The majority is required to ensure that only one value is accepted.

**Trial (P1)**: An acceptor must accept the first proposal it receives.
**Problem** (<u>Deadlock</u>): If multiple values ​​are proposed concurrently by different proposers, none could reach the required majority. An acceptor must be able to accept multiple proposals. 
**Solution**: We keep track of the different proposals by assigning each one a unique proposal number (total sorting). A value is chosen when a single proposal with that value has been accepted by a <u>majority</u> of acceptors (**chosen proposal**). <mark style="background:rgba(173, 239, 239, 0.55)">We may allow multiple proposals to be accepted, but all accepted proposals must be of equal value.</mark>

**Trial (P2)**: If a proposal with value $v$ is chosen, every higher-numbered proposal that is chosen has value $v$.
To ensure P2, we go through two successive refinements:
- **P2a:** If a proposal with value $v$ is accepted, every higher-numbered proposal accepted by _any_ acceptor has value $v$.
	- _Problem:_ What happens if an acceptor $c$ doesn't see an accepted proposal with value $v$, and a new proposer wakes up proposing a different value $v'$ that $c$ is forced to accept for P1?
- **P2b:** If a proposal with value $v$ is chosen, every higher-numbered proposal **issued by any proposer** has value $v$.
To ensure $P2b$, we keep the following invariant for any value $v$ and number $n$:
- **P2c**: For any $v$ and $n$, if a proposal with value $v$ and number $n$ is issued, then there exists a set $S$ consisting of a majority of acceptors such that:
	- No acceptor in $S$ has accepted any proposal numbered less than $n$, or
	- $v$ is the value of the proposal with the highest number among all proposals numbered less than $n$ accepted by acceptors in $S$.

To maintain $P2c$, a proposer who wants to issue a proposal with number $n$ must first discover (or promise to bind) the value of the highest proposal less than $n$ that is accepted (or will be accepted) by a majority of acceptors. The protocol is divided into two main phases (each consisting of a request and a response):
1. **PHASE 1: Prepare / Promise**
	1. **Prepare Request:** A proposer chooses a new proposal version number $n$ (unique) and sends a prepare request $(\text{PREPARE}, n)$ to a majority of acceptors:
		- _(a)_ Can I make a proposal with number $n$?
		- _(b)_ If so, can you suggest a value for my proposal?
	2. **Acceptor Response (Promise):** When an acceptor receives $(\text{PREPARE}, n)$:
		- If $n$ is greater than any previously received prepare number, it responds with a **promise** to no longer accept any proposals numbered lower than $n$.
		- In its response, it suggests the value $v'$ of the highest-numbered proposal it has accepted so far (if any), or $\perp$ (bottom/null) if it has accepted nothing.
		- The response message is of the form:
			- $(\text{ACK}, n, n', v')$ if the acceptor has already accepted a proposal with number $n'$ and value $v'$.
			- $(\text{ACK}, n, \perp, \perp)$ if it has not accepted any proposals.
		- **NACK:** If the acceptor receives a prepare with a number $n$ lower than a $n'$ to which it has already responded, it sends a rejection $(\text{NACK}, n')$ indicating that the proposal is too old.
	
2. **PHASE 2: Accept / Accepted**
	1. **Accept Request:** If the proposer receives responses (ACK) from a **majority** of acceptors, it can proceed to issue an accept request $(\text{ACCEPT}, n, v)$ where:
		- _(a)_ $n$ is the number that appeared in the prepare request.
		- _(b)_ $v$ is the value of the proposal with the highest number among all the values ​​returned in the acceptor responses (or the proposer's own value if none of the acceptors had previously accepted any proposals).
	2. **Acceptor Acceptance:** If an acceptor receives an $(\text{ACCEPT}, n, v)$ request, it accepts the proposal unless it has already responded to a prepare request with a number **greater** than $n$.
3. **Learning phase**: Whenever an acceptor accepts a proposal, it responds to all learners by sending $(\text{ACCEPT}, n, v)$. A learner receives $(\text{ACCEPT}, n, v)$ from a majority of acceptors, decides on the value $v$, and sends $(\text{DECIDE}, v)$ to all other learners. Learners who receive $(\text{DECIDE}, v)$ learn and officially record the decided value.

**Liveness Issues and Solutions**: 
- <u>Competing Proposers</u> (Livelock): If two competing proposers continuously issue increasing numbers of prepares ($n_1, n_2, n_3 \dots$), they can invalidate each other, blocking the algorithm from terminating (neither one succeeds in completing phase 2). 
- <u>Practical Solution</u>: The proposers are "elected" or coordinated using an external Leader Election protocol. This way, the protocol operates as a 2-Phase Commit (or 3-Phase Commit in the event of leader failure).


## Ordered communication

In distributed systems, reliable broadcast ensures that all correct processes deliver the same set of messages, but does not impose any properties on the order of delivery.
⚠️ The Anomaly Problem: Without order guarantees, serious application inconsistencies can arise. For example, if a client first sends "reserve a seat" and then "cancel my reservation," but the messages are delivered reversed or processed by different servers in the wrong random order, the system risks canceling a reservation that never existed.
Ordered communications add one or more ordering properties to reliable broadcast, and are mainly divided into three categories: 
1. FIFO Order,
2. Causal Order, 
3. Total Order / Atomic Broadcast.

#### FIFO Broadcast
**FIFO Broadcast** ensures that messages sent from the same sender are delivered to all processes in the same order in which they were issued. 
The properties of the _FIFO Reliable Broadcast_ (`frb` instance) extend those of standard reliable broadcast:
- **FRB1 - FRB4:** Satisfy all the basic properties of reliable broadcast (Validity, Agreement, Integrity, Uniformity).
- **FRB5 (FIFO delivery):** If a process $p$ emits message $m_1$ before $m_2$ ($\text{broadcast}(m_1) \to \text{broadcast}(m_2)$), then no correct process will deliver $m_2$ if it has not previously delivered $m_1$.


$$
\begin{aligned}
&\textbf{Algorithm 3.12: Broadcast with Sequence Number}\\[4pt]
&\textbf{Implements: } \text{FIFOReliableBroadcast, instance } frb.\\
&\textbf{Uses: } \text{ReliableBroadcast, instance } rb.\\[6pt]
&\textbf{upon event } \langle frb, Init \rangle \textbf{ do}\\
&\qquad lsn := 0;\\
&\qquad pending := \emptyset;\\
&\qquad next := [1]^N;\\[6pt]
&\textbf{upon event } \langle frb, Broadcast \mid m \rangle \textbf{ do}\\
&\qquad lsn := lsn + 1;\\
&\qquad \textbf{trigger } \langle rb, Broadcast \mid [DATA, self, m, lsn] \rangle;\\[6pt]
&\textbf{upon event } \langle rb, Deliver \mid p, [DATA, s, m, sn] \rangle \textbf{ do}\\
&\qquad pending := pending \cup \{(s,m,sn)\};\\
&\qquad \textbf{while there exists } (s,m,sn) \in pending
\textbf{ such that } sn = next[s] \textbf{ do}\\
&\qquad\qquad next[s] := next[s] + 1;\\
&\qquad\qquad pending := pending \setminus \{(s,m,sn)\};\\
&\qquad\qquad \textbf{trigger } \langle frb, Deliver \mid s,m \rangle.
\end{aligned}
$$

The algorithm uses a local sequence counter and a pending data structure: Initialization: $lsn := 0$, $\text{pending} := \emptyset$, $\text{next} := [1]^N$. Broadcast ($m$):$$lsn := lsn + 1$$
The underlying layer is activated by sending $\text{rb.Broadcast}([DATA, \text{self}, m, lsn])$. Deliver ($p, [DATA, s, m, sn]$):
The message is inserted into $\text{pending}$. Using a while loop, messages are released and delivered (frb.Deliver) only when the sequence number $sn'$ exactly matches the expected value $\text{next}[s]$.

![[Dependable distributed system 1-1793988432290.webp]]
In the diagram, process $P_1$ transmits two messages in sequence:$m_1$ with local sequence number $lsn = 1$ ($\langle P_1, m_1, 1 \rangle$)$m_2$ with local sequence number $lsn = 2$ ($\langle P_1, m_2, 2 \rangle$)Each process maintains a next vector indicating the sequence number of the next message it expects to receive from each sender (initially set to $1$) and a dangling data structure to accumulate early-arrived messages.What happens in individual processesProcess $P_3$ (Arrival in order):First receives $m_1$ ($sn = 1$), which exactly matches the expected value in next [P1]. It is delivered immediately (Deliver), and next [P1] is incremented to $2$. Next, it receives $m_2$ ($sn = 2$), which corresponds to the new expected value. It is delivered immediately, bringing $3$ next to it. Process $P_2$ (Out of Order Arrival): Due to network delays, $m_2$ ($sn = 2$) arrives at $P_2$ before $m_1$ ($sn = 1$). Since $P_2$ expects the message with $sn = 1$ (next [P1] = 1), the arrival of $อง$ with $sn = 2$ violates the FIFO rule if delivered immediately. The algorithm intervenes by inserting $m_2$ into the pending message queue: $\text{Pending} = \{\langle p_1, m_2, 2 \rangle\}$. When $m_1$ ($sn = 1$) finally arrives, $P_2$ delivers it immediately and updates next[P1] to $2$. At this point, the control loop (while the algorithm is running) detects that there is a message in the Pending queue with $sn' = \text{next}[s]$ (i.e., $m_2$ with $sn = 2$). $P_2$ removes $m_2$ from Pending, delivers it, and updates next [P1] to $3$.

#### Casual order broadcast
Causal order extends the concept of ordering by capturing the cause-effect (happened-before) dependency relationships between messages.
A message $m_1$ has potentially caused a message $m_2$ ($m_1 \rightarrow m_2$) if at least one of the following conditions is true: 
- The same process $p$ emits $m_1$ before $m_2$.
- A process $p$ delivers $m_1$ and subsequently emits $m_2$. 
- There exists an intermediate message $m'$ such that $m_1 \rightarrow m'$ and $m'$ $\rightarrow m_2$ (transitive property).
![[Dependable distributed system 1-1794059108083.webp]]

###### Properties 
- CRB1-CRB4: Same as properties RB1-RB4 in (regular) reliable broadcast;
- CRB5: Causal delivery, for any message $m_1$ that potentially caused a message $m_2$, $m_1 \rightarrow m_2$, no process delivers $m_2$ unless it has already delivered $m_1$. 

>[!Observations] 
>Casual broadcast = reliable broadcast + casual order;
>Casual order $\implies$ FIFO order
>Fifo order $\not\implies$ Casual order
>Casual order = FIFO order + local order.
**Local order**: if a process delivers a message $m_a$ before sending a message $m_b$, then no correct process deliver $m_b$ if it has not already delivered $m_a$.


![[Dependable distributed system 1-1794059567279.webp]]
![[Dependable distributed system 1-1794059581268.webp]]

$$
\begin{aligned}
&\textbf{Algorithm 3.15:} \text{ Waiting Causal Broadcast} \\[0.5em]
&\textbf{Implements:} \quad \text{CausalOrderReliableBroadcast, instance } crb. \\
&\textbf{Uses:} \quad \text{ReliableBroadcast, instance } rb. \\[0.8em]
&\mathbf{upon \ event} \ \langle crb, Init \rangle \ \mathbf{do} \\
&\quad V := [0]^N; \\
&\quad lsn := 0; \\
&\quad pending := \emptyset; \\[0.8em]
&\mathbf{upon \ event} \ \langle crb, Broadcast \mid m \rangle \ \mathbf{do} \\
&\quad W := V; \\
&\quad W[rank(self)] := lsn; \\
&\quad lsn := lsn + 1; \\
&\quad \mathbf{trigger} \ \langle rb, Broadcast \mid [DATA, W, m] \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle rb, Deliver \mid p, [DATA, W, m] \rangle \ \mathbf{do} \\
&\quad pending := pending \cup \{(p, W, m)\}; \\
&\quad \mathbf{while} \ \text{exists } (p', W', m') \in pending \ \text{such that } W' \le V \ \mathbf{do} \\
&\quad\quad pending := pending \setminus \{(p', W', m')\}; \\
&\quad\quad V[rank(p')] := V[rank(p')] + 1; \\
&\quad\quad \mathbf{trigger} \ \langle crb, Deliver \mid p', m' \rangle;
\end{aligned}
$$

![[Dependable distributed system 1-1794060669854.webp]]

##### Safety property
If two messages $m$ and $m'$ are related by a causal relationship $broadcast(m) \rightarrow broadcast(m')$, then each process must deliver $m$ before $m'$. Simply: if $m$ "generated" $m'$ (directly or indirectly), no process can ever deliver $m'$ by skipping or anticipating $m$. The causal order must be strictly preserved in the delivery. 
If $m$ is the $k$-th message sent by process $p_i$, then $m.\text{VC}[i] = k-1$.
<u>How it works:</u> When a process $p_i$ broadcasts a message, it attaches a clock vector $W$ (called $m.\text{VC}$) to it. <u>The meaning:</u> If that message is the $k$-th message emitted by $p_i$, the cell of the vector corresponding to $p_i$ will have the value $k-1$. This value acts as an "order label": it tells the recipient of the message how many previous messages that particular process had already emitted before this one.

>[!Definitions] Casual distance
>Let two broadcast events $b$ and $b'$ with $b \rightarrow b'$. These events have a **casual distance k** if $\exists$ a sequence of $k$ broadcast events $b_1, ..., b_k$ such that:
>	- $\forall I \in {1, ...k} b_i \rightarrow b_{i+1} \land (\not\exists) m* | b_i \rightarrow m* \rightarrow b_{i+1}$ 
>	- $b \rightarrow b_1$
>	- $b_k \rightarrow b'$

###### Safety Property Proof of the Causal Order Broadcast Protocol: Base Case ($K = 0$)

The base case considers two messages $m$ and $m'$ such that $broadcast(m) \rightarrow broadcast(m')$, with **zero causal distance** ($k=0$). This means that $m$ directly causes $m'$ and there is no intermediate message** $m''$ between them ($\text{broadcast}(m) \rightarrow \text{broadcast}(m'') \rightarrow \text{broadcast}(m')$).

Depending on who sends the messages, the proof is divided into two distinct cases:

###### Case 1: Messages are emitted by the _same process_ ($p_i$)

Suppose both $m$ and $m'$ were sent by the same process $p_i$, and that $p_j$ is the receiving process.

1. **Vector Clock Update (_Vector Clock_):** From line 3 of the broadcast procedure of Algorithm 3.15, when a process emits a message, it increments its component in the vector:
$$m'.\text{VC}[i] := m.\text{VC}[i] + 1$$
If $m$ is the $h$-th message sent by $p_i$, then its clock will be $m.\text{VC}[i] = h - 1$, while for the next message it will be $m'.\text{VC}[i] = h$.
2. **Delivery Condition:** A receiving process $p_j$ can deliver message $m'$ only if its local vector $V_j$ satisfies the causal dominance condition:
$$\forall x \in \{1, \dots, n\}, \ m'.\text{VC}[x] \le V_j[x] \quad \text{and in particular} \quad m'.\text{VC}[i] \le V_j[i]$$

3. **The Unlocking Mechanism:** In the vector of $m'$, the $i$-th component is $h$ ($m'.\text{VC}[i] = h$). For the inequality $h \le V_j[i]$ to be true, the recipient's local vector must have $V_j[i] \ge h$. The clock vector property states that $V_j[i] = h$ **if and only if** the recipient has already delivered all the first $h$ messages sent by $p_i$ (including $m$, which was the $h$-th).
4. **Conclusion of Case 1:** Consequently, process $p_j$ **can never deliver $m'$ unless it has first delivered $m$**.
###### Case 2: Messages are issued by _distinct processes_ ($p_i$ and $p_j$)
Suppose that $m$ was issued by $p_i$ and that $m'$ was issued by a different process $p_j$ _after_ $p_j$ delivered $m$. The final recipient is a generic process $p_k[cite: 60]$.
1. **Causal Relationship and Vector Clocks:** Since $broadcast(m) \rightarrow broadcast(m')$ and $m'$ is emitted after the delivery of $m$ (with $m$ being the $h$-th message of $p_i$, i.e. $m.\text{VC}[i] = h-1$), by the clock vector update rule we have that the new message inherits and updates the component.$$m'.\text{VC}[i] = h$$
2. **Condition Verification at Receiver ($p_k$):** Process $p_k$ receives $m'$ and must verify the delivery condition:
$$m'.\text{VC}[i] \le V_k[i] \quad \implies \quad h \le V_k[i]$$
3. **Interpretation of $V_k[i]$:** Within the system, $V_k[i] = h$ if and only if the The $h$-th message emitted by $p_i$ (i.e., our message $m$) was actually delivered by $p_k$.
4. **Conclusion of Case 2:** Since the component in the vector of $m'$ requires that $V_k[i] \ge h$, process $p_k$ is forced to block $m'$ waiting for its local view to reach that value. Therefore, **$p_k$ can deliver $m'$ only after delivering $m$**.

##### Liveness property
Eventually each message will be delivered. Liveness is guaranteed by the following assumptions:
- The number of broadcast events that precedes a certain event is finite;
- Channels are reliable.
$$
\begin{aligned}
&\textbf{Algorithm 3.13:} \text{ No-Waiting Causal Broadcast} \\[0.5em]
&\textbf{Implements:} \quad \text{CausalOrderReliableBroadcast, instance } crb. \\
&\textbf{Uses:} \quad \text{ReliableBroadcast, instance } rb. \\[0.8em]
&\mathbf{upon \ event} \ \langle crb, Init \rangle \ \mathbf{do} \\
&\quad delivered := \emptyset; \\
&\quad past := []; \\[0.8em]
&\mathbf{upon \ event} \ \langle crb, Broadcast \mid m \rangle \ \mathbf{do} \\
&\quad \mathbf{trigger} \ \langle rb, Broadcast \mid [DATA, past, m] \rangle; \\
&\quad append(past, (self, m)); \\[0.8em]
&\mathbf{upon \ event} \ \langle rb, Deliver \mid p, [DATA, mpast, m] \rangle \ \mathbf{do} \\
&\quad \mathbf{if} \ m \notin delivered \ \mathbf{do} \\
&\quad\quad \mathbf{forall} \ (s, n) \in mpast \ \mathbf{do} \\
&\quad\quad\quad \mathbf{if} \ n \notin delivered \ \mathbf{do} \\
&\quad\quad\quad\quad \mathbf{trigger} \ \langle crb, Deliver \mid s, n \rangle; \\
&\quad\quad\quad\quad delivered := delivered \cup \{n\}; \\
&\quad\quad\quad\quad \mathbf{if} \ (s, n) \notin past \ \mathbf{do} \\
&\quad\quad\quad\quad\quad append(past, (s, n)); \\
&\quad\quad \mathbf{trigger} \ \langle crb, Deliver \mid p, m \rangle; \\
&\quad\quad delivered := delivered \cup \{m\}; \\
&\quad\quad \mathbf{if} \ (p, m) \notin past \ \mathbf{do} \\
&\quad\quad\quad append(past, (p, m));
\end{aligned}
$$
![[Dependable distributed system 1-1794062366984.webp]]
1. **Broadcast of the first message ($m_1$)** 
	1. Process $p_1$ emits message $m_1$. According to the No-Waiting algorithm, $m_1$ travels along with the past message list, which currently contains only $\langle p_1, m_1 \rangle$.
	2. Process $p_3$ receives $m_1$, immediately delivers it ($m_1$), and adds $\langle p_1, m_1 \rangle$ to its local past list. 
2. **The causal chain and the second message ($m_2$)**
	1. Subsequently, process $p_3$ emits a new message $m_2$.
	2. Since $p_3$ has previously seen/delivered $m_1$, message $m_2$ is sent along with the past history containing $\langle p_1, m_1 \rangle$ (i.e., the information that $m_1$ causally precedes $m_2$). 
3. **What happens when $m_2$ reaches the other processes?** 
		1. On process $p_1$: 
			1. $p_1$ receives $m_2$. Since $p_1$ was the original author of $m_1$, it already knows $\langle p_1, m_1 \rangle$ in its local list. 
			2. Therefore, $p_1$ does not have to retrieve anything from the past and delivers $m_2$ directly. 
		2. On process $p_2$ (The most interesting case): 
			1. Due to network delays, $m_1$ had not yet arrived directly at $p_2$. Instead, it receives $m_2$ directly (which also carries $\langle p_1, m_1 \rangle$ in its backpack).
			2. The algorithm reads the mpast packet, notices that $m_1$ has not yet been delivered ($n \notin delivered$), and applies the recursive rule: it delivers $m_1$ first, and then immediately delivers $m_2$. 
			3. This way, the causal order is respected without $p_2$ having to block execution waiting for a lost message: $m_2$ also took care of delivering its "ancestor" $m_1$.
		3. The late arrival of $m_1$ on $p_2$: Subsequently, the "slow" and direct copy of $m_1$ still reaches $p_2$. Since $p_2$ has already delivered $m_1$ thanks to $m_2$, the "if m not in delivered" check causes $p_2$ to discard the duplicate.
No-Waiting avoids having messages wait in a blocking queue: if a message arrives "early" bringing with it the history (mpast) of causally previous messages that the recipient does not yet have, the algorithm unpacks the history, delivers the missing messages in the correct order and discards any subsequent duplicates.

###### Casual order is not enough strong to avoid anomalies
![[Dependable distributed system 1-1794063162496.webp]]
Same initial state, different final state at the two sites. To have the same final state we need to ensure that the order of deliveries is the same at each process. Note that ensuring the same delivery order at each replicas does not consider the sending
order of messages

## Total order broadcast

A total order reliable broadcast abstraction order <u>all</u> messages, even those from different senders and those that are note causally related. The total-order broadcast abstraction is sometimes also called **atomic broadcast**.
Total order is orthogonal with respect to FIFO and Causal Order. Total order would accept indeed a computation in which a process $p_i$ sends $n$ messages to a group, and each of the processes of the group delivers such messages in the reverse order of their sending. The computation is totally ordered but it is not FIFO.
![[Dependable distributed system 1-1794063677428.webp]]

###### Properties 
- All processes agree on the same set of delivered messages.
- All processes agree on the same delivery sequence.
- The delivery action behaves like an atomic event: the message is delivered to everyone or no one, and any other messages are strictly ordered before or after it.
###### Relationship between broadcast specifications
![[Dependable distributed system 1-1794063748711.webp]]

In the following image, we can see the Venn diagram representing the **hierarchy and intersection between the different ordering and reliability properties in distributed broadcast systems**. It helps us understand the guarantees offered by the different abstractions and how they overlap with each other through four example executions (taken from Lesson 12 of _Dependable Distributed Systems_).
- **Reliable Broadcast (Larger Outer Circle):** This is the minimum level. It only guarantees that all messages are delivered correctly to everyone or no one, **without any ordering rules**.
- **FIFO Reliable Broadcast (Middle Circle):** This adds the FIFO constraint (messages issued by the _same_ sender must be delivered in the order they were sent).
- **Causal Reliable Broadcast (Smaller Inner Circle):** This is the most restrictive of the sender-based ordered broadcasts. Requires that the causal (cause-effect) order between messages be respected.
The vertical green oval represents the Total Order property (or Atomic Broadcast, in which all processes deliver all messages in the exact same global order).
The diagram positions four different runs of a distributed system to show which category they fall into:
- **Execution 1 (In external Reliable Broadcast, outside Total** Order):
	- ***Meaning***: This is an execution that respects the basic rules of Reliable Broadcast, but does not satisfy either Total Order or the advanced FIFO/Causal ordering properties.

- **Execution 2 (Completely outside or at the margin):**
	- ***Meaning***: This is an execution that violates even the basic properties or fails to fall within the desired standard ordering categories (e.g., messages lost or delivered incorrectly compared to specifications).

- **Execution 3 (At the intersection of Total Order and FIFO, but outside Causal):**
	- **_Meaning**:_ An execution that guarantees **Total Order** (everyone sees messages in the same sequence) and **FIFO Order** (messages from the same sender are in order), but does not respect global causality between different senders.

- **Execution 4 (In the middle, at the narrowest intersection):**
	- _**Meaning**:_ This is the ideal and strongest case. It lies within the _Causal Reliable Broadcast_ **and is covered by Total Order**. It simultaneously satisfies causality, FIFO, and total order (often called _Causal Atomic Broadcast_).
![[Dependable distributed system 1-1794063846265.webp]]
#### System model 
- **Process set**: $\Pi = \{p_1, p_2, \dots, p_n\}$ (static set).
- **Communication channels**: Perfect channels, meaning that messages exchanged between correct processes are delivered reliably without loss or corruption.
- **Time model**: Asynchronous.
- **Fault model**: Crash fault model (processes can crash without sending corrupted messages).
- **Execution representation**: The space of feasible behaviors of the system is characterized by the set of possible executions $R$.

###### Properties
The formal specification of Total Order Broadcast consists of four fundamental pillars, briefly referred to as $\text{TO}(V, I, A, O)$: 
1. **Validity** ($V$): Ensures that messages sent by the correct processes are ultimately delivered. 
2. **Integrity** ($I$): Ensures that there are no spurious, duplicate, or invented messages; every delivered message has been previously issued. 
3. **Agreement** ($A$): Imposes constraints on the set of messages that must be delivered. 
4. **Order** ($O$): Imposes constraints on the sequence in which messages are delivered.

Each of these properties can be either Uniform or Non-Uniform. 
- A **uniform** property imposes restrictions on the behavior of all processes, including those that will crash.
- A **non-uniform** property restricts the behavior of only the correct processes.
![[Dependable distributed system 1-1794064432960.webp]]
The agreement defines whether failed and successful processes must share the exact same set of delivered messages:
- **Uniform Agreement (UA):** $\text{If a process (successful or not) TODelivers a message } m, \text{then all successful processes will eventually deliver } m.$
- **Non-Uniform Agreement (NUA):** $\text{If a successful process TODelivers a message } m, \text{then all successful processes will eventually deliver } m.$
Let $M$ be the set of messages delivered by correct processes, and $M_p$ be the set of messages delivered by a generic process $p$ (possibly a failed one): With UA, the relation $M_p \subseteq M$ holds (correct processes agree perfectly on the set $M$, and failed processes can only deliver a subset of $M$ before crashing). With NUA, it is allowed that $M_p - M \neq \emptyset$ (a failed process may have delivered messages that correct processes will never see).
![[Dependable distributed system 1-1794064694938.webp]]
###### Order properties
Ordering determines how messages are sequenced. The slides compare four main variants: 
1. **Strong Uniform Total Order (SUTO)** If a process delivers message $m$ before $m'$, then no process (correct or not) can deliver $m'$ until it has first delivered $m$. **Characteristics**: All processes see exactly the same message sequence prefix. If a process crashes, it has still followed the exact same sequence as the living ones until it crashed.
2. **Weak Uniform Total Order (WUTO)** If process $p$ and process $q$ both deliver messages $m$ and $m'$, then $p$ delivers $m$ before $m'$ if and only if $q$ delivers $m$ before $m'$. **Characteristics**: There is no restriction on whether a process must deliver all the other processes' messages, but if two processes deliver the same subset of messages, they do so in the same order. After an omission, processes may have disjoint sets of delivered messages, as long as the relative order on the common messages is identical.
3. **Non-Uniform Versions (SNUTO and WNUTO)**: The strong (Strong Non-Uniform Total Order) and weak (Weak Non-Uniform Total Order) versions, respectively: apply the same order consistency rules but only to healthy processes, ignoring the behavior of crashed processes.

![[Dependable distributed system 1-1794222876355.webp]]


###### Total Order Specification Hierarchy

Combining the agreement property  ($UA$ vs $NUA$) and the order properties ($\text{SUTO}$, $\text{WUTO}$, $\text{SNUTO}$, $\text{WNUTO}$), we can obtain 8 combinations, where:
- The strongest one: $TO(UA, SUTO)$
- The weakest one: $TO(NUA, WNUTO)$


##### The 8 combinations

###### 1. $\text{TO(UA, SUTO)}$ :
- **Composition:** Uniform Agreement + Strong Uniform Total Order.
- **What it means:** This is the most rigid and secure specification. All processes (even those that will later crash) agree on exactly the same set of delivered messages (UA) and receive them in the exact same sequential order and with the same prefix (SUTO). No process will ever see a divergence, not even before it dies.
- **Assumption:** Both underlying modules are uniform (Uniform Reliable Broadcast and Uniform Consensus).
	- **Result:** This yields the strongest possible specification: **Uniform Agreement** and **Strong Uniform Total Order**.
- **Why it works (The Proof):**
	1. _Agreement on messages (UA):_ Since Uniform Reliable Broadcast (URB) is used, all processes—including those that eventually crash—deliver the exact same set of messages. Consequently, the local `unordered` buffer will contain the identical set of messages on every node. 
	2. _Strong ordering (SUTO):_ Thanks to Uniform Consensus (UC), every consensus instance ensures that all processes (even faulty ones) decide on the exact same list of messages for each round. Because the messages are ordered using a deterministic rule (`sort`), all nodes process and deliver the messages in the exact same, rigid sequence.
		![[Dependable distributed system 1-1794225248148.webp]]
###### 2. $\text{TO(UA, WUTO)}$
- **Composition:** Uniform Agreement + Weak Uniform Total Order.
- **What this means:** The agreement is uniform (all correct processes ultimately receive the same set of messages $M$), and the ordering is weak but uniform: if two processes deliver the same two messages $m$ and $m'$, they do so in the same order. However, due to the "weak" (weak) nature, if a process crashes, the prefixes of the messages delivered before the branch may not match perfectly.

###### 3. $\text{TO(UA, SNUTO)}$
- **Composition:** Uniform Agreement + Strong Non-Uniform Total Order.
- **What this means:** The message agreement is strong and uniform (all correct processes receive the exact same set of messages), but the strong non-uniform total order (SUTO) rule is only guaranteed for processes that remain _correct_. Faulty processes before the crash may violate the strict prefix constraint.

###### 4. $\text{TO(UA, WNUTO)}$
- **Composition:** Uniform Agreement + Weak Non-Uniform Total Order.
- **What this means:** The set of delivered messages is guaranteed to be uniform among all healthy processes (UA), but weak ordering (agreeing on the relative order of shared messages) is required only among healthy processes (WNUTO).
- **Assumption:** Reliable Broadcast is Uniform (URB), but Consensus is Non-Uniform (NUC).
	- **Result:** Agreement on messages remains uniform ($\text{UA}$), but the ordering property degrades to weak non-uniform ordering ($\text{WNUTO}$).
- **Why it works (The Proof):**
	1. _Agreement on messages (UA):_ Since URB guarantees that _all_ processes (including faulty ones) receive the same set of messages, the buffer of unordered messages is identical for everyone. This preserves Uniform Agreement regarding the messages delivered. 
	2. _Loss of uniformity in ordering (WNUTO):_ Because Consensus is Non-Uniform ($\text{NUC}$), it only guarantees agreement on the decision **among correct processes**. Processes destined to crash (_faulty_ nodes) might decide on a slightly different list or stop midway. Consequently, healthy (correct) nodes agree on the final order, whereas faulty nodes might deliver a different sequence just before failing.
   ![[Dependable distributed system 1-1794225224317.webp]]
###### 5. $\text{TO(NUA, SUTO)}$
- **Composition:** Non-Uniform Agreement + Strong Uniform Total Order.
- **What this means:** The agreement is non-uniform (if a correct process delivers a message, all correct processes do so, but failed processes can have different $M_p$ sets). Conversely, the ordering rule is strong and uniform (SUTO): the order in which messages are passed is strictly constrained for everyone.

###### 6. $\text{TO(NUA, WUTO)}$
- **Composition:** Non-Uniform Agreement + Weak Uniform Total Order.
- **What this means:** The agreement is limited to correct processes only (NUA), and the ordering is weak but uniform (WUTO): processes that share the delivery of $m$ and $m'$ respect the same relative order, but tolerances are accepted for the anomalous behavior of failed nodes.

###### 7. $\text{TO(NUA, SNUTO)}$
- **Composition:** Non-Uniform Agreement + Strong Non-Uniform Total Order.
- **What this means:** Both the Agreement and the Strong Order lose their total uniformity: they serve as a hard guarantee for the _correct_ processes of the network, while nodes that crash may show discrepancies in both messages and order before shutting down.

###### 8. $\text{TO(NUA, WNUTO)}$ — The weakest of all
- **Composition:** Non-Uniform Agreement + Weak Non-Uniform Total Order.
- **What this means:** This is the minimal Total Order specification. All guarantees (both message agreement and relative ordering) are constrained **exclusively** for correct processes. If a process fails, there are no formal restrictions on what messages it saw or in what order it printed them before the crash; the important thing is that the "healthy" part of the distributed system sees things consistently.
- **Assumption:** Both underlying modules are Non-Uniform (NURB and NUC).
	- **Result:** The system defaults to the weaker specification, characterized by **Non-Uniform Agreement** ($\text{NUA}$) and **Weak Non-Uniform Total Order** ($\text{WNUTO}$).
- **Why it works (The Proof):**
	1. _Agreement limited to correct processes (NUA):_ Since the Reliable Broadcast is non-uniform ($\text{NURB}$), only *correct* processes are guaranteed to deliver the same set of messages. Faulty processes may deliver different or incomplete sets of messages. 
	2. _Weak, non-uniform ordering (WNUTO):_ The consensus mechanism is also non-uniform ($\text{NUC}$). Consequently, only the correct processes in the network agree on the same ordered sequence of messages, whereas nodes that fail may exhibit discrepancies—both in the messages they perceive and in the order of those messages—prior to their crash.
	![[Dependable distributed system 1-1794225196731.webp]]





![[Dependable distributed system 1-1794223719991.webp]]

So we can see how the module interact with each other. The total order broadcast is the principal abstractions that we see and use. So a process send a message $m$ ($TOBroadcast(m)$) asking the total order. The reliable broadcast guarantee the the robustness and propagation of messages in the network ($rbBroadcast(m)$). The consensus ensures order agreement. Since messages arrive to processes at different times and in random order, a mechanism is needed to agree on which batch or sequence of messages should be considered "next" in chronological order ($propose(s_n, list), decide(s_n, list)$). Then we have the command for the delivery. 

$$
\begin{aligned}
&\textbf{Algorithm 6.1:} \text{ Consensus-Based Total-Order Broadcast} \\[0.5em]
&\textbf{Implements:} \quad \text{TotalOrderBroadcast, instance } tob. \\
&\textbf{Uses:} \quad \text{ReliableBroadcast, instance } rb; \ \text{Consensus (multiple instances)}. \\[0.8em]
&\mathbf{upon \ event} \ \langle tob, Init \rangle \ \mathbf{do} \\
&\quad unordered := \emptyset; \\
&\quad delivered := \emptyset; \\
&\quad round := 1; \\
&\quad wait := FALSE; \\[0.8em]
&\mathbf{upon \ event} \ \langle tob, Broadcast \mid m \rangle \ \mathbf{do} \\
&\quad \mathbf{trigger} \ \langle rb, Broadcast \mid m \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle rb, Deliver \mid p, m \rangle \ \mathbf{do} \\
&\quad \mathbf{if} \ m \notin delivered \ \mathbf{do} \\
&\quad\quad unordered := unordered \cup \{(p, m)\}; \\[0.8em]
&\mathbf{upon} \ unordered \neq \emptyset \ \land \ \neg wait \ \mathbf{do} \\
&\quad wait := TRUE; \\
&\quad \text{Initialize a new instance } c.round \text{ of consensus}; \\
&\quad \mathbf{trigger} \ \langle c.round, Propose \mid unordered \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle c.r, Decide \mid decided \rangle \ \mathbf{do} \\
&\quad \mathbf{forall} \ (s, m) \in sort(decided) \ \mathbf{do} \\
&\quad\quad \mathbf{trigger} \ \langle tob, Deliver \mid s, m \rangle; \\
&\quad delivered := delivered \cup decided; \\
&\quad unordered := unordered \setminus decided; \\
&\quad round := round + 1; \\
&\quad wait := FALSE;
\end{aligned}
$$
1. **State Initialization** (`Init`)
	Upon startup, each process initializes its local variables:
	- `unordered := ∅`: A set (buffer) that collects messages received from the reliable broadcast layer but not yet ordered and delivered. 
	- `delivered := ∅`: The set of all messages that have already been ordered and definitively delivered to the application. 
	- `round := 1`: A counter that identifies the current instance of the consensus protocol. 
	- `wait := FALSE`: A boolean flag indicating whether the process is currently waiting for a consensus instance to reach a decision. 

2. **Broadcast Request** (`tob, Broadcast`)
	When an application requests to send a message `m` with total ordering:
	- The process simply triggers an underlying reliable broadcast: `trigger (rb, Broadcast | m)`. In this way, the message is robustly propagated to all nodes in the network via the reliable broadcast module. 

3. **Message Reception** (`rb, Deliver`)
	When the reliable broadcast layer delivers a message `m` sent by a process `p`:
	- The algorithm checks whether the message has already been delivered (`m ∉ delivered`). 
	- If it is a new message, it is added to the buffer of messages awaiting ordering: `unordered := unordered ∪ {(p, m)}`. 

4. **Consensus Activation** (`Propose`)
	As soon as the `unordered` buffer is non-empty and the process is not already waiting for a previous decision (`wait = FALSE`):
	- The process sets `wait := TRUE` to block new proposals within the same round. 
	- It initializes a new consensus instance for the current round (`c.round`). - Propose the entire content of the unordered buffer to the consensus system: `trigger (c.round, Propose | unordered)`. 
	- _Note:_ Through this phase, all nodes propose the messages they have accumulated, and the consensus module ensures that all processes agree on a common list of messages to be ordered for this round.

5. **Decision and Delivery** (`Decide`)
	When the consensus instance for round `r` returns a decision (`c.r, Decide | decided`), indicating an agreed-upon set of messages:
	- The decided messages are ordered **deterministically** using a common function (e.g., `sort(decided)`), ensuring that all processes obtain the exact same sequence. 
	- The algorithm iterates through the ordered list and delivers each message to the application: `trigger (tob, Deliver | s, m)`. 
	- Update the local state:
	- Add the processed messages to the set of delivered messages: `delivered := delivered ∪ decided`. 
	- Remove the newly delivered messages from the unordered buffer: `unordered := unordered \ decided`. 
	- Increment the round counter (`round := round + 1`) and reset the flag (`wait := FALSE`) to allow a new consensus round to begin if there are other pending messages.
<mark style="background:#d3f8b6">Example</mark>:
![[Dependable distributed system 1-1794224727348.webp]]
![[Dependable distributed system 1-1794224756006.webp]]![[Dependable distributed system 1-1794224783375.webp]]![[Dependable distributed system 1-1794224799687.webp]]
![[Dependable distributed system 1-1794224817041.webp]]

| **Reliable Broadcast** | **Consensus**         | **TO result**               | **Main reason**                                                                                               |
| ---------------------- | --------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Uniform (URB)**      | **Uniform (UC)**      | **$\text{TO(UA, SUTO)}$**   | Maximum robustness: everyone (even the defective ones) sees the same messages and the same order.             |
| **Uniform (URB)**      | **Non-Uniform (NUC)** | **$\text{TO(UA, WNUTO)}$**  | URB preserves uniform agreement on messages ($\text{UA}$), but NUC renders the ordering weak and non-uniform. |
| **Non-Uniform (NURB)** | **Non-Uniform (NUC)** | **$\text{TO(NUA, WNUTO)}$** | Guarantees apply only to correct processes; faulty processes may diverge on everything                        |

## Software replication
In a distributed system, replication is the process by which multiple copies (called replicas) of the same logical item, piece of data, or service are created and maintained across different computing nodes (sites).  The principal **goal** of the replication is to ensure the high availability of a service (called object) despite nodes failures. 

>[!Definitions] Availability 
>Let $p$ be the probability of failure of a single item $O$. The availability of the single item is given by: $Availability = 1 - p$. 
>If we replicate the item $O$ on $n$ independent nodes assuming that the failures are independent of each other, the probability that every node fail at the same time is $p^n$. The availability of the replicated system is $Availability = 1 - p^n$ 

#### System model

**Set of processes:** Clients and servers connected via **perfect point-to-point channels** (no message loss or corruption).
* **Failure model:** *Crash fault model* (processes may fail by crashing, without sending corrupted or malicious messages).
* **Objects and operations:** Servers manage a set of objects $X$. Each object has its own state and is accessible through a set of operations. 
* An operation performed by a process $p_i$ on an object $x$ consists of an **Invocation/Response** pair. 
* **Invocation:** Denoted as $[x \circ p(\text{arg}) p_i]$, where $\text{arg}$ represents the operation arguments. 
* **Response:** Denoted as $[x \text{ ok}(\text{res}) p_i]$, where $\text{res}$ is the returned result. 
* **Complete pair:** $[x \circ p(\text{arg}) / \text{ok}(\text{res}) p_i]$. 
* **Client blocking:** After issuing an invocation, the client process remains blocked until it receives the corresponding response.

#### Replication requirements

To tolerate process crashes, a logical object must have several physical replicas located at different sites in the network ($x^1, x^2, \dots, x^l$).

* Each replica $x^i$ situated at a site $s$ is managed by a process $p_j$ that is also located at $s$.
* It is assumed that a process $p_j$ crashes precisely when its corresponding replica $x^i$ fails.
* **Transparency:** The complexity and the very existence of replication must be completely hidden from client processes. *Replication is transparent to the client processes.*

#### Consistency criteria

A **consistency criterion** defines the formal contract between the programmer and the replication system regarding the results returned by operations. These criteria fall into two broad categories:
1. **Strong Consistency:**
	* Linearizability
	* Sequential consistency
2. **Weak Consistency:**
	* Causal consistency
The primary difference between the various criteria lies in how they define the "most recent" state visible to the client.

###### Linearizability 
The linearizability is the strongest consistency criterion most rigorous. Requires the introduction of two important relations between operations:
- **Precedence ($<$):** If the response to operation $O_1$ temporally precedes the invocation of $O_2$, then $O_1 < O_2$.
* **Concurrency ($||$):** If the operations overlap in time.

>[!definitiosn] Linearizability
>An execution $E$ is linearizable if there exist a global sequence $S$ (called sequential specification) that includes every operations of $E$ such that:
>	1. **Preservation of precedence order:** If $O_1 < O_2$, then $O_1$ must appear before $O_2$ in the global sequence $S$.
>	2. **Legality of the sequence:** The sequence $S$ must adhere to the object's sequential specification (e.g., the logical behavior of a variable or a FIFO queue).

**Sufficient Conditions for Linearizability**
To ensure linearizability across replicas, the system must satisfy two fundamental properties:
* **Atomicity:** If a replica of an object $x$ executes an invocation, *every* correct replica of $x$ must execute the same invocation.
* **Ordering:** If two replicas both execute two invocations, they must execute them in the **exact same order**.

<mark style="background:#d3f8b6">Example: simple variables </mark>
![[Dependable distributed system 1-1794227050700.webp]]

<mark style="background:#d3f8b6">Example: FIFO queue</mark>
![[Dependable distributed system 1-1794227085608.webp]]
![[Dependable distributed system 1-1794227098604.webp]]

#### Replication techniques
There are two main architectural approaches for implementing linearizability:
1. **Primary-Backup (Passive Replication)**
2. **Active Replication**

###### Primary-backup (passive replication)
In the primary-backup method, the replication's roles are asymmetric:
- **Primary:** This is a single designated replica that receives invocations from clients, processes requests, updates its own state, and coordinates the backups.
* **Backups:** Passive replicas that receive update messages from the primary and replicate its state to ensure fault tolerance in the event of a primary crash.

<mark style="background:#fff88f">Flow in the absence of crashes</mark>
$$
\begin{aligned}
&\mathbf{1.} \text{ The client sends the invocation to the Primary } (prim(x)). \\
&\mathbf{2.} \text{ The Primary processes the operation and sends an } update \text{ message to all Backups.} \\
&\mathbf{3.} \text{ The Backups update their state and send an } ack \text{ message back to the Primary.} \\
&\mathbf{4.} \text{ The Primary waits for all } ack \text{ messages from the correct backups to arrive and then responds to the client.}
\end{aligned}
$$
![[Dependable distributed system 1-1794228076737.webp]]


When the **primary node crashes**, a new leader must be elected from among the backup replicas. Three critical scenarios must be analyzed:

* **Scenario 1: The primary fails *after* sending the response to the client.**
	* *Cases:* Either the client received the response (all is well), or the response was lost in transit, prompting the client to retransmit the request after a timeout. 
	* *Solution:* The new primary identifies the request as already processed (using a unique ID) and sends the response directly, without re-executing the update on the replicas.
		![[Dependable distributed system 1-1794227978791.webp]]


* **Scenario 2: The primary fails *before* sending the update messages.**
	* *Solution:* The client receives no response, times out, and retransmits the request to the new primary, which treats it as an entirely new request.
		![[Dependable distributed system 1-1794228001469.webp]]

* **Scenario 3: The primary fails *after* sending the updates but *before* receiving all acknowledgments (ACKs).**
	* *Problem:* A violation of atomicity (the update has reached only some of the backups). 
	* *Solution:* This requires rigorous consensus mechanisms to ensure that the update is either applied by all replicas or by none at all before the transition to a new primary takes place.
		![[Dependable distributed system 1-1794228036908.webp]]

#### Active replication

In **Active Replication**, there is no central coordinator or primary node: **all replicas have exactly the same role** and execute the same code in parallel.
* **Determinism:** To function correctly, each replica must be *deterministic*. Given the same initial state and the same sequence of inputs, they will always produce the same output.
* **Interaction with Clients:** The client sends its request to *all* replicas simultaneously and will receive responses from each of them.

To maintain atomicity and ordering without a primary, active replication requires the rigorous use of **Total Order Broadcast** (including the initial submission by clients), ensuring that all replicas receive the same requests in the exact same sequence.
* **Crash Handling:** If a replica fails, the system does not require complex state-recovery procedures or leader elections, as the remaining replicas continue to process requests without interruption.

## Distributed registers

In multiprocessor systems with hardware-shared memory, processes communicate by reading from and writing to physical memory registers connected to a common bus. In **message-passing distributed systems**, there is no shared physical memory: processes communicate exclusively by sending messages to one another over the network.

The abstraction of a **distributed register** serves to hide this complexity, offering programmers the illusion of a global shared variable accessible via read and write operations, even though it is implemented entirely on top of a message-passing system.

>[!definitions] Register
>A register is a shared variable accessed by processes through *read* and *write* operations.
>	-`read()`: return the current value $v$ of the register without modify the content of the register;
>	-`write()`: write the value $v$ in the register and return $true$ ($ack$) at the end of the operations. 
>The register stores positive integers and is initialized to $0$. 
>A process cannot invoke a new operation before the previously invoked one has returned a response. 
>$(X,Y)$: Denotes a register to which $X$ processes can write and from which $Y$ processes can read (e.g., $(1,1)$ or $(1,N)$).
>- **(1,1)** denotes a register where only a process can write and only a process can read. It is a priori known which process can write and which can read;
>- **(1,N)** denotes a register where a single process, a priori known, can write, and N processes can read.

#### Operations

Each operation is delimited by two instantaneous, indivisible events: the **Invocation** and the **Return** (or response).
* **Completed operation:** The time interval between the invocation and the return.
* **Failed operation:** An operation invoked by a process $p_i$ that crashes before receiving the return.
* **Temporal precedence ($<$):** Given two operations $o_1$ and $o_2$, we say that $o_1$ precedes $o_2$ ($o_1 < o_2$) if the return time of $o_1$ occurs before the invocation time of $o_2$.
![[Dependable distributed system 1-1794229820988.webp]]
* **Concurrency ($\vert\vert$):** If the time intervals of two operations overlap—either partially or entirely—and no precedence relationship can be defined, the operations are said to be concurrent.

#### Semantic evolution
The semantics depends on the assumptions on the executions:
1. **Serial system**: 
	- _Assumption:_ Pure serial access (a process does not invoke an operation if another process has initiated one that has not yet completed). No crashes. 
	- _Liveness:_ Every operation always terminates. 
	- _Safety:_ Every read operation returns the most recently written value in chronological order.
		![[Dependable distributed system 1-1794230236002.webp|574]]
2. **Concurrency**:
	- *Assumptions*: several processes can access the register, concurrent access, no failures.
		![[Dependable distributed system 1-1794230358374.webp|608]]
3. **Failures:**
	- *Assumptions*: several processes can access the register, serial access, processes can fail by crashing, i.e. after some point in time they stop to run their algorithm forever
		![[Dependable distributed system 1-1794230570175.webp|503]]
4. **Failure and concurrency:** A process can invokes a write operation and then crash before the corresponding response event is generated. The write operation could have taken place or not.
		![[Dependable distributed system 1-1794230723357.webp]]


>[!Important]
>A read may return both: 
>- The value written by the last write operation which completes 
>- The value given as input to the last write operation, even though this operation will fail

>[!definitions] Failed operation
A process fails at some time in between the invocation and the response of the operation


#### Register specifications

There are two main levels of guarantee for registers in a concurrent environment:
- **Regular register $(1, N)$**: Regular registers allow for the retrieval of either the last completed value or one of the values ​​written concurrently.
	- *Termination*. If a correct process invokes an operation, then the operation eventually receives the corresponding confirmation.
	- *Validity*.
		- A read operation that is *not* concurrent with any write returns the last written value. 
		- A read operation that is *concurrent* with one or more writes may return the last written value *or* one of the values ​​written concurrently.
	![[Dependable distributed system 1-1794231774237.webp]]
- **Atomic register $(1,N)$:** Atomic registers, by contrast, impose a rigid, sequential global order, eliminating any ambiguity and ensuring that everyone sees the same timeline.
	- *Termination* (ONRR1). If a correct process invokes an operation, then the operation eventually receives the corresponding confirmation. 
	- *Validity* (ONRR2). A read operation returns the last value written or the value concurrently being written.
	- *Ordering*. If a read operation returns a value $v_1$ and a subsequent read operation (in terms of time) returns $v_2$, then the write operation that introduced $v_2$ cannot have occurred before the write operation that introduced $v_1$. It is not permitted to "go back in time" by observing write operations in an order that is the reverse of the sequence actually experienced by the clients.
	![[Dependable distributed system 1-1794319677956.webp]]
	The process $p_1$ executes a first write, write(5), and a second write, write(6). The first write end before the start of the second write. The process $p_2$ arrives later, when both writes are already completed, and performs a read() operation that returns the value 5 (green bar). If the read had returned 5, while another previous read had returned 6, we would have violated atomicity (because time would go backwards). But here there is only one read that sees 5. Atomicity is respected because the operations can be linearized into a valid serial sequence in which write(5) happens, then write(6) happens, and the read is placed temporally in a way consistent with a view in which the value 5 is still valid or is the intercepted reference point.

![[Dependable distributed system 1-1794232190035.webp]]

###### Regular register: Read-One Write-ALL (ROWA) algorithm FAIL-STOP
What does "Perfect" mean? It has two absolute formal guarantees:
1. *Strong completeness*: if a process die, every healthy process knows that;
2. *Strong accuracy*: if the detector say that a process is died, that process is died. 
This accuracy is the superpower of the algorithm. To make the nodes communicate with each other the algorithm uses <u>perfect point-to-point links</u> and <u>bebBroadcast</u>.
**Idea of the algorithm:**
The philosophy behind this approach is to "share the burden" asymmetrically between reading and writing:
- **Each process has a local copy of the registry:** Instead of a central server, each node has a copy of the registry value on its own disk/memory.
- **Read-One (Reading is free):** When a process wants to read the registry value, **it doesn't send any messages over the network**. It simply reads the value stored in _its_ local copy. This makes reads extremely fast (zero network messages).
- **Write-All (Writing is expensive):** When the writer process wants to update the registry with a new value:
	1. Sends the new value to **all** processes that the detector considers alive (not crashed).
	2. The write is not considered complete until the writer receives a write acknowledgement (**ACK**) from _each_ of the non-crashed processes.
$$
\begin{aligned}
&\textbf{Algorithm 4.1:} \text{ Read-One Write-All} \\
&\textbf{Implements:} \\
&\quad (1, N)\text{-RegularRegister, \textbf{instance } } onrr. \\
&\textbf{Uses:} \\
&\quad \text{BestEffortBroadcast, \textbf{instance } } beb; \\
&\quad \text{PerfectPointToPointLinks, \textbf{instance } } pl; \\
&\quad \text{PerfectFailureDetector, \textbf{instance } } \mathcal{P}. \\
& \\
&\textbf{upon event } \langle onrr, Init \rangle \textbf{ do} \\
&\quad val := \bot; \\
&\quad correct := \Pi; \\
&\quad writeset := \emptyset; \\
& \\
&\textbf{upon event } \langle \mathcal{P}, Crash \mid p \rangle \textbf{ do} \\
&\quad correct := correct \setminus \{p\}; \\
& \\
&\textbf{upon event } \langle onrr, Read \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle onrr, ReadReturn \mid val \rangle; \\
& \\
&\textbf{upon event } \langle onrr, Write \mid v \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle beb, Broadcast \mid [\textsc{Write}, v] \rangle; \\
& \\
&\textbf{upon event } \langle beb, Deliver \mid q, [\textsc{Write}, v] \rangle \textbf{ do} \\
&\quad val := v; \\
&\quad \textbf{trigger } \langle pl, Send \mid q, \text{ACK} \rangle; \\
& \\
&\textbf{upon event } \langle pl, Deliver \mid p, \text{ACK} \rangle \textbf{ do} \\
&\quad writeset := writeset \cup \{p\}; \\
& \\
&\textbf{upon event } \text{correct} \subseteq \text{writeset} \textbf{ do} \\
&\quad writeset := \emptyset; \\
& \\
&\quad \textbf{trigger } \langle onrr, WriteReturn \rangle;
\end{aligned}
$$

The algorithm implements a Regular Register $(1,N)$** (where there is only 1 writer and $N$ readers) based on the Read-One Write-All (ROWA) paradigm, under the assumption of a Perfect Fault Detector ($\mathcal{P}$).

1. **Header and Interfaces (`Implements` and `Uses`)**
	- **`Implements (1, N)-RegularRegister, instance onrr`**: The algorithm provides the abstraction of a regular register identified by the `onrr` (on-regular-register) instance.
	- **`Uses`**: The algorithm relies on three underlying modules:
		1. `beb` (Best-Effort Broadcast): to send messages to all nodes.
		2. `pl` (Perfect Point-to-Point Links): to exchange reliable messages in pairs.
		3. $\mathcal{P}$ (Perfect Failure Detector): A perfect crash detector (with no false positives or false negatives).

2. **Initialization (`Init`)**
	$val := \bot; correct := \Pi; writeset := \emptyset;$
	- **`val`**: Stores the current value of the local register (initialized to $\bot$).
	- **`correct`**: The set of all processes considered healthy (initially set to $\Pi$, the set of all processes on the system).
	- **`writeset`**: The set of processes that have confirmed writing the value during a write operation.

3. **Gestione dei Crash (`upon event`$< \mathcal{P}, Crash \mid p >$)**
	  $correct := correct \setminus \{p\};$
	- Quando il detector perfetto segnala che il processo $p$ è caduto (`Crash`), il processo lo rimuove immediatamente dall'insieme dei nodi considerati corretti (`correct`).
    

4. **Read Operation (`Read`)**
$$
upon event < onrr, Read > do \\
\quad \quad \quad trigger < onrr, ReadReturn \mid val >;
$$
	- **Read-One**: When a read request (`Read`) arrives, the process **does not send any messages over the network**. It simply reads the value stored in its local variable `val` and immediately returns the result (`ReadReturn | val`). Reads are therefore local and have no communication cost.

5. **Write Operation (`Write`)**
$$
upon event < onrr, Write \mid v > do \\
\quad \quad trigger < beb, Broadcast \mid [WRITE, v] >;
$$
	- When the writer wants to write a value $v$, it uses Best-Effort Broadcast (`beb`) to send a `[WRITE, v]` message to all nodes on the network.

6. **Receiving Writes on Nodes (`beb, Deliver`)**

$$
upon event < beb, Deliver \mid q, [WRITE, v] > do \\
val := v;\\ 
trigger < pl, Send \mid q, ACK >; \\
$$
	- When any process receives the write message from node $q$, it immediately updates its local copy (`val := v`) and sends an acknowledgement (`ACK`) directly to the writer using point-to-point links (`pl`).

7. **ACK Collection and Closing the Write (`writeset`)**

$$
upon event < pl, Deliver \mid p, ACK > do \\
writeset := writeset \cup \{p\};\\ 
\\
upon event correct \subseteq writeset do\\
writeset := \emptyset;\\
trigger < onrr, WriteReturn >;
$$
	- **Write-All**: As ACKs arrive from the various $p$ processes, they are added to the `writeset`.
	- The write is completed by unlocking the client (`WriteReturn`) **only when the set of correct nodes (`correct`) is entirely included in the `writeset`** (i.e., the writer has received ACKs from _all_ live nodes). The `writeset` is then cleared for the next write.

![[Dependable distributed system 1-1794321842531.webp]]
![[Dependable distributed system 1-1794321876912.webp]]
![[Dependable distributed system 1-1794321861055.webp]]
![[Dependable distributed system 1-1794321897084.webp]]![[Dependable distributed system 1-1794321912808.webp]]![[Dependable distributed system 1-1794321930765.webp]]
###### Correctness
A distributed algorithm is correct if it satisfies the termination and validity properties.
- **Termination**
	- _Reading (`read`):_ It is trivial because it is executed entirely locally. The process reads from its memory variable without waiting for network messages, then terminates immediately.
	- _Writing (`write`):_ It terminates thanks to the properties of the communication primitives (Best-Effort Broadcast and perfect point-to-point links) and the **Strong Completeness** of the perfect fault detector $\mathcal{P}$. Since every correct process eventually receives the message and sends the ACK, and since real crashes are detected promptly without blocking the writer indefinitely, the write operation always completes.
- **Validity**:** Validity ensures that the value returned by a read is logically correct. Thanks to the **Strong Accuracy** of the $\mathcal{P}$ detector (which produces no false positives), a write completes only after _all_ non-crashed processes have updated their local copies. Consequently, only two possible cases occur:
	1. _Non-concurrent read:_ If the read operation does not overlap in time with the last invoked write, the process will read exactly the last value written.
	2. _Concurrent read:_ If the read occurs concurrently with a write, due to the "no creation" property of communication channels, the value returned will be either the last previously written value or the one currently being written (which is concurrent). This flexibility exactly respects the definition of a _regular_ register.

###### Performance (_Performance_)
Performance is evaluated based on the number of network messages exchanged:
- **Write:** Requests a maximum of $2N$ messages. The writer broadcasts the write message to all ($N$ messages) and receives an ACK from each surviving node ($N$ messages), totaling at most $2N$ messages.
- **Read:** Requests **0 messages** over the network. Since each node holds an up-to-date local copy of the ledger, the operation is resolved entirely locally.

> [!Warning] Problem of the algorithm
> The algorithm does not ensure validity if the failure detector is not perfect. The following scenario could happen: ![[Dependable distributed system 1-1794322193528.webp]]
> $P_1$ invokes write(6) and then falsely suspects $p_2$ . Thus, $p_1$ completes the write operation without waiting for the ack of $p_2$, i.e. without being sure that the value 6 has been written in the local copy of the register at $p_2$. 

###### Fail-silent algorithm: majority voting regular register
While in the Fail-Stop model we had a “superpower” (the perfect fault detector $\mathcal{P}$ that told us who was dead), here we enter the real world of asynchronous systems. <u>Process crashes can never be reliably detected</u>. 

>[!important] The problem
 In a real network, if a node stops responding, you can't know if it's dead forever (crashed) or just very slow or stuck in a network delay. There's no perfect detector. Therefore, we must design algorithms that work without knowing who's alive and who's dead.

To **ensure consistency** despite uncertainty about crashes, the algorithm relies on two assumptions: 
- **Structure**: There are $N$ processes, of which only 1 is a writer and $N$ readers.
- **Majority assumption** (Majority of correct processes): It is assumed that the majority of processes ($> N/2$) will not crash and will remain correct throughout execution. Without this assumption, an asynchronous system would be impossible to reach any agreement (*FLP theorem*).
Communication primitives:
- Perfect point-to-point link;
- Best-effort broadcast.

**Idea of the algorithm:**
-  Each process locally stores a copy of the current value of the register;
-  Each written value is univocally associated to a timestamp;
- The writer and the reader processes use a set of witness processes, to track the last value written;
-  Quorum: the intersection of any two sets of witness processes is not empty;
-  “*Majority Voting*”: each set is constituted by a majority of processes.
$$
\begin{aligned}
&\textbf{Algorithm 4.2:} \text{ Majority Voting Regular Register} \\
&\textbf{Implements:} \\
&\quad (1, N)\text{-RegularRegister, \textbf{instance } } onrr. \\
&\textbf{Uses:} \\
&\quad \text{BestEffortBroadcast, \textbf{instance } } beb; \\
&\quad \text{PerfectPointToPointLinks, \textbf{instance } } pl. \\
& \\
&\textbf{upon event } \langle onrr, Init \rangle \textbf{ do} \\
&\quad (ts, val) := (0, \bot); \\
&\quad wts := 0; \\
&\quad acks := 0; \\
&\quad rid := 0; \\
&\quad readlist := [\bot]^N; \\
& \\
&\mathbf{\text{--- Write() Operation ---}} \\
&\textbf{upon event } \langle onrr, Write \mid v \rangle \textbf{ do} \\
&\quad wts := wts + 1; \\
&\quad acks := 0; \\
&\quad \textbf{trigger } \langle beb, Broadcast \mid [\textsc{Write}, wts, v] \rangle; \\
& \\
&\textbf{upon event } \langle beb, Deliver \mid p, [\textsc{Write}, ts', v'] \rangle \textbf{ do} \\
&\quad \textbf{if } ts' > ts \textbf{ then} \\
&\quad\quad (ts, val) := (ts', v'); \\
&\quad \textbf{trigger } \langle pl, Send \mid p, [\textsc{Ack}, ts'] \rangle; \\
& \\
&\textbf{upon event } \langle pl, Deliver \mid q, [\textsc{Ack}, ts'] \rangle \textbf{ such that } ts' = wts \textbf{ do} \\
&\quad acks := acks + 1; \\
&\quad \textbf{if } acks > N/2 \textbf{ then} \\
&\quad\quad acks := 0; \\
&\quad\quad \textbf{trigger } \langle onrr, WriteReturn \rangle; \\
& \\
&\mathbf{\text{--- Read() Operation ---}} \\
&\textbf{upon event } \langle onrr, Read \rangle \textbf{ do} \\
&\quad rid := rid + 1; \\
&\quad readlist := [\bot]^N; \\
&\quad \textbf{trigger } \langle beb, Broadcast \mid [\textsc{Read}, rid] \rangle; \\
& \\
&\textbf{upon event } \langle beb, Deliver \mid p, [\textsc{Read}, r] \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle pl, Send \mid p, [\textsc{Value}, r, ts, val] \rangle; \\
& \\
&\textbf{upon event } \langle pl, Deliver \mid q, [\textsc{Value}, r, ts', v'] \rangle \textbf{ such that } r = rid \textbf{ do} \\
&\quad readlist[q] := (ts', v'); \\
&\quad \textbf{if } \#(readlist) > N/2 \textbf{ then} \\
&\quad\quad v := \text{highestval}(readlist); \\
&\quad\quad readlist := [\bot]^N; \\
&\quad\quad \textbf{trigger } \langle onrr, ReadReturn \mid v \rangle;
\end{aligned}
$$1. **State Variables (`Init`)**
	Each process initializes its local variables as follows:
	- `(ts, val) := (0, \bot)`: They store the **timestamp** associated with the current value and the **value** of the register itself, respectively. Initially, the timestamp is 0 and the value is null ($\bot$).
	- `wts := 0`: Used only by the writer process to track the last timestamp generated for its writes.
	- `acks := 0`: Counter used by the writer to count how many acknowledgements it receives from nodes.
	- `rid := 0`: Read Identifier, used by the reader to avoid confusing responses from previous reads.
	- `readlist := `[\alt]$^N$: An $N$ large array in which the reader accumulates the `(timestamp, value)` pairs received from the various nodes.
    
 2. **The Write Operation (`Write`) Under the Hood**
	When a `Write(v)` is invoked:
	1. **Timestamp Update:** The writer increments its timestamp counter (`wts := wts + 1`). This ensures that each new write has a timestamp strictly greater than the previous one.
	2. **Broadcast:** Sends a Best-Effort Broadcast message to all nodes: `[WRITE, wts, v]`.
	3. **Receive at Nodes (`beb, Deliver`):** When a node receives the message, it performs a crucial check:
	$$\text{if } ts' > ts \text{ then } (ts, val) := (ts', v')$$
	
	If the incoming timestamp ($ts'$) is higher than the node's current memory timestamp ($ts$), then the node updates itself. _Note:_ Regardless of whether it has updated or not, the node always responds to the writer by sending an [ACK, ts] via a point-to-point link (pl).
	4. **Write Closure:** The writer collects the ACKs. When the condition `acks > N/2` is met (i.e., it has received acknowledgements from a strict majority of nodes), the writer knows that the write has been sufficiently logged in the system, resets the counters, and unlocks the application with `WriteReturn`.
    
2. **The Read Operation (`Read`) Under the Hood**
	When a `Read()` is invoked:
	1. **New Read ID:** The reader increments `rid` and clears the `readlist`.
	2. **General Polling:** Broadcasts `[READ, rid]` to all nodes.
	3. **Node Response:** Each node, upon receiving the read request, responds privately to the reader by sending its current state via a `[VALUE, r, ts, val]` message.
	4. **Collect and Deliver Value (`pl, Deliver Value`):** The reader saves the responses in the `readlist`. As soon as the number of collected responses exceeds the majority (`#(readlist) > N/2`), the reader activates the selection logic:
	$$v := \text{highestval}(readlist)$$
	
	That is, it examines all the timestamps collected among the nodes in the majority, selects the pair with the absolute **highest timestamp**, extracts the associated $v$ value, and returns it to the client (`ReadReturn | v`).
![[Dependable distributed system 1-1794323778839.webp]]
###### Correctness
A distributed algorithm in a Fail-Silent model must guarantee termination and validity without relying on perfect failure detectors:
- **Termination**
	- It is based on the properties of communication channels (Best-Effort Broadcast and reliable point-to-point links) and the **fundamental assumption of a majority of correct processes** ($> N/2$).
	- Since writing and reading require acknowledgement exclusively from a majority (and not from _all_ nodes as in ROWA), the operation cannot be blocked indefinitely due to slow or crashed nodes, as long as there is at least a majority of live nodes responding.
- **Validity**
	- It derives directly from the **quorum intersection property**.
	- Since every majority always intersects with every other majority in at least one node, a read operation that queries a majority will always find a record of the last write that occurred. By choosing the highest timestamp (`highestval`), the returned value is guaranteed to be logically correct according to the semantics of the regular ledger.
###### Performance (_Performance_)
Unlike ROWA (where reading was costless and writing was costly), here the costs are distributed across both operations:
- **Write:** Requests a maximum of $2N$ messages. The writer broadcasts to all nodes ($N$ messages) and receives ACKs from the majority nodes (and at most $N$ responses), totaling a maximum of $2N$ messages.
- **Read:** This also requires a maximum of $2N$ messages. The reader broadcasts the request to all nodes ($N$ messages) and collects the valuable responses from the queried nodes ($N$ messages).


##### Implementation of atomic register (1, $N$)
To build an Atomic Register $(1,N)$ (the maximum level of consistency, or linearizability).
The construction occurs in two distinct phases:
1. **Phase 1**: Starting from a regular register $(1,N)$, we build an atomic register $(1,1)$ (a single writer, a single reader).
2. **Phase 2**: Using a set of atomic registers $(1,1)$, we finally build the complete atomic register $(1,N)$ (a single writer, $N$ readers).
Let's analyze *Phase 1* in detail (from Regular $(1,N)$ to Atomic $(1,1)$):

**The Key Idea**:
- **The Context**: We have a process $p_1$ (the writer) and a process $p_2$ (the reader), and we want to implement an atomic register $(1,1)$ between them.
- **The Underlying Tool:** We exploit an existing regular register $(1,N)$ (denoted by $rr$), where $p_1$ writes and $p_2$ reads.
- **The Regularity Problem:** As we saw before, a _regular_ register has a temporal consistency flaw: if there are concurrent reads, a reader might paradoxically read an "old" value immediately after seeing a "new" one (time appears to run backward).
- **The Solution (Timestamp and Filter):**
	1. Every time $p_1$ writes to the atom, it actually writes a pair consisting of `(value, timestamp)` to the underlying register.
	2. Reader $p_2$, when reading from the regular register, **tracks and stores the last timestamp seen**. If a read returns a value with a timestamp older than the one previously read, the reader "discards" it (or returns the last valid value stored), thus preventing time from running backward and turning regularity into **atomicity**.

$$
\begin{aligned}
&\textbf{Algorithm 4.3:} \text{ From } (1, N)\text{-Regular to } (1, 1)\text{-Atomic Registers} \\
&\textbf{Implements:} \\
&\quad (1, 1)\text{-AtomicRegister, \textbf{instance } } ooar. \\
&\textbf{Uses:} \\
&\quad (1, N)\text{-RegularRegister, \textbf{instance } } onrr. \\
& \\
&\textbf{upon event } \langle ooar, Init \rangle \textbf{ do} \\
&\quad (ts, val) := (0, \bot); \\
&\quad wts := 0; \\
& \\
&\textbf{upon event } \langle ooar, Write \mid v \rangle \textbf{ do} \\
&\quad wts := wts + 1; \\
&\quad \textbf{trigger } \langle onrr, Write \mid (wts, v) \rangle; \\
& \\
&\textbf{upon event } \langle onrr, WriteReturn \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle ooar, WriteReturn \rangle; \\
& \\
&\textbf{upon event } \langle ooar, Read \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle onrr, Read \rangle; \\
& \\
&\textbf{upon event } \langle onrr, ReadReturn \mid (ts', v') \rangle \textbf{ do} \\
&\quad \textbf{if } ts' > ts \textbf{ then} \\
&\quad\quad (ts, val) := (ts', v'); \\
&\quad \textbf{trigger } \langle ooar, ReadReturn \mid val \rangle;
\end{aligned}
$$
The goal of this algorithm is to upgrade consistency: starting from an underlying regular register $(1,N)$ (`onrr`), we construct an atomic register $(1,1)$ (`ooar`) between a single writer and a single reader.

 1. **Header and Interfaces (`Implements` and `Uses`)**
	- **`Implements (1, 1)-AtomicRegister, instance ooar`**: The algorithm provides the abstraction of an atomic register for a single writer and a single reader.
	- **`Uses (1, N)-RegularRegister, instance onrr`**: Uses a regular register $(1,N)$ as the underlying building block.

2. **Initialization** (`Init`)
	$$
	(ts, val) := (0, \bot); wts := 0;
	 $$
	- `ts`: The timestamp associated with the current value (initialized to 0).
	- `val`: The stored value (initialized to null $\bot$).
	- `wts`: The last timestamp generated by the writer for its local writes.

3. **The Write Operation** (`Write`)
   $$
	upon event < ooar, Write \mid v > do \\
	wts := wts + 1; \\
	trigger < onrr, Write \mid (wts, v) >;\\
	\\
	upon event < onrr, WriteReturn > do\\
	trigger < ooar, WriteReturn >;
	$$
	- When a request arrives to write a value $v$:
		1. The writer increments its timestamp counter (`wts := wts + 1`).
		2. It writes to the underlying regular register not only the value, but the entire **`(wts, v)`** pair.
		3. When the lower write is complete (`WriteReturn`), it unlocks the upper client.

4. **The Read Operation and the "Atomicity Filter" (`Read` and `ReadReturn`)**
$$ 
upon event < ooar, Read > do \\
trigger < onrr, Read >; \\
\\
upon event < onrr, ReadReturn \mid (ts', v') > do\\
if ts' > ts then\\
(ts, val) := (ts', v'); \\
trigger < ooar, ReadReturn \mid val >;\\
$$

	- **Request (`Read`):** The reader simply forwards the read request to the underlying regular register.
	- **Response and Check (The Heart of Atomicity):** When the regular register returns a `(ts', v')` pair:
		- The reader performs a fundamental check: **`if ts' > ts`** (whether the timestamp just read is more recent than the one it stored in memory).
		- _If it is newer_, it updates its local state `(ts, val) := (ts', v')`.
		- _If it is not (perhaps because the regular register temporarily returned an old value due to a concurrent read), it discards it and keeps the freshest value it already knew.
		- Finally, it always returns the updated `val` value. This filter prevents time from "going backwards," transforming regularity into **pure atomicity**.

###### Correctness (_Correctness_)
To demonstrate that the algorithm correctly implements an atomic register $(1,1)$, three fundamental properties are verified:
- **Termination (Termination):** Derives directly from the termination property of the underlying regular register (`onrr`). Since both writing and reading to the regular register always terminate, the corresponding operations on the upper atomic register will also terminate without blocking.
- **Validity (Validity):** Guaranteed by the validity of the underlying regular register. Every value written or read is semantically consistent with the operations performed.
- **Ordering (Sorting / Atomicity):** This is the key property that transforms _regularity_ into _atomicity_. Thanks to the mechanism in which the writer associates a progressive timestamp (`wts, v`) and the reader **tracks the last timestamp read** (discarding any values ​​with a lower timestamp), the temporal anomaly typical of regular registers is eliminated. A read will always return a value with a timestamp **greater than or equal to** the previous read, ensuring a globally consistent (linearizable) sorting over time.

###### Performance (_Performance_)
- **Write:** Requests a single write operation to the underlying regular register $(1,N)$.
- **Read:** Requests a single read operation to the underlying regular register $(1,N)$.
- **IMPORTANT NOTE:** From a network message perspective, **there are no additional messages** beyond those already provided by a regular register implementation $(1,1)$. Timestamp-based filtering occurs entirely locally on the client/reader, with no network overhead.

![[Dependable distributed system 1-1794325035328.webp]]![[Dependable distributed system 1-1794325048225.webp]]
$$
\begin{aligned}
&\textbf{Algorithm 4.4:} \text{ From } (1, 1)\text{-Atomic to } (1, N)\text{-Atomic Registers} \\
&\textbf{Implements:} \\
&\quad (1, N)\text{-AtomicRegister, \textbf{instance } } onar. \\
&\textbf{Uses:} \\
&\quad (1, 1)\text{-AtomicRegister } (\text{multiple instances}). \\
& \\
&\textbf{upon event } \langle onar, Init \rangle \text{ do} \\
&\quad ts := 0; \\
&\quad acks := 0; \\
&\quad writing := \text{FALSE}; \\
&\quad readval := \bot; \\
&\quad readlist := [\bot]^N; \\
&\quad \textbf{forall } q \in \Pi, r \in \Pi \text{ do} \\
&\quad\quad \text{Initialize a new instance } \text{ooar}_{q,r} \text{ of } (1, 1)\text{-AtomicRegister} \\
&\quad\quad \text{with writer } r \text{ and reader } q; \\
& \\
&\textbf{upon event } \langle onar, Write \mid v \rangle \text{ do} \\
&\quad ts := ts + 1; \\
&\quad writing := \text{TRUE}; \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle \text{ooar}_{q,\text{self}}, Write \mid (ts, v) \rangle; \\
& \\
&\textbf{upon event } \langle \text{ooar}_{q,\text{self}}, WriteReturn \rangle \text{ do} \\
&\quad acks := acks + 1; \\
&\quad \textbf{if } acks = N \text{ then} \\
&\quad\quad acks := 0; \\
&\quad\quad \textbf{if } writing = \text{TRUE} \text{ then} \\
&\quad\quad\quad \text{trigger } \langle onar, WriteReturn \rangle; \\
&\quad\quad\quad writing := \text{FALSE}; \\
&\quad\quad \textbf{else} \\
&\quad\quad\quad \text{trigger } \langle onar, ReadReturn \mid readval \rangle; \\
& \\
&\textbf{upon event } \langle onar, Read \rangle \text{ do} \\
&\quad \textbf{forall } r \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle \text{ooar}_{\text{self},r}, Read \rangle; \\
& \\
&\textbf{upon event } \langle \text{ooar}_{\text{self},r}, ReadReturn \mid (ts', v') \rangle \text{ do} \\
&\quad \text{readlist}[r] := (ts', v'); \\
&\quad \textbf{if } \#(\text{readlist}) = N \text{ then} \\
&\quad\quad (\text{maxts}, readval) := \text{highest}(\text{readlist}); \\
&\quad\quad \text{readlist} := [\bot]^N; \\
&\quad\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad\quad \text{trigger } \langle \text{ooar}_{q,\text{self}}, Write \mid (\text{maxts}, readval) \rangle;
\end{aligned}
$$
![[Dependable distributed system 1-1794325135384.webp]]
![[Dependable distributed system 1-1794325150135.webp]]
![[Dependable distributed system 1-1794325167962.webp]]

###### Correctness (_Correctness_)
- **Termination (Termination):** Derives directly from the termination property of the underlying $(1,1)$ atomic registers. Since every write or read operation on the individual $(1,1)$ registers always terminates, the global $(1,N)$ algorithm will also complete its operations without blocking.
- **Validity (Validity):** Guaranteed by the validity of the underlying $(1,1)$ atomic registers. The values ​​written and read maintain correct semantic consistency.
- **Ordering (Ordering / Atomicity):**
	- This is the crucial property for ensuring linearizability.
	- **How ​​it works:** Suppose we have a write $w_1$​ (writing a value $v_1$​ with timestamp $s_1$​) and a previous write $w_2$​ (with value $v_2$​ and timestamp $s_2$​, where $s_1 < s_2$).
	- If a read returns $v_2$​, it means that for each process, the last value written to the $(1,1)$ registers was based on $s_2$​.
	- Due to the ordering property of the underlying $(1,1)$ atomic registers, each subsequent read will return a value with a timestamp **greater than or equal to $s_2$​**. Consequently, the previous and lowest timestamp $s_1​$ can no longer be returned, ensuring strict global time ordering.
###### Performance (_Performance_)
- **Write):** Each write operation to an atomic register $(1,N)$ requires performing **N write operations** in parallel to the underlying atomic registers $(1,1)$ (one for each reader/process).
- **Read:**
	- Each read operation to an atomic register $(1,N)$ is more expensive and requires two steps:
		1. Read from **NNN different atomic registers $(1,1)$** to collect all versions.
		2. Perform the "repair/propagation" phase by writing the freshest value to **N atomic registers $(1,1)$** (the write-back phase seen in the algorithm).


###### RIWA: Read-Impose Write-All algorithm for atomic register 
As we've seen, in a regular ledger, if there's a concurrent read and write, the reader can receive a "new" value. However, if a subsequent reader reads from a process that hasn't yet been updated, it risks reading an older value, creating a temporal anomaly (time "rolls back"), which violates atomicity (linearizability). To address this issue, the golden rule applies: "The read operation writes."
**How the key idea works:**
1. **The Read phase**: When a reader queries nodes, it finds the value with the most recent timestamp.
2. **The Write-All phase:** Before actually returning the value to the client (ReadReturn), the reader forces all valid processes to update their local copy with that value (unless they already have a version with an even more recent timestamp).
3. **The result**: This "forced write" phase (impose) ensures that the most recent value propagates instantaneously across the network. No subsequent reader will ever again encounter an obsolete version.

$$
\begin{aligned}
&\textbf{Algorithm 4.5:} \text{ Read-Impose Write-All Atomic Register} \\
&\textbf{Implements:} \\
&\quad (1, N)\text{-AtomicRegister, \textbf{instance } } onar. \\
&\textbf{Uses:} \\
&\quad \text{BestEffortBroadcast, \textbf{instance } } beb; \\
&\quad \text{PerfectPointToPointLinks, \textbf{instance } } pl; \\
&\quad \text{PerfectFailureDetector, \textbf{instance } } \mathcal{P}. \\
& \\
&\textbf{upon event } \langle onar, Init \rangle \textbf{ do} \\
&\quad (ts, val) := (0, \bot); \\
&\quad correct := \Pi; \\
&\quad writeset := \emptyset; \\
&\quad readval := \bot; \\
&\quad reading := \text{FALSE}; \\
& \\
&\textbf{upon event } \langle \mathcal{P}, Crash \mid p \rangle \textbf{ do} \\
&\quad correct := correct \setminus \{p\}; \\
& \\
&\textbf{upon event } \langle onar, Read \rangle \textbf{ do} \\
&\quad reading := \text{TRUE}; \\
&\quad readval := val; \\
&\quad \textbf{trigger } \langle beb, Broadcast \mid [\textsc{Write}, ts, val] \rangle; \\
& \\
&\textbf{upon event } \langle onar, Write \mid v \rangle \textbf{ do} \\
&\quad \textbf{trigger } \langle beb, Broadcast \mid [\textsc{Write}, ts + 1, v] \rangle; \\
& \\
&\textbf{upon event } \langle beb, Deliver \mid p, [\textsc{Write}, ts', v'] \rangle \textbf{ do} \\
&\quad \textbf{if } ts' > ts \textbf{ then} \\
&\quad\quad (ts, val) := (ts', v'); \\
&\quad \textbf{trigger } \langle pl, Send \mid p, [\textsc{Ack}] \rangle; \\
& \\
&\textbf{upon event } \langle pl, Deliver \mid p, [\textsc{Ack}] \rangle \textbf{ do} \\
&\quad writeset := writeset \cup \{p\}; \\
& \\
&\textbf{upon event } correct \subseteq writeset \textbf{ do} \\
&\quad writeset := \emptyset; \\
&\quad \textbf{if } reading = \text{TRUE} \textbf{ then} \\
&\quad\quad reading := \text{FALSE}; \\
&\quad\quad \textbf{trigger } \langle onar, ReadReturn \mid readval \rangle; \\
&\quad \textbf{else} \\
&\quad\quad \textbf{trigger } \langle onar, WriteReturn \rangle;
\end{aligned}
$$

1. **Initialization and Variables**
	- `(ts, val) := (0, \bot)`: Stores the timestamp and current value.
	- `reading := \text{FALSE}`: A Boolean flag used to determine whether the process is performing a read (`TRUE`) or write (`FALSE`) operation.
	- `readval`: Temporarily stores the value the reader intends to return.

2. **The Read Operation (`onar, Read`)**
	When a read request arrives
	```
	reading := \text{TRUE};
	readval := val;
	trigger \langle beb, Broadcast \mid [\textsc{Write}, ts, val] \rangle;
	```
	- The reader sets the `reading` flag and saves the current value locally (`readval := val`).
	- Immediately afterward, it broadcasts the timestamp and its current value to all nodes (`[\textsc{Write}, ts, val]`). This is the _enforcement_ phase: it forces the network to align on its value.

3. **The Write Operation (`onar, Write \mid v`)**
	When a request arrives to write a new value $v$:
	```
	trigger \langle beb, Broadcast \mid [\textsc{Write}, ts + 1, v] \rangle;
	```
	- The writer increments the timestamp by 1 (`ts + 1`) to make it more recent than any other, and broadcasts the new value to all nodes.

4. **Receiving at Nodes (`beb, Deliver`)**
	When any process receives a write/enforce message `[\textsc{Write}, ts', v']`:
	```
	if ts' > ts then
	(ts, val) := (ts', v');
	trigger \langle pl, Send \mid p, [\textsc{Ack}] \rangle;
	```
	
	- **Freshness check:** If the incoming timestamp ($ts'$) is higher than the node's memory timestamp ($ts$), the node **updates its local copy**.
	
	- **Acknowledgement:** In any case, the node responds by sending an ACK to the writer/reader via point-to-point links (`pl`).

5. **Terminate the operation (`correct \subseteq writeset`)**	
	```
	if reading = \text{TRUE} then
	reading := \text{FALSE};
	trigger \langle onar, ReadReturn \mid readval \rangle;
	else
	trigger \langle onar, WriteReturn \rangle;
	```
	- When the writer (or the broadcasting reader) receives ACKs from **all valid processes** (thanks to the perfect detector $\mathcal{P}$):
	- If the current operation was a **read** (`reading = TRUE`), it resets the flag and returns the value to the client (`ReadReturn | readval`).
	- If it was a **write**, it unblocks the application with `WriteReturn`.

![[Dependable distributed system 1-1794326088156.webp]]
###### Correctness (_Correctness_)
- **Termination (Termination):** This derives from exactly the same properties as the regular ROWA register. Thanks to the perfect fault detector ($\mathcal{P}$) and communication primitives, both writing and reading always terminate, as they don't block indefinitely (every correct process responds with an ACK).
- **Validity (Validity):** Here too, the same principle applies as for the regular ROWA register: the values ​​read or written are semantically consistent and valid within the system.
- **Ordering (Atomicity):**
	- This is the property that distinguishes atomicity from regularity. To complete a read operation, the reader process **must ensure** (by receiving ACKs from all correct processes) that every other node has updated its local copy with the value (and timestamp) just read.
	- _Why is it crucial?_ This way, any subsequent read will query nodes that already have an updated (or even more recent) version, categorically preventing a future reader from encountering an old value. Time cannot "go back," thus ensuring linearizability (atomicity).
###### Performance (_Performance_)
Unlike the regular version (where reading was costless), in the atomic RIWA implementation, the cost is also shifted to reading, due to the need to enforce the value:
- **Write:** Requires a maximum of $2N$ messages.** The writer broadcasts the value to all nodes ($N$ messages) and receives ACKs from all valid processes ($N$ messages).
- **Read:** Also requires a maximum of $2N$ messages. Because of the _Read-Impose_ mechanism, the read also needs to broadcast to all nodes to force the timestamp update ($N$ messages) and collect all confirmation ACKs ($N$ messages).

##### Read-Impose Write-Majority Atomic register fail-silent
In the _Fail-Stop_ model (first seen with RIWA), we could force writes on _all_ live nodes (`correct`), because we had a perfect detector that told us exactly which ones were alive. In the real world **Fail-Silent**, we can't know for sure whether a node is dead or just slow. Consequently:
- We can't demand responses from all nodes.
- We rely on the **majority** ($> N/2$).

<u>How the algorithm works:</u>
1. **Write_:** When the writer wants to update the ledger, it sends the new value (with an incremented timestamp) and **waits for ACKs from a majority of processes**. As soon as the majority confirms, the write is considered valid.
2. **Read-Impose_:** When a reader wants to read, it first queries a majority to find the value with the highest timestamp. Then, similar to the principle seen above, it forces that value onto a majority of processes, forcing them to update their local copies before returning the result to the client.


**Why does it guarantee Atomicity (Linearizability)?**

The combination of these two rules always leverages the mathematical magic of quorum intersection:
- Any read forces its value onto a majority $M_1$.
- Any subsequent read or write will query or update another majority $M_2$.
- Since $M_1$ and $M_2$ always intersect at at least one node, the freshest value is never lost, and no future reader will ever see an outdated version.

$$
\begin{aligned}
&\textbf{Algorithm 4.6:} \text{ Read-Impose Write-Majority Atomic Register (Read)} \\
&\textbf{Implements:} \\
&\quad (1, N)\text{-AtomicRegister, \textbf{instance } } onar. \\
&\textbf{Uses:} \\
&\quad \text{BestEffortBroadcast, \textbf{instance } } beb; \\
&\quad \text{PerfectPointToPointLinks, \textbf{instance } } pl. \\
& \\
&\textbf{upon event } \langle onar, Init \rangle \text{ do} \\
&\quad (ts, val) := (0, \bot); \\
&\quad wts := 0; \\
&\quad acks := 0; \\
&\quad rid := 0; \\
&\quad readlist := [\bot]^N; \\
&\quad readval := \bot; \\
&\quad reading := \text{FALSE}; \\
& \\
&\textbf{upon event } \langle onar, Read \rangle \text{ do} \\
&\quad rid := rid + 1; \\
&\quad acks := 0; \\
&\quad readlist := [\bot]^N; \\
&\quad reading := \text{TRUE}; \\
&\quad \textbf{trigger } \langle beb, Broadcast \mid [\text{READ}, rid] \rangle; \\
& \\
&\textbf{upon event } \langle beb, Deliver \mid p, [\text{READ}, r] \rangle \text{ do} \\
&\quad \textbf{trigger } \langle pl, Send \mid p, [\text{VALUE}, r, ts, val] \rangle; \\
& \\
&\textbf{upon event } \langle pl, Deliver \mid q, [\text{VALUE}, r, ts', v'] \rangle \text{ such that } r = rid \text{ do} \\
&\quad \text{readlist}[q] := (ts', v'); \\
&\quad \textbf{if } \#(\text{readlist}) > N/2 \text{ then} \\
&\quad\quad (\text{maxts}, readval) := \text{highest}(\text{readlist}); \\
&\quad\quad \text{readlist} := [\bot]^N; \\
&\quad\quad \textbf{trigger } \langle beb, Broadcast \mid [\text{WRITE}, rid, \text{maxts}, readval] \rangle;
&\textbf{upon event } \langle onar, Write \mid v \rangle \text{ do} \\ &\quad rid := rid + 1; \\ &\quad wts := wts + 1; \\ &\quad acks := 0; \\ &\quad \textbf{trigger } \langle beb, Broadcast \mid [\text{WRITE}, rid, wts, v] \rangle; \\ & \\ &\textbf{upon event } \langle beb, Deliver \mid p, [\text{WRITE}, r, ts', v'] \rangle \text{ do} \\ &\quad \textbf{if } ts' > ts \text{ then} \\ &\quad\quad (ts, val) := (ts', v'); \\ &\quad \textbf{trigger } \langle pl, Send \mid p, [\text{ACK}, r] \rangle; \\ & \\ &\textbf{upon event } \langle pl, Deliver \mid q, [\text{ACK}, r] \rangle \text{ such that } r = rid \text{ do} \\ &\quad acks := acks + 1; \\ &\quad \textbf{if } acks > N/2 \text{ then} \\ &\quad\quad acks := 0; \\ &\quad\quad \textbf{if } reading = \text{TRUE} \text{ then} \\ &\quad\quad\quad reading := \text{FALSE}; \\ &\quad\quad\quad \textbf{trigger } \langle onar, ReadReturn \mid readval \rangle; \\ &\quad\quad \textbf{else} \\ &\quad\quad\quad \textbf{trigger } \langle onar, WriteReturn \rangle; \end{aligned}$$
This algorithm combines two key concepts:
1. **Write-Majority**: Since we can't trust all nodes and don't know which ones are dead or slow, we only need to collect responses from a strict majority of nodes ($> N/2$) to consider an operation valid.
2. **Read-Impose**: When a reader reads, it doesn't just passively observe, but forces a majority of nodes to update with the freshest value found. This prevents future readers from encountering stale data, ensuring atomicity (linearizability).
 
**1. When a process wants to read from the register:**
- **Phase 1 (Polling):** The reader increments the read identifier (`rid := rid + 1`) to avoid confusion with old or concurrent reads, clears the list, and broadcasts the request (`[READ, rid]`) to all nodes.
- **Phase 2 (Node Response):** Each node receives the request and responds privately with its current state via a `[VALUE, r, ts, val]` message.
- **Phase 3 (Collection, Selection, and Enforcement):** The reader collects the responses in the `readlist`. As soon as the number of responses exceeds the majority ($\#(\text{readlist}) > N/2$):
	- Selects the value with the highest timestamp using `highest(readlist)` (`maxts, readval`).
	- **The "Read-Impose":** Immediately executes a new `[WRITE, rid, maxts, readval]` broadcast to force nodes to update with this value.

**2. The Write Operation (`onar, Write \mid v`) and ACK Management**
When the writer (or the reader in the write-back phase) sends a value:
- Increments `rid` and the write timestamp `wts := wts + 1`, sending a broadcast `[WRITE, rid, wts, v]`.
- **Checking Nodes:** When a node receives the write message, it checks whether the incoming timestamp is more recent than its own (`ts' > ts`). If so, it updates its local memory `(ts, val) := (ts', v')` and returns an acknowledgment `[ACK, rid]`.
- **Termination of the Operation (`acks > N/2`):** The process waits to receive ACKs from a **majority** of nodes. As soon as the threshold is exceeded:
	- If the current operation was a read (`reading = TRUE`), return the value to the client with `ReadReturn | readval`.
	- If it was a write, unblock the application with `WriteReturn`.

###### Correctness (_Correctness_)
- **Termination (Termination):** Derives directly from the same properties as the regular _Majority Voting_ register. Since the algorithm relies on the assumption that there is a **majority of correct processes** and uses reliable communication primitives, read and write operations do not block indefinitely, as they simply wait for acknowledgements from the majority ($> N/2$) to complete them.
- **Validity (Validity):** Also inherited from the regular _Majority Voting_ register: every value returned or written is semantically valid and belongs to the set of values ​​entered into the system by the writer.
- **Ordering (Ordering / Atomicity):**
- This is the key property that elevates the algorithm to _atomic_. It results from the combination of two factors:
	1. The **Read-Impose** mechanism, which forces the reader to propagate and impose the newly read value on a majority of processes before returning it to the client.
	2. The **quorum intersection property (majority)**, whereby any future operation will query a majority that shares at least one node with the previous one, categorically preventing a reader from viewing an outdated version (time never goes back).

###### Performance (_Performance_)
In the majority _Fail-Silent_ model, ensuring atomicity requires a higher message cost, especially for the read operation:
- **Write:** Requires **maximum $2N$ messages**. The writer performs a Best-Effort Broadcast to all nodes ($N$ messages) and collects ACKs from the majority (at most $N$ response messages), for a total of $2N$.
- **Read:** Requires **up to $4N$ messages**. The cost is higher because reading consists of _two distinct phases_, each based on a majority vote:
	1. **Polling Phase (Read):** Broadcast the read request ($N$ messages) + receive values ​​from nodes ($N$ messages).
	2. **Impose Phase (Write/Repair):** Broadcast the freshest value found to update the network ($N$ messages) + receive confirmation ACKs ($N$ messages).
	- Grand total: $N + N + N + N = 4N$ messages.

## Byzantine processes 
Unlike **Crash-Stop** or **Crash-Failure** models, in the **Byzantine Model** faulty processes (called _Byzantine processes_ or arbitrary processes) can behave in a completely hostile, unpredictable, or malicious manner.

**What can Byzantine processes do?**
1. **Arbitrarily deviate** from the algorithm's instructions:
- Create forged messages.
- Lose or discard messages.
- Deliberately delay deliveries.
- Alter the content of messages in transit.
2. **Actively act to sabotage** the system, preventing the achievement of the algorithm's objectives (e.g., preventing consensus or coherence).

As in the crash failure model, we distinguish between faulty and correct processes.

>[!Warning]
>A Byzantine process may act arbitrarily, and no mechanism can guarantee anything related to its actions.

The first fundamental tool for defending against Byzantine behavior is the use of cryptography to implement Authenticated Perfect Links. But cryptography alone is not enough. If a sender is arbitrarily flawed (Byzantine), requiring it to digitally sign every broadcast message does not solve the problem. A corrupted sender can simply sign and send two different messages to two different recipients, creating network inconsistency.

#### Authenticated Perfect Link

Before defining broadcast protocols, we must ensure that messages cannot be spoofed or altered in transit between two legitimate nodes.

**AuthPerfectPointToPointLinks (`al`)**
- <u>Requested Events</u>: `(al, Send | q, m)` -> Requests sending message $m$ to process $q$.
- <u>Indication Events</u>:` (al, Deliver | p, m)` -> Delivers message $m$ sent by process $p$.
###### Properties:
- **AL1 (Reliable delivery):** If a correct process sends message $m$ to a correct process $q$, then $q$ ultimately delivers $m$.
- **AL2 (No duplication):** No message is delivered by a correct process more than once.
- **AL3 (Authenticity):** If a correct process $q$ delivers message $m$ with sender $p$ (and $p$ is correct), then $m$ was previously sent to $q$ by $p$.

#### Byzantine Consistent Broadcast (BCB)

**Consistent Broadcast** in the presence of Byzantine failures guarantees that all nodes agree on the same message, but _without_ the strong guarantee of universal termination if the sender fails.

###### ByzantineConsistentBroadcast (`bcb`)
- **Instance Name:** `bcb`, with sender $s$.
- **Events:**
	- Request: `(bcb, Broadcast | m)` -> Executed **only** by the sender process $s$.
	- Indication: `(bcb, Deliver | p, m)` -> Delivers message $m$ issued by $p$.
###### Properties:
- **BCB1 (Validity):** If a correct process $p$ (acting as the sender) broadcasts a message $m$, then every correct process eventually delivers $m$.
- **BCB2 (No duplication):** Each correct process delivers at most one message.
- **BCB3 (Integrity):** If a correct process delivers a message $m$ with sender $p$ (correct), then $m$ was previously broadcast by $p$.
- **BCB4 (Consistency):** If a correct process delivers a message $m$ and another correct process delivers a message $m'$, then $m = m'$. _(Note: This refers to a single broadcast event.)_

$$
\begin{aligned}
&\textbf{Algorithm 3.16:} \text{ Authenticated Echo Broadcast} \\
&\textbf{Implements:} \\
&\quad \text{ByzantineConsistentBroadcast, \textbf{instance } } bcb, \text{ with sender } s. \\
&\textbf{Uses:} \\
&\quad \text{AuthPerfectPointToPointLinks, \textbf{instance } } al. \\
& \\
&\textbf{upon event } \langle bcb, \text{Init} \rangle \text{ do} \\
&\quad sentecho := \text{FALSE}; \\
&\quad delivered := \text{FALSE}; \\
&\quad echos := [\bot]^N; \\
& \\
&\textbf{upon event } \langle bcb, \text{Broadcast} \mid m \rangle \text{ do} && \text{// only process } s \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle al, \text{Send} \mid q, [\textsc{Send}, m] \rangle; \\
& \\
&\textbf{upon event } \langle al, \text{Deliver} \mid p, [\textsc{Send}, m] \rangle \text{ such that } p = s \text{ and } sentecho = \text{FALSE} \text{ do} \\
&\quad sentecho := \text{TRUE}; \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle al, \text{Send} \mid q, [\textsc{Echo}, m] \rangle; \\
& \\
&\textbf{upon event } \langle al, \text{Deliver} \mid p, [\textsc{Echo}, m] \rangle \text{ do} \\
&\quad \textbf{if } echos[p] = \bot \text{ then} \\
&\quad\quad echos[p] := m; \\
& \\
&\textbf{upon exists } m \neq \bot \text{ such that } \#(\{p \in \Pi \mid echos[p] = m\}) > \frac{N + f}{2} \\
&\quad \text{and } delivered = \text{FALSE} \text{ do} \\
&\quad delivered := \text{TRUE}; \\
&\quad \text{trigger } \langle bcb, \text{Deliver} \mid s, m \rangle;
\end{aligned}
$$
- **Initialization (`Init`)**: Each process sets the Boolean flags `sentecho` and `delivered` to false and initializes an $N$-sized `echos` array filled with empty elements ($\bot$).
- **Broadcast (`Broadcast | m`)**: Executed only by the sender $s$, it sends a `[SEND, m]` message to all processes on the network via authenticated links (`al`).
- **Receiving SEND (`al, Deliver | p, [SEND, m]`)**: When a process receives the message from the correct sender $s$ (and has not yet responded with an echo), it sets `sentecho = TRUE` and sends an acknowledgement message `[Echo, m]` to all nodes.
- **Echo Phase and Delivery Condition**: As processes receive `[ECHO, m]` messages, they record them in the `echos` vector. A process performs delivery (`bcb, Deliver`) as soon as it detects that there is a message $m$ (other than $\bot$) whose number of received echoes exceeds the safety threshold $\frac{N+f}{2}$, assuming that the system tolerates up to $f$ Byzantine faults with the fundamental condition $N > 3f$.
![[Dependable distributed system 1-1794329369872.webp]]
![[Dependable distributed system 1-1794390442015.webp]]
![[Dependable distributed system 1-1794390461604.webp]]
![[Dependable distributed system 1-1794390481211.webp]]

#### Byzantine Reliable Broadcast (BRB)
**Reliable Broadcast** adds a fundamental property over Consistent Broadcast: **Totality**, meaning that if any correct node delivers the message, then all correct nodes will eventually deliver it.

- **BRB1-BRB4 Properties:** Identical to the BCB1-BCB4 properties of Consistent Broadcast.
- **BRB5 (Totality):** If a message is delivered by any correct process, then every correct process eventually delivers a message.
To achieve Reliable Broadcast in the presence of Byzantine processes, a two-phase protocol (Echo + Ready), known as **Double-Echo Broadcast**, is used.

$$
\begin{aligned}
&\textbf{Algorithm 3.18:} \text{ Authenticated Double-Echo Broadcast} \\
&\textbf{Implements:} \\
&\quad \text{ByzantineReliableBroadcast, instance } brb \text{ with sender } s. \\
&\textbf{Uses:} \\
&\quad \text{AuthPerfectPointToPointLinks, instance } al. \\
& \\
&\textbf{upon event } \langle brb, Init \rangle \text{ do} \\
&\quad sentecho := \text{FALSE}; \\
&\quad sentready := \text{FALSE}; \\
&\quad delivered := \text{FALSE}; \\
&\quad echos := [\bot]^N; \\
&\quad readys := [\bot]^N; \\
& \\
&\textbf{upon event } \langle brb, Broadcast \mid m \rangle \text{ do} \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle al, Send \mid q, [\text{SEND}, m] \rangle; \\
& \\
&\textbf{upon event } \langle al, Deliver \mid p, [\text{SEND}, m] \rangle \text{ such that } p = s \text{ and } sentecho = \text{FALSE} \text{ do} \\
&\quad sentecho := \text{TRUE}; \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle al, Send \mid q, [\text{ECHO}, m] \rangle; \\
& \\
&\textbf{upon event } \langle al, Deliver \mid p, [\text{ECHO}, m] \rangle \text{ do} \\
&\quad \textbf{if } echos[p] = \bot \text{ then} \\
&\quad\quad echos[p] := m; \\
& \\
&\textbf{upon exists } m \neq \bot \text{ such that } \#(\{p \in \Pi \mid echos[p] = m\}) > \frac{N + f}{2} \text{ and } sentready = \text{FALSE} \text{ do} \\
&\quad sentready := \text{TRUE}; \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle al, Send \mid q, [\text{READY}, m] \rangle; \\
& \\
&\textbf{upon event } \langle al, Deliver \mid p, [\text{READY}, m] \rangle \text{ do} \\
&\quad \textbf{if } readys[p] = \bot \text{ then} \\
&\quad\quad readys[p] := m; \\
& \\
&\textbf{upon exists } m \neq \bot \text{ such that } \#(\{p \in \Pi \mid readys[p] = m\}) > f \text{ and } sentready = \text{FALSE} \text{ do} \\
&\quad sentready := \text{TRUE}; \\
&\quad \textbf{forall } q \in \Pi \text{ do} \\
&\quad\quad \text{trigger } \langle al, Send \mid q, [\text{READY}, m] \rangle; \\
& \\
&\textbf{upon exists } m \neq \bot \text{ such that } \#(\{p \in \Pi \mid readys[p] = m\}) > 2f \text{ and } delivered = \text{FALSE} \text{ do} \\
&\quad delivered := \text{TRUE}; \\
&\quad \text{trigger } \langle brb, Deliver \mid s, m \rangle;
\end{aligned}
$$
- **ECHO Phase:** This works similarly to Consistent Broadcast. When it receives more than $\frac{N + f}{2}$ of echoes, the node sends a `READY` message.
- **READY and Propagation Phase:** If a node sees that at least $f$ nodes have sent a `READY` message for the same $m$ (even without having had time to echo), it joins by sending a `READY` message of its own (`sentready := TRUE`). This guarantees **Totality**, propagating the delivery intent throughout the network despite Byzantine behaviors.
- **Deliver Condition:** A node definitively delivers the message $m$ via `brb, Deliver` when it receives `READY` messages from more than $2f$ (`#> 2f`).
- **Resilience:** Again, the entire system is mathematically safe and correct assuming the fundamental fault-tolerance condition $N > 3f$.

![[Dependable distributed system 1-1794390908927.webp|654]]![[Dependable distributed system 1-1794390937647.webp]]![[Dependable distributed system 1-1794390950615.webp]]![[Dependable distributed system 1-1794390964921.webp]]![[Dependable distributed system 1-1794390986135.webp]]
![[Dependable distributed system 1-1794391005141.webp]]

## Registers in Presence of Byzantine Processes
In previous lessons, we analyzed registers in the presence of crash-related failures (_Crash-Stop_ and _Crash-Failure_). In this lesson, we extend the study to Byzantine fault-tolerant registers (with 1 writer and $N$ readers, i.e., $(1, N)$ registers).
###### System Model:
- **Client-Server Paradigm:** We have 1 Writer ($W$), $n$ Readers (the clients), and $N$ Servers/Replicas (where $N$ is the total number of servers).
- **Careful Terminology:** Do not confuse $n$ (the number of readers) with $N$ (the number of replica servers).
- **Failures:** Up to $f$ servers can be **Byzantine** (arbitrary and malicious behavior). Clients can crash but cannot be Byzantine.
- **Communication Channels:** They use flawless, authenticated (`al`) point-to-point links.

#### Safe register
We have two properties for this type of register:
- **BONSR1 (Termination):** If a valid process invokes an operation, it eventually completes.
- **BONSR2 (Validity):** A read that is not concurrent with a write returns the last value written.

In Byzantine broadcast, a quorum of $\frac{N+f}{2}$ was sufficient (with $N > 3f$). However, for **Safe Registers** the semantics are stronger: we require that a write be visible to everyone once it finishes.
To achieve this guarantee, **Masking Quorums** are used:
$$\text{Quorum} = \frac{N + 2f}{2} \quad (\text{ i.e. } 3f + 1), \quad \text{with} N > 4f$$

>[!definitions] Quorum
>Immagina un **quorum** semplicemente come **un comitato o una sotto-fetta di server (o nodi) di dimensione ben definita**. In un sistema distribuito asincrono, se un client vuole scrivere un valore su un registro, vorrebbe teoricamente aggiornare tutti i server della rete. Ma questo è impossibile nella realtà perché: 
> - Alcuni server potrebbero essersi **guasti** (crash).
> - Altri potrebbero essere **estremamente lenti** a rispondere (ritardi di rete).
Se il client aspettasse la risposta di _tutti_ i nodi per completare l'operazione, rimarrebbe bloccato per sempre in attesa del server più lento. D'altra parte, se si accontentasse di un server qualsiasi, rischierebbe di leggere un dato vecchio.
La soluzione consiste nello stabilire una regola matematica: **"Il client non deve aspettare tutti, ma gli basta ricevere risposte da un sottoinsieme predefinito di nodi (il Quorum)"**.
>- **Quorum di Scrittura:** Per dire che una scrittura è riuscita, lo scrittore deve ricevere conferma (ACK) da un certo numero di server appartenenti a quel quorum.
>- **Quorum di Lettura:** Per leggere il valore più recente, il lettore interroga un quorum di server e cerca la versione più fresca tra le risposte ricevute.
>
>Il motivo per cui i quorum funzionano e garantiscono la consistenza (cioè che non si leggano mai dati obsoleti) è la **proprietà matematica di intersezione**.
Qualsiasi quorum di scrittura e qualsiasi quorum di lettura **devono avere almeno un server in comune**.
>- Supponi di avere 5 server in totale.
>- Un quorum di scrittura richiede 3 server. Un quorum di lettura richiede 3 server.
>- Se scrivi aggiornando i server `{1, 2, 3}` e poi leggi interrogando i server `{3, 4, 5}`, noterai che **il server 3 è in comune** tra i due gruppi!
>- Sarà proprio quel server in comune a "passare" al lettore l'informazione aggiornata, impedendo al sistema di perdere il dato o di tornare indietro nel tempo.
>Nei documenti che stiamo studiando, le dimensioni del quorum cambiano in base al livello di sicurezza richiesto:
>1. **Modello Crash / Fail-Silent (Semplice Maggioranza):**
    - Se i nodi possono solo spegnersi (crash) e vogliamo tollerare fino a $f$ guasti, basta una maggioranza semplice: **$\frac{N}{2}$** o **$\frac{N+1}{2}$** (cioè la metà più uno dei nodi).
>2. **Modello Bizantino (Con nodi che mentono o sabotano):**
    - Se ci sono server "bizantini" che possono inviare dati falsi a tradimento, la semplice maggioranza non basta più, perché i bugiardi potrebbero corrompere il risultato.
    - Per questo si usano quorum più grandi (come il _Masking Quorum_ pari a **$\frac{N+2f}{2}$**, cioè $3f+1$), dove $f$ è il numero massimo di traditori tollerati. In questo modo, anche se $f$ nodi mentono spudoratamente, la maggioranza pulita all'interno del quorum è così ampia da "mascherare" e correggere la falsità.

