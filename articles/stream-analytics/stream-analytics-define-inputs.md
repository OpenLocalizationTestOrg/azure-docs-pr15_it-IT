<properties
    pageTitle="Connessione dati: del flusso di dati di input da un flusso di eventi | Microsoft Azure"
    description="Informazioni sull'impostazione di una connessione dati al flusso Analitica denominata 'input'. Ingressi includono un flusso di dati di eventi e anche fanno riferimento a dati."
    keywords="flusso di dati, connessione dati, flusso di eventi"
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

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Connessione dati: informazioni sui dati di input, flusso dall'eventi al flusso Analitica

La connessione dati al flusso Analitica è un flusso di dati di eventi da un'origine dati. Questo comando si chiama "input". Flusso Analitica ha costrutto integrazione con Azure flusso origini evento Hub IoT Hub e Blob di archiviazione dei dati può essere dalla sottoscrizione Azure stessa o in altre come il processo di analitica.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipi di input di dati: dati flusso e dati di riferimento
Come dati vengono spostati a un'origine dati, è utilizzato dal processo flusso Analitica ed elaborata in tempo reale. Ingressi sono suddivise in due diversi tipi: dati flusso di input e di riferimento dei dati di input.

### <a name="data-stream-inputs"></a>Dati di input di flusso
Un flusso di dati è sequenza illimitata di eventi a essere recapitata nel tempo. Flusso Analitica processi devono includere almeno un dato flusso input per essere utilizzato ed trasformato dal processo. Archiviazione BLOB, hub di eventi e IoT hub sono supportati come origini di input flusso di dati. Hub di evento vengono utilizzate per raccogliere i flussi di eventi da più dispositivi e servizi, ad esempio feed attività di social networking, le informazioni finanziarie o dati dai sensori. Hub IoT sono ottimizzati per raccogliere dati da dispositivi connessi in scenari Internet di elementi (IoT).  Archiviazione BLOB può essere utilizzati come un'origine di input per il caricamento di massa dei dati come flusso.  

### <a name="reference-data"></a>Dati di riferimento
Flusso Analitica supporta un secondo tipo di input noto come dati di riferimento. Si tratta dati supplementari statica o modifica lenta nel tempo e che in genere utilizzati per l'esecuzione di correlazione e le ricerche. Archiviazione Blob Azure è attualmente l'origine di input supportato solo per i dati di riferimento. BLOB di origine dati di riferimento sono limitati a 100MB di dimensione.
Per informazioni su come creare riferimento dei dati di input, vedere [Usare i dati di riferimento](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Creare un flusso di dati di input con un Hub di evento

[Gli hub evento Azure](https://azure.microsoft.com/services/event-hubs/) sono scalabilità pubblicazione sottoscrizione ingestor evento. Consente di raccogliere milioni di eventi al secondo, in modo che è possibile elaborare e analizzare grandi quantità di dati prodotti per i dispositivi connessi e le applicazioni. È uno degli input più comunemente usati per flusso Analitica. Hub di eventi e flusso Analitica insieme prevedere clienti una soluzione to end analitica in tempo reale. Hub di evento consentono agli utenti di feed di eventi in Azure in tempo reale e processi flusso Analitica possono elaborare in tempo reale. Ad esempio, clienti possono inviare fa clic su web, lettura dei sensori, eventi online a un evento hub e creare processi flusso Analitica per utilizzare gli hub di evento come i flussi di dati di input per filtrare in tempo reale, aggregare e correlazione.

È importante tenere presente che il timestamp predefinita di eventi provenienti da hub di evento in flusso Analitica timestamp l'evento ricevuti hub evento ossia EventEnqueuedUtcTime. Per elaborare i dati come flusso usando un timestamp nel payload evento, è necessario utilizzare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) .

### <a name="consumer-groups"></a>Gruppi di consumatori

Ogni input flusso Analitica evento Hub deve essere configurato per il proprio gruppo di consumer. Quando un processo contiene un self-join o più input, alcuni input possono essere letti da più di un lettore valle, quale impatto il numero di utilità per la lettura di un gruppo di consumer singola. Per evitare che superano il limite di evento Hub di 5 lettori per ogni gruppo consumer per partizione, è consigliabile designare un gruppo di consumer per ogni processo flusso Analitica. Si noti che è anche un limite di 20 gruppi di consumer per ogni evento Hub. Per informazioni dettagliate, vedere la [Guida alla programmazione di eventi hub](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Configurare Hub evento come un flusso di dati di input

Nella tabella riportata di seguito vengono illustrate ogni proprietà nel caso in cui Hub input scheda con la relativa descrizione:

| NOME DELLA PROPRIETÀ | DESCRIZIONE |
|------|------|
| Alias di input | Un nome descrittivo da utilizzare nella query processo per fare riferimento a questo input |
| Servizio Bus Namespace | Uno spazio dei nomi del servizio Bus è un contenitore di un insieme di messaggistica entità. Quando si crea un nuovo evento Hub, è inoltre creato uno spazio dei nomi Bus di servizio. |
| Hub di evento | Il nome dell'Hub di eventi di input. |
| Nome del criterio Hub evento | I criteri di accesso condiviso, che possono essere creati nella scheda evento Hub configurare. Ogni criterio di accesso condiviso verrà dispone di un nome, le autorizzazioni impostate, e tasti di scelta. |
| Chiave dei criteri di evento Hub | Il tasto di scelta condivisi utilizzato per eseguire l'autenticazione accesso allo spazio dei nomi Bus di servizio. |
| Gruppo Consumer Hub evento (facoltativo) | Gruppo Consumer per acquisire dati da Hub evento. Se non viene specificato, processi flusso Analitica utilizzerà il gruppo di Consumer predefinita per l'acquisizione di dati dall'Hub evento. È consigliabile utilizzare un gruppo di consumer distinto per ogni processo flusso Analitica. |
| Formato di serializzazione evento | Per assicurarsi che le query nel modo desiderato, è necessario sapere quale formato di serializzazione (JSON, CSV o Avro) Analitica flusso di lavoro che si usa per i flussi di dati in arrivo. |
| Codifica | UTF-8 è il formato di codifica supportato solo in questo momento. |

Quando i dati provenienti da un'origine evento Hub, è possibile accedere ai campi di metadati alcuni nella query flusso Analitica. Nella tabella riportata di seguito sono elencati i campi e la relativa descrizione.

| PROPRIETÀ | DESCRIZIONE |
|------|------|
| EventProcessedUtcTime | Data e ora in cui l'evento è stata elaborata dal flusso Analitica. |
| EventEnqueuedUtcTime | Data e ora in cui l'evento è stato ricevuto da hub di evento. |
| PartitionId | L'ID di partizione in base zero per la scheda input. |

Ad esempio, è possibile scrivere una query simile alla seguente:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Creare un flusso di dati di IoT Hub input

Azure Iot Hub è una scalabilità pubblicazione sottoscrizione ingestor evento ottimizzato per gli scenari IoT.
È importante tenere presente che il timestamp predefinita di eventi provenienti da hub IoT in flusso Analitica timestamp l'evento ricevuti hub IoT ossia EventEnqueuedUtcTime. Per elaborare i dati come flusso usando un timestamp nel payload evento, è necessario utilizzare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) .

> [AZURE.NOTE] È possibile elaborare solo i messaggi inviati con una proprietà DeviceClient.

### <a name="consumer-groups"></a>Gruppi di consumatori

Ogni input flusso Analitica IoT Hub deve essere configurato per il proprio gruppo di consumer. Quando un processo contiene un self-join o più input, alcuni input possono essere letti da più di un lettore valle, quale impatto il numero di utilità per la lettura di un gruppo di consumer singola. Per evitare che superano il limite di IoT Hub di 5 lettori per ogni gruppo consumer per partizione, è consigliabile designare un gruppo di consumer per ogni processo flusso Analitica.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Configurare IoT Hub come un flusso di dati di input

Nella tabella riportata di seguito vengono illustrate ogni proprietà nella scheda input IoT Hub con la relativa descrizione:

| NOME DELLA PROPRIETÀ | DESCRIZIONE |
|------|------|
| Alias di input | Un nome descrittivo da utilizzare nella query processo per fare riferimento a questo input. |
| Hub IoT | Un IoT Hub è un contenitore di un insieme di entità messaggistica. |
| Punto finale | Il nome dell'endpoint IoT Hub. |
| Nome del criterio di accesso condiviso | Criterio di accesso condiviso per concedere l'accesso a IoT Hub. Ogni criterio di accesso condiviso verrà dispone di un nome, le autorizzazioni impostate, e tasti di scelta. |
| Chiave dei criteri di accesso condiviso | Il tasto di scelta condivisi utilizzato per eseguire l'autenticazione di accesso a IoT Hub. |
| Gruppo consumer (facoltativo) | Gruppo Consumer per acquisire dati da IoT Hub. Se non viene specificato, processi flusso Analitica utilizzerà il gruppo di Consumer predefinita per l'acquisizione di dati dall'IoT Hub. È consigliabile utilizzare un gruppo di consumer distinto per ogni processo flusso Analitica. |
| Formato di serializzazione evento | Per assicurarsi che le query nel modo desiderato, è necessario sapere quale formato di serializzazione (JSON, CSV o Avro) Analitica flusso di lavoro che si usa per i flussi di dati in arrivo. |
| Codifica | UTF-8 è il formato di codifica supportato solo in questo momento. |

Quando i dati provenienti da un'origine IoT Hub, è possibile accedere ai campi di metadati alcuni nella query flusso Analitica. Nella tabella riportata di seguito sono elencati i campi e la relativa descrizione.

| PROPRIETÀ | DESCRIZIONE |
|------|------|
| EventProcessedUtcTime | Data e ora dell'elaborazione dell'evento. |
| EventEnqueuedUtcTime | Data e ora in cui l'evento è stato ricevuto dall'IoT Hub. |
| PartitionId | L'ID di partizione in base zero per la scheda input. |
| IoTHub.MessageId | Consente di creare una relazione tra di comunicazione bidirezionali IoT hub. |
| IoTHub.CorrelationId | Utilizzata per le risposte ai messaggi e il feedback IoT hub. |
| IoTHub.ConnectionDeviceId | Id autenticato utilizzato per inviare il messaggio contrassegnato nei messaggi servicebound dall'IoT Hub. |
| IoTHub.ConnectionDeviceGenerationId | GenerationId del dispositivo autenticato utilizzato per inviare il messaggio contrassegnato nei messaggi servicebound dall'IoT Hub. |
| IoTHub.EnqueuedTime | Ora quando il messaggio è stato ricevuto dall'IoT Hub. |
| IoTHub.StreamId | Proprietà di evento personalizzato aggiunto dal dispositivo mittente. |

## <a name="create-a-blob-storage-data-stream-input"></a>Creare un input di flusso dei dati dello spazio di archiviazione Blob

Per scenari con grandi quantità di dati non strutturati per l'archiviazione nel cloud, archiviazione Blob offre una soluzione conveniente e scalabilità. Dati in [archiviazione Blob](https://azure.microsoft.com/services/storage/blobs/) viene generalmente considerati come dati "inattivi", ma possono essere elaborato come un flusso di dati dal flusso Analitica. Uno scenario comune per gli input di spazio di archiviazione Blob con flusso Analitica è l'elaborazione di log, dove telemetria acquisita da un sistema e deve essere analizzati ed elaborati per estrarre dati significativi.

È importante tenere presente che il timestamp predefinito degli eventi di spazio di archiviazione Blob nel flusso Analitica timestamp che il blob dell'ultima modifica quali *isBlobLastModifiedUtcTime*. Per elaborare i dati come flusso usando un timestamp nel payload evento, è necessario utilizzare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) .

Si noti inoltre che CSV formattati come input di **richiedere** una riga di intestazione per definire i campi per il set di dati. Ulteriori campi di intestazione di riga devono essere **univoco**.

> [AZURE.NOTE] Flusso Analitica non supporta l'aggiunta di contenuto a un blob esistente. Flusso Analitica visualizzeranno un blob solo una volta e le modifiche effettuate dopo la lettura non verrà eseguita. La procedura consigliata consiste nel caricare una sola volta tutti i dati e non aggiungere eventuali altri eventi a un archivio blob.

Nella tabella riportata di seguito vengono illustrate ogni proprietà nella scheda input dello spazio di archiviazione Blob con la relativa descrizione:

<table>
<tbody>
<tr>
<td>NOME DELLA PROPRIETÀ</td>
<td>DESCRIZIONE</td>
</tr>
<tr>
<td>Alias di input</td>
<td>Un nome descrittivo da utilizzare nella query processo per fare riferimento a questo input.</td>
</tr>
<tr>
<td>Account di archiviazione</td>
<td>Il nome dell'account di archiviazione in cui si trovano i file di archivio blob.</td>
</tr>
<tr>
<td>Chiave Account lo spazio di archiviazione</td>
<td>Segreto associato all'account di archiviazione.</td>
</tr>
<tr>
<td>Contenitore di spazio di archiviazione
</td>
<td>I contenitori forniscono un raggruppamento logico per BLOB archiviati nel servizio di Microsoft Azure Blob. Quando si carica un blob al servizio Blob, è necessario specificare un contenitore per tale blob.</td>
</tr>
<tr>
<td>Percorso prefisso motivo [facoltativo]</td>
<td>Il percorso del file utilizzato per individuare il BLOB all'interno del contenitore specificato.
All'interno del percorso, è possibile specificare una o più istanze delle variabili di 3 seguenti:<BR>{date}, {time}<BR>{partizione}<BR>Esempio 1: cluster1/log / {date} / {ora} / {partizione}<BR>Esempio 2: cluster1/log / {date}<P>Si noti che "*" non è un valore consentito per pathprefix. Solo validi <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">i caratteri blob Azure</a> consentiti.</td>
</tr>
<tr>
<td>Formato di data [facoltativo]</td>
<td>Se nel percorso prefisso viene utilizzato il token di data, è possibile selezionare il formato di data in cui sono organizzati i file. Esempio: Gg/MM/aaaa</td>
</tr>
<tr>
<td>Formato ora [facoltativo]</td>
<td>Se nel percorso prefisso viene utilizzato il token di tempo, specificare il formato di ora in cui sono organizzati i file. L'unico valore supportato è al momento HH.</td>
</tr>
<tr>
<td>Formato di serializzazione evento</td>
<td>Per assicurarsi che le query nel modo desiderato, è necessario sapere quale formato di serializzazione (JSON, CSV o Avro) Analitica flusso di lavoro che si usa per i flussi di dati in arrivo.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Per CSV e JSON, UTF-8 è il formato di codifica supportato solo in questo momento.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Flusso Analitica supporta un numero di delimitatori comuni per serializzazione dei dati in formato CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
</tbody>
</table>

Quando sarà disponibile a breve i dati da una fonte di spazio di archiviazione Blob, è possibile accedere alcuni campi di metadati nella query flusso Analitica. Nella tabella riportata di seguito sono elencati i campi e la relativa descrizione.

| PROPRIETÀ | DESCRIZIONE |
|------|------|
| BlobName | Il nome del blob input proviene l'evento. |
| EventProcessedUtcTime | Data e ora in cui l'evento è stata elaborata dal flusso Analitica. |
| BlobLastModifiedUtcTime | Data e ora dell'ultima modifica il blob. |
| PartitionId | L'ID di partizione in base zero per la scheda input. |

Ad esempio, è possibile scrivere una query simile alla seguente:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle opzioni di connessione dati di Azure appreso per i processi di flusso Analitica. Per ulteriori informazioni sulle flusso Analitica, vedere:

- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
