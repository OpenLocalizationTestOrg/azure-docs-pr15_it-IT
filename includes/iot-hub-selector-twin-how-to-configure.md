> [AZURE.SELECTOR]
- [Node](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>Introduzione

Nella [Guida introduttiva a due facce IoT Hub][lnk-twin-tutorial], è stato descritto come impostare i metadati dispositivo dal soluzione back-end utilizzando i *tag*, segnalare le condizioni di dispositivo da un'app di dispositivo utilizzando *proprietà segnalate*e queste informazioni tramite un linguaggio di query.

In questa esercitazione, si apprenderà come utilizzare il di un doppio *proprietà desiderate* in combinazione con *segnalato proprietà*configurare in remoto dispositivo app. Più specificamente, in questa esercitazione viene illustrata la modalità di segnalazione di un doppio e proprietà desiderate consentono una configurazione multi-passaggio una dispositivo di impostazione dell'applicazione e forniscono la visibilità per il back-end soluzione dello stato di questa operazione in tutti i dispositivi.

A un livello superiore, in questa esercitazione segue il *motivo stato desiderato* per la gestione dei dispositivi. L'idea di questo modello è avere soluzione back-end specificare lo stato desiderato per i dispositivi gestiti, anziché l'invio di comandi specifici. In questo modo il dispositivo responsabile della creazione il modo migliore per raggiungere lo stato desiderato (molto importante negli scenari IoT in condizioni dispositivo specifico influisce sulla possibilità di eseguire immediatamente i comandi specifici) durante la segnalazione al back-end continuamente lo stato corrente e potenziali condizioni di errore. Il motivo stato desiderato è fondamentale per la gestione di gruppi di dispositivi, grandi dimensioni consente il back-end di visibilità completa dello stato del processo di configurazione tra tutti i dispositivi.
È possibile trovare ulteriori informazioni relative al ruolo del motivo stato desiderato nella gestione di dispositivi di [gestione dei dispositivi di panoramica di Azure IoT Hub][lnk-dm-overview].

> [AZURE.NOTE] In scenari in cui i dispositivi sono controllati in modo più interattivo (Abilita la ventola da un'app controllata dall'utente), è consigliabile utilizzare i [metodi per il cloud per dispositivo][lnk-methods].

In questa esercitazione, le modifiche di back-end applicazione la configurazione di telemetria di un dispositivo di destinazione e, in seguito a che l'app dispositivo segue vari passaggi per applicare un aggiornamento della configurazione (ad esempio richiedere il riavvio modulo software), che in questa esercitazione simula con un ritardo semplice).

Il server back-end vengono archiviati la configurazione nelle proprietà desiderata di un doppio dispositivo nel modo seguente:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] Poiché le configurazioni possono essere oggetti complessi, in genere vengono assegnati ID univoci (gli hash o [GUID][lnk-guid]) per semplificare il confronto.

L'app dispositivo segnala la configurazione corrente mirroring la proprietà desiderata **telemetryConfig** nelle proprietà del segnalate:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Si noti come segnalate **telemetryConfig** è una proprietà aggiuntiva **stato**, utilizzato per indicare lo stato del processo di aggiornamento della configurazione.

Quando si riceve una nuova configurazione desiderata, app dispositivo segnala una configurazione in sospeso modificando le informazioni:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

In un secondo momento, app dispositivo segnalerà quindi, il successo di errore di questa operazione aggiornando le proprietà sopra elencate.
Si noti come back-end è possibile, in qualsiasi momento per interrogare lo stato del processo di configurazione tra tutti i dispositivi.

In questa esercitazione viene illustrato come per:

- Creare un dispositivo simulato per ricevere aggiornamenti configurazione dal back-end e report più aggiornamenti come *proprietà segnalate* nel processo di aggiornamento della configurazione.
- Creare un'app di back-end che consente di aggiornare la configurazione di un dispositivo desiderata e quindi interroga il processo di aggiornamento della configurazione.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier