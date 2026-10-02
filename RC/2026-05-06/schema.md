# Modellistica della Telecamera e Proiezione Prospettica per il Servoing Visivo

## Overview Didattica

In questa lezione viene introdotta la modellistica geometrica e ottica dei sensori di visione, elemento propedeutico fondamentale per le tecniche di **Controllo Visivo** (*Visual Servoing*). Dopo una breve contestualizzazione sulle modalità di impiego dell'informazione visiva per la stima della posa 3D del manipolatore, l'analisi si sposta sulla formalizzazione analitica del processo di formazione dell'immagine.

I concetti chiave trattati includono:
- **Modello della Lente Sottile** (*Thin Lens Model*): descrizione del comportamento dei raggi luminosi che attraversano la lente, definizione dell'asse ottico (*Optical Axis*), della lunghezza focale (*Focal Length*) e relazione fondamentale delle lenti.
- **Sistemi di Riferimento della Telecamera** (*Camera Reference Frames*): definizione geometrica della terna solida alla telecamera (*Camera Frame*), posizionata nel centro ottico, e delle relative trasformazioni omogenee rispetto alla terna base (*Base Frame*).
- **Piano Immagine e Proiezione Prospettica** (*Image Plane and Perspective Projection*): definizione del sistema di riferimento piano e derivazione delle equazioni di proiezione delle coordinate spaziali $3D$ sul piano immagine $2D$ mediante relazioni di similitudine geometrica.

---

### Introduzione al Servoing Visivo e Modello di Telecamera

Nel contesto del **Servoing Visivo** (*Visual Servoing*), esistono principalmente due approcci metodologici:
1. **Ricostruzione della posa 3D:** Si utilizzano le informazioni estratte dalle telecamere per ricostruire la posa tridimensionale del manipolatore rispetto al mondo (o al target). Una volta stimata la posa 3D, si applicano direttamente le tecniche di controllo cinematico o dinamico convenzionali.
2. **Controllo basato direttamente sulle immagini:** Si utilizzano le caratteristiche estratte dal piano immagine per generare la legge di controllo senza un passaggio intermedio di ricostruzione 3D esplicita.

Per comprendere a fondo entrambi i metodi, è necessario formalizzare il modello matematico con cui la realtà tridimensionale viene proiettata sul piano bidimensionale del sensore.

#### Cos'è una Telecamera?
Una telecamera è un dispositivo optoelettronico il cui scopo fondamentale è misurare l'intensità della luce riflessa dagli oggetti presenti nella scena. 
* **Pixel:** L'elemento fotosensibile di base che trasforma l'intensità luminosa incidente in un segnale/vettore elettrico.
* **Lente (*Lens*):** Elemento ottico con il compito di focalizzare i raggi luminosi riflessi dall'oggetto sul **Piano Immagine** (*Image Plane*), ossia il piano fisico su cui sono disposti i pixel.

---

### Il Modello della Lente Sottile (*Thin Lens Model*)

Per analizzare il comportamento ottico di base della telecamera, si adotta il modello ideale della **Lente Sottile** (*Thin Lens Model*).

```
                 Asse Ottico (Zc)
        \               |               /
         \              |              /
          \             |             /
-----------+------------O------------+-----------  Lente
            \           |           /
             \          |          /
              \         |         /
               *--------+--------*
                  Fuoco |  Fuoco
                    λ   |    λ
```

Geometria della lente sottile:
* **Asse Ottico (*Optical Axis*):** L'asse perpendicolare alla lente e passante per il suo centro geometrico.
* **Centro Ottico ($O_c$):** Il centro della lente. I raggi luminosi che passano esattamente per $O_c$ **non subiscono alcuna deviazione** e proseguono rettilinei.
* **Lunghezza Focale ($\lambda$ o $f$):** Tutti i raggi paralleli all'asse ottico che attraversano la lente vengono deviati e convergono in un punto situato sull'asse ottico a una distanza $\lambda$ dalla lente, detto **Punto Focale** (*Focal Point*).

#### Equazione Fondamentale della Lente Sottile
La relazione fondamentale che lega la distanza dell'oggetto dalla lente, la distanza del piano immagine e la lunghezza focale è definita come:

$$\frac{1}{Z} + \frac{1}{z} = \frac{1}{\lambda}$$

Dove:
* $Z$: Distanza fisica dell'oggetto reale rispetto alla lente (lungo l'asse ottico).
* $z$: Distanza del piano immagine (dove si forma l'immagine a fuoco) rispetto alla lente.
* $\lambda$: Lunghezza focale (*Focal Length*) della lente.

---

### Sistemi di Riferimento e Geometria della Proiezione

Per esprimere analiticamente la proiezione dei punti della scena nell'immagine, definiamo i seguenti sistemi di riferimento ortonormali:

1. **Sistema di Riferimento Telecamera $\{C\}$ ($O_c - X_c Y_c Z_c$):**
   * **Origine ($O_c$):** Coincide con il centro ottico della lente.
   * **Asse $Z_c$:** Orientato lungo l'asse ottico, uscente verso la scena.
   * **Assi $X_c, Y_c$:** Giacciono sul piano della lente e formano una terna destrorsa.

2. **Sistema di Riferimento Base $\{B\}$:** Il sistema di riferimento fisso del mondo/robot. La relazione tra un punto espresso in coordinate base $P_b$ e in coordinate telecamera $P_c$ è descritta dalla matrice di trasformazione omogenea $T_c^b$:

   $$\tilde{P}_c = T_c^b \tilde{P}_b$$

   dove $\tilde{P} = [X, Y, Z, 1]^T$ rappresenta la rappresentazione in coordinate omogenee.

3. **Sistema di Riferimento Piano Immagine ($X_f - Y_f$):**
   * Posizionato a una distanza $f$ (lunghezza focale) dalla lente lungo l'asse ottico $Z_c$.
   * L'origine è posta nell'intersezione tra l'asse ottico $Z_c$ e il piano immagine.
   * Gli assi $X_f$ e $Y_f$ sono scelti paralleli rispettivamente agli assi $X_c$ e $Y_c$.

---

### Proiezione Prospettica (*Perspective Projection*)

Consideriamo un punto generico $P$ nello spazio 3D, le cui coordinate nel sistema telecamera sono $P_c = [X_c, Y_c, Z_c]^T$. Per determinare le coordinate del corrispondente punto proiettato sul piano immagine $(x_f, y_f)$, si traccia il raggio ottico passante per $P_c$ e per il centro ottico $O_c$.

Sfruttando la similitudine tra triangoli geometrici formati dal raggio con l'asse ottico, si ottiene la **Trasformazione Prospettica** (*Perspective Transformation*):

$$x_f = -f \frac{X_c}{Z_c}$$

$$y_f = -f \frac{Y_c}{Z_c}$$

#### Il Piano Immagine Virtuale
Il segno meno nelle equazioni precedenti rispecchia il fatto fisico che le immagini proiettate dietro la lente risultano capovolte (sia sull'asse $X$ che sull'asse $Y$).

Per semplificare la trattazione matematica ed eliminare il segno negativo, si adotta convenzionalmente il modello del **Piano Immagine Virtuale** (*Virtual Image Plane*), ipotizzando che il piano immagine si trovi *davanti* al centro ottico a una distanza $+f$ lungo l'asse $Z_c$.

Le equazioni della proiezione prospettica diventano quindi:

$$x_f = f \frac{X_c}{Z_c}$$

$$y_f = f \frac{Y_c}{Z_c}$$

---

### Proprietà Fondamentali e Singolarità

Dall'analisi delle equazioni di proiezione prospettica emergono due proprietà fondamentali per il controllo visivo:

> **CONCETTO CHIAVE: Perdita dell'Informazione di Profondità (*Loss of Depth Information*)**
> Le coordinate proiettate $(x_f, y_f)$ dipendono unicamente dal rapporto tra le coordinate trasversali ($X_c, Y_c$) e la profondità ($Z_c$). 
> Di conseguenza, tutti i punti dello spazio 3D situati lungo la medesima retta passante per il centro ottico $O_c$ vengono proiettati esattamente nello **stesso identico punto** sul piano immagine. Questo fenomeno comporta la perdita irreversibile dell'informazione sulla terza dimensione (la profondità $Z_c$) all'interno di una singola immagine 2D.

> **CONCETTO CHIAVE: Singolarità della Trasformazione Prospettica**
> La trasformazione prospettica non è definita per punti in cui $Z_c = 0$. 
> Se un punto $P$ si trova sul piano $X_c - Y_c$ (ovvero giace sul piano della lente passante per l'origine $O_c$), il raggio passante per $P$ e per il centro ottico giace interamente su tale piano ed è parallelo al piano immagine. Non esistendo alcun punto di intersezione, la trasformazione prospettica diventa matematicamente **singolare**.

---

### Coordinate Pixel e Parametri Intrinseci

Nel passaggio dal modello teorico della telecamera al sensore reale, occorre considerare che l'immagine viene digitalizzata. In un sensore reale, la superficie di proiezione è discretizzata in pixel (Coordinate Pixel - *Pixel Coordinates*). Pertanto, le coordinate di un punto sul piano immagine non sono numeri continui, ma valori quantizzati identificati con $(X_i, Y_i)$.

Per passare dalle coordinate continue del piano immagine $(x_f, y_f)$ alle coordinate discrete in pixel $(X_i, Y_i)$, introduciamo due fattori fisici:
1. **Fattori di scala ($\alpha_x, \alpha_y$):** Rappresentano il numero di pixel per unità di lunghezza (o l'inverso della dimensione fisica del pixel) lungo gli assi $X$ e $Y$.
2. **Offset o Centro Principale ($x_0, y_0$):** Nell'immagine reale, l'origine del sistema di riferimento delle coordinate pixel $(0,0)$ non si trova al centro dell'asse ottico, ma tipicamente in un angolo del sensore (es. in alto a sinistra o in basso a sinistra). L'offset $(x_0, y_0)$ rappresenta quindi la posizione del centro ottico espressa nel sistema di riferimento dei pixel.

Le equazioni di trasformazione dalle coordinate metriche $(x_f, y_f)$ alle coordinate pixel $(X_i, Y_i)$ sono:

$$X_i = \alpha_x x_f + x_0$$
$$Y_i = \alpha_y y_f + y_0$$

Ricordando la trasformazione prospettica ideale dove $x_f = f \frac{X_c}{Z_c}$ e $y_f = f \frac{Y_c}{Z_c}$ (con $f$ lunghezza focale - *Focal Length*, e $P^c = [X_c, Y_c, Z_c]^T$ coordinate del punto nel sistema di riferimento telecamera), possiamo riscrivere le coordinate pixel come una relazione non lineare:

$$X_i = \alpha_x f \frac{X_c}{Z_c} + x_0$$
$$Y_i = \alpha_y f \frac{Y_c}{Z_c} + y_0$$

---

### Rappresentazione Omogenea e Linearizzazione della Proiezione

La presenza della profondità $Z_c$ al denominatore rende la relazione di proiezione prospettica fortemente **non lineare**. Per rendere calcoli, stime e controlli analitici più semplici, si convertono queste equazioni in forma lineare utilizzando la **Rappresentazione Omogenea** (*Homogeneous Representation*).

Definiamo il vettore omogeneo delle coordinate pixel come $\tilde{m}_i = [X_i, Y_i, 1]^T$ e il punto $P^c$ in coordinate omogenee come $\bar{P}^c = [X_c, Y_c, Z_c, 1]^T$.

Possiamo esprimere il processo di proiezione tramite il prodotto di matrici introducendo un fattore di scala $\lambda$, dove **$\lambda = Z_c$** rappresenta la coordinata di profondità (*depth*) del punto nello spazio rispetto alla telecamera ($Z_c > 0$):

$$\lambda \begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \Sigma \Pi \bar{P}^c$$

Dove:
* **$\Sigma$ (Matrice dei Parametri Intrinseci - *Intrinsic Parameters Matrix*):** È una matrice $3 \times 3$ che racchiude la geometria interna della telecamera.
* **$\Pi$ (Matrice di Proiezione Standard - *Canonical Projection Matrix*):** È una matrice $3 \times 4$ definita come $\Pi = \begin{bmatrix} I_{3 \times 3} & \mathbf{0}_{3 \times 1} \end{bmatrix}$.

La matrice dei parametri intrinseci $\Sigma$ ha la seguente struttura:

$$\Sigma = \begin{bmatrix} \alpha_x f & 0 & x_0 \\ 0 & \alpha_y f & y_0 \\ 0 & 0 & 1 \end{bmatrix}$$

#### Dimostrazione ed Esplicitazione dei Passaggi
*Nota: Passaggio integrato con chiarezza didattica.*

Svolgiamo la moltiplicazione $\Sigma \cdot \Pi$:

$$\Sigma \Pi = \begin{bmatrix} \alpha_x f & 0 & x_0 \\ 0 & \alpha_y f & y_0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} = \begin{bmatrix} \alpha_x f & 0 & x_0 & 0 \\ 0 & \alpha_y f & y_0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix}$$

Moltiplicando ora questa matrice $3 \times 4$ per il punto $\bar{P}^c = [X_c, Y_c, Z_c, 1]^T$:

$$\begin{bmatrix} \alpha_x f & 0 & x_0 & 0 \\ 0 & \alpha_y f & y_0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} \begin{bmatrix} X_c \\ Y_c \\ Z_c \\ 1 \end{bmatrix} = \begin{bmatrix} \alpha_x f X_c + x_0 Z_c \\ \alpha_y f Y_c + y_0 Z_c \\ Z_c \end{bmatrix}$$

Uguagliando al membro di sinistra $\lambda [X_i, Y_i, 1]^T$ e ponendo $\lambda = Z_c$:

$$\begin{bmatrix} Z_c X_i \\ Z_c Y_i \\ Z_c \end{bmatrix} = \begin{bmatrix} \alpha_x f X_c + x_0 Z_c \\ \alpha_y f Y_c + y_0 Z_c \\ Z_c \end{bmatrix}$$

Dividendo la prima e la seconda riga per $Z_c$, ritroviamo esattamente le equazioni di partenza:
$$X_i = \alpha_x f \frac{X_c}{Z_c} + x_0$$
$$Y_i = \alpha_y f \frac{Y_c}{Z_c} + y_0$$

Questo conferma la validità formale dell'uso delle coordinate omogenee per linearizzare il modello di proiezione.

---

### La Matrice di Calibrazione della Telecamera

Un punto $P$ descritto originariamente rispetto a un sistema di riferimento base (*Base Frame*) $P^b$ può essere trasformato nel riferimento telecamera $P^c$ mediante la matrice di trasformazione omogenea $T_b^c$:

$$\bar{P}^c = T_b^c \bar{P}^b = \begin{bmatrix} R_b^c & t_b^c \\ \mathbf{0}^T & 1 \end{bmatrix} \bar{P}^b$$

Sostituendo la relazione di rototraslazione all'interno della formula di proiezione, otteniamo l'equazione fondamentale della visione:

$$\lambda \begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \Sigma \Pi T_b^c \bar{P}^b$$

La matrice risultante dal prodotto viene comunemente definita **Matrice di Calibrazione della Telecamera** (*Camera Calibration Matrix*).

> **CONCETTO CHIAVE: Parametri Intrinseci vs Estrinseci**
>
> All'interno della Matrice di Calibrazione sono sintetizzate due distinte tipologie di informazioni:
> * **Parametri Intrinseci ($\Sigma$):** Dipendono unicamente dall'hardware della telecamera (lunghezza focale $f$, dimensioni dei pixel $\alpha_x, \alpha_y$, offset $x_0, y_0$). Non cambiano quando la telecamera si muove nello spazio.
> * **Parametri Estrinseci ($T_b^c$):** Identificano la posa relativa (rotazione $R_b^c$ e traslazione $t_b^c$) del sistema di riferimento della telecamera rispetto al sistema di riferimento mondo/base.

---

### Coordinate Normalizzate

Nello sviluppo di algoritmi di controllo visivo (*Vision-Based Control*) e nell'analisi analitica, si fa ampio uso delle **Coordinate Normalizzate** (*Normalized Coordinates*).

#### Definizione
Le coordinate normalizzate $(x, y)$ sono le coordinate che si otterrebbero sul piano immagine se la telecamera avesse una **lunghezza focale unitaria ($f = 1$)** e se il sensore non introducesse distorsioni, traslazioni dell'origine o fattori di scala ($\alpha_x = \alpha_y = 1$, $x_0 = y_0 = 0$).

Geometricamente, corrispondono alle coordinate del punto proiettato su un piano posto a distanza unitaria ($Z=1$) davanti al centro ottico della telecamera:

$$x = \frac{X_c}{Z_c}$$
$$y = \frac{Y_c}{Z_c}$$

 In coordinate omogenee, il vettore delle coordinate normalizzate è definito come:

$$\tilde{m}_n = \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}$$

#### Relazione tra Coordinate Pixel e Coordinate Normalizzate
Sfruttando la matrice dei parametri intrinseci $\Sigma$, è possibile legare direttamente le coordinate pixel misurate $(X_i, Y_i)$ alle coordinate normalizzate $(x, y)$:

$$\begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \Sigma \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}$$

Esplicitando il prodotto:

$$\begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \begin{bmatrix} \alpha_x f & 0 & x_0 \\ 0 & \alpha_y f & y_0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \begin{bmatrix} \alpha_x f x + x_0 \\ \alpha_y f y + y_0 \\ 1 \end{bmatrix}$$

#### Uso Pratico
In un'applicazione reale, la telecamera misura direttamente i punti sull'immagine sotto forma di **coordinate pixel** $(X_i, Y_i)$. Conoscendo i parametri intrinseci della telecamera (tramite un processo preventivo di calibrazione che fornisce la matrice $\Sigma$), possiamo invertire la relazione per calcolare le corrispettive **coordinate normalizzate**:

$$\begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \Sigma^{-1} \begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix}$$

Questo passaggio è fondamentale: ci permette di svincolare l'algoritmo di controllo e l'elaborazione dati dalle specifiche caratteristiche fisiche del sensore impiegato, lavorando con coordinate puramente geometriche.

---

### Configurazione del Sistema e Approcci al Controllo Servovisivo

Nel controllo basato su visione (*Visual Servoing*), l'obiettivo principale è guidare l'organo terminale (*End-Effector*) di un manipolatore da una configurazione iniziale a una posizione finale desiderata utilizzando esclusivamente le informazioni estratte dai sensori visivi.

La configurazione di riferimento trattata è quella di tipo **Eye-in-Hand** (Telecamera sull'Organo Terminale), in cui la telecamera è montata in modo solidale all'organo terminale del robot.

```
 [ Base Frame {b} ]
        |
        v (Cinematica Robot)
 [ End-Effector {e} ] === (Vincolo Rigido Costante) ===> [ Camera Frame {c} ]
                                                               |
                                                        (Piani di Visione)
                                                               v
                                                      [ Object Frame {o} ]
```

Da questa disposizione rigida derivano alcune proprietà fondamentali:
* Il telaio di riferimento della telecamera $\{c\}$ si muove e ruota in modo perfettamente solidale con il telaio dell'organo terminale $\{e\}$.
* La posa relativa tra il telaio della telecamera e quello dell'organo terminale (espressa dalla matrice di trasformazione omogenea $\mathbf{T}_c^e$) è **costante nel tempo**.
* Gli assi dei due telai non devono necessariamente essere paralleli; è sufficiente che la loro relazione spaziale sia rigida e nota.

Per risolvere il problema del posizionamento tramite visione esistono due filosofie principali:

1. **Controllo Servovisivo Basato sulla Posizione (Position-Based Visual Servoing - PBVS):**
   Utilizza le immagini acquisite dalla telecamera per ricostruire analiticamente la posa 3D relativa tra il telaio della telecamera e un telaio di riferimento del mondo o dell'oggetto. Una volta stimata la posizione e l'orientamento nello spazio 3D, si applicano gli algoritmi di controllo cinematico classici.
2. **Controllo Servovisivo Basato sulle Immagini (Image-Based Visual Servoing - IBVS):**
   L'errore di controllo viene calcolato ed eliminato direttamente sul piano immagine 2D, senza passare attraverso la ricostruzione tridimensionale della posa.

---

### Formulazione del Problema di Ricostruzione della Posa (PBVS)

> **CONCETTO CHIAVE**
> L'obiettivo primario dell'approccio PBVS descritto analiticamente è ricostruire la posa relativa tra il telaio di riferimento della telecamera $\{c\}$ e il telaio di riferimento fisso all'oggetto $\{o\}$, sfruttando esclusivamente le proiezione 2D di punti noti (*feature*) sul piano immagine normalizzato.

#### Modello dello Scenario di Lavoro
Consideriamo il seguente scenario operativo:
* Un oggetto statico (non in movimento) presente nell'ambiente.
* Sullo stesso oggetto sono definiti ed estratti un insieme di punti di interesse geometrici (*keypoints* o *feature* 3D).
* Mediante algoritmi di rilevamento punti (*Point Detectors* / *Feature Extractors*), vengono individuate le proiezioni di questi punti sul piano immagine.
* Le *feature* utilizzate dal sistema sono le **coordinate normalizzate** ($\tilde{x}, \tilde{y}$) di tali punti sul piano immagine della telecamera.

#### Telai di Riferimento e Catena di Trasformazioni Omogenee
Per formalizzare il problema si definiscono quattro sistemi di coordinate principali:
* **$\{b\}$ - Telaio della Base (Base Frame):** Sistema di riferimento fisso del robot.
* **$\{o\}$ - Telaio dell'Oggetto (Object Frame):** Sistema di riferimento solidale all'oggetto da tracciare.
* **$\{c\}$ - Telaio della Telecamera (Camera Frame):** Sistema di riferimento mobile con l'ottica.
* **$\{e\}$ - Telaio dell'Organo Terminale (End-Effector Frame):** Sistema di riferimento mobile dell'utensile.

Poiché l'oggetto è statico nello spazio di lavoro, la posa relativa tra il telaio della base $\{b\}$ e il telaio dell'oggetto $\{o\}$, rappresentata dalla matrice di trasformazione omogenea $\mathbf{T}_o^b$, è **costante e nota a priori**.

L'obiettivo analitico si articola nei seguenti passaggi:

1. Stima della trasformazione omogenea $\mathbf{T}_o^c$ (posa dell'oggetto rispetto alla telecamera) a partire dalle coordinate normalizzate dei punti sul piano immagine.
2. Calcolo della posa della telecamera rispetto alla base $\mathbf{T}_c^b$ invertendo la catena cinematica:
   $$\mathbf{T}_c^b = \mathbf{T}_o^b \cdot (\mathbf{T}_o^c)^{-1}$$
3. Determinazione della posa dell'organo terminale rispetto alla base $\mathbf{T}_e^b$, nota la relazione rigida costante $\mathbf{T}_c^e$:
   $$\mathbf{T}_e^b = \mathbf{T}_c^b \cdot (\mathbf{T}_c^e)^{-1}$$

Una volta ricavata $\mathbf{T}_e^b$ istante per istante, la traiettoria dell'organo terminale può essere controllata mediante le consuete tecniche di controllo cinematico nello spazio operativo 3D.

---

### Definizione del Vettore di Feature e Coordinate Normalizzate

Per definire formalmente il problema della stima della posa da immagini, è necessario introdurre un *Vettore di Feature (Feature Vector)*, indicato con $s$. Tale vettore raccoglie le informazioni geometriche estratte dall'immagine.

Anziché utilizzare direttamente le coordinate in pixel $(u, v)$, si utilizzano le **coordinate normalizzate** $(x, y)$ sul piano immagine. 

$$\begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = K^{-1} \begin{bmatrix} u \\ v \\ 1 \end{bmatrix}$$

L'impiego delle coordinate normalizzate è una scelta fondamentale: assumendo nota la *Matrice dei Parametri Intrinseci (Intrinsic Parameters Matrix)* $K$ (o $\Sigma$), è possibile invertire la relazione tra pixel e coordinate fisiche. L'uso delle coordinate normalizzate semplifica notevolmente la derivazione analitica delle soluzioni.

#### Struttura del Vettore di Feature
* **Per un singolo punto $i$**: il vettore delle feature $s_i$ ha dimensione 2:
  $$s_i = \begin{bmatrix} x_i \\ y_i \end{bmatrix} \in \mathbb{R}^2$$

* **Per un insieme di $n$ punti**: il vettore di feature globale $s$ è ottenuto impilando (*stacking*) i vettori dei singoli punti. La sua dimensione $k$ sarà quindi $k = 2n$:
  $$s = \begin{bmatrix} s_1 \\ s_2 \\ \vdots \\ s_n \end{bmatrix} = \begin{bmatrix} x_1 \\ y_1 \\ x_2 \\ y_2 \\ \vdots \\ x_n \\ y_n \end{bmatrix} \in \mathbb{R}^{2n}$$

Per i calcoli geometrici e le proiezioni prospettiche risulta utile rappresentare il punto in **coordinate omogenee**, aggiungendo una terza coordinata unitaria:

$$\tilde{s}_i = \begin{bmatrix} x_i \\ y_i \\ 1 \end{bmatrix} \in \mathbb{R}^3$$

---

### Sistemi di Riferimento e Notazione Vettoriale

Per legare la geometria 3D dell'oggetto al piano immagine della telecamera, definiamo tre sistemi di riferimento principali:
1. **Terna Oggetto ($F_o$)**: fissata sull'oggetto, con origine $O_o$.
2. **Terna Telecamera ($F_c$)**: fissata al centro ottico della telecamera, con origine $O_c$.
3. **Terna Base ($F_b$)**: terna di riferimento assoluta/mondo, con origine $O_b$.

La relazione spaziale tra l'oggetto e la telecamera è espressa dalla *Matrice di Trasformazione Omogenea (Homogeneous Transformation Matrix)* $T_o^c \in \mathbb{R}^{4 \times 4}$:

$$T_o^c = \begin{bmatrix} R_o^c & o_{c,o}^c \\ 0_{1 \times 3} & 1 \end{bmatrix}$$

Dove:
* $R_o^c \in \mathbb{R}^{3 \times 3}$ è la matrice di rotazione che descrive l'orientamento relativo dell'oggetto rispetto alla telecamera.
* $o_{c,o}^c \in \mathbb{R}^3$ è il vettore traslazione che congiunge l'origine della telecamera $O_c$ con l'origine dell'oggetto $O_o$, espresso rispetto alla terna telecamera.

> **Concetto Chiave: Chiarimento sulla Notazione dei Vettori**
> 
> Un errore comune è valutare il vettore $o_c^c$ come un vettore nullo $(0,0,0)^T$. 
> * $o_c^c$ rappresenta il vettore che va dall'origine della **Terna Base** ($O_b$) all'origine della **Terna Telecamera** ($O_c$), espresso nelle coordinate della terna telecamera.
> * Analogamente, $o_o^c$ è il vettore dall'origine della Terna Base ($O_b$) all'origine dell'Oggetto ($O_o$), espresso nella terna telecamera.
> 
> Per la proprietà di composizione vettoriale vale infatti la relazione:
> $$o_{c,o}^c = o_o^c - o_c^c$$

---

### Formulazione del Problema di Stima della Posa (PnP)

Il problema fondamentale consiste nel determinare la matrice di trasformazione omogenea $T_o^c$ a partire da misurazioni visive 2D e informazioni geometriche 3D note a priori.

```
       [ Oggetto 3D ]  ---> Coordinate note a priori: r_{o,i}^o
             |
             v  (Proiezione Prospettica)
       [ Immagine 2D ] ---> Coordinate misurate in real-time: s_i = [x_i, y_i]^T
             |
             v
 [ OBIETTIVO: Reconstruct T_o^c (12 parametri incogniti) ]
```

#### Dati a Disposizione:
1. **Coordinate 3D note dell'oggetto** (*A priori*): Conosciamo la posizione 3D di $n$ punti $P_i$ rispetto alla terna oggetto $F_o$, indicata con il vettore $r_{o,i}^o \in \mathbb{R}^3$.
2. **Coordinate 2D misurate nell'immagine** (*Real-time*): Attraverso algoritmi di visione artificiale, misuriamo le coordinate normalizzate $s_i = [x_i, y_i]^T$ per ciascuno degli $n$ punti nell'immagine.

#### Conteggio delle Incognite:
La matrice $T_o^c$ contiene 12 elementi incogniti da calcolare (9 per la matrice di rotazione $R_o^c$ e 3 per la traslazione $o_{c,o}^c$). Di conseguenza, l'informazione fornita da un solo punto ($2$ equazioni scalari) non è sufficiente per determinare la posa dell'oggetto.

#### Modello Matematico di Proiezione
Per un generico punto $i$-esimo, la trasformazione rigida del punto $P_i$ dalla terna oggetto alla terna telecamera è espressa in coordinate omogenee come:

$$\tilde{r}_{o,i}^c = T_o^c \tilde{r}_{o,i}^o$$

Moltiplicando per il modello di proiezione prospettica si ottiene la relazione fondamentale che lega le feature misurate nell'immagine con le coordinate 3D dell'oggetto:

$$\lambda_i \tilde{s}_i = \begin{bmatrix} I_{3 \times 3} & 0_{3 \times 1} \end{bmatrix} T_o^c \tilde{r}_{o,i}^o$$

*(Nota: Passaggio integrato con chiarezza didattica per esplicitare la matrice di proiezione prospettica canonica $\Pi = [I_{3 \times 3} \mid 0_{3 \times 1}]$).*

Dove:
* $\tilde{s}_i = [x_i, y_i, 1]^T$ è il vettore omogeneo delle feature misurate per il punto $i$.
* $\lambda_i$ rappresenta il fattore di scala incognito, corrispondente alla profondità $z_i^c$ del punto rispetto al centro ottico della telecamera.
* $\tilde{r}_{o,i}^o = [x_{o,i}, y_{o,i}, z_{o,i}, 1]^T$ è la posizione omogenea del punto $i$ nota nella terna oggetto.

Impilando queste equazioni per tutti gli $n$ punti disponibili ($i = 1, \dots, n$), si ottiene un sistema di equazioni la cui risoluzione permette di stimare la matrice $T_o^c$.

---

### Il Problema Perspective-n-Point (PnP) e Caso Complanare

La risoluzione del sistema per il calcolo di $T_o^c$ è nota in letteratura come problema **Perspective-n-Point (PnP)**.

La possibilità di trovare una soluzione analitica o numerica e la sua unicità dipendono dal numero di punti $n$ e dalla loro configurazione geometrica nello spazio (ad esempio se i punti sono allineati o giacciono sullo stesso piano).

> **Concetto Chiave: Condizioni per la Soluzione Analitica Unica (P4P Complanare)**
> 
> Nel caso analitico condotto con **$n = 4$ punti**, la soluzione del problema PnP è **unica** sotto le seguenti ipotesi geometriche inderogabili:
> 1. I 4 punti appartengono tutti allo **stesso piano** (punti complanari).
> 2. **Nessuna terna** di punti tra i 4 considerati è allineata (non collinearità a tre a tre).
> 
> Se queste condizioni sono soddisfatte, è possibile determinare in modo esatto e analitico la matrice di trasformazione omogenea $T_o^c$.