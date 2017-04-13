<properties
 pageTitle="Metrica diagnostica IoT Hub"
 description="Cenni preliminari metriche di Azure IoT Hub, consentendo agli utenti di valutare l'andamento delle risorse"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-diagnostic-metrics"></a>Introduzione alle metriche di diagnostiche

Metriche di diagnostiche per ottenere migliori dati sullo stato delle risorse di Azure nell'abbonamento Azure. Metriche consentono di valutare l'andamento del servizio e i dispositivi connessi a tale. Statistiche esposti all'utente sono importanti perché consentono di vedere qual è il problema con i problemi di causa principale IoT hub e la Guida senza che sia necessario contattare il supporto di Azure.

È possibile abilitare metriche di diagnostiche dal portale di Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Come abilitare metriche di diagnostiche

1. Creare un hub IoT. È possibile trovare istruzioni su come creare un hub IoT nella [Guida introduttiva] [ lnk-get-started] Guida.

2. Aprire e il dell'hub IoT. A questo punto, fare clic su **diagnostica**.

    ![][1]

3. Configurare la diagnostica, impostare lo stato **in** e selezionando un account di archiviazione Azure per archiviare i dati di diagnostica. Verificare **le metriche**e quindi premere **Salva**. Si noti che è necessario creare l'account di archiviazione Azure anticipo e che addebitate separatamente per lo spazio di archiviazione. È anche possibile inviare i dati di diagnostica a un evento hub endpoint.

    ![][2]

4. Dopo avere configurato la diagnostica, tornare al e hub IoT **Panoramica** . Informazioni metriche viene popolate nella sezione della stessa e **monitoraggio** . Fare clic sul grafico apre il riquadro metriche nel punto in cui è possibile visualizzare un riepilogo delle informazioni metriche per il tuo fulcro IoT e modificare la selezione di metriche visualizzate nel grafico. È inoltre possibile configurare avvisi in base ai valori metrici.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Metrica e sul loro utilizzo

IoT Hub offre diverse metriche per ottenere una panoramica dell'integrità di un centro di raccolta e il numero totale di dispositivi connessi a tale. È possibile combinare le informazioni provenienti da più metriche per disegnare un'immagine più grande dello stato di hub IoT. Nella tabella seguente descrive le metriche tiene traccia di ogni hub IoT e la relazione tra ogni unità di misura metriche e lo stato generale dell'hub IoT.

| Unità di misura metriche | Descrizione metriche | Quali la metrica viene utilizzata per |
| ---- | ---- | ---- |
| d2c.telemetry.Ingress.allProtocol | Il numero di messaggi inviati da tutti i dispositivi | Dati di panoramica sul Invia messaggio |
| d2c.telemetry.Ingress.Success | Il conteggio di tutti i messaggi completati all'hub | Panoramica di ingresso corretta del messaggio all'hub |
| c2d.Commands.egress.complete.Success | Il conteggio di tutti i messaggi di comando completata entro il dispositivo di ricezione da tutti i dispositivi | Insieme metriche abandon e rifiuta, viene fornita una panoramica di velocità C2D di successo di comando |
| c2d.Commands.egress.Abandon.Success | Il conteggio di tutti i messaggi chiusa correttamente per il dispositivo di ricezione da tutti i dispositivi | Se i messaggi sono Guida chiusa più spesso del previsto in evidenza potenziali problemi |
| c2d.Commands.egress.Reject.Success | Il conteggio di tutti i messaggi correttamente rifiutato dal dispositivo di ricezione da tutti i dispositivi | Se i messaggi vengono Guida respinti più spesso del previsto in evidenza potenziali problemi |
| devices.totalDevices | La media, min e max del numero di dispositivi registrati a hub IoT | Il numero di dispositivi registrati per hub |
| devices.connectedDevices.allProtocol | La media, min e max del numero di dispositivi connessi simultanei | Panoramica del numero di dispositivi connessi alla hub |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato una panoramica di diagnostiche metriche, seguire questo collegamento per ulteriori informazioni sulla gestione di Azure IoT Hub:

- [Monitoraggio delle operazioni][lnk-monitor]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
