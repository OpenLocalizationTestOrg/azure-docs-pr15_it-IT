<properties
 pageTitle="Gli argomenti della Guida per sviluppatori per IoT Hub | Microsoft Azure"
 description="Guida di sviluppo Azure IoT Hub che include i punti finali IoT Hub, sicurezza, del Registro di sistema di dispositivo identità, gestione dei dispositivi e messaggistica"
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

# <a name="azure-iot-hub-developer-guide"></a>Guida di sviluppo IoT Hub Azure

Azure IoT Hub è un servizio completamente gestito che consente di attivare comunicazioni bidirezionale sicura e affidabile tra milioni di dispositivi IoT e la fine di un'applicazione di nuovo.

IoT Hub Azure offre con:

* Proteggere le comunicazioni con le credenziali di sicurezza per dispositivo e controllo di accesso.
* Dispositivo nel cloud e cloud per dispositivo hyper scala messaggistica affidabile.
* Connettività dei dispositivi facilmente con le raccolte di dispositivo per le lingue e piattaforme più popolari.

Questa Guida per sviluppatori IoT Hub include gli articoli seguenti:

- [Inviare e ricevere messaggi con IoT Hub] [ devguide-messaging] descrive le funzionalità di messaggistica (dispositivo nel cloud e cloud per dispositivo) esposti IoT Hub. L'articolo include anche informazioni su argomenti, ad esempio i formati dei messaggi e i protocolli di comunicazione supportata e i numeri di porta che usino.
- [Caricare i file da un dispositivo] [ devguide-upload] descrive come è possibile caricare i file da un dispositivo. L'articolo include anche informazioni su argomenti, ad esempio le notifiche di possibile inviare il processo di uplaod.
- [Gestione delle identità dispositivo hub IoT] [ devguide-identities] vengono descritte le operazioni Archivia le informazioni registro di sistema dell'hub ogni IoT dispositivo identità e come è possibile accedere e modificarlo.
- [Controllare l'accesso a IoT Hub] [ devguide-security] viene descritto il modello di sicurezza utilizzato per concedere l'accesso alle funzionalità IoT Hub per entrambi i dispositivi e cloud componenti. L'articolo include informazioni sull'utilizzo di token e certificati x. 509 e dettagli delle autorizzazioni che è possibile concedere.
- [Utilizzare due facce di dispositivo per sincronizzare stato e configurazioni] [ devguide-device-twins] descrive il concetto di *doppi dispositivo* e funzionalità esposti, ad esempio la sincronizzazione di un dispositivo con la disponibilità di una copia. L'articolo include informazioni relative ai dati archiviati in un doppi dispositivo.
- [Richiamare un metodo diretto in un dispositivo] [ devguide-directmethods] descrive il ciclo di vita di un metodo diretto, informazioni su come richiamare metodi in un dispositivo dell'App back-end e gestire il metodo diretto nel dispositivo.
- [Pianificare processi in più dispositivi] [ devguide-jobs] descrive come è possibile pianificare processi in più dispositivi. L'articolo viene descritto come inviare i processi che eseguono attività come l'esecuzione di un metodo diretto, l'aggiornamento di un devcie utilizzando un doppi devcie. Viene inoltre descritto per lo stato di un processo di query.
- [Guida di riferimento - endpoint IoT Hub] [ devguide-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime. L'articolo vengono illustrati anche come è possibile utilizzare un gateway di campo per abilitare alcuni dispositivi a cui connettersi gli endpoint IoT Hub.
- [Riferimento: linguaggio di query per due facce, metodi e processi] [ devguide-query] descrive il linguaggio di query che consente di recuperare le informazioni dal tuo fulcro relativi ai due facce dispositivo e processi.
- [Guida di riferimento - quote e limitazioni] [ devguide-quotas] sono riepilogate le quote impostate nel servizio IoT Hub e il comportamento di limitazione dovrebbe essere visualizzato quando si supera la quota.
- [Guida di riferimento - dispositivo e servizio SDK] [ devguide-sdks] elenchi SDK è possibile utilizzare sviluppano applicazioni dispositivi e servizi che interagiscono con il tuo fulcro IoT. L'articolo include collegamenti a documentazione API online.
- [Guida di riferimento - supporto IoT Hub MQTT] [ devguide-mqtt] fornisce informazioni dettagliate su come IoT Hub supporta il protocollo MQTT. L'articolo viene descritto il supporto per il protocollo MQTT incorporato per gli SDK e fornisce informazioni sull'utilizzo del protocollo MQTT direttamente.
- [Glossario di] [ devguide-glossary] un elenco di termini correlati IoT Hub comuni.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

