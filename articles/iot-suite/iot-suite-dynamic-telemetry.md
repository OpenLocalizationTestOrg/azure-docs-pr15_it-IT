<properties
    pageTitle="Utilizzare telemetria dinamico | Microsoft Azure"
    description="Seguire questa esercitazione per imparare a usare telemetria dinamica con la soluzione preconfigurata monitoraggio remota."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Utilizzo di telemetria dinamica con monitoraggio remoto preconfigurato soluzione

## <a name="introduction"></a>Introduzione

Telemetria dinamica consente di visualizzare qualsiasi telemetria inviato alla soluzione preconfigurata monitoraggio remota. I dispositivi simulati distribuire la soluzione preconfigurato inviano telemetria temperatura e umidità, è possibile visualizzare nel dashboard. Se si personalizzano i dispositivi simulati esistenti, creare nuovi dispositivi simulati o connettere dispositivi fisici alla soluzione preconfigurata è possibile inviare altri valori di telemetria, ad esempio la temperatura esterna, RPM o velocità del vento. È quindi possibile visualizzare questa telemetria aggiuntiva nel dashboard.

In questa esercitazione utilizza un dispositivo di simulata Node semplice che è possibile modificare facilmente per sperimentare telemetria dinamico.

Per completare questa esercitazione, è necessario:

- Un abbonamento attivo a Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure][lnk_free_trial].
- [Node] [ lnk-node] versione 0.12.x o versione successiva.

È possibile completare questa esercitazione dal sistema operativo, ad esempio Windows o Linux, in cui è possibile installare Node.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configurare il dispositivo simulato Node

1. Nel dashboard di monitoraggio remoto, fare clic su **+ Aggiungi un dispositivo** e quindi aggiungere un dispositivo personalizzato. Prendere nota dell'IoT Hub hostname, id dispositivo e chiave dispositivo. È necessario più avanti in questa esercitazione quando si prepara l'applicazione client di dispositivo remote_monitoring.js.

2. Assicurarsi che la versione Node 0.12.x o versioni successive è installato nel computer di sviluppo. Eseguire `node --version` al prompt dei comandi o in una shell per verificare la versione. Per informazioni sull'utilizzo di un manager pacchetto per installare Node su Linux, vedere [Installazione Node tramite Gestione pacchetti][node-linux].

3. Dopo aver installato Node, duplicare l'ultima versione di [SDK di iot azure] [ lnk-github-repo] repository al computer di sviluppo. Usa sempre ramo **master** per la versione più recente della raccolte e gli esempi.

4. La copia locale del [SDK di iot azure] [ lnk-github-repo] repository, copiare le seguenti due file dalla cartella nodo / / esempi relativi ai dispositivi in una cartella vuota nel computer di sviluppo:

  - Packages.JSON
  - remote_monitoring.js

5. Aprire il file remote_monitoring.js e cercare la definizione della variabile seguente:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Sostituire **[stringa di connessione dispositivo IoT Hub]** con la stringa di connessione di dispositivi. Utilizzare i valori per nome host IoT Hub, id dispositivo e chiave dispositivo che si sia preso una nota nel passaggio 1. Una stringa di connessione di dispositivi è nel formato seguente:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se il nome host IoT Hub è **contoso** e l'id di dispositivo è **mydevice**, la stringa di connessione è simile alla seguente:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Salvare il file. Eseguire i comandi seguenti in una shell o prompt dei comandi nella cartella contenente i file per installare i pacchetti necessari e quindi eseguire l'applicazione di esempio:

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Osservare telemetria dinamica in azione

Dashboard di Mostra telemetria temperatura e umidità dei dispositivi di simulata esistenti:

![Il dashboard predefinito][image1]

Se si seleziona il dispositivo simulato Node che è stato eseguito nella sezione precedente, vedere temperatura, umidità e telemetria temperatura esterna:

![Aggiungere temperatura esterna al dashboard][image2]

Soluzione di monitoraggio remoto automaticamente rileva il tipo di telemetria aggiuntive temperatura esterni e lo aggiunge al grafico nel dashboard di.

## <a name="add-a-telemetry-type"></a>Aggiungere un tipo di telemetria

Il passaggio successivo consiste nel sostituire telemetria generata dal dispositivo simulato Node con un nuovo set di valori:

1. Interrompere il dispositivo simulato Node digitando **Ctrl + C** nel prompt dei comandi o shell.

2. Nel file remote_monitoring.js, è possibile visualizzare i valori di dati di base per la temperatura esistente, umidità e telemetria temperatura esterna. Aggiungere un valore di dati di base per **rpm** come segue:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Il dispositivo simulato Node utilizza la funzione **generateRandomIncrement** nel file remote_monitoring.js per aggiungere un incremento casuale per i valori di dati di base. Casuale il valore di **rpm** mediante l'aggiunta di una riga di codice dopo randomizations esistente come indicato di seguito:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Aggiungere il nuovo valore rpm al payload JSON che il dispositivo invia a IoT Hub:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Eseguire il dispositivo simulato Node utilizzando il comando seguente:

    ```
    node remote_monitoring.js
    ```

6. Osservare il nuovo tipo di telemetria RPM che vengono visualizzate nel grafico nel dashboard:

![Aggiungere RPM al dashboard][image3]

> [AZURE.NOTE] Potrebbe essere necessario disattivare e quindi attivare il dispositivo node nella pagina **dispositivi** nel dashboard di vedere immediatamente le modifiche.

## <a name="customize-the-dashboard-display"></a>Personalizzare la visualizzazione di dashboard

Il messaggio **Informazioni dispositivo** può includere metadati relativi telemetria che il dispositivo può inviare messaggi a IoT Hub. Metadati è possono specificare i tipi di telemetria che invia il dispositivo. Modificare il valore di **deviceMetaData** nel file remote_monitoring.js per includere una definizione di **telemetria** dopo la definizione di **comandi** . Frammento di codice seguente viene illustrata la definizione di **comandi** (assicurarsi di aggiungere una `,` dopo la definizione di **comandi** ):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] Soluzione di monitoraggio remoto utilizza una distinzione tra maiuscole per confrontare la definizione di metadati con i dati all'interno del flusso di telemetria.

Aggiunta di una definizione di **telemetria** come illustrato nel frammento di codice precedente non modificare il comportamento del dashboard. Tuttavia, i metadati possono includere anche un attributo **DisplayName** per personalizzare la visualizzazione del dashboard di. Aggiornare la definizione di metadati di **telemetria** come illustrato nel frammento di codice seguente:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

La schermata seguente mostra come questa modifica consente di modificare la legenda del grafico nel dashboard:

![Personalizzare la legenda del grafico][image4]

> [AZURE.NOTE] Potrebbe essere necessario disattivare e quindi attivare il dispositivo node nella pagina **dispositivi** nel dashboard di vedere immediatamente le modifiche.

## <a name="filter-the-telemetry-types"></a>Filtrare i tipi di telemetria

Per impostazione predefinita, il grafico nel dashboard di Mostra tutte le serie di dati all'interno del flusso di telemetria. Per disattivare la visualizzazione dei tipi di telemetria specifiche del grafico, è possibile utilizzare i metadati di **Informazioni sul dispositivo** . 

Per rendere il grafico mostra solo telemetria temperatura e umidità, omettere **ExternalTemperature** dai metadati di **telemetria** **Informazioni dispositivo** come indicato di seguito:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

La **Temperatura all'aria aperta** non viene più visualizzato nel grafico:

![Filtrare telemetria nel dashboard][image5]

Questa modifica influisce solo la visualizzazione del grafico. I valori dei dati **ExternalTemperature** vengono comunque memorizzati e resi disponibili per le elaborazioni back-end.

> [AZURE.NOTE] Potrebbe essere necessario disattivare e quindi attivare il dispositivo node nella pagina **dispositivi** nel dashboard di vedere immediatamente le modifiche.

## <a name="handle-errors"></a>Gestione degli errori

Per un flusso di dati da visualizzare nel grafico, il **tipo** di metadati **Info dispositivo** deve corrispondere al tipo di dati dei valori di telemetria. Ad esempio, se i metadati specificano che il **tipo** di dati di umidità è **int** e **double** si trova all'interno del flusso di telemetria quindi telemetria umidità non vengono visualizzati nel grafico. Tuttavia, i valori di **umidità** sono comunque memorizzati e resi disponibili per le elaborazioni back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato come usare telemetria dinamico, per ulteriori informazioni sull'utilizzano delle informazioni sul dispositivo le soluzioni preconfigurate: [preconfigurato soluzione di monitoraggio dei metadati informazioni dispositivo in remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
