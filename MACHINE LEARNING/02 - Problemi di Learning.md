## Problemi di Learning
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
