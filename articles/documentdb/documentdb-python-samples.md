<properties 
    pageTitle="Esempi di NoSQL Python per DocumentDB | Microsoft Azure" 
    description="Trovare NoSQL Python esempi su github per le attività comuni in DocumentDB, incluse le operazioni CRUD per i documenti JSON nei database NoSQL." 
    keywords="esempi di Python"
    services="documentdb" 
    authors="moderakh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter="python"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/18/2016" 
    ms.author="moderakh"/>


# <a name="documentdb-python-examples"></a>Esempi di DocumentDB Python

> [AZURE.SELECTOR]
- [Esempi di .NET](documentdb-dotnet-samples.md)
- [Esempi di Node](documentdb-nodejs-samples.md)
- [Esempi di Python](documentdb-python-samples.md)
- [Raccolta di Azure codice di esempio](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Soluzioni di esempio che eseguono le operazioni CRUD e altre operazioni comuni sulle risorse Azure DocumentDB sono inclusi nell'archivio di GitHub [python di documentdb azure](https://github.com/Azure/azure-documentdb-python/tree/master/samples) . In questo articolo fornisce:

- Collegamenti alle attività in ciascuno dei file di progetto di esempio Python. 
- Collegamenti a API correlata fare riferimento a contenuto.

**Prerequisiti**

1. È necessario un account Azure da utilizzare in questi esempi vengono Python:
    - È possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): viene visualizzato crediti è possibile utilizzare per provare a pagamento servizi Azure e anche se vengono utilizzati massimo è possibile mantenere l'account e usare gratuiti servizi Azure, ad esempio siti Web. Carta di credito non verranno mai addebitata, a meno che non in modo esplicito modificare le impostazioni e chiedere di essere addebitati.
   - È possibile [attivare vantaggi sottoscrittore Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): abbonamento Your Visual Studio offre crediti ogni mese che è possibile utilizzare per i servizi di Azure a pagamento.
2. È inoltre necessario [SDK Python](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Ogni campione è indipendente, stesso configura automaticamente e pulisce dopo la stessa proprietà. Di conseguenza, gli esempi di problemi più chiamate a [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Ogni volta che questa operazione viene eseguita l'iscrizione per la fatturazione per 1 ora di utilizzo per il livello di prestazioni della raccolta da creare. 

## <a name="database-examples"></a>Esempi di database

Il file [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) del progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) viene illustrato come eseguire le operazioni seguenti.

Attività | Riferimento all'API
--- | ---
[Creare un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Query di un account per un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document_client. QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Leggere un database tramite l'Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document_client. IntegrazioneLettura](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Elenca i database per un account](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Eliminare un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document_client. DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## <a name="collection-examples"></a>Esempi di raccolta 

Il file [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) del progetto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) viene illustrato come eseguire le operazioni seguenti.

Attività | Riferimento all'API
--- | ---
[Creare una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Lettura di un elenco di tutte le raccolte in un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document_client. ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ottenere un insieme in base all'Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document_client. ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ottenere il livello di prestazioni di una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Modificare il livello di prestazioni di una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document_client. ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Eliminare una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document_client. DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
