<properties
    pageTitle="Personalizzazione preconfigurato soluzioni | Microsoft Azure"
    description="Vengono fornite indicazioni su come personalizzare le soluzioni di Azure IoT Suite preconfigurato."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Personalizzare una soluzione preconfigurata

Le soluzioni preconfigurate fornite con la famiglia IoT Azure illustrano i servizi all'interno del gruppo lavora insieme per fornire una soluzione-to-end. A questo punto di partenza, sono disponibili una varietà di posizioni in cui è possibile estendere e personalizzare la soluzione per scenari specifici. Le sezioni seguenti descrivono questi punti di personalizzazione comuni.

## <a name="finding-the-source-code"></a>Trovare il codice sorgente

Il codice sorgente per le soluzioni preconfigurate è disponibile in GitHub disponibili negli archivi seguenti:

- Monitoraggio remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Stima manutenzione: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Il codice sorgente per le soluzioni preconfigurate è fornito per illustrare i modelli e procedure consigliate utilizzate per implementare funzionalità-to-end di una soluzione IoT utilizzando Azure IoT Suite. È possibile trovare altre informazioni su come creare e distribuire le soluzioni disponibili negli archivi GitHub.

## <a name="changing-the-preconfigured-rules"></a>Modificare le regole preconfigurate

Soluzione di monitoraggio remoto include tre processi di [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/) per implementare la logica di regole, di informazioni e di telemetria dispositivo visualizzata per la soluzione.

I processi di analitica tre flusso e della relativa sintassi viene descritta in modo approfondito in [remoto il monitoraggio di questa procedura dettagliata soluzione preconfigurato](iot-suite-remote-monitoring-sample-walkthrough.md). 

È possibile modificare questi processi direttamente per modificare la logica o aggiungere logica specifica al proprio scenario. È possibile trovare i processi di flusso Analitica come indicato di seguito:
 
1. Accedere al [portale Azure](https://portal.azure.com).
2. Passare al gruppo di risorse con lo stesso nome soluzione IoT. 
3. Selezionare il processo di Azure flusso Analitica che si desidera modificare. 
4. Interrompere il processo selezionando **interrompere**l'insieme di comandi. 
5. Modifica di input, query e output.

    Una semplice modifica consiste nel modificare la query per il processo di **regole** di utilizzare un **"<"** invece di un **">"**. Portale soluzione verrà comunque visualizzato **">"** quando si modifica una regola, ma si noterà che il comportamento viene capovolta a causa la modifica nel processo sottostante.

6. Avviare il processo

> [AZURE.NOTE] Dashboard di monitoraggio remoto dipende da dati specifici, in modo che modifica i processi può causare il dashboard per avere esito negativo.

## <a name="adding-your-own-rules"></a>Aggiunta di regole personalizzate

Oltre a modificare i processi di Azure flusso Analitica preconfigurati, è possibile utilizzare il portale di Azure per aggiungere nuovi processi o aggiungere nuove query ai processi esistenti.

## <a name="customizing-devices"></a>Personalizzazione dei dispositivi

Una delle attività più comuni di estensione sta collaborando con dispositivi specifici per il proprio scenario. Sono disponibili diversi metodi per l'uso con i dispositivi. Questi metodi includono la modifica di un dispositivo simulato in modo che corrisponda proprio scenario o utilizzo [IoT dispositivo SDK][] per connettere il dispositivo fisico alla soluzione.

Per una Guida dettagliata all'aggiunta di dispositivi alla soluzione preconfigurata monitoraggio remota, vedere [Iot Suite connessione di dispositivi](iot-suite-connecting-devices.md) e il [Campione di SDK C monitoraggio remoto](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) è progettato per funzionare con la soluzione preconfigurata monitoraggio remota.

### <a name="creating-your-own-simulated-device"></a>Creare una simulata dispositivo

Incluso nel remote monitoring soluzione codice sorgente (sopracitato), è un simulatore di .NET. Questo simulatore uno provisioning come parte della soluzione e può essere modificato per inviare metadati diversi, telemetria o rispondere a comandi diversi.

Simulatore preconfigurato della soluzione preconfigurato monitoraggio remoto è un dispositivo più fredda che genera telemetria temperatura e umidità, è possibile modificare il simulatore nel progetto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando è stata duplicata repository GitHub.

### <a name="available-locations-for-simulated-devices"></a>Posizioni disponibili per i dispositivi simulati

Il set di percorsi predefinito è Seattle/Redmond, Washington e degli Stati Uniti. È possibile modificare questi percorsi in [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Creazione e utilizzo dispositivo (fisico)

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) fornisce raccolte per la connessione numerosi tipi di dispositivo (lingue e sistemi operativi) in IoT soluzioni.

## <a name="modifying-dashboard-limits"></a>La modifica dei limiti di dashboard

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Numero di dispositivi visualizzati nell'elenco a discesa del dashboard

Il valore predefinito è 200. È possibile modificare il numero di [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Numero di pin da visualizzare nel controllo mappa di Bing

Il valore predefinito è 200. È possibile modificare il numero di [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Periodo di tempo del grafico di telemetria

Il valore predefinito è 10 minuti. È possibile modificarlo in [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configurare manualmente i ruoli applicazione

La procedura seguente viene descritto come aggiungere i ruoli di applicazione di **amministratore** e di **sola lettura** per una soluzione preconfigurata. Si noti che le soluzioni preconfigurate viene completato il provisioning del sito azureiotsuite.com già includono i ruoli di **amministratore** e di **sola lettura** .

I membri del ruolo di **sola lettura** è possono visualizzare il dashboard e l'elenco dei dispositivi, ma non sono consentiti per aggiungere dispositivi, modificare gli attributi di dispositivo o inviare comandi.  I membri del ruolo **amministratore** hanno accesso completo a tutte le funzionalità della soluzione.

1. Passare al [portale classica Azure][lnk-classic-portal].

2. Selezionare **Active Directory**.

3. Fare clic sul nome del tenant AAD che è utilizzato quando viene completato il provisioning della soluzione.

4. Fare clic su **applicazioni**.

5. Fare clic sul nome dell'applicazione che corrisponde al nome del soluzione preconfigurato. Se non è visibile l'applicazione nell'elenco, selezionare **applicazioni appartiene alla società** nella **visualizzazione** elenco a discesa e fare clic sul segno di spunta.

6.  Nella parte inferiore della pagina, fare clic su **Gestisci manifesto** e **Scaricare manifesto**.

7. Consente di scaricare un file .json nel computer locale.  Aprire il file per modificarlo in un editor di testo di propria scelta.

8. Nella terza riga del file .json, si noterà che:

  ```
  "appRoles" : [],
  ```
  Sostituire con le operazioni seguenti:

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Salvare il file aggiornato .json (è possibile sovrascrivere il file esistente).

10.  Nel portale di gestione di Azure, nella parte inferiore della pagina, selezionare **Gestisci manifesto** quindi **Caricare manifesto** per caricare il file .json che è stato salvato nel passaggio precedente.

11. È stato aggiunto i ruoli di **amministratore** e di **sola lettura** per l'applicazione.

12. Per assegnare uno di questi ruoli a un utente nella directory, vedere [le autorizzazioni nel sito azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Commenti e suggerimenti

Si dispone di una personalizzazione si preferisce visualizzare coperte in questo documento? Aggiungere suggerimenti sulle funzionalità [Vocali di utente](https://feedback.azure.com/forums/321918-azure-iot)o il commento in seguito in questo articolo. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni per la personalizzazione di soluzioni preconfigurate, vedere:

- [Connessione logica App alla soluzione Azure IoT Suite monitoraggio remoto preconfigurato][lnk-logicapp]
- [Utilizzo di telemetria dinamica con monitoraggio remoto preconfigurato soluzione][lnk-dynamic]
- [Informazioni i metadati della soluzione preconfigurato monitoraggio remoto][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[Dispositivo IoT SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
