<properties
    pageTitle="Spostare i dati da e verso archiviazione Blob Azure | Microsoft Azure"
    description="Spostare i dati da e verso archiviazione Blob Azure"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>Spostare i dati da e verso archiviazione Blob Azure

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Indicazioni sulle tecnologie utilizzati per spostare i dati a e/o dallo spazio di archiviazione Blob Azure sono collegati qui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
Il metodo più adatto per si dipende dallo scenario. L'articolo [scenari per analitica avanzate in apprendimento Azure](machine-learning-data-science-plan-sample-scenarios.md) consente di determinare le risorse che necessarie per un'ampia varietà di flussi di lavoro di scienze dati nel processo di analitica avanzate.

> [AZURE.NOTE] Per un'introduzione completa a archiviazione blob Azure, fare riferimento alle [Attività di base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) e al [Servizio Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

In alternativa, è possibile utilizzare [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) per: 

- creazione e programmazione di una pipeline che il download dei dati da archiviazione blob Azure, 
- passare a un servizio web pubblicato apprendimento Azure 
- ricevere i risultati analitica previsione e 
- caricare i risultati allo spazio di archiviazione. 

Per ulteriori informazioni, vedere [creare pipeline previsione utilizzando Azure Data Factory e apprendimento Azure](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Prerequisiti

In questo documento si presuppone che si dispone di un abbonamento a Azure, un account di archiviazione e la chiave di spazio di archiviazione corrispondente per l'account. Prima di caricamento/download dei dati, è necessario conoscere la chiave account e nome account Azure dello spazio di archiviazione.

- Per configurare un abbonamento a Azure, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni chiave e account, vedere [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md).
