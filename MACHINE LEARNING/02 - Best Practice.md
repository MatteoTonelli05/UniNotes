### Il problema della "Black Box"

I grandi modelli (soprattutto le reti neurali profonde) raggiungono prestazioni elevate, ma la loro complessità rende difficile comprendere **il perché** di una specifica decisione o predizione. La XAI offre strumenti per interpretare il comportamento del modello in diversi contesti applicativi.

### 2. Spiegabilità Globale vs. Locale

- **Spiegabilità Globale (Interpretabilità Nativa):**
    
    - Modelli più semplici (es. **regressione lineare**, **alberi decisionali**) forniscono nativamente parametri o metriche per quantificare l'importanza complessiva delle varie _feature_ per l'intero modello, indipendentemente dal singolo dato.
        
- **Spiegabilità Locale (Post-hoc / Model-independent):**
    
    - Tecniche come **LIME** e **SHAP** analizzano il comportamento del modello attorno a un **singolo data point** in fase di inferenza. Consentono di capire quali specifiche _feature_ di quell'istanza abbiano spinto il modello verso una determinata decisione.
        
