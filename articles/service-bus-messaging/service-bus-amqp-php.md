<properties 
    pageTitle="Servizio Bus e PHP con AMQP 1.0 | Microsoft Azure"
    description="Utilizzo del servizio Bus da PHP con AMQP."
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>Utilizzo del servizio Bus da PHP con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton PHP è un'associazione lingua PHP a Proton-C; vale a dire Proton PHP è implementata come spaziale intorno a un motore implementato in C.

## <a name="downloading-the-proton-client-library"></a>Scaricare la raccolta di client Proton

È possibile scaricare Proton C e le relative associazioni associate (inclusi PHP) da [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Il download è in forma di codice sorgente. Per generare il codice, seguire le istruzioni contenute nel pacchetto scaricato.

> [AZURE.IMPORTANT] Al momento della loro supporto SSL Proton c è disponibile solo per sistemi operativi Linux. Poiché Bus di servizio Azure richiede l'utilizzo di SSL, Proton-C (e le associazioni language) possono solo utilizzate per accedere Bus di servizio da Linux al momento. Lavoro per abilitare Proton-C con SSL su Windows è in corso, controllate spesso per gli aggiornamenti.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Utilizzo di code, argomenti e le sottoscrizioni da PHP Bus di servizio

Il codice seguente viene illustrato come inviare e ricevere messaggi da un servizio Bus messaggistica entità.

### <a name="sending-messages-using-proton-php"></a>Invio di messaggi mediante Proton PHP

Il codice seguente viene illustrato come inviare un messaggio a un Bus di servizio di messaggistica entità.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Ricezione di messaggi mediante Proton PHP

Il codice seguente viene illustrato come ricevere un messaggio da un servizio Bus messaggistica entità.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Messaggistica tra .NET e Proton PHP

### <a name="application-properties"></a>Proprietà delle applicazioni

#### <a name="protonphp-to-service-bus-net-apis"></a>ProtonPHP a Bus .NET API di servizio

I messaggi proton PHP supportano le proprietà di applicazione dei seguenti tipi: **integer**, **double**, **booleano**, **stringa**e **oggetto**. Il codice PHP seguente mostra come impostare le proprietà relative a un messaggio usando dei diversi tipi di proprietà.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Nelle API di .NET Bus del servizio, vengono applicate le proprietà delle applicazioni di messaggio dell'insieme di **proprietà** di [BrokeredMessage][]. Il codice seguente viene illustrato come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

Nella tabella seguente mappa i tipi di proprietà PHP ai tipi di proprietà .NET.

| Tipo di proprietà PHP | Tipo di proprietà .NET |
|-------------------|--------------------|
| numero intero           | int                |
| doppia            | doppia             |
| valore booleano           | bool               |
| stringa            | stringa             |
| oggetto            | Oggetto             |

#### <a name="service-bus-net-apis-to-php"></a>Servizio Bus API .NET a PHP

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

Il codice PHP seguente viene illustrato come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client .NET Bus di servizio.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

Nella tabella seguente mappa i tipi di proprietà .NET ai tipi di proprietà PHP.

| Tipo di proprietà .NET | Tipo di proprietà PHP | Note                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | numero intero           | -                                                                                                                                                                     |
| SByte              | numero intero           | -                                                                                                                                                                     |
| caratt               | Caratt              | Classe proton PHP                                                                                                                                                    |
| breve              | numero intero           | -                                                                                                                                                                     |
| ushort             | numero intero           | -                                                                                                                                                                     |
| int                | numero intero           | -                                                                                                                                                                     |
| uint               | Numero intero           | -                                                                                                                                                                     |
| lungo               | numero intero           | -                                                                                                                                                                     |
| ulong              | numero intero           | -                                                                                                                                                                     |
| margine di flessibilità              | doppia            | -                                                                                                                                                                     |
| doppia             | doppia            | -                                                                                                                                                                     |
| decimale            | stringa            | Decimale non è attualmente supportato con Proton.                                                                                                                     |
| bool               | valore booleano           | -                                                                                                                                                                     |
| GUID               | UUID              | Classe proton PHP                                                                                                                                                    |
| stringa             | stringa            | -                                                                                                                                                                     |
| DateTime           | numero intero           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | Mappata tipo AMQP DateTimeOffset.UtcTicks:<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan           | DescribedType     | Mappata tipo AMQP Timespan.Ticks:<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| URI                | DescribedType     | Mappata tipo AMQP Uri.AbsoluteUri:<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Proprietà standard

Le tabelle seguenti illustrano il mapping tra le proprietà di messaggio standard Proton PHP e le proprietà di messaggio standard [BrokeredMessage][] .

| Proton PHP           | Servizio Bus .NET         | Note                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Permanente              | n/d                      | Servizio Bus supporta solo i messaggi permanenti.          |
| Priorità             | n/d                      | Servizio Bus supporta solo una priorità più singolo messaggio. |
| TTL                  | Message.TimeToLive       | Conversione, PHP Proton TTL è definita in millisecondi.   |
| prima\_acquirente      | -                          | -                                                          |
| recapito\_conteggio      | -                          | -                                                          |
| ID                   | Message.Id               | -                                                          |
| utente\_id             | -                          | -                                                          |
| Indirizzo              | Message.To               | -                                                          |
| Oggetto              | Message.Label            | -                                                          |
| risposta\_a            | Message.ReplyTo          | -                                                          |
| correlazione\_id      | Message. CorrelationId    | -                                                          |
| contenuto\_tipo        | Message.ContentType      | -                                                          |
| contenuto\_codifica    | n/d                      | -                                                          |
| scadenza\_ora         | Message.ExpiresAtUTC     | -                                                          |
| creazione\_ora       | n/d                      | -                                                          |
| gruppo\_id            | Message.SessionId        | -                                                          |
| gruppo\_sequenza      | -                          | -                                                          |
| risposta\_a\_gruppo\_id | Message.ReplyToSessionId | -                                                          |
| Formato               | n/d                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>Servizio Bus API .NET a PHP Proton

| Servizio Bus .NET        | Proton PHP                                             | Note                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Messaggio -\>contenuto\_tipo                                | -                                                        |
| CorrelationId           | Messaggio -\>correlazione\_id                              | -                                                        |
| EnqueuedTimeUtc         | Messaggio -\>annotazioni [x-acconsentire-Accodamento-time]             | -                                                        |
| Etichetta                   | Messaggio -\>oggetto                                      | -                                                        |
| ID messaggio               | Messaggio -\>id                                           | -                                                        |
| Rispondi                 | Messaggio -\>risposta\_a                                    | -                                                        |
| ReplyToSessionId        | Messaggio -\>risposta\_a\_gruppo\_id                         | -                                                        |
| ScheduledEnqueueTimeUtc | Messaggio -\>annotazioni ["x-acconsentire-pianificato-accodare-ora"] | -                                                        |
| ID sessione               | Messaggio -\>gruppo\_id                                    | -                                                        |
| TimeToLive              | Messaggio -\>ttl                                          | Conversione, PHP Proton TTL è definita in millisecondi. |
| A                      | Messaggio -\>indirizzo                                      | -                                                        |

## <a name="next-steps"></a>Passaggi successivi

Pronti per altre informazioni? Visitare i collegamenti seguenti:

- [Panoramica di servizio Bus AMQP]
- [AMQP in Bus di servizio per Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP in Bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Panoramica di servizio Bus AMQP]: service-bus-amqp-overview.md
