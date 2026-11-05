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
    - Once processing is fully completed, it triggers the confirmation event: `trigger ⟨jh, Confirm | job⟩;`
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
&\textbf{Uses:} \text{ JobHandler, instance } jh. \\
& \\
&\textbf{Upon event } \langle th, \text{Init} \rangle \textbf{ do} \\
&\quad \text{top} := 1; \quad \text{bottom} := 1; \\
&\quad \text{handling} := \text{FALSE}; \quad \text{buffer} := [\bot]^M; \\
& \\
&\textbf{Upon event } \langle th, \text{Submit} \mid \text{job} \rangle \textbf{ do} \\
&\quad \textbf{if } \text{bottom} + M = \text{top} \textbf{ then} \\
&\quad \quad \text{trigger } \langle th, \text{Error} \mid \text{job} \rangle; \\
&\quad \textbf{else} \\
&\quad \quad \text{buffer}[\text{top} \bmod M + 1] := \text{job}; \\
&\quad \quad \text{top} := \text{top} + 1; \\
&\quad \quad \text{trigger } \langle th, \text{Confirm} \mid \text{job} \rangle; \\
& \\
&\textbf{Upon } \text{bottom} < \text{top} \land \text{handling} = \text{FALSE} \textbf{ do} \\
&\quad \text{job} := \text{buffer}[\text{bottom} \bmod M + 1]; \\
&\quad \text{bottom} := \text{bottom} + 1; \\
&\quad \text{handling} := \text{TRUE}; \\
&\quad \text{trigger } \langle jh, \text{Submit} \mid \text{job} \rangle; \\
& \\
&\textbf{Upon event } \langle th, \text{Confirm} \mid \text{job} \rangle \textbf{ do} \\
&\quad \text{handling} := \text{FALSE};
\end{aligned}
$$This algorithm maintains a buffer of size $M$ to temporarily store incoming jobs and send them one at a time to the underlying JobHandler ($jh$):
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

>[!definitions] Distributed algorithm
>A **distributed algorithm** is a distributed collection of automata, one per process. The automaton regulates how the process executes its computation steps, i.e. how it reacts to a message; every process runs the same automaton. An execution is a sequence of steps executed by the processes.

>[!definitions] Safety and liveness
>A **safety** property states that the algorithm does not do anything wrong: it can be violated at some time $t$ and never be satisfied again after $t$ (equivalently, if it is violated in an execution $E$, there is a partial execution $E^′$ of $E$ such that it is violated in every extension of $E^′$ ). A **liveness** property ensures that eventually something good happens: for any time t there is some hope that it can be satisfied at some $t^′ ≥ t$.

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

>[!definitions] Fault tolerance, resilience
**Fault tolerance** means designing an algorithm that works despite faults, by assuming that at most $f$ of the $N$ processes are faulty. The relation between $f$ and $N$ is called **resilience**; an upper bound $f$ means that any number of processes up to $f$ may fail. *Crash-stop* does not preclude recovery: a recovered process is simply no longer part of the system, and the algorithm must not rely on it to make progress.

**Crash-recovery**. A process is *faulty* if it crashes and never recovers, or if it keeps crashing and recovering infinitely often; otherwise it is correct, even if it crashed and recovered a finite number of times.
A characteristic of this model is *amnesia*: a crashed process loses its internal state, so after recovering it might send messages that contradict those sent before the crash. To cope with this, every process is assumed to have a stable storage (a log), accessed through $store()$ and $retrieve()$, and to be aware upon recovery that it crashed and recovered.![[Dependable distributed system-1793621296255.webp|637]]The he second one crashes and recovers infinitely often so its faulty. The third one its correct and the fourth one crashes and recover finitely so its correct.

#### Timing assumptions

| Model                 | Assumption                                                                                                                              |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Synchronous           | known upper bounds on (i) the time to execute a basic step, (ii) message transit time, (iii) the drift of local clocks w.r.t. real time |
| Asynchronous          | no known bound on processes or links                                                                                                    |
| Partially synchronous | there exists a time t after which the synchrony bounds hold                                                                             |

**Synchronous systems**: enable timed failure detection, measuring transit delays, time-based coordination, worst-case performance guarantees and synchronised clocks. The major problem is the coverage of the synchrony assumption: it is hard to build a system in which the bounds hold with high probability. 
**Asynchronous systems**: even without physical clocks, the passage of time can be measured through the transmission and delivery of messages: time is defined with respect to communication. This is *logical time*, and the resulting notion of clock is a logical clock.
**Partial (eventual) synchrony**: distributed systems are usually synchronous most of the time, with periods of bounded asynchrony. This is captured by eventual synchrony: there is an unknown time t after which the system becomes synchronous. It does not mean that the whole system (hardware, software, network) is synchronous forever after t, nor that the system starts asynchronous and later becomes synchronous. What we need is a period of synchrony long enough for the algorithm to terminate.![[Dependable distributed system-1793621710737.webp|739]]

## Time in distributed systems

In a distributed system the processes run on different nodes interconnected by mean of a network (LAN, WLAN...). This processes cooperate to complete a computation and communicate only through message-based communications. Many applications require a perfect ordering between events and synchronization to terminate correctly. **So time is a critical factor for distributed system.** 

#### Why time is so important? 
We want to measure the time, but why? because many algorithm depends on time and we have many problems related to time: 
1. **Data consistency**: Ensure that different replicas of a database reflect updates in the correct order;
2. **Authentication**: Validate tokens and time-based certificates;
3. **Double processing avoidance**: Prevent the same operation from being performed twice on different nodes.
To solve these problem ordering events is fundamental.

#### System model
A distributed system is composed by a set $\Pi = {{p_1, ..., p_n}}$  of n processes. Each *process* runs on a single mono-processor machine with no shared memory. Each process $p_i$ has a *state* $s_i$ that is changed by the actions it takes during algorithm execution. The state $s_i$ includes all the values of variables maintained by $p_i$. Each process can communicate with other processes only by exchanging messages. 
Each process generates a sequence of events:
- Internal event, an event that transforms the process state;
- External event. send/receive message;
- $e^k_i$, k-th event generated by $P_i$ .

The evolution of the computation can be represented with a space-time diagram:![[Dependable distributed system-1793719418826.webp|694x331]]We denote with $\rightarrow _i$  the ordering relationship between two events in the same processes $p_i$: $e \rightarrow_i$ $e' \iff e \text{ is happened before } e' \text{ in }p_i$. 

>[!definitions] Local history
>Sequence of events produced by a process: $\text{history}(p_1) = h_1 = <e_1^1, e_1^2, e_1^3, e_1^4, e_1^5, e_1^6>$ 

>[!definitions] Partial local history
>Prefix of local history $h_1^m = e_1^1 \text{ ... } e_1^m$ 

>[!definitions] Global history
>Set containing every local history: $H = \cup_i h_i$ per $1 \leq i \leq n$

The first (and bigger) circle is the local history of $p_1$, the second circle is a partial local history of $h_3^3$  
![[Dependable distributed system-1793730759525.webp|650]]
#### Time-stamping and physical clocks
The naive solution to build a global history is *time-stamping*: every process labels each event with the value of its own physical clock. Events of the same process are always ordered this way, **but for processes on different machines the network and processing delays make it impossible to realise a common clock shared by all of them**. What can be done is to synchronize the physical clocks, through suitable algorithms, up to a certain accuracy (synchronization error), and to timestamp events with the synchronized local clock. 
A **physical clock** is a counting register incremented at every tick of an oscillator (hardware clock $H_i(t)$). The operating system reads the hardware $H_i(t)$ and produces a software clock $C_i(t) = \alpha H_i(t) + \beta$ which approximates the real time $t$ at  process $p_i$.
*Example*: $C_i(t)$ may be implemented by a 64-bit word, representing nanoseconds that have elapsed at time t.
Generally this clock is not completely accurate: it can be different from the real time $t$, it can be different at any process due to the precision of the approximation. 
$C_i$ can be used such as timestamp for event produced by $p_i$. To distinguish two events, its resolution (the time between two consecutive increments) must satisfy $T_{resolution} < T$  between the two events.
##### Parameters affecting the Clock Synchronization accuracy
We have two different parameter that affect the clock synchronization accuracy:
- **Skew**: the difference in time between two clocks $\rightarrow |C_i(t) - C_j(t)|$ ;
- **Drift rate**: the gradual misalignment of once synchronized clocks caused by the slight inaccuracies of the time keeping-mechanism. Difference for time unit with respect to an ideal clock. 
The standard of time is the **UTC** $\rightarrow$ *U*niversal *T*ime *C*oordinated, is the base of any international measure. Based on international atomic time: 1 sec = time a cesium atom needs for 9192631770 state transitions.

We have two type of synchronization:
1. **External Synchronization**: processes synchronize their clock $C_i$ with an authoritative external source $S$. Let $D > 0$ be the synchronization bound and $S$ be the source of UTC. Clocks $C_i$ for $i = 1, ... , N$ are **externally synchronized** with a time source $S$ (UTC) if for each time interval $I$: $|S(t) - C_i(t)|$ for $i = 1, 2, ..., N$ and for real time $t$ in $I$.  We say that clocks $C_i$ are accurate within the bound of $D$.
2. **Internal Synchronization**: all the processes synchronize their clocks $C_i$ between them. Let $D > 0$ be the synchronization  bound and let $C_i$ and $C_j$ the clocks at processes $p_i$ and $p_j$ respectively. Clocks are **internally synchronized** in a time interval $I$: $|C_i(t) - C_j(t)| < D$ for $i, j  = 1,2, ..., N$ and for all time $t$ in $I$. We say that clocks $C_i, C_j$ agree within the bound of $D$.

Internally synchronized clocks are not necessarily externally synchronized: they may drift collectively from the external source. Conversely, processes externally synchronized within D are internally synchronized within 2D (by the triangle inequality).

#### Correct clock 
An *hardware clock* $H$ is **correct** if its drift rate is within a limited bound of $\rho > 0$, respecting: $1 - \rho \leq \frac{dC}{dt} \leq 1 + \rho$. 
In presence of a correct hardware clock $H$ we can measure a time interval [$t, t'$] ($\forall t' > t$) introducing only limited error. So we can quantify and limit the error: $(1 - \rho)(t' - t) \leq H(t') - H(t) \leq (1 + \rho)(t' - t)$ . So on the left of the formula we have lower limit, that describes the case when the hardware clock is slow. So the interval of time measured by the hardware will be shorter than the real time, but the formula guarantee that it can't be < than ($1 - \rho$) $\cdot$ the real interval. 
![[Dependable distributed system-1793740789023.webp|505]]

>[!important] A hardware clock is considered correct if its speed deviation respects this band between $(1 - \rho)$ and $(1 + \rho)$. Thanks to this inequality, we know with mathematical certainty that, measuring an interval with a clock with a certain drift $\rho$, the introduced error will remain confined within very precise and predictable limits.!

Software clocks have to be **monotone** $t > t' \rightarrow C(t') > C(t)$. The monotonic property can be guaranteed choosing opportune α and β. (Note that α and β can be a function of time) 

**Clock failure**:
◦ *crash failure* : clock simply stops
◦ *arbitrary failure* : clock behaves arbitrarily (e.g. Y2K bug: the day after the 31/1/1999
becomes 1/1/1900 rather than 1/1/2000)

#### Synchronization algorithm based on time server

##### Christian's algorithm (external synchronization)

- **Operation**: A process $p$ sends a time request (ask the current time) through a message $m_r$  to a time server $S$ (connected to a UTC source) and receives the timestamp $t$ via $m_t$. 
![[Dependable distributed system-1793782188573.webp|616]]
- **Timing**: The process sets its clock to $t + \frac{\text{RTT}}{2}$, where $\text{RTT}$ (Round Trip Time) is the total round-trip time for the message. 
- **Accuracy**: The maximum estimated error is $\pm\left(\frac{\text{RTT}}{2} - \min\right)$, where $\min$ is the minimum estimated transmission time on the network.
- **Limitations**: Represents a single point of failure; requires the use of redundant server clusters and authentication mechanisms. A time server can crash and can be attacked. There could exist periods in which the synchronization is not possible $\rightarrow$ ask to multiple servers at the same time. 
![[Dependable distributed system-1793740918279.webp]]*
##### Berkeley Algorithm (Internal Synchronization) 
- **Structure**: Master-slave architecture. 
- **Algorithm steps**: 
	1. The master $p_m$ periodically polls the slaves, sending its own timestamp $t_1$ and receiving the nodes' local timestamps $t_2$ in return. 
	2. The master calculates the differences $\Delta$ ($\Delta = \frac{(t_1 + t_3)}{2 - t_2}$ where $t_3$ is the local clock when the master receive the timestamps $t_2$ in return) between its own clock and that of each process. 
	3. The master discards the values ​​of faulty processes (whose difference exceeds a threshold $\gamma$). 
	4. It calculates the average of the valid values $\Delta p_i$  ​​and sends each process the algebraic correction to apply using $Adg}_p _i = avg - \Delta p_i$. 
- **Slowdown management**: To avoid jumping back in time (violating causality and monotonicity), excess clocks are not set to a past value, but are slowed down by temporarily hiding hardware clock interrupts.
![[Dependable distributed system-1793782696302.webp]]
![[Dependable distributed system-1793782931443.webp]]
- **Accuracy**: the protocol accuracy depends on the maximum round-trip time. The master does not consider clock values associated to RTT grater than the maximum one. 
- **Fault** **tolerance**:
	- If the master crashes, another master is elected (in an unknown time);
	- It is tolerant to arbitrary behavior (eg. slaves that send wrong values);
	- Master process consider a certain number of clock values and these values do not differ between them over a certain threshold;
>[!Observation] What does slowing down a clock mean? 
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
![[Dependable distributed system-1793783886141.webp]]The NTP hierarchy is reconfigurable in presence of faults: 
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
- If two events $e_i$ and $e_j$ are not linked by any cause-effect relationship, they are said to be **concurrent** ($e_i \vert{}\vert{} e_j$).
- In a distributed system, for any pair of events only one of three conditions is always true:
	1. $e_i \rightarrow e_j$;
	2. $e_j \rightarrow e_i$;
	3. $e_i \vert{}\vert{} e_j$.
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

<mark style="background:#affad1">Example: </mark>![[Dependable distributed system-1793786065529.webp]]
$e_i^j$ is $j-th$ event of process $p_i$, $L_i$ is the logical clock of $p_i$. 
- **System Overview:** The diagram illustrates Lamport's Logical Clocks in a distributed system consisting of three processes ($p_1$, $p_2$, and $p_3$).
- **Initial State:** All processes start with their logical clocks set to zero ($L_1 = 0$, $L_2 = 0$, $L_3 = 0$).
- **Local Events & Internal Increments:** As time progresses locally within each process, events occur and local counters increment sequentially (e.g., $L_3$ goes from $1$ to $4$).
- **Message Passing & Synchronization (Blue Arrows):** When a message is sent from one process to another, it carries the sender's current timestamp. Upon reception, the receiving process updates its own clock by taking the maximum between its current local clock and the incoming timestamp, then adding $1$ ($\max(\text{local}, \text{msg}) + 1$).

$e_1^1 → e^2_1$ and timestamps reflect this property
$e^1_1 || e^1_3$ and respective timestamps have the same value
$e^1_2 || e^1_3$ but respective timestamps have different values

###### Properties and Limitations: 
- **Guaranteed Property**: If $e \rightarrow e'$, then their timestamp satisfies the inequality $ts_e < ts_{e'}$.
- **Critical Bound**: The implication is one-way. The converse is not true: if $ts_e < ts_{e'}$, we cannot deduce with certainty that $e \rightarrow e'$. With scalar clocks, it is not possible to determine whether two events are causally related or concurrent ($\vert{}\vert{}\}$).
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

Lets us consider a set of processes $\Pi ={p_1, ..., p_n}$ and a set of resources $R = {r_1, ..., r_m}$ . The system is asynchronous, the process will be always correct and can communicate by exchanging messages on top of perfect point to point links. 
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
	- `pending = `: Initializes a queue or empty pool to collect incoming requests from various nodes.
	- `current_in_CS = ⊥`: A variable that keeps track of which process is currently in the Critical Section (initially empty/null, denoted by $\bot$).

- **`upon event pp2pDeliver(REQ, j) from pj` (When the coordinator receives a request):**
	- `pending = pending  {pj}`: Takes the requesting process $p_j$ and adds it to the pool of pending requests.

- **The `when pending   and current_in_CS = ⊥` control block (The scheduling logic):**
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
- **Performance**: Entering the CS requires exactly **2 messages** (1 `REQ` and 1 `GRANT`), equivalent to a single RTT (Round Trip Time). Releasing the resource requires **1 **REL` message.
- **Pros**: Extremely lightweight and easy to implement protocol.
- **Cons**:
- Scalability issues if traffic increases on the coordinator.
- **Single Point of Failure (SPOF)**: If the coordinator crashes, the entire mutual exclusion system crashes.

#### Token-based Algorithm on Logical Ring
The processes are organized in a logical ring topology (overlay network) using existing point-to-point communication channels. A single special message called a Token continuously circulates along the ring: only those who possess it are authorized to enter the Critical Section.

**How ​​it works**
1. **Ring construction**: Each process $p_i$ stores the name of the next process in a local variable (`next = p_(i+1) mod N`).
2. **Initialization**: A single token is created (usually generated by an initial node like $p_0$) and inserted into the ring.
3. **Request handling**:
- When a process wants to access the CS, it enters the `waiting` state and **waits to receive the token**.
- When the token arrives, if the process was waiting, it enters the CS (notifying `ok()`). When it is finished (`release()`), it passes the token to the next node (`next`).
- If the process receives the token but is _not_ interested in the CS, it simply **immediately** forwards it to the next process in the ring.

![[Dependable distributed system-1793810509841.webp]]1. we construct an overlay (i.e., a logical network) as a ring exploiting existing point-to point communication channels. The ring is obtained by:
	◦ storing in a local variable the name of the next process in the ring 
	◦ allowing the communication only with the next.
![[Dependable distributed system-1793810667840.webp|681]]$$
\begin{aligned}
&\textbf{Init} \\
&\textbf{state} \text{= idle} \\
&\textbf{next} = p_{i+1}mod (n) 
\end{aligned}
$$2. A token is created and propagated in the ring during the initialization phase (i.e., it is assigned to a process of the system). <mark style="background:#fff88f">WARNING: The token must be unique to guarantee mutual exclusion</mark>. Only one process (selected trough a deterministic function) can create the token during the init.
![[Dependable distributed system-1793810978086.webp|673]]$$
\begin{aligned}
&\textbf{Init} \\
&\textbf{state} \text{= idle} \\
&\textbf{next} = p_{i+1}mod (n) \\
&\text{if self =} p_0 \\
& \quad \quad \quad \text{trigger pp2pSend(TOKEN) to next} 
\end{aligned}
$$
![[Dependable distributed system-1793811185758.webp]]

$$
\begin{aligned}
&\textbf{upon event } \text{request}() \textbf{ do} \\
&\quad \text{state} := \text{waiting}; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{TOKEN}) \textbf{ do} \\
&\quad \textbf{if } \text{state} = \text{waiting} \textbf{ then} \\
&\quad \quad \text{state} := \text{CS}; \\
&\quad \quad \text{trigger } \text{ok}(); \\
&\quad \textbf{else} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next}; \\
& \\
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
&\quad \quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next}; \\
& \\
&\textbf{upon event } \text{request}() \textbf{ do} \\
&\quad \text{state} := \text{waiting}; \\
& \\
&\textbf{upon event } \text{pp2pDeliver}(\text{TOKEN}) \textbf{ do} \\
&\quad \textbf{if } \text{state} = \text{waiting} \textbf{ then} \\
&\quad \quad \text{state} := \text{CS}; \\
&\quad \quad \text{trigger } \text{ok}(); \\
&\quad \textbf{else} \\
&\quad \quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next}; \\
& \\
&\textbf{upon event } \text{release}() \textbf{ do} \\
&\quad \text{state} := \text{idle}; \\
&\quad \text{trigger } \text{pp2pSend}(\text{TOKEN}) \text{ to next};
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
3. All sets have the same size $\vert{}V_i\vert{} = K$ (fair load principle).
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
					$K \approx \sqrt{N}$ and $M = K$.
A rule of thumb for constructing the groups ($V_i$) is to arrange the $N$ processes in a matrix $\sqrt{N} \times \sqrt{N}$ and define the voting set $V_i$ as **the union of the row and column** in which the process $p_i$ is located.

<mark style="background:#d3f8b6">Example: </mark>
Let us consider a system composed by N = 4 processes. $M = K \sim \sqrt[2]{4}$  
Let's place processes in the matrix and compute $V_i$ 

| Process id | V               |
| ---------- | --------------- |
| 1          | $p_1, p_2, p_3$ |
| 2          | $p_1, p_2, p_4$ |
| 3          | $p_1, p_3, p_4$ |
| 4          | $p_2, p_3, p_4$ |
![[Dependable distributed system-1793812092141.webp]]

>[!Warning] 
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
- **Eventual Strong Accuracy** ($\diamond S$)_: There is a time after which no correct process is suspected by another correct process.


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
  - When a process receives a `HEARTBEATREQUEST`, it immediately replies with a `HEARTBEATREPLY.
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
&\quad \text{delay} := \emptyset; \quad (\text{nota: alive } := \emptyset) \\
&\quad \text{starttimer}(\text{delay}); \\
& \\
&\textbf{upon event } \langle pl, \text{Deliver} \mid q, [\text{HEARTBEATREQUEST}] \rangle \text{ do} \\
&\quad \textbf{trigger } \langle pl, \text{Send} \mid q, [\text{HEARTBEATREPLY}] \rangle; \\
& \\
&\textbf{upon event } \text{pl, Deliver} \mid p, [\text{HEARTBEATREPLY}] \rangle \text{ do} \\
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
- **Broadcasting Heartbeats (`HEARTBEATREQUEST`)**: The process sends a new heartbeat request to all nodes, resets the `alive` set to empty (`alive`$:= \emptyset$), and restarts the timer with the updated (or maintained) `delay`.

 2. **Message Delivery** (`Deliver`)
- When a node receives a `HEARTBEATREQUEST`, it immediately responds with a `HEARTBEATREPLY`.
- When a monitoring node receives a `HEARTBEATREPLY` from $p$, it adds $p$ back into the `alive` set (`alive` $:= alive \cup \{p\}$).

###### Correctness 
 1. **Strong Completeness**
	- **What this means:** _"If a process crashes, it stops sending messages. Consequently, the process will be suspected by every healthy process, and no process will change its mind again."
	- **Explanation:** This property is **always true** from the beginning (it doesn't need the word "eventually"). If a node truly dies, it will no longer respond to heartbeat checks, so sooner or later all healthy nodes will notice its absence and permanently add it to the suspect list.

 2. **Eventual Strong Accuracy**
	- **What this means:** _"After time $T$, the system becomes synchronous (i.e., messages between healthy processes arrive within a guaranteed maximum time). If $p$ was suspected in error by $q$, then $q$ will revise its suspicion."
	- **Why is it "eventual"?** This is where the temporal aspect of $\diamond \mathcal{P}$ comes into play.
		- _Before time $T$_ (when the network is asynchronous and unpredictable), the system can make mistakes: a live node can be mistakenly suspected simply because the network delayed a message or because the timeout was set too short.
		- _After time $T$, the magic of **eventually** comes true_: the network stabilizes and becomes synchronous. Messages travel within a certain time limit. If $q had wrongly suspected a live node $p$, as soon as it receives a real message from $p$, it will realize its error and **revise its suspicion**.

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
 1. **Handling Process Crashes** ($\mathcal{P}$, Crash $\mid p$)
- When the underlying Perfect Failure Detector ($\mathcal{P}$) detects that a process $p$ has crashed, it triggers an event.
- The algorithm catches this event and adds $p$ to the `suspected` set (`suspected := suspected \cup \{p\}`).
 1. **Continuous Guard** (`leader ` $\neq maxrank(\Pi \setminus suspected)$)
- This is a reactive guard condition (`upon ... do`) that constantly monitors the set of active (non-suspected) processes: $\Pi \setminus \text{suspected}$.
- **Selecting the Leader**: It computes `maxrank(...)`, which identifies the process with the highest rank among the currently alive processes.
- **Updating and Notifying**: If the current `leader` differs from this newly calculated highest-rank process, the variable is updated (`leader := maxrank(...)`), and a notification is broadcasted via the trigger `trigger `$< le, Leader \mid leader >$` to inform the system of the new leader.

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
- `suspected := \emptyset`: Initially, no processes are suspected of having crashed.
- `leader := \bot`: The local leader variable is undefined ($\bot$) at startup.

 2. **Handling Failure Detector Events** (`Suspect` / `Restore`)
- **`upon event < \diamond\mathcal{P}, Suspect \mid p >`**: When the underlying Eventually Perfect Failure Detector ($\diamond\mathcal{P}$) suspects that a process $p$ has crashed, the algorithm adds $p$ to the `suspected` set (`suspected := suspected \cup \{p\}`).
- **`upon event < \diamond\mathcal{P}, Restore \mid p >`**: If the failure detector later realizes it made a mistake and restores $p$, the algorithm removes $p$ from the `suspected` set (`suspected := suspected \setminus \{p\} psicologico`).

 3. **Continuous Guard and Trust** (`leader \neq maxrank(\Pi \setminus suspected)`)
- This reactive guard continuously monitors the set of currently trusted (non-suspected) processes: $\Pi \setminus \text{suspected}$.
- **Selecting the New Leader**: It computes `maxrank(...)` to find the process with the highest rank among the active processes.
- **Updating and Trusting**: If the calculated highest-rank process differs from the current `leader`, the local variable is updated, and a notification is sent out via `trigger < \Omega, Trust \mid leader >` to signal that the process now trusts this new leader.

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

Until now we studied how a process can communicate with another process, but now we want to study how a process can communicate with 

![[Dependable distributed system-1793873296104.webp]]