# Lezione 14: Software Replication (Replicazione del Software)

## 1. Motivazione e Tolleranza ai Guasti

Nei sistemi distribuiti, l'obiettivo principale della replicazione è garantire l'**alta disponibilità** (*availability*) di un servizio (spesso chiamato *oggetto*) nonostante i guasti dei nodi.

### Calcolo della Disponibilità

* Sia $p$ la probabilità di guasto di un singolo oggetto $O$. La disponibilità dell'oggetto singolo è data da:
  $$
  \text{Availability} = 1 - p
  $$

* Se replichiamo l'oggetto $O$ su $n$ nodi indipendenti, assumendo che i guasti siano indipendenti tra loro, la probabilità che *tutti* i nodi falliscano contemporaneamente è $p^n$. La disponibilità del sistema replicato sale drasticamente a:
  $$
  \text{Availability} = 1 - p^n
  $$

---

## 2. Modello di Sistema e Interazioni

* **Insieme di processi:** Clienti e server connessi tramite **canali point-to-point perfetti** (nessuna perdita o corruzione di messaggi).
* **Modello dei guasti:** *Crash fault model* (i processi possono arrestarsi in modo anomalo senza inviare messaggi corrotti o malevoli).
* **Oggetti e Operazioni:** I server gestiscono un insieme di oggetti $X$. Ogni oggetto ha il proprio stato ed è accessibile tramite un insieme di operazioni.
  * Un'operazione eseguita da un processo $p_i$ su un oggetto $x$ si compone di una coppia **Invocazione/Risposta**.
  * **Invocazione:** Notata come $[x \circ p(\text{arg}) p_i]$, dove $\text{arg}$ sono gli argomenti dell'operazione.
  * **Risposta:** Notata come $[x \text{ ok}(\text{res}) p_i]$, dove $\text{res}$ è il risultato restituito.
  * **Coppia completa:** $[x \circ p(\text{arg}) / \text{ok}(\text{res}) p_i]$.
  * **Blocco del client:** Dopo aver emesso un'invocazione, il processo client rimane bloccato finché non riceve la risposta corrispondente.

---

## 3. Requisiti di Replicazione e Trasparenza

Per tollerare il crash dei processi, un oggetto logico deve possedere diverse repliche fisiche dislocate in siti differenti della rete ($x^1, x^2, \dots, x^l$).

* Ogni replica $x^i$ situata sul sito $s$ è gestita da un processo $p_j$ anch'esso situato su $s$.
* Si assume che il processo $p_j$ subisca un crash esattamente quando la sua replica $x^i$ fallisce.
* **Trasparenza:** La complessità e la presenza della replicazione devono essere completamente nascoste ai processi client.

---

## 4. Criteri di Consistenza

Un **criterio di consistenza** definisce il contratto formale tra il programmatore e il sistema di replicazione riguardo al risultato restituito dalle operazioni. Si dividono in due grandi categorie:

1. **Strong Consistency (Consistenza Forte):**
   * *Linearizability* (Linearizzabilità)
   * *Sequential consistency* (Consistenza Sequenziale)
2. **Weak Consistency (Consistenza Debole):**
   * *Causal consistency* (Consistenza Causale)

La differenza principale tra i vari criteri risiede nella definizione dello stato "più recente" visibile al client.

---

## 5. Linearizzabilità (Linearizability)

La linearizzabilità è il criterio di consistenza forte più rigoroso. Richiede l'introduzione di due relazioni fondamentali tra le operazioni:
* **Precedenza ($<$):** Se la risposta dell'operazione $O_1$ precede temporalmente l'invocazione di $O_2$, allora $O_1 < O_2$.
* **Concorrenza ($||$):** Se le operazioni si sovrappongono nel tempo.

### Definizione Formale
Un'esecuzione $E$ è **linearizzabile** se esiste una sequenza globale $S$ (chiamata *sequential specification*) che include tutte le operazioni di $E$ tale che:
1. **Preservazione dell'ordine di precedenza:** Se $O_1 < O_2$, allora $O_1$ deve comparire prima di $O_2$ nella sequenza globale $S$.
2. **Legalità della sequenza:** La sequenza $S$ deve rispettare la specifica sequenziale dell'oggetto (es. il comportamento logico di una variabile o di una coda FIFO).

### Condizioni Sufficienti per la Linearizzabilità
Per garantire la linearizzabilità tra le repliche, il sistema deve rispettare due proprietà fondamentali:
* **Atomicità:** Se una replica di un oggetto $x$ esegue un'invocazione, *ogni* replica corretta di $x$ deve eseguire la stessa invocazione.
* **Ordinamento:** Se due repliche eseguono entrambe due invocazioni, devono eseguirle nello **stesso identico ordine**.

---

## 6. Tecniche di Replicazione

Esistono due approcci architetturali principali per implementare la linearizzabilità:
1. **Primary-Backup (Replicazione Passiva)**
2. **Active Replication (Replicazione Attiva)**

---

## 7. Replicazione Passiva: Primary-Backup

 Nell'approccio *Primary-Backup*, i ruoli delle repliche sono asimmetrici:
* **Primary (Primario):** È un'unica replica designata che riceve le invocazioni dai client, elabora le richieste, aggiorna il proprio stato e coordina i backup.
* **Backup:** Repliche passive che ricevono messaggi di aggiornamento dal primario e replicano lo stato per garantire tolleranza ai guasti in caso di crash del primario.

### Flusso Normale (In assenza di crash)

```latex
\begin{aligned}
&\mathbf{1.} \text{ Il client invia l'invocazione al Primary } (prim(x)). \\
&\mathbf{2.} \text{ Il Primary elabora l'operazione e invia un messaggio di } update \text{ a tutti i Backup.} \\
&\mathbf{3.} \text{ I Backup aggiornano il proprio stato e restituiscono un messaggio di } ack \text{ al Primary.} \\
&\mathbf{4.} \text{ Il Primary attende l'arrivo di tutti gli } ack \text{ dai backup corretti e risponde al client.}
\end{aligned}
```

### Gestione dei Guasti (I Tre Scenari di Crash del Primary)

Quando il primario subisce un crash, è necessario eleggere un nuovo leader tra le repliche backup. Vanno analizzati tre momenti critici:

* **Scenario 1: Il Primary fallisce *dopo* aver inviato la risposta al client.**
  * *Casi:* O il client ha ricevuto la risposta (tutti felici), oppure la risposta è andata perduta a causa dei canali e il client ritrasmette la richiesta dopo un timeout.
  * *Soluzione:* Il nuovo primario riconosce la richiesta come già processata (grazie a un ID univoco) e risponde direttamente senza rieseguire l'aggiornamento sulle repliche.

* **Scenario 2: Il Primary fallisce *prima* di inviare i messaggi di update.**
  * *Soluzione:* Il client non riceve risposte, va in timeout e ritrasmette la richiesta al nuovo primario, che la tratterà come una richiesta del tutto nuova.

* **Scenario 3: Il Primary fallisce *dopo* aver inviato gli update ma *prima* di ricevere tutti gli ack.**
  * *Problema:* Violazione dell'atomicità (l'aggiornamento è arrivato solo ad alcuni backup).
  * *Soluzione:* Richiede meccanismi rigorosi di accordo affinché l'aggiornamento sia recepito da tutti o da nessuno prima del passaggio di consegne al nuovo primario.

---

## 8. Replicazione Attiva (Active Replication)

 Nella **Replicazione Attiva** non esiste un coordinatore centrale o un primario: **tutte le repliche hanno esattamente lo stesso ruolo** ed eseguono lo stesso codice in parallelo.

* **Il Determinismo:** Per funzionare, ogni replica deve essere *deterministica*. Partendo dallo stesso stato iniziale e ricevendo lo stesso input nello stesso ordine, produrranno sempre lo stesso output.
* **Interazione con i Client:** Il client invia la propria invocazione a *tutte* le repliche contemporaneamente e riceverà risposte da ciascuna di esse.

### Come garantire la Linearizzabilità nella Replicazione Attiva?
Per mantenere atomicità e ordinamento senza un primary, la replicazione attiva richiede l'uso rigoroso del **Total Order Broadcast** (incluso l'invio iniziale da parte dei client), in modo che tutte le repliche ricevano le stesse richieste nella medesima sequenza.

* **Gestione dei Crash:** Se una replica fallisce, il sistema non richiede complesse procedure di recupero dello stato o elezioni di leader, poiché le altre repliche continuano a elaborare le richieste senza interruzioni.