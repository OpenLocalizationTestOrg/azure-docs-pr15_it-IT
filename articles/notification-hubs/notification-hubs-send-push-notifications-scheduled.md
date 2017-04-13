<properties
    pageTitle="Come inviare notifiche pianificate | Microsoft Azure"
    description="Questo argomento descrive l'utilizzo delle notifiche pianificata con gli hub di notifica di Azure."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="notifiche push, invio di notifiche, pianificazione delle notifiche push"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Procedura: Inviare notifiche pianificate


##<a name="overview"></a>Panoramica

Se si dispone di uno scenario in cui si desidera inviare una notifica a un certo punto in futuro, ma non è un modo semplice per riattivare il codice di back-end per inviare la notifica. Livello standard notifica hub supporta una caratteristica che consente di pianificare le notifiche di backup per 7 giorni in futuro.

Quando si invia una notifica, utilizzare semplicemente la classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) in SDK hub notifica come illustrato nell'esempio seguente:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Inoltre, è possibile annullare una notifica precedentemente pianificata utilizzando il relativo notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Non esistono limiti al numero di notifiche pianificate che è possibile inviare.