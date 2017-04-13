<properties 
    pageTitle="Come utilizzare 1.0 AMQP con l'API di Bus del servizio di linguaggio | Microsoft Azure" 
    description="Come utilizzare Java Message Service (JMS) con Bus di servizio Azure e avanzate messaggio Accodamento Protodol (AMQP) 1.0." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Come usare Java Message Service (JMS) API con Bus di servizio e AMQP 1.0

L'avanzate messaggio Accodamento Protocol (AMQP) 1.0 è un protocollo messaggistica efficiente, affidabile e a livello di rete che è possibile utilizzare la creazione di applicazioni di messaggistica affidabile e multipiattaforma.

Supporto per 1.0 AMQP nel servizio Bus indica che è possibile utilizzare l'accodamento e pubblicazione/sottoscrizione funzionalità di messaggistica gestita da un intervallo di piattaforme con un utilizzo efficiente protocollo binario. Inoltre, è possibile creare applicazioni comprende i componenti creati utilizzando una combinazione di sistemi operativi, Framework e lingue.

In questo articolo viene illustrato come utilizzare Bus di servizio di messaggistica caratteristiche (code e gli argomenti della pubblicazione/sottoscrizione) dalle applicazioni di linguaggio mediante popolari Java Message Service (JMS) API standard. Esiste un [altro articolo](service-bus-dotnet-advanced-message-queuing.md) che spiega come eseguire la stessa operazione utilizzando l'API di .NET Bus del servizio. È possibile utilizzare queste due guide insieme per maggiori informazioni sulla messaggistica multipiattaforma utilizzando AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Guida introduttiva a Bus di servizio

Questa guida presuppone che si dispone già di un servizio Bus dello spazio dei nomi contenente una coda denominata **queue1**. In caso contrario, è possibile [creare i nomi e coda](service-bus-create-namespace-portal.md) tramite il [portale di Azure](https://portal.azure.com). Per ulteriori informazioni su come creare spazi dei nomi Bus di servizio e code, vedere [come utilizzare code Bus di servizio](service-bus-dotnet-get-started-with-queues.md).

> [AZURE.NOTE] Gli argomenti e code partizionate supportano anche AMQP. Per ulteriori informazioni, vedere [partizionati messaggistica entità](service-bus-partitioning.md) e [il supporto di AMQP 1.0 per Bus di servizio suddiviso code e argomenti](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## <a name="downloading-the-amqp-10-jms-client-library"></a>Scaricare la raccolta di client AMQP 1.0 JMS

Per informazioni su come scaricare la versione più recente della libreria client Apache Qpid JMS AMQP 1.0, visitare [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

È necessario aggiungere i seguenti file VASO quattro dall'archivio di distribuzione Apache Qpid JMS AMQP 1.0 percorso di classe Java durante la creazione e l'esecuzione di applicazioni JMS con Bus di servizio:

- jms geronimo\_1.1\_1.0.jar specifiche
- qpid-amqp-1-0-client-[Version].jar
- qpid-amqp-1-0-client-JMS-[Version].jar
- qpid-amqp-1-0-Common-[Version].jar

## <a name="coding-java-applications"></a>Codifica applicazioni Java

### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface (JNDI)

JMS utilizza il linguaggio denominazione e Directory Interface (JNDI) per creare una separazione tra i nomi logici e i nomi fisici. Due tipi di oggetti JMS vengono risolti utilizzando JNDI: ConnectionFactory e destinazione. JNDI utilizza un modello di provider in cui è possibile collegare diversi servizi directory per gestire i compiti di risoluzione dei nomi. 1.0 AMQP JMS di Qpid Apache, raccolta viene fornita con un semplice proprietà formato Provider JNDI basata su file che viene configurato tramite un file delle proprietà delle operazioni seguenti:

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurare la ConnectionFactory

La voce utilizzata per definire una **ConnectionFactory** nel Provider Qpid proprietà File JNDI è del formato seguente:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

In **[jndi_name]** e **[ConnectionURL]** significato è il seguente:

- **[jndi_name]**: nome logico della ConnectionFactory. Questo è il nome che verrà risolto nell'applicazione di linguaggio utilizzando il metodo IntialContext.lookup() JNDI.
- **[ConnectionURL]**: A URL che fornisce la raccolta JMS con le informazioni necessarie per il gestore AMQP.

Il formato di **ConnectionURL** è il seguente:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
In **[spazio dei nomi]**, **[SASPolicyName]** e **[SASPolicyKey]** significato è il seguente:

- **[spazio dei nomi]**: il servizio Bus dello spazio dei nomi.
- **[SASPolicyName]**: nome del criterio di coda condiviso Access della firma.
- **[SASPolicyKey]**: chiave dei criteri di coda condiviso Access della firma.

> [AZURE.NOTE] È necessario codificare URL la password manualmente. La codifica URL utilità è disponibile in [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Configurare le destinazioni

La voce utilizzata per definire una destinazione nel Provider Qpid proprietà File JNDI è del formato seguente:

```
queue.[jndi_name] = [physical_name]
```

o

```
topic.[jndi_name] = [physical_name]
```

Dove **[jndi\_nome]** e **[fisica\_nome]** significato è il seguente:

- **[jndi_name]**: il nome logico di destinazione. Questo è il nome che verrà risolto nell'applicazione di linguaggio utilizzando il metodo IntialContext.lookup() JNDI.
- **[physical_name]**: il nome dell'entità Bus di servizio in cui l'applicazione invia o riceve messaggi.

> [AZURE.NOTE] Quando si riceve una sottoscrizione di argomento Bus di servizio, il nome fisico specificato in JNDI dovrebbe essere il nome dell'argomento. Il nome dell'abbonamento è disponibile quando si crea la sottoscrizione permanente nel codice dell'applicazione JMS. [Servizio Bus AMQP 1.0 Guida per gli sviluppatori](service-bus-amqp-dotnet.md) sono presenti maggiori dettagli sull'utilizzo delle sottoscrizioni argomento Bus di servizio da JMS.

### <a name="write-the-jms-application"></a>Scrivere l'applicazione JMS

Non esistono API o le opzioni necessarie per l'utilizzo JMS con Bus di servizio speciale. Tuttavia, esistono alcune limitazioni che verranno trattati in un secondo momento. Come per qualsiasi applicazione JMS prima cosa necessari configurazione dell'ambiente JNDI, in grado di risolvere un **ConnectionFactory** e destinazioni.

#### <a name="configure-the-jndi-initialcontext"></a>Configurare InitialContext JNDI

È configurato l'ambiente JNDI passando hashtable delle informazioni di configurazione nel costruttore della classe javax.naming.InitialContext. I due necessari per gli elementi in hashtable sono il nome della classe di Factory contesto iniziale e l'URL di Provider. Il codice seguente mostra come configurare l'ambiente JNDI per utilizzare il file di proprietà Qpid in base a Provider JNDI con un file di proprietà denominato **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Una semplice applicazione JMS utilizzando una coda Bus di servizio

Il programma di esempio seguente invia JMS TextMessages a una coda di servizio Bus con il nome logico JNDI di coda e riceve i messaggi di nuovo.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione genera l'output del modulo:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Multipiattaforma e su Messaggistica tra JMS e .NET

Questa guida viene illustrato come inviare e ricevere messaggi da e verso Bus di servizio utilizzando JMS. Uno dei vantaggi principali di 1.0 AMQP invece che consente alle applicazioni di essere compilato da componenti scritti in lingue diverse, con i messaggi scambiati in modo affidabile e fedeltà completa.

Usare l'applicazione di esempio JMS descritte in precedenza e un'applicazione .NET simile acquisito da una guida correlata, [come usare 1.0 AMQP con l'API di .NET .NET Bus del servizio](service-bus-dotnet-advanced-message-queuing.md), è possibile scambiare messaggi tra .NET e Java. 

Per ulteriori informazioni dei dettagli relativi a multipiattaforma messaggistica utilizzando Bus di servizio e AMQP 1.0, vedere la [Guida per sviluppatori di servizio Bus AMQP 1.0](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS a .NET

Per illustrare JMS alla messaggistica .NET:

* Avviare l'applicazione di esempio .NET senza argomenti della riga di comando.
* Avviare l'applicazione di esempio Java con l'argomento della riga di comando "sendonly". In questa modalità, l'applicazione non si riceverà messaggi dalla coda, verranno inviati solo.
* Premere **INVIO** più volte in linguaggio applicazione console che impedirà l'invio di messaggi.
* I messaggi vengono ricevuti dall'applicazione .NET.

#### <a name="output-from-jms-application"></a>Uscita dall'applicazione JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Output applicazione .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET a JMS

Per illustrare .NET alla messaggistica JMS:

* Avviare l'applicazione di esempio .NET con l'argomento della riga di comando "sendonly". In questa modalità, l'applicazione non si riceverà messaggi dalla coda, verranno inviati solo.
* Avviare l'applicazione di esempio Java senza argomenti della riga di comando.
* Premere **INVIO** più volte nella console di applicazione .NET, che impedirà l'invio di messaggi.
* I messaggi vengono ricevuti da applicazione Java.

#### <a name="output-from-net-application"></a>Output applicazione .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Uscita dall'applicazione JMS

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restrizioni e le caratteristiche non supportate

Limitazioni rispetto all'utilizzo JMS 1.0 AMQP con Bus di servizio, vale a dire:

* Sola **MessageProducer** o **MessageConsumer** è consentito per ogni **sessione**. Se è necessario creare più **MessageProducers** o **MessageConsumers** in un'applicazione, creare una **sessione** di dedicato per ognuno di essi.
* Le sottoscrizioni argomento volatili non sono attualmente supportate.
* **MessageSelectors** non sono attualmente supportati.
* Temporanee destinazioni, ad esempio, **TemporaryQueue**, **TemporaryTopic** non sono attualmente supportate, oltre al **QueueRequestor** e **TopicRequestor** API che ne fanno uso.
* Sessioni transazione e transazioni distribuite non sono supportate.

## <a name="summary"></a>Riepilogo

Questa Guida procedure viene illustrato come utilizzare Bus di servizio negoziate funzionalità di messaggistica (code e gli argomenti della pubblicazione/sottoscrizione) da Java utilizzando le impostazioni generali JMS API AMQP 1.0.

È anche possibile usare servizio Bus AMQP 1.0 da altre lingue, tra cui .NET, C, Python e PHP. Componenti costruiti queste lingue diverse possano scambiare messaggi in modo affidabile e in tutta utilizzando 1.0 AMQP supporto nei Bus di servizio. Per ulteriori informazioni, vedere la [Guida per sviluppatori di servizio Bus AMQP 1.0](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi

* [Supporto di AMQP 1.0 in Bus di servizio Azure](service-bus-amqp-overview.md)
* [Come utilizzare 1.0 AMQP con l'API di .NET Bus del servizio](service-bus-dotnet-advanced-message-queuing.md)
* [Servizio Bus AMQP 1.0 Guida per gli sviluppatori](service-bus-amqp-dotnet.md)
* [Come usare code Bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Centro per sviluppatori di linguaggio](/develop/java/).



 
