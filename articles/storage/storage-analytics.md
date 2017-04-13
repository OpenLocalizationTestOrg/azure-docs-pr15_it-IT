<properties
    pageTitle="Utilizzare lo spazio di archiviazione Analitica per raccogliere dati metriche e registri | Microsoft Azure"
    description="Spazio di archiviazione Analitica consente di tenere traccia dei dati metriche per tutti i servizi di archiviazione e raccogliere i registri per lo spazio di archiviazione Blob, coda e una tabella."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Analitica lo spazio di archiviazione

## <a name="overview"></a>Panoramica

Azure Analitica lo spazio di archiviazione esegue la registrazione e fornisce i dati metriche per un account di archiviazione. È possibile usare questi dati per le richieste di analisi, analizzare le tendenze di utilizzo e diagnosticare i problemi con l'account di archiviazione.

Per utilizzare lo spazio di archiviazione Analitica, è necessario attivare il singolarmente per ogni servizio che si desidera eseguire il monitoraggio. È possibile abilitare dal [Portale di Azure](https://portal.azure.com). Per informazioni dettagliate, vedere [Monitor di un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md). È anche possibile attivare Analitica lo spazio di archiviazione a livello di programmazione tramite l'API REST o alla raccolta di client. Utilizzare le operazioni [Ottieni Blob servizio proprietà](https://msdn.microsoft.com/library/hh452239.aspx), [Ottenere proprietà del servizio di coda](https://msdn.microsoft.com/library/hh452243.aspx), [Ottenere proprietà del servizio di tabella](https://msdn.microsoft.com/library/hh452238.aspx)e [Ottenere proprietà di File del servizio](https://msdn.microsoft.com/library/mt427369.aspx) per abilitare Analitica lo spazio di archiviazione per ogni servizio.

Dati aggregati vengono archiviati in un blob noto (per l'accesso) e nelle tabelle note (per le metriche), che è possibile accedere tramite il servizio di Blob e API del servizio di tabella.

Spazio di archiviazione Analitica ha un limite di 20 TB alla quantità di dati memorizzati indipendente del limite di totale per l'account di archiviazione. Per ulteriori informazioni sulla fatturazione e i criteri di conservazione dei dati, vedere [Analitica lo spazio di archiviazione e la fatturazione](https://msdn.microsoft.com/library/hh360997.aspx). Per ulteriori informazioni sui limiti di archiviazione account, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](storage-scalability-targets.md).

Per una Guida dettagliata sull'utilizzo di archiviazione Analitica e altri strumenti per identificare, diagnosi e risoluzione dei problemi relativi a spazio di archiviazione di Azure, vedere [Monitor, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>Informazioni sulla registrazione Analitica lo spazio di archiviazione

Spazio di archiviazione Analitica registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per eseguire il monitoraggio delle singole richieste e diagnosticare i problemi relativi a un servizio di archiviazione. Le richieste di connessi alla scala cronologica usufruendo.

Voci di log vengono create solo se è presente attività del servizio di archiviazione. Ad esempio, se un account di archiviazione include attività nel servizio corrispondente Blob ma non in relativi servizi di tabella o una coda, verranno creati solo i registri relativi al servizio Blob.

Spazio di archiviazione Analitica registrazione non è disponibile per il servizio di File di Azure.

### <a name="logging-authenticated-requests"></a>Richieste di accesso autenticato

Si è connessi i tipi di richieste autenticate seguenti:

- Richieste completate.

- Non è possibile richieste, inclusi timeout, limitazione, rete, autorizzazioni e altri errori.

- Richieste utilizzando un condiviso Access firma (SA), incluse le richieste di riusciti e non riuscite.

- Richieste di dati analitica.

Richieste effettuate da Analitica lo spazio di archiviazione, ad esempio la creazione di log o l'eliminazione, non vengono registrate. Un elenco completo dei dati registrati descritte negli argomenti [operazioni registrate Analitica di spazio di archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) e [Lo spazio di archiviazione Analitica Log formato](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Registrazione delle richieste anonime
I tipi seguenti di richieste anonimi connessi:

- Richieste completate.

- Errori di server.

- Errori di timeout per client e server.

- Richieste non riuscite GET con codice errore 304 (non modificati).

Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati descritte negli argomenti [operazioni registrate Analitica di spazio di archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) e [Lo spazio di archiviazione Analitica Log formato](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>Modalità di archiviazione dei log
Tutti i log sono archiviati in BLOB blocco in un contenitore denominato $logs, viene creata automaticamente quando lo spazio di archiviazione Analitica è attivata per un account di archiviazione. Il contenitore $logs è disponibile dello spazio dei nomi di blob dell'account di archiviazione, ad esempio: `http://<accountname>.blob.core.windows.net/$logs`. In questo contenitore non può essere eliminato dopo lo spazio di archiviazione Analitica è stata attivata, anche se il relativo contenuto può essere eliminato.

>[Azure.NOTE] Il contenitore $logs non viene visualizzato quando un contenitore voce operazione viene eseguito, ad esempio il metodo [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . È necessario accedere direttamente. Ad esempio, è possibile utilizzare il metodo [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) per accedere a BLOB nel `$logs` contenitore.
Mentre si è connessi richieste, lo spazio di archiviazione Analitica sarà caricato risultati intermedi come blocchi. Periodicamente, lo spazio di archiviazione Analitica verranno commit questi blocchi e renderli disponibili come blob.

I record duplicati anche per i log creati della stessa ora. È possibile determinare se un record è un duplicato selezionando il numero di **ID richiesta** e il **funzionamento** .

### <a name="log-naming-conventions"></a>Registro convenzioni di denominazione
Ogni log verranno scritti nel formato seguente.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Nella tabella seguente vengono descritti gli attributi nel nome del registro.

| Attributo         | Descrizione                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < nome servizio >    | Il nome del servizio di archiviazione. Ad esempio: blob, una tabella o coda.                                                                                                                          |
| AAAA              | Anno a quattro cifre per il log. Ad esempio: 2011.                                                                                                                                           |
| MM                | Il mese a due cifre per il log. Ad esempio: 07.                                                                                                                                             |
| GG                | Il mese a due cifre per il log. Ad esempio: 07.                                                                                                                                             |
| hh                | L'ora a due cifre che indica l'ora di inizio per i log in formato UTC 24 ore. Ad esempio: 18.                                                                                     |
| mm                | Il numero di due cifre che indica il minuto iniziale per i log. Questo valore non è supportato nella versione corrente di spazio di archiviazione Analitica, e il relativo valore viene mantenuto 00.     |
| <counter>         | Contatore in base zero con sei cifre che indica il numero di BLOB log generati per il servizio di archiviazione in un'ora periodo di tempo. Questo contatore compreso 000000. Ad esempio: 000001.     |

Di seguito è un nome di log di esempio completo che combina degli esempi precedenti.

    blob/2011/07/31/1800/000001.log

Di seguito è un esempio di URI possono essere usato per accedere al log precedente.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Quando una richiesta di spazio di archiviazione è connesso, il nome del log risultante correlato all'ora quando l'operazione completata. Ad esempio, se è stata completata una richiesta di GetBlob ore 6:30. il 31/7/2011, il log è verrebbero il prefisso seguenti:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadati di log
Tutti i log BLOB vengono archiviati con metadati che possono essere utilizzati per identificare i dati di registrazione il blob contiene. Nella tabella seguente vengono descritti gli attributi di metadati.

| Attributo     | Descrizione                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | Descrive se il log contiene le informazioni relative per leggere, scrivere o operazioni di eliminazione. Questo valore può includere un solo tipo o una combinazione di tutti e tre, separati da virgole. Esempio 1: scrivere. Esempio 2: lettura, scrittura; Esempio 3: lettura, scrittura, eliminazione.    |
| Ora di inizio     | La prima volta una voce nel log delle sotto forma di YYYY-MM-DDThh:mm:ssZ. Ad esempio: 2011-07-31T18:21:46Z.                                                                                                                                             |
| Ora fine       | L'ultima volta una voce nel log, sotto forma di YYYY-MM-DDThh:mm:ssZ. Ad esempio: 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | Versione del formato di log. Al momento l'unico valore supportato è 1.0.                                                                                                                                                                                     |

L'elenco seguente consente di visualizzare i metadati di esempio completo tramite degli esempi precedenti.

- LogType = scrittura

- Ora di inizio = 2011-07-31T18:21:46Z

- Ora fine = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>Accesso ai dati di registrazione

Tutti i dati di `$logs` contenitore accessibili tramite il servizio di Blob API, comprese le API .NET fornite da di Azure libreria gestita. L'amministratore dell'account di archiviazione leggere ed eliminare log, ma non è possibile creare o aggiornarli. Metadati del log e nome del log utilizzabili durante la ricerca di un file di log. È possibile che i registri di una determinata ora visualizzare nell'ordine corretto, ma i metadati specificano sempre il periodo di tempo di voci di log in un file di log. Pertanto, è possibile utilizzare una combinazione di nomi dei log e dei metadati quando si cerca un particolare file di log.

## <a name="about-storage-analytics-metrics"></a>Su metriche Analitica lo spazio di archiviazione

Spazio di archiviazione Analitica consentono di memorizzare metriche contenenti transazione aggregato statistiche e capacità dati sulle richieste a un servizio di archiviazione. Le transazioni vengono segnalate sia a livello dell'API operazione oltre che al livello del servizio di archiviazione e la capacità viene riportata al livello del servizio di archiviazione. Metrica dati possono essere utilizzati per analizzare l'utilizzo del servizio di archiviazione, diagnosticare i problemi con le richieste di eseguire il servizio di archiviazione e per migliorare le prestazioni di applicazioni che utilizzano un servizio.

Per utilizzare lo spazio di archiviazione Analitica, è necessario attivare il singolarmente per ogni servizio che si desidera eseguire il monitoraggio. È possibile abilitare dal [Portale di Azure](https://portal.azure.com). Per informazioni dettagliate, vedere [Monitor di un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md). È anche possibile attivare Analitica lo spazio di archiviazione a livello di programmazione tramite l'API REST o alla raccolta di client. Utilizzare le operazioni di **Ottenere le proprietà del servizio** per abilitare Analitica lo spazio di archiviazione per ogni servizio.

### <a name="transaction-metrics"></a>Metriche transazione

Un set di dati è registrato intervalli di ore o minuti per ogni servizio di archiviazione e richiesto operazione, API, ad esempio in ingresso/uscita, disponibilità, errori e classificati percentuali richiesta. È possibile visualizzare un elenco completo dei dettagli transazione nell'argomento [Schema tabella metriche di archiviazione Analitica](https://msdn.microsoft.com/library/hh343264.aspx) .

Dati delle transazioni sono registrati su due livelli: il livello di servizio e il livello di API. Livello di servizio statistiche riepilogo di tutti richiesto operazioni API vengono scritte in una tabella di entità ogni ora anche se non esistono richieste sono state apportate al servizio. Livello di operazione API statistiche vengono scritte solo a un'entità se è stata richiesta l'operazione all'interno di tale ora.

Ad esempio, se si esegue un'operazione **GetBlob** del servizio Blob, metriche di archiviazione Analitica verranno registrare la richiesta e includere i dati aggregati per il servizio Blob sia, nonché l'operazione **GetBlob** . Tuttavia, se non è richiesta alcuna operazione **GetBlob** durante l'ora, un'entità non essere scritti per il `$MetricsTransactionsBlob` per tale operazione.

Metriche transazione vengono registrate per le richieste degli utenti e le richieste Analitica archiviazione stessa. Ad esempio, vengono registrate richieste per lo spazio di archiviazione Analitica per scrivere i registri ed entità della tabella. Per ulteriori informazioni su come vengono addebitate queste richieste, vedere [Analitica lo spazio di archiviazione e la fatturazione](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Metrica capacità

>[AZURE.NOTE] Al momento metriche capacità sono disponibili solo per il servizio Blob. Metrica capacità per i servizi di tabella e coda saranno disponibile nelle versioni future di spazio di archiviazione Analitica.

Dati sulla capacità sono registrati ogni giorno per Blob servizio dell'account di archiviazione e vengono scritte due entità della tabella. Un'entità fornisce statistiche per i dati utente e l'altro fornisce statistiche sul `$logs` contenitore blob utilizzato per lo spazio di archiviazione Analitica. il `$MetricsCapacityBlob` tabella include le seguenti statistiche:

- **Capacità**: la quantità di spazio di archiviazione utilizzato dal servizio Blob dell'account di archiviazione, in byte.

- **ContainerCount**: il numero di contenitori blob nel servizio dell'account di archiviazione Blob.

- **ObjectCount**: il numero di BLOB blocco o pagina approvato e non salvate nel servizio dell'account di archiviazione Blob.

Per ulteriori informazioni su metriche capacità, vedere [Schema tabella metriche di archiviazione Analitica](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Modalità di archiviazione metriche di

Tutte le metriche per ognuno dei servizi di archiviazione vengono memorizzati in tre tabelle riservate servizio: una tabella per informazioni sulle transazioni, una tabella per informazioni sulle transazioni minuto e un'altra tabella per informazioni sulla capacità. Informazioni sulle transazioni transazione e minuto è costituito da dati richiesta e risposta e le informazioni sulla capacità include lo spazio di archiviazione dati di utilizzo. Metriche di ora, minute metriche e capacità per servizio Blob dell'account di archiviazione è possibile accedervi in tabelle denominate come descritto nella tabella seguente.

| Livello di metriche                         | Nomi delle tabelle                                                                                                                   | Versioni supportate                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Metrica oraria, posizione principale      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versioni precedenti 2013-08-15 solo. Sebbene i nomi seguenti sono ancora supportati, è consigliabile passare al utilizzando le tabelle elencate di seguito.  |
| Metrica oraria, posizione principale      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Tutte le versioni, tra cui 2013-08-15.                                                                                                               |
| Metrica minuta, posizione principale      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Tutte le versioni, tra cui 2013-08-15.                                                                                                               |
| Metrica oraria, posizione secondaria    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Tutte le versioni, tra cui 2013-08-15. Accesso in lettura geografico ridondanti replica deve essere abilitata.                                                    |
| Metrica minuta, posizione secondaria    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Tutte le versioni, tra cui 2013-08-15. Accesso in lettura geografico ridondanti replica deve essere abilitata.                                                    |
| Capacità (solo servizio Blob)          | $MetricsCapacityBlob                                                                                                          | Tutte le versioni, tra cui 2013-08-15.                                                                                                               |


Le tabelle seguenti vengono create automaticamente quando lo spazio di archiviazione Analitica è attivata per un account di archiviazione. Vi si accede tramite lo spazio dei nomi dell'account di archiviazione, ad esempio:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Accesso ai dati metriche

Tutti i dati nelle tabelle metriche accessibili tramite l'API del servizio di tabella, incluse le API .NET fornite da di Azure gestite raccolta. L'amministratore dell'account di archiviazione leggere ed eliminare entità della tabella, ma non è possibile creare o aggiornarli.

## <a name="billing-for-storage-analytics"></a>Fatturazione per lo spazio di archiviazione Analitica

Spazio di archiviazione Analitica è attivata per un proprietario dell'account di archiviazione; non è attivata per impostazione predefinita. Tutti i dati di metrica è scritto da servizi di un account di archiviazione. Di conseguenza, ogni operazione di scrittura eseguita da Analitica lo spazio di archiviazione è fatturabile. Inoltre, la quantità di spazio di archiviazione utilizzato dai dati metriche è fatturabile.

Le seguenti azioni eseguite da archiviazione Analitica sono fatturabili:

- Richieste di creare BLOB per la registrazione. 

- Richieste di creare entità della tabella per le metriche.

Se è stato configurato un criterio di conservazione dei dati, non addebitate per le transazioni Elimina quando lo spazio di archiviazione Analitica Elimina vecchi dati di registrazione e la metrica. Tuttavia, le transazioni Elimina da un client sono fatturabile. Per ulteriori informazioni sui criteri di conservazione, vedere [impostare un criterio di conservazione dei dati Analitica lo spazio di archiviazione](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Informazioni sulle richieste fatturabili

Tutte le richieste inviate al servizio di archiviazione di un account sono fatturabile o non fatturabile. Spazio di archiviazione Analitica registra ogni singola richiesta apportata a un servizio, incluso un messaggio di stato che indica la modalità della richiesta. Allo stesso modo, lo spazio di archiviazione Analitica archivia metrica per un servizio e le operazioni dell'API del servizio, incluse le percentuali e contare determinati messaggi di stato. Insieme, queste funzionalità consente di analizzare le richieste di fatturabili, apportare miglioramenti dell'applicazione e diagnosticare i problemi con le richieste di servizi. Per ulteriori informazioni sulla fatturazione, vedere [Informazioni sui Azure lo spazio di archiviazione di fatturazione - della larghezza di banda, le transazioni e capacità](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Quando si visualizza dati Analitica lo spazio di archiviazione, è possibile utilizzare le tabelle nella sezione [operazioni registrate Analitica di spazio di archiviazione e messaggi di stato](https://msdn.microsoft.com/library/azure/hh343260.aspx) per determinare quali richieste vengono fatturabile. È quindi possibile confrontare i log e dati di metrica per i messaggi di stato per verificare se sono stato addebitato per una determinata richiesta. È anche possibile utilizzare le tabelle nell'argomento precedente per analizzare la disponibilità di un servizio di archiviazione o singoli operazione API.

## <a name="next-steps"></a>Passaggi successivi

### <a name="setting-up-storage-analytics"></a>La configurazione archiviazione Analitica
- [Monitor di un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)
- [Abilitare e configurare Analitica lo spazio di archiviazione](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Registrazione Analitica lo spazio di archiviazione  
- [Informazioni sulla registrazione Analitica lo spazio di archiviazione](https://msdn.microsoft.com/library/hh343262.aspx)
- [Formato di memorizzazione Analitica Log](https://msdn.microsoft.com/library/hh343259.aspx)
- [Spazio di archiviazione Analitica registrati operazioni e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Metriche Analitica lo spazio di archiviazione
- [Su metriche Analitica lo spazio di archiviazione](https://msdn.microsoft.com/library/hh343258.aspx)
- [Schema di archiviazione Analitica metriche tabella](https://msdn.microsoft.com/library/hh343264.aspx)
- [Spazio di archiviazione Analitica registrati operazioni e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx)  
