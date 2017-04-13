<properties
    pageTitle="Aggiungere le notifiche Push per iOS App con App Mobile Azure"
    description="Informazioni su come usare le app Mobile Azure per inviare le notifiche push per l'app iOS."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Aggiungere l'App iOS delle notifiche Push

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione, aggiungere le notifiche push su [iOS rapido avvia] progetto in modo che una notifica push viene inviata al dispositivo ogni volta che viene inserito un record.

Se non si utilizza il progetto di server introduttive scaricato, sarà necessario il pacchetto di estensione di notifica push. Per ulteriori informazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

[IOS simulatore non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). È necessario un dispositivo iOS fisica e un' [iscrizione al programma di sviluppo di Apple](https://developer.apple.com/programs/ios/).

##<a name="configure-hub"></a>Configurare Hub di notifica

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrare app per le notifiche push

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurare Azure per inviare le notifiche push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Aggiornare la copia nel server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Aggiungere le notifiche push all'app

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Notifiche push di test

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Più

* Modelli offrono la possibilità di inviare push multipiattaforma e inserisce localizzati. [Come usare iOS libreria Client per le App per dispositivi mobili Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) viene illustrato come registrare modelli.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Guida introduttiva di iOS]: app-service-mobile-ios-get-started.md
