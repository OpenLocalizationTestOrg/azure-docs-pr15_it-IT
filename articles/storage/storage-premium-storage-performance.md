<properties
    pageTitle="Archiviazione di Azure Premium: Progettare per prestazioni ottimali | Microsoft Azure"
    description="Progettare applicazioni prestazioni elevate utilizzando lo spazio di archiviazione di Azure Premium. Archiviazione Premium offre prestazioni elevate, bassa latenza disco supporto per I/O-che richiede significative attività carichi di lavoro in esecuzione in macchine virtuali di Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Archiviazione di Azure Premium: Modalità le prestazioni

## <a name="overview"></a>Panoramica  
In questo articolo vengono fornite le linee per la creazione di applicazioni prestazioni utilizzando lo spazio di archiviazione di Azure Premium. È possibile utilizzare le istruzioni fornite in questo documento combinato con prestazioni ottimali aggiuntivi applicabili ad tecnologie utilizzate dall'applicazione in uso. Per illustrare le linee guida, sono stati utilizzati SQL Server in esecuzione su Premium archiviazione come esempio tutto il documento.

Mentre si soddisfano i requisiti di prestazioni scenari per il livello di archiviazione in questo articolo, sarà necessario ottimizzare il livello di applicazione. Ad esempio, se si ospita una Farm di SharePoint nello spazio di archiviazione di Azure Premium, è possibile utilizzare gli esempi di SQL Server in questo articolo per ottimizzare il server di database. Inoltre, ottimizzare la Farm di SharePoint server Web e server di applicazioni per ottenere la maggior parte delle prestazioni.

In questo articolo viene spiegato answer seguendo domande frequenti sull'ottimizzazione delle prestazioni dell'applicazione di archiviazione Premium Azure,

-   Come è possibile misurare le prestazioni dell'applicazione?  
-   Perché non si visualizzano prestazioni prevista?  
-   Fattori che influenzano le prestazioni dell'applicazione in archiviazione Premium?  
-   Questi fattori influenza le prestazioni dell'applicazione su Premium lo spazio di archiviazione?  
-   Come è possibile ottimizzare per IOPS, della larghezza di banda e latenza?  

Vengono fornite le linee guida per l'archiviazione Premium perché carichi di lavoro in esecuzione in archiviazione Premium sono molto sensibili alle prestazioni. Sono disponibili esempi quando opportuno. È inoltre possibile applicare alcune di queste istruzioni per le applicazioni in macchine virtuali IaaS con dischi di archiviazione Standard.

Prima di iniziare, se si ha familiarità con lo spazio di archiviazione Premium, leggere innanzitutto il [archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md) articolo e [scalabilità dello spazio di archiviazione di Azure Premium e obiettivi](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Indicatori di prestazioni dell'applicazione  
Abbiamo valutare se l'esecuzione di un'applicazione anche o meno utilizzando come indicatori di prestazioni, velocità un'applicazione di elaborazione di una richiesta di utente, la quantità di dati è l'elaborazione di un'applicazione per ogni richiesta, il numero di richieste un'applicazione di elaborazione in un determinato periodo di tempo, il periodo di tempo che un utente deve attendere per ottenere una risposta dopo averlo inviato loro richiesta. Termini tecnici per questi indicatori di prestazioni sono IOPS, velocità o la larghezza di banda e latenza.

In questa sezione verranno trattati gli indicatori di prestazioni comuni nel contesto di spazio di archiviazione Premium. Nella sezione seguente requisiti dell'applicazione di raccolta, si imparerà a misurare questi indicatori di prestazioni dell'applicazione. In un secondo momento nella ottimizzazione delle prestazioni dell'applicazione, verranno illustrati i fattori che influenzano questi indicatori di prestazioni e consigli per ottimizzare i.

## <a name="iops"></a>IOPS  
IOPS il numero di richieste per l'applicazione di inviare a dischi lo spazio di archiviazione in un secondo. Un'operazione di ingresso/uscita è possibile leggere o scrivere, sequenziale o casuale. Le applicazioni OLTP ad esempio un sito Web rivendita online devono elaborare molte richieste simultanee utente immediatamente. Le richieste di utente sono Inserisci e aggiornare le transazioni di database in modo intensivo, l'applicazione deve elaborare rapidamente. Le applicazioni OLTP richiedono pertanto IOPS molto elevato. Tali applicazioni gestiscono milioni di richieste di IO casuale e di piccole dimensioni. Se si dispone di un'applicazione, è necessario progettare l'infrastruttura di applicazione ottimizzare IOPS. Nella sezione successiva, *Ottimizzare le prestazioni dell'applicazione*, verranno trattati gli argomenti in modo dettagliato i fattori che è necessario prendere in considerazione per ottenere IOPS elevato.

Quando si collega un disco di archiviazione premium a una scala elevata macchine Virtuali, disposizioni Azure è un numero di IOPS garantito in base alla specifica disco. Ad esempio un disco P30 disposizioni 5000 IOPS. Ogni scala elevata dimensione memoria virtuale ha anche un limite IOPS specifico che possono sostenere. Ad esempio, una macchina virtuale GS5 Standard ha 80.000 IOPS limitare.

## <a name="throughput"></a>Velocità di trasmissione  
Velocità o la larghezza di banda è la quantità di dati che l'applicazione invia dischi lo spazio di archiviazione in un intervallo di tempo specificato. Se l'applicazione esegue operazioni di input/output di grandi dimensioni dei file unità IO, è necessario alta velocità. Applicazioni di data warehouse comandi sono particolarmente utili per eseguire le operazioni più complesse analisi che accedono a parti di grandi dimensioni dei dati in un momento e simboli eseguono operazioni di massa. In altre parole, tali applicazioni richiedono maggiore produttività. Se si dispone di un'applicazione, è necessario progettare l'infrastruttura di ottimizzare la velocità di trasmissione. Nella sezione successiva verranno trattati gli argomenti in modo dettagliato i fattori è necessario ottimizzare a tale scopo.

Quando si collega un disco di archiviazione premium a una scala elevata macchine Virtuali, Azure disposizioni velocità in base a tale specifica del disco. Ad esempio, un disco P30 disposizioni 200 MB per secondo disco effettiva. Ogni scala elevata dimensione memoria virtuale è installato anche come limite di velocità specifico che in grado di sostenere. Ad esempio macchina virtuale GS5 Standard è una velocità massima di 2.000 MB al secondo.

Esiste una relazione tra velocità e IOPS come illustrato nella formula riportata di seguito.

![](media/storage-premium-storage-performance/image1.png)

È importante per determinare i valori di velocità e IOPS ottimali che richiede l'applicazione. Quando si tenta di ottimizzare una, gli altri anche Ottiene interessato. In una sezione successiva, *Ottimizzare le prestazioni dell'applicazione*, verranno trattati per ulteriori informazioni sull'ottimizzazione secondo e la velocità di trasmissione.

## <a name="latency"></a>Latenza  
La latenza è il tempo impiegato un'applicazione per ricevere una singola richiesta, inviare a dischi lo spazio di archiviazione e inviare la risposta al client. Questa è una misura critica delle prestazioni di un'applicazione oltre al secondo e la velocità di trasmissione. La latenza di un disco di archiviazione premium è il tempo impiegato per recuperare le informazioni per una richiesta e comunicare all'applicazione. Spazio di archiviazione Premium fornisce latenza bassa coerenti. Se si abilita la cache per dischi di archiviazione premium host di sola lettura, è possibile ottenere molto basso latenza di lettura. Verranno illustrate la cache del disco in modo più dettagliato nella sezione successiva sulle *Ottimizzazione delle prestazioni dell'applicazione*.

Quando si ottimizza l'applicazione per ottenere maggiore secondo e la velocità di trasmissione, incide la latenza dell'applicazione. Dopo l'ottimizzazione delle prestazioni dell'applicazione, valutare sempre la latenza dell'applicazione per evitare comportamenti imprevisti latenza elevata.

## <a name="gather-application-performance-requirements"></a>Raccogliere i requisiti di prestazioni dell'applicazione  
Il primo passo per la progettazione di applicazioni elevate in esecuzione sullo spazio di archiviazione di Azure Premium è, per comprendere i requisiti di prestazioni dell'applicazione. Dopo aver raccolto requisiti relativi alle prestazioni, è possibile ottimizzare l'applicazione per ottenere le prestazioni ottimale.

Nella sezione precedente spiegare gli indicatori di prestazioni comuni, IOPS, velocità e la latenza. È necessario identificare quali di questi indicatori di prestazioni sono importanti per l'applicazione per offrire l'esperienza utente desiderato. Ad esempio IOPS alta più rilevanti per le applicazioni OLTP elaborazione milioni di transazioni in un secondo. Mentre è fondamentale per le applicazioni di Data Warehouse l'elaborazione di grandi quantità di dati in un secondo alta velocità. Latenza è fondamentale per le applicazioni in tempo reale come video live streaming siti Web.

Successivamente, misurare i requisiti di prestazioni dell'applicazione per tutta la durata. Utilizzare l'elenco di controllo di esempio seguente come un avvio. Registrare i requisiti delle massime prestazioni durante la visualizzazione normale, periodi di carico di lavoro punta e orario di lavoro. Identificando i requisiti per tutti i livelli di carichi di lavoro, sarà possibile determinare il requisito di prestazione complessiva dell'applicazione. Ad esempio, il carico di lavoro normale di un sito Web e-commerce sarà transazioni che serve durante la maggior parte dei giorni in un anno. Il carico di lavoro punta del sito Web sarà transazioni che serve durante natalizio o eventi di vendita speciale. Il carico di lavoro punta è in genere esperto per un periodo di tempo limitato, ma può richiedere l'applicazione per ridimensionare il normale funzionamento di due o più volte. Scoprire i requisiti di 99 percentile e percentile 90, 50 ° percentile. In questo modo filtrare gli eventuali valori erratici nei requisiti di prestazioni e sarà possibile concentrare l'attenzione su ottimizzazione per i valori corretti.

**Requisiti di sistema per le prestazioni di applicazione**

| **Requisiti relativi alle prestazioni** | **50 percentile** | **90 ° percentile** | **Percentile 99** |
|---|---|---|---|
| Max. Transazioni al secondo | | | |
| Operazioni di lettura %            | | | |
| Operazioni di scrittura %           | | | |
| Operazioni casuale %          | | | |
| Operazioni sequenziali %      | | | |
| Dimensione della richiesta di IO              | | | |
| Velocità media           | | | |
| Max. Velocità di trasmissione              | | | |
| Min. Latenza                 | | | |
| Latenza media              | | | |
| Max. CPU                     | | | |
| Medio CPU                  | | | |
| Max. Memoria                  | | | |
| Media della memoria               | | | |
| Profondità coda                  | | | |

>**Importante:**  
>È necessario prendere in considerazione proporzioni dei caratteri questi numeri in base a crescita futura prevista dell'applicazione. È consigliabile pianificare la crescita anticipo, perché potrebbe essere difficile per modificare l'infrastruttura per migliorare le prestazioni in un secondo momento.

Se si dispone di un'applicazione esistente e si desidera spostare allo spazio di archiviazione Premium, prima di tutto creare l'elenco di controllo sopra per l'applicazione esistente. Creare un prototipo dell'applicazione su Premium lo spazio di archiviazione, quindi progettazione dell'applicazione in base alle istruzioni riportate in *Ottimizzare le prestazioni dell'applicazione* in una sezione successiva di questo documento. La sezione seguente vengono descritti gli strumenti che è possibile utilizzare per raccogliere le misure delle prestazioni.

Creare un elenco di controllo è simile all'applicazione esistente per il prototipo. Utilizzo degli strumenti Benchmarking è possibile simulare i carichi di lavoro e misurare le prestazioni dell'applicazione prototipo. Vedere la sezione sulla [Benchmarking](#benchmarking) per altre informazioni. In questo modo è possibile determinare se lo spazio di archiviazione Premium possono corrispondono o superano i requisiti di prestazioni dell'applicazione. È quindi possibile implementare le stesse linee guida per l'applicazione di produzione.

### <a name="counters-to-measure-application-performance-requirements"></a>Contatori misurare requisiti relativi alle prestazioni dell'applicazione  
Il modo migliore per misurare requisiti relativi alle prestazioni dell'applicazione, consiste nell'utilizzare gli strumenti di monitoraggio delle prestazioni forniti dal sistema operativo del server. È possibile utilizzare PerfMon per Windows e iostat per Linux. Questi strumenti acquisiscono contatori corrispondenti a ogni misura spiegata nella sezione precedente. È necessario acquisire i valori di questi contatori durante l'esecuzione di un'applicazione relativa normale, orario di lavoro e punta carichi di lavoro.

Contatori sono disponibili per processore, memoria e, ogni disco logico e disco del server di posta. Quando si usa dischi di archiviazione premium con una macchina virtuale, i contatori disco fisico sono per ogni disco di archiviazione premium e contatori del disco logico siano relativi a ogni volume creato dischi archiviazione premium. È necessario acquisire i valori per i dischi contenenti il carico di lavoro dell'applicazione. Se esiste un mapping uno-a-uno tra dischi fisici e logici, è possibile fare riferimento a contatori disco. in caso contrario fare riferimento ai contatori disco logico. Su Linux, il comando iostat genera un report di utilizzo della CPU e disco. Il report di utilizzo del disco fornisce le statistiche di ogni dispositivo fisico o partizione. Se si dispone di un server di database dei relativi dati e i log su dischi separati, raccoglierli per entrambi i dischi. Sotto la tabella sono descritte contatori per dischi, processore e memoria:

| Contatore | Descrizione | PerfMon | Iostat |
|---|---|---|---|
| **IOPS o transazioni al secondo** | Numero di richieste i/o inviati al disco di archiviazione al secondo. | Lettura disco/sec <br> Scrittura su disco al secondo | transazioni al secondo <br> r o s <br> w/s |
| **Disco lettura e scrittura** | % di lettura e scrittura operazioni eseguite sul disco. | % Tempo lettura disco <br> % Tempo scrittura disco | r o s <br> w/s |
| **Velocità di trasmissione** | Quantità di dati leggere o scrivere su disco al secondo. | Disco byte letti/sec <br> Byte scrittura su disco/sec | kB_read/s <br> kB_wrtn/s |
| **Latenza** | Tempo totale per completare una richiesta di IO disco. | Media disco sec/letto <br> Media disco/sec | attesa <br> svctm |
| **Dimensioni IO** | Le dimensioni dei / o le richieste di problemi relativi ai dischi lo spazio di archiviazione. | Media disco byte/letto <br> Media disco byte/scrittura | avgrq sz |
| **Profondità coda** | Numero operazioni in attesa di richieste in attesa per la lettura di maschera o scrivere il disco di archiviazione. | Lunghezza coda disco corrente | avgqu sz |
| **Max. Memoria** | Quantità di memoria necessaria per l'esecuzione dell'applicazione in modo uniforme | % Approvata byte in uso | Utilizzare vmstat |
| **Max. CPU** | Importo CPU necessaria per l'esecuzione dell'applicazione in modo uniforme | % Tempo processore | % util |

Ulteriori informazioni su [iostat](http://linuxcommand.org/man_pages/iostat1.html) e [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Ottimizzare le prestazioni dell'applicazione  
Principali fattori che influenzano le prestazioni di un'applicazione di archiviazione Premium sono natura di IO richieste, dimensione memoria virtuale, dimensioni del disco, numero di dischi, la cache del disco, Multithreading e profondità coda. È possibile controllare alcuni di questi fattori con comandi forniti dal sistema. La maggior parte delle applicazioni non possono assegnare un'opzione per modificare le dimensioni di IO e profondità coda direttamente. Ad esempio, se si utilizza SQL Server, non è possibile selezionare antero-posteriore IO, dimensioni e coda. SQL Server sceglie i IO dimensione e coda antero-posteriore valori ottimali per ottenere la maggior parte delle prestazioni. È importante conoscere gli effetti di entrambi i tipi di fattori sulle prestazioni dell'applicazione in modo che è possibile eseguire il provisioning di risorse appropriate in base alle esigenze di prestazioni.

In questa sezione, consultare l'elenco di controllo requisiti dell'applicazione che è stato creato, per identificare quantità necessarie per ottimizzare le prestazioni dell'applicazione. A seconda che, sarà possibile stabilire quali fattori di questa sezione è necessario regolare. Per vedere gli effetti di ogni fattore sulle prestazioni dell'applicazione, benchmark strumenti eseguiti su installazione dell'applicazione. Fare riferimento alla sezione [Benchmarking](#Benchmarking) alla fine di questo articolo per istruzioni su come eseguire gli strumenti di analisi comparativa comuni in Windows e macchine virtuali Linux.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Ottimizzazione IOPS, velocità e la latenza a colpo d'occhio  
Nella tabella seguente vengono riepilogate tutte fattori relativi alle prestazioni e i passaggi per ottimizzare IOPS, velocità e la latenza. Le sezioni successive di questo riepilogo per descrivere ogni fattore è molto più antero-posteriore.

| | **IOPS** | **Velocità di trasmissione** | **Latenza** |
|---|---|---|---|
| **Scenario di esempio** | Applicazione aziendale OLTP che richiedono transazioni molto elevate numero al secondo.                                                                                                                                 | Dati aziendali deposito elevati elaborazione dell'applicazione di dati. | Nelle applicazioni in tempo reale che richiedono immediate risposte alle richieste dell'utente, ad esempio gioco in linea. |
| Fattori relativi alle prestazioni  | | | |
| **Dimensioni IO** | Dimensioni IO produce IOPS superiore.                                                                                                                                                                           | Dimensione maggiore per IO a produce velocità effettiva superiore. | |
| **Dimensione memoria virtuale** | Utilizzare una dimensione macchine Virtuali che offre maggiore il requisito di applicazione IOPS. Vedere i limiti IOPS e le dimensioni massime macchine Virtuali. | Utilizzare una dimensione di memoria virtuale con limite di velocità maggiore il requisito di applicazione. Vedere i limiti di velocità e le dimensioni massime macchine Virtuali. | Utilizzare una dimensione di memoria virtuale che offre scalare limiti maggiori il requisito di applicazione. Vedere qui i limiti e le dimensioni massime macchine Virtuali. |
| **Dimensione del disco** | Utilizzare una dimensione disco offre IOPS maggiore il requisito di applicazione. Vedere i limiti IOPS e le dimensioni massime disco. | Utilizzare una dimensione disco con limite di velocità maggiore il requisito di applicazione. Vedere i limiti di velocità e le dimensioni massime disco. | Utilizzare una dimensione del disco che offre scalare limiti maggiori il requisito di applicazione. Vedere qui i limiti e le dimensioni massime disco. |
| **Macchine Virtuali e limiti di ridimensionamento disco** | Limite IOPS di dimensione memoria virtuale scelto deve essere maggiore di totale IOPS basata sulle risorse da dischi di archiviazione premium allegati. | Limite di velocità di dimensione memoria virtuale scelto deve essere maggiore di prestazioni più elevate basata sulle risorse da dischi di archiviazione premium allegati. | Limiti di ridimensionamento delle dimensioni macchine Virtuali scelto devono essere maggiori di limiti di ridimensionamento totale premium allegati dei dischi di archiviazione. |
| **La cache del disco** | Abilitare Cache di sola lettura in dischi di archiviazione premium con le operazioni piena di lettura per ottenere maggiore IOPS di lettura. | | Attivare dischi di archiviazione premium con operazioni piena pronti per ottenere lettura molto basso latenza della Cache di sola lettura. |
| **Striping del disco** | Utilizzare più dischi e stripe in modo da ottenere un limite combinato secondo e velocità maggiore. Si noti che il limite combinato per macchine Virtuali deve essere superiore ai limiti combinati di dischi premium allegati. | |
| **Dimensioni strisce** | Dimensioni strisce per casuale motivo IO small visualizzato nelle applicazioni OLTP. Ad esempio, utilizzare strisce dimensione di 64KB applicazione OLTP di SQL Server. | Dimensione maggiore per strisce per sequenza motivo IO grande visualizzato nelle applicazioni Data Warehouse. Ad esempio, utilizzare dimensioni strisce 256KB per SQL Server Data warehouse applicazione. | |
| **Il multithreading** | Utilizzare il multithreading per push numero maggiore di richieste allo spazio di archiviazione Premium che potrebbero causare di secondo e velocità superiore. Ad esempio, in SQL Server impostare un valore MAXDOP elevato da allocare altre CPU a SQL Server. | |
| **Profondità coda** | Profondità coda maggiore produce IOPS superiore. | Profondità coda maggiore produce velocità effettiva superiore. | Dimensioni antero-posteriore coda produce latenza inferiore. |

## <a name="nature-of-io-requests"></a>Natura delle richieste di IO  
Una richiesta di IO è un'unità dell'operazione di ingresso/uscita che verrà eseguita l'applicazione. Che identifica la natura delle richieste di IO, casuali o sequenziale, lettura o scrittura, piccola o grande, consente di determinare i requisiti di prestazioni dell'applicazione. È importante conoscere la natura di richieste di IO, per prendere decisioni destra durante la progettazione dell'infrastruttura di applicazione.

Dimensioni IO corrisponde a uno dei fattori più importanti. Le dimensioni di IO sono la dimensione della richiesta di input/output operazione generata dall'applicazione. Le dimensioni di IO hanno un impatto sulle prestazioni in particolare nel e larghezza di banda in grado di ottenere l'applicazione. Nella formula seguente viene illustrata la relazione tra IOPS, IO dimensioni e la larghezza di banda/effettiva.  
    ![](media/storage-premium-storage-performance/image1.png)

Alcune applicazioni consentono di modificare le dimensioni di IO, mentre non alcune applicazioni. Ad esempio SQL Server determina le dimensioni ottimali di IO stesso e non fornire agli utenti eventuali comandi per modificarlo. Mano, Oracle fornisce un parametro denominato [DB\_blocco\_dimensioni](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) tramite cui è possibile configurare la dimensione della richiesta dei / o del database.

Se si usa un'applicazione che non consentono di modificare le dimensioni di IO, utilizzare le indicazioni in questo articolo per ottimizzare le prestazioni KPI più appropriato per l'applicazione. Per esempio

-   Un'applicazione OLTP genera milioni di richieste di IO casuale e di piccole dimensioni. Per gestire questi tipi di richieste di IO, è necessario progettare l'infrastruttura di applicazione per ottenere IOPS superiore.  
-   Un data warehouse applicazione genera le richieste di IO di grandi dimensioni e sequenziale. Per gestire questi tipi di richieste di IO, è necessario progettare dell'infrastruttura di applicazione per ottenere maggiori della larghezza di banda o effettiva.

Se si usa un'applicazione che consente di modificare le dimensioni di IO, utilizzare questa regola generale per le dimensioni di IO oltre alle altre linee guida,

-   Dimensioni IO per ottenere IOPS superiore. Ad esempio, 8 KB per un'applicazione OLTP.  
-   Dimensione maggiore per IO per ottenere la larghezza di banda/velocità effettiva superiore. Ad esempio 1024 KB per un'applicazione di data warehouse.

Ecco un esempio su come è possibile calcolare il e la velocità di trasmissione/larghezza di banda per l'applicazione. Valutare la possibilità di un'applicazione tramite un disco P30. Il valore massimo possibile ottenere e la velocità di trasmissione/larghezza di banda un disco P30 è 5000 secondo e 200 MB rispettivamente al secondo. A questo punto, se l'applicazione richiede IOPS massimo dal disco P30 e si utilizza una versione ridotta IO come 8 KB, la larghezza di banda risultante sarà possibile ottenere è 40 MB al secondo. Tuttavia, se l'applicazione richiede la massima produttività/larghezza di banda da disco P30 e si utilizza una dimensione maggiore per IO come 1024 KB, IOPS risultante sarà minore, 200 IOPS. Regolare le dimensioni di IO, pertanto, in modo che soddisfi i requisiti e la velocità di trasmissione/larghezza di banda sia dell'applicazione. Tabella seguente sono riepilogate le dimensioni di IO diversi e IOPS e corrispondenti velocità per un disco P30.

| **Requisito per un'applicazione** | **Dimensione i/o** | **IOPS** | **La velocità di trasmissione/larghezza di banda** |
|-----------------------------|--------------|----------|--------------------------|
| IOPS max                    | 8 KB         | 5.000    | 40 MB al secondo         |
| Velocità massima              | 1024 KB      | 200      | 200 MB al secondo        |
| Max Throughput + IOPS elevato  | 64 KB        | 3.200    | 200 MB al secondo        |
| Max IOPS + alta velocità  | 32 KB        | 5.000    | 160 MB al secondo        |

Per ottenere e larghezza di banda supera rispetto al valore massimo di un disco di archiviazione premium singola, utilizzare più dischi premium raggruppati. Ad esempio strisce due P30 dischi per ottenere un IOPS combinato di 10.000 IOPS o una velocità combinato di 400 MB al secondo. Come illustrato nella sezione successiva, è necessario utilizzare una dimensione macchina virtuale che supporta il totale su disco al secondo e velocità.

>**Nota:**  
>Quando si aumenta IOPS o gli altri aumenta la velocità di trasmissione, assicurarsi che non rilevamento di velocità o limiti IOPS del disco o macchine Virtuali quando viene incrementato uno.

Per vedere gli effetti delle dimensioni IO sulle prestazioni dell'applicazione, è possibile eseguire strumenti benchmark in macchine Virtuali e dischi. Creare più esecuzioni dei test e consente di controllare l'impatto dimensione IO diversa per ogni esecuzione. Fare riferimento alla sezione [Benchmarking](#Benchmarking) alla fine di questo articolo per informazioni dettagliate.

## <a name="high-scale-vm-sizes"></a>Dimensioni di macchine Virtuali scala elevato  
Quando si avvia un'applicazione di progettazione, una delle prime operazioni da eseguire è, scegliere una macchina virtuale per ospitare l'applicazione. Spazio di archiviazione Premium viene fornito con dimensioni elevate macchine Virtuali di scala che supporti l'esecuzione di applicazioni che richiedono maggiore potenza di calcolo e un disco locale elevato prestazioni i/o. Queste macchine virtuali forniscono processori più veloci, un rapporto di memoria a core superiore e allo stato solido unità (SSD) per il disco locale. Esempi di altezza macchine virtuali di scala supporto Premium lo spazio di archiviazione sono la serie DS, DSv2 e GS macchine virtuali.

Alta macchine virtuali di scala sono disponibili in diversi formati con un diverso numero di CPU Core, memoria, del sistema operativo e dimensioni disco temporaneo. Ogni dimensione memoria virtuale è installato anche numero massimo di dischi dati che è possibile allegare alle macchina virtuale. Pertanto, la dimensione di memoria virtuale scelta viene influenzata dalle quantità di elaborazione, memoria, e capacità di archiviazione è disponibile per l'applicazione. Influisce anche il calcolo e lo spazio di archiviazione dei costi. Ad esempio, di seguito sono le specifiche di dimensioni superiori macchine Virtuali in una serie di dominio Active Directory, DSv2 serie e una serie GS:

| Dimensione memoria virtuale | Core CPU | Memoria | Dimensioni del disco macchine Virtuali | Max. dischi di dati | Dimensione della cache | IOPS | Limiti di larghezza di banda Cache IO |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | GB 112 | OS = 1023 GB <br> SSD locale = 224 GB | 32 | GB 576 | 50.000 IOPS <br> 512 MB al secondo | Secondo 4.000 e 33 MB al secondo |
| Standard_GS5 | 32 | GB 448 | OS = 1023 GB <br> SSD locale = 896 GB | 64 | GB 4224 | 80.000 IOPS <br> 2.000 MB al secondo | 5.000 secondo e 50 MB al secondo |

Per visualizzare un elenco completo di tutte le dimensioni di macchine Virtuali di Azure disponibili, fare riferimento a [dimensioni macchine Virtuali di Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [le dimensioni di macchine Virtuali Linux](../virtual-machines/virtual-machines-linux-sizes.md). Scegliere una dimensione macchine Virtuali che consentono di organizzare riunioni e ridurre i requisiti di prestazioni dell'applicazione desiderata. Inoltre, prendere in considerazione seguendo importanti considerazioni quando si sceglie di dimensioni macchine Virtuali.


*Limiti di ridimensionamento*  
I limiti massimi IOPS per macchine Virtuali al disco sono diversi e indipendenti tra loro. Assicurarsi che l'applicazione risulta difficile IOPS entro i limiti della macchina virtuale, nonché dischi premium allegati. In caso contrario, le prestazioni dell'applicazione saranno l'esperienza limitazione.

Ad esempio, se che un requisito di applicazione è un massimo di 4.000 IOPS. A tale scopo, eseguire il provisioning un disco P30 in una macchina virtuale DS1. Il disco P30 possibile recapitare IOPS fino a 5.000. Tuttavia, la macchina virtuale DS1 è limitata ai 3.200 IOPS. Di conseguenza, le prestazioni dell'applicazione verranno vincolata dal limite di macchine Virtuali in 3.200 IOPS e si verificherà negativamente sulle prestazioni. Per evitare la situazione, scegliere una dimensione macchine Virtuali e disco che possa sia soddisfare requisiti dell'applicazione.

*Costo dell'operazione*  
In molti casi, è possibile che il costo totale di spazio di archiviazione Premium esercizio è inferiore rispetto all'utilizzo di spazio di archiviazione Standard.

Si consideri ad esempio un'applicazione che richiedono 16.000 IOPS. Per ottenere questo prestazioni, sarà necessario uno Standard di\_D14 Azure IaaS macchine Virtuali, che può fornire un massimo IOPS 16.000 utilizzando 32 dischi di 1TB di spazio di archiviazione standard. Ogni disco standard dello spazio di archiviazione di 1TB è possibile ottenere un massimo di 500 IOPS. Il costo previsto di questa macchina virtuale al mese sarà $1,570. Costo mensile di 32 dischi di archiviazione standard sarà $1,638. Il costo mensile totale stimato sarà $3,208.

Tuttavia, se è ospitata della stessa applicazione allo spazio di memorizzazione Premium, è necessario un piccolo macchine Virtuali e dischi di archiviazione di un numero minore premium, riducendo il costo totale. Uno Standard di\_macchine Virtuali DS13 possibile soddisfare i requisiti IOPS 16.000 con quattro dischi P30. La macchina virtuale DS13 un massimo IOPS 25,600, che ogni disco P30 ha IOPS un massimo di 5.000. In generale, è possibile utilizzare questa configurazione ottenere 5.000 x 4 = 20.000 IOPS. Il costo previsto di questa macchina virtuale al mese sarà $1,003. Il costo mensile di quattro dischi di archiviazione premium P30 sarà $544.34. Il costo mensile totale stimato sarà $1,544.

Tabella che segue sono riepilogate scomposizione dei costi di questo scenario per l'archiviazione Premium e Standard.

| | **Standard** | **Premium** |
|---|---|---|
| **Costo di macchine Virtuali al mese** | $1,570.58 (standard\_D14)   | $1,003.66 (standard\_DS13) |
| **Costo di dischi per mese** | $1,638.40 (dischi 32 x 1 TB) | $544.34 (4 x P30 dischi) |
| **Costo totale per mese**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Con l'archiviazione Premium Azure, viene visualizzato lo stesso livello di prestazioni per macchine virtuali che eseguono Windows e Linux. È supportato molti tipi di distros Linux ed è possibile visualizzare l'elenco completo [di seguito](../virtual-machines/virtual-machines-linux-endorsed-distros.md). È importante tenere presente che distros diversi sono più adatte per diversi tipi di carichi di lavoro. Verrà visualizzata diversi livelli di prestazioni a seconda distro che il carico di lavoro è in esecuzione. Test distros Linux con l'applicazione, scegliere quella appropriata.


Durante l'esecuzione di Linux con Premium lo spazio di archiviazione, controllare gli aggiornamenti più recenti sui driver necessari per ottimizzare le prestazioni.

## <a name="premium-storage-disk-sizes"></a>Dimensioni di Premium lo spazio di archiviazione su disco  
Lo spazio di archiviazione di Azure Premium offre attualmente tre formati disco. Le dimensioni di ogni disco hanno un limite di scala diversa per la larghezza di banda, secondo e lo spazio di archiviazione. Scegliere le dimensioni del disco di archiviazione Premium in base ai requisiti dell'applicazione e la scala elevata dimensione memoria virtuale a destra. La tabella seguente illustra le dimensioni di tre dischi e le relative funzionalità.

| **Tipo di disco**       | **P10**           | **P20**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Dimensione del disco           | GiB 128           | 512 giB           | 1024 giB (1 TB)   |
| IOPS disco       | 500               | 2300              | 5000              |
| Produttività per disco | 100 MB al secondo | 150 MB al secondo | 200 MB al secondo |

Quanti dischi scelto dipende dal disco di dimensione scelto. È possibile utilizzare un singolo disco P30 o più dischi P10 per soddisfare i requisiti dell'applicazione. Prendere in considerazioni sull'account elencati di seguito quando si effettua la scelta.

*Limiti di ridimensionamento (secondo e velocità)*  
I limiti secondo e velocità della dimensione di ciascun disco Premium è diverso e indipendenti da limiti di ridimensionamento macchine Virtuali. Assicurarsi che il totale IOPS e velocità dai dischi siano all'interno di limiti di ridimensionamento della dimensione di memoria virtuale scelto.

Se ad esempio un requisito di applicazione è un massimo di 250 MB/sec velocità e si sta usando una macchina DS4 virtuale con un solo disco P30. La macchina virtuale DS4 può concedere fino a 256 MB/sec velocità. Tuttavia, un singolo disco P30 ha limite di velocità di 200 MB/sec. Di conseguenza, l'applicazione verrà essere vincolato 200 MB/sec a causa il limite di disco. Per ovviare a questo limite, effettuare il provisioning di più dischi di dati per la macchina virtuale.

>**Nota:**  
>Lettura servita dalla cache non è inclusi il disco IOPS e la velocità, pertanto non soggetta ai limiti di disco. Cache è il limite di secondo e velocità separato per macchine Virtuali.
>
>Ad esempio inizialmente la lettura e scrittura è 60MB/sec e 40MB/sec rispettivamente. Nel tempo, la cache di riscaldamento e serve di legge dalla cache. Quindi, è possibile ottenere scrittura maggiore produttività dal disco.

*Numero di dischi*  
Determinare il numero di dischi che sarà necessario valutando requisiti dell'applicazione. Ogni dimensione memoria virtuale ha anche un limite al numero di dischi che è possibile allegare alle macchina virtuale. In genere si tratta due volte il numero di core. Assicurarsi che le dimensioni di macchine Virtuali che scelto supportino il numero di dischi necessari.

Tenere presente che dischi Premium lo spazio di archiviazione sono le prestazioni superiori rispetto a dischi di archiviazione Standard. Pertanto, se si esegue la migrazione dell'applicazione da macchine Virtuali IaaS Azure mediante archiviazione Standard allo spazio di archiviazione Premium, è necessario probabilmente meno dischi premium per ottenere le prestazioni stesso o versioni successive per l'applicazione.

## <a name="disk-caching"></a>La cache del disco  
Alta macchine virtuali di scala che utilizzano lo spazio di archiviazione di Azure Premium dispone di una tecnologia di memorizzazione nella cache multilivello denominata BlobCache. BlobCache utilizza una combinazione di RAM macchina virtuale e SSD locale per la memorizzazione nella cache. Questa cache è disponibile per i dischi persistenti Premium lo spazio di archiviazione e dischi locali macchine Virtuali. Per impostazione predefinita, questa cache è impostata su lettura/scrittura per dischi del sistema operativo e di sola lettura per dischi dati ospitati in archiviazione Premium. Con la cache del disco attivata dischi Premium lo spazio di archiviazione, la scala elevata macchine virtuali possa raggiungere livelli estremamente elevati delle prestazioni che superano le prestazioni del disco sottostante.

>[AZURE.WARNING] Modifica delle impostazioni della cache di un disco Azure disconnette e nuovamente associato il disco di destinazione. Se il disco di sistema operativo, si riavvia la macchina virtuale. Interrompere tutti applicazioni/servizi che può essere influenzati da questo disservizi prima di modificare l'impostazione della cache disco.

Per ulteriori informazioni sul funzionamento del BlobCache, fare riferimento all'interno [Dello spazio di archiviazione di Azure Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) post di blog.

È importante abilitare cache su dei dischi di destra. Se si deve attivare la cache del disco su un disco premium o non dipenderà il modello di carico di lavoro tale disco sarà gestione. Tabella che segue mostra il valore predefinito impostazioni della cache per dischi del sistema operativo e i dati.

| **Tipo di disco** | **Impostazione della Cache** |
|---|---|
| Disco rigido del sistema operativo | ReadWrite |
| Disco di dati | Nessuno |

Ecco le impostazioni della cache disco consigliato per dischi di dati

| **Impostazione della cache disco** | **Consigli su quando utilizzare questa impostazione** |
|---|---|
| Nessuno | Configurare cache host come Nessuno per dischi in sola lettura e scrittura Incolla. |
| Sola lettura | Configurare cache host come sola lettura per dischi di lettura e di sola lettura e scrittura. |
| ReadWrite | Configurare cache host come ReadWrite solo se l'applicazione gestisce correttamente la scrittura dei dati memorizzati nella cache persistenti dischi quando necessario. |

*Sola lettura*  
Se si configura la cache per dati Premium archiviazione dischi sola lettura, è possibile ottenere bassa latenza di lettura e ottenere molto elevato secondo lettura e la velocità di trasmissione dell'applicazione. Si tratta di scadenza due motivi

1.  Lettura eseguita dalla cache, si trova in macchine Virtuali memoria e SSD locale, sono molto più legge dal disco di dati, si trova in archiviazione blob Azure veloci.  
2.  Spazio di archiviazione Premium non conta legge servite dalla cache, verso il disco IOPS e la velocità di trasmissione. Di conseguenza, l'applicazione in grado di ottenere maggiore totale secondo e la velocità di trasmissione.

*ReadWrite*  
Per impostazione predefinita, dischi OS hanno ReadWrite attivata la cache. Sono stati aggiunti di recente il supporto per la memorizzazione nella cache dati dischi anche ReadWrite. Se si utilizza ReadWrite memorizzazione nella cache, è necessario disporre migliore per scrivere dischi permanenti i dati dalla cache. Ad esempio SQL Server gestisce la scrittura dei dati memorizzati nella cache dischi archivio permanente autonomamente. Utilizzo della cache ReadWrite con un'applicazione che non gestisce mantenere i dati necessari può comportare la perdita di dati, se la macchina virtuale determina l'arresto anomalo.

Ad esempio, è possibile applicare alle linee guida seguenti per SQL Server in esecuzione in archiviazione Premium eseguendo le operazioni seguenti

1.  Configurare "Sola lettura" cache dischi di archiviazione premium che ospita i file di dati.  
    un.  Il fast legge dalla cache inferiore il tempo di query SQL Server, dal momento che le pagine di dati vengono recuperate in modo più rapido dalla cache di confrontata direttamente dai dati di dischi.  
    b.  Disporre di lettura da cache, indica che è disponibile da dischi dati premium velocità aggiuntive. SQL Server possono utilizzare questo velocità aggiuntive verso il recupero di più pagine di dati e altre operazioni come backup e ripristino batch carica e la ricostruzione dell'indice.  
2.  Configurare "Nessuno" nella cache dischi di archiviazione premium che ospita i file di log.  
    un.  File di log hanno principalmente operazioni Incolla di scrittura. Di conseguenza, non vantaggio dalla cache di sola lettura.

## <a name="disk-striping"></a>Striping del disco  
Quando una scala elevata che macchine Virtuali sono associato a diverse premium archiviazione persistente dischi, i dischi possano raggruppati per aggregare i IOPs della larghezza di banda e capacità di archiviazione.

In Windows, è possibile usare spazi di archiviazione a strisce dischi insieme. È necessario configurare una colonna per ogni disco in un pool. In caso contrario, le prestazioni complessive del volume strisce possono essere inferiore al previsto, a causa di distribuzione uniforme del traffico tra i dischi.

Importante: Usa UI Server Manager, è possibile impostare il numero totale di colonne fino a 8 per un volume strisce. Quando si aggiungono più di 8 dischi, usare PowerShell per creare il volume. Utilizzo di PowerShell, è possibile impostare il numero di colonne in base al numero di dischi. Ad esempio, se sono presenti 16 dischi in un set di singola striscia; specificare 16 colonne nel parametro *NumberOfColumns* del cmdlet di PowerShell *VirtualDisk di nuovo* .


Su Linux, l'utilità MDADM a strisce dischi insieme. Per informazioni dettagliate sui dischi striping Linux fare riferimento alla [Configurazione RAID Software su Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Dimensioni strisce*  
Una configurazione importante nello striping è la dimensione strisce. La dimensione strisce o le dimensioni di blocco sono il più piccolo blocco di dati per possono gestire applicazioni su un volume strisce. Dimensioni strisce che configurare dipendono dal tipo di applicazione e il modello di richiesta. Se si sceglie la dimensione strisce errato, potrebbero causare l'allineati di IO, conduce ad negativamente sulle prestazioni dell'applicazione.

Ad esempio, se una richiesta di IO generata dall'applicazione in uso è maggiore di dimensioni strisce del disco, il sistema di archiviazione scrive oltre i confini unità strisce su più di un disco. All'orario stabilito per accedere ai dati, sarà necessario per la ricerca tra più unità di strisce per completare la richiesta. L'effetto cumulativo di questo comportamento può comportare prestazioni significative. Mano, se la dimensione della richiesta di IO è minore di dimensioni strisce e casuale, le richieste di IO possono aggiungere sullo stesso disco causa una bottiglia e infine influire sulle prestazioni IO.


A seconda del tipo di carico di lavoro che viene eseguita l'applicazione, scegliere una dimensione strisce appropriato. Richieste casuali small IO, usare una versione ridotta strisce. Invece di grandi dimensioni IO sequenza le richieste di utilizzano una dimensione maggiore per strisce. Scoprire la striscia dimensioni consigliate per l'applicazione che verrà eseguita su Premium lo spazio di archiviazione. Per SQL Server, configurare le dimensioni strisce di 64KB per carichi di lavoro OLTP e 256KB per carichi di lavoro warehouse di dati. Vedere [prestazioni ottimali SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) per altre informazioni.


>**Nota:**  
>È possibile stripe insieme un massimo di 32 premium lo spazio di archiviazione su una serie di dominio Active Directory macchine Virtuali 64 premium archiviazione dischi e su una serie GS macchine Virtuali.

## <a name="multi-threading"></a>Multithreading  
Azure progettata piattaforma di archiviazione Premium sia ampiamente in parallelo. Di conseguenza, un'applicazione multithreading raggiunge prestazioni molto elevate rispetto a un'applicazione multithread. Un'applicazione multithreading suddivide le attività tra più thread e aumento dell'efficienza della relativa esecuzione utilizzando le risorse macchine Virtuali e disco per il valore massimo.

Ad esempio, se l'applicazione è in esecuzione su un singolo core macchine Virtuali con due thread, la CPU passare tra i due thread per ottenere l'efficienza. Mentre un thread è in attesa su un disco IO per completare la CPU è possibile passare a altro thread. In questo modo, due thread possono essere svolte più rispetto a un singolo thread. Se la macchina virtuale ha più core, ridurre ulteriormente tempo di esecuzione, in quanto ogni core può eseguire attività in parallelo.

Potrebbe risultare impossibile modificare la modalità di un'applicazione di preconfigurate implementato singolo thread o multithreading. Ad esempio SQL Server è in grado di gestire più CPU e multi-core. Tuttavia, SQL Server decide le condizioni verrà utilizzati uno o più thread per l'elaborazione di una query. Può eseguire query e creare gli indici utilizzando il multithreading. Per una query che includono la partecipazione alle tabelle di grandi dimensioni e ordinamento dei dati prima di restituire all'utente, SQL Server utilizzerà probabilmente più thread. Tuttavia, un utente non è possibile controllare se SQL Server esegue una query utilizzando un singolo thread o più thread.

Sono disponibili le impostazioni di configurazione che è possibile modificare per influenzare questo multithreading o in parallelo l'elaborazione di un'applicazione. In caso di SQL Server, ad esempio, è la configurazione di grado di parallelismo massima. L'elaborazione di questa impostazione chiamata MAXDOP, consente di configurare il numero massimo di processori che SQL Server può utilizzare quando in parallelo. È possibile configurare MAXDOP per singole query o operazioni di indice. Questo è utile quando si desidera bilanciare le risorse di sistema per un'applicazione critica prestazioni.

Si supponga ad esempio, l'applicazione utilizzando SQL Server è in esecuzione una query di grandi dimensioni e un'operazione di indice nello stesso momento. Supponiamo che si desidera rendere offre prestazioni migliori rispetto alla query di grandi dimensioni l'operazione di indice. In questo caso, è possibile impostare il valore MAXDOP dell'operazione di indice per essere superiore al valore MAXDOP per la query. In questo modo, SQL Server ha più numero di processori può utilizzare per l'operazione di indice rispetto al numero di processori che possono dedicare alla query di grandi dimensioni. Tenere presente che non si controlla il numero di thread di SQL Server verrà utilizzato per ogni operazione. È possibile controllare il numero massimo di processori viene dedicato per il multithreading.

Ulteriori informazioni su [Gradi di parallelismo](https://technet.microsoft.com/library/ms188611.aspx) in SQL Server. Informazioni su tali impostazioni che influiscono sulla multithreading dell'applicazione e le configurazioni per ottimizzare le prestazioni.

## <a name="queue-depth"></a>Profondità coda  
La dimensione coda o coda o antero-posteriore coda è il numero di richieste di IO in sospeso nel sistema. Il valore di profondità coda determina il numero di operazioni IO applicazione consentono di allineare il, che verranno elaborati dischi lo spazio di archiviazione. Avrà effetto su tutti gli indicatori di prestazioni tre applicazione descritto in questo articolo bastano, IOPS, velocità e la latenza in.

Accodare antero-posteriore e multithreading sono strettamente correlati. Il valore di profondità coda indica quanto spazio multithreading può essere ottenuto l'applicazione. Se antero-posteriore coda è grande, applicazione di eseguire altre operazioni contemporaneamente, in altre parole, più multithreading. Se antero-posteriore coda è piccolo, anche se l'applicazione multithread, non avrà abbastanza richieste allineate per l'esecuzione simultanea.

In genere orientarsi applicazioni non consente di modificare Profondità coda perché se impostato in modo non corretto produrrà più male che bene. Applicazioni verranno impostare il valore di destro della profondità coda per ottenere prestazioni ottimali. Tuttavia, è importante conoscere il concetto in modo che è possibile risolvere i problemi di prestazioni con l'applicazione. È anche possibile osservare gli effetti di profondità coda eseguendo strumenti benchmark nel sistema.

Alcune applicazioni di specificare le impostazioni che influenzano antero-posteriore coda. Ad esempio, l'impostazione (massimo grado di parallelismo) MAXDOP in SQL Server descritta nella sezione precedente. MAXDOP è un modo per influenzare Profondità coda e multithreading, anche se non modifichi direttamente il valore di profondità coda di SQL Server.

*Profondità coda elevato*  
Una profondità coda elevato linee le altre operazioni sul disco. Il disco sa la richiesta successiva nella coda di anticipo. Di conseguenza, il disco può pianificare le operazioni di anticipo e li elaborino in una sequenza ottimale. Poiché l'applicazione sta inviando più richieste sul disco, il disco può elaborare ulteriori IOs in parallelo. Infine, l'applicazione sarà possibile ottenere IOPS superiore. Dall'applicazione di elaborazione delle altre richieste, aumenta anche la velocità totale dell'applicazione.

In genere, è possibile utilizzare un'applicazione di ottenere massima produttività con 8-16 + inevasi IOs per disco collegato. Se una profondità coda, applicazione non è inserendo abbastanza IOs al sistema e meno l'importo della elaborerà in un determinato periodo. In altre parole, minore velocità.

Ad esempio, in SQL Server, impostazione del valore MAXDOP per una query per "4" informa SQL Server che utilizzi fino a quattro core per eseguire la query. SQL Server consente di verificare che cos'è migliore coda antero-posteriore valore e il numero di core per l'esecuzione della query.

*Profondità coda ottimale*  
Valore di profondità coda molto elevato è installato anche relativi svantaggi. Se coda antero-posteriore è troppo alto, l'applicazione tenterà di unità IOPS molto elevato. A meno che l'applicazione ha dischi persistenti con IOPS provisioning sufficienti, questo può influire negativamente sulle latenza dell'applicazione. Formula seguente mostra la relazione tra latenza, secondo e profondità coda.  
    ![](media/storage-premium-storage-performance/image6.png)

È necessario configurare Profondità coda non a un qualsiasi valore elevato, ma a un valore ottimale, in grado di offrire sufficiente IOPS per l'applicazione senza influire sulla latenza. Ad esempio, se la latenza di applicazione deve essere 1 millisecondi, Profondità coda necessaria per ottenere 5.000 IOPS è, QD = 5000 x 0,001 = 5.

*Profondità coda per regolare il Volume strisce*  
Per un volume a strisce con mantenere una profondità coda sufficientemente elevato in modo che, ogni disco disponga di una profondità coda punta singolarmente. Si consideri ad esempio un'applicazione che inserisce una profondità coda pari a 2 e 4 dischi presenti la striscia. Le richieste di IO due verranno inoltrate a due dischi e rimanente due dischi sarà inattivo. Pertanto, configurare antero-posteriore coda in modo che tutti i dischi possono essere occupati. Formula riportata di seguito viene illustrato come determinare la profondità coda di volumi strisce.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitazione  
Disposizioni Premium archiviazione Azure numero specificato di secondo e la velocità di trasmissione a seconda della dimensione macchine Virtuali e le dimensioni del disco che scelto. Ogni volta che l'applicazione tenta di unità IOPS o la velocità oltre questi limiti di cosa può gestire le macchine Virtuali o un disco, lo spazio di archiviazione Premium rallentamento essa. Questo manifesti sotto forma di riduzione delle prestazioni dell'applicazione. Questo significa latenza superiore, inferiore effettiva o abbassare IOPS. Se lo spazio di archiviazione Premium non limitare, l'applicazione completamente potrebbe non riuscire superando quali le risorse sono in grado di raggiungere. Pertanto, per evitare problemi di prestazioni a causa di limitazione, sempre effettuare il provisioning di risorse sufficienti per l'applicazione. Prendere in considerazione sono descritti nelle sezioni di dimensioni disco sopra e le dimensioni di macchine Virtuali. Benchmark è il modo migliore per determinare quali risorse è necessario pubblicare l'applicazione.

## <a name="benchmarking"></a>Benchmark  
Benchmark è il processo di simulazione diversi carichi di lavoro nell'applicazione e la misurazione delle prestazioni dell'applicazione per ogni carico di lavoro. Utilizzare i passaggi descritti in una sezione precedente, sono state raccolte i requisiti di prestazioni dell'applicazione. Eseguendo strumenti benchmark in macchine virtuali che ospita l'applicazione, è possibile determinare le prestazioni in grado di ottenere l'applicazione di archiviazione Premium. In questa sezione, viene assegnato esempi di benchmark una macchina virtuale DS14 Standard viene completato il provisioning con dischi lo spazio di archiviazione di Azure Premium.

È stato utilizzato comuni strumenti benchmark Iometer e FIO, rispettivamente per Windows o Linux. Questi strumenti generano più thread simulare una produzione come carico di lavoro e misurano le prestazioni di sistema. L'utilizzo degli strumenti è inoltre possibile configurare i parametri come Blocca dimensioni e coda antero-posteriore, che è in genere non è possibile modificare per un'applicazione. In questo modo è più possibile unità massime prestazioni su scala elevata macchine Virtuali viene completato il provisioning con dischi premium per diversi tipi di carichi di lavoro dell'applicazione. Per ulteriori informazioni su ogni strumento benchmark visitare [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Per seguire gli esempi riportati di seguito, creare una macchina virtuale DS14 Standard e connettere la macchina virtuale 11 dischi di archiviazione Premium. Dischi 11 configurare 10 dischi all'host la memorizzazione nella cache come "Nessuno" e li stripe in un volume denominato NoCacheWrites. Configurare la memorizzazione nella cache come "Sola lettura" nel disco rimanente host e creare un volume denominato CacheReads con questo disco. Con questa impostazione, sarà possibile visualizzare le massime prestazioni di lettura e scrittura da una macchina virtuale DS14 Standard. Per informazioni dettagliate sulla creazione di una macchina virtuale DS14 con dischi premium, passare a [creare e utilizzare lo spazio di archiviazione Premium account per un disco dati macchina virtuale](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Avvio in corso la Cache*  
Il disco con host di sola lettura la memorizzazione nella cache saranno in grado di assegnare IOPS superiore rispetto al limite di disco. Per ottenere il massima prestazioni lettura dalla cache di host, prima di tutto è necessario riscaldamento la cache del disco. In questo modo che IOs lettura dello strumento benchmark sarà basate su volume CacheReads effettivamente raggiunge la cache e non il disco direttamente. Il risultato di accessi cache in IOPS aggiuntive dalla cache di singola abilitato disco.

>**Importante:**  
>È necessario riscaldamento cache prima di eseguire benchmark, ogni volta che viene riavviato macchine Virtuali.

#### <a name="iometer"></a>Iometer   
[Scaricare lo strumento Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) nella macchina virtuale.

*File di test*  
Iometer utilizza un file di test archiviata nel volume in cui eseguire il test benchmark. L'unità legge e scrive su questo file di test per misurare il disco al secondo e la velocità di trasmissione. Se sono state fornite, Iometer crea file di test. Creare un file di test 200GB denominato iobw.tst volumi CacheReads e NoCacheWrites.

*Specifiche di Access*  
Specifiche, richiedere IO dimensioni, % lettura/scrittura, % casuale/sequenziale sono configurate tramite la scheda "Specifiche di Access" nell'Iometer. Creare una specifica di accesso per ogni gli scenari descritti di seguito. Creare le specifiche di access e "Salva" con un'appropriata assegnare un nome simile – RandomWrites\_8K, RandomReads\_8K. Selezionare la corrispondente specifica durante l'esecuzione di uno scenario di test.

Un esempio di specifiche di access per gli scenari di scrittura IOPS massimo è illustrato di seguito,  
    ![](media/storage-premium-storage-performance/image8.png)

*Specifiche di Test IOPS massimo*  
Per dimostrare IOPs massimo, utilizzare più piccolo richiesta. Utilizzare la dimensione della richiesta 8 KB e creare specifiche per scrive casuale e lettura.

| Specifica di accesso | Dimensione della richiesta | Casuale % | % Di lettura |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K     | 8 KB           | 100      | 0      |
| RandomReads\_8K      | 8K           | 100      | 100    |

*Specifiche di Test di velocità massima*  
Per dimostrare velocità massima, utilizzare dimensione maggiore per richiesta. Utilizzare la dimensione della richiesta 64 KB e creare specifiche per lettura e scrive casuali.

| Specifica di accesso | Dimensione della richiesta | Casuale % | % Di lettura |
|----------------------|--------------|----------|--------|
| RandomWrites\_64 KB    | 64 KB          | 100      | 0      |
| RandomReads\_64 KB     | 64 KB          | 100      | 100    |

*Esecuzione di Test Iometer*  
Eseguire la procedura seguente per riscaldamento cache

1.  Creare due specifiche di access con valori riportati di seguito,

  	| Nome              | Dimensione della richiesta | Casuale % | % Di lettura |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1MB | 1MB          | 100      | 0      |
  	| RandomReads\_1MB  | 1MB          | 100      | 100    |

2.  Eseguire il test Iometer per l'inizializzazione disco cache parametri seguenti. Usare tre thread di lavoro per il volume di destinazione e una profondità coda di 128. Impostare la durata del test "Fase di esecuzione" 2hrs nella scheda "Testare il programma di installazione".

  	| Scenario:              | Volume di destinazione | Nome              | Durata |
  	|-----------------------|---------------|-------------------|----------|
  	| Inizializzare Cache disco | CacheReads    | RandomWrites\_1MB | 2hrs     |

3.  Eseguire il test Iometer per riscaldamento disco cache con i parametri seguenti. Usare tre thread di lavoro per il volume di destinazione e una profondità coda di 128. Impostare la durata del test "Fase di esecuzione" 2hrs nella scheda "Testare il programma di installazione".

  	| Scenario:           | Volume di destinazione | Nome             | Durata |
  	|--------------------|---------------|------------------|----------|
  	| Riscaldamento Cache disco | CacheReads    | RandomReads\_1MB | 2hrs     |

Dopo la cache è riscaldato disco, procedere con gli scenari di test elencati di seguito. Per eseguire il test Iometer, utilizzare almeno tre thread di lavoro per **ogni** volume di destinazione. Per ogni thread di lavoro, selezionare il volume di destinazione, impostare l'intensità di coda e selezionare una delle specifiche di test salvati, come illustrato nella tabella seguente, per eseguire lo scenario di test corrispondenti. Nella tabella sono inoltre i risultati previsti di secondo e velocità durante l'esecuzione di questi test. Per tutti gli scenari, viene utilizzate una dimensione piccola IO di 8KB e una profondità coda elevato di 128.

| Scenario di test      | Volume di destinazione | Nome              | Risultato       |
|--------------------|---------------|-------------------|--------------|
| Max. Lettura IOPS     | CacheReads    | RandomWrites\_8K  | 50.000 IOPS  |
| Max. Scrivere IOPS    | NoCacheWrites | RandomReads\_8K   | 64.000 IOPS  |
| Max. IOPS combinati | CacheReads    | RandomWrites\_8K  | 100.000 IOP |
|                    | NoCacheWrites | RandomReads\_8K   |              |
| Max. Leggere MB/sec   | CacheReads    | RandomWrites\_64 KB | 524 MB/sec   |
| Max. Scrivere MB/sec  | NoCacheWrites | RandomReads\_64 KB  | 524 MB/sec   |
| Totale MB/sec    | CacheReads    | RandomWrites\_64 KB | 1000 MB/sec  |
|                    | NoCacheWrites | RandomReads\_64 KB  |              |

Di seguito sono schermate della Iometer risultati dei test per scenari di secondo e velocità combinati.

*IOPS massimo combinato lettura e scrittura*  
![](media/storage-premium-storage-performance/image9.png)

*Produttività massima combinato lettura e scrittura*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO è uno strumento popolari allo spazio di archiviazione benchmark in macchine virtuali Linux. Ha la possibilità di selezionare dimensioni IO diverse, sequenziale o lettura casuale e si scrive. Generati thread di lavoro o processi per eseguire le operazioni dei / o specificate. È possibile specificare il tipo di operazioni i/o che ogni thread di lavoro deve eseguire l'utilizzo dei file di processo. Abbiamo creato un file di lavoro per ogni scenario illustrato negli esempi seguenti. È possibile modificare le impostazioni in questi file processo di benchmarking diversi carichi di lavoro in esecuzione su Premium lo spazio di archiviazione. Negli esempi, si utilizza una macchina virtuale 14 DS Standard esecuzione **Ubuntu**. Utilizzare la stessa impostazione descritta all'inizio della [sezione Benchmarking](#Benchmarking) e affettuoso per la configurazione della cache prima di eseguire le verifiche benchmark.

Prima di iniziare, [scaricare FIO](https://github.com/axboe/fio) e installarlo nel computer virtuale.

Eseguire il seguente comando per Ubuntu,

        apt-get install fio

Quattro thread di lavoro per favorire le operazioni di scrittura e quattro thread di lavoro verrà utilizzato per le operazioni di lettura del disco. Lo scrittura dei collaboratori verranno favorire il traffico del volume "nocache" con 10 dischi cache impostata su "Nessuno". Worker lettura verranno guidando il traffico del volume "readcache" è 1 disco cache è impostata su "Sola lettura".

*Massima di scrittura IOPS*  
Creare il file di lavoro con seguenti specifiche per ottenere massima IOPS scrivere. Assegnare il nome "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Nota i cambiamenti principali Segui alle linee guida di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per unità massime IOPS,  
-   Una profondità coda elevato di 256 caratteri.  
-   Dimensioni blocco piccolo di 8KB.  
-   Più thread eseguono casuale scrive.

Eseguire il seguente comando per avviare il test FIO per 30 secondi,  

    sudo fio --runtime 30 fiowrite.ini

Durante l'esecuzione di test, sarà possibile visualizzare il numero di scrittura IOPS macchina virtuale e l'esecuzione di dischi Premium. Come mostrato nell'esempio seguente, la macchina virtuale DS14 offre la massima limite IOPS di 50.000 IOPS di scrittura.  
    ![](media/storage-premium-storage-performance/image11.png)

*Lettura massima IOPS*  
Creare il file di lavoro con seguenti specifiche per ottenere massima IOPS di lettura. Assegnare il nome "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Nota i cambiamenti principali Segui alle linee guida di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per unità massime IOPS,

-   Una profondità coda elevato di 256 caratteri.  
-   Dimensioni blocco piccolo di 8KB.  
-   Più thread eseguono casuale scrive.

Eseguire il seguente comando per avviare il test FIO per 30 secondi,

    sudo fio --runtime 30 fioread.ini

Durante l'esecuzione di test, sarà possibile visualizzare il numero di leggere IOPS macchina virtuale e l'esecuzione di dischi Premium. Come mostrato nell'esempio seguente, la macchina virtuale DS14 offre più di 64.000 IOPS di lettura. Questa è una combinazione del disco e le prestazioni della cache.  
    ![](media/storage-premium-storage-performance/image12.png)

*IOPS massime di lettura e scrittura*  
Creare il file di lavoro con seguenti specifiche per ottenere massima combinati lettura e scrittura IOPS. Assegnare il nome "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Nota i cambiamenti principali Segui alle linee guida di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per unità massime IOPS,

-   Una profondità coda elevato di 128.  
-   Dimensione di blocco di piccole dimensioni di 4KB.  
-   Più thread eseguono casuale leggere e scrivere.

Eseguire il seguente comando per avviare il test FIO per 30 secondi,

    sudo fio --runtime 30 fioreadwrite.ini

Durante l'esecuzione di test, sarà possibile visualizzare il numero di combinato lettura e scrittura IOPS macchina virtuale e l'esecuzione di dischi Premium. Come mostrato nell'esempio seguente, la macchina virtuale DS14 è l'esecuzione di più di 100.000 lettura combinati e scrivere IOPS. Questa è una combinazione del disco e le prestazioni della cache.  
    ![](media/storage-premium-storage-performance/image13.png)

*Massimo combinato velocità*  
Per ottenere il massimo combinati lettura e scrittura effettiva, utilizzare una dimensione maggiore per il blocco e antero-posteriore coda di grandi dimensioni con più thread eseguono lettura e scrittura. È possibile utilizzare una dimensione di blocco di 64KB e profondità coda di 128.

## <a name="next-steps"></a>Passaggi successivi  

Ulteriori informazioni sull'archiviazione di Azure Premium:

- [Spazio di archiviazione Premium: Spazio di archiviazione High-Performance per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md)  

Per gli utenti di SQL Server, articoli sulle prestazioni procedure consigliate per SQL Server:

- [Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Lo spazio di archiviazione di Azure Premium offre prestazioni più elevate per SQL Server in macchine Virtuali di Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
