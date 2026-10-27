> [!question] Cosa sono le caratteristiche (**feature**)?
> Sono un tipo di rappresentazione di informazioni di un'immagine, rappresentando le **parti più interessanti** sotto forma di **vettori numerici** compatti.
## Dalle Feature Manuali al Deep Learning

Prima del successo del Deep Learning, i programmatori dovevano agire come "traduttori". Se volevi che un computer riconoscesse un gatto, dovevi scrivere algoritmi che cercassero specificamente le **feature manuali** (scelte da te)

### Machine Learning Classico

Il **Machine Learning** si basa su un processo diviso in due fasi distinte:
	
1) **Scelta manuale delle feature (Feature Engineering):** Un esperto umano analizza i dati e decide quali caratteristiche sono rilevanti per distinguere gli oggetti (es. per distinguere un gatto da un pesce, l'umano indica al sistema di guardare la forma delle orecchie o la presenza di squame).
    
2) **Classificazione:** Un algoritmo statistico riceve queste caratteristiche numeriche e impara a dividerle in categorie (Cane, Gatto, Pesce).
	
    ![[Pasted image 20260408104825.png]]

### Deep Learning

Il **Deep Learning** è una sottocategoria del Machine Learning che elimina la necessità dell'intervento umano nella selezione delle caratteristiche

- **Apprendimento End-to-End:** Sia l'estrazione delle caratteristiche che la classificazione avvengono all'interno della stessa rete neurale. La rete guarda i dati grezzi (i pixel dell'immagine) e decide autonomamente quali pattern sono utili per il risultato.
    
- **Feature Apprese in Automatico:** Nei primi livelli della rete vengono identificate linee e bordi; nei livelli successivi (quelli "profondi") queste linee vengono combinate per riconoscere strutture complesse come occhi, musi o arti.
	
    ![[Pasted image 20260408104920.png]]

> [!question] Ma se impara tutto lui noi cosa diamo alla rete?
> Noi diamo grandi volumi di dati (Big Data), completamente etichettati (**Labeled Data**) così da insegnare alla rete ogni possibile variante dell'oggetto interessato.


---

## Le Reti Neurali Convoluzionali (CNN)

Le **CNN** sono architetture nate per elaborare dati con struttura a griglia, come le immagini.

**Sintesi dei livelli principali:**
- **Convolution:** Applica i filtri per estrarre caratteristiche.
- **ReLU:** Funzione di attivazione che introduce non-linearità.
- **Pooling:** Riduce la risoluzione spaziale per rendere la rete più robusta e veloce.
- **Softmax (Livello finale):** Trasforma i numeri in uscita in probabilità percentuali.
### Il ruolo dei Filtri e la Profondità

- **Apprendimento automatico:** Nelle CNN i filtri non sono progettati dall'uomo. I valori numerici dei filtri (pesi) sono **decisi dalla rete stessa** durante la fase di addestramento (_training_).

> [!info] Perchè si parla di studio **"profondo"** (Deep)?
> Perché la rete è composta da numerosi livelli sovrapposti. Andando in profondità, i filtri diventano progressivamente più specifici:
> - **Livelli superficiali:** Estraggono feature semplici (bordi, linee, colori)
> - **Livelli profondi:** Estraggono feature complesse (forme, parti di oggetti, ...)



---
### OpenCV - Classificazione Cifre

``` Python
# Carica la rete da file (modello già addestrato)
net = cv.dnn.readNet('minst/model.onnx')

# Carica un'immagine di test
img = cv.imread('minst/test_6.png', cv.IMREAD_GRAYSCALE)

# Fornisce l'immagine di test in ingresso alla rete previa conversione in BLOB
net.setInput(cv.dnn.blobFromImage(img))

# Esegue la rete per generare l'ouput (inferenza)
out = net.forward()

# Il risultato (out) è un vettore di probabilità. 
# argmax estrae l'indice della probabilità più alta (la cifra predetta).
classe = np.argmax(out) # Risultato della classificazione (0..9)
```
### Concetti Chiave: Inferenza e Blob

> [!question] **Cos'è l'Inferenza?** 
> È la fase in cui la rete, già addestrata, viene utilizzata per generare un output su dati nuovi. 
	
>[!question] **Cos'è un Blob?** 
>Le immagini non possono essere date "pure" alla rete. Un **Blob** è un'immagine pre-processata e trasformata in un formato tensoriale (matrice multidimensionale). La funzione `cv.dnn.blobFromImage` si occupa di:
>
>1. **Ridimensionare** l'immagine (es. portarla a 28x28).
>2. **Normalizzare** i valori dei pixel.
>3. **Organizzare** i canali colore in modo che la rete possa leggerli correttamente.

### OpenCV - Ispezione della Rete e Output Intermedi

``` Python
# Stampa tipo e forma dell'input e ouput di ogni livello
for i, l_in, l_out in zip(*net.getLayersShapes((1,1,28,28))):
    i = int(i[0]); l = net.getLayer(i)
    print(i, l.type, l_in[0].squeeze()[1:], l_out[0].squeeze()[1:])

# Esegue la rete e restituisce l'output di TUTTI i livelli
all_outs = net.forward(net.getLayerNames())

# Visualizza l'output dei filtri del livello 1 come 8 immagini distinte.
# Questo permette di vedere quali bordi o dettagli il primo livello sta estraendo.
va.show(*all_outs[1][0]) 
```
![[Pasted image 20260408114959.png]]

## Object Detection: Il sistema YOLO (You Only Look Once)

> [!info] Ricorda
> L'**object detection** identifica l'oggetto (**classificazione**) e ne individua la posizione (**localizzazione**).

### Caratteristiche di YOLO v3

- **Architettura:** È una CNN estremamente profonda, composta da ben **106 livelli**.
    
- **Efficienza:** A differenza di altri sistemi che analizzano l'immagine più volte o a pezzi, YOLO guarda l'intera immagine in un unico passaggio ("You Only Look Once"). Questo lo rende estremamente veloce, ideale per il video in tempo reale.
    
- **Capacità:** La versione standard è addestrata sul dataset COCO, che comprende **80 classi** di oggetti comuni (persone, auto, animali, sedie, ecc.).
    
### L'Output della rete

Per ogni oggetto rilevato, la rete restituisce tre informazioni fondamentali:

1. **Classe:** Di che oggetto si tratta (es. "Cane").
    
2. **Bounding Box:** Le coordinate del rettangolo che racchiude l'oggetto (centro, larghezza, altezza).
    
3. **Confidence Score:** Un valore di probabilità che indica quanto la rete è sicura che l'oggetto si trovi proprio in quel punto.
	
![[Pasted image 20260408121438.png]]

---