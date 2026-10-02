# Controllo Adattativo Diretto e Parametrizzazione Lineare della Dinamica

## Overview Didattica

In questa lezione viene introdotta la famiglia dei **controllori adattativi** (*adaptive controllers*), focalizzandosi in particolare sul **Controllo Adattativo Diretto** (*Direct Adaptive Control*). Il problema principale affrontato è il controllo di traiettoria (*trajectory tracking*) di un manipolatore robotico in presenza di incertezza sui parametri dinamici del sistema (quali masse, momenti d'inerzia e posizioni dei centri di massa).

I punti chiave trattati nella lezione comprendono:
* **Obiettivo del Controllo Adattativo Diretto:** L'obiettivo primario è guidare l'errore di inseguimento a zero ($e \to 0$) aggiornando in tempo reale le stime dei parametri dinamici. Si evidenzia la distinzione rispetto al *Controllo Adattativo Indiretto* (*Indirect Adaptive Control*): nel controllo diretto l'errore di inseguimento si annulla anche se i parametri stimati non convergono necessariamente ai loro valori reali.
* **Primo Ingrediente — Parametrizzazione Lineare della Dinamica:** Scomposizione del modello dinamico del robot nel prodotto tra la **Matrice Regressore** (*Regressor Matrix*) $Y(q, \dot{q}, \ddot{q})$, dipendente dallo stato cinematico del robot e di struttura nota, e il **Vettore dei Parametri Dinamici** (*Dynamic Parameter Vector*) $\pi$, di cui si possiede solo una stima $\hat{\pi}$.
* **Secondo Ingrediente — Richiamo delle Tecniche di Controllo basate sul Modello:** Analisi del comportamento dei controllori di tipo **Feedforward + PD** e di **Linearizzazione per Retroazione** (*Feedback Linearization* / *Inverse Dynamics*). Viene illustrato come, in presenza di incertezze parametriche, la cancellazione non lineare imperfetta comporti il confinamento delle traiettorie all'interno di un intorno limitato (un "tubo" di errore) attorno alla traiettoria desiderata.

---

### Introduzione al Controllo Adattativo Diretto

Nel controllo di manipolatori robotici, spesso non si possiede una conoscenza precisa e perfetta del modello dinamico del sistema, ma soltanto una sua stima. L'obiettivo del **Controllo Adattativo (Adaptive Control)** è gestire queste incertezze parametriche aggiornando l'invariante del modello in tempo reale durante l'esecuzione del compito.

In questo contesto, distinguiamo due approcci principali:

1. **Controllo Adattativo Diretto (Direct Adaptive Control):** Il nostro scopo primario è far tendere l'errore di inseguimento a zero ($e(t) \to 0$). Aggiorniamo continuamente la stima dei parametri dinamici durante il funzionamento, ma *non è necessario* che i parametri stimati convergano ai loro valori reali reali. È sufficiente garantire che l'errore di traiettoria vada a zero.
2. **Controllo Adattativo Indiretto (Indirect Adaptive Control):** Richiede una formulazione più raffinata con l'obiettivo esplicito di stimare con esattezza i veri parametri dinamici fisici del robot.

> **Concetto Chiave**
> Nel **Controllo Adattativo Diretto**, l'obiettivo principale è l'azzeramento dell'errore di inseguimento della traiettoria ($e \to 0$), **non** la convergenza esatta dei parametri dinamici ai loro valori reali. Possiamo ottenere un inseguimento perfetto anche con stime stazionarie errate dei parametri.

---

### I Due Ingredienti Fondamentali

Per progettare una legge di controllo adattativo diretto ci serviamo di due "ingredienti" concettuali fondamentali.

#### Ingrediente 1: Parametrizzazione Lineare della Dinamica

La dinamica di un manipolatore a $n$ giunti può essere riscritta sfruttando la proprietà di **Parametrizzazione Lineare (Linear Parameterization)**:

$$
\tau = Y(q, \dot{q}, \ddot{ddot{q}}) \pi
$$

Dove:
* $Y(q, \dot{q}, \ddot{q}) \in \mathbb{R}^{n \times p}$ è la **Matrice Regressore (Regressor Matrix)**. Dipende esclusivamente dalle variabili cinematiche (posizioni, velocità e accelerazioni dei giunti) e dalla geometria del robot (lunghezza dei link), che assumiamo note.
* $\pi \in \mathbb{R}^{p}$ è il **Vettore dei Parametri Dinamici (Dynamic Parameters Vector)**. Contiene la combinazione delle proprietà fisiche delle masse, dei momenti di inerzia e della posizione dei baricentri dei vari link.

**Proprietà del Regressore e dei Parametri:**
* **Dimensione ridotta di $\pi$:** Teoricamente avremmo 10 parametri dinamici per ciascun link ($10n$). Tuttavia, non tutti i parametri entrano attivamente nella dinamica e molti si combinano linearmente tra loro. La dimensione effettiva $p$ di $\pi$ viene quindi opportunamente ridotta eliminando i termini trascurabili o combinandoli.
* **Struttura di $Y$:** La matrice regressore $Y$ ha dipendenza:
  * *Lineare* rispetto alle accelerazioni dei giunti $\ddot{q}$.
  * *Quadratica* rispetto alle velocità dei giunti $\dot{q}$.
  * *Non lineare* (funzioni trigonometriche come seno e coseno) rispetto alle posizioni dei giunti $q$.

Nel nostro scenario di incertezza, consideriamo $Y$ perfettamente nota (poiché la cinematica è nota), mentre per il vettore dei parametri dinamici possediamo solo una stima incerta, indicata con $\hat{\pi}$.

#### Ingrediente 2: Selezione della Struttura di Controllo e Problemi di Stabilità

Riprendiamo le strategie di controllo non lineare per il tracciamento di traiettoria:

1. **Linearizzazione mediante Feedback (Feedback Linearization):** 
   Cerca di cancellare esattamente la dinamica non lineare inserendo le matrici stimate $\hat{B}(q)$, $\hat{C}(q, \dot{q})$ e $\hat{g}(q)$. 
   * *Problema con l'adattativo:* Renderla adattativa online è problematico. La matrice di inerzia stimata $\hat{B}(q)$ deve rimanere sempre definita positiva (tutti gli autovalori $\lambda > 0$). Durante l'aggiornamento online a frequenze elevate (es. $1 \text{ kHz}$), piccole oscillazioni di stima possono far diventare negativo anche solo un autovalore molto piccolo (es. da $+0.001$ a $-0.001$). Questo cambio di segno distrugge la stabilità del sistema creando violente instabilità.
   
2. **Legge di Controllo Avanzata (Feedforward Ibrido + Feedback PD):**
   Per evitare problemi di definitezza positiva, si preferisce una legge di controllo globale asintoticamente stabile che non inverta la matrice di inerzia. La struttura di partenza (in caso di parametri incerti $\hat{\pi}$) valuta i termini dinamici non sulla traiettoria desiderata ma sullo stato corrente:

$$
\tau = \hat{B}(q)\ddot{q}_d + \hat{C}(q, \dot{q})\dot{q}_d + \hat{g}(q) + K_d (\dot{q}_d - \dot{q}) + K_p (q_d - q)
$$

Sfruttando il regressore $Y$, la parte strutturale del modello può essere espressa direttamente in funzione della stima dei parametri $\hat{\pi}$:

$$
\tau = Y(q, \dot{q}, \dot{q}_d, \ddot{q}_d) \hat{\pi} + K_d \dot{e} + K_p e
$$

---

### Incertezza Parametrica e Modifica della Traiettoria di Riferimento

Quando lavoriamo con la legge adattativa, l'obiettivo è fornire una legge di aggiornamento nel tempo per la derivata della stima dei parametri, $\dot{\hat{\pi}}(t)$, la quale integrata ci fornisce l'evoluzione di $\hat{\pi}(t)$.

Tuttavia, applicare direttamente la legge indicata sopra presenta un inconveniente pratico noto:

> **Attenzione (Inconveniente del Drift di Posizione):**
> L'adattamento diretto basato sul semplice errore di velocità tende ad azzerare perfettamente l'errore di velocità ($\dot{e} \to 0$), ma può produrre un fenomeno di deriva (drift) sull'errore di posizione ($e \ne 0$), portando il robot lontano dalla traiettoria desiderata in determinate configurazioni.

#### Robustificazione tramite Velocità di Riferimento

Per ovviare a questo inconveniente e garantire robustezza sia sulla posizione che sulla velocità, introduciamo una **Velocità di Riferimento (Reference Velocity)** $\dot{q}_r$, definita modificando la velocità desiderata $\dot{q}_d$ con un termine correttivo sull'errore di posizione:

$$
\dot{q}_r = \dot{q}_d + \Lambda e = \dot{q}_d + \Lambda (q_d - q)
$$

Dove $\Lambda$ (o $\Gamma$) è una matrice guadagno definita positiva. 
Di conseguenza, l'accelerazione di riferimento diventa:

$$
\ddot{q}_r = \ddot{q}_d + \Lambda \dot{e}
$$

Definiamo inoltre l'**Errore Tracciante Modificato (Filtered Tracking Error)** $s$:

$$
s = \dot{q}_r - \dot{q} = \dot{e} + \Lambda e
$$

Sostituendo $\dot{q}_d$ e $\ddot{q}_d$ con le corrispettive quantità di riferimento $\dot{q}_r$ e $\ddot{q}_r$ all'interno della matrice regressore $Y$, si ottiene la formulazione robusta su cui si innescherà il meccanismo di aggiornamento adattativo dei parametri $\hat{\pi}$.

---

### Modifica della Traiettoria di Riferimento: Inserimento dell'Errore di Posizione

 Per comprendere la necessità di modificare la traiettoria di riferimento, consideriamo un semplice esempio concettuale: il tracciamento di una massa ideale (rappresentata in verde) che si muove a una velocità desiderata $\dot{q}_d$. Il nostro obiettivo è far sì che la massa attuata (rappresentata in giallo), la cui posizione è $q$, si sovrapponga perfettamente a quella verde.

Se fornissimo al controllore unicamente la velocità desiderata $\dot{q}_d$ come riferimento da inseguire, la strategia fallirebbe. 

 Supponiamo che la massa gialla si trovi dietro la massa verde di una distanza $e > 0$ (dove l'errore di posizione è $\tilde{q} = q_d - q$), ma che stia muovendosi esattamente alla stessa velocità ($\dot{q} = \dot{q}_d$). In questo scenario:
* L'errore di velocità è nullo ($\dot{q}_d - \dot{q} = 0$).
* L'attuatore non applica alcuna forza ($u = 0$).

Di conseguenza, il sistema continuerà a muoversi alla velocità $\dot{q}_d$, mantenendo però l'errore di posizione $e > 0$ costante nel tempo senza mai annullarlo.

#### Soluzione: Introduzione della Velocità di Riferimento Modificata

Per superare questo limite, ridefiniamo la velocità fornita al controllore. Invece di utilizzare direttamente $\dot{q}_d$, introduciamo una **Velocità di Riferimento (Reference Velocity)** $\dot{q}_r$ che include un termine correttivo proporzionale all'Errore di Inseguimento di Posizione (*Position Tracking Error*) $\tilde{q}$:

$$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$

dove:
* $\tilde{q} = q_d - q$ è l'errore di posizione.
* $\Lambda$ (Lambda) è una matrice di guadagno definita positiva (*Positive Definite Gain Matrix*), impostata convenzionalmente come $\Lambda = K_D^{-1} K_P > 0$.

In questo modo, anche se l'errore di velocità nominale fosse nullo, la presenza di un errore di posizione $\tilde{q} \neq 0$ genererebbe una velocità di riferimento $\dot{q}_r$ maggiore, imponendo una forza di attuazione che spinge il sistema a ridurre l'errore a zero.

#### Definizioni Frequenti: Errore Filtrato $\sigma$

Possiamo definire una nuova variabile di stato sintetica, detta **Errore Filtrato (Filtered Tracking Error)** $\sigma$, espressa come:

$$\sigma = \dot{q}_r - \dot{q} = \dot{\tilde{q}} + \Lambda \tilde{q}$$

Notiamo che $\sigma$ rappresenta direttamente la differenza tra la nuova velocità di riferimento e la velocità attuale del sistema.

---

### Formulazione della Legge di Controllo e Dinamica in Anello Chiuso

Sostituiamo ora i termini nominali di velocità $\dot{q}_d$ e accelerazione $\ddot{q}_d$ con i rispettivi termini di riferimento $\dot{q}_r$ e $\ddot{q}_r$ all'interno della legge di controllo.

Derivando $\dot{q}_r$ rispetto al tempo, otteniamo l me **Accelerazione di Riferimento (Reference Acceleration)** $\ddot{q}_r$:

$$\ddot{q}_r = \ddot{q}_d + \Lambda \dot{\tilde{q}}$$

#### Legge di Controllo nel Caso Ideale
Assumendo di avere una **conoscenza perfetta dei parametri dinamici** del sistema ($B(q)$ matrice d'inerzia, $C(q, \dot{q})$ matrice dei termini di Coriolis e centrifughi, $g(q)$ vettore di gravità), la legge di controllo proposta è:

$$u = B(q)\ddot{q}_r + C(q,\dot{q})\dot{q}_r + g(q) + K_D \sigma$$

Questa struttura rappresenta una combinazione ibrida tra un'azione di avanzamento (*Feedforward Term*) basata sulle traiettorie di riferimento modificate e un'azione di retroazione (*Feedback Term*) sull'errore filtrato $\sigma$.

#### Equazione del Sistema in Anello Chiuso
Sostituendo l'azione di controllo $u$ all'interno del modello dinamico del robot $B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = u$, otteniamo:

$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = B(q)\ddot{q}_r + C(q,\dot{q})\dot{q}_r + g(q) + K_D \sigma$$

Semplificando $g(q)$ e riorganizzando i termini:

$$B(q)(\ddot{q}_r - \ddot{q}) + C(q,\dot{q})(\dot{q}_r - \dot{q}) + K_D \sigma = 0$$

Poiché $\dot{q}_r - \dot{q} = \sigma$ e $\ddot{q}_r - \ddot{q} = \dot{\sigma}$, l'equazione differenziale che governa la dinamica dell'errore in anello chiuso (*Closed-loop Error Dynamics*) diventa semplicemente:

$$B(q)\dot{\sigma} + C(q,\dot{q})\sigma + K_D \sigma = 0$$

---

### Analisi di Stabilità di Lyapunov nel Caso Ideale

> **CONCETTO CHIAVE**
> Dimostrare che la legge di controllo modificata garantisce la **Stabilità Asintotica Globale (Global Asymptotic Stability - GAS)** del sistema nell'ipotesi di conoscenza perfetta dei parametri dinamici. La convergenza dell'errore filtrato $\sigma \to 0$ implica direttamente che sia l'errore di posizione che l'errore di velocità tendono asintoticamente a zero ($\tilde{q} \to 0, \dot{\tilde{q}} \to 0$).

Per effettuare la prova di stabilità, applichiamo il **Metodo Diretto di Lyapunov (Lyapunov Direct Method)**.

#### 1. Scelta della Funzione di Lyapunov Candidata
Definiamo la funzione scalare $V(\sigma, \tilde{q})$ come somma di un termine di energia cinetica espresso rispetto a $\sigma$ e di una forma quadratica dipendente dall'errore di posizione $\tilde{q}$:

$$V(\sigma, \tilde{q}) = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{q}^T M \tilde{q}$$

dove $M$ è una matrice definita positiva da progettare ($M > 0$).

* **Proprietà di $V$:** Poiché $B(q) > 0$ e $M > 0$, la funzione $V(\sigma, \tilde{q})$ è strettamente definita positiva ($V > 0$ per ogni $(\sigma, \tilde{q}) \neq (0,0)$) e si annulla solo nell'origine $(\sigma = 0, \tilde{q} = 0)$.

#### 2. Calcolo della Derivata Temporale $\dot{V}$
Calcoliamo la derivata temporale lungo le traiettorie del sistema:

$$\dot{V} = \sigma^T B(q) \dot{\sigma} + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

Dall'equazione dinamica dell'errore sappiamo che $B(q)\dot{\sigma} = -C(q,\dot{q})\sigma - K_D \sigma$. Sostituendo questa relazione nella derivata:

$$\dot{V} = \sigma^T \left( -C(q,\dot{q})\sigma - K_D \sigma \right) + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

Raggruppando i termini quadratici in $\sigma$:

$$\dot{V} = \frac{1}{2} \sigma^T \left( \dot{B}(q) - 2C(q,\dot{q}) \right) \sigma - \sigma^T K_D \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

Sfruttando la proprietà fondamentale di **antisimmetria (*Skew-Symmetry*)** della matrice $(\dot{B}(q) - 2C(q,\dot{q}))$, il primo termine si annulla identicamente:

$$\frac{1}{2} \sigma^T \left( \dot{B}(q) - 2C(q,\dot{q}) \right) \sigma = 0$$

La derivata temporale si riduce quindi a:

$$\dot{V} = -\sigma^T K_D \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

#### 3. Sostituzione ed Eliminazione dei Termini Incrociati
*(Nota: Passaggio integrato con chiarezza didattica per esplicitare i passaggi algebrici condotti a lezione)*.

Sostituiamo la definizione $\sigma = \dot{\tilde{q}} + \Lambda \tilde{q}$ all'interno del termine $-\sigma^T K_D \sigma$:

$$-\sigma^T K_D \sigma = -(\dot{\tilde{q}} + \Lambda \tilde{q})^T K_D (\dot{\tilde{q}} + \Lambda \tilde{q}) = -\dot{\tilde{q}}^T K_D \dot{\tilde{q}} - 2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}} - \tilde{q}^T \Lambda K_D \Lambda \tilde{q}$$

Sostituendo questo sviluppo nell'espressione di $\dot{V}$:

$$\dot{V} = -\dot{\tilde{q}}^T K_D \dot{\tilde{q}} - 2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}} - \tilde{q}^T \Lambda K_D \Lambda \tilde{q} + \tilde{q}^T M \dot{\tilde{q}}$$

Sfruttando il grado di libertà nella scelta della matrice definita positiva $M$, poniamo specificamente:

$$M = 2 \Lambda K_D$$

Con questa scelta opportuna, il termine $+ \tilde{q}^T M \dot{\tilde{q}} = 2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}}$ cancella esattamente il termine incrociato $-2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}}$.

L'espressione finale della derivata di Lyapunov diventa:

$$\dot{V} = -\dot{\tilde{q}}^T K_D \dot{\tilde{q}} - \tilde{q}^T \Lambda K_D \Lambda \tilde{q}$$

#### 4. Conclusione sulla Stabilità
Poiché $K_D > 0$ e $\Lambda > 0$, la matrice $K_D$ e la matrice $\Lambda K_D \Lambda$ sono entrambe definite positive.

Di conseguenza, $\dot{V}$ risulta essere **strettamente definita negativa** ($\dot{V} < 0$) per qualsiasi configurazione dell'errore diversa dall'origine:

$$\dot{V} < 0 \quad \forall (\tilde{q}, \dot{\tilde{q}}) \neq (0,0)$$

Questo dimostra formalmente che lo stato d'errore converge asintoticamente e globalmente all'origine:

$$\lim_{t \to \infty} \tilde{q}(t) = 0 \quad \text{e} \quad \lim_{t \to \infty} \dot{\tilde{q}}(t) = 0$$

Con questo risultato abbiamo confermato matematicamente che la modifica della traiettoria di riferimento garantisce il tracciamento perfetto senza deriva di posizione nel caso ideale. Il passo successivo sarà estendere questa formulazione al caso in cui la conoscenza dei parametri dinamici sia imperfetta, introducendo le leggi di **Controllo Adattativo (Adaptive Control)**.

---

### Controllo Adattativo basato su Modello (Adaptive Control)

#### Motivazione e Formulazione del Problema
Nelle lezioni precedenti è stata analizzata la legge di controllo sotto l'ipotesi di conoscenza perfetta dei parametri dinamici del sistema. Nella realtà, tuttavia, si dispone quasi sempre di una conoscenza approssimata del modello. 

L'obiettivo del **Controllo Adattativo (*Adaptive Control*)** è aggiornare in tempo reale la stima dei parametri del sistema durante il funzionamento dell'algoritmo, in modo da garantire l'annullamento dell'errore di inseguimento (*tracking error*), compensando le incertezze modellistiche.

Sfruttando la proprietà di **parametrizzazione lineare (*linear parameterization*)**, la dinamica reale del sistema e il modello approssimato possono essere espressi tramite la matrice regressore $Y$ e il vettore dei parametri dinamici $\pi$:

*   **Modello Reale:** $B(q)\ddot{q} + C(q, \dot{q})\dot{q} + F\dot{q} + g(q) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\pi$
*   **Modello Stimato:** $\hat{B}(q)\ddot{q}_r + \hat{C}(q, \dot{q})\dot{q}_r + \hat{F}\dot{q}_r + \hat{g}(q) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi}$

Dove $\hat{\pi}$ rappresenta il vettore dei parametri stimati e la variabile di riferimento per la velocità $\dot{q}_r$ è definita in funzione dell'errore di posizione $\tilde{q} = q_d - q$ e della matrice di guadagno $\Lambda$:

$$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$

La variabile di errore filtrato $\sigma$ è definita come:

$$\sigma = \dot{q}_r - \dot{q} = \dot{\tilde{q}} + \Lambda \tilde{q}$$

Proponiamo la seguente legge di controllo adattativa:

$$u = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi} + K_D \sigma$$

con $K_D$ matrice definita positiva.

---

#### Legge di Controllo e Dinamica dell'Errore
Sostituendo la legge di controllo $u$ all'interno della dinamica reale del sistema, si ricava la dinamica a ciclo chiuso dell'errore. 

> **Nota:** *Passaggio integrato con chiarezza didattica.*
> Sommando e sottraendo i termini della dinamica reale valutati su $\dot{q}_r$ e $\ddot{q}_r$, possiamo esprimere l'equazione del sistema in funzione dell'errore filtrato $\sigma$ e del **mismatch parametrico (*model mismatch*)** $\tilde{\pi} = \hat{\pi} - \pi$:

$$B(q)\dot{\sigma} + C(q, \dot{q})\sigma + F\sigma + K_D \sigma = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\tilde{\pi}$$

Dove $\tilde{\pi} = \hat{\pi} - \pi$ rappresenta l'errore di stima sui parametri (notando che $\dot{\tilde{\pi}} = \dot{\hat{\pi}}$, in quanto il valore reale dei parametri $\pi$ è considerato costante nel tempo).

---

#### Analisi di Stabilità di Lyapunov e Legge di Aggiornamento
Per ricavare la **legge di aggiornamento (*update law*)** continua nel tempo per il vettore dei parametri stimati $\hat{\pi}$, utilizziamo la teoria di stabilità di Lyapunov. 

Scegliiamo una **Funzione Candidata di Lyapunov (*Lyapunov Function Candidate*)** definita positiva, costituita dalla somma dell'energia cinetica associata all'errore e da un termine quadratico legato al mismatch parametrico:

$$V(\sigma, \tilde{\pi}) = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{\pi}^T K_\pi \tilde{\pi}$$

dove $K_\pi$ è una matrice definita positiva personalizzabile.

Calcoliamo la derivata temporale di $V$:

$$\dot{V} = \sigma^T B(q) \dot{\sigma} + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{\pi}^T K_\pi \dot{\tilde{\pi}}$$

Sostituendo la dinamica dell'errore $B(q)\dot{\sigma} = - C(q, \dot{q})\sigma - F\sigma - K_D \sigma + Y\tilde{\pi}$ e sfruttando la proprietà di antisimmetria della matrice $(\dot{B} - 2C)$, i termini $C(q, \dot{q})$ si elidono:

$$\dot{V} = -\sigma^T (K_D + F) \sigma + \sigma^T Y \tilde{\pi} + \tilde{\pi}^T K_\pi \dot{\hat{\pi}}$$

Raggruppando i termini contenenti l'errore parametrico $\tilde{\pi}$:

$$\dot{V} = -\sigma^T (K_D + F) \sigma + \tilde{\pi}^T \left( Y^T \sigma + K_\pi \dot{\hat{\pi}} \right)$$

Per rendere la derivata di Lyapunov definita negativa o semidefinita negativa, annulliamo il termine tra parentesi imponendo la seguente **legge di aggiornamento adattativa**:

$$Y^T \sigma + K_\pi \dot{\hat{\pi}} = 0 \implies \dot{\hat{\pi}} = - K_\pi^{-1} Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma$$

Sostituendo tale legge di aggiornamento, la derivata temporale diventa:

$$\dot{V} = -\sigma^T (K_D + F) \sigma \le 0$$

Poiché $\dot{V} \le 0$, il sistema è stabile nel senso di Lyapunov e, per il Teorema di Barbalat, l'errore filtrato $\sigma \to 0$ per $t \to \infty$. Di conseguenza, si garantisce che:

$$\tilde{q} \to 0 \quad \text{e} \quad \dot{\tilde{q}} \to 0 \quad \text{per } t \to \infty$$

ossia l'errore di inseguimento converge esattamente a zero.

---

### Convergenza dei Parametri e Aggiustamento del Modello

#### Errore di Inseguimento vs Convergenza dei Parametri
Un aspetto fondamentale del controllo adattativo risiede nella distinzione tra la convergenza dell'errore di inseguimento e la convergenza dell'errore di stima parametrica.

> **Concetto Chiave**
> L'annullamento dell'errore di inseguimento ($\tilde{q} \to 0$) **NON garantisce** che il vettore dei parametri stimati $\hat{\pi}$ converga al valore reale $\pi$. 
> La teoria di Lyapunov garantisce solo che $\hat{\pi}$ rimanga limitato e converga a un valore costante $\bar{\pi}$, ma non è detto che $\bar{\pi} = \pi$.

All'equilibrio ($\sigma = 0$), la legge di aggiornamento si annulla ($\dot{\hat{\pi}} = 0$). Se la condizione $Y\tilde{\pi} = 0$ viene soddisfatta con un vettore $\tilde{\pi} \neq 0$ appartenente al nucleo (*kernel*) del regressore $Y$, il sistema raggiungerà un errore di inseguimento pari a zero pur mantenendo una conoscenza imperfetta dei parametri dinamici.

---

#### Il Ruolo della Traiettoria: Eccitazione Persistente (Persistent Excitation)
Affinché la stima dei parametri $\hat{\pi}$ converga al valore reale $\pi$, la traiettoria di riferimento desiderata $q_d(t)$ deve essere **sufficientemente ricca (*persistently exciting*)** da eccitare tutti i modi dinamici del sistema.

*   **Sistemi Lineari:** Esistono condizioni analitiche precise sulla frequenza e sulla forma d'onda del segnale per garantire l'eccitazione di tutte le dinamiche.
*   **Sistemi Non Lineari:** Trovare condizioni teoriche *a priori* per l'eccitazione persistente è estremamente complesso. In genere si ricorre all'iniezione di traiettorie composte da armoniche a differenti frequenze (es. somme di sinusoidi) per stimare accuratamente la dinamica del sistema.

---

### Esempio Pratico: Pendolo Inverso con Attrito

#### Modellistica e Parametrizzazione Lineare
Consideriamo il modello di un pendolo inverso con presenza di attrito viscoso al giunto:

$$I \ddot{\theta} + M g d \sin(\theta) + F_v \dot{\theta} = u$$

Possiamo decomporre l'equazione nel prodotto tra la matrice regressore $Y$ e il vettore dei parametri $\pi \in \mathbb{R}^3$:

$$Y(\theta, \dot{\theta}, \dot{\theta}_r, \ddot{\theta}_r) = \begin{bmatrix} \ddot{\theta}_r & \sin(\theta) & \dot{\theta}_r \end{bmatrix}, \quad \pi = \begin{bmatrix} I \\ M g d \\ F_v \end{bmatrix}$$

Le leggi combinate di controllo e adattamento sono:

$$u = Y \hat{\pi} + K_D \sigma, \quad \dot{\hat{\pi}} = - K_\pi^{-1} Y^T \sigma$$

con $\sigma = (\dot{\theta}_d - \dot{\theta}) + \lambda (\theta_d - \theta)$.

---

#### Confronto tra Traiettorie (Sinusoide vs Traiettoria Bang-Bang)
Vengono simulate due differenti traiettorie di riferimento $q_d(t)$ per valutare le prestazioni del controllo e la stima dei parametri:

1.  **Traiettoria Sinusoidale a Singola Frequenza:** $\theta_d(t) = A \sin(\omega t)$
2.  **Traiettoria Bang-Bang in Accelerazione:** Profilo di accelerazione alternato $\ddot{\theta}_d(t) \in \{+1, -1\}$ a intervalli regolari (ricco di componenti armoniche).

| Metrica di Prestazione | Traiettoria Sinusoidale (Singola Frequenza) | Traiettoria Bang-Bang (Ricca) |
| :--- | :--- | :--- |
| **Errore di Posizione/Velocità ($\tilde{q}, \dot{\tilde{q}}$)** | Converge a zero ($\to 0$) | Converge a zero ($\to 0$) con transitori più nervosi |
| **Andamento della Coppia ($u$)** | Fluido e quasi-sinusoidale | Estremamente dinamico |
| **Convergenza dei Parametri ($\hat{\pi} \to \pi$)** | **Incompleta:** L'attrito $F_v$ viene stimato, ma permangono errori su Inerzia $I$ e massa $M g d$. | **Completa:** Tutti e 3 i parametri stimati convergono ai valori reali. |

**Conclusione dall'esempio:** In entrambi i casi l'obiettivo principale del controllo (azzeramento dell'errore di inseguimento) viene raggiunto. Tuttavia, solo la traiettoria *Bang-Bang*, grazie alla sua maggior ricchezza spettrale (*Persistent Excitation*), permette la completa identificazione dei parametri dinamici reali del sistema.