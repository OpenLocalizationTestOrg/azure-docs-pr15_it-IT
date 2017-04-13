<properties 
    pageTitle="Servizio Bus e Python con AMQP 1.0 | Microsoft Azure"
    description="Utilizzo del servizio Bus da Python con AMQP."
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

# <a name="using-service-bus-from-python-with-amqp-10"></a>Utilizzo del servizio Bus da Python con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton Python è un'associazione lingua Python a Proton-C; vale a dire Proton Python è implementata come spaziale intorno a un motore implementato in C.

## <a name="download-the-proton-client-library"></a>Scaricare la raccolta di client Proton

È possibile scaricare Proton C e le relative associazioni associate (inclusi Python) da [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Il download è in forma di codice sorgente. Per generare il codice, seguire le istruzioni incluse nel pacchetto scaricato.

Si noti che al momento della loro supporto SSL Proton c è disponibile solo per sistemi operativi Linux. Poiché Bus di servizio Azure richiede l'utilizzo di SSL, Proton-C (e le associazioni language) possono solo utilizzate per accedere Bus di servizio da Linux al momento. Per abilitare Proton-C con SSL su Windows è un controllo in corso così spesso per gli aggiornamenti.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Utilizzo di code, argomenti e le sottoscrizioni da Python Bus di servizio

Il codice seguente viene illustrato come inviare e ricevere messaggi da un servizio Bus messaggistica entità.

### <a name="send-messages-using-proton-python"></a>Invio di messaggi tramite Proton Python

Il codice seguente viene illustrato come inviare un messaggio a un Bus di servizio di messaggistica entità.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Ricevere messaggi che utilizzano Proton Python

Il codice seguente viene illustrato come ricevere un messaggio da un servizio Bus messaggistica entità.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Messaggistica tra .NET e Proton Python

### <a name="application-properties"></a>Proprietà delle applicazioni

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton Python a Bus .NET API di servizio

I messaggi di proton Python supportano le proprietà di applicazione dei seguenti tipi: **int**, **lungo**, **margine di flessibilità**, **uuid**, **bool**, **stringa**. Il codice Python seguente mostra come impostare le proprietà relative a un messaggio usando dei diversi tipi di proprietà.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Nell'API di .NET Bus del servizio, vengono applicate le proprietà delle applicazioni di messaggio dell'insieme di **proprietà** di [BrokeredMessage][]. Il codice seguente viene illustrato come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client Python.

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

Nella tabella seguente mappa i tipi di proprietà Python ai tipi di proprietà .NET.

| Tipo di proprietà Python | Tipo di proprietà .NET |
|----------------------|--------------------|
| int                  | int                |
| margine di flessibilità                | doppia             |
| lungo                 | Int64              |
| UUID                 | GUID               |
| bool                 | bool               |
| stringa               | stringa             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Servizio Bus API .NET a Proton Python

Il tipo di [BrokeredMessage][] supporta le proprietà di applicazione dei tipi seguenti: **byte**, **sbyte**, **char**, **breve**, **ushort**, **int**, **uint**, **long**, **ulong**, **margine di flessibilità**, **doppia**, **decimal**, **bool**, **Guid**, **stringa**, **Uri**, **DateTime**, **DateTimeOffset**e **TimeSpan**. Il codice .NET seguente mostra come impostare le proprietà su un oggetto [BrokeredMessage][] utilizzando ognuno di questi tipi di proprietà.

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Il codice Python seguente viene illustrato come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client .NET Bus di servizio.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

Nella tabella seguente mappa i tipi di proprietà .NET ai tipi di proprietà Python.

| Tipo di proprietà .NET | Tipo di proprietà Python | Note                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | int                  | -                                                                                                                                                                     |
| SByte              | int                  | -                                                                                                                                                                     |
| caratt               | caratt                 | Classe proton Python                                                                                                                                                 |
| breve              | int                  | -                                                                                                                                                                     |
| ushort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| lungo               | int                  | -                                                                                                                                                                     |
| ulong              | lungo                 | Classe proton Python                                                                                                                                                 |
| margine di flessibilità              | margine di flessibilità                | -                                                                                                                                                                     |
| doppia             | margine di flessibilità                | -                                                                                                                                                                     |
| decimale            | Stringa               | Decimale non è attualmente supportato con Proton.                                                                                                                     |
| bool               | bool                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Classe proton Python                                                                                                                                                 |
| stringa             | stringa               | -                                                                                                                                                                     |
| DateTime           | timestamp            | Classe proton Python                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | Mappata tipo AMQP DateTimeOffset.UtcTicks:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Mappata tipo AMQP Timespan.Ticks:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Mappata tipo AMQP Uri.AbsoluteUri:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Proprietà standard

Le tabelle seguenti illustrano il mapping tra le proprietà di messaggio standard Proton Python e le proprietà di messaggio standard [BrokeredMessage][] .

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton Python a Bus .NET API di servizio

| Proton Python        | Servizio Bus .NET         | Note                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| permanente              | n/d                      | Servizio Bus supporta solo i messaggi permanenti.               |
| priorità             | n/d                      | Servizio Bus supporta solo una priorità più singolo messaggio.      |
| TTL                  | Message.TimeToLive       | Conversione, Proton Python TTL è definita in millisecondi. |
| prima\_acquirente      | n/d                      | -                                                           |
| recapito\_conteggio      | n/d                      | -                                                           |
| ID                   | Message.MessageID        | -                                                           |
| utente\_id             | n/d                      | -                                                           |
| indirizzo              | Message.To               | -                                                           |
| Oggetto              | Message.Label            | -                                                           |
| risposta\_a            | Message.ReplyTo          | -                                                           |
| correlazione\_id      | Message. CorrelationId    | -                                                           |
| contenuto\_tipo        | Message.ContentType      | -                                                           |
| contenuto\_codifica    | n/d                      | -                                                           |
| scadenza\_ora         | n/d                      | -                                                           |
| creazione\_ora       | n/d                      | -                                                           |
| gruppo\_id            | Message.SessionId        | -                                                           |
| gruppo\_sequenza      | n/d                      | -                                                           |
| risposta\_a\_gruppo\_id | Message.ReplyToSessionId | -                                                           |
| formato               | n/d                      | -                                                           |

| Servizio Bus .NET        | Proton                       | Note                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_tipo        | -                                                           |
| CorrelationId           | Message.Correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/d                          | -                                                           |
| Etichetta                   | Message.Subject              | -                                                           |
| ID messaggio               | Message.ID                   | -                                                           |
| Rispondi                 | Message.Reply\_a            | -                                                           |
| ReplyToSessionId        | Message.Reply\_a\_gruppo\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/d                          | -                                                           |
| ID sessione               | Message.Group\_id            | -                                                           |
| TimeToLive              | Message.TTL                  | Conversione, Proton Python TTL è definita in millisecondi. |
| A                      | Message.Address              | -                                                           |

## <a name="next-steps"></a>Passaggi successivi

Pronti per altre informazioni? Visitare i collegamenti seguenti:

- [Panoramica di servizio Bus AMQP]
- [AMQP in Bus di servizio per Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP in Bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Panoramica di servizio Bus AMQP]: service-bus-amqp-overview.md
