![[Scheda 1-1795014051579.webp]]

We have two type of sync:
- **External sync**: processes synchronize their clock $C_i$ with an authoritative external source $S$. Let $D>0$ be the sync bound and $S$ source of UTC. Clocks $C_i$ are external sync, for $i = 1, ...,n$, if for each time interval $I$: $|S(t) - C_i(t)| < D$ for real time $t$ in $I$. We say that clock $C_i$ are accurate within the bound $D$.
- **Internal sync**: all the processes sync their clocks $C_i$ between them. Let $D>0$ be the sync bound and let $C_i$ and $C_j$ the clocks at processes $p_i$ and $p_j$ respectively. Clock are internally sync in a time interval $I$: $|C_i(t) - C_j(t)|< D$ for $i =1, ..., n$. We say that clocks $C_i, C_j$ agree within the bound of D. 
1) $D_{ext} = \frac{RTT}{2} - min$ where $RTT= L_{max} \cdot 2$ 
	1) $p_1 \rightarrow$ $D_{ext} = \frac{RTT}{2} = \frac{2 m/s}{2} - 0 = 1m/s$ 
	2) $p_2 \rightarrow$ $D_{ext} = \frac{RTT}{2} = \frac{4 m/s}{2} - 0 = 2m/s$ 
2) Yes because a process externally sync within D is internally sync with 2D:
	1) $|C_1(t) - C_2(t)| < D \rightarrow |C_1(t) - S(t) + S(t) - C_2(t)| \leq |C_1(t) - S(t)| + |S - C_2(t)| = D _{ext}+ D_{ext}$
	2) $D_{int}  = D _{ext}+ D_{ext} = 1 m/s + 2 m/s = 3 m/s$ 

![[Scheda 1-1795014974809.webp]]
![[Scheda 1-1795015214912.webp|737]]
a) <font color="#ff0000">FALSE</font>. To have $e_5 \rightarrow e_7$, there should be a direct causal path from $e_5$ (on $p_3$) to $e_7$ (on $p_2$). In the diagram, $e_5$ is the reception event of an automatic message sent by $e_3$ ($e_3 \rightarrow e_5$), and after $e_5$ on process $p_3$ there is only $e_8$ (which is also a reception, not a send). No arrows originate from $e_5$ to other processes. Likewise, there is no reverse path from $e_7$ to $e_5$: the two events are therefore **concurrent** ($e_5 \parallel e_7$).
b) <font color="#00b050">TRUE</font>. Both events have a common causal ancestor in $e_3$ (both the message received in $e_4$ on $p_1$ and the self-message received in $e_5$ on $p_3$ originate from $e_3$, so $e_3 \rightarrow e_4$ and $e_3 \rightarrow e_5$). However, once $e_3$ has sent those messages, **there is no communication or causal path between $e_4$ and $e_5$**: from $e_5$ no arrows originate to $p_1$, and from $e_4$ (or from subsequent events on $p_1$, such as $e_9$) no path to $e_5$ on $p_3$ originates. Since there is neither $e_4 \rightarrow e_5$ nor $e_5 \rightarrow e_4$, the two events are effectively **concurrent** ($e_4 \parallel e_5$).
c) <font color="#ff0000">FALSE</font>. - **Scalar Logic Clocks (Lamport) — Calculating $CK_3(e_8)$**
Let's follow the evolution of the Lamport scalar clock $CK_3$ on process $p_3$, starting from $CK_3 = 0$ at time $t$:
- **Event $e_2$ (received from $e_1$):**
	- We know from the previous point that $e_1$ sends the message with timestamp $ts = 1$.
	- $CK_3(e_2) = \max(0, 1) + 1 = 2$
- **Event $e_3$ (multiple / internal sending):**
	- On $p_3$, the event $e_3$ corresponds to the sending of the message to $p_1$ (received in $e_4$), to $p_2$ (received in $e_{10}$), and to itself (received in $e_5$).
	- Being a sending event, it increments the local clock by $1$:
$$CK_3(e_3) = 2 + 1 = 3$$
- The message self-sent from $e_3$ to $e_5$ therefore carries the timestamp $ts = 3$.
- **Event $e_5$ (receiving the self-message from $e_3$):**
	- Applying the reception rule:
$$CK_3(e_5) = \max(CK_3(e_3), ts) + 1 = \max(3, 3) + 1 = 4$$
- **Event $e_8$ (receiving the message from $e_6$):**
	- We know from the previous point that $e_6$ on $p_2$ has timestamp $ts = 1$.
	- Applying the reception rule on $p_3$:
$$CK_3(e_8) = \max(CK_3(e_5), ts) + 1 = \max(4, 1) + 1 = 5$$

> [!success] Scalar Clock Result
> The value of the scalar logical clock associated with the event $e_8$ is **$CK_3(e_8) = 5$**.

- **Vector Clocks — Calculating $CK_3(e_8)$**

> [!info] Vector Clock Rules
> With $3$ processes ($p_1, p_2, p_3$), each process $p_i$ maintains a vector $CK_i = [v_1, v_2, v_3]$, initialized to $[0, 0, 0]$ at time $t$:
> 1. **Local or send event:** $p_i$ increments only its own ($i$-th) position by $1$:
> $$CK_i[i] = CK_i[i] + 1$$
> 2. **Receive event** (of a message with vector $V_{msg}$):
> - First, update each component by taking the maximum element by element:
> $$CK_i[k] = \max(CK_i[k], V_{msg}[k]) \quad \forall k \in \{1, 2, 3\}$$
> - Then it increments its position by $1$:
> $$CK_i[i] = CK_i[i] + 1$$
First, we determine the event vectors on $p_1$ and $p_2$ that send messages to $p_3$:
- **On $p_1$ — Event $e_1$ (sending to $e_2$):** starts at $[0, 0, 0]$ and increments the $1^\text{a}$ position $\implies CK_1(e_1) = [1, 0, 0]$.
- **On $p_2$ — Event $e_6$ (send to $e_8$):** This is the first event on $p_2$, so it starts at $[0, 0, 0]$ and increments the $2^\text{a}$ position $\implies CK_2(e_6) = [0, 1, 0]$.

Now let's follow the line of $p_3$ starting from $[0, 0, 0]$ (where $p_3$ always updates the $3^\text{a}$ position):
1. **Event $e_2$ (receiving from $e_1 = [1, 0, 0]$):**
	- Maximum element-by-element between $[0, 0, 0]$ and $[1, 0, 0] \rightarrow [1, 0, 0]$
	- $+1$ at the $3^\text{a}$ position $\implies CK_3(e_2) = [1, 0, 1]$
2. **Event $e_3$ (sending):**
	- $+1$ at the $3^\text{a}$ position $\implies CK_3(e_3) = [1, 0, 2]$
3. **Event $e_5$ (receiving the self-message from $e_3 = [1, 0, 2]$):**
	- Maximum of $[1, 0, 2]$ and $[1, 0, 2] \rightarrow [1, 0, 2]$
	- $+1$ at $3^\text{a}$ position $\implies CK_3(e_5) = [1, 0, 3]$
4. **Event $e_8$ (receiving from $e_6 = [0, 1, 0]$):**
	- Maximum element-by-element between $[1, 0, 3]$ and $[0, 1, 0] \rightarrow [1, 1, 3]$
	- $+1$ at $3^\text{a}$ position $\implies CK_3(e_8) = \mathbf{[1, 1, 4]}$

> [!failure] Conclusion
> The vector clock associated with $e_8$ is **$[1, 1, 4]$** (and not $[3, 0, 3]$), so the statement is **False (F)**.

![[Scheda 1-1795015687040.webp|855]]

![[Scheda 1-1795016547604.webp|712]]

![[Scheda 1-1795015700023.webp]]