## Il problema della "Black Box"

I grandi modelli (soprattutto le reti neurali profonde) raggiungono prestazioni elevate, ma la loro complessità rende difficile comprendere **il perché** di una specifica decisione o predizione. La XAI offre strumenti per interpretare il comportamento del modello in diversi contesti applicativi.

- **Spiegabilità Globale (Interpretabilità Nativa):**
    - Modelli più semplici (es. **regressione lineare**, **alberi decisionali**) forniscono nativamente parametri o metriche per quantificare l'importanza complessiva delle varie _feature_ per l'intero modello, indipendentemente dal singolo dato.
        
- **Spiegabilità Locale (Post-hoc / Model-independent):**
    - Tecniche come **LIME** e **SHAP** analizzano il comportamento del modello attorno a un **singolo data point** in fase di inferenza. Consentono di capire quali specifiche _feature_ di quell'istanza abbiano spinto il modello verso una determinata decisione.
        

## Best Practice

### Dati e Reperimento

- **Disponibilità dei dati:** Evitare di applicare il Machine Learning se non si dispone di un numero sufficiente di dati per il training e il test.
    
- **Costo dell'etichettatura:** La raccolta e l'annotazione dei dati richiedono uno sforzo ingente. È possibile velocizzare il processo cercando dataset online o utilizzando piattaforme di _crowdsourcing_ (es. Amazon Mechanical Turk).
    

### Gestione del Dataset e Validazione

- **Rappresentatività:** Raccogliere dati reali e rappresentativi del problema senza scartare a priori i casi difficili o "scomodi".
    
- **Divisione corretta:** Suddividere accuratamente i dati in set di _Train_, _Validation_ e _Test_ (preferibilmente usando la **Cross-Validation**).
    
- **Evitare il Cherry Picking:** Non selezionare manualmente o manipolare il Test Set per far sembrare il modello più performante (_overfitting del test set_).
    

### 3. Valutazione e Confronti

- **Automazione delle pipeline:** Automatizzare fin da subito il codice per la valutazione delle metriche: verrà eseguito centinaia di volte durante gli esperimenti.
    
- **Confronti equi:** Confrontare le prestazioni del modello solo con sistemi addestrati sullo **stesso dataset** e seguendo il **medesimo protocollo di test**.
    
- **Affidabilità statistica:** Se i dataset sono piccoli, valutare gli **intervalli di confidenza** ed eseguire più _run_ indipendenti con diverse condizioni iniziali/seed per verificare la stabilità dei risultati.
    

### 4. Qualità del Codice (Ingegneria del Software)

- **Codice strutturato e testing:** Scrivere codice ordinato e implementare _unit testing_ e _debug_ incrementale.
    
- **Natura probabilistica:** Poiché i modelli di ML sono approssimati e non "esatti", scovare un errore concettuale o un bug nel codice di addestramento può essere estremamente complesso se il progetto non è ben strutturato.
### 2. Come si vince una competizione ML / Kaggle? (Slide 50)

Attraverso le parole del **#1 Kaggler al mondo (2018)**, la slide evidenzia un approccio di lavoro metodico e sistematico:

1. **Comprensione del problema:** Leggere attentamente la descrizione della competizione e dei dati.
    
2. **Ricerca preliminare:** Cercare competizioni Kaggle simili, analizzare le soluzioni passate e studiare la letteratura scientifica per non perdersi lo stato dell'arte.
    
3. **Validation Set stabile:** Analizzare i dati e costruire una Cross-Validation (CV) robusta e affidabile.
    
4. **Engineering & Training:** Pre-processing dei dati, feature engineering e addestramento dei primi modelli.
    
5. **Analisi degli Errori:** Analizzare la distribuzione delle predizioni e studiare i casi critici (_hard examples_).
    
6. **Diversità & Ensembling:** Progettare architetture per risolvere gli errori riscontrati e combinare più modelli in un **Ensemble** (es. multi-classificatori).
    
7. **Iterazione:** Ritornare ai passi precedenti in base a ciò che emerge dalle analisi.
    

### 3. Model vs. Data Optimization: Il paradigma Data-Centric (Slide 51)

Mentre la maggior parte del tempo viene spesso spesa a raffinare le architetture o fare il tuning degli iperparametri, la realtà industriale richiede un cambio di mentalità:

> _"Systematic improvement of data quality on a basic model is better than chasing the state-of-the-art models with low-quality data"_ — **Andrew Ng**

- **Miglioramento della qualità dei dati:**
    
    - **Data Cleaning:** Pulizia del rumore e rimozione degli errori di etichettatura.
        
    - **Error Analysis:** Riconoscere i fallimenti del sistema e raccogliere nuovi dati specifici per quei casi critici.
        
    - **Data Augmentation:** Generazione di dati sintetici o trasformazione di dati esistenti.
        
    - **Auto-Labelling:** Uso di tecniche di etichettatura automatica/incrementale o self-labelling.
        
    - **Tool dedicati:** Sviluppo e utilizzo di strumenti efficienti per gestire l'intero ciclo di vita del dato.
        

### 4. Aspetti Critici con Clienti e nel Mondo Reale (Slide 52)

Quando si lavora a un progetto reale di ML con un cliente o uno stakeholder non tecnico, è essenziale chiarire preventivamente diversi aspetti controintuitivi:

- **Incertezza iniziale:** L'accuratezza finale del sistema **non è nota a priori** prima di aver addestrato e testato il modello sui dati reali.
    
- **Costo e natura iterativa dei dati:** Potrebbe essere necessaria una fase iniziale costosa per la _Data Collection_, seguita da ulteriori raccolte dati in itinere.
    
- **Il divario laboratorio vs. produzione:** Un modello che funziona bene nei test può fallire in ambiente reale a causa di:
    
    - **Data Drift:** Il cambio nella distribuzione dei dati di input nel tempo.
        
    - **Concept Drift:** La variazione della relazione tra input e output nel mondo reale.
        
- **Manutenzione continua:** Le prestazioni possono degraddare nel tempo; occorre monitorare continuamente il sistema e pianificare re-addestramenti periodici.
    
- **Bias ed Etica:** Il modello può ereditare o amplificare pregiudizi presenti nei dati (es. disparità di trattamento per certi gruppi) difficili da individuare prima del rilascio.
    
- **Opacità (Black-Box):** Può essere difficile spiegare al cliente o alle autorità di regolamentazione la motivazione esatta dietro una singola predizione del modello (motivo per cui si usano tecniche di Explainable AI - XAI).