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
$$num_{epoch}=\frac{num_{iter}\cdot size_{mb}}{n}$$
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

---
## Softmax

> [!fail] Problema
> **Tanh** e **Sigmoid** non danno probabilità: la somma delle uscite NON è 1.

Il livello di attivazione $net_k$ dei singoli neuroni dell'ultimo livello si calcola nel modo consueto, ma come funzione di attivazione per il neurone $k-esimo$ si utilizza:
$$z_k=f(net_k) = \frac{e^{net_k}}{\sum_{c=1\dots s}e^{net_c}}$$

$$z_k \in [0\dots 1]\quad \mid \quad \sum_i^{k}z_i=1$$
-  $z_k$: è l'output finale per la classe $k$, ovvero la probabilità stimata che l'input appartenga alla classe $k$.

- $f(net_k)$: indica l'applicazione della funzione di attivazione $f$ (la Softmax) all'input netto del neurone $k$.

- $net_k$: è la somma pesata degli input (più il bias) che arriva al neurone $k$ prima dell'attivazione (spesso chiamata _logit_).

## Cross-entropy

> [!abstract] È la loss che si abbina al softmax
> Misura **quanto una distribuzione di probabilità $q$ (l’output della rete) si discosta da una distribuzione $p$ (il target)**. Più la rete assegna probabilità bassa alla risposta giusta, più la loss sale.

$$H(p,q)=-\sum_v p(v)\,\log q(v)$$
- $p=\mathbf t$ è il target, **fissato**;
- $q=\mathbf z$ è l’output della rete, che deve essere una distribuzione. 
	Per questo serve il **softmax**: $z_k\in(0,1)$ e $\sum_k z_k=1$.

> [!hint] Quindi la loss dipende **solo dalla probabilità che la rete assegna alla classe giusta**. Delle altre uscite non importa direttamente nulla, ma poiché il softmax somma a 1, alzare $z_g$ abbassa automaticamente le altre.

Usando Softmax e Cross-Entropy **calcoliamo l'errore sul pattern $\mathbf{x}$ come**:
$$J(w,x)=-\log(\frac{e^{net^g}}{\sum_{c=1\dots s}{e^{net_c}}})=-net^g+\log\sum_{c=1\dots s}e^{net_c}$$
### One-hot

> [!abstract] Si dice che il target vector per $x$ assume la forma *one-hot* se il vettore $t$ ha tutti $0$ tranne un $1$ per la classe corretta $g$

$$J(w,x)=H(t,z)=-log(z_g)$$

---
## ## Output e Loss: scegliere in base al problema

> [!hint] Ricorda il _"mapping desiderato"_, diceva che il comportamento dipende dal **problema**. 
> **Attivazione finale e loss si scelgono in base al compito** (classificazione o regressione). 
> Tutto il resto della rete (hidden, backpropagation, SGD) **non cambia**.

##### Classificazione multi-classe
Utilizzare funzione di attivazione **softmax** nel livello finale e **cross-entropy** come loss function.

##### Classificazione binaria
Si può ricadere in due opzioni diverse:
1. Trattare il problema come multiclasse con 2 classi
2. Utilizzare **1 neurone di uscita** che codifica la probabilità della **SOLA** prima classe.
	- funzione di attivazione **sigmoide**

##### Classificazione multi-label

> [!question] Perché non basta la softmax?
> La softmax impone $\sum_k z_k=1$: le classi **competono** e il pattern ha **una sola** classe vera. Con un'immagine che contiene **cane e gatto** il target $[1,1,0]$ non è rappresentabile: al massimo si otterrebbe $[0.5,0.5,0]$ ("metà e metà"), mentre vogliamo dire "sicuramente entrambi".

> [!hint] **Idea**
> Trattare il problema come **$s$ classificazioni binarie indipendenti** ("la classe $k$ è presente sì/no?") fatte da un'unica rete.

- ogni neurone di uscita ha una **sigmoid**: $z_k=P(\text{classe }k\text{ presente})$;
- ovviamente non c'è più il vincolo di somma a 1;
- target **multi-hot** (es. $\mathbf t=[1,1,0]$);
- loss: **somma** della cross-entropy binaria sui neuroni:

$$J=-\sum_{k=1\dots s}\Big[t_k\log z_k+(1-t_k)\log(1-z_k)\Big]$$

Per ogni neurone vale ancora $\delta_k=t_k-z_k$; gli errori si combinano solo negli hidden (che sono condivisi).

##### Regressione

> [!question] Cosa cambia rispetto alla classificazione?
> La rete risponde a *"quanto vale?"*: l'output è uno o più **numeri reali**. 
> Non ci sono classi, quindi **niente softmax e niente cross-entropy**. 

**Nessuna attivazione finale** ($z_k=net_k$, $f'=1$), in quanto una funzione di attivazione ridurrebbe il range in output, mentre la regressione deve poter ritornare valori teoricamente infiniti.

> [!hint] Perché riscalare comunque in $[0,1]$
> Si parla di **preprocessing dei dati**. È solo per avere numeri piccoli e quindi gradienti gestibili, come per la normalizzazione degli input. Prima di addestrare dividi i prezzi per il massimo, e dopo l’inferenza moltiplichi per il massimo.

**Loss (a scelta):**

| Loss     | Formula                                                 | Caratteristiche                                                        |
| -------- | ------------------------------------------------------- | ---------------------------------------------------------------------- |
| **MSE**  | $\frac1n\sum(t-z)^2$                                    | errori grandi pesano molto, sensibile agli outlier                     |
| **MAE**  | $\frac1n\sum\lvert t-z\rvert$                           | errore lineare, più robusta agli outlier; gradiente costante in modulo |
| **MAPE** | $\frac{100}{n}\sum\left\lvert\frac{t-z}{t}\right\rvert$ | errore **relativo** in %, problematica se $t\approx0$                  |
## Rendere il training efficace: dettagli pratici

> [!abstract] Il filo logico di questa sezione Abbiamo: una rete, una loss $J$, la backpropagation e la regola $w\leftarrow w-\eta,\partial J/\partial w$ (SGD). Ora si affrontano i **quattro modi in cui il training può andare male**, uno per sezione:
> 
> |Problema|Soluzione|
> |---|---|
> |la discesa **non parte** (gradienti ~0 fin dall'inizio)|**inizializzazione** dei pesi|
> |la rete **memorizza** il training set (overfitting)|**regolarizzazione** (L1/L2, weight decay)|
> |la discesa è **lenta, instabile o a zig-zag**|**learning rate**, **momentum**, **ottimizzatori adattivi**|
> |non si capisce **cosa non va**|**check-list** finale|

---

### Inizializzazione dei pesi

I pesi di partenza sono **random**, ma il **range** conta moltissimo per la convergenza.

> [!question] Cosa non va con la "vecchia" inizializzazione $\mathcal N(0,1)$? Un neurone con $fan_{in}$ ingressi calcola $net=\sum_j w_j x_j$. Se pesi e ingressi sono indipendenti con media 0:
> 
> $$\mathrm{Var}(net)\approx fan_{in}\cdot\mathrm{Var}(w)\cdot\mathrm{Var}(x)$$
> 
> Con $\mathrm{Var}(w)=1$ la varianza dell'uscita è $fan_{in}$ volte quella dell'ingresso. Esempio: $fan_{in}=100$ → la deviazione standard di $net$ è circa $10$ volte quella degli input.
> 
> Con **sigmoid o tanh** (funzioni **saturanti**) un $net$ di valore $\pm10$ cade nelle zone piatte dove $f'\approx0$: i $\delta$ si annullano, le correzioni sono quasi nulle e la rete **non converge**. Peggio ancora con più livelli, perché l'effetto si compone.

**Idea:** scegliere $\mathrm{Var}(w)$ in modo che la varianza dell'output di un livello sia **simile a quella dell'input**.

#### Xavier (o Glorot) — per sigmoid/tanh

Glorot e Bengio (2010). Sia $fan_{avg}=\dfrac{fan_{in}+fan_{out}}{2}$ (media tra numero di input e di output del livello).

- distribuzione **normale** a media 0 e deviazione standard $\sigma=\sqrt{\dfrac{1}{fan_{avg}}}$;
- oppure distribuzione **uniforme** in $\Big[-\sqrt{\dfrac{3}{fan_{avg}}},;+\sqrt{\dfrac{3}{fan_{avg}}}\Big]$.

> [!hint] Perché compare anche $fan_{out}$ $fan_{in}$ da solo manterrebbe la varianza nella **forward** propagation. Il compromesso con $fan_{out}$ la mantiene anche nei gradienti della **backward**.

#### He — per ReLU (default)

Con **ReLU** (funzione non saturante, introdotta nel deck Deep Learning) metà dei neuroni è spenta ($f=0$ per $net<0$) e la varianza si dimezza. Si compensa raddoppiando:

$$\sigma=\sqrt{\frac{2}{fan_{in}}}$$

|Attivazione|Inizializzazione|
|---|---|
|sigmoid, tanh|**Xavier**|
|ReLU (e varianti)|**He**|

> [!tip] Collegamento Questo è il punto che nella sezione "Setup per la classificazione" avevamo lasciato come _"i pesi iniziali sono random in range specifici (vedi Xavier / He)"_.

---

### Regolarizzazione

> [!abstract] Obiettivo Ridurre il rischio di **overfitting** di reti con **molti parametri** (pesi). Importante soprattutto quando il training set non è grande rispetto alla capacità del modello.

**Idea:** reti con pesi **piccoli** (vicini a 0) producono output più **regolare e stabile** e generalizzano meglio. Per spingere la rete verso pesi piccoli si **aggiunge un termine alla loss**:

$$J_{Tot}=J_{Cross\text{-}Entropy}+J_{Reg}$$

||Termine $J_{Reg}$|Nota|
|---|---|---|
|**L2**|$\lambda\sum_i w_i^2$|somma dei **quadrati** di tutti i pesi|
|**L1**|$\lambda\sum_i \lvert w_i\rvert$|somma dei **valori assoluti**|

$\lambda$ regola la **forza** della regolarizzazione ($\lambda$ grande → pesi molto piccoli, rischio di _underfitting_).

> [!info] L1 vs L2 **L1 ha un effetto sparsificante** (porta molti pesi esattamente a 0) più forte di L2. Motivo: con L2 la spinta verso 0 è **proporzionale al peso**, quindi vicino allo zero diventa minuscola e il peso non si azzera mai del tutto. Con L1 la spinta ha **intensità costante** ($\pm\lambda$) qualunque sia il peso, e lo porta a 0.

#### Weight decay

Consideriamo L2. Il gradiente di $J_{Tot}$ rispetto a un parametro è la **somma** dei gradienti dei due termini. Quello di $J_{Reg}$ (scrivendo il termine come $\frac{\lambda}{2}\sum_i w_i^2$, che è la convenzione che rende il conto pulito, in modo analogo al $\frac12$ della SSE):

$$\frac{\partial J_{Reg}}{\partial w_k}=\frac{\partial}{\partial w_k}\Big(\frac{\lambda}{2}\sum_i w_i^2\Big)=\lambda\cdot w_k$$

L'aggiornamento di SGD:

$$w_k=w_k-\eta,\frac{\partial J_{CE}}{\partial w_k}\qquad\text{diventa}\qquad w_k=w_k-\eta\Big(\frac{\partial J_{CE}}{\partial w_k}+\lambda,w_k\Big)$$

Riordinando:

$$w_k=\underbrace{(1-\eta\lambda)}_{<1},w_k-\eta,\frac{\partial J_{CE}}{\partial w_k}$$

A ogni iterazione il peso viene **prima rimpicciolito** del fattore $(1-\eta\lambda)$ e poi corretto dal gradiente: è il **decadimento del peso** (_weight decay_), che lo tira verso 0.

> [!example] Numeri $\eta=0.1$, $\lambda=0.01$, $w=2$, gradiente della loss $=0$ → $w=(1-0.001)\cdot2=1.998$. Piccolo ma costante: i pesi che **non servono** (gradiente ~0) decadono, quelli utili restano perché il gradiente li sostiene.

> [!question] Per L1? La derivata di $\lambda\lvert w\rvert$ è $\lambda\cdot\mathrm{sign}(w_k)$: una spinta di valore **fisso** verso lo zero (in $w=0$ non è definita; in pratica si pone 0). Conferma l'effetto sparsificante.

> [!attention] Nota sulla slide Il prof. scrive $J_{Reg}=\lambda\sum w_i^2$ e poi deriva come se ci fosse un $\frac12$. È solo una questione di convenzione (il fattore 2 si assorbe in $\lambda$): il risultato qualitativo è lo stesso.

> [!tip] Collegamento Vedrai che il **Dropout** (deck Deep Learning) è un altro regolarizzatore, che agisce sui neuroni invece che sui pesi.

---

### Learning rate

$\eta$ è l'iperparametro **più importante** da tarare.

|$\eta$|Effetto|
|---|---|
|**troppo piccolo**|convergenza lenta, più facile restare intrappolati in minimi locali|
|**troppo grande**|oscillazioni e/o divergenza|

- Il valore ottimale dipende da architettura, loss, $size_{mb}$ ecc. (vedi la relazione con il minibatch nella sezione SGD).
- **Consiglio:** tararlo **per ultimo**, dopo aver fissato tutto il resto.
- Se si parte da un'architettura nota già usata su problemi simili, **partire dai valori consigliati** e provare ad aumentare/diminuire, monitorando convergenza e generalizzazione.

> [!attention] La loss che scende non basta Il fatto che la loss diminuisca senza oscillare **non significa** che $\eta$ sia ottimale (specialmente nel tuning di reti pre-addestrate). Va monitorata l'**accuratezza sul validation set**.

Aiutano: tecniche di **scheduling** del learning rate (si riduce $\eta$ durante il training) e **ottimizzatori con learning rate adattivo** (vedi dopo).

---

### Momentum

> [!question] Problema SGD con mini-batch può scendere a **zig-zag** (il gradiente cambia direzione da un'iterazione all'altra), rallentando la convergenza e rendendola meno stabile.

**Analogia fisica:** la discesa è una **biglia che rotola** su una superficie scoscesa. Per il **momento** la biglia ha un moto senza oscillazioni o cambi bruschi di direzione e, grazie all'inerzia, supera piccoli avvallamenti (minimi locali).

**Implementazione:** per ogni parametro $w_k$ si tiene traccia dell'**ultimo aggiornamento** $\Delta w_k$. Il nuovo aggiornamento è una combinazione lineare del precedente (che dà **stabilità**) e del gradiente attuale (che **corregge la direzione**):

$$\Delta w_k=\mu\cdot\Delta w_k-\eta,\frac{\partial J_{Tot}}{\partial w_k}\qquad\qquad w_k=w_k+\Delta w_k$$

Valore tipico: $\mu=0.9$. Con $\mu=0$ si torna a SGD semplice.

> [!example] Perché funziona
> 
> - **Gradiente costante** $g$ (si va sempre nella stessa direzione): l'aggiornamento converge a $-\dfrac{\eta g}{1-\mu}=-10,\eta g$ per $\mu=0.9$ → passi fino a **10 volte più lunghi**.
> - **Gradiente che si inverte** a ogni passo (zig-zag): i contributi con segno opposto si **compensano** → oscillazioni smorzate.

---

### Learning rate adattativo (altri ottimizzatori)

Oltre a Momentum esistono altri **optimizers** per l'aggiornamento dei pesi (vedi _Faster Optimizers_ in [A. Géron]):

- Nesterov Accelerated Gradient (**NAG**)
- Adaptive Gradient (**Adagrad**)
- **Adadelta**
- **RMSProp**
- **Adam** (e **AdamW**)

**Idea comune:** adattare $\eta$ **a ogni singolo parametro**: passi più lunghi lungo le direzioni (parametri) in cui il gradiente varia poco. Per farlo si mantengono **statistiche per parametro** sulla variazione del gradiente (costo in memoria).

> [!info] Quale usare **Adam** è considerato lo stato dell'arte, ma **non sempre** dà risultati migliori del classico **SGD con Momentum**. Non c'è una scelta universale: dipende dal problema.

---

### La rete non converge: check-list

Cose da verificare quando la loss sul training set **non parte** (la rete sembra bloccata). Ogni punto rimanda a una sezione:

> [!todo] Check-list
> 
> - [ ] **Input normalizzati** (min-max, standard scaling)? → _Setup per la classificazione_
> - [ ] **Pesi inizializzati** random in range ragionevoli (Xavier, He)? Altrimenti si può essere in **zona di saturazione** da cui non si esce → _Inizializzazione_
> - [ ] Pattern delle diverse classi **mescolati random** nei minibatch? → _SGD_
> - [ ] **Etichette** nel formato giusto (interi sparsi, one-hot, float per la regressione)?
> - [ ] **Loss** corretta e adeguata al tipo di label (attenzione a label sparse vs vettori one-hot)? → _Output e Loss_
> - [ ] **Attivazione finale** corretta: sigmoid per binaria, softmax per cross-entropy multiclasse, nessuna per regressione? → _Output e Loss_
> - [ ] **Learning rate** adeguato? → _Learning rate_

---