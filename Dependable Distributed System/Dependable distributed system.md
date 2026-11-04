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
&\textbf{Algorithm 1.3: } \text{Job-Transformation by Buffering} \\
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
==Stratum Hierarchy==: 
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


