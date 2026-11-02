# Lezione 1: Introduzione al Machine Learning e Teoria delle Decisioni

## 1. Il Paradigma del Machine Learning
Nell'ingegneria del software classica (es. progettazione di un algoritmo di `Insertion Sort`), l'approccio è analitico e deterministico: conosciamo il problema, abbiamo soluzioni esatte, garanzie teoriche e il progettista ha il pieno controllo di ogni singola linea di codice. 

Il Machine Learning (ML) subentra quando il fenomeno sottostante è troppo complesso per essere modellato con un set statico di regole, oppure quando è intrinsecamente stocastico.

> [!definition] Apprendimento Automatico (Tom Mitchell, 1997)
> Un programma informatico apprende dall'**Esperienza E**, rispetto a una classe di **Task T** e una misura di performance **P**, se la sua performance nel task T, misurata da P, migliora con l'esperienza E.

> [!intuition] Perché funziona?
> Invece di codificare esplicitamente la logica di risoluzione (deduzione), forniamo al modello i dati storici (Esperienza E) affinché sia l'algoritmo stesso a inferire le regole e i pattern sottostanti (induzione) per generalizzare su nuovi dati.

---

## 2. Tassonomia del Machine Learning
Possiamo categorizzare i problemi di ML in base alla struttura dei dati a disposizione:

1. **[[Supervised Learning]] (Apprendimento Supervisionato)**:
   - **Cosa facciamo**: Abbiamo a disposizione un dataset di coppie input-output $(x_1, y_1), \dots, (x_n, y_n)$ dove $x \in \mathcal{X}$ è il contesto/input e $y \in \mathcal{Y}$ è la label. L'obiettivo è apprendere la mappa $x \rightarrow y$.
   - **Sottocategorie:
     - *Classificazione Binaria*: $y \in \{0, 1\}$
     - *Classificazione Multi-classe*: $y \in \{1, 2, \dots, K\}$
     - *Regressione*: $y \in \mathbb{R}^d$
2. **[[Unsupervised Learning]] (Apprendimento Non Supervisionato)**:
   - **Cosa facciamo**: Osserviamo solo gli input $x_1, x_2, \dots$ senza alcuna etichetta.
   - **Perché lo facciamo**: L'obiettivo è scoprire la struttura intrinseca o i pattern nascosti (es. clustering, riduzione della dimensionalità) all'interno della distribuzione dei dati.
1. **[[Reinforcement Learning]]**: 
   - Apprendimento basato su premi/punizioni (es. insegnare a un agente a giocare a un videogioco interagendo con l'ambiente).

---

## 3. Le Sfide Fondamentali e l'Assunzione i.i.d.
Il problema centrale del ML non è l'interpolazione (imparare a memoria i dati di training), ma l'**estrapolazione** (generalizzazione): date le osservazioni passate $(x_i, y_i)$, come possiamo fare predizioni accurate per un nuovo $x$ mai visto prima?

> [!theorem] Assunzione Distribuzionale (i.i.d.)
> Affinché i dati passati e i dati futuri siano correlati, assumiamo che esista una distribuzione di probabilità congiunta incognita $\mathcal{D}$ sullo spazio $\mathcal{X} \times \mathcal{Y}$. Assumiamo che:
> 1. I dati di addestramento siano campionati in modo indipendente e identicamente distribuito: $(x_i, y_i) \sim \mathcal{D}$.
> 2. I dati futuri (di test) provengano dalla medesima distribuzione: $(x, y) \sim \mathcal{D}$.

Per simulare questo scenario e valutare i modelli nella pratica, i dati disponibili vengono rigorosamente partizionati:
- **[[Training Set]]**: Utilizzato per "addestrare" i parametri del modello.
- **[[Validation Set]]**: Utilizzato per confrontare modelli diversi e ottimizzare gli iperparametri.
- **[[Test Set]]**: Mantenuto isolato e utilizzato *solo alla fine* per stimare la performance reale e imparziale del modello scelto.
- Una variante robusta di questo partizionamento è la **K-Fold Cross Validation.

---

## 4. Teoria delle Decisioni: E se conoscessimo $\mathcal{D}$?
Se avessimo un "oracolo" che ci fornisce l'esatta distribuzione congiunta $\mathcal{D}$, il problema dell'apprendimento collasserebbe in un puro problema di [[Decision Theory]]. 

Dato un input $x$, noi facciamo una predizione $\hat{y} = f(x)$, ma il vero valore (rivelato a posteriori) è $y$. Dobbiamo quantificare quanto la nostra predizione sia "sbagliata".

> [!definition] Funzione di Loss (Perdita)
> Una funzione di loss è una mappa $l: \mathcal{Y} \times \mathcal{Y} \rightarrow \mathbb{R}_+$ che quantifica il costo associato al commettere un errore. 

**Esempi notevoli di funzioni di loss**:
1. **0-1 Loss**: $l(a,b) = \mathbb{I}_{\{a \neq b\}}$ (restituisce 1 se si sbaglia, 0 altrimenti). Tipica della classificazione.
2. **Loss Quadratica (Squared Loss)**: $l(a,b) = (a-b)^2$. Penalizza in modo aggressivo gli errori molto grandi (outliers). Tipica della regressione.
3. **$L_1$ Loss (Absolute Loss)**: $l(a,b) = |a-b|$.
4. **Loss Asimmetrica**: Penalizza diversamente i Falsi Positivi dai Falsi Negativi (es. $c_1$ se $a=0, b=1$; $c_2$ se $a=1, b=0$). Cruciale in contesti come quello medico o finanziario.

---

## 5. Rischio Atteso (Expected Risk)
Poiché il nostro obiettivo è costruire un modello $f: \mathcal{X} \rightarrow \mathcal{Y}$ che generalizzi bene, non possiamo limitarci a valutare la loss su un singolo punto, ma dobbiamo valutarla *in media* su tutta la popolazione generata da $\mathcal{D}$.

> [!theorem] Formulazione del Rischio Atteso
> Il Rischio Atteso $R_{\mathcal{D}}(f)$ di un predittore $f$ è il valore atteso della loss calcolata rispetto alla vera distribuzione congiunta $\mathcal{D}$:
> $$ R_{\mathcal{D}}(f) = \mathbb{E}_{(x,y) \sim \mathcal{D}}[l(y, f(x))] $$
> Nel caso di variabili discrete, sfruttando la definizione di valore atteso e la regola della probabilità condizionata, l'espressione diventa:
> $$ R_{\mathcal{D}}(f) = \sum_{x \in \mathcal{X}, y \in \mathcal{Y}} P(X=x, Y=y) \cdot l(y, f(x)) $$
> $$ = \sum_{x \in \mathcal{X}} P(X=x) \sum_{y \in \mathcal{Y}} P(Y=y | X=x) \cdot l(y, f(x)) $$

> [!intuition] Perché il Rischio Atteso?
> Calcolare il rischio atteso significa pesare l'errore $l(y, f(x))$ per l'effettiva probabilità con cui quello specifico scenario $(x,y)$ si presenta nella realtà. Il nostro obiettivo ultimo (che definiremo tramite la [[Empirical Risk Minimization]]) sarà trovare la funzione $f^*$ che minimizza questa quantità teorica.

### Calcolo Pratico del Rischio Atteso

> [!example] Caso Studio: Calcolo analitico su distribuzione discreta nota
> Consideriamo uno spazio delle feature $\mathcal{X} = \{1, 2, 3\}$ e uno spazio delle label $\mathcal{Y} = \{0, 1\}$.
> Ci viene fornita la seguente distribuzione di probabilità congiunta $P(X, Y)$:
> 
> | | $X=1$ | $X=2$ | $X=3$ |
> |---|---|---|---|
> | **$Y=0$** | $0$ | $1/2$ | $1/12$ |
> | **$Y=1$** | $1/4$ | $0$ | $1/6$ |
> 
> Vogliamo calcolare il Rischio Atteso per uno specifico predittore $f: \mathcal{X} \rightarrow \mathcal{Y}$ definito come:
> - $f(1) = 1$
> - $f(2) = 0$
> - $f(3) = 1$
> 
> Utilizziamo la **0-1 Loss** (l'errore vale 1 se $y \neq f(x)$, altrimenti 0).
> Applichiamo la formula della sommatoria su tutte le possibili coppie $(x,y)$:
> $$ R_{\mathcal{D}}(f) = \sum_{(x,y)} P(x, y) \cdot l(y, f(x)) $$
> 
> Calcoliamo i contributi individuali:
> - Per $X=1, Y=0$: $P=0$, predizione $f(1)=1 \neq 0 \implies \text{Loss}=1 \implies 0 \cdot 1 = 0$
> - Per $X=1, Y=1$: $P=1/4$, predizione $f(1)=1 = 1 \implies \text{Loss}=0 \implies 1/4 \cdot 0 = 0$
> - Per $X=2, Y=0$: $P=1/2$, predizione $f(2)=0 = 0 \implies \text{Loss}=0 \implies 1/2 \cdot 0 = 0$
> - Per $X=2, Y=1$: $P=0$, predizione $f(2)=0 \neq 1 \implies \text{Loss}=1 \implies 0 \cdot 1 = 0$
> - Per $X=3, Y=0$: $P=1/12$, predizione $f(3)=1 \neq 0 \implies \text{Loss}=1 \implies 1/12 \cdot 1 = 1/12$
> - Per $X=3, Y=1$: $P=1/6$, predizione $f(3)=1 = 1 \implies \text{Loss}=0 \implies 1/6 \cdot 0 = 0$
> 
> Sommando tutti i termini otteniamo il Rischio Atteso:
> $$ R_{\mathcal{D}}(f) = 0 + 0 + 0 + 0 + \frac{1}{12} + 0 = \frac{1}{12} $$

---
# Lezione 2: Rischio Atteso, Predittore di Bayes e Overfitting

## 1. Il Framework del Supervised Learning e il Rischio Atteso

Nel contesto del [[Supervised Learning]], abbiamo a disposizione un set di campioni utilizzati per il training denotato come $(x_1, y_1), (x_2, y_2), \dots, (x_n, y_n)$. L'obiettivo è formulare una predizione su un "nuovo punto" $(x, y)$.

> [!theorem] Assunzione i.i.d. e Rischio Atteso Assumiamo l'esistenza di una distribuzione di probabilità congiunta incognita $\mathcal{D}$ sullo spazio $\mathcal{X} \times \mathcal{Y}$ da cui i dati passati $(x_i, y_i)$ e i dati futuri $(x,y)$ sono campionati in modo indipendente e identicamente distribuito (i.i.d.). Data una funzione di loss $l: \mathcal{Y} \times \mathcal{Y} \rightarrow \mathbb{R}$, il **Rischio Atteso** (Expected Risk) di un predittore $f$ è definito come:
> 
> $$R_{\mathcal{D}}(f) = \mathbb{E}[l(f(x), y)]$$
> 
> Sfruttando la legge della probabilità totale (calcolando il valore atteso rispetto ad $X$ del valore atteso rispetto ad $Y$ condizionato ad $X$), possiamo riscriverlo come:
> 
> $$R_{\mathcal{D}}(f) = \mathbb{E}_X \left[ \mathbb{E}_Y [l(f(x), y) \vert{} X] \right]$$

> [!intuition] Perché questa scomposizione? Riscrivere il Rischio Atteso condizionandolo sull'osservazione $X=x$ ci permette di isolare il problema punto per punto nello spazio delle feature. Questo è il passaggio chiave per definire teoricamente il miglior classificatore possibile, in quanto ci autorizza a minimizzare l'errore per ogni singolo $x$ noto.

## 2. Il Predittore di Bayes (Bayes Predictor)

Se avessimo pieno accesso alla distribuzione $\mathcal{D}$, potremmo costruire il predittore ideale minimizzando il rischio atteso per ogni specifica istanza $x$.

> [!definition] Predittore di Bayes Il Predittore di Bayes, denotato con $f^*$, è la funzione che, per ogni input $x$, seleziona la label $z \in \mathcal{Y}$ che minimizza il valore atteso della loss condizionato a $X=x$:
> 
> $$f^*(x) \in \arg\min_{z \in \mathcal{Y}} \mathbb{E}[l(z, y) \vert{} X=x]$$

### Derivazione Matematica del Predittore di Bayes

Analizziamo il procedimento analitico per ricavare $f^*(x)$ nei due scenari principali (variabili discrete e continue) applicando la formula della probabilità condizionata $P(A\vert{}B) = \frac{P(A, B)}{P(B)}$.

**Caso Discreto (Variabili Aleatorie Discrete):** La definizione di Rischio Atteso si esprime tramite sommatorie:

$$R_{\mathcal{D}}(f) = \sum_{(x,y) \in \mathcal{X} \times \mathcal{Y}} P(X=x, Y=y)l(f(x),y)$$

Scomponendo la probabilità congiunta si ottiene:

$$= \sum_{x \in \mathcal{X}} P(X=x) \left[ \sum_{y \in \mathcal{Y}} P(Y=y \vert{} X=x) l(f(x), y) \right]$$

Il miglior predittore $f^*$ deve minimizzare il termine interno ("blue terms" negli appunti) per ogni $x$. Di conseguenza:

$$f^*(x) \in \arg\min_{z \in \mathcal{Y}} \sum_{y \in \mathcal{Y}} P(Y=y \vert{} X=x) l(z, y)$$

**Caso Continuo (Variabili Aleatorie Continue):** Sostituendo le sommatorie con gli integrali e le probabilità con le funzioni di densità $\rho$:

$$R_{\mathcal{D}}(f) = \int_{\mathcal{X}} \rho_X(x) \int_{\mathcal{Y}} \rho_{Y\vert{}X}(y) l(f(x), y) dy dx$$

Il corrispondente predittore di Bayes diventa:

$$f^*(x) \in \arg\min_{z \in \mathcal{Y}} \int_{\mathcal{Y}} \rho_{Y\vert{}X}(y) l(z, y) dy$$

> [!theorem] Il Rischio Bayesiano La performance del Predittore di Bayes rappresenta il limite teorico invalicabile per il problema. Il rischio ad esso associato si chiama **Rischio Bayesiano** ed è definito come $R_{\mathcal{D}}^* = R_{\mathcal{D}}(f^*)$. _Osservazione cruciale_: Per trovare $f^*$ è strettamente necessario conoscere l'esatta distribuzione $\mathcal{D}$. Poiché nel mondo reale $\mathcal{D}$ è sempre incognita, l'errore bayesiano è fisicamente inevitabile ("unavoidable").

## 3. Rischio Atteso vs. Rischio Empirico

Poiché non possiamo calcolare il Rischio Atteso reale per l'assenza di $\mathcal{D}$, dobbiamo affidarci a una sua stima quantificabile basata sui campioni $S$.

|**Concetto**|**Definizione Matematica**|**Osservazioni**|
|---|---|---|
|**Rischio Atteso** ($R_{\mathcal{D}}(f)$)|$\mathbb{E}[l(f(x), y)]$|È il nostro "Actual Risk", ma **non** è direttamente accessibile (necessita di $\mathcal{D}$).|
|**[[Rischio Empirico]]** ($R_S(f)$)|$\frac{1}{\vert{}S\vert{}} \sum_{i=1}^{\vert{}S\vert{}} l(f(x_i), y_i)$|È calcolabile basandosi esclusivamente sul training set $S=\{(x_1, y_1), \dots\}$. **Non** è il nostro vero rischio.|

> [!intuition] L'approccio Proxy e i suoi Rischi L'idea fondamentale del Machine Learning pratico è utilizzare il Rischio Empirico come _proxy_ (sostituto stimato) per il Rischio Atteso. In generale è una buona idea, ma in pratica richiede molta attenzione a causa del fenomeno dell'**Overfitting**.

> [!definition] Overfitting (Sovradattamento) Un predittore $f : \mathcal{X} \rightarrow \mathcal{Y}$ va in overfitting se si verificano contemporaneamente queste due condizioni:
> 
> 1. $R_S(f)$ è PICCOLO (ha buone performance sui campioni di training).
>     
> 2. $R_{\mathcal{D}}(f)$ è GRANDE (ha pessime performance nella realtà sui dati non visti).
>     

## 4. Caso Studio: Un esempio analitico di Overfitting

Costruiamo un modello matematico che dimostra come l'azzeramento del Rischio Empirico possa produrre un predittore disastroso.

> [!example] Modello su Quadrato Continuo Sia lo spazio delle feature $\mathcal{X} = [-1, 1]^2$ (un quadrato di lato 2) e $X \sim \text{Unif}([-1, 1]^2)$. Definiamo un sottoinsieme interno $Q_{\delta} = [-\delta, \delta]^2$. Il vero processo generativo assegna la label nel modo seguente:
> 
> $$y = \begin{cases} +1 & \text{se } x \in Q_{\delta} \\ -1 & \text{se } x \notin Q_{\delta} \end{cases}$$
> 
> **1. Comportamento Ottimale (Predittore di Bayes)** Usando la loss 0/1, il Predittore di Bayes $f^*$ copia esattamente la regola generativa:
> 
> $$f^*(x) = \begin{cases} +1 & \text{se } x \in Q_{\delta} \\ -1 & \text{se } x \notin Q_{\delta} \end{cases}$$
> 
> Chiaramente, il suo Rischio Atteso (Rischio Bayesiano) è nullo: $R_{\mathcal{D}}^* = R_{\mathcal{D}}(f^*) = 0$.
> 
> **2. Il Classificatore Candidato ($\tilde{f}$)** Proponiamo un classificatore "memorizzatore" $\tilde{f}$ definito come:
> 
> $$\tilde{f}(x) = \begin{cases} +1 & \text{se } (x, 1) \in S \\ -1 & \text{altrimenti} \end{cases}$$
> 
> (Nota: $\tilde{f}$ predice $+1$ _solo_ per i punti esatti osservati nel training set associati alla label $+1$, prevedendo di default $-1$ altrove).

**Analisi delle performance del Classificatore Candidato**:

1. Sull'insieme $S$, $\tilde{f}$ è perfetto: Il Rischio Empirico è $\hat{R}_S(\tilde{f}) = 0$.
    
2. Su $\mathcal{D}$, $\tilde{f}$ è pessimo. Calcoliamo rigorosamente il suo Rischio Atteso partendo dalla definizione di 0/1 loss:
    
    $$R_{\mathcal{D}}(\tilde{f}) = \mathbb{E}[l(\tilde{f}(x), y)] = \mathbb{E}[\mathbb{I}_{\{\tilde{f}(x) \neq y\}}]$$
    
    Espandendo tramite le probabilità condizionate:
    
    $$= P(\tilde{f}(x) \neq y \mid x \in Q_{\delta})P(x \in Q_{\delta}) + P(\tilde{f}(x) \neq y \mid x \notin Q_{\delta})P(x \notin Q_{\delta})$$
    
    Analizziamo i termini:
    
    - All'esterno di $Q_{\delta}$, $\tilde{f}$ predice $-1$ per default, che è la risposta corretta. Pertanto, l'errore è 0: $P(\tilde{f}(x) \neq y \mid x \notin Q_{\delta}) = 0$.
        
    - L'area totale è $2^2=4$ e l'area di $Q_{\delta}$ è $(2\delta)^2=4\delta^2$, quindi per la distribuzione uniforme: $P(x \in Q_{\delta}) = \frac{4\delta^2}{4} = \delta^2$.
        
    - All'interno di $Q_{\delta}$ (dove la vera label è $+1$), $\tilde{f}$ sbaglia a meno che non incontri _esattamente_ un punto $x_i$ già visto nel training set. Ma poiché $X$ ha una distribuzione continua in $\mathbb{R}^2$, la probabilità di ricampionare un punto esatto $(x_i, y_i) \in S$ è nulla (0). Pertanto l'errore dentro il quadrato interno è certo: $P(\tilde{f}(x) \neq y \mid x \in Q_{\delta}) = 1$.
        
    
    Sostituendo questi valori, otteniamo che il Rischio Atteso effettivo è:
    
    $$R_{\mathcal{D}}(\tilde{f}) = 1 \cdot \delta^2 + 0 = \delta^2$$
    

> [!intuition] Conclusione dell'Esempio Se fissiamo ad esempio $\delta = 1/2$, il nostro predittore ha un errore empirico dello $0\%$, ma un errore atteso nella realtà pari a $(1/2)^2 = 1/4$ ($25\%$), che è un errore estremamente largo. Questo dimostra formalmente che fidarsi ciecamente della minimizzazione del Rischio Empirico, permettendo al modello di memorizzare i dati, porta inevitabilmente a un fallimento predittivo (Overfitting) su funzioni complesse.

# Lezione 3: Disuguaglianze di Concentrazione, Uniform Convergence e Scomposizione dell'Errore

## 1. Il Problema della Sample Complexity

Nel setting di [[Supervised Learning]], assumiamo di avere una distribuzione incognita $\mathcal{D}$ su $\mathcal{X} \times \mathcal{Y}$ e un dataset di campioni i.i.d. $S = \{(x_1, y_1), \dots, (x_m, y_m)\}$. Dato un classificatore $f$, conosciamo il suo Rischio Empirico $R_S(f) = \frac{1}{\vert{}S\vert{}}\sum_{i \in S}l(f(x_i), y_i)$ e vorremmo che fosse una buona approssimazione del Rischio Atteso $R_{\mathcal{D}}(f) = \mathbb{E}[l(f(x), y)]$, specificatamente limitandoci alla 0/1 loss per la classificazione binaria.

> [!definition] La Domanda Fondamentale (Sample Complexity) Data una tolleranza o precisione $\epsilon \in (0,1)$ e una probabilità di fallimento $\delta \in (0,1)$, quanti campioni i.i.d. $m$ sono necessari per garantire che la discrepanza tra il rischio empirico e il rischio atteso sia limitata da $\epsilon$, con una probabilità di successo pari ad almeno $(1-\delta)$? Formalmente, cerchiamo $m$ tale che:
> 
> $$P(\vert{}\hat{R}_S(f) - R_{\mathcal{D}}(f)\vert{} \le \epsilon) \ge 1 - \delta$$

## 2. Lo Strumento Matematico: Disuguaglianza di Chernoff-Hoeffding

Per rispondere al problema, dobbiamo collegare la media campionaria al valore atteso teorico sfruttando i teoremi di concentrazione della misura.

> [!theorem] Lemma 1 (Chernoff-Hoeffding) Siano $Z_1, \dots, Z_n$ variabili aleatorie indipendenti e identicamente distribuite (i.i.d.) con valore atteso $\mu$ e limitate tali che $0 \le Z_t \le 1$ per ogni $t = 1, \dots, n$. Definiamo la media empirica $\bar{Z} = \frac{1}{n}\sum_{t=1}^n Z_t$. Allora, per qualsiasi $\epsilon > 0$, la probabilità che la media empirica devii dal valore atteso di una quantità maggiore di $\epsilon$ decade esponenzialmente:
> 
> $$\mathbb{P}\left(\frac{1}{n}\sum_{t=1}^n Z_t > \mu + \epsilon\right) \le e^{-2\epsilon^2 n} \quad \text{e} \quad \mathbb{P}\left(\frac{1}{n}\sum_{t=1}^n Z_t < \mu - \epsilon\right) \le e^{-2\epsilon^2 n}$$

> [!intuition] Perché lo applichiamo al Machine Learning? Possiamo mappare direttamente le variabili del teorema di Hoeffding al nostro problema di apprendimento.
> 
> - **Cosa facciamo**: Definiamo $Z_i = l(f(x_i), y_i) = \mathbb{I}_{\{f(x_i) \neq y_i\}}$ (la 0/1 loss per ogni campione, che è strettamente limitata tra 0 e 1).
>     
> - **Conseguenza**: La media empirica $\bar{Z}$ coincide esattamente con il Rischio Empirico $\hat{R}_S(f)$ e il valore atteso $\mu = \mathbb{E}[Z_i]$ coincide con il Rischio Atteso $R_{\mathcal{D}}(f)$.
>     

Applicando il limite bilaterale (sommando le due code della disuguaglianza) otteniamo:

$$P(\vert{}\hat{R}_S(f) - R_{\mathcal{D}}(f)\vert{} > \epsilon) \le 2e^{-2\epsilon^2 m}$$

Imponendo che questa probabilità di errore sia al massimo pari a $\delta$, possiamo esplicitare il numero minimo di campioni necessari $m$:

$$2e^{-2\epsilon^2 m} \le \delta \implies e^{-2\epsilon^2 m} \le \frac{\delta}{2} \implies -2\epsilon^2 m \le \ln\left(\frac{\delta}{2}\right)$$

$$m \ge \frac{1}{2\epsilon^2} \ln\left(\frac{2}{\delta}\right)$$

## 3. Dalla singola funzione alla Famiglia di Ipotesi $\mathcal{H}$

Il limite trovato garantisce la convergenza per _un singolo_ classificatore $f$ fissato a priori. Tuttavia, nel Machine Learning, l'algoritmo esplora uno spazio per trovare il modello migliore. Per evitare l'**Overfitting**, ci vincoliamo in anticipo a una famiglia di classificatori $\mathcal{H}$ (es. tutti i modelli lineari).

Dobbiamo estendere la garanzia in modo che valga **simultaneamente per tutti** i classificatori $h \in \mathcal{H}$.

> [!theorem] Union Bound (Disuguaglianza di Boole) Dati due eventi qualsiasi $A$ e $B$, la probabilità della loro unione è limitata dalla somma delle loro probabilità:
> 
> $$P(A \cup B) \le P(A) + P(B)$$
> 
> _Dimostrazione_: Dalla teoria degli insiemi, $P(A \cup B) = P(A) + P(B) - P(A \cap B)$. Poiché la probabilità dell'intersezione è sempre non negativa, rimuovendola si ottiene il limite superiore. Esteso a un'unione finita di insiemi e applicando le leggi di De Morgan, si mappa al limite sulle probabilità di fallimento.

Assumendo che lo spazio delle ipotesi sia finito ($\vert{}\mathcal{H}\vert{} < +\infty$), definiamo l'evento di successo per una specifica ipotesi $h$ come $\xi_h = \{\vert{}\hat{R}_S(h) - R_{\mathcal{D}}(h)\vert{} \le \epsilon\}$. Sappiamo da Hoeffding che l'evento complementare di fallimento è limitato: $P(\xi_h^c) \le 2e^{-2\epsilon^2 m}$. Vogliamo la probabilità che _tutte_ le ipotesi abbiano successo: $P(\cap_{h \in \mathcal{H}} \xi_h)$. Valutiamo la probabilità dell'evento avverso totale applicando l'Union Bound:

$$P\left(\bigcup_{h \in \mathcal{H}} \xi_h^c\right) \le \sum_{h \in \mathcal{H}} P(\xi_h^c) \le 2\vert{}\mathcal{H}\vert{}e^{-2\epsilon^2 m}$$

### Convergenza Uniforme (Uniform Convergence)

Questo risultato sfocia nel Teorema fondamentale della [[Uniform Convergence]].

> [!theorem] Teorema 1.3 (Uniform Convergence per Spazi di Ipotesi Finiti) Sia $\epsilon \in (0,1)$ un parametro di precisione prefissato. Consideriamo una famiglia finita di classificatori $\mathcal{H}$. Dati $m$ campioni i.i.d., vale il seguente limite superiore per l'ipotesi che devia maggiormente:
> 
> $$P\left(\max_{h \in \mathcal{H}} \vert{}R_{\mathcal{D}}(h) - \hat{R}_S(h)\vert{} > \epsilon\right) \le 2\vert{}\mathcal{H}\vert{}e^{-2\epsilon^2 m}$$
> 
> Ponendo questo limite uguale a $\delta$, otteniamo la Sample Complexity per l'intera famiglia $\mathcal{H}$:
> 
> $$m \ge \frac{1}{2\epsilon^2} \log\left(\frac{2\vert{}\mathcal{H}\vert{}}{\delta}\right)$$

## 4. Empirical Risk Minimizer (ERM) e Decomposizione dell'Errore

In possesso di un dataset in cui la convergenza uniforme è garantita, definiamo formalmente il processo di apprendimento.

> [!definition] [[Empirical Risk Minimizer (ERM)]] Dati $m$ campioni, l'algoritmo ERM seleziona e restituisce l'ipotesi $\hat{h}^*$ all'interno di $\mathcal{H}$ che minimizza il rischio empirico:
> 
> $$\hat{h}^* \in \arg\min_{h \in \mathcal{H}} \hat{R}_S(h)$$

> [!intuition] Distinzione cruciale tra i "Migliori" È fondamentale distinguere $\hat{h}^*$ dal classificatore $h^* \in \arg\min_{h \in \mathcal{H}} R_{\mathcal{D}}(h)$, ovvero il miglior classificatore che avremmo scelto se avessimo conosciuto il rischio reale all'interno della famiglia $\mathcal{H}$. A causa della varianza campionaria, spesso $\hat{h}^* \neq h^*$.

### Analisi Teorica: Quanto è buono $\hat{h}^*$?

Vogliamo valutare analiticamente il Rischio Atteso effettivo del nostro predittore empirico: $R_{\mathcal{D}}(\hat{h}^*)$. Possiamo scomporre questo errore totale in tre componenti fondamentali aggiungendo e sottraendo termini opportuni:

$$R_{\mathcal{D}}(\hat{h}^*) = R_{\mathcal{D}}^* + (R_{\mathcal{D}}(h^*) - R_{\mathcal{D}}^*) + (R_{\mathcal{D}}(\hat{h}^*) - R_{\mathcal{D}}(h^*))$$

1. **Bayes Error ($R_{\mathcal{D}}^*$):** L'errore intrinseco del problema, generato dalla stocasticità dei dati. Totalmente _inevitabile_ ("Unavoidable").
    
2. **[[Inductive Bias]] (Errore di Approssimazione / Inductive Error):** Il termine $(R_{\mathcal{D}}(h^*) - R_{\mathcal{D}}^*)$.
    
    - _Perché è importante?_ Misura quanto perdiamo restringendo la nostra ricerca alla famiglia $\mathcal{H}$ rispetto a tutte le funzioni possibili.
        
    - _Da cosa dipende?_ **Non dipende dai campioni** a disposizione, ma dipende esclusivamente da quanto è _espressiva_ o ampia la famiglia $\mathcal{H}$.
        
3. **Variance Error (Errore di Stima):** Il termine $(R_{\mathcal{D}}(\hat{h}^*) - R_{\mathcal{D}}(h^*))$.
    
    - _Perché è importante?_ Quantifica l'errore commesso per aver scelto il modello basandoci su campioni finiti (rischio empirico) anziché sulla distribuzione reale.
        
    - _Da cosa dipende?_ Dipende direttamente dalla convergenza uniforme.
        

> [!theorem] Bounding dell'Errore di Stima Assumendo di avere il numero di campioni necessario per soddisfare la condizione di convergenza uniforme $\vert{}\hat{R}_S(h) - R_{\mathcal{D}}(h)\vert{} \le \epsilon, \forall h \in \mathcal{H}$, possiamo limitare l'errore di stima. Aggiungendo e sottraendo i rischi empirici $\hat{R}_S(h^*)$ e $\hat{R}_S(\hat{h}^*)$ si ottiene:
> 
> $$R_{\mathcal{D}}(\hat{h}^*) - R_{\mathcal{D}}(h^*) = (R_{\mathcal{D}}(\hat{h}^*) - \hat{R}_S(\hat{h}^*)) + (\hat{R}_S(\hat{h}^*) - \hat{R}_S(h^*)) + (\hat{R}_S(h^*) - R_{\mathcal{D}}(h^*))$$
> 
> Analizziamo i tre termini:
> 
> 1. $(R_{\mathcal{D}}(\hat{h}^*) - \hat{R}_S(\hat{h}^*)) \le \epsilon$ (per l'assunzione di convergenza uniforme garantita dal Theorem 1.3).
>     
> 2. $(\hat{R}_S(\hat{h}^*) - \hat{R}_S(h^*)) \le 0$ (perché $\hat{h}^*$ è, per definizione ERM, il minimo assoluto del rischio empirico in $\mathcal{H}$, quindi il suo rischio empirico è $\le$ di quello di qualsiasi altro $h$, incluso $h^*$).
>     
> 3. $(\hat{R}_S(h^*) - R_{\mathcal{D}}(h^*)) \le \epsilon$ (ancora per convergenza uniforme).
>     
> 
> Risultato finale: l'errore di stima è limitato superiormente da **$2\epsilon$**.

**Take-Home Message (Il Trade-Off Fondamentale)**: Se scegliamo una famiglia $\mathcal{H}$ molto "ricca" o espressiva (LARGER/RICHER $\mathcal{H}$), abbattiamo l'Inductive Error, ma rendiamo enorme lo spazio delle ipotesi. Poiché la disuguaglianza finale per $m$ dipende logaritmicamente da $\vert{}\mathcal{H}\vert{}$, famiglie più espressive richiedono categoricamente più campioni (MORE SAMPLES) per mantenere la garanzia di convergenza uniforme e non far esplodere la componente di Variance Error.

# Lezione 4: Realizzabilità, Shattering e Vapnik-Chervonenkis (VC) Dimension

## 1. Recap: Overfitting e Convergenza Uniforme
Abbiamo visto che è possibile costruire un predittore $\hat{h}$ che va in grave overfitting, ottenendo un rischio empirico nullo $\hat{R}_S(\hat{h}) = 0$ ma un rischio atteso $R_{\mathcal{D}}(\hat{h})$ molto grande. 
È cruciale notare che questo **non è in contraddizione** con le disuguaglianze di concentrazione precedentemente derivate. Le garanzie di Hoeffding valgono per una *singola* ipotesi fissata o per una famiglia in cui abbiamo garantito la [[Uniform Convergence]]: $|\hat{R}_S(h) - R_{\mathcal{D}}(h)| < \epsilon, \forall h \in \mathcal{H}$. Se la convergenza uniforme è garantita da un numero sufficiente di campioni, l'errore del nostro Empirical Risk Minimizer (ERM) sarà sempre limitato da $R_{\mathcal{D}}(\hat{h}^*) \le R_{\mathcal{D}}(h^*) + 2\epsilon$.

---

## 2. Il Caso Realizzabile (Spazio Finito)
Cosa succede se il nostro spazio delle ipotesi contiene il "predittore perfetto"? 

> [!definition] Caso Realizzabile (Realizable Case)
> Assumiamo che la famiglia $\mathcal{H}$ sia finita e che il problema sia *realizzabile*, ovvero esista almeno un'ipotesi $h^* \in \mathcal{H}$ tale per cui l'errore reale è nullo: $R_{\mathcal{D}}(h^*) = 0$. 

> [!theorem] Sample Complexity nel Caso Realizzabile
> Sotto l'ipotesi di realizzabilità, la dipendenza dal parametro di tolleranza $\epsilon$ per il numero di campioni necessari scende da $O(1/\epsilon^2)$ a $O(1/\epsilon)$.
> **Derivazione analitica:**
> 1. Definiamo un'ipotesi $h$ "cattiva" (bad) se il suo rischio atteso è inaccettabile: $R_{\mathcal{D}}(h) > \epsilon$.
> 2. Fissata una singola ipotesi cattiva $h$, qual è la probabilità che essa inganni l'ERM sembrando perfetta sui dati di training ($\hat{R}_S(h) = 0$)?
>    Poiché i campioni sono i.i.d., la probabilità di classificare correttamente un singolo campione è $(1 - R_{\mathcal{D}}(h)) < (1 - \epsilon)$. Su $n$ campioni, la probabilità congiunta è:
>    $$ \mathbb{P}(\hat{R}_S(h) = 0) \le (1 - \epsilon)^n \le e^{-\epsilon n} $$
> 3. Applicando l'Union Bound su tutte le possibili ipotesi cattive in $\mathcal{H}$ (al massimo $|\mathcal{H}|$):
>    $$ \mathbb{P}(\exists h \text{ bad con } \hat{R}_S(h) = 0) \le |\mathcal{H}|e^{-\epsilon n} $$
> 4. Imponendo questa probabilità $\le \delta$, otteniamo la nuova Sample Complexity:
>    $$ n \ge \frac{1}{\epsilon} \ln\left(\frac{|\mathcal{H}|}{\delta}\right) $$

> [!intuition] Perché lo facciamo?
> Nel caso realizzabile, se un'ipotesi è intrinsecamente "cattiva", basteranno molti meno campioni (tasso $1/\epsilon$) affinché essa commetta almeno un errore nel training set e venga scartata dall'algoritmo ERM.

---

## 3. Realizzabilità con Classi Infinite: Il caso delle Soglie (Thresholds)
Fino ad ora abbiamo assunto $|\mathcal{H}| < +\infty$. È possibile imparare con campioni finiti se la classe $\mathcal{H}$ ha infinite funzioni? Sì, purché la sua "complessità" non sia infinita.

> [!example] Caso Studio: Classificatore a Soglia
> Sia $\mathcal{X} = [0,1]$ con distribuzione uniforme e sia la classe delle ipotesi infinita $\mathcal{H} = \{h_\theta : \theta \in [0,1]\}$, dove $h_\theta(x) = +1$ se $x \ge \theta$, e $-1$ altrimenti.
> Assumiamo la realizzabilità tramite una soglia perfetta $\theta^*$.
> - **Rischio Atteso**: Per una generica soglia $\theta$, l'errore reale corrisponde alla probabilità di campionare un punto nell'intervallo tra $\theta$ e $\theta^*$, quindi $R_{\mathcal{D}}(h_\theta) = |\theta - \theta^*|$.
> - **Errore dell'ERM**: Un ERM $\hat{\theta}$ commette un errore $>\epsilon$ se $\hat{\theta} > \theta^* + \epsilon$ oppure $\hat{\theta} < \theta^* - \epsilon$. Questo scenario disastroso si verifica solo se *nessun campione* del training set cade nella regione di "scoperta" $[\theta^*, \theta^* + \epsilon]$ (o simmetricamente a sinistra).
> - **Probabilità di fallimento**: La probabilità che un singolo campione cada fuori dall'intervallo critico di ampiezza $\epsilon$ è $(1-\epsilon)$. Per $n$ campioni, la probabilità che *nessuno* vi cada è $(1-\epsilon)^n \le e^{-n\epsilon}$. Considerando entrambi i lati, $\mathbb{P}(R_{\mathcal{D}}(\hat{h}) > \epsilon) \le 2e^{-n\epsilon}$.
> - **Conclusione**: Servono $n \ge \frac{1}{\epsilon} \ln\left(\frac{2}{\delta}\right)$ campioni per avere garanzie, nonostante $\mathcal{H}$ contenga infinite soglie.

---

## 4. Shattering e Vapnik-Chervonenkis (VC) Dimension
Il caso delle soglie dimostra che la dimensione dell'insieme $|\mathcal{H}|$ non è la misura corretta per quantificare l'espressività geometrica e topologica di un classificatore. Serve una nuova metrica: la **VC-Dimension**.

> [!definition] Shattering (Frantumazione)
> Dato un insieme di punti $S = \{x_1, \dots, x_{|S|}\}$, diciamo che la famiglia $\mathcal{H}$ **shattera** $S$ se $\mathcal{H}$ può realizzare *qualsiasi* delle $2^{|S|}$ possibili etichettature binarie di $S$. Ovvero, per ogni possibile vettore di label $(y_1, \dots, y_{|S|}) \in \{0,1\}^{|S|}$, esiste almeno un'ipotesi $h \in \mathcal{H}$ tale che $h(x_i) = y_i$ per tutti gli $x_i \in S$.

> [!definition] VC-Dimension
> Data una classe di ipotesi $\mathcal{H}$, la sua VC-Dimension (Vapnik-Chervonenkis dimension) è la **dimensione del più grande insieme di punti che può essere shatterato da $\mathcal{H}$**. 
> Formalmente, la VC-dim è $d$ se:
> 1. $\exists$ almeno un set $S$ di cardinalità $d$ che viene shatterato da $\mathcal{H}$.
> 2. $\forall$ set $S$ di cardinalità $d+1$, esso **NON** può essere shatterato da $\mathcal{H}$.
> Se $\mathcal{H}$ può shatterare insiemi di grandezza arbitraria, la VC-dim è $+\infty$.

### Esempi Notabili di VC-Dimension

1. **Soglie in 1D (Thresholds)**: $\mathcal{H}_{threshold}$. 
   - **VC-dim = 1**. Può shatterare 1 punto (selezionando $\theta$ a destra o a sinistra del punto).
   - *Perché non 2?* Dati $x_1 < x_2$, non esiste alcun $h_\theta$ in grado di assegnare $h(x_1) = +1$ e $h(x_2) = -1$. Se $x_1 \ge \theta$, allora necessariamente anche $x_2 \ge \theta$, forzando $h(x_2) = +1$.
2. **Intervalli in 1D**: $h_{[a,b]}(x) = 1$ se $x \in [a,b]$, $-1$ altrimenti.
   - **VC-dim = 2**. Può isolare una coppia di punti assegnando $+1$ all'intervallo che li contiene.
   - *Perché non 3?* Dati 3 punti ordinati $x_1 < x_2 < x_3$, la sequenza di label $(+1, -1, +1)$ è geometricamente irrealizzabile, poiché un singolo intervallo convesso non può saltare il punto centrale.
3. **Unione di $k$ intervalli**: $h(x) = 1$ se $x \in \bigcup_{i=1}^k [a_i, b_i]$.
   - **VC-dim = $2k$**. 
4. **Insiemi Convessi in $\mathbb{R}^2$**: $h_C(x) = 1$ se $x \in C$ (dove $C$ è un set convesso).
   - **VC-dim = $+\infty$**. *Intuizione geometrica:* Se disponiamo $n$ punti sul perimetro di una circonferenza, qualsiasi sottoinsieme di questi punti formerà un poligono convesso che esclude tutti i punti rimanenti. Possiamo shatterare insiemi arbitrariamente grandi.

---

## 5. Il Teorema Fondamentale del PAC Learning
La VC-Dimension ci permette di scrivere la versione finale e universale del limite di complessità campionaria.

> [!theorem] Teorema (Convergenza Uniforme basata su VC-Dim)
> Consideriamo una famiglia fissata di classificatori $\mathcal{H}$ con VC-Dimension finita pari a $d$. Data una precisione $\epsilon$ e una probabilità di fallimento $\delta$, se campioniamo in modo i.i.d. un numero di campioni $m$ tale che:
> $$ m \ge \frac{C}{\epsilon^2} \left( d \log\left(\frac{1}{\epsilon}\right) + \dots \right) $$
> (dove $C$ è una costante e i termini in sospeso dipendono da $\delta$), allora la convergenza uniforme è garantita con probabilità $1-\delta$:
> $$ |\hat{R}_S(h) - R_{\mathcal{D}}(h)| \le \epsilon \quad \forall h \in \mathcal{H} $$
> E conseguentemente, il rischio del nostro ERM sarà limitato da:
> $$ R_{\mathcal{D}}(\hat{h}^*) \le R_{\mathcal{D}}(h^*) + 2\epsilon $$

> [!intuition] Perché è fondamentale?
> Questo teorema sancisce che la condizione necessaria e sufficiente affinché una famiglia di modelli sia "imparabile" (PAC learnable) senza andare in overfitting inesorabile, è che **la sua VC-Dimension sia finita**. Modelli con VC-Dim infinita (es. insiemi convessi arbitrari) possono letteralmente memorizzare qualsiasi set di training, perdendo ogni capacità di estrapolazione.

# Lezione 5: Classificatori Lineari e Algoritmo Perceptron

## 1. I Classificatori Lineari
Fino ad ora abbiamo studiato i limiti teorici dell'apprendimento su classi generiche. Ora introduciamo una delle famiglie di ipotesi $\mathcal{H}$ più fondamentali: i **[[Classificatori Lineari]]**.

- **Cosa facciamo**: Operiamo in uno spazio delle feature continuo $\mathcal{X} = \mathbb{R}^d$ per un problema di classificazione binaria dove $\mathcal{Y} \in \{-1, 1\}$. Un classificatore lineare $h_w(x)$ è parametrizzato da un vettore dei pesi $w \in \mathbb{R}^d$ ed è definito come il segno del prodotto scalare tra il vettore dei pesi e l'input:
  $$h_w(x) = \text{sign}(\langle w, x \rangle) = \begin{cases} +1 & \text{se } \langle w, x \rangle > 0 \\ -1 & \text{se } \langle w, x \rangle < 0 \end{cases}$$
  Ricordando la definizione algebrica e geometrica del prodotto scalare:
  $$\langle a, b \rangle = \sum_{i=1}^d a_i b_i = \vert{}\vert{}a\vert{}\vert{} \cdot \vert{}\vert{}b\vert{}\vert{} \cdot \cos(\theta)$$
- **Perché lo facciamo (Intuizione geometrica)**: Il segno del prodotto scalare $\langle w, x \rangle$ ci dice da che parte si trova $x$ rispetto all'iperpiano ortogonale a $w$ passante per l'origine. Se l'angolo compreso $\theta$ è acuto ($\cos(\theta) > 0$), il punto è classificato come $+1$, altrimenti come $-1$.

---

## 2. La VC-Dimension dei Classificatori Lineari in $\mathbb{R}^d$

> [!theorem] Teorema: VC-Dimension dei Classificatori Lineari
> La [[VC-Dimension]] della famiglia dei classificatori lineari in $\mathbb{R}^d$ passanti per l'origine è esattamente $d$.

> [!intuition] Dimostrazione (Costruttiva e per Assurdo)
> Per dimostrare formalmente che la VC-dim è $d$, dobbiamo soddisfare due requisiti: dimostrare che *esiste* un set di dimensione $d$ che può essere shatterato (First Step), e dimostrare che *nessun* set di dimensione $d+1$ può essere shatterato (Second Step).
> 
> **Step 1: Esiste $S$ con $\vert{}S\vert{}=d$ che è shatterato**
> Scegliamo come set $S$ i vettori della base canonica: $S = \{e_1, e_2, \dots, e_d\}$, dove $e_i$ ha $1$ in posizione $i$ e $0$ altrove. 
> Data una *qualsiasi* assegnazione arbitraria di label (rappresentata come un sottoinsieme di target positivi $S' \subseteq S$), possiamo costruire un vettore pesi $w'$ perfetto ponendo:
> $$w'_i = \begin{cases} +1 & \text{se } e_i \in S' \\ -1 & \text{se } e_i \notin S' \end{cases}$$
> Calcolando la predizione per il punto $e_i$ avremo $h_{w'}(e_i) = \text{sign}(\langle w', e_i \rangle) = \text{sign}(w'_i)$, che corrisponderà esattamente a $+1$ se $e_i \in S'$ e $-1$ altrimenti. Il set è shatterato.
> 
> **Step 2: Nessun set $S$ con $\vert{}S\vert{}=d+1$ può essere shatterato**
> Consideriamo $d+1$ vettori distinti $x_1, \dots, x_{d+1} \in \mathbb{R}^d$. Per le proprietà dell'algebra lineare, in uno spazio a $d$ dimensioni, un set di $d+1$ vettori è per forza linearmente dipendente.
> Pertanto, esistono dei coefficienti $\alpha_1, \dots, \alpha_{d+1} \in \mathbb{R}$ (di cui almeno uno non nullo) tali che:
> $$\sum_{i=1}^{d+1} \alpha_i x_i = 0$$
> Separiamo l'equazione raggruppando da un lato i coefficienti positivi ($S' = \{i \in S \mid \alpha_i > 0\}$) e dall'altro quelli negativi/nulli:
> $$\sum_{i \in S'} \alpha_i x_i = \sum_{j \notin S'} (-\alpha_j) x_j$$
> Ora, supponiamo per assurdo che $S$ possa essere shatterato. Questo significherebbe che esiste un classificatore lineare $w$ capace di classificare $+1$ per tutti gli elementi di $S'$ e $-1$ per il resto. Ovvero: $\langle w, x_i \rangle > 0$ per $i \in S'$ e $\langle w, x_j \rangle < 0$ per $j \notin S'$.
> Applichiamo il prodotto scalare con $w$ ad ambo i membri dell'equazione della dipendenza lineare:
> $$\left\langle \sum_{i \in S'} \alpha_i x_i, w \right\rangle = \left\langle \sum_{j \notin S'} (-\alpha_j) x_j, w \right\rangle$$
> Sviluppando per la linearità del prodotto scalare:
> $$\sum_{i \in S'} \alpha_i \langle x_i, w \rangle = \sum_{j \notin S'} (-\alpha_j) \langle x_j, w \rangle$$
> Analizzando i segni, a sinistra abbiamo la somma di termini strettamente positivi ($>0$), mentre a destra abbiamo la somma di termini strettamente negativi ($<0$). Si giunge quindi all'assurdo: un numero strettamente positivo non può essere uguale a uno strettamente negativo. Ne consegue che il set non può essere shatterato.

**Conseguenze Pratiche (PAC Learning)**:
1. Possiamo sempre apprendere il miglior classificatore lineare fintanto che abbiamo abbastanza campioni. 
2. La Sample Complexity (numero di campioni necessari) per l'Uniform Convergence è proporzionale a $\mathcal{O}(d/\epsilon^2)$. Il numero di campioni scala linearmente con la dimensionalità $d$ dello spazio (che in applicazioni reali può essere enorme!).

---

## 3. L'Algoritmo Perceptron (1957)
Il teorema precedente ci garantisce l'apprendibilità, ma solleva un problema algoritmico: come calcoliamo *efficientemente* l'[[Empirical Risk Minimizer (ERM)]]?
La risposta storica (Frank Rosenblatt, 1957) è l'algoritmo **Batch Perceptron**, progettato per trovare il miglior classificatore lineare nel **Caso Realizzabile**.

> [!definition] Assunzione di Realizzabilità per il Perceptron
> Esiste un vettore dei pesi "ideale" $w^*$ tale che $y_i \langle w^*, x_i \rangle > 0$ per ogni punto $i$ del dataset.
> Per semplicità di analisi, e senza perdere in generalità (visto che si può sempre scalare il vettore), possiamo richiedere una disuguaglianza stretta sul margine d'errore:
> $$\exists w^* : y_i \langle w^*, x_i \rangle \ge 1 \quad \forall i$$

### Pseudocodice dell'Algoritmo
```
Input: Un training set S = {(x_1, y_1), ..., (x_m, y_m)}
Inizializzazione: w^(1) = (0, ..., 0)
Ciclo per t = 1, 2, ...
    Se esiste un punto i nel dataset che è classificato male,
    ovvero tale che: y_i * <w^(t), x_i> <= 0
        Allora: 
            w^(t+1) = w^(t) + y_i * x_i
    Altrimenti:
        Restituisci w^(t)  (Tutti i punti sono corretti)
```


> [!intuition] Perché l'aggiornamento $w^{(t+1)} = w^{(t)} + y_i x_i$ funziona? Quando il Perceptron sbaglia su un punto $(x_i, y_i)$, corregge il vettore $w$ sommando $x_i$ (pesato per la label $y_i$). Analizziamo il prodotto scalare del nuovo vettore rispetto allo stesso punto $x_i$: $$ y_i \langle w^{(t+1)}, x_i \rangle = y_i \langle w^{(t)} + y_i x_i, x_i \rangle = y_i \langle w^{(t)}, x_i \rangle + y_i^2 ||x_i||_2^2 = y_i \langle w^{(t)}, x_i \rangle + ||x_i||_2^2 $$ Dato che $||x_i||_2^2 > 0$, il nuovo prodotto scalare è algebricamente "spostato" nella direzione corretta della label $y_i$. L'algoritmo diventa istantaneamente "meno sbagliato" su quel punto.

## 4. Teorema di Convergenza (Novikoff)

> [!theorem] Teorema (Limite Superiore al numero di iterazioni) Sia $T$ il numero totale di aggiornamenti (iterazioni del for-loop) eseguiti dal Perceptron prima di convergere. Definiamo:
> 
> - $R = \max_i ||x_i||_2$ (il raggio massimo dei dati, ovvero quanto sono "grandi" vettorialmente).
>     
> - $B = \min_w ||w||_2$ soggetto a $y_i \langle w, x_i \rangle \ge 1, \forall i$ (la norma del miglior classificatore a margine unitario, che decodifica la separabilità lineare del problema). Allora, l'algoritmo termina in un numero finito di passi limitato da: $$ T \le B^2 R^2 $$
>     

> [!intuition] Dimostrazione in 3 Passi **1. Crescita del Prodotto Scalare (Lower Bound)**: Denotiamo $w^*$ come l'arg min di $B$ (il classificatore ottimale). Calcoliamo il prodotto scalare tra $w^*$ e lo stato finale dei pesi del perceptron $w^{(T+1)}$ srotolando la sommatoria di tutti i $T$ aggiornamenti effettuati sui punti "problematici" $x_{i_t}$: $$ \langle w^_, w^{(T+1)} \rangle = \sum_{t=1}^T \langle w^_, w^{(t+1)} - w^{(t)} \rangle = \sum_{t=1}^T y_{i_t} \langle w^_, x_{i_t} \rangle $$ Poiché sappiamo che $w^_$ soddisfa il margine $y_i \langle w^*, x_i \rangle \ge 1$ per ogni punto, la somma è garantita essere: $$ \langle w^*, w^{(T+1)} \rangle \ge T \cdot 1 = T $$
> 
> **2. Crescita della Norma (Upper Bound)**: Valutiamo come cresce al massimo la norma quadratica dei pesi a ogni errore: $$ ||w^{(t+1)}||^2 = ||w^{(t)} + y_{i_t} x_{i_t}||^2 = ||w^{(t)}||^2 + 2y_{i_t} \langle w^{(t)}, x_{i_t} \rangle + (y_{i_t})^2 ||x_{i_t}||^2 $$ Poiché l'aggiornamento avviene _solo_ in caso di errore, sappiamo che $y_{i_t} \langle w^{(t)}, x_{i_t} \rangle \le 0$. Inoltre, $||x_{i_t}||^2 \le R^2$. Sostituendo si ottiene: $$ ||w^{(t+1)}||^2 \le ||w^{(t)}||^2 + R^2 $$ Ripetendo per $T$ step partendo da $||w^{(1)}|| = 0$, otteniamo $||w^{(T+1)}||^2 \le T R^2$, estraendo la radice: $$ ||w^{(T+1)}|| \le \sqrt{T} R $$
> 
> **3. Sintesi tramite Cauchy-Schwarz**: Per la disuguaglianza di Cauchy-Schwarz, $\langle w^*, w^{(T+1)} \rangle \le ||w^*|| \cdot ||w^{(T+1)}||$. Inserendo i bounds trovati nei passi 1 e 2: $$ T \le \langle w^_, w^{(T+1)} \rangle \le ||w^_|| \cdot ||w^{(T+1)}|| \le B \cdot \sqrt{T} R $$ Dividendo per $\sqrt{T}$ otteniamo: $\sqrt{T} \le BR \implies T \le B^2 R^2$.

## 5. Osservazione Finale: Gestione del Termine di Bias ($b$)

Nella formulazione precedente abbiamo forzato l'iperpiano a passare per l'origine. E se volessimo un iperpiano traslato $h_{w,b}(x) = \text{sign}(\langle w, x \rangle + b)$?

Possiamo riassorbire il parametro di bias $b$ per ricondurre il tutto alla formulazione pura passante per l'origine. È sufficiente aumentare di una dimensione lo spazio dei dati (il cosiddetto "augmented space trick"), definendo un nuovo vettore dati e un nuovo vettore pesi: $$ \tilde{x} = \begin{pmatrix} x \ 1 \end{pmatrix} \quad \text{e} \quad \tilde{w} = \begin{pmatrix} w \ b \end{pmatrix} $$ In questo modo, il classificatore originario equivale esattamente a $h_{\tilde{w}}(\tilde{x}) = \text{sign}(\langle \tilde{w}, \tilde{x} \rangle)$, e tutta la teoria sviluppata e l'algoritmo Perceptron rimangono intatti e operativi nello spazio $\mathbb{R}^{d+1}$.

# Lezione 6: SVM (Support Vector Machines) e Rilassamento Convesso

## 1. Classificatori Lineari vs. Classificatori Affini
Nelle lezioni precedenti abbiamo studiato classificatori lineari puri (che passano per l'origine), $h_w(x) = \text{sign}(\langle w, x \rangle)$. Cosa cambia introducendo il termine di intercetta $b$?

> [!definition] Classificatore Affine
> Un classificatore affine in $\mathbb{R}^d$ è parametrizzato sia dai pesi $w \in \mathbb{R}^d$ sia dal bias $b \in \mathbb{R}$:
> $$ h_{w,b}(x) = \text{sign}(\langle w, x \rangle + b) = \begin{cases} +1 & \text{se } \langle w, x \rangle > -b \\ -1 & \text{altrimenti} \end{cases} $$

Possiamo ricondurre lo studio degli affini ai lineari tramite una **Feature Map** $\phi$. Mappiamo ogni punto di $\mathbb{R}^d$ in $\mathbb{R}^{d+1}$ aggiungendo una costante unitaria alla fine del vettore:
$$ x \mapsto \phi(x) = (x, 1) $$
E allo stesso modo, estendiamo i pesi: $w \mapsto (w, b)$.
Il prodotto scalare nello spazio esteso restituisce la formula affine esatta:
$$ \langle (w, b), \phi(x) \rangle = \sum_{i=1}^{d+1} (w, b)_i \cdot (x,1)_i = \left( \sum_{i=1}^d w_i \cdot x_i \right) + b \cdot 1 = \langle w, x \rangle + b $$
Di conseguenza, un classificatore affine in $\mathbb{R}^d$ è esattamente un classificatore lineare in $\mathbb{R}^{d+1}$. 
**Corollario immediato**: La VC-Dimension dei classificatori affini in $\mathbb{R}^d$ è $d + 1$.

---

## 2. La Complessità Computazionale e la necessità di un nuovo paradigma
Se il problema è **separabile** (caso realizzabile, esiste almeno un piano che separa perfettamete i dati), il *Perceptron* o la Programmazione Lineare trovano l'iperpiano separatore in tempo polinomiale. 
Ma cosa succede nel setting *Agnostico* (dati non perfettamente separabili, in presenza di rumore)?

> [!theorem] NP-Hardness del setting Agnostico per l'ERM (Guruswami & Raghavendra, 2009)
> Trovare il vettore $w$ che minimizza il Rischio Empirico sotto la 0/1 Loss (cioè minimizzare il numero di errori) è **NP-Hard**. Ancor peggio, è NP-Hard persino da *approssimare* (Weak Proper Agnostic Learning is hard). Questo paralizza l'algoritmo ERM puro: la 0/1 loss, essendo piatta e discontinua ("a gradino"), è matematicamente intrattabile per i solver in spazi ad alta dimensionalità.

L'intera teoria delle Support Vector Machines e del Deep Learning moderno nasce da questa difficoltà: dobbiamo ridefinire il problema matematico in modo che sia convesso e quindi ottimizzabile.

---

## 3. L'Ottimo Geometrico: Hard-SVM (Caso Separabile)
Partiamo dal caso separabile e definiamo il classificatore "migliore possibile" (Boser, Guyon, Vapnik; COLT '92). 
Fissato un vettore direzionale $w$, l'iperpiano che esso definisce è $H_w = \{x \in \mathbb{R}^d : \langle w, x \rangle = 0\}$. La distanza (con segno) di un generico punto $x$ dall'iperpiano $H_w$ è data dalla proiezione ortogonale:
$$ \text{dist}(x, H_w) = \frac{\langle w, x \rangle}{||w||_2} $$

> [!definition] Margine Ottimale ($\gamma^*$)
> Il **Margine** di uno specifico classificatore direzionale (di norma unitaria) è la distanza geometrica del punto del training set più vicino all'iperpiano.
> $$ \gamma(w) = \min_i \frac{y_i \langle x_i, w \rangle}{||w||_2} $$
> L'obiettivo delle SVM è trovare il vettore di pesi che **massimizza questo margine minimo**:
> $$ \gamma^* = \max_w \min_i \frac{y_i \langle x_i, w \rangle}{||w||_2} $$

### Formulazione Primale Hard-SVM
Anziché risolvere un problema di massimizzazione frazionaria complessa, possiamo trasformarlo in un problema di Programmazione Quadratica Convessa equivalente. 
Dato che l'equazione $h_w(x) = \text{sign}(\langle w, x \rangle)$ è invariante rispetto alla scala dei pesi, possiamo forzare (riscalare) la scala di $w$ affinché il prodotto scalare per il punto più vicino valga *esattamente* 1 (margine unitario). Ovvero $\min_i y_i \langle w, x_i \rangle = 1$. 
Con questo vincolo, il margine geometrico diventa $\gamma = \frac{1}{||w||_2}$. 
Massimizzare $1/||w||_2$ è equivalente a minimizzare $||w||_2$, portando alla formulazione canonica:

> [!theorem] Formulazione Hard-SVM (Primal Problem)
> $$ \min_w \frac{1}{2} ||w||_2^2 \quad \text{soggetto a} \quad y_i \langle w, x_i \rangle \ge 1 \quad \forall i $$

> [!intuition] Support Vectors e Condizioni di Ottimalità
> Applicando le condizioni di Fritz John (estensione KKT), possiamo esprimere l'ottimo in forma duale come combinazione lineare dei gradienti dei vincoli attivi:
> $$ \nabla f(w) + \sum \alpha_i \nabla g_i(w) = 0 \implies w^* = \sum_{i \in I} \alpha_i y_i x_i $$
> dove $I$ è l'insieme dei punti in cui il vincolo è "stringente", ovvero per cui l'uguaglianza è esatta $y_i \langle w, x_i \rangle = 1$. 
> Questi vettori, essenziali per la definizione strutturale del piano, prendono il nome di **Support Vectors**. Tutti i punti per cui la disuguaglianza è stretta hanno moltiplicatore $\alpha = 0$ e diventano irrilevanti per l'addestramento.

---

## 4. Soft-SVM e Rilassamento Convesso (Caso Agnostico/Non Separabile)
Quando i dati si sovrappongono e l'iperpiano perfetto non esiste, i vincoli dell'Hard-SVM diventano irrisolvibili. 

Introduciamo le variabili di Slack (tolleranza) $\xi_i \ge 0$, che permettono ai punti di penetrare il margine e posizionarsi nel lato sbagliato al costo di una penalità ("loss").

> [!theorem] Formulazione Soft-SVM Classica
> $$ \min_{w, \xi} \frac{\lambda}{2} ||w||_2^2 + \sum_{i=1}^m \xi_i $$
> Soggetto a: $y_i \langle w, x_i \rangle \ge 1 - \xi_i$ e $\xi_i \ge 0$, $\forall i$

Possiamo ridurre questo problema vincolato eliminando esplicitamente $\xi_i$. 
Il miglior $\xi_i$ per minimizzare il costo totale è il valore minimo che soddisfa il vincolo: 
se $y_i \langle w, x_i \rangle \ge 1$ (punto corretto oltre il margine), $\xi_i = 0$; 
se $y_i \langle w, x_i \rangle < 1$, allora $\xi_i = 1 - y_i \langle w, x_i \rangle$.
Questo si unifica nella funzione massimale nota come **Hinge Loss**:
$$ \xi_i = (1 - y_i \langle w, x_i \rangle)_+ = \max(0, 1 - y_i \langle w, x_i \rangle) $$

> [!theorem] Formulazione Soft-SVM Unificata (Hinge Loss)
> L'obiettivo equivalente di minimizzazione senza vincoli diventa:
> $$ \min_w \underbrace{\frac{\lambda}{2} ||w||_2^2}_{\text{Regolarizzazione}} + \sum_{i=1}^m \underbrace{(1 - y_i \langle w, x_i \rangle)_+}_{\text{Hinge Loss}} $$

> [!intuition] Perché la Hinge Loss cambia le regole del gioco?
> 1. La Hinge Loss è un Upper Bound *convesso* (a forma di V sdraiata) della 0/1 Loss (a forma di gradino). 
> 2. Permette di raggirare il blocco NP-Hard: essendo una funzione convessa (una composizione di funzioni lineari tra la regolarizzazione forte e l'operatore max), la minimizzazione diventa trattabile polinomialmente tramite algoritmi di Discesa del Gradiente o risolutori lineari.
> 3. L'iperparametro $\lambda$ agisce come regolatore ("balancer"): se $\lambda$ è enorme, forza $w$ verso 0 massimizzando la regolarizzazione geometrica; se $\lambda \rightarrow 0$, forza il modello a minimizzare disperatamente gli errori ignorando il rischio di overfitting.

# Lezione 7: Ottimizzazione Convessa, Subgradienti e Lemma Fondamentale

## 1. Recap: Dal Caso Separabile alla Hinge Loss
Ripartiamo dalla dicotomia fondamentale tra i due regimi delle [[Support Vector Machines]]:

- **Hard SVM (Caso Separabile)**: Cerca il margine ottimo minimizzando $\frac{1}{2}||w||^2$ sotto il vincolo stringente $y_i \langle x_i, w \rangle \ge 1$.
- **Soft SVM (Caso Agnostico)**: Rilassa i vincoli introducendo le variabili di slack e unifica il problema in un'unica funzione obiettivo non vincolata.

> [!definition] Hinge Loss vs. 0-1 Loss
> Nel Soft SVM minimizziamo la Hinge Loss (sommata al termine di regolarizzazione):
> $$ l^{Hinge}(a,b) = (1 - ab)_+ $$
> dove l'operatore $(\cdot)_+$ restituisce l'argomento se positivo, 0 altrimenti.
> Al contrario, la loss teorica originaria è la 0-1 loss:
> $$ l^{0-1}(a,b) = \mathbb{I}(a \neq b) $$

> [!intuition] Perché usiamo la Hinge Loss?
> La 0-1 loss assegna a tutti gli errori lo stesso peso (creando una funzione a "gradino" piatta e discontinua). La Hinge Loss rappresenta un limite superiore (*upper bound*) convesso della 0-1 loss. Essendo convessa, ci permette di applicare algoritmi iterativi di discesa, penalizzando in modo proporzionale e continuo i punti che violano il margine.

---

## 2. Gradient Descent (Discesa del Gradiente)
Per minimizzare una funzione obiettivo convessa $f(\theta)$, l'approccio standard è il [[Gradient Descent]].

- **Cosa facciamo**: Aggiorniamo iterativamente i pesi muovendoci nella direzione in cui la funzione decresce più velocemente. La regola di aggiornamento al tempo $t$ è:
  $$ w_{t+1} = w_t - \eta \nabla f(w_t) $$
  dove $-\nabla f(w_t)$ è la direzione di massima decrescita e $\eta$ è il *Learning Rate* (tasso di apprendimento).
- **Interpretazione Geometrica**: Sulla mappa delle curve di livello (*contour map*), ogni ellisse rappresenta un valore costante dell'obiettivo. I passi di discesa attraversano perpendicolarmente queste valli per convergere verso il centro (l'ottimo $w^*$). Su una superficie tridimensionale, l'algoritmo fa "scivolare" il punto lungo la pendenza fino al minimo globale.

---

## 3. Il Problema della Non-Differenziabilità: Il Subgradiente
C'è un ostacolo matematico: la Hinge Loss presenta un "angolo" (quando $1 - y_i \langle w, x_i \rangle = 0$), in cui il gradiente classico *non è definito*. 

> [!intuition] Perché funziona lo stesso?
> Se il gradiente non è definito in un punto, *qualsiasi* direzione che garantisca una decrescita della funzione ("will do the trick") è accettabile per l'algoritmo. Formalizziamo questa nozione geometrica generalizzando la retta tangente.

> [!definition] Subgradiente (Subgradient)
> Sia $F: S \rightarrow \mathbb{R}$ una funzione definita su un dominio convesso $S \subseteq \mathbb{R}^d$. Un vettore $v \in \mathbb{R}^d$ è un **subgradiente** di $F$ nel punto $u \in S$ se soddisfa la disuguaglianza per ogni $z \in S$:
> $$ f(z) \ge f(u) + \langle z-u, v \rangle $$

> [!example] Derivata vs. Subgradiente
> - **Punto differenziabile**: Per $f(u) = \frac{1}{2}u^2$ nel punto $u=1$, l'unica pendenza di supporto è la retta tangente data dalla derivata $f'(1) = 1$. Il set dei subgradienti contiene un solo elemento: $\partial f(1) = \{1\}$.
> - **Punto angoloso (Corner)**: Per $f(u) = |u|$ nel punto $u=0$, esistono infinite rette che passano per l'origine e rimangono *strettamente sotto* la funzione (es. con pendenza $g=0$, $g=0.7$, $g=-0.7$). Il set dei subgradienti è l'intero intervallo: $\partial f(0) = [-1, 1]$.

---

## 4. Analisi di Convergenza: Il Lemma Fondamentale (Lemma 14.1)
Per dimostrare che un algoritmo basato sui subgradienti convergerà all'ottimo, abbiamo bisogno di limitare l'errore accumulato nel tempo. Questo teorema è il cuore dell'[[Online Learning]] e dell'ottimizzazione convessa.

> [!theorem] Lemma 14.1 (Bound sull'aggiornamento iterativo)
> Sia $v_1, \dots, v_T$ una sequenza arbitraria di vettori. Qualsiasi algoritmo con inizializzazione $w^{(1)} = 0$ e regola di aggiornamento della forma $w^{(t+1)} = w^{(t)} - \eta v_t$ soddisfa:
> $$ \sum_{t=1}^T \langle w^{(t)} - w^*, v_t \rangle \le \frac{||w^*||^2}{2\eta} + \frac{\eta}{2} \sum_{t=1}^T ||v_t||^2 $$
> Inoltre, se assumiamo che $||v_t|| \le \rho$ per ogni $t$, $||w^*|| \le B$, e impostiamo il learning rate ottimale $\eta = \sqrt{\frac{B^2}{\rho^2 T}}$, otteniamo il limite di convergenza:
> $$ \frac{1}{T} \sum_{t=1}^T \langle w^{(t)} - w^*, v_t \rangle \le \frac{B \rho}{\sqrt{T}} $$

### Dimostrazione Analitica Passo-Passo

**Passo 1: L'identità algebrica di base**
Partiamo dalla scomposizione della norma quadrata della differenza di due vettori:
$$ ||a - b||^2 = ||a||^2 + ||b||^2 - 2\langle a, b \rangle $$
Isolando il prodotto scalare otteniamo:
$$ \langle a, b \rangle = \frac{1}{2} \left[ ||a||^2 + ||b||^2 - ||a - b||^2 \right] $$

**Passo 2: Sostituzione con le variabili del gradiente**
Poniamo $a = w^{(t)} - w^*$ (la distanza dall'ottimo al passo $t$) e $b = \eta v_t$ (il passo di aggiornamento). 
Notiamo che il termine $a - b$ diventa esattamente la distanza dall'ottimo al passo successivo: 
$$ a - b = w^{(t)} - w^* - \eta v_t = w^{(t+1)} - w^* $$
Sostituendo questi termini nell'identità del Passo 1:
$$ \langle w^{(t)} - w^*, \eta v_t \rangle = \frac{1}{2} \left[ ||w^{(t)} - w^*||^2 + ||\eta v_t||^2 - ||w^{(t+1)} - w^*||^2 \right] $$
Dividendo tutto per $\eta$ (sfruttando la linearità del prodotto scalare) ed estraendo $\eta^2$ dalla norma:
$$ \langle w^{(t)} - w^*, v_t \rangle = \frac{1}{2\eta} \left[ ||w^{(t)} - w^*||^2 - ||w^{(t+1)} - w^*||^2 \right] + \frac{\eta}{2} ||v_t||^2 $$

**Passo 3: Somma Telescopica**
Sommiamo la disuguaglianza per tutti i time-step da $t=1$ a $T$. La somma si divide in due blocchi:
$$ \sum_{t=1}^T \langle w^{(t)} - w^*, v_t \rangle = \frac{1}{2\eta} \sum_{t=1}^T \left[ ||w^{(t)} - w^*||^2 - ||w^{(t+1)} - w^*||^2 \right] + \frac{\eta}{2} \sum_{t=1}^T ||v_t||^2 $$
Il primo blocco è una *somma telescopica*: tutti i termini intermedi si annullano a vicenda, lasciando solo il primo e l'ultimo elemento:
$$ = \frac{1}{2\eta} \left( ||w^{(1)} - w^*||^2 - ||w^{(T+1)} - w^*||^2 \right) + \frac{\eta}{2} \sum_{t=1}^T ||v_t||^2 $$
Poiché l'algoritmo parte dall'origine ($w^{(1)} = 0$), il primo termine diventa $||w^*||^2$. Ignorando il termine $-||w^{(T+1)} - w^*||^2$ (che è sempre $\le 0$ essendo una norma cambiata di segno), limitiamo superiormente l'espressione, dimostrando la prima parte del Lemma:
$$ \le \frac{1}{2\eta} ||w^*||^2 + \frac{\eta}{2} \sum_{t=1}^T ||v_t||^2 $$

**Passo 4: Ottimizzazione del Learning Rate ($\eta$)**
Introduciamo i limiti superiori (bounds) ipotizzati dal teorema: $||w^*|| \le B$ e $||v_t|| \le \rho$. Sostituendo questi massimali, la somma è limitata da:
$$ \le \frac{1}{2\eta} B^2 + \frac{\eta}{2} T \rho^2 $$
Vogliamo impostare $\eta$ per rendere questo errore *il più piccolo possibile*. Analiticamente, la somma di due componenti (una inversamente proporzionale a $\eta$ e l'altra direttamente proporzionale) è minima quando i due termini sono uguali:
$$ \frac{1}{2\eta} B^2 = \frac{\eta}{2} T \rho^2 $$
Risolvendo per $\eta$, troviamo il learning rate teorico ottimale:
$$ \eta = \frac{B}{\sqrt{T} \rho} $$
Sostituendo questo $\eta$ ottimale all'interno del limite e dividendo tutto per $T$ si ottiene il bound finale $\le \frac{B \rho}{\sqrt{T}}$, che conclude la dimostrazione.

# Lezione 8: Convessità, Subgradient Descent e Ottimizzazione Stocastica

## 1. Convessità (Definizioni Fondamentali)
Prima di procedere all'applicazione del Lemma 14.1 all'algoritmo Gradient Descent, dobbiamo cristallizzare analiticamente il concetto di [[Convessità]].

> [!definition] Insieme Convesso
> Un dominio $S \subseteq \mathbb{R}^d$ è convesso se, per qualsiasi coppia di punti $x_1, x_2 \in S$, l'intero segmento che li congiunge appartiene a $S$.
> In formula: $\forall \lambda \in [0,1], \lambda x_1 + (1-\lambda)x_2 \in S$.

> [!definition] Funzione Convessa (Definizione di Base)
> Una funzione $F : S \rightarrow \mathbb{R}$ definita su un set convesso $S$ è convessa se il segmento (corda) che congiunge due punti qualsiasi sulla superficie della funzione si trova sempre *al di sopra* (o coincide con) il grafico della funzione stessa.
> $$ F(\lambda x_1 + (1-\lambda)x_2) \le \lambda F(x_1) + (1-\lambda)F(x_2) \quad \forall x_1, x_2 \in S, \forall \lambda \in [0,1] $$

Per dimostrare l'efficacia del gradiente, è spesso più utile una definizione alternativa basata sulla derivabilità:

> [!theorem] Definizione Equivalente (Funzioni Differenziabili)
> Una funzione differenziabile $F$ è convessa se e solo se l'iperpiano tangente in ogni punto $w$ giace sempre *al di sotto* della funzione in tutto il dominio:
> $$ F(u) \ge F(w) + \langle \nabla F(w), u-w \rangle \quad \forall u, w \in \mathbb{R}^d $$
> 
> *Note di derivazione*: È possibile dimostrare matematicamente l'equivalenza partendo dalla definizione di convessità di base, valutando la derivata direzionale lungo il limite per $t \rightarrow 0$ del segmento parametrizzato $t u + (1-t) w$.

---

## 2. Garanzie di Convergenza del Gradient Descent
Ora colleghiamo le definizioni per valutare le prestazioni dell'algoritmo [[Gradient Descent]] standard: $w^{(t+1)} = w^{(t)} - \eta \nabla F(w^{(t)})$.

Al termine dell'algoritmo, per avere un limite robusto sull'errore, non valutiamo l'ultimissimo punto $w^{(T)}$, ma definiamo l'output come la **media di tutti i pesi generati** $\bar{w} = \frac{1}{T}\sum_{t=1}^T w^{(t)}$.
Confrontiamo questo punto restituito con l'ottimo globale vero $w^*$:
$$ F(\bar{w}) - F(w^*) = F\left(\frac{1}{T}\sum_{t=1}^T w^{(t)}\right) - F(w^*) $$
Per la convessità della funzione $F$, il valore della media è $\le$ della media dei valori (disuguaglianza di Jensen):
$$ \le \frac{1}{T} \sum_{t=1}^T \left[ F(w^{(t)}) - F(w^*) \right] $$
Sostituendo la definizione equivalente di convessità, limitiamo superioremente la differenza di altezza con l'approssimazione lineare (dove il ruolo di $v_t$ del lemma 14.1 è giocato dal gradiente $\nabla F(w^{(t)})$):
$$ \le \frac{1}{T} \sum_{t=1}^T \langle \nabla F(w^{(t)}), w^{(t)} - w^* \rangle $$
Ora possiamo far collassare direttamente questa espressione applicando il Lemma 14.1 dimostrato nella Lezione 7.

> [!theorem] Convergenza del Gradient Descent
> Assumendo che la funzione obiettivo $F$ sia $\rho$-Lipschitziana (il che implica che $||\nabla F(w)|| \le \rho$ per ogni $w$), e che $||w^*|| \le B$, eseguendo il Gradient Descent per $T$ iterazioni con un learning rate ottimale $\eta = B / (\rho \sqrt{T})$, si ottiene la garanzia:
> $$ F(\bar{w}) - F(w^*) \le \frac{B\rho}{\sqrt{T}} $$

---

## 3. Subgradient Descent ed Esempi di Subgradienti
Se la funzione *non* è differenziabile, sostituiamo il gradiente esatto con il **subgradiente** (definito nella Lezione 7). L'algoritmo risultante (Subgradient Descent) gode esattamente delle stesse identiche garanzie matematiche del Gradient Descent derivate tramite il Lemma 14.1.

Vediamo come si calcola la formula chiusa del set dei subgradienti (denotato come $\partial F$) in due casi notevoli.

> [!example] Caso $F(x) = |x|$
> - Se $x > 0$, la derivata esiste e vale $+1 \implies \partial |x| = \{1\}$.
> - Se $x < 0$, la derivata esiste e vale $-1 \implies \partial |x| = \{-1\}$.
> - Se $x = 0$, applicando la definizione $F(u) \ge F(w) + \langle g, u-w \rangle \implies |u| \ge g \cdot u$. Questo è vero per ogni $u$ solo se $g \in [-1, 1]$. Quindi il set dei subgradienti nell'origine è l'intero intervallo $\partial |x|(0) = [-1, 1]$.

> [!example] Caso $F(x) = \max(0, x) = (x)_+$ (Regione cerniera della Hinge Loss)
> - Se $x > 0 \implies \partial F = \{1\}$.
> - Se $x < 0 \implies \partial F = \{0\}$.
> - Se $x = 0$, $F(u) \ge g \cdot u \implies \max(0, u) \ge g \cdot u$. L'intervallo valido è $\partial F(0) = [0, 1]$.

### Subgradient Descent per l'ERM delle Soft-SVM (Primal)
Applichiamo le regole di subdifferenziazione appena derivate per calcolare il passo di ottimizzazione della Soft-SVM. L'obiettivo da minimizzare è:
$$ F(w) = \frac{\lambda}{2}||w||^2 + \frac{1}{m}\sum_{i=1}^m \underbrace{(1 - y_i \langle x_i, w \rangle)_+}_{l_i(w)} $$
Per la linearità del subgradiente, valutiamo i termini singolarmente. Il subgradiente di $l_i(w)$ rispetto a $w$ vale:
- $-y_i x_i$, se $y_i\langle x_i, w \rangle < 1$ (siamo nel tratto in pendenza del max, $x>0$).
- $0$, se $y_i\langle x_i, w \rangle > 1$ (siamo nel tratto piatto del max, $x<0$).
- $\{-\alpha y_i x_i \mid \alpha \in [0,1]\}$, se $y_i\langle x_i, w \rangle = 1$ (il punto angoloso).

Per operare, selezioniamo semplicemente un singolo valore arbitrario dal set dei subgradienti quando ci troviamo nel punto angoloso (tipicamente assegniamo zero o lo includiamo nei punti in violazione). L'aggiornamento dell'intero dataset richiede di sommare tutti i gradienti dei vincoli violati:
$$ w^{(t+1)} = w^{(t)} - \eta \left( \lambda w^{(t)} - \frac{1}{m}\sum_{i : y_i\langle x_i, w \rangle < 1} y_i x_i \right) $$

> [!intuition] Il collo di bottiglia computazionale (O(md))
> L'algoritmo funziona ed è matematicamente garantito. Tuttavia, a *ogni* singola iterazione di ottimizzazione, dobbiamo valutare la condizione $y_i \langle x_i, w \rangle < 1$ per tutti gli $m$ campioni del dataset. Essendo $w$ e $x_i$ in dimensione $d$, il tempo richiesto per uno step è $\mathcal{O}(m d)$. In contesti di Big Data ($m$ nell'ordine dei milioni, $d$ elevato), un singolo passo di discesa è computazionalmente inaccettabile.

---

## 4. SGD: Stochastic Gradient Descent
Per risolvere il collo di bottiglia temporale $\mathcal{O}(md)$, introduciamo la **Randomizzazione**. 

Invece di usare tutto il dataset per calcolare il vero subgradiente direzionale di $F(w^{(t)})$, usiamo una variabile aleatoria $V_t$ (un vettore) tale che il suo **valore atteso coincida col subgradiente corretto**:
$$ \mathbb{E}[V_t] \in \partial F(w^{(t)}) $$

> [!definition] Passo di aggiornamento dell'algoritmo [[SGD (Stochastic Gradient Descent)]]
> A ogni tempo $t$, l'algoritmo pesca in modo Uniforme Casuale (Uniformly at Random) **un singolo punto** indice $i_t$ dal training set, e calcola il gradiente dell'obiettivo basandosi unicamente su quel singolo esempio.
> L'aggiornamento aleatorio diventa:
> $$ V_t = \lambda w^{(t)} - y_{i_t} x_{i_t} \cdot \mathbb{I}_{\{y_{i_t}\langle x_{i_t}, w^{(t)} \rangle < 1\}} $$
> $$ w^{(t+1)} = w^{(t)} - \eta V_t $$
> Questo approccio stocastico taglia drasticamente il tempo per iterazione a $\mathcal{O}(d)$ mantenendo (in senso probabilistico) le garanzie globali di convergenza e portando l'ottimizzazione dell'ERM ad essere scalabile nei grandi dataset.

# Lezione 9: Forte Convessità (Strong Convexity) e Ottimizzazione Accelerata

## 1. Recap: Il limite della Convessità Semplice
Partendo dal Teorema 4.1, se $F: \mathbb{R}^d \rightarrow \mathbb{R}$ è puramente convessa, il Subgradient Descent (con learning rate costante $\eta$) garantisce un errore limitato da:
$$ F(\bar{w}^{(T)}) - F^* \le \frac{B\rho}{\sqrt{T}} $$
Questo significa che per ottenere un livello di errore (precisione) massimo pari a $\epsilon$, dobbiamo imporre $\frac{1}{\sqrt{T}} \approx \epsilon$, il che implica che il numero di passi necessari esplode quadraticamente: $T = \mathcal{O}(1/\epsilon^2)$. 
È possibile fare meglio? Sì, introducendo un'assunzione geometrica più forte sulla funzione: la **Forte Convessità** ("Convexity on steroids").

---

## 2. La Forte Convessità (Strong Convexity)
> [!definition] Forte Convessità ($\mu$-Strong Convexity)
> Una funzione convessa $F: \mathbb{R}^d \rightarrow \mathbb{R}$ è detta **$\mu$-fortemente convessa** (con $\mu > 0$) se per ogni $w, u \in \mathbb{R}^d$ e per ogni subgradiente $g \in \partial F(w)$ vale:
> $$ F(u) \ge F(w) + \langle g, u-w \rangle + \frac{\mu}{2} ||u-w||^2 $$

> [!intuition] L'interpretazione geometrica
> Nella convessità semplice, il subgradiente forniva un limite inferiore (*lower bound*) lineare (un piano) che restava sempre sotto la funzione. Nella *forte* convessità, la funzione curva così rapidamente verso l'alto che possiamo inserirci sotto non solo un piano, ma un'intera **parabola** (quadratica) descritta dal termine $\frac{\mu}{2}||u-w||^2$. L'ottimo $w^*$ si troverà quindi intrappolato in una "valle" molto stretta, permettendo all'algoritmo di convergere esponenzialmente più in fretta.

**Formulazione equivalente**: Una funzione $F(w)$ è $\mu$-fortemente convessa se e solo se la funzione "depotenziata" $F(w) - \frac{\mu}{2}||w||^2$ risulta ancora matematicamente convessa.

---

## 3. Subgradient Descent per Funzioni Fortemente Convesse
Per sfruttare la curvatura quadratica, l'algoritmo necessita di una modifica cruciale: il *learning rate* $\eta_t$ non deve essere costante, ma deve decadere nel tempo ($Time-varying~learning~rate$).
Scegliamo esplicitamente un decadimento armonico dipendente dal fattore di forte convessità $\mu$:
$$ \eta_t = \frac{1}{\mu t} $$

> [!theorem] Teorema 6.2: Convergenza per Forte Convessità
> Sia $F$ una funzione $\mu$-fortemente convessa con minimizzatore $w^*$, e assumiamo che la norma dei subgradienti calcolati sia limitata: $||g^{(t)}|| \le \rho$. Utilizzando il decadimento $\eta_t = \frac{1}{\mu t}$, l'errore sulla media ordinaria dei pesi decade con rate $\mathcal{O}\left(\frac{\log T}{T}\right)$:
> $$ F(\bar{w}^{(T)}) - F^* \le \frac{\rho^2 (1 + \log T)}{2\mu T} $$

### Dimostrazione Analitica Passo-Passo
La dimostrazione unisce l'identità algebrica di base del gradiente con la nuova disuguaglianza parabolica.

**Passo 1: Identità Algebrica del Passo**
Sviluppando la distanza dall'ottimo al passo successivo usando $||a-b||^2 = ||a||^2 + ||b||^2 - 2\langle a,b \rangle$, otteniamo il prodotto scalare incrociato:
$$ \langle w^{(t)} - w^*, g^{(t)} \rangle = \frac{||w^{(t)} - w^*||^2 - ||w^{(t+1)} - w^*||^2}{2\eta_t} + \frac{\eta_t}{2} ||g^{(t)}||^2 $$

**Passo 2: Iniezione della Forte Convessità**
Applichiamo la definizione di $\mu$-forte convessità scegliendo come punto di ancoraggio $w = w^{(t)}$ e come punto di destinazione $u = w^*$ (il minimo globale):
$$ F(w^*) \ge F(w^{(t)}) + \langle g^{(t)}, w^* - w^{(t)} \rangle + \frac{\mu}{2} ||w^* - w^{(t)}||^2 $$
Riarrangiando i termini per isolare il prodotto scalare (cambiando segno al vettore):
$$ \langle w^{(t)} - w^*, g^{(t)} \rangle \ge F(w^{(t)}) - F(w^*) + \frac{\mu}{2} ||w^{(t)} - w^*||^2 $$

**Passo 3: Fusione delle Disuguaglianze**
Il prodotto scalare (Step 2) è minore o uguale al limite algebrico (Step 1). Li uniamo per isolare la perdita $F(w^{(t)}) - F(w^*)$:
$$ F(w^{(t)}) - F(w^*) + \frac{\mu}{2} ||w^{(t)} - w^*||^2 \le \frac{||w^{(t)} - w^*||^2 - ||w^{(t+1)} - w^*||^2}{2\eta_t} + \frac{\eta_t}{2} ||g^{(t)}||^2 $$
Raccogliamo a fattor comune i termini associati a $||w^{(t)} - w^*||^2$:
$$ F(w^{(t)}) - F(w^*) \le \frac{1}{2} \left[ \left(\frac{1}{\eta_t} - \mu\right) ||w^{(t)} - w^*||^2 - \frac{1}{\eta_t} ||w^{(t+1)} - w^*||^2 \right] + \frac{\eta_t}{2} ||g^{(t)}||^2 $$

**Passo 4: Sostituzione del Learning Rate Decrescente**
Inseriamo esplicitamente il nostro specifico learning rate temporale: $\frac{1}{\eta_t} = \mu t$. Di conseguenza, il coefficiente del primo termine quadratico diventa $(\mu t - \mu) = \mu(t-1)$. La disuguaglianza diventa:
$$ F(w^{(t)}) - F(w^*) \le \frac{\mu}{2} \left[ (t-1) ||w^{(t)} - w^*||^2 - t ||w^{(t+1)} - w^*||^2 \right] + \frac{1}{2\mu t} ||g^{(t)}||^2 $$

**Passo 5: Somma Telescopica**
Valutiamo l'errore medio applicando la convessità standard: $F(\bar{w}^{(T)}) - F^* \le \frac{1}{T} \sum_{t=1}^T \left( F(w^{(t)}) - F^* \right)$.
Sommiamo i termini destri ricavati nel Passo 4. Osserviamo la struttura della sommatoria per il blocco quadratico:
- Per $t=1$: $\frac{\mu}{2} \left[ 0 \cdot ||w^{(1)} - w^*||^2 - 1 \cdot ||w^{(2)} - w^*||^2 \right]$
- Per $t=2$: $\frac{\mu}{2} \left[ 1 \cdot ||w^{(2)} - w^*||^2 - 2 \cdot ||w^{(3)} - w^*||^2 \right]$
- Eccetera.
Si tratta di una **somma telescopica perfetta**. Tutti i termini si annullano a croce, ad eccezione dell'ultimo elemento negativo associato a $T$: $-T||w^{(T+1)} - w^*||^2$. Poiché questo termine è sempre negativo, l'intero blocco quadratico collassa a un valore $\le 0$ e viene eliminato dal limite superiore.

Resta solo la somma dei quadrati dei subgradienti divisa per $\mu t$:
$$ \dots \le \frac{1}{T} \sum_{t=1}^T \frac{1}{2\mu t} ||g^{(t)}||^2 $$
Applicando il bound noto per i subgradienti ($||g^{(t)}|| \le \rho$):
$$ \le \frac{\rho^2}{2\mu T} \sum_{t=1}^T \frac{1}{t} $$

**Passo 6: Approssimazione della Serie Armonica**
L'ultimo passaggio richiede di limitare la somma $\sum_{t=1}^T \frac{1}{t}$.
Geometricamente, possiamo approssimare la somma dei rettangoli di area $\frac{1}{t}$ (la serie armonica) con l'integrale della funzione continua $f(x) = \frac{1}{x}$. 
Isolando il primo rettangolo ($t=1$, area 1) e integrando per la parte rimanente da 1 a $T$:
$$ \sum_{t=1}^T \frac{1}{t} \le 1 + \int_1^T \frac{1}{x} dx = 1 + [\ln(x)]_1^T = 1 + \log T $$
Inserendo questo limite integrale nell'equazione del Passo 5, si ottiene il risultato finale del Teorema:
$$ F(\bar{w}^{(T)}) - F^* \le \frac{\rho^2}{2\mu T} (\log T + 1) $$

> [!intuition] Accelerazione Computazionale
> Questo risultato è profondamente significativo: siamo passati da un rate di convergenza $\mathcal{O}(1/\sqrt{T})$ a un rate (quasi) lineare $\mathcal{O}(\log T / T)$. Ciò garantisce che, per funzioni che presentano una forte curvatura strutturale attorno al minimo, l'algoritmo SGD impiegherà esponenzialmente meno iterazioni per raggiungere l'altissima precisione desiderata.

# Lezione 10: SGD Avanzato, SVM Stocastica e Limiti Teorici

## 1. Analisi di SGD (Stochastic Sub-Gradient Descent)
Nelle lezioni precedenti abbiamo visto che il costo computazionale di calcolare l'esatto subgradiente per la Soft-SVM scala linearmente col numero di campioni: $\mathcal{O}(md)$. Per risolvere questo, utilizziamo lo Stochastic Gradient Descent (SGD).

- **Il Modello Empirico**: La funzione obiettivo dell'ERM (es. Soft-SVM) può sempre essere scritta come somma di un termine di regolarizzazione deterministico $r(w)$ e di un valore medio dei costi su ogni campione $l_i(w)$:
  $$ F(w) = r(w) + \frac{1}{n}\sum_{i=1}^n l_i(w) $$
- **L'Algoritmo SGD**:
  1. Inizializza $w^{(1)} = 0$.
  2. Ad ogni passo $t$, seleziona un indice casuale $i_t$ uniformemente dal dataset $\{1, \dots, n\}$.
  3. Calcola il subgradiente deterministico della regolarizzazione: $q_t \in \partial r(w^{(t)})$.
  4. Calcola il subgradiente della loss stocastica *solo sul punto $i_t$*: $s_t \in \partial l_{i_t}(w^{(t)})$.
  5. Il gradiente stimato diventa $\hat{g}^{(t)} = q_t + s_t$.
  6. Aggiorna i pesi: $w^{(t+1)} = w^{(t)} - \eta_t \hat{g}^{(t)}$.
  7. L'output finale dell'algoritmo è la media dei percorsi: $\bar{w}^{(T)} = \frac{1}{T}\sum_{t=1}^T w^{(t)}$.

> [!intuition] Traiettoria Deterministica vs Stocastica
> Geometricamente, il Gradient Descent puro traccia una curva liscia (GD trajectory) attraversando ortogonalmente le curve di livello fino al minimo. L'SGD introduce un "rumore" di campionamento (SGD trajectory): il percorso oscilla bruscamente attorno alla direzione ideale ("a zig-zag"), ma in valore atteso la "nuvola" dei punti viaggia inesorabilmente verso l'ottimo, e la media dei punti visitati ($\bar{w}^{(T)}$) stabilizza questa varianza convergendo in modo eccellente al minimizzatore $w^*$.

### Garanzie di Convergenza di SGD
Per applicare le garanzie teoriche, dobbiamo verificare che il gradiente stocastico sia in media uguale al gradiente vero condizionato alla storia pregressa ($\mathcal{F}_t$):
$$ \mathbb{E}[\hat{g}^{(t)} | \mathcal{F}_t] \in \partial F(w^{(t)}) $$

> [!theorem] Convergenza di SGD in Convessità e Forte Convessità
> 1. **Convessità semplice (Teorema 5.1)**: Utilizzando un learning rate costante $\eta = B / (\rho \sqrt{T})$, l'errore atteso del modello medio SGD è identico al GD puro:
>    $$ \mathbb{E}[F(\bar{w}^{(T)})] - F(w^*) \le \frac{B\rho}{\sqrt{T}} $$
> 2. **Forte Convessità (Teorema 5.2)**: Utilizzando il decadimento $\eta_t = 1/(\mu t)$, l'errore atteso decresce esponenzialmente più in fretta, riproducendo il limite esatto del modello deterministico:
>    $$ \mathbb{E}[F(\bar{w}^{(T)})] - F(w^*) \le \frac{\rho^2(1 + \log T)}{2\mu T} $$

> [!intuition] Dimostrazione (Legge dell'Aspettativa Totale)
> La prova è analoga al Lemma 14.1. Quando calcoliamo il valore atteso della disuguaglianza del prodotto scalare, per la legge dell'aspettativa totale (Total Expectation Law) possiamo scrivere $\mathbb{E}[V] = \mathbb{E}[\mathbb{E}[V | \mathcal{F}_t]]$. Essendo $w^{(t)}$ e $w^*$ determinati al tempo $t$, l'unico elemento aleatorio del passo isolato è $\hat{g}^{(t)}$. Il suo valore atteso si "smonta" matematicamente restituendo il gradiente teorico $\nabla F(w^{(t)})$, permettendo al Lemma base di funzionare senza intoppi, giustificando il limite in valore atteso finale.

---

## 2. Applicazione Teorica Piena: Soft-SVM con SGD
Il framework teorico si adatta perfettamente alla Soft-SVM. Dobbiamo solo dimostrare formalmente che soddisfa l'assunzione di forte convessità.

> [!theorem] Forte Convessità della Soft-SVM
> La funzione di costo della Soft-SVM $F(w) = \frac{\lambda}{2}||w||^2 + \frac{1}{m}\sum l_i(w)$ è fortemente convessa con $\mu = \lambda$.

**Dimostrazione analitica:**
Utilizziamo l'identità interpolante per $z = (1-\alpha)w + \alpha u$ (con $\alpha \in [0,1]$): 
$$ ||z||^2 = (1-\alpha)||w||^2 + \alpha ||u||^2 - \alpha(1-\alpha)||u-w||^2 $$.
Sostituendo $z$ nella funzione obiettivo (Step 1):
$$ F(z) = \frac{\lambda}{2} \left[ (1-\alpha)||w||^2 + \alpha ||u||^2 - \alpha(1-\alpha)||u-w||^2 \right] + \frac{1}{m}\sum l_i(z) $$
Per la convessità della Hinge Loss, $\sum l_i(z) \le (1-\alpha)\sum l_i(w) + \alpha \sum l_i(u)$. Ricomponendo i termini, otteniamo:
$$ F(z) \le (1-\alpha)F(w) + \alpha F(u) - \frac{\lambda}{2}\alpha(1-\alpha)||u-w||^2 $$
Inoltre (Step 2), per la definizione geometrica del subgradiente, un gradiente valutato in $w$ deve rimanere sempre sotto la curva e quindi al di sotto del punto interpolato $z$:
$$ F(z) \ge F(w) + \langle g, z-w \rangle = F(w) + \alpha \langle g, u-w \rangle $$
Unendo le due disuguaglianze (Step 3) ed eliminando la divisione per $\alpha$ al limite $\alpha \to 0$:
$$ F(w) + \alpha \langle g, u-w \rangle \le (1-\alpha)F(w) + \alpha F(u) - \alpha(1-\alpha)\frac{\lambda}{2}||u-w||^2 $$
$$ F(u) \ge F(w) + \langle g, u-w \rangle + (1-\alpha)\frac{\lambda}{2}||u-w||^2 $$
Impostando $\alpha \to 0$, si ottiene l'esatta equazione di definizione della $\mu$-forte convessità con $\mu = \lambda$.

---

## 3. Limiti Intrinseci di Ottimo, Stati e Direzione (Lemma 6.2)
Per fissare la costanti dei tassi di convergenza ($B$ e $\rho$), abbiamo bisogno di limitare quanto possono crescere i pesi e i gradienti, condizionati alla sola ampiezza del dataset $R = \max ||x_i||$.

> [!theorem] Bounding the SVM Variables (Lemma 6.2)
> Data la Soft-SVM con fattore $\lambda$ e ampiezza dati massima $R$:
> 1. Il modulo dell'ottimo è limitato: $||w^*|| \le R / \lambda$ (cioè $B = R / \lambda$).
> 2. Se impostiamo $\eta_t \le 1/\lambda$, il modulo dei pesi in ogni iterazione è limitato: $||w^{(t)}|| \le R / \lambda$.
> 3. La magnitudine dei subgradienti calcolati è limitata: $||g^{(t)}|| \le 2R$ (cioè $\rho = 2R$).

### Derivazioni
* **Primo Limite ($||w^*||$)**: Partiamo dalla disuguaglianza $F(w^*(1-\alpha)) - F(w^*) \ge 0$ (poiché l'ottimo è un minimo assoluto per qualsiasi variazione $\alpha$). 
Usando la sub-additività dell'operatore $(\cdot)_+$ e l'identità di disuguaglianza di Cauchy-Schwarz $| \langle a, b \rangle | \le ||a|| \cdot ||b||$, il termine di variazione del subgradiente (loss) viene limitato superiormente da $\alpha R ||w^*||$.
Semplificando l'algebra per $\alpha \to 0$, resta $0 \le -\frac{1}{2}\lambda||w^*||^2 + R||w^*||$, che ridotto dà direttamente $||w^*|| \le R/\lambda$.

* **Secondo Limite ($||w^{(t)}||$)**: Si procede per Induzione. $w^{(1)} = 0 \le R/\lambda$ (Base ok). Applichiamo l'aggiornamento (Step induttivo): 
$$ w^{(t+1)} = (1 - \lambda \eta_t)w^{(t)} + \eta_t y_{i_t} x_{i_t} \cdot \mathbb{I}_{\{y \langle x,w \rangle < 1\}} $$
Applicando la disuguaglianza triangolare e assumendo $\eta_t \le 1/\lambda$ per garantire positività del termine $(1-\lambda\eta_t)$ e assumendo l'ipotesi induttiva $||w^{(t)}|| \le R/\lambda$:
$$ ||w^{(t+1)}|| \le (1-\lambda\eta_t)\frac{R}{\lambda} + \eta_t R = \frac{R}{\lambda} - \eta_t R + \eta_t R = \frac{R}{\lambda} $$

* **Terzo Limite ($||g^{(t)}||$)**: Partiamo dalla definizione di $\hat{g}^{(t)} = \lambda w^{(t)} - y_{i_t} x_{i_t} \cdot \mathbb{I}$.
Per la disuguaglianza triangolare e usando il secondo limite appena ricavato:
$$ ||\hat{g}^{(t)}|| \le \lambda ||w^{(t)}|| + ||x_{i_t}|| \le \lambda \frac{R}{\lambda} + R = 2R $$

**Conclusione - Corollario 6.3**: Sostituendo $B=R/\lambda$ e $\rho=2R$ nelle equazioni classiche dell'SGD e ponendo i learning rates ottimali dedotti teoricamente, l'algoritmo SGD e l'algoritmo FD (Full Descent) deterministico per la Soft SVM collassano sullo **stesso identico bound finale**:
$$ \eta_t = \frac{1}{\lambda t} \implies \mathbb{E}[F(\bar{w}^{(T)})] - F(w^*) \le \frac{2R^2(1 + \log T)}{\lambda T} $$
Questo chiude il cerchio dimostrando la supremazia dell'ottimizzazione stocastica su larga scala ("No difference between the stochastic and full rates").

# Lezione 11: Regressione Lineare e Ordinary Least Squares (OLS)

## 1. Il Problema della Regressione Lineare
Finora ci siamo concentrati sulla classificazione (label discrete). Ora passiamo al problema di predire valori continui.

- **Cosa facciamo**: Il dominio delle features è $\mathcal{X} = \mathbb{R}^d$ e lo spazio dei target è continuo $\mathcal{Y} = \mathbb{R}$. Cerchiamo un predittore all'interno della famiglia delle funzioni lineari:
  $$ \mathcal{H} = \{ f_\theta : \mathbb{R}^d \rightarrow \mathbb{R} \mid f_\theta(x) = \langle \theta, x \rangle, \text{ per } \theta \in \mathbb{R}^d \} $$
- **Funzione di Loss**: Sostituiamo la 0-1 loss con la **Squared Loss** (Loss Quadratica):
  $$ l(f(x), y) = (f(x) - y)^2 $$

L'obiettivo dell'apprendimento diventa la minimizzazione dell'[[Empirical Risk]] (Errore Quadratico Medio Empirico o MSE):
$$ \hat{R}_S(f_\theta) = \frac{1}{n}\sum_{i=1}^n (f_\theta(x_i) - y_i)^2 $$

---

## 2. Derivazione in 1D: La Retta di Regressione Affine
Per costruire l'intuizione, risolviamo analiticamente il problema per $d=1$ con funzioni affini (una retta nel piano cartesiano).

> [!example] Regressione Lineare Semplice (1D)
> Il modello è $f(x) = mx + q$. L'errore empirico sui campioni è:
> $$ \hat{R}_S(m,q) = \frac{1}{n} \sum_{i=1}^n (m x_i + q - y_i)^2 $$
> Poiché $\hat{R}_S$ è una funzione quadratica (quindi convessa) nei parametri $m$ e $q$, e limitata inferiormente da 0, il suo minimo globale si trova dove il gradiente è nullo.

**Calcolo dell'Intercetta ($q$)**:
Poniamo la derivata parziale rispetto a $q$ uguale a 0:
$$ \frac{\partial \hat{R}_S}{\partial q} = \frac{1}{n} \sum_{i=1}^n 2(m x_i + q - y_i) = 0 $$
Definendo le medie campionarie $\bar{x} = \frac{1}{n}\sum x_i$ e $\bar{y} = \frac{1}{n}\sum y_i$, ed espandendo la sommatoria:
$$ q = \frac{1}{n}\sum_{i=1}^n (y_i - m x_i) \implies q = \bar{y} - m\bar{x} $$

**Calcolo della Pendenza ($m$)**:
Poniamo la derivata parziale rispetto a $m$ uguale a 0 e sostituiamo $q = \bar{y} - m\bar{x}$:
$$ \frac{\partial \hat{R}_S}{\partial m} = \frac{1}{n} \sum_{i=1}^n 2x_i(m x_i + q - y_i) = 0 $$
$$ \sum_{i=1}^n x_i (m x_i - m\bar{x} + \bar{y} - y_i) = 0 $$
Isoliamo i termini con $m$:
$$ m \sum_{i=1}^n x_i (x_i - \bar{x}) = \sum_{i=1}^n x_i (y_i - \bar{y}) $$
Sfruttando le proprietà delle medie ($\sum(x_i - \bar{x}) = 0$), possiamo sottrarre $\bar{x}$ dentro la sommatoria di sinistra senza alterare il risultato, ottenendo:
$$ m = \frac{\sum_{i=1}^n (x_i - \bar{x})(y_i - \bar{y})}{\sum_{i=1}^n (x_i - \bar{x})^2} $$
Questa è esattamente la formula che esprime $m$ come il rapporto tra la Covarianza Empirica di $X$ e $Y$ diviso la Varianza Empirica di $X$.

*(Nota sulle eccezioni: se tutti gli $x_i$ sono identici, la varianza è zero e la pendenza $m$ è indefinita. In tal caso, il modello degenera costringendoci a porre $m=0$ e $q = \bar{y}$).*

---

## 3. Generalizzazione in $\mathbb{R}^d$ e lo stimatore OLS
Estendiamo il problema a $d$ dimensioni. Per farlo in modo pulito, passiamo alla notazione vettoriale e matriciale.

> [!definition] Design Matrix
> Dato un dataset di $n$ campioni, definiamo la Design Matrix $X \in \mathbb{R}^{n \times d}$, dove ogni riga è la trasposta di un vettore di feature $x_i^T$. Definiamo inoltre il vettore colonna delle label $y \in \mathbb{R}^n$.
> In questa forma compatta, le previsioni per tutti i punti sono date dal prodotto matrice-vettore $X\theta$[cite: 100].

Il Rischio Empirico si riscrive come norma quadratica del vettore residuo[cite: 100, 101]:
$$ \hat{R}_S(\theta) = \frac{1}{n} ||y - X\theta||^2 = \frac{1}{n} \langle y - X\theta, y - X\theta \rangle $$

### Derivazione dell'Equazione Normale
Espandiamo il prodotto scalare per isolare $\theta$[cite: 101]:
$$ \hat{R}_S(\theta) = \frac{1}{n} [||y||^2 + ||X\theta||^2 - 2y^T X\theta] $$
Calcoliamo il gradiente rispetto al vettore $\theta$ e lo poniamo uguale a 0[cite: 101]:
$$ \nabla \hat{R}_S(\theta) = \frac{2}{n} [X^T X\theta - X^T y] = 0 $$
$$ X^T X\theta = X^T y $$
Questa è l'**Equazione Normale**[cite: 101]. Se la matrice quadrata $X^T X$ (di dimensione $d \times d$) è invertibile, possiamo isolare $\theta$ e trovare la soluzione chiusa[cite: 101].

> [!theorem] Ordinary Least Squares (OLS) Estimator
> Assumendo che $X^T X$ sia invertibile (il che richiede che le features siano linearmente indipendenti e $n \ge d$), lo stimatore ottimale è[cite: 101]:
> $$ \hat{\theta} = (X^T X)^{-1} X^T y $$

> [!intuition] Perché è importante?
> A differenza del Perceptron, del Gradient Descent o della Soft-SVM, la Regressione Lineare con MSE non richiede cicli iterativi: se la matrice è invertibile, la soluzione ottima globale si calcola istantaneamente in "one-shot" con un'operazione di algebra lineare[cite: 101].

---

## 4. Analisi Statistica: La Linear Assumption
Abbiamo trovato l'ERM. Ma per valutare quanto sia "buono" teoricamente, dobbiamo fare un'assunzione sul processo generativo reale dei dati (il Predittore di Bayes).

> [!definition] Linear Assumption
> Diciamo che la vera distribuzione incognita $\mathcal{D}$ soddisfa l'**Assunzione Lineare** se esiste un vettore "perfetto" $\theta^* \in \mathbb{R}^d$ tale che[cite: 102]:
> $$ Y = X^T \theta^* + \epsilon $$
> dove $\epsilon$ è una variabile aleatoria di rumore, indipendente da $X$, con media nulla ($\mathbb{E}[\epsilon]=0$) e varianza finita ($\mathbb{E}[\epsilon^2] = \sigma^2 < \infty$)[cite: 102].

> [!theorem] Predittore Ottimo Sotto Assunzione Lineare
> Il predittore ottimo in assoluto per la loss quadratica, che minimizza il rischio atteso condizionato $X=x$, è proprio il valore atteso condizionato $\mathbb{E}[Y | X=x]$[cite: 102].
> *Dimostrazione:* Troviamo il minimo di $g(z) = \mathbb{E}[(z-Y)^2 | X=x]$. Derivando e ponendo a 0 otteniamo $g'(z) = \mathbb{E}[2(z-Y) | X=x] = 0 \implies z = \mathbb{E}[Y | X=x]$[cite: 102].
> Inserendo l'Assunzione Lineare, otteniamo:
> $$ \mathbb{E}[Y | X=x] = \mathbb{E}[X^T \theta^* + \epsilon | X=x] = X^T \theta^* + \mathbb{E}[\epsilon | X=x] $$
> Poiché $\epsilon$ ha media 0 ed è indipendente, il termine di rumore si annulla, lasciando esattamente il modello lineare $X^T \theta^*$ come target teorico insuperabile[cite: 102].

### Correttezza dello Stimatore OLS ($\mathbb{E}[\hat{\theta}] = \theta^*$)
Verifichiamo analiticamente se il nostro algoritmo empirico "scopre" il vero vettore $\theta^*$[cite: 103].

> [!theorem] Non distorsione (Unbiasedness) di OLS
> Sotto la Linear Assumption, il valore atteso del nostro stimatore $\hat{\theta}$ coincide esattamente con i pesi reali generativi: $\mathbb{E}[\hat{\theta}] = \theta^*$[cite: 103].

> [!intuition] Dimostrazione (Algebra delle Aspettative)
> Sostituiamo i dati reali $y = X\theta^* + \epsilon$ (in notazione vettoriale) nella formula dello stimatore OLS[cite: 103]. Calcoliamo il valore atteso condizionato alla conoscenza di $X$ (cioè $x_1, \dots, x_n$ sono costanti note)[cite: 103]:
> $$ \mathbb{E}[\hat{\theta} | X] = \mathbb{E}[(X^T X)^{-1} X^T y | X] $$
> $$ = \mathbb{E}[(X^T X)^{-1} X^T (X\theta^* + \epsilon) | X] $$
> Distribuiamo la moltiplicazione matriciale[cite: 103]:
> $$ = \mathbb{E}[\underbrace{(X^T X)^{-1} (X^T X)}_{=I} \theta^* | X] + \mathbb{E}[(X^T X)^{-1} X^T \epsilon | X] $$
> Poiché $X$ è considerato costante in questo valore atteso condizionato, possiamo estrarlo dal secondo termine[cite: 103]:
> $$ = \theta^* + (X^T X)^{-1} X^T \mathbb{E}[\epsilon | X] $$
> Poiché $\epsilon$ ha media zero indipendente da $X$, l'ultimo termine si annulla ($\mathbb{E}[\epsilon|X]=0$)[cite: 103]. Ne consegue che $\mathbb{E}[\hat{\theta} | X] = \theta^*$[cite: 103].
> Infine, applicando la Total Expectation Law, estraiamo l'aspettativa totale rispetto a $X$, ottenendo $\mathbb{E}[\hat{\theta}] = \theta^*$, dimostrando che OLS non ha bias sistematici[cite: 103].