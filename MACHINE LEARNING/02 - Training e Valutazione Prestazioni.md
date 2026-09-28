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

## Metriche per Problemi di Regressione

### RMSE (Root Mean Squared Error)

È la metrica standard usata nei problemi di regressione per valutare lo scostamento continuo tra le stime e i valori reali.

- Si calcola eseguendo la radice quadrata della media dei quadrati delle differenze tra i valori predetti ($pred_i$) e i valori reali ($true_i$).$$\text{RMSE} = \sqrt{\frac{1}{N}\sum_{i=1}^{N}(pred_i - true_i)^2}$$

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

Assumiamo che sulle righe ci siano le classi `True` (le classi effettive) e sulle colonne le classi `Predicted` (predette dal modello) 

Una cella $(r,c)$ riporta il numero di casi in cui il sistema ha predetto di classe $c$ un pattern di classe vera $r$.
![[Pasted image 20260925144129.png|392]]
_Idealmente la matrice dovrebbe essere diagonale._

> [!attention] La matrice può essere normalizzata per righe (classi True) per osservare la percentuale di errore
> ![[Pasted image 20260925145611.png]]

## Metriche per la Classificazione Binaria

> [!abstract] Distinguiamo 4 tipi di risultati:
>- **True Positive (TP)**: pattern positivo correttamente assegnato ai positivi
>- **True Negative (TN)**: pattern negativo correttamente assegnato ai negativi
>- **False Positive (FP)**: pattern negativo erroneamente assegnato ai positivi. 
>	- Detto anche errore di `Tipo 1` o `False`
>- **False Negative (FN)**: pattern positivo erroneamente assegnato ai negativi
>	- Detto anche errore di `Tipo 2` o `True


Dato un **classificatore binario** e $T = P + N$ pattern da classificare con (P positivi e N negativi) possiamo anche calcolare
$$TPR(True Positive Rate) = \frac{TP}{P}$$
$$TNR(True Negative Rate) = \frac{TN}{N}$$
$$FPR(False Positive Rate) = \frac{FP}{N}$$
$$FNR(False Negative Rate) = \frac{FN}{P}$$
Con questa notazione l’accuratezza di classificazione può essere scritta come:
$$Accuracy = \frac{TP+N}{T} \quad con \quad T = P+N$$
### Errori sulla matrice di confusione
![[Pasted image 20260925151522.png|406]]
### DET e ROC

> [!abstract] Cosa sono DET e ROC?
> Sia la curva **ROC** (_Receiver Operating Characteristic_) sia la curva **DET** (_Detection Error Tradeoff_) sono strumenti grafici usati per valutare e confrontare le prestazioni dei **classificatori binari** al variare della soglia di decisione (_decision threshold_).
> 

> [!hint] L'output di un classificatore è solitamente  **probabilistico**
> vale a dire un valore continuo compreso nell'intervallo $[0,1]$, dunque in particolare nei problemi di classificazione binaria la decisione finale dipende dal confronto con una soglia $t$.
> 
> _es. è un cane al 60%, è un gatto al 30%, soglia $t$ al 50%, dunque **è un cane**_

- **Soglie restrittive (elevate, $t \to 1$):** Riducono i Falsi Positivi ($\text{FPR}$), ma aumentano i Falsi Negativi ($\text{FNR}$).
    
- **Soglie tolleranti (basse, $t \to 0$):** Riducono i Falsi Negativi ($\text{FNR}$), ma aumentano i Falsi Positivi ($\text{FPR}$).
![[Pasted image 20260928093834.png]]_Il grafico in alto mostra l'andamento dei due errori al variare di $t$. Il punto di intersezione tra le due curve ($\text{FPR} = \text{FNR}$) definisce l'**Equal Error Rate (EER)**.

> [!hint] Invece di osservare entrambi gli errori in funzione di $t$, si elimina il parametro $t$ tracciando un tipo di errore direttamente in funzione dell'altro:
> ![[Pasted image 20260928095317.png]]



>**Curva DET (Detection Error Tradeoff):**
> Mostra direttamente il _trade-off_ tra i due tipi di errore. Più la curva si avvicina all'origine $(0,0)$, migliore è il classificatore.
        
> **Curva ROC (Receiver Operating Characteristic):**
>Poiché l'asse $Y$ misura i _True Positives_ ($\text{TPR}$) anziché i _False Negatives_ ($\text{FNR}$), la curva ROC risulta essere **ribaltata verticalmente** rispetto alla curva DET. Più la curva si avvicina all'angolo in alto a sinistra $(0,1)$, migliore è il modello.

> [!danger]  
> se le due curve si intersecano, **nessun modello domina l'altro per tutti i possibili valori di soglia**
> 
> ![[Pasted image 20260928095747.png]]

> [!important] Soluzione
> Per ottenere un confronto sintetico e globale svincolato da una singola soglia, si calcola l'**AUC**, che "media" le prestazioni del sistema su tutti i punti di lavoro:
>
>- **Definizione:** È l'area della superficie compresa sotto la curva ROC, con valori compresi nell'intervallo $[0, 1]$.
  >  
  ![[Pasted image 20260928095721.png]]