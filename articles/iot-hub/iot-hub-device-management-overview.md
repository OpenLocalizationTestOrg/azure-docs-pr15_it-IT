<properties
 pageTitle="Panoramica della gestione di dispositivi IoT Hub | Microsoft Azure"
 description="In questo articolo viene fornita una panoramica di gestione dei dispositivi in Azure IoT Hub: enterprise ciclo di vita del dispositivo, riavviare il computer, reimpostare factory, aggiornamento del firmware, configurazione, due facce dispositivo, query, processi"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Panoramica della gestione dei dispositivi di Azure IoT Hub (preview)

## <a name="introduction"></a>Introduzione

IoT Hub Azure fornisce le caratteristiche e un modello di estensibilità che consentono agli sviluppatori di back-end di creare soluzioni per la gestione di dispositivi IoT efficaci e dispositivo. IoT dispositivi compreso sensori vincolati e MicroController singolo scopo, e potenti gateway che instradare le comunicazioni per i gruppi di dispositivi.  Inoltre, i casi di utilizzo e i requisiti per gli operatori IoT variano notevolmente tra settore.  Nonostante questa variante, gestione dei dispositivi di Azure IoT Hub offre funzionalità, modelli e librerie di codice, tenere in considerazione un set di dispositivi e utenti finali.

Una parte fondamentale di creazione di una soluzione di IoT per definire una strategia per come operatori di gestiscono la gestione continuativa della loro raccolta dei dispositivi aziendale ha esito positivo. Gli operatori IoT richiedono applicazioni che consentono loro di focalizzare l'attenzione aspetti più strategico del proprio lavoro e gli strumenti di semplici e affidabili. In questo articolo fornisce:

- Una breve panoramica delle approccio Azure IoT Hub per la gestione dei dispositivi.
- Descrizione del comuni principi di gestione di dispositivi.
- Descrizione del ciclo di vita del dispositivo.
- Panoramica dei modelli di gestione di dispositivi comuni.

## <a name="iot-device-management-principles"></a>Principi di gestione di dispositivi IoT

IoT offre un una serie di problemi legati alla gestione dei dispositivi e ogni soluzione aziendale deve soddisfare i principi seguenti:

![Azure IoT Hub dispositivo gestione principi elemento grafico][img-dm_principles]

- **Scala e automazione**: soluzioni IoT richiedono semplici strumenti che consentono di automatizzare le attività più comuni e attivare un relativamente al personale addetto gestire milioni di dispositivi. Base giornaliera, operatori prevedono gestire le operazioni del dispositivo in modalità remota, in blocco e solo ricevere una notifica quando si verificano problemi che richiedono attenzione diretta.

- **Apertura e la compatibilità**: ecosistema dei dispositivi di IoT è particolarmente diverso. Strumenti di gestione devono essere personalizzati in modo da una vasta gamma di classi di dispositivi, piattaforme e i protocolli. Operatori devono essere in grado di supportare molti tipi di dispositivi da più limitate processori processo singolo incorporate nel computer potenti e completamente funzionale.

- **Informazioni sulla presenza contesto**: IoT ambienti sono dinamiche e modificare mai. Affidabilità del servizio è fondamentale. Operazioni di gestione di dispositivi necessario suddividere il contratto di servizio di manutenzione, stati di rete e power, condizioni in uso e georilevazione dispositivo per assicurarsi che il periodo di inattività manutenzione non influiscono sulle operazioni aziendali importanti o creare condizioni pericolose.

- **Servizio diversi ruoli**: supporto per i flussi di lavoro univoci e processi dei ruoli di operazioni IoT è fondamentale. Lo staff deve lavorare harmoniously considerati i vincoli di reparti IT interni.  Sono anche necessario trovare modi sostenibili per informazioni sulle operazioni di dispositivo in tempo reale superficie superiori e altri ruoli gestionale business.

## <a name="iot-device-lifecycle"></a>Ciclo di vita del dispositivo IoT

Esiste un insieme di fasi di gestione dispositivi generali comuni a tutti i progetti IoT dell'organizzazione. In IoT Azure, sono disponibili cinque fasi all'interno del ciclo di vita di dispositivo IoT:

![Le fasi del ciclo di vita di cinque Azure IoT dispositivo: pianificare, eseguire il provisioning, configurare, monitorare, ritirare][img-device_lifecycle]

All'interno di ognuna di queste cinque fasi, esistono alcuni requisiti di operatore dispositivo che devono essere soddisfatte per fornire una soluzione completa:

- **Pianificare**: permettere agli operatori di creazione di una combinazione di metadati dispositivo che consente alle con facilità e precisione per la query e un gruppo di dispositivi per operazioni di gestione di massa di destinazione. È possibile utilizzare doppi dispositivo per archiviare metadati dispositivo sotto forma di tag e proprietà.

    *Ulteriori informazioni*: [Guida introduttiva a due facce dispositivo][lnk-twins-getstarted], [due facce dispositivo comprendere][lnk-twins-devguide], [come utilizzare le proprietà di un doppio][lnk-twin-properties]

- **Provisioning**: eseguire il provisioning di IoT Hub nuovi dispositivi e permettere agli operatori di immediatamente alla scoperta di funzionalità del dispositivo in modo sicuro.  Usare il Registro di sistema di dispositivo IoT Hub per creare le credenziali e identità dispositivo flessibili ed eseguire questa operazione in blocco tramite un processo. Creare dispositivi per segnalare le funzionalità e le condizioni tramite proprietà dei dispositivi in doppi dispositivo.

    *Ulteriori informazioni*: [gestire le identità dispositivo][lnk-identity-registry], [gestione di massa di identità dispositivo][lnk-bulk-identity], [come utilizzare le proprietà di un doppio][lnk-twin-properties]

- **Configura**: semplificare le modifiche alla configurazione in blocco e aggiornamenti firmware ai dispositivi mantenendo salute e sicurezza. Eseguire queste operazioni di gestione dispositivi in blocco tramite la proprietà desiderate o con metodi diretti e processi di trasmissione.

    *Ulteriori informazioni*: [utilizzare metodi diretti][lnk-c2d-methods], [richiama un metodo diretto in un dispositivo][lnk-methods-devguide], [come utilizzare le proprietà di un doppio][lnk-twin-properties], [pianificare e processi di trasmissione][lnk-jobs], [pianificare processi in più dispositivi][lnk-jobs-devguide]

- **Monitor**: monitorare complessiva dispositivo dell'integrità della raccolta, lo stato delle operazioni in corso, e avvisare gli operatori ai problemi che potrebbero richiedere attenzione.  Applicare doppi dispositivo per consentire ai dispositivi di condizioni di funzionamento in tempo reale di report e stato di operazioni di aggiornamento. Creare relazioni di tipo dashboard potenti che mostrano i problemi più immediati tramite dispositivo doppi query.

    *Ulteriori informazioni*: [le modalità di utilizzo doppi][lnk-twin-properties], [linguaggio di Query per due facce e processi][lnk-query-language]

- **Ritira**: sostituire o rimuovere le autorizzazioni di dispositivi dopo un errore, eseguire l'aggiornamento del ciclo, o alla fine della durata di servizio.  Utilizzare doppi dispositivo per gestire informazioni dispositivo se il dispositivo fisico viene sostituito o archiviate se viene utilizzato. Usare il Registro di sistema di dispositivo IoT Hub di revoca in modo sicuro le credenziali e identità di dispositivo.

    *Ulteriori informazioni*: [come usare le proprietà di un doppio][lnk-twin-properties], [gestire le identità dispositivo][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Modelli di gestione di dispositivi IoT Hub

IoT Hub Abilita il set di criteri di gestione dei dispositivi seguente.  [Esercitazioni di gestione di dispositivi] [ lnk-get-started] illustrano in modo più dettagliato come estendere questi modelli per adattarlo allo scenario esatto e sulla progettazione di nuovi modelli in base a questi modelli di base.

- **Riavviare** - applicazione back-end informa il dispositivo tramite un metodo diretto che è stata avviata riavviare il computer.  Il dispositivo usa il dispositivo doppi segnalati proprietà per aggiornare lo stato di riavvio del dispositivo.

    ![Gestione di dispositivi IoT Hub Azure riavviare grafico serie][img-reboot_pattern]

- **Reimpostare factory** - applicazione back-end informa il dispositivo tramite un metodo diretto che è stata avviata la reimpostazione factory.  Il dispositivo utilizzato doppi dispositivo segnalate proprietà per aggiornare la factory Reimposta stato del dispositivo.

    ![Azure factory di gestione di dispositivi IoT Hub Reimposta elemento grafico serie][img-facreset_pattern]

- **Configurazione** - applicazione back-end utilizza le proprietà di un doppio bene accolta dispositivo per configurare il software in esecuzione nel dispositivo.  Il dispositivo usa il dispositivo doppi segnalati proprietà da aggiornare lo stato di configurazione del dispositivo.

    ![Grafico di motivo di Azure IoT Hub dispositivo Gestione configurazione][img-config_pattern]

- **Aggiornamento del firmware** - applicazione back-end informa il dispositivo tramite un metodo diretto che è stata avviata un aggiornamento del firmware.  Il dispositivo invia un processo più passaggi per scaricare il pacchetto del firmware, applicare il pacchetto firmware e infine riconnettersi al servizio IoT Hub.  Durante il processo di mult passaggio, il dispositivo usa il dispositivo doppi segnalati proprietà per aggiornare l'avanzamento e lo stato del dispositivo.

    ![Azure firmware di gestione di dispositivi IoT Hub aggiornare grafico serie][img-fwupdate_pattern]

- **Segnalare l'avanzamento e stato** - applicazione back-end esegue query doppi dispositivo, un set di dispositivi, per segnalare lo stato e lo stato di avanzamento delle azioni in esecuzione nei dispositivi.

    ![Gestione di dispositivi IoT Hub Azure reporting grafico motivo lo stato di avanzamento e lo stato][img-report_progress_pattern]

## <a name="next-steps"></a>Passaggi successivi

È possibile utilizzare la funzionalità, modelli e librerie di codice che consente di gestione di dispositivi Azure IoT Hub, per creare applicazioni IoT che soddisfano i criteri di operatore IoT enterprise all'interno di ogni fase del ciclo di vita di dispositivo.

Per ulteriori informazioni sulle funzioni di Azure IoT Hub dispositivo, vedere la [Guida introduttiva a gestione di dispositivi Azure IoT Hub] [ lnk-get-started] esercitazione.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md