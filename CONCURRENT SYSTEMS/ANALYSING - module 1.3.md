# Analysing Concurrent Systems

## Analisi dei Sistemi: Verification vs Testing
### Definizioni: Fault, Error e Failure
### Testing di Safety e Liveness
### Limiti del Testing nei Sistemi Concorrenti
### Verifica Formale vs Testing

## Metodi Formali per la Verifica
### Approcci Principali: Model Checking e Prove Induttive
### Il Principio del Model-Checking
### Applicazioni del Model-Checking (Hardware e Software)
### Il Problema dell'Esplosione dello Spazio degli Stati (State-Space Explosion)

## Specificare le Proprietà dei Sistemi
### Proprietà di Safety e Proprietà di Liveness
### Specificare Proprietà in FSP

## Supporto Ad-Hoc in FSP
### Property Processes (Safety)
#### Funzionamento, Stato ERROR e Requisito di Determinismo
### Progress Properties (Liveness)
#### Sintassi `progress` e Assenza di Starvation
#### Esempio di Violazione del Progress

## Supporto Logico Generale: Fluents e LTL
### Introduzione alle Logiche Temporali
### Linear Temporal Logic (LTL) e Operatori di Base
#### Operatore Always (`[]` / `G`)
#### Operatore Eventually (`<>` / `F`)
### Proprietà Fondamentali e Deduzione Temporale

## Fluents in FSP
### Definizione e Sintassi dei Fluents
### Families of Fluents (Indicizzati)
### Action Fluents
### Espressioni Fluent e Operatori Logici
### Espressioni `forall` ed `exists`

## Model Checking in FSP tramite FLTL (Fluent LTL)
### Caso di Studio: Il Problema della Sezione Critica (Critical Section)
### Verifica della Safety (Mutua Esclusione con `[]`)
### Individuazione delle Violazioni con LTSA
### Risoluzione del Problema tramite Lock
### Verifica della Liveness (Assenza di Starvation con `<>`)

## Altri Operatori della Fluent Linear Temporal Logic (FLTL)
### Operatore Until (`U`)
#### Specificare Safety e Liveness con `Until`
#### Mutex tramite `Until`
### Operatore Weak Until (`W`)
### Equivalenze Algebriche tra Operatori Temporali
### Espressioni "Rigid" (`rigid(1)` e `rigid(0)`)