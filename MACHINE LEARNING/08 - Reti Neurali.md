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

Si ottiene dalla sigmoide con scala ($\times2$) e traslazione ($-1$):
![[Pasted image 20261112155050.png]]
> [!abstract] Ovviamente le funzioni sono prese nel range di valori in $[0...1]$

> [!question] Che differenza c'è tra **Sigmoid** e ** tanh**? 
> Tanh è leggermente più complessa ma **preferibile** (convergenza più rapida) perché è **simmetrica rispetto allo zero**.
> 
> Un input molto negativo dà un valore vicino a $-1$, uno molto positivo dà un valore vicino a $+1$, e $x=0$ dà $0$.

> [!question] Perché le derivate "comode" contano?
>Per sigmoid e tanh la derivata è scritta **in funzione dell’output**:
$$\sigma'(net)=\sigma(net)\big(1-\sigma(net)\big)\qquad\tau'(net)=1-\tau(net)^2$$
>
>Cioè, se $y=f(net)$ è l’output già calcolato:
>
>- **sigmoid**: $f'=y\,(1-y)$ → una sottrazione e una moltiplicazione;
>- **tanh**: $f'=1-y^2$ → un quadrato e una sottrazione.
>
>Non devi più calcolare un esponenziale né ricalcolare $net$: il valore è già in memoria e basta un paio di operazioni aritmetiche.

---
## Tipologie di reti neurali

Le reti sono gruppi di neuroni organizzati in **livelli**: un livello di **input**, uno di **output** e uno o più livelli **intermedi (hidden)**. Ogni livello contiene uno o più neuroni.

![[Pasted image 20261112155535.png]]
Le reti si distinguono principalmente in:
1. **Feedforward** , con **connessioni** solo verso il livello successivo. 
2. **Recurrent**, in cui sono previste connessioni di feedback (in genere verso lo stesso livello, ma anche all'indietro).

> [!hint] **Nota sul Recurrent** 
> Flusso delle informazioni e addestramento più complicati (_unfolding in time_), ma adatte alle sequenze *(es. audio, video, frasi)* perché hanno un effetto memoria di breve termine: al tempo $t$ è disponibile l'informazione di $t-1, t-2, \dots$. Un tipo particolare è la **LSTM** (_Long Short Term Memory_).

---
## Multilayer Perceptron (MLP)

### Dal percettrone all'MLP

**PERCETTRONE** - funzione di attivazione **lineare a soglia** (scalino). Un singolo percettrone, o una rete a **due soli livelli** (input e output), si addestra con una semplice **delta rule** (ispirata alla regola di Hebb).
$$net=\sum_j w_j\,x_j+w_0\qquad out=\begin{cases}1&\text{se }net\ge0\\0&\text{altrimenti}\end{cases}$$
![[Pasted image 20261112160246.png|524]]
> [!fail] Un percettrone divide lo spazio degli input con **una retta** (in 2D; un piano in 3D, un iperpiano in generale). Da un lato risponde 1, dall’altro 0.

**MLP** (**Multilayer Perceptron**) - è una rete **feedforward** con **almeno 3 livelli** (almeno 1 hidden) e **funzioni di attivazione non lineari**.

> [!info] **Universal approximation theorem** 
> Ogni funzione **continua** che mappa intervalli di numeri reali su un intervallo di numeri reali può essere approssimata da un MLP con **un solo hidden layer**. 
> > [!attention] Ma **l'esistenza teorica di una soluzione non implica che esista un modo efficace per ottenerla**.

### Forward propagation (inference)

Con **forward propagation** (o _inference_) si intende la propagazione delle informazioni **in avanti**, dall'input all'output. Una volta addestrata, una rete elabora i pattern semplicemente con la forward propagation.
![[Pasted image 20261112161108.png|529]]
Nell’immagine una rete a 3 livelli $(𝑑: 𝑛_𝐻: 𝑠)$: 
- **input** $𝑑$ neuroni, 
- livello nascosto (**hidden**) $𝑛_𝐻$ neuroni, 
- **output** $𝑠$ neuroni.
- **Numero di pesi (parametri):**$\qquad d\times n_H+n_H\times s+n_H+s$ $\quad$_($(n_H + s)$ sono i pesi dei **bias**)._

**$k$-esimo valore di output (Eq. 1):**
$$z_k=f\Big(\sum_{j=1\dots n_H}w_{jk}\cdot y_j+w_{0k}\Big)=f\Big(\sum_{j=1\dots n_H}w_{jk}\cdot f\Big(\sum_{i=1\dots d}w_{ij}\cdot x_i+w_{0j}\Big)+w_{0k}\Big)$$

> [!example] **Esempio grafico: rete 2:3:2:1 (2 livelli nascosti, senza bias)** 
> ![[Pasted image 20261112161547.png]]

---
## MLP: training

> [!abstract] **Definizione di addestramento**
> Fissata la **topologia** (numero di livelli e neuroni), **addestrare** significa **determinare il valore dei pesi $\mathbf{w}$** che realizzano il **mapping desiderato** tra input e output.

> [!question] Cosa vuol dire "mapping desiderato"? Dipende dal problema:
> 
> - **classificatore** → l'output desiderato è l'**etichetta corretta** della classe del pattern;
> - **regressione** → l'output desiderato è il **valore corretto** della variabile dipendente, dato il valore della variabile indipendente in input.

**STORIA** - i primi neuroni artificiali sono degli anni '40, ma fino a metà anni '80 non c'erano algoritmi di training efficaci. Nel **1986** Rumelhart, Hinton e Williams introducono l'**Error Backpropagation**.
	
- Risolve il **credit assignment problem** (quali pesi sono responsabili degli errori) spostando il comportamento della rete per minimizzare l'errore su di essi.
### Setup per la classificazione

Problema di **classificazione supervisionata** con $s$ classi e pattern $d$-dimensionali.
**Struttura della rete MLP $d:n_H:s$**

- **tanti neuroni di input quante le feature** ($d$);
- **tanti neuroni di output quante le classi** ($s$);
- $n_H$ è un **iperparametro**: un valore ragionevole è $n_H=\frac{1}{10}n$.

**Preparazione**

- è utile la **pre-normalizzazione** dei pattern di input (_min-max scaling_, _standardization_ o _whitening_), che favorisce la convergenza;
- i **pesi iniziali** sono random in range specifici (vedi Xavier / He).

**Addestramento**

- è **supervisionato**: si presentano pattern di classe nota e si propagano in avanti con l'**Eq. 1**;
- l'**errore** è la differenza tra output prodotto e desiderato;
- l'**obiettivo** è modificare i pesi per **minimizzare l'errore medio** sui pattern del training set.

> [!example] **Output desiderato con tanh** Per un pattern di classe $g$:
> 
> $$\mathbf{t}=[-1,-1,\dots,\underset{\text{posizione }g}{1},\dots,-1]$$
> 
> Es. con $s=3$ e classe 2: $\mathbf{t}=[-1,\,1,\,-1]$.

---

### Loss: somma dei quadrati degli errori

Siano $\mathbf{z}=[z_1\dots z_s]$ l'output prodotto per il pattern $\mathbf{x}=[x_1\dots x_d]$ di classe $g$, e $\mathbf{t}=[t_1\dots t_s]$ l'output desiderato dove $t_i=1$ per $i=g$, $t_i=-1$ altrimenti.

**Calcoliamo l'errore sul pattern $\mathbf{x}$ come**

$$J(\mathbf{w},\mathbf{x})\equiv\frac{1}{2}\sum_{c=1\dots s}(t_c-z_c)^2$$

> [!hint] **Note**
> 
> - la dipendenza dai pesi $\mathbf{w}$ è **implicita in $\mathbf{z}$**;
> - l'errore sull'intero training set $J(\mathbf{w})$ è la **media** di $J(\mathbf{w},\mathbf{x})$ su tutti i pattern;
> - la somma dei quadrati **non è una loss ottimale** per la classificazione, ma si usa per semplicità;
> - il $\frac12$ serve solo a **cancellare il 2** che esce derivando il quadrato.

---

### Discesa del gradiente

$J(\mathbf{w})$ si riduce modificando i pesi in **direzione opposta al gradiente**: il gradiente indica la direzione di **maggior crescita** della funzione, quindi muovendosi in direzione opposta si riduce (al massimo) l'errore.
$$w\leftarrow w-\eta\,\frac{\partial J}{\partial w}$$
in cui $\eta$ è il tasso di apprendimento e $\frac{\partial J}{\partial w}$ è il gradiente positivo (dove cresce di più)
![[Pasted image 20261112165245.png|527]]
> [!info] **Gradient descent e secondo ordine** 
> Quando la minimizzazione avviene con passi opposti al gradiente, la backpropagation si chiama anche **gradient descent**. Esistono tecniche del **secondo ordine** che possono accelerare la convergenza, ma sono applicabili in pratica solo a reti e training set di **piccole/medie dimensioni**.

> [!hint] Si considerano **prima** i pesi **hidden-output** ($w_{jk}$), **poi** quelli **input-hidden** ($w_{ij}$).

---
### Modifica dei pesi hidden-output

> [!abstract] **Voglio sapere quanto cambia l’errore $J$ se muovo un peso $w_{jk}$. Lo ottengo derivando $J$ e scomponendo la derivata con la regola della catena.**

$$\boxed{\,w_{jk}=w_{jk}+\eta\cdot\delta_k\cdot y_j\,}\qquad$$

#### Dimostrazione della formula

Vogliamo la derivata dell’errore rispetto al peso $w_{jk}$, che collega il neurone hidden $j$ al neurone di output $k$:

$$\frac{\partial J}{\partial w_{jk}}=\frac{\partial}{\partial w_{jk}}\Big(\frac12\sum_{c=1\dots s}(t_c-z_c)^2\Big)$$

Il peso $w_{jk}$ arriva solo al neurone $k$, quindi cambia solo $z_k$. Tutti i termini della somma con $c\neq k$ non dipendono da $w_{jk}$ e hanno derivata nulla, per cui resta il solo termine $c=k$:

$$=\frac{\partial}{\partial w_{jk}}\Big[\frac12(t_k-z_k)^2\Big]$$

Derivando il quadrato, l’esponente 2 scende davanti e si moltiplica per la derivata di ciò che sta dentro la parentesi. Il $\frac12$ e il 2 si cancellano:

$$=\frac12\cdot2\,(t_k-z_k)\cdot\frac{\partial(t_k-z_k)}{\partial w_{jk}}=(t_k-z_k)\cdot\frac{\partial(t_k-z_k)}{\partial w_{jk}}$$

Il target $t_k$ è una costante che non dipende dai pesi, quindi la sua derivata è zero e rimane solo la derivata di $-z_k$:

$$\frac{\partial(t_k-z_k)}{\partial w_{jk}}=\underbrace{\frac{\partial t_k}{\partial w_{jk}}}_{=0}-\frac{\partial z_k}{\partial w_{jk}}=-\frac{\partial z_k}{\partial w_{jk}}$$

Sostituendo, il segno meno esce davanti:

$$\frac{\partial J}{\partial w_{jk}}=-(t_k-z_k)\cdot\frac{\partial z_k}{\partial w_{jk}}$$

L’uscita è l’attivazione applicata a $net_k$, cioè $z_k=f(net_k)$:

$$=-(t_k-z_k)\cdot\frac{\partial f(net_k)}{\partial w_{jk}}$$

Il peso non agisce direttamente su $f$ ma su $net_k$, che entra in $f$. Con la regola della catena la derivata si spezza in due fattori:

$$\frac{\partial f(net_k)}{\partial w_{jk}}=\frac{\partial f(net_k)}{\partial net_k}\cdot\frac{\partial net_k}{\partial w_{jk}}=f'(net_k)\cdot\frac{\partial net_k}{\partial w_{jk}}$$

e quindi:

$$\frac{\partial J}{\partial w_{jk}}=-(t_k-z_k)\cdot f'(net_k)\cdot\frac{\partial net_k}{\partial w_{jk}}$$

Resta da calcolare l’ultimo fattore. $net_k$ è la somma pesata degli output degli hidden:

$$net_k=\sum_{s=1\dots n_H}w_{sk}\cdot y_s$$

e la sua derivata rispetto a $w_{jk}$ si ottiene derivando termine per termine:

$$\frac{\partial net_k}{\partial w_{jk}}=\sum_{s=1\dots n_H}\frac{\partial(w_{sk}\cdot y_s)}{\partial w_{jk}}$$

Solo il termine $s=j$ contiene $w_{jk}$ e la sua derivata vale $y_j$, mentre tutti gli altri sono costanti rispetto a $w_{jk}$ e danno zero:

$$\frac{\partial(w_{sk}\cdot y_s)}{\partial w_{jk}}=\begin{cases}y_j&\text{se }s=j\\0&\text{se }s\neq j\end{cases}\qquad\Rightarrow\qquad\frac{\partial net_k}{\partial w_{jk}}=y_j$$

Inserendo questo risultato:

$$\frac{\partial J}{\partial w_{jk}}=-(t_k-z_k)\cdot f'(net_k)\cdot y_j$$

La parte che dipende solo dal neurone di output $k$ si raggruppa in un unico termine, chiamato $\delta_k$:

$$\delta_k=(t_k-z_k)\cdot f'(net_k)\qquad\text{(Eq. 2)}$$

e la derivata si scrive in forma compatta:

$$\frac{\partial J}{\partial w_{jk}}=-\delta_k\cdot y_j\qquad\text{(Eq. 3)}$$

Per ridurre l’errore il peso si sposta in direzione opposta al gradiente, con $\eta$ learning rate:

$$w_{jk}\leftarrow w_{jk}-\eta\cdot\frac{\partial J}{\partial w_{jk}}$$

Sostituendo l’Eq. 3 i due segni meno si cancellano:

$$w_{jk}=w_{jk}-\eta\cdot(-\delta_k\cdot y_j)$$ $$\boxed{\,w_{jk}=w_{jk}+\eta\cdot\delta_k\cdot y_j\,}\qquad\text{(Eq. 4)}$$

Il bias $w_{0k}$ è un peso come gli altri con ingresso fisso a 1, quindi si aggiorna con la stessa formula ponendo $y_0=1$.

---

#### Il filo logico del "back"

1. il $\delta$ del livello di **output** nasce dall'**errore** $(t-z)$ moltiplicato per la **derivata dell'attivazione**;
2. il $\delta$ di un neurone **hidden** è la **somma dei $\delta$ del livello dopo, pesati dai pesi che li collegano**, moltiplicata per la derivata dell'attivazione;
3. l'**aggiornamento** di un peso è sempre: $$\eta\cdot\delta_{\text{(neurone a valle)}}\cdot(\text{valore in ingresso al peso})$$

> [!tip] **In una frase** 
> Con $\delta_k$ in funzione dei $\delta$ successivi, l'errore viene "**spinto all'indietro**" livello per livello.

> [!attention] **Ordine delle operazioni** 
> Prima si calcolano **tutti i $\delta$** (con i pesi ancora vecchi), poi si aggiornano i pesi.

---
## Piccolo Sommario

- **Forward Propagation** - È il calcolo dell'output. I dati entrano, passano attraverso i pesi e le funzioni di attivazione, ed esce una predizione $z$.
    
- **Loss Function** - È il metro di giudizio. Confronta l'output della rete $z$ con quello desiderato $t$ e calcola quanto la rete ha sbagliato.
    
- **Backpropagation** - È il calcolo della "colpa" (il **gradiente**). Usa la regola della catena per capire _in che direzione_ e _di quanto_ bisogna modificare ogni singolo peso per ridurre quell'errore.
    
- **Gradient Descent / SGD** - È la **strategia operativa** di aggiornamento. Decide _ogni quanti esempi_ applicare effettivamente le modifiche ai pesi calcolate dalla backpropagation.
## Stochastic Gradient Descent (SGD)

> [!abstract] È l’approccio più utilizzato per implementare backpropagation

### Terminologia

**EPOCA (epoch)** - la presentazione alla rete di tutti i pattern del training set (1 volta)

**MINI-BATCH** - un piccolo sottoinsieme (gruppo) di pattern di uguale dimensione ($size_{mb}$) estratto dal training set dopo che quest'ultimo è stato mescolato in modo casuale ad ogni epoca.

**ITERAZIONE (iteration)** - la presentazione dei pattern costituenti un mini-batch e il conseguente aggiornamento dei pesi (1 volta)
![[Pasted image 20261116180001.png]]
### Tarare il minibatch size

> [!abstract] Tipologie di discesa del gradiente in base a $size_{mb}$
>- **STOCHASTIC** - se $size_{mb} = 1$
>- **FULL** - se $size_{mb} = n$

La scelta del *minibatch size* dipende da:
##### **Efficienza computazionale**
    
- Un $size_{mb}$ maggiore velocizza il training (meno aggiornamenti del gradiente e calcolo più efficiente su batch grandi).
        
> [!fail] _Limite hardware_
> Nei modelli grandi, la dimensione massima è vincolata dalla memoria disponibile nella GPU (il gradiente di un batch viene memorizzato in variabili temporanee)
        
##### **Capacità di generalizzazione:**
    
- I modelli generalizzano meglio quando convergono verso **flat minima** (minimi piatti) anziché _sharp minima_ (minimi stretti/appuntiti).
        
- Mini-batch piccoli o medi introducono maggiore casualità (rumore), rendendo la ricerca più esplorativa ed evitando di restare intrappolati negli _sharp minima_ (da cui con batch grandi è difficile uscire).

> [!hint] **Relazione con il Learning Rate ($\eta$):**
> $size_{mb}$ e $\eta$ non sono indipendenti: con mini-batch più grandi il gradiente medio è più stabile, quindi si può usare un learning rate più alto 

