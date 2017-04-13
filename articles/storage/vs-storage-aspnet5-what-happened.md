<properties
    pageTitle="Dov'è il progetto ASP.NET 5 (Visual Studio connesso servizi) | Spazio di archiviazione di Microsoft Azure"
    description="Descrivere cosa accade dopo la connessione a un account di archiviazione Azure in un progetto di Visual Studio ASP.NET 5 utilizzando Visual Studio connessi servizi"
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

# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Dov'è il progetto ASP.NET 5 (lo spazio di archiviazione di Visual Studio Azure connesso servizi)?

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

Inoltre, è stato aggiunto il pacchetto NuGet **Microsoft.Framework.Configuration.Json** .

## <a name="connection-string-for-azure-storage-added"></a>Stringa di connessione per l'archiviazione di Azure aggiunto
Nel file config.json del progetto, un elemento è stato creato con l'account di archiviazione selezionato stringa di connessione e la chiave.

Per ulteriori informazioni, vedere [ASP.NET 5](http://www.asp.net/vnext).
