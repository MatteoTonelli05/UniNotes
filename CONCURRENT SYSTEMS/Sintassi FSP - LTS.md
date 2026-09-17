> [!attention] FSP usa una regola sintattica sui nomi:
> Le **Azioni** (gli eventi/transizioni) iniziano sempre con la **lettera minuscola** (es. `on`, `off`, `tick`).
> I **Processi** (gli stati/comportamenti) iniziano sempre con la **lettera MAIUSCOLA** (es. `SWITCH`, `ON`, `STOP`).

## Main Operator

### Action Prefix

L'operatore `->` (prefisso d'azione) indica una sequenza temporale in cui prima avviene l'azione a sinistra, poi il sistema si comporta come il processo a destra.

```
PROCESSO = (azione -> PROCESSO_SUCCESSIVO).
```
_(Nota: ogni definizione di processo in FSP termina con un punto `.`)_
#### Esempio 1: Processo che termina (`STOP`)

`STOP` è un processo speciale predefinito che rappresenta uno stato finale (nessuna transizione ulteriore).

```
ONESHOT = (once -> STOP).
```

**Cosa significa?** Il processo `ONESHOT` esegue l'azione `once` e poi si ferma (`STOP`).

![[Pasted image 20260917181019.png]]
#### Esempio 2: Processo Ricorsivo (Ciclo Infinito)

Se definiamo un processo richiamando se stesso, otteniamo un comportamento che si ripete all'infinito:

```
SWITCH = OFF,
OFF    = (on -> ON),
ON     = (off -> OFF).
```

Oppure in forma contratta (sostituendo le definizioni):
```
SWITCH = (on -> off -> SWITCH).
```

**Come si legge?**
1. `SWITCH` parte nello stato iniziale (`OFF`).
2. Fa l'azione `on` e va nello stato `ON`.
3. Fa l'azione `off` e torna allo stato iniziale (`SWITCH` / stato `OFF`).

_(nel LTS qui sotto :   OFF = 0 e ON = 1)_
![[Pasted image 20260917180926.png]]

