## Classificatori Nearest Neighbor (NN e k-NN)

> [!abstract] Data un metrica $dist(\cdot)$ nello spazio multidimensionale *(es. distanza euclidea)* il classificatore **nearest neighbor** classifica un pattern $x$ con la stessa classe dell'elemento $x'$ da esso più vicino nel training set $TS$:
> $$dist(x,x') = min_{x_i\in{TS}}{\{dist(x,x_i)\}}$$

> [!question] Come funziona?
> Si presuppone che se $x'$ è molto vicino a $x$: $$P(w_i|x) \approx P(w_i|x')$$

> [!error] Attenzione
> Nonostante si possa **dimostrare** che la probabilità di errore $P$ del NN non è mai peggiore del doppo del minimo errore possibile $P^*$ (quello Bayesiano)
> Nonostante ciò bisogna valutare il contesto.
### Nearest Neighbor (1-NN) e Tassellazione di Voronoi

La regola nearest neighbor produce un partizionamento dello dello spazio, noto come **tassellazione di Voronoi** 
![[Pasted image 20261013152934.png]]
> [!attention] Così facendo ogni elemento determina un tassello all'interno del quale i pattern saranno assegnati alla stessa classe, il problema è che basta un elemento **outlier** affinchè tutta la classificazione vada in vacca
### k-Nearest Neighbor (k-NN) e Confidenza

> [!hint] Come ovviare alla problematica dell'1-NN
> La regola **k-Nearest Neighbor (k-NN)** determina i $k$ elementi più vicini al pattern $𝐱$ da classificare ($k$ è un iperparametro); ogni pattern tra i $k$ vicini vota per la classe cui esso stesso appartiene; il pattern $𝐱$ viene assegnato alla classe che ha ottenuto il maggior numero di voti.
> 
> ![[Pasted image 20261013154508.png]]

Da un classificatore k-NN risulta piuttosto semplice estrarre una confidenza (probabilistica) circa la classificazione eseguita:$$[v_1,v_2,...v_s], \qquad \sum_{i=1}^s{v_i=k}$$
i voti ottenuti dal pattern $𝐱$, allora le confidenze (vedi sfumature in figura sotto) possono essere semplicemente ottenute dividendo per 𝑘 i voti ottenuti:
![[Pasted image 20261013160112.png|341]]
### Complessità Computazionale, Editing e Condensing

> [!error] Per il classificatore NN serve **memorizzare** tutti i pattern del training set e, per ogni classificazione, calcolare la distanza da tutti i pattern e ordinare le distanze per prendere le più piccole.

##### Soluzione 1 :  editing/condensing

##### Soluzione 2 : strutture spaziali

Soluzione 3 : libreria FLANN (C++)