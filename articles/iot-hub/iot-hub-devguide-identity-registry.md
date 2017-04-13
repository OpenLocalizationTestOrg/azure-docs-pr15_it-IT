<properties
 pageTitle="Guida per sviluppatori - del Registro di sistema di dispositivo identità | Microsoft Azure"
 description="Guida per sviluppatori di IoT Hub Azure - descrizione del Registro di sistema di identità dispositivo e come utilizzarlo per gestire i dispositivi"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="manage-device-identities-in-iot-hub"></a>Gestione delle identità dispositivo IoT hub

## <a name="overview"></a>Panoramica

Ogni hub IoT con un dispositivo identità del Registro di sistema che consente di memorizzare informazioni i dispositivi sono autorizzati a connettersi all'hub. Per un dispositivo potersi connettere a un hub, devono essere presenti una voce per il dispositivo del Registro di sistema dell'hub dispositivo identità. Deve anche eseguire l'autenticazione di un dispositivo con hub in base alle credenziali memorizzate nel Registro di sistema di identità dispositivo.

Un alto livello, il Registro di sistema di identità di dispositivo è una raccolta che supporta il resto delle risorse di identità dispositivo. Quando si aggiunge una voce nel Registro di sistema, IoT Hub consente di creare un set di risorse per dispositivo nel servizio, ad esempio coda che contiene i messaggi cloud al dispositivo in corso.

### <a name="when-to-use"></a>Quando utilizzare

Usare il Registro di sistema di identità di dispositivo quando è necessario effettuare il provisioning di dispositivi che si connettono a IoT hub e quando è necessario controllare l'accesso al dispositivo per gli endpoint dispositivo esposto il tuo fulcro.

> [AZURE.NOTE] Registro di sistema di identità dispositivo non contiene i metadati specifici dell'applicazione.

## <a name="device-identity-registry-operations"></a>Operazioni del Registro di sistema di identità dispositivo

Il Registro di sistema IoT Hub identità dispositivo esposti le operazioni seguenti:

* Creare identità dispositivo
* Aggiornare l'identità di dispositivo
* Recuperare l'identità di dispositivo per ID
* Eliminare l'identità di dispositivo
* Fino a 1000 identità dell'elenco
* Esportare tutte le identità in archiviazione blob Azure
* Importare identità dallo spazio di archiviazione blob Azure

Tutte queste operazioni possono utilizzare ottimistica, come specificato nei [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] L'unico modo per recuperare tutte le identità del Registro di sistema dell'hub identità consiste nell'utilizzare la funzionalità di [esportazione] [ lnk-export] funzionalità.

Un IoT Hub dispositivo identità Registro di sistema:

- Non contiene i metadati di applicazione.
- Utilizzando **deviceId** come chiave, è possibile accedere ad esempio un dizionario.
- Non supporta query esplicite.

Una soluzione IoT è in genere un archivio specifici separato che contiene i metadati specifici dell'applicazione. Ad esempio, l'archivio di soluzione specifico in una soluzione smart predefiniti consente di registrare la chat room in cui viene distribuito un sensore di temperatura.

> [AZURE.IMPORTANT] Utilizzare solo il Registro di sistema di identità di dispositivo per la gestione di dispositivi e le operazioni di provisioning. Operazioni di alta velocità in fase di esecuzione non devono dipendere operazioni nel Registro di sistema di identità dispositivo. Ad esempio, verifica dello stato di connessione di un dispositivo prima dell'invio di un comando non è un motivo supportato. Controllare la [limitazione tariffe] [ lnk-quotas] per il Registro di sistema di identità di dispositivo e l' [heartbeat dispositivo] [ lnk-guidance-heartbeat] motivo.

## <a name="disable-devices"></a>Disattivazione dei dispositivi

È possibile disattivare dispositivi aggiornando la proprietà di **stato** di un'identità del Registro di sistema. In genere, utilizzare questa proprietà in due scenari:

- Durante un processo di orchestrazione provisioning. Per ulteriori informazioni, vedere [Provisioning dispositivo][lnk-guidance-provisioning].
- Se per qualsiasi motivo, è prendere in considerazione un dispositivo è stata compromessa ed è diventato non autorizzato.

## <a name="import-and-export-device-identities"></a>Importare ed esportare identità dispositivo

È possibile esportare le identità di dispositivo in blocco da Registro di sistema di identità dell'hub un IoT, utilizzando le operazioni asincrone sull' [endpoint provider risorsa IoT Hub][lnk-endpoints]. Esportazioni sono processi lunga che utilizzano un contenitore di blob fornite dal cliente per salvare i dati di identità dispositivo leggere il Registro di sistema di identità.

È possibile importare identità di dispositivo in blocco in Registro di sistema di identità dell'hub un IoT, utilizzando le operazioni asincrone sull' [endpoint provider risorsa IoT Hub][lnk-endpoints]. Importa è processi lunga che utilizzano i dati in un contenitore di blob fornite dal cliente per scrivere i dati di identità dispositivo nel Registro di sistema di identità dispositivo.

- Per informazioni dettagliate sull'importazione / esportazione API, vedere [provider di risorse IoT Hub API REST][lnk-resource-provider-apis].
- Per ulteriori informazioni sull'esecuzione di importare ed esportare processi, vedere [gestione di massa di identità dispositivo IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Provisioning dispositivo

I dati di dispositivo contenente una determinata soluzione IoT dipendono dai requisiti specifici della soluzione. Tuttavia, come minimo, è necessario archiviare una soluzione dispositivo identità e chiavi di autenticazione. IoT Hub Azure include un registro di sistema di identità che possono essere archiviati i valori per ogni dispositivo, ad esempio gli ID, i tasti di autenticazione e i codici di stato. Una soluzione è possibile usare altri servizi di Azure ad esempio archivio tabelle Azure, archiviazione blob Azure o Azure DocumentDB per memorizzare i dati di altri dispositivi.

*Provisioning dispositivo* è il processo di aggiunta di dati di dispositivo iniziale archivi della soluzione. Per abilitare un nuovo dispositivo per la connessione a un centro di raccolta, è necessario aggiungere un nuovo ID dispositivo e chiavi del Registro di sistema di identità IoT Hub. Come parte del processo di provisioning, potrebbe essere necessario inizializzare i dati specifici di dispositivo in altri archivi soluzione.

## <a name="device-heartbeat"></a>Heartbeat dispositivo

Registro di sistema di identità IoT Hub contiene un campo denominato **connectionState**. Utilizzare il campo **connectionState** solo in fase di sviluppo e debug IoT soluzioni devono non query il campo in fase di esecuzione (ad esempio, per verificare se un dispositivo è collegato per decidere se si desidera inviare un messaggio cloud al dispositivo o un SMS).

Se la soluzione IoT deve sapere se un dispositivo sia connesso (in fase di esecuzione, o con maggiore precisione rispetto alla proprietà **connectionState** ), la soluzione deve implementare *motivo heartbeat*.

Nel modello di heartbeat, il dispositivo invia messaggi dispositivo nel cloud almeno una volta ogni periodo di tempo (ad esempio, almeno una volta ogni ora) stabilito. Questo errore indica che anche se un dispositivo non dispone di tutti i dati da inviare, esso continua a inviare un messaggio di dispositivo nel cloud vuoto (in genere con una proprietà che identifica come heartbeat). Sul lato del servizio, la soluzione mantiene una mappa con l'ultimo heartbeat ricevuto per ogni dispositivo e si presuppone che si verifica un problema con un dispositivo se non riceve un messaggio heartbeat entro i tempi previsti.

Implementazione più complessa può includere le informazioni delle [operazioni di monitoraggio] [ lnk-devguide-opmon] per identificare i dispositivi che si tenta di connettersi o comunicare ma non funziona. Quando si implementare il modello di heartbeat, assicurarsi di controllare [le quote di Hub IoT e limita][lnk-quotas].

> [AZURE.NOTE] Se una soluzione IoT richiede lo stato della connessione dispositivo esclusivamente per determinare se inviare messaggi cloud al dispositivo e i messaggi non vengono estesi a insiemi estesi di dispositivi, un motivo molto più semplice da considerare consiste nell'usare una breve ora di scadenza. Consente di ottenere lo stesso risultato di gestione di un dispositivo connessione lo stato del Registro di sistema utilizzando il modello di heartbeat, pur essendo molto più efficienti. È anche possibile, richiedendo riconoscimenti messaggio, la notifica tramite IoT Hub di quali dispositivi sono in grado di ricevere messaggi e che non sono in linea o non è riuscite.

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni sul Registro di sistema di identità devcie.

## <a name="device-identity-properties"></a>Proprietà dei dispositivi identità

Identità dispositivo vengono rappresentate come documenti JSON con le proprietà seguenti.

| Proprietà | Opzioni | Descrizione |
| -------- | ------- | ----------- |
| deviceId | aggiornamenti obbligatori e in sola lettura | Specifica una stringa (fino a 128 caratteri) caratteri alfanumerici ASCII a 7 bit + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | richiesta di sola lettura | Stringa generati dall'hub tra maiuscole e minuscole fino a 128 caratteri. Viene utilizzato per distinguere i dispositivi con la stessa **deviceId**, quando eliminare e ricreare. |
| eTag | richiesta di sola lettura | Stringa che rappresenta un etag debole per l'identità del dispositivo, in base alle [RFC7232][lnk-rfc7232].|
| autenticazione | facoltativo | Oggetto composto contenenti materiali di protezione e informazioni di autenticazione. |
| auth.symkey | facoltativo | Un oggetto composto contenente primaria e chiave secondaria, memorizzate in formato base 64. |
| stato | Obbligatorio | Un indicatore di accesso. Può essere **attivato** o **disattivato**. Se **abilitata**, il dispositivo è consentito la connessione. Se **disabilitato**, questo dispositivo non è possibile accedere qualsiasi dispositivo esposto endpoint. |
| statusReason | facoltativo | Una stringa di caratteri lungo 128 che archivia il motivo lo stato di identità dispositivo. Tutti i caratteri UTF-8 consentiti. |
| statusUpdateTime | sola lettura | Indicatore temporale, che mostra la data e l'ora dell'ultimo aggiornamento di stato. |
| connectionState | sola lettura | Un campo che indica lo stato della connessione: **connesso** o **disconnesso**. Questo campo rappresenta la visualizzazione di IoT Hub dispositivo stato della connessione. **Importante**: questo campo deve essere utilizzato solo a fini/debug di sviluppo. Lo stato della connessione viene aggiornato solo per i dispositivi tramite MQTT o AMQP. Inoltre, dipende dal livello di protocollo ping (ping MQTT o AMQP ping) e può avere un ritardo massimo di 5 minuti solo. Per questi motivi, è possibile che falsi positivi, come dispositivi segnalato come connessi, ma in realtà disconnessi. |
| connectionStateUpdatedTime | sola lettura | Un indicatore temporale, che mostra la data e l'ultima volta lo stato della connessione è stato aggiornato. |
| lastActivityTime  | sola lettura | Un indicatore temporale, che mostra la data e l'ultima volta il dispositivo connesso, ricevuto o inviato un messaggio. |

> [AZURE.NOTE] Lo stato della connessione può rappresentare solo la visualizzazione IoT Hub lo stato della connessione. Gli aggiornamenti a questo stato possono essere ritardati, a seconda configurazioni e le condizioni di rete.

## <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato illustrato come utilizzare il Registro di sistema IoT Hub identità dispositivo, potrebbero interessare gli argomenti della Guida per gli sviluppatori seguenti:

- [Controllare l'accesso alle IoT Hub][lnk-devguide-security]
- [Utilizzare due facce di dispositivo per sincronizzare stato e configurazioni][lnk-devguide-device-twins]
- [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare nell'esercitazione IoT Hub che segue:

- [Guida introduttiva di Azure IoT Hub][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md