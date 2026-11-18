## Sequence to Sequence

Le reti feedforward (MLP, CNN) lavorano su **vettori di dimensione fissa** (_one to one_). Molte applicazioni richiedono invece input e/o output come **sequenze di lunghezza variabile**.
![[Pasted image 20261118174958.png]]
> [!example] Esempi
> **One to One** - da vettore immagine a vettore probabilità delle classi *(classificazione)*
> **One to Many** - da vettore immagine a testo *(image captioning)*
> **Many to One** - da  testo a valore continuo *(sentiment analysis)*
> **Many to Many** - da testo a testo *(language translation)*

---
## Reti Ricorrenti (RNN)

![[Pasted image 20261118175716.png]]

Prevedono collegamenti **verso lo stesso livello** (o all'indietro). Ad ogni step $t$ il livello riceve l'input $x_{(t)}$ **e** il suo output precedente $y_{(t-1)}$.

> [!important] Effetto memoria La rete decide in base alla **storia passata**: conta non solo _quali_ elementi ci sono nella sequenza, ma anche la loro **posizione reciproca**.

> [!example] Quando serve una RNN?
> 
> - Frase: non basta la presenza delle parole, conta come sono legate → **RNN utile**
> - Video su animali: basta la detection in qualche frame → **RNN non serve**
> - Linguaggio dei segni: contano i movimenti tra frame → **RNN utile**

### Unfolding in time

Una **cella** è la parte di rete che mantiene uno **stato (memoria) interno** $h_{(t)}$:

$$h_{(t)} = f(h_{(t-1)}, x_{(t)})$$

Per addestrarla con backpropagation si fa l'**unfolding (unrolling)**: si fissa a priori il numero di passi temporali e si "srotola" la rete.

> [!warning] Costo e vanishing gradient Una RNN unfolded su 20 step ≈ una DNN feedforward con **20 livelli** → training costoso e critico per la convergenza (**vanishing gradient**).

> [!tip] Nella rete unfolded I **pesi della cella sono condivisi** tra tutte le istanze (come i pesi condivisi nelle CNN).

### Basic Cell

$$h_{(t)} = \phi\left(x_{(t)}^T \cdot W_x + h_{(t-1)}^T \cdot W_h + b\right), \qquad y_{(t)} = h_{(t)}$$

- $\phi$ = funzione di attivazione (es. ReLU)
- $W_x, W_h, b$ = parametri da apprendere _(nelle slide c'è un refuso: scrive $W_y$ al posto di $W_h$)_

> [!warning] Limite La memoria dei primi input **svanisce**, ma in una frase anche le prime parole possono essere cruciali → servono celle con **memoria a lungo termine**: **LSTM** e **GRU**.

### LSTM (Long Short-Term Memory)

Lo stato è diviso in due vettori:

- $h_{(t)}$ → memoria a **breve** termine (= output $y_{(t)}$)
- $c_{(t)}$ → memoria a **lungo** termine

Tre gate (appresi durante il training):

1. **Forget gate**: cosa dimenticare di $c_{(t-1)}$
2. **Input gate**: cosa estrarre dall'input $x_{(t)}$ e aggiungere
3. **Output gate**: combina input corrente e memoria a lungo termine per calcolare $y_{(t)} = h_{(t)}$

### GRU (Gated Recurrent Unit)

Versione **semplificata** di LSTM (meno parametri e complessità):

- **un solo stato** $h_{(t)}$
- **un solo gate** con logica invertita per decidere quanto dimenticare e quanto aggiungere (_se devo aggiungere, prima devo dimenticare_)

### Deep RNN

Più celle **stacked**: l'output di un livello è l'input del successivo → si apprendono relazioni più complesse.

- Il training supervisionato fornisce l'output desiderato **solo per l'ultimo livello**
- Input = **immagini** → $x_{(t)}$ sono feature di alto livello estratte da una **CNN** (non pixel)
- Input = **parole** → $x_{(t)}$ sono **word embedding**

---

## Time Series

Una serie temporale (borsa, temperatura, consumo energetico…) è una funzione campionata nel tempo: noti i valori fino a $t$, si vuole predire $t+1$ (anche con input non 1D).

> [!important] Prima di usare modelli complessi (rischio overfitting) Provare **baseline semplici** e valutarne la generalizzazione:
> 
> - **Persistence model**: predizione a $t+1$ = valore a $t$
> - **Linear model**: regressore lineare
> - **Modelli statistici classici**: studiati da decenni, spesso preferibili alle RNN

> [!warning] Decorrelazione Se i valori sono correlati nel tempo sembra di predire bene (es. titolo di borsa: anche il persistence model ha buon MSE). Si **decorrela** sostituendo il valore in $t$ con la differenza $x_t - x_{t-1}$ (_time differencing_), così si vede la reale capacità predittiva.

### Classificazione/Regressione con Deep RNN

Un MLP semplice (1 o pochi layer) è collegato a $Y_{(t)}$:

- **Classificazione**: un neurone di output per classe
- **Regressione**: un neurone per ogni _prediction ahead_ (es. 3 neuroni per $t+1, t+2, t+3$)

### Dati statici + dinamici

Esempio: info di base di un paziente (**statici**) + livelli di glucosio nel tempo (**dinamici**). Tre soluzioni:

1. Replicare i dati statici in ogni $X_{(t)}$ → poco efficiente
2. Darli all'MLP finale → la parte ricorrente **non ne è influenzata** (non ottimale)
3. Usarli per definire lo **stato hidden iniziale** $h$ (non più 0) → di fatto un modello **encoder-decoder**: l'encoder opera su un solo istante (dati statici) e produce il **contesto** per il decoder ✅

---

## Natural Language Processing (NLP)

Settore a cui il deep learning ha dato più contributi. Applicazioni principali:

- **Text classification** (es. sentiment analysis)
- **Entity extraction / NER**: estrarre info da testo non strutturato (es. parametri vitali da cartella clinica)
- **Summarization**
- **Generation** (con stile specificato)
- **Machine translation** (anche tra linguaggi di programmazione)
- **Coding**: completamento, funzioni da specifiche, commenti
- **Question answering**
- **Digital assistant / chatbot** (Alexa, Siri, ChatGPT)
- **Speech recognition / synthesis**
- **Multimodal extensions** (immagini, suoni)

### Tokenization

Suddivisione del testo in **token** (unità più piccole): parole intere, parti di parole, singoli caratteri, simboli (emoticon).

> [!tip] Perché token e non parole?
> 
> - Lavorare con **più lingue** e alfabeti diversi, e con simboli dei linguaggi di programmazione
> - Creare **nuove parole** unendo token

- Il numero di token utilizzabili = **vocabolario** (GPT-2 e GPT-3: ~**50K**)
- **BPE (Byte Pair Encoding)** costruisce il vocabolario e permette a GPT-3 di supportare fino a 46 lingue senza aumentare i token
- In GPT-3, per l'inglese un token ≈ **4 caratteri**

> [!example] BPE: come si crea il dizionario Corpus: `low`(5), `lower`(2), `newest`(6), `widest`(3)
> 
> 1. Vocabolario iniziale = **singoli caratteri**
> 2. Si trova la **coppia più frequente** e la si fonde in un nuovo token (es. `e`+`s` → `es`)
> 3. Si ripete fino a un criterio di stop (dimensione vocabolario o frequenza minima)
> 
> Risultato: `lowest` → `['lo','we','st']`. Si gestiscono parole rare e lingue diverse senza gonfiare il vocabolario.

### Word (token) Embedding

ID dei token o **one-hot vector** non codificano **semantica e similarità** (sinonimi).

L'**embedding** associa a ogni token un **vettore di dimensione contenuta** (es. 512); token di significato simile → **vettori vicini**.

- **Word2Vec**: uno dei primi modelli (rete a due livelli, **unsupervised**)
- Nei modelli recenti (GPT-3 e successivi) l'embedding è **appreso insieme agli altri parametri**

> [!important] Algebra dei vettori Similarità semantica = **distanza euclidea**, e i concetti si manipolano con operazioni algebriche: $$\text{King} - \text{Man} + \text{Woman} \approx \text{Queen}$$ Vale anche per input multimediali nello spazio latente (es. uomo con occhiali − uomo senza + donna senza = donna con occhiali).

### Generazione autoregressiva

Il risultato è prodotto **token per token**: il token $i$-esimo generato viene **appeso all'input** per produrre il token $i+1$, e così via. Inizio e fine sono gestiti da token speciali: `<SOS>` (Start-Of-Sequence) e `<EOS>` (End-Of-Sequence).

---

## Neural Machine Translation con RNN

Elaborazione **many-to-many** a lunghezza variabile.

- Architettura **Encoder-Decoder** RNN (storicamente la più usata) con meccanismi di **attenzione** (per ogni parola assegna un peso alle altre parole importanti nella frase)
- Il decoder è **autoregressivo**: genera un token per volta e lo riporta in input
- Pipeline: testo → ID token → **embedding lookup** → RNN → **softmax** sul vocabolario

---

## Transformers

> [!question] Perché superare LSTM? Le LSTM modellano male le relazioni tra porzioni **lontane** dell'input e la computazione è **sequenziale** → difficile addestrare grandi modelli.

L'evoluzione dei **meccanismi di attenzione** (a quali token devo far attenzione e quali ignorare) ha portato ai **transformer** (Google Brain, **2017**).

> [!important] Self-attention Elemento chiave: mette in relazione le parole della frase tra loro per **specificarne la semantica** (es. "palla" + "rete" + "90 minuto" → palla da calcio).

Oggi usati nella maggior parte delle applicazioni NLP e con buone prestazioni anche in **visione e speech** (architettura unificata del futuro? Stanno sostituendo lentamente le CNN).

### Architettura decoder-only

- **Colonnare**: una colonna per token; **pesi condivisi** tra tutte le colonne; generazione **autoregressiva**
- **Residual stream** (per colonna): spazio multidimensionale che veicola l'informazione dall'input all'output
- Per ogni layer due blocchi:
    - **MLP**: legge e scrive **solo sul proprio** residual stream
    - **Attention**: **legge** dai residual stream di **tutti i token precedenti** e scrive sul proprio
- Nei modelli recenti: oltre **100 layer** e oltre **100.000 token** (colonne)

> [!tip] Residual stream = canale ad alta capacità Dimensione $d_{model} = 12288$ in GPT-3. Attention e MLP leggono/scrivono su **sottospazi** (anche senza interferire con l'informazione presente: _message passing_) e possono anche **cancellare** informazione scrivendone la versione negativa. Fonte: _A Mathematical Framework for Transformer Circuits_ (Elhage et al., Anthropic, 2021).

### Dove sono i pesi?

Esempio: LLM di medie dimensioni, **90B parametri**, **100 layer**.

- I pesi sono condivisi tra colonne → **non dipendono dalla lunghezza del prompt**
- Trascurando encoding/decoding: $90B / 100 = $ **900M per layer**
- Proporzione tipica: **2/3 MLP** (600M) + **1/3 Attention** (300M)

### Transformer vs RNN

||RNN|Transformer|
|---|---|---|
|Informazione|si **diluisce** durante la propagazione|attention **globale**, non diluisce|
|Parallelismo|**wavefront**, poco efficiente su GPU|**tutto il layer** processabile in parallelo|

Nel Transformer le celle di un livello dipendono solo dal livello precedente, non da quelle dello stesso livello.

---

## Large Language Models (LLM)

Modelli linguistici **autoregressivi** di enormi dimensioni (miliardi di parametri), addestrati su grandi corpus (news, libri, articoli, codice…).

Efficacia dovuta a: architettura **transformer** + tecnica di addestramento (**predici il token successivo**) + **scala** (parametri e dataset) → risultati oltre le aspettative anche in problem solving, reasoning, coding.

> [!example] Numeri dello stato dell'arte (2025)
> 
> - **Parametri**: 100–2000 miliardi
> - **Corpus**: 10.000–100.000 miliardi di token (rapporto di almeno **20:1** rispetto ai parametri)
> - **Costo training**: 100–500 M$ (decine di migliaia di GPU per mesi)

### Next token prediction

Parte più corposa del training: si fa scorrere sul testo una **finestra a lunghezza fissa** e si predice il token successivo. È **self-supervised**: non serve etichettatura manuale (il testo è tutto noto).

> [!important] Perché funziona (nonostante sembri troppo semplice) Impara non solo le proprietà **linguistiche e statistiche**, ma forza il modello a fare previsioni corrette in **matematica, logica, coding, senso comune**, dove (senza oracolo o memorizzazione esaustiva) si può predire bene **solo apprendendo un modello del problema sottostante**.

---

## Prompt Engineering

Costruire prompt efficaci è "_more of an art than a science_". Conviene inserire elementi che **dirigono** la risposta:

- terminare con un punto elenco → il modello capisce che vogliamo una lista
- istruzioni esplicite: "elenca i passi che ti hanno portato alla soluzione"
- inserire **esempi** nel prompt → **Chain-of-Thought**, che facilita anche la **verifica** della correttezza

### Zero, One, Few-shot

Si aggiungono esempi `Q: … A: …` direttamente nel prompt:

- **Zero-shot**: nessun esempio, domanda diretta
- **One-shot**: un solo esempio
- **Few-shot**: alcuni esempi (in genere < 5)

---

## Embedding, Indexing, Tuning

### Document Embedding

Un LLM estrae da un intero testo un **vettore di feature semantiche**. Utile per trovare analogie o **raggruppare testi per similarità** (es. richieste a un call center).

> [!warning] Non confondere Il **document embedding** (testo intero) è diverso dal **token embedding** applicato in ingresso ai singoli token.

- Si calcolano **distanze** (euclidea o coseno) e/o si applica **clustering**
- Operativamente: si **pre-calcola** l'embedding dei documenti d'archivio; per un nuovo documento si calcola **solo il suo** embedding e lo si confronta con quelli memorizzati

### Interrogare una knowledge base proprietaria (RAG)

Problema: usare un LLM potente su un argomento specifico che non conosce (es. manuale di 1000 pagine).

> [!question] Alternative
> 
> - **Fine-tuning** → non semplice/efficiente
> - **Tutto il manuale nel prompt** → troppo lungo, inefficiente
> - **Data indexing con embedding** ✅ (es. LlamaIndex)

Procedura:

1. Dividere il manuale in **chunk** parzialmente sovrapposti
2. Calcolare l'embedding di ogni chunk con l'**LLM A** e creare un **indice**
3. Data una query, calcolarne l'embedding (sempre con A) e cercare nell'indice i **chunk simili**
4. Inviare all'**LLM B** la query con nel prompt, come **contesto**, solo i chunk simili

> [!tip] A può essere un LLM leggero (anche open source in locale → **privacy e confidenzialità**), B un LLM stato dell'arte in cloud.

### Fine Tuning

Si adatta **una parte** dei parametri (di un **clone** del modello) per ottimizzare le risposte su un dominio specifico.

- Serve un set **etichettato** (Prompt – Output) di **almeno qualche centinaio** di esempi, da dividere in training e validation
- Le prestazioni crescono solo **linearmente** al raddoppio degli esempi

> [!warning] Il modello può "dimenticare" cose che sapeva fare. È tollerato?

Non si possono tunare miliardi di parametri con poche centinaia di esempi, quindi:

- **Bloccare** la maggior parte dei livelli e tunare solo l'**ultimo/ultimi layer**
- Aggiungere **adapters** (layer laterali) inizializzati per non alterare le risposte iniziali; il comportamento cambia "lentamente" durante il tuning → tecnica **LoRA** (Low-Rank Adaptation), tra le più usate

---

## Reinforcement Learning

**Obiettivo**: apprendere un **comportamento ottimale** dalle esperienze passate.

Un **agente** esegue **azioni** ($a$) che modificano l'**ambiente**, causando passaggi di **stato** ($s$). Quando ottiene risultati positivi riceve un **reward** ($r$), che può essere **ritardato** rispetto all'azione (o sequenza di azioni) che lo ha causato.

**Episodio (game)** = sequenza finita di stati, azioni, reward: $$s_0, a_0, r_1, s_1, a_1, r_2, s_2, \dots, s_{n-1}, a_{n-1}, r_n, s_n$$

In ogni stato si sceglie l'azione che massimizza il **future reward**: $$R_t = r_t + r_{t+1} + \dots + r_n$$

> [!important] Discounted future reward Ambiente spesso **stocastico** → logica del "meglio un uovo oggi che una gallina domani": si pesano di più i reward vicini nel tempo. $$R_t = r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \dots + \gamma^{n-t} r_n \qquad (0 \le \gamma \le 1)$$ Definizione ricorsiva: $R_t = r_t + \gamma \cdot R_{t+1}$

---

## Q-Learning

$Q(s,a)$ indica la **qualità** dell'azione $a$ nello stato $s$, ponendo: $$Q(s_t, a_t) = \max R_{t+1}$$

Se $Q$ è nota, la **policy ottimale** sceglie: $$a^* = \arg\max_a Q(s,a)$$

Il punto cruciale è **apprendere $Q$**. Data una transizione $\langle s_t, a_t, r_{t+1}, s_{t+1}\rangle$ si ottiene l'**Equazione di Bellman**:

$$Q(s_t,a_t) = r_{t+1} + \gamma \cdot \max_a Q(s_{t+1}, a)$$

### Algoritmo

```
inizializza Q(s,a) in modo casuale
esegui m episodi
    t = 0
    do
        seleziona a_t = argmax_a Q(s_t, a)
        esegui a_t e osserva r_{t+1} e s_{t+1}
        Q(s_t,a_t) = Q(s_t,a_t) + α · ( r_{t+1} + γ·max_a Q(s_{t+1},a) − Q(s_t,a_t) )
        t = t + 1
    while (episodio non terminato)
```

- $\alpha$ = **learning rate**: se $\alpha = 1$ l'aggiornamento è esattamente Bellman; se $\alpha < 1$ si va nella stessa direzione con passi più piccoli
- Valori tipici iniziali: $\gamma = 0.9$, $\alpha = 0.5$ ($\alpha$ di solito viene ridotto durante l'apprendimento)

> [!warning] Problema pratico Dimensione di $Q$ = **n° stati × n° azioni** → ingestibile con spazi di stati grandi.

> [!example] Esempio: Grasping (OpenAI Gym + Box2D) Braccio robotico con 3 giunti e pinza deve afferrare una pallina su un piedistallo di posizione/altezza casuali.
> 
> - **Stato**: $\Delta x$, $\Delta y$ pinza–pallina + stato pinza (aperta/chiusa)
> - **Azioni** (7): rotazione dx/sx su ciascuno dei 3 giunti + inverti stato pinza
> - **Reward**: avvicinamento **+1**, allontanamento **−1**, movimento a pinza chiusa **−0.2**, cattura **+100**
> - Tabella $Q$ memorizzata esplicitamente; $\varepsilon = 0.1$, $\gamma = 0.6$, $\alpha = 0.2$
> - Il grafico mostra la % di episodi vinti durante il training (si stabilizza intorno al 65–70%)

Altri esempi: **balancing** (Cart-Pole, piano mobile, simulatore Bullet Physics) e manipolazione di oggetti.

---

## Deep Reinforcement Learning

**2013**: DeepMind (poi acquisita da Google) pubblica _Playing Atari with Deep Reinforcement Learning_: un agente impara a giocare a livello **super-human** a molti giochi Atari.

> [!important] Cosa lo rende straordinario Lo stato $s$ non sono feature game-specific (posizione, velocità…) ma **raw pixel** dello schermo → lo **stesso algoritmo** impara giochi diversi semplicemente giocando.

> [!question] Perché la tabella $Q$ non è gestibile? Stato = 4 immagini a 256 livelli di grigio, risoluzione 84×84: $$256^{84 \times 84 \times 4} \approx 10^{67970}$$ più degli atomi nell'universo conosciuto. _(le slide scrivono 86×86 nel testo, ma il calcolo usa 84×84)_

> [!tip] Idea **Approssimare $Q$ con una rete deep (CNN)** che, per ogni stato di input, restituisce **un valore di qualità per ogni azione**.

- Ulteriori raffinamenti → **AlphaGo**, che nel **2016** ha battuto a Go il campione **Lee Sedol**
- Approcci più efficienti e robusti: **PPO** (Proximal Policy Optimization) e **GRPO** (Group Relative Policy Optimization), oggi molto usati anche per ottimizzare il **comportamento degli LLM**