<properties 
    pageTitle="Come usare code Bus di servizio in Node | Microsoft Azure" 
    description="Informazioni su come utilizzare code Bus di servizio in Azure da un'app Node." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Come usare code Bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In questo articolo viene descritto come utilizzare code Bus di servizio in Node. Gli esempi scritto in JavaScript e usare il modulo di Azure Node. Gli scenari coperti includono **creare code**, **inviare e ricevere messaggi**ed **eliminazione di code**. Per ulteriori informazioni sulle code, vedere la sezione [passaggi successivi](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node

Creare un'applicazione Node vuota. Per istruzioni su come creare un'applicazione Node, vedere [creare e distribuire un'applicazione di Node in un sito Web Azure][], o un [Servizio Cloud Node][] usando Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Per utilizzare Bus di servizio Azure, scaricare e usare il pacchetto di Azure Node. Questo pacchetto include un set di raccolte che comunicare con i servizi di assistenza Bus resto.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Consente di ottenere il pacchetto di nodo pacchetto Manager (NPM)

1. Utilizzare la finestra di comando di **Windows PowerShell per Node** di spostarsi tra i **c:\\nodo\\sbqueues\\WebRole1** cartella in cui è stato creato l'applicazione di esempio.

2. Digitare **npm installare azure** nella finestra di comando, che deve risultare in output simile al seguente:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3. È possibile eseguire manualmente il comando **ls** per verificare che un **nodo\_moduli** cartella è stata creata. All'interno di tale cartella individuare il pacchetto di **azure** , che contiene le librerie che è necessario accedere alle code Bus di servizio.

### <a name="import-the-module"></a>Importare il modulo

Usa il blocco note o un altro editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

```
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurare una connessione Bus di servizio Azure

Modulo di Azure legge le variabili di ambiente AZURE\_SERVICEBUS\_spazio dei nomi e AZURE\_SERVICEBUS\_accesso\_chiave per ottenere informazioni necessarie per connettersi al servizio Bus. Se non sono tali variabili, è necessario specificare le informazioni sull'account quando si chiama **createServiceBusService**.

Per un esempio di impostazione le variabili di ambiente in un file di configurazione per un servizio Cloud di Azure, vedere [Servizio Cloud Node con lo spazio di archiviazione][].

Per un esempio di impostazione le variabili di ambiente in [Azure portale classico][] per un sito Web di Azure, vedere [Applicazione Web Node con lo spazio di archiviazione][].

## <a name="create-a-queue"></a>Creare una coda

L'oggetto **ServiceBusService** consente di utilizzare code Bus di servizio. Il codice seguente viene creato un oggetto **ServiceBusService** . Aggiungere tale record nella parte superiore del file **server.js** dopo l'istruzione per importare il modulo di Azure:

```
var serviceBusService = azure.createServiceBusService();
```

Chiamando **createQueueIfNotExists** sull'oggetto **ServiceBusService** (se presente), viene restituita coda specificata o viene creata una nuova coda con il nome specificato. Il codice seguente utilizza **createQueueIfNotExists** per creare o connettersi alla coda denominata `myqueue`:

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** supporta anche altre opzioni che consentono di ignorare le impostazioni di coda predefinite, ad esempio durata messaggio Dimensione coda live o massimo. Nell'esempio seguente imposta le dimensioni massime coda a 5 GB e un'ora in live valore (TTL) di 1 minuto:

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtri

Operazioni di filtro facoltative possono essere applicate alle operazioni eseguite mediante **ServiceBusService**. Operazioni di filtro, è possono includere registrazione automaticamente la ripetizione, e così via. Filtri sono oggetti che implementano un metodo con la firma:

```
function handle (requestOptions, next)
```

Dopo aver eseguendo il pre-elaborazione sulle opzioni di richiesta, è necessario chiamare il metodo `next`, passando un callback con la firma seguente:

```
function (returnObject, finalCallback, next)
```

In questo callback e dopo l'elaborazione **returnObject** (la risposta della richiesta al server), il callback deve richiamare `next` esistente per continuare l'elaborazione di altri filtri o semplicemente richiamare `finalCallback`, che termina chiamata del servizio.

Due filtri che implementano ritentare sono inclusi in Azure SDK per Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Di seguito crea un oggetto **ServiceBusService** che utilizza **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

Per inviare un messaggio a una coda Bus di servizio, l'applicazione chiama il metodo **sendQueueMessage** sull'oggetto **ServiceBusService** . Messaggi inviate a (e ricevute da) code Bus di servizio sono oggetti **BrokeredMessage** e hanno un insieme di proprietà standard (ad esempio **etichette** e **TimeToLive**), un dizionario che viene utilizzata per contenere le proprietà personalizzate specifiche di un'applicazione e il corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo del messaggio passando una stringa del messaggio. Proprietà standard richieste vengono popolati con valori predefiniti.

Nell'esempio seguente viene illustrato come inviare un messaggio di prova alla coda denominata `myqueue` utilizzando **sendQueueMessage**:

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Code Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in una coda ma c'è un'estremità alla dimensione totale dei messaggi contenute in una coda. Questa dimensione coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per ulteriori informazioni sulle quote, vedere [le quote di Bus di servizio][].

## <a name="receive-messages-from-a-queue"></a>Ricevere i messaggi da una coda

I messaggi vengono ricevuti da una coda utilizzando il metodo **receiveQueueMessage** sull'oggetto **ServiceBusService** . Per impostazione predefinita, i messaggi eliminati dalla coda quando vengono letti; Tuttavia, è possibile leggere (anteprima) e bloccare il messaggio senza eliminare dalla coda mediante l'impostazione di parametri facoltativi **isPeekLock** su **true**.

Il comportamento predefinito di lettura e l'eliminazione del messaggio come parte dell'operazione di ricezione è il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Se il parametro **isPeekLock** è impostato su **true**, la ricezione diventa un in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamata **deleteMessage** metodo e fornendo il messaggio da eliminare come parametro. Il metodo **deleteMessage** contrassegnare il messaggio come utilizzata e rimuoverlo dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi che utilizzano **receiveQueueMessage**. Nell'esempio prima di tutto riceve un messaggio viene eliminato e riceve un messaggio usando **isPeekLock** impostato su **true**e quindi Elimina il messaggio utilizzando **deleteMessage**:

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **unlockMessage** sull'oggetto **ServiceBusService** . In questo modo Bus di servizio sbloccare il messaggio all'interno della coda e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della coda, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima che venga chiamato il metodo **deleteMessage** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Spesso detta **Almeno una volta elaborazione**, vale a dire ogni messaggio verrà elaborato almeno una volta ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando la proprietà **ID messaggio** del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle code, vedere le risorse seguenti.

-   [Code, argomenti e le sottoscrizioni][]
-   Archivio di [Azure SDK per nodo][] in GitHub
-   [Centro per sviluppatori Node](/develop/nodejs/)

  [Azure SDK per nodo]: https://github.com/Azure/azure-sdk-for-node
  [Portale classica Azure]: http://manage.windowsazure.com
  
  [Servizio Cloud Node]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
  [Creare e distribuire un'applicazione di Node in un sito Web di Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Servizio Cloud Node con lo spazio di archiviazione]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Applicazione Web Node con lo spazio di archiviazione]: ../storage/storage-nodejs-how-to-use-table-storage.md
  [Quote di Bus di servizio]: service-bus-quotas.md
 
