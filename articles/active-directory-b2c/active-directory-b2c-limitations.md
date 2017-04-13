<properties
    pageTitle="Azure B2C Directory attiva: Restrizioni e limitazioni | Microsoft Azure"
    description="Un elenco di limiti e le restrizioni con Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure B2C Directory attiva: Restrizioni e limitazioni

Sono disponibili diverse caratteristiche e funzionalità di Azure Active Directory (Azure Active Directory) B2C che non sono ancora supportate. Molti di questi problemi noti e limitazioni verrà risolto in futuro, ma dovrebbe essere conoscenza se si creano applicazioni consumer esposto tramite B2C di Azure Active Directory.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemi durante la creazione di Azure Active Directory B2C tenant

Se si verificano problemi durante la [creazione di un tenant di Azure Active Directory B2C](active-directory-b2c-get-started.md), vedere [creare un tenant di Azure Active Directory o un tenant di Azure Active Directory B2C - problemi e soluzioni](active-directory-b2c-support-create-directory.md) per indicazioni.

Si noti che esistono noti problemi quando si elimina un tenant B2C esistente e ricrearlo con lo stesso nome di dominio. È necessario creare un tenant B2C con un nome di dominio diverso.

## <a name="note-about-b2c-tenant-quotas"></a>Nota sulle quote tenant B2C

Per impostazione predefinita, il numero di utenti in un tenant B2C è limitato a 50.000 utenti. Se è necessario generare la quota di tenant B2C, è necessario contattare il supporto.

## <a name="branding-issues-on-verification-email"></a>Problemi di personalizzazione nel messaggio di verifica

Il messaggio di verifica predefinito contiene personalizzazioni Microsoft. Si verrà rimosso in futuro. Per ora, è possibile rimuoverlo utilizzando la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Restrizioni sulle applicazioni

I tipi di applicazioni seguenti non sono attualmente supportati in Azure Active Directory B2C. Per una descrizione dei tipi di applicazioni supportati, vedere [Azure Active Directory B2C: tipi di applicazioni](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Applicazioni singola pagina (JavaScript)

Molte applicazioni moderne dispone di una singola pagina applicazione (SPA) front-end che scritte principalmente in JavaScript e spesso viene utilizzata una struttura SPA, ad esempio AngularJS, Ember.js, Durandal e così via. Questo flusso non sono ancora disponibile in Azure Active Directory B2C.

### <a name="daemons--server-side-applications"></a>Daemon / lato server applicazioni

Applicazioni che contengono i processi di esecuzione prolungata o che funzionare senza la presenza di un utente necessitano anche un modo per accedere alle risorse protette, ad esempio API Web. Queste applicazioni di eseguire l'autenticazione e ottenere i token utilizzando l'identità dell'applicazione, anziché identità delegata del consumatore, nel [flusso di credenziali client OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Questo flusso non è ancora disponibile in Azure Active Directory B2C, in modo che per il momento le applicazioni possono ottenere i token solo dopo che si è verificato un flusso di accesso consumer interattivi.

### <a name="standalone-web-apis"></a>API Web autonomo

In B2C Active Directory Azure, avere la possibilità di [creare un API Web che vengono protetti mediante i token OAuth 2.0](active-directory-b2c-apps.md#web-apis). Tuttavia, l'API Web solo saranno in grado di ricevere i token da un client che condivide l'applicazione stesso ID. Creazione di un'API Web che è possibile accedere da più client differenti non è supportata.

### <a name="web-api-chains-on-behalf-of"></a>Web catene API (per conto dell'utente)

Molte architetture includano un'API Web che è necessario chiamare un'altra API Web downstream sia protetto da Azure Active Directory B2C. Questo scenario è comune nei client nativi che hanno un API Web back-end, che a sua volta chiama un servizio online Microsoft come l'API di Azure Active Directory grafico.

Questo scenario API Web concatenato può essere supportato utilizzando la concessione delle credenziali di titolare OAuth 2.0 Jwt, nota anche come via per conto di flusso. Tuttavia, via per conto di flusso non è attualmente implementato in B2C di Active Directory Azure.

## <a name="restriction-on-libraries-and-sdks"></a>Restrizione sulle librerie e SDK

Il set di raccolte in Microsoft supportati compatibili Azure Active Directory B2C è limitato al momento. Si sono supportati in .NET basato su web App e servizi, nonché NodeJS web App e servizi.  Esiste anche una raccolta di client preview .NET nota come MSAL che possono essere usate con Azure Active Directory B2C in Windows e altre applicazioni di .NET.

Si dispone di raccolta supporta tutte le altre lingue o piattaforme, tra cui iOS e Android.  Se si desidera una piattaforma diversi da quelli indicati in precedenza, è consigliabile utilizzare un SDK Apri origine, fare riferimento a [OAuth 2.0 e OpenID connettersi protocollo riferimento](active-directory-b2c-reference-protocols.md) in base alle esigenze.  Azure Active Directory B2C implementata OAuth & OpenID connessione, che consente di utilizzare una raccolta OAuth o connettersi OpenID generica per l'integrazione.

Il nostro iOS e Android introduttive esercitazioni utilizzano raccolte Apri origine che è stato verificato per la compatibilità con Azure Active Directory B2C.  Tutte le esercitazioni di avvio rapido sono disponibili nella sezione [Guida introduttiva](active-directory-b2c-overview.md#getting-started) .

## <a name="restriction-on-protocols"></a>Restrizione su protocolli

Azure Active Directory B2C supporta OpenID connettersi e OAuth 2.0. Tuttavia, non tutte le caratteristiche e le funzionalità di ogni protocollo implementazione. Per comprendere meglio l'ambito della funzionalità di protocollo supportato in Azure Active Directory B2C, leggere il nostro [OpenID connettersi e riferimento protocollo OAuth 2.0](active-directory-b2c-reference-protocols.md). Supporto del protocollo SAML e WS Fed non è disponibile.

## <a name="restriction-on-tokens"></a>Restrizioni sui token

Molti dei token emessi Azure Active Directory B2C implementate come JSON Web token o JWTs. Tuttavia, non tutte le informazioni contenute in JWTs (noto come "attestazioni") sono molto dovrebbe essere o manca. Alcuni esempi includono "sub" e le richieste di "preferred_username".  Come valori, formato o significato di modifica delle attestazioni nel tempo, i token per i criteri esistenti rimarranno invariati, è possibile utilizzare i rispettivi valori nelle applicazioni di produzione.  Quando cambiano i valori, si fornirà l'opportunità di configurare queste modifiche per ognuna dei criteri.  Per comprendere meglio i token emessi attualmente dal servizio di Azure Active Directory B2C, leggere il [riferimento token](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Limitazione dei gruppi nidificati

Appartenenze annidate non sono supportate in Azure Active Directory B2C tenant. Non si intende aggiungere questa funzionalità.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restrizioni sulla funzionalità di query differenziale sull'API di Azure Active Directory grafico

La [caratteristica query differenziale API di Azure Active Directory grafico](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) non è supportata in Azure Active Directory B2C tenant. Verrà visualizzata nella Guida di orientamento al nostro a lungo termine.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemi con la gestione degli utenti nel portale di classica Azure

B2C caratteristiche sono accessibili nel portale di Azure. Tuttavia, è possibile usare il portale classico Azure per accedere ad altre funzionalità tenant, compresa la gestione. Attualmente esistono un paio di problemi noti di gestione degli utenti (scheda **utenti** ) nel portale di classica Azure:

- Per un utente con account locali (ad esempio, un utente si iscrive a un indirizzo di posta elettronica e password, o un nome utente e password), il campo **Nome utente** non corrisponde all'accesso identificatore (indirizzo di posta elettronica o il nome utente) utilizzato durante l'iscrizione. In questo modo il campo visualizzato nel portale di classica Azure è effettivamente l'identità UPN (nome utente), che non è utilizzata in scenari B2C. Per visualizzare l'identificatore di accesso dell'account locale, trovare l'oggetto utente in [Esplora grafico](https://graphexplorer.cloudapp.net/). Si possono trovare lo stesso problema con un account di social networking utente (ad esempio, un utente si iscrive a Facebook, Google + e così via), ma in tal caso, non c'è alcun identificatore di accesso per parlare di.

    ![Account locali - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Per un utente di account locale, sarà necessario non è possibile modificare i campi e salvare le modifiche nella scheda **profilo** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemi di amministrazione avviata la reimpostazione della password nel portale di classica Azure

Se si reimposta la password per un consumer basato su account locale nel portale di classica Azure (comando **Reimposta Password** nella scheda **utenti** ), che il controllo consumer non saranno in grado di modificare la password di accesso successivo, se si utilizza un segno di o criteri, l'accesso e deve essere bloccato le applicazioni. In alternativa, utilizzare l' [API di Azure Active Directory grafico](active-directory-b2c-devquickstarts-graph-dotnet.md) per reimpostare la password dell'utente (senza scadenza delle password) o utilizzare un segno di criteri anziché un segno effettuarlo criteri.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemi con la creazione di un attributo personalizzato

Un [attributo personalizzato aggiunto nel portale di Azure](active-directory-b2c-reference-custom-attr.md) non viene immediatamente creata nel tenant di B2C. È necessario utilizzare l'attributo personalizzato in almeno uno dei criteri per renderla vengano creati nel tenant di B2C e diventano disponibili tramite API di grafico.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemi relativi alla verifica di un dominio nel portale di classica Azure

Attualmente non è possibile verificare un dominio correttamente nel [portale classica Azure](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemi con l'accesso con i criteri di MFA nel browser Safari

Richieste di criteri di accesso (con MFA attivato) fail intermittente nel browser Safari con errori HTTP 400 (richiesta non valida). Verrà visualizzata la scadenza limiti di dimensioni cookie bassa di Safari. Esistono un paio di soluzioni alternative per risolvere il problema:

- Utilizzare il "per l'abbonamento o accesso criterio" anziché "Criteri accesso".
- Ridurre il numero dei **reclami applicazione** richiesto nei criteri di.
