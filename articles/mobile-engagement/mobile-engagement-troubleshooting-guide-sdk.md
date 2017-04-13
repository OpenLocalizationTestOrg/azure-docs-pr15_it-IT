<properties 
   pageTitle="Azure coinvolgimento Mobile risoluzione dei problemi guida - SDK" 
   description="Risoluzione dei problemi di integrazione SDK in Azure Mobile coinvolgimento" 
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

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guida alla risoluzione dei problemi per problemi di integrazione SDK

Di seguito sono possibili problemi che possono verificarsi con Azure Mobile coinvolgimento come si integra l'applicazione.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemi di SDK rilevati da un errore in un'altra area dell'applicazione

### <a name="issue"></a>Problema
- Interfaccia utente dati insieme errore (Analitica, monitoraggio, segmentazione o dashboard).
- Push errori (push non funzionano in app riduzione app).
- Avanzate funzionalità errori (verifica, Georilevazione o piattaforma che push specifico non funzionano).
- API errori (API fail spesso automaticamente senza messaggi di errore).
- Errori di servizio (nessuno dei Azure Mobile coinvolgimento funziona per l'applicazione).

### <a name="causes"></a>Cause

- La maggior parte dei problemi che devono essere risolti con Azure Mobile coinvolgimento SDK verranno scoperte da un errore nell'applicazione (ad esempio un errore di raccolta dati dell'interfaccia utente, errore push, errore funzionalità avanzata, errore API, un blocco delle applicazioni o interruzione del servizio evidente).  
- Se una particolare caratteristica Azure Mobile coinvolgimento mai ha lavorato nell'app prima, sarà necessario completare l'integrazione. 
- Se una particolare caratteristica Azure Mobile impegno è stata utilizza e arrestato, potrebbe essere necessario eseguire l'aggiornamento all'ultima versione con Azure Mobile coinvolgimento SDK. Tenere presente che una versione diversa di Azure Mobile coinvolgimento SDK per ogni piattaforma supportata da Azure Mobile coinvolgimento (Android, iOS, Windows e Windows Phone).

#### <a name="sdk-integration"></a>Integrazione di SDK

- Azure coinvolgimento Mobile integrato in modo non corretto SDK (Analitica).
- Raggiungere in modo non corretto integrata SDK (In App e dall'App inserisce).
- Certificato scaduto o non corretti produzione e DEV (solo per iOS).
- GCM o ADM non correttamente integrata SDK (Android solo - servizio specifico inserisce).
- Verifica non correttamente integrata SDK (archivio di installazione verifica).
- Prato posizione o la posizione GPS non correttamente integrata SDK (destinazione in base all'ubicazione geografico).


**Vedere anche:**

- [Documentazione SDK - integrazione Guide][Link 5] 
- [Risoluzione dei problemi guida - Push][Link 23]

#### <a name="sdk-upgrade"></a>Aggiornamento SDK

- È necessario eseguire l'aggiornamento SDK per risolvere i problemi con le versioni precedenti di SDK (spesso correlati alle versioni più recenti di dispositivo del sistema operativo).
- Disinstallare tutte le versioni precedenti dell'app sul dispositivo e reinstallare la versione più recente dell'app, registrare nuovamente l'ID di dispositivo dall'interfaccia utente di impegno Azure Mobile per verificare che il dispositivo è la versione più recente dell'app.

**Vedere anche:**

- [Documentazione SDK - note sulla versione](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentazione SDK - Guide aggiornamento](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>Altri SDK

- Gli errori in "AndroidManifest.xml" manifesto dell'applicazione possono causare Azure Mobile coinvolgimento non riuscire (solo Android).
- Un problema con integrazione SDK e l'uso di API comune consiste nel confondere le chiavi SDK e le API.

**Vedere anche:**

- [Concetti - glossario][Link 6]

## <a name="advanced-coding-issues"></a>Avanzate codifica problemi

### <a name="issue"></a>Problema
-  Codice specifico della piattaforma non direttamente correlati all'Azure Mobile coinvolgimento può causare problemi di iOS, Android e Windows Phone.

### <a name="causes"></a>Cause

- Molti avanzate di codifica con Azure Mobile coinvolgimento errori sono causati da codice specifico della piattaforma sviluppato in modo errato non direttamente correlati all'impegno Mobile Azure. È necessario consultare la documentazione specifica della piattaforma che sviluppano applicazioni per oltre la documentazione di Azure Mobile coinvolgimento (Android, iOS, Web, Windows e Windows Phone).
- Configurazione non corretta "categorie", non il collegamento da una notifica a un'altra posizione all'interno o all'esterno dell'app (solo Android). 
- Non impostazione "UIKit.framework" da "facoltativo" nel codice iOS, visualizza una "Simbolo di errore non trovato" e/o si blocca in dispositivi iOS precedenti (solo per iOS).
- I certificati scaduti o non correttamente utilizzando la versione di sviluppo o produzione del certificato, cause push problemi (solo per iOS).
- Esistono alcune limitazioni inerenti a una piattaforma Azure Mobile impegno non è possibile controllare (come il funzionamento dell'interfaccia di sistema per disconnettersi da app inserisce in iOS e Android).
- Azure Mobile coinvolgimento pubblica un elenco completo dei pacchetti interni utilizzato da Azure Mobile impegno per iOS e Android per riferimento. Tenere presente che alcune caratteristiche di Azure Mobile coinvolgimento siano specifiche della piattaforma (Android, iOS, Web, Windows e Windows Phone).

### <a name="see-also"></a>Vedere anche

 - [Risoluzione dei problemi guida - Push][Link 23] 
 - [Documentazione SDK - note sulla versione][Link 5]
 - [Documentazione SDK - Guide aggiornamento][Link 5]

## <a name="application-crashes"></a>Blocco delle applicazioni

### <a name="issue"></a>Problema
- Determina l'arresto anomalo dell'applicazione nel dispositivo degli utenti finali.

### <a name="causes"></a>Cause

- Informazioni sul blocco possono essere visualizzate nell' *Interfaccia utente Analitica* o l' *API Analitica*
- È possibile trovare l'ID di dispositivo del dispositivo test e scrivere la stessa operazione che ha causato l'applicazione subisce un arresto anomalo per un utente finale di permettono di individuare la causa dell'arresto anomalo.
- Problemi noti di Azure Mobile coinvolgimento SDK che causano in modo anomalo risolti in alcuni casi l'aggiornamento alla versione più recente di SDK. Assicurarsi di selezionare le note sulla versione sulla piattaforma durante l'analisi di anomalo.

### <a name="see-also"></a>Vedere anche

- [Documentazione SDK - note sulla versione][Link 5]
- [Documentazione SDK - Guide aggiornamento][Link 5]

## <a name="app-store-upload-failures"></a>Errori di caricamento di App store

### <a name="issue"></a>Problema
- Errori relativi al caricamento la versione più recente dell'app per Apple, Google o App di Windows store.

### <a name="causes"></a>Cause

- App memorizza talvolta App blocco con alcune caratteristiche abilitate (ad esempio Apple Store impedisce l'utilizzo di IDFV nel App nello store e l'archivio GooglePlay la condivisione di informazioni sull'applicazione tra App). 
- Assicurarsi di selezionare le note sulla versione relativi ai piattaforma e la versione corrente di SDK se si incontrano difficoltà nel caricare un'app per l'archivio.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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
 
