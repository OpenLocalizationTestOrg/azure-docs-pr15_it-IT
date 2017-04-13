<properties
    pageTitle="Guida introduttiva di Azure impegno per dispositivi mobili per Xamarin.Android"
    description="Informazioni su come usare Azure Mobile coinvolgimento con Analitica e delle notifiche Push per App Xamarin.Android."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Guida introduttiva di Azure impegno per dispositivi mobili per le app Xamarin.Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e su come inviare le notifiche push agli utenti segmentati di un'applicazione Xamarin.Android.
In questa esercitazione viene illustrato lo scenario di trasmissione semplice con coinvolgimento di dispositivi mobili. Al suo interno, si crea un'app Xamarin.Android vuota che consente di raccogliere dati di base e riceverà le notifiche push utilizzando Google Cloud messaggistica (GCM).

In questa esercitazione richiede le operazioni seguenti:

+ [Studio Xamarin](http://xamarin.com/studio). È anche possibile utilizzare Visual Studio con Xamarin ma in questa esercitazione utilizza Xamarin Studio. Per istruzioni sull'installazione, vedere [installazione e installare per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ [SDK Xamarin impegno per dispositivi mobili](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Configurazione di dispositivi mobili impegno per l'app Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato una "integrazione di base", ovvero il numero minimo necessario per raccogliere dati e inviare una notifica push. 

Verrà creata un'app di base con Xamarin Studio per illustrare l'integrazione.

###<a name="create-a-new-xamarinandroid-project"></a>Creare un nuovo progetto Xamarin.Android

1. Barra di avvio **Xamarin Studio** andare a **File** -> **nuova** -> **soluzione** 

    ![][1]

2. Selezionare **App Android** quindi verificare che la lingua selezionata è **c#** e fare clic su **Avanti**.

    ![][2]

3. Immettere il **Nome dell'App** e **identificativo dell'organizzazione**. Assicurarsi di segno di spunta **Google Play Services** e quindi fare clic su **Avanti**. 

    ![][3]
    
4. Aggiornare il **Nome del progetto**, **Nome soluzione** e **luogo** , se richiesto e fare clic su **Crea**.

    ![][4]
 
Xamarin Studio creerà l'app in cui si prevede di integrare coinvolgimento Mobile. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Fare clic con il pulsante destro sulla cartella **pacchetti** in Esplora e selezionare **Aggiungi pacchetti**

    ![][5]

2. Cercare **SDK Xamarin coinvolgimento di Microsoft Azure Mobile** e aggiungerlo alla soluzione.  

    ![][6]
   
3. Aprire **MainActivity.cs** e aggiungere le seguenti istruzioni using:

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. Nel `OnCreate` metodo, aggiungere le operazioni seguenti per inizializzare la connessione con coinvolgimento Mobile back-end. Assicurarsi di aggiungere il **ConnectionString**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Aggiungere una dichiarazione di servizio e autorizzazioni

1. Aprire il file **manifest** sotto la cartella proprietà. Selezionare la scheda origine in modo che si aggiorna direttamente l'origine XML.
 
2. Aggiungere queste autorizzazioni manifest (che può trovarsi sotto la cartella **proprietà** ) del progetto immediatamente prima o dopo il `<application>` tag:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Aggiungere quanto segue tra il `<application>` e `</application>` tag per dichiarare il servizio agente:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. Nel codice incollato, sostituire `"<Your application name>"` nell'etichetta. Viene visualizzato nel menu **Impostazioni** in cui gli utenti possono vedere services in esecuzione nel dispositivo. È possibile aggiungere la parola "Servizio" in tale etichetta, ad esempio.

###<a name="send-a-screen-to-mobile-engagement"></a>Inviare una schermata a coinvolgimento Mobile

Per avviare l'invio di dati e gli utenti siano attivi, è necessario inviare almeno una schermata a back-end coinvolgimento Mobile. Per eseguire questa operazione, assicurarsi che la `MainActivity` eredita `EngagementActivity` invece di `Activity`.

    public class MainActivity : EngagementActivity
    
In alternativa, se non è possibile ereditare da `EngagementActivity` quindi è necessario aggiungere `.StartActivity` e `.EndActivity` metodi `OnResume` e `OnPause` rispettivamente.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivare le notifiche push e la messaggistica in app

Coinvolgimento mobile consente di interagire con e raggiungere gli utenti con le notifiche push e messaggistica nel contesto delle campagne di app. In questo modulo è chiamato portata nel portale di impegno Mobile.
Nelle sezioni seguenti vengono configura automaticamente l'app per riceverle.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
