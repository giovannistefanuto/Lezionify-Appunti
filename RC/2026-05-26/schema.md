# Modellistica Dinamica e Controllo Adattativo per Manipolatori Robotici

## Overview Didattica
In questa lezione vengono presentate le linee guida per la modellizzazione dinamica e il controllo di un braccio robotico (*Robotic Arm*), prendendo come riferimento le specifiche del terzo elaborato didattico. 

I concetti chiave affrontati includono:

*   **Convenzione di Denavit-Hartenberg (*Denavit-Hartenberg Convention - DH*):** Analisi e verifica della correttezza della definizione delle variabili di giunto — in particolare per un primo giunto prismatico e un secondo giunto rotoidale ($q_2$) — secondo le regole convenzionali e le buone pratiche di modellazione (*Rule of Thumb*).
*   **Modello Dinamico del Manipolatore (*Dynamic Model of the Manipulator*):** Formulazione delle equazioni del moto nella forma standard:
    $$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = \tau$$
*   **Controllo PD con Compensazione Costante di Gravità (*PD Control with Constant Gravity Compensation*):** Progettazione di una legge di controllo Proporzionale-Differenziale affiancata da un termine di compensazione gravitazionale costante, valutato nella configurazione finale desiderata $q_d$, ovvero $g(q_d)$.
*   **Parametrizzazione Lineare della Dinamica (*Linear Parameterization of Dynamic Model*):** Dimostrazione della linearità del modello dinamico rispetto a un set di parametri fisici ed estrazione della matrice regressore (*Regressor Matrix*) $Y(q, \dot{q}, \ddot{q})$ e del vettore dei parametri $\theta$, tali che:
    $$Y(q, \dot{q}, \ddot{q})\theta = \tau$$
*   **Progettazione del Controllo Adattativo (*Adaptive Controller Design*):** Introduzione della velocità di riferimento (*Reference Velocity*) $\dot{q}_r$ e formulazione della matrice regressore ridefinita in funzione delle traiettorie desiderate e dello stato di riferimento del sistema.

---

### Assegnazione del Terzo Homework e Comunicazioni Organizzative

#### Scadenze e Valutazione
A causa dei ritardi nell'assegnazione, il **Terzo Homework** avrà un valore di **2 punti** (invece del consueto singolo punto). 

*   **Scadenza (Deadline):** La consegna per il Terzo Homework (e per il successivo Quarto Homework) è fissata tassativamente per il **25 Giugno**. Le consegne devono avvenire prima della sessione d'esame.
*   **Opzionalità:** Gli homework mantengono carattere opzionale.

#### Dettagli e Struttura del Terzo Homework
L'obiettivo centrale dell'homework è la realizzazione del modello dinamico e dello schema di controllo per un manipolatore robotico (braccio meccanico).

1. **Analisi Cinematica e Convenzioni Denavit-Hartenberg:**
   * Il primo giunto è prismatico (quindi la variabile $q_1$ rappresenta una lunghezza), mentre il secondo giunto è rotoidale ($q_2$ rappresenta un angolo).
   * Verificare la compatibilità della definizione delle variabili $q_1$ e $q_2$ fornita nel testo con la procedura di Denavit-Hartenberg (*Denavit-Hartenberg Procedure*) e le regole pratiche viste a lezione.
   * Se si ritiene opportuno ridefinire $q_2$ in modo più coerente con la convenzione, è possibile farlo purché la scelta sia esplicitamente discussa e motivata. Qualsiasi variazione nell'angolo $q_2$ modificherà di conseguenza i valori numerici di riferimento (es. valori angolari come $\pi$).

2. **Controllo Proporzionale-Derivativo con Compensazione della Gravità Costante:**
   * Progettare una legge di controllo Proporzionale-Derivativa (PD) accoppiata a un termine di compensazione della gravità costante:
     $$\tau = K_p e + K_d \dot{e} + g(q_d)$$
   * *Nota didattica:* La compensazione della gravità viene calcolata unicamente nella configurazione finale desiderata $q_d$, e non lungo l'intero stato dinamico istantaneo $g(q)$.

3. **Parametrizzazione Lineare del Modello Dinamico:**
   * Dimostrare che il modello dinamico del robot può essere espresso in forma linearmente parametrizzata rispetto a un vettore di parametri dinamici incogniti $\pi$:
     $$\tau = Y(q, \dot{q}, \ddot{ddot{q}}) \pi$$
   * Dove $Y(q, \dot{q}, \ddot{q})$ rappresenta la **Matrice Regressore** (*Regressor Matrix*).

4. **Progetto del Controllo Adattativo (*Adaptive Controller*):**
   * Introdurre la velocità di riferimento $g_r$ (o variabile di scorrimento/errore di velocità sintetico $\dot{q}_r$).
   * Calcolare il regressore esteso, il quale non dipenderà più dalle accelerazioni reali ma dalle traiettorie e velocità di riferimento:
     $$Y_r = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)$$

> **Concetto Chiave: Attenzione al Regressore nel Controllo Adattativo**
> La corretta definizione della matrice regressore $Y_r$ in funzione delle velocità e accelerazioni di riferimento ($\dot{q}_r, \ddot{q}_r$) è uno dei punti storicamente più critici e complessi dell'intero corso. È essenziale prestare la massima attenzione alle sostituzioni dei termini di stato all'interno della struttura dinamica.

---

### Programma delle Ultime Lezioni: Interazione Robot-Ambiente

Il ciclo conclusivo del corso è strutturato come segue:

1. **Inquadramento Teorico (Lezione Corrente):** Modellizzazione matematica della dinamica di un robot in presenza di vincoli cinematici rigidi (*Constrained Dynamics*).
2. **Applicazioni Pratiche (Lezioni Successive):** Studio delle tecniche operative per la gestione del contatto:
   * Controllo di Impedenza (*Impedance Control*)
   * Controllo di Ammettenza / Controllo Aumentato (*Admittance Control / Augmented Control*)

---

### Modellistica dell'Interazione con l'Ambiente: Introduzione ai Vincoli

Per comprendere come un robot interagisce con l'ambiente esterno, occorre estendere la formulazione dinamica classica di Lagrange inserendo le forze di reazione vincolare generate dalle superfici di contatto.

#### Esempio Fondamentale: Massa Puntiforme su Parete Rigida
Consideriamo il sistema fisico elementare di una massa $m$ immersa in un piano $(x, y)$, spinta da una forza orizzontale $F_x$ e da una forza verticale $F_y$. A una posizione orizzontale $x = c$ è presente una parete infinitamente rigida (*Infinitely Stiff Environment*).

```
          y ^
            |       | Parete Rigida
            |   m   | (x = c)
            +--->   |
           F_y |    |
               o--->| F_x
               |    |
  -------------+----+--------> x
               |    |
```

##### 1. Movimento Libero ($x < c$)
Finché la massa non tocca la parete, la forza di reazione dell'ambiente $F_e$ è nulla ($F_e = 0$). Le equazioni del moto seguono direttamente la seconda legge di Newton:
$$m \ddot{x} = F_x$$
$$m \ddot{y} = F_y$$

##### 2. Contatto con la Parete Rigida ($x = c$)
Quando la massa raggiunge la coordinata $x = c$, l'ambiente impedisce qualsiasi ulteriore penetrazione. Assumendo l'assenza di deformazioni (ambiente infinitamente rigido):
* La velocità orizzontale si annulla: $\dot{x} = 0$
* L'accelerazione orizzontale si annulla: $\ddot{x} = 0$

Per il principio di azione e reazione, la parete esercita una forza di reazione vincolare $F_e$ lungo l'asse $x$ pari e contraria alla forza applicata:
$$F_e = -F_x$$

L'unica dinamica ammissibile per la massa diventa un movimento di scorrimento lungo la direzione verticale $y$ (*Constrained Motion*):
$$m \ddot{y} = F_y$$

#### Necessità di una Formulazione Generalizzata
Nell'esempio cartesiano ortogonale il vincolo è banale. Tuttavia, se l'organo terminale del robot (*End-Effector*) fosse vincolato a muoversi lungo una superficie complessa o una traiettoria circolare descritta da un'equazione non lineare del tipo:
$$\phi(x, y) = 0$$

Non è più possibile separare le equazioni del moto con un'analisi scalare intuitiva. Diventa quindi necessario sviluppare una **Formulazione di Lagrange Estesa** (*Extended Lagrangian Theory*) capace di integrare formalmente i vincoli cinematici mediante l'uso dei moltiplicatori di Lagrange.

---

### Formulazione Teorica della Dinamica Vincolata mediante Lagrangiana Aumentata e Moltiplicatori di Lagrange

---

### Introduzione ai Vincoli Geometrici nello Spazio dei Task e dei Giunti

Per descrivere la dinamica di un manipolatore i cui movimenti sono limitati da un vincolo ambientale, partiamo dal modello dinamico standard non vincolato nello spazio dei giunti:

$$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u$$

dove $q \in \mathbb{R}^n$ è il vettore delle coordinate generalizzate (posizioni dei giunti), $B(q)$ è la matrice di inerzia, $C(q, \dot{q})\dot{q}$ rappresenta i contributi di Coriolis e centrifughi, $g(q)$ è il vettore di gravità e $u$ è il vettore delle forze/coppie applicate ai giunti.

Assumiamo che l'uscita del task (*task output function*) sia descritta da una relazione cinematica diretta del tipo $r = f(q) \in \mathbb{R}^p$. Se l'organo terminale (*end-effector*) è soggetto a un numero $M$ di vincoli geometrici (con $M < n$), tali vincoli possono essere espressi in forma implicita nello spazio operativo come $k(r) = 0$.

Sostituendo la cinematica diretta all'interno dell'equazione del vincolo, possiamo definire una funzione generica $h(q)$ espressa direttamente nelle coordinate generalizzate dei giunti:

$$h(q) = 0$$

dove $h(q): \mathbb{R}^n \to \mathbb{R}^M$ descrive in modo compatto le $M$ equazioni di vincolo geometrico che il robot deve soddisfare durante il movimento.

---

### La Lagrangiana Aumentata e i Moltiplicatori di Lagrange

Per derivare le equazioni del moto vincolato in modo sistematico, si estende il formalismo classico della meccanica lagrangiana. L'idea è incorporare direttamente la presenza del vincolo all'interno della funzione Lagrangiana originale $\mathcal{L}(q, \dot{q}) = K(q, \dot{q}) - P(q)$ (dove $K$ è l'energia cinematica e $P$ l'energia potenziale).

Definiamo la **Lagrangiana Aumentata** (*Augmented Lagrangian*) $\mathcal{L}_a$ come:

$$\mathcal{L}_a(q, \dot{q}, \lambda) = \mathcal{L}(q, \dot{q}) + \lambda^T h(q)$$

dove $\lambda \in \mathbb{R}^M$ è il vettore dei **Moltiplicatori di Lagrange** (*Lagrange Multipliers*).

> **Concetto Chiave: Significato Fisico dei Moltiplicatori di Lagrange**
> In ottimizzazione matematica, $\lambda$ rappresenta la variabile duale introdotta per gestire i vincoli. In ambito robotico e fisico, $\lambda$ possiede un significato fisico ben preciso: rappresenta l'intensità delle **forze di reazione vincolare** (*generalized reaction forces*) generati dall'ambiente quando il robot tenta di violare il vincolo geometrico $h(q) = 0$.

Notiamo che la Lagrangiana Aumentata $\mathcal{L}_a$ non è più soltanto funzione delle posizioni $q$ e delle velocità $\dot{q}$, ma dipende anche dall'insieme delle variabili duali $\lambda$.

---

### Derivazione delle Equazioni del Moto Vincolato

Applicando le equazioni di Eulero-Lagrange alla Lagrangiana Aumentata $\mathcal{L}_a$, dobbiamo calcolare le derivate parziali rispetto a $q$, $\dot{q}$ e $\lambda$:

1. **Derivata rispetto a $q$ e $\dot{q}$:**
   Le classiche equazioni di Eulero-Lagrange assumono la forma:
   $$\frac{d}{dt}\left( \frac{\partial \mathcal{L}_a}{\partial \dot{q}} \right) - \frac{\partial \mathcal{L}_a}{\partial q} = u$$

   Sviluppando i singoli termini:
   - Poiché $h(q)$ non dipende da $\dot{q}$, si ha: $\frac{\partial \mathcal{L}_a}{\partial \dot{q}} = \frac{\partial \mathcal{L}}{\partial \dot{q}}$.
   - Derivando rispetto a $q$, bisogna considerare che $q$ compare sia nella Lagrangiana classica sia nella funzione di vincolo $h(q)$:
     $$\frac{\partial \mathcal{L}_a}{\partial q} = \frac{\partial \mathcal{L}}{\partial q} + \frac{\partial}{\partial q}\left(\lambda^T h(q)\right) = \frac{\partial \mathcal{L}}{\partial q} + A^T(q)\lambda$$
     dove $A(q) = \frac{\partial h(q)}{\partial q} \in \mathbb{R}^{M \times n}$ è lo **Jacobiano dei Vincoli** (*Constraint Jacobian*).

   Sostituendo questi termini si ottiene la prima equazione del moto:
   $$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u + A^T(q)\lambda$$

2. **Derivata rispetto a $\lambda$:**
   Considerando la derivata rispetto alle variabili duali:
   $$\frac{d}{dt}\left( \frac{\partial \mathcal{L}_a}{\partial \dot{\lambda}} \right) - \frac{\partial \mathcal{L}_a}{\partial \lambda} = 0$$
   Poiché $\dot{\lambda}$ non compare nella Lagrangiana Aumentata, il primo termine è nullo. Essendo $\mathcal{L}_a$ lineare rispetto a $\lambda$, la derivata parziale rispetto a $\lambda$ restituisce direttamente l'equazione del vincolo:
   $$h(q) = 0$$

> **Concetto Chiave: Lavoro Virtuale nullo delle Forze di Reazione**
> Nel membro di destra dell'equazione rispetto a $\lambda$ si pone $0$ poiché le forze di reazione vincolare associate a $\lambda$ non compiono lavoro non conservativo. La forza di reazione agisce sempre in direzione ortogonale alla superficie di vincolo (lungo la normale), mentre lo spostamento elementare del robot avviene lungo la direzione tangente al vincolo. Il loro prodotto scalare è quindi nullo:
> $$\delta W = F_{vincolo}^T \cdot \delta r = 0$$

---

### Eliminazione del Moltiplicatore di Lagrange e Matrice di Proiezione Dinamica

Le equazioni dinamiche vincolate ottenute costituiscono un sistema differenziale-algebrico (DAE):

$$\begin{cases} B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u + A^T(q)\lambda \\ h(q) = 0 \end{cases}$$

L'obiettivo è eliminare la dipendenza esplicita da $\lambda$ per esprimere il moto vincolato mediante un'unica equazione differenziale.

#### 1. Derivazione temporale del vincolo
Per legare le accelerazioni dei giunti $\ddot{q}$ al vincolo geometrico, deriviamo due volte $h(q) = 0$ rispetto al tempo:
1. Prima derivata temporale (regola della catena):
   $$\dot{h}(q) = A(q)\dot{q} = 0$$
2. Seconda derivata temporale:
   $$\ddot{h}(q) = A(q)\ddot{q} + \dot{A}(q, \dot{q})\dot{q} = 0 \implies A(q)\ddot{q} = -\dot{A}(q, \dot{q})\dot{q}$$

#### 2. Calcolo esplicito di $\lambda$
Dall'equazione della dinamica isoliamo il vettore delle accelerazioni $\ddot{q}$:

$$\ddot{q} = B^{-1}(q) \left( u - C(q,\dot{q})\dot{q} - g(q) + A^T(q)\lambda \right)$$

Premoltiplicando entrambi i membri per la matrice Jacobiana del vincolo $A(q)$ e sostituendo la condizione sull'accelerazione $A(q)\ddot{q} = -\dot{A}(q)\dot{q}$, otteniamo:

$$A(q) B^{-1}(q) \left( u - C(q,\dot{q})\dot{q} - g(q) + A^T(q)\lambda \right) = -\dot{A}(q)\dot{q}$$

Riorganizzando i termini in funzione di $\lambda$:

$$\left( A(q) B^{-1}(q) A^T(q) \right) \lambda = -\dot{A}(q)\dot{q} + A(q) B^{-1}(q) \left( C(q,\dot{q})\dot{q} + g(q) - u \right)$$

Assumendo che $A(q)$ sia a rango riga pieno ($rank(A) = M$, che richiede $M < n$) e sapendo che la matrice d'inerzia $B(q)$ è sempre definita positiva e invertibile, la matrice $(A B^{-1} A^T) \in \mathbb{R}^{M \times M}$ risulta invertibile.

Definiamo la **Pseudoinversa Pesata sull'Inerzia** (*Inertia-Weighted Pseudo-Inverse*) dello Jacobiano dei vincoli come:

$$A_B^\dagger(q) = B^{-1}(q) A^T(q) \left( A(q) B^{-1}(q) A^T(q) \right)^{-1}$$

Possiamo dunque esprimere il vettore delle forze di reazione vincolare $\lambda$ in forma chiusa:

$$\lambda = (A B^{-1} A^T)^{-1} \left( A B^{-1} (C\dot{q} + g - u) - \dot{A}\dot{q} \right)$$

#### 3. Equazione del moto proiettata
Sostituendo l'espressione di $\lambda$ all'interno dell'equazione della dinamica del robot, e raccogliendo i termini, si ottiene la forma compatta del moto vincolato:

$$B(q)\ddot{q} + P_B^T(q) \left( C(q,\dot{q})\dot{q} + g(q) \right) = P_B^T(q) u - B(q) A_B^\dagger(q) \dot{A}(q)\dot{q}$$

dove $P_B(q)$ è la **Matrice di Proiezione Dinamicamente Consistente** (*Dynamically Consistent Projection Matrix*), definita come:

$$P_B(q) = I - A_B^\dagger(q) A(q)$$

> **Interpretazione Fisica:** La matrice $P_B^T(q)$ agisce come un filtro proiettivo che seleziona ed elimina tutte le componenti di forza (sia le forze esterne applicate $u$ che le forze fittizie o di gravità) che violerebbero il vincolo, trasmettendo al sistema unicamente le forze che producono un moto lungo il sottospazio consentito dalle superfici di vincolo.

---

### Verifiche di Consistenza e Simulazione del Moto

Per garantire che la simulazione numerica del modello vincolato sia fisicamente consistente, le condizioni iniziali del sistema $(q(0), \dot{q}(0))$ devono soddisfare rigorosamente i vincoli cinematici:

$$\begin{cases} h(q(0)) = 0 \\ A(q(0))\dot{q}(0) = 0 \end{cases}$$

Se queste condizioni iniziali sono rispettate, l'integrazione temporale della dinamica proiettata garantisce che la traiettoria risultante rimanga vincolata alla superficie $h(q) = 0$ per qualsiasi profilo di coppia $u(t)$ applicato ai giunti.

---

### Esempi Applicativi

#### Esempio 1: Massa Puntiforme Vincolata su una Parete Verticale
Consideriamo un punto materiale di massa $m$ che si muove nel piano $xy$, soggetto ad un vincolo geometrico rigido posto in $x = c$.

- **Coordinate generalizzate:** $q = \begin{bmatrix} x \\ y \end{bmatrix}$
- **Matrice d'inerzia:** $B = \begin{bmatrix} m & 0 \\ 0 & m \end{bmatrix}$
- **Forze applicate:** $u = \begin{bmatrix} f_x \\ f_y \end{bmatrix}$
- **Equazione del vincolo:** $h(q) = x - c = 0 \implies A = \frac{\partial h}{\partial q} = \begin{bmatrix} 1 & 0 \end{bmatrix}$

*(Nota: Passaggio integrato con chiarezza didattica per completare il calcolo esplicito).*

Calcoliamo i termini dell'operatore proiettivo:
$$A B^{-1} A^T = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} \frac{1}{m} & 0 \\ 0 & \frac{1}{m} \end{bmatrix} \begin{bmatrix} 1 \\ 0 \end{bmatrix} = \frac{1}{m}$$

$$A_B^\dagger = B^{-1} A^T (A B^{-1} A^T)^{-1} = \begin{bmatrix} \frac{1}{m} \\ 0 \end{bmatrix} m = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$$

Il moltiplicatore di Lagrange $\lambda$ si riduce a:
$$\lambda = (A B^{-1} A^T)^{-1} A B^{-1} (-u) = m \cdot \left( -\frac{f_x}{m} \right) = -f_x$$

Il valore $\lambda = -f_x$ conferma l'intuizione fisica: la reazione vincolare esercitata dalla parete annulla esattamente la forza $f_x$ spinta contro di essa.

La matrice di proiezione $P_B$ risulta:
$$P_B = I - A_B^\dagger A = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} - \begin{bmatrix} 1 \\ 0 \end{bmatrix} \begin{bmatrix} 1 & 0 \end{bmatrix} = \begin{bmatrix} 0 & 0 \\ 0 & 1 \end{bmatrix}$$

La dinamica filtrata del sistema diviene:
$$\begin{bmatrix} \ddot{x} \\ \ddot{y} \end{bmatrix} = \begin{bmatrix} 0 & 0 \\ 0 & \frac{1}{m} \end{bmatrix} \begin{bmatrix} f_x \\ f_y \end{bmatrix} \implies \begin{cases} \ddot{x} = 0 \\ m\ddot{y} = f_y \end{cases}$$

Il moto lungo $x$ è soppresso, mentre la dinamica lungo $y$ resta libera.

#### Esempio 2: End-Effector Vincolato a Muoversi su una Circonferenza
Immaginiamo un manipolatore planare a due bracci il cui organo terminale è vincolato a scorrere lungo una guida circolare di raggio $R$ e centro $(x_0, y_0)$.

1. **Vincolo nello spazio operativo:**
   $$k(r) = (x - x_0)^2 + (y - y_0)^2 - R^2 = 0$$

2. **Conversione nello spazio dei giunti:**
   Sostituendo la cinematica diretta $r = \begin{bmatrix} x(q) \\ y(q) \end{bmatrix} = f(q)$, si esprime la funzione di vincolo $h(q)$:
   $$h(q) = (x(q) - x_0)^2 + (y(q) - y_0)^2 - R^2 = 0$$

3. **Calcolo dello Jacobiano del vincolo $A(q)$:**
   $$A(q) = \frac{\partial h(q)}{\partial q} = 2(x(q) - x_0)\frac{\partial x(q)}{\partial q} + 2(y(q) - y_0)\frac{\partial y(q)}{\partial q}$$

Inserendo $A(q)$ nelle formule generali di proiezione ($P_B$ e $A_B^\dagger$), è possibile simulare con accuratezza la dinamica vincolata del robot tenendo conto delle reazioni vincolari $A^T(q)\lambda$ che insorgono lungo la circonferenza.

---

3. Modello Dinamico Ridotto e Pseudo-Velocità

#### Formulazione Matematica dello Spazio Ridotto

Quando un sistema meccanico presenta $n$ coordinate generalizzate e $m$ vincoli cinematici del tipo $A(q)\dot{q} = 0$, la sua dinamica effettiva possiede $n - m$ gradi di libertà liberi. Dal punto di vista matematico ed esecutivo (ad esempio negli ambienti di simulazione interattiva come MuJoCo), è vantaggioso descrivere l'evoluzione del sistema direttamente nello spazio a dimensione ridotta $(n - m)$.

L'obiettivo è definire un nuovo insieme di variabili prive di vincoli, dette **Pseudo-velocità** (*Pseudo-velocities*), con dimensione $n - m$.

La matrice dei vincoli $A(q) \in \mathbb{R}^{m \times n}$ ha rango pieno $m$ (con $m < n$). Per completare la base dello spazio delle velocità, introduciamo una matrice arbitraria $D(q) \in \mathbb{R}^{(n-m) \times n}$ tale per cui la matrice blocco risultante sia quadrata $(n \times n)$ e non singolare (invertibile):

$$\begin{bmatrix} A(q) \\ D(q) \end{bmatrix} \in \mathbb{R}^{n \times n}$$

Poiché la matrice è invertibile, possiamo definire la sua inversa partizionandola in due matrici $E(q) \in \mathbb{R}^{n \times m}$ ed $F(q) \in \mathbb{R}^{n \times (n-m)}$:

$$\begin{bmatrix} A(q) \\ D(q) \end{bmatrix}^{-1} = \begin{bmatrix} E(q) & F(q) \end{bmatrix}$$

Dalla proprietà dell'inversa $\begin{bmatrix} A \\ D \end{bmatrix} \begin{bmatrix} E & F \end{bmatrix} = I_n$, discendono direttamente le seguenti identità algebriche fondamentali:

$$A E = I_m, \quad D F = I_{n-m}, \quad A F = 0_{m \times (n-m)}, \quad D E = 0_{(n-m) \times m}$$

> **Concetto Chiave**: La matrice $F(q)$ proietta qualsiasi vettore nello spazio nullo (*null space*) della matrice dei vincoli $A(q)$, poiché $A(q)F(q) = 0$.

Definiamo il vettore delle **pseudo-velocità** $v \in \mathbb{R}^{n-m}$ come:

$$v = D(q)\dot{q}$$

Sfruttando la matrice inversa, la relazione fondamentale che lega le velocità generalizzate reali $\dot{q}$ alle pseudo-velocità $v$ è data da:

$$\dot{q} = E(q)A(q)\dot{q} + F(q)D(q)\dot{q}$$

Essendo il vincolo rispettato ($A(q)\dot{q} = 0$), l'equazione si riduce a:

$$\dot{q} = F(q)v$$

Derivando rispetto al tempo, otteniamo l'espressione per le accelerazioni generalizzate:

$$\ddot{q} = F(q)\dot{v} + \dot{F}(q)v$$

Sebbene le pseudo-velocità $v$ possano non avere un significato fisico diretto, esse garantiscono una descrizione equivalente ed esatta del sistema nello spazio a dimensione ridotta.

---

#### Derivazione della Dinamica Ridotta

Sostituiamo le espressioni di $\dot{q}$ e $\ddot{q}$ all'interno dell'equazione dinamica completa del robot con vincoli:

$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = u + A^T(q)\lambda$$

$$B(q)(F\dot{v} + \dot{F}v) + C(q,\dot{q})F v + g(q) = u + A^T(q)\lambda$$

Moltiplichiamo a sinistra l'intera equazione per $F^T(q)$:

$$F^T B F \dot{v} + F^T (B \dot{F} v + C F v + g) = F^T u + F^T A^T \lambda$$

Poiché $A F = 0$, si ha che $F^T A^T = (A F)^T = 0$. Di conseguenza, il termine legato alle forze vincolari $A^T \lambda$ **scompare completamente** dall'equazione proiettata.

Il sistema dinamico ridotto assume la forma:

$$B_v(q)\dot{v} + n_v(q,v) = F^T(q)u$$

Dove:
*   $B_v(q) = F^T(q) B(q) F(q) \in \mathbb{R}^{(n-m) \times (n-m)}$ rappresenta la **Matrice d'Inerzia Ridotta** (*Reduced Inertia Matrix*).
*   $n_v(q,v) = F^T(q)\left(B(q)\dot{F}(q)v + C(q,Fv)F(q)v + g(q)\right) \in \mathbb{R}^{n-m}$ raccoglie i termini di Coriolis, centrifughi e di gravità.

> **Concetto Chiave**: Risolvere ed integrare la dinamica sul sistema ridotto $(n-m)$ richiede un carico computazionale notevolmente inferiore rispetto al sistema dinamico completo $n$-dimensionale con vincoli espliciti.

I moltiplicatori di Lagrange $\lambda$ (ovvero le forze di contatto) possono essere calcolati in modo indipendente premoltiplicando l'equazione dinamica per $E^T(q)$.

---

#### Applicazione Pratica: Punto Materiale Vincolato

Riconsideriamo l'esempio semplice del punto materiale di massa $m$ in un piano 2D ($n=2$), vincolato a muoversi lungo l'asse orizzontale ($m=1$).

*Coordinate generalizzate*: $q = [x, y]^T$, $\dot{q} = [\dot{x}, \dot{y}]^T$.
*Vincolo*: $y = c \implies \dot{y} = 0 \implies A = [0 \quad 1]$.

Per completare la matrice, scegliamo $D = [1 \quad 0]$ (*Nota: Passaggio integrato con chiarezza didattica*):

$$\begin{bmatrix} A \\ D \end{bmatrix} = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix}$$

L'inversa di questa matrice è:

$$\begin{bmatrix} A \\ D \end{bmatrix}^{-1} = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} = \begin{bmatrix} E & F \end{bmatrix} \implies E = \begin{bmatrix} 0 \\ 1 \end{bmatrix}, \quad F = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$$

La pseudo-velocità risulta:

$$v = D\dot{q} = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} \dot{x} \\ \dot{y} \end{bmatrix} = \dot{x}$$

In questo caso specifico, la pseudo-velocità ha un significato fisico immediato: rappresenta la velocità lungo il grado di libertà libero ($x$).

Applicando la proiezione con $F = [1 \quad 0]^T$ e sapendo che la matrice d'inerzia è $B = \text{diag}(m, m)$:

$$B_v = F^T B F = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} m & 0 \\ 0 & m \end{bmatrix} \begin{bmatrix} 1 \\ 0 \end{bmatrix} = m$$

L'equazione dinamica ridotta monodimensionale risulta semplicemente:

$$m \ddot{x} = u_1$$

Mentre la componente della forza vincolare si ricava univocamente come $\lambda = -f_y$. I risultati concordano perfettamente con l'analisi dinamica elementare.

---

### Disaccoppiamento del Controllo e Linearizzazione per Retroazione

L'espressione del modello ridotto e la formulazione esplicita dei moltiplicatori di Lagrange $\lambda$ suggeriscono una tecnica di progettazione del controllo basata su **Linearizzazione per Retroazione** (*Feedback Linearization*).

Progettando opportunamente l'ingresso di controllo $u$, è possibile disaccoppiare completamente il problema in due sotto-problemi indipendenti:
1. **Controllo del Movimento** (*Motion Control*): gestione della traiettoria lungo i gradi di libertà liberi (spaziatura tangente al vincolo).
2. **Controllo della Forza** (*Force Control*): modulazione della forza esercitata perpendicolarmente contro l'ambiente.

$$\begin{cases} \dot{v} = a_v \quad \text{(Doppio integratore per il movimento)} \\ \lambda = f_\lambda \quad \text{(Controllo algebrico/dinamico della forza)} \end{cases}$$

#### Esempi Applicativi Reali

*   **Scrittura con una Penna:**
    *   L'azione $u_1$ modula la velocità di scorrimento del tratto della penna lungo la superficie del foglio.
    *   L'azione $u_2$ modula la forza di pressione verticale applicata dalla punta sul foglio per garantire il rilascio dell'inchiostro senza spezzare la mina.

*   **Lavorazione Meccanica / Asportazione Truciolo (*Material Removal*):**
    *   L'azione $u_1$ controlla la velocità di avanzamento dell'utensile lungo il profilo del pezzo.
    *   L'azione $u_2$ regola la forza normale di contatto necessaria a penetrare il materiale secondo la legge tecnologica dell'utensile.

---

### Estensione a Contatti Cedevoli (Cenni)

Nelle trattazioni precedenti si è ipotizzata una rigidità infinita sia per la struttura del robot sia per l'ambiente circostante (*Infinitely Rigid Assumption*).

Nelle applicazioni reali, tuttavia, si riscontrano deformazioni elastiche. Per modellare tale dinamica, la teoria viene estesa introducendo componenti di **cedevolezza** (*Compliance Modeling*), rappresentate matematicamente come elementi elastici (molle e smorzatori) posizionati all'interfaccia di contatto. Questo approccio esteso consente di progettare schemi di controllo conformabili (*Compliant Control*) stabili anche in presenza di deformazioni strutturali o dell'ambiente.

---

> [!NOTE]
> ### Note per l'Esame e Avvisi del Docente
> - **Scadenza Homework**: L'ultimo homework ha come scadenza tassativa il 25 giugno (prima dell'inizio degli esami) e la consegna degli homework è opzionale.
> - **Argomenti NON richiesti all'esame**: La trattazione e derivazione teorica sulla dinamica vincolata (Lagrangiana aumentata e moltiplicatori di Lagrange) non fa parte degli argomenti d'esame.