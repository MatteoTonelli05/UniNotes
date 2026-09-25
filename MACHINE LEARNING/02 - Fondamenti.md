# Dati

> [!quote] I dati sono il nostro carburante per i modelli **non pre-programmati**

Generalmente un campione (multi-dimensionale) nel dominio di interesse è definito **Data-point**. _(utilizzeremo il termine **Pattern** per indicare i Data-point)_

> [!important] Pattern Recognition
> è la disciplina che studia il riconoscimento dei pattern (non solo con tecniche di learning ma anche con algoritmi pre-programmati).

# Tipologie di dati

> [!abstract] Panoramica dei Tipi di Pattern
> I dati possono presentarsi in diverse forme. La scelta della rappresentazione influisce sul tipo di modello da utilizzare.

### 1. Pattern Numerici
- **Descrizione:** Caratteristiche misurabili o conteggi quantitativi.
- **Natura dei dati:** Continui o discreti, sempre soggetti a **ordinamento**.
- **Rappresentazione:** Vettori numerici multidimensionali (*Feature Vectors*).
- **Esempio:** Persona $\rightarrow$ `[altezza_cm, circonferenza_torace, lunghezza_piede]`
### 2. Pattern Categorici
- **Descrizione:** Attributi qualitative o presenza/assenza di una caratteristica (yes/no).
- **Natura dei dati:** Qualitativi (es. colore) o **ordinali** se esiste una gerarchia (es. *alta, media, bassa*).
- **Gestione:**
  - Supportati nativamente da **alberi decisionali** e **sistemi a regole**.
  - Per modelli numerici richiedono **encoding** (es. *One-Hot Encoding*) o **embedding**.
- **Esempio:** Persona $\rightarrow$ `[sesso, maggiorenne (yes/no), colore_occhi, gruppo_sanguigno]`
### 3. Sequenze
- **Descrizione:** Dati con relazioni spaziali o temporali intrinseche.
- **Natura dei dati:** Spesso a **lunghezza variabile**; la posizione ed il contesto (passato/futuro) sono fondamentali.
- **Gestione:** Modelli dotati di memoria o meccanismi di attenzione (**LSTM**, **Transformers**).
- **Esempi:** Frasi in linguaggio naturale, audio, video, serie temporali finanziarie.
### 4. Altri Dati Strutturati
- **Descrizione:** Dati organizzati in strutture relazionali complesse.
- **Natura dei dati:** Strutture ad **albero** o a **grafo**.
- **Gestione:** Algoritmi dedicati come **HMM**, **Reti Bayesiane**, **Graph Neural Networks (GNN)**.
- **Esempi:** *Parse tree* nella traduzione del linguaggio naturale, grafi molecolari, sequenze di DNA.

> [!attention] Dati Tabulari (Eterogenei)
> Tipo di organizzazione dati estremamente diffuso in ambito aziendale, gestito tipicamente tramite tabelle (modelli relazionali DBMS).
> - **Struttura:** Dati organizzati in righe (record / data-points) e colonne (attributi / features).
>- **Eterogeneità:** Le colonne contengono tipi di dati diversi (es. età come valore numerico, città di residenza come valore categorico, salario come continuo).
>- **Incompletezza e Sbilanciamento:** Spesso presentano valori mancanti (`null`) e classi fortemente sbilanciate.
>- **Metrica di Similarità:** Risulta difficile definire una funzione di distanza/similarità univoca tra i record a causa della diversità dei tipi di dato.
>- **Modelli consigliati:** 
	> 	 - I modelli di Deep Learning eccellono su dati omogenei non strutturati (immagini, audio, testo), ma **non sono (ancora) altrettanto competitivi sui dati tabulari**.
	  >	- I **multi-classificatori basati su alberi** (es. *Random Forest*, *Gradient Boosting*) rimangono la scelta più performante ed efficace per dati tabulari.

---
### Tecniche di Encoding Principali

- **Ordinal Encoding**
	  - **Come funziona:** Assegna un numero progressivo alle categorie ($1, 2, 3...$).
	  - **Vantaggi:** Mantiene una sola colonna numerica ed è ben tollerato dai modelli ad albero.
	  - **Svantaggi:** Può creare finti legami di similarità tra valori vicini se l'ordine è arbitrario.

- **One-Hot Encoding**
	  - **Come funziona:** Si rimuove il campo e al suo posto si aggiungono tanti campi (o colonne) quanti sono i valori distinti (5 in questo caso). Ciascun campo è associato a un materiale diverso e può assume solo valore 0/1, dove 1 indica che il pattern è di quel materiale.
	  - **Vantaggi:** Non introduce alcun ordine arbitrario tra le categorie.
	  - **Svantaggi:** Rischio di esplosione delle dimensioni se ci sono troppe categorie.


# Problemi di Learning

### Classificazione (o riconoscimento)

> Assegnare una classe (**categoria discreta**) a un pattern.

Può essere di due tipologie principali:
- binaria, ovvero su 2 classi
- multi-classe, se più di 2 classi
![[Pasted image 20260921134344.png|362]]
### Regressione

> assegnare un **valore continuo** a un pattern
> (_predizione di valori continui_)

![[Pasted image 20260921134444.png]]
### Clustering

> individuare **gruppi** (cluster) di pattern con caratteristiche simili.

**Caratteristiche**:
- le classi del problema non sono note e i pattern non etichettati
- spesso nemmeno il numero di cluster

![[Pasted image 20260921134609.png|388]]
### Riduzione Dimensionalità

> ridurre il numero di dimensioni dei pattern in input.
> _Consiste nell'apprendimento di un mapping da $R^d$ a $R^k$ con $k < d$_

![[Pasted image 20260921134949.png|383]]
> [!attention] L’operazione comporta una perdita di informazione. L’obiettivo è conservare le informazioni «**importanti**»

### Representation Learning (o feature learning)

> Sostituire l'estrazione manuale delle feature (**_hand-crafted features_**) con l'apprendimento automatico delle caratteristiche a partire dai dati grezzi (**_raw data_**)

![[Pasted image 20260921135432.png|531]]
> [!important] Caso Studio: Applicazioni nel dominio della Visione
> ![[Pasted image 20260921140051.png]]
> 1. **Classification:** Determina la classe dell'oggetto principale
> 2. **Localization:** Identifica la classe dell'oggetto e ne disegna la *bounding box*
> 3. **Object Detection:** Individua e localizza più oggetti contemporaneamente
> 4. **Instance Segmentation:** Sostituisce il bounding box delineando la forma esatta


# Modelli Discriminativi vs Generativi

> [!abstract] Tutti i problemi di Machine Learning appena letti sono trattati come sotto-problemi nel contesto AI

### Modelli Discriminativi

> I modelli **discriminativi** (classificatori) hanno l’obiettivo di assegnare un nuovo datapoint a una classe. La cosa importante è apprendere il **decision boundary** che separa le classi

![[Pasted image 20260921140520.png]]

### Modelli Generativi

> I modelli **generativi** apprendono (esplicitamente/implicitamente) la distribuzione probabilistica degli esempi usati per il loro addestramento. Dopo l'addestramento dunque possono:

![[Pasted image 20260921140550.png]]
> [!attention] il termine **generativo** NON è uguale a **creativo**, poichè generativo può significare anche interpolazione.

# Tipi di Apprendimento

## In base alle etichette

> [!abstract] Come sono fatti i dati che diamo al modello?

- **Supervisionato:** sono note le classi dei pattern utilizzati per l'addestramento 
	(*training set etichettato*)

- **Non Supervisionato:** NON sono note le classi dei pattern utilizzati per l’addestramento (*training set non etichettato*)

- **Semi-Supervisionato:** il *training set* è etichettato parzialmente

> [!attention] la distribuzione dei pattern non etichettati può aiutare a ottimizzare la regola di classificazione.
> ![[Pasted image 20260921142613.png]]

## In base alle tempistiche

> [!abstract] Quando e quante volte apprende il modello?

- **Batch:** l’addestramento è effettuato **una sola volta** su un training set dato, dopodichè il modello è in _working mode_

- **Incrementale:** seguito dell’addestramento iniziale, sono possibili ulteriori sessioni di addestramento.

- **Naturale:** addesstramento continuo anche durante la _working mode_

> [!attention] Nell'esempio di un addestramento Incrementale si rischia il **Catastrofic Forgetting**, una situazione in cui il sistema dimentica quello che ha appreso in precedenza

## Reinforcement Learning (RL)

> [!attention] A differenza dei tipi di apprendimento già elencati precedentemente, il RL non c'è un dataset statico.

> Un agente esegue azioni che modificano l’ambiente, provocando passaggi da uno stato all’altro. Quando l’agente ottiene risultati positivi riceve una ricompensa (**reward**) che però può essere temporalmente ritardata rispetto all’azione, o alla sequenza di azioni, che l’hanno determinata.

![[Pasted image 20260921150539.png|358]]
# Parametri, Iperparametri e Funzione Obiettivo

In generale, il comportamento di un modello $M$ di machine learning è regolato da un set di parametri $\theta$. Dunque per rendere esplicita la dipendenta definiamo:
- il modello come $M(\theta)$
- il valore ottimo dei parametri come $\theta^*$

> [!attention] L'obiettivo dell'apprendimento consiste nel determinare  $\theta^*$

## Funzione Obiettivo

La **Funzione Obiettivo** $f(\text{Train}, M(\Theta))$ misura matematicamente la qualità delle prestazioni del modello sui dati di addestramento. 
Si presenta in due forme principali:

- **Misura di Ottimalità (da Massimizzare):** Rappresenta il punteggio o l'accuratezza del modello. Si cerca la configurazione di parametri che massimizza tale valore: $$\Theta^* = \arg\max_{\Theta} f(\text{Train}, M(\Theta))$$
- **Misura di Errore o Loss Function (da Minimizzare):** Rappresenta il costo o lo scostamento tra la predizione del modello e la realtà. Si cerca la configurazione di parametri che minimizza l'errore: $$\Theta^* = \arg\min_{\Theta} f(\text{Train}, M(\Theta))$$
---
### Modalità di Ottimizzazione
Per determinare i parametri ottimi $\Theta^*$, la funzione obiettivo può essere ottimizzata secondo due approcci di ottimizzazione:

- **Esplicita:** con metodi che operano a partire dalla sua definizione matematica.

- **Implicita:** utilizzando euristici che modificano i parametri in modo coerente con $f$

---
## Iperparametri

### Esempi Comuni di Iperparametri
- **Reti Neurali:** Numero di layer nascosti, numero di neuroni per layer, tasso di apprendimento (*learning rate*).
- **Classificatore $k$-NN:** Il numero $k$ di vicini da considerare per la decisione.
- **Regressione Polinomiale:** Il grado del polinomio utilizzato per approssimare i dati.
- **Modelli in Generale:** Il tipo di *Loss Function* adottata.

> [!question] Cosa cambia dai parametri normali?
> - **Parametri ($\Theta$):** Vengono appresi e aggiornati **automaticamente** dall'algoritmo durante l'addestramento sui dati.
>- **Iperparametri ($H$):** Vengono impostati **manualmente dal progettista PRIMA** che l'addestramento abbia inizio, e servono per deinire l'architettura e l'addestramento del modello.

 _Il modello può essere indicato da $M(H, \Theta)$ per indicare la dipendenza sia dagli iperparametri che dai parametri._

### Selezione Automatica Iperparametri

Esistono diversi algoritmi per automatizzare la selezione degli iperparametri, tra cui:
1. **Grid Search:** 
	- Si definisce una griglia discreta di valori per ciascun iperparametro. 
	- Il sistema valuta in modo esaustivo **tutte le combinazioni possibili**. 
	- È una tecnica accurata ma ad **alto costo computazionale** (specialmente se combinata con la Cross-Validation).  
>In Scikit-Learn si implementa tramite `GridSearchCV`.

2. **Random Search:** 
	- sorteggia causalmente valori di iperparametri dai range/distribuzioni specificati/e, eseguendo un numero prefissato di iterazioni.
>Funzione RandomizedSearchCV di Scikit-Learn

---
### Architettura dell'Addestramento a Due Livelli
Per identificare sia la combinazione ottima di iperparametri ($H^*$) che quella dei parametri ($\Theta^*$), si adotta una procedura nidificata:
![[Pasted image 20260921153842.png|300]]
1. **Livello Esterno (Scelta Iperparametri):**
	   - Si fissa una combinazione specifica di iperparametri $H$.
2. **Livello Interno (Addestramento e Valutazione):**
	   - Si addestra il modello sul **Training Set** trovando i parametri ottimi $\Theta^*$ relativi a quel valore di $H$.
	   - Si valuta la bontà della soluzione calcolando le prestazioni su un **Validation Set** disgiunto.
3. **Selezione Finale:**
	   - Si confrontano i risultati su Validation Set ottenuti con diverse combinazioni e si selezionano gli iperparametri $H^*$ che hanno restituito le prestazioni migliori.

### Auto-ML

>[!abstract] Aggiunge un ulteriore "ciclo esterno"
> Il ciclo itera su vari modelli per scegliere il migliore per la risoluzione del problema
> 
> ![[Pasted image 20260921164025.png]]

---
# I Tre insiemi di Dati

> [!important] Durante le spiegazioni abbiamo parlato di 3 insiemi:
> - **Training Set:** Utilizzato esclusivamente dall'algoritmo per calcolare e aggiornare i parametri del modello. 
> - **Validation Set:** Utilizzato dal progettista per valutare diverse configurazioni e selezionare gli iperparametri ottimali. 
> - **Test Set:** Riservato unicamente alla valutazione finale delle prestazioni su dati mai visti durante lo sviluppo.

## Metodi di suddivisione

### Set Disgiunti

> [!attention] Utilizzabile solo se i dati sono sufficientemente numerosi per non produrre _overfitting_

**Caratteristiche principale:** 
- Un *Training Set* più ampio consente un addestramento migliore. 
- *Validation* e *Test Set* devono comunque mantenere dimensioni sufficienti a garantire significatività statistica sulle misurazioni dell'errore. 

Lo split può essere random (**shuffling**) oppure mirato (es. per mantenere separati periodi temporali di train e test).

> [!question] Perchè non si può usare lo shuffling per serie temporali?
> Nel caso di serie temporali, dove il sistema è addestrato su un periodo storico e produrre previsioni per un periodo successivo, uno split random non sarebbe corretto in quanto il training set includerebbe data point temporalmente vicini (molto simili) a quelli di test

> funzione `train_test_split` di Scikit Learn 
### K-fold Cross-Validation

> [!attention] Utilizzabile quando l'insieme di dati è di dimensioni ridotte, *in generale raggiunge una scelta più robusta degli iperparametri

**Come funziona?**
1. Si accantona inizialmente il Test Set
2. Si suddividono i pattern (**i dati**) rimanenti in **$K$ fold**
3. Per ogni **combinazione di iperparametri $H_i$ che si vuole valutare:**
	- Si esegue $K$ volte il training scegliendo uno dei fold come *Validation Set* e i rimanenti $K-1$ come *Train Set*
	- Si calcola l'accuratezza $avg\_acc_i$ come media delle $K$ accuratezze
- Si sceglie la combinazione di iperparametri con migliore $avg\_acc$ 
- Scelti gli iperparametri ottimali si riaddestra il modello su tutto il training set ($K$ fold) e, solo a questo punto, si verificano le prestazioni sul test set.
![[Pasted image 20260921154735.png]]
**Leave-One-Out:** Caso limite della Cross-Validation in cui $K$ equivale al numero totale dei campioni ($N$). Si applica solo con dataset estremamente piccoli ($N < 100$) a causa dell'alto costo computazionale.

> funzione `cross_val_score` di Scikit Learn 
---

> [!abstract] Cherry Picking
> Usare il *test set* per orientare le scelte durante lo sviluppo di un sistema è a forte rischio di overfitting (crea modelli capaci solo su quel set).

---
# Metriche per la misura di prestazioni

> [!question] Differenze tra funzione obiettivo e metriche di misura?
> - La funzione obiettivo viene usata durante l'addestramento per ottimizzare i parametri.
> - Per quantificare le prestazioni reali del modello si preferisce utilizzare metriche legate direttamente alla semantica dello specifico problema.
## Metriche per Problemi di Classificazione

### Accuratezza ed Errore di classificazione
$$\text{Accuratezza} = \frac{\text{pattern correttamente classificati}}{\text{pattern totali classificati}}$$
$$\text{Errore} = 100\% - \text{Accuratezza}$$

> [!attention] Limiti dell'Accuratezza con Classi Sbilanciate
  >  In un problema di classificazione binaria dove una classe è molto rara, un classificatore "dummy" che non predice mai la classe rara può raggiungere un'accuratezza vicina al 100% pur essendo del tutto inutile.
  >  
  >  **In contesti con classi sbilanciate è necessario valutare le prestazioni tramite metriche diverse come quelle Precision e Recall.**

### Matrice di Confusione

La matrice di confusione (**confusion matrix**) è molto utile nei problemi di classificazione per capire come sono distribuiti gli errori.

![[Pasted image 20260925144129.png|392]]

## Metriche per Problemi di Regressione

### RMSE (Root Mean Squared Error)

È la metrica standard usata nei problemi di regressione per valutare lo scostamento continuo tra le stime e i valori reali.

- Si calcola eseguendo la radice quadrata della media dei quadrati delle differenze tra i valori predetti ($pred_i$) e i valori reali ($true_i$).$$\text{RMSE} = \sqrt{\frac{1}{N}\sum_{i=1}^{N}(pred_i - true_i)^2}$$


