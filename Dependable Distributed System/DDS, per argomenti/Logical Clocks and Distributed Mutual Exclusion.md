---
cssclasses:
  - dds-notes
---
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

