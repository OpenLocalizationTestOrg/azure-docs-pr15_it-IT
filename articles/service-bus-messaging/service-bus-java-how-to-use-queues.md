<properties
    pageTitle="Come utilizzare code Bus di servizio con Java | Microsoft Azure"
    description="Informazioni su come usare code Bus di servizio in Azure. Esempi di codice scritti in linguaggio."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Come usare code Bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In questo articolo viene descritto come utilizzare code Bus di servizio. Gli esempi scritto in linguaggio e usare [Azure SDK per Java][]. Gli scenari coperti includono **creare code**, **inviare e ricevere messaggi**ed **eliminazione di code**.

## <a name="what-are-service-bus-queues"></a>Quali sono le code Bus del servizio?

Le code Bus di servizio supportano un modello di comunicazione **gestito messaggistica** . Quando si usa code, componenti di un'applicazione distribuita non comunicano direttamente tra loro. Se, tuttavia scambiati messaggi tramite una coda, che opera a livello intermedio (gestore). Un produttore di messaggio (mittente) mani disattivare un messaggio nella coda e quindi continua l'elaborazione.
In modo asincrono, consumer messaggio (ricevitore) recupera il messaggio dalla coda ed elabora. Il produttore non è necessario attendere una risposta dal consumer per continuare a elaborare e inviare altri messaggi. Code offrono il recapito dei messaggi **del primo FIFO (First Out)** a uno o più consumer concorrenti. Ovvero i messaggi vengono in genere ricevuti ed elaborati da ricevitori nell'ordine in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuta ed elaborata da consumer solo un messaggio.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Servizio Bus code sono una tecnologia generica che può essere usata per una vasta gamma di scenari:

- Comunicazioni tra i ruoli web e di lavoro in un'applicazione Azure a più livelli.
- Comunicazioni tra App locale e Azure ospitato App in una soluzione ibrida.
- Comunicazione tra i componenti di un'applicazione distribuita in esecuzione in locale in diverse organizzazioni o reparti di un'organizzazione.

Utilizzo di code consente di scalabilità più facilmente le applicazioni e quindi abilitare flessibilità dell'architettura.

## <a name="create-a-service-namespace"></a>Creare uno spazio dei nomi del servizio

Per iniziare a utilizzare code Bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Assicurarsi che siano installati [Azure SDK per Java][] prima di procedere in questo esempio. Se si utilizza Eclisse, è possibile installare il [Toolkit di Azure per Eclisse][] che include Azure SDK per Java. È quindi possibile aggiungere **Librerie di Microsoft Azure per Java** al progetto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Aggiungere quanto segue `import` le istruzioni nella parte superiore del file di linguaggio:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Creare una coda

Eseguire le operazioni di gestione di code Bus di servizio mediante la classe **ServiceBusContract** . Costruzione di un oggetto **ServiceBusContract** con una configurazione appropriata che incapsula token sa con le autorizzazioni per la gestione e la classe **ServiceBusContract** è il punto di comunicazione con Azure esclusivo.

La classe **ServiceBusService** fornisce metodi per creare, enumerare ed eliminare code. Nell'esempio seguente mostra come un oggetto **ServiceBusService** può essere utilizzato per creare una coda denominata "TestQueue" con uno spazio dei nomi denominato "HowToSample":

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

In **QueueInfo** metodi che consentono di proprietà della coda da ottimizzare (ad esempio: impostare time to live (TTL) quello predefinito da applicare ai messaggi inviati alla coda). Nell'esempio seguente viene illustrato come creare una coda denominata `TestQueue` con un massimo di 5GB:

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Si noti che è possibile utilizzare il metodo **listQueues** sugli oggetti **ServiceBusContract** per verificare se esiste già una coda con un nome specificato nello spazio dei servizi.

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

Per inviare un messaggio a una coda Bus di servizio, l'applicazione ottiene un oggetto **ServiceBusContract** . Il codice seguente viene illustrato come inviare un messaggio per il `TestQueue` coda creata in precedenza nel `HowToSample` spazio dei nomi:

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

I messaggi inviati a e ricevuto dal servizio Bus code sono istanze della classe [BrokeredMessage][] . Gli oggetti [BrokeredMessage][] è un insieme di proprietà standard (ad esempio [etichette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario che viene utilizzata per contenere proprietà specifiche di applicazione personalizzata e il corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo del messaggio passando qualsiasi oggetto serializzabile nel costruttore di [BrokeredMessage][]e serializzatore appropriato quindi essere usato per serializzare l'oggetto. In alternativa, è possibile fornire un linguaggio **. IO. InputStream** oggetto.

Nell'esempio seguente viene illustrato come inviare cinque testare i messaggi per il `TestQueue` **MessageSender** abbiamo ottenuto il frammento di codice precedente:

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Code Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in una coda ma c'è un'estremità alla dimensione totale dei messaggi contenute in una coda. Questa dimensione coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ricevere i messaggi da una coda

Il modo migliore per ricevere i messaggi da una coda consiste nell'utilizzare un oggetto **ServiceBusContract** . Messaggi ricevuti possono lavorare in modi diversi: **ReceiveAndDelete** e **PeekLock**.

Ricezione di utilizzo della modalità **ReceiveAndDelete** è un'operazione su una singola finestra di dialogo - vale a dire quando Bus di servizio riceve una richiesta di lettura per un messaggio in una coda, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità **ReceiveAndDelete** , ovvero la modalità predefinita, il modello più semplice che funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo.
Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Nella modalità di **PeekLock** ricevere assume la forma di un'operazione in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando **eliminare** sul messaggio ricevuto. Quando servizio Bus rileva la chiamata **eliminare** , verrà contrassegnare il messaggio come utilizzata e rimuoverlo dalla coda.

Nell'esempio seguente viene illustrato messaggi possono essere ricevuti ed elaborati utilizzando **PeekLock** modalità (non l'impostazione predefinita). Nell'esempio seguente viene infinito ed elabora i messaggi in arrivo in nostro "TestQueue":

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **unlockMessage** sul messaggio ricevuto (anziché il metodo **deleteMessage** ). In questo modo Bus di servizio sbloccare il messaggio all'interno della coda e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della coda, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima di rilasciata la richiesta di **deleteMessage** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Spesso detta **Almeno una volta elaborazione**, vale a dire ogni messaggio verrà elaborato almeno una volta ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando il metodo **getMessageId** del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di code Bus di servizio, vedere [code, argomenti e le sottoscrizioni][] per altre informazioni.

Per ulteriori informazioni, vedere il [Centro per sviluppatori di linguaggio](/develop/java/).


  [Azure SDK per Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit per Eclisse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

