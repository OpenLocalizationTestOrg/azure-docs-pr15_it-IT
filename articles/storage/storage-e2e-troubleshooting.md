<properties
    pageTitle="To-End risoluzione dei problemi con Azure metriche di archiviazione e la registrazione, AzCopy e Analyzer messaggio | Microsoft Azure"
    description="Un'esercitazione-to-end con Azure archiviazione Analitica, AzCopy e Microsoft messaggio Analyzer risoluzione dei problemi"
    services="storage"
    documentationCenter="dotnet"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Risoluzione dei problemi-to-End tramite Azure metriche di archiviazione e la registrazione, AzCopy e Analyzer messaggio

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Panoramica

La diagnosi e risoluzione dei problemi è una competenza chiave per la creazione e supporta le applicazioni client con lo spazio di archiviazione di Microsoft Azure. A causa di un'applicazione di Azure, distribuita natura, la diagnosi e risoluzione dei problemi di prestazioni potrebbe essere più complesse in ambienti tradizionali.

In questa esercitazione verrà illustrato identificare client alcuni errori che possono influire sulle prestazioni e risolvere gli errori da-to-end tramite strumenti forniti da Microsoft e archiviazione di Azure, per ottimizzare l'applicazione client.

In questa esercitazione offre un'esplorazione pratica di uno scenario di risoluzione dei problemi-to-end. Per una Guida dettagliata concettuale di risoluzione dei problemi di applicazioni Azure dello spazio di archiviazione, vedere [Monitor, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Strumenti per la risoluzione dei problemi di applicazioni di archiviazione Azure

Per risolvere i problemi di applicazioni client utilizzando lo spazio di archiviazione di Microsoft Azure, è possibile utilizzare una combinazione di strumenti per stabilire quando si è verificato un problema e cosa potrebbe essere la causa del problema. Questi strumenti includono:

- **Spazio di archiviazione azure Analitica**. [Azure archiviazione Analitica](http://msdn.microsoft.com/library/azure/hh343270.aspx) fornisce metrica e la registrazione per lo spazio di archiviazione di Azure.
    - **Metriche di archiviazione** tiene traccia delle transazioni metriche e metriche capacità per l'account di archiviazione. Utilizzando la metrica, è possibile determinare le prestazioni dell'applicazione in base a una serie di misure diversi. Per ulteriori informazioni sui tipi di metriche verrà registrate Analitica lo spazio di archiviazione, vedere [Schema tabella metriche di archiviazione Analitica](http://msdn.microsoft.com/library/azure/hh343264.aspx) .

    - **Spazio di archiviazione registrazione** registra ogni richiesta per i servizi di archiviazione di Azure a un file di log lato server. Il log tiene traccia dati dettagliati per ogni richiesta, inclusi l'operazione eseguita, lo stato dell'operazione e informazioni di latenza. Per ulteriori informazioni sui dati di richiesta e risposta in cui scrivere i registri di base dello spazio di archiviazione Analitica, vedere [Lo spazio di archiviazione Analitica Log formato](http://msdn.microsoft.com/library/azure/hh343259.aspx) .

> [AZURE.NOTE] Gli account di archiviazione a un tipo di replica di spazio di archiviazione zona ridondanti (ZRS) non si hanno la metrica o la funzionalità di registrazione abilitata al momento. 

- **Portale di azure**. È possibile configurare la registrazione e metriche per il proprio account di archiviazione nel [Portale di Azure](https://portal.azure.com). È anche possibile visualizzare i grafici che mostrano le prestazioni dell'applicazione nel tempo e configurare avvisi per comunicare se l'applicazione esegue in modo diverso dal previsto per una metrica specificata.

    Per informazioni sulla configurazione di monitoraggio nel portale di Azure, vedere [Monitor di un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md) .

- **AzCopy**. Registri del server per l'archiviazione di Azure vengono memorizzati come BLOB, in modo che è possibile utilizzare AzCopy per copiare una directory locale per analisi tramite Microsoft messaggio Analyzer BLOB log. Per ulteriori informazioni sulla AzCopy, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) .

- **Messaggio Microsoft Analyzer**. Messaggio Analyzer è uno strumento che fruisce dei file di log e visualizza i dati di log in un formato visivo semplifica l'individuazione di filtro, ricerca e raggruppare i dati di log in set utile che consente di analizzare gli errori e problemi di prestazioni. Vedere [Guida di operativo Microsoft messaggio Analyzer](http://technet.microsoft.com/library/jj649776.aspx) per ulteriori informazioni sull'analisi di messaggio.

## <a name="about-the-sample-scenario"></a>Sullo scenario di esempio

Per questa esercitazione verrà esaminato uno scenario in cui metriche di archiviazione di Azure indica un tasso di successo percentuale bassa per un'applicazione che chiama lo spazio di archiviazione Azure. L'unità di misura metriche tasso di successo percentuale bassa (come illustrato come **PercentSuccess** nel [Portale di Azure](https://portal.azure.com) e nelle tabelle metriche) tiene traccia delle operazioni eseguite con esito positivo, ma che restituiscono un codice di stato HTTP è maggiore di 299. Nei file di log di archiviazione sul lato server queste operazioni sono registrate con lo stato transazione **ClientOtherErrors**. Per ulteriori informazioni sulle unità di misura metriche successo percentuale bassa, vedere [metriche mostrano PercentSuccess bassa o voci di log analitica avere operazioni con lo stato di ClientOtherErrors transazione](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operazioni di archiviazione Azure potrebbero restituire codici di stato HTTP superiore a 299 come parte delle funzionalità normale. Ma questi errori in alcuni casi indicano che è possibile ottimizzare l'applicazione client per migliorare le prestazioni.

In questo scenario, si vedrà un tasso di successo percentuale bassa a qualsiasi elemento inferiore al 100%. È possibile scegliere un livello di metriche diverso, tuttavia, in base alle proprie esigenze. È consigliabile durante la verifica dell'applicazione, creare una tolleranza prevista per le metriche di prestazioni chiave. Ad esempio, è possibile stabilire, in base ai test, l'applicazione deve disporre di un tasso di successo percentuale coerente di 90% o 85%. Se i dati metriche indicano che l'applicazione è la deviazione da tale numero, quindi è possibile esaminare che possono causare l'aumento.

Per questo scenario di esempio, una volta è stata stabilita che unità di misura metriche tasso percentuale di completamento è inferiore al 100%, esamineremo i log per trovare gli errori che correlare le metriche e usarli per stabilire la causa la percentuale di successo percentuale inferiore. Vengono illustrate in modo specifico alla errori nell'intervallo 400. È quindi in modo approfondito maggiormente 404 errori (non disponibile).

### <a name="some-causes-of-400-range-errors"></a>Alcune cause degli errori 400 intervallo

Gli esempi riportati di seguito illustra un esempio di alcuni degli errori di intervallo 400 richieste rispetto a archiviazione Blob Azure e le possibili cause. Uno di questi errori, come gli errori in intervallo 300 e l'intervallo 500 può contribuire a un tasso di successo percentuale bassa.

Si noti che non sono elencati di seguito completata. Visualizzare [lo stato e codici di errore](http://msdn.microsoft.com/library/azure/dd179382.aspx) su MSDN per ulteriori informazioni sugli errori di archiviazione Azure generali e informazioni sugli errori specifici per ognuno dei servizi di archiviazione.

**Esempi di stato codice 404 (non disponibile)**

Si verifica quando un'operazione di lettura in base a un contenitore o blob non riesce perché non viene trovato il blob o il contenitore.

- Si verifica se un contenitore o blob è stato eliminato da un altro client prima della richiesta.
- Si verifica se si utilizza una chiamata API che consente di creare il contenitore o blob dopo aver verificato se esiste. APIs CreateIfNotExists effettuare una chiamata di testa per verificare l'esistenza del contenitore o blob; Se non esiste, viene restituito un errore 404 e quindi una seconda chiamata di inserire viene eseguita per scrivere il contenitore o blob.

**Codice di stato 409 esempi (conflitto)**

- Si verifica se si usa un API crea per creare un nuovo contenitore o blob, senza prima verificare la presenza e un contenitore o blob con lo stesso nome già esistente.
- Si verifica se un contenitore viene eliminato e si tenta di creare un contenitore di nuovo con lo stesso nome prima del completamento dell'operazione di eliminazione.
- Si verifica se si specifica un leasing in un contenitore o blob ed esiste già un leasing presenta.

**Codice di stato 412 (preliminare non riuscita) esempi**

- Si verifica quando non è stata soddisfatta la condizione specificata da un'intestazione condizionale.
- Si verifica quando l'ID di leasing specificato corrisponde all'ID leasing sul contenitore o blob.

## <a name="generate-log-files-for-analysis"></a>Generare file di log di analisi

In questa esercitazione si userà messaggio Analyzer per l'uso con tre diversi tipi di file di log, anche se è possibile scegliere di lavorare con uno di questi piani:

- **Registro server**, che viene creata quando si attiva la registrazione di archiviazione Azure. Il registro server contiene dati relativi a ogni operazione denominata rispetto a uno dei servizi di archiviazione Azure - blob, code, tabella e file. Registro del server indica il tipo di operazione è stato chiamato e il codice di stato è stati restituiti, nonché gli altri dettagli sulla richiesta e risposta.
- **Registro client .NET**, che viene creata quando si attiva la registrazione lato client all'interno dell'applicazione .NET. Il registro client include informazioni dettagliate su come il client prepara la richiesta e riceve ed elabora la risposta.
- Il **Registro di traccia di rete HTTP**, che consente di raccogliere dati sui dati di richiesta e risposta HTTP/HTTPS, inclusi per operazioni in archiviazione Azure. In questa esercitazione, si verrà generare la traccia di rete tramite analizzatore di messaggio.

### <a name="configure-server-side-logging-and-metrics"></a>Configurare la registrazione lato server e metriche

Prima di tutto, è necessario configurare la registrazione di archiviazione Azure e metriche, in modo che è stata creata dall'applicazione client per analizzare i dati. È possibile configurare la registrazione e la metrica in vari modi, tramite il [Portale di Azure](https://portal.azure.com)tramite PowerShell, o a livello di programmazione. Per informazioni dettagliate sulla configurazione di registrazione e metriche, vedere [attivazione metriche di archiviazione e la visualizzazione di dati di metrica](http://msdn.microsoft.com/library/azure/dn782843.aspx) e [Abilitazione della registrazione di spazio di archiviazione e accesso ai dati di Log](http://msdn.microsoft.com/library/azure/dn782840.aspx) su MSDN.

**Tramite il portale di Azure**

Per configurare la registrazione e la metrica per l'account di archiviazione tramite il [Portale di Azure](https://portal.azure.com), seguire le istruzioni disponibili in [Monitor di un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] Non è possibile impostare minute metriche tramite il portale di Azure. Tuttavia, è consigliabile impostare li ai fini di questa esercitazione e per l'analisi dei problemi di prestazioni con l'applicazione. È possibile impostare minute metriche tramite PowerShell come illustrato di seguito o a livello di programmazione tramite la libreria di client lo spazio di archiviazione.
>
> Si noti che il portale di Azure non è possibile visualizzare metriche minute, solo orarie metriche.

**Tramite PowerShell**

Per iniziare a usare PowerShell per Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

1. Utilizzare il cmdlet [AzureAccount Aggiungi](http://msdn.microsoft.com/library/azure/dn722528.aspx) per aggiungere l'account utente Azure nella finestra di PowerShell:

    ```
    Add-AzureAccount
    ```

2. Nella finestra di **accedere a Microsoft Azure** , digitare l'indirizzo di posta elettronica e la password associati all'account. Azure autentica e consente di salvare le informazioni sulle credenziali e quindi si chiude la finestra.
3. Impostare l'account di archiviazione predefinito per l'account di archiviazione in uso per l'esercitazione eseguendo questi comandi nella finestra di PowerShell:

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Attivare la registrazione di spazio di archiviazione per il servizio Blob:

    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```
5. Abilitare metriche di archiviazione per il servizio Blob, assicurandosi di impostare **- MetricsType** `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configurare la registrazione lato client .NET

Per configurare la registrazione lato client per un'applicazione .NET, attivare la diagnostica .NET nel file di configurazione dell'applicazione (config o App). Per informazioni dettagliate, vedere [la registrazione lato Client con la raccolta di Client .NET lo spazio di archiviazione](http://msdn.microsoft.com/library/azure/dn782839.aspx) e [la registrazione con Microsoft Azure archiviazione SDK per Java sul lato Client](http://msdn.microsoft.com/library/azure/dn782844.aspx) su MSDN.

Log lato client include informazioni dettagliate su come il client prepara la richiesta e riceve ed elabora la risposta.

La raccolta di Client archiviazione archivia i log lato client dati nella posizione specificata nel file di configurazione dell'applicazione (config o App).

### <a name="collect-a-network-trace"></a>Raccogliere una traccia di rete

È possibile utilizzare messaggio Analyzer per raccogliere una traccia di rete HTTP/HTTPS durante l'esecuzione di un'applicazione client. Messaggio Analyzer utilizza [Fiddler](http://www.telerik.com/fiddler) back-end. Prima di selezionare i la traccia di rete, è consigliabile configurare Fiddler per registrare il traffico HTTPS non crittografato:

1. Installare [Fiddler](http://www.telerik.com/download/fiddler).
2. Avviare Fiddler.
2. Selezionare Strumenti **| Opzioni Fiddler**.
3. Nella finestra di dialogo Opzioni, assicurarsi che **Connette HTTPS acquisire** e **Decrittografare il traffico HTTPS** siano entrambe selezionate, come illustrato di seguito.

![Configurare le opzioni di Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Per l'esercitazione, prima di salvare una traccia di rete in messaggio Analyzer raccolta, quindi creare una sessione di analisi per analizzare la traccia e i log. Per raccogliere una traccia di rete nell'analizzatore messaggio:

1. Nella finestra di messaggio Analyzer, selezionare **File | Analisi rapida | Non crittografato HTTPS**.
2. La traccia, verrà avviata immediatamente. Selezionare **Interrompi** per interrompere la traccia in modo che è possibile configurare per il traffico solo di traccia lo spazio di archiviazione.
3. Scegliere **Modifica** per modificare la sessione di registrazione.
4. Selezionare il collegamento **Configura** a destra del provider ETW **WebProxy di Pef Microsoft** .
5. Nella finestra di dialogo **Impostazioni avanzate** , fare clic sulla scheda **Provider** .
6. Nella casella **Filtro Hostname** , specificare l'endpoint di spazio di archiviazione, separati da spazi. Ad esempio, è possibile specificare l'endpoint come segue: modificare `storagesample` il nome del proprio account di archiviazione:

    ```
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Chiudere la finestra di dialogo e fare clic su **Riavvia** per iniziare la raccolta la traccia con il filtro hostname sul posto, in modo che solo il traffico di rete di archiviazione Azure è incluso nella traccia.

>[AZURE.NOTE] Dopo aver completato la raccolta traccia di rete, si consiglia di ripristinare le impostazioni modificate in Fiddler per il traffico HTTPS decrittografare. Nella finestra di dialogo Opzioni Fiddler, deselezionare le caselle di controllo **Acquisire connette HTTPS** e **Decrittografare il traffico HTTPS** .

Vedere [utilizzo delle funzionalità di analisi di rete](http://technet.microsoft.com/library/jj674819.aspx) su Technet per informazioni dettagliate.

## <a name="review-metrics-data-in-the-azure-portal"></a>Verificare le metriche dati nel portale di Azure

Dopo l'applicazione è stata eseguita per un periodo di tempo, è possibile esaminare i grafici metriche visualizzati nel [Portale di Azure](https://portal.azure.com) per osservare come l'esecuzione del servizio. Prima di tutto, passare al proprio account di archiviazione nel portale di Azure e aggiungere un grafico per la **Percentuale di successo** metrica.

Nel portale di Azure, si noterà ora **Percentuale di successo** del grafico monitoraggio, insieme a eventuali altre metriche siano state aggiunte. Nel lo scenario è in modo approfondito avanti l'analisi dei registri del messaggio di analisi, la percentuale di successo percentuale è inferiore al 100%.

Per ulteriori informazioni sull'aggiunta di metriche alla pagina monitoraggio, vedere [come: aggiungere metriche alla tabella metriche](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Può richiedere del tempo per le metriche di visualizzare i dati nel portale di Azure dopo l'abilitazione metriche di archiviazione. In questo modo orarie metriche per dell'ora precedente non vengono visualizzate nel portale di Azure fino a quando non è trascorso l'ora corrente. Inoltre, metriche minute non attualmente visualizzate nel portale di Azure. Pertanto a seconda del quando si attiva la metrica, potrebbe richiedere fino a due ore per visualizzare dati di metrica.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilizzare AzCopy per copiare i registri di server in una directory locale

Spazio di archiviazione Azure scrive server log dati BLOB, mentre metriche siano scritti in tabelle. Registro BLOB sono disponibili in conosciuti `$logs` contenitore per il proprio account di archiviazione. Registro BLOB sono denominate in una struttura gerarchica per anno, mese, giorno e ora, in modo che è possibile individuare facilmente l'intervallo di tempo che si desidera esaminare. Ad esempio, nel `storagesample` account, il contenitore per BLOB log per 01/02/2015, dalle 8 9, `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. I singoli BLOB in questo contenitore sono denominati in sequenza, a partire da `000000.log`.

È possibile utilizzare lo strumento della riga di comando AzCopy per scaricare questi file di log lato server in una posizione di propria scelta sul computer locale. Ad esempio, è possibile utilizzare il comando seguente per scaricare i file di log per le operazioni di blob avvenute il 2 gennaio 2015 nella cartella `C:\Temp\Logs\Server`; Sostituire `<storageaccountname>` con il nome del proprio account di archiviazione, e `<storageaccountkey>` con il tasto di scelta account:

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy è disponibile per il download della pagina di [Download di Azure](https://azure.microsoft.com/downloads/) . Per informazioni dettagliate sull'uso di AzCopy, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

Per ulteriori informazioni sui registri lato server download, vedere [scaricare registrazione lo spazio di archiviazione dati di log](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilizzare Microsoft messaggio Analyzer per analizzare i dati dei registri

Microsoft messaggio Analyzer è uno strumento per l'acquisizione, la visualizzazione e analisi protocollo messaggistica il traffico, eventi e altri messaggi di sistema o dell'applicazione in scenari di diagnostici e risoluzione dei problemi. Messaggio Analyzer inoltre consente di caricare, aggregare e analizzare i dati dal log e salvati i file di analisi. Per ulteriori informazioni su messaggi Analyzer, vedere [Microsoft messaggio Analyzer operativo Guida](http://technet.microsoft.com/library/jj649776.aspx).

Messaggio Analyzer include risorse per l'archiviazione di Azure che consentono di analizzare i registri di rete, client e server. In questa sezione verrà trattato come usare gli strumenti per risolvere il problema di successo percentuale bassa nei registri di spazio di archiviazione.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Scaricare e installare Analyzer messaggio e le risorse di archiviazione di Azure

1. Scaricare [Analyzer messaggio](http://www.microsoft.com/download/details.aspx?id=44226) da Microsoft Download Center ed eseguire il programma di installazione.
2. Avviare l'analizzatore di messaggio.
3. Dal menu **Strumenti** selezionare **Asset Manager**. Nella finestra di dialogo **Asset Manager** selezionare **download**e quindi applicare il filtro **Archiviazione Azure**. Verranno visualizzati i beni di spazio di archiviazione di Azure, come illustrato nella figura seguente.
4. Fare clic su **Sincronizza tutti visualizzati gli elementi** per installare i beni di spazio di archiviazione Azure. Le risorse disponibili includono:
    - **Archiviazione azure colori regole:** Regole di colore dello spazio di archiviazione Azure consentono di definire filtri speciali che usano colori, il testo e gli stili dei caratteri per evidenziare i messaggi che contengono informazioni specifiche in una traccia.
    - **Archiviazione azure grafici:** Azure lo spazio di archiviazione sono grafici predefiniti che server log dati del grafico. Si noti che per utilizzare grafici di archiviazione Azure al momento, si può caricare solo registro del server nella griglia di analisi.
    - **Archiviazione azure parser:** Parser di archiviazione Azure analizzare lo spazio di archiviazione di Azure client, server e HTTP registri per visualizzarli nella griglia di analisi.
    - **Filtri archiviazione azure:** Filtri di spazio di archiviazione Azure sono criteri predefiniti che è possibile utilizzare per i dati nella griglia di analisi della query.
    - **Archiviazione azure visualizzazione layout:** Layout di visualizzazione dello spazio di archiviazione Azure sono raggruppamenti nella griglia di analisi e layout di colonne predefinite.
4. Riavviare Analyzer messaggio dopo aver installato le risorse.

![Messaggio Analyzer Asset Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] Installare tutti i beni di archiviazione Azure a scopo di questa esercitazione.

### <a name="import-your-log-files-into-message-analyzer"></a>Importare i file di log Analyzer messaggio

È possibile importare tutti i file salvati log (lato server, sul lato client e rete) in una singola sessione in Microsoft messaggio Analyzer per l'analisi.

1. Menu **File** di Microsoft messaggio Analyzer, fare clic su **Nuova sessione**e quindi fare clic su **Vuota sessione**. Nella finestra di dialogo **Nuova sessione** , immettere un nome per la sessione di analisi. Nel riquadro **Dei dettagli di sessione** , fare clic sul pulsante **file** .
1. Per caricare i dati di traccia di rete generati da Analyzer messaggio, fare clic su **Aggiungi file**, passare al percorso in cui è stato salvato il file .matp dalla sessione di analisi web, selezionare il file .matp e fare clic su **Apri**.
1. Per caricare i dati dei registri lato server, fare clic su **Aggiungi file**, individuare il percorso in cui è stato scaricato i registri lato server, selezionare i file di log per l'intervallo di tempo che si desidera analizzare e fare clic su **Apri**. Quindi, nel riquadro **Dei dettagli di sessione** , impostare la **Configurazione del Registro di testo** elenco a discesa per ogni file di log lato server **AzureStorageLog** per assicurarsi che Microsoft messaggio Analyzer analizzabile correttamente il file di log.
1. Per caricare i dati dei registri lato client, fare clic su **Aggiungi file**, passare al percorso in cui è stato salvato il log lato client, selezionare i file di log che si desidera analizzare e fare clic su **Apri**. Quindi, nel riquadro **Dei dettagli di sessione** , impostare la **Configurazione del Registro di testo** elenco a discesa per ogni file di log lato client **AzureStorageClientDotNetV4** per assicurarsi che Microsoft messaggio Analyzer consente di analizzare correttamente il file di log.
1. Fare clic su **Start** nella finestra di dialogo **Nuova sessione** per caricare e analizzare i dati del registro. Consente di visualizzare i dati del registro nella griglia di analisi analizzatore di messaggio.

Nell'immagine seguente mostra una sessione di esempio configurata con server, client e file di log di traccia di rete.

![Configurare la sessione di messaggistica Analyzer](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Si noti che messaggio Analyzer Carica file di log in memoria. Se si dispone di un set di grandi dimensioni dei dati del registro, desiderata filtrare per ottenere le massime prestazioni da analizzatore di messaggio.

Prima di tutto determinare l'intervallo di tempo che si è interessati a esaminare e mantenere al minimo questo periodo di tempo. In molti casi possibile esaminare un periodo di minuti o ore al massimo. Importare il set più piccolo dei registri in grado di soddisfare le proprie esigenze.

Se si riscontrano ancora una grande quantità di dati del registro, si desidera specificare una sessione di filtro per filtrare i dati di registro prima di caricarlo. Nella casella **Filtro di sessione** , selezionare il pulsante di **una raccolta** per scegliere un filtro predefinito. ad esempio, scegliere **filtro I globale ora** i filtri di archiviazione Azure a filtro in un intervallo di tempo. È quindi possibile modificare i criteri di filtro per specificare le iniziali e finali timestamp per l'intervallo che si desidera visualizzare. È anche possibile filtrare un codice di stato specifico. ad esempio, è possibile caricare solo i movimenti di log in cui il codice di stato è 404.

Per ulteriori informazioni sull'importazione di dati del registro in Microsoft messaggio Analyzer, vedere [Il recupero dei dati dei messaggi](http://technet.microsoft.com/library/dn772437.aspx) in TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilizzare l'ID di richiesta di client per correlare i dati di file di log

La raccolta di Client lo spazio di archiviazione di Azure genera automaticamente un ID di richiesta di client univoco per ogni richiesta. Questo valore viene registrato il log di client, il registro del server e la traccia di rete, in modo che è possibile utilizzare per correlare i dati tra tutti e tre i registri Analyzer messaggio. Consultare l' [ID di richiesta di Client](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) per ulteriori informazioni sulla richiesta del client ID.

Nelle sezioni seguenti viene descritto come utilizzare le visualizzazioni layout predefiniti e personalizzati per correlare e raggruppare i dati in base all'ID di richiesta client.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selezionare un layout di visualizzazione da visualizzare nella griglia di analisi

Le risorse di archiviazione per Analizzatore di messaggio includono i layout di visualizzazione dello spazio di archiviazione di Azure, che sono viste preconfigurate che è possibile utilizzare per visualizzare i dati con raggruppamenti utili e le colonne per diversi scenari. È anche possibile creare layout di visualizzazione personalizzata e salvarli per il riutilizzo.

Nell'immagine seguente Mostra menu **Visualizzazione Layout** , disponibile scegliendo **Visualizzazione Layout** dalla barra multifunzione sulla barra degli strumenti. I layout di visualizzazione per l'archiviazione di Azure sono raggruppati sotto il nodo **Dello spazio di archiviazione di Azure** nel menu. È possibile cercare `Azure Storage` nella casella di ricerca per filtrare lo spazio di archiviazione di Azure visualizzare solo i layout. È inoltre possibile selezionare la stella accanto a un layout di visualizzazione per renderla preferito da visualizzare nella parte superiore del menu.

![Dal menu Layout di visualizzazione](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Per iniziare, selezionare **raggruppato ClientRequestID e modulo**. Questo Visualizza gruppi di layout registra innanzitutto i dati da tutti e tre i registri per ID client richiesta, quindi per il file di log di origine (o **modulo** nel messaggio Analyzer). Con questa visualizzazione è possibile drill-down ID di client specifico e visualizzare dati da tutti i file di log tre per tale richiesta del client ID.

L'immagine riportata di seguito mostra la visualizzazione layout applicata ai dati del Registro di esempio, un sottoinsieme di colonne visualizzate. È possibile visualizzare per un ID richiesta client specifico, la griglia analisi Visualizza i dati di traccia di rete, registro client e Registro server.

![Layout di visualizzazione di archiviazione Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] File di log hanno colonne diverse, in modo che i dati da più file di log verrà visualizzata nella griglia di analisi alcune colonne non possono contenere i dati per una determinata riga. Ad esempio, nella figura riportata sopra, le righe di registro client non presentino qualsiasi tipo di dati **Timestamp**, **TimeElapsed**, **origine**e colonne di **destinazione** , perché queste colonne non sono presenti nel registro client, ma presenti nella traccia di rete. Analogamente, la colonna **Timestamp** vengono visualizzati i dati timestamp registro del server, ma non vengono visualizzati dati per le colonne **TimeElapsed**, di **origine**e di **destinazione** , che non fanno parte del Registro di server.

Oltre a utilizzare i layout di visualizzazione dello spazio di archiviazione di Azure, è possibile definire e salvare i layout di visualizzazione. È possibile selezionare gli altri campi desiderati per il raggruppamento di dati e salvare il raggruppamento come parte del anche il layout personalizzato.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Applicare le regole di colore alla griglia di analisi

Le risorse di archiviazione includono anche le regole di colore, che offrono un elemento visivo indica che identificare i diversi tipi di errori nella griglia di analisi. Il colore predefinito di regole per gli errori HTTP, in modo che vengono visualizzati solo per la traccia di log e di rete server.

Per applicare le regole di colore, selezionare **Le regole di colore** dalla barra multifunzione sulla barra degli strumenti. Si noterà le regole di colore dello spazio di archiviazione di Azure nel menu. Per l'esercitazione, selezionare **Gli errori di Client (StatusCode tra 400 e 499)**, come illustrato nella figura seguente.

![Layout di visualizzazione di archiviazione Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Oltre a utilizzare le regole di colore dello spazio di archiviazione di Azure, è possibile definire e salvare le regole di colore.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Raggruppamento e filtro registrare i dati per trovare gli errori di intervallo 400

Successivamente, si verrà raggruppare e filtrare i dati di log per trovare tutti gli errori nell'intervallo 400.

1. Individuare la colonna **StatusCode** nella griglia di analisi, pulsante destro del mouse sull'intestazione di colonna e selezionare **gruppo**.
2. Gruppo successivo, la colonna **ClientRequestId** . Si noterà che i dati nella griglia di analisi organizzati dal codice di stato e dal ID client richiesta.
1. Visualizzare la finestra di filtro della visualizzazione degli strumenti se non è già visualizzata. Sulla barra multifunzione sulla barra degli strumenti selezionare **Finestre**, quindi **Filtro della visualizzazione**.
2. Per filtrare i dati dei registri per visualizzare solo gli errori di intervallo 400, aggiungere i criteri di filtro seguenti nella finestra di **Filtro della visualizzazione** , quindi fare clic su **Applica**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

Nell'immagine seguente mostra i risultati di questo raggruppamento e filtro. Espandere il campo **ClientRequestID** sotto il raggruppamento per il codice di stato 409, ad esempio, Mostra un'operazione che hanno generato il codice di stato.

![Layout di visualizzazione di archiviazione Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Dopo avere applicato il filtro, si noterà che sono escluse le righe di registro di client, come il client log non include una colonna **StatusCode** . Per iniziare, vengono rivisti nel server e registri di traccia di rete di individuare gli 404 errori e quindi viene restituito nel log di client per esaminare le operazioni del client che hanno il li.

>[AZURE.NOTE] È possibile filtrare una colonna **StatusCode** e continuerà a visualizzare dati da tutti i tre registri, incluso il Registro di client, se si aggiunge un'espressione di filtro che include le voci di log in cui il codice di stato è null. Per creare l'espressione di filtro, usare:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Il filtro restituisce tutte le righe dal client di log e solo le righe il log di server e registro HTTP nel punto in cui il codice di stato è maggiore di 400. Se si applica al layout di visualizzazione raggruppato modulo e l'ID di richiesta di client, è possibile cercare o scorrere tra le voci di log per trovare quelle in cui sono rappresentati tutti e tre i registri.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrare i dati dei registri per individuare gli 404 errori

Le risorse di archiviazione includono filtri predefiniti che è possibile utilizzare per limitare i dati dei registri per trovare gli errori o le tendenze che si sta cercando. È quindi verranno applicati due filtri predefiniti: uno che filtra il server registri traccia di rete per 404 errori e uno che filtra i dati in un intervallo di tempo specificato.

1. Visualizzare la finestra di filtro della visualizzazione degli strumenti se non è già visualizzata. Sulla barra multifunzione sulla barra degli strumenti selezionare **Finestre**, quindi **Filtro della visualizzazione**.
2. Nella finestra di filtro della visualizzazione, selezionare la **raccolta**e cercare `Azure Storage` per trovare lo spazio di archiviazione Azure i filtri. Selezionare il filtro per i **404 messaggi (non trovato) in tutti i log**.
3. Visualizzare il menu di **una raccolta** di nuovo e individuare e selezionare il **Filtro per periodo globale**.
4. Modificare la data e ora illustrato nel filtro per l'intervallo che si desidera visualizzare. In questo modo per limitare l'intervallo di dati da analizzare.
5. Filtro dovrebbe essere simile al seguente esempio. Fare clic su **Applica** per applicare il filtro alla griglia di analisi.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Layout di visualizzazione di archiviazione Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analizzare i dati di log

Raggruppare e filtrare i dati, è possibile esaminare i dettagli delle singole richieste che ha generato 404 errori. In layout di visualizzazione corrente, i dati sono raggruppati per ID di richiesta di client, quindi dall'origine log. Poiché si sta applicazione del filtro a richieste nel punto in cui il campo StatusCode contiene 404, vedremo solo il server e i dati di traccia di rete, non i dati del Registro di client.

Nell'immagine seguente mostra una richiesta specifica in un'operazione di ottenere Blob risultante un 404 poiché il blob non esiste. Si noti che alcune colonne sono state rimosse dalla visualizzazione standard per visualizzare i dati rilevanti.

![Server filtrato e registri di traccia di rete](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Successivamente, si verrà correlare l'ID di richiesta client con i dati del Registro di client per vedere quali azioni su richiede il client quando si è verificato l'errore. È possibile visualizzare una nuova visualizzazione griglia di analisi per questa sessione visualizzare i dati di registro client, viene aperto in una seconda scheda:

1. Prima di tutto, copiare il valore del campo **ClientRequestId** negli Appunti. È possibile eseguire questa operazione, la selezione di una riga, individuare il campo **ClientRequestId** , il pulsante destro del valore dati e scegliere **Copia 'ClientRequestId'**.
1. Sulla barra multifunzione sulla barra degli strumenti selezionare **Nuovo Visualizzatore**, quindi selezionare Blocca sulla **Griglia di analisi** per aprire una nuova scheda. La nuova scheda Mostra tutti i dati nei file di log, senza raggruppare, filtrare o le regole di colore.
2. Sulla barra multifunzione sulla barra degli strumenti selezionare **Il Layout di visualizzazione**, quindi selezionare **Tutte le colonne Client .NET** nella sezione **Archiviazione Azure** . Questa visualizzazione Mostra i dati dal client di log, nonché i registri di traccia server e di rete. Per impostazione predefinita vengono ordinati sulla colonna **MessageNumber** .
3. Successivamente, cercare il log di client l'ID di richiesta client. Sulla barra multifunzione sulla barra degli strumenti selezionare **Trova messaggi**, quindi applicare un filtro personalizzato per l'ID di richiesta di client nel campo di **ricerca** . Utilizzare la sintassi seguente per il filtro che specifica il proprio ID di richiesta di client:

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Messaggio Analyzer individua e seleziona la prima voce in cui i criteri di ricerca corrisponde l'ID di richiesta client. Nel log client esistono più voci per ogni ID di richiesta di client, in modo che può essere necessario raggrupparle in base al campo **ClientRequestId** per rendere più semplice per vederle tutte insieme. Nell'immagine seguente mostra tutti i messaggi nel Registro di client per il client specificato ID richiesta.

![Con registro client 404 errori](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Usa i dati visualizzati nei layout di visualizzazione in queste due schede, è possibile analizzare i dati della richiesta per determinare sulle possibili cause dell'errore. È anche possibile esaminare le convocazioni preceduto questa occorrenza per vedere se l'errore 404 può hanno generato un evento precedente. Ad esempio, è possibile esaminare le voci di log client precedente per l'ID di richiesta di client per determinare se il blob sono stato eliminato o se è stato eseguito l'errore per l'applicazione client la chiamata a un API CreateIfNotExists in un contenitore o blob. Nel log client è possibile trovare l'indirizzo del blob nel campo **Descrizione** . in server e registri di traccia di rete, questa informazione verrà visualizzata nel campo **Riepilogo** .

Quando si conosce l'indirizzo del blob che restituito l'errore 404, è possibile esaminare ulteriormente. Se si esegue la ricerca di voci di log per altri messaggi relativi alle operazioni su blob stesso, è possibile verificare se il client precedentemente eliminato l'entità.

## <a name="analyze-other-types-of-storage-errors"></a>Analizzare altri tipi di errori di archiviazione

Ora che si ha familiarità con l'uso di messaggio Analyzer per analizzare i dati di log, è possibile analizzare altri tipi di errori tramite la visualizzazione layout, le regole di colore e la ricerca/filtro. Tabelle riportate di seguito sono elencati alcuni problemi che possono verificarsi e i criteri di filtro è possibile utilizzare per semplificarne l'individuazione. Per ulteriori informazioni sulla creazione di filtri e analizzatore messaggio filtro lingua, vedere [Filtro dei dati di messaggio](http://technet.microsoft.com/library/jj819365.aspx).

|    Per verificare se sono...                                                                                               |    Utilizzare l'espressione di filtro...                                                                                                                                                                                                                                        |    Espressione viene applicata a Log (Client, Server di rete, tutti)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Ritardi imprevisti in recapito dei messaggi in una coda                                                              |    AzureStorageClientDotNetV4.Description contiene "Impossibile ripetendo il" operazione".                                                                                                                                                                                |    Client                                                        |
|    Aumento HTTP PercentThrottlingError                                                                       |    HTTP. Response.StatusCode = = 500 #124; & #124; HTTP. Response.StatusCode = = 503                                                                                                                                                                                          |    Rete                                                       |
|    Aumentare in PercentTimeoutError                                                                               |    HTTP. Response.StatusCode = = 500                                                                                                                                                                                                                             |    Rete                                                       |
|    Aumentare in PercentTimeoutError (tutte)                                                                         |    * StatusCode = = 500                                                                                                                                                                                                                                          |    Tutti                                                           |
|    Aumentare in PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Client                                                        |
|    HTTP 403 (accesso negato) dei messaggi                                                                                 |    HTTP. Response.StatusCode = = 403                                                                                                                                                                                                                             |    Rete                                                       |
|    HTTP 404 (non trovato) dei messaggi                                                                                 |    HTTP. Response.StatusCode = = 404                                                                                                                                                                                                                             |    Rete                                                       |
|    404 (tutte)                                                                                                     |    * StatusCode = = 404                                                                                                                                                                                                                                          |    Tutti                                                           |
|    Condiviso problema di autorizzazione di accesso della firma (SA)                                                             |    AzureStorageLog.RequestStatus = = "SASAuthorizationError"                                                                                                                                                                                                     |    Rete                                                       |
|    HTTP 409 (conflitto) dei messaggi                                                                                  |    HTTP. Response.StatusCode = = 409                                                                                                                                                                                                                             |    Rete                                                       |
|    409 (tutte)                                                                                                     |    * StatusCode = = 409                                                                                                                                                                                                                                          |    Tutti                                                           |
|    Voci di log PercentSuccess bassa o analitica avere operazioni con stato ClientOtherErrors    |    AzureStorageLog.RequestStatus = = "ClientOtherError"                                                                                                                                                                                                         |    Server                                                        |
|    Nagle avviso                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) e (AzureStorageLog.RequestPacketSize < 1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Server                                                        |
|    Intervallo di tempo nei registri Server e di rete                                                                    |    #Timestamp > = 2014-10-20T16:36:38 e #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Server, rete                                               |
|    Intervallo di tempo nei registri Server                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 e AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Server                                                        |


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi scenari-to-end di archiviazione Azure, vedere queste risorse:

- [Monitorare, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
- [Spazio di archiviazione Analitica](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Monitor di un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)
- [Trasferire i dati con l'utilità AzCopy della riga di comando](storage-use-azcopy.md)
- [Messaggio di Microsoft Analyzer operativo Guida](http://technet.microsoft.com/library/jj649776.aspx)
