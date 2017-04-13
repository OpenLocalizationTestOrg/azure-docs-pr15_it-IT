<properties
    pageTitle="Come utilizzare argomenti Bus di servizio con Java | Microsoft Azure"
    description="Informazioni su come usare gli argomenti Bus di servizio e le sottoscrizioni in Azure. Esempi di codice vengono scritte per le applicazioni Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare le sottoscrizioni e gli argomenti della Bus di servizio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questa guida viene descritto come utilizzare le sottoscrizioni e argomenti Bus di servizio. Gli esempi scritto in linguaggio e usare [Azure SDK per Java][]. Scenari coperti includono **la creazione di argomenti e le sottoscrizioni**, **creazione di filtri di abbonamento**, **l'invio di messaggi a un argomento**, **la ricezione di messaggi da un abbonamento**ed **eliminazione argomenti e le sottoscrizioni**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Quali sono le sottoscrizioni e gli argomenti della Bus di servizio?

Supportano per sottoscrizioni e argomenti Bus di servizio una *pubblicazione/sottoscrizione* messaggistica modello di comunicazione. Quando si utilizza gli argomenti e le sottoscrizioni, componenti di un'applicazione distribuita non comunicano direttamente tra loro. Se, tuttavia scambiati messaggi tramite un argomento che si comporta come valore intermedio.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

A differenza dei code Bus di servizio, in cui ogni messaggio viene elaborato da un utente singolo, argomenti e le sottoscrizioni forniscono una forma "uno-a-molti" di comunicazione, utilizzando un modello di pubblicazione/sottoscrizione. È possibile registrare più abbonamenti a un argomento. Quando un messaggio viene inviato a un argomento, quindi renderlo disponibile per ciascuna sottoscrizione al punto/processo in modo indipendente.

Una sottoscrizione a un argomento è simile a una coda virtuale che riceve le copie dei messaggi inviati all'argomento. Facoltativamente, è possibile registrare le regole di filtro per un argomento in base al richiede la sottoscrizione, che consente di filtrare/limitare i messaggi a un argomento vengono ricevuti dalle quali sottoscrizioni argomento.

Le sottoscrizioni e servizio Bus argomenti consentono di scalare per l'elaborazione di un numero elevato di messaggi tra un numero elevato di utenti e applicazioni.

## <a name="create-a-service-namespace"></a>Creare uno spazio dei nomi del servizio

Per iniziare a utilizzare gli argomenti Bus di servizio e le sottoscrizioni di Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio. Uno spazio dei nomi fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Assicurarsi che siano installati [Azure SDK per Java][] prima di procedere in questo esempio. Se si utilizza Eclisse, è possibile installare il [Toolkit di Azure per Eclisse][] che include Azure SDK per Java. È quindi possibile aggiungere **Librerie di Microsoft Azure per Java** al progetto:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Aggiungere le seguenti istruzioni di importazione nella parte superiore del file di linguaggio:

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Aggiungere librerie di Azure per Java al percorso di compilazione e includere l'assembly di distribuzione di project.

## <a name="create-a-topic"></a>Creare un argomento

Eseguire le operazioni di gestione degli argomenti Bus del servizio mediante la classe **ServiceBusContract** . Costruzione di un oggetto **ServiceBusContract** con una configurazione appropriata che incapsula token sa con le autorizzazioni per la gestione e la classe **ServiceBusContract** è il punto di comunicazione con Azure esclusivo.

La classe **ServiceBusService** fornisce metodi per creare, enumerare ed eliminare argomenti. Nell'esempio seguente mostra come un oggetto **ServiceBusService** può essere usato per creare un argomento denominato `TestTopic`, con uno spazio dei nomi `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

In **TopicInfo** metodi che consentono di proprietà dell'argomento deve essere impostato (ad esempio: impostare time to live (TTL) quello predefinito da applicare ai messaggi inviati all'argomento). Nell'esempio seguente viene illustrato come creare un argomento denominato `TestTopic` con un massimo di 5 GB:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Si noti che è possibile utilizzare il metodo **listTopics** sugli oggetti **ServiceBusContract** di verificare se un argomento con un nome specificato è già presente nello spazio dei servizi.

## <a name="create-subscriptions"></a>Creare le sottoscrizioni

Gli abbonamenti a argomenti vengono anche creati con la classe **ServiceBusService** . Le sottoscrizioni sono denominate e possono avere un filtro facoltativo che limita il set di messaggi passati a coda virtuale dell'abbonamento.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare un abbonamento con il filtro predefinito (MatchAll)

Il filtro **MatchAll** è il filtro predefinito che viene utilizzato se viene specificato alcun filtro quando viene creato un nuovo abbonamento. Quando viene utilizzato il filtro **MatchAll** , tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale dell'abbonamento. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e utilizza il filtro di **MatchAll** predefinito.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Creare gli abbonamenti con i filtri

È anche possibile creare filtri che consentono di ambito che i messaggi inviati a un argomento devono apparire all'interno di una sottoscrizione di un argomento specifico.

Il tipo di filtro supportata dagli abbonamenti più flessibile è [SqlFilter][], che implementa un sottoinsieme di SQL92. Filtri SQL funzionano con le proprietà dei messaggi pubblicati all'argomento. Per ulteriori informazioni sulle espressioni che possono essere usate con un filtro SQL, controllare la sintassi [SqlFilter.SqlExpression][] .

Nell'esempio seguente viene creata una sottoscrizione denominata `HighMessages` con un oggetto [SqlFilter][] che consente di selezionare solo i messaggi che hanno una proprietà personalizzata di **MessageNumber** maggiore di 3:

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Allo stesso modo, nell'esempio seguente viene creata una sottoscrizione denominata `LowMessages` con un oggetto [SqlFilter][] che consente di selezionare solo i messaggi che hanno una proprietà di **MessageNumber** minore o uguale a 3:

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Quando un messaggio a questo punto viene inviato alla `TestTopic`, sempre essere recapitato ricevitori sottoscritti il `AllMessages` abbonamento e in modo selettivo recapitato a ricevitori sottoscritto l'abbonamento alla `HighMessages` e `LowMessages` sottoscrizioni (in base al contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

Per inviare un messaggio a un argomento Bus di servizio, l'applicazione ottiene un oggetto **ServiceBusContract** . Il codice seguente viene illustrato come inviare un messaggio il `TestTopic` argomento creato in precedenza all'interno di `HowToSample` spazio dei nomi:

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

I messaggi inviati al servizio Bus argomenti sono istanze della classe [BrokeredMessage][] . [BrokeredMessage][] *gli oggetti è un insieme di metodi standard (ad esempio * *setLabel* * e * *TimeToLive**), un dizionario che viene utilizzata per contenere le proprietà personalizzate specifiche di un'applicazione e un corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo del messaggio passando qualsiasi oggetto serializzabile nel costruttore di [BrokeredMessage][]e appropriato * *DataContractSerializer* * quindi essere usata per serializzare l'oggetto. In alternativa, un * *java.io.InputStream** può essere fornito.

Nell'esempio seguente viene illustrato come inviare cinque testare i messaggi per il `TestTopic` **MessageSender** abbiamo ottenuto il frammento di codice precedente.
Si noti come il valore della proprietà **MessageNumber** di ogni messaggio dipende dalla iterazione del ciclo (questo determinerà le sottoscrizioni che ricevano dal destinatario):

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Argomenti Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in un argomento ma c'è un'estremità alla dimensione totale dei messaggi contenute in un argomento. Dimensione in questo argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Come ricevere i messaggi da un abbonamento

Per ricevere i messaggi da un abbonamento, utilizzare un oggetto **ServiceBusContract** . Messaggi ricevuti possono lavorare in modi diversi: **ReceiveAndDelete** e **PeekLock**.

Ricezione di utilizzo della modalità **ReceiveAndDelete** è un'operazione su una singola finestra di dialogo - vale a dire quando Bus di servizio riceve una richiesta di lettura per un messaggio, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità di **ReceiveAndDelete** il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Nella modalità di **PeekLock** ricevere assume la forma di un'operazione in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando **eliminare** sul messaggio ricevuto. Quando servizio Bus rileva la chiamata **eliminare** , verrà contrassegnare il messaggio come utilizzata e rimuoverla da un argomento.

Nell'esempio seguente viene illustrato messaggi possono essere ricevuti ed elaborati utilizzando **PeekLock** modalità (non l'impostazione predefinita). Nell'esempio seguente esegue un ciclo elabora i messaggi nella sottoscrizione "HighMessages" e quindi si chiude quando non sono presenti più messaggi (in alternativa, se può essere impostata in attesa per i nuovi messaggi).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
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
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
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

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **unlockMessage** sul messaggio ricevuto (anziché il metodo **deleteMessage** ). In questo modo Bus di servizio sbloccare il messaggio all'interno dell'argomento e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno dell'argomento, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima di rilasciata la richiesta di **deleteMessage** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Spesso detta **Almeno una volta elaborazione**, vale a dire ogni messaggio verrà elaborato almeno una volta ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando il metodo **getMessageId** del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare gli argomenti e le sottoscrizioni

Il modo migliore per eliminare gli argomenti e le sottoscrizioni consiste nell'utilizzare un oggetto **ServiceBusContract** . Eliminazione di un argomento eliminerà anche le sottoscrizioni che sono registrate con l'argomento. Le sottoscrizioni possono anche essere eliminate in modo indipendente.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di code Bus di servizio, vedere [sottoscrizioni, argomenti e code Bus di servizio][] per ulteriori informazioni.

  [Azure SDK per Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit per Eclisse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Abbonamenti, argomenti e code Bus di servizio]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
