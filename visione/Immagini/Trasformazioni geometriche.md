## Coordinate
dobbiamo pensarle come numeri reali, è possibile "inserirsi tra i singoli pixel"
$$\mathbb{I}: \mathbb{R} \times \mathbb{R} \times \mathbb{N} \rightarrow \mathbb{R}$$
- l'insieme $\mathbb{N}$ sono i canali (profondità)

![[Pasted image 20260317094245.png|238]]

---

# Trasformazioni geometriche
Si tratta di trasformazioni che si applicano alle coordinate e non ai valori dei pixel.
- La griglia dei pixel viene deformata e mappata sull'immagine di destinazione

**Funzione di mapping:** $g: \mathbb{R} \times \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R}$

![[Pasted image 20260317105414.png|490]]
 
**Mapping inverso:** $f: \mathbb{R} \times \mathbb{R} \rightarrow \mathbb{R} \times \mathbb{R}, f=g^{-1}$

![[Pasted image 20260317105608.png|496]]

> [!attention] Problemi
> 1. Gestire le coordinate fuori dall'immagine sorgente (non esistenti)?
> 2. Quale valore del pixel quando $f(x,y)$ non restituisce coordinate intere?

## Gestire le coordinate fuori dall'immagine

 Gli approcci più comuni per estrapolare il valore (con denominazione OpenCV):
 - Colore di background costante (`cv.BORDER_CONSTANT`)
-  Copia del pixel più vicino (`cv.BORDER_REPLICATE`)
-  Riflessione dei pixel dell'immagine (`cv.BORDER_REFLECT`)
-  Replica dell'immagine (`cv.BORDER_WRAP`)
-  Nessuna modifica: lascia il valore già presente nell'immagine destinazione (`cv.BORDER_TRANSPARENT`)
- 
![[Pasted image 20260317105934.png]]

## Gestire le coordinate NON intere
### Stimare un valore per il pixel da coordinate non intere
Nel caso in cui le coordinate dei valori da recuperare nell'immagine sorgente siano **numeri con la virgola**, si procede in diverso modi:
- si sceglie il pixel più vicinio (**nearest-neighbor**)
- si prende **stimando** il valore dai pixel più vicini tramite **interpolazione**

![[Pasted image 20260317110616.png]]

> [!info] 
> **Bilineare** e **Bicubica** sono due metodi di interpolazione.
> 
> La **Bicubica** è una media pesata dei 16 vicini pesati per la vicinanza al punto preso in considerazione.

---
## Ingrandimento di un'immagine

è un caso particolare di **trasformazione affine** 

``` Python
img = cv.imread('esempi/study.png')
h, w = img.shape[:2]
scale = 7.5 # Fattore di scala
size = (int(w*scale),int(h*scale)) # size è una Tupla (x, y)
img_nn = cv.resize(img, size, interpolation=cv.INTER_NEAREST)
img_bl = cv.resize(img, size, interpolation=cv.INTER_LINEAR)
img_bc = cv.resize(img, size, interpolation=cv.INTER_CUBIC)
```

Cosa cambia?
- **Nearest-neighbor** è più veloce
- **Lineare/Bilineare** è un buon compromesso tra qualità e tempo
- **Bicubica** è più lenta ma con maggiore qualità

## Rimpicciolire un'immagine

> [!attention]
> Quando si rimpicciolisce **molto** un immagine NON si usa Biineare o Bicubica.

In questi casi in OpenCV si può utilizzare **INTER_AREA** che considera tutti i pixel coinvolti.

> [!info] Perchè?
> Ogni pixel dell'immagine destinazione corrisponde a 100 pixel della sorgente (rimpicciolimento x100), mentre l'interpolazione bilineare ne considera solo 4 e la bicubica solo 16.


