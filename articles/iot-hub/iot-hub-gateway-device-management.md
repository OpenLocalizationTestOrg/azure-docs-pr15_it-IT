<properties
 pageTitle="Abilitare dispositivi gestiti dietro un gateway IoT | Microsoft Azure"
 description="Argomento Guida utilizzando un IoT Gateway creati mediante il SDK di Gateway IoT insieme dispositivi gestiti da IoT Hub."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Abilitare dispositivi gestiti dietro un gateway IoT

## <a name="basic-device-isolation"></a>Isolamento di dispositivi di base

Organizzazioni utilizzano spesso IoT gateway per aumentare la sicurezza delle soluzioni IoT globale. Alcuni dispositivi necessario inviare i dati nel cloud, ma non sono in grado di proteggersi dai rischi su internet. È possibile evitare questi dispositivi da thread esterni la comunicazione con l'esterno tramite un gateway.

Il gateway si trova sul bordo tra un ambiente protetto e internet aperto. Comunicare con i dispositivi del gateway e il gateway passa i messaggi lungo alla destinazione corretta cloud. Il gateway è protezione avanzato rispetto a thread esterni, blocca le richieste non autorizzate, consente il traffico in ingresso autorizzato e inoltra il traffico in ingresso al dispositivo corretto.

![][1]

È inoltre possibile inserire dispositivi che è possano proteggersi dietro un gateway per un nuovo livello di protezione. In questo scenario è necessario mantenere il gateway OS corretto per i problemi di protezione più recenti, invece di aggiornare il sistema operativo su qualsiasi dispositivo.

## <a name="isolation-plus-intelligence"></a>Isolamento plus business intelligence

Protezione avanzato router è un gateway sufficiente per semplicemente isolare dispositivi. Tuttavia, soluzioni IoT richiedono spesso che un gateway fornisce più lunghe di isolamento semplicemente di dispositivi. È consigliabile, ad esempio, per gestire i dispositivi dal cloud. Questo punto è possibile utilizzare [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), un protocollo di gestione di dispositivi standard per la parte di gestione cloud della soluzione. Tuttavia, i dispositivi invia telemetria usando un TCP/IP non abilitato protocollo. Inoltre, i dispositivi di prodotti agricoli grandi quantità di dati e si desidera caricare un sottoinsieme filtrato di telemetria. È possibile creare una soluzione che comprende un gateway IoT in grado di gestire con due flussi distinti di dati. Il gateway deve:

-   Comprendere **telemetria**, filtro e quindi caricarlo nel cloud tramite il gateway. Il gateway non è più semplice router che inoltra semplicemente dati tra il dispositivo e nel cloud.

-   È sufficiente scambiare i **dati di gestione di dispositivi LWM2M** tra i dispositivi e nel cloud. Il gateway non è necessario conoscere i dati provenienti al suo interno e sufficiente verificare che i dati vengono passati avanti e indietro tra i dispositivi e nel cloud.

La figura seguente illustra questo scenario:

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>Soluzione: gestione di dispositivi IoT Azure e IoT Gateway SDK 

Il pubblico anteprima rilascio di [gestione di dispositivi Azure IoT] [ lnk-device-management] e versione beta di [Azure IoT Gateway SDK] abilitare questo scenario. Il gateway gestisce ogni flusso di dati come segue:

-   **Telemetria**: È possibile utilizzare SDK Gateway IoT per creare una pipeline che comprende, filtri e invia dati di telemetria nel cloud. SDK di Gateway IoT fornisce codice di implementazione parti di questa pipeline per conto di sviluppo. Sono disponibili ulteriori informazioni sull'architettura di SDK [IoT Gateway SDK - Guida introduttiva] [ lnk-gateway-get-started] esercitazione.

-   **Gestione di dispositivi**: gestione di dispositivi Azure fornisce un client LWM2M che viene eseguita nel dispositivo, nonché un'interfaccia cloud per inviare i comandi di gestione al dispositivo.
    
    Perché non è necessario elaborare i dati LWM2M scambiati tra il dispositivo e il tuo fulcro IoT non è richiesta una logica speciale nel gateway. È possibile abilitare la condivisione, connessione internet una funzionalità di molti sistemi operativi moderni, nel gateway per consentire lo scambio di dati LWM2M. È possibile scegliere un sistema operativo appropriato per questo scenario perché SDK di Gateway IoT supporta una varietà di sistemi operativi. Ecco le istruzioni per l'attivazione di Condivisione connessione internet in [Windows 10] e [Ubuntu], due dei numerosi sistemi operativi supportati.

L'illustrazione seguente mostra architettura di alto livello usato per abilitare questo scenario mediante [la gestione dei dispositivi Azure IoT] [ lnk-device-management] e [Azure IoT Gateway SDK].

![][3]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare SDK di Gateway IoT, vedere le esercitazioni seguenti:

- [IoT Gateway SDK - iniziare a utilizzare Linux][lnk-gateway-get-started]
- [IoT Gateway SDK: inviare messaggi di dispositivo nel cloud con un dispositivo simulato con Linux][lnk-gateway-simulated]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Gateway IoT Azure SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md