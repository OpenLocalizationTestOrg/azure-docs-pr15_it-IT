<properties
    pageTitle="Dov'è il progetto ASP.NET? | Microsoft Azure | Visual Studio connettersi servizi"
    description="Descrivere cosa accade dopo l'aggiunta di spazio di archiviazione di Azure a un progetto ASP.NET utilizzando Visual Studio connessione servizi"
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

# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Dov'è il progetto ASP.NET (lo spazio di archiviazione di Visual Studio Azure connesso servizio)?

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

##<a name="connection-string-for-azure-storage-added"></a>Stringa di connessione per l'archiviazione di Azure aggiunto
Nel file config del progetto, un elemento è stato creato con l'account di archiviazione selezionato stringa di connessione e la chiave.

Per ulteriori informazioni, vedere [ASP.NET](http://www.asp.net).
