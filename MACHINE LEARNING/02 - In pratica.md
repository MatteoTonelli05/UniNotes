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