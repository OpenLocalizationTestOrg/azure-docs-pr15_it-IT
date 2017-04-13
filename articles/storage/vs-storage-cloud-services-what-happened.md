<properties
    pageTitle="Dov'è il progetto di servizio cloud? | Microsoft Azure | Visual Studio connettersi servizi"
    description="Descrivere cosa accade in un progetto di servizi cloud dopo la connessione a un account di archiviazione Azure tramite Visual Studio connessi servizi"
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

# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Dov'è il progetto di servizi cloud (lo spazio di archiviazione di Visual Studio Azure connesso servizio)?

## <a name="references-added"></a>Riferimenti aggiunti

Il pacchetto di Azure archiviazione NuGet è stata aggiunta al progetto di Visual Studio.  
Questo pacchetto aggiunge i riferimenti .NET seguenti:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Stringa di connessione per l'archiviazione di Azure aggiunto
Elementi creati con l'account di archiviazione selezionato stringa di connessione e la chiave. Sono state apportate modifiche per i file seguenti:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**
