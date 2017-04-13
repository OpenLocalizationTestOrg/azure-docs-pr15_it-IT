<properties
 pageTitle="Leggere i messaggi persistente in archiviazione Azure | Microsoft Azure"
 description="Monitorare i messaggi di dispositivo nel cloud quando vengono scritti allo spazio di archiviazione tabelle Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="33-read-messages-persisted-in-azure-storage"></a>3.3 messaggi letti persistente in archiviazione Azure

## <a name="331-what-will-you-do"></a>3.3.1 cosa fare

Monitorare i dispositivo nel cloud i messaggi inviati dai 3 di pi greco Raspberry per il tuo fulcro IoT come i messaggi vengono scritti allo spazio di archiviazione tabelle Azure. Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="332-what-will-you-learn"></a>3.3.2 informazioni in

- Come usare l'attività di lettura messaggio confessano per leggere i messaggi persistente in archiviazione tabelle Azure.

## <a name="333-what-do-you-need"></a>3.3.3 cosa occorre

- È necessario che sia completata la sezione precedente, [eseguire l'applicazione di esempio intermittenza Azure sui 3 di pi greco Raspberry](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

## <a name="334-read-new-messages-from-your-storage-account"></a>3.3.4 leggere i nuovi messaggi dall'account di archiviazione

Nella sezione precedente è stato eseguito un'applicazione di esempio con il pi greco. I messaggi di applicazione inviato di esempio per l'hub di Azure IoT. I messaggi inviati a un centro di raccolta IoT sono archiviati nello spazio di archiviazione tabelle Azure tramite l'app funzione Azure. È necessario la stringa di connessione di archiviazione Azure per leggere i messaggi dall'archivio tabelle Azure.

Per leggere i messaggi archiviati in archiviazione tabelle Azure, procedere come segue:

1. Visualizzare la stringa di connessione eseguendo i comandi seguenti:

    ```bash
    az storage account list -g iot-sample --query [].name
    az storage account show-connection-string -g iot-sample -n {storage name}
    ```

    Il primo comando Recupera il `storage name` che viene utilizzato il comando secondo per ottenere la stringa di connessione. `iot-sample`è il valore predefinito di `{resource group name}` se non è stato modificato il valore della lezione 2.

2. Aprire il file di configurazione `config-raspberrypi.json` file in Visual Studio codice eseguendo il comando seguente:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Sostituire `[Azure Storage connection string]` con la stringa di connessione ottenuto nel passaggio 1.
4. Salvare la `config-raspberrypi.json` file.
5. Inviare di nuovo i messaggi e leggerle dall'archivio tabelle Azure eseguendo il comando seguente:

    ```bash
    gulp run --read-storage
    ```

    È la logica per la lettura dall'archivio tabelle Azure il `azure-table.js` file.

    ![confessano eseguita lettura spazio di archiviazione](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="335-summary"></a>3.3.5 riepilogo

Correttamente aver connesso il Pi l'hub IoT nel cloud e utilizzare l'applicazione di esempio intermittenza per inviare messaggi di dispositivo nel cloud. Anche l'app funzione Azure viene usato per archiviare i messaggi in arrivo hub IoT allo spazio di archiviazione tabelle Azure. È possibile spostare in con la lezione successiva su come inviare messaggi cloud per dispositivo dall'hub IoT per il pi greco.

## <a name="next-steps"></a>Passaggi successivi

[Lezione 4: Invio di messaggi cloud al dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)
