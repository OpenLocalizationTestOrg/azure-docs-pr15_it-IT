<properties 
   pageTitle="Guida introduttiva di Azure SDK Node Azure dati Lake archivi | Microsoft Azure"
   description="Informazioni su come utilizzare Node per lavorare con dati Lake archivio account e il file system." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Guida introduttiva di Azure SDK Node Azure dati Lake archivio

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)


Informazioni su come usare Azure SDK per node per creare un account Azure dati Lake Store ed eseguire operazioni di base, ad esempio durante la creazione di cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per ulteriori informazioni sui dati Lake Store, vedere [Panoramica dei dati Lake Store](data-lake-store-overview.md). Attualmente, supporta SDK

  *  **Versione Node: 0.10.0 o versioni successive**
  *  **Versione API REST per Account: anteprima 01 di 10 2015**
  *  **Versione API REST per file System: anteprima 01 di 10 2015**

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Creare un'applicazione di Azure Active Directory**. Utilizzare l'applicazione di Azure Active Directory per autenticare l'applicazione di archivio di dati Lake con Azure Active Directory. Sono disponibili diversi metodi per l'autenticazione con Azure Active Directory, che sono **al servizio**o **l'autenticazione degli utenti finali** . Per ulteriori informazioni su come eseguire l'autenticazione e istruzioni, vedere [autentica con dati Lake Store con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Come installare

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Eseguire l'autenticazione tramite Azure Active Directory

Frammenti riportata di seguito mostrano due modi diversi di autenticazione con archivio Lake dati utilizzando Azure Active Directory. Per informazioni dettagliate sui vari metodi da utilizzare per l'autenticazione con dati Lake Store, vedere [autentica con dati Lake Store con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

Il frammento seguente richiede l'input come nome di dominio Active Directory Azure, ID client per un'app di Azure Active Directory e così via. Tutti questi dettagli possono essere recuperati da Azure AD applicazioni che è necessario creato, i dettagli di cui sono inclusi anche in collegamento.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Creare i client di archivio di dati Lake

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Creare un Account di archivio di dati Lake

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>Creare un file con il contenuto
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Ottenere un elenco di file e cartelle

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Vedere anche

- [Microsoft Azure SDK per Node](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK per Node - Lake Analitica la gestione dei dati](https://www.npmjs.com/package/azure-arm-datalake-analytics)
