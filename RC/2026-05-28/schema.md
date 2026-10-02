# Controllo dell'Interazione Robot-Ambiente: Vincoli Naturali, Vincoli Artificiali e Formalizzazione delle Specifiche

## Overview Didattica

In questa lezione conclusiva del corso viene completata l'analisi delle strategie di controllo per robot che interagiscono con l'ambiente circostante. L'obiettivo principale è la formalizzazione delle specifiche di controllo mediante la definizione rigorosa di due insiemi complementari di vincoli geometrici e cinematici:

- **Vincoli Naturali (*Natural Constraints*)**: Rappresentano le limitazioni fisiche imposte dall'ambiente e dalla cinematica del sistema. Essi definiscono le direzioni lungo le quali il movimento (velocità lineari o angolari) è impedito e le direzioni lungo le quali non si generano reazioni vincolari (forze o coppie).
- **Vincoli Artificiali (*Artificial Constraints*)**: Rappresentano le specifiche di progetto determinate dal controllore. Essi definiscono le traiettorie di velocità desiderate lungo le direzioni di libero movimento e i valori di forza o coppia desiderati lungo le direzioni vincolate.

La lezione illustra l'applicazione pratica di questo formalismo attraverso l'analisi dettagliata dell'operazione di rotazione di una manovella (*crank*). Viene inoltre mostrata la parametrizzazione matematica necessaria per trasformare le grandezze cinematiche dal terna di riferimento locale (*Reference Frame*) alla terna base (*Base Frame*) mediante matrici di selezione (*Selection Matrices*) e matrici di rotazione nodale ($R_z(\alpha)$).

---

### Vincoli Naturali e Artificiali: Esempio Pratico della Manovella

Nella modellazione dell'interazione tra un robot e l'ambiente circostante, la scelta delle variabili da controllare (forze o velocità) è dettata dalla geometria del contatto. I vincoli descrivono come lo spazio operativo a 6 gradi di libertà (DOF, *Degrees of Freedom*) venga suddiviso in due sottospazi complementari:
* **Vincoli Naturali (*Natural Constraints*)**: vincoli imposti dalla cinematica e dalla geometria dell'ambiente.
* **Vincoli Artificiali (*Artificial Constraints*)**: vincoli imposti dal sistema di controllo per raggiungere l'obiettivo del compito (*task*).

Se le direzioni lungo cui il movimento è bloccato sono $k$, avremo $6 - k$ direzioni lungo cui il movimento è libero. Di conseguenza, le forze di reazione si manifestano lungo le $k$ direzioni vincolate, mentre sono nulle lungo le $6 - k$ direzioni libere.

---

#### Modellazione Geometrica del Sistema "Manovella"

Consideriamo il problema di far ruotare una manovella (*crank*) mediante l'end-effector del robot.

1. **Definizione delle Terne di Riferimento**:
   * **Terna Fissa/Base (*Base Frame*) $\Sigma_0$**: la terna solidale al telaio su cui è montata la manovella, non in movimento.
   * **Terna di Riferimento/Compliance (*Reference/Compliant Frame*) $\Sigma_c$**: terna scelta in modo intuitivo, posizionata sull'impugnatura della manovella:
     * L'asse $y_c$ è scelto **tangente** alla traiettoria circolare (direzione del movimento lineare consentito).
     * L'asse $x_c$ è orientato lungo il braccio della manovella (direzione radiale).
     * L'asse $z_c$ è parallelo all'asse di rotazione della manovella.

2. **Rotazione tra i Sistemi di Riferimento**:
   La posizione angolare della manovella è definita dall'angolo $\alpha$. La rotazione per passare dalla terna base $\Sigma_0$ alla terna mobile $\Sigma_c$ è rappresentata dalla matrice di rotazione elementare attorno all'asse $z$:
   $$R_z(\alpha) = \begin{bmatrix} \cos\alpha & -\sin\alpha & 0 \\ \sin\alpha & \cos\alpha & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

---

#### Analisi dei Vincoli Naturali

I vincoli naturali definiscono cosa l'ambiente **impossibilita** o **consente** di fare in termini di velocità e quali forze di reazione nascono di conseguenza.

##### Velocità (Cinematica)
* **Velocità Lineari**: L'impugnatura è vincolata a muoversi solo lungo la tangente alla circonferenza ($y_c$). Non è possibile traslare lungo $x_c$ o $z_c$.
  $$v_x = 0, \quad v_z = 0$$
* **Velocità Angolari**: La manovella ruota liberamente attorno all'asse $z_c$, ma la struttura meccanica impedisce rotazioni attorno a $x_c$ e $y_c$.
  $$\omega_x = 0, \quad \omega_y = 0$$

##### Forze e Coppie di Reazione
Poiché lungo le direzioni libere non vi sono vincoli rigidi (ipotizzando assenza di attrito ideale):
* La reazione vincolare lungo $y_c$ è nulla: $f_y = 0$.
* La coppia di reazione attorno all'asse $z_c$ è nulla: $\tau_z = 0$.

Riassumendo, il vettore dei vincoli naturali si compone di:
$$\text{Vincoli Naturali:} \quad \{ v_x = 0, \; v_z = 0, \; \omega_x = 0, \; \omega_y = 0, \; f_y = 0, \; \tau_z = 0 \}$$

---

#### Analisi dei Vincoli Artificiali

I vincoli artificiali rappresentano le specifiche assegnate al controllore sulle variabili libere.

##### Velocità Desiderate
Sulle direzioni in cui il movimento è naturale, imposiamp una traiettoria di velocità:
* **Velocità Lineare Desiderata**: $v_y = v_{y,d}$ (velocità tangenziale impostata per far girare la manovella).
* **Velocità Angolare Desiderata**: $\omega_z = \omega_{z,d}$ (spesso imposta a $0$ se non si desidera far ruotare il giunto dell'impugnatura su se stesso).

##### Forze e Coppie Desiderate
Sulle direzioni vincolate dall'ambiente, il robot applica o controlla le forze/coppie di reazione. Per evitare inutili stress meccanici alla struttura, si impongono valori desiderati nulli:
* **Forze Desiderate**: $f_x = f_{x,d} = 0$, $f_z = f_{z,d} = 0$.
* **Coppie Desiderate**: $\tau_x = \tau_{x,d} = 0$, $\tau_y = \tau_{y,d} = 0$.

Riassumendo, i vincoli artificiali imposti al controllore sono:
$$\text{Vincoli Artificiali:} \quad \{ v_y = v_{y,d}, \; \omega_z = \omega_{z,d}, \; f_x = f_{x,d}, \; f_z = f_{z,d}, \; \tau_x = \tau_{x,d}, \; \tau_y = \tau_{y,d} \}$$

> **Concetto Chiave**
> I vincoli naturali e artificiali sono **perfettamente complementari**. Le direzioni in cui si impone un vincolo di velocità artificiale corrispondono a direzioni con forza naturale nulla, e viceversa. Il principio del lavoro virtuale (*Virtual Work*) assicura che le forze di reazione non compiano lavoro lungo le direzioni di movimento consentito:
> $$v^T h = 0$$

---

#### Parametrizzazione e Trasformazione di Frame

Per utilizzare questo formalismo all'interno di un controllore espresso rispetto alla terna fissa della base $\Sigma_0$, si introducono le matrici di selezione e trasformazione.

*(Nota: Passaggio integrato con chiarezza didattica per esplicitare la struttura matriciale)*

##### 1. Parametrizzazione delle Velocità
Isoliamo le due velocità libere nel vettore $\mathbf{\nu} = \begin{bmatrix} v_y \\ \omega_z \end{bmatrix} \in \mathbb{R}^2$. Il vettore di velocità a 6 DOF nel frame locale $\Sigma_c$, indicato con $v_c$, si ottiene tramite una matrice di selezione $S_v \in \mathbb{R}^{6 \times 2}$:

$$v_c = \begin{bmatrix} v_x \\ v_y \\ v_z \\ \omega_x \\ \omega_y \\ \omega_z \end{bmatrix} = S_v \mathbf{\nu} = \begin{bmatrix} 0 & 0 \\ 1 & 0 \\ 0 & 0 \\ 0 & 0 \\ 0 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} v_y \\ \omega_z \end{bmatrix}$$

Per esprimere la velocità $v_0$ rispetto alla terna base $\Sigma_0$, applichiamo la matrice di rotazione a blocco $T(R_z(\alpha))$:

$$v_0 = \begin{bmatrix} R_z(\alpha) & \mathbf{0}_{3\times3} \\ \mathbf{0}_{3\times3} & R_z(\alpha) \end{bmatrix} v_c = \begin{bmatrix} R_z(\alpha) & \mathbf{0}_{3\times3} \\ \mathbf{0}_{3\times3} & R_z(\alpha) \end{bmatrix} S_v \mathbf{\nu}$$

##### 2. Parametrizzazione delle Forze
Isoliamo le quattro componenti di forza/coppia reattiva nel vettore $\mathbf{\lambda} = \begin{bmatrix} f_x \\ f_z \\ \tau_x \\ \tau_y \end{bmatrix} \in \mathbb{R}^4$. Il vettore generalizzato di forza $h_c \in \mathbb{R}^6$ si esprime mediante la matrice di selezione $S_f \in \mathbb{R}^{6 \times 4}$:

$$h_c = \begin{bmatrix} f_x \\ f_y \\ f_z \\ \tau_x \\ \tau_y \\ \tau_z \end{bmatrix} = S_f \mathbf{\lambda} = \begin{bmatrix} 1 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 0 & 0 & 0 & 0 \end{bmatrix} \begin{bmatrix} f_x \\ f_z \\ \tau_x \\ \tau_y \end{bmatrix}$$

Analogamente, per esprimerlo rispetto al frame della base $\Sigma_0$:

$$h_0 = \begin{bmatrix} R_z(\alpha) & \mathbf{0}_{3\times3} \\ \mathbf{0}_{3\times3} & R_z(\alpha) \end{bmatrix} S_f \mathbf{\lambda}$$

##### 3. Verifiche di Ortogonalità
Eseguendo il prodotto trasposto tra le matrici di selezione locali, si conferma la complementarietà ortogonale del sistema:

$$S_v^T S_f = \mathbf{0}_{2 \times 4}$$

Questo garantisce che il lavoro prodotto dalle forze vincolari sulle velocità ammissibili sia sempre rigorosamente nullo:

$$v_c^T h_c = (S_v \mathbf{\nu})^T (S_f \mathbf{\lambda}) = \mathbf{\nu}^T S_v^T S_f \mathbf{\lambda} = 0$$

---

### Sistemi di Riferimento Principali

Per poter progettare il controllore in presenza di vincoli ambientali, è necessario definire chiaramente i diversi sistemi di riferimento (*reference frames*) coinvolti nell'analisi:

*   **Sistema di Riferimento Base (*Base Frame*):** Il sistema di riferimento inerziale fisso rispetto a cui vengono descritte le grandezze cinematiche generali del robot.
*   **Sistema di Riferimento dell'End-Effector (*End-Effector Frame*):** Solidale con l'organo terminale del manipolatore.
*   **Sistema di Riferimento del Sensore (*Sensor Frame*):** Solidale con l'eventuale sensore di forza/coppia (*Force/Torque sensor*). Tipicamente coincide o è collocato a meno di una trasformazione rigida nota rispetto al frame dell'end-effector. Viene impiegato sia per misure dirette, sia per la stima delle forze di contatto tramite sensori di forza "morbidi" (*soft sensors*).
*   **Sistema di Riferimento del Compito (*Task Frame*):** Un frame introdotto ad hoc per descrivere la geometria del contatto con l'ambiente. Ad esempio, nel caso di una superficie, è naturale definire un asse ortogonale ad essa (direzione del vincolo di forza) e gli assi rimanenti tangenti (direzioni consentite per il moto). La determinazione completa del frame segue la regola della mano destra.

Tutte le grandezze fisiche e le matrici utilizzate per l'algoritmo di controllo devono essere opportunamente espresse rispetto al sistema di riferimento comune per poter essere elaborate correttamente.

---

### Modello Dinamico e Cinematico nello Spazio Giunti

La formulazione del problema parte dall'espressione del modello dinamico del robot nello **Spazio Giunti (*Joint Space*)**:

$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = \tau - J^T(q) h_e$$

Dove:
*   $B(q)$ è la matrice di inerzia del robot (*inertia matrix*).
*   $C(q,\dot{q})$ rappresenta i termini di Coriolis e di forza centrifuga (*Coriolis and centrifugal terms*).
*   $g(q)$ è il vettore delle forze gravitazionali (*gravitational terms*).
*   $\tau$ è il vettore delle coppie applicate ai giunti (*joint torques*), ovvero l'ingresso di controllo da progettare.
*   $h_e = \begin{bmatrix} f_e \\ \mu_e \end{bmatrix}$ è il vettore delle forze e coppie esterne esercitate dall'end-effector sull'ambiente (*end-effector force/torque vector*).
*   $J^T(q)$ è la trasposta della matrice Jacobiana, necessaria per proiettare le forze operative dello spazio operativo sulle coppie ai giunti equivalenti.

> **Concetto Chiave: Jacobiano Geometrico vs Analitico**
> La cinematica diretta del sistema lega le velocità dei giunti $\dot{q}$ alle velocità lineari $\dot{p}$ ed angolari $\omega$ dell'end-effector attraverso lo **Jacobiano Geometrico (*Geometric Jacobian*)** $J(q)$:
> $$v = \begin{bmatrix} \dot{p} \\ \omega \end{bmatrix} = J(q)\dot{q}$$
> È fondamentale rimarcare che, trattando direttamente le velocità angolari fisiche $\omega$ e non le derivate temporali di una parametrizzazione d'attitudine (come gli angoli di Eulero), si sta facendo impiego esplicito dello *Jacobiano Geometrico*.

---

### Formalismo dei Vincoli e Parametrizzazione del Moto/Forza

Nel caso di contatto rigido tra robot ed ambiente (ipotesi di assenza di deformazioni o cedevolezze), il moto dell'end-effector non è completamente libero, ma risulta vincolato a sottospazi specifici definiti dalle superfici di contatto.

#### Parametrizzazione della Velocità (Gradi di Libertà di Moto)
Definiamo un vettore di coordinate indipendenti $s$ (di dimensione $k$, pari ai gradi di libertà di moto consentiti dal vincolo) che parametrizza la traiettoria ammissibile sulla superficie. La velocità dell'end-effector $v$ può essere espressa come:

$$v = D(s)\dot{s}$$

Dove:
*   $s$ rappresenta la coordinata curvilinea o la coordinata generalizzata lungo la direzione del vincolo (ad esempio la posizione lungo una guida o l'angolo di rotazione di una manovella).
*   $\dot{s}$ è la derivata temporale della parametrizzazione.
*   $D(s)$ (connessa alla derivata della mappa cinematica del vincolo $\frac{\partial \alpha}{\partial y}$) è la matrice che mappa le variazioni del parametro $s$ nelle velocità lineari ed angolari dell'end-effector espresse nel frame di base.

#### Parametrizzazione delle Forze di Reazione (Gradi di Libertà di Forza)
Allo stesso modo, le forze di reazione $h_e$ derivanti dal vincolo ambientale non sono arbitrarie, ma giacciono nello spazio ortogonale alle direzioni consentite per il moto. Possono essere parametrizzate tramite il vettore $\lambda$:

$$h_e = Y(s)\lambda$$

Dove:
*   $\lambda$ è il vettore dei moltiplicatori di Lagrange (di dimensione $6-k$), che rappresenta le componenti indipendenti della forza di reazione.
*   $Y(s)$ è la matrice delle direzioni delle forze di vincolo.

Essendo i sottospazi delle forze di vincolo e dei movimenti consentiti reciprocamente ortogonali, vale rigorosamente la condizione di ortogonalità:

$$Y^T(s) D(s) = 0$$

> **Concetto Chiave: Vantaggio della Parametrizzazione dei Vincoli**
> L'adozione della parametrizzazione $(s, \lambda)$ permette di incorporare implicitamente i vincoli cinematici e naturali nel modello. Utilizzare le variabili $s$ equivale a rappresentare il sistema dinamico proiettato direttamente sul sottospazio in cui il moto è fisicamente possibile, "riassorbendo" matematicamente la presenza del vincolo stesso.

---

### Definizione degli Obiettivi del Controllo

L'obiettivo primario nell'interazione vincolata è la progettazione dell'azione di controllo $\tau$ (ingresso di coppia ai giunti) per soddisfare contemporaneamente due task distinti:

1.  **Inseguimento di Traiettoria di Moto (*Motion Tracking*):** Garantire che l'end-effector segua un profilo desiderato di posizione, velocità e accelerazione espresso tramite la variabile di parametrizzazione:
    $$s(t) \to s_d(t), \quad \dot{s}(t) \to \dot{s}_d(t), \quad \ddot{s}(t) \to \ddot{s}_d(t)$$
    *(Esempio: far scorrere l'end-effector lungo una guida rettilinea secondo un determinato profilo temporale).*

2.  **Inseguimento di Forza (*Force Tracking*):** Garantire che le forze esercitate dall'end-effector sull'ambiente seguano un profilo di forza desiderato $\lambda_d(t)$:
    $$\lambda(t) \to \lambda_d(t)$$
    *(Esempio: premere sulla superficie mentre ci si sposta lungo di essa per asportare dello strato di colla o materiale).*

A differenza dello spazio libero (in cui tutte le $6$ dimensioni dello spazio operativo sono impiegate per il tracciamento di posizione/orientamento), nel moto vincolato la presenza dell'ambiente richiede il controllo simultaneo e disaccoppiato di $k$ variabili di moto ($s$) e di $6-k$ variabili di forza ($\lambda$).

---

### Sintesi del Controllo Ibrido: Approccio a Due Fasi

L'obiettivo principale della sintesi del controllo ibrido di posizione/forza è consentire al manipolatore di inseguire contemporaneamente due traiettorie desiderate:
1. Una traiettoria cinematica nello spazio delle posizioni/velocità ammissibili, parametrizzata dalla variabile $s(t)$.
2. Una traiettoria di forza/coppia di reazione vincolare, parametrizzata dalla variabile $\lambda(t)$.

Per raggiungere questo obiettivo si utilizza un approccio in cascata basato su **Linearizzazione al Feedback (Feedback Linearization)** e disaccoppiamento, articolato in due fasi sequenziali:

1. **Disaccoppiamento e Linearizzazione:** Progettare la legge di controllo per le coppie ai giunti $u$ in funzione di due accelerazioni ausiliarie ($a_s$ per la posizione ed $a_\lambda$ per la forza), ottenendo una dinamica semplificata e disaccoppiata.
2. **Stabilizzazione dell'Errore:** Progettare le accelerazioni ausiliarie $a_s$ ed $a_\lambda$ per garantire la convergenza a zero degli errori di inseguimento $e_s = s_d - s$ ed $e_\lambda = \lambda_d - \lambda$.

---

### Passo 1: Disaccoppiamento e Linearizzazione tramite Feedback

Ipotizziamo che il sistema lavorino lontano da punti di singolarità cinematica e che il numero di gradi di libertà del robot sia pari a quello dello spazio operativo ($n = m = 6$).

Ricordiamo che la velocità dello spazio operativo $V_\Omega$ e le forze di contatto $f_m$ possono essere espresse tramite le nuove parametrizzazioni legate ai vincoli:
$$V_\Omega = J(q)\dot{q} = S \dot{s}$$
$$f_m = Y(s)\lambda$$

Derivando rispetto al tempo le relazioni cinematiche e sostituendo le equazioni della dinamica del robot nel dominio dei giunti, è possibile esprimere l'accelerazione dei giunti $\ddot{q}$ in funzione delle derivate delle nuove variabili parametrizzate $\ddot{s}$ e della variabile di forza $\lambda$.

> **Nota: Passaggio integrato con chiarezza didattica**
> Sostituendo la relazione delle forze di reazione $f_m = Y(s)\lambda$ e l'espressione di $\ddot{q}(s, \dot{s}, \ddot{s})$ nelle equazioni dinamiche del manipolatore, si ottiene una struttura in cui il vettore delle accelerazioni di giunto e delle forze di reazione appare in forma matriciale blocco-composta:

$$A(q) \begin{bmatrix} \ddot{s} \\ \lambda \end{bmatrix} + b(q, \dot{q}) = u$$

Dove:
* $A(q)$ è una matrice di dimensione $n \times n$ (con $n=6$) formata da due blocchi di colonne: il primo blocco moltiplica l'accelerazione ausiliaria $\ddot{s}$, mentre il secondo blocco (derivante dal termine $-J^T Y$) moltiplica il vettore dei moltiplicatori di Lagrange $\lambda$.
* $b(q, \dot{q})$ è un vettore che raggruppa tutti i termini di correlazione non lineare (Coriolis, forza centripeta, gravità e derivate delle matrici di parametrizzazione).
* $u$ è il vettore delle coppie applicate ai giunti del robot.

Per linearizzare e disaccoppiare completamente il sistema, si sceglie la legge di controllo $u$ come:

$$u = A(q) \begin{bmatrix} a_s \\ a_\lambda \end{bmatrix} + b(q, \dot{q})$$

Sostituendo questa legge di controllo $u$ nell'equazione del sistema, otteniamo il disaccoppiamento perfetto desiderato:

$$\begin{bmatrix} \ddot{s} \\ \lambda \end{bmatrix} = \begin{bmatrix} a_s \\ a_\lambda \end{bmatrix} \implies \begin{cases} \ddot{s} = a_s \\ \lambda = a_\lambda \end{cases}$$

Con questo primo passaggio, il sistema non lineare complesso è stato ridotto a:
* Un **doppio integratore (Double Integrator)** lungo le direzioni del moto ammissibile ($s$).
* Una relazione algebrica diretta lungo le direzioni di vincolo della forza ($\lambda$).

---

### Passo 2: Stabilizzazione dell'Errore di Inseguimento

Una volta ottenuto il sistema disaccoppiato, è necessario progettare le ingressi ausiliari $a_s$ ed $a_\lambda$.

#### Controllo di Posizione/Velocità ($s$)
Per la componente di movimento, l'obiettivo è azzerare l'errore di posizione $e_s(t) = s_d(t) - s(t)$. Essendo la dinamica equivalente quella di un doppio integratore $\ddot{s} = a_s$, si adotta una legge di controllo con termine di feedforward dell'accelerazione desiderata ed un'azione correttiva di tipo Proporzionale-Deritativa (PD):

$$a_s = \ddot{s}_d + K_{ds}(\dot{s}_d - \dot{s}) + K_{ps}(s_d - s)$$

Sostituendo $a_s = \ddot{s}$ nell'equazione del doppio integratore, si ottiene la dinamica dell'errore:

$$\ddot{s}_d - \ddot{s} + K_{ds}(\dot{s}_d - \dot{s}) + K_{ps}(s_d - s) = 0$$

$$\ddot{e}_s + K_{ds}\dot{e}_s + K_{ps}e_s = 0$$

> **Concetto Chiave: Stabilità dell'Errore di Posizione**  
> Scegliendo le matrici dei guadagni $K_{ps}$ e $K_{ds}$ definite positive (Positive Definite Gains), l'equazione differenziale del secondo ordine a coefficienti costanti è asintoticamente ed **esponenzialmente stabile**. L'errore di posizione $e_s(t)$ converge a zero: $\lim_{t \to \infty} e_s(t) = 0$.

#### Controllo di Forza ($\lambda$)
Per la componente di forza, l'obiettivo è azzerare l'errore $e_\lambda(t) = \lambda_d(t) - \lambda(t)$. Trattandosi di una relazione algebrica diretta ($\lambda = a_\lambda$), in linea teorica basterebbe porre $a_\lambda = \lambda_d$. Tuttavia, per garantire robustezza contro incertezze di modello o rumore di misura, si aggiunge un termine di **azione integrativa (Integral Action)**:

$$a_\lambda = \lambda_d + K_{i\lambda} \int_{0}^{t} (\lambda_d(\tau) - \lambda(\tau)) d\tau$$

Essendo $\lambda = a_\lambda$, sostituendo l'espressione si ottiene:

$$\lambda = \lambda_d + K_{i\lambda} \int_{0}^{t} e_\lambda(\tau) d\tau \implies e_\lambda(t) + K_{i\lambda} \int_{0}^{t} e_\lambda(\tau) d\tau = 0$$

Derivando questa equazione rispetto al tempo:

$$\dot{e}_\lambda + K_{i\lambda} e_\lambda = 0$$

> **Concetto Chiave: Stabilità dell'Errore di Forza**  
> L'aggiunta dell'azione integrativa trasforma la dinamica dell'errore di forza in un'equazione differenziale del **primo ordine**. Se il guadagno integrativo $K_{i\lambda}$ è positivo, l'errore di forza converge esponenzialmente a zero, fornendo al contempo robustezza contro imperfezioni nei sensori.

---

### Architettura del Schema di Controllo Ibrido

L'architettura complessiva del controllo ibrido (Hybrid Control Scheme) si presenta come una struttura in retroazione (Feedback) organizzata nei seguenti blocchi logici:

* **Sensori di Misura:** Il sistema richiede la conoscenza in tempo reale delle variabili di stato $s, \dot{s}$ (velocità e posizioni lungo le direzioni ammissibili) e delle forze $\lambda$.
  * Le variabili di moto sono ricavate dai sensori cinematici di giunto.
  * Le forze di reazione $\lambda$ sono misurate direttamente tramite **Sensori di Forza (Force Sensors)** posti al terminale del robot, oppure stimate tramite algoritmi software definiti **Sensori Morbidi (Soft Sensors)**.
* **Filtro di Misura:** Nella pratica industriale, i segnali acquisiti dai sensori di forza vengono pre-filtrati per smorzare picchi ad alta frequenza e rumori di misura prima di essere inviati al blocco di feedback.
* **Modulo di Linearizzazione al Feedback:** Calcola la matrice $A(q)$ e il vettore $b(q, \dot{q})$ basandosi sui vincoli naturali ed artificiali del task (Task Reference Frame).

```
                      +-------------------+
                      |   Traiettorie     |
                      |  s_d, lambda_d    |
                      +---------+---------+
                                |
                                v
+-------------+       +-------------------+       +--------------------+      +----------+
|  Sensori    |------>|  Fasi di Controllo|------>| Linearizzazione al |----->|  Robot / |
| Pos. & Forz.|       |  (PD su s, I su λ)| (a_s, | Feedback u=A*a + b | (u)  | Ambiente |
+-------------+       +-------------------+  a_λ) +--------------------+      +----------+
```

Questo approccio garantisce la stabilità e le prestazioni desiderate, specialmente in applicazioni in cui sia la struttura del manipolatore sia l'ambiente di contatto presentano un'elevata rigidezza meccanica (Stiff System).

---

### Attività Pratiche ed Esperienze Numeriche (Numerical Experiences)

#### Struttura dei Laboratori Computazionali
Per completare il percorso applicativo del corso, vengono messe a disposizione delle esercitazioni pratiche basate su simulazioni numeriche. Il materiale è organizzato in moduli che coprono due pilastri fondamentali della robotica dei manipolatori:

*   **Cinematica (Kinematics):** Analisi della posizione e dell'orientamento dell'organo terminale (*end-effector*) di architetture standard come i robot **SCARA** e i manipolatori articolati tipo **UR (Universal Robots)**.
*   **Controllo Dinamico (Dynamic Control):** Implementazione degli algoritmi di controllo della coppia ai giunti per l'inseguimento di traiettorie su modelli dinamici completi.

L'obiettivo pratico consiste nel manipolare i file di codice forniti, completare l'implementazione degli algoritmi di controllo richiesti e analizzare le prestazioni dei sistemi simulati.

---

### Guida agli Esercizi di Esame e Scorciatoie Analitiche

#### Cinematica e Assegnazione dei Sistemi di Riferimento
Negli esercizi dedicati alla cinematica, viene solitamente fornita una struttura meccanica specifica di un braccio robotico con l'indicazione dei giunti e dei bracci (*limbs*). La prova richiede di:
1.  Orientare ed assegnare gli assi dei sistemi di riferimento coordinati (secondo le convenzioni note, come Denavit-Hartenberg).
2.  Analizzare o calcolare lo **Spazio di Lavoro (Workspace)** raggiungibile dal manipolatore.

#### Semplificazione dei Calcoli e Intuizione Fisica

> **CONCETTO CHIAVE: Uso dell'Intuizione Fisica contro il Calcolo Automatico**
>
> Durante lo svolgimento degli esercizi analitici della dinamica, **non bisogna buttarsi a capofitto in calcoli matriciali complessi**. Spesso esistono scorciatoie concettuali basate sulla fisica del sistema che semplificano drasticamente le equazioni.

Consideriamo la generica equazione della dinamica di un manipolatore:

$$M(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = \tau$$

Dove $M(q)$ è la matrice d'inerzia, $C(q, \dot{q})$ raccoglie i termini di Coriolis e centrifughi, $g(q)$ è il vettore della gravità e $\tau$ è la coppia applicata ai giunti.

*   **Esempio Pratico:** Se una parte del robot o uno specifico giunto compie un movimento di traslazione puramente orizzontale (o si muove all'interno di un piano perpendicolare al vettore di gravità $\vec{g}$), il potenziale gravitazionale $U$ del sistema rimane costante ($\Delta U = 0$).
*   **Conseguenza Analitica:** In questo caso, la derivata del potenziale rispetto alle coordinate libere $q$ è nulla, il che implica direttamente che il vettore delle forze gravitazionali è nullo:

$$g(q) = \frac{\partial U(q)}{\partial q} = 0$$

Riconoscere subito questa condizione fisica evita di calcolare inutilmente trasformazioni di coordinate o matrici complesse per determinare la componente gravitazionale.

#### Sezione Teorica: Progetto dei Controllori (Controller Design)
La parte teorica dell'esame si concentra fortemente sul **Disegno dei Controllori (Controller Design)**. Le domande richiedono risposte precise, strutturate e puntuali su architetture di controllo specifiche (es. Controllo a Coppia Calcolata / *Computed Torque Control*, Controllo PD con compensazione di gravità, Controllo nello Spazio Operativo).

---

### Frontiere della Ricerca in Robotica (Advanced Research Topics)

#### Apprendimento per Rinforzo nella Robotica (Reinforcement Learning for Robotics)
L'integrazione tra l'**Apprendimento per Rinforzo (Reinforcement Learning - RL)** e la robotica classica mira ad aumentare l'autonomia dei robot nell'esecuzione di compiti complessi in ambienti incerti. La ricerca si sviluppa sia su piattaforme di simulazione sia su hardware reale (es. manipolatori robotici da laboratorio), affrontando sfide quali:
*   Il passaggio dalla simulazione al mondo reale (*Sim-to-Real transfer*).
*   La stabilità e la sicurezza dell'interazione meccanica durante l'apprendimento.

#### Modelli Linguistici e Software su Grande Scala (LLMs & Large-Scale Software)
Un'altra linea di ricerca avanzata riguarda la combinazione di **Grandi Modelli Linguistici (Large Language Models - LLMs)** con i sistemi software di controllo robotico.

*   **Caso d'Uso (Interfaccia ad Alto Livello):** Un utente interagisce con un robot (es. un quadricottero / *quadrotor*) tramite un comando in linguaggio naturale ad alto livello: *"Ho perso il mio zaino nel parco, puoi inviarmi le coordinate di dove si trova?"*.
*   **Sfida Tecnologica:** Il modello deve tradurre questo *prompt* astratto in una sequenza logica ed esecutiva di azioni atomiche (es. pianificazione dell'esplorazione, rilevamento visivo dell'oggetto, estrazione delle coordinate GPS).
*   **Vincolo Operativo:** I modelli di linguaggio standard sono enormi e computazionalmente pesanti. La ricerca punta a creare modelli alleggeriti (*Small Language Models*) capaci di girare a bordo del robot rispettando i severi vincoli di **risorse computazionali ed energetiche** dei sistemi *embedded*.

---

### Chiarimenti sulla Generazione di Traiettorie (Trajectory Generation)

Nelle esercitazioni pratiche di controllo dinamico, l'obiettivo primario è il tracciamento di un riferimento. Se la legge di generazione della traiettoria desiderata $q_d(t)$ non è esplicitamente vincolata o definita nel testo dell'esercizio:
*   È possibile utilizzare **qualsiasi approccio standard di generazione delle traiettorie** presente in letteratura (es. profili di velocità trapezoidali, polinomi cubici o quinti).
*   L'attenzione principale deve rimanere focalizzata sulla corretta sintesi della legge di controllo $\tau$ in grado di annullare l'errore di tracciamento $e(t) = q_d(t) - q(t)$.

---

> [!NOTE]
> ### Note per l'Esame e Avvisi del Docente
> - **Date e scadenze**: La data dell'esame indicata è il 29 dicembre. La scadenza per la consegna dell'ultimo elaborato/esperienza numerica è fissata per il 25 giugno.
> - **Programma d'esame**: L'esame comprenderà quasi tutto il materiale presente nei file PDF della cartella del corso (salvo alcune eccezioni che verranno specificate in seguito), inclusa espressamente la parte sul controllo ibrido (*hybrid control*).
> - **Esercizi pratici all'esame**: Gli esercizi pratici riguarderanno molto probabilmente la **cinematica** (ad esempio, data la struttura di un braccio robotico, orientare gli assi o analizzare/progettare lo spazio di lavoro - *workspace*).
> - **Parte teorica dell'esame**: La parte teorica si concentrerà principalmente sulla **progettazione dei controllori** (*design of controllers*). Il docente ha specificato di richiedere risposte precise e puntuali a domande dirette.
> - **Dimostrazioni e derivazioni**: Durante la prova potrebbero essere fornite alcune equazioni di partenza con la richiesta di svolgere i passaggi e le derivazioni matematiche finali.