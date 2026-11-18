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

