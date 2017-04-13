<properties 
   pageTitle="Informare gli utenti di dati ricevuti da sensori o altri sistemi | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare gli hub di evento per segnalare agli utenti di dati sensore."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Informare gli utenti di dati ricevuti da sensori o altri sistemi

Si supponga di che disporre un'applicazione che esegue il monitoraggio dei dati in tempo reale o produce report in base alla pianificazione. Se osserva il sito Web in cui vengono visualizzati i grafici in tempo reale o rapporti, potrebbe essere visualizzato un elemento che richiede l'azione. Cosa fare se è necessario essere avvisi per i casi, piuttosto che ricordare a visitare il sito Web? Si supponga che si dispone di una luce Ingrandisci in una serra ed è necessario sapere immediatamente se la luce in uscita. È possibile eseguire da con un sensore di luce in serra, la disposizione da inviare tramite posta elettronica se la luce è disattivata.

![][1]

In un altro scenario, si supponga che si esegue una funzionalità di dispositivo da compagnia e deve ricevere una notifica ai livelli di fornitura inventario inferiore. Ad esempio, potrebbe disporre di inviare un messaggio di testo dal sistema ERP se l'inventario di magazzino di cibo è caduta a un livello critico. 

![][2]

Il problema è illustrato come ottenere informazioni più importanti quando vengono soddisfatte determinate condizioni, non quando Orienta per l'estrazione di un report statico. Se si utilizza un [Hub evento Azure][] o [Azure IoT Hub][] per ricevere dati da dispositivi o applicazioni aziendali, ad esempio [Dynamics AX][], sono disponibili diverse opzioni per la procedura per l'elaborazione. È possibile visualizzarli in un sito Web, è possibile analizzare, è possibile archiviare e usarli per attivare i comandi di eseguire un'azione. A questo scopo è possibile utilizzare potenti strumenti, ad esempio [Siti Web di Azure][], [SQL Azure][], [HDInsight][], [Famiglia di prodotti Business Intelligence Cortana][], [Famiglia di prodotti IoT][], [App logica][]o [Hub di notifica di Azure][]. Ma a volte si desidera eseguire per inviare i dati a un utente con un minimo di sovraccarico. Per mostrare come eseguire questa operazione con solo un po' di codice, è disponibile un nuovo campione [AppToNotifyUsers][]. Opzioni incluse sono posta elettronica (SMTP), SMS e telefono.

## <a name="application-structure"></a>Struttura dell'applicazione

L'applicazione è scritta in c# e il file Leggimi nel campione contiene tutte le informazioni che necessarie per modificare, creare e pubblicare l'applicazione. Nelle sezioni seguenti offrono una panoramica delle funzionalità dell'applicazione.

Iniziamo con la si presuppone che gli eventi critici vengano applicati a un Hub evento Azure o IoT Hub. Qualsiasi hub produrrà, purché sia accedervi e che la stringa di connessione.

Se non si dispone già di un evento Hub o IoT hub, è possibile impostare facilmente un cuscino di test con un'icona Arduino e un Pi Raspberry e seguire le istruzioni disponibili in project [La connessione di punti](https://github.com/Azure/connectthedots) . Sensore light su schermo Arduino invia illuminazione tramite la Pi a un [Hub evento Azure][] (**ehdevices**) e un processo di [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/) inserisce gli avvisi per un secondo hub di evento (**ehalerts**) se illuminazione ricevuti inferiori a un determinato livello.

Quando si avvia **AppToNotify** , legge un file di configurazione (App) per ottenere l'URL e le credenziali per l'Hub di evento ricevere gli avvisi. Genera quindi un processo per controllare continuamente che Hub di evento per ogni messaggio arriva, purché disponga può accedere all'URL per l'evento Hub o IoT hub e credenziali valide, il codice di evento hub reader leggerà continuamente nuove caratteristiche. Durante l'avvio, l'applicazione legge anche l'URL e le credenziali per il servizio di messaggistica (telefono posta elettronica, SMS,) che si desidera utilizzare e il nome o indirizzo del mittente e un elenco di destinatari.

Dopo il monitor Hub evento rileva un messaggio, attiva un processo che invia il messaggio utilizzando il metodo specificato nel file di configurazione. Si noti che invia tutti i messaggi che vengano rilevati. Se si imposta il monitor in modo che puntino a un Hub di eventi che riceve dieci messaggi al secondo, il mittente invierà dieci messaggi al secondo-dieci messaggi di posta elettronica al secondo, messaggi SMS dieci al secondo, dieci telefonate al secondo. Per questo motivo, assicurarsi che monitorare un Hub di eventi che riceve solo gli avvisi che devono essere inviate, non un Hub di eventi che riceve tutti i dati non elaborati dal sensori o applicazioni.

## <a name="applicability"></a>Applicabilità

Il codice in questo esempio mostra solo come controllare gli hub di eventi e come chiamare i servizi di messaggistica esterna nel caso in cui si desidera aggiungere questa funzionalità nell'applicazione. Si noti che questa soluzione è un DIY solo esempio incentrati sui sviluppo. Essa non soddisfare requisiti dell'organizzazione come ridondanza, fail sopra, riavviare all'errore e così via. Per altre soluzioni globale e di produzione, vedere le operazioni seguenti:

- Usare i connettori o le notifiche push mediante il servizio di [Azure logica App](../app-service-logic/app-service-logic-connectors-list.md) .
- Utilizzando [Gli hub di notifica di Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), come descritto blog [le notifiche push per la trasmissione a milioni di dispositivi mobili con gli hub di notifica di Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## <a name="next-steps"></a>Passaggi successivi

È semplice creare un servizio di notifica semplice che invia messaggi di posta elettronica o SMS ai destinatari o effettua, ai dati di inoltro ricevuti da un evento Hub o IoT Hub. Per distribuire la soluzione per informare gli utenti sulla base dei dati ricevuti da questi hub, visitare [AppToNotifyUsers][].

Per ulteriori informazioni su questi hub, vedere gli articoli seguenti:

- [Hub evento Azure]
- [Hub IoT Azure]
- Guida introduttiva a un [evento hub esercitazione].
- Un' [applicazione di esempio che utilizza evento hub]completa.

Per distribuire la soluzione per informare gli utenti in base a dati ricevuti da questi hub, visitare:

- [AppToNotifyUsers][]

[Esercitazione hub di evento]: event-hubs-csharp-ephcs-getstarted.md
[Hub IoT Azure]: https://azure.microsoft.com/services/iot-hub/
[Hub evento Azure]: https://azure.microsoft.com/services/event-hubs/
[Hub evento Azure]: https://azure.microsoft.com/services/event-hubs/
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Siti Web Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence famiglia]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[Famiglia di prodotti IoT]: https://azure.microsoft.com/solutions/iot-suite/
[App logica]: https://azure.microsoft.com/services/app-service/logic/
[Hub di notifica Azure]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png