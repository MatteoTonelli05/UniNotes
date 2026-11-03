# Petri Nets
## Introduzione e Concetti Base

> [!definition] Definizione
> Le **Reti di Petri** (introdotte da Carl Adam Petri attorno al 1965) costituiscono un modello matematico e grafico per descrivere e analizzare il flusso di informazioni e di controllo in sistemi distribuiti, asincroni e concorrenti.

> [!question] Cosa cambia da **FSP/LTS**?
> Le Reti di Petri non si basano sull'interleaving per modellare la concorrenza, ma rappresentano direttamente le relazioni di dipendenza causale e di indipendenza tra eventi.
### Struttura Grafica (Posti, Transizioni e Archi)

Una Rete di Petri è rappresentata graficamente come un **grafo orientato bipartito** composto da due tipi distinti di nodi:

1. **POSTI (PLACES)** $\quad \rightarrow \quad$ Rappresentano le condizioni, gli stati locali o le risorse del sistema. *Rappresentati dai cerchi $(p_1,p_2,...)$.*

2. **TRANSIZIONI (TRANSITION)** $\quad \rightarrow \quad$ Rappresentano gli eventi o le azioni che modificano lo stato del sistema. *Rappresentate dalle barre/rettangoli $(t_1,t_2,...)$.*
![[Pasted image 20261009170354.png|290]]
> [!error] Archi Orientati (Directed Arcs)
> - Connettono nodi di **tipo diverso**
> - **NON** esistono archi diretti tra nodi di stesso tipo

### Token e Marca (Marking)

La dinamica di una Rete di Petri viene espressa mediante l'uso dei **Token**:

- **TOKEN (MARCHE)** $\quad \rightarrow \quad$ Indicano quali condizioni sono attualmente verificate o quante risorse sono disponibili in un determinato istante. *Rappresentati da pallini neri.*

- **MARCA (MARKING)** $\quad \rightarrow \quad$ La distribuzione globale dei token all'interno di tutti i posti della rete definisce il **Marking** (la marca), che corrisponde allo **stato globale istantaneo** della Rete di Petri.
![[Pasted image 20261009172621.png]]
## Dinamica e Regole di Esecuzione

L'evoluzione dello stato (il cambio di marca) in una Rete di Petri avviene attraverso il movimento dei token generato dallo **scatto (firing)** delle transizioni.
### Abilitazione e Scatto (Firing)

> [!question] A cosa servono i token?
> Una transizione $t$ è abilitata ed è pronta a scattare se **ciascuno dei suoi posti in input possiede almeno un token**.

![[Pasted image 20261009174323.png]]
> [!hint] Scatto (Firing)
> Quando una transizione scatta, compie un'azione ATOMICA e ISTANTANEA
> 1. Rimuove un token da **ognuno** dei suoi posti in input
> 2. Deposita un nuovo token in **ognuno** dei suoi posti in output
### Transizioni al Limite (Source e Sink)
![[Pasted image 20261009174721.png|400]]
### Archi Pesati

In una variante molto usata (weighted PetriNets), ad ogni arco orientato viene associato un numero intero positivo detto **peso dell'arco** $w(p,t)$ o $w(t,p)$:

- **Abilitazione con peso**: Una transizione $t$ è abilitata solo se ogni posto in input $p$ contiene **almeno $w(p,t)$ token**.
    
- **Scatto con peso**: Lo scatto rimuove esattamente $w(p,t)$ token da ciascun posto in input $p$ e aggiunge $w(t,p)$ token in ciascun posto in output $p$.
    
- _Esempio di reazione chimica_: $2H_2 + O_2 \rightarrow 2H_2O$ si modella ponendo peso 2 sull'arco da $H_2$ alla transizione e peso 2 sull'arco dalla transizione a $H_2O$.
![[Pasted image 20261009174854.png]]
## Modellazione di Sistemi Concorrenti
### Interpretazioni di Posti e Transizioni

![[Pasted image 20261009175950.png]]
### Parallelismo e Sincronizzazione

Le Reti di Petri sono ideali per modellare sistemi a controllo distribuito con molteplici processi che si verificano contemporaneamente:
![[Pasted image 20261009180019.png|465]]
### Conflitto, Scelta e Confusione

Nelle Reti di Petri possono verificarsi diverse relazioni strutturali tra gli eventi
##### 1. Eventi in Conflitto
Due transizioni $t_1$ e $t_2$ si dicono in **conflitto** se condividono lo stesso posto in input $p_1$. Se $p_1$ contiene un solo token, l'attivazione di $t_1$ disabilita $t_2$ (e viceversa). Può scattare una sola delle due transizioni.
![[Pasted image 20261009180654.png]]
##### 2. Eventi Concorrenti
Due eventi $e_1$ ed $e_2$ sono **concorrenti** se entrambi possono avvenire in qualsiasi ordine (o contemporaneamente) senza che l'uno disabiliti l'altro.
##### 3. Confusione
Si verifica quando in una rete **conflitto e concorrenza sono combinati insieme**, rendendo l'esito del conflitto dipendente dall'ordine di scatto di una transizione concorrente

![[Pasted image 20261009180705.png]]

> [!question] Come si fa a parlare di tempo se non esiste un "clock globale"?
> L'unica proprietà temporale rilevante è l'**ordinamento parziale degli eventi**: due eventi vengono vincolati nel tempo solo se esiste una dipendenza causale diretta tra di essi.

> [!hint] Principio di località
> Ogni sotto-parte di un sistema complesso può essere modellata da una Rete di Petri locale. L'abilitazione e lo scatto di una transizione dipendono **esclusivamente dallo stato locale** (la marca dei posti in input) e modificano solo lo stato dei posti direttamente connessi ad essa.


> [!attention] Non-Determinismo
> Nelle reti di petri la scelta di quale transizione far scattare viene effettuata in modo non deterministico (casualmente o da forze non modellate)

## Aspetti Avanzati di Modellazione
### Eventi Atomici vs Non-Atomici

_Il verificarsi di eventi (primitivi) è **istantaneo**, gli eventi non primitivi devono essere modellati tramite eventi primitivi multipli_
![[Pasted image 20261012115223.png|445]]
### Gerarchie (Astrazione e Raffinamento)

ASTRAZIONE $\quad \rightarrow \quad$ rimpiazzare un'intera rete con un singolo stato
RAFFINAMENTO $\quad \rightarrow \quad$ rimpiazzare un singolo stato con una rete dettagliata
![[Pasted image 20261012115753.png]]