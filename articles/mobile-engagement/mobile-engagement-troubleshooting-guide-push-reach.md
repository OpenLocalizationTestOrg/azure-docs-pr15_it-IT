<properties 
   pageTitle="Azure coinvolgimento Mobile risoluzione dei problemi guida - Push/portata" 
   description="Risoluzione dei problemi di interazione e la notifica di utente coinvolgimento Mobile Azure" 
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

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Risoluzione dei problemi di Guida per inserire e raggiungere problemi

Di seguito sono possibili problemi che possono verificarsi con come Azure Mobile coinvolgimento invia informazioni agli utenti.
 
## <a name="push-failures"></a>Errori di push

### <a name="issue"></a>Problema
- Inserisce non funzionano (in app riduzione app).

### <a name="causes"></a>Cause
- Tutte le volte push errore è l'indicazione che Azure Mobile coinvolgimento, portata o un'altra funzionalità avanzata di Azure Mobile coinvolgimento non corretta è integrata o che per risolvere un problema noto con una nuova piattaforma del sistema operativo o un dispositivo in SDK è necessario un aggiornamento.
- Testare solo un push In App e solo un push di App per determinare se un elemento è un problema nell'App o dall'App.
- Test di interfaccia utente e le API come di risoluzione dei problemi per vedere quali informazioni aggiuntive sull'errore disponibile entrambe le posizioni.
- Disconnettersi da App push non funzionano a meno che non vengano integrati Azure Mobile coinvolgimento e portata in SDK.
- Inserisce non funzionano se certificati non validi o utilizzano produzione e DEV correttamente (solo per iOS). (**Nota:** "dall'app" notifiche push potrebbero non essere inviate a iOS, se si dispone di entrambi lo sviluppo (DEV) e versioni di produzione (produzione) di un'applicazione installata nello stesso dispositivo? poiché il token di sicurezza associato il certificato potrebbero Invalidate da Apple. Per risolvere il problema, disinstallare versioni DEV e produzione di un'applicazione e reinstallare l'una versione sul dispositivo.)
- Disconnettersi da push App conteggi vengono gestiti in modo leggermente diverso in piattaforme diverse (iOS Mostra meno informazioni più Android se push nativi sono disabilitati in un dispositivo, l'API può fornire ulteriori informazioni rispetto all'interfaccia utente su Stat push).
- Disconnettersi da App è possibile bloccare push dai clienti a livello del sistema operativo (iOS e Android).
- Dall'App push verrà visualizzato come disabilitato nell'interfaccia utente di impegno di Azure Mobile se non è integrati correttamente, ma potrebbe non riuscire automaticamente dall'API.
- Nell'App push non funzionano a meno che non vengano integrati Azure Mobile coinvolgimento e portata in SDK.
- Inserisce GCM e ADM non funzionano a meno che non vengano integrati Azure Mobile coinvolgimento e il server specifico in SDK (solo Android).
- In App e dall'App push è opportuno sottoporre separatamente per determinare se si tratta di un problema Push o portata.
- In App push è necessario che l'app sia aperta per la ricezione.
- Nell'App push sono spesso di configurazione per essere filtrati in base a un tag info app consenso esplicito o esclusione.
- Se in una categoria personalizzata portata consente di visualizzare le notifiche di app, è necessario seguire il ciclo di vita corretto della notifica, altrimenti la notifica non è deselezionata quando l'utente chiuderla.
- Se si inizia una campagna senza data di fine e un dispositivo riceve l'app in notifica ma non visualizzazione non è ancora, l'utente continuerà ricevere notifica la volta successiva che accedono all'App, anche se si chiude manualmente la campagna.
- Per problemi con l'API Push, verificare che realmente da usare l'API Push anziché l'API raggiunga (poiché l'API raggiunga utilizzato più spesso) e che sono non confondere i parametri "payload" e "notifica".
- Testare la campagna push con dispositivo sia connesso tramite WIFI e 3G per eliminare la connessione di rete come origine possibili problemi di.

## <a name="push-testing"></a>Push test

### <a name="issue"></a>Problema
- Inserisce possono essere inviati a un dispositivo specifico in base a un dispositivo ID.

### <a name="causes"></a>Cause

- Test dispositivi sono il programma di installazione in modo diverso per ogni piattaforma, ma può causare un evento, l'app in un dispositivo di test e cercare l'ID di dispositivo nel portale funzionerà per trovare l'ID di dispositivo per tutte le piattaforme.
- Test dispositivi funzionano in modo diverso con IDFA e IDFV (solo per iOS).


## <a name="push-customization"></a>Personalizzazione di push

### <a name="issue"></a>Problema
- Avanzate push contenuto non è possibile usare elemento (badge, squillo, vibrazione, immagini, ecc.).
- Collegamenti da push non funzionano (fuori app app a un sito Web, in una posizione nell'app).
- Statistiche push indicano che un push non è stato inviato a un numero di utenti come previsto (troppi o non è sufficiente).
- Push duplicati e ricevuto due volte.
- Non è possibile registrare il dispositivo di test per Azure Mobile coinvolgimento inserisce (con il proprio app di produzione o DEV).

### <a name="causes"></a>Cause

- Per creare un collegamento a una posizione specifica in app richiede "categorie" (solo Android).
- Complete combinazioni di collegamento per reindirizzare gli utenti in un percorso alternativo dopo aver selezionato una notifica push necessario creato in e gestito direttamente dall'applicazione e il dispositivo del sistema operativo non da coinvolgimento Mobile. (**Nota:** dall'app le notifiche non è possibile creare un collegamento direttamente a in posizioni app con iOS differenza di quanto accade con Android.)
- Server di immagini esterne devono essere in grado di utilizzare HTTP "GET" e "Testa" per quadro inserisce al lavoro (solo Android).
- Nel codice, è possibile disattivare l'agente di Azure Mobile coinvolgimento quando si apre la tastiera e avere il codice riattivare agente di Azure Mobile coinvolgimento dopo la tastiera viene chiuso in modo che la tastiera non è possibile modificare l'aspetto della notifica (solo per iOS).
- Alcuni elementi non funzionano in test simulazioni, ma solo le campagne reale (badge, squillo, vibrazione, immagini, ecc.).
- Quando si utilizza il pulsante "verificare" inserimenti, dati lato server non ha eseguito l'accesso. Dati viene registrati solo per le campagne push reali.
- Per informazioni su come isolare il problema, risolvere i problemi: test, simulare e una campagna reale in quanto ogni funzionano in modo leggermente diverso.
- L'intervallo di tempo "in app" le campagne "mai" pianificate per l'esecuzione può influire i numeri di recapito poiché una campagna verrà recapitata solo a utenti che appartengono "app" durante l'esecuzione della campagna (e gli utenti che dispongono di impostazioni del proprio dispositivo per la ricezione delle notifiche "dall'app").
- Le differenze tra la modalità di gestione fuori notifiche dell'app iOS e Android rende difficile direttamente confrontare le statistiche push tra la versione di iOS e Android dell'applicazione. Android informazioni ulteriori OS notifica livello rispetto a iOS. Android segnala quando una notifica nativa viene ricevuta, selezionata o eliminata nell'interfaccia di notifica, ma iOS non segnala queste informazioni a meno che non si fa clic sul messaggio di notifica. 
- Il motivo principale "inseriti" numeri sono diversi rispetto a diverso rispetto a "recapitato" numeri per raggiungono campagne che "in app" e "dall'app" le notifiche vengono conteggiate in modo leggermente diverso. "In app" le notifiche vengono gestite dal coinvolgimento Mobile, ma "dall'app" le notifiche vengono gestite dal centro notifiche nel sistema operativo del dispositivo.

## <a name="push-targeting"></a>Spingere la destinazione

### <a name="issue"></a>Problema
- Integrato in destinazione non funziona come previsto.
- L'assegnazione del contrassegno informazioni App non funziona come previsto.
- L'assegnazione del percorso geografico non funziona come previsto.
- Opzioni relative alla lingua non funzionano come previsto.

### <a name="causes"></a>Cause

- Assicurarsi di aver caricato tag info app tramite l'interfaccia utente di impegno di Azure Mobile o API.
- Limitazione la velocità di push quota push a livello di applicazione o la limitazione ai partecipanti al livello della campagna possibile impedire la ricezione di un specifico push anche se soddisfano i criteri di assegnazione di gruppi altri una persona. 
- L'impostazione "Lingua" è diverso da quello di destinazione in base al paese o impostazioni locali, sono diversa da quello di destinazione in base alla posizione geografico in base a una posizione di telefono o la posizione GPS.
- Il messaggio "lingua predefinita" viene inviato agli utenti che non hanno il dispositivo impostato su una delle lingue alternative specificati.


## <a name="push-scheduling"></a>Spingere la programmazione

### <a name="issue"></a>Problema
- Pianificazione push non funziona come previsto (inviato troppo anticipata o posticipata).

### <a name="causes"></a>Cause

- Fusi orari possibile problemi con la pianificazione, soprattutto quando si utilizzano fuso orario degli utenti finali.
- Caratteristiche avanzate push possono ritardare push.
- L'assegnazione in base al telefono possono ritardare impostazioni (anziché tag Info App) inserisce dopo la partecipazione attiva Mobile Azure, potrebbe essere necessario richiedere dati in tempo reale il telefono prima dell'invio di un push.
- Campagne create senza una data di fine memorizzare il push in locale nel dispositivo e mostrano alla successiva apertura dell'app, anche se la campagna manualmente è terminata.
- Avvio della più campagna nello stesso momento può richiedere più tempo per digitalizzare la base di utenti (provare iniziare solo uno campagna alla volta con un massimo di quattro, anche destinazione solo agli utenti attivi in modo che gli utenti precedenti non sono necessario eseguire la scansione).
- Se si usa l'opzione "Gruppo di destinatari Ignora push verrà inviato agli utenti tramite l'API" nella sezione "Campagna" di una campagna di raggiungere la campagna non invierà automaticamente, sarà necessario inviare manualmente tramite l'API raggiungere.
- Se in una categoria personalizzata portata consente di visualizzare le notifiche di app, è necessario seguire il ciclo di vita corretto di una notifica, altrimenti la notifica non è deselezionata quando l'utente chiuderla.

 
