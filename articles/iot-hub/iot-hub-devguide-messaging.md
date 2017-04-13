<properties
 pageTitle="Guida per sviluppatori - messaggistica | Microsoft Azure"
 description="Azure Guida per sviluppatori di IoT Hub - dispositivo nel cloud e la messaggistica cloud al dispositivo"
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

# <a name="send-and-receive-messages-with-iot-hub"></a>Inviare e ricevere messaggi con IoT Hub

## <a name="overview"></a>Panoramica

IoT Hub sono disponibili le seguenti primitive di messaggistica per comunicare con un dispositivo:

- [Dispositivo nel cloud] [ lnk-d2c] da un dispositivo a un'applicazione back-end.
- [Cloud per dispositivo] [ lnk-c2d] da un'applicazione back-end (*servizio* o *su cloud*).

Proprietà principali di funzionalità di messaggistica IoT Hub sono la durata e affidabilità dei messaggi. Queste proprietà consentono adattabilità alla connettività intermittente sul lato dispositivo e caricare picchi di elaborazione sul lato cloud eventi. IoT Hub implementato *almeno una volta* recapito garanzie per la messaggistica dispositivo nel cloud e cloud al dispositivo.

IoT Hub supporta più [protocolli dispositivo esposto] [ lnk-protocols] (ad esempio MQTT, AMQP e HTTP). Per supportare perfetta interoperabilità tra i protocolli, IoT Hub definisce un [formato di messaggio comune] [ lnk-message-format] che supportano tutti i protocolli esposto dispositivo.

IoT Hub esposto un [endpoint Hub evento compatibile con] [ lnk-compatible-endpoint] per consentire alle applicazioni di back-end leggere i messaggi di dispositivo nel cloud ricevuti dall'hub.

### <a name="when-to-use"></a>Quando utilizzare

Messaggistica è una funzionalità di base di IoT Hub. Utilizzarla ogni volta che è necessario inviare messaggi dal dispositivo per il back-end o inviare messaggi dal back-end a un dispositivo.

Per un confronto dei servizi IoT Hub e hub di evento, vedere [confronto di IoT Hub e hub di evento][lnk-compare].

## <a name="device-to-cloud-messages"></a>Messaggi di dispositivo nel cloud

Inviare messaggi dispositivo nel cloud tramite un endpoint esposto dispositivo (**/devices/ {deviceId} / messaggi/eventi**). Il servizio di back-end riceve messaggi di dispositivo nel cloud tramite un endpoint servizio esposto (**/messages/events**) che è compatibile con [Gli hub di evento][lnk-event-hubs]. Pertanto, è possibile utilizzare [l'integrazione di hub di eventi e SDK] standard[ lnk-compatible-endpoint] per ricevere i messaggi di dispositivo nel cloud.

IoT Hub implementa messaggistica dispositivo nel cloud in modo simile a un [Evento hub][lnk-event-hubs]. I messaggi di dispositivo nel cloud dell'IoT Hub sono più come hub di evento *eventi* di [Servizio Bus] [ lnk-servicebus] *messaggi*.

Questa implementazione implica quanto segue:

* Analogamente agli eventi hub di evento, dispositivo nel cloud messaggi sono permanenti e mantenuti in un hub IoT sette giorni (vedere [Opzioni di configurazione di dispositivi nel cloud][lnk-d2c-configuration]).
* I messaggi di dispositivo nel cloud suddivisa in un set fisso delle partizioni impostato al momento della creazione (vedere [Opzioni di configurazione di dispositivi nel cloud][lnk-d2c-configuration]).
* Modo analogo a un hub di eventi, i client la lettura dei messaggi di dispositivo nel cloud necessario gestire partizioni e arresto. Vedere [Evento hub - utilizzo degli eventi][lnk-event-hubs-consuming-events].
* Come hub di evento eventi, i messaggi di dispositivo nel cloud possono contenere al massimo 256 KB e possono essere raggruppati in batch ottimizzare Invia. Batch può contenere al massimo 500 messaggi e 256 KB.

Esistono tuttavia alcune importanti differenze IoT Hub messaggistica dispositivo nel cloud e hub evento:

* Come illustrato nella finestra [controllo dell'accesso alle IoT Hub] [ lnk-devguide-security] sezione IoT Hub consente il controllo di autenticazione e accesso al dispositivo.
* IoT Hub consente milioni di dispositivi connessi contemporaneamente (vedere [quote e limitazioni][lnk-quotas]), mentre gli hub di evento è limitato ai 5000 connessioni AMQP per spazio dei nomi.
* Non non autorizzato partizioni con un **PartitionKey**IoT Hub. Dispositivo nel cloud messaggi sono suddivisi in base alle loro origine **deviceId**.
* Il ridimensionamento IoT Hub è leggermente diverso rispetto alla hub evento. Per ulteriori informazioni, vedere [Il ridimensionamento IoT Hub][lnk-guidance-scale].

> [AZURE.NOTE] Non è possibile utilizzare IoT Hub per hub di evento in tutti gli scenari. Ad esempio, in alcuni calcoli di elaborazione evento, potrebbe essere necessario eseguire eventi rispetto a una proprietà diversa o un campo prima di analizzare i flussi di dati. In questo scenario, è possibile utilizzare un Hub di evento per separare due parti del flusso pipeline di elaborazione. Per ulteriori informazioni, vedere *le partizioni* in [Azure Event hub Overview][lnk-eventhub-partitions].

Per informazioni dettagliate su come usare la messaggistica dispositivo nel cloud, vedere [IoT Hub API e SDK][lnk-sdks].

> [AZURE.NOTE] Quando si utilizza HTTP per inviare messaggi di dispositivo nel cloud, nomi di proprietà e i valori possono contenere solo caratteri alfanumerici ASCII, più ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Traffico non telemetria

Spesso, oltre ai punti di dati di telemetria, dispositivi anche inviare messaggi e le richieste che richiedono l'esecuzione e gestione dal livello di logica business dell'applicazione. Ad esempio gli avvisi critici che è necessario attivare un'azione specifica in back-end o risposte ai comandi inviati da back-end.

Per ulteriori informazioni sul modo migliore per l'elaborazione di questo tipo di messaggio, vedere il [Esercitazione: la modalità di elaborazione dei messaggi di dispositivo nel cloud IoT Hub] [ lnk-d2c-tutorial] esercitazione.

### <a name="device-to-cloud-configuration-options"></a>Opzioni di configurazione di dispositivi nel cloud

Un hub IoT espone le proprietà seguenti per abilitare il controllo messaggistica dispositivo nel cloud.

* **Numero di partizioni**. Impostare questa proprietà al momento della creazione per definire il numero delle partizioni per acquisizione evento dispositivo nel cloud.
* **Tempo di conservazione**. Questa proprietà consente di specificare l'ora di conservazione per i messaggi di dispositivo nel cloud. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni.

Inoltre, modo analogo a un hub di evento, IoT Hub consente di gestire i gruppi di consumer sul dispositivo nel cloud ricevono endpoint.

È possibile modificare tutte queste proprietà a livello di programmazione tramite il [provider di risorse IoT Hub API REST][lnk-resource-provider-apis], o tramite il [portale di Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Proprietà anti-spoofing

Per evitare dispositivo spoofing nei messaggi di dispositivo nel cloud, IoT Hub timbri tutti i messaggi con le proprietà seguenti:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

I primi due contengono la **deviceId** e **generationId** del dispositivo di origine, in base alle [proprietà dei dispositivi identità][lnk-device-properties].

La proprietà **ConnectionAuthMethod** contiene un oggetto JSON serializzato, con le proprietà seguenti:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Messaggi cloud al dispositivo

È inviare messaggi cloud al dispositivo tramite un endpoint servizio esposto (**/messages/devicebound**). Un dispositivo li riceve tramite un endpoint specifico di dispositivo (**/devices/ {deviceId} / messaggi/devicebound**).

Ogni messaggio cloud al dispositivo è destinato a un singolo dispositivo impostando la proprietà **per** su **/devices/ {deviceId} / messaggi/devicebound**.

>[AZURE.IMPORTANT] Ogni coda dispositivo contiene al massimo 50 messaggi cloud al dispositivo. Tentativo di inviare messaggi più gli stessi risultati di dispositivo in un messaggio di errore.

> [AZURE.NOTE] Quando si inviano messaggi cloud per dispositivo, nomi di proprietà e i valori possono contenere caratteri alfanumerici ASCII, più ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Ciclo di vita del messaggio

Per garantire almeno una volta recapito dei messaggi, IoT Hub persiste cloud per dispositivo messaggi nelle code per dispositivo. Dispositivi in modo esplicito necessario confermare *il completamento* per IoT Hub per rimuoverli dalla coda. In questo modo adattabilità contro la connettività ed errori del dispositivo.

Il diagramma seguente illustra il grafico sullo stato del ciclo di vita per un messaggio cloud al dispositivo.

![Ciclo di vita del messaggio cloud al dispositivo][img-lifecycle]

Quando il servizio invia un messaggio, si ritiene *Accodamento*. Quando un dispositivo desidera *ricevere* un messaggio, IoT Hub *blocchi* il messaggio (imposta lo stato su **invisibili**) che consente di altri thread nello stesso dispositivo per avviare la ricezione di altri messaggi. Al termine di elaborazione di un messaggio, un thread di dispositivo notifica IoT Hub, *completare* il messaggio.

Un dispositivo è anche possibile:

- *Rifiutare* il messaggio, causando IoT Hub impostare lo stato di **Deadlettered** . Nota: dispositivi che si connettono con MQTT non è possibile rifiutare messaggi C2D.
- *Abbandonare* il messaggio, causando IoT Hub inserire il messaggio nuovamente nella coda, con lo stato è impostato su **Accodamento**.

Un thread potrebbe non riuscire per l'elaborazione di un messaggio senza notifica IoT Hub. In questo caso, i messaggi automaticamente transizione da stato **invisibile** allo stato **in coda** dopo un *timeout visibilità (o blocco)*. Il valore predefinito di questo timeout è un minuto.

Un messaggio può passare tra **l'Accodamento** e **invisibili** stati per al massimo, il numero di volte specificato nella proprietà **numero massimo recapito** IoT hub. Dopo il numero delle transizioni, IoT Hub imposta lo stato del messaggio per **Deadlettered**. Analogamente, IoT Hub imposta lo stato di un messaggio a **Deadlettered** dopo la data di scadenza (vedere [il periodo di validità][lnk-ttl]).

Per un'esercitazione su cloud per dispositivo messaggi, vedere [Esercitazione: come inviare messaggi cloud per dispositivo con IoT Hub][lnk-c2d-tutorial]. Per gli argomenti della Guida di riferimento sul funzionamento di diversi API e SDK esporre la funzionalità cloud per dispositivi, vedere [IoT Hub API e SDK][lnk-sdks].

> [AZURE.NOTE] In genere, messaggi cloud per dispositivo completare ogni volta che la perdita del messaggio riguarda solo la logica dell'applicazione. Ad esempio, il contenuto del messaggio è stato mantenuto nell'archivio locale o un'operazione è stata eseguita correttamente. Il messaggio potrebbe anche portare con sé informazioni temporanee, il cui perdita sarebbe ha effetto sulla funzionalità dell'applicazione. In alcuni casi, per le attività lunga, è possibile completare il messaggio cloud al dispositivo dopo il salvataggio la descrizione delle attività in un archivio locale. È quindi possibile comunicare applicazione back-end con uno o più messaggi dispositivo nel cloud in varie fasi sullo stato di avanzamento dell'attività.

### <a name="message-expiration-time-to-live"></a>Scadenza messaggio (durata)

Ogni messaggio cloud per dispositivo ha una scadenza. Questa volta è impostata per il servizio (nella proprietà **ExpiryTimeUtc** ) o per IoT Hub utilizzando il predefinito *il periodo di validità* specificata come proprietà IoT Hub. Vedere [le opzioni di configurazione Cloud per dispositivo][lnk-c2d-configuration].

> [AZURE.NOTE] Un modo comune per sfruttare la scadenza dei messaggi ed evitare l'invio di messaggi da dispositivi disconnessi, consiste nell'impostare ora breve 24h TTL valori. Questo approccio consente di ottenere lo stesso risultato di mantenere lo stato di connessione di dispositivi, pur essendo più efficiente. Quando si richiedono riconoscimenti messaggio, IoT Hub per segnalare quali dispositivi sono in grado di ricevere messaggi e i dispositivi non sono in linea oppure non sono riuscita.

### <a name="message-feedback"></a>Commenti e suggerimenti messaggio

Quando si invia un messaggio cloud per dispositivo, il servizio può richiedere il recapito di commenti e suggerimenti per ogni messaggio relativa allo stato finale del messaggio.

- Se si imposta la proprietà **Ack** in **un valore positivo**, IoT Hub genera un messaggio di commenti e suggerimenti se e solo se il messaggio cloud per dispositivo raggiunto lo stato **completato** .
- Se si imposta la proprietà **Ack** a **negativo**, IoT Hub genera un messaggio di commenti e suggerimenti, solo se, il messaggio cloud per dispositivo raggiunge lo stato di **Deadlettered** .
- Se si imposta la proprietà **Ack** per **intero**, IoT Hub genera un messaggio di commenti e suggerimenti in entrambi i casi.

> [AZURE.NOTE] Se non si riceve un messaggio di commenti e suggerimenti **Ack** si **riempie**, significa che il messaggio di feedback è scaduto. Il servizio non è possibile sapere dov'è il messaggio originale. In pratica, un servizio necessario assicurarsi che sia possibile elaborare i commenti prima della scadenza. L'ora di scadenza massimo due giorni, che consente di molto tempo per ottenere il servizio esegue nuovamente se si verifica un errore.

Come illustrato in [endpoint][lnk-endpoints], IoT Hub offre commenti e suggerimenti tramite un endpoint servizio esposto (**/messages/servicebound/feedback**) come messaggi. La semantica per ricevere commenti e suggerimenti sono gli stessi messaggi cloud al dispositivo e di avere la stessa [ciclo di vita del messaggio][lnk-lifecycle]. Se possibile, viene eseguito in batch commenti e suggerimenti messaggio in un singolo messaggio, nel formato seguente:

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTime | Indicazione quando il messaggio è stato creato. |
| ID utente | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata JSON dei record, ognuna con le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTimeUtc | Indicazione quando si è verificato il risultato del messaggio. Ad esempio, il dispositivo completata o nel messaggio scaduto. |
| OriginalMessageId | **ID messaggio** del messaggio cloud al dispositivo in cui si riferiscono queste informazioni e suggerimenti. |
| StatusCode | Numero intero necessario. Nei messaggi di commenti e suggerimenti generati da IoT Hub utilizzati. <br/> 0 = esito positivo <br/> 1 = messaggio è scaduto <br/> 2 = superato il numero massimo di recapito <br/> 3 = messaggio rifiutato |
| Descrizione | I valori stringa per **StatusCode**. |
| DeviceId | **DeviceId** del dispositivo di destinazione del messaggio cloud al dispositivo in cui si riferisce questa parte di commenti e suggerimenti. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo di destinazione del messaggio cloud al dispositivo in cui si riferisce questa parte di commenti e suggerimenti. |


>[AZURE.IMPORTANT] Il servizio è necessario specificare un **ID messaggio** per il messaggio cloud per dispositivi consentire di correlare i relativi commenti e suggerimenti con il messaggio originale.

Nell'esempio seguente viene illustrato il corpo di un messaggio di commenti e suggerimenti.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Opzioni di configurazione cloud al dispositivo

Ogni hub IoT espone le seguenti opzioni di configurazione per la messaggistica cloud al dispositivo.

| Proprietà | Descrizione | Intervallo e predefinito |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predefinito per i messaggi cloud al dispositivo. | Intervallo di ISO_8601 fino a 2D (minimo 1 minuto). Predefinito: 1 ora. |
| maxDeliveryCount | Conteggio di recapito massimo per code cloud al dispositivo per dispositivo. | da 1 a 100. Predefinito: 10. |
| feedback.ttlAsIso8601 | Criteri di conservazione per i messaggi di commenti e suggerimenti associate ai servizi. | Intervallo di ISO_8601 fino a 2D (minimo 1 minuto). Predefinito: 1 ora. |
| feedback.maxDeliveryCount | Conteggio di recapito massimo per coda di commenti e suggerimenti. | da 1 a 100. Predefinito: 100. |

Per ulteriori informazioni, vedere [creare IoT hub][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Leggere i messaggi di dispositivo nel cloud

IoT Hub esposto un endpoint per i servizi di back-end leggere i messaggi dispositivo nel cloud ricevuti tramite il tuo fulcro. L'endpoint è un evento compatibile con Hub, che consente di utilizzare uno dei meccanismi il servizio di evento hub supporta per la lettura dei messaggi.

Quando si utilizza [Azure Service Bus SDK per .NET] [ lnk-servicebus-sdk] o [Evento hub - evento processore Host][lnk-eventprocessorhost], è possibile utilizzare qualsiasi stringa di connessione IoT Hub con le autorizzazioni corrette. Usare quindi **messaggi e gli eventi** come il nome dell'evento Hub.

Quando si utilizza SDK (o integrazioni dei prodotti) che sono a conoscenza del IoT Hub, è necessario recuperare un endpoint compatibile con evento Hub e un nome di Hub evento compatibile con dalle impostazioni IoT Hub nel [portale di Azure][lnk-management-portal]:

1. Selezionare **messaggistica**e il hub IoT.
2. Nella sezione **Impostazioni dispositivo nel cloud** individuare i valori seguenti: **Hub evento compatibile con endpoint**, **Hub evento compatibile con nome**e **le partizioni**.

    ![Impostazioni dispositivo nel cloud][img-eventhubcompatible]

> [AZURE.NOTE] Se il SDK richiede un valore di **nome host** o **Namespace** , rimuovere la combinazione di dall' **Hub evento compatibile con endpoint**. Ad esempio, se l'endpoint Hub evento compatibile con **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **Hostname** sarebbe **iothub-ns-myiothub-1234.servicebus.windows.net**e **Namespace** sarebbe **iothub-ns myiothub 1234**.

È quindi possibile utilizzare eventuali criteri di sicurezza di accesso condiviso dotato di autorizzazioni **ServiceConnect** a cui connettersi Hub evento specificato.

Se è necessario creare una stringa di connessione Hub di evento in base alle informazioni precedente, utilizzare il modello seguente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Di seguito è un elenco di SDK e le integrazioni che è possibile usare con i punti finali Hub evento compatibile con esposti IoT Hub:

* [Client hub evento Java](https://github.com/hdinsight/eventhubs-client)
* [Eccesso Apache spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare [spout origine](https://github.com/apache/storm/tree/master/external/storm-eventhubs) nella GitHub.
* [Integrazione di motori di Apache](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni su scambio di messaggi con IoT Hub.

## <a name="message-format"></a>Formato del messaggio

I messaggi IoT Hub includono:

* Un insieme di *Proprietà-sistema*. Proprietà IoT Hub interpreta o agli insiemi. Il valore impostato è predeterminato.
* Un insieme di *proprietà dell'applicazione*. Dizionario delle proprietà di stringa che è possibile definire l'applicazione e accesso senza dover deserializzare il corpo del messaggio. IoT Hub mai modifica queste proprietà.
* Un corpo binario opaco.

Per ulteriori informazioni su come il messaggio codificato in diversi protocolli, vedere [IoT Hub API e SDK][lnk-sdks].

La tabella seguente elenca l'insieme di proprietà-sistema nei messaggi IoT Hub.

| Proprietà | Descrizione |
| -------- | ----------- |
| ID messaggio | Un identificatore utente-che è possibile impostare per il messaggio, utilizzato per motivi di risposta convocazione. Formato: Distinzione tra maiuscole e stringa (fino a 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numero di sequenza | (Univoco per dispositivo coda) un numero assegnato dall'IoT Hub a ogni messaggio cloud al dispositivo. |
| A | Una destinazione specificata nel [Cloud per dispositivo] [ lnk-c2d] messaggi. |
| ExpiryTimeUtc | Data e ora di scadenza di messaggio. |
| EnqueuedTime | Data e ora che il messaggio è stato ricevuto dall'IoT Hub. |
| CorrelationId | Proprietà stringa in un messaggio di risposta che contiene in genere ID messaggio di richiesta, nei modelli di risposta convocazione. |
| ID utente | ID utilizzato per specificare l'origine dei messaggi. Quando i messaggi generati da IoT Hub, viene impostata su `{iot hub name}`. |
| ACK | Un generatore di commenti e suggerimenti messaggio. Questa proprietà è utilizzata nei messaggi cloud per dispositivi per richiedere IoT Hub per generare i messaggi di commenti e suggerimenti in seguito al consumo del messaggio dal dispositivo. I valori possibili: **Nessuna** (impostazione predefinita): viene generato alcun messaggio commenti e suggerimenti, **positivo**: riceve un messaggio di commenti e suggerimenti se il messaggio è stato completato, **negativo**: riceve un messaggio di commenti e suggerimenti se il messaggio è scaduto (o numero massimo di recapito è stato raggiunto) senza viene completata da dispositivo o **completo**: positivi e negativi. Per ulteriori informazioni, vedere [commenti e suggerimenti messaggio][lnk-feedback]. |
| ConnectionDeviceId | ID impostati dall'IoT Hub nei messaggi di dispositivo nel cloud. La presentazione contiene **deviceId** del dispositivo che ha inviato il messaggio. |
| ConnectionDeviceGenerationId | ID impostati dall'IoT Hub nei messaggi di dispositivo nel cloud. Contiene **generationId** (in base alle [proprietà dei dispositivi identità][lnk-device-properties]) del dispositivo che ha inviato il messaggio. |
| ConnectionAuthMethod | Un metodo di autenticazione impostato dall'IoT Hub nei messaggi di dispositivo nel cloud. Questa proprietà contiene informazioni sul metodo di autenticazione utilizzato per eseguire l'autenticazione dispositivo invio del messaggio. Per ulteriori informazioni, vedere [dispositivo nel cloud anti-spoofing][lnk-antispoofing].|

## <a name="communication-protocols"></a>Protocolli di comunicazione

IoT Hub consente ai dispositivi di utilizzare [MQTT][lnk-mqtt], MQTT su WebSockets, [AMQP][lnk-amqp], AMQP WebSockets e HTTP protocolli per le comunicazioni sul dispositivo. La tabella seguente contiene raccomandazioni importanti per la scelta del protocollo:

| Protocollo | Quando si deve scegliere questo protocollo |
| -------- | ------------------------------------ |
| MQTT <br> MQTT su WebSocket     | Usare in tutti i dispositivi che non richiedono per collegare più dispositivi (ciascuno con il proprio le credenziali per dispositivo) tramite la stessa connessione TLS. |
| AMQP <br> AMQP su WebSocket    | Usare gateway campo e cloud in modo da sfruttare connessione multiplexing tra i dispositivi. |
| HTTP    | Usare per i dispositivi che non è in grado di supportare altri protocolli. |

Quando si sceglie il protocollo per le comunicazioni sul dispositivo, tenere presente quanto segue:

* **Motivo cloud al dispositivo**. HTTP non esiste un modo efficace per implementare push server. Di conseguenza, quando si utilizza HTTP, dispositivi sondaggio IoT Hub per i messaggi cloud al dispositivo. Questo approccio è poco efficace per dispositivo sia IoT Hub. In linee guida HTTP corrente, ogni dispositivo deve sondaggio per i messaggi di ogni 25 minuti o più. Mano, MQTT e AMQP supportano push server quando si ricevono messaggi cloud al dispositivo. Consentono push immediato dei messaggi dall'IoT Hub al dispositivo. Se la latenza di recapito è un problema, MQTT o AMQP sono i protocolli migliori da utilizzare. Per i dispositivi connessi raramente, HTTP come si ottengono buoni risultati.
* **Gateway di campo**. Quando si utilizza MQTT e HTTP, non è possibile connettersi più dispositivi (ciascuno con il proprio le credenziali per dispositivo) utilizzando la stessa connessione TLS. In questo modo, per [gli scenari gateway campo][lnk-azure-gateway-guidance], questi protocolli sono non ottimali perché richiedono una connessione tra il gateway di campo e IoT Hub TLS per tutti i dispositivi connessi al gateway di campo.
* **Dispositivi di risorse in**. Raccolte MQTT e HTTP hanno un impatto minore rispetto a raccolte AMQP. Di conseguenza, se il dispositivo è limitata risorse (ad esempio, minore di 1 MB di RAM), questi protocolli possono essere l'unica implementazione di protocollo disponibile.
* **Scorrimento di rete**. Il protocollo AMQP standard utilizza la porta 5671, mentre MQTT in attesa sulla porta 8883, potrebbe causare problemi nelle reti che vengono chiuse per protocolli non HTTP. MQTT su WebSockets, AMQP WebSockets e HTTP sono disponibili per essere utilizzato in questo scenario.
* **Dimensione payload**. MQTT e AMQP sono protocolli binari, conseguenti payload più compatto da HTTP.

> [AZURE.NOTE] Quando si utilizza HTTP, ogni dispositivo deve sondaggio per i messaggi cloud per dispositivo ogni 25 minuti o più. Tuttavia, durante lo sviluppo, è possibile sondaggio più frequentemente ogni 25 minuti.

## <a name="port-numbers"></a>Numeri di porta

Dispositivi possono comunicare IoT Hub in Azure con diversi protocolli. In genere, la scelta del protocollo è determinata dai requisiti specifici della soluzione. Nella tabella seguente sono elencate le porte in uscita che devono essere aperte per un dispositivo in grado di utilizzare un protocollo specifico:

| Protocollo | Porte |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT su WebSockets | 443    |
| AMQP     | 5671    |
| AMQP su WebSockets | 443    |
| HTTP     | 443     |
| LWM2M (gestione di dispositivi) | 5684 |

Dopo aver creato un hub IoT in un'area di Azure, hub mantiene lo stesso indirizzo IP per la durata di tale hub. Tuttavia, per mantenere la qualità del servizio, se Microsoft hub IoT viene spostato in un'unità diversa scala è assegnato sarà un nuovo indirizzo IP.

## <a name="notes-on-mqtt-support"></a>Note sul supporto MQTT

IoT Hub consente il protocollo v3.1.1 MQTT con le limitazioni seguenti e il comportamento specifico:

  * **2 QoS non è supportato**. Quando un client dispositivo pubblica un messaggio con **QoS 2**, IoT Hub chiude la connessione di rete. Quando un client di dispositivo si iscrive a un argomento con **QoS 2**, IoT Hub concede massimo QoS livello 1 nel pacchetto **SUBACK** .
  * **Mantieni messaggi non vengono mantenute**. Se un client dispositivo pubblica un messaggio con il flag MANTIENI impostato su 1, IoT Hub aggiunge la **x-acconsentire a mantenere la** proprietà dell'applicazione al messaggio. In questo caso, IoT Hub non viene mantenuto il messaggio Mantieni, ma invece passa all'applicazione back-end.

Per ulteriori informazioni, vedere [MQTT Hub IoT supportano][lnk-devguide-mqtt].

Per garantire la finale, è necessario esaminare il [gateway protocollo Azure IoT] [ lnk-azure-protocol-gateway] che consente di distribuire un gateway prestazioni elevate protocollo personalizzato che si interfaccia direttamente con IoT Hub. Il gateway di protocollo IoT Azure consente di personalizzare il protocollo di dispositivo per distribuzioni MQTT brownfield o altri protocolli personalizzati. Questo approccio, tuttavia, è necessario eseguire e operare un gateway protocollo personalizzato.

## <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come inviare e ricevere messaggi con IoT Hub, potrebbero interessare gli argomenti della Guida per gli sviluppatori seguenti:

- [Caricare i file da un dispositivo][lnk-devguide-upload]
- [Gestione delle identità dispositivo IoT hub][lnk-devguide-identities]
- [Controllare l'accesso alle IoT Hub][lnk-devguide-security]
- [Utilizzare due facce di dispositivo per sincronizzare stato e configurazioni][lnk-devguide-device-twins]
- [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare le seguenti esercitazioni IoT Hub:

- [Guida introduttiva di Azure IoT Hub][lnk-getstarted-tutorial]
- [Come inviare messaggi cloud per dispositivo con IoT Hub][lnk-c2d-tutorial]
- [Modalità di elaborazione dei messaggi di IoT Hub dispositivo nel cloud][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
