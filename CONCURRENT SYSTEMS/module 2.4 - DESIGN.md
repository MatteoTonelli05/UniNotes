# GUIDELINES FOR DESIGNING CONCURRENT PROGRAMS

## Step 1: Analisi

> [!abstract] Prima di scrivere qualsiasi riga di codice o scegliere la struttura del programma, devi capire come scomporre il problema.

1. **Decomposizione dei Task e dei Dati** - si spezzetta il problema per renderlo più gestibile

2. **Identificare le dipendenze** - Capire quali compiti possono andare in parallelo e quali in sequenza.

> [!hint] Come si può decomporre i task?
> Si può dividere per **task** (es. "mentre un componente scarica un file, un altro processa un’immagine") o per **dati** (es. "ho un array di 1 milione di elementi, ne do un quarto a testa a 4 processi diversi").

## Step 2: La Progettazione

> [!abstract] Una volta definiti i task, devi passare all'architettura concreta del programma, ovvero CHI fa COSA e COME si coordinano.

1. 

