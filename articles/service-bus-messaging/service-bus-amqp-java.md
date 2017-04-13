<properties 
    pageTitle="Servizio Bus e Java con AMQP 1.0 | Microsoft Azure"
    description="Utilizzo del servizio Bus da Java con AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="use-service-bus-from-java-with-amqp-10"></a>Utilizzare Bus di servizio da Java con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java Message Service (JMS) è un'API standard per l'utilizzo di middleware orientati ai messaggi della piattaforma Java. Microsoft che Azure Bus di servizio sono stati testati con 1.0 AMQP in base a una raccolta di client JMS sviluppata da project Apache Qpid. Questa raccolta supporta l'API 1.1 JMS completo e può essere utilizzata con qualsiasi servizio di messaggistica compatibile AMQP 1.0. Questo scenario è supportato anche in [Service Bus per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (locale Service Bus). Per ulteriori informazioni, vedere [AMQP nel servizio Bus per Windows Server][].

## <a name="download-the-apache-qpid-amqp-10-jms-client-library"></a>Scaricare la raccolta di client Apache Qpid AMQP 1.0 JMS

Per informazioni su come scaricare la versione più recente della libreria client Apache Qpid JMS AMQP 1.0, visitare [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

È necessario aggiungere i seguenti file VASO quattro dall'archivio di distribuzione Apache Qpid JMS AMQP 1.0 percorso di classe Java durante la creazione e l'esecuzione di applicazioni JMS con Bus di servizio:

-   jms geronimo\_1.1\_JAR specifiche-[versione]

-   qpid-amqp-1-0-client-[Version].jar

-   qpid-amqp-1-0-client-JMS-[Version].jar

-   qpid-amqp-1-0-Common-[Version].jar

## <a name="work-with-service-bus-queues-topics-and-subscriptions-from-jms"></a>Lavorare con code, argomenti e le sottoscrizioni da JMS Bus di servizio

### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface (JNDI)

JMS utilizza il linguaggio denominazione e Directory Interface (JNDI) per creare una separazione tra i nomi logici e i nomi fisici. Due tipi di oggetti JMS vengono risolti utilizzando JNDI: **ConnectionFactory** e **destinazione**. JNDI utilizza un modello di provider in cui è possibile collegare diversi servizi directory per gestire i compiti di risoluzione dei nomi. La raccolta di Apache Qpid JMS AMQP 1.0 viene fornita con un semplice proprietà Provider JNDI basata su file che viene configurato tramite un file di testo.

Il Provider di JNDI File proprietà Qpid è configurato con un file delle proprietà del formato seguente:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### <a name="configure-the-connection-factory"></a>Configurare la connessione predefinita

La voce utilizzata per definire una **ConnectionFactory** nel Provider Qpid proprietà File JNDI è del formato seguente:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Dove `[jndi\_name]` e `[ConnectionURL]` significato è il seguente:

| Nome            | Significato                                                                                                                                    |   |   |   |   |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]`    | Nome logico di connessione. Questo nome viene risolto nell'applicazione di linguaggio utilizzando il JNDI `IntialContext.lookup()` metodo. |   |   |   |   |
| `[ConnectionURL]` | URL che fornisce la raccolta JMS con le informazioni necessarie per il gestore AMQP.                                                      |   |   |   |   |

Il formato della connessione URL è come indicato di seguito:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Dove `[namespace]`, `[username]`, e `[password]` significato è il seguente:

| Nome          | Significato                                                                        |   |   |   |   |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | Spazio dei nomi del servizio Bus ottenuto dal [portale di Azure][].                      |   |   |   |   |
| `[username]`  | Il nome della chiave del servizio Bus SA ottenuto dal [portale di Azure][].                    |   |   |   |   |
| `[password]`  | Modulo con codifica URL della chiave del servizio Bus SA ottenuto dal [portale di Azure][]. |   |   |   |   |

> [AZURE.NOTE] È necessario codificare URL la password manualmente. Un'utilità di codifica URL è disponibile in [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Ad esempio, se le informazioni ricevute dal portale di è la seguente:

| Namespace:   | test.ServiceBus.Windows.NET                  |
|--------------|----------------------------------------------|
| Nome emittente: | RootManageSharedAccessKey                                        |
| Chiave:  | abcdefg |

Quindi per definire un oggetto **ConnectionFactory** denominato `SBCONNECTIONFACTORY`, la stringa di configurazione sarà il seguente:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://RootManageSharedAccessKey:abcdefg@test.servicebus.windows.net
```

#### <a name="configure-destinations"></a>Configurare le destinazioni

La voce che definisce una destinazione nel Provider Qpid proprietà File JNDI è del formato seguente:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Dove `[jndi\_name]` e `[physical\_name]` significato è il seguente:

| Nome              | Significato                                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]`    | Nome logico di destinazione. Si tratta di nome viene risolto nell'applicazione di linguaggio mediante il JNDI `IntialContext.lookup()` metodo. |
| `[physical\name]` | Il nome dell'entità Bus di servizio in cui l'applicazione invia o riceve messaggi.                                                  |

Tenere presente quanto segue:

- Il `[physical\name]` valore può essere una coda Bus di servizio o l'argomento.
- Quando si riceve una sottoscrizione di argomento Bus di servizio, il nome fisico specificato in JNDI dovrebbe essere il nome dell'argomento. Il nome dell'abbonamento è disponibile quando si crea la sottoscrizione permanente nel codice dell'applicazione JMS.
- È anche possibile gestire una sottoscrizione argomento Bus di servizio come una coda. Esistono diversi vantaggi di questo approccio: lo stesso codice ricevitore utilizzabile per gli abbonamenti argomento e code e tutte le informazioni di indirizzo (il nome dell'argomento e abbonamento) sono esternalizzate nel file di proprietà.
- Per attribuire una sottoscrizione argomento Bus di servizio a una coda, deve essere la voce nel file di proprietà del modulo: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`. |

Per definire una destinazione JMS logica denominata "Argomento" che esegue il mapping a un argomento Bus di servizio denominato "argomento1", la voce nel file di proprietà dovrebbero essere come indicato di seguito:

```
topic.TOPIC = topic1
```

### <a name="send-messages-using-jms"></a>Invio di messaggi tramite JMS

Il codice seguente viene illustrato come inviare un messaggio a un argomento Bus di servizio. Si presuppone che `SBCONNECTIONFACTORY` e `TOPIC` sono definiti in un file di configurazione **servicebus.properties** come descritto nella sezione precedente.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### <a name="receive-messages-using-jms"></a>Ricevere messaggi con JMS

Il codice seguente mostra `how` per ricevere un messaggio da un abbonamento argomento Bus di servizio. Si presuppone che `SBCONNECTIONFACTORY` e argomento sono definiti in un file di configurazione **servicebus.properties** come descritto nella sezione precedente. Anche presuppone che sia il nome dell'abbonamento `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### <a name="guidelines-for-building-robust-applications"></a>Linee guida per la creazione di applicazioni affidabili

La specifica JMS definisce come contratto eccezione dei metodi API e il codice dell'applicazione deve essere scritte gestire tali eccezioni. Ecco alcuni altri aspetti da considerare relative alle eccezioni:

-   Registrare un **ExceptionListener** con la connessione JMS utilizzando **connection.setExceptionListener**. In questo modo un client ricevere una notifica di un problema in modo asincrono. Questa notifica è particolarmente importante per le connessioni che utilizzano solo i messaggi, come non hanno nessun altro modo per scoprire che la connessione non è riuscita. **ExceptionListener** è chiamato se esiste un problema con la connessione, sessione o collegamento sottostante AMQP. In questo caso, dell'applicazione necessario ricreare la **Connessione JMS**, **sessione**, **MessageProducer** e oggetti **MessageConsumer** da zero.

-   Per verificare che un messaggio è stato inviato con correttamente da un **MessageProducer** a un'entità Bus di servizio, assicurarsi che l'applicazione sia stata configurata con la **qpid.sync\_pubblicare** set di proprietà di sistema. È possibile eseguire questa operazione avvio del programma con il **-Dqpid.sync\_pubblicare = true** l'opzione SV linguaggio impostato nella riga di comando all'avvio dell'applicazione. Questa opzione consente di configurare la raccolta viene restituito dalla chiamata invia finché non è stata ricevuta che il messaggio è stato accettato dal servizio Bus conferma. Se si verifica un problema durante l'operazione di invio, viene generato un **JMSException** . Esistono due possibili cause: 
    1. Se il problema è dovuto Bus di servizio rifiuto determinato messaggio inviato, verrà generata un'eccezione di **MessageRejectedException** . Questo errore viene segnalato transitori, o a causa di un problema con il messaggio. La procedura consigliata da seguire è per rendere più tenta di ripetere l'operazione con una logica back-off. Se il problema persiste, il messaggio dovrebbe essere abbandonato con un errore ha eseguito l'accesso locale. Non è necessario ricreare gli oggetti **Connessione JMS**, **Session**o **MessageProducer** in questo caso. 
    2. Se il problema è dovuto Bus di servizio chiudere il collegamento AMQP, verrà generata un'eccezione di **InvalidDestinationException** . Può trattarsi di a causa di un problema temporaneo o a causa di entità messaggio da eliminare. In entrambi i casi, gli oggetti **Connessione JMS**, **sessione**e **MessageProducer** necessario creare di nuovo. Se la condizione di errore era temporanea, questa operazione non hanno un limite sarà completata. Se l'entità è stata eliminata, l'errore sarà permanente.

## <a name="messaging-between-net-and-jms"></a>Messaggistica tra .NET e JMS

### <a name="message-bodies"></a>Corpo dei messaggi

JMS definisce cinque diversi tipi di messaggi: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage**e **TextMessage**. L'API di .NET Bus del servizio è un tipo di singolo messaggio, [BrokeredMessage][].

#### <a name="jms-to-service-bus-net-api"></a>JMS al servizio Bus API .NET

Nelle sezioni seguenti viene illustrato come utilizzare i messaggi di tutti i tipi di messaggio JMS da .NET. Un esempio di **ObjectMessage** non è stato incluso, come corpo di un **ObjectMessage** contiene un oggetto serializzabile in linguaggio di programmazione Java, ossia non interpretato da un'applicazione .NET.

##### <a name="bytesmessage"></a>BytesMessage

Il codice seguente viene illustrato come utilizzare il corpo di un oggetto **BytesMessage** utilizzando le API di .NET Bus di servizio.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### <a name="mapmessage"></a>MapMessage

Il codice seguente viene illustrato come utilizzare il corpo di un oggetto **MapMessage** utilizzando le API di .NET Bus di servizio. Questo codice scorre gli elementi della mappa, la visualizzazione nome e il valore di ogni elemento.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### <a name="streammessage"></a>StreamMessage

Il codice seguente viene illustrato come utilizzare il corpo di un oggetto **StreamMessage** utilizzando le API di .NET Bus di servizio. Questo codice sono elencati tutti gli elementi dal flusso, insieme ai tipi.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### <a name="textmessage"></a>TextMessage

Il codice seguente viene illustrato come utilizzare il corpo di un oggetto **TextMessage** utilizzando le API di .NET Bus di servizio. Questo codice consente di visualizzare la stringa di testo contenuta nel corpo del messaggio.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### <a name="service-bus-net-apis-to-jms"></a>Servizio Bus API .NET a JMS

Nelle sezioni seguenti mostrano come un'applicazione .NET può creare un messaggio che verrà ricevuto nel JMS in ciascuno dei diversi tipi di messaggio JMS. Un esempio di **ObjectMessage** non è stato incluso, come corpo di un **ObjectMessage** contiene un oggetto serializzabile in linguaggio di programmazione Java, ossia non interpretato da un'applicazione .NET.

##### <a name="bytesmessage"></a>BytesMessage

Il codice seguente viene illustrato come creare un oggetto [BrokeredMessage][] in .NET ricevuto da un client JMS come un **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### <a name="streammessage"></a>StreamMessage

Il codice seguente viene illustrato come creare un oggetto [BrokeredMessage][] in .NET ricevuto da un client JMS come un **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### <a name="textmessage"></a>TextMessage

Il codice seguente viene illustrato come utilizzare il corpo di un **TextMessage** tramite l'API di .NET Bus di servizio. Questo codice consente di visualizzare la stringa di testo contenuta nel corpo del messaggio.

```
message = new BrokeredMessage("this is a text string");
```

### <a name="application-properties"></a>Proprietà delle applicazioni

####<a name="jms-to-service-bus-net-apis"></a>JMS a Bus .NET API di servizio

Messaggi JMS supportano le proprietà di applicazione dei tipi seguenti: **boolean**, **byte**, **breve**, **int**, **long**, **margine di flessibilità**, **double**e **stringa**. Il codice Java seguente mostra come impostare le proprietà relative a un messaggio usando dei diversi tipi di proprietà.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

Nelle API di .NET Bus del servizio, vengono applicate le proprietà delle applicazioni di messaggio dell'insieme di **proprietà** di [BrokeredMessage][]. Il codice seguente viene illustrato come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client JMS.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

Nella tabella seguente mostra come i tipi di proprietà JMS eseguire il mapping a tipi di proprietà .NET.

| Tipo di proprietà JMS | Tipo di proprietà .NET |
|-------------------|--------------------|
| Byte              | SByte              |
| Numero intero           | int                |
| Margine di flessibilità             | margine di flessibilità              |
| Effettuare un doppio            | doppia             |
| Valore booleano           | bool               |
| Stringa            | stringa             |

Il tipo di [BrokeredMessage][] supporta le proprietà di applicazione dei tipi seguenti: **byte**, **sbyte**, **char**, **breve**, **ushort**, **int**, **uint**, **long**, **ulong**, **margine di flessibilità**, **double**, **decimal**, **bool**, **Guid**, **stringa**, **Uri**, **DateTime**, **DateTimeOffset**e **TimeSpan**. Il codice .NET seguente mostra come impostare le proprietà su un oggetto [BrokeredMessage][] utilizzando ognuno di questi tipi di proprietà.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Il codice Java seguente viene illustrato come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client .NET Bus di servizio.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

Nella tabella seguente mostra come i tipi di proprietà .NET eseguire il mapping a tipi di proprietà JMS.

| Tipo di proprietà .NET | Tipo di proprietà JMS | Note                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | UnsignedByte      | -                                                                                                                                                                      |
| SByte              | Byte              | -                                                                                                                                                                     |
| caratt               | Carattere         | -                                                                                                                                                                     |
| breve              | Breve             | -                                                                                                                                                                     |
| ushort             | UnsignedShort     | -                                                                                                                                                                     |
| int                | Numero intero           | -                                                                                                                                                                     |
| uint               | UnsignedInteger   | -                                                                                                                                                                     |
| lungo               | Lungo              | -                                                                                                                                                                     |
| ulong              | UnsignedLong      | -                                                                                                                                                                     |
| margine di flessibilità              | Margine di flessibilità             | -                                                                                                                                                                     |
| doppia             | Effettuare un doppio            | -                                                                                                                                                                     |
| decimale            | BigDecimal        | -                                                                                                                                                                     |
| bool               | Valore booleano           | -                                                                                                                                                                     |
| GUID               | UUID              | -                                                                                                                                                                     |
| stringa             | Stringa            | -                                                                                                                                                                     |
| DateTime           | Data              | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | Mappata tipo AMQP DateTimeOffset.UtcTicks:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType     | Mappata tipo AMQP Timespan.Ticks:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType     | Mappata tipo AMQP Uri.AbsoluteUri:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-headers"></a>Intestazioni standard

Le tabelle seguenti illustrano come le intestazioni Standard JMS e le proprietà standard [BrokeredMessage][] sono mappati utilizzando AMQP 1.0.

#### <a name="jms-to-service-bus-net-apis"></a>JMS a Bus .NET API di servizio

| JMS              | Servizio Bus .NET               | Note                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message. CorrelationId          | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSDeliveryMode  | Non è attualmente disponibile        | Servizio Bus supporta solo i messaggi permanenti; ad esempio DeliveryMode.PERSISTENT, indipendentemente da quanto specificato.                                                                                                                                                                                                                                                                                                                                                         |
| JMSDestination   | Message.To                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSExpiration    | Messaggio. TimeToLive            | Conversione                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSMessageID     | Message.MessageID              | Per impostazione predefinita, JMSMessageID viene codificata in formato binario nel messaggio AMQP. Ricevuta id messaggio binario, la libreria client .NET viene convertita in una rappresentazione in base ai valori unicode dei byte. Per passare raccolta JMS per utilizzare gli ID messaggio stringa, aggiungere il "ID messaggio binario = false" stringa ai parametri di query di ConnectionURL JNDI. Ad esempio: “amqps://[username]:[password]@[namespace].servicebus.windows.net? ID messaggio binario = false ". |
| JMSPriority      | Non è attualmente disponibile        | Servizio Bus non supporta la priorità dei messaggi.                                                                                                                                                                                                                                                                                                                                                                                                                             |
| JMSRedelivered   | Non è attualmente disponibile        | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSReplyTo       | Messaggio. Rispondi               | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| JMSTimestamp     | Message.EnqueuedTimeUtc        | Conversione                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSType          | Message.Properties ["jms-tipo"] | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

#### <a name="service-bus-net-apis-to-jms"></a>Servizio Bus API .NET a JMS

| Servizio Bus .NET        | JMS              | Note                   |
|-------------------------|------------------|-------------------------|
| ContentType             | -                  | Non è attualmente disponibile |
| CorrelationId           | JMSCorrelationID | -                         |
| EnqueuedTimeUtc         | JMSTimestamp     | Conversione              |
| Etichetta                   | n/d              | Non è attualmente disponibile |
| ID messaggio               | JMSMessageID     | -                         |
| Rispondi                 | JMSReplyTo       | -                         |
| ReplyToSessionId        | n/d              | Non è attualmente disponibile |
| ScheduledEnqueueTimeUtc | n/d              | Non è attualmente disponibile |
| ID sessione               | n/d              | Non è attualmente disponibile |
| TimeToLive              | JMSExpiration    | Conversione              |
| A                      | JMSDestination   | -                         |

## <a name="unsupported-features-and-restrictions"></a>Restrizioni e le caratteristiche non supportate

Limitazioni rispetto all'utilizzo JMS 1.0 AMQP con Bus di servizio:

-   Sola **MessageProducer** o **MessageConsumer** è consentito per ogni sessione. Se si desidera creare più oggetti **MessageProducer** o **MessageConsumer** in un'applicazione, creare sessioni dedicate per ognuno di essi.

-   Le sottoscrizioni argomento volatili non sono attualmente supportate.

-   Oggetti **MessageSelector** non sono supportati.

-   Destinazioni temporanee; ad esempio, **TemporaryQueue** o **TemporaryTopic**, non sono supportati, oltre al **QueueRequestor** e **TopicRequestor** API che ne fanno uso.

-   Sessioni transazione non sono supportate.

-   Transazioni distribuite non sono supportate.

## <a name="next-steps"></a>Passaggi successivi

Pronti per altre informazioni? Visitare i collegamenti seguenti:

- [Panoramica di servizio Bus AMQP]
- [AMQP in Bus di servizio per Windows Server]

[AMQP in Bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Panoramica di servizio Bus AMQP]: service-bus-amqp-overview.md
[Portale di Azure]: https://portal.azure.com
