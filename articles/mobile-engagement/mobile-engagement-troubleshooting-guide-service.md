<properties 
   pageTitle="Azure coinvolgimento Mobile Guida - servizio di risoluzione dei problemi" 
   description="Risoluzione dei problemi di guide per Azure impegno per dispositivi mobili" 
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

# <a name="troubleshooting-guide-for-service-issues"></a>Guida alla risoluzione dei problemi dei problemi dei servizi

Di seguito sono possibili problemi che possono verificarsi con la modalità di esecuzione Azure Mobile coinvolgimento.

## <a name="service-outages"></a>Interruzioni del servizio

### <a name="issue"></a>Problema
- Problemi che sembrano essere dovuto a interruzioni dei servizi di Azure Mobile coinvolgimento.

### <a name="causes"></a>Cause
- Problemi che sembrano essere dovuto a interruzioni dei servizi di Azure Mobile impegno possono essere causati da diversi fattori:
    - Problemi isolati originariamente visualizzati sistematici a tutti Azure Mobile coinvolgimento
    - Problemi noti dovuti interruzioni dei server (non sempre presentazioni allo stato del server):
    - Pianificazione ritardi, gli errori di destinazione, problemi di aggiornamento di Badge, Interrompi statistiche per la raccolta, smette di Push funzionare, Interrompi uso dell'API, nuove app o gli utenti non è possibile creare, gli errori DNS e gli errori di Timeout dell'interfaccia utente, API o App in un dispositivo.
    - Cloud dipendenza interruzioni [lo stato dei servizi Azure](http://status.azure.com/)
    - Dipendenza interruzioni dei servizi (PNS) di notifica push
    - Interruzioni di App Store

1) Per verificare se il problema è sistematico è possibile testare la stessa funzione da un altro
   
   - Applicazione di impegno Mobile integrata Azure
   - Dispositivo di test
   - Versione per dispositivi OS di test
   - Campagna
   - Account di amministratore
   - Browser (Internet Explorer, Firefox, Chrome, ecc.)
   - Computer

2) Per verificare se il problema riguarda solo l'interfaccia utente o l'API:

   - Testare la stessa funzione dall'interfaccia utente coinvolgimento di Azure Mobile e le API di impegno Mobile Azure.

3) Per verificare se il problema alla rete aziendale telefono cellulare:

   - Eseguire il test mentre si è connessi a Internet tramite WIFI e durante la connessione tramite la rete cellulare 3G.
   - Verificare che il firewall non blocchi le indirizzi IP di Azure Mobile coinvolgimento o porte.

4) Per verificare se il problema con il dispositivo:

   - Verificare se il dispositivo è in grado di connettersi a Azure Mobile coinvolgimento con un'altra app integrata Azure Mobile coinvolgimento.
   - Test che è possibile generare eventi, i processi e anomalo dal telefono che può essere visualizzato nell'interfaccia utente di impegno di Azure Mobile. 
   - Verificare se è possibile inviare le notifiche push dall'interfaccia utente di impegno di Azure Mobile nel dispositivo in base al dispositivo ID. 

5) Per verificare se il problema con l'App:

   - Installare e testare l'applicazione di un emulatore anziché da un dispositivo fisico:
   
6) Per verificare se con gli aggiornamenti del sistema operativo per utente finale dispositivi che richiedono un aggiornamento SDK per risolvere il problema:

   - Testare l'applicazione su diversi dispositivi con versioni diverse del sistema operativo.
   - Confermare che si utilizza la versione più recente di SDK.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Connettività e problemi di informazioni non corrette

### <a name="issue"></a>Problema
- Problemi di accesso nell'interfaccia utente di impegno di Azure Mobile.
- Errori di connessione con l'API di impegno Mobile Azure.
- Problemi di caricamento App Info tag tramite l'API di dispositivi.
- Problemi di download dei dati esportati o i log da Azure Mobile coinvolgimento.
- Informazioni non corrette visualizzate nell'interfaccia utente di impegno di Azure Mobile.
- Informazioni non corrette visualizzate nei log di Azure Mobile coinvolgimento.

### <a name="causes"></a>Cause
* Verificare che l'account utente abbia autorizzazioni sufficienti per eseguire l'attività.
* Verificare che il problema non è limitato a un computer o alla rete locale.
* Verificare che che il servizio di Azure Mobile coinvolgimento non ha alcun segnalato interruzioni.
* Verificare che i file App Info Tag seguano tutte le seguenti regole:
    - Utilizzare solo il set di caratteri UTF8 (set di caratteri ANSI non è supportato).
    - Utilizzare un punto e virgola "," come carattere separatore (è possibile aprire un servizio richiesta per modificare il carattere separatore CSV da un punto e virgola "," in un altro carattere, ad esempio un punto e virgola ";").
    - Usare tutti minuscoli o valori booleani "true" e "false".
    - Utilizzare un file che è minore di dimensioni massime dei file di 35MB.
 
