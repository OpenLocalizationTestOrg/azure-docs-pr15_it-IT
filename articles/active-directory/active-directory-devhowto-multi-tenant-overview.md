<properties
   pageTitle="Modalità di compilazione di un'applicazione che può accedere a qualsiasi utente di Azure Active Directory | Microsoft Azure"
   description="Step by step istruzioni per la creazione di un'applicazione che possono accedere a un utente da qualsiasi tenant di Azure Active Directory, noto anche come un'applicazione multi-tenant."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Come effettuare l'accesso a tutti gli utenti di Azure Active Directory (AD) utilizzando il modello di applicazione multi-tenant
Se viene offerta una Software come un'applicazione del servizio per molte organizzazioni, è possibile configurare l'applicazione di accettare gli accessi da qualsiasi tenant di Azure Active Directory.  In Azure Active Directory questo comando si chiama effettua multi-tenant l'applicazione.  Gli utenti di qualsiasi tenant di Azure Active Directory saranno in grado di accedere all'applicazione dopo acconsentire usare il proprio account con l'applicazione.  

Se si dispone di un'applicazione esistente con il proprio sistema account o supporta altri tipi di accesso da altri provider di servizi cloud, l'aggiunta di accesso di Azure Active Directory da qualsiasi tenant è semplice come registrare l'app, aggiunta di accesso nel codice tramite OAuth2, OpenID connettersi o SAML e inserire un segno di con il pulsante Microsoft sull'applicazione. Fare clic sul pulsante per ulteriori informazioni sulla personalizzazione dell'applicazione.

[! [Effettuarlo pulsante] [AAD-accesso In]][AAD-App-Branding]


In questo articolo si presuppone che si ha già familiarità con la creazione di un'applicazione tenant singola per Azure Active Directory.  Se non si è, testa fino [home page di Guida di sviluppo] [ AAD-Dev-Guide] e provare uno dei nostro avvio rapido!

Esistono quattro semplici passaggi per convertire l'applicazione in un'app multi-tenant di Azure Active Directory:

1.  Aggiornare la registrazione di applicazione per essere multi-tenant
2.  Aggiornamento del codice per inviare richieste per il /common endpoint 
3.  Aggiornare il codice per gestire più valori emittente
4.  Comprendere utenti e amministratori consenso e apportare modifiche al codice appropriato

Esaminiamo ogni passaggio in modo dettagliato. È anche possibile passare direttamente [all'elenco degli esempi multi-tenant][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Aggiornare la registrazione per essere multi-tenant
Per impostazione predefinita, le registrazioni app/API web in Azure Active Directory vengono tenant singola.  Per rendere la registrazione multi-tenant, trovare l'opzione "Applicazione è multi-Tenant" nella pagina Configurazione della finestra della registrazione dell'applicazione nel [portale classica Azure] [ AZURE-classic-portal] e impostandolo su "Sì".

Nota: Prima di effettuare un'applicazione multi-tenant, Azure Active Directory richiede l'URI ID App dell'applicazione univocità a livello globale. URI ID App è uno dei modi indicati di che un'applicazione viene identificata nei messaggi di protocollo.  Per un'app singola tenant, è sufficiente per l'URI ID App sia univoco all'interno di tale tenant.  Per un'applicazione multi-tenant, deve essere univoco globale in modo Azure Active Directory può trovare l'applicazione in tutti i tenant.  Viene applicata l'univocità globale richiedendo URI ID App da un nome host che corrisponde a un dominio verificato del tenant di Azure Active Directory.  Ad esempio, se il nome del tenant di stato contoso.onmicrosoft.com quindi valido App ID URI sarebbe `https://contoso.onmicrosoft.com/myapp`.  Se il tenant aveva un dominio verificato di `contoso.com`, quindi URI ID App valido anche sarebbe `https://contoso.com/myapp`.  Impostazione di un'applicazione come multi-tenant avrà esito negativo se l'URI ID App non seguire questo modello.

La registrazione dei client nativi sono multi-tenant per impostazione predefinita.  Non è necessario eseguire alcuna operazione per rendere nativa multi-tenant registrazione dell'applicazione client.

## <a name="update-your-code-to-send-requests-to-common"></a>Aggiornamento del codice per inviare richieste al /common
In un'applicazione tenant singola, le richieste di accesso vengono inviate agli endpoint di accesso del tenant.   Ad esempio, per contoso.onmicrosoft.com il punto finale sarà:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Le richieste inviate all'endpoint del tenant possono accedere utenti (o utenti guest) nel tenant alle applicazioni di tenant.  Con un'applicazione multi-tenant, l'applicazione non sa anticipo quali tenant l'utente è stata inviata da, in modo che non è possibile inviare richieste all'endpoint del tenant.  Se, tuttavia, le richieste vengono inviate a un endpoint multiplexes tra tenant tutti Azure Active Directory:

    https://login.microsoftonline.com/common

Quando Azure Active Directory riceve una richiesta nella /common endpoint, accede all'utente e di conseguenza individua quali tenant l'utente è stata inviata da.  La/endpoint comuni funziona con tutti i protocolli di autenticazione supportati da Azure Active Directory: OpenID connettersi, OAuth 2.0, SAML 2.0 e WS-Federation.

Il segno di risposta quindi all'applicazione contiene un token che rappresenta l'utente.  Il valore di emittente nel token indica un'applicazione quali tenant l'utente è stata inviata da.  Quando una risposta restituisce il /comuni endpoint, il valore di emittente nel token corrisponderà a tenant dell'utente.  È importante tenere presente la /common endpoint non è un tenant e non è un emittente, è sufficiente un multiplexer.  Quando si utilizza /common, la logica dell'applicazione per convalidare i token deve essere aggiornata per tenerne conto. 

Come detto in precedenza, applicazioni multi-tenant inoltre necessario fornire un'esperienza di accesso coerente per gli utenti, seguendo l'applicazione di Azure Active Directory personalizzazione linee guida. Fare clic sul pulsante per ulteriori informazioni sulla personalizzazione dell'applicazione.

[! [Effettuarlo pulsante] [AAD-accesso In]][AAD-App-Branding]

Esaminiamo l'uso del /common endpoint e l'implementazione di codice in modo più dettagliato.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aggiornare il codice per gestire più valori emittente
Le applicazioni Web e web API ricevere e convalidare i token da Azure Active Directory.  

> [AZURE.NOTE] Mentre le applicazioni client nativi richiedere e ricevano i token da Azure Active Directory, a tale scopo per inviare all'API, in cui vengono convalidati.  Applicazioni native non convalidano i token e necessario gestirle come opaco.

Ecco come un'applicazione convalida token riceve da Azure Active Directory.  Un'applicazione tenant singola dura in genere un valore endpoint come:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e usarlo per creare un URL di metadati (in questo caso, connettersi OpenID) come:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Per scaricare due tipi di informazioni importanti che vengono utilizzati per convalidare i token: il tenant di firma del chiavi e valori emittente.  Ogni tenant di Azure Active Directory è un valore univoco emittente della maschera:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

nel punto in cui il valore GUID è la versione Rinomina dell'ID tenant del tenant.  Se si fa clic sul collegamento basato su metadati per `contoso.onmicrosoft.com`, è possibile visualizzare questo valore emittente nel documento.

Quando un'applicazione tenant singola convalida un token, verifica la firma di token con i tasti di firma dal documento basato su metadati e verifica che il valore di emittente nel token corrisponde a quello che è stato trovato nel documento basato su metadati.

Dopo la /common endpoint non corrisponde a un tenant e non è un emittente quando è esaminare il valore di emittente nei metadati per/comuni che contiene un URL basato su modelli anziché un valore effettivo:

    https://sts.windows.net/{tenantid}/

Di conseguenza, un'applicazione multi-tenant non è possibile convalidare i token sufficiente corrisponde al valore emittente nei metadati con la `issuer` valore nel token.  Un'applicazione multi-tenant logica per decidere quali valori emittente sono valide e che non è necessario, in base al tenant parte ID del valore emittente.  

Ad esempio, se un'applicazione multi-tenant consente l'accesso solo da tenant specifici che hanno effettuato l'iscrizione ai servizi, è necessario controllare il valore di emittente o `tid` richiedere valore token per garantire sempre tenant nell'elenco degli abbonati.  Se un'applicazione multi-tenant solo riguarda le persone e non si delle decisioni di access in base a tenant, è possibile ignorare il valore emittente completamente.

Negli esempi multi-tenant che sono disponibili nella sezione [Contenuto correlato](#related-content) alla fine di questo articolo, convalida emittente è disattivata per attivare qualsiasi tenant di Azure AD effettuare l'accesso.

Ora esaminiamo l'esperienza utente per gli utenti di accedere alle applicazioni multi-tenant.

## <a name="understanding-user-and-admin-consent"></a>Informazioni sulle utenti e amministratori consenso
Per un utente accede a un'applicazione di Azure Active Directory, l'applicazione deve essere rappresentato nel tenant dell'utente.  In questo modo l'organizzazione di eseguire operazioni come applicare criteri univoci quando gli utenti dal tenant di accedere all'applicazione.  Per un'applicazione tenant singola la registrazione è semplice. è quello che succede quando si registra l'applicazione nel [portale classica Azure][AZURE-classic-portal].

Per un'applicazione multi-tenant, la registrazione iniziale per l'applicazione si trova nel tenant di Azure Active Directory utilizzato dello sviluppatore.  Quando un utente da un tenant diversi accede all'applicazione per la prima volta, Azure Active Directory chiede di consenso per le autorizzazioni necessarie per l'applicazione.  Se si accetta, quindi viene creata una rappresentazione dell'applicazione denominata *servizio principale* nel tenant dell'utente e possano continuare a Accedi. Inoltre è creata una delega nella directory che registra il consenso dell'utente all'applicazione. Vedere [gli oggetti dell'applicazione e servizio capitale] [ AAD-App-SP-Objects] per informazioni dettagliate sugli oggetti dell'applicazione e ServicePrincipal dell'applicazione e le relazioni tra loro.

![Consenso a livello singolo app][Consent-Single-Tier] 

L'esperienza di consenso dipende dalle autorizzazioni richiesti dall'applicazione.  Azure Active Directory supporta due tipi di autorizzazioni, app e di sola delegate:

- Un'autorizzazione delegata concede un'applicazione la possibilità di agire come l'accesso all'utente per un sottoinsieme di operazioni l'utente è possibile eseguire.  Ad esempio, è possibile concedere un'applicazione delegare l'autorizzazione per leggere l'accesso del calendario dell'utente.
- Un solo app autorizzazione direttamente all'identità dell'applicazione.  Ad esempio, è possibile concedere l'autorizzazione solo app a leggere l'elenco di utenti in un tenant di un'applicazione e sarà in grado di eseguire questa operazione indipendentemente da chi ha effettuato l'accesso all'applicazione.

Alcune autorizzazioni possano acconsentite da un utente normale, mentre altri richiedono il consenso dell'amministratore tenant. 

### <a name="admin-consent"></a>Autorizzazione di amministratore
Le autorizzazioni di sola App richiedono sempre il consenso dell'amministratore tenant.  Se l'applicazione richiede un'autorizzazione solo app e un normale utente prova ad accedere all'applicazione, l'applicazione verrà visualizzato un messaggio di errore che indica che l'utente non è in grado di confermare.

Alcune autorizzazioni delegate richiedono anche il consenso dell'amministratore tenant.  Ad esempio, per scrivere Azure Active Directory come l'accesso all'utente è necessario il consenso dell'amministratore tenant.  Ad esempio le autorizzazioni di sola app, se un utente ordinario prova ad accedere a un'applicazione che richiede un'autorizzazione delegata che richiede l'autorizzazione di amministratore, l'applicazione riceverà un messaggio di errore.  Se non richiede un'autorizzazione consenso dell'utente amministratore viene determinato dello sviluppatore che ha pubblicato la risorsa e può essere disponibili nella documentazione per la risorsa.  Collegamenti ad argomenti che descrivono le autorizzazioni disponibili per l'API di Azure Active Directory Graph e Microsoft Graph API disponibili nella sezione [Correlata del contenuto](#related-content) di questo articolo.

Se l'applicazione utilizza le autorizzazioni che richiedono l'autorizzazione di amministratore, è necessario avere un movimento nell'applicazione, ad esempio un pulsante o un collegamento nel punto in cui l'amministratore può avviare l'azione.  La richiesta l'applicazione invia per questa azione è una richiesta di autorizzazione OAuth2/OpenID connettersi consueta, ma che include anche il `prompt=admin_consent` parametro stringa di query.  Dopo che l'amministratore ha acconsentito e principale del servizio viene creata nel tenant del cliente, non è necessario accedere successive richieste di `prompt=admin_consent` parametro.   Poiché l'amministratore ha scelto di che autorizzazioni richieste sono accettabili, nessun altro utente nel tenant di verranno richiesto il consenso a quel punto.

Il `prompt=admin_consent` parametro può anche essere utilizzato dalle applicazioni che richiedono autorizzazioni che non richiedono l'autorizzazione di amministratore, ma si desidera fornire un ambiente in cui l'amministratore tenant "iscrive" per l'applicazione di una sola volta e altri utenti non vengono richiesto il consenso da quel momento in.

Se un'applicazione richiede l'autorizzazione di amministratore e l'amministratore accede all'applicazione, ma il `prompt=admin_consent` parametro non è stato inviato, l'amministratore sarà in grado di correttamente consenso all'applicazione ma verranno consenso solo per il proprio account utente.  Utenti normali ancora non sarà possibile accedere e consenso all'applicazione.  Ciò è utile se si desidera consentire l'amministratore del tenant di esplorare l'applicazione prima di consentire ad altri utenti l'accesso.

Un amministratore tenant può disattivare la possibilità degli utenti di consenso alle applicazioni.  Se questa funzionalità è disattivata, amministratori consenso è sempre necessario per l'applicazione di impostare nel tenant.  Se si desidera testare l'applicazione con il consenso dell'utente regolare disabilitata, è possibile trovare l'opzione configurazione nel tenant di Azure Active Directory sezione di configurazione del [portale classica Azure][AZURE-classic-portal].

> [AZURE.NOTE] Alcune applicazioni di cui un'esperienza nel punto in cui gli utenti standard sono in grado di consenso inizialmente e in un secondo momento l'applicazione può comportare le autorizzazioni di amministratore e richiedere che richiedono il consenso dell'utente amministratore.  Non è possibile eseguire questa operazione con una registrazione singola applicazione in Azure Active Directory oggi.  La prossima endpoint v2 Azure Active Directory consentirà applicazioni per richiedere le autorizzazioni in fase di esecuzione, anziché in fase di registrazione, in modo da consentire questo scenario.  Per ulteriori informazioni, vedere la [Guida per gli sviluppatori di Azure Active Directory App modello v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Applicazioni di consenso e a più livelli
L'applicazione potrebbe essere più livelli, ciascuna rappresentata dalla propria registrazione in Azure Active Directory.  Ad esempio, un'applicazione nativa che chiama un'API web o da un'applicazione web che chiama dell'API web.  In entrambi i casi, il client (nativo app o web app) richiede le autorizzazioni per chiamare la risorsa (dell'API web).  Per il client per essere correttamente acconsentito nel tenant di un cliente, tutte le risorse a cui viene richiesto di autorizzazioni deve essere esistente nel tenant del cliente.  Se non è soddisfatta la condizione, Azure Active Directory restituirà un errore che è necessario aggiungere innanzitutto la risorsa.

Può trattarsi di un problema se l'applicazione logico è costituito da due o più registrazioni applicazione, ad esempio un client distinto e delle risorse.  Come si ottiene la risorsa nel tenant cliente prima?  Azure Active Directory è descritta in questo caso, consentendo di client e delle risorse per acconsentito in un unico passaggio, in cui l'utente potrà visualizzare il totale di autorizzazioni richieste dal client e risorse della pagina di consenso dell'utente.  Per attivare, registrazione dell'applicazione della risorsa deve includere l'ID del client App come un `knownClientApplications` nel manifesto di applicazione.  Per esempio:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

È possibile aggiornare questa proprietà tramite della risorsa [manifesto dell'applicazione][AAD-App-Manifest]e viene illustrato in client nativi multilivello chiamata esempio dell'API web nella sezione [Contenuto correlato](#related-content) alla fine di questo articolo. La figura seguente viene fornita una panoramica dell'autorizzazione per un'app di più livelli:

![Consenso all'applicazione client noti a più livelli][Consent-Multi-Tier-Known-Client] 

Un caso simile si verifica se diversi livelli di un'applicazione sono registrati in altro tenant.  Si consideri ad esempio nel caso di creazione di un'applicazione client nativo che chiama l'API di Office 365 Exchange Online.  Per sviluppare nativo dell'applicazione e versioni successiva per l'applicazione nativa eseguire nel tenant di un cliente, deve essere presenta il capitale servizio Exchange Online.  In questo caso il cliente deve acquistare Exchange Online per il servizio principale da creare nel tenant di.  In caso di un'API creata da un'organizzazione diversa da Microsoft, lo sviluppatore dell'API deve fornire un modo per i clienti di consenso loro applicazione in un tenant cliente, ad esempio una pagina web che spinge l'autorizzazione tramite i meccanismi descritti in questo articolo.  Dopo la creazione dell'entità servizio nel tenant, l'applicazione nativa accessibile token per l'API.

La figura seguente viene fornita una panoramica dell'autorizzazione per un'app multilivello registrata tenant diversi:

![Consenso all'app più parti più livelli][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revoca dell'autorizzazione
Utenti e amministratori possono revocare consenso all'applicazione in qualsiasi momento:

- Gli utenti revocano l'accesso alle singole applicazioni rimuovendoli delle [Applicazioni del Pannello di accesso] [ AAD-Access-Panel] elenco.
- Gli amministratori revocano l'accesso alle applicazioni rimuovendoli dalla Azure Active Directory utilizzando la sezione gestione di Azure Active Directory del [portale classica Azure][AZURE-classic-portal].

Se un amministratore acconsente a un'applicazione per tutti gli utenti in un tenant, gli utenti non è possibile revocare l'accesso singolarmente.  Solo l'amministratore può revocare l'accesso e solo per l'intera applicazione.

### <a name="consent-and-protocol-support"></a>Consenso dell'utente e il supporto del protocollo
Consenso è supportato in Azure Active Directory tramite OAuth, OpenID connettersi, WS-Federation e i protocolli SAML.  I protocolli SAML e WS-Federation non supportano il `prompt=admin_consent` parametro, in modo consenso dell'utente amministratore è possibile solo mediante OAuth e OpenID connettersi.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Le applicazioni multi-Tenant e la memorizzazione nella cache token di accesso
Le applicazioni multi-tenant accessibile anche token di accesso per chiamare API che sono protetti da Azure Active Directory.  Un errore comune quando si utilizza la raccolta di autenticazione Active Directory (ADAL) con un'applicazione multi-tenant è inizialmente richiedere un token per un utente che usa /common, riceve una risposta, quindi richiedere un altro token di tale utente stesso usano lo stesso /common.  Poiché la risposta da Azure Active Directory proviene da un tenant non/comune, ADAL memorizza il token come provenienti tenant. La chiamata successiva al /common per ottenere un token di accesso dell'utente perde la voce della cache e viene chiesto di accedere di nuovo.  Per evitare di perdere la cache, assicurarsi che le successive chiamate per un utente già effettuato l'accesso sono stata progettate per endpoint del tenant.

## <a name="related-content"></a>Contenuti correlati

- [Esempi di applicazioni multi-tenant][AAD-Samples-MT]
- [Personalizzazione delle linee guida per le applicazioni][AAD-App-Branding]
- [Azure Guida per gli sviluppatori Active Directory][AAD-Dev-Guide]
- [Oggetti dell'applicazione e principale del servizio][AAD-App-SP-Objects]
- [Integrazione di applicazioni con Azure Active Directory][AAD-Integrating-Apps]
- [Panoramica di Framework consenso dell'utente][AAD-Consent-Overview]
- [Microsoft Graph API ambiti di autorizzazione][MSFT-Graph-AAD]
- [Azure Active Directory grafico API ambiti di autorizzazione][AAD-Graph-Perm-Scopes]

Utilizzare la sezione commenti Disqus riportata di seguito per inviare commenti e suggerimenti degli perfezionare e definire il contenuto.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














