<properties
    pageTitle="Procedura dettagliata evento hub archivio Azure | Microsoft Azure"
    description="Esempio che utilizza Azure Python SDK per dimostrare mediante la funzione di archiviazione hub evento."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Procedura dettagliata hub archivio evento: Python

Evento hub archivio è una nuova caratteristica hub di eventi che consente di fornire automaticamente i dati di flusso l'Hub di eventi a un account di archiviazione Blob Azure preferito. In questo modo più semplice eseguire elaborazione batch nella dati flussi in tempo reale. In questo articolo viene descritto come utilizzare evento hub archivio con Python. Per ulteriori informazioni sull'evento hub archivio, vedere l' [articolo introduttivo](event-hubs-archive-overview.md).

In questo esempio viene usata Azure Python SDK per dimostrare utilizzando la funzione di archiviazione. Il sender.py invia simulato telemetria ambientale a un hub di evento in formato JSON. Hub evento è configurato per utilizzare la funzione di archiviazione per scrivere i dati per lo spazio di archiviazione in batch blob. Il archivereader.py quindi legge tali BLOB e crea un file di Accodamento per dispositivo e scrittura dei dati in file CSV.

Cosa viene effettuata

1.  Creare un account di archiviazione Blob Azure e un contenitore di blob suo interno, tramite il portale di Azure

2.  Creare uno spazio dei nomi di evento Hub, tramite il portale di Azure

3.  Creare un Hub di eventi con la funzione di archiviazione abilitata, tramite il portale di Azure

4.  Inviare dati a Hub evento con uno script Python

5.  Leggere i file dall'archivio e li diagrammi di processo con un altro script Python

Prerequisiti

1.  Python 2.7.x

2.  Una sottoscrizione di Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione Azure

1.  Accedere al [portale di Azure][].

2.  Nel riquadro di spostamento sinistro del portale, fare clic su Nuovo, quindi fare clic su dati + spazio di archiviazione e quindi fare clic su Account di archiviazione.

3.  Compilare i campi e il conto dello spazio di archiviazione e fare clic su **Crea**.

    ![][1]

4.  Dopo che viene visualizzato il messaggio **Distribuzioni ha avuto esito positivo** , fare clic su nuovo account di archiviazione ed e il **Nozioni di base** fare clic su **BLOB**. Quando si apre e il **servizio Blob** , fare clic su **+ contenitore** nella parte superiore. Assegnare un nome a tale contenitore **archivio**e quindi chiudere e il **servizio di Blob** .

5.  Fare clic sui **tasti di scelta** e il sinistro e copiare il nome dell'account di archiviazione e il valore di **chiave1**. Salvare questi valori nel blocco note o in un'altra posizione temporanea.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Creare uno script Python per inviare gli eventi per l'Hub di evento

1.  Aprire l'editor Python preferito, ad esempio [Codice Visual Studio][].

2.  Creare uno script chiamato **sender.py**. Questo script viene inviato 200 eventi l'Hub di evento. Sono semplici valori ambientale inviati in JSON.

3.  Incollare il codice seguente sender.py:

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Aggiornare il codice per usare il nome di spazio dei nomi e valori chiave che è stato acquistato al momento della creazione dello spazio dei nomi hub evento precedente.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Creare uno script Python per leggere i file di archivio

1.  Compilare e il e fare clic su **Crea**.

2.  Creare uno script chiamato **archivereader.py**. Questo script leggere i file di archivio e creare un file per dispositivo per scrivere i dati solo per il dispositivo.

3.  Incollare il codice seguente archivereader.py:

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Assicurarsi di incollare i valori appropriati per il nome dell'account di archiviazione e la chiave nella chiamata a `startProcessing`.

## <a name="run-the-scripts"></a>Eseguire gli script

1.  Aprire un prompt dei comandi con Python relativo percorso e quindi eseguire questi comandi per installare i pacchetti prerequisiti Python:

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Se si dispone di una versione precedente di uno spazio di archiviazione azure o azure potrebbe essere necessario usare il **-eseguire l'aggiornamento** opzione

    È anche possibile eseguire le operazioni seguenti (non necessario nella maggior parte dei sistemi):

    ```
    pip install cryptography
    ```

2.  Passare dalla directory in qualsiasi percorso è stato salvato sender.py e archivereader.py e il seguente comando:

    ```
    start python sender.py
    ```
    
    Verrà avviato un nuovo processo Python per eseguire il mittente.

3. A questo punto, attendere alcuni minuti per l'archivio per l'esecuzione. Nella finestra del comando originale quindi digitare il comando seguente:

    ```
    python archivereader.py
    ```

Questo processore archivio utilizza la directory locale per scaricare tutti i BLOB dall'account/contenitore di spazio di archiviazione. Elaborerà tutti quelli che non sono vuoti e scrivere i risultati come file con estensione csv nella directory locale.

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare ulteriori informazioni hub di evento, visitare i collegamenti seguenti:

- [Panoramica dell'evento hub archiviare][]
- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .
- [Cenni preliminari sui hub di eventi][]
 

[Portale di Azure]: https://portal.azure.com/
[Panoramica dell'evento hub archiviare]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Codice Visual Studio]: https://code.visualstudio.com/
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
