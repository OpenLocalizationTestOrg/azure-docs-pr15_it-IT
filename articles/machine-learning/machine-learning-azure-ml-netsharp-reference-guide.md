<properties 
    pageTitle="Guida alla rete # reti neurali specifica della lingua | Microsoft Azure" 
    description="Sintassi per la rete # neural reti lingua specifica, insieme a esempi su come creare un modello di rete neurali personalizzato in Microsoft Azure ML utilizzando Net#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guida alla lingua specifica di rete neurale Net # per l'apprendimento Azure

## <a name="overview"></a>Panoramica
NET # è un linguaggio sviluppato da Microsoft tramite cui viene utilizzato per definire architetture di rete neurale per i moduli di rete neurale in Microsoft Azure apprendimento. In questo articolo verranno fornite:  

-   Nozioni di base relative alle reti neurali
-   Requisiti di rete neurale e come definire i componenti principali
-   La sintassi e parole chiave del linguaggio specifica Net #
-   Esempi di reti neurali personalizzate create con Net# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Nozioni fondamentali sulla rete neurale
Una struttura di rete neurali è costituito da ***nodi*** organizzati in ***livelli***e ponderata ***connessioni*** (o ***bordi***) tra i nodi. Le connessioni sono direzionali ogni connessione è un nodo di ***origine*** e un nodo di ***destinazione*** .  

Ogni ***layer trainable*** (nascosto o un livello di output) include uno o più ***pacchetti di connessione***. Un raggruppamento di connessione è costituito da un livello di origine e una specifica delle connessioni di tale livello di origine. Tutte le connessioni in un determinato gruppo condividono lo stesso ***livello di origine*** e lo stesso ***livello di destinazione***. In rete # un raggruppamento di connessione è considerato come appartenenti a livello di destinazione dell'aggregazione.  
 
NET # supporta diversi tipi di connessione bundle, che consente di personalizzare gli input modo in cui sono mappati alla livelli nascosti e mappare gli output.   

Il raggruppamento standard o predefinito è un **raggruppamento completo**, in cui ogni nodo nel livello di origine è connesso a tutti i nodi nel livello di destinazione.  

Inoltre, Net # supporta quattro tipi di pacchetti di connessione avanzate seguenti:  

-   **Bundle filtrato**. È possibile definire un predicato utilizzando i percorsi del nodo di livello di origine e il nodo del livello di destinazione. I nodi connessi ogni volta che il predicato è vero.
-   **Bundle convolutional**. È possibile definire small Villaggi dei nodi nel livello di origine. Ogni nodo nel livello di destinazione sia connesso a risorse di uno dei nodi nel livello di origine.
-   **Il pool di pacchetti** e **pacchi normalizzazione risposta**. Questi sono simili a bundle convolutional nell'utente definisce small Villaggi dei nodi per il livello di origine. La differenza è che spessori dei bordi in questi pacchetti non sono trainable. Se, tuttavia, una funzione predefinita viene applicata ai valori di nodo origine per determinare il valore del nodo di destinazione.  

Utilizzando Net # per definire la struttura di una rete neurale consente di definire strutture complesse, ad esempio reti neurali profondità o convoluzioni dimensioni non autorizzato, noti per migliorare la formazione sui dati, ad esempio immagini, audio o video.  

## <a name="supported-customizations"></a>Personalizzazioni supportate
L'architettura di modelli di rete neurale creati in Azure apprendimento può essere personalizzato ampiamente tramite Net #. Si può:  

-   Creare livelli nascosti e controllare il numero dei nodi in ogni livello.
-   Specificare la modalità Layer a cui connettersi tra loro.
-   Definire le strutture di connettività speciali, ad esempio convoluzioni e lo spessore bundle di condivisione.
-   Specificare le funzioni di attivazione diverso.  

Per informazioni dettagliate della sintassi lingua specifica, vedere [Specifica della struttura](#Structure-specifications).  
 
Per esempi di definizione reti neurali per alcuni apprendimento comune di attività da simplex ai più complessi, vedere [esempi](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Requisiti generali
-   Devono essere presenti esattamente un output, almeno un livello di input e zero o più livelli nascosti. 
-   Ogni livello è un numero fisso di nodi, livello concettuale disposti in una matrice rettangolare di dimensioni non autorizzate. 
-   Livelli di input non hanno associati esperti parametri e rappresentano il punto in cui i dati dell'istanza accedono alla rete. 
-   Livelli trainable (livelli nascosti e di output) sono associati parametri esperti, noti come spessori ed errore sistematico. 
-   I nodi di origine e destinazione devono essere in livelli separati. 
-   Connessioni devono essere aciclici; in altre parole, non può essere una catena di connessioni con il nodo di origine iniziale.
-   Il livello di output non può essere un livello di origine di un raggruppamento di connessione.  

## <a name="structure-specifications"></a>Specifiche di struttura
Una specifica struttura di rete neurale è costituita da tre sezioni: la **dichiarazione di costante**, la **dichiarazione di livello**, la **dichiarazione di connessione**. È inoltre disponibile una sezione facoltativa **condividere dichiarazione** . È possibile specificare le sezioni in qualsiasi ordine.  

## <a name="constant-declaration"></a>Dichiarazione di costante 
Una dichiarazione di costante è facoltativa. Un modo per definire valori utilizzati in un' posizione nella definizione di rete neurale. L'istruzione dichiarazione è costituita da un identificatore seguito da un segno di uguale e un'espressione di valori.   

Ad esempio, l'istruzione seguente definisce una costante **x**:  


    Const X = 28;  

Per definire contemporaneamente due o più costanti, racchiudere i nomi degli identificatori e i valori tra parentesi graffe e separarli con un punto e virgola. Per esempio:  

    Const { X = 28; Y = 4; }  

Sul lato destro di ogni espressione di assegnazione può essere un numero intero, un numero reale, un valore Boolean (True o False) o un'espressione matematica. Per esempio:  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Dichiarazione di livello
È necessaria la dichiarazione di livello. Definisce la dimensione e la fonte di livello, compresi i pacchetti di connessione e gli attributi. Istruzione di dichiarazione inizia con il nome del livello (di input, nascosti o output), seguito dalle dimensioni del livello (una tupla di numeri interi positivi). Per esempio:  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   Il prodotto delle dimensioni è il numero dei nodi nel livello. In questo esempio, sono disponibili due dimensioni [5,20], vale a dire esistono 100 nodi nel livello.
-   I livelli possono essere dichiarati in qualsiasi ordine, con un'eccezione: se è stato definito più di un livello di input, l'ordine in cui vengono dichiarati deve corrispondere l'ordine delle caratteristiche nei dati di input.  


Per specificare che il numero dei nodi in un livello stabilito automaticamente, utilizzare la parola chiave **auto** . La parola chiave **auto** ha effetti diversi, a seconda del livello:  

-   Dichiarazione di un livello di input, il numero dei nodi è il numero delle caratteristiche nei dati di input.
-   In una dichiarazione di livello nascosto, il numero dei nodi è il numero specificato dal valore del parametro per **il numero dei nodi nascosti**. 
-   In una dichiarazione di livello di output, il numero dei nodi è 2 per la classificazione due classe, 1 per regressione e uguale al numero dei nodi di output per la classificazione multiclass.   

Ad esempio, la definizione di rete seguenti consente le dimensioni di tutti i livelli per essere determinato automaticamente:  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Una dichiarazione di livello per un livello trainable (livelli nascosti o di output) possa includere la funzione output (denominata anche una funzione di attivazione), che per impostazione predefinita per **sigmoid** per i modelli di classificazione e **lineare** per i modelli di regressione. (Anche se si utilizza l'impostazione predefinita, si può indicare esplicitamente la funzione di attivazione, se lo si desidera per motivi di chiarezza.)

Sono supportate le funzioni di output seguenti:  

-   sigmoid
-   lineare
-   softmax
-   rlinear
-   quadrato
-   RADQ
-   srlinear
-   Ass
-   TANH 
-   brlinear  

Ad esempio la dichiarazione che segue viene utilizzata la funzione **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Dichiarazione di connessione
Immediatamente dopo aver definito il livello trainable, è necessario dichiarare connessioni tra i vari livelli definiti. La dichiarazione di aggregazione connessione inizia con la parola chiave **da**, seguito dal nome del livello di origine di raggruppamento e il tipo di aggregazione di connessione per creare.   

Attualmente sono supportati cinque tipi di pacchetti di connessione:  

-   Bundle **completo** , indicati da **tutte le** parole chiave
-   Bundle **filtrato** , contrassegnati con la parola chiave **in cui**, seguita da un'espressione predicativa
-   Bundle **Convolutional** , contrassegnati con la parola chiave **convolve**, seguite da attributi convoluzione
-   **Pool** bundle, indicati da parole chiave **massima del pool** o **significa pool**
-   Bundle **normalizzazione risposta** , contrassegnati con la parola chiave **norm risposta**      

## <a name="full-bundles"></a>Bundle completi  

Un raggruppamento di connessione completa include una connessione da ogni nodo nel livello di origine per ogni livello di nodo di livello di destinazione. Questo è il tipo di connessione di rete predefinito.  

## <a name="filtered-bundles"></a>Bundle filtrati
Una specifica di raggruppamento connessione filtrata include molto predicato, la sintassi di espresso come un'espressione lambda c#. Nell'esempio seguente definisce due bundle filtrati:  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   Nel predicato per _ByRow_, **s** è un parametro che rappresenta un indice nella matrice rettangolare dei nodi del livello di input, _pixel_e **d** è un parametro che rappresenta un indice nella matrice dei nodi di livello nascosto, _ByRow_. Il tipo di **s** e **d** è una tupla di numeri interi lunghezza due. Livello concettuale, **s** intervalli su tutte le coppie di numeri interi con _0 < = s [0] < 10_ e _0 < =[1] < 20 s_, e **d** intervalli su tutte le coppie di numeri interi, con _0 < = d [0] < 10_ e _0 < = d[1] < 12_. 
-   Sul lato destro dell'espressione predicato, esiste una condizione. In questo esempio, per ogni valore di **s** e **d** in modo che la condizione è vera, esiste un bordo dal nodo di livello di origine per il nodo del livello di destinazione. In questo modo, questa espressione di filtro indica che il raggruppamento include una connessione tra il nodo definito da **s** per il nodo definito da **d** in tutti i casi in cui è uguale a d [0] s [0].  

Facoltativamente, è possibile specificare un set di spessori per un'aggregazione filtrata. Il valore dell'attributo **spessori** deve essere una tupla di valori a virgola mobile con una lunghezza che corrisponde al numero di connessioni definite per il raggruppamento. Per impostazione predefinita, spessori generate in modo casuale.  

I valori di peso sono raggruppati per l'indice di nodo di destinazione. Ovvero, se il primo nodo di destinazione sia connesso a nodi di origine K, gli elementi di _K_ prima della tupla **spessori** sono spessori per il primo nodo di destinazione, in ordine di indice di origine. Lo stesso vale per gli altri nodi di destinazione.  

È possibile specificare spessori direttamente come valori costanti. Ad esempio, se è stato il peso in precedenza, è possibile specificare loro come costanti utilizzando la sintassi seguente:

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Bundle convolutional
Se i dati di formazione presenta una struttura omogenea, connessioni convolutional vengono generalmente utilizzate per apprendere le funzioni di alto livello dei dati. Ad esempio nell'immagine, audio o video dati dimensionalità spaziale o temporale possono essere uniformi.  

Bundle convolutional utilizzano rettangolare **. x** che sono fatto scorrere tramite le dimensioni. In pratica, ogni kernel definisce un set di spessori applicati in Villaggi locali, denominato **applicazioni kernel**. Ogni applicazione kernel corrisponde a un nodo nel livello di origine, viene definito il **nodo centrale**. Spessori di un kernel vengono condivisi tra molte connessioni. In un raggruppamento convolutional tutte le applicazioni di kernel sono le stesse dimensioni di ogni kernel è rettangolare.  

Bundle convolutional supportano gli attributi seguenti:

**InputShape** definisce la dimensionalità del livello di origine per quanto riguarda il raggruppamento convolutional. Il valore deve essere una tupla di numeri interi positivi. Il prodotto dei valori interi deve essere uguale il numero dei nodi nel livello di origine, ma in caso contrario, è necessario in modo che corrispondano dimensionalità dichiarate per il livello di origine. La lunghezza di questa tupla diventa il valore di **grado** per il pacchetto convolutional. (In genere grado indica il numero di argomenti o operandi può richiedere una funzione.)  

Per definire la forma e le posizioni della x, utilizzare gli attributi **KernelShape** **Stride**, **spaziatura**, **LowerPad**e **UpperPad**:   

-   **KernelShape**: (obbligatorio) definisce la dimensionalità di ogni kernel per il pacchetto convolutional. Il valore deve essere una tupla di numeri interi positivi con una lunghezza che è uguale al grado di aggregazione. Ogni componente di questo tupla non deve essere maggiore rispetto al relativo componente di **InputShape**. 
-   **STRIDE**: (facoltativo) consente di definire le dimensioni passaggio scorrevole di convoluzione (dimensioni di un unico passaggio per ogni dimensione), che rappresenta la distanza tra i nodi centrale. Il valore deve essere una tupla di numeri interi positivi con una lunghezza che il grado di aggregazione. Ogni componente di questo tupla non deve essere maggiore rispetto al relativo componente di **KernelShape**. Il valore predefinito è una tupla con tutti i componenti uguale a uno. 
-   **Condivisione**: (facoltativo) consente di definire il peso condivisione per ogni dimensione della convoluzione. Il valore può essere un singolo valore booleano o una tupla di valori booleani con una lunghezza il grado di aggregazione. Un singolo valore booleano viene esteso per essere una tupla della lunghezza corretta con tutti i componenti uguale al valore specificato. Il valore predefinito è una tupla costituita da tutti i valori True. 
-   **MapCount**: (facoltativo) definisce il numero di funzionalità maps per il raggruppamento convolutional. Il valore può essere un numero intero positivo o una tupla di numeri interi positivi con una lunghezza che il grado di aggregazione. Un singolo valore integer viene esteso per essere una tupla della lunghezza corretta con i componenti prima uguale a quello specificato e tutti i componenti rimanenti uguale a uno. Il valore predefinito è uno. Il numero totale di mappe funzionalità è il prodotto dei componenti della tupla. La suddivisione del numero totale di diversi componenti determina la modalità di raggruppamento i valori di mappa funzionalità nei nodi di destinazione. 
-   **Spessori**: (facoltativo) consente di definire il peso iniziale per il raggruppamento. Il valore deve essere una tupla di valori a virgola mobile con una lunghezza che rappresenta il numero di volte in cui vengono il numero di spessori per kernel, come definito più avanti in questo articolo. Spessori predefinita in modo casuale generati.  

Esistono due serie di proprietà che ne definiscono spaziatura interna, le proprietà come si escludono a vicenda:

-   **Spaziatura interna**: (facoltativo) consente di determinare se l'input deve essere aggiunti utilizzando una **combinazione di spaziatura interna predefinita**. Il valore può essere un singolo valore booleano o può trattarsi di una tupla di valori booleani con una lunghezza il grado di aggregazione. Un singolo valore booleano viene esteso per essere una tupla della lunghezza corretta con tutti i componenti uguale al valore specificato. Se il valore per una dimensione è vero, l'origine in modo logico vengono inserito in tale dimensione con le celle con valore zero per supportare kernel altre applicazioni, in modo che i nodi centrali del prima e ultima x della dimensione sono i nodi nome e il cognome della dimensione del livello di origine. In questo modo, il numero dei nodi "fittizi" in ogni dimensione viene determinato automaticamente, in modo da adattarlo esattamente _(InputShape [d] - 1) / Stride [d] + 1_ . x a livello di origine zeri iniziali. Se il valore di una dimensione è FALSO, la x vengono definiti in modo che il numero dei nodi ogni lato sinistro fuori corrisponde (fino a differenza di 1). Il valore predefinito di questo attributo è una tupla con tutti i componenti uguale a False.
-   **UpperPad** e **LowerPad**: (facoltativo) specifica un controllo maggiore sulla quantità di riempimento da utilizzare. **Importanti:** Questi attributi possono essere definiti solo se la **spaziatura** sopra è ***non*** definito. I valori devono essere valori interi tuple con lunghezze che sono il grado di aggregazione. Quando vengono specificati questi attributi, vengono aggiunti "fittizi" nodi alle estremità superiore e inferiore di ogni dimensione del livello di input. Il numero dei nodi aggiunti alle estremità superiore e inferiore di ogni dimensione è **LowerPad**[i] e **UpperPad**[i] rispettivamente. Per garantire che vengono corrisponde solo per i nodi "vero" e non ai nodi "fittizi", devono essere soddisfatte le condizioni seguenti:
    -   Ogni componente di **LowerPad** deve essere minore di KernelShape [d] / 2. 
    -   Ogni componente di **UpperPad** non deve essere maggiore rispetto a KernelShape [d] / 2. 
    -   Il valore predefinito di questi attributi è una tupla con tutti i componenti uguale a 0. 

L'impostazione **di spaziatura** = true consente quantità spaziatura interna in base alle esigenze per mantenere il "centro" del kernel all'interno di "reale" di input. Questa operazione modificherà matematiche un po' per il calcolo le dimensioni di output. In genere, la dimensione di output _D_ viene calcolata come _D = (I - K) / S + 1_, dove _I_ è la dimensione di input, _K_ è la dimensione del kernel, _S_ è stride, e _/_ è la divisione di interi (arrotondare per difetto). Se si imposta UpperPad = [1, 1], la dimensione di input _si_ è in modo efficace 29 e quindi _D = (29-5) / 2 + 1 = 13_. Tuttavia, quando **la spaziatura interna** = vero, essenzialmente _si_ Ottiene aumentata dalle _K - 1_; pertanto _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. Specificando valori per **UpperPad** e **LowerPad** modo molto più preciso la spaziatura interna di se è possibile impostare **la spaziatura interna** = true.

Per ulteriori informazioni sulle reti convolutional e le applicazioni, vedere gli articoli seguenti:  

-   [http://deeplearning.NET/Tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.microsoft.com/pubs/68920/icdar03.PDF](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://People.csail.MIT.edu/jvb/papers/cnn_tutorial.PDF](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Il pool di aggregazioni
Un **pool di raggruppamento** applica entità geometriche simili alla connettività convolutional, ma utilizza funzioni predefinite per i valori di nodo di origine per determinare il valore di nodo di destinazione. Di conseguenza, bundle del pool sono non trainable (spessori o errore sistematico). Bundle del pool supportano tutti gli attributi convolutional ad eccezione di **condivisione**, **MapCount**e **peso**.  

In genere, non si sovrapporla vengono riepilogate nelle unità del pool adiacenti. Se Stride [d] è uguale a KernelShape [d] in ciascuna dimensione, il livello ottenuto è il tradizionale livello locale del pool, in genere utilizzato in reti neurali convolutional. Ogni nodo di destinazione calcola il valore massimo o la media delle attività dei relativi kernel nel livello di origine.  

Nell'esempio seguente illustra un raggruppamento del pool: 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   Il grado di aggregazione è 3 (la lunghezza di tuple **InputShape**, **KernelShape**e **Stride**). 
-   Il numero dei nodi nel livello di origine è _5 *24* 24 = 2880_. 
-   In questo modo un livello del pool locale tradizionale **KernelShape** e **Stride** sono uguali. 
-   Il numero dei nodi nel livello di destinazione è _5 *12* 12 = 1440_.  
    
Per ulteriori informazioni sui livelli del pool, vedere gli articoli seguenti:  

-   [http://www.cs.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sezione 3.4)
-   [http://cs.Nyu.edu/~koray/publis/lecun-iscas-10.PDF](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://cs.Nyu.edu/~koray/publis/jarrett-iccv-09.PDF](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Bundle normalizzazione risposta
**Normalizzazione risposta** è una combinazione di normalizzazione locale che è stato introdotto da Geoffrey Hinton, ecc, in questo documento [ImageNet Classiﬁcation con reti neurali Convolutional complete](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalizzazione di risposta viene utilizzata per agevolare generalizzazione in reti neurali. Quando una neuron la generazione di un livello di attivazione molto elevato, un livello di normalizzazione risposta locale elimina il livello di attivazione di neurons adiacente. È possibile usare tre parametri (***α***, ***β***e ***k***) e una struttura convolutional (o una forma delle risorse). Ogni neuron nel livello di destinazione ***y*** corrisponde a un neuron ***x*** nel livello di origine. Il livello di attivazione di ***y*** è dato dalla formula seguente, dove ***f*** è il livello di attivazione di un neuron e ***Nx*** è kernel (o set di contenente neurons circa ***x***), come definito dalla struttura convolutional seguente:  

![][1]  

Pacchetti di risposta normalizzazione supportano tutti gli attributi convolutional ad eccezione di **condivisione**, **MapCount**e **peso**.  
 
-   Se il kernel contiene neurons nella stessa mappa come ***x***, la combinazione di normalizzazione viene considerata **normalizzazione mappa stessa**. Per definire stesso normalizzazione mappa, la prima coordinata in **InputShape** deve avere il valore 1.
-   Se il kernel contiene neurons nella stessa posizione spaziale ***x***, ma la neurons nelle altre mappe, la combinazione di normalizzazione è chiamata **tramite normalizzazione mappe**. Questo tipo di risposta normalizzazione implementato un modulo di inibizione laterale progettata partendo dal tipo disponibile nella parte reale neurons, la creazione di concorrenza per i livelli di attivazione grande tra l'output di neuron calcolato sulle mappe diverse. Per definire tra normalizzazione mappe, la prima coordinata deve essere un numero intero maggiore di uno e non maggiore del numero di mappe e il resto delle coordinate deve avere il valore 1.  

Poiché bundle normalizzazione risposta applicano una funzione predefinita per i valori di nodo di origine per determinare il valore del nodo di destinazione, non presentano stati trainable (spessori o errore sistematico).   

**Avviso**: I nodi nel livello di destinazione corrispondono ai neurons sono i nodi centrali della x. Se, ad esempio, KernelShape [d] è dispari, quindi _KernelShape [d] / 2_ corrisponde al livello di nodo kernel centrale. Se _KernelShape [d]_ è pari, il nodo centrale si trova _KernelShape [d] / 2-1_. Pertanto, se **la spaziatura interna**[d] è FALSO, il primo e l'ultimo _KernelShape [d] / 2_ nodi non dispongono di nodi corrispondenti nel livello di destinazione. Per evitare questo problema, definire **la spaziatura interna** come [true, true,..., true].  

Oltre alle quattro attributi descritti in precedenza, bundle normalizzazione risposta supportano anche gli attributi seguenti:  

-   **Alfa**: (obbligatorio) specifica un valore a virgola mobile che corrisponde al ***α*** nella formula precedente. 
-   **Versione beta**: (obbligatorio) specifica un valore a virgola mobile che corrisponde al ***β*** nella formula precedente. 
-   **Offset**: specifica (facoltativo) valore a virgola mobile che corrisponde a ***k*** nella formula precedente. L'impostazione predefinita è 1.  

Nell'esempio seguente viene definito un raggruppamento di normalizzazione risposta uso degli attributi seguenti:  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   Il livello di origine include cinque mappe, ognuna con aof dimensione di 12 x 12, per un totale di 1440 nodi. 
-   Il valore di **KernelShape** indica che si tratta di un livello di normalizzazione mappa stessa, in cui le risorse è un 3x3 rettangolo. 
-   Il valore predefinito di **riempimento** è FALSO, pertanto il livello di destinazione è solo 10 nodi in ciascuna dimensione. Per includere un nodo nel livello di destinazione che corrisponde a tutti i nodi nel livello di origine, aggiungere spaziatura = [vero; vero; vero]; e modificare le dimensioni di RN1 a [5, 12, 12].  

## <a name="share-declaration"></a>Dichiarazione di condivisione 
NET # facoltativamente supporta la definizione di più pacchetti con Spessori condivisi. Spessori di qualsiasi due bundle possono essere condivisi se le strutture sono uguali. La sintassi seguente definisce bundle con Spessori condivise:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

Ad esempio la dichiarazione di condivisione seguente specifica i nomi dei livelli, che indica che deve essere condiviso spessori ed errore sistematico:  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   Le caratteristiche inpue sono suddivisi in due livelli di input dimensioni uguali. 
-   Livelli nascosti per calcolare le caratteristiche di livello superiore in due livelli di input. 
-   Dichiarazione di condivisione specifica che dovranno essere calcolate _H1_ e _H2_ nello stesso modo da relativi input rispettivi.  
 
In alternativa, questa può essere specificata con due dichiarazioni separate Condividi come indicato di seguito:  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

È possibile utilizzare la forma abbreviata solo quando i livelli contengono un singolo raggruppamento. In generale, è possibile condividere solo quando la struttura pertinenti è identica, vale a dire che hanno le stesse dimensioni, stessa geometria convolutional e così via.  

## <a name="examples-of-net-usage"></a>Esempi di utilizzo di rete #
In questa sezione vengono forniti alcuni esempi di come è possibile utilizzare Net # per aggiungere livelli nascosti, definire il modo in cui livelli nascosti interagiscono con altri livelli e realizzare reti convolutional.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definire una semplice rete neurale personalizzata: esempio "Hello World"
In questo esempio viene illustrato come creare un modello di rete neurale che ha un solo livello nascosto.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

L'esempio illustra alcuni comandi di base come indicato di seguito:  

-   La prima riga definisce il livello di input (denominato _dati_). Quando si utilizza la parola chiave **auto** , della rete neurale include automaticamente tutte le colonne funzionalità negli esempi inpui. 
-   Nella seconda riga Crea livello nascosto. Il nome _H_ viene assegnato al layer nascosti, che ha 200 nodi. Questo livello completamente sia connesso a livello di input.
-   La terza riga definisce il livello output (denominato _O_), che contiene 10 nodi di output. Se la rete neurale viene utilizzata per la classificazione, è disponibile un nodo di output per ogni classe. La parola chiave **sigmoid** indica che la funzione di output viene applicata al livello di output.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definire più livelli nascosti: esempio visione computer
Nell'esempio seguente viene illustrato come definire una rete neurale leggermente più complessa, a più livelli nascosti personalizzati.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Questo esempio illustra alcune funzionalità del linguaggio specifica reti neurali:  

-   La struttura con due livelli di input, _pixel_ e i _metadati_.
-   Il livello di _pixel_ è un livello di origine per due bundle di connessione, con i livelli di destinazione, _ByRow_ e _ByCol_.
-   I livelli _raccogliere_ e _risultati_ sono i livelli di destinazione in più pacchetti di connessione.
-   Il livello di output, _risultato_, è un livello di destinazione in due bundle di connessione; uno con il secondo livello nascoste (raccogliere) come livello di destinazione e altro con il livello di input (metadati) come livello di destinazione.
-   I livelli nascosti, _ByRow_ e _ByCol_, specificare connettività filtrata utilizzando le espressioni predicati. Con maggiore precisione il nodo _ByRow_ in [x, y] sia connesso ai nodi in _pixel_ che presentano le coordinate di indice uguale a coordinata prima del nodo, x. Analogamente, il nodo _ByCol in [x, y] sia connesso ai nodi in _pixel_ con coordinata all'interno di una seconda coordinata del nodo, il secondo indice y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definire una rete convolutional per la classificazione multiclass: esempio riconoscimento cifre
La definizione della rete seguente è progettata per riconoscere i numeri e illustra alcune tecniche avanzate per la personalizzazione di una rete neurale.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   La struttura ha un solo livello di input, _immagine_.
-   La parola chiave **convolve** indica che i livelli denominati _Conv1_ e _Conv2_ convolutional livelli. Ognuna di queste dichiarazioni di livello è seguita da un elenco degli attributi convoluzione.
-   La rete è una terza nascosto layer, _Hid3_, completamente connessa al secondo livello nascosto, _Conv2_.
-   Il livello di output, _cifre_, è connesso solo per il terzo livello nascosto, _Hid3_. **Tutte le** parole chiave indica che il livello di output completamente sia connesso a _Hid3_.
-   Il grado della convoluzione è tre (la lunghezza di tuple **InputShape**, **KernelShape**, **Stride**e **condivisione**). 
-   Il numero di spessori per kernel è _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. O 26 * 50 = 1300_.
-   È possibile calcolare i nodi di ogni livello nascosto come indicato di seguito:
    -   **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   Il numero totale di nodi può essere calcolato utilizzando la dimensionalità dichiarata del livello, [50, 5, 5], come indicato di seguito: _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Poiché **condivisione**[d] è FALSO solo per _d = = 0_, il numero di x è _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Riconoscimenti

La lingua Net # per personalizzare l'architettura delle reti neurali sviluppata Microsoft Shon Katzenberger (progettista, apprendimento) e Alexey Kamenev (tecnico, Microsoft Research). Viene utilizzato internamente per apprendimento progetti e le applicazioni che vanno dal rilevamento di immagine per analitica testo. Per ulteriori informazioni, vedere [Reti neurali in Azure ML - Introduzione alla rete #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
