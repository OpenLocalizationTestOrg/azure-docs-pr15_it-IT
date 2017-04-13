<properties
    pageTitle="Guida introduttiva di Azure Mobile impegno per le applicazioni Web | Microsoft Azure"
    description="Informazioni su come usare Azure Mobile coinvolgimento con le notifiche push e analitica per Web Apps."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Guida introduttiva di Azure Mobile impegno per Web Apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di Web App.

In questa esercitazione richiede le operazioni seguenti:

+ Visual Studio 2015 o un altro editor di propria scelta
+ [Web SDK](http://aka.ms/P7b453) 

Questo SDK Web in anteprima solo supporta Analitica al momento e non supporta ancora le notifiche push browser o in app inviare. 

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Configurazione di impegno per dispositivi mobili per un'app Web

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato una "integrazione di base," che è il numero minimo necessario per la raccolta di dati.

Verrà creata un'app web di base con Visual Studio per illustrare l'integrazione anche se è possibile eseguire i passaggi per tutte le applicazioni web creata anche all'esterno di Visual Studio. 

###<a name="create-a-new-web-app"></a>Creare una nuova App Web

I passaggi seguenti presuppongono l'utilizzo di Visual Studio 2015 anche se i passaggi sono simili nelle versioni precedenti di Visual Studio. 

1. Avviare Visual Studio e nella schermata **Home** selezionare **Nuovo progetto**.

2. Nella finestra popup selezionare **Web** -> **Applicazione Web ASP.Net**. Compilazione app **nome**, **percorso** e il **nome della soluzione**e quindi fare clic su **OK**.

3. In **Selezionare un modello di** scelta rapida selezionare **vuota** in **ASP.Net 4.5 modelli** e fare clic su **OK**. 

A questo punto è stato creato un nuovo progetto vuoto Web App in cui si prevede di integrare Azure Mobile coinvolgimento Web SDK.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Creare una nuova cartella denominata **javascript** della soluzione e aggiungere il file Web SDK JS **azure engagement.js** al suo interno. 

2. Aggiungere un nuovo file denominato **main.js** in questa cartella javascript con il codice seguente. Assicurarsi di aggiornare la stringa di connessione. Questo `azureEngagement` oggetto verrà utilizzato per accedere a Web SDK metodi. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio con file js][1]

##<a name="enable-real-time-monitoring"></a>Attivare il monitoraggio in tempo reale

Per iniziare subito a inviare dati e assicurare che gli utenti attivi, è necessario inviare almeno un'attività a back-end coinvolgimento Mobile. Un'attività nel contesto di un'app web è una pagina web. 

1. Creare una nuova pagina denominata **Home. HTML** della soluzione e impostarla come pagina iniziale per un'app web. 
2. Includere due JavaScript che abbiamo aggiunto in precedenza in questa pagina aggiungendo quanto segue all'interno del tag corpo. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Aggiornare il tag corpo per chiamare del EngagementAgent `startActivity` metodo
        
        <body onload="engagement.agent.startActivity('Home')">

4. Ecco come apparirà la **Home. HTML**
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Estendere analitica

Di seguito sono attualmente disponibili con Web SDK che è possibile utilizzare per analitica tutti i metodi:

1. Attività/pagine:

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Eventi
        
        engagement.agent.sendEvent(name, extras);

3. Errori

        engagement.agent.sendError(name, extras);

4. Processi

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

