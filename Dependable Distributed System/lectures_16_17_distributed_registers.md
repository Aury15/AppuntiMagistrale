# Lezioni 16 & 17: Distributed Registers (Registri Distribuiti)

## 1. Introduzione all'Astrazione del Registro

Nei sistemi multiprocessore con memoria condivisa hardware, i processi comunicano leggendo e scrivendo su registri di memoria fisici collegati a un bus comune. Nei **sistemi distribuiti a scambio di messaggi**, non esiste una memoria fisica condivisa: i processi comunicano esclusivamente inviandosi messaggi attraverso la rete.

L'astrazione del **registro distribuito** serve a nascondere questa complessità, offrendo ai programmatori l'illusione di una variabile condivisa globale accessibile tramite operazioni di lettura e scrittura, pur essendo implementata interamente sopra un sistema a message-passing.

### Proprietà e Assunzioni Iniziali
* **Operazioni supportate:** 
  * `read()`: restituisce il valore corrente $v$ del registro senza modificarne il contenuto.
  * `write(v)`: scrive il valore $v$ nel registro e restituisce conferma (*ack*) al completamento.
* **Dominio dei valori:** Il registro memorizza numeri interi positivi ed è inizializzato a $0$.
* **Esecuzione sequenziale per processo:** Un processo non può invocare una nuova operazione prima che quella precedentemente invocata abbia restituito una risposta (*return*).
* **Notazione $(X,Y)$:** Denota un registro in cui $X$ processi possono scrivere e $Y$ processi possono leggere (es. $(1,1)$ o $(1,N)$).

---

## 2. Modello Temporale e Relazioni tra Operazioni

Ogni operazione è delimitata da due eventi istantanei e indivisibili: l'**Invocazione** e il **Return** (o risposta).
* **Operazione completata:** L'intervallo di tempo compreso tra l'invocazione e il return.
* **Operazione fallita (Failed):** Un'operazione invocata da un processo $p_i$ che subisce un crash prima di ricevere il return.
* **Precedenza temporale ($<$):** Dati due operazioni $o_1$ e $o_2$, diciamo che $o_1$ precede $o_2$ ($o_1 < o_2$) se il tempo di return di $o_1$ è antecedente al tempo di invocazione di $o_2$.
* **Concorrenza ($\vert\vert$):** Se gli intervalli temporali di due operazioni si sovrappongono parzialmente o totalmente e non è possibile definire una relazione di precedenza, le operazioni si dicono concorrenti.

---

## 3. Specifiche dei Registri $(1,N)$

Esistono due livelli principali di garanzia per i registri in ambiente concorrente: il registro **Regolare** e il registro **Atomico** (linearizzabile).

### A. $(1,N)$ Regular Register
* **Termination:** Se un processo corretto invoca un'operazione, questa termina sempre.
* **Validity:** 
  1. Una lettura *non* concorrente con alcuna scrittura restituisce l'ultimo valore scritto.
  2. Una lettura *concorrente* con una o più scritture può restituire l'ultimo valore scritto *oppure* uno dei valori scritti in modo concorrente.

### B. $(1,N)$ Atomic Register (Linearizzabile)
Un registro atomico aggiunge un vincolo di ordinamento globale ferreo (equivalente alla linearizzabilità vista per la replicazione):
* **Ordering:** Se una lettura restituisce un valore $v_1$ e una lettura successiva (nel tempo) restituisce $v_2$, allora la scrittura che ha immesso $v_2$ non può essere avvenuta prima della scrittura di $v_1$. Non è consentito "tornare indietro nel tempo" osservando scritture in ordine inverso rispetto alla realtà osservata dai client.

---

## 4. Implementazioni con Failure Detector Perfetto ($\mathcal{P}$) — Algoritmi Fail-Stop

Quando il sistema dispone di un rilevatore di guasti perfetto ($\mathcal{P}$), i crash dei processi possono essere identificati senza errori.

### Read-One Write-All (ROWA) per Registri Regolari (Algoritmo 4.1)
* **Idea:** Ogni processo mantiene una copia locale della variabile. 
  * La **Read** legge istantaneamente dalla propria copia locale (Read-One).
  * La **Write** invia il valore tramite Best-Effort Broadcast a tutti i processi ritenuti vivi e attende gli ACK da parte di tutti i processi non sospettati (Write-All).

```latex
$$
\begin{aligned}
&\textbf{Algorithm 4.1:} \text{ Read-One Write-All (1,N) Regular Register} \\[0.5em]
&\mathbf{upon \ event} \ \langle onrr, Write \mid v \rangle \ \mathbf{do} \\
&\quad \mathbf{trigger} \ \langle beb, Broadcast \mid [WRITE, v] \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle beb, Deliver \mid q, [WRITE, v] \rangle \ \mathbf{do} \\
&\quad val := v; \\
&\quad \mathbf{trigger} \ \langle pl, Send \mid q, ACK \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle pl, Deliver \mid p, ACK \rangle \ \mathbf{do} \\
&\quad writeset := writeset \cup \{p\}; \\
&\mathbf{upon} \ correct \subseteq writeset \ \mathbf{do} \\
&\quad writeset := \emptyset; \\
&\quad \mathbf{trigger} \ \langle onrr, WriteReturn \rangle;
\end{aligned}
$$
```

### Read-Impose Write-All per Registri Atomici (Algoritmo 4.5)
Per trasformare il registro in atomico in presenza di concorrenza, si adotta la regola *"The read operation writes"*: durante un'operazione di lettura, il processo lettore impone a tutti gli altri di aggiornare la propria copia locale con il valore più recente trovato, associandovi un timestamp incrementale.

---

## 5. Implementazioni Fail-Silent (Basate su Maggioranze / Quorum)

Nei sistemi asincroni reali non è possibile avere un failure detector perfetto. Si utilizzano quindi algoritmi **Fail-Silent** basati sull'assunzione che esista una **maggioranza di processi corretti** ($N/2$).

### Majority Voting per Registri Regolari (Algoritmo 4.2)
* **Idea:** Ogni valore scritto viene associato a un timestamp univoco ($ts$). Le operazioni di scrittura e lettura interrogano e aggiornano un **Quorum di maggioranza** ($> N/2$). Poiché l'intersezione tra due maggioranze non è mai vuota, una lettura troverà sempre almeno un processo aggiornato dall'ultima scrittura.

```latex
$$
\begin{aligned}
&\textbf{Algorithm 4.2:} \text{ Majority Voting Regular Register (Write phase)} \\[0.5em]
&\mathbf{upon \ event} \ \langle onrr, Write \mid v \rangle \ \mathbf{do} \\
&\quad wts := wts + 1; \\
&\quad acks := 0; \\
&\quad \mathbf{trigger} \ \langle beb, Broadcast \mid [WRITE, wts, v] \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle beb, Deliver \p, [WRITE, ts', v'] \rangle \ \mathbf{do} \\
&\quad \mathbf{if} \ ts' > ts \ \mathbf{do} \\
&\quad\quad (ts, val) := (ts', v'); \\
&\quad\quad \mathbf{trigger} \ \langle pl, Send \mid p, [ACK, ts'] \rangle; \\[0.8em]
&\mathbf{upon \ event} \ \langle pl, Deliver \mid q, [ACK, ts'] \rangle \ \text{such that} \ ts' = wts \ \mathbf{do} \\
&\quad acks := acks + 1; \\
&\quad \mathbf{if} \ acks > N/2 \ \mathbf{do} \\
&\quad\quad acks := 0; \\
&\quad\quad \mathbf{trigger} \ \langle onrr, WriteReturn \rangle;
\end{aligned}
$$
```

---

## 6. Riduzioni Architetturali: Da Regolare ad Atomico e da $(1,1)$ a $(1,N)$

Nelle lezioni viene mostrato come costruire strutture complesse per passi modulari:
1. **Fase 1:** Si utilizza un registro regolare $(1,N)$ per costruire un registro atomico $(1,1)$ (Algoritmo 4.3), tracciando i timestamp lato lettore per scartare valori obsoleti.
2. **Fase 2:** Si utilizzano istanze multiple di registri atomici $(1,1)$ per costruire un registro atomico $(1,N)$ completo (Algoritmo 4.4).