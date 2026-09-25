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



