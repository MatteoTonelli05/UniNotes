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

### Soluzioni al Vanishing 

Nelle reti profonde, l’utilizzo della **sigmoide** è problematico per la retro propagazione del gradiente (problema del vanishing gradient)

#### **Relu** (Rectified Linear)
![[Pasted image 20261118113753.png|608]]
#### **Leaky Relu**

Evita che nella parte negativa il gradiente sia 0, e quindi permette a neuroni «spenti» durante il training di potersi «risvegliare»
![[Pasted image 20261118113940.png]]
#### **ELU** (Exponential Linear Unit)

Oltre a non avere gradiente nullo nella parte negativa, è più smoothed vicino allo 0 (no discontinuità per 𝛼 = 1)

![[Pasted image 20261118114045.png]]