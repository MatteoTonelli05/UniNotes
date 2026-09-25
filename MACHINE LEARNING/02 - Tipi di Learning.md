# Tipi di Apprendimento

## In base alle etichette

> [!abstract] Come sono fatti i dati che diamo al modello?

- **Supervisionato:** sono note le classi dei pattern utilizzati per l'addestramento 
	(*training set etichettato*)

- **Non Supervisionato:** NON sono note le classi dei pattern utilizzati per l’addestramento (*training set non etichettato*)

- **Semi-Supervisionato:** il *training set* è etichettato parzialmente

> [!attention] la distribuzione dei pattern non etichettati può aiutare a ottimizzare la regola di classificazione.
> ![[Pasted image 20260921142613.png]]

## In base alle tempistiche

> [!abstract] Quando e quante volte apprende il modello?

- **Batch:** l’addestramento è effettuato **una sola volta** su un training set dato, dopodichè il modello è in _working mode_

- **Incrementale:** seguito dell’addestramento iniziale, sono possibili ulteriori sessioni di addestramento.

- **Naturale:** addesstramento continuo anche durante la _working mode_

> [!attention] Nell'esempio di un addestramento Incrementale si rischia il **Catastrofic Forgetting**, una situazione in cui il sistema dimentica quello che ha appreso in precedenza

## Reinforcement Learning (RL)

> [!attention] A differenza dei tipi di apprendimento già elencati precedentemente, il RL non c'è un dataset statico.

> Un agente esegue azioni che modificano l’ambiente, provocando passaggi da uno stato all’altro. Quando l’agente ottiene risultati positivi riceve una ricompensa (**reward**) che però può essere temporalmente ritardata rispetto all’azione, o alla sequenza di azioni, che l’hanno determinata.

![[Pasted image 20260921150539.png|358]]