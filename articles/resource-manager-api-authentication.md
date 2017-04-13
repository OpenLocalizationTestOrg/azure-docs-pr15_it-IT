<properties 
   pageTitle="Autenticazione di Active Directory e Gestione risorse | Microsoft Azure"
   description="Guida per sviluppatori per l'autenticazione con l'API di gestione risorse di Azure e Active Directory per l'integrazione di un'app con gli altri abbonamenti Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Come usare Azure Active Directory e Manager delle risorse per gestire le risorse del cliente

## <a name="introduction"></a>Introduzione

Se si è uno sviluppatore software che è necessario creare un'applicazione che gestisce le risorse di Azure del cliente, in questo argomento viene illustrato come eseguire l'autenticazione con l'API di gestione risorse Azure e accedere alle risorse in altri abbonamenti. 

L'app è possibile accedere API Manager delle risorse in due modi:

1. **Ogni utente + app accedere**: per le applicazioni che accedono alle risorse per conto di un utente effettuato l'accesso. Questo approccio funziona per le applicazioni, ad esempio web apps e gli strumenti della riga di comando, che gestiscono solo "gestione interattivi" di Azure risorse.
1. **Accesso solo app**: per le applicazioni che eseguono servizi daemon e programmate. Identità dell'applicazione è concesso l'accesso diretto alle risorse. Questo approccio funziona per le applicazioni che accedono a lungo termine "offline" in Azure.

In questo argomento fornisce istruzioni dettagliate per creare un'applicazione che utilizza entrambi i metodi di autorizzazione. Viene illustrato come eseguire ogni passaggio con API REST o c#. Applicazione di Asp.net completamento è disponibile in [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Tutto il codice per questo argomento è in esecuzione come un'applicazione web che è possibile provare a [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>Qual è il web app

L'applicazione web:

1. Segni di un utente di Azure.
2. Richiede utente per concedere l'accesso di app web di Manager delle risorse.
3. Ottiene utente + token di accesso di app per l'accesso a Gestione risorse.
4. Utilizza token (dal passaggio 3) per chiamare Manager delle risorse e assegnare un ruolo nella sottoscrizione, che consente l'accesso a lungo termine app all'abbonamento principale del servizio dell'applicazione.
5. Ottiene token di accesso solo app.
6. Utilizza token (dal passaggio 5) per gestire le risorse nella sottoscrizione tramite Gestione risorse.

Ecco il flusso di fine-fine dell'applicazione web.

![Flusso di autenticazione Manager delle risorse](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Come utente, specificare l'id di abbonamento per l'abbonamento a cui che si desidera utilizzare:

![fornire l'id di abbonamento](./media/resource-manager-api-authentication/sample-ux-1.png)

Selezionare l'account da utilizzare per l'accesso.

![Selezionare account](./media/resource-manager-api-authentication/sample-ux-2.png)

Specificare le credenziali.

![specificare le credenziali](./media/resource-manager-api-authentication/sample-ux-3.png)

Concedere l'accesso di app per le proprie sottoscrizioni Azure:
 
![Concedere l'accesso](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Gestire le sottoscrizioni connesse:

![Connettere l'abbonamento](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Registrare l'applicazione

Prima di iniziare la codifica, registrare l'applicazione web con Azure Active Directory (AD). La registrazione di app crea un'identità centrale per l'app in Azure Active Directory. Contiene informazioni di base sull'applicazione come ID Client OAuth, URL di risposta e le credenziali utilizzate dall'applicazione di eseguire l'autenticazione e accedere a API di Azure Manager delle risorse. La registrazione di app registra anche le varie autorizzazioni delegate dell'applicazione quando si accede a Microsoft APIs per conto dell'utente. 

Poiché l'app accede gli altri abbonamenti, è necessario configurare come applicazione multi-tenant. Per passare la convalida, fornire un dominio associato di Active Directory. Per visualizzare i domini associati di Active Directory, accedere al [portale classica](https://manage.windowsazure.com). Selezionare di Active Directory e quindi selezionare **domini**.

Nell'esempio seguente viene illustrato come registrare l'app tramite PowerShell Azure. È necessario disporre l'ultima versione (agosto 2016) di Azure PowerShell per questo comando per l'uso. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
Per eseguire l'accesso come l'applicazione di Active Directory, è necessario l'id dell'applicazione e la password. Per visualizzare l'id dell'applicazione restituito dal comando precedente, usare:

    $app.ApplicationId

Nell'esempio seguente viene illustrato come registrare l'app tramite CLI Azure. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

I risultati includono AppId, è necessario per l'autenticazione dell'applicazione.

### <a name="optional-configuration---certificate-credential"></a>Configurazione facoltativa - credenziali certificato

Azure Active Directory supporta anche le credenziali del certificato per le applicazioni: creare un certificato autofirmato, mantenere la chiave privata e aggiungere la chiave pubblica per la registrazione di applicazione di Azure Active Directory. Per l'autenticazione, l'applicazione invia un piccolo payload di Azure Active Directory firmato con la chiave privata e Azure Active Directory convalida la firma mediante la chiave pubblica è registrato.

Per informazioni sulla creazione di un'app di Active Directory con un certificato, vedere [Usare PowerShell Azure per creare un servizio principale per accedere alle risorse](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) o [Uso Azure CLI per creare un servizio principale per accedere alle risorse](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>È possibile ottenere id tenant dall'id abbonamento

Per richiedere un token che può essere utilizzato per chiamare Manager delle risorse, è necessario indicare l'ID tenant del tenant di Azure Active Directory che ospita la sottoscrizione Azure l'applicazione. È probabile che gli utenti conoscono loro ID sottoscrizione, ma potrebbero non sanno tenant di ID per Active Directory. Per ottenere l'id dell'utente tenant, chiedere all'utente per l'id di sottoscrizione. Quando si invia una richiesta sulla sottoscrizione, specificare tale id abbonamento:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

La richiesta non riesce perché l'utente ha non ancora connessi, ma è possibile recuperare l'id tenant dalla risposta. In tale eccezione recuperare l'id tenant rispetto al valore di intestazione di risposta per **Eseguire l'autenticazione WWW**. Viene visualizzato l'implementazione del metodo [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Iniziare ogni utente + token di accesso di app

L'applicazione reindirizza l'utente di Azure Active Directory con una OAuth 2.0 autorizzare richiesta - per eseguire l'autenticazione le credenziali dell'utente e ottenere un codice di autorizzazione. L'applicazione utilizza il codice di autorizzazione per ottenere un accesso token per Manager delle risorse. Il metodo [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) crea la richiesta di autorizzazione.

Questo argomento illustra le richieste API REST di autenticazione dell'utente. È anche possibile utilizzare librerie di supporto per eseguire l'autenticazione nel codice. Per ulteriori informazioni su queste raccolte, vedere [Le raccolte di Azure Active Directory l'autenticazione](./active-directory/active-directory-authentication-libraries.md). Per indicazioni sull'integrazione di gestione delle identità in un'applicazione, vedere [Guida per gli sviluppatori di Azure Active Directory](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Richiesta di autenticazione (OAuth 2.0)

Inviare un'Apri ID Connetti/OAuth2.0 autorizzare richiesta per l'endpoint di Azure Active Directory autorizzazione:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Parametri della stringa di query sono disponibili per la richiesta sono descritti nell'argomento della [richiesta di un codice di autorizzazione](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

Nell'esempio seguente viene illustrato come richiedere l'autorizzazione OAuth2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure Active Directory autentica l'utente e, se necessario, chiede all'utente di concedere l'autorizzazione per l'app. Restituisce il codice di autorizzazione per l'URL di risposta dell'applicazione. A seconda del tipo response_mode richiesto, Azure Active Directory è invia i dati nella stringa di query o come dati post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Richiesta di autenticazione (aperta ID connessione)

Se si desidera non solo accedere a Gestione risorse di Azure per conto dell'utente, ma anche consentire all'utente di eseguire l'accesso all'applicazione utilizzando il proprio account Azure Active Directory, inviare un Apri ID autorizzare richiesta di connessione. Con Apri ID connettere l'applicazione riceve un id_token da Azure Active Directory che l'app è possibile usare per accedere all'utente.

Parametri della stringa di query sono disponibili per la richiesta sono descritti nell'argomento [inviare la richiesta di accesso](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Una richiesta di connettersi ID aperto di esempio è:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure Active Directory autentica l'utente e, se necessario, chiede all'utente di concedere l'autorizzazione per l'app. Restituisce il codice di autorizzazione per l'URL di risposta dell'applicazione. A seconda del tipo response_mode richiesto, Azure Active Directory è invia i dati nella stringa di query o come dati post.

Un esempio connettersi ID Apri risposta è:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Richiesta token (OAuth2.0 codice Grant flusso)

Ora che l'applicazione ha ricevuto il codice di autorizzazione da Azure Active Directory, è possibile ottenere accesso alla token per gestione di risorse di Azure.  Registrare una OAuth2.0 codice Grant Token richiesta per l'endpoint di Azure Active Directory Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametri della stringa di query sono disponibili per la richiesta sono descritti nell'argomento con [il codice di autorizzazione](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

Nell'esempio seguente viene illustrata la richiesta di token di concessione di codice con credenziali password:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Quando si utilizzano le credenziali del certificato, creare un JSON Web Token (JWT) ed effettuare l'accesso (RSA SHA256) utilizzando la chiave privata delle credenziali di certificato dell'applicazione. I tipi di richiesta di rimborso per il token vengono visualizzati in [attestazioni token JWT](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Per riferimento, vedere il [codice di Active Directory Auth libreria (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) per firmare i token JWT asserzione Client.

Vedere [Apri ID connessione specifiche](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) per informazioni dettagliate sull'autenticazione client. 

Nell'esempio seguente viene illustrata la richiesta di token di concessione di codice con credenziali certificato:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Una risposta di esempio per i codici di concedere token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Gestire codice grant token risposta

Una risposta corretta token contiene (utente + app) token di accesso per gestione di risorse di Azure. L'applicazione utilizza questo token di accesso per accedere a Gestione risorse per conto dell'utente. La durata del token di accesso rilasciato da Azure Active Directory è un'ora. Non viene in genere che l'applicazione web deve rinnovare (utente + app) token di accesso. Se è necessario rinnovare il token di accesso, utilizzare il token di aggiornamento che l'applicazione riceve nella risposta token. Registrare una OAuth2.0 Token richiesta per l'endpoint di Azure Active Directory Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

I parametri da utilizzare con la richiesta di aggiornamento sono descritti [nell'aggiornamento dei token di accesso](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

Nell'esempio seguente viene illustrato come utilizzare l'aggiornamento token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Sebbene i token di aggiornamento possono essere utilizzati per ottenere nuovi token di accesso per gestione di risorse di Azure, non sono adatti per l'accesso offline tramite l'applicazione. La durata di token di aggiornamento è limitata e i token di aggiornamento sono associati all'utente. Se l'utente lascia l'organizzazione, l'applicazione utilizzando il token di aggiornamento perde l'accesso. Questo approccio non è adatto per le applicazioni che vengono utilizzate dal team per gestire le risorse Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verificare se l'utente può assegnare l'accesso all'abbonamento

A questo punto l'applicazione ha un token per accedere a Gestione risorse di Azure per conto dell'utente. Il passaggio successivo consiste nel connettere l'app all'abbonamento. Dopo la connessione, l'app può gestire le sottoscrizioni anche quando l'utente non è presenta (l'accesso offline a lungo termine). 

Per ogni sottoscrizione per la connessione, chiamare le [autorizzazioni elenco Gestione risorse](https://msdn.microsoft.com/library/azure/dn906889.aspx) API per determinare se l'utente dispone dei diritti di gestione di accesso per la sottoscrizione.

Il metodo [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) dell'applicazione di esempio MVC ASP.NET implementata la chiamata.

Nell'esempio seguente viene illustrato come richiedere le autorizzazioni dell'utente per un abbonamento. 83cfe939-2402-4581-b761-4f59b0a041e4 corrisponde all'id della sottoscrizione.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Un esempio della risposta per ottenere le autorizzazioni utente nella sottoscrizione è:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Le autorizzazioni API restituisce più autorizzazioni. Ogni autorizzazione è costituito da azioni consentite (azioni) e non sono ammessi azioni (notactions). Se un'azione è presenta nell'elenco azioni consentite di un'autorizzazione e non è presente nell'elenco notactions di tale autorizzazione, l'utente è autorizzato a eseguire l'azione. **Microsoft.Authorization/roleassignments/Write** è l'azione che rights management che concede l'accesso. L'applicazione è necessario analizzare il risultato di autorizzazioni per cercare una corrispondenza regex in questa stringa dell'azione in azioni e le notactions di ciascuna autorizzazione.

## <a name="get-app-only-access-token"></a>Ottenere accesso solo app token

A questo punto, è possibile sapere se l'utente è possibile assegnare l'accesso all'abbonamento Azure. Passaggi successivi sono:

1. Assegnare il ruolo appropriato RBAC a identità dell'applicazione dell'abbonamento.
2. Convalidare l'assegnazione di access tramite una query per l'autorizzazione dell'applicazione dell'abbonamento o accedendo Manager delle risorse utilizzando token solo app.
1. Registrare la connessione nella struttura di dati di applicazioni "abbonamenti connesso" - mantenere l'id della sottoscrizione.

Vediamo più da vicino il primo passaggio. Per assegnare il ruolo RBAC appropriato per l'identità dell'applicazione, è necessario determinare:

- L'id dell'identità dell'applicazione nel Azure Active Directory dell'utente
- Identificatore del ruolo RBAC che richiede l'applicazione dell'abbonamento

Quando l'applicazione autentica un utente da un annuncio Azure, viene creato un oggetto principale del servizio per l'applicazione in tale Azure AD. Azure consente ruoli RBAC da assegnare alle identità di servizio per concedere l'accesso diretto alle applicazioni corrispondente sulle risorse Azure. Questa azione è esattamente quali si desidera eseguire. Query l'API di Azure Active Directory grafico per determinare l'identificatore dell'identità di servizio dell'applicazione nell'utente firmato 's Azure Active Directory.

È sufficiente un token di accesso per gestione di risorse di Azure, è necessario un nuovo token di accesso per chiamare l'API di Azure Active Directory grafico. Ogni applicazione in Azure Active Directory dispone dell'autorizzazione per ottenere tramite query il proprio oggetto principale del servizio, è sufficiente un token di accesso solo app.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Ottenere accesso solo app token per API di Azure Active Directory grafico

Per eseguire l'autenticazione l'app e ottenere un token all'API di Azure Active Directory Graph, inviare una richiesta di token flusso Client credenziali Grant OAuth2.0 all'endpoint token di Azure Active Directory (**https://login.microsoftonline.com/ {directory_domain_name} / OAuth2/Token**).

Il metodo [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) dell'applicazione di esempio Asp.net ottiene un accesso solo app token per API grafico con Active Directory Authentication Library per .NET.

Parametri della stringa di query sono disponibili per la richiesta sono descritti nell'argomento della [richiesta di un Token di accesso](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Una richiesta di esempio per credenziale concedere token: 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Una risposta di esempio per credenziale concedere token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Ottenere ObjectId dell'identità di applicazione servizio utente Azure Active Directory

A questo punto, utilizzare il token di accesso solo app eseguire una query di [identità di servizio di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API per determinare l'Id di oggetto del servizio dell'applicazione principale nella directory.

Il metodo [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) dell'applicazione di esempio Asp.net implementata la chiamata.

Nell'esempio seguente viene illustrato come richiedere principale del servizio dell'applicazione. a0448380-c346-4f9f-b897-c18733de9394 corrisponde all'id client dell'applicazione.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Nell'esempio seguente mostra una risposta alla richiesta di servizio dell'applicazione principale 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Ottenere l'identificatore di ruolo RBAC Azure

Per assegnare il ruolo RBAC appropriato per il servizio principale, è necessario determinare l'identificatore del ruolo RBAC Azure.

Ruolo RBAC più appropriato per l'applicazione:

- Se l'applicazione consente di controllare solo l'abbonamento senza apportare modifiche, è necessario solo le autorizzazioni di lettura dell'abbonamento. Assegnare il ruolo di **utilità per la lettura** .
- Se l'applicazione gestisce Azure dell'abbonamento, creazione, modifica o eliminazione entità richiede l'esecuzione di una delle autorizzazioni per i collaboratori.
  - Per gestire un determinato tipo di risorsa, assegnare i ruoli per i collaboratori specifiche delle risorse (macchina virtuale collaboratori, virtuale collaboratori di rete, lo spazio di archiviazione per i collaboratori Account e così via)
  - Per gestire qualsiasi tipo di risorsa, assegnare il ruolo di **Collaboratore** .

Assegnazione di ruolo per l'applicazione è visibile per gli utenti, pertanto selezionare dei privilegi minimi necessari.

Chiamare la [definizione di ruolo di Manager delle risorse API](https://msdn.microsoft.com/library/azure/dn906879.aspx) per elencare tutti i ruoli RBAC Azure e ricerca, quindi scorrere il risultato per trovare la definizione di ruolo desiderato in base al nome.

Il metodo [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) dell'applicazione di esempio MVC ASP.net implementata la chiamata.

Nell'esempio seguente viene richiesta viene illustrato come ottenere l'identificatore di ruolo RBAC Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb corrisponde all'id della sottoscrizione.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La risposta è nel formato seguente: 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Non è necessario chiamare l'API in modo continuativo. Dopo aver stabilito GUID conosciuto della definizione di ruolo, è possibile creare l'id di definizione ruolo come:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Ecco i GUID noti dei ruoli predefiniti comuni:

| Ruolo | GUID |
| ----- | ------ |
| Utilità per la lettura | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Per i collaboratori | b24988ac-6180-42a0-ab88-20f7382dd24c
| Per i collaboratori macchina virtuale | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Virtuali collaboratori | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Spazio di archiviazione Account collaboratori | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Sito Web per i collaboratori | de139f84-1756-47ae-9be6-808fbbe84772
| Web piano collaboratori | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server collaboratori | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB collaboratori | 9b7fa17d-e63e-47B0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Assegnare ruoli RBAC all'applicazione

C' che è necessario assegnare il ruolo appropriato RBAC principale del servizio mediante la [Gestione risorse creare assegnazione di ruolo](https://msdn.microsoft.com/library/azure/dn906887.aspx) API.

Il metodo [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) dell'applicazione di esempio MVC ASP.net implementata la chiamata.

Una richiesta di esempio per assegnare il ruolo RBAC all'applicazione: 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Nella convocazione, vengono utilizzati i valori seguenti:

| GUID | Descrizione |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | l'id dell'abbonamento
| c3097b31-7309-4c59-b4e3-770f8406bad2 | l'id dell'entità servizio dell'applicazione
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | l'id del ruolo Lettore
| 4f87261d-2816-465D-8311-70a27558df4c | un nuovo guid creato per la nuova assegnazione di ruolo

La risposta è nel formato seguente: 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Ottenere accesso solo app token per gestione di risorse di Azure

Per convalidare che app ha desiderato di accesso dell'abbonamento, eseguire un'attività di test dell'abbonamento tramite un token solo app.

Per ottenere un accesso solo app token, seguire le istruzioni dalla sezione [accedere solo app token per API di Azure Active Directory Graph](#app-azure-ad-graph), con un valore diverso per il parametro risorsa: 

    https://management.core.windows.net/

Il metodo [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) dell'applicazione di esempio Asp.net ottiene un accesso solo app token per gestione di risorse di Azure con Active Directory Authentication Library per .net.

#### <a name="get-applications-permissions-on-subscription"></a>Ottenere le autorizzazioni dell'applicazione in abbonamento

Per verificare che l'applicazione abbia accesso desiderato su un abbonamento a Azure, è possibile chiamare anche le [Autorizzazioni di gestione risorse](https://msdn.microsoft.com/library/azure/dn906889.aspx) API. Questo approccio è simile a come è determinato se l'utente dispone dei diritti di gestione dell'accesso per la sottoscrizione. Tuttavia, in questo caso, chiamare le autorizzazioni API con il token di accesso solo app ricevuto nel passaggio precedente.

Il metodo [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) dell'applicazione di esempio MVC ASP.NET implementata la chiamata.

## <a name="manage-connected-subscriptions"></a>Gestire le sottoscrizioni connesse

Quando viene assegnato il ruolo appropriato RBAC al servizio dell'applicazione principale dell'abbonamento, l'applicazione può mantenere monitoraggio/gestione mediante i token di accesso solo app per gestione di risorse di Azure.

Se il proprietario di un abbonamento rimuove l'assegnazione di ruolo dell'applicazione tramite il portale classica o strumenti della riga di comando, l'applicazione non è più poter accedere con l'abbonamento. In tal caso, è necessario comunicare all'utente che è stata interrotta la connessione con l'abbonamento da fuori dall'applicazione e assegnarle un'opzione per "correggere" la connessione. "Ripristina" si è sufficiente creare di nuovo l'assegnazione di ruolo ricoperto dai partecipanti che è stata eliminata non in linea.

Come è stata abilitata l'utente a connettersi sottoscrizioni per l'applicazione, è necessario consentire all'utente da disconnettere troppo abbonamenti. Da un punto di vista della gestione di accesso, scollegare significa rimuovere l'assegnazione di ruolo principale del servizio dell'applicazione con l'abbonamento. Facoltativamente, uno stato nell'applicazione per la sottoscrizione potrebbe essere rimossa troppo. Solo gli utenti con autorizzazione di gestione dell'abbonamento in grado di scollegare l'abbonamento.

Il [metodo RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) dell'applicazione di esempio MVC ASP.net implementata la chiamata.

Ecco fatto: gli utenti possono ora facilmente connettersi e gestire le sottoscrizioni Azure con l'applicazione.

