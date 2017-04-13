<properties
    pageTitle="Output JSON per Analitica flusso | Microsoft Azure"
    description="Informazioni su come flusso Analitica possibile assegnare Azure DocumentDB per l'output JSON, per l'archiviazione dei dati e bassa latenza query su dati JSON non strutturati."
    keywords="JSON output"
    documentationCenter=""
    services="stream-analytics,documentdb"
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

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Destinazione Azure DocumentDB per l'output JSON Analitica flusso

Flusso Analitica possibile destinare [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) JSON genera l'output, per consentire query di archiviazione e la latenza bassa dati su dati JSON non strutturati. Informazioni su come implementare al meglio questa integrazione.

Per coloro che si ha familiarità con DocumentDB, esaminiamo [DocumentDB percorso di formazione](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per iniziare.

## <a name="basics-of-documentdb-as-an-output-target"></a>Nozioni fondamentali di DocumentDB come destinazione di output
L'output di Azure DocumentDB in flusso Analitica consente la scrittura del flusso di elaborazione dei risultati come output JSON nel presente DocumentDB. Flusso Analitica non creare raccolte nel database, invece che sia necessario crearli spese. Si tratta in modo da rendere trasparenti all'utente i costi fatturazione tra insiemi, DocumentDB e, in modo che è possibile ottimizzare le prestazioni, la coerenza e la capacità delle raccolte direttamente tramite l' [API DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). È consigliabile utilizzare un Database di DocumentDB per flusso di lavoro in modo logico separare le raccolte per un flusso di lavoro.

Alcune delle opzioni della raccolta DocumentDB sono riportate di seguito.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare la coerenza, disponibilità e latenza

In modo che corrispondano ai propri requisiti di applicazione, DocumentDB consente di regolare con maggiore precisione il Database e le raccolte e apportare compromesso tra la coerenza, disponibilità e latenza. A seconda di quali livelli di coerenza di lettura esigenze dello scenario rispetto a leggere e scrivere latenza, che è possibile scegliere un livello di coerenza nel proprio account di database. Per impostazione predefinita, DocumentDB consente inoltre l'icona del indicizzazione di ogni operazione CRUD alla raccolta. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura in DocumentDB. Per ulteriori informazioni su questo argomento, vedere l'articolo di [modificare i livelli di coerenza database e di ricerca](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Scegliere un livello di prestazioni

È possibile creare raccolte DocumentDB 3 livelli di prestazioni diversi (S1, S2 o S3), che determinano la velocità disponibile per CRUDs a tale raccolta. Inoltre, rallentare le prestazioni per i livelli di indicizzazione/coerenza nella raccolta. Vedere l' [articolo](../documentdb/documentdb-performance-levels.md) per comprendere le prestazioni in modo dettagliato.

## <a name="upserts-from-stream-analytics"></a>Upserts dal flusso Analitica

Integrazione di Analitica flusso con DocumentDB consente di inserire o aggiornare i record nella raccolta DocumentDB in base a una colonna ID documento. Questo è anche un *Upsert*.

Flusso Analitica utilizza un approccio Upsert durate ottimistico, in cui gli aggiornamenti vengono eseguiti solo quando Inserisci ha esito negativo a causa di un conflitto di ID documento. Questo aggiornamento viene eseguito dal flusso Analitica come PATCH, in modo che consente di aggiornamenti parziali al documento, vale a dire aggiunta di nuove proprietà o sostituzione di che una proprietà esistente viene eseguita in modo incrementale. Si noti che le modifiche ai valori di proprietà in forma di matrice nei risultati documento JSON nell'intera matrice Guida sovrascritto, ovvero la matrice non venga unito.

## <a name="data-partitioning-in-documentdb"></a>Dati partizione DocumentDB

Raccolte DocumentDB consentono di dividere i dati in base alle esigenze di prestazioni dell'applicazione e modelli di query. Ogni insieme può contenere fino a 10GB di dati (max) e attualmente non è possibile scalare (o di overflow) una raccolta. Scalabilità, flusso Analitica consente di per scrivere in più raccolte con un determinato prefisso (vedere i dettagli relativi all'uso riportata di seguito). Flusso Analitica utilizza la strategia [Del Risolutore partizione Hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) coerenza in base all'utente specificato PartitionKey colonna per suddividere il record di output. Il numero di raccolte con il prefisso specificato all'avvio del processo trasmissione viene utilizzato come il numero di partizione di output, in cui il processo di scrittura in parallelo (raccolte DocumentDB = Output partizioni). Per un'unica raccolta S3 con Prato eseguire indicizzazione solo agli inserimenti, sulle 0,4 velocità di scrittura MB/s è prevista. Utilizzo di più raccolte in modo da ottenere maggiore produttività e maggiore capacità.

Se si prevede di aumentare il numero di partizione in futuro, potrebbe essere necessario arrestare il processo, nuova partizione i dati dalle raccolte esistenti nelle nuove raccolte e quindi riavviare il processo di flusso Analitica. Ulteriori informazioni sull'uso PartitionResolver e ripartizionamento insieme codice di esempio, verranno inclusi in un post del completamento. L' articolo [partizione DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) offre informazioni dettagliate su questo.

## <a name="documentdb-settings-for-json-output"></a>Impostazioni di DocumentDB per l'output JSON

Creazione di DocumentDB come un output Analitica flusso genera una richiesta di informazioni come indicato di seguito. In questa sezione viene fornita una spiegazione della definizione di proprietà.

![schermata di output analitica flusso documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Alias di output** : un alias per fare riferimento l'output della query ASA  
-   **Nome dell'account** : il nome o l'endpoint URI dell'account DocumentDB.  
-   **Chiave account** : il tasto di scelta condiviso per l'account DocumentDB.  
-   **Database** : DocumentDB il nome del database.  
-   **Modello di raccolta nome** : il modello di nome insieme per gli insiemi di file da utilizzare. Il formato del nome raccolta può essere creato tramite token facoltativo {partizione} nel punto in cui le partizioni iniziano da 0. Di seguito sono input validi di esempio:  
   1\) MyCollection – è necessario che esista una raccolta denominata "MyCollection".  
   2\) MyCollection {partizione}-ad esempio raccolte deve essere presente: "MyCollection0", "MyCollection1", "MyCollection2" e così via.  
-   **Chiave di partizione** : il nome del campo negli eventi di output utilizzati per specificare la chiave per partizioni output nelle raccolte. Per l'output unica raccolta, qualsiasi colonna output non autorizzato può essere utilizzato ad esempio PartitionId.  
-   **ID documento** – facoltativo. Il nome del campo negli eventi di output utilizzati per specificare la chiave primaria nella quale Inserisci o aggiorna le operazioni seguono si basano.  
