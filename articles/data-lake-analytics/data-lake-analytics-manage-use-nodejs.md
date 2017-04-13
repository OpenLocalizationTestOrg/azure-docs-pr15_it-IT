<properties
   pageTitle="Gestire Azure dati Lake Analitica utilizzando Azure SDK per Node | Azure"
   description="Informazioni su come gestire gli account dati Lake Analitica, origini dati, processi e gli utenti che utilizzano Azure SDK per Node"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gestire Azure dati Lake Analitica utilizzando Azure SDK per Node


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK per Node utilizzabili per la gestione di account Azure dati Lake Analitica, processi e cataloghi. Per vedere l'argomento relativo alla gestione usare altri strumenti, fare clic su Seleziona la scheda precedente.

Ora supporta:

  *  **Versione Node: 0.10.0 o versioni successive**
  *  **Versione API REST per Account: anteprima 01 di 10 2015**
  *  **Versione API REST per il catalogo: anteprima 01 di 10 2015**
  *  **Versione API REST per processo: anteprima 20 di 03 2016**

## <a name="features"></a>Caratteristiche

- Gestione degli account: creare, visualizzare, elenco, aggiornare ed eliminare.
- Gestione: inviare, ricevere, elenco, annullare.
- Gestione del catalogo: ottenere, elenco, creare (informazioni riservate), aggiornare (informazioni riservate) ed eliminare (informazioni riservate).

## <a name="how-to-install"></a>Come installare

```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Eseguire l'autenticazione tramite Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Creare il client di dati Lake Analitica

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Creare un account di dati Lake Analitica

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
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

## <a name="get-a-list-of-jobs"></a>Ottenere un elenco dei processi

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Ottenere un elenco di database nel catalogo dati Lake Analitica
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Vedere anche

- [Microsoft Azure SDK per Node](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK per Node - gestione dati Lake archivio](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)
