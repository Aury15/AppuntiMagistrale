---
cssclasses:
  - dds-notes
---
A distributed system pursues its goal by running a distributed algorithm, i.e. a piece of software that takes care of communication, coordination and resource sharing. The road to a distributed abstraction has three steps: 
1. **System model**: describe the relevant elements in an abstract way, identify their intrinsic properties and characterize their interactions;
2. **Distributed abstraction**: design a protocol that captures a recurring interaction pattern of distributed applications;
3. **Implementation and deployment of the algorithm**, possibly as part of the middleware. This step is system dependent (language, architectural pattern) and belongs to Software Engineering and Lab.

Abstraction are important because they capture properties common to a large and significant range of systems, help to distinguish the fundamental from the accessory, and prevent designers from reinventing the same solution for slight variants of the same problem. Here components are abstracted by their specification and interfaces, and we focus on distributed abstractions and their algorithmic aspects.

#### Composition model 

Protocol are written in pseudo-code that reflects a reactive computing model: components of the same process communicate by exchanging events and and algorithm is a set of event handlers that react to incoming events and possibly trigger new ones. 
$$
\begin{aligned}
&\textbf{upon event } \langle c_1, \text{Event}_1 \mid \text{att}_1, \text{att}_2, \dots \rangle \textbf{ do} \\
&\quad \text{do something;} \quad // \text{send-some-event} \\ 
&\quad \textbf{trigger } \langle c_2, \text{Event}_2 \mid \text{att}_1, \text{att}_2, \dots \rangle; \\
&\textbf{upon condition do} \quad // \text{an-internal-event} \\
&\quad \text{do something;}
\end{aligned}
$$

##### Algorithm Components
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
![[Modelling distributed system-1795363576169.webp|621]]
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
In this example we have two component: the job handler and the instance $jh$ and we can do trhee action. 
Two implementations satisfy this specification. In the *synchronous* one the job is processed before being confirmed; in the *asynchronous* one it is only stored in a buffer, which is why the confirmation says “has been or will be” processed.

###### Synchronous job handler 
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
###### Asynchronous job handler 
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
 **Initialization (`Init`):**
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
Layering has two properties: TH1 and TH2, and these properties has to be respe