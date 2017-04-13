<properties
    pageTitle="Usare lo spazio di archiviazione Azure nelle applicazioni di Windows Store | Microsoft Azure"
    description="Informazioni su come creare un'app di Windows Store che utilizza l'archiviazione Blob Azure, coda, una tabella o File."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Come utilizzare lo spazio di archiviazione di Azure nelle applicazioni di Windows Store

## <a name="overview"></a>Panoramica

Questa guida viene illustrato come iniziare a utilizzare lo sviluppo di un'app di Windows Store che utilizza l'archiviazione Azure.

## <a name="download-required-tools"></a>Scaricare gli strumenti necessari

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) è semplice creare, eseguire il debug, localizzare, creare un pacchetto e distribuire le applicazioni di Windows Store. Visual Studio 2012 o versioni successiva è necessario.
- La [Libreria Client lo spazio di archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fornisce una libreria di classi Windows Runtime per l'utilizzo di archiviazione Azure.
- [Strumenti di WCF dati Services per Windows Store App](http://www.microsoft.com/download/details.aspx?id=30714) estende l'esperienza di Aggiungi riferimento al servizio di supporto di OData lato client per le applicazioni di Windows Store in Visual Studio.

## <a name="develop-apps"></a>Sviluppare App

### <a name="getting-ready"></a>Operazioni preliminari

Creare un nuovo progetto di Windows Store app in Visual Studio 2012 o versione successiva:

![archivio-App-lo spazio di archiviazione-e-progetto][store-apps-storage-vs-project]

Aggiungere un riferimento alla libreria di Client lo spazio di archiviazione di Azure facendo clic su **riferimenti**, facendo clic su **Aggiungi riferimento**e quindi cercare l'archiviazione raccolta per Windows Runtime del Client che è stato scaricato:

![archivio-App--scegliere-libreria di archiviazione][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Utilizza la libreria con i servizi Blob e coda

A questo punto, l'app è pronto per chiamare i servizi Blob Azure e coda. Aggiungere le seguenti istruzioni **using** in modo che i tipi di archiviazione Azure è possono fare riferimento direttamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Aggiungere un pulsante alla pagina. Aggiungere il codice seguente al relativo evento **fare clic su** e modificare il metodo utilizzando la [parola chiave asincrono](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

In questo esempio si presuppone che il stringa due variabili, *nome account* e *accountKey*. Rappresentano il nome del proprio account di archiviazione e la chiave account associato a quell'account.

Creare ed eseguire l'applicazione. Fare clic sul pulsante verrà verificare l'esistenza di un contenitore denominato *container1* nell'account e quindi crearlo se non.

### <a name="using-the-library-with-the-table-service"></a>Utilizzo della libreria con il servizio di tabella

Tipi utilizzati per comunicare con il servizio di Azure tabella dipendono dal WCF Data Services per la raccolta di Windows Store app. Aggiungere un riferimento alle librerie WCF necessari tramite la Console di gestione pacchetti:

![archivio App-lo spazio di archiviazione-pacchetto-gestione][store-apps-storage-package-manager]

Utilizzare il comando seguente per Gestione pacchetti punto il percorso nel computer in uso:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Questo comando verrà aggiunte automaticamente tutti i riferimenti necessari al progetto. Se non si desidera utilizzare la Console di gestione pacchetti, è possibile aggiungere la cartella NuGet di servizi dati WCF sul computer locale nell'elenco di origine del pacchetto e quindi aggiungere il riferimento tramite l'interfaccia utente, come descritto nella [Gestione di pacchetti NuGet utilizzando la finestra di dialogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Quando si è fatto riferimento il pacchetto NuGet di servizi dati WCF, modificare il codice **fare clic su** evento del pulsante:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Questo codice viene verificato se una tabella denominata *Tabella1* presente nell'account e quindi crea se non.

È anche possibile aggiungere un riferimento a Microsoft.WindowsAzure.Storage.Table.dll, disponibile nello stesso pacchetto scaricato. Questa raccolta contiene funzionalità aggiuntive, ad esempio serializzazione basate su riflesso e query generica. Si noti che questa raccolta non supporta JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
