# Controllo nello Spazio Operativo: Algoritmo PD con Compensazione di Gravità

## Overview Didattica

In questa lezione viene introdotto il passaggio fondamentale dal controllo nello **Spazio dei Giunti** (*Joint Space*) al controllo nello **Spazio Operativo** (*Operational Space*). Tale cambio di prospettiva è essenziale quando si analizza l'interazione del robot con l'ambiente esterno, la quale avviene direttamente a livello dell'**Organo Terminale** (*End-Effector*).

I concetti chiave trattati includono:
* **Formulazione dell'Errore nello Spazio Operativo**: Definizione dell'errore di posa dell'end-effector $\tilde{x} = x_d - x$, abbandonando la dipendenza diretta dall'errore di giunto $\tilde{q}$.
* **Progettazione del Controllo PD con Compensazione di Gravità**: Estensione dell'algoritmo Proporzionale-Derivativo (*Proportional-Derivative Control - PD*) basato su un'opportuna **Funzione Candidata di Lyapunov** (*Lyapunov Candidate Function*).
* **Cinematica Differenziale e Giacobiano Analitico**: Utilizzo della relazione di cinematica differenziale mediante il **Giacobiano Analitico** (*Analytical Jacobian*) $J_A(q)$ per raccordare le velocità nello spazio operativo ($\dot{x}$) con le velocità nello spazio dei giunti ($\dot{q}$).
* **Analisi di Stabilità**: Derivazione della legge di controllo alle coppie di giunto $u$ tale da garantire che la derivata temporale della funzione di Lyapunov $\dot{V}$ sia semidefinita negativa, assicurando il raggiungimento della posa desiderata a velocità nulla ($\dot{q} = 0$).

---

### Introduzione al Controllo nello Spazio Operativo

Fino a questo momento, la maggior parte degli algoritmi di controllo è stata sviluppata nello **spazio dei giunti** (*joint space*), una scelta naturale dato che gli attuatori agiscono direttamente sui singoli giunti del manipolatore. 

Tuttavia, quando il robot deve svolgere compiti di **interazione con l'ambiente** (*interaction with the environment*), la variabile di interesse principale diventa la posizione e l'orientamento (posa) dell'**organo terminale** (*end-effector*). In questi contesti, è molto più efficace ed intuitivo progettare l'algoritmo di controllo direttamente sulla base dell'errore calcolato nello **spazio operativo** (*operational space*).

---

### Formulazione dell'Errore e Funzione Candidata di Lyapunov

Assumiamo che l'organo terminale debba raggiungere una posa desiderata costante $x_d$ nello spazio operativo. Definiamo l'errore di posa nello spazio operativo $\tilde{x}$ come:

$$\tilde{x} = x_d - x$$

dove $x$ rappresenta la posa attuale dell'organo terminale (espressa, ad esempio, mediante posizione e angoli di Eulero).

Per progettare una legge di controllo stazionario proporzionale-derivativa (PD) con compensazione del termine di gravità, proponiamo una **funzione candidata di Lyapunov** (*Lyapunov function candidate*) $V(q, \dot{q})$ espressa come somma dell'energia cinetica del sistema e dell'energia potenziale associata all'errore nello spazio operativo:

$$V(q, \dot{q}) = \frac{1}{2} \dot{q}^T B(q) \dot{q} + \frac{1}{2} \tilde{x}^T K_p \tilde{x}$$

dove:
* $B(q)$ è la matrice d'inerzia del manipolatore, definita positiva ($B(q) > 0$).
* $K_p$ è la matrice dei guadagni proporzionali, scelta come matrice simmetrica e definita positiva ($K_p = K_p^T > 0$).

#### Proprietà della Funzione di Lyapunov
La funzione $V(q, \dot{q})$ rispetta le seguenti proprietà fondamentali:
1. $V(q, \dot{q}) \ge 0$ per qualsiasi stato.
2. $V(q, \dot{q}) = 0$ se e solo se l'errore e la velocità sono contemporaneamente nulli, ossia $\tilde{x} = 0$ e $\dot{q} = 0$.

---

### Derivazione Matematica della Legge di Controllo

Per garantire la stabilità al chiuso del sistema, occorre calcolare la derivata temporale di $V(q, \dot{q})$ lungo le traiettorie del sistema e progettarne la legge di comando $u$ in modo da renderla semidefinita negativa ($\dot{V} \le 0$).

#### Relazione Cinematica Differenziale
Derivando l'errore temporale $\tilde{x}$ rispetto al tempo, ricordando che la posa desiderata $x_d$ è costante ($\dot{x}_d = 0$), si ottiene:

$$\dot{\tilde{x}} = -\dot{x}$$

Utilizzando la **cinematica differenziale analitica** (*analytical differential kinematics*), la velocità dell'end-effector $\dot{x}$ è legata alle velocità dei giunti $\dot{q}$ tramite lo **Jacobiano analitico** (*analytical Jacobian*) $J_A(q)$:

$$\dot{x} = J_A(q) \dot{q} \implies \dot{\tilde{x}} = -J_A(q) \dot{q}$$

#### Calcolo della Derivata temporale $\dot{V}$
Derivando $V(q, \dot{q})$ rispetto al tempo si ottiene:

$$\dot{V} = \dot{q}^T B(q) \ddot{q} + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} + \tilde{x}^T K_p \dot{\tilde{x}}$$

Sostituendo la relazione cinematica $\dot{\tilde{x}} = -J_A(q)\dot{q}$:

$$\dot{V} = \dot{q}^T B(q) \ddot{q} + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} - \tilde{x}^T K_p J_A(q) \dot{q}$$

Sfruttando il modello dinamico del robot:

$$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u \implies B(q)\ddot{q} = u - C(q, \dot{q})\dot{q} - g(q)$$

Sostituendo l'espressione di $B(q)\ddot{q}$ in $\dot{V}$:

$$\dot{V} = \dot{q}^T \left( u - C(q, \dot{q})\dot{q} - g(q) \right) + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} - \dot{q}^T J_A^T(q) K_p \tilde{x}$$

Raggruppando i termini e sfruttando la **proprietà di antisimmetria** (*skew-symmetry property*) per cui la matrice $\dot{B}(q) - 2C(q, \dot{q})$ è antisimmetrica (ovvero $\dot{q}^T (\dot{B}(q) - 2C(q, \dot{q})) \dot{q} = 0$), la derivata si semplifica in:

$$\dot{V} = \dot{q}^T \left( u - g(q) - J_A^T(q) K_p \tilde{x} \right)$$

#### Sintesi della Legge di Comando PD
Per fare in modo che $\dot{V}$ sia semidefinita o definita negativa, scegliamo l'azione di controllo $u$ inserendo una compensazione della gravità, l'azione proporzionale nello spazio operativo e un termine derivativo di smorzamento:

$$u = g(q) + J_A^T(q) K_p \tilde{x} - J_A^T(q) K_d J_A(q) \dot{q}$$

dove $K_d > 0$ è una matrice di guadagno derivativo definita positiva. 

*Nota*: L'azione derivativa può anche essere espressa direttamente in funzione della velocità dell'organo terminale: dato che $\dot{x} = J_A(q)\dot{q}$, il termine $-J_A^T(q) K_d J_A(q) \dot{q}$ corrisponde a $-J_A^T(q) K_d \dot{x}$.

Sostituendo $u$ all'interno di $\dot{V}$, otteniamo:

$$\dot{V} = -\dot{q}^T J_A^T(q) K_d J_A(q) \dot{q} \le 0$$

Essendo $K_d > 0$, la forma quadratica è sempre minore o uguale a zero per qualsiasi valore di $\dot{q}$.

---

### Analisi di Stabilità e Punti di Equilibrio

> **CONCETTO CHIAVE: Analisi di Convergenza e Singolarità Cinematica**
> 
> Per verificare se il sistema converga esattamente alla posa desiderata ($\tilde{x} = 0$), si applica il **Principio di Invarianza di LaSalle** (*LaSalle's Invariance Principle*).
> 
> Quando $\dot{V} = 0$, si ha che $J_A(q)\dot{q} = 0$, il che implica $\dot{q} = 0$ (a meno di velocità appartenenti allo spazio nullo). Con $\dot{q} = 0$ e $\ddot{q} = 0$, sostituendo la legge di controllo nell'equazione dinamica del manipolatore si ottiene l'equazione d'equilibrio a regime:
> 
> $$J_A^T(q) K_p \tilde{x} = 0$$
> 
> Da questa relazione si possono trarre due conclusioni fondamentali:
> 
> 1. **Configurazione Non Singolare (Rango Pieno)**: Se lo Jacobiano analitico $J_A(q)$ ha **rango pieno** (*full rank*), allora $J_A^T(q)$ definisce una trasformazione iniettiva. Di conseguenza, l'unica soluzione possibile all'equazione d'equilibrio è:
>    $$K_p \tilde{x} = 0 \implies \tilde{x} = 0$$
>    In questo caso è garantita la convergenza asintotica all'errore nullo nello spazio operativo.
> 
> 2. **Configurazione Singolare**: Se il robot attraversa o si trova in una **singolarità cinematica** (*kinematic singularity*), la matrice $J_A^T(q)$ perde rango. In tale situazione, l'errore $K_p \tilde{x}$ potrebbe appartenere al kernel (spazio nullo) di $J_A^T(q)$ ($\tilde{x} \in \ker(J_A^T)$). Questo implica che il robot potrebbe bloccarsi in una configurazione di equilibrio indesiderata con un errore di posa non nullo ($\tilde{x} \neq 0$).

---

### Linearizzazione con Retroazione nello Spazio dei Giunti (Richiamo)

Prima di passare allo spazio operativo, richiamiamo brevemente il funzionamento della **Linearizzazione con Retroazione** (*Feedback Linearization*) nello **Spazio dei Giunti** (*Joint Space*), utilizzata per l'inseguimento di traiettoria (*trajectory tracking*).

Consideriamo il modello dinamico del manipolatore espresso in forma compatta:

$$B(q)\ddot{q} + n(q, \dot{q}) = u$$

dove $B(q)$ è la matrice di inerzia e $n(q, \dot{q})$ raccoglie tutti i termini non lineari (effetti centrifugi, di Coriolis, attriti e forze gravitazionali).

La tecnica di linearizzazione con retroazione si articola in due passi principali:

1. **Linearizzazione esatta della dinamica:** Si sceglie la legge di controllo $u$ in funzione di un ingresso ausiliario $y$:

   $$u = B(q)y + n(q, \dot{q})$$

   Sostituendo $u$ nel modello dinamico, i termini non lineari si cancellano e si ottiene una dinamica disaccoppiata e linearizzata descritta da un doppio integratore:

   $$\ddot{q} = y$$

2. **Progetto dell'ingresso ausiliario $y$:** Nello spazio dei giunti, l'ingresso ausiliario viene progettato tramite uno schema **Feedforward + Azione Proporzionale-Derivativa (PD)**:

   $$y = \ddot{q}_d + K_d(\dot{q}_d - \dot{q}) + K_p(q_d - q)$$

   Definendo l'errore ai giunti come $\tilde{q} = q_d - q$, la dinamica dell'errore diventa un'equazione differenziale del secondo ordine a coefficienti costanti:

   $$\ddot{\tilde{q}} + K_d \dot{\tilde{q}} + K_p \tilde{q} = 0$$

   Se le matrici dei guadagni $K_p$ e $K_d$ sono definite positive, l'errore $\tilde{q}(t)$ converge a zero in modo esponenzialmente veloce.

---

### Estensione della Linearizzazione con Retroazione allo Spazio Operativo

Nella maggior parte delle applicazioni reali, la traiettoria desiderata $x_d(t)$ è definita nello **Spazio Operativo** (*Operational Space*), non nello spazio dei giunti. Non vogliamo quindi calcolare l'errore sui giunti, ma vogliamo controllare direttamente l'errore nello spazio operativo:

$$\tilde{x} = x_d - x_e$$

Per fare ciò, dobbiamo legare la dinamica ausiliaria $\ddot{q} = y$ alle grandezze nello spazio operativo (posizione, velocità e accelerazione).

#### Relazione Cinematica Differenziale del Secondo Ordine
Dalla cinematica differenziale sapendo che $\dot{x}_e = J_A(q)\dot{q}$ (dove $J_A$ è il **Jacobiano Analitico**, *Analytical Jacobian*), deriviamo rispetto al tempo per ottenere la relazione per le accelerazioni:

$$\ddot{x}_e = J_A(q)\ddot{q} + \dot{J}_A(q, \dot{q})\dot{q}$$

#### Progetto dell'Ingresso Ausiliario $y$
Manteniamo il primo passo di cancellazione dinamica $u = B(q)y + n(q, \dot{q})$, in modo da avere ancora la dinamica semplificata $\ddot{q} = y$. 

Vogliamo che la dinamica dell'errore nello spazio operativo sia regolata da un'equazione del secondo ordine analoga a quella vista nei giunti:

$$\ddot{\tilde{x}} + K_d \dot{\tilde{x}} + K_p \tilde{x} = 0 \implies \ddot{x}_e = \ddot{x}_d + K_d (\dot{x}_d - \dot{x}_e) + K_p (x_d - x_e)$$

Per ottenere questo risultato, l'ingresso ausiliario $y$ deve "tradurre" le grandezze dallo spazio operativo allo spazio dei giunti. Definiamo quindi $y$ come:

$$y = J_A^{-1}(q) \left( \ddot{x}_d + K_d(\dot{x}_d - \dot{x}_e) + K_p(x_d - x_e) - \dot{J}_A(q, \dot{q})\dot{q} \right)$$

#### Dimostrazione e Verifica della Dinamica dell'Errore
*Nota: Passaggio integrato con chiarezza didattica.*

Per verificare l'efficacia di questa legge di controllo, sostituiamo la definizione di $y$ nella relazione delle accelerazioni dello spazio operativo $\ddot{x}_e = J_A(q)y + \dot{J}_A(q, \dot{q})\dot{q}$ (ricordando che $\ddot{q} = y$):

$$\ddot{x}_e = J_A(q) \left[ J_A^{-1}(q) \left( \ddot{x}_d + K_d\dot{\tilde{x}} + K_p\tilde{x} - \dot{J}_A(q, \dot{q})\dot{q} \right) \right] + \dot{J}_A(q, \dot{q})\dot{q}$$

Poiché $J_A(q) J_A^{-1}(q) = I$ (matrice identità), i termini si semplificano:

$$\ddot{x}_e = \left( \ddot{x}_d + K_d\dot{\tilde{x}} + K_p\tilde{x} - \dot{J}_A(q, \dot{q})\dot{q} \right) + \dot{J}_A(q, \dot{q})\dot{q}$$

I termini legati alla derivata del Jacobiano $\dot{J}_A(q, \dot{q})\dot{q}$ si cancellano a vicenda:

$$\ddot{x}_e = \ddot{x}_d + K_d\dot{\tilde{x}} + K_p\tilde{x}$$

Riorganizzando i termini e ricordando che $\ddot{\tilde{x}} = \ddot{x}_d - \ddot{x}_e$, otteniamo la dinamica dell'errore desiderata:

$$\ddot{\tilde{x}} + K_d\dot{\tilde{x}} + K_p\tilde{x} = 0$$

Se $K_p$ e $K_d$ sono matrici definite positive, l'errore $\tilde{x}(t)$ nello spazio operativo converge a zero in modo esponenzialmente veloce.

---

### Analisi Critica e Limiti dello Schema nello Spazio Operativo

> **CONCETTO CHIAVE: Complessità Computazionale e Singolarità**
>
> Il controllo tramite linearizzazione con retroazione nello spazio operativo presenta una serie di svantaggi strutturali severi rispetto alla controparte nello spazio dei giunti:
>
> 1. **Calcolo Inverso Continuo:** Richiede l'inversione analitica del Jacobiano $J_A^{-1}(q)$ ad ogni istante di campionamento. Questo rende il controllo assai più complesso dal punto di vista computazionale.
> 2. **Assunzione di Matrice Quadrata:** L'esistenza di $J_A^{-1}(q)$ presuppone che il Jacobiano sia una matrice quadrata (numero di giunti pari ai gradi di libertà dello spazio operativo). Non è direttamente applicabile a manipolatori ridondanti senza opportune modifiche.
> 3. **Presenza di Singolarità Cinematiche:** Se il manipolatore attraversa o si avvicina a una configurazione singolare, $\det(J_A) \to 0$ e la matrice $J_A^{-1}(q)$ non è ben definita (diverge).
> 4. **Problema dello Spazio Nullo (*Null Space*):** Se l'errore entra nello spazio nullo del Jacobiano (ad esempio nei controllori PD con compensazione di gravità), il manipolatore rischia di bloccarsi in una configurazione diversa da quella desiderata (*stuck configuration*).

---

3. Formulazione del Modello Dinamico nello Spazio Operativo e Controllo d'Inerzia

Mantenere il modello espresso nelle variabili di giunto $\tau$ e definire i requisiti di controllo nello *Spazio Operativo (Operational Space)* rende la progettazione delle leggi di controllo complessa. Per ovviare a questo problema, cambia il punto di vista: si riformula l'intero modello dinamico direttamente in funzione delle coordinate dello spazio operativo.

### Cambio di Prospettiva: Il Modello Dinamico Fittizio

L'obiettivo è ricavare un modello dinamico che descriva la relazione diretta tra le forze generalizzate agenti sull'end-effector e un insieme minimo di coordinate per descriverne posizione e orientamento nello spazio operativo.

#### Introduzione delle Forze Equivalenti
I torchi di giunto $\tau$ sono applicati per muovere la struttura. Se all'end-effector agisce un vettore di forze e coppie $h_e$, il loro effetto equivalente ai giunti è dato da $J^T(q) h_e$. 

In modo analogo, si definisce un vettore di **forze/coppie fittizie equivalenti nello spazio operativo** $\gamma_e$, applicate all'end-effector, tali da produrre ai giunti esattamente gli stessi torchi $\tau$:
$$\tau = J^T(q) \gamma_e$$

> **Concetto Chiave**: Le forze $\gamma_e$ non sono forze fisicamente applicate dall'esterno all'end-effector, ma rappresentano una quantità *fittizia (fictitious)* che permette di interpretare i torchi erogati dai motori ai giunti come se fossero generati direttamente nello spazio operativo.

#### Transizione al Jacobiano Analitico
Per garantire un'omogeneità di descrizione nello spazio operativo, si preferisce utilizzare il *Jacobiano Analitico (Analytical Jacobian)* $J_A(q)$ rispetto al *Jacobiano Geometrico (Geometric Jacobian)* $J(q)$. 

Ricordando la relazione di trasformazione $J(q) = T_A(x_e) J_A(q)$ (dove $T_A$ è la matrice di trasformazione cinematica), è possibile definire le forze equivalenti associate al Jacobiano analitico come $\gamma_a$:
$$\tau = J_A^T(q) \gamma_a$$
dove $\gamma_a = T_A^T(x_e) \gamma_e$. Allo stesso modo, le forze esterne riscalate diventano $h_a = T_A^T(x_e) h_e$.

---

### Derivazione Matematica del Modello nello Spazio Operativo

Si parte dal modello dinamico standard nello spazio dei giunti (omettendo gli attriti per semplicità):
$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = \tau - J_A^T(q)h_a$$

Sostituendo $\tau = J_A^T(q)\gamma_a$, si esplicita l'accelerazione dei giunti $\ddot{q}$:
$$\ddot{q} = B^{-1}(q) \left[ J_A^T(q)(\gamma_a - h_a) - C(q,\dot{q})\dot{q} - g(q) \right]$$

Si considera ora la relazione cinematica di accelerazione nello spazio operativo:
$$\ddot{x}_e = J_A(q)\ddot{q} + \dot{J}_A(q)\dot{q}$$

Sostituendo l'espressione di $\ddot{q}$ all'interno della relazione cinematica:
$$\ddot{x}_e = J_A(q) B^{-1}(q) J_A^T(q) (\gamma_a - h_a) - J_A(q) B^{-1}(q) \left( C(q,\dot{q})\dot{q} + g(q) \right) + \dot{J}_A(q)\dot{q}$$

#### Definizione delle Matrici di Inerzia, Coriolis e Gravità Fittizie
Per isolare il termine di forza $(\gamma_a - h_a)$, si definisce la **Matrice d'Inerzia nello Spazio Operativo** $B_A(q)$:
$$B_A(q) = \left( J_A(q) B^{-1}(q) J_A^T(q) \right)^{-1}$$

Moltiplicando entrambi i membri della relazione per $B_A(q)$, si ottiene:
$$B_A(q)\ddot{x}_e = \gamma_a - h_a - B_A(q) J_A(q) B^{-1}(q) \left( C(q,\dot{q})\dot{q} + g(q) \right) + B_A(q)\dot{J}_A(q)\dot{q}$$

Riorganizzando i termini, si definiscono le componenti fittizie lette dallo spazio operativo:
1. **Vettore di Gravità nello Spazio Operativo** $g_A(q)$:
   $$g_A(q) = B_A(q) J_A(q) B^{-1}(q) g(q)$$
2. **Matrice di Coriolis e Centripeta nello Spazio Operativo** $C_A(q, \dot{q})$:
   *Nota: Passaggio integrato con chiarezza didattica.* Raggruppando i termini dipendenti dalla velocità $\dot{q}$ e $\dot{x}_e$:
   $$C_A(q, \dot{q})\dot{x}_e = B_A(q) J_A(q) B^{-1}(q) C(q, \dot{q})\dot{q} - B_A(q)\dot{J}_A(q)\dot{q}$$

La struttura finale del modello dinamico nello spazio operativo è quindi:
$$B_A(q)\ddot{x}_e + C_A(q, \dot{q})\dot{x}_e + g_A(q) = \gamma_a - h_a$$

> **Concetto Chiave**: La struttura matematica di questo modello riflette esattamente quella dello spazio dei giunti, ma le matrici $B_A, C_A, g_A$ **non sono le reali masse o gravità fisiche**, bensì le quantità equivalenti viste "attraverso gli occhiali" dello spazio operativo.

#### Caso Particolare: Manipolatore Non Ridondante
Se il robot non è ridondante e si trova fuori dalle singolarità, la matrice Jacobiana $J_A(q)$ è quadrata e invertibile. In questo caso, le espressioni si semplificano applicando la proprietà dell'inverso del prodotto $(ABC)^{-1} = C^{-1}B^{-1}A^{-1}$:
$$B_A(q) = J_A^{-T}(q) B(q) J_A^{-1}(q)$$

---

### Controllo a Linearizzazione del Feedback nello Spazio Operativo

Ipotizzando che il robot non interagisca con l'ambiente ($h_a = 0$), l'obiettivo è far tracciare all'end-effector una traiettoria desiderata definita in posizione, velocità e accelerazione: $x_d(t), \dot{x}_d(t), \ddot{x}_d(t)$.

Si applica la strategia di controllo in due fasi (*Linearizzazione del Feedback* + *Controllo Ausiliario*):

```
 Traiettoria   +  _   LBL        Controllo        Linearizzazione      Modello Reale 
 Desiderata    --->(X)--->[  A  ]------------>[ γ_a ]------------->[  τ = J_A^T γ_a  ]---> Robot
  (x_d, ... )       ^   
                    | Errori
                    +--- Stato Operativo Misurato (x_e, x_e_dot)
```

#### Fase 1: Linearizzazione del Feedback
Si progetta la forza fittizia $\gamma_a$ per cancellare le dinamiche non lineari dello spazio operativo:
$$\gamma_a = B_A(q) a + C_A(q, \dot{q})\dot{x}_e + g_A(q)$$

Sostituendo $\gamma_a$ nel modello dello spazio operativo, il sistema si riduce a un doppio integratore disaccoppiato:
$$\ddot{x}_e = a$$

#### Fase 2: Progettazione del Controllo Ausiliario $a$
L'ingresso di controllo ausiliario $a$ viene progettato come un controllo PD (Proporzionale-Derivativo) con azione feedforward in accelerazione:
$$a = \ddot{x}_d + K_d (\dot{x}_d - \dot{x}_e) + K_p (x_d - x_e)$$

Definendo l'errore nello spazio operativo come $\tilde{x} = x_d - x_e$, la dinamica dell'errore diventa:
$$\ddot{\tilde{x}} + K_d \dot{\tilde{x}} + K_p \tilde{x} = 0$$

Scegliendo le matrici di guadagno $K_p$ e $K_d$ definite positive, l'errore $\tilde{x}(t)$ converge a zero in modo esponenzialmente veloce.

#### Calcolo delle Coppie Reali di Giunto
Una volta calcolato il vettore delle forze fittizie $\gamma_a$, i torchi effettivi $\tau$ da inviare ai motori dei giunti si calcolano semplicemente tramite la trasposta del Jacobiano Analitico:
$$\tau = J_A^T(q) \left[ B_A(q) \left( \ddot{x}_d + K_d \dot{\tilde{x}} + K_p \tilde{x} \right) + C_A(q, \dot{q})\dot{x}_e + g_A(q) \right]$$

---

3. Estensioni della Modellistica e Introduzione all'Interazione con l'Ambiente

#### Sintesi sul Modello nello Spazio Operativo e Controllo PD con Compensazione di Gravità

Prima di passare all'interazione con l'ambiente, facciamo una precisazione conclusiva sul modello di dinamica nello spazio operativo (*Operational Space*). 

Abbiamo visto che, rimodellando le equazioni del moto direttamente nello spazio operativo, possiamo descrivere la dinamica del robot mediante matrici equivalenti: matrice d'inerzia nello spazio operativo, matrice dei termini di Coriolis e centrifughi, e vettore delle forze gravitazionali.

Partendo da questa descrizione, è possibile progettare un **Controllo Proporzionale-Derivativo (PD) con compensazione di gravità** direttamente nello spazio task. 

> **Concetto Chiave**: Per applicare il metodo diretto di Lyapunov e ricavare formalmente il controllo PD con compensazione di gravità nello spazio operativo, non possiamo usare la classica energia cinetica espressa nelle coordinate giuntali. È necessario definire una **fittizia energia cinetica** espressa mediante le variabili dello spazio operativo:
>
> $$T = \frac{1}{2} \dot{x}_c^T B_a(x) \dot{x}_c$$
>
> dove $B_a(x)$ rappresenta la matrice d'inerzia analitica nello spazio operativo e $\dot{x}_c$ è la velocità operativa. Associando a questa una funzione quadratica definita sull'errore di posizione $\tilde{x}$, si costruisce la funzione di Lyapunov opportuna per garantire la stabilità dell'equilibrio target.

---

#### Interazione Robot-Ambiente: Strategie Passive vs Strategie Attive

Quando l'organo terminale (*end-effector*) del robot entra in contatto con l'ambiente circostante, nascono delle forze di interazione che alterano il comportamento dinamico del sistema. Per gestire ed eventualmente controllare queste forze, esistono due famiglie di approcci:

1. **Strategie di Controllo Passivo (*Passive Control*)**: Si basano su elementi meccanici cedevoli integrati direttamente sulla struttura fisica della catena cinematica, senza la necessità di misurare le forze o modificare l'algoritmo di controllo in tempo reale.
2. **Strategie di Controllo Attivo (*Active Control*)**: Sfruttano sensori di forza per misurare le interazioni e modificano attivamente le traiettorie o le coppie ai giunti tramite algoritmi dedicati.

---

#### Controllo Passivo e il Dispositivo RCC (Remote Center Compliance)

Il controllo passivo è ampiamente impiegato nell'industria grazie alla sua semplicità, immediatezza e robustezza. Il dispositivo passivo più celebre è il **Dispositivo di Cedevolezza Centro-Remota (*Remote Center Compliance - RCC*)**.

##### Struttura Meccanica dell'RCC
Il dispositivo RCC viene montato alla fine del braccio, tra il flangia del robot e l'utensile finale (*tool*). 
È composto da:
* Due piastre metalliche parallele.
* Aste o elementi elastici flessibili che collegano le due piastre.

Questa specifica geometria elastica permette alla piastra esterna di traslare lateralmente e ruotare rispetto a quella fissa in risposta alle forze esterne ricevute.

```
       [ Flangia del Robot ]
               |||
       +-----------------+  <-- Piastra Fissa
        \   \   \   \   \   <-- Elementi Elastici / Flessibili
         +---------------+  <-- Piastra Mobile
                 |
          [ Utensile/Peg ]
```

##### Esempio Applicativo: Il Compito di Inserimento Perno-Foro (*Peg-in-Hole*)

Consideriamo un classico task industriale di inserimento di un perno in una sede cilindrica (*Peg-in-Hole task*):

* **Caso Ideale**: Se avessimo una conoscenza perfetta della geometria del foro e della posizione del robot, potremmo pianificare una traiettoria puramente cinematica. Il perno entrerebbe perfettamente senza toccare i bordi. Nella realtà, questo è impossibile a causa di incertezze di posizionamento, tolleranze di lavorazione e deformazioni.
* **Comportamento Senza RCC (Robot Rigido)**: Se il robot arriva sul foro con un piccolo disallineamento e prova a seguire la traiettoria rigida impostata, il perno tocca il bordo. Generando un contatto rigido, il robot continua a spingere per tracciare la traiettoria desiderata: questo provoca il blocco meccanico (*jamming*), elevate forze di contatto e potenziale danneggiamento dei componenti.
* **Comportamento Con RCC**: Quando il perno impatta sul bordo del foro, si genera una forza di reazione $F$. La cedevolezza strutturale dell'RCC fa sì che questa forza deformi gli elementi elastici. Il dispositivo converte la forza di contatto in uno scorrimento o in una rotazione passiva dell'utensile, "guidando" spontaneamente il perno verso il centro del foro. 

> **Concetto Chiave**: Il dispositivo RCC rende il sistema di manipolazione **robusto rispetto ai piccoli disallineamenti geometrici**, adattando la posizione dell'end-effector in modo meramente meccanico e istantaneo, senza richiedere alcun ciclo di feedback software o calcolo computazionale.

##### Limiti del Controllo Passivo
Sebbene l'RCC funzioni perfettamente per operazioni ripetitive e ben delimitate (come l'inserimento di componenti su linee di montaggio), mostra evidenti limiti per task complessi o variabili. Se l'applicazione richiede di:
* Eseguire compiti differenti con lo stesso utensile,
* Seguire superfici con curvature incognite (*contour following*),
* Interagire con ambienti fortemente dinamici e non strutturati (es. aprire una porta, ruotare una maniglia),

il controllo passivo non è più sufficiente ed è necessario passare al controllo attivo.

---

#### Introduzione al Controllo Attivo e Sensori di Forza

Per implementare strategie di controllo attivo, la struttura del robot deve essere in grado di percepire l'effetto dinamico del contatto.

A questo scopo si utilizzano **Sensori di Forza/Coppia (*Force/Torque Sensors*)**, tipicamente montati al polso del robot (*wrist force sensors*). Si tratta di sensori multi-asse capaci di misurare simultaneamente:
* Il vettore delle forze lineari $\mathbf{f} = [F_x, F_y, F_z]^T$
* Il vettore dei momenti/coppie $\boldsymbol{\tau} = [\tau_x, \tau_y, \tau_z]^T$

Nelle prossime lezioni svilupperemo la teoria del controllo attivo dell'interazione (come il *Controllo d'Impedenza* e il *Controllo Ibrido di Forza/Posizione*), assumendo la presenza di un sensore di forza al polso in grado di chiudere il loop di feedback sull'interazione dinamica con l'ambiente.