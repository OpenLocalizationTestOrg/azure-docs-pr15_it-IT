<properties
    pageTitle="Applicazione demo Mobile coinvolgimento Azure | Microsoft Azure"
    description="Descrive come scaricare, come utilizzare e vantaggi dell'uso delle app demo Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Applicazione demo Mobile coinvolgimento Azure

Abbiamo pubblicata un'app di demo Azure Mobile impegno per **iOS**, **Android**e **Windows** piattaforme consentono di trovare risorse utili e altre informazioni sull'impegno di dispositivi mobili.

L'app consente di:

- Trovare facilmente collegamenti utili alle risorse Mobile coinvolgimento come video, documentazione, i forum di supporto e dove è possibile per generare richieste di funzionalità.
- Notifiche di esempio esperienza supportate da coinvolgimento Mobile per avere un'idea delle applicazioni per dispositivi mobili.
- Utilizzare un'implementazione di riferimento per lo Studio su campioni come implementare coinvolgimento Mobile nell'applicazione. È possibile capire come:

    - Raccogliere dati analitica.
    - Implementare scenari avanzati notifica dei tipi, ad esempio *a schermo intero intermedio* o *a comparsa*.
    - Implementare sondaggi.
    - Implementare scenari di dati e push push invisibile.   

## <a name="app-installation"></a>Installazione di App
L'app è disponibile negli archivi app seguenti:

- **App demo universale di Windows**:

    - Scaricare l'app in cui [memorizzare l'App di Windows](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - L'app sviluppato come app universale di Windows 10. Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **iOS demo app**:

    - Scaricare l'app in [Apple archiviare](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - L'app stata sviluppata in iOS Swift. Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Demo android app**:

    - Scaricare l'app in [Google Play archiviare](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - Il codice sorgente è disponibile in [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![App demo universale di Windows][1]

![iOS demo app][2]
![app demo Android][3]


## <a name="usage"></a>Uso

È possibile usare l'app nei modi seguenti:

**Scaricare l'app in un dispositivo dall'applicazione memorizzare i collegamenti (forniti in precedenza):**

>[AZURE.IMPORTANT] Non è necessario un account Azure o necessità di collegare l'app back-end. L'app funziona in modo indipendente.

- Dopo aver ottenuto l'app nel dispositivo, è possibile accedere tramite i collegamenti nel menu a sinistra per trovare risorse utili sull'impegno di dispositivi mobili.
- Abbiamo creato il [feed RSS del servizio](https://aka.ms/azmerssfeed) in questa applicazione in modo che sta sempre aggiornati sugli aggiornamenti più recenti.
- È anche possibile accedere tramite gli scenari di notifica di esempio in cui si verifica il tipo di notifiche supportate da Mobile impegno per ogni piattaforma. Queste notifiche possono verificare durante l'in locale, ossia che è, è possibile fare clic sui pulsanti negli schermi per mostrare l'esperienza di notifiche, equivale all'invio di notifiche da piattaforma coinvolgimento Mobile.

![Menu dell'App per Windows][4]

![Menu dell'App per iOS][5]
![menu App per Android][6]

**Scaricare il codice sorgente da collegamenti GitHub (forniti in precedenza):**

- Dopo aver scaricato il codice sorgente, aprirla nell'ambiente di sviluppo rispettivi - XCode per iOS, Android Studio per Android e Visual Studio per Windows.
- È necessario seguire la [procedura di base SDK integrazione](mobile-engagement-windows-store-dotnet-get-started.md) successivo, in modo che è in grado di connettersi questa app per la propria istanza di back-end coinvolgimento Mobile.
    - È necessario configurare una stringa di connessione nell'app.
    - È anche necessario configurare la piattaforma di notifica push per l'app.
- Si noterà che l'app stesso strumentazione con coinvolgimento di dispositivi mobili. Di conseguenza, quando si apre l'app dopo la connessione al back-end, sarà possibile visualizzare la sessione utente, attività, eventi e così via, nella scheda **Monitor** .
- Inoltre, sarà possibile inviare notifiche per questa app dall'istanza di impegno Mobile, invece di usare le notifiche locale.
    - Qui è possibile aggiungere il dispositivo come dispositivo test utilizzando la voce di menu **ottenere l'ID di dispositivo** nell'app. In questo modo un ID dispositivo quindi registrare come un dispositivo di test con l'istanza di back-end piattaforma.

    ![ID di dispositivo in Windows][7]

    ![ID di dispositivo in iOS][8]
    ![ID dispositivo Android][9]

## <a name="key-features-of-the-demo-app"></a>Caratteristiche chiave di app demo

- Come descritto in precedenza, con questa app sono presenti tutte le risorse chiave per dispositivi mobili coinvolgimento mano. È possibile accedere tramite i collegamenti nel menu a sinistra.

- Si possono verificare le notifiche di fuori dell'app per ogni piattaforma. Queste notifiche possono essere inviate come **notifica solo**, in cui facendo clic su messaggio di notifica semplicemente verrà visualizzata la schermata nativo dell'applicazione (mediante **collegamento completo**) - o un **annuncio il Web**, in cui si può offrire contenuto HTML aggiuntivo da back-end Mobile coinvolgimento da visualizzare quando si fa clic sul messaggio di notifica.

    ![Notifiche di fuori dell'app][29]

- In iOS, è necessario chiudere l'app per visualizzare le notifiche push di fuori dell'app o del sistema. È possibile visualizzare l'implementazione per l'aggiunta di **pulsanti di azione**, ad esempio quelli aggiunti a questa notifica di fuori dell'app per *commenti e suggerimenti* e *condividere* (in modo che l'utente può richiedere destra azione dalla notifica stesso).

    ![Notifiche di fuori dell'app in iOS][11] ![Visualizzazione di notifica di fuori dell'app in iOS][14]

- Android, le opzioni supportate vengono aggiunto testo su più righe (**Testo grande**) o un'immagine di notifica (**Ampio**) per la notifica, insieme i **pulsanti di azione** (come supportato da iOS).

    ![Notifiche di fuori dell'app in Android][12] ![Visualizzazione di notifica di fuori dell'app in Android][15]

- In Windows 10, è possibile visualizzare l'aspetto le notifiche nel PC. Questa notifica viene inoltre visualizzato nell'interfaccia Windows 10 **Centro notifiche**. Non è disponibile alcun supporto per l'aggiunta di **pulsanti di azione** al momento in Windows SDK.

    ![Notifiche di fuori dell'app in Windows][10] ![Visualizzazione di fuori dell'app in Windows][13]

- Possono verificarsi notifiche "in app" predefiniti per ogni piattaforma. Si tratta di un'esperienza in due passaggi in una finestra di **notifica** da visualizzare per primo. Quando si fa clic su esso, si apre un **annuncio**a schermo intero come visualizzato nella schermata seguente. Il contenuto di questo annuncio proviene dall'istanza di back-end coinvolgimento Mobile. SDK include i modelli per le notifiche e gli annunci. È possibile facilmente personalizzarli, come mostrato in questa app demo con l'aggiunta del logo e colori.  

    ![In app notifiche su Windows][16]

    ![Notifiche nell'app in iOS][17]  ![Notifiche in app nel dispositivo Android][18]

    **iOS**, **Android**

- È anche possibile usare la caratteristica di **categoria** di impegno Mobile per personalizzare l'esperienza predefinita. Nell'app demo sono state illustrati due modi per modificare l'esperienza delle notifiche. Si noti che la caratteristica di categoria non è ancora supportata in Windows SDK.

    **Intermedi a schermo intero:**

    ![In app notifica - categoria intermedio][30]

    ![Categoria intermedio iOS][21]  ![Categoria intermedio in Android][22]

    **Notifica popup:**

    ![In app notifica - categoria popup][31]

    ![Notifica popup in iOS][19]   ![Notifica popup in Android][20]

**iOS**, **Android**

- Coinvolgimento Mobile supporta anche un tipo specifico di notifica nell'app chiamato **sondaggi**. In questo modo è possibile inviare agli utenti di app segmentato sondaggi rapidi. È possibile aggiungere alle domande e le opzioni per ogni domanda come illustrato nella schermata seguente. Questo quindi ottenere visualizzato come una notifica in app per l'utente di app.   

    ![Notifiche di sondaggio][32]

    ![Sondaggio in Windows][26]

    ![Sondaggio in iOS][27]   ![Sondaggio in Android][28]

**iOS**, **Android**

- Coinvolgimento Mobile supporta anche l'invio di notifiche **Push dati** invisibile. Con queste notifiche, è possibile inviare dati dal servizio (ad esempio i dati JSON nell'esempio seguente), che è possibile gestire le app e intraprendere azioni. Un esempio è come stiamo cambiando il prezzo di un elemento in modo selettivo utilizzando le notifiche push di dati.

    ![Notifica push di dati][33]

    ![Notifica push di dati in Windows][23]

    ![Notifica push di dati in iOS][24]  ![Notifica push di dati in Android][25]

**iOS**, **Android**

> [AZURE.NOTE] È possibile visualizzare istruzioni dettagliate per uno di questi notifiche facendo clic su **fare clic qui per istruzioni su come inviare le notifiche relative a piattaforma coinvolgimento Mobile** in qualsiasi schermata di notifica di esempio.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
