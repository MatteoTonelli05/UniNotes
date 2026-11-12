# Reti Neurali

## Neuroni biologici

I neuroni sono le più importanti **cellule del sistema nervoso**. Parti principali:
![[Pasted image 20261112151625.png]]
- **SOMA:** corpo cellulare del neurone.
- **DENDRITI:** fibre minori ramificate dal soma; raccolgono input da neuroni afferenti (tramite le sinapsi) e li propagano al soma.
- **ASSONE:** fibra principale che parte dal soma e porta l'output ad altri neuroni (anche distanti).
- **SINAPSI:** "porte di collegamento" per il passaggio dell'informazione tra neuroni.


### Come passa l'informazione (processo elettro-chimico)

![[Pasted image 20261112152717.png]]
1. il **neurone presinaptico** libera **neurotrasmettitori** nel breve _gap sinaptico_;
2. vengono captati da recettori (**canali ionici**) sulla membrana del **neurone postsinaptico**;
3. l'ingresso di **ioni** crea una **differenza di potenziale** tra corpo del neurone e esterno;
4. se il potenziale supera una **soglia** si produce uno **spike** (_potenziale d'azione_) che viaggia lungo l'assone e fa rilasciare neurotrasmettitori dalle sinapsi dell'assone.

> [!abstract] **Reweighting e regola di Hebb** 
> Il **reweighting** delle sinapsi (modifica della loro efficacia di trasmissione) è collegato ad **apprendimento e memoria**. 
> 
> **Hebbian rule**: se due neuroni connessi da una o più sinapsi sono **ripetutamente attivati insieme**, le sinapsi che li collegano vengono **rinforzate**.

![[Screenshot 2026-11-12 152819.png]]$\qquad \qquad \qquad \qquad \qquad$_rete neurale umana_

---

## Neurone artificiale

Primo modello: **McCulloch e Pitts (1943)**, con input e output **binari**, capace di eseguire computazioni logiche.

Neurone moderno di indice $i$ (una rete ne contiene molti, quindi serve distinguerli):
![[Pasted image 20261112153705.png]]

| Simbolo              | Significato                                                                                                                      |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| $in_1\dots in_d$     | i $d$ **ingressi** che il neurone riceve dagli assoni di neuroni afferenti                                                       |
| $w_{1i}\dots w_{di}$ | **pesi** (weight): efficacia delle connessioni sinaptiche dei dendriti. **Sono i valori su cui agiremo durante l'apprendimento** |
| $w_{0i}$ (**bias**)  | peso collegato a un input fittizio sempre uguale a 1: serve a "tarare" il punto di lavoro ottimale                               |
| $net_i$              | **livello di eccitazione** globale del neurone (potenziale interno)                                                              |
| $f(\cdot)$           | **funzione di attivazione**: determina l'output $out_i$ in funzione di $net_i$                                                   |

### Funzione di attivazione

> [!abstract] La funzione di attivazione è una funzione **tutto-niente temporizzata**
Quando $net_i$ supera una soglia il neurone "spara" uno spike e poi torna a riposo. 

Le reti più usate operano con **livelli continui**, e $f$ è:

- **non lineare** → servono non-linearità per ottenere mapping complessi tra input e output;
- **continua e differenziabile** (quasi ovunque) → necessario per la **retropropagazione dell'errore**.

#### Sigmoide (standard logistic function)

![[Pasted image 20261112155030.png]]
#### Tangente iperbolica (tanh)

Valori in $[-1,1]$. Si ottiene dalla sigmoide con scala ($\times2$) e traslazione ($-1$):
![[Pasted image 20261112155050.png]]

> [!question] Che differenza c'è tra **Sigmoid** e ** tanh**? 
> Tanh è leggermente più complessa ma **preferibile** (convergenza più rapida) perché è **simmetrica rispetto allo zero**.

> [!question] Perché le derivate "comode" contano?
>Le derivate di sigmoid e tanh si esprimono con l'**output stesso** $$\sigma'=\sigma(1-\sigma)\ ,\ \tau'=1-\tau^2$$ durante la backpropagation basta riusare il valore già calcolato in forward.

---

## Tipologie di reti neurali

Le reti sono gruppi di neuroni organizzati in **livelli**: un livello di **input**, uno di **output** e uno o più livelli **intermedi (hidden)**. Ogni livello contiene uno o più neuroni.

![[Pasted image 20261112155535.png]]
Le reti si distinguono principalmente in:
1. **Feedforward** , con **connessioni** solo verso il livello successivo. 
2. **Recurrent**, in cui sono previste connessioni di feedback (in genere verso lo stesso livello, ma anche all'indietro).
	- **Note:** flusso delle informazioni e addestramento più complicati (_unfolding in time_), ma adatte alle sequenze *(es. audio, video, frasi)* perché hanno un effetto memoria di breve termine: al tempo $t$ è disponibile l'informazione di $t-1, t-2, \dots$. Un tipo particolare è la **LSTM** (_Long Short Term Memory_).

---

## Multilayer Perceptron (MLP)

### Dal percettrone all'MLP

**PERCETTRONE** (Rosenblatt, **1956**): funzione di attivazione **lineare a soglia** (scalino). Un singolo percettrone, o una rete a **due soli livelli** (input e output), si addestra con una semplice **delta rule** (ispirata alla regola di Hebb).
![[Pasted image 20261112160246.png|524]]
- Una rete a due livelli di percettroni lineari a soglia impara **solo mapping lineari**: il numero di funzioni approssimabili è limitato.

**MLP** (**Multilayer Perceptron**) è una rete **feedforward** con **almeno 3 livelli** (almeno 1 hidden) e **funzioni di attivazione non lineari**.

> [!info] **Universal approximation theorem** 
> Ogni funzione **continua** che mappa intervalli di numeri reali su un intervallo di numeri reali può essere approssimata da un MLP con **un solo hidden layer**. Per molti decenni (fino al deep learning) ci si è fermati a reti a 3 livelli anche per questo. Ma **l'esistenza teorica di una soluzione non implica che esista un modo efficace per ottenerla**.

### Forward propagation (inference)

Con **forward propagation** (o _inference_) si intende la propagazione delle informazioni **in avanti**, dall'input all'output. Una volta addestrata, una rete elabora i pattern semplicemente con la forward propagation.
![[Pasted image 20261112161108.png|529]]
Nell’immagine una rete a 3 livelli $(𝑑: 𝑛_𝐻: 𝑠)$: **input** $𝑑$ neuroni, livello nascosto (**hidden**) $𝑛_𝐻$ neuroni, **output** $𝑠$ neuroni.
- **Numero di pesi (parametri):**$\qquad d\times n_H+n_H\times s+n_H+s$ $\quad$_($(n_H + s)$ sono i pesi dei **bias**)._

**$k$-esimo valore di output (Eq. 1):**
$$z_k=f\Big(\sum_{j=1\dots n_H}w_{jk}\cdot y_j+w_{0k}\Big)=f\Big(\sum_{j=1\dots n_H}w_{jk}\cdot f\Big(\sum_{i=1\dots d}w_{ij}\cdot x_i+w_{0j}\Big)+w_{0k}\Big)$$

> [!example] **Esempio grafico: rete 2:3:2:1 (2 livelli nascosti, senza bias)** 
> ![[Pasted image 20261112161547.png]]

---

## MLP: training

Fissata la **topologia** (numero di livelli e neuroni), **addestrare** significa **determinare il valore dei pesi $\mathbf{w}$** che realizzano il **mapping desiderato** tra input e output.

> [!question] Cosa vuol dire "mapping desiderato"? Dipende dal problema:
> 
> - **classificatore** → l'output desiderato è l'**etichetta corretta** della classe del pattern;
> - **regressione** → l'output desiderato è il **valore corretto** della variabile dipendente, dato il valore della variabile indipendente in input.

**Storia:** i primi neuroni artificiali sono degli anni '40, ma fino a metà anni '80 non c'erano algoritmi di training efficaci. Nel **1986** Rumelhart, Hinton e Williams introducono l'**Error Backpropagation**.

- Risolve il **credit assignment problem** (quali pesi sono responsabili degli errori) attraverso un "**tiro alla fune**" dei training pattern, che cercano di spostare il comportamento della rete per minimizzare l'errore su di essi.
- Matematicamente è l'applicazione della **regola di derivazione a catena** (idea che per decenni nessuno aveva applicato con successo in questo contesto).

> [!tip] **Intuizione del training** Si presentano alla rete pattern etichettati (es. immagini di gatti e di cani); si calcola l'**errore** $|\text{output rete}-\text{output desiderato}|$; l'errore viene **retropropagato** e i pesi vengono aggiornati.

### Backpropagation: setup per la classificazione

Problema di **classificazione supervisionata** con $s$ classi e pattern $d$-dimensionali:

- rete MLP $d:n_H:s$ → **tanti neuroni di input quante le feature**, **tanti di output quante le classi**;
- $n_H$ è un **iperparametro**: un valore ragionevole è $n_H=\frac{1}{10}n$;
- è utile la **pre-normalizzazione** dei pattern di input (_min-max scaling_, _standardization_ o _whitening_), che favorisce la convergenza;
- l'addestramento è **supervisionato**: si presentano pattern di classe nota e si propagano in avanti con l'Eq. 1;
- con **tanh** come attivazione, per un pattern di classe $g$ il vettore di output desiderato è:

$$\mathbf{t}=[-1,-1,\dots,\underset{\text{posizione }g}{1},\dots,-1]$$

- l'**errore** è la differenza tra output prodotto e desiderato; l'obiettivo è **modificare i pesi per minimizzare l'errore medio sui pattern del training set**;
- i pesi iniziali sono **random in range specifici** (vedi sezione Inizializzazione).

### Loss: somma dei quadrati degli errori

Siano $\mathbf{z}=[z_1\dots z_s]$ l'output prodotto per il pattern $\mathbf{x}=[x_1\dots x_d]$ di classe $g$, e $\mathbf{t}$ l'output desiderato ($t_i=1$ per $i=g$, $t_i=-1$ altrimenti).

$$J(\mathbf{w},\mathbf{x})\equiv\frac{1}{2}\sum_{c=1\dots s}(t_c-z_c)^2$$

> [!hint] Note
> 
> - la dipendenza dai pesi $\mathbf{w}$ è **implicita in $\mathbf{z}$**;
> - l'errore sull'intero training set $J(\mathbf{w})$ è la **media** di $J(\mathbf{w},\mathbf{x})$ su tutti i pattern;
> - la somma dei quadrati **non è una loss ottimale** per la classificazione (vedi cross-entropy), ma si usa per semplicità.

### Discesa del gradiente

$J(\mathbf{w})$ si riduce **modificando i pesi in direzione opposta al gradiente**: il gradiente indica la direzione di **maggior crescita** della funzione, quindi muovendosi in direzione opposta si riduce (al massimo) l'errore.

Quando la minimizzazione avviene con passi opposti al gradiente, la backpropagation si chiama anche **gradient descent**. Esistono tecniche del **secondo ordine** che possono accelerare la convergenza, ma sono applicabili in pratica solo a reti e training set di piccole/medie dimensioni.

Si considerano prima i pesi **hidden-output** ($w_{jk}$), poi quelli **input-hidden** ($w_{ij}$).

### Modifica dei pesi hidden-output

Solo $z_k$ dipende da $w_{jk}$, quindi con la regola della catena:

$$\frac{\partial J}{\partial w_{jk}}=-(t_k-z_k)\cdot\frac{\partial f(net_k)}{\partial net_k}\cdot\frac{\partial net_k}{\partial w_{jk}}=-(t_k-z_k)\cdot f'(net_k)\cdot y_j$$

Posto

$$\delta_k=(t_k-z_k)\cdot f'(net_k)\qquad\text{(Eq. 2)}$$

si ha

$$\frac{\partial J}{\partial w_{jk}}=-\delta_k\cdot y_j\qquad\text{(Eq. 3)}$$

e l'aggiornamento (con $\eta$ = **learning rate**):

$$w_{jk}=w_{jk}+\eta\cdot\delta_k\cdot y_j\qquad\text{(Eq. 4)}$$

> [!note] Bias Se si usano i bias, $w_{0k}$ si aggiorna ponendo $y_0=1$.

### Modifica dei pesi input-hidden

Qui il peso $w_{ij}$ influenza **tutte** le uscite (passa da $y_j$), quindi compare una somma su $c$:

$$\frac{\partial J}{\partial w_{ij}}=-\sum_{c=1\dots s}(t_c-z_c),f'(net_c),\frac{\partial net_c}{\partial w_{ij}}$$

con $\dfrac{\partial net_c}{\partial w_{ij}}=w_{jc}\cdot f'(net_j)\cdot x_i$ (solo $net_j$ dipende da $w_{ij}$). Quindi:

$$\frac{\partial J}{\partial w_{ij}}=-x_i\cdot f'(net_j)\sum_{c=1\dots s}w_{jc}\cdot\delta_c$$

Posto

$$\delta_j=f'(net_j)\cdot\sum_{c=1\dots s}w_{jc}\cdot\delta_c\qquad\text{(Eq. 5)}$$

($\delta_c$ vedi Eq. 2), si ottiene

$$\frac{\partial J}{\partial w_{ij}}=-\delta_j\cdot x_i\qquad\text{(Eq. 6)}$$

$$w_{ij}=w_{ij}+\eta\cdot\delta_j\cdot x_i\qquad\text{(Eq. 7)}$$

> [!tip] **Il filo logico del "back"**
> 
> - il $\delta$ del livello di **output** nasce dall'**errore** $(t-z)$ moltiplicato per la derivata dell'attivazione;
> - il $\delta$ di un neurone **hidden** è la **somma dei $\delta$ del livello dopo, pesati dai pesi che li collegano**, moltiplicata per la derivata dell'attivazione;
> - l'aggiornamento di un peso è sempre: $\eta\cdot\delta_{\text{(neurone a valle)}}\cdot(\text{valore in ingresso al peso})$.
> 
> Con $\delta_k$ in funzione dei $\delta$ successivi, l'errore viene "spinto all'indietro" livello per livello.

> [!example] **Backpropagation sulla rete 2:3:2:1 (senza bias)**
> 
> - uscita: $\delta=(t-y)\cdot f_6'(net)$;
> - livello 2: $\delta_4=w_{46},\delta,f_4'(net_4)$, $\delta_5=w_{56},\delta,f_5'(net_5)$;
> - livello 1: $\delta_1=(w_{14}\delta_4+w_{15}\delta_5),f_1'(net_1)$, e analogamente $\delta_2,\delta_3$;
> - aggiornamento: $w'_{(x1)1}=w_{(x1)1}+\eta,\delta_1,x_1$, ecc. per tutti gli altri pesi.
> 
> Sul sito http://playground.tensorflow.org si possono sperimentare questi concetti visivamente.

---

## Algoritmo Backpropagation online

Nella versione **online** i pattern sono presentati **sequenzialmente** e i pesi sono aggiornati **dopo ogni pattern**.

```text
Inizializza n_H, w, η, num_epoch, epoch ← 0
do epoch ← epoch + 1
    totErr ← 0                       // errore cumulato su tutti i pattern del TS
    for each x in Training Set
        forward step: x → z_k, k = 1..s          (eq. 1)
        totErr ← totErr + J(w, x)
        backward step: δ_k, k = 1..s (eq. 2),  δ_j, j = 1..n_H (eq. 5)
        aggiorna pesi hidden-output  w_jk = w_jk + η·δ_k·y_j    (eq. 4)
        aggiorna pesi input-hidden   w_ij = w_ij + η·δ_j·x_i    (eq. 7)
    loss ← totErr / n                // errore medio sul TS
    calcola accuratezza su Train Set e Validation Set
while (not convergence and epoch < num_epoch)
```

- La **convergenza** si valuta monitorando l'andamento della **loss** e l'**accuratezza sul validation set**.
- Svantaggi dell'online: problemi di **efficienza** (molti update di pesi) e di **robustezza** (un outlier può far fare passi in direzioni sbagliate).

---

## Stochastic Gradient Descent (SGD)

È l'approccio **più utilizzato** per implementare la backpropagation.

- a ogni epoca gli $n$ pattern sono **ordinati a caso** e poi suddivisi, in sequenza, in gruppi di uguale dimensione $size_{mb}$ chiamati **mini-batch**;
    - $size_{mb}=1$ → "stochastic" **online**;
    - $size_{mb}=n$ → "full" **batch**;
- i valori del **gradiente** sono **accumulati** (algebricamente) in variabili temporanee, una per peso; i pesi si aggiornano **solo quando tutti i pattern del mini-batch sono stati processati**.

```text
Inizializza n_H, w, η, num_epoch, size_mb, epoch ← 0
do epoch ← epoch + 1
    random sort the Training Set (n patterns)
    for each mini-batch B of size_mb
        reset gradiente
        for each x in B
            forward step
            backward step            // gradiente cumulato su tutti i pattern del mini-batch
        aggiorna pesi w con learning rate η
    calcola Loss
    calcola accuratezza su Training Set e Validation Set
while (not convergence and epoch < num_epoch)
```

### Terminologia

|Termine|Significato|
|---|---|
|**Epoca** (epoch)|presentazione alla rete di **tutti i pattern** del training set (1 volta)|
|**Iterazione**|presentazione di **un mini-batch** + conseguente **aggiornamento dei pesi** (1 volta)|

- $n/size_{mb}$ = numero di **iterazioni per epoca** (se non è intero, l'ultima iterazione processa meno pattern).
- Alcuni tool (tra cui **Caffe**) chiedono in input il numero totale di iterazioni $num_{iter}$ e $size_{mb}$ invece di $num_{epoch}$. Le epoche (non necessariamente intere) sono:

$$num_{epoch}=\frac{num_{iter}\cdot size_{mb}}{n}$$

### Tarare il minibatch size

Non è banale, come per altri iperparametri.

- **Efficienza**: $size_{mb}$ **maggiori** → training più veloce (meno aggiornamenti e processing più efficiente). Attenzione: nei modelli grandi $size_{mb}$ è limitato dalla **memoria della GPU**.
- **Generalizzazione**: i modelli generalizzano meglio quando convergono a **flat minima** (non _sharp_). Minibatch **piccoli o medi** sono **più esplorativi** (randomness) e meno attratti dagli sharp minima (da cui con large minibatch è difficile uscire).
- **$size_{mb}$ e $\eta$ non sono indipendenti**: con minibatch più grandi si può usare un learning rate **più grande**, grazie a un effetto "media" più forte nelle correzioni. Alcuni autori propongono un **aumento lineare** di $\eta$ all'aumentare di $size_{mb}$.

---

## Softmax e Cross-Entropy

### Il problema dell'output della rete

Se nel livello di output si usa:

- **Tanh** → uscite in $[-1,1]$: **non sono probabilità**;
- **Sigmoid** → uscite in $[0,1]$, ma **nessuna garanzia che la somma sia 1** (requisito fondamentale per una distribuzione di probabilità): $\sum_{c=1\dots s}z_c\neq1$.

### Softmax

Per un classificatore **multi-classe**, la softmax trasforma i valori di $net$ dell'ultimo livello in **probabilità delle classi**. Il $net_k$ si calcola come al solito, ma la funzione di attivazione del neurone $k$ è:

$$z_k=f(net_k)=\frac{e^{net_k}}{\sum_{c=1\dots s}e^{net_c}}$$

- i valori $z_k$ sono in $[0,1]$ e **sommano a 1** → interpretabili come **probabilità delle classi**;
- l'esponenziale (utile con la cross-entropy) interpreta i $net$ come **unnormalized log probabilities** delle classi.

### Cross-Entropy Loss

Con attivazione **tanh** + **sum of squared error** (usate finora) la scelta **non è ottimale**: gli output non rappresentano probabilità e la **non imposizione del vincolo di somma a 1** rende in genere meno efficace l'apprendimento.

Per la classificazione multi-classe si consiglia la **cross-entropy** (detta anche _Multinomial Log Loss_ o _Multinomial Logistic Regression Loss_). Tra due distribuzioni discrete $p$ e $q$ (fissata $p$, misura quanto $q$ differisce da $p$):

$$H(p,q)=-\sum_v p(v)\cdot\log\big(q(v)\big)$$

Come loss: $p$ (fissato) è il vettore **target**, $q$ il vettore di **output** della rete.

- $H\ge0$, con valore minimo quando target e output coincidono;
- il logaritmo non esiste in 0, ma gli output di softmax e sigmoid **non valgono mai 0** (vi tendono solo asintoticamente).

### Cross-Entropy con target one-hot

Con target **one-hot** (tutti 0 tranne un 1 per la classe corretta $g$): $\mathbf{t}=[0,0,\dots,\underset{\text{pos. }g}{1},\dots,0]$

$$J(\mathbf{w},\mathbf{x})\equiv H(\mathbf{t},\mathbf{z})=-\log(z_g)$$

e se $z_g$ viene dalla softmax:

$$J(\mathbf{w},\mathbf{x})=-\log\Big(\frac{e^{net_g}}{\sum_{c=1\dots s}e^{net_c}}\Big)=-net_g+\log\sum_{c=1\dots s}e^{net_c}$$

> [!tip] **Intuizione** Si punta ad **alzare** il $net$ della classe giusta ($-net_g$) e ad **abbassare** il totale degli esponenziali. La derivazione del gradiente è nel report _"Notes on Backpropagation"_ di Peter Sadowski.

### Quale loss per quale problema

|Problema|Attivazione ultimo livello|Loss|
|---|---|---|
|**Multi-classe**|**softmax**|**cross-entropy**. Target **one-hot** (default) → semplificazione sopra. In caso di incertezza si possono usare **soft target** (valori tra 0 e 1, somma 1) con la formula completa|
|**Binaria**|**sigmoid**, **1 solo neurone** di uscita (probabilità della prima classe, la seconda è il complemento a 1)|$H(t,z)=-\big(t\log z+(1-t)\log(1-z)\big)$, con $t,z$ scalari in $[0,1]$ (preferibile al caso multiclasse con 2 classi: spesso convergenza migliore)|
|**Multi-label** (un pattern può appartenere a più classi, es. immagine con cane **e** gatto)|**sigmoid** su ogni uscita; **vincolo di somma a 1 rilassato**|estensione della binaria: si **somma $H(t,z)$ su più neuroni**|
|**Regressione** (variabili input e output vettori = _multivariate multiple regression_)|**nessuna** attivazione nel livello finale se l'output è reale non limitato (i target $\mathbf{t}=[t_1\dots t_s]$ non hanno vincoli); per stabilità conviene **riscalare l'output in $[0,1]$**|una qualsiasi delle precedenti: **MSE, MAE, MAPE**|

> [!note] Regressione e "lineare" La rete trova un mapping **non lineare** tra input e output, quindi il termine _linear_ non si applica.

---

## Inizializzazione dei pesi

L'inizializzazione random dei pesi in un **range ottimale** è molto importante per la convergenza.

> [!attention] **Il problema della "vecchia" inizializzazione** Con distribuzione **normale a media 0 e deviazione standard 1** la **varianza degli output** di un livello tende a essere **maggiore** di quella degli input. Combinato con attivazioni **saturanti** (sigmoid, tanh), il gradiente diventa quasi nullo e la rete può **non convergere**.

**Inizializzazione Xavier (Glorot)** (Glorot e Bengio, 2010): per mantenere la varianza di output simile a quella di input, con $fan_{avg}$ = media tra numero di input ($fan_{in}$) e di output ($fan_{out}$) del livello:

- distribuzione **normale** a media 0 e $\sigma=\sqrt{\dfrac{1}{fan_{avg}}}$;
- distribuzione **uniforme** in $\Big[-\sqrt{\dfrac{3}{fan_{avg}}},\ +\sqrt{\dfrac{3}{fan_{avg}}}\Big]$.

**Inizializzazione He**: usata di default con la **ReLU** (non saturante, introdotta in seguito):

$$\sigma=\sqrt{\frac{2}{fan_{in}}}$$

---

## Regolarizzazione e weight decay

Per ridurre il rischio di **overfitting** di una rete con molti parametri si usa la **regolarizzazione**, molto importante quando il training set non è grande rispetto alla **capacità del modello**.

> [!tip] **Perché pesi piccoli** Reti i cui pesi (o parte di essi) assumono **valori piccoli** (vicini a zero) producono output più **regolare e stabile**, spesso con **migliore generalizzazione**.

Si aggiunge un **termine di regolarizzazione alla loss** (es. con cross-entropy):

$$J_{Tot}=J_{Cross\text{-}Entropy}+J_{Reg}$$

|Regolarizzazione|$J_{Reg}$|
|---|---|
|**L2**|$\dfrac{1}{2}\lambda\sum_i w_i^2$ (somma dei **quadrati** di tutti i pesi)|
|**L1**|$\lambda\sum_i\lvert w_i\rvert$ (valore **assoluto**)|

In entrambi i casi $\lambda$ regola la **forza** della regolarizzazione.

> [!info] **L1 vs. L2: sparsificazione** **L1** ha un effetto **sparsificante** (porta **molti pesi a 0**) maggiore di L2. Con L2, quando i pesi sono vicini a zero il quadrato **riduce eccessivamente i correttivi** ai pesi, rendendo difficile azzerarli.

### Weight decay (da L2)

Il gradiente di $J_{Tot}$ è la **somma** dei gradienti di $J_{Cross\text{-}Entropy}$ e di $J_{Reg}$. Quest'ultimo vale:

$$\frac{\partial J_{Reg}}{\partial w_k}=\frac{\partial}{\partial w_k}\Big(\frac{1}{2}\lambda\sum_i w_i^2\Big)=\lambda\cdot w_k$$

L'aggiornamento di SGD (Eq. 4 e 7):

$$w_k=w_k-\eta\cdot\frac{\partial J_{Cross\text{-}Entropy}}{\partial w_k}$$

diventa

$$w_k=w_k-\eta\Big(\frac{\partial J_{Cross\text{-}Entropy}}{\partial w_k}+\lambda\cdot w_k\Big)$$

Il termine $\lambda\cdot w_k$ è il **weight decay** (decadimento del peso): **tira il peso verso 0**.

> [!question] **Domanda della slide: e per L1? Quanto vale la derivata del valore assoluto?** $\dfrac{\partial\lvert w\rvert}{\partial w}=\text{sign}(w)$, cioè $+1$ se $w>0$, $-1$ se $w<0$ (in $w=0$ non è definita). Quindi con L1 il termine aggiunto vale $\lambda\cdot\text{sign}(w_k)$: il peso viene spinto verso 0 con una forza **costante**, indipendente dalla sua grandezza. Questo spiega perché L1 riesce a **azzerare** i pesi, mentre in L2 la forza $\lambda w_k$ si **riduce** avvicinandosi a 0.

---

## Tarare il learning rate

Tarare $\eta$ in modo ottimale è molto importante:

- **troppo piccolo** → convergenza **lenta** e più facile rimanere intrappolati in **minimi locali**;
- **troppo grande** → **oscillazione** e/o **divergenza**.

Consigli:

- il valore ottimale cambia con architettura, loss, minibatch size, ecc.: **tararlo per ultimo**, dopo aver definito tutto il resto;
- partendo da un'architettura nota, usare i **valori consigliati** e provare ad aumentare/diminuire **monitorando convergenza e generalizzazione**;
- **attenzione**: il fatto che la loss diminuisca senza oscillare **non** significa che il valore sia ottimale (specialmente nel tuning di reti pre-addestrate): **monitorare l'accuratezza sul validation set**;
- aiutano le tecniche di **scheduling** del learning rate e gli **ottimizzatori con learning rate adattivo**.

---

## Momentum

SGD con mini-batch può produrre una discesa a **zig-zag**, che rallenta la convergenza e la rende meno stabile.

> [!example] **Analogia fisica** La discesa del gradiente è la traiettoria di una **biglia** che rotola su una superficie scoscesa, attratta dalla gravità verso il punto più basso. Il **momento** le dà un moto senza oscillazioni e cambi di direzione repentini; l'**inerzia** accumulata le permette inoltre di superare **piccoli avvallamenti** (minimi locali).

In SGD si emula questo comportamento: si tiene traccia dell'**ultimo aggiornamento** $\Delta w_k$ di ogni parametro, e il nuovo è una **combinazione lineare** del precedente (che dà **stabilità**) e del gradiente attuale (che **corregge la direzione**):

$$\Delta w_k=\mu\cdot\Delta w_k-\eta,\frac{\partial J_{Tot}}{\partial w_k}\qquad w_k=w_k+\Delta w_k$$

Valore tipico: $\mu=0.9$.

---

## Learning rate adattativo

Oltre al momentum esistono altri **ottimizzatori** che possono accelerare la convergenza (vedi _Faster Optimizers_ in [A. Géron]):

- **NAG** (Nesterov Accelerated Gradient);
- **Adagrad** (Adaptive Gradient);
- **Adadelta**;
- **RMSProp**;
- **Adam** (e **AdamW**).

La maggior parte **adatta $\eta$ a ogni specifico parametro**: **passi più lunghi** lungo le direzioni (parametri) in cui il gradiente **varia poco**. Serve mantenere **statistiche parametro per parametro** sulla variazione del gradiente.

> [!tip] **Adam** È considerato lo **stato dell'arte**, anche se non sempre dà risultati migliori del più classico **SGD con Momentum**.

---

## La rete non converge: check list

Cose da verificare quando la convergenza sul training set **non parte** (la rete sembra bloccata):

- [ ] gli **input** sono stati **normalizzati** (min-max, standard scaling)?
- [ ] i **pesi** sono stati inizializzati random in range ragionevoli (es. **Xavier, He**)? Altrimenti si può essere in **zona di saturazione** da cui non si esce;
- [ ] i pattern delle diverse classi sono **mescolati random** all'interno dei minibatch?
- [ ] le **etichette** sono nel formato giusto (interi sparsi, one-hot, float per regressione...)?
- [ ] la **loss function** è corretta e adeguata al tipo di label (attenzione alla differenza tra **label sparse** e **vettori one-hot**)?
- [ ] la **funzione di attivazione** all'ultimo livello è corretta (**sigmoid** per binaria, **softmax** per cross-entropy multiclasse, **nessuna** per regressione)?
- [ ] il **learning rate** è adeguato?

