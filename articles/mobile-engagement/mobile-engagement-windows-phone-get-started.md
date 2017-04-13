<properties
    pageTitle="Guida introduttiva di Azure Mobile impegno per Windows Phone Silverlight App"
    description="Informazioni su come usare Azure Mobile coinvolgimento con le notifiche push e analitica per le applicazioni di Windows Phone Silverlight."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Guida introduttiva di Azure Mobile impegno per Windows Phone Silverlight App

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e inviare le notifiche push agli utenti segmentati di un'applicazione di Windows Phone Silverlight.
In questa esercitazione viene illustrato lo scenario di trasmissione semplice con coinvolgimento di dispositivi mobili. Al suo interno, si crea un'app di Windows Phone Silverlight vuota che consente di raccogliere dati di base e riceverà le notifiche push utilizzando Microsoft Push Notification Service (MPNS).

> [AZURE.NOTE] Se la destinazione è Windows Phone 8.1 (non Silverlight), vedere l' [esercitazione universale di Windows](mobile-engagement-windows-store-dotnet-get-started.md).

In questa esercitazione richiede le operazioni seguenti:

+ Visual Studio 2013
+ Pacchetto Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configurazione di dispositivi mobili impegno per il Windows Phone app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato una "integrazione di base", ovvero il numero minimo necessario per raccogliere dati e inviare una notifica push. La documentazione completa integrazione sono disponibili in [Mobile coinvolgimento Windows Phone SDK integrazione](mobile-engagement-windows-phone-sdk-overview.md)

Verrà creata un'app di base con Visual Studio per illustrare l'integrazione.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Creare un nuovo progetto Silverlight Windows Phone

I passaggi seguenti presuppongono l'utilizzo di Visual Studio 2015 anche se i passaggi sono simili nelle versioni precedenti di Visual Studio. 

1. Avviare Visual Studio e nella schermata **Home** selezionare **Nuovo progetto**.

2. Nella finestra popup selezionare **Windows 8** -> **Windows Phone** -> **App vuota (Windows Phone Silverlight)**. Immettere il **nome** di app e **Nome soluzione**e quindi fare clic su **OK**.

    ![][1]

3. È possibile scegliere di assegnazione del **Windows Phone 8.0** o **Windows Phone 8.1**.

È stata creata una nuova app di Windows Phone Silverlight in cui si prevede di integrare Azure Mobile coinvolgimento SDK.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Installare il pacchetto di nuget [MicrosoftAzure.MobileEngagement] nel progetto.

2. Apri `WMAppManifest.xml` , nella cartella proprietà e verificare che le operazioni seguenti vengono dichiarate (aggiungerli se non si trovano) nel `<Capabilities />` tag:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Incollare la stringa di connessione che sono stati copiati in precedenza per l'app Mobile coinvolgimento e incollarlo in ora la `Resources\EngagementConfiguration.xml` file, tra il `<connectionString>` e `</connectionString>` tag:

    ![][3]

4. Nel `App.xaml.cs` file:

    un. Aggiungere il `using` istruzione:

            using Microsoft.Azure.Engagement;

    b. Inizializzare SDK nel `Application_Launching` metodo:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Inserire le seguenti operazioni nel `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Attivare il monitoraggio in tempo reale

Per iniziare subito a inviare dati e assicurare che gli utenti attivi, è necessario inviare almeno una schermata (attività) back-end coinvolgimento Mobile.

1. Nella finestra MainPage.xaml.cs, aggiungere il `using` istruzione:

        using Microsoft.Azure.Engagement;

2. Sostituire la classe base di **MainPage**, ovvero prima **PhoneApplicationPage**, con **EngagementPage**.

        class MainPage : EngagementPage 
    
3. Nel `MainPage.xml` file:

    un. Aggiungere le dichiarazioni di spazi dei nomi:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Sostituire `phone:PhoneApplicationPage` il nome di tag XML con `engagement:EngagementPage`.

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivare le notifiche push e la messaggistica in app

Coinvolgimento mobile consente di interagire e raggiungere gli utenti con le notifiche Push e messaggistica all'interno dell'applicazione nel contesto delle campagne. In questo modulo è chiamato portata nel portale di impegno Mobile.
Nelle sezioni seguenti vengono configurare l'app per riceverle.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Abilitare l'app ricevere notifiche Push MPNS

Aggiungere nuove funzionalità per le `WMAppManifest.xml` file:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Inizializzare SDK portata

1. In `App.xaml.cs`, chiamare `EngagementReach.Instance.Init();` nella funzione **Application_Launching** destro del mouse dopo l'inizializzazione agente:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. In `App.xaml.cs`, chiamare `EngagementReach.Instance.OnActivated(e);` nella funzione **Application_Activated** destro del mouse dopo l'inizializzazione agente:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

È possibile iniziare. A questo punto si verificherà che hanno correttamente cried all'integrazione di base.

##<a id="send"></a>Invia una notifica applicazione in uso.

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Verrà visualizzata una notifica in un dispositivo che verrà visualizzato come una notifica all'interno dell'applicazione se l'app è aperto in caso contrario come una notifica di tipo avviso popup simile al seguente: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
