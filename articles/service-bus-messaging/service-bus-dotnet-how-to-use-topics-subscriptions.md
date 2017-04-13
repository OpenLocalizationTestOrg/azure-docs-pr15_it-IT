<properties
    pageTitle="Utilizzare argomenti Bus di servizio con .NET | Microsoft Azure"
    description="Informazioni su come utilizzare gli argomenti Bus di servizio e gli abbonamenti con .NET in Azure. Esempi di codice vengono scritte per le applicazioni .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare le sottoscrizioni e gli argomenti della Bus di servizio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In questo articolo viene descritto come utilizzare le sottoscrizioni e argomenti Bus di servizio. Gli esempi scritto in c# e utilizzano le API di .NET. Gli scenari coperti includono la creazione di abbonamenti, creazione filtri abbonamento, l'invio di messaggi a un argomento, la ricezione di messaggi da una sottoscrizione ed eliminazione argomenti e le sottoscrizioni e argomenti. Per ulteriori informazioni su argomenti e le sottoscrizioni, vedere la sezione [passaggi successivi](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurare un'applicazione di utilizzare Bus di servizio

Quando si crea un'applicazione che utilizza Bus di servizio, è necessario aggiungere un riferimento a assembly Bus di servizio e includere gli spazi dei nomi corrispondente. Il modo più semplice per eseguire questa operazione consiste nel scaricare il pacchetto [NuGet](https://www.nuget.org) appropriato.

## <a name="get-the-service-bus-nuget-package"></a>Ottenere il pacchetto di servizio Bus NuGet

Il [pacchetto del servizio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è il modo più semplice per ottenere l'API di Bus del servizio e per configurare l'applicazione con tutte le dipendenze Bus di servizio necessarie. Per installare il pacchetto di servizio Bus NuGet nel progetto, eseguire le operazioni seguenti:

1.  In Esplora soluzioni rapida **riferimenti**e quindi fare clic su **Gestisci pacchetti NuGet**.
2.  Cercare "Servizio Bus" e selezionare l'elemento **Bus di servizio di Microsoft Azure** . Fare clic su **Installa** per completare l'installazione, quindi chiudere la finestra di dialogo seguenti:

    ![][7]

A questo punto si è pronti scrivere codice per Bus di servizio.

## <a name="create-a-service-bus-connection-string"></a>Creare una stringa di connessione Bus di servizio

Servizio Bus utilizza una stringa di connessione per archiviare i punti finali e le credenziali. È possibile inserire la stringa di connessione in un file di configurazione, anziché hardcoded:

- Quando si utilizzano i servizi di Azure, si consiglia di memorizzare la stringa di connessione con il sistema di configurazione del servizio Azure (file .csdef e cscfg).
- Quando si usano siti Web Azure o macchine virtuali di Azure, si consiglia di memorizzare la stringa di connessione con il sistema di configurazione di .NET (ad esempio, il file config).

In entrambi i casi, è possibile recuperare la stringa di connessione utilizzando il `CloudConfigurationManager.GetSetting` metodo, come illustrato di seguito in questo articolo.

### <a name="configure-your-connection-string"></a>Configurare la stringa di connessione

Il meccanismo di configurazione del servizio consente di modificare in modo dinamico le impostazioni di configurazione dal [portale di Azure][] senza ridistribuire l'applicazione. Ad esempio, aggiungere un `Setting` etichetta al file di definizione (**.csdef**) del servizio, come illustrato nell'esempio riportato di seguito.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

È quindi possibile specificare i valori nel file di configurazione (. cscfg) del servizio.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Usare il nome della chiave condiviso accesso firma (SA) e i valori chiavi recuperati dal portale di come descritto in precedenza.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurare la stringa di connessione quando si usano i siti Web Azure o macchine virtuali di Azure

Quando si usano siti Web o macchine virtuali, si consiglia di utilizzare il sistema di configurazione di .NET (ad esempio config). Memorizzare la stringa di connessione utilizzando il `<appSettings>` elemento.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Usare il nome SA e i valori di chiave recuperati dal [portale di Azure][], come descritto in precedenza.

## <a name="create-a-topic"></a>Creare un argomento

È possibile eseguire le operazioni di gestione per gli abbonamenti utilizzando la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) e argomenti Bus di servizio. Questa classe fornisce metodi per creare, enumerare ed eliminare argomenti.

Nell'esempio seguente viene creata una `NamespaceManager` utilizzando il Azure `CloudConfigurationManager` classe con una stringa di connessione costituita l'indirizzo di base di uno spazio dei nomi Bus di servizio e le SA appropriate credenziali con autorizzazioni per gestirlo. Questa stringa di connessione è il seguente formato:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilizzare l'esempio seguente, in base alle impostazioni configurazione nella sezione precedente.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Esistono overload del metodo [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) che consentono di impostare le proprietà dell'argomento. ad esempio, per impostare il valore predefinito time to live (TTL) valore da applicare ai messaggi inviati all'argomento. Queste impostazioni vengono applicate utilizzando la classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . Nell'esempio seguente viene illustrato come creare un argomento denominato **TestTopic** con un massimo di 5 GB e un messaggio predefinito TTL di 1 minuto.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] È possibile utilizzare il metodo [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) sugli oggetti [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per verificare se un argomento con un nome specificato esiste già all'interno di uno spazio dei nomi.

## <a name="create-a-subscription"></a>Creare una sottoscrizione

È anche possibile creare sottoscrizioni argomento utilizzando la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Le sottoscrizioni sono denominate e possono avere un filtro facoltativo che limita il set di messaggi passati a coda virtuale dell'abbonamento.

> [AZURE.IMPORTANT] In ordine per i messaggi ricevuti da una sottoscrizione, è necessario creare l'abbonamento prima dell'invio di messaggi all'argomento. Se sono non presenti alcuna sottoscrizione a un argomento, l'argomento ignora i messaggi.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare un abbonamento con il filtro predefinito (MatchAll)

Se viene specificato alcun filtro quando viene creato un nuovo abbonamento, il filtro **MatchAll** è il filtro predefinito che viene utilizzato. Quando si utilizza il filtro **MatchAll** , tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale dell'abbonamento. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e utilizza il filtro di **MatchAll** predefinito.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Creare gli abbonamenti con i filtri

È anche possibile impostare i filtri che consentono di specificare quali messaggi inviati a un argomento devono trovarsi all'interno di una sottoscrizione di un argomento specifico.

Il tipo di filtro supportata dagli abbonamenti più flessibile è la classe [SqlFilter][] che implementa un sottoinsieme dei SQL92. Filtri SQL funzionano con le proprietà dei messaggi pubblicati all'argomento. Per ulteriori informazioni sulle espressioni che possono essere usate con un filtro SQL, vedere la sintassi [SqlFilter.SqlExpression][] .

Nell'esempio seguente viene creato un abbonamento con un oggetto [SqlFilter][] che consente di selezionare solo i messaggi che hanno una proprietà personalizzata di **MessageNumber** maggiore di 3 **HighMessages** .

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Allo stesso modo, nell'esempio seguente viene creata una sottoscrizione denominata **LowMessages** con [SqlFilter][] che consente di selezionare solo i messaggi che hanno una proprietà di **MessageNumber** minore o uguale a 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

A questo punto quando un messaggio viene inviato al `TestTopic`, è sempre inviato a ricevitori sottoscritto l'abbonamento argomento **AllMessages** e in modo selettivo recapitati ricevitori sottoscritti le sottoscrizioni di argomento **HighMessages** e **LowMessages** (a seconda il contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

Per inviare un messaggio a un argomento Bus di servizio, l'applicazione crea un oggetto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) utilizzando la stringa di connessione.

Il codice seguente viene illustrato come creare un oggetto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) per l'argomento **TestTopic** creata in precedenza mediante la [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) chiamata API.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

I messaggi inviati ad argomenti Bus di servizio sono istanze della classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Gli oggetti [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) è un insieme di proprietà standard (ad esempio [etichette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario che viene utilizzata per contenere le proprietà personalizzate specifiche di un'applicazione e il corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo del messaggio, passando qualsiasi oggetto serializzabile costruttore dell'oggetto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e appropriato **DataContractSerializer** quindi è utilizzata per serializzare l'oggetto. In alternativa, è possibile fornire un **System.IO.Stream** .

Nell'esempio seguente viene illustrato come inviare messaggi di prova cinque all'oggetto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) **TestTopic** ottenuto nell'esempio precedente. Si noti che il valore di proprietà [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) di ogni messaggio varia a seconda dell'iterazione del ciclo (questa impostazione determina le sottoscrizioni che ricevano dal destinatario).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Argomenti Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in un argomento ma c'è un'estremità alla dimensione totale dei messaggi contenute in un argomento. Dimensione in questo argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Se è abilitata la partizione, il valore massimo è superiore. Per ulteriori informazioni, vedere [partizionati messaggistica entità](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Come ricevere i messaggi da un abbonamento

Il modo consigliato per ricevere i messaggi da un abbonamento consiste nell'utilizzare un oggetto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) . Oggetti [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) possono lavorare in modi diversi: [ *ReceiveAndDelete* e *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Ricezione di utilizzo della modalità **ReceiveAndDelete** è un'operazione su una singola finestra di dialogo; vale a dire quando Bus di servizio riceve una richiesta di lettura per un messaggio in una sottoscrizione, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità di **ReceiveAndDelete** il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché Bus di servizio è contrassegnato il messaggio come consumate quando l'applicazione riavvia e inizia a utilizzare nuovamente i messaggi, essa verrà perse il messaggio che è stato utilizzato prima l'arresto anomalo.

Nella modalità **PeekLock** , ovvero la modalità predefinita, il processo di ricezione assume la forma di un'operazione di due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando [completata](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) nel messaggio ricevuto. Quando Bus di servizio rileva **completa** chiamata, contrassegna il messaggio come utilizzata e lo rimuove dalla sottoscrizione.

Nell'esempio seguente viene illustrato messaggi possono essere ricevuti ed elaborati utilizzando la modalità di **PeekLock** predefinita. Per specificare un valore [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) diverso, è possibile usare un altro overload per [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Questo esempio viene usata callback [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) per elaborare i messaggi in arrivo nella sottoscrizione **HighMessages** .

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

In questo esempio configurato callback [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) con l'oggetto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) . [Completamento automatico](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) è impostato su **false** per abilitare il controllo manuale del momento in cui chiamare [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) è impostato su 1 minuto, che determina il client necessario attendere fino a un minuto prima di terminare la caratteristica di rinnovo automatico e il client effettua una chiamata di nuova a controllare i messaggi. Valore di questa proprietà consente di ridurre il numero di volte in cui che il client effettua chiamate carico che non consentono di recuperare i messaggi.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione di destinazione è possibile elaborare il messaggio per qualche motivo, è possibile chiamare il metodo [abbandonare](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) sul messaggio ricevuto (invece del metodo di [completamento](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). In questo modo Bus di servizio sbloccare il messaggio all'interno della sottoscrizione e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della sottoscrizione, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio sblocca automaticamente il messaggio e rende disponibile al ricevuto nuovamente.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima di rilasciata la richiesta di [completamento](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) , il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Si tratta spesso *almeno una volta elaborazione*; vale a dire ogni messaggio viene elaborato almeno una volta, ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando la proprietà [ID messaggio](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio, rimane costante tra tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare gli argomenti e le sottoscrizioni

Nell'esempio seguente viene illustrato come eliminare l'argomento **TestTopic** dello spazio dei nomi di servizio **HowToSample** .

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Eliminazione di un argomento elimina anche le sottoscrizioni che sono registrate con l'argomento. Le sottoscrizioni possono anche essere eliminate in modo indipendente. Il codice seguente viene illustrato come eliminare una sottoscrizione denominata **HighMessages** dall'argomento **TestTopic** .

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base degli argomenti Bus di servizio e abbonamenti, seguire questi collegamenti per altre informazioni.

-   [Code, argomenti e le sottoscrizioni][].
-   [Esempio di filtri argomento][]
-   Guida di riferimento API per [SqlFilter][].
-   Creare un'applicazione che invia e riceve messaggi da e verso una coda Bus di servizio: [servizio Bus negoziate messaggistica esercitazione .NET][].
-   Esempi di Bus di servizio: Download degli [esempi di Azure][] o vedere la [Panoramica](service-bus-samples.md).

  [Portale di Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
  [Esempio di filtri argomento]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Servizio Bus negoziate messaggistica .NET esercitazione]: service-bus-brokered-tutorial-dotnet.md
  [Esempi di Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
