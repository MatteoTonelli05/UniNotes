# Excursus Storico e Applicativo dell'AI 

## Il Test di Turing 

- **Turing Test (1950):** Test ideato da Alan Turing per stabilire se una macchina è "intelligente". Un interrogante ($C$) interagisce via testo con un uomo e un calcolatore; se non riesce a distinguerli, la macchina supera il test.
    
- **Stato attuale & Critiche:** Superato formalmente dai modelli più recenti (es. GPT-4.5). Oggi è considerato inadeguato: accedere a immensi volumi di dati permette di rispondere in modo sensato senza una vera comprensione o intelligenza sottostante.
    
## I Nuovi Benchmark

Per misurare le reali capacità di _reasoning_ e intelligenza generale (AGI) dei Large Language Models (LLM) si usano benchmark complessi:

- **BIG-bench (2022):** Oltre 200 task di logica, matematica, codice e comprensione del mondo.
    
- **GPQA (Google-Proof Q&A, 2023):** Domande di fisica, chimica e biologia a livello PhD, non risolvibili con una semplice ricerca web.
    
- **HLE (Humanity's Last Exam, 2025):** 2500 problemi estremamente complessi ideati per testare i limiti dei modelli più avanzati.
    
## AI e Giochi: Dagli Scacchi a Deep Blue 

> [!attention] **Alberi di Gioco e Minimax:** 
> Nei giochi di strategia ogni stato ha un punteggio. L'algoritmo _Minimax_ valuta le mosse future assumendo un avversario ottimale. Poiché l'albero di ricerca esplode combinatoriamente, si usano euristiche e _Alpha-Beta pruning_ per tagliare i rami inutili.
    
- **Deep Blue vs Kasparov (1997, Slide 14):** Storica vittoria di IBM contro il campione di scacchi. Basato su hardware dedicato (valutava 200 milioni di posizioni al secondo) unitamente a funzioni di valutazione ottimizzate sui dati di partite reali.


- **Watson in Jeopardy! (IBM, 2011):** Sistema in grado di vincere al celebre quiz TV elaborando il linguaggio naturale in modalità _Open-Domain Question Answering_.Accesso in tempo reale in RAM a 200 milioni di pagine di conoscenza (Wikipedia, dizionari, ontologie) per estrarre e valutare risposte in pochi secondi.


- **AlphaGo vs Lee Sedol (Google DeepMind, 2016 - Slide 16):** Il gioco del Go ha un fattore di ramificazione enorme (361). AlphaGo ha combinato _Monte Carlo Tree Search_ (MCTS) con reti neurali profonde, prima imitando i professionisti e poi giocando milioni di partite contro sé stesso via _Reinforcement Learning_.

## Ere dell'AI

# Cronologia e Stagioni dell'AI

# Cronologia e Stagioni dell'AI

| Periodo         | Fase                     | Tecnologia Chiave                                        | Cosa è successo & Perché                                                                                                                                                       | Traguardi e Note                                                                      |
| :-------------- | :----------------------- | :------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------ |
| **1940 – 1974** | **Nascita & Anni d'Oro** | AI Simbolica, Logic Reasoning, primi Neuroni Artificiali | **Nascita della disciplina:** forte entusiasmo per i primi programmi logici e stime troppo ottimistiche sul raggiungere l'intelligenza umana in pochi anni.                    | Test di Turing, Dartmouth Workshop (1956) inventa l'AI, GPS, ELIZA                    |
| **1974 – 1980** | **1° Inverno dell'AI**   | Stallo computazionale e teorico                          | **Crollo e tagli ai fondi:** le promesse non vengono mantenute a causa dei limiti di calcolo dell'hardware, mancanza di dati ed esplosione combinatoria.                       | Abbandono temporaneo dell'approccio connessionista (reti neurali)                     |
| **1980 – 1987** | **Nuova Primavera**      | Sistemi Esperti, Backpropagation                         | **L'AI entra nell'industria:** si diffondono i programmi basati su regole per le aziende e si sbloccano le reti neurali grazie all'algoritmo di retropropagazione dell'errore. | Regole logiche a dominio, algoritmo Backpropagation (Hinton, 1986)                    |
| **1987 – 1993** | **2° Inverno dell'AI**   | Flop "Quinta Generazione"                                | **Nuovo stop ai finanziamenti:** i Sistemi Esperti si rivelano troppo rigidi e costosi da aggiornare; i PC tradizionali superano l'hardware AI dedicato.                       | Fallimento commerciale delle macchine Lisp e dell'hardware dedicato                   |
| **1993 – 2011** | **Tempi Moderni**        | ML Statistico, Feature Hand-crafted, Reti Bayesiane      | **Rifondazione matematica e pragmatica:** abbandonata l'idea di AGI immediata, ci si concentra su compiti specifici usando modelli matematici e statistici robusti.            | Support Vector Machines (SVM), Random Forest, **Deep Blue** (1997), **Watson** (2011) |
| **2011 – 2018** | **Deep Learning**        | Reti Neurali Profonde (CNN), Reinforcement Learning      | **Rivoluzione della Computer Vision:** si sbloccano i due ingredienti chiave (**Big Data** e **GPU**), superando l'uomo nel riconoscimento di pattern.                         | **AlexNet** (2012 su ImageNet), **AlphaGo** (2016), Speech & Translation              |
| **2018 – Oggi** | **Generative AI**        | Large Language Models (LLM), Transformer                 | **Passaggio a modelli generativi "general-purpose":** reti addestrate su scala globale mostrano capacità emergenti di *reasoning*, traduzione e codice.                        | GPT-3/4, Gemini, Claude                                                               |

> [!TIP] Sintesi per l'esame
> - **Inverni:** Promesse irrealistiche + Hardware limitato + Mancanza di dati.
> - **Svolta moderna:** Cambio di paradigma dal *scrivere le regole a mano* (AI Simbolica) all'*estrarre i pattern dai dati* (AI Subsimbolica) grazie a **Big Data + GPU**.
