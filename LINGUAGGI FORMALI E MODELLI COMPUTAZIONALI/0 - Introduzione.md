## Origine della linguistica formale

I linguaggi naturali e i linguaggi di programmazione condividono una struttura basata su **lessico** e **sintassi**. 
- il **lessico** è l'insieme di tutte le parole che formano una lingua
- la **sintassi** è l'insieme delle regole che legano le parole tra loro per costruire frasi

>[!abstract] Pillole di storia
>Lo studio dei linguaggi naturali è iniziato negli anni '50 con Noam Chomsky, che ha introdotto le grammatiche a struttura di frase e la relativa classificazione, ponendo le basi della teoria matematica dei linguaggi.
>
>  Dagli anni '60 questi concetti sono stati applicati ai linguaggi di programmazione per descrivere formalmente e in modo rigoroso le procedure di risoluzione dei problemi. 

## Compilazione e parsing automatizzato

Oggi si utilizzano approcci dichiarativi per generare automaticamente il codice dei parser a partire da specifiche formali:
- **Lessico**: definito mediante espressioni regolari o automi a stati finiti (FSA).
- **Sintassi**: definita mediante grammatiche (es. EBNF) o automi a pila.
    
Il parser riceve in input il codice sorgente di un programma e genera:
- Un elenco di eventuali errori lessicali o sintattici.
- L'albero sintattico del programma, se il codice risulta privo di errori.
![[Pasted image 20260922170945.png|493]]
## Modellazione del linguaggio naturale e Language Model (LM)

> [!attention] Problema
> I linguaggi naturali presentano problemi di ambiguità: una singola frase corretta può generare più alberi sintattici distinti con significati differenti.

> [!important] Soluzione
> Per risolvere l'ambiguità si impiegano le **grammatiche probabilistiche**, che permettono di determinare l'albero sintattico più probabile per una determinata sequenza.

> [!abstract] I **Language Model (LM)** 
> Gli LM sono una sottoclasse dei modelli generativi probabilistici che rappresentano in modo finito una distribuzione di probabilità su un data space costituito da sequenze di parole o token.

### Evoluzione dei modelli di linguaggio

1. **Approccio probabilistico classico**: comprende grammatiche probabilistiche, modelli Bag of Words (n-grammi) e Hidden Markov Model (HMM - automi a stati finiti probabilistici). In questi modelli la conoscenza linguistica viene rappresentata in modo esplicito, mentre i parametri distribuzionali vengono appresi dai dati.
    
2. **Approccio neurale moderno**: include Recurrent Neural Networks (RNN), Transformer e Large Language Models (LLM). In questo paradigma le regolarità lessicali e sintattiche emergono implicitamente durante la fase di addestramento sui dati.
    
3. **Frontiera neuro-simbolica**: combina i due approcci, ad esempio tramite grammatiche probabilistiche neurali.
    

## Modelli computazionali

I modelli computazionali si dividono in due macro-categorie:

- **Generative Model** (inclusi i LM): permettono di effettuare apprendimento (supervisionato/non supervisionato), inferenza/classificazione (come il parsing) e generazione di dati tramite campionamento delle distribuzioni.
    
- **Discriminative Model**: focalizzati esclusivamente sulla classificazione senza generazione di dati (es. regressione logistica, Convolutional Neural Networks).
    
> _guarda machine learning_
## Logiche formali e verifica di sistemi distribuiti

Le logiche di base (proposizionale e dei predicati) si fondano su sistemi deduttivi costituiti da assiomi e regole di inferenza per ricavare conseguenze logiche.

Estendendo la logica con la dimensione temporale (**Linear Time Logic - LTL**), si ottengono strumenti utili per:
- Rappresentare la conoscenza negli agenti intelligenti.
- Definire la semantica formale dei linguaggi naturali.
- Specificare e verificare proprietà fondamentali dei sistemi distribuiti e concorrenti.
    

### Model Checking e Sistemi Distribuiti

I sistemi distribuiti o concorrenti sono modellati come molteplici programmi interagenti tramite modelli quali:

- Labeled Transition System (LTS)
- Process Algebra
- Petri Net
    
Il **Model Checking** è un procedimento algoritmico che verifica se un modello di sistema soddisfa una determinata proprietà espressa in logica temporale (es. assenza di deadlock, proprietà di safety, liveness o fairness). Se una proprietà non è soddisfatta, il model checker genera un controesempio che evidenzia l'esecuzione errata.
