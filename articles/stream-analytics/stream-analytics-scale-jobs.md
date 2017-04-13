<properties
    pageTitle="Ridimensionare processi flusso Analitica per aumentare la velocità | Microsoft Azure"
    description="Informazioni su come scalare processi Analitica flusso per la configurazione delle partizioni inpue, l'ottimizzazione la definizione della query e l'impostazione processo streaming unità."
    keywords="dati di flusso, il flusso di elaborazione dei dati, ottimizzare analitica"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Ridimensionare i processi di Azure flusso Analitica per aumentare la velocità di elaborazione dei dati di flusso

Informazioni su come ottimizzare i processi analitica e calcolare *streaming unità* di flusso Analitica, come scala processi flusso Analitica la configurazione delle partizioni inpue, ottimizzazione la definizione della query analitica e impostando processo streaming unità. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quali sono le parti di un processo Analitica flusso?
Definizione di un processo di flusso Analitica include ingressi, di una query e di uscita. Ingressi provengono da nel punto in cui il processo di legge il flusso di dati, la query viene utilizzata per trasformare il flusso di input di dati e l'output è nel punto in cui il processo invia i risultati della processo.  

Un processo richiede almeno un'origine di input per il flusso di dati. L'origine di input di flusso di dati può essere archiviato in un evento Hub di Azure servizio Bus o in archiviazione Blob Azure. Per ulteriori informazioni, vedere [Introduzione ai Analitica flusso Azure](stream-analytics-introduction.md) e [iniziare a utilizzare Azure flusso Analitica](stream-analytics-get-started.md).

## <a name="configuring-streaming-units"></a>Configurazione di unità di trasmissione
Unità di trasmissione (SUs) rappresentano le risorse e potenza necessari per eseguire un processo di Azure flusso Analitica. SUs consentono di descrizione dell'evento relativa elaborazione capacità in base a una misura fusione di CPU e memoria, leggere e scrivere tariffe. Ogni flusso unità corrisponde a circa 1MB/secondo di trasmissione. 

Scelta di SUs quanti sono necessari per un determinato processo dipenderà dalla configurazione partizione per l'input e la query definite per il processo. È possibile selezionare tutte le quote in streaming unità di misura di un processo tramite il portale classica Azure. Ogni sottoscrizione Azure per impostazione predefinita è disponibile una quota di tutte le 50 unità flusso per tutti i processi di analitica in un'area specifica. Per aumentare flusso unità per le proprie sottoscrizioni, contattare il [Supporto tecnico Microsoft](http://support.microsoft.com).

Il numero di unità di flusso che può essere utilizzata in un processo dipende dalla configurazione partizione per l'input e la query definite per il processo. Si noti inoltre che deve essere utilizzato un valore valido per le unità del flusso. I valori validi comincia da 1, 3, 6 e quindi verso l'alto in incrementi di 6, come illustrato di seguito.

![Flusso Azure Analitica flusso unità scala][img.stream.analytics.streaming.units.scale]

In questo articolo illustra come calcolare e ottimizzare la query per aumentare la velocità per i processi analitica.

## <a name="embarrassingly-parallel-job"></a>Processo imbarazzanti in parallelo
Il processo imbarazzanti parallelo è lo scenario più scalable che abbiamo Azure flusso Analitica. Una partizione di input a un'istanza della query si connette a una partizione di output. Per questo parallelismo è necessaria una alcuni aspetti:

1.  Se la logica di query dipende la stessa chiave elaborata dalla stessa istanza di query, è necessario assicurarsi che gli eventi passare alla stessa partizione dell'input. In caso di evento hub, significa che i dati dell'evento deve avere **PartitionKey** impostato o è possibile utilizzare i mittenti partizionati. Per Blob, ciò significa che gli eventi vengono inviati nella stessa cartella di partizione. Se la logica di query non richiede la stessa chiave verrà eseguita dalla stessa istanza di query, è possibile ignorare questo requisito. Un esempio è una query di selezione/progetto/filtro semplice.  
2.  Una volta i dati sono disposti come deve essere sul lato input, è necessario assicurarsi che la query è suddiviso. Questa operazione è necessario usare **Partizione** in tutti i passaggi. Sono consentiti più passaggi, ma verranno tutti devono essere suddivise dalla stessa chiave. Un altro aspetto importante è che, attualmente, il tasto partizione deve essere impostata su **PartitionId** disporre di un processo completamente parallelo.  
3.  Solo gli hub di eventi e Blob supportano attualmente output partizionata. Per l'output hub di evento, è necessario configurare il campo **PartitionKey** per essere **PartitionId**. Per Blob, non occorre fare nulla.  
4.  Un altro aspetto importante, il numero di input partizioni deve essere uguale il numero di output partizioni. Output BLOB attualmente non supporta le partizioni, ma questo è possibile perché verrà eredita la combinazione di partizione della query padre. Esempi di valori di partizione che consentono di un processo completamente in parallelo.  
    1.  8 evento hub input partizioni e 8 evento hub output partizioni
    2.  8 evento hub input partizioni e Blob Output  
    3.  8 partizioni inpue blob e Blob Output  
    4.  8 partizioni inpue blob e 8 evento hub output partizioni  

Ecco alcuni scenari di esempio imbarazzanti paralleli.

### <a name="simple-query"></a>Query semplice
Hub di evento input-evento hub con 8 partizioni Output – con 8 partizioni

**Query:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Si tratta di un filtro semplice e di conseguenza, non occorre preoccuparsi di partizione input che si invia a un hub di evento. Si noterà che la query è **Partizione di base** di **PartitionId**, pertanto abbiamo soddisfare requisiti #2 dall'alto. Per l'output, è necessario configurare l'output hub di eventi del processo per il campo **PartitionKey** impostato su **PartitionId**. Un controllo ultimo, partizioni inpue = = output partizioni. Questa topologia è imbarazzanti in parallelo.

### <a name="query-with-grouping-key"></a>Query con chiave di raggruppamento
Archivio Blob di input-evento hub con 8 partizioni Output:

**Query:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Query con una chiave di raggruppamento e di conseguenza, la stessa chiave deve essere elaborato dalla stessa istanza di query. Di conseguenza, che è necessario inviare il nostro eventi a un hub di eventi in modo partizionato. La chiave è importante sulle? **PartitionId** è un concetto di logica di processo, che interessante è **TollBoothId**. Di conseguenza, è necessario impostare **PartitionKey** dei dati dell'evento è inviare a un hub di eventi a essere **TollBoothId** dell'evento. La query contiene **Partizione da** di **PartitionId**, non è utile non esiste. Per l'output, poiché si tratta Blob, non occorre preoccuparsi della configurazione **PartitionKey**. Per questo requisito #4, anche in questo caso Blob, in modo che non occorre preoccuparsi. Questa topologia è imbarazzanti in parallelo.

### <a name="multi-step-query-with-grouping-key"></a>Query con più passaggio con chiave di raggruppamento ###
Hub di evento di input: evento Hub con 8 partizioni Output – con 8 partizioni

**Query:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Query con una chiave di raggruppamento e di conseguenza, la stessa chiave deve essere elaborato dalla stessa istanza di query. È possibile utilizzare la stessa strategia come la query precedente. La query ha più passaggi. Istruzioni dettagliate dispone **Partizione di base** di **PartitionId**? Sì, non è valida. Per l'output, è necessario impostare **PartitionKey** su **PartitionId** come in precedenza ed è inoltre possibile notare che ha lo stesso numero di partizioni come input. Questa topologia è imbarazzanti in parallelo.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Scenari di esempio che non sono imbarazzanti parallelo

### <a name="mismatched-partition-count"></a>Numero di partizioni non corrispondenti ###
Hub di evento input-evento hub con 8 partizioni Output – con 32 partizioni

Non è importante la query novità in questo caso perché l'input partizione conteggio! = numero di partizioni di output.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>Non si usa evento hub o BLOB come output
Ottenere informazioni di input-evento hub con 8 partizioni Output:

Ottenere informazioni output attualmente non supporta partizioni.

### <a name="multi-step-query-with-different-partition-by-values"></a>Query passaggio con più valori diversi partizione da
Hub di evento input-evento Hub con 8 partizioni Output – con 8 partizioni

**Query:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Come si può notare, il secondo passaggio utilizza **TollBoothId** come chiave partizione. Questo non è diverso da quello del primo passaggio e pertanto richiedere di eseguire una casuale. 

Ecco alcuni esempi e counterexamples dei processi flusso Analitica che saranno in grado di ottenere una topologia imbarazzanti in parallelo ed è la possibilità di scala massima. Per i processi che non corrispondono a uno di questi profili, saranno aggiornamenti futuri dettaglio come quello ottenuto scalare alcuni degli altri scenari flusso Analitica canonici.

Per effettuare adesso per l'uso della Guida generale riportata di seguito:

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcolare il numero massimo di unità di un processo di flusso
Il numero totale di unità di flusso che può essere utilizzato da un processo Analitica flusso dipende dal numero di passaggi di query definiti per il processo e il numero delle partizioni per ogni passaggio.

### <a name="steps-in-a-query"></a>Procedura in una query
Una query può avere uno o più passaggi. Ogni passaggio è una sottoquery definita mediante la parola chiave **WITH** . La query sola che non rientra la parola chiave **WITH** anche viene conteggiata come un'azione, ad esempio, l'istruzione **SELECT** nella query seguente:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

La query precedente contiene due passaggi.

> [AZURE.NOTE] La query di esempio è spiegata avanti in questo articolo.

### <a name="partition-a-step"></a>Dividere un passaggio

Partizione un passaggio richiede le condizioni seguenti:

- L'origine di input deve essere suddiviso. Per ulteriori informazioni, vedere la [Guida alla programmazione di eventi hub](../event-hubs/event-hubs-programming-guide.md).
- L'istruzione **SELECT** della query deve leggere da un'origine di input partizionata.
- La query all'interno del passo deve avere la parola chiave **Partizione By**

Quando una query è suddiviso, gli eventi di input sarà partizione separata elaborati e aggregato in gruppi e gli eventi di output vengono generati per ognuno dei gruppi. Se una funzione di aggregazione combinato è utile, è necessario creare un secondo passaggio non suddiviso da aggregare.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcolare del limite massimo di unità di misura di un processo di flusso

Tutti i passaggi non suddiviso insieme ridimensionare fino a sei unità di misura trasmissione di un processo di flusso Analitica. Per aggiungere ulteriori streaming unità di un passaggio deve essere suddiviso. Ogni partizione può avere sei unità flusso.

<table border="1">
<tr><th>Query di un processo</th><th>Numero massimo di unità per il processo di flusso</th></td>

<tr><td>
<ul>
<li>La query contiene un unico passaggio.</li>
<li>Il passaggio non è suddiviso.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Il flusso di dati di input viene suddivise da 3.</li>
<li>La query contiene un unico passaggio.</li>
<li>Il passaggio è suddiviso.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La query contiene due passaggi.</li>
<li>Nessuno dei passaggi suddiviso.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>L'immissione di flusso dei dati è suddivise da 3.</li>
<li>La query contiene due passaggi. Il passaggio input è suddiviso e non è il secondo passaggio.</li>
<li>L'istruzione SELECT legge dall'input partizionata.</li>
</ul>
</td>
<td>24 (18 per la procedura partizionata) + 6 per la procedura non suddiviso</td></tr>
</table>

### <a name="examples-of-scale"></a>Esempi di scala
La query seguente calcola il numero di auto passano attraverso un numero a pagamento di espansione con tre tollbooths all'interno di una finestra di tre minuti. Questa query può essere ridimensionata fino a sei unità flusso.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Per utilizzare più unità di flusso per la query, i dati di flusso di input e la query deve essere suddiviso. Dato che la partizione di flusso di dati è impostata su 3, è possibile ridimensionare la query seguente modificata tutte le unità di trasmissione 18:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando una query è suddiviso, gli eventi di input vengono elaborati e aggregati in gruppi di partizioni separate. Eventi di output vengono inoltre generati per ognuno dei gruppi. Partizioni può causare alcuni risultati imprevisti quando il campo **Group by** non è la chiave partizione l'immissione di flusso dei dati. Ad esempio, il campo **TollBoothId** nella query di esempio precedente non è la chiave di Input1 partizione. I dati da caselli #1 possono essere suddiviso in più partizioni.

Ciascuna delle partizioni Input1 verrà elaborato separatamente dal flusso Analitica e più record del conteggio passaggio tramite Auto per la stessa caselli nella stessa finestra ha verrà creato. Se non è possibile modificare la chiave partizione input questo problema può essere risolto mediante l'aggiunta di un'operazione non partizione aggiuntiva, ad esempio:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Questa query sono adatti alle 24 streaming unità.

>[AZURE.NOTE] Se si esegue il join di due flussi, assicurarsi che i flussi sono suddivise da chiave di partizione della colonna che si eseguire il join e avere lo stesso numero di partizioni in entrambi i flussi.


## <a name="configure-stream-analytics-job-partition"></a>Configurare Analitica flusso processo partizione

**Per modificare un'unità di flusso per un processo**

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Nel riquadro a sinistra, fare clic su **Flusso Analitica** .
3. Fare clic su processo flusso Analitica che si desidera ridimensionare.
4. Fare clic su **scala** nella parte superiore della pagina.

![Flusso Azure Analitica flusso unità scala][img.stream.analytics.streaming.units.scale]

Nel portale di Azure scala possibile accedere a impostazioni in impostazioni:

![Configurazione di Azure portale Analitica di flusso del processo][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Monitorare le prestazioni del processo

Tramite il portale di gestione, è possibile registrare la velocità di un processo in eventi/secondo:

![Azure flusso Analitica monitorare i processi][img.stream.analytics.monitor.job]

Calcolare la velocità prevista del carico di lavoro in eventi/secondo. Se la velocità è minore del previsto, ottimizzare la partizione di input, ottimizzare la query e aggiungere unità flusso aggiuntive per il lavoro.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Flusso Analitica velocità scala - scenario di pi greco Raspberry


Per capire come processi analitica flusso adattarsi in uno scenario tipico in termini di velocità di elaborazione più unità di trasmissione, ecco una prova che invia dati sensore (client) hub di evento, viene elaborato e Invia avviso o statistiche come un output a un altro evento Hub.

Il client invii sensore sintesi dati a un evento hub nei formati JSON Analitica flusso e l'output dei dati è in formato JSON.  Ecco come i dati di esempio risulterebbe simile a:  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Query: "inviare un avviso quando si spegne luce"  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Misurare la velocità effettiva: Velocità in questo contesto è la quantità di dati di input elaborati dal flusso Analitica un valore fisso di tempo (10 minuti). Per ottenere una migliore velocità di elaborazione dei dati di input, i dati di flusso di input e la query deve essere suddiviso. Anche **Count ()**è incluso nella query misurare elaborati quanti eventi di input. Per garantire che il processo non è sufficiente è in attesa gli eventi di input in arrivo, ognuna delle Hub evento Input stato precaricato sufficiente dei dati di input (circa 300MB).  

Di seguito sono i risultati con aumentare il numero di unità di flusso e partizione corrispondenti vengono contati nell'hub evento.  

<table border="1">
<tr><th>Partizioni inpue</th><th>Partizioni di output</th><th>Unità di trasmissione</th><th>Velocità
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
