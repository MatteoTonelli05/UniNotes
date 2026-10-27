Si dicono **non lineari** perché non soddisfano il **principio di sovrapposizione**, che è la base dei filtri lineari (come il filtro di Media o il Gaussiano).

In matematica, un operatore $L$ è lineare se rispetta le proprietà di:

1. **Additività:** $L(f + g) = L(f) + L(g)$
	    _"Se sommi $A$ e $B$ e poi calcoli la mediana, il risultato sarà diverso rispetto a calcolare la mediana di $A$, la mediana di $B$ e poi sommarle."_

2. **Omogeneità:** $L(k \cdot f) = k \cdot L(f)$
    

> [!quote] La non linearità è evidente quando il filtro deve decidere quale valore "vince". In un filtro lineare, ogni pixel contribuisce un po'. Nel mediano, **un solo pixel "vince" e gli altri vengono ignorati**.

---
# Il Filtro Mediano
$$I'(y, x) = \text{median}\{w_{xy}\}$$
## Come funziona?

1. Si definisce un intorno (finestra) attorno al pixel target.
    
2. Si ordinano tutti i valori dei pixel nell'intorno dal più piccolo al più grande.
    
3. Si assegna al pixel centrale il valore che si trova esattamente a metà della lista, ovvero la **mediana**

![[Pasted image 20260405011002.png|625]]
---

## Vantaggi rispetto ai filtri lineari

### Riduzione del Rumore "Impulsivo" (Salt and Pepper)

Il filtro mediano è considerato il migliore contro il rumore **sale e pepe** (quei puntini bianchi e neri isolati).

- **Filtro Lineare (Media/Gauss):** Il pixel bianco (rumore) viene mediato con i vicini, creando una macchia grigia sfocata. Il rumore viene "spalmato" ma non eliminato.
    
- **Filtro Mediano:** Poiché il pixel rumoroso è un valore estremo (molto alto o molto basso), non sarà mai la mediana della finestra. Viene quindi completamente rimpiazzato da un valore "normale" dei vicini, eliminando il rumore senza lasciare tracce.
    

### Preservazione dei Bordi

A differenza dello smooth gaussiano, che sfoca i passaggi netti di intensità, la mediana tende a mantenere i bordi più nitidi perché non mescola i valori di due regioni diverse, ma sceglie il valore più rappresentativo di una delle due.

---

## In OpenCV

Nel codice proposto si nota una differenza fondamentale nella dimensione dei kernel:

``` Python
# Carica un'immagine con rumore nello sfondo (alcuni piccoli punti più chiari)
img = cv.imread('esempi/bolts.png', cv.IMREAD_GRAYSCALE)

# Filtro Gaussiano: richiede un kernel 25x25 per eliminare il rumore
noise_reduction_gaussian_filter = cv.GaussianBlur(img, (25,25), 0)

# Filtro Mediano: ottiene un risultato simile (o migliore) con un kernel solo 9x9
noise_reduction_median_filter = cv.medianBlur(img, 9)
```
![[Pasted image 20260405014507.png]]

> [!quote] Il fatto che il filtro mediano richieda un kernel molto più piccolo ($9 \times 9$ contro $25 \times 25$) per ottenere lo stesso obiettivo conferma la sua maggiore efficienza specifica contro piccoli punti chiari (rumore impulsivo). 

---

### Quando NON usare il filtro mediano?

Nonostante i vantaggi, ha dei limiti:

- **Costo computazionale:** Ordinare i valori per ogni pixel è più lento rispetto a una semplice somma pesata (convoluzione).
    
- **Dettagli fini:** Se l'immagine contiene dettagli molto piccoli (es. linee sottili di 1-2 pixel), il filtro mediano potrebbe scambiarli per rumore ed eliminarli