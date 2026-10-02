# Assegnazione Homework su Modellistica e Traiettorie & Introduzione alle Strategie di Controllo Avanzate per Manipolatori

## Overview Didattica

In questa lezione vengono affrontati due blocchi principali: le linee guida per lo svolgimento del terzo *homework* pratico e un richiamo metodologico sulle architetture di controllo per manipolatori robotici. 

Gli argomenti principali trattati comprendono:
* **Modellistica Cinematica e Assegnazione dei Riferimenti:** Procedura corretta per l'assegnazione dei sistemi di riferimento (*frames*) ai giunti del manipolatore, fondamentale per derivare il modello cinematico senza ricorrere ad assunzioni arbitrarie.
* **Pianificazione di Traiettorie nello Spazio Giunti e Operativo:** Progettazione di una traiettoria rettilinea per l'organo terminale (*end-effector*) nello spazio operativo (*task space*) e sua conversione nello spazio giunti (*joint space*), rispettando i vincoli sulle velocità iniziali e finali nulle.
* **Analisi della Dinamica dell'Errore e Simulazione del Controllore:** Studio della risposta del sistema tramite un controllore a linearizzazione mediante feedback (*Feedback Linearization*). In questo contesto, l'evoluzione dell'errore ai giunti viene ridotta a un sistema di equazioni differenziali ordinarie del secondo ordine, permettendo di analizzare la convergenza del tracciamento (*trajectory tracking*) senza dover simulare la dinamica non lineare completa del robot.
* **Inquadramento delle Strategie di Controllo per Manipolatori:** Revisione sintetica dell'evoluzione delle tecniche di controllo, dal classico approccio a combinazione *Feedforward* + *Feedback* (a convergenza locale) alla linearizzazione esatta mediante feedback (a convergenza globale), introducendo la transizione verso il controllo adattativo (*Adaptive Control*).

---

3. Organizzazione degli Homework, Logistica e Scadenze

 In questa sezione vengono dettagliati i requisiti, la struttura dei punteggi e la logistica per il completamento dell'Homework 3 (e un cenno al successivo Homework 4). L'obiettivo dell'homework è consolidare la modellistica e il controllo di manipolatori robotici attraverso l'applicazione rigorosa delle metodologie viste a lezione.

---

#### Struttura dell'Homework 3 e Ripartizione dei Punti

L'homework è articolato in tre parti principali per un totale di **2 punti complessivi** (più eventuali bonus di qualità):

1. **Modellistica del Robot (1.0 Punto - Obbligatorio)**
2. **Pianificazione della Traiettoria (0.5 Punti)**
3. **Progetto del Controllo di Inseguimento Traiettoria (0.5 Punti - Opzionale)**

---

#### Parte 1: Modellistica Cinematica e Assegnazione dei Terna (1 Punto)

In questa prima parte si richiede di ricavare il modello cinematico/dinamico della struttura robotica fornita.

> **CONCETTO CHIAVE: Regole di Assegnazione dei Sistemi di Riferimento (Frame Assignment)**
>
> Per ottenere il punteggio pieno è **fondamentale** seguire la procedura sistematica per l'assegnazione delle terne di riferimento (*Frame Assignment*) spiegata a lezione (es. convenzione di Denavit-Hartenberg).
> 
> Non bisogna posizionare gli assi in modo arbitrario o causale: anche se una scelta di frame diversa genera un modello matematicamente valido, l'esercizio ha lo scopo di verificare la padronanza del metodo standard appreso nel corso. Sulla base dello storico degli anni precedenti, circa un terzo degli studenti sbaglia o personalizza l'assegnazione dei frame ottenendo 0 punti in questa parte.

---

#### Parte 2: Pianificazione della Traiettoria (0.5 Punti)

La seconda parte richiede di progettare una traiettoria nello spazio giunti (*Joint Space*) a partire da specifiche definite nello spazio operativo/cartesiano (*Operational Space*):

* **Geometria del moto:** L'organo terminale (*End-Effector*) deve muoversi lungo una **linea retta** nel piano cartesiano tra la configurazione iniziale e quella finale.
* **Tempistica e Condizioni al Contorno:** Il moto deve completarsi in un tempo $T = 5\text{ s}$, partendo da velocità nulla e arrivando a velocità nulla ($\dot{q}(0) = 0$ e $\dot{q}(T) = 0$).

##### Strategia Risolutiva Consigliata:
1. Progettare la traiettoria rettilinea nello spazio cartesiano per la posizione dell'organo terminale.
2. Mappare la traiettoria cartesiana nello spazio dei giunti $q(t)$ mediante l'uso della *Cinematica Inversa (Inverse Kinematics)*.
3. Considerare l'evoluzione cinematica: durante il movimento rettilineo dell'end-effector, i singoli giunti ruoteranno/trasleranno in modo non lineare, potendo richiedere inversioni di moto locali per mantenere la traiettoria cartesiana retta.

---

#### Parte 3: Progetto e Simulazione del Controllo di Inseguimento (0.5 Punti Opzionali)

In questa sezione opzionale, l'obiettivo è progettare un controllore di inseguimento traiettoria (*Trajectory Tracking Controller*) e verificare la sua capacità di far convergere l'errore a zero partendo da condizioni iniziali perturbate (es. posizionando il robot in una configurazione iniziale leggermente differente da quella nominale $q(0) \neq q_d(0)$).

> **CONCETTO CHIAVE: Semplificazione della Simulazione del Controllore**
>
> Per verificare la convergenza e mostrare il comportamento del controllore **NON è necessario implementare un modello dinamico non lineare completo del robot** (es. via simulazione dinamica complessa). 
> 
> Poiché il controllore a linearizzazione con reazione (*Feedback Linearization*) o ad inversione dinamica cancella idealmente le dinamiche non lineari del robot, la dinamica dell'errore a ciclo chiuso si riduce a un **sistema differenziale lineare del secondo ordine**.

##### Derivazione dell'Equazione dell'Errore:
*(Nota: Passaggio integrato con chiarezza didattica)*

Definiamo l'errore di posizione ai giunti come:
$$e(t) = q_d(t) - q(t)$$

Applicando la tecnica di linearizzazione esatta tramite reazione, l'evoluzione temporale dell'errore di giunto risponde alla seguente equazione differenziale matriciale del secondo ordine:

$$\ddot{e}(t) + K_d \dot{e}(t) + K_p e(t) = 0$$

Dove $K_p$ e $K_d$ sono le matrici dei guadagni propozionale e derivativo. 

Se le matrici dei guadagni vengono scelte diagonali:
$$K_p = \text{diag}(k_{p1}, k_{p2}), \quad K_d = \text{diag}(k_{d1}, k_{d2})$$

L'equazione matriciale del sistema si disaccoppia in equazioni differenziali scalari indipendenti per ciascun giunto $i$:

$$\ddot{e}_i(t) + k_{di} \dot{e}_i(t) + k_{pi} e_i(t) = 0, \quad i=1, 2$$

##### Cosa presentare nella soluzione:
* Risolvere analiticamente o numericamente le equazioni differenziali ordinarie (ODE) dell'errore lineare partendo dalla condizione iniziale perturbata $e(0) \neq 0$.
* Tracciare i grafici temporali dell'evoluzione dell'errore ai giunti $e_q(t) = [e_1(t), e_2(t)]^T$ o dell'errore nello spazio cartesiano $e_x(t), e_y(t)$.
* Mostrare l'effetto della variazione dei guadagni $K_p$ e $K_d$ sulle prestazioni di convergenza del sistema.

---

#### Valutazione Bonus e Criterii di Pulizia

Oltre ai 2 punti nominali, possono essere assegnati frazioni di punto bonus ($\sim 0.5$ punti casuali) per elaborati svolti in modo eccezionalmente chiaro, con grafici ben curati e privi di risposte generiche generate passivamente da strumenti di AI (es. ChatGPT).

---

#### Quadro delle Scadenze (Deadlines)

| Attività / Homework | Descrizione | Scadenza Concordata |
| :--- | :--- | :--- |
| **Homework 3** | Modellistica, Traiettoria e Controllo (con parte Adattativa) | **Fine Maggio** |
| **Homework 4** | Esercitazione numerica/simulazione | **Oltre Giugno** (dopo la fine delle lezioni) |

---

### Controllo Adattativo Basato su Traiettoria di Riferimento Modificata

Nelle lezioni precedenti abbiamo visto che l'approccio classico al progetto del controllore per un manipolatore robotico prevede una struttura a reazione e azione in avanti (*Feedforward + Feedback*). Tuttavia, la compensazione in avanti basata sulla sola traiettoria desiderata $(q_d, \dot{q}_d, \ddot{q}_d)$ garantisce unicamente la convergenza locale degli errori. Sebbene la *Feedback Linearization* consenta di ottenere una convergenza globale, essa richiede una conoscenza esatta dei parametri dinamici del sistema.

Quando la conoscenza del modello è approssimata, il controllo feedforward standard può mostrare buone prestazioni nell'inseguimento della velocità, ma genera spesso un errore a regime permanente (*offset*) sulla posizione. Per superare questa limitazione, si modifica la traiettoria nominale introducendo una **velocità di riferimento** modificata $q_r$.

#### Dalla Velocità Desiderata alla Velocità di Riferimento ($q_r$)

Definiamo l'errore di tracciamento della posizione come $\tilde{q} = q_d - q$. L'idea fondamentale è sommare alla velocità desiderata $\dot{q}_d$ un termine proporzionale all'errore di posizione:

$$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$

dove $\Lambda$ è una matrice guadagno diagonale e definita positiva.
Derivando rispetto al tempo, la corrispondente accelerazione di riferimento è:

$$\ddot{q}_r = \ddot{q}_d + \Lambda \dot{\tilde{q}}$$

Definiamo ora la variabile di errore composito $\sigma$ (ovvero l'errore di velocità di riferimento):

$$\sigma = \dot{q}_r - \dot{q} = \dot{\tilde{q}} + \Lambda \tilde{q}$$

Se $\sigma \to 0$, l'equazione differenziale $\dot{\tilde{q}} + \Lambda \tilde{q} = 0$ garantisce che anche l'errore di posizione $\tilde{q}$ e l'errore di velocità $\dot{\tilde{q}}$ convergano asintoticamente a zero.

---

### Caso Ideale: Analisi di Stabilità di Lyapunov con Modello Perfetto

Prima di affrontare l'adattamento parametrico, dimostriamo la convergenza globale considerando nota la dinamica del robot, descritta da:

$$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + F\dot{q} + g(q) = u$$

Sostituendo i termini desiderati con le grandezze di riferimento $(\dot{q}_r, \ddot{q}_r)$, proponiamo la seguente legge di controllo:

$$u = B(q)\ddot{q}_r + C(q, \dot{q})\dot{q}_r + F\dot{q}_r + g(q) + K_d \sigma$$

Sostituendo il controllo $u$ nell'equazione della dinamica del sistema, otteniamo la dinamica dell'errore a ciclo chiuso:

$$B(q)\dot{\sigma} + C(q, \dot{q})\sigma + F\sigma + K_d \sigma = 0$$

> **Concetto Chiave**: Dimostrare la stabilità attraverso la variabile composita $\sigma$ risulta matematicamente più semplice rispetto all'uso diretto delle variabili di stato classiche, e garantisce comunque la convergenza globale dello stato all'origine.

#### Dimostrazione della Stabilità (Lyapunov)

Scegliamo la seguente funzione candidata di Lyapunov $V(q, \sigma, \tilde{q})$:

$$V = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{q}^T H \tilde{q}$$

dove $H$ è una matrice simmetrica e definita positiva scelta opportunamente come $H = 2 \Lambda K_d$. Poiché $B(q)$ è la matrice d'inerzia (definito-positiva), $V > 0$ per tutti gli stati diversi dallo zero.

Calcoliamo la derivata temporale $\dot{V}$ lungo le traiettorie del sistema:

$$\dot{V} = \sigma^T B(q) \dot{\sigma} + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T H \dot{\tilde{q}}$$

Sostituendo la dinamica dell'errore $B(q)\dot{\sigma} = -C(q, \dot{q})\sigma - F\sigma - K_d \sigma$:

$$\dot{V} = \sigma^T \left( -C(q, \dot{q})\sigma - F\sigma - K_d \sigma \right) + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T H \dot{\tilde{q}}$$

Raggruppando i termini e sfruttando la proprietà di antisimmetria della matrice $(\dot{B} - 2C)$, per cui $\sigma^T (\dot{B}(q) - 2C(q,\dot{q})) \sigma = 0$:

$$\dot{V} = -\sigma^T K_d \sigma - \sigma^T F \sigma + \tilde{q}^T H \dot{\tilde{q}}$$

*Nota: Passaggio integrato con chiarezza didattica.*
Poiché $F$ rappresenta l'attrito (matrice semidefinita positiva) e scegliendo $H = 2 \Lambda K_d$ con un'opportuna semplificazione algebrica dei termini incrociati, la derivata si riduce alla forma semidefinita negativa:

$$\dot{V} \le -\sigma^T K_d \sigma \le 0$$

Applicando il **Principìo d'Invarianza di La Salle** (*LaSalle's Invariance Principle*), l'unico insieme invariante in cui $\dot{V} = 0$ è quello per cui $\sigma = 0$. Ciò implica direttamente che:

$$\lim_{t \to \infty} \tilde{q}(t) = 0 \quad \text{e} \quad \lim_{t \to \infty} \dot{\tilde{q}}(t) = 0$$

L'errore di inseguimento converge quindi globalmente a zero.

---

### Caso Reale: Controllo Adattativo in Presenza di Incertezza Parametrica

Nel caso reale non conosciamo con precisione i parametri fisici del sistema (masse, baricentri, inerzie, attriti). Sostituiamo le matrici reali con le loro stime nominali $\hat{B}, \hat{C}, \hat{F}, \hat{G}$.

Sfruttando la proprietà di linearità nei parametri dinamici (*Linearity in Parameters*), possiamo riscrivere il modello dinamico rispetto al **vettore dei parametri** $\pi \in \mathbb{R}^p$ e alla **matrice regressore** $Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \in \mathbb{R}^{n \times p}$:

$$B(q)\ddot{q}_r + C(q, \dot{q})\dot{q}_r + F\dot{q}_r + g(q) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \pi$$

> **Concetto Chiave**: Nel Regressore $Y$, le accelerazioni e le velocità di riferimento ($\ddot{q}_r, \dot{q}_r$) sostituiscono i termini desiderati, mentre lo stato reale del sistema ($q, \dot{q}$) continua ad essere utilizzato dove necessario. Identificare quali termini vadano sostituiti è un passaggio fondamentale nel progetto del controllore.

La legge di controllo effettivamente applicata diventa:

$$u = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi} + K_d \sigma$$

dove $\hat{\pi}$ è la stima istantanea dei parametri dinamici.
Definiamo l'errore di stima parametrica come:

$$\tilde{\pi} = \hat{\pi} - \pi$$

Sostituendo questa legge di controllo nella dinamica reale del robot, otteniamo la dinamica dell'errore perturbata dall'errore parametrico:

$$B(q)\dot{\sigma} + C(q, \dot{q})\sigma + F\sigma + K_d \sigma = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \tilde{\pi}$$

Se il vettore dei parametri stimati $\hat{\pi}$ rimanesse costante ($\hat{\pi} = \text{costante}$), la presenza di $\tilde{\pi} \neq 0$ impedirebbe all'errore di convergere a zero, confinando lo stato all'interno di un intorno dell'origine (un "tubo" di errore proporzionale all'incertezza parametrica).

---

### Legge di Adattamento dei Parametri e Stabilità Globale

Per garantire la convergenza dell'errore di tracciamento a zero, occorre aggiornare la stima $\hat{\pi}(t)$ dinamicamente lungo la traiettoria.

Estendiamo la funzione candidata di Lyapunov inserendo un termine quadratico legato all'errore parametrico:

$$V(q, \sigma, \tilde{q}, \tilde{\pi}) = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{q}^T H \tilde{q} + \frac{1}{2} \tilde{\pi}^T \Gamma^{-1} \tilde{\pi}$$

dove $\Gamma = \Gamma^T > 0$ è la matrice del **tasso di adattamento** (*adaptation rate matrix*).

Poiché i parametri reali del sistema $\pi$ sono costanti nel tempo, si ha $\dot{\tilde{\pi}} = \dot{\hat{\pi}}$. Calcolando la derivata temporale di $V$:

$$\dot{V} = -\sigma^T K_d \sigma + \tilde{\pi}^T Y^T \sigma + \tilde{\pi}^T \Gamma^{-1} \dot{\hat{\pi}}$$

Raggruppando i termini che dipendono dall'errore parametrico $\tilde{\pi}$:

$$\dot{V} = -\sigma^T K_d \sigma + \tilde{\pi}^T \left( Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma + \Gamma^{-1} \dot{\hat{\pi}} \right)$$

Per annullare l'effetto dell'incertezza parametrica sulla derivata di Lyapunov, imponiamo che il termine tra parentesi sia nullo:

$$Y^T \sigma + \Gamma^{-1} \dot{\hat{\pi}} = 0 \implies \dot{\hat{\pi}} = -\Gamma Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma$$

Sostituendo la **legge di adattamento** così progettata, la derivata della funzione di Lyapunov diventa:

$$\dot{V} = -\sigma^T K_d \sigma \le 0$$

#### Proprietà di Convergenza del Sistema Adattativo

1. **Errore di Inseguimento**: Poiché $\dot{V} \le 0$, le variabili $\sigma, \tilde{q}, \dot{\tilde{q}}$ sono limitate e conververgeranno asintoticamente a zero:

$$\lim_{t \to \infty} \tilde{q}(t) = 0, \quad \lim_{t \to \infty} \dot{\tilde{q}}(t) = 0$$

2. **Stima dei Parametri**: Poiché $\dot{V} \le 0$, anche l'errore parametrico $\tilde{\pi}$ rimane limitato. Poiché $\sigma \to 0$, la legge di aggiornamento assicura che $\dot{\hat{\pi}} \to 0$, il che significa che $\hat{\pi}$ converge a un valore costante.

> **Concetto Chiave**: L'obiettivo primario di questo schema di controllo adattativo è annullare l'errore di inseguimento della traiettoria ($\tilde{q} \to 0$), **non** garantire che la stima parametrica converga ai valori reali ($\hat{\pi} \to \pi$). La convergenza di $\hat{\pi} \to \pi$ si verifica solo se la traiettoria garantisce condizioni di *Eccitazione Persistente* (*Persistent Excitation*), ovvero se il regressore $Y$ ha rango pieno lungo la traiettoria. Se $\tilde{\pi} \in \text{ker}(Y)$, l'errore dinamico sarà comunque nullo anche con parametri stimati errati.

---

### Architettura dello Schema di Controllo Adattativo

L'architettura complessiva dell'algoritmo di controllo adattativo si compone di due loop integrati:
1. **Loop di Controllo in Tempo Reale**: Calcola il segnale di ingresso $u(t)$ basandosi sulle stime attuali $\hat{\pi}(t)$ e sullo stato del robot $(q, \dot{q})$.
2. **Loop di Adattamento**: Aggiorna continuamente il vettore delle stime parametriche $\hat{\pi}(t)$ integrando la legge differenziale $\dot{\hat{\pi}}$.

```
Traiettoria Desiderata (qd, qd_dot, qd_ddot)
       │
       ▼
 ┌───────────┐      qr_dot, qr_ddot      ┌─────────────────────┐
 ├─► q_ref ──┼──────────────────────────►│ Matrice Regressore  │
 │ └─────────┘                           │   Y(q,q_dot,qr,qr)  │
 │     ▲                                 └──────────┬──────────┘
 │     │ q, q_dot                                   │
 │     │                                            ▼
 │  ┌──┴────────┐   sigma   ┌───────────┐     Y^T * sigma     ┌─────────────────────┐
 │  │ Calcolo   ├──────────►│ Guadagno  ├────────────────────►│ Legge d'Adattamento │
 │  │   sigma   │           │    Kd     │                     │  pi_hat_dot = -Γ Yᵀσ│
 │  └──┬────────┘           └─────┬─────┘                     └──────────┬──────────┘
 │     ▲                          │                                      │
 │     │                          ▼                                      ▼
 │     │                       ┌──┴──┐                                ┌──┴──┐
 │     │                       │  +  │◄─── Y * pi_hat ───────────────┤ ∫dt │ (pi_hat)
 │     │                       └──┬──┘                                └─────┘
 │     │                          │ u(t)
 │     │                          ▼
 │     │                   ┌─────────────┐
 └─────┴───────────────────┤ Manipolatore│
        q, q_dot           │   (Robot)   │
                           └─────────────┘
```

#### Passaggi Logici di Calcolo:

1. **Generazione dei Segnali di Riferimento**:
   $$\tilde{q} = q_d - q$$
   $$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$
   $$\ddot{q}_r = \ddot{q}_d + \Lambda \dot{\tilde{q}}$$
   $$\sigma = \dot{q}_r - \dot{q}$$

2. **Aggiornamento dei Parametri**:
   $$\dot{\hat{\pi}}(t) = -\Gamma Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma$$
   $$\hat{\pi}(t) = \hat{\pi}(0) + \int_{0}^{t} \dot{\hat{\pi}}(\tau) \, d\tau$$

3. **Calcolo della Legge di Controllo**:
   $$u(t) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi}(t) + K_d \sigma(t)$$

---

### Esempio Pratico di Controllo Adattativo: Il Pendolo Singolo (Single Link)

Per comprendere concretamente l'applicazione del controllo adattativo, consideriamo un sistema meccanico semplice: un pendolo ad un singolo braccio (Single Link Pendulum).

#### Modello Dinamico del Pendolo
La dinamica di questo sistema è descritta dalla seguente equazione differenziale non lineare:

$$I \ddot{\theta} + m g d \sin(\theta) + f_v \dot{\theta} = u$$

Dove:
*   $\theta$ rappresenta lo spostamento angolare del pendolo.
*   $\dot{\theta}$ e $\ddot{\theta}$ sono rispettivamente la velocità angolare e l'accelerazione angolare.
*   $I$ è il momento d'inerzia totale rispetto all'asse di rotazione. Per il teorema degli assi paralleli (Teorema di Huygens-Steiner), $I = I_G + m d^2$, dove $I_G$ è l'inerzia rispetto al baricentro, $m$ è la massa e $d$ è la distanza tra il baricentro e l'asse di rotazione.
*   $m g d \sin(\theta)$ è il termine gravitazionale.
*   $f_v$ è il coefficiente di attrito viscoso (Viscous Friction Coefficient).
*   $u$ è la coppia di controllo applicata (Total Applied Torque).

---

#### Parametrizzazione Lineare del Modello
Un ingrediente fondamentale per la progettazione del controllo adattativo è la **Parametrizzazione Lineare** (*Linear Parameterization*). Essa consiste nel separare le variabili di stato (note e misurabili) dai parametri dinamici incerti, esprimendo la dinamica nella forma:

$$Y(\theta, \dot{\theta}, \ddot{\theta}) \, \pi = u$$

Dove $Y$ è la **Matrice Regressore** (*Regressor Matrix*) e $\pi$ è il **Vettore dei Parametri Dinamici** (*Dynamic Parameter Vector*). 

Nel nostro caso specifico, possiamo riscrivere la dinamica come prodotto scalare tra il vettore riga $Y$ e il vettore colonna $\pi$:

$$Y(\theta, \dot{\theta}, \ddot{\theta}) = \begin{bmatrix} \ddot{\theta} & \sin(\theta) & \dot{\theta} \end{bmatrix}$$

$$\pi = \begin{bmatrix} I \\ m g d \\ f_v \end{bmatrix}$$

Moltiplicando $Y \cdot \pi$, riotteniamo esattamente l'equazione del moto:

$$I \ddot{\theta} + m g d \sin(\theta) + f_v \dot{\theta} = u$$

---

#### Progettazione della Legge di Controllo Adattativo
Ipotizziamo di non conoscere esattamente i parametri dinamici reali ($I$, $m g d$, $f_v$), ma di disporre solo di una loro stima al tempo attuale, indicata con $\hat{\pi} = \begin{bmatrix} \hat{I} & \widehat{m g d} & \hat{f}_v \end{bmatrix}^T$.

Per prima cosa definiamo le variabili di riferimento e gli errori:
1.  **Errore di Posizione**: $e = \theta_d - \theta$ (dove $\theta_d$ è la traiettoria desiderata).
2.  **Velocità Virtuale di Riferimento**: $\dot{\theta}_r = \dot{\theta}_d + \lambda e$, con $\lambda = \frac{K_p}{K_d} > 0$ (rapporto tra i guadagni proporzionale e derivativo).
3.  **Segnale Filtrato d'Errore (o di Scorrimento)**: $\sigma = \dot{\theta}_r - \dot{\theta} = \dot{e} + \lambda e$.

La legge di controllo effettivamente implementata si basa sul regressore valutato lungo le variabili di riferimento $Y_r$:

$$Y_r = Y(\theta, \dot{\theta}, \dot{\theta}_r, \ddot{\theta}_r) = \begin{bmatrix} \ddot{\theta}_r & \sin(\theta) & \dot{\theta}_r \end{bmatrix}$$

> **Nota**: Il termine posizionale $\sin(\theta)$ non varia nel regressore di riferimento poiché lo stato della posizione $\theta$ è direttamente misurato e non necessita di stima o sostituzione virtuale.

La **Coppia di Controllo** ($u$) risulta quindi composta da un termine di compensazione dinamica basato sui parametri stimati e da un termine d'azione proporzionale-derivativa ($K_D \sigma$):

$$u = Y_r \hat{\pi} + K_D \sigma = \hat{I} \ddot{\theta}_r + \widehat{m g d} \sin(\theta) + \hat{f}_v \dot{\theta}_r + K_D \sigma$$

---

#### Legge di Aggiornamento dei Parametri (Adaptation Law)
I parametri stimati devono variare nel tempo per ridurre l'errore di inseguimento. La legge di adattamento per la derivata temporale della stima dei parametri $\dot{\hat{\pi}}$ è definita come:

$$\dot{\hat{\pi}} = K_\pi^{-1} Y_r^T \sigma$$

Dove $K_\pi^{-1}$ è una matrice diagonale di guadagni di adattamento definiti positivi:

$$K_\pi^{-1} = \begin{bmatrix} \gamma_1 & 0 & 0 \\ 0 & \gamma_2 & 0 \\ 0 & 0 & \gamma_3 \end{bmatrix}$$

Esplicitando i calcoli vettore-matrice:

$$\begin{bmatrix} \dot{\hat{I}} \\ \dot{\widehat{m g d}} \\ \dot{\hat{f}}_v \end{bmatrix} = \begin{bmatrix} \gamma_1 & 0 & 0 \\ 0 & \gamma_2 & 0 \\ 0 & 0 & \gamma_3 \end{bmatrix} \begin{bmatrix} \ddot{\theta}_r \\ \sin(\theta) \\ \dot{\theta}_r \end{bmatrix} \sigma$$

*Nota: Passaggio integrato con chiarezza didattica per mostrare l'aggiornamento dei singoli parametri:*

$$\begin{cases} \dot{\hat{I}} = \gamma_1 \ddot{\theta}_r \sigma \\ \dot{\widehat{m g d}} = \gamma_2 \sin(\theta) \sigma \\ \dot{\hat{f}}_v = \gamma_3 \dot{\theta}_r \sigma \end{cases}$$

---

### Analisi delle Prestazioni ed Esempi Simulativi

Analizziamo il comportamento del sistema sotto due diverse traiettorie desiderate $\theta_d(t)$, partendo dalle condizioni iniziali $\theta(0) = 0$ e $\dot{\theta}(0) = 0$.

*   **Errore Posizionale Iniziale**: $e(0) = \theta_d(0) - \theta(0) = 0$.
*   **Errore di Velocità Iniziale**: Se la traiettoria desiderata parte con velocità $\dot{\theta}_d(0) = -1$, si ha $\dot{e}(0) = -1 - 0 = -1$. Questo spiega perché nelle simulazioni grafiche l'errore di velocità parte da $-1$ mentre quello di posizione parte da $0$.

#### Caso 1: Traiettoria Desiderata Sinusoidale
Forniamo al controller una traiettoria posizionale di tipo sinusoidale: $\theta_d(t) = -\sin(t)$.

*   **Inseguimento della traiettoria**: Entrambi gli errori (posizione e velocità) convergono asintoticamente a zero ($e \to 0, \dot{e} \to 0$). Questo rispetta la stabilità garantita dalla teoria.
*   **Stima dei parametri**: Guardando l'evoluzione dei parametri stimati $\hat{\pi}$, osserviamo che la derivata $\dot{\hat{\pi}} \to 0$, il che significa che i parametri stabili si assestano su dei valori costanti. **Tuttavia, i parametri stimati non convergono ai valori reali del sistema!** Soltanto un parametro ($\hat{f}_v$) giunge vicino al valore reale, mentre gli altri due convergono a valori errati che si autocompensano reciprocamente nel modello.

#### Caso 2: Traiettoria ad Accelerazione Bang-Bang
Forniamo ora una traiettoria in cui l'accelerazione desiderata ha un profilo ad onda quadra ("Bang-Bang"), oscillando tra $+1$ e $-1$ a frequenza costante.

*   **Inseguimento della traiettoria**: Come nel caso precedente, la posizione e la velocità convergono perfettamente a zero.
*   **Stima dei parametri**: In questo caso, **tutti e tre i parametri stima $\hat{\pi}$ convergono esattamente ai valori reali del sistema ($\pi$)**.

---

### Eccitazione Persistente (Persistent Excitation)

> **CONCETTO CHIAVE: Eccitazione Persistente**
> Per garantire che i parametri stimati $\hat{\pi}$ convergano ai loro **valori fisici reali** $\pi$ (e non solo a valori numerici che annullano l'errore di tracciamento), la traiettoria desiderata deve essere **Persistentemente Eccitante** (*Persistently Exciting*).
> 
> *   Una **traiettoria sinusoidale pura** eccita un range troppo limitato di frequenze della dinamica non lineare del sistema. Di conseguenza, il controller "si accontenta" di una combinazione di parametri errati ma sufficiente a compensare la specifica sinusoide.
> *   Una **traiettoria Bang-Bang** (o ricca di armoniche e sbalzi di accelerazione) sollecita l'intera dinamica del sistema. Questo contenuto spettrale più ampio costringe il regressore $Y_r$ ad esplorare lo spazio degli stati in modo completo, eliminando le ambiguità e garantendo la convergenza di $\hat{\pi} \to \pi$.

Nel caso di sistemi non lineari, determinare analiticamente se una traiettoria sia persistentemente eccitante è un compito complesso; tuttavia, il principio generale rimane legato alla "ricchezza" in frequenza del segnale di riferimento.

---

### Estensione: Conoscenza Parziale dei Parametri

Se conosciamo a priori con esattezza alcuni parametri dinamici (ad esempio se il coefficiente d'attrito $f_v$ è noto con precisione), non è necessario adattarli.

Possiamo suddividere la parametrizzazione lineare in due parti distinte:

$$Y \pi = Y_u \pi_u + Y_k \pi_k$$

Dove:
*   $\pi_u$ (Uncertain Parameters): Vettore dei parametri incerti da adattare.
*   $Y_u$: Parte del regressore associata ai parametri incerti.
*   $\pi_k$ (Known Parameters): Vettore dei parametri noti con precisione.
*   $Y_k$: Parte del regressore associata ai parametri noti.

La legge di controllo si riduce a:

$$u = Y_{u,r} \hat{\pi}_u + Y_{k,r} \pi_k + K_D \sigma$$

L'adattamento sarà applicato unicamente alla componente incerta $\dot{\hat{\pi}}_u = K_{\pi,u}^{-1} Y_{u,r}^T \sigma$. 

**Vantaggio pratico**: Riducendo il numero di parametri incerti da stimare in tempo reale, si ottiene una dinamica transitoria decisamente più rapida e una velocità di convergenza dell'errore nettamente superiore.

---

### Introduzione all'Asservimento Visivo (Visual Servoing)

Fino a questo momento abbiamo analizzato schemi di controllo ipotizzando di poter misurare direttamente le grandezze fisiche del robot, come le posizioni e le velocità articolari (al livello dei giunti) o le rispettive grandezze nello spazio cartesiano.

Rimuoviamo ora questa ipotesi e consideriamo uno scenario in cui le informazioni di feedback provengono primariamente da sistemi visivi (telecamere). Il controllo di un robot guidato da misurazioni visive prende il nome di **Asservimento Visivo (*Visual Servoing*)**.

```
[Mondo Reale / Robot] ---> [Telecamera] ---> [Estrazione Feature (2D)] ---> [Calcolo Errore] ---> [Controllo]
```

#### Gestione del Flusso Dati e Riduzione dell'Informazione
Quando si acquisisce un flusso video in tempo reale, non è computazionalmente sostenibile né utile salvare ed elaborare l'intera immagine pixel per pixel per ogni frame. I sistemi di controllo richiedono risposte rapide, pertanto è necessario elaborare l'immagine al volo tramite algoritmi di elaborazione delle immagini (*Image Processing*) per estrarre solo una sintesi di informazione utile al compito (task).

> **Esempio:** Se il robot deve inseguire una sfera verde, l'immagine ad alta risoluzione viene convertita in una mappa binaria (in bianco e nero) dove risalta solo l'oggetto di interesse. Da questa mappa si estraggono pochi parametri sintetici: le coordinate 2D del centro della sfera e il suo raggio nel piano immagine.

---

### Classificazione degli Approcci di Controllo Visivo

Esistono due modalità fondamentali per progettare uno schema di *Visual Servoing*:

#### 1. Position-Based Visual Servoing (PBVS)
Nel controllo basato sulla posizione (3D):
1. Si utilizzano le immagini acquisite da una o più telecamere (ad esempio tramite tecniche di **Visione Stereoscopica (*Stereo Vision*)**) per ricostruire la posa tridimensionale dell'end-effector rispetto a un sistema di riferimento del mondo/base.
2. Una volta stimata la posa 3D $\boldsymbol{x}$, si calcola l'errore nello spazio cartesiano 3D rispetto alla posa desiderata $\boldsymbol{x}_d$.
3. Si applicano direttamente le leggi di controllo cartesiano già studiate.

Poiché la ricostruzione 3D attiene ai corsi di *Computer Vision* avanzata, questo approccio non costituisce l'elemento di novità principale di questa sezione del corso.

#### 2. Image-Based Visual Servoing (IBVS)
Nel controllo basato sull'immagine (2D):
1. Non si effettua la ricostruzione tridimensionale della scena.
2. Si proietta la configurazione desiderata dell'end-effector direttamente sul **Piano Immagine (*Image Plane*)** a due dimensioni della telecamera.
3. Si definisce l'errore di inseguimento/posizionamento direttamente nel piano 2D dell'immagine come differenza tra la posizione delle caratteristiche visive attuali e quelle desiderate.
4. Si progetta la legge di controllo per far evolvere il robot lavorando direttamente nello spazio immagine $\mathbb{R}^2$.

> **CONCETTO CHIAVE: Differenza Fondamentale tra PBVS e IBVS**
> * **PBVS:** Immagine 2D $\rightarrow$ Ricostruzione Posizione 3D $\rightarrow$ Calcolo Errore 3D $\rightarrow$ Controllo Spazio Cartesiano.
> * **IBVS:** Immagine 2D $\rightarrow$ Estrazione Caratteristiche 2D $\rightarrow$ Calcolo Errore 2D direttamente nel Piano Immagine $\rightarrow$ Controllo Spazio Immagine.
> 
> La vera sfida scientifica e metodologica di questa parte del corso consiste nel riprogettare le leggi di controllo (es. approcci basati su Lyapunov) affinché lavorino direttamente sulle misurazioni 2D del piano immagine.

---

### Caratteristiche dell'Immagine e Modellazione Semplificata

In un algoritmo di *Visual Servoing*, l'informazione estratta prende il nome di **Caratteristica dell'Immagine (*Image Feature*)**, a cui sono associati dei **Parametri della Caratteristica (*Feature Parameters*)**:
* Se la *feature* è un punto, il parametro è dato dalle sue coordinate 2D $(u, v)$ sul piano immagine.
* Se la *feature* è una linea, i parametri possono essere il coefficiente angolare e l'intercetta.
* Se si tratta di una regione, si possono considerare i momenti geometrici o i componenti principali.

#### Ipotesi di Lavoro per il Corso
Per mantenere l'analisi matematica accessibile e concentrarci sulla sintesi del controllo:
1. Assumeremo che gli algoritmi di visione estraggano e traccino esclusivamente **punti di interesse visivi (*Point Features*)**.
2. Indicheremo con il vettore $\boldsymbol{s} \in \mathbb{R}^{2k}$ l'insieme delle coordinate 2D dei $k$ punti tracciati nel piano immagine.

---

### Configurazioni Hardware del Sistema Visivo

A seconda della collocazione fisica della telecamera rispetto alla struttura cinematica del manipolatore, si identificano tre architetture principali:

1. **Eye-in-Hand (Telecamera sul Manipolatore):**
   La telecamera è solidale con l'end-effector del robot e si muove nello spazio insieme ad esso. Le immagini cambiano continuamente in funzione del movimento del robot.

2. **Eye-to-Hand / Eye-off-Hand (Telecamera Fissa nell'Ambiente):**
   La telecamera è posizionata in un punto fisso dell'ambiente di lavoro ed inquadra sia il robot che l'oggetto da manipolare.

3. **Configurazioni Ibride (*Hybrid Configurations*):**
   Sistemi complessi che combinano sia telecamere mobili montate sul manipolatore (*Eye-in-Hand*) che telecamere fisse nell'ambiente (*Eye-to-Hand*).

> **CONCETTO CHIAVE: Architettura di Riferimento del Corso**
> Nelle prossime lezioni faremo riferimento esclusivo alla configurazione **Single Eye-in-Hand** (singola telecamera montata sull'end-effector del robot).

---

### Anticipazione: Progettazione del Controllo IBVS tramite Lyapunov

L'obiettivo delle prossime lezioni sarà adattare i metodi di controllo basati sulla stabilità di Lyapunov al contesto IBVS. 

La funzione di Lyapunov candidata $V$ incorporerà tipicamente il contributo dell'energia cinetica del manipolatore e un termine quadratico associato all'errore calcolato nel piano immagine:

$$V(\boldsymbol{q}, \dot{\boldsymbol{q}}, \boldsymbol{e}) = \frac{1}{2} \dot{\boldsymbol{q}}^T \boldsymbol{B}(\boldsymbol{q}) \dot{\boldsymbol{q}} + U(\boldsymbol{e})$$

dove:
* $\boldsymbol{q}$ e $\dot{\boldsymbol{q}}$ sono le posizioni e velocità ai giunti.
* $\boldsymbol{B}(\boldsymbol{q})$ è la matrice di inerzia del manipolatore.
* $\boldsymbol{e} = \boldsymbol{s} - \boldsymbol{s}_d$ è l'errore espresso direttamente sul piano immagine tra le caratteristiche viste $\boldsymbol{s}$ e quelle desiderate $\boldsymbol{s}_d$.