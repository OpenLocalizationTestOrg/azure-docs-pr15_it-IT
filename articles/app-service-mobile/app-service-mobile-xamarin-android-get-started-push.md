<properties
    pageTitle="Aggiungere le notifiche push per l'app Xamarin.Android | Servizio App Azure"
    description="Informazioni su come utilizzare il servizio di App Azure e Azure notifica hub per inviare le notifiche push Xamarin.Android all'applicazione in uso"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Aggiungere l'app Xamarin.Android le notifiche push

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Panoramica


In questa esercitazione, aggiungere le notifiche push [Xamarin.Android rapido avvia](app-service-mobile-windows-store-dotnet-get-started.md) progetto in modo che una notifica push viene inviata al dispositivo ogni volta che viene inserito un record.

Se non si utilizza il progetto di server introduttive scaricato, sarà necessario il pacchetto di estensione di notifica push. Per ulteriori informazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .


##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ Un account di Google attivo. È possibile iscriversi a un account di Google in [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ [Componente Client di messaggistica Google Cloud](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Configurare un Hub di notifica

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Abilitare Firebase Cloud messaggistica

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Configurare Azure per inviare le richieste di push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Aggiornare project server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurare il progetto di client per le notifiche push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Aggiungere codice notifiche push applicazione in uso.

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Notifiche push di test nell'app

È possibile verificare l'app usando un dispositivo virtuale nell'emulatore. Sono disponibili ulteriori passaggi di configurazione richieste durante l'esecuzione su un emulatore.

1. Verificare che si sta per distribuzione o il debug su un dispositivo virtuale che dispone di Google APIs impostare come destinazione, come illustrato di seguito in Gestione dispositivi virtuale Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Aggiungere un account di Google in un dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiungi account**, quindi seguire le istruzioni.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Eseguire l'app di elenco attività come prima di inserire un nuovo elemento todo. In questo caso, viene visualizzata un'icona di notifica nell'area di notifica. È possibile aprire il cassetto della notifica per visualizzare il testo completo della notifica.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
