## 🛠️ MODULO OPENCV (`cv`)

### I/O, Visualizzazione e Spazi Colore

- **`cv.imread(filename[, flags])`**: Carica un'immagine da file. Di default la decodifica nello spazio colore **BGR** (Blue, Green, Red) anziché RGB.
    
- **`cv.imwrite(filename, img)`**: Salva un'immagine su file. Il formato di esportazione viene dedotto automaticamente dall'estensione (es. `.png`, `.jpg`).
    
- **`cv.cvtColor(src, code)`**: Converte lo spazio colore dell'immagine.
    
	    - _Codici usati:_ `cv.COLOR_BGR2GRAY` (scala di grigi), `cv.COLOR_BGR2HSV` (Tonalità/Saturazione/Valore), `cv.COLOR_GRAY2BGR` (ricrea 3 canali identici da un grigio per scriverci sopra a colori).
        
- **`cv.split(m)`**: Divide un'immagine multicanale nei suoi canali singoli isolati (es. `b, g, r = cv.split(img)` o `h, s, v = cv.split(hsv_img)`).
    
- **`cv.merge(mv)`**: Combina una lista o tupla di matrici a canale singolo in un'unica immagine multicanale (es. `cv.merge([b, g, r])`).
    
- **`cv.copyMakeBorder(src, top, bottom, left, right, borderType[, value])`**: Estende un'immagine aggiungendo un bordo attorno al perimetro. Utilizzato per la gestione dei pixel di bordo prima di applicare grafici affiancati o filtri spaziali (`borderType=cv.BORDER_CONSTANT` con un valore di intensità fisso o `cv.BORDER_REPLICATE`).
    

### Soglia, Binarizzazione e Segmentazione

- **`cv.threshold(src, thresh, maxval, type)`**: Applica una soglia fissa globale a un'immagine a canale singolo.
    
    - _Tipi:_ `cv.THRESH_BINARY` (se $> \text{thresh} \to \text{maxval}$, altrimenti $0$), `cv.THRESH_BINARY_INV` (inverte il comportamento).
        
    - _Ottimizzazione:_ `cv.THRESH_OTSU` (da sommare bit a bit con i precedenti, es. `cv.THRESH_BINARY | cv.THRESH_OTSU`). Calcola automaticamente la soglia ideale analizzando il bimodalismo dell'istogramma.
        
- **`cv.adaptiveThreshold(src, maxValue, adaptiveMethod, thresholdType, blockSize, C)`**: Calcola una soglia locale e dinamica per ogni pixel basandosi sul suo vicinato di dimensione `blockSize`. Risolve i problemi di illuminazione non uniforme.
    
    - _Metodi:_ `cv.ADAPTIVE_THRESH_MEAN_C` (media aritmetica del vicinato), `cv.ADAPTIVE_THRESH_GAUSSIAN_C` (media pesata gaussiana).
        
- **`cv.inRange(src, lowerb, upperb)`**: Genera una maschera binaria (`uint8`) isolando i pixel i cui canali si trovano contemporaneamente all'interno dell'intervallo $[ \text{lowerb}, \text{upperb} ]$. Utilizzato nello spazio HSV per isolare oggetti per colore in modo robusto.
    

### Analisi Statistica e Istogrammi

- **`cv.calcHist(images, channels, mask, histSize, ranges)`**: Calcola l'istogramma di distribuzione delle frequenze dei pixel.
    
    - _Argomenti:_ `images` (lista di immagini, es. `[img]`), `channels` (indici dei canali, es. `[0]`), `mask` (opzionale, per calcolarlo solo su una ROI), `histSize` (numero di bin, es. `[180]` o `[256]`), `ranges` (limiti di valore, es. `[0, 180]` per la coordinata H o `[0, 256]` per i pixel standard).
        
- **`cv.normalize(src, dst, alpha, beta, norm_type[, dtype])`**: Riscala i valori di un array o di un istogramma all'interno di un intervallo di sicurezza. Nel tracking e nei filtri si usa `cv.NORM_MINMAX` con `alpha=0` e `beta=255` per mappare i valori decimali o frequenziali in un formato immagine visualizzabile (`dtype=cv.CV_8U`).
    

### Calcolo dei Gradienti e Rilevamento dei Bordi

- **`cv.spatialGradient(src[, ksize])`**: Calcola istantaneamente i gradienti derivati dell'immagine del primo ordine usando l'operatore di Sobel su una finestra `ksize` (default=3). Restituisce una coppia di matrici `(dx, dy)` memorizzate come interi a 16 bit con segno (`int16`) per evitare l'underflow nei gradienti negativi.
    
- **`cv.Sobel(src, ddepth, dx, dy[, ksize])`**: Calcola la derivata parziale lungo una specifica direzione spaziale. `dx=1, dy=0` estrae le variazioni orizzontali (bordi verticali); `dx=0, dy=1` estrae le variazioni verticali (bordi orizzontali). `ddepth` specifica la profondità dell'output (es. `cv.CV_16S` o `cv.CV_32F`).
    
- **`cv.Canny(image, threshold1, threshold2[, apertureSize[, L2gradient]])`**: Estrattore di bordi multistadio ottimale. Esegue una sfocatura, calcola i gradienti, sopprime i non-massimi locali e applica una sogliatura con isteresi usando `threshold1` (soglia minima di aggancio) e `threshold2` (soglia massima di innesco). Restituisce un'immagine binaria pulita.
    

### Filtri Spaziali e Convoluzioni

	- **`cv.filter2D(src, ddepth, kernel)`**: Convoluzione lineare bidimensionale personalizzata. Applica la matrice bidimensionale float `kernel` sull'immagine `src`.
    
- **`cv.GaussianBlur(src, ksize, sigmaX[, sigmaY])`**: Applica un filtro di sfocatura (low-pass spaziale) usando un nucleo Gaussiano di dimensione `ksize` (deve essere dispari, es. `(5,5)`) e deviazione standard `sigmaX`. Elimina il rumore ad alta frequenza.
    
- **`cv.medianBlur(src, ksize)`**: Filtro non lineare che sostituisce ogni pixel con la mediana dei pixel nel vicinato quadrato `ksize`. È l'operatore ideale per rimuovere il rumore impulsivo di tipo "sale e pepe".
	
- **`cv.boxFilter(src, ddepth, ksize[, normalize, borderType]):`** Sfoca l'immagine calcolando la somma dei pixel in una finestra `ksize=(w,h)`. Se `normalize=True` (default), agisce come un filtro media aritmetica (`cv.blur`). Se `normalize=False`, calcola la somma pura non normalizzata, utile per accumulare intensità locali in algoritmi avanzati senza cicli lenti. Per evitare l'overflow con la somma pura, si imposta un `ddepth` capiente (es. `cv.CV_32F`).

### Morfologia Matematic

- **`cv.getStructuringElement(shape, ksize)`**: Configura l'elemento strutturante (kernel morfologico) di dimensione `ksize`.
    
    - _Forme (`shape`):_ `cv.MORPH_RECT` (rettangolare), `cv.MORPH_ELLIPSE` (ellittico, perfetto per conservare le forme tondeggianti), `cv.MORPH_CROSS` (a croce).
        
- **`cv.morphologyEx(src, op, kernel)`**: Esegue operazioni morfologiche geometriche su immagini binarie o in scala di grigi.
    
    - _Operatori (`op`):_ * `cv.MORPH_ERODE` (Erosione: consuma i bordi del foreground).
        
        - `cv.MORPH_DILATE` (Dilatazione: espande i bordi del foreground).
            
        - `cv.MORPH_OPEN` (Apertura: Erosione $\to$ Dilatazione. Rimuove piccoli dettagli di rumore bianco isolati).
            
        - `cv.MORPH_CLOSE` (Chiusura: Dilatazione $\to$ Erosione. Salda interruzioni e chiude piccoli buchi neri all'interno delle forme).
            

### Componenti Connesse, Contorni e Geometria delle Forme

- **`cv.connectedComponentsWithStats(image)`**: Analizza un'immagine binaria raggruppando i pixel attivi in blob isolati. Restituisce una tupla di 4 elementi: `(num_labels, labels, stats, centroids)`.
    
    - `labels` è una mappa di interi dove ogni pixel contiene l'ID della propria componente.
        
    - `stats` è una matrice in cui si accede alle proprietà geometriche tramite costanti dedicate: `cv.CC_STAT_LEFT`, `cv.CC_STAT_TOP`, `cv.CC_STAT_WIDTH`, `cv.CC_STAT_HEIGHT`, `cv.CC_STAT_AREA` (Area in pixel).
        
- **`cv.findContours(image, mode, method)`**: Estrae le catene dei vettori di contorno degli oggetti presenti nella matrice binaria.
    
    - _Modalità (`mode`):_ `cv.RETR_EXTERNAL` (isola solo i contorni più esterni), `cv.RETR_TREE` (ricostruisce l'intera gerarchia di nidificazione dei contorni).
        
    - _Metodo (`method`):_ `cv.CHAIN_APPROX_SIMPLE` (comprime i segmenti orizzontali, verticali e diagonali salvando solo i vertici estremi per risparmiare memoria).
        
- **`cv.drawContours(image, contours, contourIdx, color[, thickness, lineType])`**: Disegna i vettori di contorno geometrici direttamente sopra un'immagine. Se `contourIdx=-1`, vengono disegnati tutti i contorni della lista.
    
- **`cv.contourArea(contour)`**: Calcola l'area racchiusa dal contorno tramite la formula di Green (analisi poligonale).
    
- **`cv.arcLength(contour, closed)`**: Restituisce la lunghezza totale del perimetro del contorno. Il booleano `closed` specifica se il profilo è chiuso.
    
- **`cv.minEnclosingCircle(contour)`**: Calcola la circonferenza minima che racchiude completamente il contorno, restituendo il centroide `(x,y)` float e il raggio `radius`.
    

### Trasformazioni Geometriche e Posizionamento

- **`cv.resize(src, dsize[, fx, fy, interpolation])`**: Cambia la risoluzione spaziale dell'immagine in base a una dimensione fissa `dsize=(larghezza, altezza)` o a fattori di scala decimali `fx` e `fy`.
    
- **`cv.warpAffine(src, M, dsize)`**: Applica una trasformazione geometrica affine (rotazione, traslazione, riscalamento) basata su una matrice di trasformazione $2 \times 3$ `M`.
    

### Localizzazione e Inseguimento (Tracking e Matching)

- **`cv.matchTemplate(image, templ, method)`**: Esegue uno scorrimento a finestra del template `templ` sulla mappa `image`. Genera una matrice float in cui ogni cella indica il grado di somiglianza.
    
    - _Metodi normalizzati stabili:_ `cv.TM_CCOEFF_NORMED` (coefficiente di correlazione) o `cv.TM_SQDIFF_NORMED` (differenza quadratica).
        
- **`cv.minMaxLoc(src)`**: Scansiona una matrice a canale singolo restituendo i valori estremi e le loro esatte coordinate spaziali: `min_val, max_val, min_loc, max_loc`. Nei metodi normalizzati di Template Matching, `max_loc` indica la posizione esatta della corrispondenza superiore (o `min_loc` in caso di `SQDIFF`).
    
- **`cv.calcBackProject(images, channels, hist, ranges, scale)`**: Calcola la retroproiezione dell'istogramma. Genera una mappa di probabilità ad un canale in cui l'intensità di ogni pixel è direttamente proporzionale alla frequenza del suo colore nell'istogramma di riferimento `hist`.
    
- **`cv.meanShift(probImage, window, criteria)`**: Algoritmo di tracking non parametrico. Sposta iterativamente una finestra di ricerca `window=(x, y, w, h)` verso l'area a maggiore densità di probabilità (massa dei pixel luminosi) all'interno di `probImage`. Restituisce il numero di iterazioni effettuate e la nuova finestra aggiornata.
    
    - _Criteri:_ `criteria = (cv.TERM_CRITERIA_EPS | cv.TERM_CRITERIA_COUNT, 10, 1)` (si arresta dopo 10 iterazioni o se lo spostamento del centroide è inferiore a 1 pixel).
        

### Calibrazione della Telecamera e Geometria 3D

- **`cv.findChessboardCorners(image, patternSize)`**: Cerca i punti di intersezione interni di un pattern a scacchiera di dimensioni `patternSize=(colonne_interne, righe_interne)`. Restituisce un booleano di successo e la matrice dei punti geometrici 2D scoperti.
    
- **`cv.calibrateCamera(objectPoints, imagePoints, imageSize, None, None)`**: Calcola i parametri ottici geometrici della telecamera. Riceve le coordinate 3D del mondo reale della scacchiera (`objectPoints`) e le coordinate 2D stimate sui frame (`imagePoints`). Restituisce l'errore di riproiezione RMS, la matrice intrinseca $3 \times 3$ (`cameraMatrix` o `mtx`), i coefficienti di distorsione della lente (`dist`), e i vettori di rototraslazione estrinseci (`rvecs`, `tvecs`) per ogni vista.
    
- **`cv.undistort(src, cameraMatrix, distCoeffs)`**: Corregge le distorsioni radiali e tangenziali introdotte dalle lenti fisiche della telecamera, raddrizzando le linee curve.
    
- **`cv.projectPoints(objectPoints, rvec, tvec, cameraMatrix, distCoeffs)`**: Prende coordinate di punti 3D nello spazio reale e, applicando i parametri di calibrazione estratti e i vettori di posizionamento della camera, li proietta sul piano immagine 2D trasformandoli in pixel.
    
- **`cv.polylines(img, pts, isClosed, color[, thickness])`**: Disegna una serie di segmenti geometrici collegati tra loro a partire da una lista di vettori di punti. Utilizzato per tracciare le strutture spaziali dei solidi 3D proiettati (es. i lati di un cubo ideale posizionato sulla scacchiera).
    

### Gestione Flussi Video (Video I/O)

- **`cv.VideoCapture(filename)`**: Instanzia e apre uno stream di decodifica video a partire da un file multimediale (o cattura l'hardware live passando un indice intero come `0`).
    
- **`cap.isOpened()`**: Restituisce un valore booleano che attesta la corretta allocazione e accessibilità della risorsa video.
    
- **`ret, frame = cap.read()`**: Estrae e decodifica il fotogramma successivo del video. `ret` indica la presenza di dati (diventa `False` alla fine del video), `frame` è la matrice d'immagine BGR NumPy associata.
    
- **`cap.get(propId)`**: Interroga i metadati strutturali del video.
    
    - _Proprietà chiave:_ `cv.CAP_PROP_FRAME_WIDTH` (larghezza), `cv.CAP_PROP_FRAME_HEIGHT` (altezza), `cv.CAP_PROP_FPS` (frame al secondo), `cv.CAP_PROP_FRAME_COUNT` (conteggio totale dei fotogrammi del file), `cv.CAP_PROP_POS_FRAMES` (indice del puntatore del frame corrente).
        
- **`cap.set(propId, value)`**: Sovrascrive una proprietà di lettura dello stream. Utilizzato per il salto temporale immediato (_seeking_) impostando l'indice del frame da leggere: `cap.set(cv.CAP_PROP_POS_FRAMES, frame_index)`.
    
- **`cap.release()`**: Chiude i descrittori del file video o disalloca la periferica hardware di cattura, liberando le risorse di sistema.
    

### Deep Learning Modulo `cv.dnn` (Inferenza YOLO)

- **`cv.dnn.readNet(model[, config])`** o **`cv.dnn.readNetFromDarknet(cfgFile, darknetModel)`**: Carica in memoria l'architettura strutturale (file di configurazione `.cfg`) e i pesi addestrati stabili (file binario `.weights`) di una rete neurale Darknet/YOLO.
    
- **`cv.dnn.blobFromImage(image, scalefactor, size, mean, swapRB, crop)`**: Preprocessa un'immagine standard trasformandola in un tensore quadridimensionale standardizzato (Batch, Canali, Altezza, Larghezza) ottimizzato per i layer della rete neurale.
    
    - _Configurazione YOLO standard nei lab:_ `scalefactor=1/255.0` (normalizza l'intervallo a $[0, 1]$), `size=(416, 416)` (risoluzione di input fissa della rete), `swapRB=True` (converte lo spazio colore da BGR di OpenCV a RGB nativo della rete).
        
- **`net.setInput(blob)`**: Inserisce il blob quadridimensionale elaborato all'interno del layer di input della rete neurale.
    
- **`net.getLayerNames()`**: Restituisce la lista completa di stringhe contenente i nomi identificativi di tutti i layer dell'architettura della rete.
    
- **`net.getUnconnectedOutLayers()`**: Isola gli indici numerici dei layer terminali di output non connessi a layer successivi (in YOLO v3 rappresentano i 3 layer deputati a emettere le matrici di predizione alle varie scale geometriche).
    
- **`net.forward(outputNames)`**: Avvia il calcolo computazionale in avanti della rete (_forward pass_), restituendo le matrici dei risultati associati ai layer di output indicati in `outputNames`.
    
- **`cv.dnn.NMSBoxes(bboxes, confidences, score_threshold, nms_threshold)`**: Algoritmo di **Non-Maximum Suppression**. Analizza una lista di rettangoli predittivi (`bboxes`) e le relative confidenze, eliminando le sovrapposizioni ridondanti che superano il rapporto di compenetrazione geometrica `nms_threshold` (Intersection over Union - IoU), mantenendo solo la box a confidenza massima per ogni oggetto identificato.
    

## 🔢 MODULO NUMPY (`np`)

### Creazione e Allocazione Strutture Data

- **`np.array(object, dtype=...)`**: Inizializza un array NumPy a partire da strutture dati Python native (liste o tuple). Consente il forzamento esplicito dei tipi di allocazione di memoria tramite l'argomento `dtype` (es. `np.uint8`, `np.float32`, `np.int16`).
    
- **`np.zeros(shape, dtype=...)`**: Alloca una matrice interamente riempita di zeri con la dimensionalità passata in `shape` (es. `(H, W)`). Rappresenta una tela o immagine vuota completamente nera.
    
- **`np.ones(shape, dtype=...)`**: Alloca una matrice riempita interamente con elementi pari a 1 (es. usata per impostare maschere o come base per filtri uniformi).
    
- **`np.arange([start,] stop[, step])`**: Genera un vettore monodimensionale contenente valori interi o float sequenziali spaziati regolarmente dall'intervallo `step`.
    
- **`np.linspace(start, stop, num)`**: Genera un vettore monodimensionale di `num` elementi equispaziati linearmente compresi rigorosamente nell'intervallo chiuso tra `start` e `stop`.
    
- **`np.meshgrid(*xi)`**: Prende due o più vettori di coordinate monodimensionali e restituisce matrici di coordinate N-dimensionali corrispondenti a una griglia regolare. Utilizzata per mappare geometricamente le coordinate di pixel spaziali o per la generazione di filtri analitici (es. Gaussiani).
    

### Proprietà Strutturali delle Matrici (Attributi dell'Oggetto Array)

- **`arr.shape`**: Restituisce una tupla che descrive le dimensioni esatte degli assi della matrice: `(altezza, larghezza, canali)` per immagini a colori BGR, `(altezza, larghezza)` per immagini monocromatiche in scala di grigi.
    
- **`arr.dtype`**: Indica la codifica del tipo di dato binario dei singoli elementi memorizzati (es. `uint8` per immagini standard, `float32` per calcoli ad alta precisione filtrati o matrici di probabilità, `int16` per i gradienti con segno).
    
- **`arr.ndim`**: Restituisce il numero intero totale di dimensioni (assi) dell'array (es. `2` per i grigi, `3` per le immagini a colori, `4` per i blob DNN).
    
- **`arr.T`**: Restituisce la vista trasposta della matrice (inversione degli assi principali).
    

### Manipolazione di Forma, Assi e Dimensione dei Tensori

- **`arr.reshape(newshape)`**: Riorganizza la disposizione dei dati dell'array secondo una nuova forma geometrica definita in `newshape`, senza alterare o copiare l'ordine dei dati sottostanti in memoria. L'indice `-1` indica a NumPy di calcolare automaticamente la dimensione residua compatibile (es. `img.reshape(-1, 3)` appiattisce i pixel mantenendo separati i tre canali colore).
    
- **`arr.flatten()`**: Collassa qualsiasi matrice multi-dimensionale trasformandola in un vettore monodimensionale continuo. Genera sempre una copia fisica dei dati in una nuova allocazione di memoria.
    
- **`arr.ravel()`**: Appiattisce l'array in un vettore monodimensionale. A differenza di `flatten()`, restituisce una _vista_ diretta dei dati originali ogni volta che è strutturalmente possibile, evitando l'uso superfluo di memoria.
    
- **`np.expand_dims(a, axis)`**: Inserisce un nuovo asse di dimensione unitaria (pari a 1) nella posizione indicata da `axis`. Utilizzato per trasformare un'immagine singola 3D `(H,W,C)` in un batch a 4 dimensioni `(1,H,W,C)` richiesto per l'inferenza dalle reti neurali.
    
- **`np.transpose(a, axes)`**: Permuta l'ordine degli assi della matrice secondo la sequenza di indici indicata in `axes`. È fondamentale per convertire il formato nativo delle immagini OpenCV `(Altezza, Larghezza, Canali)` nel formato tensoriale `(Canali, Altezza, Larghezza)` richiesto dalle architetture di Deep Learning.
    
- **`np.squeeze(a[, axis])`**: Rimuove gli assi con dimensione unitaria (pari a 1) dall'array, riducendone la dimensionalità.
    

### Tecniche di Slicing Avanzato, Indicizzazione e ROI

- **`img[y1:y2, x1:x2]`**: Isola una regione rettangolare di interesse (**ROI**) dall'immagine tramite slicing nativo lungo gli assi delle righe (coordinata spaziale verticale $Y$) e delle colonne (coordinata spaziale orizzontale $X$).
    
- **`img[::-1, :]`**: Ribalta verticalmente l'immagine invertendo l'asse delle righe (operazione di specchiamento verticale).
    
- **`img[:, ::-1]`**: Ribalta orizzontalmente l'immagine invertendo l'asse delle colonne (operazione di specchiamento orizzontale / effetto specchio).
    
- **`img[:, :, 0]`**: Estrae selettivamente e isola l'intero canale index zero (che corrisponde al canale del Blu nello spazio colore standard BGR di OpenCV).
    
- **`lut[img]`** _(Advanced Indexing)_: Applicazione immediata vettoriale di una **Look-Up Table (LUT)**. Se `lut` è un array NumPy di 256 elementi e `img` contiene pixel interi validi `uint8` $[0, 255]$, questa istruzione mappa e sostituisce istantaneamente e in parallelo ogni pixel dell'immagine con il rispettivo valore indicizzato nella tabella, evitando cicli `for` lenti.
    

### Operazioni Vettoriali Condizionali, Maschere e Modifiche in Place

- **`mask = (img > 128)`**: Genera una matrice booleana di identica dimensione dell'immagine originaria, in cui ogni cella assume valore `True` se soddisfa la relazione logica e `False` in caso contrario.
    
- **`img[mask] = 255`**: Sostituisce in blocco con il valore 255 solo ed esclusivamente i pixel che si trovano in corrispondenza delle coordinate valutate come `True` all'interno della matrice booleana `mask`.
    
- **`np.where(condition, x, y)`**: Operatore di selezione e sostituzione condizionale elemento per elemento. Restituisce una matrice in cui un elemento viene estratto da `x` se la condizione è `True`, o da `y` se è `False` (es. `np.where(img > 128, 255, 0)` esegue una binarizzazione rapida a soglia fissa).
    
- **`np.clip(a, a_min, a_max)`**: Forza tutti gli elementi dell'immagine a rientrare rigidamente all'interno dell'intervallo di sicurezza $[ \text{a\_min}, \text{a\_max} ]$. I valori inferiori a `a_min` vengono portati a `a_min` e quelli superiori a `a_max` vengono portati a `a_max`. È un'operazione fondamentale per prevenire il fenomeno distruttivo del wrapping aritmetico prima di effettuare il casting a `uint8` (es. evitando che il valore intero float 256 diventi 0 per overflow ciclico).
    
- **`arr.astype(dtype)`**: Esegue la conversione di tipo (_casting_) esplicita e massiva di tutti gli elementi dell'array nel tipo di dato specificato in `dtype` (es. `.astype(np.float32)` per abilitare divisioni decimali accurate o `.astype(np.uint8)` per il ripristino finale in formato immagine).
    

### Riduzioni, Funzioni Statistiche e Analisi degli Assi

La maggior parte di queste funzioni supporta l'argomento **`axis`**, che consente di limitare l'operazione statistica specificamente lungo le righe (`axis=0`), lungo le colonne (`axis=1`), lungo i canali colore (`axis=2`) oppure globalmente sull'intera matrice in caso di assenza del parametro.

- **`np.min(a[, axis])`** / **`np.max(a[, axis])`**: Individua il valore minimo o massimo assoluto contenuto nell'array o lungo gli assi selezionati.
    
- **`np.argmin(a[, axis])`** / **`np.argmax(a[, axis])`**: Restituisce l'indice o la coordinata lineare esatta in cui risiede il valore minimo o massimo della matrice.
    
- **`np.sum(a[, axis])`**: Calcola la sommatoria algebrica totale di tutti gli elementi.
    
- **`np.mean(a[, axis])`**: Calcola il valore medio aritmetico degli elementi (usato ad esempio per calcolare l'illuminazione o la luminosità media globale di un canale).
    
- **`np.std(a[, axis])`**: Calcola lo scarto quadratico medio (deviazione standard). Nell'analisi d'immagine rappresenta una misura quantificabile del contrasto globale.
    

### Funzioni Matematiche Vettorializzate

- **`np.abs(x)`**: Calcola il valore assoluto elemento per elemento. Nel _Motion Detection_ applicato sui video, l'operazione `np.abs(frame1.astype(np.int16) - frame2.astype(np.int16))` permette di rilevare l'entità assoluta del cambiamento tra fotogrammi consecutivi, evitando che le differenze negative vengano azzerate o alterate dall'overflow del formato `uint8`.
    
- **`np.sqrt(x)`**: Calcola la radice quadrata di ogni singolo elemento della matrice (es. utilizzata per estrarre la magnitudo del gradiente $\sqrt{dx^2 + dy^2}$ partendo dalle componenti cartesiane).
    
- **`np.round(a)`**: Arrotonda i valori a virgola mobile all'entità intera più vicina.
    
- **`np.arctan2(y, x)`**: Calcola l'arcotangente a quattro quadranti dell'angolo formato dalle componenti cartesiane $Y$ e $X$. Restituisce l'angolo esatto espresso in radianti compreso nell'intervallo $[-\pi, \pi]$. È l'istruzione chiave per estrarre l'orientazione precisa dei bordi d'immagine a valle di un filtraggio a gradiente (es. Sobel o `spatialGradient`).
    

### Combinazione e Concatenazione Spaziale di Matrici

- **`np.hstack(tup)`**: Concatena in senso orizzontale (lungo l'asse delle colonne) una sequenza o lista di matrici. Le immagini devono possedere la medesima altezza in pixel. Usato per affiancare i risultati grafici.
    
- **`np.vstack(tup)`**: Concatena in senso verticale (lungo l'asse delle righe) una sequenza o lista di matrici. Le immagini devono possedere la medesima larghezza in pixel.
    
- **`np.concatenate((a1, a2, ...)[, axis])`**: Unisce una lista di array lungo l'asse geometrico esplicitamente specificato nell'argomento `axis`.
    

### Funzioni Built-in Nativo Python e Costrutti Standard

- **`open('path').read().splitlines()`**: Idioma di lettura file. Apre un file di testo (es. le etichette delle classi di YOLO `coco.names`), ne legge l'intero contenuto in un'unica stringa monolitica (`.read()`) e la spezza riga per riga (`.splitlines()`). Questa specifica funzione ha il vantaggio cruciale rispetto a `.readlines()` di **rimuovere automaticamente il carattere invisibile di a capo (`\n`)** in fondo alle stringhe, impedendo errori di formattazione grafici quando i nomi delle classi vengono passati a `cv.putText`.
    
- **`zip(*iterables)`**: Prende una serie di collezioni o liste e ne accoppia gli elementi corrispondenti indice per indice in tuple (es. usato per scorrere contemporaneamente i rettangoli delle box, i punteggi di confidenza e le label testuali estratte da YOLO).
    
- **`enumerate(iterable)`**: Restituisce un iteratore che fornisce coppie composte dall'indice numerico incrementale e dal rispettivo elemento della collezione durante un ciclo di scorrimento.
    
- **`len(s)`**: Restituisce un numero intero che esprime il conteggio degli elementi contenuti in una struttura dati (es. il numero di contorni individuati o il numero totale di righe di una matrice).
    
- **`sorted(iterable, key=..., reverse=...)`**: Ordina gli elementi di una collezione. L'argomento `key` accetta una funzione lambda o un criterio personalizzato per regolare i pesi di ordinamento (es. ordinare le componenti connesse in base all'estensione della loro area o alla loro coordinata spaziale).
    
- **`v[i::4]`** _(Slicing con Stride)_: Sintassi nativa delle liste e degli array Python per campionare gli elementi applicando un passo (passo pari a 4 in questo esempio) a partire dall'indice di avvio `i`. Utilizzato nel laboratorio di calibrazione per isolare i vertici omologhi necessari a tracciare i lati paralleli del cubo 3D proiettato.
    
    
    cv.rotate(img_g, cv.ROTATE_90_COUNTERCLOCKWISE)
