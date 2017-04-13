<properties
    pageTitle="Guida introduttiva di Azure App per dispositivi mobili per le app Xamarin.Android"
    description="Seguire questa esercitazione per iniziare a usare App Mobile Azure per lo sviluppo di Xamarin Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Creare un'App Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come aggiungere un servizio basato su cloud back-end per un'app Xamarin.Android. Per ulteriori informazioni, vedere [informazioni sui App Mobile](app-service-mobile-value-prop.md).

Di seguito viene screenshot dall'app completate:

![][0]

Esercitazione è un prerequisito per tutte le altre esercitazioni App Mobile per le app Xamarin.Android.

##<a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario i prerequisiti seguenti:

* Un account Azure attivo. Se non si dispone di un account, iscriversi a una versione di valutazione di Azure e ottenere fino a 10 App Mobile gratuito. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Per istruzioni, vedere [il programma di installazione e installare per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

>[AZURE.NOTE]Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio di App provare](https://tryappservice.azure.com/?appServiceName=mobile).  È possibile creare immediatamente un breve starter App Mobile nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end App Mobile Azure

Seguire questa procedura per creare un back-end App Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ora viene completato il provisioning un back-end Azure Mobile App che possono essere usate dalle applicazioni client mobili. Successivamente, scaricare un progetto per un semplice "elenco" server di back-end e pubblicarla in Azure.

## <a name="configure-the-server-project"></a>Configurare project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Scaricare ed eseguire l'app Xamarin.Android

1. In **Download ed eseguire il progetto Xamarin.Android**, fare clic sul pulsante **Scarica** .

    Salvare il file di progetto in formato compresso al computer locale e prendere nota in cui viene salvato.

2. Premere **F5 per compilare il progetto e avviare l'app** .

3. Nell'app digitare testo significativo, ad esempio _completata l'esercitazione_ e quindi fare clic sul pulsante **Aggiungi** .

    ![][10]

    Dati della richiesta viene inseriti nella tabella TodoItem. Elementi archiviati nella tabella vengono restituiti dalla back-end app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

    > [AZURE.NOTE] È possibile esaminare il codice che accede il back-end di app per dispositivi mobili per eseguire una query e inserire i dati che si trova nel file ToDoActivity.cs c#.

##<a name="next-steps"></a>Passaggi successivi

* [Aggiungere l'app di sincronizzazione Offline](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Aggiungere l'autenticazione applicazione in uso.](app-service-mobile-xamarin-android-get-started-users.md)
* [Aggiungere l'app Xamarin.Android le notifiche push](app-service-mobile-xamarin-android-get-started-push.md)
* [Come utilizzare il client gestito per App Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
