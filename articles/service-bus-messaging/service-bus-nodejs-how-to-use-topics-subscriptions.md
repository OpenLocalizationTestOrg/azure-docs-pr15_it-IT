<properties 
    pageTitle="Come utilizzare argomenti Bus di servizio con Node | Microsoft Azure" 
    description="Informazioni su come usare argomenti Bus di servizio e le sottoscrizioni di Azure da un'app Node." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare servizio Bus argomenti e le sottoscrizioni

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questa guida viene descritto come utilizzare gli argomenti Bus di servizio e le sottoscrizioni da applicazioni Node. Gli scenari coperti includono **la creazione di argomenti e le sottoscrizioni**, **creazione di filtri di abbonamento**, **l'invio di messaggi** a un argomento, **la ricezione di messaggi da una sottoscrizione**ed **eliminare gli argomenti e le sottoscrizioni**. Per ulteriori informazioni su argomenti e le sottoscrizioni, vedere la sezione [passaggi successivi](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node

Creare un'applicazione Node vuota. Per istruzioni sulla creazione di un'applicazione Node, vedere [creare e distribuire un'applicazione di Node a un sito Web di Azure], [Servizio Cloud Node][] mediante Windows PowerShell, o il sito Web con WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Per utilizzare Bus di servizio, scaricare il pacchetto di Azure Node. Questo pacchetto include un set di raccolte che comunicare con i servizi di assistenza Bus resto.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Consente di ottenere il pacchetto di nodo pacchetto Manager (NPM)

1.  Utilizzare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix), passare alla cartella in cui è stato creato l'applicazione di esempio.

2.  Digitare **npm installare azure** nella finestra di comando, che deve risultare nell'output seguente:

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

3.  È possibile eseguire manualmente il comando **ls** per verificare che un **nodo\_moduli** cartella è stata creata. All'interno di tale cartella individuare il pacchetto di **azure** , che contiene le librerie che è necessario accedere ad argomenti Bus di servizio.

### <a name="import-the-module"></a>Importare il modulo

Usa il blocco note o un altro editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurare una connessione Bus di servizio

Modulo di Azure legge le variabili di ambiente AZURE\_SERVICEBUS\_spazio dei nomi e AZURE\_SERVICEBUS\_accesso\_chiave le informazioni necessarie per connettersi a Bus di servizio. Se non sono tali variabili, è necessario specificare le informazioni sull'account quando si chiama **createServiceBusService**.

Per un esempio di impostazione le variabili di ambiente in un file di configurazione per un servizio Cloud di Azure, vedere [Servizio Cloud Node con lo spazio di archiviazione][].

Per un esempio di impostazione le variabili di ambiente in [Azure portale classico][] per un sito Web di Azure, vedere [Applicazione Web Node con lo spazio di archiviazione][].

## <a name="create-a-topic"></a>Creare un argomento

L'oggetto **ServiceBusService** consente di utilizzare gli argomenti. Il codice seguente viene creato un oggetto **ServiceBusService** . Aggiungere tale record nella parte superiore del file **server.js** dopo l'istruzione per importare il modulo di azure:

```
var serviceBusService = azure.createServiceBusService();
```

Chiamando **createTopicIfNotExists** sull'oggetto **ServiceBusService** , verrà restituito l'argomento (se presente) o verrà creato un nuovo argomento con il nome specificato. Il codice seguente utilizza **createTopicIfNotExists** per creare o connettersi all'argomento relativo alla 'MyTopic':

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** supporta anche altre opzioni che consentono di ignorare le impostazioni predefinite argomento, ad esempio durata messaggio o le dimensioni massime argomento. Nell'esempio seguente imposta le dimensioni massime argomento a 5GB con una durata di 1 minuto:

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtri

Operazioni di filtro facoltative possono essere applicate alle operazioni eseguite mediante **ServiceBusService**. Operazioni di filtro, è possono includere registrazione automaticamente la ripetizione, e così via. Filtri sono oggetti che implementano un metodo con la firma:

```
function handle (requestOptions, next)
```

Dopo aver completato la pre-elaborazione sulle opzioni di richiesta, il metodo chiama `next` passando callback con la firma seguente:

```
function (returnObject, finalCallback, next)
```

In questo callback e dopo l'elaborazione **returnObject** (la risposta della richiesta al server), le esigenze di callback a uno richiamano Avanti esistenza per continuare l'elaborazione di altri filtri o semplicemente richiamare in caso contrario **finalCallback** per terminare la chiamata del servizio.

Due filtri che implementano ritentare sono inclusi in Azure SDK per Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Di seguito crea un oggetto **ServiceBusService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Creare le sottoscrizioni

Sottoscrizioni argomento vengono anche create con l'oggetto **ServiceBusService** . Le sottoscrizioni sono denominate e possono avere un filtro facoltativo che limita l'insieme di messaggi recapitati a coda virtuale dell'abbonamento.

> [AZURE.NOTE] Le sottoscrizioni sono permanenti e continueranno a esistere fino a quando uno dei due abbiano o l'argomento sono associate, vengono eliminati. Se l'applicazione contiene la logica per creare una sottoscrizione, deve verificare se la sottoscrizione esiste già utilizzando il metodo **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare un abbonamento con il filtro predefinito (MatchAll)

Il filtro **MatchAll** è il filtro predefinito che viene utilizzato se viene specificato alcun filtro quando viene creato un nuovo abbonamento. Quando viene utilizzato il filtro **MatchAll** , tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale dell'abbonamento. Nell'esempio seguente viene creata una sottoscrizione denominata 'AllMessages' e viene utilizzato il filtro di **MatchAll** predefinito.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Creare gli abbonamenti con i filtri

È anche possibile creare filtri che consentono di ambito che i messaggi inviati a un argomento deve apparire all'interno di una sottoscrizione di un argomento specifico.

Il tipo di filtro supportata dagli abbonamenti più flessibile è **SqlFilter**, che implementa un sottoinsieme di SQL92. Filtri SQL funzionano con le proprietà dei messaggi pubblicati all'argomento. Per ulteriori informazioni sulle espressioni che possono essere usate con un filtro SQL rivedere [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] sintassi.

Filtri possono essere aggiunti a un abbonamento utilizzando il metodo **createRule** dell'oggetto **ServiceBusService** . Questo metodo consente di aggiungere nuovi filtri a un abbonamento esistente.

> [AZURE.NOTE] Poiché il filtro predefinito viene applicato automaticamente a tutti i nuovi abbonamenti, è necessario rimuovere il filtro predefinito o **MatchAll** sovrascriverà i filtri che è possibile specificare. È possibile rimuovere la regola predefinita utilizzando il metodo **deleteRule** dell'oggetto **ServiceBusService** .

Nell'esempio seguente viene creata una sottoscrizione denominata `HighMessages` con **SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà personalizzata **messagenumber** maggiore di 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Allo stesso modo, nell'esempio seguente viene creata una sottoscrizione denominata `LowMessages` con **SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà di **messagenumber** minore o uguale a 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Quando un messaggio a questo punto viene inviato alla `MyTopic`, sempre essere recapitato ricevitori sottoscritti il `AllMessages` abbonamento argomento e in modo selettivo recapitato a ricevitori sottoscritto l'abbonamento alla `HighMessages` e `LowMessages` abbonamenti argomento (in base al contenuto del messaggio).

## <a name="how-to-send-messages-to-a-topic"></a>Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento Bus di servizio, l'applicazione deve utilizzare il metodo **sendTopicMessage** dell'oggetto **ServiceBusService** .
I messaggi inviati ad argomenti Bus di servizio sono oggetti **BrokeredMessage** .
Gli oggetti **BrokeredMessage** è un insieme di proprietà standard (ad esempio **etichette** e **TimeToLive**), un dizionario che viene utilizzata per contenere proprietà specifiche di applicazione personalizzata e il corpo di dati di tipo stringa. Un'applicazione di impostare il corpo del messaggio, è possibile passare un valore stringa **sendTopicMessage** e proprietà standard richieste verrà per impostazione predefinita.

Nell'esempio seguente viene illustrato come inviare messaggi a 'MyTopic' cinque prova. Si noti che il valore della proprietà **messagenumber** di ogni messaggio varia a iterazione del ciclo (questo determinerà le sottoscrizioni che ricevano dal destinatario):

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Argomenti Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in un argomento ma c'è un'estremità alla dimensione totale dei messaggi contenute in un argomento. Dimensione in questo argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ricezione di messaggi da una sottoscrizione

I messaggi vengono ricevuti da un abbonamento utilizzando il metodo **receiveSubscriptionMessage** sull'oggetto **ServiceBusService** . Per impostazione predefinita, i messaggi vengono eliminati dalla sottoscrizione quando vengono letti; Tuttavia, è possibile leggere (anteprima) e bloccare il messaggio senza eliminare dalla sottoscrizione impostando il parametro facoltativo **isPeekLock** su **true**.

Il comportamento predefinito di lettura e l'eliminazione del messaggio come parte dell'operazione di ricezione è il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Se il parametro **isPeekLock** è impostato su **true**, la ricezione diventa un in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione.
Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamata **deleteMessage** metodo e fornendo il messaggio da eliminare come parametro. Il metodo **deleteMessage** contrassegnare il messaggio come utilizzata e rimuovere dalla sottoscrizione.

Nell'esempio seguente viene illustrato messaggi possono essere ricevuti ed elaborati utilizzando **receiveSubscriptionMessage**. Nell'esempio riceve e consente di eliminare un messaggio dalla sottoscrizione 'LowMessages' e viene ricevuto un messaggio dalla sottoscrizione 'HighMessages' con **isPeekLock** impostato su true. Quindi Elimina il messaggio utilizzando **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **unlockMessage** sull'oggetto **ServiceBusService** . In questo modo Bus di servizio sbloccare il messaggio all'interno della sottoscrizione e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della sottoscrizione, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio sblocca automaticamente il messaggio e rende disponibile al ricevuto nuovamente.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima che venga chiamato il metodo **deleteMessage** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Spesso detta **Almeno una volta elaborazione**, vale a dire ogni messaggio verrà elaborato almeno una volta ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando la proprietà **ID messaggio** del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare gli argomenti e le sottoscrizioni

Gli argomenti e le sottoscrizioni sono permanenti e devono essere eliminate in modo esplicito tramite il [portale classica Azure][] o a livello di programmazione.
Nell'esempio seguente viene illustrato come eliminare l'argomento relativo alla `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Eliminazione di un argomento eliminerà anche le sottoscrizioni che sono registrate con l'argomento. Le sottoscrizioni possono anche essere eliminate in modo indipendente. Nell'esempio seguente viene illustrato come eliminare una sottoscrizione denominata `HighMessages` dalla `MyTopic` argomento:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base degli argomenti relativi a Bus di servizio, seguire questi collegamenti per altre informazioni.

-   Vedere [code, argomenti e le sottoscrizioni][].
-   Guida di riferimento API per [SqlFilter][].
-   Visitare l'archivio di [Azure SDK per nodo][] su GitHub.

  [Azure SDK per nodo]: https://github.com/Azure/azure-sdk-for-node
  [Portale classica Azure]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Servizio Cloud Node]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Creare e distribuire un'applicazione di Node a un sito Web di Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Servizio Cloud Node con lo spazio di archiviazione]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Applicazione Web Node con lo spazio di archiviazione]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
