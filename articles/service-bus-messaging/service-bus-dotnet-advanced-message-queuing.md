<properties 
    pageTitle="Come utilizzare 1.0 AMQP con l'API di Bus del servizio .NET | Microsoft Azure" 
    description="Informazioni su come usare Advanced messaggio Accodamento Protodol (AMQP) 1.0 con l'API di Bus Azure .NET del servizio." 
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
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Come utilizzare 1.0 AMQP con l'API di .NET Bus del servizio

Le avanzate messaggio Accodamento Protocol (AMQP) 1.0 è un protocollo messaggistica efficiente, affidabile e a livello di rete che è possibile utilizzare per creare efficaci e multipiattaforma e applicazioni di messaggistica.

Supporto per 1.0 AMQP nel servizio Bus indica che è possibile utilizzare l'accodamento e pubblicazione/sottoscrizione funzionalità di messaggistica gestita da un intervallo di piattaforme con un utilizzo efficiente protocollo binario. Inoltre, è possibile creare applicazioni comprende i componenti creati utilizzando una combinazione di sistemi operativi, Framework e lingue.

In questo articolo viene illustrato come utilizzare il servizio Bus negoziate funzionalità di messaggistica (code e gli argomenti della pubblicazione/sottoscrizione) dalle applicazioni di .NET tramite l'API di .NET Bus del servizio. Esiste un [altro articolo](service-bus-java-how-to-use-jms-api-amqp.md) che spiega come eseguire la stessa utilizzando l'API Java Message Service (JMS) standard. È possibile utilizzare queste due guide insieme per maggiori informazioni sulla messaggistica multipiattaforma utilizzando AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Guida introduttiva a Bus di servizio

In questo articolo si presuppone che sia già uno spazio dei nomi Bus di servizio che contiene una coda denominata "queue1". In caso contrario, è possibile creare spazio dei nomi di coda tramite il [portale di Azure][]. Per ulteriori informazioni su come creare spazi dei nomi Bus di servizio e code, vedere [Introduzione a code Bus di servizio](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Scaricare il servizio Bus SDK

Supporto AMQP 1.0 è disponibile in Service Bus SDK 2.1 o versione successiva. È possibile scaricare i bit più recente da NuGet [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Applicazioni di .NET codice

Per impostazione predefinita, la raccolta di client servizio Bus .NET comunica con il servizio Bus di servizio utilizza un protocollo basato su SOAP dedicato. Per utilizzare 1.0 AMQP anziché il valore predefinito protocollo richiede configurazione esplicita nella stringa di connessione Bus di servizio come descritto nella sezione successiva. Diverso da questa modifica, il codice dell'applicazione rimane sostanzialmente invariato quando si utilizza AMQP 1.0.

Nella versione corrente, esistono alcune caratteristiche di API che non sono supportate quando si utilizza AMQP. Queste caratteristiche non supportate sono elencate in un secondo momento nell'area [restrizioni e le caratteristiche non supportate](#unsupported-features-and-restrictions). Alcune delle impostazioni di configurazione avanzate anche hanno un significato diverso quando si utilizza AMQP. Nessuna di queste impostazioni vengono utilizzata in questo articolo, ma sono disponibili in [Panoramica servizio Bus AMQP](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)ulteriori dettagli.

### <a name="configure-via-appconfig"></a>Configurare tramite app

È consigliabile che le applicazioni di utilizzano il file di configurazione App per archiviare le impostazioni. Per le applicazioni di servizio Bus, è possibile utilizzare App per archiviare il servizio Bus **ConnectionString**. Questa applicazione di esempio utilizza anche App per archiviare il nome del servizio Bus messaggistica entità che utilizza.

File di App di esempio è illustrato di seguito:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurare la stringa di connessione Bus di servizio

Il valore dell'impostazione **Microsoft.ServiceBus.ConnectionString** è la stringa di connessione Bus di servizio utilizzato per configurare la connessione al servizio Bus. Il formato è come indicato di seguito:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Dove `[namespace]` e `[SAS key]` ottenuto dal [portale di Azure][]. Per ulteriori informazioni, vedere [come utilizzare code Bus di servizio] [].

Quando si utilizza AMQP, la stringa di connessione viene aggiunto con `;TransportType=Amqp`, che indica la libreria di client per effettuare la connessione al servizio Bus utilizzando AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurare il nome dell'entità

Applicazione in questo esempio viene utilizzato il `EntityName` impostazione nella sezione **appSettings** del file App per configurare il nome della coda con cui l'applicazione scambia messaggi.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Una semplice applicazione .NET utilizzando una coda Bus di servizio

Nell'esempio seguente invia e riceve messaggi da e verso una coda Bus di servizio.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione genera l'output del modulo:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Multipiattaforma e su Messaggistica tra JMS e .NET

In questo argomento è stato illustrato come inviare messaggi al servizio Bus utilizzando .NET, nonché come ricevere i messaggi che utilizzano .NET. Uno dei vantaggi principali di 1.0 AMQP invece che consente alle applicazioni di essere compilato da componenti scritti in lingue diverse, con i messaggi scambiati in modo affidabile e fedeltà completa.

Utilizzando l'applicazione di esempio .NET descritte in precedenza e un'applicazione di linguaggio simile acquisito da una guida correlata, [come usare Java Message Service (JMS) API con Bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), è possibile scambiare messaggi tra .NET e Java. 

Per ulteriori informazioni relative ai dettagli della messaggistica utilizzando Bus di servizio e AMQP 1.0 per piattaforme, vedere [Panoramica di servizio Bus AMQP 1.0](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS a .NET

Per illustrare JMS alla messaggistica .NET:

* Avviare l'applicazione di esempio .NET senza argomenti della riga di comando.
* Avviare l'applicazione di esempio Java con l'argomento della riga di comando "sendonly". In questa modalità, l'applicazione non si riceverà messaggi dalla coda, verranno inviati solo.
* Premere **INVIO** più volte in linguaggio applicazione console che impedirà l'invio di messaggi.
* I messaggi vengono ricevuti dall'applicazione .NET.

### <a name="output-from-jms-application"></a>Uscita dall'applicazione JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Output applicazione .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET a JMS

Per illustrare .NET alla messaggistica JMS:

* Avviare l'applicazione di esempio .NET con l'argomento riga di comando "sendonly". In questa modalità, l'applicazione non si riceverà messaggi dalla coda, verranno inviati solo.
* Avviare l'applicazione di esempio Java senza gli argomenti della riga di comando.
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

Le caratteristiche seguenti dell'API .NET servizio Bus non sono attualmente supportate quando si utilizza AMQP:

 * Transazioni
 * Inviare tramite destinazione trasferimento

Per ulteriori informazioni, vedere [caratteristiche non supportate, restrizioni e differenze di comportamento](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come accedere alle funzionalità di messaggistica Bus di servizio negoziate (code e gli argomenti della pubblicazione/sottoscrizione) da .NET in AMQP 1.0 e le API di .NET Bus di servizio.

È anche possibile usare servizio Bus AMQP 1.0 da altre lingue tra Java, C, Python e PHP. Componenti costruiti queste lingue è possono scambiare messaggi in modo affidabile e in tutta utilizzando AMQP 1.0 in Bus di servizio. Per ulteriori informazioni, vedere la [Panoramica del servizio Bus AMQP](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che aver letto preliminari Bus di servizio e AMQP con .NET, vedere i collegamenti seguenti per altre informazioni.

* [Supporto di AMQP 1.0 in Bus di servizio Azure](service-bus-amqp-overview.md)
* [Come usare Java Message Service (JMS) API con Bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Come usare code Bus di servizio](service-bus-dotnet-get-started-with-queues.md)
 
[Portale di Azure]: https://portal.azure.com
