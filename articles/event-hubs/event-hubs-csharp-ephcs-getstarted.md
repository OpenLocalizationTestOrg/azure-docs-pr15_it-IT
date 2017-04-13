<properties
    pageTitle="Guida introduttiva a hub di evento in c# | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare con gli hub evento Azure c# e l'utilizzo di EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un servizio che elabora grandi quantità di dati dell'evento (telemetria) dalle applicazioni e i dispositivi connessi. Dopo aver raccolto dati nell'hub di evento, è possibile archiviare i dati utilizzando un cluster di memoria o trasformare i dati utilizzando un provider analitica in tempo reale. Questa funzionalità di elaborazione e raccolta evento su larga scala è un componente chiave di architetture di applicazione moderna incluse Internet di elementi (IoT).

In questa esercitazione viene illustrato come utilizzare il portale classico Azure per creare un Hub di evento. Viene inoltre come raccogliere i messaggi a un Hub di evento usando un'applicazione console scritta in c# e come recuperarli in parallelo tramite la raccolta c# [Host processore eventi][] .

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un account Azure attivo. Se non si dispone di uno, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. Da Visual Studio, aprire il progetto **ricevitore** creata in precedenza.

2. Fare doppio clic soluzione **ricevitore** , quindi fare clic su **Aggiungi**e quindi fare clic su **Progetto esistente**.
 
3. Individuare il file Sender.csproj esistente e quindi fare doppio clic su esso per aggiungerlo alla soluzione.
 
4. Fare di nuovo, fare doppio clic soluzione **ricevitore** e quindi fare clic su **proprietà**. Viene visualizzata la pagina delle proprietà **ricevitore** .

5. Fare clic su **Progetto di avvio**, quindi fare clic sul pulsante **più progetti di avvio** . Impostare la casella di **azione** per i progetti **mittente** e **ricevitore** per **iniziare**.

    ![][19]

6. Fare clic su **dipendenze del progetto**. Nella casella **progetti** , fare clic su **mittente**. Nella casella **dipendente da** verificare che sia selezionata **ricevitore** .

    ![][20]

7. Fare clic su **OK** per chiudere la finestra di dialogo **proprietà** .

1.  Premere F5 per eseguire il progetto **ricevitore** in Visual Studio, quindi attendere che venga avviato ricevitori per tutte le partizioni.

    ![][21]

2.  Il progetto **mittente** verrà eseguito automaticamente. Premere **INVIO** nella finestra della console e visualizzare gli eventi vengono visualizzati nella finestra del destinatario.

    ![][22]

Premere **Ctrl + C** nella finestra del **mittente** per terminare l'applicazione mittente, quindi premere **INVIO** nella finestra del ricevitore per arrestare tale applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che è già stata creata un'applicazione che crea un Hub di eventi e invia e riceve i dati, è possibile spostare per gli scenari seguenti:

- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .
- [Cenni preliminari sui hub di eventi][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Evento processore Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
