<properties
 pageTitle="Azure soluzioni per Internet di elementi | Microsoft Azure"
 description="Una panoramica delle IoT su Azure inclusi un'architettura di soluzione di esempio e le relazioni con gli IoT Hub Azure, SDK per dispositivi e le soluzioni preconfigurate"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Passaggi successivi

Azure IoT Hub è un servizio Azure consente sicura e attendibile bidirezionale le comunicazioni tra l'applicazione fine e milioni di dispositivi. Consente l'applicazione back-end:

- Ricevere telemetria scala dai dispositivi.
- La distribuzione dei dati dai dispositivi a un processore evento flusso.
- Ricevere il caricamento di file da dispositivi.
- Inviare i comandi cloud al dispositivo di dispositivi specifici.

È possibile utilizzare IoT Hub per implementare il proprio back-end soluzione. Inoltre, IoT Hub include un dispositivo identità del Registro di sistema usato per effettuare il provisioning di dispositivi, le proprie credenziali di sicurezza e diritti a cui connettersi hub. Per altre informazioni sull'IoT Hub, vedere [Novità IoT Hub?] [lnk-iot-hub].

Per informazioni su come IoT Hub Azure consente basate su standard di gestione di dispositivi IoT per poter gestire, configurare e aggiornare i dispositivi in modalità remota, vedere [gestione di dispositivi Panoramica di Azure IoT Hub][lnk-device-management].

Per implementare le applicazioni client su una vasta gamma di piattaforme di dispositivi hardware e sistemi operativi, è possibile usare il dispositivo IoT SDK. Il dispositivo IoT SDK includono librerie che semplificano telemetria invio a un hub IoT e ricevere comandi cloud al dispositivo. Quando si utilizza il SDK, è possibile scegliere da diversi protocolli di rete per comunicare con IoT Hub. Per ulteriori informazioni, vedere le [informazioni dispositivo SDK][lnk-device-sdks].

Per iniziare a scrivere codice e l'esecuzione di alcuni esempi, vedere la [Guida introduttiva a IoT Hub] [ lnk-getstarted] esercitazione.

Possono anche essere di interesse nella [Famiglia di prodotti IoT Azure][lnk-iot-suite], una raccolta di soluzioni preconfigurate. Famiglia di prodotti IoT consente di iniziare rapidamente e scalare IoT progetti per scenari comuni IoT, ad esempio il monitoraggio remoto, la gestione delle risorse e manutenzione previsione.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md