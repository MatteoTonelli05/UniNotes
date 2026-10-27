``` Pseudocode
1. Smooth gaussiano dell'immagine
2. Calcolo del gradiente per ogni pixel
3. Soppressione dei non-massimi in direzione ortogonale al bordo
4. Selezione dei bordi significativi mediante isteresi
```
![[Pasted image 20260403231754.png]]

---
## 1. Smooth gaussiano dell'immagine

Semplice **smooth** che abbiamo già visto un miliardo di volte

> [!info] Perchè?
> Siamo interessati solo ai bordi principali, quindi blurrando evitiamo i bordi secondari

---
## 2. Calcolo del gradiente per ogni pixel

Un **qualsiasi calcolo del gradiente** scelto tra i 3 precedentemente visti

> [!info] Curiosità
> Prima si usava un filtro 2x2 per risparmiare risorse del calcolatore, ora non cambia granchè tra un filtro 3x3 e uno 2x2.

---
## 3. Soppressione dei non-massimi in direzione ortogonale al bordo

### Cosa serve?
Uno dei problemi era quello di avere dei bordi troppo spessi, quindi cerchiamo di renderli il più sottili possibili

> [!quote] Immaginiamo di vedere una catena montuosa dall'alto, noi dobbiamo selezionare solo la cresta

### Come funziona?

Per decidere se un pixel $p$ appartiene a un bordo, dobbiamo verificare se il modulo del suo gradiente $||\nabla[p]||$ è maggiore di quello dei suoi vicini **lungo la direzione del gradiente**.
	
- Se $p$ è il valore massimo in quella direzione, viene conservato.
- Altrimenti, viene rimosso (posto a zero).

![[Pasted image 20260403233026.png]]

> [!attenzione] Non è un massimo locale
> Noi selezioniamo SOLO i **massimi locali** rispetto all'**orientazione** del gradiente (non importa la direzione)

### Come troviamo questi non-massimi?

> [!attention] Problema
> La direzione del gradiente (l'angolo $\theta$) può puntare in qualsiasi direzione e non necessariamente verso il centro di un pixel vicino. Bisogna **Interpolare** !!!
> 
> ![[Pasted image 20260403234059.png|297]]

1) Calcoliamo $d$ ovvero lo **spostamento verticale** ed è calcolato come il rapporto tra le componenti del gradiente:$$d = \frac{\nabla y[p]}{\nabla x[p]}$$
2) Calcoliamo ora il modulo del gradiente in $p1$ e $p2$ ,ovvero i pixel vicini seguendo l'orientamento del gradiente)

$$||\nabla[p_1]|| \cong d \cdot ||\nabla[p_B]|| + (1 - d) \cdot ||\nabla[p_A]||$$
$$||\nabla[p_2]|| \cong d \cdot ||\nabla[p_F]|| + (1 - d) \cdot ||\nabla[p_E]||$$
	In questo modo usiamo $d$ come peso per i due pixel _reali_


3)  Il pixel $p$ viene mantenuto **solo se** è un massimo locale, ovvero: $$||\nabla[p]|| \geq ||\nabla[p_1]|| \quad \wedge \quad ||\nabla[p]|| \geq ||\nabla[p_2]||$$
---
## 4. Selezione dei bordi significativi mediante isteresi


In questo momento quindi abbiamo i bordi evidenziati in base al modulo del gradiente in quei precisi pixel, e possiamo notare come alcuni siano più marcati e altri no. 
 
 ![[Pasted image 20260403235947.png|230]]

> [!question] Non basta mettere una soglia?
> **No**, poichè il rumore può portare a dei bordi non voluti

> [!important] Isteresi
> Scegliamo due soglie $T_1$ e $T_2$ con $T_1 < T_2$
> - sono inizialmente considerati validi solo i pixel in cui il modulo del gradiente è superiore a $T_1$
> - i pixel il cui modulo è inferiore a $T_1$ ma superiore a $T_2$ sono considerati validi solo se adiacenti a pixel validi



``` Python
# Caricamento immagine
img = cv.imread('filtri/thunderbirds.jpg')
# Smooth gaussiano con filtro sxs e sigma calcolato da OpenCV
blurred = cv.GaussianBlur(cv.cvtColor(img, cv.COLOR_BGR2GRAY), (s, s), 0)
# Algoritmo di Canny con soglie t1 e t2
edges = cv.Canny(blurred, t1, t2)
img_e = img.copy()
img_e[edges!=0] = (0,255,255)
```
![[Pasted image 20260404000835.png]]

