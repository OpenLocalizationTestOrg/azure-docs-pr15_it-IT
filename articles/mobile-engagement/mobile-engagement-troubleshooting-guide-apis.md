<properties 
   pageTitle="Azure coinvolgimento Mobile Guida - API di risoluzione dei problemi" 
   description="Risoluzione dei problemi di guide per Azure impegno per dispositivi mobili - API" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/04/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-api-issues"></a>Guida alla risoluzione dei problemi per API problemi

Di seguito sono possibili problemi che possono verificarsi come amministratori interagiscono con coinvolgimento di Mobile Azure tramite l'API.

## <a name="syntax-issues"></a>Problemi di sintassi

### <a name="issue"></a>Problema
- Errori di sintassi utilizzando l'API (o comportamenti imprevisti).

### <a name="causes"></a>Cause

- Problemi relativi alla sintassi:
    - Assicurarsi di controllare la sintassi delle API specifica in uso per verificare che l'opzione è disponibile.
    - Un problema con l'uso di API comune consiste nel confondere API raggiungere e l'API Push (maggior parte delle attività da eseguire con l'API di raggiungere anziché l'API Push). 
    - Un altro problema comune dell'integrazione SDK e l'uso di API consiste nel confondere le chiavi SDK e le API.
    - È necessario inviare dati almeno 10 minuti gli script che eseguono la connessione alle API o la connessione il timeout (soprattutto negli script l'API di Monitor in attesa di dati). Per evitare timeout, definire uno script di inviare un ping azienda 10 minuti per mantenere la sessione attivo con il server.

### <a name="see-also"></a>Vedere anche
 
- [Documentazione dell'API][Link 4]
- [Informazioni di protocollo azienda]( http://xmpp.org/extensions/xep-0199.html)
 
## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Non è possibile utilizzare l'API per eseguire la stessa operazione disponibile nell'interfaccia utente di impegno di Azure Mobile

### <a name="issue"></a>Problema
- Un'azione che funziona dall'interfaccia utente di impegno Mobile Azure non funziona dall'API coinvolgimento Mobile Azure correlati.

### <a name="causes"></a>Cause

- Per confermare che è possibile eseguire la stessa operazione dall'interfaccia utente di impegno Mobile Azure mostra che è stata integrata correttamente questa caratteristica di Azure Mobile coinvolgimento con SDK.

### <a name="see-also"></a>Vedere anche
 
- [Documentazione di interfaccia utente][Link 1]
 
## <a name="error-messages"></a>Messaggi di errore

### <a name="issue"></a>Problema
- Codici di errore tramite l'API visualizzato in fase di esecuzione o nei registri.

### <a name="causes"></a>Cause

- Ecco un elenco composto di numeri di codici stato API comuni per riferimento e risoluzione dei problemi preliminare:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Vedere anche

- [Documentazione di API - degli errori dettagliate su ogni API specifica][Link 4]
 
## <a name="silent-failures"></a>Errori

### <a name="issue"></a>Problema
- API azione ha esito negativo senza nessun messaggio di errore visualizzato in fase di esecuzione o nei registri.

### <a name="causes"></a>Cause

- Molti elementi saranno disabilitati nell'interfaccia utente di impegno di Azure Mobile se questi non integrati correttamente, ma verranno automaticamente esito negativo dall'API, pertanto è necessario ricordare di testare le stesse funzionalità dall'interfaccia utente per verificare se funziona.
- Azure coinvolgimento Mobile e molte caratteristiche avanzate di Azure Mobile coinvolgimento si sta tentando di usare, devono essere singolarmente integrato con l'app con SDK in due fasi distinte prima di poter usare.

### <a name="see-also"></a>Vedere anche

- [Risoluzione dei problemi guida - SDK][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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
 
