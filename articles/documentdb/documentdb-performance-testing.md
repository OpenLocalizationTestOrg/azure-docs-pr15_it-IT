<properties 
    pageTitle="DocumentDB scala e delle prestazioni | Microsoft Azure" 
    description="Informazioni su come eseguire test delle prestazioni con Azure DocumentDB e scala"
    keywords="verifica delle prestazioni"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Test con Azure DocumentDB proporzioni e le prestazioni
Prestazioni e scala verifica è fondamentale sviluppo di applicazioni. Per molte applicazioni, il livello di database ha un impatto sulle prestazioni e scalabilità complessiva e pertanto essenziale verifica delle prestazioni. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è per chiarezza prestazioni e scalabilità flessibile e pertanto ideale per applicazioni che richiedono un livello di database a prestazioni elevate. 

In questo articolo è un riferimento per gli sviluppatori di implementazione di gruppi di test delle prestazioni per i carichi di lavoro DocumentDB o valutazione DocumentDB per gli scenari di applicazione massime prestazioni. È incentrata principalmente su prestazioni isolata test del database, ma include anche procedure consigliate per le applicazioni.

Dopo aver letto in questo articolo, sarà possibile rispondere alle domande seguenti:   

- Dove trovano un'applicazione client .NET di esempio per la verifica delle prestazioni di Azure DocumentDB? 
- Come è possibile ottenere livelli di alta velocità con Azure DocumentDB dall'applicazione client?

Per iniziare con codice, scaricare il progetto dal [DocumentDB prestazioni-test dell'esempio](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] L'obiettivo di questa applicazione è illustrare le procedure consigliate per l'estrazione di migliorare le prestazioni fuori DocumentDB con un numero limitato di computer client. Questa operazione non è stata effettuata per illustrare la capacità di punta del servizio, possa scalare limitlessly.

Se si sta cercando opzioni di configurazione lato client migliorare le prestazioni di DocumentDB, vedere [suggerimenti per le prestazioni di DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Esecuzione di test di applicazione delle prestazioni
Il modo più rapido per iniziare consiste nel compilare ed eseguire l'esempio .NET seguente, come descritto nei passaggi seguenti. È anche possibile esaminare il codice sorgente e implementare configurazioni simili alle applicazioni client.

**Passaggio 1:** Scaricare il progetto dal [DocumentDB prestazioni-test dell'esempio](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)o divisione archivio Github.

**Passaggio 2:** Modificare le impostazioni per EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (facoltativo) in App.

> [AZURE.NOTE] Prima il provisioning di raccolte con alta velocità di esecuzione, fare riferimento alla [Pagina prezzi](https://azure.microsoft.com/pricing/details/documentdb/) per stimare i costi per ogni raccolta. Lo spazio di archiviazione di DocumentDB fatture e la velocità di trasmissione in modo indipendente oraria, in modo che è possibile risparmiare sui costi mediante l'eliminazione o ridurre la velocità delle raccolte di DocumentDB al termine del test.

**Passaggio 3:** Compilare ed eseguire l'app console dalla riga di comando. È necessario vedere output simile al seguente:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Passaggio 4, se necessario,:** La velocità di segnalati (RU/s) tramite lo strumento deve essere uguale o superiore la velocità di provisioning della raccolta. In caso contrario, aumentando DegreeOfParallelism con piccoli spostamenti consentono di raggiungere il limite. Se la velocità dell'App client avventurarci, avviare più istanze dell'app su computer stesse o in altre consente di raggiungere il limite di provisioning in istanze diverse. Se occorre assistenza per questo passaggio, scrivere un messaggio di posta elettronica a askdocdb@microsoft.com o per riempire un ticket di supporto.

Dopo avere inserito l'app in esecuzione, è possibile provare diversi [criteri di indicizzazione](documentdb-indexing-policies.md) e [livelli di coerenza](documentdb-consistency-levels.md) per comprendere l'impatto sulla velocità e la latenza. È anche possibile esaminare il codice sorgente e implementare configurazioni simili al test famiglie di prodotti o applicazioni di produzione.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo illustra come è possibile eseguire il test con DocumentDB un'app di console .NET proporzioni e le prestazioni. Fare riferimento ai collegamenti sotto per ulteriori informazioni sull'utilizzo di DocumentDB.

* [Esempio di test delle prestazioni DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opzioni di configurazione di client per migliorare le prestazioni di DocumentDB](documentdb-performance-tips.md)
* [Partizioni DocumentDB sul lato server](documentdb-partition-data.md)
* [Raccolte DocumentDB e livelli di prestazioni](documentdb-performance-levels.md)
* [DocumentDB .NET documentazione su MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Esempi di .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Blog di DocumentDB su suggerimenti sulle prestazioni](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
