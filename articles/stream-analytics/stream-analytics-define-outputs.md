<properties
    pageTitle="Flusso di output Analitica: opzioni per l'archiviazione, analisi | Microsoft Azure"
    description="Informazioni sull'utilizzo di opzioni di output dati flusso Analitica inclusi Power BI per ottenere risultati analisi."
    keywords="trasformazione dei dati, i risultati dell'analisi, opzioni di archiviazione dei dati"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
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

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Flusso di output Analitica: opzioni per l'archiviazione, analisi

Durante la creazione di un processo di flusso Analitica, prendere in considerazione la modalità di utilizzo di dati risultanti. Come si visualizzeranno i risultati del processo di flusso Analitica e in cui si archiviano essa?

Per supportare una varietà di modelli di applicazione, Azure flusso Analitica sono diverse opzioni per la memorizzazione di output e la visualizzazione di risultati dell'analisi. Questo semplifica la visualizzazione output processo e offre flessibilità nel consumo e lo spazio di archiviazione di output processo per data warehouse e altri scopi. Qualsiasi output configurato nel processo, occorre prima viene avviato il processo e gli eventi avviare il flusso. Ad esempio, se si Usa archiviazione Blob come un output, il processo non creerà un account di archiviazione automatica. È necessario essere creati dall'utente prima di avviata il processo ASA.

## <a name="azure-data-lake-store"></a>Archivio Lake dati di Azure

Flusso Analitica supporta [Archivio Lake dati di Azure](https://azure.microsoft.com/services/data-lake-store/). Questo spazio di archiviazione consente di archiviare i dati di qualsiasi dimensione, tipo e acquisizione velocità per operativi ed esplorativo analitica. In questa fase, la creazione e la configurazione di output archivio Lake dati è supportata solo nel portale classica di Azure. Inoltre, Analitica flusso deve essere autorizzata per accedere all'archivio Lake dati. Vengono fornite informazioni dettagliate sulle autorizzazioni e su come eseguire l'iscrizione per l'anteprima di archivio dati Lake, se necessario, vedere l' [articolo di output Lake dati](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autorizzare un archivio di Lake dati di Azure

Quando lo spazio di archiviazione di dati Lake è selezionato come un output nel portale di gestione di Azure, verrà richiesto di autorizzare una connessione a un archivio di Lake dati esistente.  

![Autorizzare archivio Lake dati.](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Compilare quindi le proprietà per l'output di archivio di dati Lake come indicato di seguito:

![Autorizzare archivio Lake dati.](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione necessari per la creazione di un archivio Lake dati di output.

<table>
<tbody>
<tr>
<td><B>NOME DELLA PROPRIETÀ</B></td>
<td><B>DESCRIZIONE</B></td>
</tr>
<tr>
<td>Alias di output</td>
<td>Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query all'archivio Lake dati.</td>
</tr>
<tr>
<td>Nome dell'account</td>
<td>Nome dell'account lo spazio di archiviazione di dati Lake nel punto in cui si intende inviare l'output. Verrà visualizzato un elenco degli account archivio Lake dati a cui l'utente ha eseguito l'accesso al portale di ha accesso a discesa.</td>
</tr>
<tr>
<td>Percorso prefisso motivo [<I>facoltativo</I>]</td>
<td>Il percorso del file utilizzato per scrivere i file all'interno di dall'Account archivio Lake dati specificato. <BR>{date}, {time}<BR>Esempio 1: Cartella1/log / {date} / {ora}<BR>Esempio 2: Cartella1/log / {date}</td>
</tr>
<tr>
<td>Formato di data [<I>facoltativo</I>]</td>
<td>Se nel percorso prefisso viene utilizzato il token di data, è possibile selezionare il formato di data in cui sono organizzati i file. Esempio: Gg/MM/aaaa</td>
</tr>
<tr>
<td>Formato ora [<I>facoltativo</I>]</td>
<td>Se nel percorso prefisso viene utilizzato il token di tempo, specificare il formato di ora in cui sono organizzati i file. L'unico valore supportato è al momento HH.</td>
</tr>
<tr>
<td>Formato di serializzazione evento</td>
<td>Formato di serializzazione per i dati di output. JSON, CSV e Avro sono supportate.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Se in formato CSV o JSON, è necessario specificare una codifica. UTF-8 è il formato di codifica supportato solo in questo momento.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Applicabile solo per la serializzazione CSV. Flusso Analitica supporta un numero di delimitatori comuni per serializzare dati CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
<tr>
<td>Formato</td>
<td>Applicabile solo per la serializzazione JSON. Riga di separazione specifica che verrà formattata l'output mediante la ricezione di ogni oggetto JSON separato da una nuova riga. Matrice indica che l'output verrà formattata come una matrice di oggetti JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Rinnovare autorizzazione archivio Lake dati.

È necessario ripetere l'autenticazione account archivio Lake dati se la password del proprio stato modificato dopo il lavoro è stato creato o dell'ultima autenticazione.

![Autorizzare archivio Lake dati.](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>Database SQL

[Database SQL Azure](https://azure.microsoft.com/services/sql-database/) è utilizzabile come un output dei dati relazionali natura o per le applicazioni che dipendono dai contenuti ospitato in un database relazionale. Processi Analitica flusso verranno scritto a una tabella esistente in un Database di SQL Azure.  Si noti che lo schema della tabella deve corrispondere esattamente i campi e i tipi di vengano l'output del processo. Un [Data Warehouse di SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) può anche essere specificato come un output tramite Database SQL output anche l'opzione (è una caratteristica di anteprima). Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di un Database SQL di output.

| Nome della proprietà | Descrizione |
|---------------|-------------|
| Alias di output | Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query al database. |
| Database | Il nome del database in cui si intende inviare l'output |
| Nome del server | Il nome del server di Database SQL |
| Nome utente | Il nome utente che ha accesso a scrivere nel database |
| Password | La password per connettersi al database |
| tavolo | Il nome della tabella in cui verrà scritto l'output. Il nome della tabella è tra maiuscole e minuscole e lo schema di questa tabella deve corrispondere esattamente al numero di campi e i tipi di generati da output il processo. |

> [AZURE.NOTE] L'offerta di Database SQL Azure è attualmente supportata per un output di processo in flusso Analitica. Tuttavia, una macchina virtuale Azure esegue SQL Server con un database associato non è supportata. Questo è soggetta a modifiche nelle versioni future.

## <a name="blob-storage"></a>Archiviazione BLOB

Archiviazione BLOB offre una soluzione conveniente e scalabilità per l'archiviazione di grandi quantità di dati non strutturati nel cloud.  Per informazioni introduttive su archiviazione Blob Azure e il relativo utilizzo, vedere la documentazione come [utilizzare BLOB](../storage/storage-dotnet-how-to-use-blobs.md).

Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di un output blob.

<table>
<tbody>
<tr>
<td>NOME DELLA PROPRIETÀ</td>
<td>DESCRIZIONE</td>
</tr>
<tr>
<td>Alias di output</td>
<td>Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query a questa archiviazione blob.</td>
</tr>
<tr>
<td>Account di archiviazione</td>
<td>Il nome dell'account di archiviazione in cui si intende inviare l'output.</td>
</tr>
<tr>
<td>Chiave Account lo spazio di archiviazione</td>
<td>Segreto associato all'account di archiviazione.</td>
</tr>
<tr>
<td>Contenitore di spazio di archiviazione</td>
<td>I contenitori forniscono un raggruppamento logico per BLOB archiviati nel servizio di Microsoft Azure Blob. Quando si carica un blob al servizio Blob, è necessario specificare un contenitore per tale blob.</td>
</tr>
<tr>
<td>Percorso prefisso motivo [facoltativo]</td>
<td>Il percorso del file utilizzato per scrivere il BLOB all'interno del contenitore specificato.<BR>All'interno del percorso, è possibile scegliere di utilizzare le istanze di uno o più delle seguenti 2 variabili per specificare la frequenza con cui vengono scritte BLOB:<BR>{date}, {time}<BR>Esempio 1: cluster1/log / {date} / {ora}<BR>Esempio 2: cluster1/log / {date}</td>
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
<td>Formato di serializzazione per i dati di output.  JSON, CSV e Avro sono supportate.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Se in formato CSV o JSON, è necessario specificare una codifica. UTF-8 è il formato di codifica supportato solo in questo momento.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Applicabile solo per la serializzazione CSV. Flusso Analitica supporta un numero di delimitatori comuni per serializzare dati CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
<tr>
<td>Formato</td>
<td>Applicabile solo per la serializzazione JSON. Riga di separazione specifica che verrà formattata l'output mediante la ricezione di ogni oggetto JSON separato da una nuova riga. Matrice indica che l'output verrà formattata come una matrice di oggetti JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Hub di evento

[Hub di evento](https://azure.microsoft.com/services/event-hubs/) è una scalabilità pubblicazione sottoscrizione ingestor evento. Consente di raccogliere milioni di eventi al secondo.  Viene un utilizzato Hub un evento come output quando l'output di un processo di flusso Analitica sarà l'immissione di un altro processo flusso.

Esistono alcuni parametri necessari per configurare i flussi di dati di evento Hub come un output.

| Nome della proprietà | Descrizione |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias di output | Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query a Hub questo evento. |
| Servizio Bus Namespace | Uno spazio dei nomi del servizio Bus è un contenitore di un insieme di messaggistica entità. Quando si crea un nuovo evento Hub, è inoltre creato uno spazio dei nomi Bus di servizio |
| Hub di evento | Il nome dell'evento Hub output |
| Nome del criterio Hub evento | I criteri di accesso condiviso, che possono essere creati nella scheda evento Hub configurare. Ogni criterio di accesso condiviso avrà un nome, le autorizzazioni impostate, e tasti di scelta |
| Chiave dei criteri di evento Hub | Il tasto di scelta condiviso utilizzato per eseguire l'autenticazione accesso allo spazio dei nomi Bus di servizio |
| Colonna chiave partizione [facoltativo] | In questa colonna contiene la chiave di partizione per l'output Hub evento. |
| Formato di serializzazione evento | Formato di serializzazione per i dati di output.  JSON, CSV e Avro sono supportate. |
| Codifica | Per CSV e JSON, UTF-8 è l'unico formato di codifica supportato in questa fase |
| Delimitatore | Applicabile solo per la serializzazione CSV. Flusso Analitica supporta un numero di delimitatori comuni per serializzazione dei dati in formato CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Formato | Applicabile solo per tipo JSON. Riga di separazione specifica che verrà formattata l'output mediante la ricezione di ogni oggetto JSON separato da una nuova riga. Matrice indica che l'output verrà formattata come una matrice di oggetti JSON. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) è utilizzabile come un output per un processo di flusso Analitica per fornire per un'esperienza di visualizzazione completa dei risultati di analisi. Questa funzionalità è utilizzabile per unità di misura metriche basata sulle risorse dei report, generazione di report e dashboard operativi.

### <a name="authorize-a-power-bi-account"></a>Autorizzare un account di Power BI

1.  Power BI selezionato come un output nel portale di gestione di Azure, verrà richiesto per autorizzare un utente di Business Intelligence Power esistente o creare un nuovo account di Power BI.  

    ![Autorizzare utente di Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Creare un nuovo account se non è ancora CE l'hai, quindi fare clic su autorizzare ora.  Viene visualizzata una schermata simile al seguente.  

    ![Account Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  In questo passaggio, specificare l'account aziendale o dell'istituto di istruzione per autorizzare l'output di Power BI. Se si non è già iscritti a Power BI, scegliere Accedi a questo punto. L'account aziendale o dell'istituto di istruzione usato per Power BI potrebbe essere diversa dall'account Azure abbonamento che si è connessi attualmente con.

### <a name="configure-the-power-bi-output-properties"></a>Configurare le proprietà di output di Power BI

Dopo avere inserito l'account di Power BI autenticato, è possibile configurare le proprietà per l'output di Power BI. Nella tabella riportata di seguito è l'elenco dei nomi di proprietà e la relativa descrizione per configurare l'output di Power BI.

| Nome della proprietà | Descrizione |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias di output | Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query per l'output di ottenere informazioni. |
| Area di lavoro di gruppo | Per abilitare la condivisione dei dati con altri utenti di Power BI è possibile selezionare i gruppi all'interno di account Power BI o scegliere "Area di lavoro personale" Se non si desidera scrivere a un gruppo.  Aggiornamento di un gruppo esistente, è necessario il rinnovo l'autenticazione di Power BI. | 
| Nome del set di dati | Specificare un nome di set di dati desiderato per l'output di Power BI usare |
| Nome della tabella | Specificare un nome di tabella sotto il set di dati di output di Power BI. Attualmente, l'output di Power BI processi flusso Analitica può avere solo una tabella in un set di dati |

Per una panoramica della configurazione di un output di Power BI e dashboard, vedere l'articolo [Azure flusso Analitica e Power BI](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella verrà compilati automaticamente quando viene avviato il processo e si avvia il processo output pompaggio in Power BI. Si noti che se la query di processo non genera alcun risultato, il set di dati e la tabella non verrà creati. Tenere inoltre presente che se Power BI dispone già di un set di dati e una tabella con lo stesso nome di una condizione in questa posizione lavorativa flusso Analitica, verranno sovrascritti i dati esistenti.

### <a name="renew-power-bi-authorization"></a>Rinnovare l'autorizzazione di Power BI

Sarà necessario eseguire nuovamente l'account di Power BI l'autenticazione se la password del proprio stato modificato dopo il lavoro è stato creato o dell'ultima autenticazione. Se l'autenticazione a più fattori (MFA) è configurato nel tenant di Azure Active Directory (AAD) sarà anche necessario rinnovare l'autorizzazione di Power BI ogni 2 settimane. Un indicatore di questo problema è alcun output di processo e un "autenticazione utente errore" nei log operazione:

  ![Errore di token Aggiorna di Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Per risolvere il problema, interrompere il lavoro in esecuzione e passare all'output di Power BI.  Fare clic sul collegamento "Rinnovare autorizzazione" e riavviare il processo durante l'ultimo arrestato per evitare perdite di dati.

  ![Power BI rinnovare autorizzazione](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Archiviazione tabella

[Archivio tabelle Azure](../storage/storage-introduction.md) offre disponibilità, market scalable lo spazio di archiviazione, in modo da un'applicazione può automaticamente ridimensionare in base alle esigenze degli utenti. Archiviazione tabelle è archivio di chiave/attributo NoSQL Microsoft quale è possibile sfruttare le per i dati strutturati con meno vincoli sullo schema. Archivio tabelle Azure può essere usata per archiviare dati per il recupero efficiente e persistenza.

Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di output una tabella.

| Nome della proprietà | Descrizione |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias di output | Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query in un archivio questa tabella. |
| Account di archiviazione | Il nome dell'account di archiviazione in cui si intende inviare l'output. |
| Chiave Account lo spazio di archiviazione | Tasto di scelta associato all'account di archiviazione. |
| Nome della tabella | Il nome della tabella. La tabella verrà visualizzato creata se non esiste. |
| Chiave di partizione | Il nome della colonna di output contenente la chiave di partizione. Il tasto partizione è un identificatore univoco per partizione all'interno di una tabella che costituisce la prima parte della chiave primaria di un'entità. È un valore string che può essere fino a 1 KB. |
| Chiave riga | Il nome della colonna di output contenente la chiave di riga. Il tasto riga è un identificatore univoco per un'entità all'interno di una determinata partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore string che può essere fino a 1 KB. |
| Dimensioni del batch | Il numero di record per un'operazione batch. In genere il valore predefinito è sufficiente per la maggior parte dei processi, fare riferimento alla [tabella Batch operazione specifiche](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) per altri dettagli su come modificare questa impostazione. |

## <a name="service-bus-queues"></a>Code Bus Service

[Servizio Bus code](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono una prima In recapito dei messaggi FIFO (First Out) a uno o più consumer concorrenti. I messaggi in genere, devono essere ricevuti ed elaborati per i destinatari nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuta ed elaborata da consumer solo un messaggio.

Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di un coda output.

| Nome della proprietà | Descrizione |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias di output | Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query alla coda Bus servizio. |
| Servizio Bus Namespace | Uno spazio dei nomi del servizio Bus è un contenitore di un insieme di messaggistica entità. |
| Nome coda | Il nome della coda Bus servizio. |
| Nome del criterio coda | Quando si crea una coda, è anche possibile creare criteri di accesso condiviso nella scheda coda configurare. Ogni criterio di accesso condiviso verrà dispone di un nome, le autorizzazioni impostate, e tasti di scelta. |
| Chiave dei criteri di coda | Il tasto di scelta condiviso utilizzato per eseguire l'autenticazione accesso allo spazio dei nomi Bus di servizio |
| Formato di serializzazione evento | Formato di serializzazione per i dati di output.  JSON, CSV e Avro sono supportate. |
| Codifica | Per CSV e JSON, UTF-8 è l'unico formato di codifica supportato in questa fase |
| Delimitatore | Applicabile solo per la serializzazione CSV. Flusso Analitica supporta un numero di delimitatori comuni per serializzazione dei dati in formato CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Formato | Applicabile solo per tipo JSON. Riga di separazione specifica che verrà formattata l'output mediante la ricezione di ogni oggetto JSON separato da una nuova riga. Matrice indica che l'output verrà formattata come una matrice di oggetti JSON. |

## <a name="service-bus-topics"></a>Servizio Bus argomenti

Mentre servizio Bus Code fornisce un metodo di comunicazione uno alla volta dal mittente al destinatario, [servizio Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornite una maschera uno-a-molti di comunicazione.

Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di output una tabella.

| Nome della proprietà | Descrizione |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias di output | Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query in questo argomento Bus di servizio. |
| Servizio Bus Namespace | Uno spazio dei nomi del servizio Bus è un contenitore di un insieme di messaggistica entità. Quando si crea un nuovo evento Hub, è inoltre creato uno spazio dei nomi Bus di servizio |
| Nome argomento | Gli argomenti sono messaggistica entità, simile a code e hub di evento. Sono progettati in Raccolta flussi di eventi da un numero di diversi dispositivi e servizi. Quando viene creato un argomento, inoltre viene assegnato un nome specifico. I messaggi inviati a un argomento non sarà disponibili a meno che non viene creata una sottoscrizione, questa operazione assicurarsi che vi sono uno o più sottoscrizioni nell'argomento |
| Nome argomento criteri | Quando si crea un argomento, è anche possibile creare criteri di accesso condiviso nella scheda argomento configurare. Ogni criterio di accesso condiviso avrà un nome, le autorizzazioni impostate, e tasti di scelta |
| Argomento criteri chiave | Il tasto di scelta condiviso utilizzato per eseguire l'autenticazione accesso allo spazio dei nomi Bus di servizio |
| Formato di serializzazione evento | Formato di serializzazione per i dati di output.  JSON, CSV e Avro sono supportate. |
| Codifica | Se in formato CSV o JSON, è necessario specificare una codifica. UTF-8 è l'unico formato di codifica supportato in questa fase |
| Delimitatore | Applicabile solo per la serializzazione CSV. Flusso Analitica supporta un numero di delimitatori comuni per serializzazione dei dati in formato CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale. |

## <a name="documentdb"></a>DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è un completamente gestito NoSQL documento database servizio che offre query e le transazioni su dati senza schema, prestazioni prevedibili e affidabili e sviluppo rapido.

Nella tabella riportata di seguito sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di un output DocumentDB.

<table>
<tbody>
<tr>
<td>NOME DELLA PROPRIETÀ</td>
<td>DESCRIZIONE</td>
</tr>
<tr>
<td>Nome dell'account</td>
<td>Il nome dell'account DocumentDB.  Può essere anche l'endpoint per l'account.</td>
</tr>
<tr>
<td>Chiave account</td>
<td>Tasto di scelta condiviso per l'account DocumentDB.</td>
</tr>
<tr>
<td>Database</td>
<td>Il nome del database DocumentDB.</td>
</tr>
<tr>
<td>Modello di nome insieme</td>
<td>Il modello di nome della raccolta per gli insiemi di file da utilizzare. Il formato del nome raccolta può essere creato tramite token facoltativo {partizione} nel punto in cui le partizioni iniziano da 0.<BR>Ad esempio Seguenti sono input validi:<BR>MyCollection {partizione}<BR>MyCollection<BR>Si noti che devono esistere raccolte prima che il processo di flusso Analitica viene avviato e non verrà creato automaticamente.</td>
</tr>
<tr>
<td>Chiave di partizione</td>
<td>Il nome del campo negli eventi di output utilizzati per specificare la chiave per partizioni output nelle raccolte.</td>
</tr>
<tr>
<td>ID documento</td>
<td>Il nome del campo nell'output eventi utilizzato per specificare la chiave primaria inserire o aggiornare operazioni si basano.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
È stato introdotto di flusso Analitica, un servizio gestito per lo streaming analitica su dati provenienti da Internet di elementi. Per ulteriori informazioni su questo servizio, vedere:

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
