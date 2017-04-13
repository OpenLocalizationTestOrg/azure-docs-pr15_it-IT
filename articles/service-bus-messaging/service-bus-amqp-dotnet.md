<properties 
    pageTitle="Servizio Bus con .NET e AMQP 1.0 | Microsoft Azure"
    description="Utilizzo del servizio Bus da .NET con AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>Utilizzo del servizio Bus da .NET con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Scaricare il servizio Bus SDK

Supporto AMQP 1.0 è disponibile in Service Bus SDK 2.1 o versione successiva. È possibile assicurarsi che si dispone della versione più recente scaricando bit Bus di servizio da [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurazione delle applicazioni .NET per usare AMQP 1.0

Per impostazione predefinita, la raccolta di client servizio Bus .NET comunica con il servizio Bus di servizio utilizza un protocollo basato su SOAP dedicato. Per utilizzare 1.0 AMQP anziché il valore predefinito protocollo richiede configurazione esplicita nella stringa di connessione Bus di servizio, come descritto nella sezione successiva. Diverso da questa modifica, il codice dell'applicazione rimarrà invariato quando si utilizza AMQP 1.0.

Nella versione corrente, esistono alcune caratteristiche di API che non sono supportate quando si utilizza AMQP. Queste caratteristiche non supportate sono elencate in un secondo momento nella sezione [caratteristiche non supportate, restrizioni e differenze di comportamento](#unsupported-features-restrictions-and-behavioral-differences). Alcune delle impostazioni di configurazione avanzate anche hanno un significato diverso quando si utilizza AMQP.

### <a name="configuration-using-appconfig"></a>Configurazione utilizzando app

È buona norma per utilizzare il file di configurazione App per archiviare le impostazioni delle applicazioni. Per le applicazioni di servizio Bus, è possibile utilizzare App per memorizzare le impostazioni per il valore di servizio Bus **ConnectionString** . Un file di esempio App è come indicato di seguito:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

Il valore di `Microsoft.ServiceBus.ConnectionString` è la stringa di connessione Bus di servizio utilizzato per configurare la connessione al servizio Bus. Il formato è come indicato di seguito:

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Dove `[namespace]` e `SharedAccessKey` ottenuto dal [portale di Azure][]. Per ulteriori informazioni, vedere [Introduzione a code Bus di servizio][].

Quando si utilizza AMQP, aggiungere la stringa di connessione con `;TransportType=Amqp`. Questa notazione indica la libreria di client per verificare la connessione al servizio Bus utilizzando AMQP 1.0.

## <a name="message-serialization"></a>Serializzazione dei messaggi

Quando si usa il protocollo predefinito, il comportamento di serializzazione predefinito della libreria client .NET consiste nell'utilizzare il tipo di [DataContractSerializer][] serializzare un'istanza di [BrokeredMessage][] per trasporto tra la raccolta di client e il servizio Bus di servizio. Quando si usa la modalità di trasporto AMQP, la libreria client utilizza il sistema di tipo AMQP per la serializzazione del [messaggio gestito][BrokeredMessage] in un messaggio AMQP. Serializzazione consente il messaggio ricevuto e interpretati da un'applicazione di destinazione potenzialmente eseguito su una piattaforma diversi, ad esempio, un'applicazione Java che utilizza l'API JMS per accedere a Bus di servizio.

Quando si crea un'istanza di [BrokeredMessage][] , è possibile fornire un oggetto .NET come parametro al costruttore come corpo del messaggio. Per gli oggetti che possono essere mappati a tipi di base AMQP, il corpo viene serializzato in tipi di dati AMQP. Se l'oggetto non possa essere mappato direttamente in un tipo di base AMQP; ovvero un tipo personalizzato definito dall'applicazione, quindi l'oggetto è serializzato utilizzando [DataContractSerializer][]e byte serializzati vengono inviate in un messaggio di dati AMQP.

Per facilitare l'interoperabilità con client non .NET, utilizzare solo i tipi di .NET che possono essere serializzati direttamente in tipi AMQP per il corpo del messaggio. Nella seguente tabella vengono tali tipi e il mapping corrispondente al sistema di tipo AMQP.

| Tipo di oggetto corpo .NET          | Tipo di AMQP mappate.                          | Tipo di sezione corpo AMQP                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool                           | valore booleano                                   | Valore AMQP                                                                                                                                                |
| byte                           | ubyte                                     | Valore AMQP                                                                                                                                                |
| ushort                         | ushort                                    | Valore AMQP                                                                                                                                                |
| uint                           | uint                                      | Valore AMQP                                                                                                                                                |
| ulong                          | ulong                                     | Valore AMQP                                                                                                                                                |
| SByte                          | byte                                      | Valore AMQP                                                                                                                                                |
| breve                          | breve                                     | Valore AMQP                                                                                                                                                |
| int                            | int                                       | Valore AMQP                                                                                                                                                |
| lungo                           | lungo                                      | Valore AMQP                                                                                                                                                |
| margine di flessibilità                          | margine di flessibilità                                     | Valore AMQP                                                                                                                                                |
| doppia                         | doppia                                    | Valore AMQP                                                                                                                                                |
| decimale                        | decimal128                                | Valore AMQP                                                                                                                                                |
| caratt                           | caratt                                      | Valore AMQP                                                                                                                                                |
| DateTime                       | timestamp                                 | Valore AMQP                                                                                                                                                |
| GUID                           | UUID                                      | Valore AMQP                                                                                                                                                |
| byte                         | binario                                    | Valore AMQP                                                                                                                                                |
| stringa                         | stringa                                    | Valore AMQP                                                                                                                                                |
| System.Collections.IList       | elenco                                      | Valore AMQP: gli elementi contenuti nella raccolta possono contenere solo quelli definiti in questa tabella.                                                             |
| Array                   | in forma di matrice                                     | Valore AMQP: gli elementi contenuti nella raccolta possono contenere solo quelli definiti in questa tabella.                                                             |
| System.Collections.IDictionary | mappa                                       | Valore AMQP: gli elementi contenuti nella raccolta possono contenere solo quelli definiti in questa tabella. Nota: sono supportate solo le chiavi di stringa.                        |
| URI                            | Stringa descritto (vedere la tabella seguente) | Valore AMQP                                                                                                                                                |
| DateTimeOffset                 | Tempo descritto (vedere la tabella seguente)   | Valore AMQP                                                                                                                                                |
| TimeSpan                       | Tempo descritto (vedere il seguente)         | Valore AMQP                                                                                                                                                |
| Flusso                         | binario                                    | Dati AMQP (potrebbe essere più). Le sezioni di dati contengono byte non elaborati leggere dall'oggetto flusso.                                                           |
| Altro oggetto                   | binario                                    | Dati AMQP (potrebbe essere più). Contiene il file binario serializzato dell'oggetto che utilizza la DataContractSerializer o un serializzatore fornito dall'applicazione. |

| Tipo .NET      | AMQP mappate descritto tipo                                                                                              | Note                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Caratteristiche non supportate, restrizioni e differenze di comportamento

Le caratteristiche seguenti dell'API del servizio Bus .NET non sono attualmente supportate quando si utilizza AMQP:

-   Transazioni

-   Inviare tramite destinazione trasferimento

Vi sono differenze piccole nel comportamento dell'API del servizio Bus .NET anche quando si utilizza AMQP, confrontata il protocollo predefinito:

-   La proprietà [OperationTimeout][] verrà ignorata.

-   `MessageReceiver.Receive(TimeSpan.Zero)`implementato come `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   Completare i messaggi da token di blocco può essere eseguita solo per i destinatari di messaggi ricevuti inizialmente i messaggi.

## <a name="controlling-amqp-protocol-settings"></a>Controllare le impostazioni del protocollo AMQP

API .NET esporre diverse impostazioni per controllare il comportamento del protocollo AMQP:

-   **MessageReceiver.PrefetchCount**: consente di controllare la carta di credito iniziale applicato a un collegamento. Il valore predefinito è uguale a 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: apertura di controlli la dimensione massima di frame AMQP proposto stato connessione. Il valore predefinito è 65.536 byte.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: se trasferimenti batchable, questo valore determina il ritardo massimo per l'invio di disposizioni. Per impostazione predefinita, ereditate da mittenti/destinatari. Mittente/destinatario singoli possibile ignorare l'impostazione predefinita, ovvero 20 millisecondi.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controlla connessioni se AMQP su una connessione SSL. Il valore predefinito è **true**.

## <a name="next-steps"></a>Passaggi successivi

Pronti per altre informazioni? Visitare i collegamenti seguenti:

- [Panoramica di servizio Bus AMQP]
- [Supporto di AMQP 1.0 per servizio Bus suddiviso code e argomenti]
- [AMQP in Bus di servizio per Windows Server]

  [Guida introduttiva a code Bus di servizio]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portale di Azure]: https://portal.azure.com
[Panoramica di servizio Bus AMQP]: service-bus-amqp-overview.md
[Supporto di AMQP 1.0 per servizio Bus suddiviso code e argomenti]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP in Bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
