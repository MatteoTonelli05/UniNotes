## Operatori Aritmetici

Questi operatori vengono utilizzati per eseguire le operazioni matematiche comuni.

| **Operatore** | **Nome**                          | **Esempio** |
| ------------- | --------------------------------- | ----------- |
| `+`           | Addizione                         | `x + y`     |
| `-`           | Sottrazione                       | `x - y`     |
| `*`           | Moltiplicazione                   | `x * y`     |
| `/`           | Divisione                         | `x / y`     |
| `%`           | Modulo (Resto)                    | `x % y`     |
| `**`          | Elevamento a Potenza              | `x ** y`    |
| `//`          | Divisione Intera (Floor division) | `x // y`    |

---
## Operatori di Assegnamento

Usati per assegnare valori alle variabili, spesso combinando un'operazione aritmetica con l'assegnazione.

| **Operatore** | **Esempio** | **Equivalente a...** |
| ------------- | ----------- | -------------------- |
| `=`           | `x = 5`     | `x = 5`              |
| `+=`          | `x += 3`    | `x = x + 3`          |
| `-=`          | `x -= 3`    | `x = x - 3`          |
| `*=`          | `x *= 3`    | `x = x * 3`          |
| `/=`          | `x /= 3`    | `x = x / 3`          |
| `%=`          | `x %= 3`    | `x = x % 3`          |
| `//=`         | `x //= 3`   | `x = x // 3`         |
| `**=`         | `x **= 3`   | `x = x ** 3`         |
| `&=`          | `x &= 3`    | `x = x & 3`          |
| `             | =`          | `x                   |
| `^=`          | `x ^= 3`    | `x = x ^ 3`          |
| `>>=`         | `x >>= 3`   | `x = x >> 3`         |
| `<<=`         | `x <<= 3`   | `x = x << 3`         |

---
## Operatori di Confronto

Utilizzati per confrontare due valori. Il risultato è sempre un Booleano (`True` o `False`).

|**Operatore**|**Nome**|**Esempio**|
|---|---|---|
|`==`|Uguale|`x == y`|
|`!=`|Diverso|`x != y`|
|`>`|Maggiore di|`x > y`|
|`<`|Minore di|`x < y`|
|`>=`|Maggiore o uguale a|`x >= y`|
|`<=`|Minore o uguale a|`x <= y`|

---
## Operatori Logici, di Identità e Appartenenza

Questi operatori servono a combinare condizioni o a verificare relazioni tra oggetti e sequenze.
### Logici

|**Operatore**|**Descrizione**|**Esempio**|
|---|---|---|
|`and`|Vero se entrambe le affermazioni sono vere|`x < 5 and x < 10`|
|`or`|Vero se almeno una delle affermazioni è vera|`x < 5 or x < 4`|
|`not`|Inverte il risultato (Vero diventa Falso)|`not(x < 5)`|

### Identità e Appartenenza

| **Operatore** | **Descrizione**                                      | **Esempio**  |
| ------------- | ---------------------------------------------------- | ------------ |
| `is`          | Vero se le variabili puntano allo **stesso oggetto** | `x is y`     |
| `is not`      | Vero se le variabili puntano a oggetti diversi       | `x is not y` |
| `in`          | Vero se il valore è presente nella sequenza          | `x in y`     |
| `not in`      | Vero se il valore **non** è presente nella sequenza  | `x not in y` |

---
## Operatori Bitwise (Bit-a-bit)

Operano direttamente sulla rappresentazione binaria dei numeri.

| **Operatore** | **Nome**    | **Descrizione**                                  |
| ------------- | ----------- | ------------------------------------------------ |
| `&`           | AND         | Imposta ogni bit a 1 se entrambi i bit sono 1    |
| `             | `           | OR                                               |
| `^`           | XOR         | Imposta ogni bit a 1 se solo uno dei due bit è 1 |
| `~`           | NOT         | Inverte tutti i bit                              |
| `<<`          | Left Shift  | Sposta a sinistra inserendo zeri a destra        |
| `>>`          | Right Shift | Sposta a destra (mantenendo il bit del segno)    |

---
