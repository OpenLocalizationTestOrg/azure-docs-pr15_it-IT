<properties
    pageTitle="Aggiungere le notifiche Push all'App Android con Azure App per dispositivi mobili"
    description="Informazioni su come usare le app Mobile Azure per inviare le notifiche push per l'app di Android."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Aggiungere le notifiche Push per l'App di Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione, aggiungere le notifiche push al progetto [Android introduttiva] in modo che una notifica push viene inviata al dispositivo ogni volta che viene inserito un record.

Se non si utilizza il progetto di server introduttive scaricato, è necessario il pacchetto di estensione di notifica push. Per ulteriori informazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

È necessario quanto segue:

* Un IDE a seconda di back-end del progetto:

    * [Studio Android](https://developer.android.com/sdk/index.html) se questa app ha un back-end Node.

    * Un back-end .net [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) o versione successiva se questa app.

* Android 2.3 o versione successiva, revisione di archivio di Google 27 o versioni successiva e Google Play Services 9.0.2 dei o versioni successive per la messaggistica Cloud Firebase.

* Completare [introduttive Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Creare un progetto che supporta messaggistica Cloud Firebase

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurare un Hub di notifica

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurare Azure per inviare le notifiche push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Attivare le notifiche push per project server

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Aggiungere le notifiche push applicazione in uso.

In questa sezione, aggiornare l'app di Android client per gestire le notifiche push.

### <a name="verify-android-sdk-version"></a>Verificare la versione di SDK Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Il passaggio successivo consiste nel installare Google Play services. Google Cloud messaggistica sono alcuni requisiti minimi API per lo sviluppo e la verifica, che la proprietà **minSdkVersion** nel manifesto deve essere conforme a.

Se si stanno verificando con i dispositivi meno recenti, quindi consultare [Impostata su Google riprodurre SDK dei servizi] per determinare come bassa è possibile impostare questo valore e impostarlo in modo appropriato.

### <a name="add-google-play-services-to-the-project"></a>Aggiunta di Google Play Services al progetto

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Aggiungere codice

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Verificare l'app in base al servizio mobile pubblicato

È possibile verificare l'app collegando direttamente un telefono Android con un cavo USB o usando un dispositivo virtuale nell'emulatore.

## <a name="more"></a>Più

<!-- URLs -->
[Guida introduttiva Android]: app-service-mobile-android-get-started.md

[Configurare i servizi di Google Play SDK]:https://developers.google.com/android/guides/setup
