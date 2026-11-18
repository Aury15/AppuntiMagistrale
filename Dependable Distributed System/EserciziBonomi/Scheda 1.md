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