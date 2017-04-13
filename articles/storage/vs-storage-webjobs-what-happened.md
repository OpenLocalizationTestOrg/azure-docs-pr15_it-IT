<properties
    pageTitle="Dov'è il progetto WebJob (lo spazio di archiviazione di Visual Studio Azure connesso servizio)? | Microsoft Azure"
    description="Descrive modifiche apportate in un progetto di Azure WebJob dopo la connessione a un account di archiviazione utilizzando Visual Studio connessi servizi"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Dov'è il progetto WebJob (lo spazio di archiviazione di Visual Studio Azure connesso servizio)?

## <a name="references-added"></a>Riferimenti aggiunti

Pacchetto NuGet lo spazio di archiviazione di Azure è stato aggiunto o aggiornato di un progetto di Visual Studio.  
Questo pacchetto aggiunge i riferimenti .NET seguenti:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Stringa di connessione per l'archiviazione di Azure aggiunto
Nel file di App del progetto, le voci **AzureWebJobsStorage** e **AzureWebJobsDashboard** sono state aggiornate con la stringa di connessione dell'account selezionato lo spazio di archiviazione e la chiave.

Per ulteriori informazioni, vedere [WebJobs Azure documentazione risorse](http://go.microsoft.com/fwlink/?linkid=390226).
