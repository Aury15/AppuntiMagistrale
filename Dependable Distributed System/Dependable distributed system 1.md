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
###### 2. $\text{TO(UA, WUTO)}$
- **Composition:** Uniform Agreement + Weak Uniform Total Order.
- **What this means:** The agreement is uniform (all correct processes ultimately receive the same set of messages $M$), and the ordering is weak but uniform: if two processes deliver the same two messages $m$ and $m'$, they do so in the same order. However, due to the "weak" (weak) nature, if a process crashes, the prefixes of the messages delivered before the branch may not match perfectly.

###### 3. $\text{TO(UA, SNUTO)}$
- **Composition:** Uniform Agreement + Strong Non-Uniform Total Order.
- **What this means:** The message agreement is strong and uniform (all correct processes receive the exact same set of messages), but the strong non-uniform total order (SUTO) rule is only guaranteed for processes that remain _correct_. Faulty processes before the crash may violate the strict prefix constraint.

###### 4. $\text{TO(UA, WNUTO)}$
- **Composition:** Uniform Agreement + Weak Non-Uniform Total Order.
- **What this means:** The set of delivered messages is guaranteed to be uniform among all healthy processes (UA), but weak ordering (agreeing on the relative order of shared messages) is required only among healthy processes (WNUTO).

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
1. Inizializzazione dello Stato (`Init`)
	All'avvio, ogni processo imposta le proprie variabili locali:
	- `unordered := ∅`: Un insieme (buffer) che raccoglie i messaggi ricevuti dal livello affidabile ma non ancora ordinati e consegnati.
	- `delivered := ∅`: L'insieme di tutti i messaggi che sono già stati ordinati e consegnati definitivamente all'applicazione.
	- `round := 1`: Un contatore che identifica l'istanza corrente del protocollo di consenso.
	- `wait := FALSE`: Un flag booleano che indica se il processo è attualmente in attesa che un'istanza di consenso giunga a una decisione.

 2. Richiesta di Broadcast (`tob, Broadcast`)
	Quando un'applicazione richiede di inviare un messaggio mmm con ordinamento totale:
	- Il processo attiva semplicemente un broadcast affidabile sottostante: `trigger (rb, Broadcast | m)`. In questo modo, il messaggio viene propagato in modo robusto a tutti i nodi della rete attraverso il modulo rbrbrb.

 3. Ricezione del Messaggio (`rb, Deliver`)
	Quando il livello di Reliable Broadcast consegna un messaggio mmm inviato da un processo ppp:
	- L'algoritmo verifica se il messaggio non è stato già consegnato in precedenza (`m ∉ delivered`).
	- Se è un messaggio nuovo, viene inserito nel buffer dei messaggi in attesa di ordinamento: `unordered := unordered ∪ {(p, m)}`.

 4. Attivazione del Consenso (`Propose`)

Non appena il buffer `unordered` non è vuoto e il processo non è già in attesa di una decisione precedente (`wait = FALSE`):

- Il processo imposta `wait := TRUE` per bloccare nuove proposte nello stesso round.
    
    DDS-Lecture13-2026-2027.pdf
    
- Inizializza una nuova istanza di consenso per il round corrente (`c.round`).
    
    DDS-Lecture13-2026-2027.pdf
    
- Propone al sistema di consenso l'intero contenuto del proprio buffer non ordinato: `trigger (c.round, Propose | unordered)`.
    
    DDS-Lecture13-2026-2027.pdf
    
- _Nota:_ Grazie a questa fase, tutti i nodi proporranno i messaggi che hanno accumulato, e il modulo di consenso garantirà che tutti i processi concordino su una lista comune di messaggi da ordinare per questo round.
    
    DDS-Lecture13-2026-2027.pdf
    

### 5. Decisione e Consegna (`Decide`)

Quando l'istanza di consenso del round rrr restituisce una decisione (`c.r, Decide | decided`), indicando un insieme concordato di messaggi:

- I messaggi decisi vengono ordinati in modo **deterministico** tramite una funzione comune (es. `sort(decided)`), in modo che tutti i processi ottengano esattamente la stessa sequenza.
    
- L'algoritmo scorre la lista ordinata e consegna ciascun messaggio all'applicazione: `trigger (tob, Deliver | s, m)`.
    
- Aggiorna lo stato locale:
    
    - Aggiunge i messaggi elaborati all'insieme dei consegnati: `delivered := delivered ∪ decided`.
        
    - Rimuove i messaggi appena consegnati dal buffer dei non ordinati: `unordered := unordered \ decided`.
        
    - Incrementa il contatore del round (`round := round + 1`) e sblocca il flag (`wait := FALSE`) per permettere l'avvio di un nuovo round di consenso se ci sono altri messaggi in sospeso.