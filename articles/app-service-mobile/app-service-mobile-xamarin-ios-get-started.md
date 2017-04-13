<properties
    pageTitle="Guida introduttiva a Azure App servizio Mobile App per App Xamarin.iOS | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a utilizzare App Mobile per lo sviluppo di Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Creare un'app Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come aggiungere un servizio basato su cloud back-end a un'app per dispositivi mobili Xamarin.iOS utilizzando un back-end Azure app per dispositivi mobili.  Creare un nuova app per dispositivi mobili di back-end e un semplice _elenco_ app Xamarin.iOS contenente dati app in Azure.

Esercitazione è un prerequisito per tutte le altre esercitazioni Xamarin.iOS sull'utilizzo della funzionalità di App Mobile in Azure App servizio.

##<a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario i prerequisiti seguenti:

* Un account Azure attivo. Se non si dispone di un account, iscriversi a una versione di valutazione di Azure e ottenere fino a 10 App per dispositivi mobili gratuito che è possibile continuare a usare anche il termine di valutazione. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Per istruzioni, vedere [il programma di installazione e installare per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

* Un Mac con Xcode 7.0 o versione successiva e della Community di Studio Xamarin installato. Vedere [configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [il programma di installazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Se si desidera iniziare a usare il servizio di App Azure prima che si effettua l'iscrizione per un account Azure, accedere al [Servizio di App provare](https://tryappservice.azure.com/?appServiceName=mobile). È possibile creare un'app per dispositivi mobili starter breve immediatamente nel servizio di App, ovvero non carta di credito necessari e non impegni.

## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end App Mobile Azure

Seguire questa procedura per creare un back-end App Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurare project server

Ora viene completato il provisioning un back-end Azure Mobile App che possono essere usate dalle applicazioni client mobili. Successivamente, scaricare un progetto per un semplice "elenco" server di back-end e pubblicarla in Azure.

Seguire i passaggi seguenti per configurare project server per l'utilizzo di Node o .NET back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Scaricare ed eseguire l'app Xamarin.iOS

1. Aprire il [portale di Azure] in una finestra del browser.

2. Scegliere **Guida introduttiva**blade impostazioni per l'App Mobile, > **Xamarin.iOS**. In passaggio 3 fare clic su **Crea una nuova app** se non è già selezionata.  Fare clic su **Scarica** .

    Viene scaricata un'applicazione client che si connette al back-end per dispositivi mobili. Salvare il file di progetto in formato compresso al computer locale e prendere nota in cui viene salvato.

3. Estrarre il progetto che è stato scaricato e quindi riaprirla in Xamarin Studio (o Visual Studio).

    ![][9]

    ![][8]

4. Premere F5 per generare il progetto e avviare l'app in emulatore iPhone.

5. Nell'app digitare testo significativo, ad esempio _Xamarin informazioni_e quindi fare clic sul **+** pulsante.

    ![][10]

    Dati della richiesta viene inseriti nella tabella TodoItem. Elementi archiviati nella tabella vengono restituiti dalla back-end app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

>[AZURE.NOTE]È possibile esaminare il codice che accede il back-end di app per dispositivi mobili per eseguire una query e inserire dati in file QSTodoService.cs c#.

##<a name="next-steps"></a>Passaggi successivi

* [Aggiungere l'app di sincronizzazione Offline](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Aggiungere l'autenticazione applicazione in uso.](app-service-mobile-xamarin-ios-get-started-users.md)
* [Aggiungere l'app Xamarin.Android le notifiche push](app-service-mobile-xamarin-ios-get-started-push.md)
* [Come utilizzare il client gestito per App Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portale di Azure]: https://portal.azure.com/
