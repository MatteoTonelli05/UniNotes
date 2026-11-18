Con il termine **DNN (Deep Neural Network)** si denotano reti «profonde» composte da molti livelli (**almeno 2 hidden**) organizzati gerarchicamente.

> [!question] Cosa si intende per organizzazione gerarchica?
> L’organizzazione gerarchica consente di condividere e riusare informazioni (un po' come la programmazione strutturata). Lungo la gerarchia è possibile selezionare feature specifiche e scartare dettagli inutili (al fine di massimizzare l’invarianza).

## Livelli di Complessità
### 1.**PROFONDITÀ**

> [!success] Un numero di livelli elevato aumenta le capacità di una rete (seppur leggermente)

> [!fail] Più la profondità aumenta e più la rete diventa lenta
### 2.**PESI**

> [!fail] Maggiore è il numero di **pesi** (ovvero di parametri da apprendere) maggiore è la complessità del training

### 3.NEURONI

> [!fail] Un elevato numero di neuroni (e connessioni) rende forward e back propagation più costosi, poiché aumenta il numero (G-Ops) di operazioni necessarie.

![[Pasted image 20261117175354.png]]

---
## Tipologie di **DNN**

> [!attention] Deep Learning non significa una sola rete
> Ci sono diversi tipi in base alla funzionalità (per immagini, per testo, generative, ecc.)
> Ci concentreremo in particolare su quelle per immagini

![[Pasted image 20261117180348.png|375]]

---
## Ingredienti del Deep Learning

> [!abstract] Se provi a prendere un semplice MLP e ad aggiungere 10 livelli, banalmente non si parla di Deep Learning

Servono 3 ingredienti principali:
1. **BigData**
![[Pasted image 20261117181910.png|466]]
 2. **GPU computing** - Il training di modelli complessi richiede elevate potenze computazionali.

	- **Workstation multi-GPU:** es. 4 GPU collegate direttamente tra loro tramite canali ultraveloci (_NVLink_) per non rallentare lo scambio dati.
	    
	- **Cloud:** noleggio di GPU su server Amazon, Google, ecc.
	    
	- **Supercomputer:** come _Leonardo_ al Cineca, che unisce migliaia di nodi con 4 GPU Nvidia A100 ciascuno.
	
	
3. **Vanishing** (or exploding) **gradient** - la retro propagazione del gradiente (fondamentale per backpropagation) è problematica su reti profonde, servono quindi delle gestioni che ne evitino la scomparsa

### Vanishing 

> [!fail] Il vanishing gradient nasce dalla **regola della catena** nella backpropagation: il gradiente di uno strato iniziale è un **prodotto di molti fattori**, uno per ogni strato attraversato. Se questi fattori sono in media $<1$, il prodotto decade esponenzialmente con la profondità.

Nelle reti profonde, l’utilizzo della **sigmoide** è problematico per la retro propagazione del gradiente (problema del vanishing gradient)

#### Soluzione 1 - Sostituzione funzione di attivazione
##### **Relu** (Rectified Linear)
![[Pasted image 20261118113753.png|608]]
##### **Leaky Relu**

Evita che nella parte negativa il gradiente sia 0, e quindi permette a neuroni «spenti» durante il training di potersi «risvegliare»
![[Pasted image 20261118113940.png]]
##### **ELU** (Exponential Linear Unit)

Oltre a non avere gradiente nullo nella parte negativa, è più *smoothed* vicino allo 0 (no discontinuità per 𝛼 = 1)

![[Pasted image 20261118114045.png]]
#### Soluzione 2 - Dropout

> [!abstract] Idea
> Nel caso di modelli molto grandi addestrati su dataset relativamente piccoli si riconduce all'**overfitting**.

Il dropout è una tecnica di regolarizzazione utile per controllare overfitting.
Durante il training alcuni neuroni sono «spenti» (con una data probabilità, es. 50%), per far sì che l’output non dipenda da specifici neuroni, ma sia determinato in modo più robusto. 
![[Pasted image 20261118121347.png|504]]
> [!hint] In una CNN è solitamente applicato solo ai livelli fully connected nella parte finale della rete (ma non all’output).

---

## ML Frameworks

Scrivere a mano una rete neurale semplice (il solo passaggio in avanti) è fattibile, ma scrivere da zero un sistema completo per reti profonde è estremamente complesso e richiede troppo tempo. Un buon framework deve permettere di:

- Descrivere l'architettura ad alto livello (senza incastrare tutto in codice rigido).
    
- Eseguire il training con backpropagation includendo tutte le varianti moderne (Adam, Dropout, ecc.).
    
- Sfruttare in automatico le schede video (GPU), anche più di una contemporaneamente.
    
Per questo si usano librerie open-source già pronte come **TensorFlow** (Google) e **PyTorch** (Meta/Facebook), mentre altre storiche come **Caffe** sono ormai abbandonate.

### Autodiff (Differenziazione Automatica)

> [!fail] In passato ogni volta che veniva proposto un nuovo modello, era necessario derivare manualmente il gradiente e codificare le corrispondenti equazioni nel software.

**Cosa serve per un nuovo layer.** Se definisci un layer $y = f(x, w)$ che il framework non ha, devi fornire solo i **gradienti locali**:

- $\dfrac{dy}{dx}$: output rispetto all’input
- $\dfrac{dy}{dw}$: output rispetto ai parametri locali

La propagazione e la combinazione con il resto della rete sono automatiche.

**Cosa succede nel backward.** Il layer riceve dall’alto $\frac{dL}{dy}$ (Loss in relazione all'output) e restituisce:

$$\frac{dL}{dx} = \frac{dL}{dy}\cdot\frac{dy}{dx} \qquad\qquad \frac{dL}{dw} = \frac{dL}{dy}\cdot\frac{dy}{dw}$$

Il primo termine va al layer precedente (continua la catena), il secondo serve ad aggiornare i pesi.

> [!abstract] **Tensori e Jacobiane.** 
> Input, output e pesi sono tensori, quindi le derivate parziali sono organizzate in **matrici Jacobiane**. La chain rule diventa un **prodotto di Jacobiane** (con le dovute trasposizioni). Per questo le GPU sono adatte: eseguono bene le moltiplicazioni matrice-matrice (**gemm**).

> [!example] **Esempio**
> layer fully connected senza bias, $\mathbf{y} = W\mathbf{x}$, con $\mathbf{x}\in\mathbb{R}^n$, $\mathbf{y}\in\mathbb{R}^m$, $W\in\mathbb{R}^{m\times n}$:
>
>$$  
\frac{d\mathbf{y}}{d\mathbf{x}} = \left[\frac{dy_i}{dx_j}\right] = [W_{ij}] = W  
\qquad\qquad  
\frac{d\mathbf{y}}{dW_i} = \mathbf{x},\quad i=1..m$$
dove $W_i$ è la riga $i$-esima di $W$. In parole: la derivata rispetto all’input è la matrice dei pesi, quella rispetto ai pesi di un neurone è l’input stesso.

![[Pasted image 20261118130906.png|555]]
### Inference on the Edge

> [!abstract] Idea
> Il **training** è la parte pesante e richiede GPU potenti. L’**inference** è usare una rete già addestrata per classificare un nuovo input: è solo una forward pass, molto più leggera. “On the edge” significa farla direttamente sul dispositivo (telefono, robot, telecamera).

Una volta che la rete è stata addestrata (e i suoi pesi vengono "congelati"), usarla per fare predizioni (**inference**) richiede molta meno potenza di calcolo rispetto al training. Spesso basta una normale CPU, oppure, quando serve velocità in tempo reale su dispositivi a batteria (_Edge_), si usano chip dedicati a basso consumo:

- **Smartphone:** integrano acceleratori neurali chiamati **NPU** (Neural Processing Units, come nei chip Apple Bionic) abbinati a librerie dedicate.
    
- **IoT, Robot e Droni:** usano schede compatte con GPU a basso consumo come **NVIDIA Jetson** o smart camera con chip dedicati (es. Intel Movidius).
    
- **Automotive:** chip progettati su misura per la guida autonoma, come quelli sviluppati da Tesla.

## CNN

> [!fail] Le MLP sono troppo pesanti per analizzare immagini

> [!question] Che **differenze** ci sono rispetto alla MLP?
> 1. **Connessioni locali**: ogni neurone guarda solo un piccolo pezzo del livello precedente, quindi molte meno connessioni.
>2. **Pesi condivisi**: neuroni diversi dello stesso livello usano gli **stessi pesi** su zone diverse dell’immagine, quindi molti meno pesi da apprendere.
>3. **Alternanza di feature extraction e pooling**.
>
>![[Pasted image 20261118145003.png]]

### Architettura CNN

> [!info] Ispirazione biologica
> Hubel e Wiesel (1962) scoprono nella corteccia visiva del gatto due tipi di neuroni:
>
>- **Simple cells**: rilevano feature locali (es. un bordo). Danno **selettività**.
>- **Complex cells**: fondono (pooling) le uscite di più simple cells vicine. Danno **invarianza**: il bordo viene riconosciuto anche se si sposta un po’.

Una CNN è composta da una gerarchia di livelli.
- Il livello di input è direttamente collegato ai pixel dell’immagine
- gli ultimi livelli sono generalmente fully-connected e operano come un classificatore MLP
- nei livelli intermedi si utilizzano connessioni locali e pesi condivisi.
![[Pasted image 20261118145221.png|594]] 
> [!important] 
> - Più si va avanti nella rete più il livello analizza un dettaglio più ristretto.
> - Le connessioni locali e condivise fanno si che i neuroni processino nello stesso modo porzioni diverse dell'immagine
>  
>  ![[Pasted image 20261118145715.png]]

### Convoluzione

Un filtro digitale (una piccola maschera 2D di pesi) è fatto scorrere sulle diverse posizioni di input; per ogni posizione viene generato un valore di output, eseguendo il prodotto scalare tra la maschera e la porzione dell’input coperta (entrambi trattati come vettori).

![[Pasted image 20261118145851.png|462]]
> [!question] Perchè è in questa sezione?
> Perchè i neuroni si comportano praticamente come un filtro di convoluzione
> 
> ![[Pasted image 20261118150051.png|443]]

### Volumi di una CNN

Le CNN lavorano in **3D**:
- sui piani **width - height** si conserva l’organizzazione spaziale dell'immagine in input
- su **depth** si individuano le **feature map**

> [!hint] Un’immagine a colori non è una griglia piatta: ha **3 canali** (R, G, B), da qui proviene la matrice 3D.

![[Pasted image 20261118150340.png|505]]
#### **Convoluzione 3D**

Il filtro in questo caso deve avere la stessa **depth** dell'immagine input, ogni filtro genererà quindi una matrice bidimensionale di dimensione:
$$W_{out}=\frac{(W_{in}-F+2\cdot Padding)}{Stride}+1$$
In cui 
- $W_{out}$ è la dimensione (orizzontale o verticale) della feature map di output 
- $W_{in}$ la corrispondente dimensione nell'input
- $F$ la stessa dimensione ma nel filtro
- $Stride$ è il passo con cui il filtro si sposta, di default 1
- $Padding$ indica lo spessore (in pixel) del bordo
![[Pasted image 20261118152152.png]]
> [!question] Lo spessore della matrice in uscita da cosa deriva?
> Solo ed esclusivamente dal numero di filtri utilizzati.

### Pooling

Il pooling **riduce la dimensione** delle feature map aggregando piccole zone in un solo valore. 
Fondendo le risposte di più simple cells vicine, danno così **invarianza**.

> [!question] A cosa serve?
> Dopo una convoluzione, la feature map può indicare la posizione. Ma se nell’immagine quel bordo si sposta di un pixel, la mappa cambia leggermente. Il pooling, riducendo la matrice, tiene l’informazione “in questa zona c’è un bordo” senza badare alla posizione esatta. 
> 
> **Così la rete diventa robusta a piccole traslazioni, conservando le informazioni e semplificando i calcoli.**

![[Pasted image 20261118154816.png|477]]