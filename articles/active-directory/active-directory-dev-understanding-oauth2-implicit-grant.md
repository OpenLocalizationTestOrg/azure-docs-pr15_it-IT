<properties
   pageTitle="Informazioni sulle OAuth2 impliciti concedere del flusso di Azure Active Directory | Microsoft Azure"
   description="Ulteriori informazioni sull'implementazione di Azure Active Directory di OAuth2 impliciti concedere flusso, e se è appropriata per l'applicazione."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>La comprensione del flusso di concessione impliciti OAuth2 in Azure Active Directory (AD)

La concessione implicita OAuth2 è noto per vengano la concessione con l'elenco più lunga di protezione nella specifica OAuth2. E ancora, che rappresenta l'approccio implementato JS ADAL e quella che è consigliabile quando si scrivono applicazioni SPA. Cosa offre? Si tratta tutti di compromessi: e in realtà, la concessione implicita è consigliabile è possibile adottare per applicazioni che utilizzano un API Web tramite JavaScript da un browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>Che cos'è la concessione implicita OAuth2?

Tipico [codice di autorizzazione OAuth2 concedere](https://tools.ietf.org/html/rfc6749#section-1.3.1) è la concessione di autorizzazioni che usa due endpoint separato. L'endpoint di autorizzazione viene utilizzato per la fase di interazione dell'utente, genera un codice di autorizzazione. L'endpoint di token viene quindi utilizzato dal client per lo scambio di codice per un token di accesso e spesso anche un token di aggiornamento. Le applicazioni Web sono necessari per presentare le proprie credenziali di applicazione per l'endpoint di token, in modo che il server di autorizzazione può eseguire l'autenticazione del client.

[Concedere OAuth2 implicita](https://tools.ietf.org/html/rfc6749#section-1.3.2) è un valore variant che consente di un cliente ottenere un token di accesso (e id_token, nel caso di [Connettersi OpenId](http://openid.net/specs/openid-connect-core-1_0.html)) direttamente dal punto finale di autorizzazione, senza contattare l'endpoint di token né l'applicazione client di autenticazione. Questa variante è stata progettata specificamente per le applicazioni in esecuzione in un Web browser in base a JavaScript: in specifica OAuth2 originale, vengono restituiti i token in un frammento URI. Che rende disponibile al codice JavaScript bit token nel client, ma garantisce che non saranno incluse in reindirizzamenti verso il server. Restituisce i token tramite browser reindirizza direttamente dal punto finale di autorizzazione. Offre il vantaggio di eliminare tutti i requisiti per cross chiamate di origine, se l'applicazione JavaScript è necessario contattare l'endpoint di token sono necessarie.

Una caratteristica importante della concessione implicita OAuth2 consiste nel fatto che tale scorra token mai aggiornamento al client. Come si vedrà nella sezione seguente, che non è necessario e in realtà è un problema di protezione.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scenari più appropriati per la concessione implicita OAuth2

Come le specifiche di OAuth2 dichiara, la concessione implicita è progettata per consentire alle applicazioni di agente utente, vale a dire le applicazioni di JavaScript in esecuzione in un browser. La caratteristica di definizione di tali applicazioni è codice JavaScript viene utilizzato per l'accesso alle risorse del server (in genere dell'API Web) e per l'aggiornamento di conseguenza l'applicazione dell'esperienza utente. Si tratta di applicazioni, ad esempio Gmail o Outlook Web Access: quando si seleziona un messaggio dalla posta in arrivo, solo il pannello di visualizzazione del messaggio modificata per visualizzare la nuova selezione, mentre il resto della pagina rimarrà invariato. Verrà visualizzata a differenza dei tradizionali applicazioni Web basate su Reindirizza nel punto in cui ogni interazione dell'utente genera un postback dell'intera pagina e il rendering una pagina intera della risposta del server nuovo.

Applicazioni che utilizzano l'approccio JavaScript in base alla relativa estremo sono chiamate singole applicazioni di pagina o stabilimenti termali: l'idea è che tali applicazioni servono solo una pagina HTML iniziale e JavaScript associato, con tutte le interazioni successive dipende da chiamate API Web eseguita tramite JavaScript. Tuttavia, approcci ibrida, in cui l'applicazione è principalmente basata sulle risorse postback ma esegue chiamate JS occasionali, non sono rari, la discussione sull'uso di flusso impliciti i contenuti pertinente per quelli anche.

Applicazioni basate su Reindirizza sicura in genere le proprie richieste tramite i cookie, tuttavia, che approccio non funziona anche per le applicazioni JavaScript. I cookie funzionano solo con il dominio che sono stati generati per, mentre le chiamate JavaScript potrebbero essere indirizzate altri domini. Infatti, che sarà spesso le maiuscole/minuscole: si tratta di applicazioni richiamare API di Microsoft Graph, API di Office, Azure API – tutti che si trovano all'esterno del dominio nel punto in cui sia served dell'applicazione. Sempre più diffuso per le applicazioni JavaScript non è alcun back-end, basarsi 100% nella parte 3 ° API Web per implementare la funzione aziendale.

Attualmente, il metodo di protezione di chiamate a un'API Web preferito consiste nell'utilizzare l'approccio token del titolare OAuth2, in cui ogni chiamata è accompagnato da un token di accesso OAuth2. API Web esamina il token di accesso in arrivo e, se vengono trovati in essa gli ambiti necessari, concede l'accesso per l'operazione. Il flusso implicito fornisce un pratico meccanismo per applicazioni JavaScript ottenere i token di accesso per un'API Web offre numerosi vantaggi rispetto al cookie:

- I token possono essere ottenuti in modo affidabile senza che sia necessario per cross chiamate origine – annotazione obbligatoria di reindirizzamento URI a cui vengono restituiti i token garantisce che i token non vengono spostati
- Le applicazioni JavaScript possono ottenere token di accesso illimitato secondo necessità, per numero API Web di destinazione: senza restrizioni sui domini
- Caratteristiche di HTML5 come sessione o lo spazio di archiviazione locale concedere il controllo completo su memorizzazione nella cache token e la gestione della durata, mentre la gestione dei cookie è opaca all'app
- Token di accesso non sono soggetti ad attacchi di intersito richiesta FALSO (CSRF)

Il flusso di concessione impliciti non rilascia token di aggiornamento, soprattutto per motivi di sicurezza. L'ambito di un token di aggiornamento non è impostato come ristretto come token di accesso, concessione power molto più inflicting pertanto molto più danni nel caso in cui viene perso. Nel flusso implicito token vengono inviati nell'URL, pertanto il rischio di intercettazione è superiore a quelli la concessione di codice di autorizzazione.

Tuttavia, si noti che un'applicazione JavaScript ha un altro meccanismo a disposizione per il rinnovo token di accesso senza ripetutamente che richiede all'utente le credenziali. L'applicazione è possibile utilizzare un iframe nascosto per eseguire nuove convocazioni token contro l'endpoint di autorizzazione di Azure Active Directory: come browser sono ancora presenti una sessione attiva (leggere: dispone di un cookie) con dominio Azure Active Directory la richiesta di autenticazione possa essere eseguito senza che sia necessario per l'interazione dell'utente. 

Questo modello consente di applicazione JavaScript stabilire in modo indipendente rinnovo token di accesso e persino acquisire nuovi per una nuova API (a condizione che l'utente acconsentito in precedenza per loro. In questo modo il carico di lavoro aggiunto di acquisizione, gestione e protezione di un elemento del valore elevato, ad esempio un token di aggiornamento. L'elemento che consente il rinnovo invisibile, cookie della sessione Azure Active Directory è gestito all'esterno dell'applicazione. Un altro vantaggio di questo approccio è che un utente può eseguire la disconnessione da Azure Active Directory, utilizzando le applicazioni eseguito l'accesso a Azure Active Directory, viene eseguita una qualsiasi delle schede del browser. Questo comporta l'eliminazione dei cookie della sessione di Azure Active Directory e l'applicazione JavaScript automaticamente sarà possibile rinnovare i token per l'accesso all'utente.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>È adatto per app grant impliciti?

La concessione implicita presenta ulteriori rischi maggiori altri concede. Sono anche descritte le aree è necessario prestare attenzione alle (ad esempio vedere [Fatto un uso scorretto di Access Token per rappresentare risorse proprietario in flusso impliciti] [ OAuth2-Spec-Implicit-Misuse] e [modellazione 2.0 OAuth e considerazioni sulla protezione][OAuth2-Threat-Model-And-Security-Implications]). Tuttavia, il profilo di rischio maggiore è ampiamente dovuto al fatto che è utilizzata per consentire alle applicazioni che eseguono codice attivo, rappresentate in una risorsa remota in un browser. Se si prevede un'architettura SPA non dispone di alcun componente back-end o si intende richiamare un API Web tramite JavaScript, è consigliabile utilizzare il flusso implicito per l'acquisizione di token.

Se l'applicazione client nativi, il flusso implicito non è un'ottima soluzione. Assenza di cookie della sessione di Azure Active Directory nel contesto di client nativi priva applicazione dai mezzi di gestione di una sessione di tempo temporanei. Che indica che l'applicazione ripetutamente richiederà all'utente quando si ottengono i token di accesso per le nuove risorse.

Se si sviluppa un'applicazione Web che include un back-end e l'utilizzo di un'API dal relativo codice di back-end, il flusso implicito anche non è una soluzione ideale. Altri concede offrono molto più efficace. Ad esempio, la concessione di credenziali client OAuth2 offre la possibilità di ottenere i token che riflettono le autorizzazioni assegnate all'applicazione, anziché deleghe di utenti. Questo errore indica che il client è in grado di gestire l'accesso a risorse anche quando un utente non ha avviato in una sessione e così via. Non solo che, ma tali offre maggiore sicurezza garanzie. Ad esempio, i token di accesso non è mai transito tramite il browser utente, non rischiare salvata nella cronologia del browser e così via. L'applicazione client inoltre possibile eseguire l'autenticazione di sicuro quando richiedendo un token.

## <a name="next-steps"></a>Passaggi successivi

- Per un elenco completo delle risorse per sviluppatori, incluse informazioni di riferimento per i protocolli e OAuth2 autorizzazione concedere il supporto di flussi da Azure Active Directory, vedere [Azure Active Directory per gli sviluppatori di Guida][AAD-Developers-Guide]
- Informazioni su [come integrare un'applicazione con Azure Active Directory]  [ ACOM-How-To-Integrate] per aggiuntive antero-posteriore del processo di integrazione dell'applicazione.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

