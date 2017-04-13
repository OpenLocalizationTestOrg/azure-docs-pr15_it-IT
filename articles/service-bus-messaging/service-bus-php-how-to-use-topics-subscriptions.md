<properties 
    pageTitle="Come utilizzare argomenti Bus di servizio con PHP | Microsoft Azure" 
    description="Informazioni su come utilizzare argomenti Bus di servizio con PHP in Azure." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare le sottoscrizioni e gli argomenti della Bus di servizio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In questo articolo viene illustrato come utilizzare le sottoscrizioni e argomenti Bus di servizio. Gli esempi scritto in PHP e usare [Azure SDK per PHP](../php-download-sdk.md). Scenari coperti includono **la creazione di argomenti e le sottoscrizioni**, **creazione di filtri di abbonamento**, **l'invio di messaggi a un argomento**, **la ricezione di messaggi da un abbonamento**ed **eliminazione argomenti e le sottoscrizioni**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP

L'unico requisito per la creazione di un'applicazione PHP che accede al servizio Blob Azure consiste nel fare riferimento alle classi in [Azure SDK per PHP](../php-download-sdk.md) all'interno del codice. Per creare l'applicazione o il blocco note, è possibile utilizzare gli strumenti di sviluppo.

> [AZURE.NOTE] L'installazione di PHP deve inoltre includere l' [estensione OpenSSL](http://php.net/openssl) installato e attivato.

In questo articolo viene descritto come utilizzare le funzionalità di servizio che possono essere chiamate all'interno di un'applicazione PHP in locale o nel codice in esecuzione all'interno di un ruolo Azure web, un ruolo di lavoro o un sito Web.

## <a name="get-the-azure-client-libraries"></a>Ottenere il client Azure raccolte

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Per usare l'API Bus di servizio:

1. Fare riferimento al file caricatore utilizzando [require_once] [ require-once] istruzione.
2. Fare riferimento a tutte le classi che è possibile utilizzare.

Nell'esempio seguente viene illustrato come includere il file caricatore e fare riferimento alla classe **ServiceBusService** .

> [AZURE.NOTE] In questo esempio (e altri esempi in questo articolo) si presuppone che sono stati installati librerie Client PHP per Azure tramite compositore. Se è stato installato le librerie manualmente o come pacchetto PERE, è necessario fare riferimento il file caricatore **WindowsAzure.php** .

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Negli esempi seguenti il `require_once` verrà visualizzato sempre istruzione, ma solo le classi necessarie, ad esempio eseguire fanno riferimento.

## <a name="set-up-a-service-bus-connection"></a>Configurare una connessione Bus di servizio

Per creare un'istanza di un client Bus di servizio è necessario disporre prima di tutto una stringa di connessione valido in questo formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Dove `Endpoint` è in genere del formato `https://[yourNamespace].servicebus.windows.net`.

Per creare un client di servizi Azure è necessario utilizzare la classe **ServicesBuilder** . Si può:

* Passare la stringa di connessione direttamente a.
* Utilizzare **CloudConfigurationManager (CCM)** per selezionare più origini esterne per la stringa di connessione:
    * Per impostazione predefinita è dotato di supporto per un'origine esterna - variabili di ambiente.
    * È possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource** .

Per gli esempi riportati di seguito, la stringa di connessione viene passata direttamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Creare un argomento

È possibile eseguire le operazioni di gestione degli argomenti Bus del servizio mediante la classe **ServiceBusRestProxy** . Costruzione di un oggetto di **ServiceBusRestProxy** tramite il metodo factory **ServicesBuilder::createServiceBusService** con una stringa di connessione appropriata che incapsula le autorizzazioni di token gestirlo.

Nell'esempio seguente viene illustrato come creare un'istanza di un **ServiceBusRestProxy** e chiamare **ServiceBusRestProxy -> createTopic** per creare un argomento denominato `mytopic` all'interno di un `MySBNamespace` spazio dei nomi:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] È possibile utilizzare il `listTopics` metodo su `ServiceBusRestProxy` oggetti di verificare se un argomento con un nome specificato è già presente nello spazio dei servizi.

## <a name="create-a-subscription"></a>Creare una sottoscrizione

Sottoscrizioni argomento vengono anche create con il metodo **ServiceBusRestProxy -> createSubscription** . Le sottoscrizioni sono denominate e possono avere un filtro facoltativo che limita il set di messaggi passati a coda virtuale dell'abbonamento.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare un abbonamento con il filtro predefinito (MatchAll)

Il filtro **MatchAll** è il filtro predefinito che viene utilizzato se viene specificato alcun filtro quando viene creato un nuovo abbonamento. Quando viene utilizzato il filtro **MatchAll** , tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale dell'abbonamento. Nell'esempio seguente viene creata una sottoscrizione denominata 'mysubscription' e viene utilizzato il filtro di **MatchAll** predefinito.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Creare gli abbonamenti con i filtri

È anche possibile impostare i filtri che consentono di specificare quali messaggi inviati a un argomento devono trovarsi all'interno di una sottoscrizione di un argomento specifico. Il tipo di filtro supportata dagli abbonamenti più flessibile è **SqlFilter**, che implementa un sottoinsieme di SQL92. Filtri SQL funzionano con le proprietà dei messaggi pubblicati all'argomento. Per ulteriori informazioni su SqlFilters, vedere [Proprietà SqlFilter.SqlExpression][sqlfilter].

> [AZURE.NOTE] Ogni regola per un abbonamento elabora i messaggi in arrivo in modo indipendente, aggiungere i messaggi di risultato all'abbonamento. Inoltre, ogni nuova sottoscrizione dispone di un oggetto di **regola** predefinito con un filtro che consente di aggiungere tutti i messaggi da un argomento all'abbonamento. Per ricevere solo i messaggi che corrispondono il filtro, è necessario rimuovere la regola predefinita. È possibile rimuovere la regola predefinita utilizzando il `ServiceBusRestProxy->deleteRule` metodo.

Nell'esempio seguente viene creata una sottoscrizione denominata **HighMessages** con **SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà personalizzata di **MessageNumber** maggiore di 3 (per informazioni sull'aggiunta di proprietà personalizzate ai messaggi, vedere [inviare messaggi a un argomento](#send-messages-to-a-topic) ):

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Si noti che questo codice richiede l'uso di uno spazio dei nomi aggiuntiva: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Allo stesso modo, nell'esempio seguente viene creata una sottoscrizione denominata **LowMessages** con **SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà di **MessageNumber** minore o uguale a 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

A questo punto, quando un messaggio viene inviato al `mytopic` argomento viene sempre recapitato al ricevitori sottoscritto l'abbonamento alla `mysubscription` sottoscrizione e in modo selettivo recapitato a ricevitori sottoscritto l'abbonamento alla `HighMessages` e `LowMessages` sottoscrizioni (in base al contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

Per inviare un messaggio a un argomento Bus di servizio, l'applicazione chiama il metodo **ServiceBusRestProxy -> sendTopicMessage** . Il codice seguente viene illustrato come inviare un messaggio per il `mytopic` argomento creato in precedenza all'interno di `MySBNamespace` spazio dei nomi del servizio.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

I messaggi inviati ad argomenti Bus di servizio sono istanze della classe **BrokeredMessage** . Gli oggetti **BrokeredMessage** è un insieme di proprietà standard e i metodi (ad esempio **getLabel** **getTimeToLive**, **setLabel**e **setTimeToLive**), oltre al tipo di proprietà che può essere utilizzata per contenere le proprietà personalizzate specifiche dell'applicazione. Nell'esempio seguente viene illustrato come inviare messaggi di prova 5 per le `mytopic` argomento creato in precedenza. Il metodo **setProperty** viene utilizzato per aggiungere una proprietà personalizzata (`MessageNumber`) a ogni messaggio. Si noti che la `MessageNumber` proprietà valore varia per ogni messaggio (è possibile utilizzare questo valore per determinare quali sottoscrizioni ricevano dal destinatario, come illustrato nella sezione [Crea una sottoscrizione](#create-a-subscription) ):

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Argomenti Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in un argomento ma c'è un'estremità alla dimensione totale dei messaggi contenute in un argomento. Questo limite superiore alle dimensioni argomento è 5 GB. Per ulteriori informazioni sulle quote, vedere [le quote di Bus di servizio][].

## <a name="receive-messages-from-a-subscription"></a>Ricezione di messaggi da una sottoscrizione

Il modo migliore per ricevere i messaggi da un abbonamento consiste nell'utilizzare un metodo **ServiceBusRestProxy -> receiveSubscriptionMessage** . Messaggi ricevuti possono lavorare in modi diversi: **ReceiveAndDelete** (impostazione predefinita) e **PeekLock**.

Ricezione di utilizzo della modalità **ReceiveAndDelete** è un'operazione su una singola finestra di dialogo; vale a dire quando Bus di servizio riceve una richiesta di lettura per un messaggio in una sottoscrizione, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità di **ReceiveAndDelete** il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Nella modalità di **PeekLock** riceve un messaggio diventa un'operazione in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione passando il messaggio ricevuto a **ServiceBusRestProxy -> deleteMessage**. Quando servizio Bus rileva **deleteMessage** chiamata, verrà contrassegnare il messaggio come utilizzata e rimuoverlo dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare un messaggio usando **PeekLock** modalità (non l'impostazione predefinita). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Procedura: gestire errori delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **unlockMessage** sul messaggio ricevuto (anziché il metodo **deleteMessage** ). In questo modo Bus di servizio sbloccare il messaggio all'interno della coda e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della coda, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima di rilasciata la richiesta di **deleteMessage** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Si tratta spesso **Almeno una volta elaborazione**; vale a dire ogni messaggio viene elaborato almeno una volta, ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alle applicazioni per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando il metodo **getMessageId** del messaggio, rimane costante tra tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare gli argomenti e le sottoscrizioni

Per eliminare un argomento o un abbonamento, utilizzare rispettivamente **ServiceBusRestProxy -> deleteTopic** o metodi **ServiceBusRestProxy -> deleteSubscripton** . Si noti che un argomento anche se si elimina le sottoscrizioni che sono registrate con l'argomento.

Nell'esempio seguente viene illustrato come eliminare un argomento denominato `mytopic` e le sottoscrizioni registrate.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Utilizzando il metodo **deleteSubscription** , è possibile eliminare una sottoscrizione in modo indipendente:

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di code Bus di servizio, vedere [code, argomenti e le sottoscrizioni][] per altre informazioni.

[Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Quote di Bus di servizio]: service-bus-quotas.md
