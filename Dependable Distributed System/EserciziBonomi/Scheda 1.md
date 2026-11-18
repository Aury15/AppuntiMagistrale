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
c) <font color="#ff0000">FALSE</font>. Ricordiamo le due regole di aggiornamento dell'orologio logico scalare CKiCK_iCKi​ (inizializzato a 000 al tempo ttt per tutti i processi):
1. **Evento locale o di invio:** $CK_i=CK_i + 1$
2. **Evento di ricezione** (di un messaggio con timestamp $ts$): $CK_i=max⁡(CK_i,ts)+ 1$ 

Applichiamole agli eventi interessati nel diagramma:

- **Valore di CK1CK_1CK1​ associato a e1e_1e1​:**
    
    - Al tempo ttt, CK1=0CK_1 = 0CK1​=0.
        
    - e1e_1e1​ è il primo evento su p1p_1p1​ (un invio verso p3p_3p3​), quindi incrementa di 111 il proprio orologio:
        
        CK1(e1)=0+1=1CK_1(e_1) = 0 + 1 = 1CK1​(e1​)=0+1=1
        
- **Valore di CK2CK_2CK2​ associato a e6e_6e6​:**
    
    - Al tempo ttt, CK2=0CK_2 = 0CK2​=0.
        
    - Osservando la linea temporale di p2p_2p2​ dopo l'istante ttt, non ci sono altri eventi prima di e6e_6e6​ (il messaggio proveniente da p1p_1p1​ arriva dopo, in e7e_7e7​, e quello da p3p_3p3​ arriva in e10e_{10}e10​).
        
    - e6e_6e6​ è quindi il primo evento su p2p_2p2​ (un invio verso p3p_3p3​), perciò incrementa di 111 il proprio orologio:
        
        CK2(e6)=0+1=1CK_2(e_6) = 0 + 1 = 1CK2​(e6​)=0+1=1
        

### Conclusione

Poiché CK2(e6)=1CK_2(e_6) = 1CK2​(e6​)=1 e CK1(e1)=1CK_1(e_1) = 1CK1​(e1​)=1, il valore associato a e6e_6e6​ **non è strettamente maggiore** (1≯11 \not> 11>1) di quello associato a e1e_1e1​, rendendo l'affermazione **Falsa (F)**.