<properties
 pageTitle="Sviluppo di Guida - comprendere due facce dispositivo | Microsoft Azure"
 description="Guida per sviluppatori di IoT Hub Azure - usare due facce di dispositivo per sincronizzare i dati di stato e la configurazione tra IoT Hub e i dispositivi"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Comprendere due facce dispositivo: visualizzare in anteprima

## <a name="overview"></a>Panoramica

*Due facce dispositivo* sono documenti JSON che memorizzano le informazioni sullo stato dispositivo (metadati, configurazioni e condizioni). IoT Hub persiste doppi un dispositivo per ogni dispositivo che si connette a IoT Hub. In questo articolo viene descritto:

* la struttura di doppi dispositivo: *tag*, *desiderato* e *segnalati proprietà*, e
* operazioni che App dispositivo e termina posteriore può eseguire in due facce dispositivo.

> [AZURE.NOTE] In questa fase, due facce dispositivo sono accessibili solo da dispositivi che eseguono la connessione a IoT Hub mediante il protocollo MQTT. Consultare per [supportare MQTT] [ lnk-devguide-mqtt] articolo per istruzioni su come convertire app dispositivo esistente da utilizzare MQTT.

### <a name="when-to-use"></a>Quando utilizzare

Usare due facce di dispositivo per:

* Archiviare i metadati dispositivo specifico nel cloud, ad esempio percorso di distribuzione di un distributore automatico.
* Report informazioni sullo stato corrente, ad esempio le funzionalità disponibili e le condizioni nell'app di dispositivo, ad esempio un dispositivo che si connettono tramite cellulare o wifi.
* Sincronizzare lo stato dei flussi di lavoro di esecuzione prolungata tra dispositivo app e viceversa fine, ad esempio back-end che specifica la nuova versione di firmware per installare e app dispositivo reporting le varie fasi del processo di aggiornamento.
* Il dispositivo metadati, configurazione o stato della query.

Utilizzare [i messaggi di dispositivo nel cloud] [ lnk-d2c] per le sequenze di eventi timestamp, ad esempio serie temporale di dati sensore o avvisi. Utilizzare i [metodi di cloud per dispositivo] [ lnk-methods] per un controllo interattivo dei dispositivi, ad esempio attivare una ventola.

## <a name="device-twins"></a>Due facce dispositivo

Due facce dispositivo memorizzano le informazioni relative al dispositivo che:

- Estremità dispositivo e viceversa è possibile utilizzare per sincronizzare configurazione e le condizioni di dispositivo.
- Applicazione back-end consente di query e di destinazione lunga operazioni.

Ciclo di vita di un doppi dispositivo è collegato al corrispondente [identità dispositivo][lnk-identity]. Due facce sono in modo implicito creati o eliminati quando una nuova identità di dispositivo viene creata o eliminata IoT hub.

Doppi un dispositivo sono un documento JSON che include:

* **Tag**. Un documento JSON letta e scritta da back-end. Tag non sono visibili alle App dispositivo.
* **Proprietà desiderato**. Utilizzato in combinazione con il comando proprietà segnalate per sincronizzare configurazione del dispositivo o condizione. Proprietà desiderate possono essere impostate solo dall'applicazione indietro fine e possono essere letti dall'app di dispositivo. Ricevere una notifica app dispositivo anche in tempo reale delle modifiche apportate nelle proprietà desiderate.
* **Proprietà dichiarato**. Utilizzato in combinazione con le proprietà desiderate per la sincronizzazione di configurazione del dispositivo o condizione. Proprietà segnalate può essere impostata solo dall'app di dispositivo e possano leggere ed eseguire una query mediante applicazione back-end.

Inoltre, nella cartella radice della doppi dispositivo contiene le proprietà di sola lettura di identità corrispondente come contenute nel [Registro di sistema di dispositivo identità][lnk-identity].

![][img-twin]

Ecco un esempio di un documento JSON doppi dispositivo:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

Nell'oggetto radice Elenca le proprietà di sistema e gli oggetti contenitore per `tags` ed entrambe `reported` e `desired properties`. Il `properties` contenitore contiene alcuni elementi di sola lettura (`$metadata`, `$etag`, e `$version`) che sono descritti rispettivamente nei [metadati doppi] [ lnk-twin-metadata] e [ottimistica] [ lnk-concurrency] sezioni.

### <a name="reported-property-example"></a>Esempio di proprietà segnalati

Nell'esempio precedente, doppi dispositivo contiene un `batteryLevel` proprietà svolto app dispositivo. Questa proprietà consente di query oltre a operare sui dispositivi in base al livello batteria segnalati ultimo. Un altro esempio avrebbe i dispositivo app report dispositivo o le opzioni di integrazione applicativa.

Si noti come segnalate proprietà semplificare scenari in cui l'ultimo valore noto di una proprietà interessano back-end. Utilizzare [i messaggi di dispositivo nel cloud] [ lnk-d2c] se è necessario elaborare telemetria dispositivo sotto forma di sequenze di eventi timestamp, ad esempio serie temporale back-end.

### <a name="desired-configuration-example"></a>Esempio di configurazione desiderata

Nell'esempio riportato sopra il `telemetryConfig` desiderato e segnalati vengono utilizzate dall'app di back-end e dispositivi per sincronizzare la configurazione di telemetria per il dispositivo. Per esempio:

1. App back-end imposta la proprietà desiderata con il valore di configurazione desiderata. Ecco la parte del documento con la proprietà desiderata:

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. L'app dispositivo riceve una notifica di modifiche immediatamente se connessi o presso riconnessione prima. L'app dispositivo risulterà configurazione aggiornata (o una condizione di errore utilizzando il `status` proprietà). Ecco la parte della proprietà segnalate:

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. App back-end possibile tenere traccia i risultati dell'operazione di configurazione su più dispositivi, eseguendo una [query] [ lnk-query] due facce.

> [AZURE.NOTE] Frammenti sopra descritti sono illustrati esempi, ottimizzati per la leggibilità delle possibili consente di codificare una configurazione di dispositivo e il relativo stato. IoT Hub non impone uno schema specifico per le proprietà desiderate e segnalati in due facce il dispositivo.

In molti casi due facce vengono utilizzati per sincronizzare le operazioni di esecuzione prolungata, ad esempio gli aggiornamenti del firmware. Fare riferimento a [utilizzare le proprietà desiderate per configurare i dispositivi] [ lnk-twin-properties] per ulteriori informazioni su come utilizzare le proprietà per sincronizzare e tenere traccia di tempo che eseguono operazioni su dispositivi.

## <a name="back-end-operations"></a>Operazioni di back-end

Back-end opera su doppi utilizzando le seguenti operazioni atomiche esposte tramite HTTP:

1. **Recuperare un doppio per id**. Questa operazione restituisce il contenuto del documento del doppi, inclusi i tag e lo si desidera, segnalati e proprietà-sistema.
2. **Aggiornare parzialmente doppi**. Questa operazione consente back-end per l'aggiornamento parziale di doppi tag o le proprietà desiderate. Aggiornamento parziale è espresso come documento JSON che aggiunge o aggiorna qualsiasi proprietà menzionati. Proprietà impostata su `null` vengono rimossi. Ad esempio, le operazioni seguenti crea una nuova proprietà desiderata con valore `{"newProperty": "newValue"}`, sovrascrive il valore esistente di `existingProperty` con `"otherNewValue"`e consente di rimuovere completamente `otherOldProperty`. Nessuna modifica verificarsi ad altre proprietà desiderate esistente o tag seguenti:

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Sostituire le proprietà desiderate**. Questa operazione consente back-end completamente sovrascrivere esistente tutte le proprietà desiderate e sostituire un nuovo documento JSON per `properties/desired`.
4. **Sostituire i tag**. Modo analogo per sostituire le proprietà desiderate, l'operazione consente di back-end completamente sovrascrivere tutti i contrassegni esistenti e sostituire un nuovo documento JSON per `tags`.

Tutte le operazioni precedenti supportano [ottimistica] [ lnk-concurrency] e richiedono l'autorizzazione **ServiceConnect** , come definito nella [protezione] [ lnk-security] articolo.

Oltre a queste operazioni, back-end può eseguire una query due facce utilizzando un [linguaggio di query]della mi piace SQL[lnk-query]ed eseguire operazioni sul insiemi estesi di due facce mediante [processi][lnk-jobs].

## <a name="device-operations"></a>Operazioni del dispositivo

L'app dispositivo opera su doppi utilizzando le seguenti operazioni atomiche:

1. **Recuperare un doppio**. Questa operazione viene restituito il contenuto del documento del doppi (inclusi i tag e lo si desidera, segnalati e proprietà-sistema) per il dispositivo connesso.
2. **Parzialmente aggiornamento segnalati proprietà**. Questa operazione consente l'aggiornamento parziale delle proprietà segnalate del dispositivo connesso. Si usa lo stesso formato aggiornamento JSON come back-end affiancate aggiornamento parziale di proprietà desiderate.
3. **Rispetta bene accolta proprietà**. Il dispositivo connesso può scegliere di ricevere una notifica degli aggiornamenti per le proprietà desiderate, non appena si verificano. Il dispositivo riceve lo stesso formato del aggiornamento (sostituzione completo o parziale) eseguito da back-end.

Tutte le operazioni precedenti richiedono l'autorizzazione **DeviceConnect** , come definito nella [protezione] [ lnk-security] articolo.

Il [dispositivo di Azure IoT SDK] [ lnk-sdks] rendono più semplice utilizzare le operazioni precedenti da molte lingue e piattaforme. Sono disponibili ulteriori informazioni sui dettagli di primitive IoT dell'Hub per la sincronizzazione di proprietà desiderate nel [flusso di riconnessione dispositivo][lnk-reconnection].

> [AZURE.NOTE] Due facce dispositivo sono attualmente accessibili solo da dispositivi che eseguono la connessione a IoT Hub mediante il protocollo MQTT.

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni sul controllo dell'accesso per il tuo fulcro IoT.

## <a name="tags-and-properties-format"></a>Formato tag e proprietà

Contrassegni, le proprietà desiderate e segnalati sono oggetti JSON con le restrizioni seguenti:

* Tutte le chiavi di oggetti JSON sono stringhe UNICODE 128 char maiuscole e minuscole. È consentito caratteri escludere i caratteri di controllo UNICODE (segmenti C0 e C1), e `'.'`, `' '`, e `'$'`.
* Tutti i valori di oggetto JSON possono essere dei seguenti tipi di JSON: booleano, numero, stringa, oggetto. Non sono consentite matrici.

## <a name="twin-size"></a>Dimensioni doppi

IoT Hub applica un limite di dimensioni 8KB sui valori di `tags`, `properties/desired`, e `properties/reported`, esclusione di elementi di sola lettura.
La dimensione viene calcolata il conteggio tutti i caratteri UNICODE esclusione di controllo caratteri (segmenti C0 e C1) e spazio `' '` quando viene visualizzato all'esterno di una costante stringa.
IoT Hub Rifiuta con errore di tutte le operazioni che aumentano le dimensioni dei documenti oltre il limite.

## <a name="twin-metadata"></a>Disponibilità di una copia metadati

IoT Hub mantiene il timestamp dell'ultimo aggiornamento per ogni oggetto JSON nelle proprietà segnalate e desiderata. La data e ora sono in formato UTC e codificato in formato [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Per esempio:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Queste informazioni vengono conservate a ogni livello (non solo le foglie della struttura JSON) per mantenere gli aggiornamenti per la rimozione di tasti di oggetti.

## <a name="optimistic-concurrency"></a>Ottimistica

Tag, proprietà desiderata e segnalate supporta ottimistica.
Tag di avere un etag, in base a [RFC7232], che rappresenta JSON rappresentazione il tag. È possibile utilizzare nelle operazioni di aggiornamento condizionale dal back-end per garantire la coerenza.

Proprietà desiderata e segnalate non dispone di eTag, ma hanno un `$version` valore che deve necessariamente essere incrementali. Modo analogo a un etag, la versione può essere usata dal aggiornamento parti, ad esempio (app di dispositivo per una proprietà segnalata) o back-end per una proprietà desiderata per garantire la coerenza degli aggiornamenti.

Versioni sono inoltre utili quando un agente osservatore (ad esempio, l'app dispositivo osservare le proprietà desiderate) sono riconciliare competizione tra i risultati di un'operazione di recuperare e una notifica di aggiornamento. La sezione [flusso riconnessione dispositivo] [ lnk-reconnection] vengono fornite ulteriori informazioni.

## <a name="device-reconnection-flow"></a>Flusso di riconnessione dispositivo

IoT Hub non mantiene le notifiche di aggiornamento proprietà desiderate per i dispositivi disconnessi. Di conseguenza che un dispositivo che si sta connettendo deve recuperare il documento completo proprietà desiderate, oltre la sottoscrizione per le notifiche di aggiornamento. Offerta la possibilità di competizione tra le notifiche di aggiornamento e il recupero completo, è necessario garantire il flusso relativo al seguente:

1. Dispositivo app si connette a un hub IoT.
2. Dispositivo app sottoscrive per le proprietà desiderate le notifiche di aggiornamento.
3. Dispositivo app recupera in tutto il documento per le proprietà desiderate.

L'app dispositivo possibile ignorare tutte le notifiche con `$version` minore o uguale rispetto alla versione del documento recuperato completo. In questo modo possibili IoT Hub garantisce che versioni incrementare sempre.

> [AZURE.NOTE] Questa logica già è implementata in [Azure IoT dispositivo SDK][lnk-sdks]. Questa descrizione è utile solo se l'app dispositivo non è possibile usare uno dei dispositivi di Azure IoT SDK e devono essere programmati interfaccia MQTT direttamente.

## <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

A questo punto sono state fornite sulle due facce dispositivo, che potrebbero interessare gli argomenti della Guida per gli sviluppatori seguenti:

- [Richiamare un metodo diretto in un dispositivo][lnk-methods]
- [Pianificare processi in più dispositivi][lnk-jobs]

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare le seguenti esercitazioni IoT Hub:

- [Come usare doppi dispositivo][lnk-twin-tutorial]
- [Come usare le proprietà di un doppio][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png