> [!quote]
>In un mondo 3D ci serve recuperare delle immagini 2D per lavorarci

1. Il processo inizia con un **Oggetto** che riflette (o emette) **Raggi Luminosi**.
2. Questi raggi viaggiano verso lo **strumento ottico** (occhio o fotocamera)
3. la **Lente** converge i raggi luminosi in un unico punto preciso per creare un'immagine nitida
4. La luce colpisce il **Sensore** (pellicola o retina) che registra l'immagine

![[Pasted image 20260317161455.png|180]]

> [!info] Perchè l'immagine è capovolta?
> Questo accade a causa del modo in cui le lenti convesse rifrangono la luce: i raggi provenienti dall'alto vengono deviati verso il basso e viceversa.

### Immagine Virtuale

È equivalente (e può essere più comodo) considerare l'immagine (non rovesciata), simmetrica rispetto al foro.
![[Pasted image 20260317162219.png|407]]


## Foro vs Lente

- il foro **seleziona** solo un raggio di luce (scartando tutti gli altri per evitare sfocature)
- la lente **converge** molti raggi in un unico punto corrispondente sul piano immagine (sensore)

> [!attention] L'importanza della grandezza del buco
> **Foro troppo piccolo** $\rightarrow$ Le onde di luce rimbalzano $\rightarrow$ Diffrazione 
> ![[Pasted image 20260317163327.png]]
> 
> **Foro troppo grande** $\rightarrow$ Passa troppa luce $\rightarrow$ Sfocatura
> ![[Pasted image 20260317163311.png]]

### I vantaggi della soluzione con lente

1. La lente "cattura" una porzione maggiore di luce diffusa dall'oggetto, rendendo l'immagine molto più luminosa **evitando diffrazione e sfocature**.
2. Grazie alla rifrazione, tutti i raggi che colpiscono la superficie della lente vengono indirizzati verso il **fuoco**.

> [!info] Per precisione si usa una **Lente Sottile**

## Analizziamo la geometria della lente sottile

>[!info] Cosa sono i fuochi $F_l$ e $F_r$?
>Due punti esterni al corpo della lente situati sull’asse ottico a una certa distanza dal centro 𝑂. 
>
>_Assumiamo per semplicità che 𝐹𝑙 e 𝐹𝑟 abbiano la stessa distanza 𝑓 da 𝑂 
>**(lunghezza focale)**._

![[Pasted image 20260320100302.png|458]]

### Proprietà delle lenti sottili:

- ogni raggio di luce che entra parallelamente all’asse ottico è deviato verso l'altro fuoco
	( $\overline{PR}$ viene deviato a $\overline{RF_rP}$ )
	
- ogni raggio che entra da un lato passando per il fuoco esce parallelamente all’asse ottico 
	( $\overline{PF_lS}$ viene deviato a $\overline{SP}$ )
	
- ogni raggio che passa dal centro della lente mantiene la sua direzione
	( $\overline{QF_lO}$ non viene deviato e continua dritto  )

![[Pasted image 20260320100428.png]]

> [!attention] **Equazione fondamentale delle lenti sottili**
> Date le distanze $\tilde{Z}$ fra lente e oggetto e $\tilde{z}$ fra lente e immagine: si ha: 
>$$ \frac{1}{\tilde{Z}} + \frac{1}{\tilde{z}} = \frac{1}{f} $$ 
> Da questo deriva che: se un oggetto è posto a distanza $\tilde{Z}$ sull'asse di una lente con lunghezza focale f, su uno schermo posto a distanza $\tilde{z}$ si formerà l'immagine dell'oggetto.

### Profondità di campo (Depth Of Field)

 > [!info] Perché non tutto è a fuoco?
 > In un sistema ottico ideale, quando metti a fuoco un oggetto a una precisa distanza, solo quel "piano" infinito è matematicamente nitido; ma l'occhio umano ha una certa tolleranza.
 
La profondità di campo è proprio quello spazio fisico in cui la sfocatura è così leggera da risultare invisibile ai nostri occhi. (chiamato **circolo di confusione**)
 ![[Pasted image 20260320120239.png]]

Minore sarà l'apertura del **Diaframma** (Iris), maggiore sarà la profondità di campo

![[Pasted image 20260320120344.png]]

