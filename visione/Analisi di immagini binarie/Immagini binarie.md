**Perchè sono importanti?**
Le immagini binarie possono avere diverse applicazioni, come:
- riconoscimento del **testo**
- **conteggio** e **misura** di oggetti
- **rilevamento di bordi**

> [!question] Che tipo di dato usiamo per le immagini binarie?
> In logica potremmo usare un semplice bit per ogni pixel, ma spesso (soprattutto con le trasformate) avremo bisogno di più spazio dati per singolo pixel.
> 

## Topologia digitale

> [!quote] Disciplina che studia **proprietà e caratteristiche topologiche** delle immagini (es. componenti connesse, bordi di un oggetto, ...)

Convenzionalmente le rappresentiamo in bianco e nero, ma in realtà le immagini binarie possono anche avere colori diversi.

> [!info] I due colori vengono chiamati **background** e **foreground**
> Sia ($F$) l’insieme di tutti i pixel di foreground e ($F^*$) l’insieme di quelli di background di un’immagine ($Img$)
> 
>![[Pasted image 20260407095321.png]]

---
## Metriche e Distanze

Nelle immagini digitali (griglie discrete), la distanza tra due punti $p = (x_p, y_p)$ e $q = (x_q, y_q)$ non è sempre quella euclidea. Le metriche più usate sono:

- **Distanza $d_4$ (Manhattan / City-block):** Si muove solo in orizzontale e verticale.
$$d_4(p, q) = |x_q - x_p| + |y_q - y_p|$$
- **Distanza $d_8$ (Chessboard / Chebyshev):** Include anche le diagonali.
$$d_8(p, q) = \max(|x_q - x_p|, |y_q - y_p|)$$
### Concetti di Connessione

> [!info] Questi concetti dipendono dalla metrica scelta

- **Vicini:** I pixel a distanza unitaria (1) da $p$.
![[Pasted image 20260407102754.png|348]]

- **Percorso:** Una sequenza di pixel adiacenti tra due punti.
 ![[Pasted image 20260407102815.png|288]]
 
- **Componente Connessa:** Un insieme di pixel dove ogni coppia è raggiungibile tramite un percorso interno all'insieme.

>[!attention]
> In **4-connessione**, i pixel diagonali sono considerati separati.
> In **8-connessione**, i pixel diagonali uniscono gli oggetti.
> 
> ![[Pasted image 20260407102911.png|328]]

---
## Trasformata Distanza (Distance Transform)

La Trasformata Distanza sostituisce il valore di ogni pixel del foreground ($F$) con la sua distanza minima dal background ($F^*$).

![[Pasted image 20260407103428.png]]

**Pro e applicazioni:**
- Algoritmo con **bassa complessità** computazionale
- I massimi locali di intensità in corrispondenza degli assi mediani dell'oggetto (**scheletro**)
- **Tempate matching**
- **Thinning delle tracce**

> [!info] **Algoritmo Sequenziale (usando la metrica $d_4$)**
> 
> Si ottiene con due scansioni sulla matrice immagine i pixel di $F^*$ vengono settati a 0, mentre i pixel di F seguono questa regola:
> 
> 1. se in **Scansione Diretta (Alto $\to$ Basso, Sinistra $\to$ Destra):**
>    $$Img'(p) = \min(Img'(p_{Ovest}), Img'(p_{Nord})) + 1$$
>     
> 2. se in **Scansione Inversa (Basso $\to$ Alto, Destra $\to$ Sinistra):**
>    $$Img'(p) = \min(Img'(p_{Est}) + 1, Img'(p_{Sud}) + 1, Img'(p))$$
> 
> ![[Pasted image 20260407104557.png]]

### In OpenCV

``` Python
img = cv.imread('esempi/mario-bw.png', cv.IMREAD_GRAYSCALE)
dt4 = cv.distanceTransform(img, cv.DIST_L1, 3)
dt8 = cv.distanceTransform(img, cv.DIST_C, 3)
```

---
## Estrazione dei Contorni

Il contorno è costituito dalla **sequenza ordinata dei pixel che hanno distanza unitaria da $F^*$** secondo la metrica adottata per $F^*$

> [!attention] Non è l'estrazione dei bordi (edge)
 
### In OpenCV: `cv.findContours`

I contorni possono essere memorizzati come:

- `cv.CHAIN_APPROX_NONE`: lista ordinata dei pixel
	
- `cv.CHAIN_APPROX_SIMPLE`: si approssima il contorno tramite segmenti e si salvano solo i vertici dei segmenti (risparmia memoria).
	
- `cv.CHAIN_APPROX_TC89_L1` e  `cv.CHAIN_APPROX_TC89_KCOS`: approssima con una serie di **curve**

> [!important] **Gerarchia dei Contorni**
> 
> La funzione restituisce una struttura ad albero (`RETR_TREE`) che identifica se un contorno è interno (buco) o esterno rispetto a un altro.

``` Python
esempi = ('labirinto', 'leaf', 'butterfly', 'bat')
for n in esempi:
	img = cv.imread('esempi/' + n + '.png', cv.IMREAD_GRAYSCALE)
	c, _ = cv.findContours(img, cv.RETR_EXTERNAL, cv.CHAIN_APPROX_NONE)
	res = cv.drawContours(cv.cvtColor(img, cv.COLOR_GRAY2BGR), c, -1, (255,0,0), 2)
```
![[Pasted image 20260407120317.png]]

``` Python
img = cv.imread('esempi/donuts.png')
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
# Binarizza con soglia 253, tutto ciò che è più scuro è foreground
# cv.THRESH_BINARY_INV viene invertito (solitamente il più scuro è background)
_, binarized = cv.threshold(gray, 253, 255, cv.THRESH_BINARY_INV)
# La funzione restituisce anche una struttura dati ad albero che contiene eventuali
# relazioni fra contorni (un contorno "padre" contiene uno o più "figli")
contours, tree = cv.findContours(binarized, cv.RETR_TREE, cv.CHAIN_APPROX_SIMPLE)
# Disegna tutti i contorni trovati
allc = cv.drawContours(img.copy(), contours, -1, (255,0,0), 5)
# Esclude i contorni con area piccola (che saranno probabilmente rumore) e abbina due flag booleani per sapere se
# il contorno ha un padre e/o figli
info = [(c, tree[0,i,2]>=0, tree[0,i,3]>=0) for i, c in enumerate(contours)
if cv.contourArea(c)>100]
# Colora diversamente i contorni selezionati che hanno "padri" o "figli"
	res = img.copy()
	for c, f, p in info:
		cv.drawContours(res, [c], -1, (0,0,255) if p else (255,0,0) if f else (0,255,0), 5)
```
![[Pasted image 20260407131157.png|512]]

> [!info] \_ _,_ tree = cv.findContours( ... )
> Il `tree` è un array NumPy con una forma specifica: `[1, numero_contorni, 4]`.
> Per ogni contorno `i`, esiste un vettore di 4 valori interi:
> $$[Next, Previous, First\_Child, Parent]$$
> _Quando un contorno è interno ad un altro viene detto il figlio_

> [!info] Come si naviga **tree**?
> 1. Primo indice è sempre a `0`
> 2. Il secondo indice indica il **numero identificativo del contorno** che si vuole analizzare
> 3. Il terzo indice si riferisce all'informazione che si vuole sapere 
> 	- 0 $\rightarrow$ Next
> 	- 1 $\rightarrow$ Previous
> 	- 2 $\rightarrow$ First_Child
> 	- 3 $\rightarrow$ Parent
 
### Funzioni utili che si possono applicare ai contorni
![[Pasted image 20260407133035.png]]

---
## Etichettatura Componenti Connesse

È il processo che **individua** e **assegna** un ID unico (1, 2, 3...) a ogni componente connessa nell'immagine.

**Algoritmo a due passate:**

1. **Passata 1:** Si analizzano i vicini già visitati. Se hanno etichette diverse, se ne sceglie una e si segna l'equivalenza in una tabella.
    ![[Pasted image 20260407134349.png|259]]
2. **Passata 2:** Si ripercorre l'immagine risolvendo le equivalenze e assegnando l'etichetta definitiva.
    ![[Pasted image 20260407134405.png|257]]

### in OpenCV

``` Python
img = cv.imread(name, cv.IMREAD_GRAYSCALE)
_, bw = cv.threshold(img, thr, 255, cv.THRESH_BINARY)

n, cc = cv.connectedComponents(bw)
# n è il numero di componenti connesse
# cc è un'immagine di interi in cui il valore di ogni pixel
# è l'indice della componente connessa a cui appartiene
print(f'Componenti connesse: {n}')
```
![[Pasted image 20260407133557.png]]

In OpenCV si usa `cv.connectedComponentsWithStats()`, che fornisce anche:

- **Area** (numero di pixel).
    
- **Bounding Box** (rettangolo contenitore).
    
- **Centroide** (baricentro geometrico).

``` Python
img = cv.imread('esempi/bolts.png', cv.IMREAD_GRAYSCALE)
_, bw = cv.threshold(img, 152, 255, cv.THRESH_BINARY)

# La funzione cv.connectedComponentsWithStats, oltre all'immagine
# con le etichette, restituisce, per ogni componente connessa,
# le coordinate del baricentro, l'area e il bounding box.
n, cc, stats, centroids = cv.connectedComponentsWithStats(bw)

@interact(i=(0,n-1))
def show_stats(i=0):
	res = cv.cvtColor(img, cv.COLOR_GRAY2BGR)
	res[cc==i,1] = res[cc==i,1]//4 + 255*3//4
	
	# Per ogni componente si possono visualizzare alcune statistiche
	x, y = stats[i,cv.CC_STAT_LEFT], stats[i,cv.CC_STAT_TOP]
	w, h = stats[i,cv.CC_STAT_WIDTH], stats[i,cv.CC_STAT_HEIGHT]
	area = stats[i,cv.CC_STAT_AREA]
	
	cv.rectangle(res, (x,y), (x+w,y+h), (255,0,0), 3)
	
	va.center_text(res, f'A[{i}]={area}', centroids[i].astype(int), (0,0,255))
```
![[Pasted image 20260407134303.png|216]]

---
