<properties
    pageTitle="Guida introduttiva a App Mobile utilizzando Xamarin.Forms"
    description="Seguire questa esercitazione per iniziare a usare App Mobile Azure per lo sviluppo di Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Creare un'app Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come aggiungere un servizio basato su cloud back-end per un'app per dispositivi mobili Xamarin.Forms utilizzando un back-end App Mobile Azure. È necessario creare un nuova App Mobile di back-end e un semplice _elenco_ app Xamarin.Forms contenente dati app in Azure.

Esercitazione è un prerequisito per tutte le altre esercitazioni App Mobile per Xamarin.Forms.

##<a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario quanto segue:

* Un account Azure attivo. Se non si dispone un account, è possibile iscriversi per una versione di valutazione di Azure e ottenere fino a 10 gratuiti App Mobile che è possibile continuare a usare anche il termine di valutazione. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Per istruzioni, vedere [il programma di installazione e installare per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) . 

* Un Mac con Xcode 7.0 o versione successiva e della Community di Studio Xamarin installato. Vedere [configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [il programma di installazione, installazione e verifiche per gli utenti Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](https://tryappservice.azure.com/?appServiceName=mobile), in cui è possibile creare immediatamente un breve starter App Mobile nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuova App Mobile Azure di back-end

Seguire questa procedura per creare un nuova App Mobile di back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Ora viene completato il provisioning un back-end Azure Mobile App che possono essere usate dalle applicazioni client mobili. Successivamente, che verranno scaricate un progetto per un semplice "elenco" server di back-end e pubblicarla in Azure.

## <a name="configure-the-server-project"></a>Configurare project server

Seguire la procedura seguente per configurare il progetto server per l'utilizzo di Node o .NET back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Scaricare ed eseguire la soluzione Xamarin.Forms

Qui sono disponibili due opzioni. È possibile scaricare la soluzione a un computer Mac e aprirlo in Xamarin Studio oppure è possibile scaricare la soluzione a un computer Windows e aprirlo in Visual Studio usando un Mac in rete per la creazione di app iOS. Vedere [configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) se è necessario istruzioni più dettagliate sugli scenari di configurazione Xamarin.

Di seguito procedere:

 1. In un computer Mac o in un computer Windows, aprire il [Portale di Azure] in una finestra del browser.
 2. Della pala di impostazioni per l'App Mobile, fare clic su **Guida introduttiva** (in Mobile) > **Xamarin.Forms**. In passaggio 3 fare clic su **Crea una nuova app** se non è già selezionata.  Fare clic su **Scarica** .

    Consente di scaricare un progetto che contiene un'applicazione client che sia connesso all'app per dispositivi mobili. Salvare il file di progetto in formato compresso al computer locale e prendere nota in cui viene salvato.

 3. Estrarre il progetto che è stato scaricato e quindi riaprirla in Xamarin Studio o Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Facoltativo) Eseguire il progetto di iOS

In questa sezione è per l'esecuzione del progetto di iOS Xamarin per i dispositivi iOS. È possibile ignorare questa sezione se non si lavora con i dispositivi iOS.

####<a name="in-xamarin-studio"></a>In Xamarin Studio

1. Fare clic sul progetto iOS e quindi fare clic su **Imposta come progetto di avvio**.
2. Dal menu **Esegui** fare clic su **Avvia debug** per compilare il progetto e avviare l'app in emulatore iPhone.

####<a name="in-visual-studio"></a>In Visual Studio
1. Fare clic sul progetto iOS e quindi fare clic su **Imposta come progetto di avvio**.
2. Nel menu **Compila** , fare clic su **Gestione configurazione**.
3. Nella finestra di dialogo **Gestione configurazione** , selezionare le caselle di controllo **compilare** e **distribuire** il progetto di iOS.
4. Premere **F5 per generare il progetto e avviare l'app in emulatore iPhone** .

    >[AZURE.NOTE] Nel caso di problemi durante la creazione, esecuzione NuGet Gestione pacchetti e l'aggiornamento alla versione più recente del Xamarin supporta pacchetti. A volte i progetti di Guida introduttiva potrebbero rimanere indietro in vengono aggiornate alla versione più tardi.    

Nell'app digitare testo significativo, ad esempio _Xamarin informazioni_ e quindi fare clic sul **+** pulsante.

![][10]

Si invia una richiesta POST nel nuovo app per dispositivi mobili di back-end ospitato in Azure. Dati della richiesta viene inseriti nella tabella TodoItem. Elementi archiviati nella tabella vengono restituiti dalla back-end app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

>[AZURE.NOTE]
> Sono disponibili il codice che accede il back-end di app per dispositivi mobili nel file TodoItemManager.cs c# del progetto di libreria di classi portabile della soluzione.

##<a name="optional-run-the-android-project"></a>(Facoltativo) Eseguire il progetto Android

In questa sezione è per l'esecuzione del progetto droid Xamarin per Android. Se non si lavora con i dispositivi Android, è possibile ignorare questa sezione.

####<a name="in-xamarin-studio"></a>In Xamarin Studio

1. Fare clic sul progetto Android e quindi fare clic su **Imposta come progetto di avvio**.
2. Dal menu **Esegui** fare clic su **Avvia debug** per compilare il progetto e avviare l'app in un emulatore Android.

####<a name="in-visual-studio"></a>In Visual Studio
1. Fare clic sul progetto Android (Droid) e quindi fare clic su **Imposta come progetto di avvio**.
4. Nel menu **Compila** , fare clic su **Gestione configurazione**.
5. Nella finestra di dialogo **Gestione configurazione** , selezionare le caselle di controllo **compilare** e **distribuire** il progetto Android.
6. Premere **F5 per generare il progetto e avviare l'app in un emulatore Android** .

    >[AZURE.NOTE] Nel caso di problemi durante la creazione, esecuzione NuGet Gestione pacchetti e l'aggiornamento alla versione più recente del Xamarin supporta pacchetti. A volte i progetti di Guida introduttiva potrebbero rimanere indietro in vengono aggiornate alla versione più tardi.    


Nell'app digitare testo significativo, ad esempio _Xamarin informazioni_ e quindi fare clic sul **+** pulsante.

![][11]

Si invia una richiesta POST nel nuovo app per dispositivi mobili di back-end ospitato in Azure. Dati della richiesta viene inseriti nella tabella TodoItem. Elementi archiviati nella tabella vengono restituiti dalla back-end app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

> [AZURE.NOTE]
> Sono disponibili il codice che accede il back-end di app per dispositivi mobili nel file TodoItemManager.cs c# del progetto di libreria di classi portabile della soluzione.


##<a name="optional-run-the-windows-project"></a>(Facoltativo) Eseguire il progetto di Windows


In questa sezione è per l'esecuzione del progetto Xamarin WinApp per dispositivi Windows. Se non si lavora con i dispositivi di Windows, è possibile ignorare questa sezione.


####<a name="in-visual-studio"></a>In Visual Studio
1. Pulsante destro del mouse di uno dei progetti di Windows e quindi fare clic su **Imposta come progetto di avvio**.
4. Nel menu **Compila** , fare clic su **Gestione configurazione**.
5. Nella finestra di dialogo **Gestione configurazione** , selezionare le caselle di controllo **compilare** e **distribuire** del progetto di Windows che si è scelto.
6. Premere **F5 per generare il progetto e avviare l'app in un emulatore di Windows** .

    >[AZURE.NOTE] Nel caso di problemi durante la creazione, esecuzione NuGet Gestione pacchetti e l'aggiornamento alla versione più recente del Xamarin supporta pacchetti. A volte i progetti di Guida introduttiva potrebbero rimanere indietro in vengono aggiornate alla versione più tardi.    


Nell'app digitare testo significativo, ad esempio _Xamarin informazioni_ e quindi fare clic sul **+** pulsante.

Si invia una richiesta POST nel nuovo app per dispositivi mobili di back-end ospitato in Azure. Dati della richiesta viene inseriti nella tabella TodoItem. Elementi archiviati nella tabella vengono restituiti dalla back-end app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

![][12]

> [AZURE.NOTE]
> Sono disponibili il codice che accede il back-end di app per dispositivi mobili nel file TodoItemManager.cs c# del progetto di libreria di classi portabile della soluzione.

##<a name="next-steps"></a>Passaggi successivi

* [Aggiungere l'autenticazione applicazione in uso.](app-service-mobile-xamarin-forms-get-started-users.md)  
Informazioni su come eseguire l'autenticazione degli utenti dell'app con un provider di identità.

* [Aggiungere le notifiche push applicazione in uso.](app-service-mobile-xamarin-forms-get-started-push.md)  
Informazioni su come aggiungere push notifiche supportano l'app e configurare il back-end App Mobile per l'utilizzo di Azure notifica hub per inviare le notifiche push.

* [Attivare la sincronizzazione offline per l'app](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Informazioni su come aggiungere l'app utilizzando un back-end App Mobile supporto della modalità offline. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete.

* [Come utilizzare il client gestito per App Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Informazioni su come lavorare con il client gestito SDK nell'app Xamarin. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portale di Azure]: https://portal.azure.com/

