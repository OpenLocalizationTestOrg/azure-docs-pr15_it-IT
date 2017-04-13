<properties
    pageTitle="restrizioni e limitazioni relative all'Endpoint v 2.0 | Microsoft Azure"
    description="Elenco delle limitazioni e restrizioni con l'endpoint v 2.0 Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>È consigliabile utilizzare il punto finale v 2.0?

Quando si creano applicazioni che si integrano con Azure Active Directory, è necessario decidere se i protocolli di autenticazione ed endpoint v 2.0 soddisfano le proprie esigenze.  L'endpoint di Azure Active Directory originale ancora supportata e in alcuni casi è più ricca di funzionalità della versione 2.0.  Tuttavia, la versione 2.0 endpoint [presenta vantaggi significativi](active-directory-v2-compare.md) per gli sviluppatori che può indurre è possibile utilizzare il nuovo modello di programmazione.

In questo momento, sull'uso dell'endpoint v 2.0 è come indicato di seguito:

- Se si vuole supporta gli account Microsoft personali nell'applicazione, è necessario utilizzare l'endpoint v 2.0.  Ma assicurarsi di comprendere le limitazioni elencate in questo articolo, specialmente quelle relative specificamente per lavorare & account dell'istituto di istruzione.
- Se l'applicazione richiede solo account dell'istituto di istruzione & lavoro supporto, è necessario utilizzare [i punti finali Azure Active Directory originali](active-directory-developers-guide.md).

Nel tempo, l'endpoint v 2.0 crescerà per eliminare le restrizioni elencate di seguito, in modo che solo mai è necessario usare l'endpoint v 2.0.  Nel frattempo, in questo articolo è rivolto per determinare se è l'endpoint v 2.0.  Si continuerà ad aggiornare in questo articolo nel tempo per riflettere lo stato corrente dell'endpoint v 2.0, pertanto selezionare indietro per rivalutata ai propri requisiti di rispetto alle funzionalità v 2.0.

Se si dispone di un'app esistente con Azure Active Directory che non usa l'endpoint v 2.0, è necessario iniziare da zero.  In futuro, si fornirà un modo per attivare le applicazioni di Azure Active Directory esistente per l'utilizzo con l'endpoint v 2.0.

## <a name="restrictions-on-apps"></a>Limitazioni relative App
I tipi seguenti di App non sono attualmente supportati da endpoint v 2.0.  Per una descrizione dei tipi di supportati delle App, fare riferimento al [presente articolo](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>API Web autonomo
Con endpoint v 2.0 avere la possibilità di [creare un API Web che è protetto tramite OAuth 2.0](active-directory-v2-flows.md#web-apis).  Tuttavia, l'API Web solo saranno in grado di ricevere i token da un'applicazione che condivide lo stesso ID di applicazione.  Creazione di un web API accessibili da un client con un Id diverso applicazione non è supportata.  Il client non sarà possibile richiedere o ottenere le autorizzazioni per l'API web.

Per informazioni su come creare un API Web che accetta i token da un client con lo stesso Id di App, vedere gli esempi di API Web v 2.0 endpoint nella [Guida introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

##### <a name="web-api-on-behalf-of-flow"></a>API Web per conto di flusso
Molte architetture includano un'API Web che è necessario chiamare un'altra API Web downstream sia protetto da endpoint v 2.0.  Questo scenario è comune nei client nativi che hanno un back-end API Web, che a sua volta chiama un servizio online Microsoft o un altro personalizzate dell'API web che supporta Azure Active Directory.

Questo scenario può essere supportato utilizzando la concessione delle credenziali di titolare OAuth 2.0 Jwt, nota anche come flusso di On-Behalf-Of.  Tuttavia, via per conto di flusso non è attualmente supportato per l'endpoint v 2.0.  Per vedere come funziona il questo flusso in Azure AD generalmente disponibile service, vedere l' [esempio di codice per conto di GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrizioni alle registrazioni di app
In questo momento, tutte le app che desidera integrare con l'endpoint v 2.0 necessario creare una nuova registrazione app [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Qualsiasi Azure AD esistente o App Account Microsoft non saranno compatibili con l'endpoint v 2.0 e non verranno App registrato in qualsiasi portale oltre il nuovo portale di registrazione di App.  Si prevede di fornire un modo per abilitare le applicazioni esistenti da usare come app v 2.0. In questo momento tuttavia, non è un percorso di migrazione per un'app all'endpoint v 2.0.

Analogamente, App registrato nel portale di registrazione App nuovo non funzionerà con l'endpoint di autenticazione di Azure Active Directory originale.  È possibile, tuttavia, utilizzare App create nel portale di registrazione di App per integrare correttamente con l'endpoint di autenticazione account Microsoft, `https://login.live.com`.

Inoltre, le registrazioni app create in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) è associato avvertenze seguenti:

- La proprietà **home page** , noto anche come l' **URL di accesso** non è supportata.  Senza una home page, queste applicazioni non verranno visualizzate nel riquadro App personali di Office.
- Informazioni riservate app solo due sono consentiti per ogni applicazione Id al momento.
- Solo una registrazione app può essere visualizzata e gestita da un account di sviluppo singola.  Non può essere condiviso tra più sviluppatori.
- Esistono diverse restrizioni sul formato di redirect_uri consentiti.  Vedere la sezione seguente per altri dettagli.

## <a name="restrictions-on-redirect-uris"></a>Limitazioni relative URI di reindirizzamento
App registrati nel portale di registrazione dell'applicazione nuovo sono limitate a un numero limitato di valori redirect_uri.  Redirect_uri per i servizi e applicazioni web devono iniziare con la combinazione di `https`, e tutti i valori di redirect_uri necessario condividere un unico dominio DNS.  Non è ad esempio possibile registrare un'app web che contiene redirect_uris:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Il sistema di registrazione Confronta l'intero nome DNS di redirect_uri esistente con il nome DNS del redirect_uri che si sta aggiungendo. La richiesta per aggiungere il nome DNS non riesce se vengono soddisfatte le condizioni seguenti:  

- Se l'intero nome DNS del nuovo redirect_uri non corrisponde al nome DNS del redirect_uri esistente
- Se l'intero nome DNS del nuovo redirect_uri non è un sottodominio di redirect_uri esistente

Ad esempio, se l'app è già stato redirect_uri:

`https://login.contoso.com`

È possibile aggiungere:

`https://login.contoso.com/new`

che corrisponde esattamente al nome DNS o:

`https://new.login.contoso.com`

è un sottodominio DNS del login.contoso.com.  Se si desidera ottenere un'app che ha accesso east.contoso.com e accesso west.contoso.com come redirect_uris, sarà necessario aggiungere redirect_uris seguenti nell'ordine:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Le ultime due possono essere aggiunti perché sono sottodomini del primo redirect_uri, contoso.com. Questa limitazione verrà rimosso in una versione futura.

Per informazioni su come registrare un'app nel portale di registrazione dell'applicazione di nuovo, fare riferimento al [presente articolo](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Limitazioni relative API e servizi
Endpoint v 2.0 attualmente supporta di accesso di per qualsiasi app registrato nel portale di registrazione di applicazione di nuovo, purché si trova nell'elenco dei [flussi di autenticazione supportati](active-directory-v2-flows.md).  Tuttavia, queste App solo saranno in grado di acquisire token di accesso OAuth 2.0 per un numero molto limitato di risorse.  Endpoint v 2.0 impostandoli verranno access_tokens per:

- App che ha richiesto il token.  Un'app può acquisire un Access per se stesso, se l'app logico è composto da vari componenti diversi o livelli.  Per visualizzare questo scenario in azione, consultare le esercitazioni [Introduzione](active-directory-appmodel-v2-overview.md#getting-started) .
- Posta di Outlook, calendario e contatti resto API, ognuno dei quali si trovano in https://outlook.office.com.  Per informazioni su come scrivere un'applicazione che accede a queste API, fare riferimento a queste esercitazioni [Introduzione a Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- API di Microsoft Graph.  Per informazioni su Microsoft Graph e tutti i dati che sono disponibili, visitare [https://graph.microsoft.io](https://graph.microsoft.io).

Nessun altro servizio è supportato in questa fase.  Altri servizi Online Microsoft verranno aggiunti in futuro, oltre al supporto per il proprio personalizzato API Web predefiniti e servizi.

## <a name="restrictions-on-libraries--sdks"></a>Limitazioni relative raccolte e SDK
Il supporto di una raccolta per l'endpoint v 2.0 è abbastanza limitato al momento.  Se si desidera utilizzare l'endpoint v 2.0 in un'applicazione di produzione, sono disponibili le opzioni seguenti:

- Se si sta creando un'applicazione web, è possibile utilizzare in modo sicuro il nostro generalmente disponibile middleware sul lato server per eseguire l'accesso e token di convalida.  Sono inclusi middleware OWIN Apri ID connettersi per ASP.NET e il plug-in di NodeJS Passport.  Esempi di codice utilizzando il nostro middleware sono disponibili nella sezione [Guida introduttiva](active-directory-appmodel-v2-overview.md#getting-started) anche.
- Per altre piattaforme e per le applicazioni native e dispositivi mobili, è possibile integrare anche con l'endpoint v 2.0 per l'invio e ricezione di messaggi del protocollo nel codice dell'applicazione direttamente.  V 2.0 OpenID connettersi e OAuth protocolli [documentare in modo esplicito](active-directory-v2-protocols.md) consentono di eseguire tali un'integrazione.
- Infine, è possibile utilizzare le raccolte di aprire connettere ID e OAuth Apri origine per l'integrazione con l'endpoint v 2.0.  Il protocollo v 2.0 deve essere compatibile con molte librerie di protocollo Apri origine senza principali modifiche apportate.  La disponibilità di tali librerie varia per ogni lingua e la piattaforma e i siti Web [Aperto ID connettersi](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) gestire un elenco di implementazioni più comuni. Per ulteriori informazioni e l'elenco di raccolte client Apri origine ed esempi che sono stati testati con l'endpoint v 2.0, vedere [raccolte v 2.0 e l'autenticazione di Azure Active Directory (AD)](active-directory-v2-libraries.md) .

È stato inoltre reso disponibile un'anteprima iniziale di [Una raccolta di autenticazione Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) per .NET solo.  Sono invitati a provare questa raccolta nelle applicazioni client e server .NET, ma come una raccolta di anteprima che verrà non essere accompagnato da GA qualità di supporto.

## <a name="restrictions-on-protocols"></a>Restrizioni per i protocolli
Endpoint v 2.0 supporta solo connettersi ID Apri & OAuth 2.0.  Tuttavia, non tutte le caratteristiche e le funzionalità di ogni protocollo sono state integrate endpoint v 2.0, tra cui:

- La connessione di OpenID `end_session_endpoint`, che consente di un'app terminare la sessione dell'utente con l'endpoint v 2.0.
- id_tokens rilasciato da endpoint v 2.0 contenere solo un identificatore coppie per l'utente.  Questo significa che due diverse applicazioni riceverà un ID diverso per lo stesso nome utente.  Si noti che eseguendo una query di Microsoft Graph `/me` endpoint, sarà possibile ottenere un ID correlatable per l'utente che può essere utilizzato per le applicazioni.
- id_tokens rilasciato da endpoint v 2.0 non contengono un `email` richiedere per l'utente al momento, anche se si acquista autorizzazione da parte dell'utente per visualizzare la posta elettronica.
- Endpoint OpenID connettersi utente Info. L'endpoint di informazioni utente non è implementata in endpoint v 2.0 al momento.  Tuttavia, tutti i dati del profilo utente, si ricevono potenzialmente per l'endpoint è disponibile da Microsoft Graph `/me` endpoint.
- Ruoli e delle attestazioni di gruppo.  Al momento l'endpoint v 2.0 non supporta emissione sulle attestazioni ruolo o un gruppo in id_tokens.

Per comprendere meglio l'ambito della funzionalità di protocollo supportate in endpoint v 2.0, leggere il nostro [OpenID Connetti & OAuth 2.0 protocollo riferimento](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Limitazioni per l'account aziendale e dell'istituto di istruzione
Esistono alcune caratteristiche specifiche per gli utenti dell'organizzazione/business Microsoft che non sono ancora supportati da endpoint v 2.0.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Accesso condizionale basato sul dispositivo, App per dispositivi mobili e nativi e Microsoft Graph
Endpoint v 2.0 non supporta ancora l'autenticazione di dispositivo per applicazioni per dispositivi mobili e native, ad esempio App nativi in esecuzione su iOS o Android.  Questo potrebbe bloccare applicazione nativa di chiamare Microsoft Graph per alcune organizzazioni.  Autenticazione dispositivo è necessaria quando un amministratore imposta un criterio di accesso condizionale basate su dispositivo su un'applicazione.  Per l'endpoint v 2.0 lo scenario più probabile per l'accesso condizionale basato sul dispositivo è un amministratore impostazione di criteri a una risorsa in Microsoft Graph, ad esempio l'API di Outlook.  Se un amministratore imposta questo criterio e applicazione nativa richiede un token a Microsoft Graph, la richiesta infine riuscirà perché l'autenticazione dei dispositivi non è ancora supportato.  Le applicazioni Web che richiedono token da Microsoft Graph, tuttavia, sono supportate quando sono configurati i criteri basati sul dispositivo.  In web app scenario dispositivo autenticazione tramite browser dell'utente.

Gli sviluppatori, è probabile che non si dispone di alcun controllo su quando i criteri siano impostati risorse di Microsoft Graph, o persino individuato quando si verifica.  Se si sta creando un'applicazione per gli utenti aziendale e dell'istituto di istruzione, è necessario utilizzare [l'endpoint di Azure Active Directory originale](active-directory-developers-guide.md) finché l'endpoint v 2.0 supporta l'autenticazione dei dispositivi.  Per ulteriori informazioni sull'accesso condizionale basato sul dispositivo, consultare [questo articolo](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticazione di Windows per tenant federati
Se sono state usate ADAL (e pertanto l'endpoint di Azure Active Directory originale) nelle applicazioni di Windows, è possibile che sia di trarre vantaggio da quello che viene definito come la concessione di asserzione SAML.  Assegnazione consente agli utenti di Azure AD federati tenant autenticarsi automaticamente con l'istanza di Active Directory locale senza dover immettere le credenziali.  La concessione di asserzione SAML non è attualmente supportata per l'endpoint v 2.0.
