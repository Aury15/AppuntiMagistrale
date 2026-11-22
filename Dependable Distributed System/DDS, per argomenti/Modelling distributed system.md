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

