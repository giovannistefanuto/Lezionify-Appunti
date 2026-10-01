# Controllo d'Interazione Robot-Ambiente e Modellistica nello Spazio Operativo

## Overview Didattica

Questa lezione si concentra sulla gestione dell'interazione meccanica tra il manipolatore robotico e l'ambiente circostante. L'obiettivo primario è definire una metodologia di controllo in grado di imporre una dinamica desiderata all'interfaccia di contatto, modellando la relazione tra l'organo terminale (*End-Effector*) e l'ambiente esterno come un sistema differenziale del secondo ordine equivalente a un **Sistema Massa-Molla-Smorzatore (*Mass-Spring-Damper System*)**. Tale approccio risulta particolarmente efficace nelle applicazioni in cui si desidera mantenere contenute le forze di contatto e le velocità d'impatto non sono troppo elevate.

Per comprendere e implementare questo tipo di controllo, la lezione ripercorre e formalizza i seguenti aspetti teorici fondamentali:

*   **Cinematica Differenziale e Dualità Forza/Velocità**: Richiamo della mappatura delle forze esterne dallo spazio dell'organo terminale allo spazio giunti tramite la trasposta della matrice Jacobiana.
*   **Jacobiano Geometrico vs. Jacobiano Analitico**: Analisi delle differenze tra le rappresentazioni delle velocità angolari ($\omega$, legata al *Geometric Jacobian*) e le derivate temporali degli angoli di Eulero ($\dot{\phi}$, legate all'*Analytical Jacobian*), con la relativa trasformazione delle forze generalizzate aggregate.
*   **Modellistica Dinamica nello Spazio Operativo (*Operational Space Dynamic Model*)**: Riformulazione delle equazioni del moto dal classico spazio giunti (*Joint Space*) allo spazio cartesiano/operativo. Questo cambio di prospettiva permette di descrivere la dinamica del robot direttamente dal punto di vista dell'organo terminale, semplificando la progettazione degli algoritmi di controllo d'interazione.

---

### Interazione con l'Ambiente e Dinamica Desiderata

Quando un manipolatore robotico entra in contatto con l'ambiente circostante, la sola gestione del moto nello spazio libero non è più sufficiente. L'obiettivo primario diventa la gestione della forza di contatto che si sviluppa all'organo terminale (*End-Effector*). 

Un approccio efficace per affrontare questa problematica consiste nell'imporre una **dinamica desiderata di interazione** tra l'end-effector e l'ambiente.

#### La scelta di un modello del secondo ordine
L'idea fondamentale è fare in modo che l'interazione meccanica tra robot e ambiente si comporti come un sistema differenziale del secondo ordine, tipicamente rappresentato dal classico modello **massa-smorzatore-molla** (*Mass-Spring-Damper*).

Le ragioni principali di questa scelta sono:
1. **Approssimazione fisica affidabile:** I sistemi del secondo ordine approssimano con ottima precisione una vasta gamma di problemi fisici e meccanici reali.
2. **Semplicità di controllo:** È ben noto come controllare in modo lineare un sistema del secondo ordine tramite le tecniche tradizionali della teoria del controllo.

> **Concetto Chiave**: L'approssimazione mediante una dinamica del secondo ordine funziona in modo ideale quando le **forze di contatto sono contenute** e la **velocità dell'end-effector durante l'interazione non è troppo elevata**. È una tecnica ampiamente utilizzata nelle applicazioni industriali e di robotica di servizio a contatto limitato.

---

### Richiami di Dinamica nel Spazio Giunti e Dualità Cinematica-Statica

Per comprendere come modellare il robot durante l'interazione, richiamiamo il modello dinamico nello *Spazio Giunti* (*Joint Space*). Trascurando per semplicità le attriti ai giunti, l'equazione del moto si scrive come:

$$M(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u - \tau_e$$

Dove:
* $q \in \mathbb{R}^n$ è il vettore delle coordinate giunto.
* $M(q)$ è la matrice di inerzia nello spazio giunti.
* $C(q, \dot{q})\dot{q}$ rappresenta i contributi centrifughi e di Coriolis.
* $g(q)$ è il vettore della gravità.
* $u$ è il vettore delle coppie applicate ai giunti.
* $\tau_e$ è il vettore delle coppie al giunto prodotte dalle forze esterne d'interazione.

#### Mapping delle forze esterne ai giunti
Se sull'end-effector agisce un vettore di forze e coppie esterne $F = \begin{bmatrix} f \\ \tau \end{bmatrix} \in \mathbb{R}^6$ (dove $f$ sono le forze lineari e $\tau$ i momenti), per il principio dei lavori virtuali possiamo traslare questa sollecitazione nello spazio dei giunti attraverso la trasposta della **Jacobiana Geometrica** (*Geometric Jacobian*) $J(q)$:

$$\tau_e = J^T(q) F$$

---

### Jacobiana Geometrica vs. Jacobiana Analitica

Nell'analisi delle velocità e delle forze dell'end-effector, occorre fare una distinzione fondamentale tra la rappresentazione geometrica e quella analitica.

1. **Velocità Geometrica ($v$):**
   $$v = \begin{bmatrix} \dot{p} \\ \omega \end{bmatrix} = J(q)\dot{q}$$
   Dove $\dot{p}$ è la velocità lineare e $\omega$ è la velocità angolare istantanea. La forza $F$ compie lavoro direttamente sulla velocità geometrica $v$.

2. **Velocità Analitica ($\dot{x}$):**
   $$x = \begin{bmatrix} p \\ \phi \end{bmatrix} \in \mathbb{R}^6, \quad \dot{x} = \begin{bmatrix} \dot{p} \\ \dot{\phi} \end{bmatrix} = J_A(q)\dot{q}$$
   Dove $p \in \mathbb{R}^3$ rappresenta la posizione dell'origine del *frame* dell'end-effector rispetto alla base, mentre $\phi \in \mathbb{R}^3$ è una terna di **Angoli di Eulero** (*Euler Angles*) che descrive l'orientamento.

> **Concetto Chiave**: La velocità angolare istantanea $\omega$ **non è** la derivata temporale diretta degli angoli di Eulero ($\omega \neq \dot{\phi}$). Esiste una matrice di trasformazione $T(\phi)$ tale per cui:
> $$\omega = T(\phi)\dot{\phi}$$

La relazione tra la Jacobiana Geometrica $J(q)$ e la **Jacobiana Analitica** (*Analytical Jacobian*) $J_A(q)$ è espressa da:

$$J_A(q) = T_A(x) J(q)$$

Di conseguenza, per la dualità forza-velocità, se vogliamo esprimere le forze esterne $F_A$ collegate alla rappresentazione di velocità analitica $\dot{x}$, la trasformazione equivalente delle forze esterne diventa:

$$J^T(q) F = J_A^T(q) F_A$$

Dove $F_A$ è la forza generalizzata associata allo stato operativo $x$.

---

### Modellistica Dinamica nello Spazio Operativo

Quando il robot interagisce con l'ambiente, è molto più conveniente esprimere le equazioni del moto direttamente dal punto di vista dell'organo terminale, ovvero nello **Spazio Operativo** (*Operational Space*).

Esprimiamo la dinamica rispetto al vettore di stato cartesiano $x = \begin{bmatrix} p^T & \phi^T \end{bmatrix}^T \in \mathbb{R}^6$.

#### Derivazione del modello dinamico cartesiano

 Partendo dalle relazioni cinematiche del secondo ordine:
$$\dot{x} = J_A(q)\dot{q} \implies \ddot{x} = J_A(q)\ddot{q} + \dot{J}_A(q, \dot{q})\dot{q}$$

Risolvendo per $\ddot{q}$:
$$\ddot{q} = J_A^{-1}(q)\left( \ddot{x} - \dot{J}_A(q, \dot{q})\dot{q} \right)$$

*(Nota: Passaggio integrato con chiarezza didattica - Si ipotizza che la Jacobiana sia quadrata e non singolare).*

Sostituendo $\ddot{q}$ nell'equazione dinamica ai giunti e premoltiplicando l'intero sistema per $J_A^{-T}(q)$, si ottiene il **Modello Dinamico nello Spazio Operativo**:

$$\Lambda(x)\ddot{x} + \mu(x, \dot{x})\dot{x} + p(x) = F_u - F_A$$

Dove le matrici dinamiche nello spazio operativo sono definite come:

* **Matrice di Inerzia nello Spazio Operativo** (*Operational Space Inertia Matrix*):
  $$\Lambda(x) = \left( J_A(q) M^{-1}(q) J_A^T(q) \right)^{-1}$$
* **Termine delle Forze Centrifughe e di Coriolis nello Spazio Operativo**:
  $$\mu(x, \dot{x}) = J_A^{-T}(q) C(q, \dot{q}) \dot{q} - \Lambda(x) \dot{J}_A(q, \dot{q}) \dot{q}$$
* **Vettore della Gravità nello Spazio Operativo**:
  $$p(x) = J_A^{-T}(q) g(q)$$
* **Forza Generalizzata di Controllo**:
  $$F_u = J_A^{-T}(q) u$$

Questa formulazione descrive l'effettivo comportamento dinamico che si manifesta al punto di contatto dell'end-effector, rendendo immediata la progettazione di algoritmi di controllo di forza o di impedenza.

---

### Progettazione della Legge di Controllo ad Impedenza (*Impedance Control Design*)

La progettazione di un controllo ad impedenza si articola principalmente in **due fasi logiche**:
1. **Linearizzazione mediante Reazione (*Feedback Linearization*)**: Si progetta un'azione di controllo $u$ (espressa in termini di forze/coppie) in funzione di un controllo ausiliario $a$, in modo da cancellare le nonlinearità della dinamica del robot e ridurre la relazione tra la variabile cartesiana $x$ e l'ingresso ausiliario $a$ a un doppio integratore ($\ddot{x} = a$).
2. **Imposizione della Dinamica di Impedenza Desiderata**: Si progetta il vettore ausiliario $a$ affinché la relazione dinamica tra il robot e l'ambiente sia governata da un'equazione differenziale del secondo ordine descritta dai parametri obiettivo di massa, smorzamento e rigidezza.

---

#### Passo 1: Linearizzazione mediante Reazione (*Feedback Linearization*)

Consideriamo la dinamica del robot espressa nello spazio operativo (cartesiano):

$$M_x(x)\ddot{x} + C_x(x, \dot{x})\dot{x} + g_x(x) - f_A = u_x$$

Dove:
* $M_x(x)$ è la matrice d'inerzia nello spazio cartesiano.
* $C_x(x, \dot{x})\dot{x}$ rappresenta i contributi di Coriolis e centrifughi.
* $g_x(x)$ è il vettore delle forze gravitazionali.
* $f_A$ è la forza d'interazione esercitata dall'ambiente sull'organo terminale (*End-Effector*).
* $u_x$ è la forza di controllo nello spazio cartesiano.

Per applicare la forza di controllo al livello dei giunti $u$ (coppie ai giunti $\tau$), si utilizza la matrice Jacobiana analitica trasposta $J_A^T(q)$: $u = J_A^T(q) u_x$.

Progettiamo la legge di controllo $u$ inserendo i termini per cancellare le dinamiche non lineari e le forze esterne, introducendo al contempo il comando ausiliario $a$:

$$u = J_A^T(q) \left( M_x(x)a + C_x(x, \dot{x})\dot{x} + g_x(x) - f_A \right)$$

> **Nota: Passaggio integrato con chiarezza didattica**  
> Sostituendo la legge di controllo $u$ nel modello dinamico del sistema, si nota immediatamente che i termini $C_x\dot{x}$, $g_x$ e $f_A$ si elidono a vicenda. Resta quindi l'uguaglianza $M_x(x)\ddot{x} = M_x(x)a$. Moltiplicando per $M_x^{-1}(x)$, si ottiene il doppio integratore perfetto:
> $$\ddot{x} = a$$

---

#### Passo 2: Progettazione dell'Ingresso Ausiliario $a$ per la Dinamica Obiettivo

Vogliamo che il comportamento del robot a contatto con l'ambiente rispetti il modello di impedenza target del secondo ordine:

$$M_d (\ddot{x} - \ddot{x}_d) + D_d (\dot{x} - \dot{x}_d) + K_d (x - x_d) = - f_A$$

Dove:
* $M_d$ è la matrice d'inerzia desiderata (*Target Inertia*).
* $D_d$ è la matrice di smorzamento desiderato (*Target Damping*).
* $K_d$ è la matrice di rigidezza desiderata (*Target Stiffness*).
* $x_d, \dot{x}_d, \ddot{x}_d$ rappresentano la traiettoria cartesiana desiderata (posizione, velocità e accelerazione).

Per ricavare la formulazione esplicita dell'ingresso ausiliario $a$, isoliamo l'accelerazione reale $\ddot{x}$ nell'equazione del modello target (ricordando che $\ddot{x} = a$):

$$M_d (\ddot{x} - \ddot{x}_d) = - D_d (\dot{x} - \dot{x}_d) - K_d (x - x_d) - f_A$$

Dividendo entrambi i membri per $M_d$ (ovvero pre-moltiplicando per l'inversa $M_d^{-1}$):

$$\ddot{x} - \ddot{x}_d = M_d^{-1} \left( D_d (\dot{x}_d - \dot{x}) + K_d (x_d - x) - f_A \right)$$

Poiché abbiamo imposto $\ddot{x} = a$, otteniamo la legge per l'ingresso ausiliario $a$:

$$a = \ddot{x}_d + M_d^{-1} \left( D_d (\dot{x}_d - \dot{x}) + K_d (x_d - x) - f_A \right)$$

---

### Regolazione dei Parametri e Intuizione Fisica (*Parameter Tuning*)

La progettazione dell'impedenza richiede la scelta di **quattro elementi fondamentali**:
1. Traiettoria desiderata ($x_d, \dot{x}_d, \ddot{x}_d$)
2. Inerzia desiderata ($M_d$)
3. Smorzamento desiderato ($D_d$)
4. Rigidezza desiderata ($K_d$)

#### Regole Pratiche per la Scelta dei Parametri

Consideriamo l'applicazione pratica di lavorazione superficiale (ad esempio scrittura, molatura o sformatura su una superficie metallica rigida):

> **Concetto Chiave: Strategia di Configurazione nei Compiti di Contatto**  
> Per garantire la continuità del contatto senza generare forze distruttive, si imposta una traiettoria desiderata $x_d$ che penetra **leggermente all'interno del materiale**. Il robot non raggiungerà mai fisicamente $x_d$, ma la deformazione virtuale agirà da generatore della forza di contatto.

I parametri dinamici vanno modulati differenziando il comportamento lungo le direzioni dello spazio:

* **Lungo le direzioni di contatto vincolato (normali alla superficie):**
  * **Inerzia grande ($M_d$ elevata):** Riduce le accelerazioni brusche al momento dell'impatto.
  * **Rigidezza piccola ($K_d$ ridotta):** Previene l'insorgere di forze di reazione eccessive dovute alla penetrazione della traiettoria nominale $x_d$ nell'ambiente rigido.
* **Lungo le direzioni di moto libero (tangenti alla superficie):**
  * **Inerzia piccola ($M_d$ ridotta):** Rende il robot pronto e reattivo ai comandi di movimento.
  * **Rigidezza grande ($K_d$ elevata):** Garantisce un elevato inseguimento di traiettoria (*Trajectory Tracking*) laddove non vi sono ostacoli.

```
                  [ Direzione di Moto Libero ]
                  - Massa Md: Piccola (Reattività)
                  - Rigidezza Kd: Grande (Inseguimento accurato)
                                │
                                ▼
 ──────────────┐   ┌──────────────────────────┐
  Organo       │───│  Traiettoria nominale xd │ (Impostata all'interno del materiale)
  Terminali    │   └──────────────────────────┘
 ──────────────┘                │
                                ▼
                  [ Direzione di Contatto Vincolato ]
                  - Massa Md: Grande (Attenuazione impatti)
                  - Rigidezza Kd: Piccola (Forze di contatto contenute)
 ──────────────────────────────────────────────────────── Dynamic Surface
```

#### Ruolo dello Smorzamento ($D_d$) e Rilevamento delle Forze
* **Smorzamento ($D_d$):** Serve principalmente a modellare la risposta transitoria del sistema. Un valore opportuno di smorzamento viscous evita sovraelongazioni (*overshoot*), riduce i tempi di assestamento (*settling time*) e previene instabilità o oscillazioni al momento dell'impatto.
* **Misura della Forza $f_A$:** Può avvenire tramite un sensore di forza/coppia (*Force/Torque Sensor*) montato al polso del robot oppure stimata indirettamente mediante tecniche basate su modelli (*Sensori Soft* / *Soft Sensors*).

---

### Casi Particolari e Relazione con il Controllo PD

#### Regolazione Statica e Compensazione di Gravità

Analizziamo cosa accade quando la traiettoria desiderata è statica ($x_d = \text{costante}$, quindi $\dot{x}_d = 0$ e $\ddot{x}_d = 0$) e non vi è alcuna interazione con l'ambiente ($f_A = 0$).

Sostituendo queste condizioni nella legge ausiliaria $a$:

$$a = M_d^{-1} \left( K_d (x_d - x) - D_d \dot{x} \right)$$

Sostituendo $a$ nella legge di controllo completa $u$:

$$u = J_A^T(q) \left( M_x(x) M_d^{-1} \left( K_d (x_d - x) - D_d \dot{x} \right) + C_x(x, \dot{x})\dot{x} + g_x(x) \right)$$

In assenza di moto significativo e impostando $M_d = M_x$, l'espressione si riduce a:

$$u = J_A^T(q) \left( K_d e - D_d \dot{x} \right) + g(q)$$

Dove $e = x_d - x$. Si nota chiaramente come il controllo ad impedenza in assenza di contatto e in condizione statica riconduca esattamente a un **Controllo PD nello spazio operativo con Compensazione di Gravità**.

#### Interazione con Ambiente Elastico

Quando il robot entra in contatto con un ambiente modellato come una molla pura con rigidezza $K_e$ e posizione di riposo $x_e$:

$$f_A = K_e (x - x_e)$$

In condizioni di equilibrio statico ($\ddot{x} = 0, \dot{x} = 0$), il sistema si assesta in una posizione di equilibrio $x_\infty$ che soddisfa l'uguaglianza tra la forza esercitata dall'impedenza del robot e la forza di reazione dell'ambiente:

$$K_d (x_d - x_\infty) = K_e (x_\infty - x_e)$$

Da cui si ricava la posizione finale di equilibrio:

$$x_\infty = (K_d + K_e)^{-1} (K_d x_d + K_e x_e)$$

---

### Introduzione al Controllo ad Ammettenza (*Admittance Control*)

Mentre il **Controllo ad Impedenza (*Impedance Control*)** accetta in ingresso variazioni di posizione/velocità e restituisce in uscita comandi di forza/coppia (richiedendo l'accesso diretto ai controllori di basso livello dei giunti), in molti contesti industriali questo accesso non è consentito.

I robot commerciali spesso accettano unicamente comandi di velocità o posizione al basso livello. In questi casi si ricorre al **Controllo ad Ammettenza (*Admittance Control*)**:

* **Principio di Funzionamento:** Il sensore di forza misura la forza esterna $f_A$ applicata dall'ambiente.
* **Integratore di Ammettenza:** La forza misurata viene passata a un modello dinamico (l'inverso dell'impedenza) che calcola la traiettoria modificata (velocità $\dot{x}$ o posizione $x$) da inviare al robot.
* **Controllo di Basso Livello:** L'architettura proprietaria del robot si occupa di inseguire la velocità/posizione calcolata ad altissima frequenza.

```
┌──────────┐  Forza f_A  ┌───────────────────────────┐  Traiettoria x_ref  ┌─────────────────────────────┐
│ Ambiente │────────────>│ Modello di Ammettenza     │───────────────────>│ Controllo di Basso Livello  │
└──────────┘             │ (Inverso dell'Impedenza)  │                    │ (Inseguimento Posiz./Vel.)  │
                         └───────────────────────────┘                    └─────────────────────────────┘
```

---

3. Interazione Robot-Ambiente: Modellazione dei Vincoli

Negli scenari in cui il robot entra in contatto con l'ambiente circostante, la presenza di ostacoli o superfici rigide limita la libertà di movimento dell'organo terminale (End-Effector). Per gestire e controllare la forza e il movimento durante il contatto, è necessario formalizzare i vincoli geometrici e cinematici che nascono da questa interazione.

#### Ipotesi di Lavoro e Ruolo della Retroazione

Prima di definire i vincoli, è opportuno chiarire le ipotesi di partenza per la modellazione:
* **Ambiente e Robot Perfettamente Rigidi:** Si ipotizza che non vi siano deformazioni strutturali durante il contatto.
* **Assenza di Attrito:** Il contatto viene modellato, in prima battuta, come privo di attrito (contatto ideale).

> **Concetto Chiave**  
> Sebbene nella realtà esistano attriti, deformabilità delle superfici ed errori di modello, progettiamo la strategia di controllo partendo da **condizioni ideali**. Questo approccio è giustificato dal fatto che la successiva implementazione di un **controllo in retroazione (Feedback Control)** permette di compensare efficacemente tutte le non-idealità e i disturbi non modellati presenti nel sistema reale.

---

#### La Terna di Riferimento del Compito (Task Frame)

Per descrivere l'interazione nel punto di contatto, si definisce una nuova terna di riferimento locale, detta **Terna del Compito (Task Frame)** e indicata con $RF_T$. 

* **Origine:** Posizionata esattamente nel punto di contatto tra l'end-effector e l'ambiente.
* **Natura dinamica:** Trattandosi di un punto che può muoversi nello spazio insieme al robot, $RF_T$ è una terna variante nel tempo (Time-varying frame).

Lungo i tre assi ortogonali di questa terna $(x, y, z)$, il sistema può scambiare con l'ambiente complessivamente **12 grandezze fisiche**:

1. **6 componenti di velocità** (Cinematica):
   * 3 velocità lineari: $v = [v_x, v_y, v_z]^T$
   * 3 velocità angolari: $\omega = [\omega_x, \omega_y, \omega_z]^T$
2. **6 componenti di forza/coppia** (Dinamica):
   * 3 forze lineari di reazione: $f = [f_x, f_y, f_z]^T$
   * 3 coppie di reazione: $m = [m_x, m_y, m_z]^T$

---

#### Vincoli Naturali e Vincoli Artificiali (Natural and Artificial Constraints)

L'interazione meccanica suddivide le 12 direzioni grafiche/spaziali in due insiemi fondamentali e complementari.

##### 1. Vincoli Naturali (Natural Constraints)
I vincoli naturali sono dettati esclusivamente dalla **geometria del contatto** e dalle proprietà fisiche dell'ambiente. Essi rappresentano ciò che l'ambiente "impone" spontaneamente al robot:

* **Sottoinsieme Velocità ($6-k$ direzioni):** Comprende le direzioni (lineari o rotazionali) lungo le quali il **movimento è impedito** dalla presenza fisica dell'ambiente. In tali direzioni, la velocità è vincolata a zero dalle reazioni vincolari.
* **Sottoinsieme Forze/Coppie ($k$ direzioni):** Comprende le direzioni lungo le quali **non vi sono reazioni di forza o coppia** da parte dell'ambiente (direzioni "libere" di movimento).

##### 2. Vincoli Artificiali (Artificial Constraints)
I vincoli artificiali rappresentano le specifiche del **compito desiderato (Task)** impostato dall'utente/progettista. Essi definiscono come il robot *deve* comportarsi nelle direzioni lasciate libere o vincolate dall'ambiente:

* **Sottoinsieme Velocità ($k$ direzioni):** Comprende le direzioni in cui il **movimento è fisicamente possibile**. In queste direzioni si impone un profilo di velocità desiderato $v_d$ o $\omega_d$.
* **Sottoinsieme Forze/Coppie ($6-k$ direzioni):** Comprende le direzioni in cui l'ambiente oppone resistenza. In queste direzioni si impone un valore desiderato di forza $f_d$ o coppia $m_d$ da esercitare sull'ambiente.

> **Concetto Chiave**  
> I vincoli naturali e i vincoli artificiali sono **strettamente complementari**. Ciò che è un vincolo naturale di velocità (movimento impedito) diventa la sede di un vincolo artificiale di forza (possiamo decidere quanta forza esercitare contro la superficie). Viceversa, dove l'ambiente non offre reazione (forza naturale nulla), si impone un vincolo artificiale di velocità.

---

#### Esempio Pratico: Scorrimento di un Blocco su una Guida

Consideriamo un esempio concreto per chiarire le definizioni: un blocco cubico (end-effector) vincolato a scorrere all'interno di una guida scanalata rigida.

```
       z (perpendicolare)
       ^
       |   +-----+
       |   | Blocco |  ---> x (direzione guida)
       +---|-----+----------------->
      /    
     / y (laterale)
```

Fissiamo la terna $RF_T$ nel punto di contatto con gli assi orientati come segue:
* Asse $x$: parallelo alla direzione della guida (direzione di scorrimento).
* Asse $y$: ortogonale alla guida sul piano orizzontale.
* Asse $z$: ortogonale al piano di scorrimento (verticale).

##### Analisi dei Vincoli Naturali

1. **Velocità impedite (Geometria del sistema):**
   * $v_y = 0$: il blocco non può traslare lateralmente a causa delle pareti della guida.
   * $v_z = 0$: il blocco non può compenetrare la superficie verso il basso.
   * $\omega_x = 0$: la forma della guida impedisce il rollio attorno all'asse $x$.
   * $\omega_z = 0$: la forma della guida impedisce l'imbardata attorno all'asse $z$.
   *(Nota: $\omega_y$ non è impedita meccanicamente, poiché il blocco potrebbe teoricamente rullare attorno a $y$).*

2. **Assenza di Reazioni (Assunzione di assenza di attrito):**
   * $f_x = 0$: nessuna forza opposta lungo la guida (in assenza di attrito).
   * $m_y = 0$: nessuna coppia di reazione attorno all'asse $y$.

##### Analisi dei Vincoli Artificiali

Sulla base delle libertà residue, impostiamo le specifiche di controllo del robot:

1. **Velocità Desiderate (Controllate in velocità):**
   * $v_x = v_{d}$: vogliamo far scorrere il blocco lungo l'asse $x$ con una velocità specifica $v_d$.
   * $\omega_y = \omega_{y,d} = 0$: pur essendo fisicamente possibile ruotare attorno a $y$, imponiamo $\omega_y = 0$ perché il compito richiesto è lo *scorrimento puro* e non il rotolamento.

2. **Forze/Coppie Desiderate (Controllate in forza):**
   * $f_y = f_{y,d} = 0$: non vogliamo esercitare forze inutili contro le pareti laterali.
   * $f_z = f_{z,d}$: possiamo decidere di premere contro la superficie con una forza desiderata $f_{z,d}$ (ad esempio per un'operazione di lavorazione o asportazione truciolo).
   * $m_x = m_{x,d} = 0$ e $m_z = m_{z,d} = 0$: non si desidera applicare coppie di torsione lungo questi assi.

---

#### Parametrizzazione Matriciale e Principio di Ortogonalità

Per poter utilizzare questi concetti nella progettazione del controllore dinamico, è necessario esprimere i vincoli in forma matriciale compatta.

Definiamo il vettore delle velocità generali $V$ e il vettore delle forze/coppie generali $F$ nella terna $RF_T$:

$$V = \begin{bmatrix} v \\ \omega \end{bmatrix} \in \mathbb{R}^6, \quad F = \begin{bmatrix} f \\ m \end{bmatrix} \in \mathbb{R}^6$$

Possiamo parametrizzare le velocità ammissibili (articolate dai vincoli artificiali) e le forze di reazione dell'ambiente tramite due matrici di selezione, $D$ e $Y$:

1. **Matrice delle direzioni di movimento ammissibili ($D$):**
   Relaziona un vettore di velocità ridotto $v_a \in \mathbb{R}^k$ (in questo caso $k=2$, contenente le variabili indipendenti $v_x$ e $\omega_y$) con il vettore totale $V$:
   $$V = D \cdot v_a$$

2. **Matrice delle direzioni di reazione ($Y$):**
   Relaziona un vettore di forze/coppie di reazione ridotto $f_a \in \mathbb{R}^{6-k}$ (in questo caso $6-k=4$, relativo a $f_y, f_z, m_x, m_z$) con il vettore totale $F$:
   $$F = Y \cdot f_a$$

##### Il Principio dei Lavori Virtuali e l'Ortogonalità

In condizioni ideali (assenza di attrito), le forze di reazione vincolare $F$ non compiono lavoro virtuale lungo le direzioni di movimento consentite $V$. 

> **Concetto Chiave**  
> Le matrici $D$ e $Y$ descrivono spazi vettoriali **mutuamente ortogonali**. Matematicamente, questo si traduce nella relazione fondante:
> $$Y^T D = 0 \quad \text{oppure} \quad D^T Y = 0$$

Questa proprietà di ortogonalità è la pietra angolare della teoria del **Controllo Ibrido Forza/Posizione (Hybrid Force/Position Control)**: essa garantisce che il sottospazio del controllo di posizione e il sottospazio del controllo di forza siano del tutto disaccoppiati, consentendo di progettare i rispettivi loop di controllo in modo indipendente.

---

> [!NOTE]
> ### Note per l'Esame e Avvisi del Docente
> - L'argomento relativo all'interazione tra robot ed ambiente e ai vincoli naturali e artificiali (Natural and Artificial Constraints) è espressamente confermato come parte del programma richiesto per l'esame finale ("This is due for the final exam").