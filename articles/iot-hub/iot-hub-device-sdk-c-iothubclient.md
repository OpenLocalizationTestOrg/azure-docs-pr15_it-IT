<properties
    pageTitle="Azure IoT dispositivo SDK per C - IoTHubClient | Microsoft Azure"
    description="Ulteriori informazioni sull'utilizzo di raccolta IoTHubClient nel dispositivo IoT Azure SDK per C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositivo di Microsoft Azure IoT SDK per C: ulteriori informazioni, vedere IoTHubClient

L' [articolo prima](iot-hub-device-sdk-c-intro.md) di questa serie introdotto il **dispositivo di Microsoft Azure IoT SDK per C**. L'articolo spiegazione sono presenti due livelli all'architettura SDK. Alla base è la libreria di **IoTHubClient** direttamente gestisce la comunicazione con IoT Hub. È inoltre disponibile la raccolta **serializzatore** generare per di più per fornire servizi di serializzazione. In questo articolo è necessario fornire ulteriori dettagli sulla raccolta **IoTHubClient** .

Articolo precedente viene descritto come utilizzare la libreria **IoTHubClient** per inviare gli eventi a IoT Hub e ricevere messaggi. In questo articolo spiega come gestire con maggiore precisione *quando* si inviano e ricevono dati, un'introduzione alle **API di livello inferiore**estende la discussione. Verrà inoltre viene illustrato come collegare proprietà agli eventi (e recuperarli da messaggi) utilizzando la proprietà Gestione caratteristiche della raccolta **IoTHubClient** . Infine, fornite ulteriori spiegazioni diversi modi per gestire i messaggi ricevuti da IoT Hub.

L'articolo si conclude coperta un paio di vari argomenti, inclusi più su credenziali per un dispositivo e su come modificare il comportamento di **IoTHubClient** tra le opzioni di configurazione.

Esempi SDK **IoTHubClient** verrà utilizzata per spiegare questi argomenti. Se si desidera seguire su carta, vedere la **iothub\_client\_esempio\_http** e **iothub\_client\_esempio\_amqp** applicazioni sono incluse nel dispositivo IoT Azure SDK per c tutto descritto nelle sezioni seguenti viene illustrato in questi esempi.

È possibile trovare il **dispositivo di Azure IoT SDK per C** nell'archivio di [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub e visualizzare i dettagli dell'API del [riferimento all'API C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>API di livello inferiore

Articolo precedente vengono illustrate le operazioni di base di **IotHubClient** nel contesto del **iothub\_client\_esempio\_amqp** dell'applicazione. Ad esempio spiegato come inizializzare la libreria utilizzando il codice.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Inoltre descritto come inviare gli eventi mediante la chiamata di funzione.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

L'articolo anche descritte le procedure per la ricezione di messaggi tramite la registrazione di una funzione di callback.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

L'articolo mostrato anche liberare risorse tramite codice, ad esempio le operazioni seguenti.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Esistono tuttavia funzioni correlata a ognuna di queste API:

-   IoTHubClient\_TTO\_CreateFromConnectionString

-   IoTHubClient\_TTO\_SendEventAsync

-   IoTHubClient\_TTO\_SetMessageCallback

-   IoTHubClient\_TTO\_Destroy


Queste funzioni tutti includono "Llo" del nome dell'API. Diverso da quello, i parametri di ognuna di queste funzioni sono identici alle rispettive controparti non TTO. Tuttavia, il comportamento di queste funzioni è diverso per un aspetto importante.

Quando si chiama **IoTHubClient\_CreateFromConnectionString**, le relative raccolte sottostante creano un nuovo thread che viene eseguita in background. Questo thread invia gli eventi e riceve messaggi dall'IoT Hub. Quando si lavora con API "llo", non viene creato alcun tali thread. La creazione di thread in background è utile per gli sviluppatori. Non è necessario preoccuparsi in modo esplicito inviare gli eventi e ricevere messaggi dall'IoT Hub, si verifica automaticamente in background. Invece API "llo" offrono un controllo esplicito la comunicazione con IoT Hub, se è necessario.

Per comprendere meglio questo, esaminiamo un esempio:

Quando si chiama **IoTHubClient\_SendEventAsync**, che si sta effettivamente eseguendo l'inserimento dell'evento in un buffer. Thread in background creato quando si chiama **IoTHubClient\_CreateFromConnectionString** continuamente controlla questo buffer e invia tutti i dati che contiene a IoT Hub. Si verifica questo evento in background allo stesso tempo che il thread principale utilizzato per eseguire altre operazioni.

Analogamente, quando si registra una funzione di callback per i messaggi con **IoTHubClient\_SetMessageCallback**, si sta per indicare SDK per richiamare la funzione di callback quando un messaggio ricevuto, indipendente dal thread principale thread in background.

API "llo" non lo si crea un thread in background. Se, tuttavia, è necessario chiamare una nuova API per esplicitamente inviare e ricevere dati dall'IoT Hub. Come illustrato nell'esempio seguente.

Il **iothub\_client\_esempio\_http** applicazione inclusa in SDK illustra le API di livello inferiore. In tale esempio abbiamo inviare eventi IoT Hub con codice, ad esempio le operazioni seguenti:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Le prime tre righe creare il messaggio e l'ultima riga invia l'evento. Tuttavia, come indicato in precedenza, "invio" l'evento indica che i dati sono semplicemente posizionati in un buffer. Non viene trasmesso sulla rete quando si chiama **IoTHubClient\_TTO\_SendEventAsync**. Per poter effettivamente ingresso i dati da IoT Hub, è necessario chiamare **IoTHubClient\_TTO\_DoWork**, come in questo esempio:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Questo codice (dalla **iothub\_client\_esempio\_http** applicazione) chiama ripetutamente **IoTHubClient\_TTO\_DoWork**. Ogni volta che **IoTHubClient\_TTO\_DoWork** è chiamato, invia alcuni eventi dal buffer IoT Hub e recupera un messaggio in coda inviato al dispositivo. Quest'ultimo caso significa che se è registrato una funzione di callback per i messaggi, quindi la richiamata (presupponendo che tutti i messaggi vengono accodati). Si preferisce sono registrati una funzione di callback con codice, ad esempio le operazioni seguenti:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Il motivo per cui **IoTHubClient\_TTO\_DoWork** spesso denominate in un ciclo è che ogni volta che viene chiamato, invia *alcuni* buffer eventi IoT Hub e recupera *il successivo* messaggio messe per il dispositivo. Ogni chiamata non viene garantito per inviare buffer tutti gli eventi o per recuperare tutti i messaggi in coda. Se si desidera inviare tutti gli eventi nel buffer e continuare con altre attività di elaborazione è possibile sostituire il ciclo con codice, ad esempio le operazioni seguenti:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Questo codice chiama **IoTHubClient\_TTO\_DoWork** fino a tutti gli eventi di buffer sono stati inviati a IoT Hub. Nota che non implica anche che tutti i messaggi in coda sono stati ricevuti. Parte del motivo è che il controllo dei messaggi "tutti" non sia inserito come deterministica un'azione. Cosa succede se si recuperano "tutti" dei messaggi, ma quindi un altro viene inviato al dispositivo subito dopo? Un modo migliore per risolvere il problema è con un timeout programmato. Ad esempio, la funzione di callback messaggio potrebbe reimpostare un timer ogni volta che viene richiamato. È quindi possibile scrivere la logica per continuare l'elaborazione se, ad esempio, nessun messaggio ricevuto negli ultimi *X* secondi.

Quando si è finito ingressing eventi e ricezione di messaggi, assicurarsi di chiamare la funzione corrispondente per pulire le risorse.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

In pratica esiste solo un set di API per inviare e ricevere dati con un thread in background e un'altra coppia di API che effettua la stessa operazione senza thread in background. Molti sviluppatori essere preferibile l'API non TTO, ma le API di livello inferiore sono utili quando lo sviluppatore desidera espliciti controllare le trasmissioni di rete. Ad esempio, alcuni dispositivi raccolgono i dati nel tempo e gli eventi di ingresso solo a determinati intervalli (ad esempio, una volta un'ora o una volta al giorno). Le API di livello inferiore consentono di controllare in modo esplicito quando si inviano e si ricevono dati dall'IoT Hub. Gli altri utenti verranno preferisce la semplicità che forniscono le API di livello inferiore. Tutto avviene sul thread principale anziché alcune situazione di lavoro in background.

Indipendentemente dal modello scelto, assicurarsi che sia coerente nei quali API che utilizzano. Se si avvia chiamando **IoTHubClient\_TTO\_CreateFromConnectionString**, assicurarsi di utilizzare solo le API di livello inferiore corrispondenti per il completamento lavoro:

-   IoTHubClient\_TTO\_SendEventAsync

-   IoTHubClient\_TTO\_SetMessageCallback

-   IoTHubClient\_TTO\_Destroy

-   IoTHubClient\_TTO\_DoWork

L'operazione è vera anche. Se si inizia con **IoTHubClient\_CreateFromConnectionString**, quindi usare l'API non TTO ulteriori elaborazioni.

Nel dispositivo IoT Azure SDK per C, vedere la **iothub\_client\_esempio\_http** applicazione per un esempio completo delle API di livello inferiore. Il **iothub\_client\_esempio\_amqp** applicazione è possibile fare riferimento per un esempio completo dell'API non TTO.

## <a name="property-handling"></a>Gestione di proprietà

Finora quando abbiamo descritto invio di dati, sono state stato fa riferimento al corpo del messaggio. Ad esempio, considerare questo codice:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

In questo esempio invia un messaggio a IoT Hub con il testo "Hello World". Tuttavia, IoT Hub consente inoltre alle proprietà da collegare a ogni messaggio. Proprietà sono coppie nome/valore che possono essere collegate al messaggio. Ad esempio, è possibile modificare il codice precedente per allegare una proprietà per il messaggio:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Iniziamo chiamando **IoTHubMessage\_proprietà** e passando il quadratino dei messaggi. Cosa si torna indietro è un **mappa\_gestire** riferimento che consente di iniziare ad aggiungere proprietà. Effettuate eseguendo una chiamata **mappa\_AddOrUpdate**, che contiene un riferimento a una mappa\_HANDLE, il nome della proprietà e il valore della proprietà. Con questa API è possibile aggiungere il numero di proprietà che si desidera.

Quando viene letto l'evento da **Hub di evento**, il destinatario possibile enumerare le proprietà e recuperare i valori corrispondenti. Ad esempio, in .NET per cui questa operazione accedendo all' [insieme di proprietà dell'oggetto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Nell'esempio precedente sono stiamo associare proprietà a un evento inviato a IoT Hub. Proprietà possono inoltre essere collegate ai messaggi ricevuti da IoT Hub. Se si desidera recuperare proprietà da un messaggio, possiamo usare il codice, ad esempio le operazioni seguenti nella funzione callback messaggio:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

La chiamata a **IoTHubMessage\_proprietà** restituisce il **mappa\_gestire** riferimento. Passiamo quindi che fanno riferimento a **mappa\_GetInternals** per ottenere un riferimento a una matrice di coppie nome/valore (nonché un conteggio delle proprietà). A questo punto è sufficiente enumerazione delle proprietà per accedere ai valori che vogliamo.

Non è necessario utilizzare le proprietà dell'applicazione. Tuttavia, se è necessario impostarli gli eventi o recuperarli da messaggi, la raccolta di **IoTHubClient** semplifica.

## <a name="message-handling"></a>Gestione dei messaggi

Come descritto in precedenza, all'arrivo di messaggi dall'IoT Hub raccolta **IoTHubClient** risponde chiamando una funzione di callback registrato. Esiste un parametro restituito di questa funzione che merita alcune spiegazioni supplementari. Ecco una porzione della funzione callback nel **iothub\_client\_esempio\_http** applicazione di esempio:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Si noti che il tipo restituito è **IOTHUBMESSAGE\_disposizione\_risultato** e in questo caso particolare viene restituito **IOTHUBMESSAGE\_accettato**. Sono disponibili altri valori è possibile tornare provenienti da questa funzione che cambiano in modo raccolta **IoTHubClient** reagisce alle callback messaggio. Ecco le opzioni.

-   **IOTHUBMESSAGE\_accettato** – elaborato il messaggio. Raccolta **IoTHubClient** non attivano la funzione di callback nuovamente con lo stesso messaggio.

-   **IOTHUBMESSAGE\_RIFIUTATO** : il messaggio non è stato elaborato e non esiste alcun desiderata per farlo in futuro. Raccolta **IoTHubClient** opportuno non richiamare la funzione di callback nuovamente con lo stesso messaggio.

-   **IOTHUBMESSAGE\_ABBANDONATO** : il messaggio non è stato elaborato, ma la raccolta di **IoTHubClient** deve richiamare la funzione di callback nuovamente con lo stesso messaggio.

Per i primi due codici restituiti, la raccolta **IoTHubClient** invia un messaggio a IoT Hub che indica la eliminato dalla coda di dispositivo e non recapitato nuovamente il messaggio. Il risultato è lo stesso (il messaggio viene eliminato dalla coda dispositivo), ma se il messaggio è stato accettato o rifiutato è ancora registrato.  Registrazione questa distinzione è utile ai mittenti del messaggio che è possano ascoltare i commenti e suggerimenti e verificare se un dispositivo ha accettato o rifiutato un messaggio specifico.

Nell'ultimo caso un messaggio viene inviato anche a IoT Hub, ma indica che è necessario recapitato nuovamente il messaggio. In genere è necessario abbandonare un messaggio se si verifica un errore ma tenta di elaborare nuovamente il messaggio. Il rifiuto di un messaggio è invece appropriato quando si verifica un errore irreversibile (o semplicemente, si decide che non si vuole elaborare il messaggio).

In ogni caso, prestare particolare attenzione di diversi codici in modo che è possibile richiedere il comportamento desiderato dalla raccolta di **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenziali alternative

Come illustrato in precedenza, la prima operazione da eseguire quando si lavora con la raccolta di **IoTHubClient** è per ottenere un **IOTHUB\_CLIENT\_gestire** con una chiamata, ad esempio le operazioni seguenti:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Gli argomenti da **IoTHubClient\_CreateFromConnectionString** sono la stringa di connessione di un parametro che indica il protocollo serve per comunicare con IoT Hub e il dispositivo. La stringa di connessione con un formato che viene visualizzata come segue:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Esistono quattro tipi di informazioni in questa stringa: nome IoT Hub, suffisso IoT Hub, ID dispositivo e tasto condiviso. Ottenere il nome di dominio completo (FQDN) di un hub IoT quando si crea l'istanza di hub IoT nel portale di Azure, si ottiene così il nome di hub IoT (la prima parte del nome di dominio completo) e il suffisso hub IoT (il resto del FQDN). Viene visualizzato l'ID di dispositivo e il tasto di scelta condiviso quando si registra il dispositivo con IoT Hub (come descritto nell' [articolo precedente](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** offre un modo per inizializzare la libreria. Se si preferisce, è possibile creare un nuovo **IOTHUB\_CLIENT\_gestire** utilizzando i singoli parametri anziché la stringa di connessione. Questa operazione viene eseguita con il codice seguente:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Consente di eseguire la stessa operazione come **IoTHubClient\_CreateFromConnectionString**.

Può sembrare evidente che si desidera utilizzare **IoTHubClient\_CreateFromConnectionString** invece di questo metodo più dettagliato di inizializzazione. Tenere presente, tuttavia, quando si registra un dispositivo hub IoT viene visualizzato è un ID dispositivo e chiave dispositivo (non una stringa di connessione). Lo strumento di **Gestione dispositivi** SDK introdotto nell' [articolo precedente](iot-hub-device-sdk-c-intro.md) utilizza le raccolte nel **servizio di Azure IoT SDK** per creare la stringa di connessione dal dispositivo ID, chiave dispositivo e nome host IoT Hub. Pertanto la chiamata **IoTHubClient\_TTO\_crea** può essere preferibile in quanto salva il passaggio di generazione di una stringa di connessione. Utilizzare il metodo risulta utile.

## <a name="configuration-options"></a>Opzioni di configurazione

Fino a questo punto tutto descritto sul modo in cui la raccolta **IoTHubClient** funziona riflette il comportamento predefinito. Esistono tuttavia alcune opzioni che è possibile impostare per modificare il funzionamento della raccolta. A questo scopo sfruttando il **IoTHubClient\_TTO\_SetOption** API. Valutare la possibilità di questo esempio:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Esistono un paio di opzioni di uso comune:

-   **SetBatching** (booleano:) se **true**, i dati inviati al IoT Hub viene inviato in batch. Se **false**, quindi i messaggi vengono inviati singolarmente. Il valore predefinito è **false**. Si noti che l'opzione **SetBatching** si applica solo per il protocollo HTTP e non per i protocolli MQTT o AMQP.

-   **Timeout** (unsigned int) – questo valore viene rappresentato in millisecondi. Se inviare una richiesta HTTP o la ricezione di una risposta richiede più tempo questa volta, quindi il timeout della connessione.

L'opzione batch è importante. Per impostazione predefinita, gli eventi ingresses della raccolta singolarmente (un singolo evento è qualsiasi passare a **IoTHubClient\_TTO\_SendEventAsync**). Se l'opzione batch è **vero**, la raccolta raccoglie tutti gli eventi che può dal buffer (fino alla dimensione massima dei messaggi accetta IoT Hub).  Il batch di eventi vengono inviate a IoT Hub in un'unica chiamata HTTP (i singoli eventi sono inclusi in una matrice JSON). Per consentire la divisione in batch in genere restituisce come risultato miglioramento delle prestazioni grande poiché si sta riducendo andata e ritorno di rete. Viene inoltre significativamente ridotta la larghezza di banda dal momento che si intende inviare un insieme di intestazioni HTTP con un batch di eventi anziché un insieme di intestazioni per ogni singolo evento. A meno che non si dispone di un motivo specifico da eseguire in caso contrario, in genere è consigliabile attivare il batch.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo vengono illustrati in dettaglio il comportamento della raccolta **IoTHubClient** trovato nel **dispositivo IoT Azure SDK per C**. Con queste informazioni, è necessario avere una buona conoscenza delle funzionalità di raccolta **IoTHubClient** . [Articolo successivo](iot-hub-device-sdk-c-serializer.md) fornisce dettagli simili sulla raccolta **serializzatore** .

Per ulteriori informazioni sullo sviluppo per IoT Hub, vedere [IoT Hub SDK][lnk-sdks].

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
