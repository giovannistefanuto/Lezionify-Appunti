# Stima della Posa 3D e Problema Perspective-n-Point (PnP) nel Controllo Visivo

## Overview Didattica

In questa lezione viene affrontato il problema della stima della posa tridimensionale (*Pose Estimation*) di un oggetto rispetto a una telecamera montata su un manipolatore robotico in configurazione *eye-in-hand* (telecamera solidale all'effettore finale, o *End-Effector*). L'obiettivo primario è mostrare come le informazioni visive estratte da immagini 2D possano essere utilizzate per ricostruire la struttura spaziale 3D, permettendo la transizione dal controllo a livello dei giunti (*Joint Level Control*) al controllo in spazio cartesiano (*Cartesian Level Control*).

I temi principali trattati comprendono:
* **Relazioni Cinematiche e Vincoli di Posa**: Dimostrazione di come, ipotizzando sia la telecamera solidale all'effettore finale sia l'oggetto statico rispetto al telaio di base (*Base Frame*), la determinazione della posa relativa tra telecamera e oggetto consenta di ricavare direttamente la posa dell'effettore finale rispetto alla base del robot.
* **Formulazione del Problema Perspective-n-Point (PnP)**: Modellizzazione analitica del problema $PnP$, il cui scopo è stimare la matrice di trasformazione omogenea $T_O^C$ (comprendente la rotazione $R_O^C$ e la traslazione $t_O^C$) a partire dalle coordinate 3D note di $n$ punti sul sistema di riferimento dell'oggetto e dalle relative coordinate normalizzate dell'immagine (*Normalized Image Coordinates*).
* **Condizioni Geometriche e Molteplicità delle Soluzioni**: Analisi dell'impatto della disposizione spaziale dei punti sul numero di soluzioni del problema $PnP$ (ad es. il caso $P3P$ con 3 punti non allineati che ammette 4 soluzioni, e le condizioni di unicità per 4 o più punti complanari).
* **Introduzione allo Jacobiano di Feature**: Introduzione al concetto di matrice di interazione o Jacobiano delle Feature (*Feature Jacobian / Interaction Matrix*), strumento che lega la velocità della telecamera nello spazio cartesiano alle variazioni nel tempo dei punti nell'immagine.

---

### Formulazione del Problema PnP e Stima della Posa

#### Contestualizzazione: Stima della Posa per il Controllo Visivo

Riprendiamo il quadro generale: abbiamo un manipolatore robotico su cui è montata una telecamera in configurazione *eye-in-hand* (ossia solidale all'effetto finale o *end-effector*). Durante il movimento, la telecamera acquisisce continuamente immagini dell'ambiente circostante. L'obiettivo è utilizzare esclusivamente queste informazioni visive per guidare il robot da una posizione iniziale a una configurazione finale desiderata.

Una prima strategia consiste nell'utilizzare le immagini per ricostruire la **Stima della Posa** (*Pose Estimation*) relativa tra l'effetto finale e la base del robot. 

La catena cinematica del sistema si basa su alcune relazioni geometriche fondamentali:
1. **Telecamera - End-Effector**: La telecamera è fissata in modo rigido all'end-effector. Pertanto, la posa relativa tra il riferimento telecamera e il riferimento end-effector è costante e nota a priori.
2. **Oggetto - Base**: L'oggetto osservato e la base del robot sono entrambi fermi nell'ambiente. Di conseguenza, la posa relativa tra il riferimento oggetto e il riferimento base è costante e nota.
3. **Telecamera - Oggetto**: È la variabile incognita che varia con il movimento del robot. 

Se siamo in grado di stimare la posa relativa tra la telecamera e l'oggetto osservato, possiamo ricostruire direttamente la posa dell'end-effector rispetto alla base mediante semplici composizioni di trasformazioni omogenee.

---

#### Formulazione Matematica del Problema

Consideriamo un oggetto nell'ambiente su cui sono identificati $n$ punti di interesse (*feature points*). 

Le informazioni a nostra disposizione sono:
* **Coordinate 3D note**: Le coordinate dei punti rispetto al sistema di riferimento dell'oggetto $\mathbf{r}_i^O = [x_i^O, y_i^O, z_i^O]^T$ (con $i = 1, \dots, n$), note a priori dalla geometria dell'oggetto.
* **Coordinate 2D misurate**: Le posizioni degli stessi punti misurate sul piano immagine della telecamera in coordinate pixel, da cui otteniamo le **Coordinate Normalizzate** (*Normalized Coordinates*) invertendo la matrice dei **Parametri Intrinseci** (*Intrinsic Parameters*) $\mathbf{K}$:

$$\tilde{\mathbf{s}}_i = \mathbf{K}^{-1} \begin{bmatrix} u_i \\ v_i \\ 1 \end{bmatrix}$$

La relazione proiettiva che lega i punti 3D dell'oggetto alla loro proiezioni sul piano immagine in **Coordinate Omogenee** (*Homogeneous Coordinates*) è data da:

$$\lambda_i \tilde{\mathbf{s}}_i = \mathbf{\Pi} \mathbf{T}_O^C \tilde{\mathbf{r}}_i^O$$

Dove:
* $\tilde{\mathbf{s}}_i = [u_i, v_i, 1]^T$ è il vettore delle coordinate normalizzate omogenee del punto $i$-esimo nell'immagine.
* $\tilde{\mathbf{r}}_i^O = [x_i^O, y_i^O, z_i^O, 1]^T$ è la rappresentazione omogenea del punto $i$-esimo nel riferimento oggetto.
* $\lambda_i$ è un fattore di scala incognito (profondità del punto).
* $\mathbf{\Pi} = \begin{bmatrix} \mathbf{I}_{3\times3} & \mathbf{0}_{3\times1} \end{bmatrix}$ è la matrice di proiezione canonica.
* $\mathbf{T}_O^C \in SE(3)$ è la matrice di trasformazione omogenea incognita che descrive la posa del riferimento oggetto rispetto al riferimento telecamera:

$$\mathbf{T}_O^C = \begin{bmatrix} \mathbf{R}_O^C & \mathbf{t}_O^C \\ \mathbf{0}_{1\times3} & 1 \end{bmatrix}$$

L'obiettivo algebrico consiste nel ricavare gli elementi della matrice $\mathbf{T}_O^C$ (composta dalla matrice di rotazione $\mathbf{R}_O^C \in SO(3)$ e dal vettore di traslazione $\mathbf{t}_O^C \in \mathbb{R}^3$) a partire da un insieme di $n$ equazioni di proiezione.

---

#### Il Problema Perspective-n-Point (PnP)

> **CONCETTO CHIAVE: Il Problema Perspective-n-Point (PnP)**
> Il problema di stima della posa a partire da $n$ corrispondenze tra punti 3D e le rispettive proiezioni 2D è noto in letteratura come **Problema Perspective-n-Point (PnP)**. L'unicità e il numero di soluzioni ammissibili dipendono strettamente dal numero di punti $n$ e dalla configurazione geometrica dei punti stessi.

Le soluzioni al problema PnP variano in base alle seguenti condizioni geometriche:

* **$n = 3$ punti (P3P non collineari)**: Il problema ammette fino a **4 soluzioni matematicamente distinte**.
* **$n = 4$ o $n = 5$ punti (non collineari generici)**: Si ottengono in genere almeno **2 soluzioni**.
* **$n = 4$ punti complanari (con vincolo di non-collinearità)**: Se i 4 punti giacciono tutti sullo stesso piano e nessuna tripla di punti è collineare (ovvero nessun gruppo di 3 punti giace sulla stessa retta), **la soluzione è unica**.
* **$n \ge 6$ punti (non complanari)**: Con almeno 6 punti in configurazione spaziale generica non complanare, **la soluzione è unica** e può essere ricavata analiticamente.

---

#### Semplificazione Analitica: Assunzione di Complanarità ($z = 0$)

Per ricavare una soluzione analitica elegante, ci concentreremo sul caso di **4 punti complanari**. 

Senza perdita di generalità, possiamo posizionare il sistema di riferimento dell'oggetto $\mathcal{F}_O$ con l'origine sul piano contenente i punti e l'asse $Z_O$ ortogonale ad esso. Di conseguenza, la terza coordinata di tutti i punti dell'oggetto annulla il proprio valore:

$$z_i^O = 0, \quad \forall i = 1, \dots, 4$$

I vettori posizione dei punti nel riferimento oggetto si riducono quindi a:

$$\tilde{\mathbf{r}}_i^O = \begin{bmatrix} x_i^O \\ y_i^O \\ 0 \\ 1 \end{bmatrix}$$

*Nota*: Se i punti non giacessero originariamente sul piano $z=0$, si tratterebbe semplicemente di applicare una preventiva rototraslazione al sistema di riferimento dell'oggetto per ricadere esattamente in questo modello algebrico semplificato. Questa assunzione semplifica notevolmente lo sviluppo analitico dei passaggi successivi.

---

### Soluzione Analitica e Linearizzazione del Problema PnP

---

#### 1. Formulazione del Problema e Semplificazione Planare

Per risolvere analiticamente il problema del *Perspective-n-Point* (PnP), l'obiettivo primario è convertire un set di relazioni di progetto non lineari in un sistema lineare di equazioni della forma classica:

$$A x = 0$$

L'algebra lineare ci fornisce infatti strumenti estremamente efficienti per risolvere sistemi di questo tipo.

##### Utilizzo della Matrice Antisimmetrica
La prima operazione consiste nel pre-moltiplicare le relazioni geometriche per una *Matrice Antisimmetrica (Skew-Symmetric Matrix)*, denotata con $[s]_\times$ o $S$, costruita a partire da un vettore tridimensionale. 

Ricordiamo che per un generico vettore $a = [a_x, a_y, a_z]^T \in \mathbb{R}^3$, la corrispondente matrice antisimmetrica $[a]_\times \in \mathbb{R}^{3 \times 3}$ è definita come:

$$[a]_\times = \begin{bmatrix} 0 & -a_z & a_y \\ a_z & 0 & -a_x \\ -a_y & a_x & 0 \end{bmatrix}$$

Una proprietà fondamentale sfruttata in questo passaggio è l'effetto del prodotto vettoriale di un vettore con se stesso: per qualsiasi vettore $\tilde{s}_i$, il prodotto vettoriale è nullo, ovvero:

$$[\tilde{s}_i]_\times \tilde{s}_i = 0$$

Questa identità permette di azzerare e semplificare notevolmente i termini all'interno delle equazioni.

##### Ipotesi di Planarità dei Punti del Modello
Assumiamo che tutti i punti di riferimento appartenenti al modello giacciano su un piano. Senza perdita di generalità, possiamo impostare la terza coordinata di ciascun punto pari a zero ($r_{i,z} = 0$). 

In coordinate omogenee, il vettore posizione del punto $i$-esimo diventa:

$$\tilde{r}_i = \begin{bmatrix} r_{i,x} \\ r_{i,y} \\ 0 \\ 1 \end{bmatrix}$$

Moltiplicando questo vettore per la matrice di trasformazione (composta dalla matrice di rotazione $R = [r_1 \quad r_2 \quad r_3]$ e dal vettore di traslazione $t$), la terza colonna della matrice di rotazione $r_3$ viene annullata dal valore $r_{i,z} = 0$.

Otteniamo così una matrice ridotta $H \in \mathbb{R}^{3 \times 3}$, nota in Visione Artificiale come *Matrice di Omografia Planare (Planar Homography Matrix)*:

$$H = \begin{bmatrix} r_1 & r_2 & t \end{bmatrix}$$

dove:
* $r_1 \in \mathbb{R}^3$ è la prima colonna della matrice di rotazione;
* $r_2 \in \mathbb{R}^3$ è la seconda colonna della matrice di rotazione;
* $t \in \mathbb{R}^3$ è il vettore di traslazione $o_C$.

I punti sul piano possono quindi essere espressi in un sistema di coordinate ridotto $2\text{D}$ omogeneo:

$$\tilde{r}_i^{2D} = \begin{bmatrix} r_{i,x} \\ r_{i,y} \\ 1 \end{bmatrix}$$

> **Nota:** La perdita temporanea della terza colonna $r_3$ non è un problema: poiché $R$ è una matrice di rotazione ortogonale, la terza colonna potrà essere recuperata in un secondo momento mediante il prodotto vettoriale (*cross product*) delle prime due: $r_3 = r_1 \times r_2$.

---

#### 2. Linearizzazione tramite Vettorizzazione e Prodotto di Kronecker

Per trasformare la relazione matriciale in un problema lineare $A x = 0$, utilizziamo l'operatore di vettorizzazione e il prodotto di Kronecker.

##### Operatore di Vettorizzazione
L'operatore di *Vettorizzazione (Vectorization Map)*, denotato con $\text{vec}(A)$, prende una matrice $A \in \mathbb{R}^{m \times n}$ e la converte in un vettore colonna ordinando le sue colonne una sotto l'altra:

$$\text{se } A = \begin{bmatrix} a_1 & a_2 & \dots & a_n \end{bmatrix} \implies \text{vec}(A) = \begin{bmatrix} a_1 \\ a_2 \\ \vdots \\ a_n \end{bmatrix} \in \mathbb{R}^{mn \times 1}$$

##### Prodotto di Kronecker
Dati due matrici $A \in \mathbb{R}^{m \times n}$ e $B \in \mathbb{R}^{p \times q}$, il *Prodotto di Kronecker (Kronecker Product)* $A \otimes B \in \mathbb{R}^{mp \times nq}$ è una matrice a blocchi definita come:

$$A \otimes B = \begin{bmatrix} a_{11} B & a_{12} B & \dots & a_{1n} B \\ a_{21} B & a_{22} B & \dots & a_{2n} B \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} B & a_{m2} B & \dots & a_{mn} B \end{bmatrix}$$

##### Proprietà Fondamentale della Vettorizzazione
Sfruttiamo la seguente identità algebrica per il prodotto di tre matrici $A$, $B$ e $C$:

$$\text{vec}(A B C) = (C^T \otimes A) \, \text{vec}(B)$$

*Nota: Passaggio integrato con chiarezza didattica.*

Nel nostro problema, l'equazione per il punto $i$-esimo è espressa da:

$$S_i H \tilde{r}_i = 0$$

Applicando l'operatore di vettorizzazione a entrambi i membri e ponendo $A = S_i$, $B = H$ e $C = \tilde{r}_i$ (dove $\tilde{r}_i$ è trattato come una matrice con una singola colonna):

$$\text{vec}(S_i H \tilde{r}_i) = (\tilde{r}_i^T \otimes S_i) \, \text{vec}(H) = 0$$

Definiamo:
1. Il vettore delle incognite $h \in \mathbb{R}^{9 \times 1}$ come la vettorizzazione della matrice di omografia $H$:
   $$h = \text{vec}(H) = \begin{bmatrix} r_1 \\ r_2 \\ t \end{bmatrix}$$
2. La matrice dei coefficienti noti per il singolo punto $A_i \in \mathbb{R}^{3 \times 9}$:
   $$A_i = \tilde{r}_i^T \otimes S_i = \begin{bmatrix} r_{i,x} S_i & r_{i,y} S_i & S_i \end{bmatrix}$$

L'equazione algebrica per il singolo punto $i$ diventa dunque:

$$A_i h = 0$$

---

#### 3. Costruzione del Sistema Completo e Analisi del Rango

Con un solo punto $i$, otteniamo una matrice $A_i$ di dimensione $3 \times 9$. Tuttavia, la matrice antisimmetrica $S_i$ ha rango pari a 2 ($\text{rank}(S_i) = 2$). Di conseguenza, anche il rango di ciascun blocco è:

$$\text{rank}(A_i) = 2$$

Un singolo punto fornisce quindi solo 2 equazioni linearmente indipendenti a fronte di 9 incognite.

```
+--------------------------------------------------------------------------+
|                            CONCETTO CHIAVE                               |
| Per risolvere il sistema per le 9 incognite contenute nel vettore h,     |
| è necessario considerare un set di almeno 4 punti (Problema P4P).         |
+--------------------------------------------------------------------------+
```

Impilando le equazioni per 4 punti distinti ($i = 1, 2, 3, 4$), si costruisce il sistema lineare complessivo $A h = 0$:

$$A = \begin{bmatrix} A_1 \\ A_2 \\ A_3 \\ A_4 \end{bmatrix} \in \mathbb{R}^{12 \times 9}$$

##### Analisi del Rango del Sistema
* Ciascuno dei 4 punti contribuisce con un blocco di rango 2, portando il rango massimo teorico della matrice $A$ a $4 \times 2 = 8$.
* **Ipotesi di non-collinearità:** Affinché i 4 blocchi siano reciprocamente indipendenti e garantiscano $\text{rank}(A) = 8$, **nessuna tripletta dei 4 punti deve essere allineata (collineare)**.

Se la condizione di non-collinearità è rispettata:

$$\text{rank}(A) = 8$$

Poiché il numero di incognite è 9 e il rango è 8, la teoria dell'algebra lineare garantisce l'esistenza di uno spazio nullo (*Nullspace*) di dimensione 1. La soluzione $h$ è determinata a meno di un fattore di scala incognito $c \in \mathbb{R}$:

$$h = c \cdot h_0$$

dove $h_0$ è una qualsiasi soluzione non banale dello spazio nullo di $A$.

---

#### 4. Ripristino della Scala e Ricostruzione della Rotazione

Per determinare il valore corretto del fattore di scala $c$, applichiamo i vincoli geometrici propri di una matrice di rotazione.

##### Determinazione del Fattore di Scala
Sapendo che i vettori colonna $r_1$ ed $r_2$ appartengono a una matrice di rotazione $R \in SO(3)$, la loro norma euclidea deve essere unitaria:

$$\|r_1\|_2 = 1 \quad \text{e} \quad \|r_2\|_2 = 1$$

Estratti i vettori stimati $\hat{r}_1$ ed $\hat{r}_2$ dal vettore soluzione $h_0$, si calcola il fattore di scala $c$ come:

$$c = \frac{1}{\|\hat{r}_1\|_2}$$

Moltiplicando l'intero vettore $h_0$ per $c$, si ottengono i vettori di rotazione scalati correttamente $r_1, r_2$ e il vettore traslazione $t$.

##### Ricostruzione Completa della Matrice di Rotazione
Con le prime due colonne $r_1$ ed $r_2$ note e correttamente scalate, la terza colonna della matrice di rotazione $R$ viene calcolata mediante il prodotto vettoriale:

$$r_3 = r_1 \times r_2$$

Ricomponendo la matrice di rotazione completa:

$$R = \begin{bmatrix} r_1 & r_2 & r_3 \end{bmatrix}$$

---

#### 5. Vantaggi Computazionali della Soluzione Analitica

L'approccio analitico descritto presenta notevoli vantaggi operativi:

* **Forma Chiusa (Closed-form):** Non richiede algoritmi di ottimizzazione iterativi o tentativi iniziali (*initial guess*).
* **Efficienza in Tempo Reale:** La risoluzione di un sistema lineare $12 \times 9$ tramite decomposizione SVD (*Singular Value Decomposition*) o calcolo di autovalori è un'operazione computazionalmente leggera e deterministica.
* **Applicazioni:** È ideale per sistemi robotici ad alte prestazioni e per la stima della posa in tempo reale ad alto numero di fotogrammi al secondo (*high frame-rate*).

---

### Gestione del Rumore e Outlier nella Visione Artificiale

In un ambiente di laboratorio o in contesto industriale strutturato, è comune e realistico assumere di conoscere la posizione esatta di determinati punti nello spazio, spesso identificati tramite marcatori (*markers*). In contesti non strutturati (come un drone in volo all'aperto), questa assunzione cade ed è necessario ricorrere a metodi più complessi. Tuttavia, sfruttare la struttura geometrica del problema consente di ottenere soluzioni eleganti mediante tecniche come la **Trasformazione Lineare Diretta** (*Direct Linear Transformation - DLT*).

#### Il Modello di Rumore Standard vs. Visione Artificiale

In ingegneria della misurazione e nell'elaborazione dei segnali, una singola misura $y$ di una grandezza incognita $x$ viene tipicamente modellata con un **Rumore Gaussiano Additivo** (*Additive Gaussian Noise*):

$$y = x + n, \quad n \sim \mathcal{N}(0, \sigma^2)$$

Per ridurre la varianza della stima dell'incognita $x$, la strategia classica consiste nell'effettuare $N$ misurazioni indipendenti $y_i$ e calcolarne la media aritmetica:

$$\bar{x} = \frac{1}{N} \sum_{i=1}^{N} y_i$$

Per il Teorema del Limite Centrale, al tendere di $N$ all'infinito ($N \to \infty$), la media campionaria $\bar{x}$ converge esattamente al valore vero $x$.

#### Il Problema degli Outlier nella Visione Artificiale

Nella visione artificiale (*Computer Vision*), il modello puramente gaussiano fallisce a causa della presenza di **valori anomali** (*outliers*). Un outlier può derivare da un errato coordinamento dei punti (matching errato) o da riflessi e non segue alcuna distribuzione statistica regolare.

Per gestire questa situazione nella pratica si procede in due fasi:
1. **Identificazione e filtraggio degli outlier:** Si analizzano i punti per scartare quelli che deviano in modo marcato dal modello geometrico presunto.
2. **Stima ai Minimi Quadrati (*Least Squares*):** Sul sottoinsieme di punti validi (affetti da rumore residuo ragionevolmente assimilabile a un rumore gaussiano), si applica l'algoritmo dei minimi quadrati per stimare la matrice di trasformazione o il vettore di parametri $h$.

---

### Ricostruzione della Matrice di Rotazione e Proiezione su $SO(3)$

Quando applichiamo il metodo DLT con dati reali affetti da rumore, otteniamo un vettore stimato $\hat{h}$ da cui estraiamo le tre colonne candidate $R_1, R_2, R_3$ per la matrice di rotazione. 

> **CONCETTO CHIAVE**
> Poiché il vettore $\hat{h}$ è frutto di una stima approssimata (a causa del numero finito di punti e del rumore residuo), la matrice costruita affiancando i tre vettori colonna $\tilde{R} = \begin{bmatrix} R_1 & R_2 & R_3 \end{bmatrix}$ **NON è una vera matrice di rotazione**. In particolare, i suoi vettori colonna non saranno perfettamente ortogonali fra loro e il suo determinante potrà differire da $+1$.

#### Proiezione nello Spazio delle Matrici di Rotazione $SO(3)$

Per ripristinare le proprietà geometriche rigide, occorre effettuare un'operazione di **proiezione** della matrice stimata $\tilde{R}$ sul gruppo speciale ortogonale $SO(3)$ (lo spazio delle vere matrici di rotazione).

Matematicamente, si cerca la vera matrice di rotazione $R \in SO(3)$ che minimizza la distanza dalla matrice non ortogonale $\tilde{R}$. La distanza tra matrici viene definita mediante la **Norma di Frobenius** (*Frobenius Norm*):

$$\hat{R} = \arg\min_{R \in SO(3)} \| \tilde{R} - R \|_F$$

dove per una generica matrice $A$, $\|A\|_F = \sqrt{\sum_{i}\sum_{j} a_{ij}^2}$.

Questa proiezione garantisce che la matrice finale $\hat{R}$ rispetti i vincoli di ortonormalità:

$$\hat{R}^T \hat{R} = I \quad \text{e} \quad \det(\hat{R}) = +1$$

---

### Catene di Trasformazione di Frame e Calcolo delle Pose Relative

Una volta stimata e proiettata la matrice di rotazione, ed estratto il vettore di traslazione, disponiamo della **Matrice di Trasformazione Omogenea** (*Homogeneous Transformation Matrix*) tra la telecamera e l'oggetto, indicata come $T_O^C$ (posa dell'oggetto $O$ rispetto alla telecamera $C$).

L'obiettivo finale del sistema robotico è determinare le relazioni cinematiche tra tutti i sistemi di riferimento (*frame*) coinvolti:
* **Base ($B$):** Telaio di riferimento fisso del robot.
* **Camera ($C$):** Sistema di riferimento della telecamera.
* **Organo Terminale / End-Effector ($E$):** Sistema di riferimento dell'utensile del robot.
* **Oggetto ($O$):** Sistema di riferimento dell'oggetto da manipolare.

```
      [ Base (B) ]
        /      \
       /        \
[ End-Effector (E) ] -- (Costante) -- [ Camera (C) ]
                                            |
                                      (Ricostruito DLT)
                                            |
                                      [ Oggetto (O) ]
```

#### Algebra delle Trasformazioni di Frame

Per ricavare le posizioni relative non note, si sfrutta la composizione delle matrici di trasformazione mediante moltiplicazione e inversione.

##### 1. Calcolo della posa dell'Oggetto rispetto alla Base ($T_O^B$)
Se la posa della telecamera rispetto alla base $T_C^B$ è nota, la posa dell'oggetto rispetto alla base è data da:

$$T_O^B = T_C^B \cdot T_O^C$$

Viceversa, se è nota la relazione tra base e oggetto $T_O^B$, si può ricavare la posizione della telecamera rispetto alla base $T_C^B$:

$$T_C^B = T_O^B \cdot T_C^O = T_O^B \cdot (T_O^C)^{-1}$$

*Nota: Passaggio integrato con chiarezza didattica.*

##### 2. Calcolo della posa dell'Organo Terminale rispetto alla Base ($T_E^B$)
In una configurazione *eye-in-hand* (telecamera montata sull'organo terminale), la trasformazione tra l'organo terminale e la telecamera $T_C^E$ è fissa e nota a priori tramite calibrazione.

Per trovare la trasformazione tra l'oggetto e l'organo terminale $T_O^E$:

$$T_O^E = T_C^E \cdot T_O^C$$

Di conseguenza, invertendo le catene cinematiche appropriate, si garantisce che il sistema di controllo del robot conosca in ogni istante la posizione esatta dell'oggetto $O$ rispetto alla base $B$ o all'end-effector $E$, consentendo le operazioni di aggancio o inseguimento (*tracking*).

---

### Controllo nel Piano Immagine e il Jacobiano dell'Immagine

L'obiettivo è guidare un manipolatore dalla sua posizione iniziale a una posizione finale operando direttamente sul **Piano Immagine** (*Image Plane*). Invece di ricostruire la posa relativa nello spazio 3D per riutilizzare i controllori tradizionali, si vuole sfruttare l'informazione visiva della telecamera per valutare traiettoria ed errore direttamente sul piano 2D. 

Poiché la telecamera proietta un mondo tridimensionale su un piano bidimensionale, occorre verificare se questa informazione sia sufficiente per risolvere il problema di controllo. Per farlo, è necessario introdurre un nuovo tipo di Jacobiano.

Il Jacobiano convenzionale (geometrico o analitico) mappa le velocità nello spazio dei giunti ($\dot{q}$) con le velocità dell'end-effector nello spazio cartesiano. Il **Jacobiano dell'Immagine** (*Image Jacobian*) è invece una relazione cinematica tra due differenti quantità:
1. La velocità con cui varia il **Vettore delle Caratteristiche** (*Feature Vector*) nel piano immagine ($\dot{s}$ o $\dot{x}$).
2. La velocità relativa tra il manipolatore (o la telecamera ad esso solidale) e l'oggetto.

#### Definizione delle Velocità Relative tra Camera ed Oggetto

Si consideri un oggetto in movimento relativo rispetto alla telecamera. Tale moto relativo sussiste sia se l'oggetto si muove effettivamente nello spazio, sia se l'oggetto è fermo e la telecamera è in movimento.

Definiamo i seguenti tre sistemi di riferimento (*Frame*):
* Terna base: $\{b\}$ (o priva di pedice)
* Terna telecamera: $\{c\}$
* Terna oggetto: $\{o\}$

Il vettore $o_{c,o}^c$ unisce l'origine del frame telecamera con l'origine del frame oggetto, espresso nel sistema di riferimento della telecamera:

$$o_{c,o}^c = R_c^T (o_o - o_c)$$

dove $o_o$ e $o_c$ sono le posizioni assolute dell'oggetto e della telecamera rispetto al frame base, mentre $R_c$ è la matrice di rotazione dalla telecamera al frame base ($R_c = R_c^b$).

La velocità relativa $v_{c,o}^c$ è composta da una parte lineare e da una parte angolare:

$$v_{c,o}^c = \begin{bmatrix} \dot{o}_{c,o}^c \\ R_c^T (\omega_o - \omega_c) \end{bmatrix}$$

- **Parte lineare:** È la derivata temporale del vettore posizione espresso nel frame camera $\dot{o}_{c,o}^c$.
- **Parte angolare:** Poiché i vettori di velocità angolare appartengono a uno spazio vettoriale e si possono sommare algebricamente, la variazione dell'orientamento relativo è data dalla differenza $(\omega_o - \omega_c)$ espressa rispetto al frame base. Per proiettarla nel frame della telecamera, viene premoltiplicata per la matrice di rotazione trasposta $R_c^T$.

---

### Derivazione Matematica del Jacobiano e della Matrice di Interazione

I punti di interesse dell'oggetto si proiettano sul piano immagine fornendo coordinate normalizzate, raccolte nel vettore delle caratteristiche $s \in \mathbb{R}^k$. Se si considerano $n$ punti sul piano immagine, ciascuno caratterizzato da $2$ coordinate normalizzate, la dimensione del vettore sarà $k = 2n$.

Il **Jacobiano dell'Immagine** $J_s$ (o $J_x$) è definito dalla relazione:

$$\dot{s} = J_s \, v_{c,o}^c$$

Essendo $\dot{s} \in \mathbb{R}^k$ e $v_{c,o}^c \in \mathbb{R}^6$ (composto da 3 componenti lineari e 3 angolari), la matrice $J_s$ ha dimensione $k \times 6$.

> **CONCETTO CHIAVE**
> La matrice $J_s$ dipende localmente sia dal vettore delle caratteristiche corrente $s$, sia dalla posa relativa (in particolare dalla profondità dei punti) tra la telecamera e l'oggetto.

```
 Velocità Relativa (Oggetto/Camera)        Jacobiano dell'Immagine (J_s)        Variazione Feature (Piano Immagine)
           v_{c,o}^c               ------------------------------------>                  \dot{s}
        (dimensione 6x1)                    (matrice k x 6)                           (dimensione k x 1)
```

#### Dalla Velocità Relativa alle Velocità Assolute (Matrice $\Gamma$)

Per scopi di controllo è utile esprimere la derivata del vettore feature $\dot{s}$ in funzione delle **velocità assolute** della telecamera e dell'oggetto, espresse tuttavia nel frame della telecamera.

Definiamo il vettore velocità assoluta della telecamera espresso nel frame camera $v_{c,c}^c$ e quello dell'oggetto $v_{o,c}^c$:

$$v_{c,c}^c = \begin{bmatrix} R_c^T \dot{o}_c \\ R_c^T \omega_c \end{bmatrix}, \quad v_{o,c}^c = \begin{bmatrix} R_c^T \dot{o}_o \\ R_c^T \omega_o \end{bmatrix}$$

Sviluppiamo la derivata temporale del vettore $o_{c,o}^c$:

$$\dot{o}_{c,o}^c = \frac{d}{dt} \left( R_c^T (o_o - o_c) \right) = R_c^T (\dot{o}_o - \dot{o}_c) + \dot{R}_c^T (o_o - o_c)$$

Sfruttando le proprietà delle matrici di rotazione e dell'operatore antisimmetrico (*Skew-symmetric matrix*) $S(\cdot)$, si può dimostrare che il termine dovuto alla derivata della matrice di rotazione soddisfa la seguente relazione:

$$\dot{R}_c^T (o_o - o_c) = S(o_{c,o}^c) R_c^T \omega_c$$

*(Nota: Passaggio integrato con chiarezza didattica per ricostruire i passaggi algebrici sottintesi).*

Riorganizzando i termini in forma matriciale, si ottiene la relazione che lega la velocità relativa $v_{c,o}^c$ alle velocità assolute $v_{o,c}^c$ e $v_{c,c}^c$:

$$v_{c,o}^c = v_{o,c}^c + \Gamma \, v_{c,c}^c$$

dove $\Gamma$ è una matrice di blocco $6 \times 6$ definita come:

$$\Gamma = \begin{bmatrix} -I_3 & S(o_{c,o}^c) \\ 0_3 & -I_3 \end{bmatrix}$$

in cui $I_3$ è la matrice identità $3 \times 3$, $0_3$ è la matrice nulla $3 \times 3$ e $S(o_{c,o}^c)$ è la matrice antisimmetrica associata al vettore posizione $o_{c,o}^c$.

#### Matrice di Interazione ($L_s$) e Caso di Oggetto Stazionario

Sostituendo l'espressione di $v_{c,o}^c$ all'interno della relazione fondamentale del Jacobiano dell'immagine, si ottiene:

$$\dot{s} = J_s \left( v_{o,c}^c + \Gamma v_{c,c}^c \right) = J_s v_{o,c}^c + L_s v_{c,c}^c$$

dove si definisce **Matrice di Interazione** (*Interaction Matrix*) $L_s$ il prodotto:

$$L_s = J_s \, \Gamma$$

> **CONCETTO CHIAVE**
> Nella maggior parte delle applicazioni pratiche dell'ingegneria (es. **Visual Servoing Basato su Immagine** / *Image-Based Visual Servoing - IBVS*), l'oggetto da inseguire o afferrare è immobile nello spazio ($v_{o,c}^c = 0$). In questo caso realistico, la relazione si semplifica drasticamente:
> $$\dot{s} = L_s \, v_{c,c}^c$$
> La matrice di interazione $L_s$ mappa direttamente la velocità assoluta della telecamera (espressa nel proprio frame) con la velocità di variazione delle feature sul piano immagine.

#### Proprietà d'Invertibilità della Matrice $\Gamma$

In pratica, è spesso più semplice calcolare la matrice di interazione $L_s$ e successivamente invertire la relazione per ricavare il Jacobiano dell'immagine $J_s = L_s \Gamma^{-1}$. 

La matrice $\Gamma$ è **sempre invertibile**. Essendo una matrice triangolare a blocchi $6 \times 6$:

$$\Gamma = \begin{bmatrix} -I_3 & S(o_{c,o}^c) \\ 0_3 & -I_3 \end{bmatrix}$$

I suoi $6$ autovalori sono tutti esattamente pari a $-1$. Non essendoci autovalori nulli, $\Gamma$ ha rango pieno ed è garantita la sua invertibilità in qualsiasi configurazione spaziale.