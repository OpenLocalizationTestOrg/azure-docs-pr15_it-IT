<properties 
    pageTitle="Inviare notifica personalizzata con coinvolgimento di Azure Mobile" 
    description="Come inviare notifiche personalizzate facilmente individuabili perché includono informazioni del profilo utente per le notifiche ad esempio i nomi"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Personalizzare le notifiche includendo nome utente

Nella ricerca di per rendere più interessante le notifiche per gli utenti di app, è consigliabile personalizzare loro. Un approccio potente comprende in modo selettivo utilizzando i nomi di utenti app per risolvere le notifiche per renderli più personale. Una parola di attenzione, è necessario considerare aggiungere i nomi utente per le notifiche con attenzione poiché se usare in modo eccessivo questa strategia quindi può provenire come subdolo per alcuni utenti di app. È necessario che si consente all'utente acconsentire esplicitamente al offrire questi dettagli personali prima di iniziare a utilizzare questo comando per l'utente autorizzazione completa di app per dispositivi mobili. 

Tecnicamente, con coinvolgimento di Azure Mobile, è possibile eseguire la personalizzazione di notifiche seguendo la procedura seguente si userà lo scenario di nome utente nelle notifiche. Si userà il concetto di informazioni App o tag cui i valori Impossibile trasmessi tramite gli SDK integrata nell'App Mobile oppure tramite l'API. Queste informazioni sull'App o tag può essere utilizzata:

1. per l'assegnazione del notifiche a utenti specifici in base ai valori delle informazioni di App o 
2. come segnaposto per le notifiche che verranno sostituite con valori specifici per il dispositivo/utente durante l'invio di notifiche a tale dispositivo. 

> [AZURE.IMPORTANT] Si noti che la velocità di invio di notifiche visualizzerà una riduzione a causa di queste operazioni aggiuntive di sostituire i valori di informazioni app con ogni notifiche. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registrare informazioni App nel portale di impegno per dispositivi mobili

1) Si inizia con la registrazione di informazioni App o tag nel portale di Azure. Passare a **Impostazioni** -> **Tag (informazioni App)** per l'oggetto.  

![][1]  

2) Fare clic su **nuovo tag (informazioni app)** e specificare il nome come *nome_utente* e il tipo come *stringa* e fare clic su **Invia**. 

![][2]

3) Infine, si vedrà questo informazioni app registrata simile al seguente:

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Invia informazioni sugli App dal client SDK

Di seguito si sta utilizzando l'esempio app universale di Windows ma metodi equivalenti per il nostro altri SDK anche. 

Se si dispone di un metodo in app per dispositivi mobili in cui viene visualizzato le informazioni del profilo da parte dell'utente ad esempio i nomi probabilmente dopo l'autenticazione loro, eseguiranno una chiamata `SendAppInfo` metodo qui e specificare il valore della `user_name` informazioni app registrati in precedenza in Mobile coinvolgimento servizio back-end. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Inviare notifiche personalizzate

Questo punto, è possibile inviare le notifiche tramite questo **nome_utente**. 

1) Accedere al portale di impegno Mobile nella scheda **raggiungere** per creare una notifica ed è possibile utilizzare questo segnaposto nel formato seguente in un punto qualsiasi nel corpo o il titolo della notifica. 

![][4]  

> [AZURE.NOTE] Tutti gli utenti per cui le informazioni di app nome_utente non sono impostata, verrà alcuna notifica. Se si esegue la campagna di notifica nella modalità di prova e se non si dispone delle informazioni app impostare quindi riceverai ', ' carattere da sostituire il segnaposto. 

2) Quando coinvolgimento Mobile verrà selezionare un dispositivo per inviare la notifica, che verrà esaminare informazioni questa app e sostituire il valore del segnaposto.  
Ad esempio, se è stata impostata `str = "Scott"` per un utente da dispositivo registrazione verrà ricevuto associata a informazioni app di **nome_utente = SCOTT** per questo utente e l'utente visualizzerà una di fuori di notifica push app nel formato seguente. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

