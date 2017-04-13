<properties
    pageTitle="Guida introduttiva a Android App Azure Mobile coinvolgimento"
    description="Informazioni su come usare Azure Mobile coinvolgimento con le notifiche push e analitica per App di Android."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Guida introduttiva a Azure Mobile impegno per App di Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e su come inviare le notifiche push agli utenti segmentati di un'applicazione Android.
In questa esercitazione viene illustrato lo scenario di trasmissione semplice con coinvolgimento di dispositivi mobili. Al suo interno, si crea un'app di Android vuota che consente di raccogliere dati di base e riceverà le notifiche push utilizzando Google Cloud messaggistica (GCM).

## <a name="prerequisites"></a>Prerequisiti

Esercitazione richiede [Android gli strumenti di sviluppo](https://developer.android.com/sdk/index.html), che include l'ambiente di sviluppo integrato Studio Android e la piattaforma Android più recente.

È inoltre necessario [Mobile coinvolgimento Android SDK](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurare la partecipazione attiva Mobile per l'app di Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato una "integrazione di base", ovvero il numero minimo necessario per raccogliere dati e inviare una notifica push. Creare un'app di base con Android Studio per illustrare l'integrazione.

La documentazione completa integrazione sono disponibili in [Mobile coinvolgimento Android SDK integrazione](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Creare un progetto Android

1. Nella finestra popup selezionare **Avvia un nuovo progetto di Studio Android**avviare **Studio Android**.

    ![][1]

2. Specificare un dominio app nome e la società. Prendere nota delle informazioni da inserire, perché è necessario disporre in un secondo momento. Fare clic su **Avanti**.

    ![][2]

3. Selezionare il formato di destinazione e il livello di API e fare clic su **Avanti**.

    >[AZURE.NOTE] Coinvolgimento mobile richiede il livello di API 10 minimo (Android 2.3.3).

    ![][3]

4. Selezionare **Attività vuota** qui, ossia la schermata sola per questa app e fare clic su **Avanti**.

    ![][4]

5. Infine, lasciare le impostazioni predefinite e fare clic su **Fine**.

    ![][5]

Android ora creata app demo in cui è integrare impegno Mobile.

### <a name="include-the-sdk-library-in-your-project"></a>Includere la raccolta SDK nel progetto

1. Scaricare l' [impegno per dispositivi mobili Android SDK](https://aka.ms/vq9mfn).
2. Estrarre il file di archivio in una cartella nel computer.
3. Identificare la raccolta di JAR per la versione corrente di questo SDK e copiarlo negli Appunti.

      ![][6]

4. Passare alla sezione di **Project** (1) e incollare la JAR nella cartella librerie (2).

      ![][7]

5. Per caricare la raccolta, sincronizzare il progetto.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Connettere l'app per dispositivi mobili coinvolgimento back-end con la stringa di connessione

1. Copiare le righe di codice seguenti la creazione di attività (devono essere eseguiti solo in un'unica posizione dell'applicazione, in genere l'attività principale). Per questa applicazione di esempio, è possibile aprire MainActivity in src -> principali -> cartella di linguaggio e aggiungere quanto segue:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Risolvere i riferimenti premendo Alt + INVIO o aggiungere le istruzioni di importazione seguenti:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Tornare al portale classica di Azure nella pagina relativa alle **Informazioni di connessione** dell'applicazione e copiare la **Stringa di connessione**.

      ![][9]

4. Incollare i dati nel `setConnectionString` parametro, sostituendo l'intera stringa nell'esempio seguente:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Aggiungere una dichiarazione di servizio e autorizzazioni

1. Aggiungere queste autorizzazioni manifest del progetto immediatamente prima o dopo il `<application>` tag:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Per dichiarare il servizio agente, aggiungere il codice tra le `<application>` e `</application>` tag:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. Nel codice incollato, sostituire `"<Your application name>"` nell'etichetta, che viene visualizzato nel menu **Impostazioni** in cui è possibile visualizzare services in esecuzione nel dispositivo. È possibile aggiungere la parola "Servizio" in tale etichetta, ad esempio.

### <a name="send-a-screen-to-mobile-engagement"></a>Inviare una schermata a coinvolgimento Mobile

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (attività) back-end coinvolgimento Mobile.

Passare a **MainActivity.java** e aggiungere le operazioni seguenti per sostituire la classe base di **MainActivity** a **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Se la classe non è *attività*, consultare [Avanzate Reporting Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) per la procedura di ereditare da classi diverse.


Commento la riga seguente per questo scenario di esempio semplice:

    // setSupportActionBar(toolbar);

Se si desidera mantenere la `ActionBar` nell'app, vedere [Avanzate segnalazione Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Attivare le notifiche push e la messaggistica in app

Durante una campagna coinvolgimento Mobile consente di interagire con e raggiungere gli utenti con le notifiche push e la messaggistica in app. In questo modulo è chiamato portata nel portale di impegno Mobile.
La sezione seguente configura automaticamente l'app per riceverle.

### <a name="copy-sdk-resources-in-your-project"></a>Copia SDK risorse del progetto

1. Tornare al contenuto di download SDK e copiare la cartella di **risoluzione** .

    ![][10]

2. Passare a Android Studio, selezionare la directory **principale** dei file di progetto e quindi incollarlo per aggiungere risorse al progetto.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Passare a [SDK Android](mobile-engagement-android-sdk-overview.md) per ottenere informazioni dettagliate sull'integrazione SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
