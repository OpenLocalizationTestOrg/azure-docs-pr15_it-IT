<properties
   pageTitle="Glossario di sviluppo di Azure Active Directory | Microsoft Azure"
   description="Elenco di condizioni di licenza per comune concetti di sviluppo di Azure Active Directory e funzionalità."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Glossario di sviluppo Azure Active Directory
In questo articolo contiene le definizioni per alcuni concetti core Azure Active Directory (AD) per sviluppatori, che risultano utili formazione sullo sviluppo di applicazioni per Azure Active Directory.

## <a name="access-token"></a>token di accesso
Un tipo di [token di sicurezza](#security-token) rilasciato da un [server di autorizzazione](#authorization-server)e utilizzato da un' [applicazione client](#client-application) per accedere a una [risorsa server protetto](#resource-server). In genere in forma di una [JSON Web Token (JWT)][JWT], il token incorpora le autorizzazioni concesse al client dal [proprietario della risorsa](#resource-owner), per un livello di richiesto di accesso. Il token contiene tutte le applicabili [sulle attestazioni](#claim) in merito, per consentire all'applicazione client di utilizzare come maschera di credenziali quando si accede a una determinata risorsa. Anche senza bisogno di proprietario della risorsa per esporre le credenziali al client.

Token di accesso sono definiti talvolta "Utente + App" o "App solo", a seconda delle credenziali rappresentate. Ad esempio, quando un'applicazione client utilizza il:

- [Concedere l'autorizzazione "Il codice di autorizzazione"](#authorization-grant), l'utente finale esegue l'autenticazione prima di tutto il proprietario di risorse di delega dell'autorizzazione al client per accedere alla risorsa. Il client autenticazione in seguito quando si ottengono il token di accesso. Il token può essere definito talvolta in particolare un token "Utente + App", in quanto rappresenta l'utente autorizzato all'applicazione client e l'applicazione.
- [Concedere l'autorizzazione "Credenziali client"](#authorization-grant), il client offre servizi di autenticazione esclusiva funzionare senza autenticazione/autorizzazione del proprietario risorsa, in modo che il token può essere definito talvolta un token "Solo App".

Vedere [informazioni di riferimento Azure Active Directory Token] [ AAD-Tokens-Claims] per altri dettagli.

## <a name="application-manifest"></a>manifesto dell'applicazione  
Caratteristica fornita da [Azure portale classica][AZURE-classic-portal], che genera una rappresentazione in formato JSON della configurazione di identità dell'applicazione, utilizzata come meccanismo per l'aggiornamento associata [applicazione] [ AAD-Graph-App-Entity] e [ServicePrincipal] [ AAD-Graph-Sp-Entity] entità. Vedere [informazioni sul manifesto dell'applicazione Azure Active Directory] [ AAD-App-Manifest] per altri dettagli.

## <a name="application-object"></a>oggetto Application  
Quando è registro o aggiornare un'applicazione nel [portale classica Azure][AZURE-classic-portal], il portale crea/aggiornamenti oggetto applicazione e un [oggetto principale del servizio](#service-principal-object) corrispondente per tenant. L'oggetto applicativo *definisce* l'applicazione di configurazione di identità a livello globale (per tutti i tenant nel punto in cui ha accesso), fornire un modello da cui relativi oggetti dell'entità servizio corrispondente sono *derivate* per l'utilizzo in locale in fase di esecuzione (in un tenant specifico).

Vedere [applicazioni e gli oggetti principale del servizio] [ AAD-App-SP-Objects] per ulteriori informazioni.

## <a name="application-registration"></a>registrazione dell'applicazione  
Per consentire a un'applicazione di integrare ed delegare funzioni di gestione delle identità e accesso per Azure Active Directory, deve essere registrato con un [tenant](#tenant)di Azure Active Directory. Quando si registra l'applicazione con Azure Active Directory, si desidera fornire una configurazione di identità dell'applicazione, in modo che possa integrare con Azure Active Directory e utilizzare caratteristiche, ad esempio:

- Gestione efficace di Single Sign-On utilizzo di gestione delle identità di Azure Active Directory e [Connettersi OpenID] [ OpenIDConnect] implementazione del protocollo
- Accesso gestito alle [risorse protette](#resource-server) dalle [applicazioni client](#client-application)tramite implementazione di [autorizzazione server](#authorization-server) 2.0 OAuth di Azure Active Directory
- [Framework di consenso](#consent) per la gestione di accesso client alle risorse protette, in base alle autorizzazioni di proprietario di risorse.

Vedere [le applicazioni di integrazione con Azure Active Directory] [ AAD-Integrating-Apps] per altri dettagli.

## <a name="authentication"></a>autenticazione
Azione di complessa una festa le credenziali legittime, fornendo la base per la creazione di un'identità di protezione da utilizzare per il controllo delle identità e accessi. Durante un [concedere l'autorizzazione OAuth2](#authorization-grant) , ad esempio, la parte autenticazione riempie il ruolo di [proprietario della risorsa](#resource-owner) o [applicazione client](#client-application), a seconda di concessione utilizzata.

## <a name="authorization"></a>autorizzazione
Azione di concedere un'autorizzazione dell'entità sicurezza autenticato di eseguire un'azione. Esistono due casi di utilizzo principale nel modello di programmazione di Azure Active Directory:

- Durante un flusso di [concedere l'autorizzazione OAuth2](#authorization-grant) : quando il [proprietario della risorsa](#resource-owner) concede l'autorizzazione all' [applicazione client](#client-application), consentendo il client per accedere alle risorse del proprietario delle risorse.
- Durante l'accesso alle risorse dal client: come implementato dal [server risorsa](#resource-server), tramite [richiedere](#claim) i valori presentano in [token di accesso](#access-token) per prendere decisioni di controllo accesso basate su di essi.

## <a name="authorization-code"></a>codice di autorizzazione
Un breve durata "token" disponibile per un' [applicazione client](#client-application) per l' [endpoint di autorizzazione](#authorization-endpoint), come parte del flusso di "codice di autorizzazione", uno dei OAuth2 quattro [concede l'autorizzazione](#authorization-grant). Viene restituito il codice all'applicazione client di risposta per l'autenticazione di un [proprietario della risorsa](#resource-owner), che indica che il proprietario della risorsa ha delegato l'autorizzazione ad accedere alle risorse richieste. Come parte del flusso di, il codice in un secondo momento è stato usato per un [token di accesso](#access-token).

## <a name="authorization-endpoint"></a>punto finale di autorizzazione
Uno dei punti finali implementati dal [server di autorizzazione](#authorization-server), utilizzato per interagire con il [proprietario della risorsa](#resource-owner) per fornire un [concedere l'autorizzazione](#authorization-grant) durante l'autorizzazione OAuth2 concedere flusso. A seconda del flusso di concedere l'autorizzazione usato, la concessione effettiva specificata può variare, ad esempio un [codice di autorizzazione](#authorization-code) o [token di sicurezza](#security-token).

Vedere la sezione della specifica OAuth2 [autorizzazione concedere tipi] [ OAuth2-AuthZ-Grant-Types] e [autorizzazione endpoint] [ OAuth2-AuthZ-Endpoint] sezioni e la [specifica OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] per altri dettagli.

## <a name="authorization-grant"></a>concedere l'autorizzazione
Credenziali che rappresenta l' [autorizzazione](#authorization) di [proprietario della risorsa](#resource-owner) per accedere alle risorse protette abbia concesso o meno a un' [applicazione client](#client-application). Un'applicazione client può utilizzare uno dei [quattro tipi di autorizzazioni definiti dal Framework autorizzazione OAuth2] [ OAuth2-AuthZ-Grant-Types] per ottenere un grant, a seconda di tipo/requisiti client: "autorizzazione codice grant", "credenziali client concedono", "Concedi impliciti" e "credenziali password proprietario risorsa concedono". Le credenziali restituita al client sono un [token di accesso](#access-token)o un [codice di autorizzazione](#authorization-code) (scambio di in un secondo momento per un token di accesso), a seconda del tipo di concedere l'autorizzazione utilizzato.

## <a name="authorization-server"></a>server di autorizzazione
Come definito da [OAuth2 autorizzazione Framework][OAuth2-Role-Def], il server responsabile del rilascio accesso token al [client](#client-application) al termine autenticazione il [proprietario della risorsa](#resource-owner) e ottenere l'autorizzazione. Un' [applicazione client](#client-application) interagisce con il server di autorizzazione in fase di esecuzione tramite l' [autorizzazione](#authorization-endpoint) e gli endpoint [token](#token-endpoint) , secondo la OAuth2 definiti [concede l'autorizzazione](#authorization-grant).

In caso di integrazione di applicazioni Azure Active Directory, Azure Active Directory implementa il ruolo del server di autorizzazione per le applicazioni di Azure Active Directory e Microsoft API del servizio, ad esempio [Microsoft Graph API][Microsoft-Graph].

## <a name="claim"></a>richiesta di rimborso
Un [token di sicurezza](#security-token) contiene delle attestazioni, che forniscono asserzioni su un'entità (ad esempio [un'applicazione client](#client-application) o un [proprietario delle risorse](#resource-owner)) a un'altra entità (ad esempio il [server delle risorse](#resource-server)). Le attestazioni sono coppie nome/valore che inoltrare i dati relativi a argomento token (ad esempio identità di protezione autenticato dal [server di autorizzazione](#authorization-server)). Attestazioni presente in un determinato token dipendono da diverse variabili, inclusi il tipo di token, il tipo di credenziali utilizzato per autenticare l'oggetto, la configurazione dell'applicazione e così via.

Vedere [informazioni di riferimento token Azure Active Directory] [ AAD-Tokens-Claims] per altri dettagli.

## <a name="client-application"></a>applicazione client  
Come definito da [OAuth2 autorizzazione Framework][OAuth2-Role-Def], un'applicazione che rende protetta le richieste di risorse per conto del [proprietario della risorsa](#resource-owner). Il termine "client" non implica le caratteristiche di implementazione hardware specifico, ad esempio, se l'applicazione viene eseguito su un server, un desktop o altri dispositivi.  

Un'applicazione client le richieste di [autorizzazione](#authorization) da un proprietario della risorsa a partecipare a un flusso di [concedere l'autorizzazione OAuth2](#authorization-grant) e può accedere API/dati per conto del proprietario delle risorse. Il Framework di autorizzazione OAuth2 [definisce due tipi di client][OAuth2-Client-Types], "informazioni riservate" e "pubblico", in base alle capacità dei client di mantenere la riservatezza delle proprie credenziali. Le applicazioni di implementare un [client web (riservate)](#web-client) che viene eseguito su un server web, un [client nativi (pubblico)](#native-client) installato in un dispositivo o un [client basati su agente utente (pubblico)](#user-agent-based-client) che viene eseguito nel browser del dispositivo.

## <a name="consent"></a>consenso dell'utente
Processo di un [proprietario della risorsa](#resource-owner) concessione dell'autorizzazione per un' [applicazione client](#client-application), [le autorizzazioni](#permissions) specifiche per accedere alle risorse protette, per conto di proprietario della risorsa. A seconda delle autorizzazioni richieste dal client, un amministratore o utente verrà chiesto di consenso per consentire l'accesso ai dati organizzazione/singoli rispettivamente. Si noti in uno scenario [multi-tenant](#multi-tenant-application) dell'applicazione [servizio principale](#service-principal-object) vengono inoltre registrate tenant dell'utente consenting.

## <a name="id-token"></a>Token ID
Una [Connessione OpenID] [ OpenIDConnect-ID-Token] [token di sicurezza](#security-token) fornito da un [server di autorizzazione](#authorization-server) [endpoint di autorizzazione](#authorization-endpoint), che contiene [reclami](#claim) relativi all'autenticazione di un utente finale [proprietario della risorsa](#resource-owner). Ad esempio un token di accesso, token ID anche vengono rappresentati in una firma digitale [JSON Web Token (JWT)][JWT]. Diversamente da quanto succede token di accesso delle attestazioni del token un ID non vengono utilizzate per scopi correlati per l'accesso alle risorse e controllo dell'accesso in modo specifico.

Vedere [informazioni di riferimento token Azure Active Directory] [ AAD-Tokens-Claims] per altri dettagli.

## <a name="multi-tenant-application"></a>applicazione multi-tenant
Una classe dell' [applicazione client](#client-application) che consente l'accesso e [consenso](#consent) dagli utenti effettuato il provisioning in qualsiasi Azure Active Directory [tenant](#tenant), tra cui tenant diverso da quello in cui è stato registrato il client. Al contrario, un'applicazione registrata come singolo tenant, consentirebbe accessi dagli account utente viene completato il provisioning nello stesso tenant come quella dove è stato registrato l'applicazione. Applicazioni [client nativi](#native-client) sono multi-tenant per impostazione predefinita, mentre le applicazioni [client web](#web-client) sono possibile scegliere tra singolo e multi-tenant.

Informazioni su [come effettuare l'accesso a tutti gli utenti di Azure Active Directory utilizzando il modello di applicazione multi-tenant] [ AAD-Multi-Tenant-Overview] per altri dettagli.

## <a name="native-client"></a>native client
Tipo di [applicazione client](#client-application) in cui è installato a livello nativo in un dispositivo. Dal momento che tutto il codice viene eseguito su un dispositivo, viene considerato come un client a causa non è in grado di archiviare le credenziali privatamente/riservatezza "pubblico". Vedere [profili e tipi di client OAuth2] [ OAuth2-Client-Types] per altri dettagli.

## <a name="permissions"></a>autorizzazioni
Un' [applicazione client](#client-application) accede a un [server di risorse](#resource-server) mediante la dichiarazione di richieste di accesso. Sono disponibili due tipi:

- Autorizzazioni "Delegate", richiedono l'accesso [basate su ambiti](#scopes) in delegati autorizzazione al [proprietario della risorsa](#resource-owner)eseguito l'accesso, vengono presentate alla risorsa in fase di esecuzione come ["scp" reclami](#claim) del cliente [token di accesso](#access-token).
- Autorizzazioni "Applicazione", richiedono l'accesso [basato sui ruoli](#roles) con le credenziali/identità dell'applicazione client, vengono presentate alla risorsa in fase di esecuzione come ["ruoli" dichiara](#claim) token di accesso del cliente.

Sono inoltre superficie durante il processo [di consenso](#consent) , dato l'amministratore o un proprietario della risorsa l'opportunità di concedere/negare l'accesso client alle risorse nel tenant di.

Richieste di accesso sono configurati "Applicazioni" / "Configurare" scheda nel [portale classica Azure][AZURE-classic-portal], nella sezione "Autorizzazioni ad altre applicazioni", dalle selezione desiderata "delegato autorizzazioni" e "Applicazione autorizzazioni" (quest'ultimo è necessario appartenere al ruolo amministratore globale). Perché un [client pubblico](#client-application) non è possibile gestire le credenziali, può richiedere solo le autorizzazioni delegate, mentre un [client riservate](#client-application) ha la possibilità di richiesta delegati e le autorizzazioni delle applicazioni. Del client [oggetto applicazione](#application-object) memorizza le autorizzazioni dichiarate nella relativa [proprietà requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietario della risorsa
Come definito da [OAuth2 autorizzazione Framework][OAuth2-Role-Def], un'entità in grado di concessione dell'accesso a una risorsa protetta. Proprietario della risorsa è una persona, viene definito un utente finale. Ad esempio, quando un' [applicazione client](#client-application) desidera accedere a postale una cassetta tramite l' [API di Microsoft Graph][Microsoft-Graph], richiede l'autorizzazione dal proprietario della risorsa della cassetta postale.

## <a name="resource-server"></a>server di risorse
Come definito da [OAuth2 autorizzazione Framework][OAuth2-Role-Def], un server che ospita risorse protette, in grado di accettare e rispondere a protetto le richieste di risorse da [applicazioni client](#client-application) che presentano un [token di accesso](#access-token). Nota anche come una risorsa protetta server o applicazione delle risorse.

Un server di risorse espone API e impone l'accesso alle risorse protette tramite [ambiti](#scopes) e [ruoli](#roles), utilizzando il Framework di autorizzazione OAuth 2.0. Ad esempio l'API di Azure Active Directory grafico che fornisce l'accesso ai dati di Azure Active Directory tenant e le API di Office 365 che consentono di accedere ai dati, ad esempio posta e calendario. Entrambi questi sono accessibili tramite l' [API di Microsoft Graph][Microsoft-Graph].  

Analoga a un'applicazione client, configurazione di identità dell'applicazione di risorsa viene stabilita tramite [la registrazione](#application-registration) in un tenant di Azure Active Directory, fornire l'applicazione e l'oggetto principale del servizio. Alcune API fornito da Microsoft, ad esempio l'API di Azure Active Directory grafico sono pre-registrati identità di servizio resa disponibile in tutti i tenant durante il provisioning.

## <a name="roles"></a>ruoli
Ad esempio [ambiti](#scopes)ruoli consentono di un [server delle risorse](#resource-server) controllare l'accesso alle risorse protette. Esistono due tipi: un ruolo "utente" implementazione controllo dell'accesso basato sui ruoli per gli utenti/gruppi che richiedono l'accesso alla risorsa, mentre un ruolo "applicazione" implementato la stessa operazione per [le applicazioni client](#client-application) che richiedono l'accesso.

Ruoli sono stringhe di risorsa definita (ad esempio "spesa responsabile dell'approvazione", "Sola lettura", "Directory.ReadWrite.All"), gestito nel [portale classica Azure] [ AZURE-classic-portal] tramite [manifesto dell'applicazione](#application-manifest)della risorsa e archiviato nei della risorsa [appRoles proprietà][AAD-Graph-Sp-Entity]. Il portale classico Azure viene usato anche per assegnare utenti ai ruoli "utente" e configurare [le autorizzazioni delle applicazioni](#permissions) di client per accedere a un ruolo "applicazione".

Per una descrizione dettagliata dei ruoli applicazione esposti dall'API di grafico di Azure Active Directory, vedere [Gli ambiti di autorizzazione API grafico][AAD-Graph-Perm-Scopes]. Per un esempio di implementazione dettagliata, vedere [controllo dell'accesso nelle applicazioni cloud utilizzando Azure Active Directory basato sui ruoli][Duyshant-Role-Blog].

## <a name="scopes"></a>ambiti
Ad esempio [ruoli](#roles)ambiti consentono per un [server delle risorse](#resource-server) controllare l'accesso alle risorse protette. Gli ambiti vengono utilizzati per implementare [basate su ambiti] [ OAuth2-Access-Token-Scopes] controllo dell'accesso, per un' [applicazione client](#client-application) di cui è stato assegnato delega per l'accesso alla risorsa dal proprietario.

Ambiti sono definiti risorsa stringhe (ad esempio "Mail.Read", "Directory.ReadWrite.All"), gestite nel [portale classica Azure] [ AZURE-classic-portal] tramite [manifesto dell'applicazione](#application-manifest)della risorsa e archiviato nei della risorsa [proprietà oauth2Permissions][AAD-Graph-Sp-Entity]. Il portale classico Azure viene usato anche per configurare client di applicazione [delegate le autorizzazioni](#permissions) per accedere a un ambito.

Una convenzione di denominazione esercitazione migliore, è possibile utilizzare un formato "resource.operation.constraint". Per una descrizione dettagliata degli ambiti esposti dall'API di grafico di Azure Active Directory, vedere [Gli ambiti di autorizzazione API grafico][AAD-Graph-Perm-Scopes]. Per gli ambiti esposti da servizi di Office 365, vedere [informazioni di riferimento API di Office 365 autorizzazioni][O365-Perm-Ref].

## <a name="security-token"></a>token di sicurezza
Un documento firmato contenente sulle attestazioni, ad esempio un token OAuth2 o asserzione SAML 2.0. Per un OAuth2 [concedere l'autorizzazione](#authorization-grant), un [token di accesso](#access-token) (OAuth2) e un [ID Token](OpenID Connect) sono tipi di token di sicurezza, che sono previsti un [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>oggetto principale del servizio
Quando è registro o aggiornare un'applicazione nel [portale classica Azure][AZURE-classic-portal], il portale crea/aggiornamenti un [oggetto applicazione](#application-object) e un oggetto dell'entità servizio corrispondente per tenant. L'oggetto applicativo *definisce* la configurazione dell'applicazione identità globale (per tutti i tenant nel punto in cui l'applicazione associata è stato concesso l'accesso), e il modello dal quale relativi oggetti dell'entità servizio corrispondente sono *derivate* per l'utilizzo in locale in fase di esecuzione (in un tenant specifico).

Vedere [applicazioni e gli oggetti principale del servizio] [ AAD-App-SP-Objects] per ulteriori informazioni.

## <a name="sign-in"></a>accesso
Il processo di un' [applicazione client](#client-application) avviare l'autenticazione degli utenti finali e l'acquisizione correlate stato per l'acquisizione di un [token di sicurezza](#security-token) e definizione dell'ambito della sessione dell'applicazione a tale stato. Stato possono includere elementi, ad esempio informazioni del profilo utente e informazioni fornite dai token delle attestazioni.

Funzione accesso di un'applicazione viene generalmente utilizzata per implementare single sign-on (SSO). Si può anche essere preceduto da una funzione "abbonamento", come punto di ingresso per un utente finale accedere a un'applicazione (al momento del primo accesso). La funzione per l'abbonamento viene utilizzata per raccogliere e mantenere uno stato aggiuntivo specifico dell'utente e può richiedere [consenso all'utente](#consent).

## <a name="sign-out"></a>disconnessione
Il processo di annullamento della autenticazione utente finale, scollegamento lo stato dell'utente associato alla sessione di [applicazione client](#client-application) durante [l'accesso](#sign-in)

## <a name="tenant"></a>tenant
Un'istanza di una directory di Azure Active Directory viene definita un tenant di Azure Active Directory. Sono disponibili numerose caratteristiche, tra cui:

- un servizio del Registro di sistema per applicazioni integrate
- autenticazione dell'account utente e applicazioni registrate
- Endpoint resto necessari per supportare diversi protocolli inclusi OAuth2 e SAML, inclusi l' [endpoint di autorizzazione](#authorization-endpoint), [endpoint token](#token-endpoint) e l'endpoint "comune" utilizzato da [applicazioni multi-tenant](#multi-tenant-application).

Un tenant è associato a un annuncio Azure o l'abbonamento a Office 365 durante il provisioning della sottoscrizione, fornendo caratteristiche di identità e gestione di accesso per la sottoscrizione. Informazioni su [come ottenere un tenant di Azure Active Directory] [ AAD-How-To-Tenant] per informazioni dettagliate sulle diverse modalità è possibile accedere a un tenant. Vedere [come Azure abbonamenti sono associati Azure Active Directory] [ AAD-How-Subscriptions-Assoc] per informazioni dettagliate sulla relazione tra le sottoscrizioni e un tenant di Azure Active Directory.

## <a name="token-endpoint"></a>endpoint token
Uno dei punti finali implementati dal [server di autorizzazione](#authorization-server) per il supporto OAuth2 [concede l'autorizzazione](#authorization-grant). A seconda delle autorizzazioni, può essere utilizzato per acquisire un [token di accesso](#access-token) (e correlate token "Aggiorna") a un [client](#client-application)o [token ID](#ID-token) quando si utilizza [La connessione OpenID] [ OpenIDConnect] protocollo.

## <a name="user-agent-based-client"></a>Client basato su agente utente
Tipo di [applicazione client](#client-application) Scarica codice da un server web che viene eseguita all'interno di un agente utente (ad esempio un web browser), ad esempio una singola pagina applicazione (SPA). Dal momento che tutto il codice viene eseguito su un dispositivo, viene considerato come un client a causa non è in grado di archiviare le credenziali privatamente/riservatezza "pubblico". Vedere [profili e tipi di client OAuth2] [ OAuth2-Client-Types] per altri dettagli.

## <a name="user-principal"></a>dell'entità utente
In modo simile a che un oggetto principale del servizio viene utilizzato per rappresentare un'istanza di applicazione, un oggetto dell'entità utente è un altro tipo di sicurezza principale, che rappresenta un utente. L' grafico di Azure Active Directory [entità utente] [ AAD-Graph-User-Entity] definisce lo schema per un oggetto utente, incluse le proprietà relative agli utenti, ad esempio nome e cognome, nome dell'entità utente, l'appartenenza al ruolo di directory e così via. In questo modo la configurazione di identità utente per Azure Active Directory stabilire un'entità utente in fase di esecuzione. Dell'entità utente viene utilizzato per rappresentare un utente autenticato per Single Sign-On, registrazione [consenso](#consent) delega, effettuare le decisioni di controllo di accesso e così via.

## <a name="web-client"></a>client Web
Tipo di [applicazione client](#client-application) che esegue tutto il codice in un server web in grado di funzionare come "riservato" client archiviando in modo sicuro le proprie credenziali nel server. Vedere [profili e tipi di client OAuth2] [ OAuth2-Client-Types] per altri dettagli.

## <a name="next-steps"></a>Passaggi successivi
La [Guida per gli sviluppatori di Azure Active Directory] [ AAD-Dev-Guide] è il portale da utilizzare per lo sviluppo di Azure AD argomenti, inclusa una panoramica [dell'integrazione di applicazioni] correlati[ AAD-How-To-Integrate] e nozioni di base di [autenticazione di Azure Active Directory e scenari di autenticazione supportati][AAD-Auth-Scenarios].

Utilizzare la sezione commenti Disqus seguente per inviare commenti e suggerimenti degli perfezionare e definire il contenuto.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
