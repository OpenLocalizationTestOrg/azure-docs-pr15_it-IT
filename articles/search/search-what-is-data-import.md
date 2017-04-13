<properties
    pageTitle="Caricamento dei dati di Azure ricerca | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Informazioni su come caricare dati in un indice di ricerca di Azure."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Caricare dati alla ricerca di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTO](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Modelli di ricerca Azure di caricamento dei dati
Esistono due modi per popolare l'indice di ricerca di Azure con i dati. La prima opzione è manualmente inserendo i dati nell'indice utilizzando la ricerca di Azure [API REST](search-import-data-rest-api.md) o [.NET SDK](search-import-data-dotnet.md). La seconda opzione è anche possibile [scegliere un'origine dati supportati](search-indexer-overview.md) per l'indice di ricerca di Azure e consentire Azure ricerca automaticamente estrarre i dati nel servizio di ricerca.

Questa Guida verrà illustrate solo le caratteristiche istruzioni su come utilizzare il modello push di caricamento di dati (che è supportato solo [l'API REST](search-import-data-rest-api.md) e [.NET SDK](search-import-data-dotnet.md)), ma possono comunque acquisire familiarità con il modello pull riportata di seguito.

### <a name="push-data-to-an-index"></a>Inserire i dati a un indice

Questo approccio fa riferimento a livello di programmazione l'invio di dati per la ricerca di Azure per renderla disponibile per la ricerca. L'utilità requisiti latenza molto bassa (ad esempio, se è necessario eseguire una ricerca operazioni per essere aggiornati con i database di inventario dinamico), il modello push è l'unica opzione disponibile.

È possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [.NET SDK](search-import-data-dotnet.md) per push dei dati a un indice. Attualmente non è disponibile alcun supporto strumento per l'inserimento dati tramite il portale.

Questo approccio è più flessibile il modello pull perché è possibile caricare documenti singolarmente o in batch (fino a 1000 per batch o 16 MB, indipendentemente dal limite viene primo). Il modello push consente anche di caricare documenti in Azure ricerca indipendentemente da dove sono i dati.

### <a name="pull-data-into-an-index"></a>Estrarre i dati in un indice

Il modello pull ricerca per indicizzazione di un'origine dati supportati e carica automaticamente i dati in è indice di ricerca di Azure dell'utente. Il rilevamento delle modifiche ed eliminazioni ai documenti esistenti oltre a riconoscere i nuovi documenti, indicizzatori rimuovere la necessità di gestire attivamente i dati in un indice.

Risultati di ricerca di Azure, questa funzionalità è implementata tramite *indicizzatori*, attualmente disponibile per l' [archiviazione Blob (preview)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [database SQL Azure e SQL Server in macchine virtuali di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

La funzionalità dell'indicizzatore viene esposta nel [Portale di Azure](search-import-data-portal.md) anche come l' [API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).
