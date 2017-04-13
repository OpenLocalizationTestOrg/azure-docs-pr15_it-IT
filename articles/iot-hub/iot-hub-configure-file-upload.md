<properties
     pageTitle="Usare il portale di Azure per configurare il caricamento di file | Microsoft Azure"
     description="Una panoramica su come configurare il caricamento di file tramite il portale di Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurare il caricamento di file tramite il portale di Azure

## <a name="file-upload"></a>Caricamento di file

Usare la [funzionalità di IoT Hub di caricamento di file][lnk-upload], è necessario associare un account di archiviazione Azure con il tuo fulcro. Selezionare le impostazioni di **caricamento File** per visualizzare un elenco delle proprietà di caricamento di file per l'hub IoT da modificare.

![][13]

**Contenitore di spazio di archiviazione**: usare il portale di Azure consente di selezionare un contenitore di blob in un account di archiviazione Azure nell'abbonamento a Azure corrente da associare il tuo fulcro IoT. Se necessario, è possibile creare un account di archiviazione Azure sul contenitore **gli account di archiviazione** blob e blade in e il **contenitori** . IoT Hub genera automaticamente SA URI con autorizzazioni di scrittura per questo contenitore di blob per i dispositivi da utilizzare quando si caricano i file.

![][14]

**Ricevere notifiche per i file caricati**: abilitare o disabilitare le notifiche di caricamento di file tramite l'interruttore.

**TTL SA**: questa impostazione è il tempo-to-live degli URI SA restituiti il dispositivo da IoT Hub. Per impostazione predefinita in un'ora, ma può essere personalizzato per gli altri valori utilizzando il dispositivo di scorrimento.

**Notifica di file impostazioni TTL predefinito**: il tempo-to-live di un file di notifica upload prima è scaduto. Impostare un giorno per impostazione predefinita, ma può essere personalizzato per gli altri valori utilizzando il dispositivo di scorrimento.

**Numero massimo di recapito notifica file**: il numero di volte IoT Hub tenta di inviare un file di notifica upload. Impostare a 10 per impostazione predefinita, ma può essere personalizzato per gli altri valori utilizzando il dispositivo di scorrimento.

![][15]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di caricamento di file di IoT Hub, vedere [caricare i file da un dispositivo] [ lnk-upload] nella Guida gli sviluppatori.

Seguire questi collegamenti per ulteriori informazioni sulla gestione di Azure IoT Hub:

- [In blocco gestire dispositivi IoT][lnk-bulk]
- [Metriche di utilizzo][lnk-metrics]
- [Monitoraggio delle operazioni][lnk-monitor]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]
- [Proteggere la soluzione IoT da zero backup][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md