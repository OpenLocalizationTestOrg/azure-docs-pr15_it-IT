<properties
 pageTitle="Guida per sviluppatori - metodi diretti | Microsoft Azure"
 description="Guida per sviluppatori di IoT Hub Azure - utilizzare i metodi di accesso diretto per richiamare il codice nei dispositivi"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Richiamare un metodo diretto in un dispositivo (preview)

## <a name="overview"></a>Panoramica

IoT Hub consente di richiamare metodi su dispositivi dal cloud. Metodi rappresentano un'interazione risposta convocazione con un dispositivo simile a una chiamata HTTP che sono esito positivo o negativo (dopo un timeout specificato dall'utente) comunicare all'utente che lo stato della chiamata. Questo è utile per gli scenari in cui nel corso di un'azione immediata varia a seconda che il dispositivo stato in grado di rispondere, ad esempio l'invio di un SMS attivazione a un dispositivo se un dispositivo che risulta non in linea (SMS da più costosa rispetto a una chiamata di metodo).

È possibile pensare di un metodo come una chiamata di procedura remota direttamente al dispositivo. Solo i metodi applicate in un dispositivo possono essere chiamati dal cloud. Se nel cloud tenta di richiamare un metodo in un dispositivo che non dispone di tale metodo definito, la chiamata al metodo ha esito negativo.

Ogni metodo dispositivo è destinato a un singolo dispositivo. [Processi] [ lnk-devguide-jobs] consentono di richiamare metodi su più dispositivi e accodare chiamata a un metodo per dispositivi disconnessi.

Chiunque disponga delle autorizzazioni **servizio connettersi** IoT hub può richiamare un metodo in un dispositivo.

### <a name="when-to-use"></a>Quando utilizzare

Metodi per il dispositivo sono simili ai [messaggi cloud per dispositivo] [ lnk-devguide-messages] in entrambe abilitare cloud back-end passare informazioni a un dispositivo, ma differiscono per alcuni aspetti fondamentali. Livello concettuale, i metodi sono icona del e non permanente, mentre i messaggi cloud per dispositivo asincroni con fino a 48 ore di durata.

Metodi seguono un modello di risposta richiesta e non sono permanenti. La mancanza di durata offre due vantaggi immediati quando si comandi dispositivi:

- **Immediato commenti e suggerimenti sull'esecuzione del metodo** indica che non è necessario che consentono di gestire la correlazione tra richiesta e risposta.
- **Velocità effettiva superiore** indica che le operazioni possono essere eseguite più velocemente poiché IoT Hub non offre una durata. IoT Hub consente ulteriori chiamate unitario rispetto ai messaggi cloud al dispositivo.

Messaggi cloud al dispositivo non necessariamente comandi per il dispositivo, ma rappresentano un servizio cloud passando alcuni bit delle informazioni per il dispositivo per renderla sollevare nel tempo libero e a cui il dispositivo può o non rispondere. I messaggi cloud per dispositivo hanno un timeout tempo (fino a 48 ore) mentre si metodi scadere molto più rapidamente.

Utilizzare i metodi di dispositivo per la chiamata comando immediate in un dispositivo e processi per la chiamata pianificata di un comando in un dispositivo.

## <a name="method-lifecycle"></a>Ciclo di vita del metodo

Metodi implementati sul dispositivo e possono richiedere input zero o più nel carico metodo creare correttamente. Si richiama un metodo diretto tramite un URI servizio esposto (`{iot hub}/twins/{device id}/methods/`). Un dispositivo riceve metodi diretti tramite un argomento MQTT specifico di dispositivo (`$iothub/methods/POST/{method name}/`). Si possono supporta i metodi ulteriori protocolli di rete sul dispositivo in futuro.

> [AZURE.NOTE] Quando si richiama un metodo diretto in un dispositivo, nomi di proprietà e i valori possono contenere US ASCII stampabile caratteri alfanumerici, ad eccezione di quelli le seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Icona del metodi e uno esito positivo o negativo dopo il periodo di timeout (predefinito: 30 secondi, che è possibile impostare la su 3600 secondi). Metodi sono utili in interattivi scenari in cui si desidera un dispositivo per agire solo se il dispositivo è in linea e riceventi comandi, ad esempio attivare luce da un telefono. In questi scenari, si desidera visualizzare un immediato esito positivo o negativo in modo che il servizio cloud possa agire sul risultato presto. Il dispositivo potrebbe restituire alcuni corpo del messaggio in seguito il metodo, ma non è necessario per il metodo per farlo. Non esiste nessuna garanzia sull'ordine o la semantica qualsiasi concorrenza in chiamate.

Dispositivo chiamate sono HTTP a partire dal lato cloud, MQTT sola lettura e relativi al lato dispositivo.

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni sull'utilizzo di metodi di accesso diretti.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Richiamare un metodo diretto da un'app di back-end

### <a name="method-invocation"></a>Chiamata del metodo

Chiamate dirette in un dispositivo vengono chiamate HTTP che includono:

- *URI* del dispositivo di (`{iot hub}/twins/{device id}/methods/`)
- Il *metodo* di POST
- *Intestazioni* che contengono l'autorizzazione, richiedere l'ID, tipo di contenuto e la codifica del contenuto
- Un JSON trasparente *corpo* nel formato seguente:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Timeout è espresso in secondi. Se timeout non è impostata, l'impostazione predefinita a 30 secondi.
  
### <a name="response"></a>Risposta

Back-end riceve una risposta che comprende:

- *Codice di stato HTTP*, che viene utilizzato per gli errori provenienti dall'IoT Hub, incluso un errore 404 per i dispositivi attualmente non connesso
- *Intestazioni* che contengono etag, richiedere l'ID, tipo di contenuto e la codifica del contenuto
- JSON *corpo* nel formato seguente:

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Entrambi `status` e `body` forniti dal dispositivo e utilizzati per rispondere con il codice di stato del dispositivo e/o descrizione.

## <a name="handle-a-direct-method-on-a-devcie"></a>Gestire un metodo diretto su un devcie

### <a name="method-invocation"></a>Chiamata del metodo

Dispositivi ricevano le richieste di metodo diretto sull'argomento MQTT:`$iothub/methods/POST/{method name}/?$rid={request id}`

Il corpo che riceve il dispositivo è nel formato seguente:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metodo richieste sono QoS 0.

### <a name="response"></a>Risposta

Il dispositivo invia risposte a `$iothub/methods/res/{status}/?$rid={request id}`, dove:

 - Il `status` proprietà è stato fornito dal dispositivo di esecuzione del metodo.
 - Il `$rid` proprietà corrisponde all'ID richiesta la chiamata al metodo ricevuta dall'IoT Hub.

Corpo imposta dal dispositivo e può essere rappresentato da qualsiasi stato.

## <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato illustrato come utilizzare metodi diretti, possono essere di interesse nell'argomento della Guida per gli sviluppatori seguente:

- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare nell'esercitazione IoT Hub che segue:

- [Utilizzare i metodi di cloud al dispositivo][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
