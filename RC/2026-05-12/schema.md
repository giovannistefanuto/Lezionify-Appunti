# Matrice di Interazione e Jacobiano Immagine per un Punto Feature

## Overview Didattica

In questa lezione viene approfondito lo studio della **Matrice di Interazione** (*Interaction Matrix* $L_s$), nota anche come **Jacobiano Immagine** (*Image Jacobian*), fondamentale nel controllo visivo (*Visual Servoing*) per legare le variazioni temporali dei tratti caratteristici nell'immagine alla cinematica del sistema.

I punti chiave trattati includono:
* **Relazione Cinematica Fondamentale**: Formalizzazione del legame matematico tra la derivata temporale del vettore di feature (*Feature Vector Velocity* $\dot{s}$) e la velocità assoluta e relativa tra l'oggetto e la telecamera. Si evidenzia l'ipotesi operativa comune in cui l'oggetto è stazionario (velocità dell'oggetto pari a zero).
* **Proprietà della Matrice $\Gamma$**: Analisi della struttura a blocchi della matrice $\Gamma$ (contenente matrici anti-simmetriche / *skew-symmetric matrices*). Viene mostrato che la matrice possiede 6 autovalori tutti pari a $-1$, garantendone sempre l'invertibilità e facilitando il calcolo indiretto dello Jacobiano.
* **Derivazione per un Punto Feature (*Point Feature*)**: Caso di studio relativo a un singolo punto nello spazio 3D proiettato sul piano immagine.
* **Analisi Dimensionale e Trasformazioni di Coordinate**: Definizione del vettore delle coordinate dell'immagine normalizzate (*Normalized Image Coordinates*) di dimensione 2, operante con un vettore di velocità nello spazio 3D di dimensione 6, da cui deriva una matrice di interazione $L_s \in \mathbb{R}^{2 \times 6}$. Vengono infine riviste le trasformazioni di coordinate dal sistema di riferimento base (*Base Frame*) al sistema di riferimento della telecamera (*Camera Frame*).

*(Nota: All'inizio della lezione si è accennato brevemente alla sospensione delle prossime lezioni e al completamento del corso entro la fine di maggio).*

---

### Introduzione e Informazioni Organizzative

La conclusione delle lezioni del corso è prevista entro la fine del mese di Maggio. 

In questa lezione proseguiamo l'analisi della **Matrice Jacobiana di Interazione** (*Interaction Jacobian Matrix*) e delle sue proprietà algebriche e geometriche.

---

### Cinematica della Feature Visiva e Velocità Relativa

L'obiettivo principale è descrivere matematicamente come varia nel tempo la proiezione sul piano immagine (*Image Plane*) di alcuni punti di interesse legati a un oggetto.

* **Vettore delle Feature (*Feature Vector*)** $\mathbf{s}$: racchiude le coordinate sul piano immagine dei punti caratteristici estratti dall'oggetto.
* **Velocità del Vettore delle Feature** $\dot{\mathbf{s}}$: rappresenta la variazione temporale della posizione delle feature sul piano immagine.

Quando la telecamera si muove rispetto all'oggetto (o viceversa), si genera una velocità relativa tra l'oggetto e il sistema di riferimento della telecamera (*Camera Frame*). Poiché esiste una relazione cinetica tra la variazione delle feature e le velocità in gioco, lo strumento matematico fondamentale per descriverla è un **Jacobiano**.

#### Dalle Velocità Assolute alla Matrice di Interazione

In un contesto generale, definiamo le velocità assolute espressi nel sistema di riferimento della telecamera:
* $\mathbf{v}_c$: velocità assoluta della telecamera;
* $\mathbf{v}_o$: velocità assoluta dell'oggetto.

Attraverso opportuni passaggi algebrici, è possibile legare la variazione temporale delle feature $\dot{\mathbf{s}}$ alle velocità assolute tramite una nuova struttura matriciale:

$$\dot{\mathbf{s}} = N_s \cdot \begin{bmatrix} \mathbf{v}_o \\ \mathbf{v}_c \end{bmatrix}$$

Dove la matrice $N_s$ è direttamente collegata al Jacobiano dell'immagine (*Image Jacobian*) $L_s$ tramite una matrice di blocco di trasformazione cinematica $\Gamma$:

$$N_s = L_s \cdot \Gamma$$

#### Struttura della Matrice $\Gamma$

La matrice $\Gamma$ è una matrice a blocchi che integra una **Matrice Emisimmetrica** (*Skew-Symmetric Matrix*) costruita a partire dal vettore $\mathbf{o}_c$:

$$\mathbf{o}_c$$

Il vettore $\mathbf{o}_c$ rappresenta il vettore che congiunge l'origine del sistema di riferimento della telecamera con l'origine del sistema di riferimento dell'oggetto, espresso nelle coordinate della telecamera.

> **Concetto Chiave: Invertibilità di $\Gamma$**
> 
> La matrice $\Gamma$ possiede tutti e $6$ gli autovalori pari a $-1$. Poiché nessun autovalore è nullo ($\lambda_i \neq 0$), la matrice $\Gamma$ è **sempre strettamente invertibile** ($\det(\Gamma) \neq 0$).
>
> Questo risultato è di fondamentale importanza pratica: in molti problemi è estremamente più semplice calcolare direttamente la matrice $N_s$ anziché il Jacobiano dell'immagine $L_s$. Grazie alla piena invertibilità di $\Gamma$, è sempre possibile ricavare $L_s$ mediante inversione:
> 
> $$L_s = N_s \cdot \Gamma^{-1}$$

---

### Caso Particolare: Oggetto Stazionario

Nella maggior parte delle applicazioni pratiche di servoing visivo (es. configurazione *eye-in-hand* con target fisso nell'ambiente), l'oggetto osservato è immobile rispetto al mondo.

Assumendo quindi che la velocità assoluta dell'oggetto sia nulla:

$$\mathbf{v}_o = \mathbf{0}$$

La relazione generale si semplifica notevolmente, riducendosi a un legame diretto ed esclusivo tra la velocità della feature sul piano immagine e la sola velocità assoluta della telecamera $\mathbf{v}_c$:

$$\dot{\mathbf{s}} = L_s \cdot \mathbf{v}_c$$

In questo contesto, la Matrice di Interazione funge a tutti gli effetti da Jacobiano di sistema, mappando direttamente lo spazio delle velocità cartesiane della telecamera nello spazio delle velocità delle feature visive.

---

3. Calcolo Analitico della Matrice di Interazione per Punti e Segmenti

Vediamo ora come calcolare in modo pratico e analitico la **Matrice di Interazione** (*Interaction Matrix*) nel caso fondamentale in cui il vettore delle caratteristiche visive (*feature vector*) sia rappresentato dalle coordinate di un singolo punto nello spazio. Comprendere questo sviluppo è essenziale per poi estendere il risultato a geometrie più complesse.

---

### 3.1 Cinematica del Punto e Vettore delle Caratteristiche

Consideriamo un punto fisso $P$ appartenente all'oggetto osservato. La sua posizione è descritta dal vettore $\boldsymbol{p}$ rispetto al sistema di riferimento base (*base frame*). 
La telecamera si muove nello spazio: il suo sistema di riferimento ha origine nel punto $O_c$.

```
    [Base Frame] 
         |
         +-------------------->  P (Punto fisso sull'oggetto, p)
         \                    /
          \                  /  r_C/C (Coordinate nel camera frame)
           v                v
      [Camera Frame (O_c)]
```

Definiamo il vettore $\boldsymbol{r}_{C/C} = [x_c, y_c, z_c]^T$ come il vettore posizione che va dall'origine della telecamera $O_c$ al punto $P$, espresso nelle **coordinate del sistema telecamera** (*Camera Frame*).

> **Concetto Chiave: Dimensioni dei Vettori e della Matrice**
> * Vettore delle caratteristiche $\boldsymbol{s}$: formato dalle coordinate immagine normalizzate del punto, quindi $\dim(\boldsymbol{s}) = 2$.
> * Vettore della velocità spaziale della telecamera $\boldsymbol{\nu}_c$: composto da 3 velocità traslazionali e 3 velocità angolari, quindi $\dim(\boldsymbol{\nu}_c) = 6$.
> * Matrice di Interazione $L_s$: lega $\dot{\boldsymbol{s}}$ a $\boldsymbol{\nu}_c$ ($\dot{\boldsymbol{s}} = L_s \boldsymbol{\nu}_c$), pertanto la sua dimensione deve essere rigorosamente **$2 \times 6$**.

#### Coordinate del Punto nel Camera Frame
Nel sistema di riferimento base, la posizione relativa è data da:
$$\boldsymbol{r}_C = \boldsymbol{p} - \boldsymbol{o}_c$$

Per esprimere questo vettore rispetto al sistema di riferimento della telecamera, applichiamo la matrice di rotazione $\boldsymbol{R}_c$ (che descrive l'orientamento del camera frame rispetto alla base):
$$\boldsymbol{r}_{C/C} = \boldsymbol{R}_c^T (\boldsymbol{p} - \boldsymbol{o}_c)$$

dove usiamo la trasposta $\boldsymbol{R}_c^T$ poiché $\boldsymbol{R}_c^{-1} = \boldsymbol{R}_c^T$ per le matrici di rotazione ortogonali.

#### Trasformazione Prospettica e Coordinate Normalizzate
Sfruttando il modello della **trasformazione prospettica** (*Perspective Transformation*), poniamo la lunghezza focale $f = 1$ per ottenere le coordinate immagine normalizzate che costituiscono il nostro vettore feature $\boldsymbol{s}$:

$$\boldsymbol{s} = \begin{bmatrix} X \\ Y \end{bmatrix} = \begin{bmatrix} \frac{x_c}{z_c} \\ \frac{y_c}{z_c} \end{bmatrix}$$

---

### 3.2 Derivazione della Matrice di Interazione $L_s$

Per trovare la relazione $\dot{\boldsymbol{s}} = L_s \boldsymbol{\nu}_c$, calcoliamo la derivata temporale di $\boldsymbol{s}$ applicando la **regola della catena** (*Chain Rule*):

$$\dot{\boldsymbol{s}} = \frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} \cdot \dot{\boldsymbol{r}}_{C/C}$$

Questa formulazione scompone il problema in due blocchi:
1. Lo **Jacobiano spaziale** $\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}}$ (dimensione $2 \times 3$).
2. La **velocità temporale del punto nel camera frame** $\dot{\boldsymbol{r}}_{C/C}$ (dimensione $3 \times 1$).

---

#### Primo Blocco: Calcolo dello Jacobiano Parziale $\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}}$

Calcoliamo le derivate parziali delle coordinate $X$ e $Y$ rispetto alle componenti $x_c, y_c, z_c$:

$$\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} = \begin{bmatrix} \frac{\partial X}{\partial x_c} & \frac{\partial X}{\partial y_c} & \frac{\partial X}{\partial z_c} \\[6pt] \frac{\partial Y}{\partial x_c} & \frac{\partial Y}{\partial y_c} & \frac{\partial Y}{\partial z_c} \end{bmatrix}$$

Svolgendo i calcoli analitici:
* Per $X = \frac{x_c}{z_c}$:
  $$\frac{\partial X}{\partial x_c} = \frac{1}{z_c}, \quad \frac{\partial X}{\partial y_c} = 0, \quad \frac{\partial X}{\partial z_c} = -\frac{x_c}{z_c^2} = -\frac{X}{z_c}$$

* Per $Y = \frac{y_c}{z_c}$:
  $$\frac{\partial Y}{\partial x_c} = 0, \quad \frac{\partial Y}{\partial y_c} = \frac{1}{z_c}, \quad \frac{\partial Y}{\partial z_c} = -\frac{y_c}{z_c^2} = -\frac{Y}{z_c}$$

Sostituendo le relazioni $X = \frac{x_c}{z_c}$ e $Y = \frac{y_c}{z_c}$, la matrice assume una forma elegante:

$$\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} = \begin{bmatrix} \frac{1}{z_c} & 0 & -\frac{X}{z_c} \\ 0 & \frac{1}{z_c} & -\frac{Y}{z_c} \end{bmatrix} = \frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix}$$

---

#### Secondo Blocco: Calcolo di $\dot{\boldsymbol{r}}_{C/C}$

Deriviamo ora $\boldsymbol{r}_{C/C} = \boldsymbol{R}_c^T (\boldsymbol{p} - \boldsymbol{o}_c)$ rispetto al tempo. Ricordando che il punto $P$ è fisso nel mondo ($\dot{\boldsymbol{p}} = \mathbf{0}$):

$$\dot{\boldsymbol{r}}_{C/C} = \boldsymbol{R}_c^T (-\dot{\boldsymbol{o}}_c) + \dot{\boldsymbol{R}}_c^T (\boldsymbol{p} - \boldsymbol{o}_c)$$

Notiamo che:
* $\boldsymbol{R}_c^T \dot{\boldsymbol{o}}_c = \boldsymbol{v}_c$ rappresenta la velocità traslazionale della telecamera espressa nel sistema di riferimento telecamera.
* La derivata della matrice di rotazione introduce il vettore della velocità angolare $\boldsymbol{\omega}_c$ tramite l'operatore di antisimmetria (*skew-symmetric operator*) $[\boldsymbol{r}_{C/C}]_\times$:

$$\dot{\boldsymbol{r}}_{C/C} = -\boldsymbol{v}_c - [\boldsymbol{\omega}_c]_\times \boldsymbol{r}_{C/C} = -\boldsymbol{v}_c + [\boldsymbol{r}_{C/C}]_\times \boldsymbol{\omega}_c$$

In forma matriciale patta rispetto al vettore delle velocità della telecamera $\boldsymbol{\nu}_c = \begin{bmatrix} \boldsymbol{v}_c \\ \boldsymbol{\omega}_c \end{bmatrix}$:

$$\dot{\boldsymbol{r}}_{C/C} = \begin{bmatrix} -\boldsymbol{I}_3 & [\boldsymbol{r}_{C/C}]_\times \end{bmatrix} \begin{bmatrix} \boldsymbol{v}_c \\ \boldsymbol{\omega}_c \end{bmatrix}$$

dove $\boldsymbol{I}_3$ è la matrice identità $3 \times 3$ e $[\boldsymbol{r}_{C/C}]_\times$ è la matrice antisimmetrica associata a $\boldsymbol{r}_{C/C} = [x_c, y_c, z_c]^T$:

$$[\boldsymbol{r}_{C/C}]_\times = \begin{bmatrix} 0 & -z_c & y_c \\ z_c & 0 & -x_c \\ -y_c & x_c & 0 \end{bmatrix}$$

---

#### Assemblaggio e Risultato Finale

Moltiplicando i due blocchi ottenuti:

$$\dot{\boldsymbol{s}} = \frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix} \begin{bmatrix} -\boldsymbol{I}_3 & [\boldsymbol{r}_{C/C}]_\times \end{bmatrix} \boldsymbol{\nu}_c$$

*Nota: Passaggio integrato con chiarezza didattica.* Svolgiamo il prodotto matriciale esplicito $L_s = \frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} \begin{bmatrix} -\boldsymbol{I}_3 & [\boldsymbol{r}_{C/C}]_\times \end{bmatrix}$:

1. **Parte Traslazionale (prime 3 colonne):**
   $$\frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix} \begin{bmatrix} -1 & 0 & 0 \\ 0 & -1 & 0 \\ 0 & 0 & -1 \end{bmatrix} = \begin{bmatrix} -\frac{1}{z_c} & 0 & \frac{X}{z_c} \\ 0 & -\frac{1}{z_c} & \frac{Y}{z_c} \end{bmatrix}$$

2. **Parte Rotazionale (ultime 3 colonne):**
   $$\frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix} \begin{bmatrix} 0 & -z_c & y_c \\ z_c & 0 & -x_c \\ -y_c & x_c & 0 \end{bmatrix} = \begin{bmatrix} \frac{x_c Y}{z_c} & -1 - \frac{x_c X}{z_c} & \frac{y_c}{z_c} \\ 1 + \frac{y_c Y}{z_c} & -\frac{x_c Y}{z_c} & -\frac{x_c}{z_c} \end{bmatrix} = \begin{bmatrix} XY & -(1+X^2) & Y \\ 1+Y^2 & -XY & -X \end{bmatrix}$$

Unendo i risultati otteniamo la **Matrice di Interazione analitica per un singolo punto**:

$$L_s = \begin{bmatrix} -\frac{1}{z_c} & 0 & \frac{X}{z_c} & XY & -(1+X^2) & Y \\[6pt] 0 & -\frac{1}{z_c} & \frac{Y}{z_c} & 1+Y^2 & -XY & -X \end{bmatrix}$$

> **Concetto Chiave: Il Ruolo della Profondità $z_c$**
> Osserva bene la matrice $L_s$: i termini misurabili sul piano immagine sono le coordinate $X$ e $Y$. Tuttavia, per calcolare la matrice di interazione occorre conoscere anche $z_c$, ovvero la **profondità 3D** del punto rispetto alla telecamera. 
> Questa informazione non si legge direttamente dal singolo pixel, ma può essere stimata tramite algoritmi analitici di stima della posa (es. algoritmi PnP basati su 4 punti complanari non allineati).

---

### 3.3 Estensioni: Sistemi di Punti e Segmenti Orientati

#### Caso 1: Insieme di $N$ Punti
Se l'oggetto è descritto da un set di $N$ punti fisici, il vettore delle caratteristiche $\boldsymbol{s}$ è l'impilamento (*stacking*) delle coordinate di ciascun punto:

$$\boldsymbol{s} = \begin{bmatrix} \boldsymbol{s}_1 \\ \boldsymbol{s}_2 \\ \vdots \\ \boldsymbol{s}_N \end{bmatrix} \in \mathbb{R}^{2N}$$

La matrice di interazione complessiva si ottiene semplicemente impilando le matrici di interazione dei singoli punti:

$$L_s = \begin{bmatrix} L_{s1} \\ L_{s2} \\ \vdots \\ L_{sN} \end{bmatrix} \in \mathbb{R}^{2N \times 6}$$

---

#### Caso 2: Segmento Orientato (*Aligned Segment*)
Un approccio alternativo consiste nel caratterizzare una geometria più ricca, come un segmento di retta nello spazio.

```
       P1 (x1, y1)
        \
         \     P_bar (X_bar, Y_bar) -> Centroide
          \
           P2 (x2, y2)
```

Possiamo rappresentare il segmento definendo un vettore delle caratteristiche a 4 elementi:

$$\boldsymbol{s}_{seg} = \begin{bmatrix} \bar{X} \\ \bar{Y} \\ L \\ \alpha \end{bmatrix}$$

dove:
* $\bar{X} = \frac{X_1 + X_2}{2}$ e $\bar{Y} = \frac{Y_1 + Y_2}{2}$ sono le coordinate del punto medio (*midpoint*).
* $L = \sqrt{\Delta X^2 + \Delta Y^2}$ è la lunghezza totale del segmento sul piano immagine (con $\Delta X = X_1 - X_2$ e $\Delta Y = Y_1 - Y_2$).
* $\alpha = \arctan\left(\frac{\Delta Y}{\Delta X}\right)$ è l'inclinazione (*slope*) del segmento.

Per calcolare la matrice di interazione del segmento $L_{s\_seg}$, si applica nuovamente la regola della catena considerando che $\boldsymbol{s}_{seg}$ dipende dalle posizioni dei due estremi $\boldsymbol{s}_1 = [X_1, Y_1]^T$ e $\boldsymbol{s}_2 = [X_2, Y_2]^T$:

$$\dot{\boldsymbol{s}}_{seg} = \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_1} \dot{\boldsymbol{s}}_1 + \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_2} \dot{\boldsymbol{s}}_2 = \left( \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_1} L_{s1} + \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_2} L_{s2} \right) \boldsymbol{\nu}_c$$

Pertanto:
$$L_{s\_seg} = \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_1} L_{s1} + \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_2} L_{s2}$$

Questo mostra l'estrema flessibilità del calcolo analitico: conoscendo la matrice di interazione del punto base, è possibile ricavare lo Jacobiano per qualsiasi primitiva geometrica più complessa.

---

### Introduzione al Controllo Visuale Basato su Immagine (IBVS)

Nel **Controllo Visuale Basato su Immagine (Image-Based Visual Servoing - IBVS)**, l'idea fondamentale è utilizzare direttamente le informazioni estratte dal piano immagine della telecamera per generare i comandi di controllo per il robot, senza passare attraverso una ricostruzione tridimensionale completa della scena nello spazio cartesiano.

#### Vantaggi e Svantaggi dell'Approccio IBVS

**Vantaggi:**
* **Mantenimento dell'oggetto nel Campo Visivo (Field of View - FOV):** 
  Se progettiamo un controllore nello **Spazio Operativo (Operational Space)** tradizionale (guardando solo la posizione 3D dell'organo terminale), la traiettoria pianificata potrebbe far uscire l'oggetto dal campo visivo della telecamera.
  > **Concetto Chiave:** Progettando il controllore direttamente sul piano immagine, possiamo imporre vincoli espliciti sulle coordinate delle *feature* (caratteristiche visive) per garantire che l'oggetto rimanga sempre all'interno del FOV durante tutto il movimento.

**Svantaggi:**
* **Mancanza di controllo sulle Singolarità Cinematiche (Kinematic Singularities):**
  Lavorando solo nello spazio immagine, non si ha una percezione diretta della configurazione articolare del robot. Di conseguenza, il percorso generato nello spazio immagine potrebbe spingere il robot verso configurazioni singolari o fuori dai limiti giunto.

> **Nota di contesto pratico:** Nella robotica reale si usano spesso **schemi di controllo ibridi**, che combinano le misure dei giunti/spazio operativo con i dati provenienti dalla telecamera per unire i vantaggi di entrambi i mondi. Ciononostante, è fondamentale comprendere prima la teoria pura dell'IBVS.

---

### Definizione del Problema di Controllo e Impostazione dello Scenario

Consideriamo un problema di **controllo punto-a-punto (Point-to-Point Control)**: vogliamo guidare l'organo terminale (e di conseguenza la telecamera ad esso solidale) da una configurazione iniziale a una configurazione finale desiderata.

1. **Configurazione Desiderata:** La presenza di una posizione finale desiderata per l'organo terminale implica una posizione finale desiderata per la telecamera.
2. **Vettore di Feature Desiderato ($s_d$):** Quando la telecamera si trova nella posa finale desiderata, i punti dell'oggetto proiettati sul piano immagine formano un vettore di caratteristiche visive desiderato $s_d$.
3. **Pianificazione:** Poiché si tratta di un problema punto-a-punto, $s_d$ è un vettore **costante** nel tempo (rappresenta un riferimento stazionario che vogliamo raggiungere).

```
   [ Posa Iniziale Telecamera ]  --->  Vettore Feature Corrente: s(t)
                                              |
                                              v (Tracciamento Errore)
                                              |
   [ Posa Finale Desiderata ]    --->  Vettore Feature Desiderato: s_d (Costante)
```

#### Come si ottiene $s_d$?
In una fase offline (*fase di apprendimento o insegnamento*), si porta manualmente il robot nella configurazione finale desiderata e si misurano le coordinate dei punti immagine, memorizzando il vettore $s_d$.

---

### Stima della Profondità e Matrice di Interazione

Per poter calcolare le leggi di controllo nell'IBVS, abbiamo bisogno di conoscere la **Matrice di Interazione (Interaction Matrix)** $L_s$. 

Come visto precedentemente, la matrice di interazione per un singolo punto dipende dalle sue coordinate sul piano immagine e dalla sua profondità $Z_c$ lungo l'asse ottico della telecamera.

#### Caso Studio: 4 Punti Coplanari
Consideriamo lo scenario standard in cui tracciamo $N = 4$ punti sulla superficie dell'oggetto. Assumiamo che:
* I 4 punti siano coplanari.
* Nessun terzetto di punti sia allineato (non collinearità).

In queste condizioni, risolvendo il problema di **Prospettiva a $N$-Punti (Perspective-n-Point - PnP)**, è possibile determinare in modo univoco la posa relativa tra il riferimento dell'oggetto (*Object Frame*) e il riferimento della telecamera (*Camera Frame*), rappresentata dalla matrice di trasformazione $T_c^o$.

Sfruttando la conoscenza di $T_c^o$, possiamo ricavare analiticamente la coordinata di profondità $Z_{ci}$ per ciascuno dei 4 punti ($i = 1, 2, 3, 4$):

$$Z_c = \begin{bmatrix} Z_{c1} \\ Z_{c2} \\ Z_{c3} \\ Z_{c4} \end{bmatrix}$$

#### Composizione della Matrice di Interazione Composta
Poiché per ogni punto $i$ la matrice di interazione $L_{si}$ ha dimensione $2 \times 6$ (associando le velocità della telecamera nello spazio 3D alla variazione delle coordinate 2D dell'immagine):

$$\dot{s}_i = L_{si}(s_i, Z_{ci}) \, v_c$$

Impilando le matrici dei 4 punti, otteniamo la matrice di interazione complessiva $L_s$ di dimensione $8 \times 6$:

$$L_s = \begin{bmatrix} L_{s1} \\ L_{s2} \\ L_{s3} \\ L_{s4} \end{bmatrix} \in \mathbb{R}^{8 \times 6}$$

*Nota: Passaggio integrato con chiarezza didattica per mostrare la struttura vettoriale complessiva.*

---

### Definizione dell'Errore nello Spazio Immagine

L'obiettivo del controllo IBVS è annullare la differenza tra la configurazione visiva attuale e quella desiderata.

Definiamo il **vettore di errore nello spazio immagine** $e(t)$ come:

$$e(t) = s_d - s(t)$$

Dove:
* $s_d \in \mathbb{R}^{2N}$ è il vettore delle caratteristiche desiderate (costante).
* $s(t) \in \mathbb{R}^{2N}$ è il vettore delle caratteristiche correnti misurate a ogni istante di tempo $t$.

Per un sistema con $N = 4$ punti, sia $e(t)$, che $s_d$ e $s(t)$ sono vettori di dimensione $8 \times 1$. 

> **Concetto Chiave:** L'obiettivo primario dell'algoritmo di controllo sarà quello di progettare una legge di velocità per la telecamera $v_c$ tale da garantire che l'errore converga asintoticamente a zero:
> $$\lim_{t \to \infty} e(t) = 0 \implies \lim_{t \to \infty} s(t) = s_d$$

---

### Controllo PD con Compensazione di Gravità nello Spazio Immagine

#### Formulazione della Funzione Candidata di Lyapunov

Vogliamo progettare un controllore Proporzionale-Derivativo (PD) con compensazione di gravità direttamente nello **Spazio Immagine** (*Image Space*). 

Ricordiamo l'approccio diagonale usato nello spazio dei giunti: definivamo una funzione di Lyapunov pari alla somma dell'energia cinetica e di un termine quadratico dipendente dall'errore ai giunti. Non avendo a disposizione l'errore ai giunti, imitiamo tale struttura definendo una funzione candidata di Lyapunov $V(q, \dot{q})$ composta dall'energia cinetica del manipolatore e da un termine quadratico basato sull'errore nelle caratteristiche visive (*feature error*) $e_s$:

$$V(q, \dot{q}) = \frac{1}{2} \dot{q}^T B(q) \dot{q} + \frac{1}{2} e_s^T K_p^s e_s$$

dove:
* $B(q)$ è la matrice di inerzia del robot (definita positiva).
* $e_s = s_d - s$ rappresenta l'errore nel piano immagine tra il vettore di caratteristiche desiderato $s_d$ (costante, per cui $\dot{s}_d = 0$) e quello misurato $s$.
* $K_p^s$ è una matrice di guadagno proporzionale definita positiva ($K_p^s > 0$).

Proprietà di $V(q, \dot{q})$:
* $V(q, \dot{q}) > 0$ per ogni stato non nullo.
* $V(q, \dot{q}) = 0$ se e solo se sia l'errore nello spazio immagine che la velocità ai giunti sono nulli ($e_s = 0$ e $\dot{q} = 0$).

Il punto di minimo di questa funzione corrisponde esattamene alla configurazione target: giungiamo a destinazione con velocità nulla ($\dot{q} = 0$) e vi rimaniamo.

---

#### Derivata Temporale e Relazioni Cinematiche

Calcoliamo la derivata temporale di $V(q, \dot{q})$:

$$\dot{V}(q, \dot{q}) = \dot{q}^T B(q) \ddot{q} + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} + e_s^T K_p^s \dot{e}_s$$

Dalla dinamica del robot nello spazio dei giunti sappiamo che:

$$B(q)\ddot{q} = u - C(q, \dot{q})\dot{q} - F\dot{q} - g(q)$$

Sostituendo $B(q)\ddot{q}$ nell'espressione della derivata ed sfruttando la proprietà di emisimmetria per cui la matrice $(\dot{B}(q) - 2C(q, \dot{q}))$ è emisimmetrica (ovvero $\dot{q}^T (\dot{B}(q) - 2C(q, \dot{q})) \dot{q} = 0$), otteniamo:

$$\dot{V}(q, \dot{q}) = \dot{q}^T \left( u - g(q) - F\dot{q} \right) + e_s^T K_p^s \dot{e}_s$$

> *Nota: Passaggio integrato con chiarezza didattica per esplicitare il legame tra $\dot{e}_s$ e $\dot{q}$.*

Per proseguire dobbiamo esprimere la derivata dell'errore $\dot{e}_s = -\dot{s}$ in funzione della velocità ai giunti $\dot{q}$. 

1. Sapendo che l'oggetto è fermo, la variazione delle feature visive è legata alla velocità assoluta della telecamera $v_c$ tramite la **Matrice di Interazione** (*Interaction Matrix*) $L_s$:
   $$\dot{s} = L_s v_c$$

2. Poiché la telecamera è solidale con il *end-effector*, la sua velocità $v_c$ (composta da velocità lineare e angolare) è direttamente legata a $\dot{q}$ mediante lo Jacobiano geometrico del robot $J_q(q)$, tenendo conto della rotazione $R_c$ tra il sistema di riferimento della telecamera e quello di base:
   $$v_c = \begin{bmatrix} R_c^T & 0 \\ 0 & R_c^T \end{bmatrix} J_q(q) \dot{q}$$

3. Combinando le relazioni, definiamo lo **Jacobiano Visivo** o della Feature $J_m(q, s)$:
   $$J_m(q, s) = L_s(s, Z_c) \begin{bmatrix} R_c^T & 0 \\ 0 & R_c^T \end{bmatrix} J_q(q)$$

Otteniamo quindi la relazione cinematica fondamentale:

$$\dot{e}_s = -\dot{s} = -J_m(q, s) \dot{q}$$

Sostituendo $\dot{e}_s$ nell'espressione della derivata di Lyapunov:

$$\dot{V}(q, \dot{q}) = \dot{q}^T \left( u - g(q) - F\dot{q} - J_m^T(q, s) K_p^s e_s \right)$$

---

#### Sintesi della Legge di Controllo

Per fare in modo che $\dot{V}(q, \dot{q}) \le 0$, possiamo scegliere l'ingresso di controllo $u$ uguagliando i termini fra parentesi e aggiungendo un'azione derivativa per smorzare e velocizzare il transitorio:

$$u = g(q) + J_m^T(q, s) K_p^s e_s - K_d \dot{q}$$

con $K_d > 0$. Sostituendo tale legge di controllo $u$ nell'equazione di $\dot{V}$, otteniamo:

$$\dot{V}(q, \dot{q}) = -\dot{q}^T F \dot{q} - \dot{q}^T K_d \dot{q} \le 0$$

Poiché $F > 0$ e $K_d > 0$, la derivata della funzione di Lyapunov è strettamente negativa per ogni $\dot{q} \neq 0$. Questo garantisce la dissipazione dell'energia e la convergenza del sistema verso una configurazione di equilibrio in cui $\dot{q} = 0$.

---

### Analisi di Stabilità e Punti di Equilibrio

> **CONCETTO CHIAVE: Presenza di Singolarità e Condizione di Rango Full**
> 
> L'analisi della derivata della funzione di Lyapunov garantisce che il sistema converga a uno stato di equilibrio in cui $\dot{q} = 0$. Tuttavia, questo **non garantisce automaticamente** che si sia raggiunto l'errore visivo nullo ($e_s = 0$).

Per analizzare la configurazione finale di equilibrio, consideriamo il modello dinamico all'equilibrio (dove $\dot{q} = 0$ e $\ddot{q} = 0$) in presenza della legge di controllo applicata:

$$0 = u - g(q) \implies 0 = J_m^T(q, s) K_p^s e_s$$

Da questa relazione possiamo identificare due scenari:

1. **Caso Ideale ($J_m$ a Rango Massimo)**: Se la matrice $J_m^T(q, s)$ è a rango pieno (*full rank*), il suo spazio nullo (*kernel*) contiene unicamente il vettore nullo. Di conseguenza, l'unica soluzione all'equazione sopra è:
   $$e_s = 0$$
   In questo caso la convergenza alla configurazione visiva desiderata è garantita.

2. **Caso Critico (Presenza di Singolarità)**: Se il prodotto $K_p^s e_s$ si trova all'interno dello spazio nullo di $J_m^T$ ($\text{null}(J_m^T) \neq \{0\}$), il sistema può bloccarsi in un punto di equilibrio stazionario ($\dot{q} = 0$) presenta un errore visivo **diverso da zero** ($e_s \neq 0$).

---

### Considerazioni Pratiche, Trade-off e Implementazione

#### Conoscenza del Modello e Requisiti di Misura

Per implementare praticamente la legge di controllo proposta:

$$u = g(q) + J_m^T(q, s) K_p^s e_s - K_d \dot{q}$$

è necessario disporre delle seguenti informazioni:

* **Termine Gravitazionale $g(q)$**: L'unica parte della dinamica del robot che deve essere nota a priori.
* **Jacobiano del Robot $J_q(q)$**: Ottenibile mediante la cinematica diretta del manipolatore.
* **Matrice di Interazione $L_s$**: Dipende dalle coordinate normalizzate sul piano immagine $s$ e dalla profondità $Z_c$ dei punti immagine.
* **Ricostruzione 3D (Profondità $Z_c$)**: Poiché $L_s$ dipende da $Z_c$, occorre stimare la profondità in tempo reale tramite algoritmi di ricostruzione 3D (analitici o numerici).

```
   [Sensore Telecamera] ---> Misura e_s e s
                               |
                               v
[Ricostruzione 3D / Stima Z_c] + [Cinematica Robot J_q(q)]
                               |
                               v
                     [Calcolo J_m(q,s)]
                               |
                               v
  [Controllo u = g(q) + J_m^T K_p^s e_s - K_d q_dot] ---> [Robot]
```

#### Compromessi (Trade-off) di Controllo

* **Spazio Giunti vs. Spazio Immagine**:
  * *Controllo nello Spazio Giunti*: Garantisce traiettorie fluide e convergenza globale, ma richiede la conoscenza completa dello stato e non garantisce che i punti di interesse rimangano all'interno del campo visivo (*Field of View - FOV*).
  * *Controllo nello Spazio Immagine (IBVS)*: Garantisce che le caratteristiche visive rimangano ben inquadrate nel sensore, ma paga lo scotto di possibili minimi locali (legati al rango di $J_m$) e di traiettorie nello spazio operativo meno regolari.

* **Soluzioni Ibride**: In ambito industriale si utilizzano spesso architetture miste che fondono le misure di errore nello spazio dei giunti con quelle nello spazio immagine, integrando i vantaggi di entrambe le soluzioni.

* **Casi Particolari (es. Manipolatori SCARA)**: Quando il robot lavora spostandosi lungo piani paralleli (come i robot SCARA), la profondità $Z_c$ si mantiene costantemente invariata. In questi scenari la matrice di interazione è semplificata, eliminando la necessità di ricostruire la profondità 3D online.

#### Varianti Implementative dell'Azione Derivativa

Qualora sia possibile misurare direttamente la velocità delle caratteristiche visive nel piano immagine $\dot{s}$, è diffusa un'ulteriore formulazione pratica dell'azione derivativa che sostituisce il termine $-K_d \dot{q}$ con una componente proporzionale a $\dot{s}$:

$$u = g(q) + J_m^T(q, s) K_p^s e_s - K_d^s J_m^T(q, s) \dot{s}$$

Questa variante tecnica permette di agire sullo smorzamento direttamente a livello di dinamica dell'immagine.