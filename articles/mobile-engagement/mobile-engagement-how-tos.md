<properties 
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - portata come"
   description="Panoramica dell'interfaccia utente per Azure impegno per dispositivi mobili" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Per imparare a uso e gestione inserisce per la comunicazione agli utenti finali

Una volta SDK è completamente integrato con l'app, è possibile iniziare a utilizzare la sezione portata dell'interfaccia utente per le notifiche Push per gli utenti dell'app.  

## <a name="do-your-first-push-notification-campaign"></a>Eseguire la prima campagna di notifica Push
-    Verificare che la portata è integrato con l'app con SDK. 
-    Selezionare l'applicazione
 
![First1][1]

-    Passare alla sezione "Portata" e fare clic su "nuovo annuncio"
 
![First2][2]

-    Creare una nuova campagna e denominarla
 
 ![First3][3]

-    Selezionare come la notifica inviare, come In-app solo
 
![First4][4]

-    Creare il messaggio che si desidera push
 
![First5][5]

-    È possibile scrivere un titolo nella notifica (facoltativo).
-    Scrivere il contenuto del messaggio push.
-    È possibile caricare un'immagine. Tenere presente che le dimensioni del file non superino 32.768 byte.
-    È inoltre possibile selezionare ulteriori opzioni, ma per lo stato attivo di questa esercitazione, è possibile vedere che in un secondo momento.

-    Selezionare il tipo di contenuto come notifica solo
 
![First6][6]

-    Creare una campagna di push e verrà visualizzato nell'elenco delle campagne.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testare la campagna di notifica Push
![Test1][8]

-    Eseguire la registrazione del dispositivo.
-    Fare clic sulla casella di controllo del dispositivo che si desidera inserire.
-    Fare clic sul pulsante "Test" per inviare il push al dispositivo.
 
![Test2][9]

-    Attivare la campagna
 
![Test3][10]

-    Ora che è stata creata la campagna che è necessario attivare il servizio di notifica push agli utenti.
 
## <a name="send-personalized-pushes"></a>Inviare push personalizzate
-    In questo esempio viene creato un push nel punto in cui si immette un codice di rimborso personalizzato in notifica push.
 
![Personalize1][11]

Funzionamento di personalizzazione sostituendo così un indicatore, da un tag info app, è necessario assicurarsi che l'utente ha la corretta informazioni app definite prima di tutto. In questo esempio gli utenti di destinazione avrà un tag info app denominato rebate_code definiti.
Come illustrato sopra il contenuto di notifica push include il simbolo $ indicatore {rebate_code} che indica che si tratti da sostituire con il contenuto effettivo del contrassegno info app.

> Avviso: Se il contrassegno di informazioni app non è definito per l'utente, l'utente non riceverà il push.

-    Risultato
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>È possibile personalizzare ulteriormente il testo la notifica
![Personalize3][13]

-    Inclusi il titolo della notifica della
-    E il contenuto del messaggio.
-    Scegliere il tipo di annuncio (visualizzazione di testo o Web)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Corpo di un annuncio può essere personalizzato anche con:
-    URL dell'azione, se si desidera personalizzare la pagina di destinazione
-    Il titolo
-    Corpo del messaggio.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Differenziare di notifica Push (Avanti o indietro app)
-    Scegliere il tipo di notifica verrà push, selezionare l'applicazione, passare alla sezione "Raggiungere", selezionare o creare una campagna di push e passare alla sezione "Notifica".
 
-    Fare clic sulla modalità di consegna"" desiderato.
-    Fare clic sulla casella di controllo "Limitare l'attività" quando si desidera che la notifica si verifica in attività specifiche (schermate).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modalità di consegna "Fuori App"
![Differentiate2][16]

"Fuori App" modalità di consegna fornisce una notifica push quando l'applicazione viene chiusa. Verrà visualizzata la notifica push standard.
Quando si seleziona "fuori app", è necessario aver già specificato i certificati di piattaforma che l'applicazione è basato su (APN o GCM).

### <a name="see-also"></a>Vedere anche
-  [Servizio di notifica: dei certificati Push Apple](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud messaggistica-certificato] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"in-App solo" la modalità di recapito
![Differentiate3][17]

Modalità di consegna "In-App solo" notifica push l'applicazione è in esecuzione.
Per questa notifica, non è necessario accedere tramite il sistema APN e GCM.
È possibile utilizzare il sistema di recapito in app per raggiungere gli utenti finali.
Completamente, è possibile personalizzare la notifica e decidere la visualizzazione delle attività (schermo) la notifica.

### <a name="anytime-delivery-mode"></a>Modalità di consegna "In qualsiasi momento"
È possibile scegliere una modalità di recapito "In qualsiasi momento", garantisce l'amministratore per raggiungere l'utente finale se l'applicazione è in esecuzione o meno.
Quando si seleziona "In qualsiasi momento", è necessario aver già specificato i certificati dalla piattaforma creazione di un'applicazione al (APN o GCM). 
 
## <a name="schedule-a-push-campaign"></a>Pianificare una campagna Push
### <a name="plan-to-start-a-campaign"></a>Piano per avviare una campagna
![Shedule1][18]

È il 21 di marzo e si dispone di un annuncio per rendere le per il 22 di marzo mezzanotte. Non è necessario mantenere dietro l'interfaccia per eseguire un comando! È possibile pianificare in anticipo il minuto esatto notifiche verranno inviate.
-    Deselezionare "Nessuno" casella di controllo e selezionare un'ora di inizio 
-    Scegliere la data e ora che si vuole iniziare la campagna push.

### <a name="plan-to-end-a-campaign"></a>Piano per terminare una campagna
![Shedule2][19]

Si desidera campagna interrompere su 25 di marzo 3 PM 00 ma si conosce che non sia presente per farlo.
Non è necessario rimanere dietro l'interfaccia di push! È possibile pianificare in anticipo il minuto esatto che della campagna verrà interrotto.
-    Fare clic su "Nessuno" casella di controllo o selezionare un'ora di fine
-    Scegliere la data e ora desiderate per terminare la campagna push.

### <a name="end-a-campaign-manually"></a>Terminare una campagna manualmente
![Shedule3][20]

Per impostazione predefinita, "Nessuna" siano selezionate le caselle di controllo.
Questo errore indica che la campagna verrà avviata non appena è necessario attivarlo nella sezione portata e terminerà quando si interromperà sulla sezione portata.
 
> Nota: Campagne create senza una data di fine memorizzare il push in locale nel dispositivo e mostrano alla successiva apertura dell'app, anche se la campagna manualmente è terminata.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Ottimizzare una notifica Push con una visualizzazione di testo
### <a name="what-is-a-text-view"></a>Che cos'è una visualizzazione di testo?
![TextView1][21]

Una visualizzazione di testo viene visualizzata una finestra popup con contenuto di testo. Questa opzione viene visualizzata dopo l'utente finale ha fatto clic su notifica push.
Una visualizzazione di testo è possibile presentare contenuto più all'utente finale. È anche la possibilità di presentare una chiamata all'azione, ad esempio il passaggio a una pagina dell'app, il reindirizzamento a un punto vendita, aprire una pagina web, inviare un messaggio di posta elettronica, avviare una ricerca localizzate geografico, ecc...

### <a name="example-text-view"></a>Esempio: Visualizzazione di testo
-    Creare una campagna di notifica Push nella sezione "Portata" e specificare un nome per la campagna
 
![TextView2][22]

-    Scrivere il messaggio che verrà visualizzata la notifica.
-    Selezionare il tipo di contenuto annuncio di "testo"
 
![TextView3][23]

> Nota: quando si inserisce una visualizzazione di testo, è sempre dotato di una notifica prima di tutto. 

- Definire il testo (dopo avere selezionato il contenuto di annuncio di testo, verrà visualizzata la sezione secondari, che consente di definire il testo che si desidera visualizzare.)
 
![TextView4][24]

-    Scrivere il titolo che verrà visualizzato nella parte superiore del messaggio.
-    Scrivere il contenuto principale della visualizzazione di testo.
-    Scrivere il contenuto che verrà visualizzato sul pulsante di azione (un pulsante di azione consente di effettuare un'azione specifica, ad esempio apertura di una pagina dell'applicazione, il reindirizzamento a un archivio di App o tutti i tipi di origini che è possibile fornire l'applicazione).
-    Scrivere il contenuto che verrà visualizzato sul pulsante Esci (facendo clic sul pulsante Esci, la visualizzazione di testo non sarà più visibile.)
 
-    Creare una campagna di notifica push e verrà visualizzato nell'elenco delle campagne.
 
![TextView5][25]

-    Attivare la campagna di notifica push per inviare la visualizzazione di testo per gli utenti.
 
![TextView6][26]

-    Risultato
 
![TextView7][27]

-    L'utente riceve la notifica e fare clic su di esso.
-    Visualizzazione di testo viene visualizzato come un menu a comparsa consentire all'utente di interagire con essi.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Ottimizzare una notifica Push con una visualizzazione Web
### <a name="what-is-a-web-view"></a>Che cos'è una visualizzazione Web?
![WebView1][28]

Una visualizzazione web viene visualizzata una finestra popup con contenuto web. Questa opzione viene visualizzata quando l'utente finale ha fatto clic su notifica push.
Una visualizzazione web consente di altre interagiscono con l'utente finale.
È anche la possibilità di presentare all'azione, ad esempio il reindirizzamento da App Store, aprire una pagina web, inviare un messaggio di posta elettronica, avviare una ricerca localizzate geografico, ecc...

### <a name="example-web-view"></a>Esempio: La visualizzazione Web
-    Creare una campagna di Push nella sezione "Portata" e specificare un nome per la campagna.
 
![WebView2][29]

-    Scrivere il messaggio che verrà visualizzata la notifica.
-    Selezionare il tipo di contenuto annuncio come "web"
 
![WebView3][30]

### <a name="about-announcement-types"></a>Informazioni sui tipi di annuncio:
- Notifica solo: si tratta di una notifica standard semplice. Il che significa che se si fa clic su di esso, non verrà visualizzato alcun visualizzazione aggiuntivi, ma si verifica solo l'azione associata a tale.
- Annuncio di testo: si tratta di una notifica che coinvolge l'utente abbia un aspetto in una visualizzazione di testo.
- Annuncio Web: si tratta di una notifica che coinvolge l'utente abbia un aspetto in una visualizzazione web.
Selezionare il contenuto "Annuncio Web".

> Nota: Quando si inserisce una visualizzazione web, è sempre dotato di una notifica prima di tutto.

- Definire il contenuto web (dopo avere selezionato il contenuto di annuncio web verrà visualizzata la sezione, consente di definire il contenuto di visualizzazione web che si desidera visualizzare.)

 
![WebView4][31]

-    Scrivere il titolo che verrà visualizzato nella parte superiore del messaggio (facoltativo).
-    Scrivere il codice HTML qui.
-    Fare clic su Modifica pulsante modalità per passare all'edizione e viene illustrato cosa accade ad esempio l'origine.
-    Scrivere il contenuto che verrà visualizzato sul pulsante di azione (un pulsante di azione consente di effettuare un'azione specifica, ad esempio apertura di una pagina dell'applicazione, il reindirizzamento a un archivio o tutti i tipi di origini che è possibile fornire l'applicazione).
-    Scrivere il contenuto che verrà visualizzato sul pulsante Esci (facendo clic sul pulsante Esci, la visualizzazione web scompariranno).
 
-    Risultato
 
![WebView5][32]

-    L'utente riceve la notifica e fare clic su di esso.
-    Visualizzazione di testo viene visualizzato come un menu a comparsa consentire all'utente di interagire con essi.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
