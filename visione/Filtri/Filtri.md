### Ricorda

| Tipo Operazione | Descrizione                                                                                                                                                       | Foto                                      |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| sui Pixel       | il valore di ciascun pixel nell'immagine risultato dipende solo dal corrispondente pixel dell'immagine di partenza                                                | ![[Pasted image 20260324124017.png\|462]] |
| Locali          | utilizza invece **un insieme di pixel nelle vicinanze (intorno)** del pixel dell'immagine di partenza per determinare il valore del pixel nell'immagine risultato | ![[Pasted image 20260324124033.png]]      |
# Filtri

## Filtri Lineari

### Cosa sono?
sono le operazioni locali più comuni: il valore di ciascun pixel è calcolato come **somma pesata** dei valori dei pixel nell'intorno considerato 
$$
I'[y,x] = \sum_{i,j}{ w_{xy}[i,j]\cdot F[i,j] }
$$
![[Pasted image 20260329193733.png]]

### Il filtro
$F$ è chiamato **filtro, maschera o kernel** ed è la matrice $m\times m$ (con $m$ dispari) contenente i *pesi* con cui moltiplicare l'intorno dei pixel.

> [!info] i pesi sono chiamati **coefficienti**

![[Pasted image 20260329194343.png|193]]

quindi la formula per il calcolo del valore di un pixel nell'immagine destinazione può essere scritta come: 
$$
I'[y,x]=\sum_{i=-d}^{d}\sum_{j=-d}^{d}F[i,j]\cdot I[y+i,x+j]\quad t.c. \quad d = [\frac{m}{2}]
$$
> [!attention] Problema
> Vicino ai bordi il filtro non ha un intorno completo, dunque ci sono varie possibilità
> 1) Usare un valore di "background" costante
> 2) Copiare il valore del pixel più vicino
> 3) Riflessione dei pixel dell'immagine
> ![[Pasted image 20260329203600.png]]

### Tipi di dati e normalizzazione dei valori

L'applicazione di un filtro può cambiare drasticamente i valori numerici dei pixel. È fondamentale gestire correttamente i tipi di dati:

- **Normalizzazione:** Se sommiamo i valori di un'area senza normalizzare, il risultato potrebbe superare il limite di 255 (bianco). 

> [!info]
> Se il filtro serve a "sfocare" o "mediare", dividiamo ogni coefficiente per la loro somma totale affinché la luminosità globale rimanga invariata.

- **Valori Negativi:** Alcuni filtri (come quelli per trovare i bordi, es. Sobel) hanno pesi negativi. In questo caso, il risultato non può essere salvato in un classico formato `uint8` (0-255), ma serve un formato che supporti i segni (come `int16` o `float32`).
--- 
### Esempi di filtri

**Identità: filtro che non cambia nulla**
![[Pasted image 20260329221552.png|444]]

**Traslazione: filtro che shifta verso il basso**
![[Pasted image 20260329221605.png|445]]

**Blur: filtro che sfoca**
- _in questo caso vediamo la normalizzazione_
![[Pasted image 20260329221616.png|446]]

### Visualizzare immagini con pixel di tipo int o float

Le immagini processate (specialmente quelle che estraggono i bordi) hanno spesso valori che l'occhio umano o i monitor non possono gestire direttamente (numeri negativi o molto grandi)

- **Senza valori negativi:** - Si effettua uno "stretch" (dilatazione) lineare. Il valore massimo nell'immagine diventa 255 (bianco), e lo 0 resta nero.
$$I'(y, x) = \frac{255}{\max I} \cdot I(y, x)$$
- **Con valori negativi:** Qui è necessario un **offset**. Il valore 0 viene spostato a 128 (grigio medio). I valori negativi "scendono" verso il nero, quelli positivi "salgono" verso il bianco.
$$I'(y, x) = 128 + \frac{127}{\max \text{abs}(I)} \cdot I(y, x)$$
## Correlazione e Convoluzione

> [!info] In termini semplici, la **convoluzione** è una **correlazione** in cui il filtro (kernel) è stato ruotato di 180 gradi.

- **Correlazione ($\otimes$):** Scorri il filtro sull'immagine e moltiplichi i valori corrispondenti. Misura quanto il filtro "assomiglia" a quella parte dell'immagine.
    
- **Convoluzione ($*$):** Prima di fare la stessa operazione, "ribalti" il filtro sia orizzontalmente che verticalmente ($F' = F(-i, -j)$).

### Perchè questa distinzione?

La **convoluzione** permette di sfruttare alcune proprietà algebriche:

- **Commutatività:** $I * F = F * I$. Non importa se pensi all'immagine che scorre sul filtro o viceversa.
    
- **Associatività:** $I * (F * G) = (I * F) * G$.  Ovvero che se devi applicare due filtri in sequenza, puoi prima "convolvere" i due filtri tra loro per crearne uno unico e applicare quello all'immagine. Risparmierai moltissimo tempo di calcolo!

> [!attention] **Nota**
> La correlazione **non** è associativa. Se applichi due correlazioni in sequenza, il risultato finale non è lo stesso che otterresti combinando i filtri prima.

In **OpenCV** la funzione `cv.filter2D` implementa la **correlazione**
per ottenera la **convoluzione** basta ribaltare il filtro con `cv.flip(filtro,-1)`
