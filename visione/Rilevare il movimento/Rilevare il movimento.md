> [!important] Obbiettivo
> Ottenere, a partire da un video, ad ottenere una serie di immagini binarie (video) che identifica gli oggetti in movimento
> 
> ![[Pasted image 20260407145208.png]]
> 
Questa dispensa approfondisce le tecniche per il rilevamento e l'inseguimento (tracking) di oggetti in sequenze video con telecamera fissa.

---
# Background Subtraction: Approccio Generale

L'obiettivo è ottenere un'immagine binaria per ogni frame dove i pixel in movimento sono il **foreground** ($F$) e il resto è il **background** ($F^*$).

1. **Modellazione:** Si crea un **modello del background.**
    
2. **Rilevamento:** Si confronta il frame attuale con il modello. Se la differenza supera una soglia ($thr$), il pixel è marcato come movimento.
    
3. **Aggiornamento:** Il modello viene aggiornato per adattarsi a cambi di luce, piccoli spostamenti o cambi di sfondo.

![[Pasted image 20260407145606.png]]

### Differenza Temporale (Simple Difference)

Il metodo più elementare confronta due **frame successivi**:

$$|frame_i - frame_{i-1}| > thr$$

> [!attention] Questo metodo è estremamente sensibile al rumore e non distingue oggetti che si fermano momentaneamente.

``` Python
# Costruisce un oggetto VideoCapture che permette di estrarre i frame da un file video
cap = cv.VideoCapture('esempi/simpsons_abbey_road.mp4')

thr = 50 # La soglia che si intende utilizzare
# Per ogni frame, convertito in grayscale, crea una maschera
# dei pixel la cui differenza dal precedente è maggiore di thr
fm = [] # Lista di tuple (frame, mask)
prev = None
while True:
	ret, frame = cap.read()
	if not ret: # ret è False quando non ci sono più frame
		break
	f = cv.cvtColor(frame, cv.COLOR_BGR2GRAY).astype(np.int16)
	if prev is not None:
		mask = np.zeros_like(f)
		mask[np.abs(f - prev) > thr] = 255 # A 255 i pixel la cui differenza supera thr
		fm.append( (frame, mask) ) # Lista di tuple (frame, mask)
	prev = f
cap.release() # IMPORTANTE: Rilascia le risorse (in questo caso il file)
```

---
## Analisi Statistica e Algoritmo MOG2

> [!attention] Problema
> I pixel del background non sono perfettamente costanti a causa di ombre, riflessi o foglie mosse dal vento.
> 
> ![[Pasted image 20260407153033.png]]

### Quale modello è meglio per i pixel di background?

- Quando la **variabilità è contenuta**, un semplice valore (il primo frame, il frame precedente, oppure la media degli ultimi n frame) può essere sufficiente.
	![[Pasted image 20260407153359.png]]
	
- **Modello Gaussiano Singolo:** Ogni pixel è modellato da una distribuzione normale $N(\mu, \sigma^2)$ (gaussiana). Se un pixel si discosta troppo dalla media $\mu$, è foreground.
	![[Pasted image 20260407153417.png]]
	
- **Mixture of Gaussians (MoG):** Utile per background multimodali (es. un pixel che alterna tra il colore di una foglia e quello del cielo). Ogni pixel è modellato da una somma di $K$ gaussiane.
    ![[Pasted image 20260407153438.png]]

### In OpenCV: `cv.createBackgroundSubtractorMOG2`

L'algoritmo implementa un sistema **adattivo** basato proprio su **Mixture of Gaussians**.

> [!question] Perchè **adattivo**?
> Il parametro `learningRate` (nel metodo `apply()`) determina quanto velocemente il modello "dimentica" il passato (settabile come qualsiasi numero tra $0$ e $1$):
>- $0$: Il modello non cambia mai.
>- $1$: Il modello coincide con l'ultimo frame.
>- Se **non specificato** o negativo, è scelto in automatico

``` Python
mog = cv.createBackgroundSubtractorMOG2(detectShadows=False)

cap = cv.VideoCapture('esempi/simpsons_abbey_road.mp4')
fm = [] # Lista di tuple (frame, mask)
while True:
	ret, frame = cap.read()
	if not ret:
		break
	f = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
	mask = mog.apply(f) # Utilizza l'algoritmo MOG2 sui frame grayscale
	fm.append( (frame, mask) )
cap.release()
```
---
## Raffinamento della Maschera Binaria

Spesso l'output grezzo della sottrazione del background è rumoroso. Si applicano quindi le tecniche di analisi dell'immagine viste in precedenza:

### Riduzione Risoluzione
Operare su maschere piccole ($1/2$ o $1/4$ della dimensione originale) per velocizzare i calcoli.

``` Python
# Per la maschera binaria è normalmente sufficiente una risoluzione più bassa.
# Ridurre la risoluzione del frame prima di passarlo all'algoritmo di estrazione del
# foreground riduce la complessità computazionale dell'algoritmo stesso e di
# eventuali successive altre operazioni sulla maschera.
MASK_SF = 2.0
#...
mask = mog.apply(cv.resize(frame, None, fx = 1/MASK_SF, fy = 1/MASK_SF))
# ... eventuali modifiche alla maschera (a risoluzione ridotta) ...
# Riscala la maschera alle dimensioni originali
mask = cv.resize(mask, None, fx = MASK_SF, fy = MASK_SF, interpolation = cv.INTER_NEAREST)
fm.append( (frame, mask) )
```
![[Pasted image 20260408123930.png]]

> [!info] Per questo le telecamere hanno spesso bassa risoluzione

---
### Filtraggio Componenti Connesse
Si cercano le componenti connesse per poter rimuovere piccoli cluster di pixel (rumore)
$$Area(C_i) < Area_{min} \implies C_i = 0$$

``` Python
# Rimuove piccole componenti connesse: se è possibile fare un'ipotesi sulla dimensione
# minima degli oggetti in movimento, si può supporre che tutte le componenti con
# area minore di una certa soglia siano dovute al rumore e possano essere eliminate.
n, cc, stats, _ = cv.connectedComponentsWithStats(mask, connectivity=4)
small = [i for i in range(1,n) if stats[i,cv.CC_STAT_AREA]<70]
# oppure: small = np.nonzero(stats[1:,cv.CC_STAT_AREA]<70)[0] + 1
mask[np.isin(cc, small)] = 0
```
![[Pasted image 20260408124122.png]]

### Chiudere buchi

> [!info] Ricorda
> **Chiusura:** Per unire parti dello stesso oggetto separate dal rumore.
> **Apertura:** Per rimuovere piccole protuberanze o "peli" dai contorni.

``` Python
# Chiude piccoli buchi e concavità utilizzando l'operazione di chiusura della morfologia
# matematica.
# La dimensione dell'elemento strutturante va scelta con attenzione.
# Vedremo nel prossimo lucido come chiudere eventuali buchi di dimensione maggiore.
se = cv.getStructuringElement(cv.MORPH_ELLIPSE, (5,5))
mask = cv.morphologyEx(mask, cv.MORPH_CLOSE, se)
```
![[Pasted image 20260408124917.png]]

``` Python
# Riempie eventuali altri buchi eseguendo l'estrazione delle componenti connesse
# sul "negativo" della maschera (ossia sul background). Componenti connesse con area
# inferiore a una soglia ragionevole sono normalmente "buchi" che possono essere "chiusi".
n, cc, stats, _ = cv.connectedComponentsWithStats(255-mask, connectivity=4)
holes = [i for i in range(1,n) if stats[i,cv.CC_STAT_AREA]<2000]
# oppure: holes = np.nonzero(stats[1:,cv.CC_STAT_AREA]<10000)[0] + 1
mask[np.isin(cc, holes)] = 255
```
![[Pasted image 20260408124933.png]]

``` Python
# Infine rimuove eventuali piccole protuberanze
# mediante l'operazione di apertura della morfologia matematica.
# Questo può aiutare a rimuovere eventuali artefatti dovuti al rumore che si sono "fusi"
# con le componenti connesse principali.
se = cv.getStructuringElement(cv.MORPH_ELLIPSE, (5,5))
mask = cv.morphologyEx(mask, cv.MORPH_OPEN, se)
```
![[Pasted image 20260408124952.png]]

---
# Tracking con Mean-Shift

> [!important] Obbiettivo
> Anzichè cercare l'oggetto in tutta l'immagine, preferiamo rilevarlo una volta sola, e poi seguirlo nei frame successivi. 
> 
> L'algoritmo **Mean-Shift** cerca iterativamente il massimo di densità di una "mappa di confidenza".
> 
> ![[Pasted image 20260408125326.png]]

## La Mappa di Confidenza ($C$)

Perché l'algoritmo funzioni, dobbiamo trasformare l'immagine in una **mappa di probabilità** (o confidenza). Questa mappa dice: "Quanto è probabile che questo pixel appartenga al mio oggetto target?".

>[!question] Come viene ottenuta la mappa di confidenza $C$?
>$C$ può essere ottenuta analizzando il colore o altre caratteristiche dell'oggetto.

> [!info] 
> Come sempre, per semplicità, negli esempi che seguono, come caratteristica sarà utilizzata la luminosità (valore dei pixel convertiti in grayscale $I_G[y,x]$)
### Come si crea (Esempio con la Luminosità):

1. **Modello dell'oggetto:** Nel frame precedente, guardiamo l'oggetto dentro la finestra $W$ e creiamo un **istogramma** $h(v)$. Questo istogramma ci dice quali valori di grigio sono più comuni nell'oggetto.
	![[Pasted image 20260408161429.png|392]]
	
2. **Back-projection:** Nel nuovo frame, sostituiamo ogni pixel con il valore corrispondente nell'istogramma.$$C[y,x]=h[I_G[y,x]]$$
> _Significa che se un pixel ha un valore di grigio che era molto frequente nell'oggetto (es. la giacca di bart), quel pixel riceverà un valore di confidenza $C(y,x)$ alto
> _
![[Pasted image 20260408161817.png]]
---

## Quindi come funziona il Mean-Shift

Una volta ottenuta la mappa $C$, abbiamo una nuvola di punti pesati. L'obiettivo è centrare la finestra $W$ sulla massa principale di questi punti.

### I passi dell'algoritmo:

1. **Posizionamento iniziale:** Posizioniamo la finestra $W$ nelle stesse coordinate in cui si trovava nel frame precedente.
    ![[Pasted image 20260408162104.png|546]]

2. **Calcolo del Centro di Massa (Media Pesata):** Calcoliamo il nuovo centro della finestra usando i valori di confidenza dei pixel al suo interno come "pesi".

    > _I pixel con alta confidenza attireranno la finestra verso di loro._

3. **Spostamento:** Muoviamo il centro della finestra $W$ nella nuova posizione calcolata.

    ![[Pasted image 20260408162134.png|558]]
    
4. **Convergenza:** Ripetiamo i punti 2 e 3 finché la finestra smette di muoversi (lo spostamento è inferiore a una soglia) o finché non raggiungiamo un numero massimo di tentativi.
    

---

> [!attention] Limiti
> 1.  Il Mean-shift standard non cambia la dimensione della finestra (se l'oggetto si allontana o si avvicina, la finestra resta uguale). Esiste però una variante chiamata **CamShift** che risolve questo problema.
> $$$$
> 2. Se vicino all'oggetto c'è qualcosa con colori/luminosità simili, la finestra potrebbe esserne attratta e "perdere" il bersaglio.

## MeanShift in OpenCV


``` Python
def get_wnd_and_hist(i, frame, stats, mask):
	"""Date le statistiche delle componenti connesse stats, calcola l'istogramma
	della luminosità del bounding box della componente di indice i sui soli pixel
	indicati da mask. Restituisce bounding box e istogramma normalizzato in [0,255]."""
	x, y = stats[i, cv.CC_STAT_LEFT], stats[i, cv.CC_STAT_TOP]
	w, h = stats[i, cv.CC_STAT_WIDTH], stats[i, cv.CC_STAT_HEIGHT]
	roi = cv.cvtColor(frame[y:y+h, x:x+w], cv.COLOR_BGR2GRAY)
	hist = cv.calcHist([roi],[0],mask[y:y+h, x:x+w],[256],[0,255])
	return (x, y, w, h), hist

def get_new_wnd(frame, mask, hist, wnd):
	"""Esegue meanShift sulla luminosità di frame ponendo a zero i pixel di background
	secondo mask, utilizzando hist come istogramma e wnd come regione di partenza."""
	gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
	# La funzione cv.calcBackProject calcola la mappa di confidenza a partire
	# dall'istogramma e dai valori dei pixel
	conf_map = cv.calcBackProject([gray],[0],hist,[0,255],1)
	conf_map[mask==0] = 0 # Confidenza a zero fuori dalla maschera
	_, new_wnd = cv.meanShift(conf_map, wnd, term_crit)
	return new_wnd
```

### Esempi di tracking di oggetti

``` Python
# Data la sequenza fm di tuple (frame, mask)
term_crit = ( cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 1 )
tracked_objects = [] # (window, hist, label)
next_label = 1
res_tracking = [] # Sequenza di immagini risultato
for frame, mask in fm[1:]:
	n, cc, stats, _ = cv.connectedComponentsWithStats(mask)
	cc_used, next_tracked_objects = set(), []
	
	if n > 1: # Cerca di continuare a inseguire ciascuno degli oggetti
		for wnd,hist,label in tracked_objects:
			x,y,w,h = get_new_wnd(frame, mask, hist, wnd) # Applica meanShift
			# Quanti pixel per ogni etichetta ci sono dentro la nuova finestra?
			cc_hist, _ = np.histogram(cc[y:y+h, x:x+w], n, (0,n))
			index = np.argmax(cc_hist[1:])+1 # La più rappresentata (escluso back)
			if index not in cc_used and cc_hist[index]>=MIN_AREA_IN_WND:
				wnd, hist = get_wnd_and_hist(index, frame, stats, mask)
				next_tracked_objects.append( (wnd,hist,label) )
				cc_used.add(index)
		
	# Controlla se ci sono componenti connesse non abbinate a oggetti
	for i in range(1,n):
		if (stats[i, cv.CC_STAT_AREA] >= MIN_AREA) and (i not in cc_used):
			wnd, hist = get_wnd_and_hist(i, fblurame, stats, mask)
			next_tracked_objects.append( (wnd, hist, next_label) )
			next_label += 1
			
	tracked_objects = next_tracked_objects
	
	# Disegna informazioni sugli oggetti nell'immagine risultato
	res = frame.copy()
	for (x,y,w,h),hist,label in tracked_objects:
		cv.rectangle(res, (x,y), (x+w,y+h), 255, 2)
		cv.rectangle(res, (x,y-22), (x+w,y), 255, -1)
		cv.putText(res, f'{label}', (x+5, y-5), cv.FONT_HERSHEY_PLAIN, 1, 0, 1)
		# ... altre informazioni, ad es. linea che collega tutti i baricentri di ogni
		# oggetto, contatore che indica da quanti frame è inseguito ciascun oggetto, ...
	res_tracking.append(res)
```