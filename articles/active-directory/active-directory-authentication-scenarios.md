
<properties
   pageTitle="Scenari di autenticazione per Azure AD | Microsoft Azure"
   description="Cenni preliminari i cinque scenari di autenticazione più comuni per Azure Active Directory (AAD)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Scenari di autenticazione per Azure Active Directory

Azure Active Directory (Azure Active Directory) semplifica l'autenticazione per gli sviluppatori fornendo identità come origine dei servizio con il supporto per protocolli standard, ad esempio OAuth 2.0 e OpenID connettersi, nonché aprire raccolte per piattaforme diverse avviare la codifica rapidamente. In questo documento consentono di comprendere le varie supporta scenari Azure Active Directory e illustra come iniziare. È diviso in sezioni seguenti:

- [Nozioni fondamentali di autenticazione in Azure Active Directory](#basics-of-authentication-in-azure-ad)

- [Attestazioni nei token di sicurezza di Azure Active Directory](#claims-in-azure-ad-security-tokens)

- [Nozioni fondamentali di registrazione di un'applicazione in Azure Active Directory](#basics-of-registering-an-application-in-azure-ad)

- [Tipi di applicazioni e scenari](#application-types-and-scenarios)

  - [Browser all'applicazione Web](#web-browser-to-web-application)

  - [Applicazione della singola pagina (SPA)](#single-page-application-spa)

  - [Applicazione nativa per dell'API Web](#native-application-to-web-api)

  - [Applicazione Web dell'API Web](#web-application-to-web-api)

  - [Daemon o dell'applicazione Server dell'API Web](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Nozioni fondamentali di autenticazione in Azure Active Directory

Se non si ha familiarità con concetti di base di autenticazione in Azure Active Directory, leggere questa sezione. In caso contrario, può essere necessario ignorare fino a [tipi di applicazioni e gli scenari](#application-types-and-scenarios).

Si consideri lo scenario più semplice in cui è necessario identità: un utente in un web browser richiede l'autenticazione a un'applicazione web. Questo scenario è descritto in dettaglio nella sezione [Browser all'applicazione Web](#web-browser-to-web-application) , ma è utile punto di partenza per illustrare le funzionalità di Azure Active Directory e definire il concetto di funzionamento lo scenario. È consigliabile per questo scenario nel diagramma seguente:

![Panoramica di accesso all'applicazione web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Il diagramma in precedenza in considerazione, ecco cosa è necessario conoscere i vari componenti:

- Azure Active Directory è il provider di identità, responsabile della verifica dell'identità di utenti e le applicazioni presenti nella directory dell'organizzazione e infine emissione token di sicurezza volta eseguita l'autenticazione di tali utenti e applicazioni.


- Un'applicazione che affidare l'autenticazione di Azure Active Directory deve essere registrata in Azure Active Directory, che registra e identifica in modo univoco l'app nella directory.


- Gli sviluppatori possono utilizzare le raccolte di autenticazione Apri origine Azure Active Directory per semplificare l'autenticazione mediante la gestione di dettagli protocollo dell'utente. Per ulteriori informazioni, vedere [Le raccolte di Azure Active Directory l'autenticazione](active-directory-authentication-libraries.md) .


• Dopo che un utente autenticato, l'applicazione deve convalida token di sicurezza dell'utente per assicurarsi che l'autenticazione riuscita per le parti previste. Gli sviluppatori possono utilizzare le raccolte di autenticazione disponibili per gestire la convalida di tutti i token da Azure Active Directory, ad esempio JSON Web token (JWT) o SAML 2.0. Se si desidera eseguire la convalida manualmente, vedere la documentazione per la [Gestione dei Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Azure Active Directory utilizza crittografia a chiave pubblica per firmare i token e verificare che siano validi. Visualizzare [Importanti informazioni sulla firma chiave attivazione di Azure Active Directory](active-directory-signing-key-rollover.md) per ulteriori informazioni sulla logica necessaria, è necessario disporre dell'applicazione per verificare che sia sempre aggiornati con i tasti più recente.


• Il flusso di richieste e risposte per il processo di autenticazione dipende dal protocollo di autenticazione utilizzato, ad esempio OAuth 2.0, OpenID connettersi, WS-Federation o SAML 2.0. In modo più dettagliato nella sezione [Protocolli di autenticazione di Azure Active Directory](active-directory-authentication-protocols.md) e nelle sezioni seguenti sono descritti questi protocolli.

> [AZURE.NOTE] Azure Active Directory supporta 2.0 OAuth e standard OpenID connettersi che fanno ampio uso dei token del titolare, inclusi i token del titolare rappresentati come JWTs. Un token del titolare è un token di sicurezza leggera che concede l'accesso "titolare" a una risorsa protetta. In questo caso, "titolare" è tutte le parti che possono presentare il token. Se una festa necessario eseguire l'autenticazione con Azure Active Directory per ricevere il token del titolare, se non sono prese i passaggi necessari per proteggere il token in trasmissione e lo spazio di archiviazione, può essere intercettare e utilizzato da una parte indesiderata. Alcuni token di sicurezza dispone di un meccanismo incorporato per impedire che averli autorizzati, i token del titolare non dispone di questo meccanismo mentre devono avvenire in un canale sicuro, ad esempio protezione a livello di trasporto (HTTPS). Se un token del titolare viene trasmesso in chiaro, attacco di un uomo aggiuntivo può essere usato da una festa dannosa per acquisire il token e usarlo per un accesso non autorizzato a una risorsa protetta. Possono essere applicati sicurezza anche quando l'archiviazione o la memorizzazione nella cache token del titolare per successivi utilizzi. Sempre assicurarsi che l'applicazione trasmette e archivia i token del titolare in modo sicuro. Per altre considerazioni sulla protezione sui token del titolare, vedere [RFC 6750 sezione 5](http://tools.ietf.org/html/rfc6750).


Dopo aver creato una panoramica dei concetti di base, leggere le sezioni seguenti per comprendere il funzionamento di provisioning in Azure Active Directory e supporta gli scenari comuni Azure Active Directory.


## <a name="claims-in-azure-ad-security-tokens"></a>Attestazioni nei token di sicurezza di Azure Active Directory

Token di sicurezza rilasciato da Azure Active Directory contengono i reclami o asserzioni delle informazioni sull'oggetto che è stato autenticato. Queste domande possono essere usate dall'applicazione delle varie attività. Ad esempio, può essere utilizzati per convalidare il token, identificare tenant directory del soggetto, visualizzare le informazioni utente, determinare le autorizzazioni dell'oggetto e così via. Attestazioni presente in qualsiasi token di sicurezza specificato dipendono dal tipo di token, il tipo di credenziale utilizzato per eseguire l'autenticazione dell'utente e la configurazione dell'applicazione. Una breve descrizione di ogni tipo di domanda emesso da Azure Active Directory viene specificata nella tabella seguente. Per ulteriori informazioni, consultare [supportati Token e tipi di attestazione](active-directory-token-and-claims.md).


| Richiesta di rimborso | Descrizione |
|-------|-------------|
| ID applicazione | Identifica l'applicazione che utilizza il token.
| Gruppo di destinatari | Identifica la risorsa destinatario che è destinato il token. |
| Riferimento di classe di applicazione autenticazione contesto | Indica come il client non autenticato (pubblico client e client riservate). |
| Autenticazione immediata | Registra la data e ora in cui si è verificato l'autenticazione. |
| Metodo di autenticazione | Indica la modalità di autenticazione l'oggetto del token (password, certificato e così via). |
| Nome | Fornisce il nome dell'utente come set di Azure Active Directory. |
| Gruppi | Contiene l'utente è un membro di gruppi di oggetti ID di Azure Active Directory. |
| Provider di identità | Registra il provider di identità che l'oggetto del token autenticati. |
| Rilasciato a | Registrare il tempo in cui è stato inviato il token, spesso usato per i token validità. |
| Emittente | Consente di identificare il servizio token di sicurezza che ha generato il token, nonché il tenant di Azure Active Directory. |
| Cognome | Fornisce il cognome dell'utente come set di Azure Active Directory. |
| Nome | Fornisce un valore leggibile umano che identifica l'oggetto del token. |
| Id oggetto | Contiene un identificatore univoco e non modificabile dell'argomento in Azure Active Directory. |
| Ruoli | Contiene nomi di ruoli di applicazione Azure Active Directory cui è stata concessa l'utente. |
| Ambito | Indica le autorizzazioni concesse all'applicazione client. |
| Oggetto | Indica il capitale intorno al quale il token indica informazioni. |
| Id tenant | Contiene un identificatore univoco e non modificabile del tenant di directory che ha rilasciato il token. |
| Durata | Consente di definire l'intervallo di tempo entro il quale è valido un token. |
| Nome dell'entità utente | Contiene il nome dell'entità utente dell'argomento. |
| Versione | Contiene il numero di versione del token. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Nozioni fondamentali di registrazione di un'applicazione in Azure Active Directory

Qualsiasi applicazione che concede in outsourcing l'autenticazione di Azure Active Directory deve essere registrata in una directory. Questo passaggio implica che indica di Azure Active Directory relative all'applicazione, inclusi l'URL in cui individuate, l'URL per l'invio di risposte dopo l'autenticazione, URI per identificare l'applicazione e altro ancora. Queste informazioni sono necessarie per motivi principali:

- Azure Active Directory richiede le coordinate per comunicare con l'applicazione quando si gestiscono i token di accesso o scambio. Questi sono i seguenti:

  - URI ID applicazione: L'identificatore per un'applicazione. Questo valore viene inviato a Azure Active Directory durante l'autenticazione per indicare quale applicazione chiamante richiede un token per. Inoltre, tale valore è incluso nel token in modo che l'applicazione sappia che era di destinazione.


  - Rispondere a URL e reindirizzare URI: in caso di un'applicazione web o API web, l'URL di risposta è il percorso in cui Azure Active Directory invierà la risposta di autenticazione, incluso un token di se è stata completata l'autenticazione. In caso di un'applicazione nativa URI reindirizzamento non è un identificatore univoco a cui Azure Active Directory reindirizzeranno l'agente utente in una richiesta di OAuth 2.0.


  - ID acquirente: L'ID per un'applicazione, viene generata da Azure Active Directory quando l'applicazione registrata. Quando si richiede un codice di autorizzazione o token, l'ID client e la chiave inviati di Azure Active Directory durante l'autenticazione.


  - Chiave: La chiave che verrà inviata insieme a un ID client per l'autenticazione a Azure Active Directory per chiamare un API web.


- Azure Active Directory è necessario assicurarsi che l'applicazione dispone delle autorizzazioni necessarie per accedere ai dati di directory, altre applicazioni all'interno dell'organizzazione e così via

Provisioning diventa più chiaro la conoscenza che sono disponibili due categorie di applicazioni che è possibile sviluppare e integrate con Azure Active Directory:

- È possibile applicare singola applicazione tenant: un'applicazione tenant singola deve essere utilizzato in un'organizzazione. Si tratta in genere line-of-business applicazioni line scritte da uno sviluppatore aziendale. Un'applicazione tenant singola solo deve essere accessibili agli utenti in una sola cartella e di conseguenza, è necessario solo effettuare il provisioning in una sola cartella. Queste applicazioni vengono in genere registrate da uno sviluppatore nell'organizzazione.


- Applicazione multi-tenant: un'applicazione multi-tenant deve essere utilizzato in molte organizzazioni, non appena un'organizzazione. Si tratta di applicazioni (SaaS) in genere software-as-a-service scritte da un fornitore software indipendente (). Le applicazioni multi-tenant devono essere effettuato il provisioning in ogni directory in cui verranno utilizzati, che richiedono registrarle consenso utente o dall'amministratore. Questo processo di consenso viene avviato quando un'applicazione è stata registrata nella directory ed è in grado di API grafico o un altro web API. Quando un utente o un amministratore da un'altra organizzazione si iscrive a utilizzare l'applicazione, viene visualizzata una finestra di dialogo che consente di visualizzare le autorizzazioni che richiede l'applicazione. L'utente o un amministratore può quindi consenso all'applicazione, che consente all'applicazione di accedere ai dati indicati e infine Registra l'applicazione nella directory di. Per ulteriori informazioni, vedere [Panoramica di Framework consenso](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Altre considerazioni si verificano durante lo sviluppo di un'applicazione multi-tenant invece di un'applicazione tenant singola. Ad esempio, se si rende l'applicazione disponibile per gli utenti in più directory, è necessario un meccanismo per determinare quali tenant si trovano. Un'applicazione tenant singola sufficiente da considerare nella propria directory per un utente, mentre un'applicazione multi-tenant deve identificare un utente specifico da tutte le directory in Azure Active Directory. Per eseguire questa operazione, Azure Active Directory fornisce un endpoint di autenticazione comuni in qualsiasi applicazione multi-tenant può indirizzare richieste di accesso, anziché un endpoint specifiche del tenant. Si tratta https://login.microsoftonline.com/common per tutte le directory in Azure Active Directory, mentre un endpoint specifiche del tenant potrebbe essere https://login.microsoftonline.com/contoso.onmicrosoft.com. L'endpoint comune è particolarmente importante da considerare quando si sviluppa l'applicazione perché è necessario la logica necessaria per gestire più tenant durante l'accesso, disconnessione e convalida dei token.

Se si desidera rendere disponibili per molte organizzazioni sviluppo di un'applicazione singola tenant, è possibile facilmente apportare modifiche all'applicazione e la configurazione di Azure Active Directory per renderla più tenant dotati. Inoltre, Azure Active Directory utilizza la stessa chiave di firma per tutti i token di tutte le directory, se si desidera fornire l'autenticazione in una singola tenant o applicazione multi-tenant.

Ogni scenario elencato in questo documento include una sezione secondari che descrive i requisiti di provisioning. Per informazioni più approfondite su il provisioning di un'applicazione di Azure Active Directory e le differenze tra applicazioni singole e multi-tenant, vedere [Le applicazioni di integrazione con Azure Active Directory](active-directory-integrating-applications.md) per ulteriori informazioni. Continuare la lettura per comprendere gli scenari di applicazioni comuni in Azure Active Directory.

## <a name="application-types-and-scenarios"></a>Tipi di applicazioni e scenari

Tutti gli scenari descritti in questo documento possono essere sviluppati utilizzando diverse lingue e piattaforme. Sono tutte supportate da esempi di codice completo che sono disponibili la [Guida per gli esempi di codice](active-directory-code-samples.md)o direttamente dai corrispondenti [archivi di esempio Github](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Inoltre, se l'applicazione necessita di un segmento di uno scenario-to-end o una parte specifica, nella maggior parte dei casi tale funzionalità possono essere aggiunti in modo indipendente. Ad esempio, se si dispone di un'applicazione nativa che chiama dell'API web, è possibile aggiungere facilmente un'applicazione web che anche chiamata API web. Il diagramma seguente illustra questi scenari e tipi di applicazione, e come possono essere aggiunti diversi componenti:

![Tipi di applicazioni e scenari](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Si tratta di cinque scenari di applicazione primario supportati da Azure Active Directory:

- [Web Browser all'applicazione Web](#web-browser-to-web-application): l'utente deve eseguire l'accesso a un'applicazione web che è protetto da Azure Active Directory.

- [Applicazione di pagina singola (SPA)](#single-page-application-spa): l'utente deve eseguire l'accesso a un'applicazione singola pagina protetto da Azure Active Directory.

- [Applicazione nativa all'API Web](#native-application-to-web-api): un'applicazione nativa che viene eseguita in un telefono, un tablet o un PC che richiede l'autenticazione di un utente per ottenere le risorse da un web API protetto da Azure Active Directory.

- [Applicazione Web alle API Web](#web-application-to-web-api): un'applicazione web deve ottenere risorse da un web API protetto da Azure Active Directory.

- [Daemon o applicazione Server all'API Web](#daemon-or-server-application-to-web-api): un'applicazione daemon o un server senza interfaccia utente web deve ottenere risorse da un web API protetto da Azure Active Directory.

### <a name="web-browser-to-web-application"></a>Browser all'applicazione Web

In questa sezione descrive un'applicazione che autentica un utente in un web browser a un'applicazione web. In questo scenario, l'applicazione web indirizza il browser dell'utente per accere al Azure Active Directory. Azure Active Directory restituisce una risposta accesso tramite il browser dell'utente, che contiene domande relative all'utente in un token di sicurezza. Questo scenario supporta l'accesso tramite i protocolli WS-Federation, SAML 2.0 e OpenID connettersi.


#### <a name="diagram"></a>Diagramma
![Flusso di autenticazione per browser all'applicazione web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Descrizione del flusso di protocollo


1. Quando un utente visita l'applicazione ed esigenze aziendali per l'accesso, viene reindirizzato tramite una richiesta di accesso all'endpoint l'autenticazione in Azure Active Directory.


2. L'utente accede nella pagina di accesso.


3. Se l'autenticazione ha esito positivo, Azure Active Directory crea un token di autenticazione e restituisce una risposta di accesso a URL di risposta dell'applicazione configurati nel portale di gestione di Azure. Per un'applicazione di produzione, l'URL di risposta deve essere HTTPS. Token restituito include reclami sull'utente e Azure Active Directory necessarie per l'applicazione per convalidare il token.


4. L'applicazione verifica il token utilizzando una chiave di firma pubblica e informazioni emittente disponibile il documento di metadati federazione per Azure Active Directory. Dopo l'applicazione convalidato il token, Azure Active Directory avvia una nuova sessione con l'utente. Questa sessione consente all'utente accedere all'applicazione fino alla scadenza.


#### <a name="code-samples"></a>Esempi di codice


Vedere gli esempi di codice per Web Browser per gli scenari di applicazioni Web. E controllare frequentemente - si aggiunge nuovi esempi sempre questa impostazione. [Browser all'applicazione Web](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>La registrazione


- Singolo Tenant: Se si sta creando un'applicazione solo per l'organizzazione, deve essere registrato nella directory dell'azienda tramite il portale di gestione di Azure.


- Multi-Tenant: Se si sta creando un'applicazione che può essere utilizzata dagli utenti all'esterno dell'organizzazione, devono essere registrato nella directory dell'azienda, ma anche devono essere registrato nella directory dell'organizzazione che effettuano l'accesso tramite l'applicazione. Per rendere disponibile nella directory di applicazione, è possibile includere un processo di iscrizione per i clienti che consente alle consenso all'applicazione. Quando si effettua l'iscrizione per l'applicazione, viene visualizzata una finestra di dialogo che mostra le autorizzazioni che dell'applicazione richiede e la possibilità di consenso. A seconda delle autorizzazioni necessarie, un amministratore all'interno dell'organizzazione potrebbe essere necessario fornire consenso dell'utente. Quando l'utente o amministratore consenso, l'applicazione è registrato nella directory di. Per ulteriori informazioni, vedere [Le applicazioni di integrazione con Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Scadenza token

Sessione la scade quando scade la durata del token rilasciato da Azure Active Directory. L'applicazione ridurre questo periodo di tempo se lo si desidera, ad esempio disconnessione utenti in base a un periodo di inattività. Quando scade la sessione, l'utente verrà richiesto di accedere di nuovo.





### <a name="single-page-application-spa"></a>Applicazione della singola pagina (SPA)

In questa sezione descrive l'autenticazione per una singola applicazione di pagina, che utilizza Azure Active Directory e l'autorizzazione implicita OAuth 2.0 concedere per la protezione del web API indietro terminare. Singole applicazioni di pagina sono in genere strutturate come un livello di presentazione JavaScript (front-end) che viene eseguita nel browser e un back-end API Web che viene eseguito su un server e regole business dell'applicazione. Per ulteriori informazioni su come autorizzazione implicita grant e agevolare la decisione se si tratta appropriata per lo scenario di applicazione, vedere [la comprensione del flusso di concessione impliciti OAuth2 di Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

In questo scenario, quando l'utente accede, JavaScript front end ottimali [Active Directory Authentication Library per JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) e la concessione di autorizzazione implicita per ottenere un token di ID (id_token) da Azure Active Directory. Non e il client allegarlo alla richiesta del token del titolare durante le chiamate alla relativa API Web back-end, è protetto tramite middleware OWIN. 
#### <a name="diagram"></a>Diagramma

![Diagramma applicazione pagina singola](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso di protocollo

1. L'utente si sposta all'applicazione web.


2. L'applicazione restituisce JavaScript front-end (livello di presentazione) nel browser.


3. L'utente esegue accesso, ad esempio facendo clic su un collegamento di accesso. Il browser invia GET all'endpoint autorizzazione Azure Active Directory per richiedere un token di ID. La richiesta include l'URL del client ID e la risposta nei parametri della query.


4. Azure Active Directory convalida l'URL di risposta con l'URL di risposta registrato configurati nel portale di gestione di Azure.


5. L'utente accede nella pagina di accesso.


6. Se l'autenticazione ha esito positivo, Azure Active Directory crea un token di ID e viene restituito come frammento di URL (#) a URL di risposta dell'applicazione. Per un'applicazione di produzione, l'URL di risposta deve essere HTTPS. Token restituito include reclami sull'utente e Azure Active Directory necessarie per l'applicazione per convalidare il token.


7. Il codice di client JavaScript in esecuzione nel browser estrae il token dalla risposta da utilizzare per proteggere le chiamate al web dell'applicazione che API indietro terminare.


8. Il browser web dell'applicazione API indietro terminare con il token di accesso nell'intestazione di autorizzazione delle chiamate.

#### <a name="code-samples"></a>Esempi di codice


Vedere gli esempi di codice per gli scenari singola pagina applicazione (SPA). Assicurarsi di controllare spesso - si aggiunge nuovi esempi sempre questa impostazione. [Applicazione della singola pagina (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>La registrazione


- Singolo Tenant: Se si sta creando un'applicazione solo per l'organizzazione, deve essere registrato nella directory dell'azienda tramite il portale di gestione di Azure.


- Multi-Tenant: Se si sta creando un'applicazione che può essere utilizzata dagli utenti all'esterno dell'organizzazione, devono essere registrato nella directory dell'azienda, ma anche devono essere registrato nella directory dell'organizzazione che effettuano l'accesso tramite l'applicazione. Per rendere disponibile nella directory di applicazione, è possibile includere un processo di iscrizione per i clienti che consente alle consenso all'applicazione. Quando si effettua l'iscrizione per l'applicazione, viene visualizzata una finestra di dialogo che mostra le autorizzazioni che dell'applicazione richiede e la possibilità di consenso. A seconda delle autorizzazioni necessarie, un amministratore all'interno dell'organizzazione potrebbe essere necessario fornire consenso dell'utente. Quando l'utente o amministratore consenso, l'applicazione è registrato nella directory di. Per ulteriori informazioni, vedere [Le applicazioni di integrazione con Azure Active Directory](active-directory-integrating-applications.md).

Dopo la registrazione dell'applicazione, questo deve essere configurato per l'utilizzo OAuth 2.0 impliciti Grant protocollo. Per impostazione predefinita, questo protocollo è disattivato per le applicazioni. Per abilitare il protocollo OAuth2 impliciti Grant per l'applicazione, scaricare il manifesto dell'applicazione dal portale di gestione di Azure, impostare il valore di "oauth2AllowImplicitFlow" true e quindi caricarlo indietro manifesto al portale. Per informazioni dettagliate, vedere [Attivazione di OAuth 2.0 impliciti Grant per singole applicazioni di pagina](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Scadenza token

Quando si utilizza ADAL.js per gestire l'autenticazione con Azure Active Directory, è trarre vantaggio da diverse caratteristiche che semplificano l'aggiornamento di un token scaduto come ottenere i token delle risorse API aggiuntivi relativi al web che possono essere chiamate dall'applicazione. Se l'utente viene autenticato correttamente con Azure Active Directory, per l'utente tra il browser e Azure Active Directory è stabilire una sessione protetta da un cookie. È importante tenere presente che la sessione esiste tra l'utente e Azure Active Directory e non tra l'utente e l'applicazione web con il server. Quando scade un token, ADAL.js utilizza questa sessione per ottenere automaticamente un altro token. Ciò utilizzando un iFrame nascosto per inviare e ricevere la richiesta utilizzando il protocollo Grant impliciti OAuth. ADAL.js inoltre possibile utilizzare lo stesso meccanismo per ottenere automaticamente i token di accesso da Azure Active Directory per altri web risorse API che l'applicazione chiama come queste risorse di supporto tecnico tra origine condivisore (CORS) è registrato nella directory dell'utente e qualsiasi consenso necessario è stato assegnato dall'utente durante l'accesso.


### <a name="native-application-to-web-api"></a>Applicazione nativa per dell'API Web


In questa sezione descrive un'applicazione nativa che chiama web API per conto di un utente. Questo scenario si basa sul tipo di concessione OAuth 2.0 autorizzazione codice con un client pubblico, come descritto nella sezione 4.1 della [specifica OAuth 2.0](http://tools.ietf.org/html/rfc6749). Applicazione nativa Ottiene un token di accesso per l'utente utilizzando il protocollo OAuth 2.0. Questo token di accesso viene inviato nella richiesta per il web API, che autorizza l'utente e restituisce la risorsa desiderata.

#### <a name="diagram"></a>Diagramma

![Applicazione nativa per Web API diagramma](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Flusso di autenticazione per applicazione nativa all'API

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso di protocollo


Se si usano le raccolte di autenticazione Active Directory, la maggior parte dei dettagli protocollo descritti di seguito vengono gestita, ad esempio la finestra popup del browser, memorizzazione nella cache token e gestione di token di aggiornamento.

1. Utilizzare un browser a comparsa che applicazione nativa effettua una richiesta all'endpoint autorizzazione in Azure Active Directory. La richiesta include l'ID client e il reindirizzamento URI dell'applicazione nativa, come illustrato nel portale di gestione e l'applicazione ID URI per il web API. Se l'utente non è già stato effettuato l'accesso, verrà richiesto di accedere di nuovo


2. Azure Active Directory autentica l'utente. Se si tratta di un'applicazione multi-tenant e consenso è necessario usare l'applicazione, l'utente verrà richiesto di confermare se è stata ancora eseguita. Dopo aver rilasciato l'autorizzazione e volta eseguita l'autenticazione, Azure Active Directory invia una risposta di codice di autorizzazione a URI di reindirizzamento dell'applicazione client.


3. Quando Azure Active Directory invia una risposta di codice di autorizzazione a URI di reindirizzamento, l'applicazione client si arresta interazione del browser ed estrae il codice di autorizzazione dalla risposta. Utilizzare questo codice di autorizzazione, l'applicazione client invia una richiesta all'endpoint token di Azure Active Directory che include il codice di autorizzazione, i dettagli relativi all'applicazione client (ID client e reindirizza URI) e la risorsa desiderata (applicazione ID URI per il web API).


4. Il codice di autorizzazione e informazioni sull'API web e applicazioni client vengono convalidati da Azure Active Directory. Al termine della convalida, Azure Active Directory restituisce due token: un token di accesso JWT e un token di aggiornamento JWT. Inoltre, Azure Active Directory restituisce le informazioni di base relative all'utente, ad esempio il suo visualizzazione nome e tenant ID.


5. Su HTTPS, l'applicazione client utilizza il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Titolare" nell'intestazione di autorizzazione della richiesta web API. API web convalida token JWT e se la convalida ha esito positivo, restituisce la risorsa desiderata.


6. Quando scade il token di accesso, l'applicazione client riceverà un messaggio di errore che indica che l'utente deve essere autenticato di nuovo. Se l'applicazione ha un token di aggiornamento valido, può essere utilizzato per acquisire un nuovo token di accesso senza chiedere conferma all'utente di accedere di nuovo. Se il token di aggiornamento scade, sarà necessario in modo interattivo autenticare nuovamente l'utente dell'applicazione.


> [AZURE.NOTE] Token aggiornamento rilasciato da Azure Active Directory può essere utilizzato per accedere più risorse. Ad esempio, se si dispone di un'applicazione client che dispone dell'autorizzazione per chiamare i due web API, il token di aggiornamento può essere utilizzato per ottenere un accesso token altri web API anche.


#### <a name="code-samples"></a>Esempi di codice


Vedere gli esempi di codice per un'applicazione nativa di scenari API Web. E controllare frequentemente - si aggiunge nuovi esempi sempre questa impostazione. [Applicazione nativa per dell'API Web](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>La registrazione


- Singolo Tenant: Entrambi nativo dell'applicazione e il web API devono essere registrati nella stessa directory in Azure Active Directory. API web può essere configurato per esporre un set di autorizzazioni, vengono utilizzate per limitare l'accesso alle risorse dell'applicazione nativa. L'applicazione client quindi seleziona le autorizzazioni desiderate dal menu a discesa "Autorizzazioni da altre applicazioni" nel portale di gestione di Azure.


- Multi-Tenant: Prima di tutto, l'applicazione nativa solo mai registrata lo sviluppatore o di una directory di publisher. In secondo luogo, l'applicazione nativa è configurato per indicare le autorizzazioni che necessarie per il corretto funzionamento. In questo elenco delle autorizzazioni necessarie viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione offre consenso all'applicazione, che rende disponibile all'organizzazione. Alcune applicazioni richiedono autorizzazioni solo a livello utente qualsiasi utente nell'organizzazione può consenso a. Altre applicazioni richiedono autorizzazioni a livello di amministratore, che non accetta un utente nell'organizzazione. Solo un amministratore di directory può concedere consenso dell'utente alle applicazioni che richiedono questo livello di autorizzazioni. Quando l'utente o amministratore consenso, solo l'API web è registrato nella directory di. Per ulteriori informazioni, vedere [Le applicazioni di integrazione con Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Scadenza token


Quando l'applicazione nativa utilizza il codice di autorizzazione per ottenere un accesso JWT token, riceve un token di aggiornamento JWT. Quando scade il token di accesso, è possibile utilizzare il token di aggiornamento eseguire nuovamente l'autenticazione utente senza dover accedere di nuovo. Questo aggiornamento che viene quindi utilizzato per eseguire l'autenticazione dell'utente, che genera un nuovo token di accesso e token di aggiornamento.





### <a name="web-application-to-web-api"></a>Applicazione Web dell'API Web


In questa sezione descrive un'applicazione web che è necessario ottenere risorse da un web API. In questo scenario, sono disponibili due tipi di identità che l'applicazione web consente di eseguire l'autenticazione e chiamare web API: identità di un'applicazione o un'identità utente delegato.

*Identità dell'applicazione:* Questo scenario utilizza concessione di credenziali client OAuth 2.0 per eseguire l'autenticazione dell'applicazione e accedere al web API. Quando si usa un'identità di applicazione web API possono solo rileva che l'applicazione web di chiamata, come web API non riceve le informazioni relative all'utente. Se l'applicazione riceve le informazioni relative all'utente, verrà inviato tramite il protocollo applicazione e non è stato firmato da Azure Active Directory. API web trust che l'applicazione web autenticato l'utente. Per questo motivo, questo modello è chiamato un sottosistema attendibile.

*Delegata identità dell'utente:* Questo scenario, è possibile procedere in due modi: OpenID connettersi e concedere il codice di autorizzazione OAuth 2.0 con un client riservato. L'applicazione web consente di ottenere un token di accesso dell'utente, si rivelino web API che l'utente autenticato all'applicazione web e che l'applicazione web è stato possibile ottenere un'identità utente delegato per chiamare API web. Questo token di accesso viene inviato la richiesta Web API, che autorizza l'utente e restituisce la risorsa desiderata.

#### <a name="diagram"></a>Diagramma

![Applicazione Web API Web diagramma](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Descrizione del flusso di protocollo

L'identità dell'applicazione e tipi di identità utente delegato sono illustrati in flusso riportata di seguito. La differenza tra di esse fondamentale è che l'identità dell'utente delegato prima di tutto necessario acquistare un codice di autorizzazione prima che l'utente può accesso e accedere a web API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identità dell'applicazione con Client OAuth 2.0 credenziali Grant

1. Un utente ha effettuato l'accesso a Azure Active Directory nell'applicazione web (vedere [Web Browser all'applicazione Web](#web-browser-to-web-application) sopra).


2. L'applicazione web è necessario acquisire un token di accesso in modo che possa eseguire l'autenticazione sul Web API e recuperare la risorsa desiderata. Effettua una richiesta all'endpoint token di Azure Active Directory, fornire le credenziali, ID client e applicazione web dell'API URI ID.


3. Azure Active Directory autentica l'applicazione e viene restituito un token di accesso JWT viene utilizzato per chiamare API web.


4. Su HTTPS, l'applicazione web utilizza il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Titolare" nell'intestazione di autorizzazione della richiesta API sul Web. API web convalida token JWT e se la convalida ha esito positivo, restituisce la risorsa desiderata.

##### <a name="delegated-user-identity-with-openid-connect"></a>Connettere identità utente delegato con OpenID

1. Un utente ha effettuato l'accesso a un'applicazione web utilizzando Azure Active Directory (vedere sezione [Browser all'applicazione Web](#web-browser-to-web-application) ). Se l'utente dell'applicazione web non ha ancora acconsentito per consentire l'applicazione web da chiamare web API per suo conto, sarà necessario consenso dell'utente. L'applicazione visualizzerà le autorizzazioni che necessarie, e se una di queste sono le autorizzazioni di amministratore a livello utente normale nella directory non saranno in grado di confermare. Questo processo di consenso si applica solo alle applicazioni multi-tenant, le applicazioni non singola tenant, come l'applicazione già dispone delle autorizzazioni necessarie. Quando l'utente connesso, l'applicazione web ha ricevuto un token di ID con le informazioni relative all'utente, un codice di autorizzazione.


2. Usa il codice di autorizzazione rilasciato da Azure Active Directory, l'applicazione web invia una richiesta all'endpoint token di Azure Active Directory che include il codice di autorizzazione, i dettagli relativi all'applicazione client (ID client e reindirizza URI) e la risorsa desiderata (applicazione ID URI per il web API).


3. Il codice di autorizzazione e le informazioni l'applicazione web e API web vengono convalidati da Azure Active Directory. Al termine della convalida, Azure Active Directory restituisce due token: un token di accesso JWT e un token di aggiornamento JWT.


4. Su HTTPS, l'applicazione web utilizza il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Titolare" nell'intestazione di autorizzazione della richiesta API sul Web. API web convalida token JWT e se la convalida ha esito positivo, restituisce la risorsa desiderata.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identità utente delegato con codice concedere l'autorizzazione di OAuth 2.0

1. Un utente è già stato effettuato l'accesso a un'applicazione web il cui meccanismo di autenticazione sia indipendente di Azure Active Directory.


2. L'applicazione web richiede un codice di autorizzazione per acquisire un token di accesso, in modo che invia una richiesta tramite il browser all'endpoint di autorizzazione di Azure Active Directory, fornire l'ID client e il reindirizzamento URI per l'applicazione web dopo l'autenticazione. L'utente accede a Azure Active Directory.


3. Se l'utente dell'applicazione web non ha ancora acconsentito per consentire l'applicazione web da chiamare web API per suo conto, sarà necessario consenso dell'utente. L'applicazione visualizzerà le autorizzazioni che necessarie, e se una di queste sono le autorizzazioni di amministratore a livello utente normale nella directory non saranno in grado di confermare. Questo processo di consenso si applica solo alle applicazioni multi-tenant, le applicazioni non singola tenant, come l'applicazione già dispone delle autorizzazioni necessarie.


4. Dopo l'utente ha acconsentito, l'applicazione web riceve il codice di autorizzazione che è necessario acquisire un token di accesso.


5. Usa il codice di autorizzazione rilasciato da Azure Active Directory, l'applicazione web invia una richiesta all'endpoint token di Azure Active Directory che include il codice di autorizzazione, i dettagli relativi all'applicazione client (ID client e reindirizza URI) e la risorsa desiderata (applicazione ID URI per il web API).


6. Il codice di autorizzazione e le informazioni l'applicazione web e API web vengono convalidati da Azure Active Directory. Al termine della convalida, Azure Active Directory restituisce due token: un token di accesso JWT e un token di aggiornamento JWT.


7. Su HTTPS, l'applicazione web utilizza il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Titolare" nell'intestazione di autorizzazione della richiesta API sul Web. API web convalida token JWT e se la convalida ha esito positivo, restituisce la risorsa desiderata.

#### <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per scenari API Web dell'applicazione Web. E controllare frequentemente - si aggiunge nuovi esempi sempre questa impostazione. Web [applicazione Web API](active-directory-code-samples.md#web-application-to-web-api).


#### <a name="registering"></a>La registrazione

- Singolo Tenant: Relative alle identità dell'applicazione e casi di identità utente delegato, l'applicazione web e l'API web devono essere registrati nella stessa directory nel Azure Active Directory. API web può essere configurato per esporre un set di autorizzazioni, vengono utilizzati per limitare l'accesso dell'applicazione web alle risorse. Se viene utilizzato un tipo di identità utente delegato, selezionare le autorizzazioni desiderate dal menu a discesa "Autorizzazioni da altre applicazioni" nel portale di gestione di Azure l'applicazione web. Questo passaggio non è necessario se il tipo di identità dell'applicazione in uso.


- Multi-Tenant: Prima di tutto, l'applicazione web è configurato per indicare le autorizzazioni che necessarie per il corretto funzionamento. In questo elenco delle autorizzazioni necessarie viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione offre consenso all'applicazione, che rende disponibile all'organizzazione. Alcune applicazioni richiedono autorizzazioni solo a livello utente qualsiasi utente nell'organizzazione può consenso a. Altre applicazioni richiedono autorizzazioni a livello di amministratore, che non accetta un utente nell'organizzazione. Solo un amministratore di directory può concedere consenso dell'utente alle applicazioni che richiedono questo livello di autorizzazioni. Quando l'utente o amministratore consenso, l'applicazione web e l'API web entrambe registrati nella directory di.

#### <a name="token-expiration"></a>Scadenza token

Quando l'applicazione web utilizza il codice di autorizzazione per ottenere un accesso JWT token, riceve un token di aggiornamento JWT. Quando scade il token di accesso, è possibile utilizzare il token di aggiornamento eseguire nuovamente l'autenticazione utente senza dover accedere di nuovo. Questo aggiornamento che viene quindi utilizzato per eseguire l'autenticazione dell'utente, che genera un nuovo token di accesso e token di aggiornamento.


### <a name="daemon-or-server-application-to-web-api"></a>Daemon o dell'applicazione Server dell'API Web


In questa sezione descrive un'applicazione daemon o server necessarie per ottenere le risorse da un web API. Esistono due scenari secondari da applicare a questa sezione: daemon che è necessario chiamare un web API basata su tipo di concessione credenziali client OAuth 2.0; e un'applicazione di server (ad esempio un web API) che è necessario chiamare un web API basata su specifica bozza OAuth 2.0 On-Behalf-Of.

Per lo scenario quando un'applicazione daemon deve chiamare un web API, è importante conoscere alcuni dettagli. Prima di tutto l'interazione dell'utente non è possibile con un'applicazione daemon che richiede l'applicazione per la propria identità. Esempio di un'applicazione daemon è un processo batch o un servizio di sistema operativo in esecuzione in background. Questo tipo di applicazione richiede un token di accesso utilizzando la propria identità dell'applicazione e presentare il relativo client ID, credenziali (password o certificato) e applicazione URI ID di Azure Active Directory. Dopo l'autenticazione, il daemon riceve un token di accesso da Azure Active Directory, che verrà utilizzato per chiamare API web.

Per lo scenario quando un'applicazione server deve chiamare un web API, è utile utilizzare un esempio. Si supponga che un utente autenticato in un'applicazione nativa e questa applicazione nativa deve chiamare un API web. Azure Active Directory emette un token di accesso JWT per chiamare API web. Se è l'API web necessario per chiamare un altro web downstream API, utilizzi il flusso per conto dell'utente a delegare l'identità dell'utente e sul Web di secondo livello API di autenticazione.

#### <a name="diagram"></a>Diagramma

![Daemon o applicazione Server API Web diagramma](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso di protocollo

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identità dell'applicazione con Client OAuth 2.0 credenziali Grant

1. Prima di tutto, l'applicazione server deve eseguire l'autenticazione con Azure Active Directory come stesso, senza alcuna interazione umano, ad esempio un accesso nella finestra di dialogo interattiva. Effettua una richiesta all'endpoint token di Azure Active Directory, fornire le credenziali, ID client e ID URI applicazione.


2. Azure Active Directory autentica l'applicazione e viene restituito un token di accesso JWT viene utilizzato per chiamare API web.


3. Su HTTPS, l'applicazione web utilizza il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Titolare" nell'intestazione di autorizzazione della richiesta API sul Web. API web convalida token JWT e se la convalida ha esito positivo, restituisce la risorsa desiderata.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identità utente delegato con OAuth 2.0 per conto di versione preliminare della specifica

Il flusso descritto di seguito si presuppone che un utente autenticato in un'altra applicazione (ad esempio un'applicazione nativa) e la propria identità utente è stato utilizzato per acquisire un token di accesso al web di primo livello API.

1. Applicazione nativa invia il token di accesso al web di primo livello API.


2. API web di primo livello invia una richiesta all'endpoint token di Azure Active Directory, fornendo il relativo client ID e le credenziali, nonché token di accesso dell'utente. Inoltre, viene inviata la richiesta con un on_behalf_of parametro che indica il web API richiede nuovi token per chiamare un web downstream API per conto dell'utente originale.


3. Azure Active Directory verifica che sul web di primo livello API disponga delle autorizzazioni per accedere al web di secondo livello API e convalida la richiesta restituendo un token di accesso JWT e un JWT aggiornare token sul Web di primo livello API.


4. Su HTTPS, l'API web di primo livello chiama web di secondo livello API aggiungendo la stringa del token nell'intestazione di autorizzazione nella richiesta. Web di primo livello API è possibile continuare a chiamare il web di secondo livello API, purché i token di accesso e i token di aggiornamento sono validi.

#### <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per Daemon o dell'applicazione Server scenari API Web. E controllare frequentemente - si aggiunge nuovi esempi sempre questa impostazione. [Server o Daemon applicazione Web API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>La registrazione

- Singola Tenant: Relative alle identità dell'applicazione e casi di identità utente delegato, il server o daemon applicazione deve essere registrata nella stessa directory in Azure Active Directory. API web può essere configurato per esporre un set di autorizzazioni, vengono utilizzate per limitare l'accesso alle risorse del server o il daemon. Se viene utilizzato un tipo di identità utente delegato, selezionare le autorizzazioni desiderate dal menu a discesa "Autorizzazioni da altre applicazioni" nel portale di gestione di Azure l'applicazione server. Questo passaggio non è necessario se il tipo di identità dell'applicazione in uso.


- Multi-Tenant: Prima di tutto, l'applicazione daemon o il server è configurato per indicare le autorizzazioni che necessarie per il corretto funzionamento. In questo elenco delle autorizzazioni necessarie viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione offre consenso all'applicazione, che rende disponibile all'organizzazione. Alcune applicazioni richiedono autorizzazioni solo a livello utente qualsiasi utente nell'organizzazione può consenso a. Altre applicazioni richiedono autorizzazioni a livello di amministratore, che non accetta un utente nell'organizzazione. Solo un amministratore di directory può concedere consenso dell'utente alle applicazioni che richiedono questo livello di autorizzazioni. Quando l'utente o amministratore consenso, entrambe le API web registrato nella directory di.

#### <a name="token-expiration"></a>Scadenza token

Quando l'applicazione prima utilizza il codice di autorizzazione per ottenere un accesso JWT token, riceve un token di aggiornamento JWT. Quando scade il token di accesso, è possibile utilizzare il token di aggiornamento eseguire nuovamente l'autenticazione utente senza che richiede le credenziali. Questo aggiornamento che viene quindi utilizzato per eseguire l'autenticazione dell'utente, che genera un nuovo token di accesso e token di aggiornamento.

## <a name="see-also"></a>Vedere anche

[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Esempi di codice di Azure Active Directory](active-directory-code-samples.md)

[Informazioni sull'accesso chiave attivazione di Azure Active Directory](active-directory-signing-key-rollover.md)

[OAuth 2.0 in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx)
