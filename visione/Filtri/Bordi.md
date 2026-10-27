# Edge detection

L'edge detection si occupa di estrarre i bordi, ovvero la separazione tra l'oggetto e il resto della realtà che lo circonda, da un immagine

> [!info] Cos'è il bordo di un oggetto?
> Se consideriamo un'immagine grayscale $I$ come una funzione 
> $$f:\mathbb{R}\times\mathbb{R}\rightarrow\mathbb{R},\quad con\quad f(x,y)=I[y,x]$$ 
> I bordi sono in corrispondenza dei **cambiamenti rapidi** di $f$

## Difference of Gaussians (DoG)

> [!info] Come abbiamo visto prima
> sottraendo il risultato dello smooth di un'immagine dall'immagine originale, si possono evidenziare le zone caratterizzate da cambiamenti rapidi fra valori chiari e scuri dei pixel

Il **ragionamento** è che il filtro gaussiano riduce le alte frequenze nell'immagine, quindi
- $I*G_\sigma$ contiene le basse frequenze
- $I-(I*G_\sigma)$ contiene le alte frequenze

> [!quote] Dati due filtri 𝐆𝜎𝟏 e 𝐆𝜎𝟐 , la differenza 𝐈 ∗ 𝐆𝜎𝟏 − 𝐈 ∗ 𝐆𝜎2 evidenzia uno specifico range di frequenze nell'immagine
![[Pasted image 20260401170053.png]]
> _ovviamente il risultato deve essere normalizzato_

### Costruzione del filtro DoG

Sappiamo che per trovare la differenza di frequenze si può usare la formula $$(I*G_{\sigma1})-(I*G_{\sigma2})$$ che, grazie alla proprietà distributiva $I*(F+G)=(I*F)+(I*G)$ possiamo scrivere:
$$I*DoG_{\sigma1,\sigma2} \quad con \quad DoG_{\sigma1,\sigma2}=G_{\sigma1}-G_{\sigma2}$$

> [!info] 
> Il filtro può essere dunque ottenuto dalla differenze di due filtri gaussiani
> $$ \large  I*DoG_{\sigma1,\sigma2} \quad con \quad DoG_{\sigma1,\sigma2}=G_{\sigma1}-G_{\sigma2} $$
> 
> ![[Pasted image 20260401171639.png]]

