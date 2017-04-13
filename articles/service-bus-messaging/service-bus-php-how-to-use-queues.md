<properties 
    pageTitle="Come utilizzare code Bus di servizio con PHP | Microsoft Azure" 
    description="Informazioni su come usare code Bus di servizio in Azure. Esempi di codice scritti in PHP." 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Come usare code Bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questa guida viene illustrato come utilizzare code Bus di servizio. Gli esempi scritto in PHP e usare [Azure SDK per PHP](../php-download-sdk.md). Gli scenari coperti includono **creare code**, **inviare e ricevere messaggi**ed **eliminazione di code**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP

L'unico requisito per la creazione di un'applicazione PHP che accede al servizio Blob Azure è il riferimento di classi in [Azure SDK per PHP](../php-download-sdk.md) all'interno del codice. Per creare l'applicazione o il blocco note, è possibile utilizzare gli strumenti di sviluppo.

> [AZURE.NOTE] L'installazione di PHP deve inoltre includere l' [estensione OpenSSL](http://php.net/openssl) installato e attivato.

In questa Guida, si utilizzeranno le funzionalità di servizio che possono essere chiamate da un'applicazione PHP in locale o nel codice in esecuzione all'interno di un ruolo Azure web, un ruolo di lavoro o un sito Web.

## <a name="get-the-azure-client-libraries"></a>Ottenere il client Azure raccolte

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Per utilizzare la coda di servizio Bus API, eseguire le operazioni seguenti:

1. Fare riferimento al file caricatore utilizzando [require_once] [ require_once] istruzione.
2. Fare riferimento a tutte le classi che è possibile utilizzare.

Nell'esempio seguente viene illustrato come includere il file caricatore e fare riferimento alla classe **ServicesBuilder** .

> [AZURE.NOTE] In questo esempio (e altri esempi in questo articolo) si presuppone che sono stati installati librerie Client PHP per Azure tramite compositore. Se è stato installato le librerie manualmente o come pacchetto PERE, è necessario fare riferimento il file caricatore **WindowsAzure.php** .

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Negli esempi seguenti, il `require_once` verrà visualizzato sempre istruzione, ma solo le classi necessarie, ad esempio eseguire fanno riferimento.

## <a name="set-up-a-service-bus-connection"></a>Configurare una connessione Bus di servizio

Per creare un'istanza di un client Bus di servizio, è necessario disporre prima di tutto una stringa di connessione valido in questo formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Dove **Endpoint** è in genere del formato `[yourNamespace].servicebus.windows.net`.

Per creare un client di servizi Azure è necessario utilizzare la classe **ServicesBuilder** . Si può:

* Passare la stringa di connessione direttamente a.
* Utilizzare **CloudConfigurationManager (CCM)** per selezionare più origini esterne per la stringa di connessione:
    * Per impostazione predefinita è dotato di supporto per un'origine esterna - variabili di ambiente
    * È possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi riportati di seguito, la stringa di connessione verrà passata direttamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda

È possibile eseguire operazioni di gestione per code Bus del servizio mediante la classe **ServiceBusRestProxy** . Costruzione di un oggetto di **ServiceBusRestProxy** tramite il metodo factory **ServicesBuilder::createServiceBusService** con una stringa di connessione appropriata che incapsula le autorizzazioni di token gestirlo.

Nell'esempio seguente viene illustrato come creare un'istanza di un **ServiceBusRestProxy** e chiamare **ServiceBusRestProxy -> createQueue** per creare una coda denominata `myqueue` all'interno di un `MySBNamespace` spazio dei nomi del servizio:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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

> [AZURE.NOTE] È possibile utilizzare il `listQueues` metodo su `ServiceBusRestProxy` oggetti per verificare se esiste già una coda con un nome specificato all'interno di uno spazio dei nomi.

## <a name="how-to-send-messages-to-a-queue"></a>Procedura: inviare messaggi a una coda

Per inviare un messaggio a una coda Bus di servizio, l'applicazione chiama il metodo **ServiceBusRestProxy -> sendQueueMessage** . Il codice seguente viene illustrato come inviare un messaggio per il `myqueue` coda creata in precedenza all'interno di `MySBNamespace` spazio dei nomi del servizio.

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
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Messaggi inviate a (e ricevute da) code Bus di servizio sono istanze della classe **BrokeredMessage** . Gli oggetti **BrokeredMessage** è un insieme di metodi standard (ad esempio **getLabel** **getTimeToLive**, **setLabel**e **setTimeToLive**) e le proprietà che vengono utilizzate per contenere le proprietà personalizzate specifiche di un'applicazione e corpo dei dati dell'applicazione non autorizzato.

Code Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in una coda ma c'è un'estremità alla dimensione totale dei messaggi contenute in una coda. Questo limite massimo di dimensione coda è 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Come ricevere i messaggi da una coda

Il modo migliore per ricevere i messaggi da una coda consiste nell'utilizzare un metodo **ServiceBusRestProxy -> receiveQueueMessage** . È possono ricevere i messaggi in due modi diversi: **ReceiveAndDelete** (impostazione predefinita) e **PeekLock**.

Ricezione di utilizzo della modalità **ReceiveAndDelete** è un'operazione su una singola finestra di dialogo - vale a dire quando Bus di servizio riceve una richiesta di lettura per un messaggio in una coda, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità di **ReceiveAndDelete** il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Nella modalità di **PeekLock** riceve un messaggio diventa un'operazione in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una convocazione, trova il messaggio successivo per l'utilizzo, blocca in modo da impedire che altri utenti ricevendo e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione passando il messaggio ricevuto a **ServiceBusRestProxy -> deleteMessage**. Quando servizio Bus rileva **deleteMessage** chiamata, verrà contrassegnare il messaggio come utilizzata e rimuoverlo dalla coda.

Nell'esempio seguente viene illustrato un messaggio può essere ricevuto ed elaborati utilizzando **PeekLock** modalità (non l'impostazione predefinita).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Procedura: gestire errori delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **unlockMessage** sul messaggio ricevuto (anziché il metodo **deleteMessage** ). In questo modo Bus di servizio sbloccare il messaggio all'interno della coda e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della coda, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima di rilasciata la richiesta di **deleteMessage** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Si tratta spesso **Almeno una volta elaborazione**; vale a dire ogni messaggio viene elaborato almeno una volta, ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, è consigliabile aggiungere logica aggiuntiva alle applicazioni per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando il metodo **getMessageId** del messaggio, rimane costante tra tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di code Bus di servizio, vedere [code, argomenti e le sottoscrizioni][] per altre informazioni.

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori PHP](/develop/php/).

[Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
