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
c) <font color="#ff0000">FALSE</font>. Recall the two rules for updating the scalar logical clock $CK_i$ (initialized to 0 at time t for all processes):
1. **Local or send event:** $CK_i=CK_i + 1$
2. **Receive event** (of a message with timestamp $ts$): $CK_i=max⁡(CK_i,ts)+ 1$
Let's apply them to the relevant events in the diagram:
- **Value of $CK_1$ associated with $e_1$​:**
	- At time t, $CK_1$ = 0
	- $e_1$ is the first event on $p_1​$ (a send to $p_3$​), so it increments its clock by 1: $CK_1(e_1) = 0 + 1$ $= 1$
- **Value of $CK_2$​ associated with $e_6$**
	- At time t, $CK_2 = 0$
	- Looking at the timeline of p2$p_2$p2​ after time ttt, there are no further events before $e_6$​ (the message from p1$p_1$p1​ arrives later, at $e_7$​, and the one from $p_3$​ arrives at $e_{10}$).
	- $e_6$​ is therefore the first event on $p_2$​ (a send to $p_3$​), so it increments its clock by $1$: $CK_2(e_6) = 0 + 1 = 1$
Since $CK_2(e_6) = 1$ and $CK_1(e_1) = 1$, the value associated with $e_6$​ is not strictly greater ($1 \not> 1$) than that associated with $e_1$, making the statement **False (F)**.
d) <font color="#ff0000">FALSE</font>. 
- **Orologi Logici Scalari (Lamport) — Calcolo di $CK_3(e_8)$**
  Seguiamo l'evoluzione dell'orologio scalare di Lamport $CK_3$ sul processo $p_3$, partendo da $CK_3 = 0$ al tempo $t$:
- **Evento $e_2$ (ricezione da $e_1$):**
  - Sappiamo dal punto precedente che $e_1$ invia il messaggio con timestamp $ts = 1$.
  - $CK_3(e_2) = \max(0, 1) + 1 = 2$
- **Evento $e_3$ (invio multiplo / interno):**
  - Su $p_3$, l'evento $e_3$ corrisponde all'invio del messaggio verso $p_1$ (ricevuto in $e_4$), verso $p_2$ (ricevuto in $e_{10}$) e verso sé stesso (ricevuto in $e_5$).
  - Essendo un evento di invio, incrementa l'orologio locale di $1$:
    $$CK_3(e_3) = 2 + 1 = 3$$
  - Il messaggio auto-inviato da $e_3$ a $e_5$ porta quindi il timestamp $ts = 3$.
- **Evento $e_5$ (ricezione dell'auto-messaggio da $e_3$):**
  - Applicando la regola di ricezione:
$$CK_3(e_5) = \max(CK_3(e_3), ts) + 1 = \max(3, 3) + 1 = 4$$
- **Evento $e_8$ (ricezione del messaggio da $e_6$):**
  - Sappiamo dal punto precedente che $e_6$ su $p_2$ ha timestamp $ts = 1$.
  - Applicando la regola di ricezione su $p_3$:
    $$CK_3(e_8) = \max(CK_3(e_5), ts) + 1 = \max(4, 1) + 1 = 5$$

> [!success] Risultato Orologio Scalare
> Il valore dell'orologio logico scalare associato all'evento $e_8$ è **$CK_3(e_8) = 5$**.

- **Vector Clocks — Calcolo di $CK_3(e_8)$**

> [!info] Regole dei Vector Clocks
> Con $3$ processi ($p_1, p_2, p_3$), ogni processo $p_i$ mantiene un vettore $CK_i = [v_1, v_2, v_3]$, inizializzato a $[0, 0, 0]$ al tempo $t$:
> 1. **Evento locale o di invio:** $p_i$ incrementa di $1$ solo la propria posizione ($i$-esima):
>    $$CK_i[i] = CK_i[i] + 1$$
> 2. **Evento di ricezione** (di un messaggio con vettore $V_{msg}$):
>    - Prima aggiorna ogni componente prendendo il massimo elemento per elemento:
>      $$CK_i[k] = \max(CK_i[k], V_{msg}[k]) \quad \forall k \in \{1, 2, 3\}$$
>    - Poi incrementa di $1$ la propria posizione:
>      $$CK_i[i] = CK_i[i] + 1$$
Prima determiniamo i vettori degli eventi su $p_1$ e $p_2$ che inviano messaggi verso $p_3$:
- **Su $p_1$ — Evento $e_1$ (invio verso $e_2$):** parte da $[0, 0, 0]$ e incrementa la $1^\text{a}$ posizione $\implies CK_1(e_1) = [1, 0, 0]$.
- **Su $p_2$ — Evento $e_6$ (invio verso $e_8$):** è il primo evento su $p_2$, quindi parte da $[0, 0, 0]$ e incrementa la $2^\text{a}$ posizione $\implies CK_2(e_6) = [0, 1, 0]$.

Ora seguiamo la riga di $p_3$ partendo da $[0, 0, 0]$ (dove $p_3$ aggiorna sempre la $3^\text{a}$ posizione):

1. **Evento $e_2$ (ricezione da $e_1 = [1, 0, 0]$):**
   - Massimo elemento per elemento tra $[0, 0, 0]$ e $[1, 0, 0] \rightarrow [1, 0, 0]$
   - $+1$ alla $3^\text{a}$ posizione $\implies CK_3(e_2) = [1, 0, 1]$
2. **Evento $e_3$ (invio):**
   - $+1$ alla $3^\text{a}$ posizione $\implies CK_3(e_3) = [1, 0, 2]$
3. **Evento $e_5$ (ricezione dell'auto-messaggio da $e_3 = [1, 0, 2]$):**
   - Massimo tra $[1, 0, 2]$ e $[1, 0, 2] \rightarrow [1, 0, 2]$
   - $+1$ alla $3^\text{a}$ posizione $\implies CK_3(e_5) = [1, 0, 3]$
4. **Evento $e_8$ (ricezione da $e_6 = [0, 1, 0]$):**
   - Massimo elemento per elemento tra $[1, 0, 3]$ e $[0, 1, 0] \rightarrow [1, 1, 3]$
   - $+1$ alla $3^\text{a}$ posizione $\implies CK_3(e_8) = \mathbf{[1, 1, 4]}$

> [!failure] Conclusione
> Il vector clock associato a $e_8$ è **$[1, 1, 4]$** (e non $[3, 0, 3]$), quindi l'affermazione è **Falsa (F)**.


![[Scheda 1-1795015687040.webp]]

![[Scheda 1-1795015700023.webp]]