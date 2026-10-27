## Tipi di dati
### Principali tipi primitivi
i tipi primitivi di Numpy corrispondono **sostanzialmente** a quelli disponibili in C

|**Tipo NumPy**|**Equivalente C**|**Note**|
|---|---|---|
|`np.byte`|signed char|Dipendente dalla piattaforma|
|`np.ubyte`|unsigned char|Dipendente dalla piattaforma|
|`np.short`|signed short|Dipendente dalla piattaforma|
|`np.ushort`|unsigned short|Dipendente dalla piattaforma|
|`np.intc`|signed int|Dipendente dalla piattaforma|
|`np.uintc`|unsigned int|Dipendente dalla piattaforma|
|`np.int_`|signed long|Dipendente dalla piattaforma|
|`np.uint`|unsigned long|Dipendente dalla piattaforma|
|`np.longlong`|long long|Dipendente dalla piattaforma|
|`np.ulonglong`|unsigned long long|Dipendente dalla piattaforma|
|`np.half`|-|Float a 16 bit (mezza precisione)|
|`np.single`|float|Singola precisione|
|`np.double`|double|Doppia precisione|
|`np.longdouble`|long double|Precisione estesa|
|`np.csingle`|single complex|Num. complesso prec. singola|
|`np.cdouble`|double complex|Num. complesso prec. doppia|
|`np.clongdouble`|long double complex|Num. complesso prec. estesa|

> [!attention] Molti dei tipi numerici primitivi NumPy dipendono dalla piattaforma
> Ad esempio in Windows np.int_ (come il corrispondente tipo C long) è a 32 bit, mentre su altre piattaforme è a 64 bit

> [!info] Come si risolve questo problema?
> Per evitare potenziali problemi, sono disponibili **alias** per i tipi NumPy che hanno un numero di bit prefissato e **indipendente dalla piattaforma**

### Alias

|**Tipo NumPy**|**Equivalente C (stdint.h)**|**Dimensione**|**Range / Descrizione**|
|---|---|---|---|
|`np.int8`|`int8_t`|1 byte|[-128, 127]|
|`np.uint8`|`uint8_t`|1 byte|[0, 255]|
|`np.int16`|`int16_t`|2 byte|[-32768, 32767]|
|`np.uint16`|`uint16_t`|2 byte|[0, 65535]|
|`np.int32`|`int32_t`|4 byte|[-2147483648, 2147483647]|
|`np.uint32`|`uint32_t`|4 byte|[0, 4294967295]|
|`np.int64`|`int64_t`|8 byte|[-9.22e18, 9.22e18]|
|`np.uint64`|`uint64_t`|8 byte|[0, 1.84e19]|
|`np.float32`|`float`|4 byte|Singola precisione|
|`np.float64`|`double`|8 byte|Doppia precisione (Standard Python `float`)|
|`np.complex64`|`float complex`|8 byte|Parte reale/immaginarie a 32 bit|
|`np.complex128`|`double complex`|16 byte|Parte reale/immaginarie a 64 bit (Standard Python `complex`)|
