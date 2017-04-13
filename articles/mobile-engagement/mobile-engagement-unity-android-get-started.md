<properties
    pageTitle="Guida introduttiva di Azure Mobile impegno per la distribuzione verità Android"
    description="Informazioni su come usare Azure Mobile coinvolgimento con Analitica e delle notifiche Push verità App per la distribuzione in dispositivi iOS."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Guida introduttiva di Azure Mobile impegno per la distribuzione verità Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e come inviare le notifiche push per segmentato agli utenti di un'applicazione di verità durante la distribuzione in un dispositivo Android.
In questa esercitazione utilizza il rullo di verità classica un'esercitazione ballo come punto di partenza. È necessario seguire la procedura descritta in questa [esercitazione](mobile-engagement-unity-roll-a-ball.md) prima di procedere con l'integrazione di impegno Mobile che è presentare nell'esercitazione riportata di seguito. 

In questa esercitazione richiede le operazioni seguenti:

+ [Editor unità](http://unity3d.com/get-unity)
+ [Unità di impegno per dispositivi mobili SDK](https://aka.ms/azmeunitysdk)
+ Google Android SDK

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Configurazione di dispositivi mobili impegno per l'app Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

###<a name="import-the-unity-package"></a>Importare il pacchetto di unità

1. Download del [pacchetto Mobile coinvolgimento verità](https://aka.ms/azmeunitysdk) e salvarlo nel computer locale. 

2. Passare a **risorse -> Importa pacchetto -> Package personalizzata** e selezionare il pacchetto è stato scaricato nel passaggio precedente. 

    ![][70] 

3. Verificare che tutti i file selezionati e fare clic su **Importa** . 

    ![][71] 

4. Dopo l'importazione viene completata correttamente, verrà visualizzato file SDK importati nel progetto.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Aggiornare il EngagementConfiguration

1. Apre un file di script **EngagementConfiguration** dalla cartella SDK aggiornare il **ANDROID\_connessione\_stringa** con la stringa di connessione è stato acquistato in precedenza dal portale di Azure.  

    ![][73]

2. Salvare il file 

3. Eseguire **File -> coinvolgimento -> generare manifesto Android**. Questo è il plug-in aggiunti da Mobile coinvolgimento SDK e facendo clic su di essa viene aggiornato automaticamente con le impostazioni del progetto. 

    ![][74]

> [AZURE.IMPORTANT] Assicurarsi di eseguire questa applicazione ogni volta che si aggiorna il file **EngagementConfiguration** in caso contrario che non rifletteranno le modifiche nell'app. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurare l'app per rilevamento di base

1. Apri dello script **PlayerController** collegato all'oggetto Player per la modifica. 

2. Aggiungere la seguente istruzione:

        using Microsoft.Azure.Engagement.Unity;

3. Aggiungere le operazioni seguenti per la `Start()` metodo
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Distribuire ed eseguire l'app
Assicurarsi di avere installato nel computer in uso prima di distribuire questa app verità nel dispositivo Android SDK. 

1. Connettere un dispositivo Android nel computer in uso. 

2. Apre un **File -> Impostazioni di compilazione** 

    ![][40]

3. Selezionare **Android** e quindi fare clic su **Cambia piattaforma**

    ![][51]

    ![][52]

4. Fare clic su **impostazioni di Windows Media Player** e fornire un identificatore di aggregazione valido. 

    ![][53]

5. Infine fare clic sul **Ed esecuzione compilazione**

    ![][54]

6. Potrebbe essere necessario specificare un nome di cartella per archiviare il pacchetto Android. 

7. Se tutto va bene, il pacchetto verrà distribuito al dispositivo connesso e verrà visualizzata la partita verità sul telefono! 

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivare le notifiche push e la messaggistica in app

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###<a name="update-the-engagementconfiguration"></a>Aggiornare il EngagementConfiguration

1. Apre un file di script **EngagementConfiguration** dalla cartella SDK aggiornare il **ANDROID\_GOOGLE\_numero** con il **Numero di progetto Google** ottenuto in precedenza dal portale di Google Cloud sviluppo. Si tratta di una stringa di valore dunque, verificare di racchiudere tra virgolette doppie. 

    ![][75]

2. Salvare il file. 

3. Eseguire **File -> coinvolgimento -> generare manifesto Android**. Questo è il plug-in aggiunti da Mobile coinvolgimento SDK e facendo clic su di essa viene aggiornato automaticamente con le impostazioni del progetto. 

    ![][74]

###<a name="configure-the-app-to-receive-notifications"></a>Configurare app per ricevere una notifica

1. Apri dello script **PlayerController** collegato all'oggetto Player per la modifica. 

2. Aggiungere le operazioni seguenti per la `Start()` metodo

        EngagementReachAgent.Initialize();

3. Ora che l'app viene aggiornato, distribuire ed eseguire l'app in un dispositivo seguendo le istruzioni fornite di seguito. 

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
