<properties 
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - portata" 
   description="Informazioni su come raggiungere gli utenti dell'applicazione con le notifiche push con coinvolgimento di Azure Mobile" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Come raggiungere gli utenti dell'applicazione con le notifiche push

In questo articolo descrive la scheda **raggiunga** del portale di **Impegno Mobile** . Utilizzare il portale **Mobile impegno** per monitorare e gestire le App per dispositivi mobili. Si noti che per iniziare a usare il portale è innanzitutto necessario creare un account **Azure Mobile coinvolgimento** . Per ulteriori informazioni, vedere [creare un account Azure Mobile coinvolgimento](mobile-engagement-create.md).

La sezione portata dell'interfaccia utente è lo strumento di gestione delle campagne di Push in cui è possibile creare/modifica/attivare/fine/monitor e ottenere le statistiche campagne di notifica Push e le caratteristiche che sono accessibili anche tramite l'API raggiunga (e alcuni elementi della scarsa Push API). Tenere presente che se si utilizza l'API o l'interfaccia utente, sarà necessario integrare Azure Mobile coinvolgimento e portata nell'applicazione in uso per ogni piattaforma con SDK prima di poter usare raggiunga campagne.

>[AZURE.NOTE] Numero di sezioni del portale di **Impegno Mobile** dell'interfaccia utente è disponibile il pulsante **Visualizza Guida** . Premere questo tasto per ottenere maggiori informazioni contestuali su una sezione.

## <a name="four-types-of-push-notifications"></a>Quattro tipi di notifiche Push
1.    Annunci - consentono di inviare messaggi pubblicitari agli utenti che reindirizzarli a un'altra posizione all'interno di un'applicazione o di inviarle a una pagina Web o un archivio all'esterno dell'applicazione. 
2.    Sondaggi - consente di raccogliere informazioni da utenti finali ponendo domande.
3.    Inserisce dati - consentono di inviare un file di dati binario o base 64. Le informazioni contenute in un push di dati viene inviate all'applicazione per modificare l'esperienza corrente degli utenti nell'app. L'applicazione deve essere in grado di elaborare i dati in un push di dati.

## <a name="campaign-details"></a>Dettagli campagna

È possibile modificare, clonare, eliminare o attivare campagne non state attivate, passare il mouse sopra i nomi o è possibile fare clic per aprirli. È possibile duplicare campagne sono già state attivate dal passaggio del mouse sui relativi nomi o è possibile fare clic per aprirli. Tuttavia, non è possibile modificare una campagna dopo che è stato attivato.
 
![Reach1][18]

## <a name="reach-feedback"></a>Raggiungere commenti e suggerimenti

Fare clic su **statistiche** per visualizzare i dettagli di una campagna di mano. La visualizzazione **semplice** fornisce una rappresentazione visiva sotto forma di un grafico a barre colonna sulle modifiche apportate dopo l'attivazione di una campagna. La visualizzazione **avanzata** sono presenti più granulari dettagli sul campagna push. Questi dettagli non sarà disponibili se si intende inviare una campagna di prova ad esempio un push inviato a un dispositivo di test. Ecco come deve interpretare i dettagli:

1. **Pushed** - consente di specificare il numero di messaggi inserito per i dispositivi. Questo numero dipenderà dei destinatari specificato durante la creazione della campagna push. Se non si specifica un gruppo di destinatari, quindi questo comando verrà inviato a tutti i dispositivi registrati. Ad esempio tutti gli altri servizi di push, sono le notifiche push non direttamente ai dispositivi ma inviarli invece della piattaforma rispettivi servizi di notifica Push specifici (PNS - APNS/GCM/WNS) in modo che offrono le notifiche per i dispositivi. 

2.  **Recapitati** - consente di specificare il numero di messaggi che vengono forniti da PNS al dispositivo e riconosciuto come ricevuti da Mobile coinvolgimento SDK. 
        
    *Motivi per cui recapitati contare sia minore numero inserito:*
    
    1. Se l'utente ha dopo la disinstallazione dell'app dal dispositivo, ma il PNS non sa affrontarlo quando che si invia il push per il PNS verrà rimosso il messaggio.
    2. Se il dispositivo è l'app ma periferiche stesse offline per un periodo di tempo, il PNS avrà esito negativo recapitare il messaggio al dispositivo. 
    3. Se il messaggio venga recapitato al dispositivo Mobile coinvolgimento SDK nell'app non riconosce il contenuto del messaggio, Elimina il messaggio. Questo problema può verificarsi se la personalizzazione della notifica nell'app generata un'eccezione che viene rilevato nel SDK e rilasciare i messaggi. Potrebbe verificarsi anche se l'app nel dispositivo è tramite una versione di SDK coinvolgimento Mobile non è in grado di comprendere la versione più recente di push messaggio inviato dalla piattaforma, ma si tratta solo quando l'app è stato aggiornato dopo la notifica è stata inviata dalla piattaforma servizi. Scheda **Avanzate** che indica il numero di messaggi sono stato eliminato. 
    4. In dispositivi iOS, i messaggi in alcuni casi non venga recapitati se uno dei due il dispositivo in batteria o se l'app è costituita notevole quantità di energia durante l'elaborazione di notifiche remote. Si tratta di un limite di dispositivi iOS.   

3.  **Visualizzato** - consente di specificare il numero di messaggi che vengono visualizzati correttamente all'utente di app sul dispositivo in forma di una notifica push/esterna-di-app del sistema nell'interfaccia di notifica o di una notifica all'interno dell'applicazione all'interno di app per dispositivi mobili.  Scheda **Avanzate** che indica quante sono state le notifiche di sistema e quante notifiche in app. 
    
    *Motivi per cui visualizzato contare sia minore recapitati conteggio (in attesa deve essere presentata)*
    
    1. Se la campagna di notifica dispone di una data di fine per è possibile che la notifica è stata recapitata ma quando è stato acquistato il tempo per aprire e visualizzare all'utente di app, è stata già scaduto in modo che non è mai stato visualizzato.   
    2. Se la notifica è una notifica all'interno dell'applicazione la notifica viene visualizzata solo quando l'utente app apre l'app. In casi in cui l'utente app non è aperta l'app SDK segnalerà che la notifica è stata recapitata ma non ancora visualizzata finché non viene aperta l'app. 
    2. Se la notifica è una notifica di app e configurato per essere visualizzate in una specifica attività/schermata quindi anche la notifica verrà indicata come recapitato ma non ancora recapitato finché l'utente verrà aperta l'app su uno schermo specifico. 
    
4.  **Interazioni dell'utente** - consente di specificare il numero di messaggi all'utente di app è interazione e includerà i messaggi che vengono prese in considerazione o terminato. 

    - *L'utente app possibile azione una notifica in uno dei modi seguenti:*
            
        1. Se la notifica è una notifica di sistema/esterna-di-app o una notifica nell'app inviato come sola notifica, quindi l'utente app fa clic su messaggio di notifica.
        2. Se la notifica è una notifica all'interno dell'applicazione con un testo o la visualizzazione web o sondaggi l'utente app fa clic sul pulsante di azione nel messaggio di notifica.
        3. Se la notifica è una notifica all'interno dell'applicazione con la visualizzazione web utente app fa clic su un URL web solo nella visualizzazione [Android]
    
    - *L'utente di app per chiudere la notifica in uno dei modi seguenti:*
    
        1. Fare clic sul pulsante chiudere della notifica di direttamente. 
        2. Scorrere rapidamente verso fuori sede o eliminazione di notifica. 
        3. Notifiche all'interno dell'applicazione con il contenuto di testo o del web e sondaggi vengono in genere visualizzate all'utente di app in due passaggi. Vedono prima di tutto una notifica e quando si fa clic su di esso, vedono il contenuto di testo/web/sondaggio. L'utente di app per chiudere la notifica in uno di questi passaggi e i dettagli in visualizzazione avanzata acquisisce seguente. 

5.  **Actioned** - consente di specificare il numero di messaggi che sono stati esplicitamente prese in considerazione dall'utente app. Questo è il numero più interessante come questa indica il numero di utenti app sono stato interessato dal messaggio inserite nel messaggio di notifica. 
 
> [AZURE.NOTE] In iOS e finestre piattaforme, se l'utente dispone dell'app aprire la campagna è stata una campagna "In qualsiasi momento" quindi è possibile che entrambi tutte le potenzialità app e le notifiche in app vengono visualizzati allo stesso tempo. Ciò potrebbe causare un conteggio visualizzato superiore al recapitati. Se l'utente interagisce o azioni la notifica, quindi anche il numero totale di utente interazioni/Actioned potrebbe essere maggiore di recapitati. 


![Reach2][19]

## <a name="see-also"></a>Vedere anche

- [Concetti][Link 6]
- [Risoluzione dei problemi del servizio di Guida][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
