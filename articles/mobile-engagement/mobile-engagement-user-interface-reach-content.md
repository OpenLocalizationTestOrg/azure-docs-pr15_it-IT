<properties 
   pageTitle="Interfaccia utente di Azure impegno per dispositivi mobili - portata contenuto" 
   description="Informazioni su come gestire il contenuto univoco dei diversi tipi di campagne di notifica push di Azure Mobile coinvolgimento" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Come gestire il contenuto univoco dei diversi tipi di campagne di notifica push
 
È possibile utilizzare la sezione del contenuto di una nuova campagna di mano per modificare il contenuto di annunci, sondaggi, inserisce dati e riquadri (solo per Windows Phone). L'impostazione del contenuto delle campagne Push è specifico per il tipo della campagna. 
 
### <a name="content-types"></a>Tipi di contenuto:
- Annunci
- Sondaggi
- Inserisce dati
- Riquadri (solo per Windows Phone)
 
## <a name="content-of-announcements"></a>Contenuto di annunci
 ![Portata Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Scegliere il tipo di annuncio:
-    Notifica solo: si tratta di una notifica standard semplice. Il che significa che se si fa clic su di esso, non verrà visualizzato alcun visualizzazione aggiuntivi, ma si verifica solo l'azione associata a tale.
-    Annuncio di testo: si tratta di una notifica che coinvolge l'utente abbia un aspetto in una visualizzazione di testo.
-    Annuncio Web: si tratta di una notifica che coinvolge l'utente abbia un aspetto in una visualizzazione web.

### <a name="see-also"></a>Vedere anche
- [Raggiungere - come visive - annunci][Link 3] 

### <a name="about-web-view-announcements"></a>Su Web per visualizzare gli annunci:
Occorrenze del criterio "{deviceid}" nel codice HTML o codice JavaScript che qui verranno sostituite automaticamente dall'identificatore del dispositivo di visualizzazione dell'annuncio. Questo è un modo semplice per recuperare gli identificatori di dispositivo Azure Mobile impegno in un servizio web esterni ospitati in back-office.
Se si desidera creare una visualizzazione a schermo intero web (senza l'azione e uscita pulsanti predefiniti sono disponibili) è possibile utilizzare le funzioni seguenti dal codice JavaScript dell'annuncio visualizzazione web: 

-    eseguire l'azione annuncio: ReachContent.actionContent()
-    uscire dall'annuncio: ReachContent.exitContent()
 
### <a name="choose-your-action"></a>Scegliere l'azione:

### <a name="about-action-urls"></a>Sull'URL di azione:
Un URL che può essere interpretato dal sistema operativo del dispositivo di destinazione può essere utilizzato come un URL di azione.
Qualsiasi dedicato URL che potrebbe supportare l'applicazione (ad esempio, per rendere gli utenti di passare a un determinato screen) possono essere utilizzati anche come un URL di azione.
Ogni occorrenza del modello {deviceid} viene automaticamente sostituito da identificatore del dispositivo che esegue l'azione. Può essere utilizzato per recuperare più facilmente gli identificatori di dispositivo Azure Mobile coinvolgimento tramite un servizio web esterni ospitati in back-office.

- **Android + iOS azioni**
    - Aprire una pagina web
    - http://\[dominio di siti web\] 
    - Esempio: http://www.azure.com
    - Inviare un messaggio di posta elettronica
    - mailto:\[destinatario di posta elettronica\]?subject =\[oggetto\]& corpo =\[messaggio\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Inviare un SMS
    - SMS:\[numero di telefono\] 
    - Esempio: sms:2125551212
    - Comporre un numero di telefono
    - Tel:\[numero di telefono\] 
    - Esempio: tel:2125551212
- **Azioni sola Android**
    - Scaricare un'applicazione nel Play Store
    - market://details?id=\[pacchetto dell'app\] 
    - Esempio: market://details?id=com.microsoft.office.word
    - Avviare una ricerca localizzate geografico
    - geo:0, 0?q =\[query di ricerca\] 
    - Esempio: geo:0, 0? q = starbucks, Parigi
- **azioni solo iOS**
    - Scaricare un'applicazione su App Store
    - /id /app/ [nome applicazione] http://iTunes.Apple.com/ [country] [id app]? mt = 8 
    - Esempio: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Azioni di Windows
    - Aprire una pagina web
    - http://\[dominio di siti web\] 
    - Esempio: http://www.azure.com
    - Inviare un messaggio di posta elettronica
    - mailto:\[destinatario di posta elettronica\]?subject =\[oggetto\]& corpo =\[messaggio\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Inviare un SMS (App Store Skype obbligatorio)
    - SMS:\[numero di telefono\] 
    - Esempio: sms:2125551212
    - Comporre un numero di telefono (App Store Skype obbligatorio)
    - Tel:\[numero di telefono\] 
    - Esempio: tel:2125551212
    - Scaricare un'applicazione nel Play Store
    - MS-windows-store: PDP?PFN =\[ID pacchetto app\] 
    - Esempio: ms-windows-store: PDP? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Avviare una ricerca bingmaps
    - bingmaps:?q =\[query di ricerca\] 
    - Esempio: bingmaps:? q = starbucks, Parigi
    - Utilizzare una combinazione personalizzata
    - \[combinazione personalizzata\]://\[parametri combinazione personalizzata\] 
    - Esempio: myCustomProtocol://myCustomParams
    - Utilizzare un pacchetto dati (App Store per estensione leggere obbligatorio)
    - \[cartella\]\[dati\]. \[estensione\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Generare un URL di verifica:
-    Vedere la sezione "Impostazioni" del <UI Documentation> per istruzioni sulla creazione di un URL di verifica che consente agli utenti di scaricare una delle altre applicazioni.
 
### <a name="define-the-texts-of-your-announcement"></a>Definire il testo delle annuncio
Digitare il titolo, il contenuto e testo pulsante delle annuncio. È possibile assegnare un gruppo di destinatari di una campagna futura in base ai commenti portata della come utenti risposto alla campagna. Destinatari possono essere in base ai commenti di se la campagna è stata appena inserita, risposta, prese in considerazione o terminato.

### <a name="see-also"></a>Vedere anche
- [Interfaccia utente documentazione - portata - nuovo Push criterio][Link 28]

## <a name="content-of-polls"></a>Contenuto di sondaggi
![Portata Content2][31] inserire il titolo, descrizione e testo pulsante delle annuncio. Aggiungere quindi le opzioni per le risposte alle proprie domande e domande.
È possibile assegnare un gruppo di destinatari di una campagna futura in base ai commenti portata della come utenti risposto alla campagna. Destinatari possono essere basate sulle se la campagna è stata appena inserita, risposta, prese in considerazione o terminato. Destinatari possono anche essere basate sulle commenti e suggerimenti answer sondaggio, in cui la domanda e risposta scelta vengono utilizzati come criteri.

### <a name="see-also"></a>Vedere anche
- [Interfaccia utente documentazione - portata - nuovo Push criterio][Link 28]
 
## <a name="content-of-data-pushes"></a>Inserisce il contenuto dei dati
![Portata Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Scegliere il tipo di dati:
- Testo
- Dati binari
- Dati di base 64

### <a name="define-the-content-of-your-data"></a>Definire il contenuto dei dati
- Se si sceglie di inserire i dati di testo, copiare e incollare il testo nella casella "contenuto".
- Se si sceglie di inserire i dati binario o base 64, utilizzare il pulsante "caricare il file" per caricare il file.
- È possibile assegnare un gruppo di destinatari di una campagna futura in base ai commenti portata della come utenti risposto alla campagna. Destinatari possono essere basate sulle se la campagna è stata appena inserita, risposta, prese in considerazione o terminato.

### <a name="see-also"></a>Vedere anche
- [Interfaccia utente documentazione - portata - nuovo Push criterio][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Contenuto dei riquadri (solo per Windows Phone)
![Portata Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definire il contenuto della sezione
Il riquadro payload è il testo da visualizzare nel riquadro dell'app nei dispositivi Windows Phone.
Un riquadro push è la versione di Microsoft Push Notification Service (MPNS) di un nativo push per Windows Phone. Il riquadro push tipo è l'unico tipo push che non dispone di una risposta e pertanto non è possibile generare i destinatari delle campagne future sui risultati di una campagna di push riquadro. 

### <a name="see-also"></a>Vedere anche
- [API documentazione - portata API - nativo Push][Link 4]

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
 
