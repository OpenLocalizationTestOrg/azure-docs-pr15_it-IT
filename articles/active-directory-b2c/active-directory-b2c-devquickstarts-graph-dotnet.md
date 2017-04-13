<properties
    pageTitle="Azure Active Directory B2C: Usare il grafico API | Microsoft Azure"
    description="Come chiamare l'API di grafico un tenant B2C tramite un'identità di applicazione per automatizzare il processo."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure Active Directory B2C: Usare il grafico API

Azure Active Directory (Azure Active Directory) B2C tenant sono di dimensioni molto grandi. Questo errore indica che è necessario eseguire a livello di programmazione molte attività comuni di gestione dei tenant. Un esempio principale è la gestione degli utenti. Potrebbe essere necessario eseguire la migrazione di un archivio utente esistente a un tenant B2C. È consigliabile ospitare la registrazione utente nella propria pagina e creare gli account utente in Active Directory Azure dietro le quinte. Questi tipi di attività richiedono la possibilità di creare, leggere, aggiornare ed eliminare gli account utente. È possibile eseguire queste operazioni tramite l'API di Azure Active Directory grafico.

Per i tenant B2C, sono disponibili due modalità primaria per comunicare con l'API di grafico.

- Per le attività, una volta Esegui interattive, deve fungere da un account di amministratore tenant B2C quando si eseguono le attività. Questa modalità richiede un amministratore per accedere con le credenziali prima che l'amministratore può eseguire le chiamate all'API di grafico.
- Per le attività automatica e continue, è necessario utilizzare un tipo di fornire i privilegi necessari per eseguire attività di gestione degli account del servizio. In Azure Active Directory, è possibile eseguire la registrazione di un'applicazione e autenticazione in Azure Active Directory. In tal caso, con un **ID applicazione** che utilizza [le credenziali del client 2.0 OAuth concedono](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). In questo caso, l'applicazione funge stesso, non come un utente, chiamare l'API di grafico.

In questo articolo verrà trattato come effettuare il caso di utilizzo automatico. Per dimostrare, verrà creata una .NET 4.5 `B2CGraphClient` che esegue utente creare, leggere, aggiornare ed eliminazione (CRUD). Il client avrà un'interfaccia della riga di comando di Windows (CLI) che consente di richiamare vari metodi. Tuttavia, il codice è scritto a si comportano in modo automatico non interattivo.

## <a name="get-an-azure-ad-b2c-tenant"></a>Ottenere un tenant di Azure Active Directory B2C

Prima di poter creare applicazioni o gli utenti o interagire con Azure Active Directory tutto, è necessario un tenant di Azure Active Directory B2C e un account di amministratore globale nel tenant. Se non si dispone di un tenant già, [Guida introduttiva di Azure Active Directory B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Registrare un'applicazione di servizio nel tenant

Dopo aver ottenuto un tenant B2C, è necessario creare l'applicazione di servizio utilizzando i cmdlet PowerShell di Azure Active Directory.
Prima di tutto, scaricare e installare [Microsoft Online Services Assistente per l'accesso](http://go.microsoft.com/fwlink/?LinkID=286152). Scaricare e installare il [modulo di Azure Active Directory 64 bit di Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Per utilizzare l'API grafico con il tenant B2C, sarà necessario registrare un'applicazione dedicata tramite PowerShell. Seguire le istruzioni in questo articolo per farlo. Non è possibile riutilizzare le applicazioni B2C già esistenti registrati nel portale di Azure.

Dopo l'installazione del modulo PowerShell, aprire PowerShell e connettersi al tenant di B2C. Dopo aver eseguito `Get-Credential`, verrà richiesto per un nome utente e password, immettere il nome utente e la password del proprio account di amministratore tenant B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Prima di creare l'applicazione, è necessario generare un nuovo **segreto del client**.  L'applicazione utilizzerà il segreto del client per l'autenticazione di Azure Active Directory e acquisire token di accesso. È possibile generare un segreto valido PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

Il comando finale deve essere stampato il nuovo segreto del client. Copiarlo in un luogo sicuro. È necessario in un secondo momento. A questo punto è possibile creare l'applicazione fornendo il nuovo segreto client come credenziali per l'app:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Se si crea correttamente l'applicazione, deve stampare le proprietà dell'applicazione simili a quelli riportati sopra. È necessario entrambe `ObjectId` e `AppPrincipalId`, quindi copiare troppo tali valori.

Dopo aver creato un'applicazione nel tenant di B2C, è necessario assegnare le autorizzazioni che necessarie per eseguire le operazioni CRUD utente. Assegnare i ruoli di tre dell'applicazione: directory lettori (Vedi gli utenti), gli autori directory (per creare e aggiornare gli utenti) e un amministratore di account utente (per eliminare utenti). Questi ruoli sono identificatori noti, in modo che è possibile sostituire il `-RoleMemberObjectId` parametro con `ObjectId` dall'alto ed eseguire i comandi seguenti. Per visualizzare l'elenco di tutti i ruoli di directory, provare a eseguire `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Ora installata un'applicazione che dispone dell'autorizzazione per creare, leggere, aggiornare ed eliminare utenti da tenant B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Scaricare, configurare e generare il codice di esempio

Prima di tutto, scaricare il codice di esempio e l'esecuzione. Quindi verrà dettaglio su di esso.  È possibile [scaricare il codice di esempio come file ZIP](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Si può anche duplicare in una directory di propria scelta:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Aprire la `B2CGraphClient\B2CGraphClient.sln` soluzione Visual Studio in Visual Studio. Nel `B2CGraphClient` del progetto, aprire il file `App.config`. Sostituire le impostazioni di tre app con i valori:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Successivamente, fare clic sul `B2CGraphClient` soluzione e rigenera il campione. Se hanno esito positivo, è necessario avere un `B2C.exe` file eseguibile che si trova `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Creare le operazioni CRUD utente tramite l'API di grafico

Per utilizzare il B2CGraphClient, aprire una `cmd` comandi di Windows richiesta e modificare la directory per il `Debug` directory. Eseguire il `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Verrà visualizzata una breve descrizione di ogni comando. Ogni volta che si richiama uno di questi comandi `B2CGraphClient` effettua una richiesta all'API di Azure Active Directory grafico.

### <a name="get-an-access-token"></a>Ottenere un token di accesso

Qualsiasi richiesta all'API grafico richiede un token di accesso per l'autenticazione. `B2CGraphClient`utilizza l'Apri origine Active Directory autenticazione raccolta (ADAL) per acquisire token di accesso. ADAL semplifica acquisizione token mediante una semplice API e verificando di alcuni dettagli importanti, ad esempio la memorizzazione nella cache token di accesso. Non è necessario utilizzare ADAL per ottenere i token, attraverso. È inoltre possibile ottenere i token creando richieste HTTP.

> [AZURE.NOTE]
    In questo esempio viene usata v2 ADAL per comunicare con l'API di grafico.  Per ottenere i token di accesso che possono essere utilizzati con l'API di Azure Active Directory grafico, è necessario utilizzare ADAL versione 2 o 3.

Quando `B2CGraphClient` viene eseguito, crea un'istanza del `B2CGraphClient` classe. Costruttore per questa classe configura automaticamente una struttura mediante l'autenticazione ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Si userà il `B2C Get-User` comando come esempio. Quando `B2C Get-User` richiamato senza qualsiasi input aggiuntivi, le chiamate CLI il `B2CGraphClient.GetAllUsers(...)` metodo. Questo metodo chiama `B2CGraphClient.SendGraphGetRequest(...)`, che invia una richiesta HTTP GET all'API di grafico. Prima di `B2CGraphClient.SendGraphGetRequest(...)` Invia richiesta GET, prima di tutto Ottiene un accesso token utilizzando ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

È possibile ottenere un accesso token per l'API grafico chiamando il supporto di ADAL `AuthenticationContext.AcquireToken(...)` metodo. ADAL restituisce quindi un `access_token` che rappresenta l'identità dell'applicazione.

### <a name="read-users"></a>Leggere gli utenti

Quando si desidera ottenere un elenco di utenti o ottenere un utente specifico dall'API di grafico, è possibile inviare un HTTP `GET` richiesta per il `/users` endpoint. Una richiesta di tutti gli utenti in un tenant è simile alla seguente:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare la richiesta, eseguire:

 ```
 > B2C Get-User
 ```

Esistono due conseguenze importanti da tenere presente:

- Il token di accesso acquisito tramite ADAL viene aggiunto al `Authorization` intestazione utilizzando il `Bearer` schema.
- Per i tenant B2C, è necessario utilizzare il parametro di query `api-version=1.6`.

In entrambi i dettagli vengono gestiti i `B2CGraphClient.SendGraphGetRequest(...)` metodo:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Creare gli account utente consumer

Quando si creano l'account utente nel tenant di B2C, è possibile inviare un HTTP `POST` richiesta per il `/users` endpoint:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La maggior parte di queste proprietà nella richiesta sono necessari per creare utenti consumer. Per ulteriori informazioni, fare clic [qui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Si noti che la `//` i commenti sono state inclusi per illustrazione. Non includere loro in una richiesta effettiva.

Per visualizzare la richiesta, eseguire uno dei comandi seguenti:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Il `Create-User` comando accetta un file .json come un parametro di input. Questa pagina contiene una rappresentazione in formato JSON di un oggetto utente. Esistono due file .json di esempio nel codice di esempio: `usertemplate-email.json` e `usertemplate-username.json`. È possibile modificare questi file in base alle esigenze. Oltre ai campi obbligatori sopra, numerosi campi facoltativi che è possibile utilizzare sono inclusi in questi file. Sono disponibili informazioni dettagliate sui campi facoltativi nel [riferimento di entità API di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

È possibile vedere come sia impostata la richiesta POST in `B2CGraphClient.SendGraphPostRequest(...)`.

- Allega un token di accesso per la `Authorization` intestazione della richiesta.
- Imposta `api-version=1.6`.
- Include l'oggetto utente JSON nel corpo della convocazione.

> [AZURE.NOTE]
Se l'account che si desidera eseguire la migrazione da un archivio utente esistente inferiore password la potenza è rispetto [alla rottura di password complessa stabilita dal B2C di Azure Active Directory](https://msdn.microsoft.com/library/azure/jj943764.aspx), è possibile disattivare il requisito di password complesse utilizzando il `DisableStrongPassword` valore nel `passwordPolicies` proprietà. Ad esempio, è possibile modificare la richiesta di utente crea fornita in precedenza come indicato di seguito: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Aggiornare gli account utente consumer

Quando si aggiornano gli oggetti utente, il processo è simile a quella da utilizzare per creare oggetti utente. Ma questo processo viene utilizzato il protocollo HTTP `PATCH` metodo:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Provare ad aggiornare un utente aggiornando i file JSON con i nuovi dati. È quindi possibile utilizzare `B2CGraphClient` per eseguire uno di questi comandi:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Verificare la presenza di `B2CGraphClient.SendGraphPatchRequest(...)` metodo per informazioni dettagliate su come inviare la richiesta.

### <a name="search-users"></a>Ricerca utenti

È possibile cercare utenti nel tenant di B2C in due modi. Uno, con l'utente oggetto ID o due, utilizzare identificatore di accesso dell'utente (ad esempio, il `signInNames` proprietà).

Eseguire uno dei comandi seguenti per cercare un utente specifico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Ecco un paio di esempi:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminare utenti

Il processo per l'eliminazione di un utente è semplice. Usare il protocollo HTTP `DELETE` metodo e costrutto l'URL che contiene il corretto ID oggetto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare un esempio, immettere il seguente comando e visualizzare la richiesta di eliminazione viene stampata nella console:

```
> B2C Delete-User <object-id-of-user>
```

Verificare la presenza di `B2CGraphClient.SendGraphDeleteRequest(...)` metodo per informazioni dettagliate su come inviare la richiesta.

È possibile eseguire molte altre azioni con l'API di Azure Active Directory grafico oltre la gestione degli utenti. Il [riferimento all'API di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) vengono fornite informazioni dettagliate su ciascuna azione, insieme a richieste di esempio.

## <a name="use-custom-attributes"></a>Utilizzare gli attributi personalizzati

La maggior parte delle applicazioni consumer è necessario archiviare un tipo di informazioni del profilo utente personalizzato. Per effettuare questa operazione è possibile definire un attributo personalizzato nel tenant di B2C. È quindi possibile considerare l'attributo allo stesso modo si considera qualsiasi altra proprietà su un oggetto utente. È possibile aggiornare l'attributo, eliminare l'attributo, eseguire una query per l'attributo, inviare l'attributo come attestazione token di accesso e altro ancora.

Per definire un attributo personalizzato nel tenant di B2C, vedere la [Guida di riferimento B2C attributo personalizzato](active-directory-b2c-reference-custom-attr.md).

È possibile visualizzare gli attributi personalizzati definiti nel tenant di B2C utilizzando `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

L'output di queste funzioni, vengono visualizzate i dettagli di ogni attributo personalizzato, ad esempio:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

È possibile usare il nome completo, ad esempio `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, come una proprietà sugli oggetti utente.  Aggiornare il file .json con la nuova proprietà e un valore per la proprietà e quindi eseguire:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Utilizzando `B2CGraphClient`, si ha un'applicazione di servizio in grado di gestire gli utenti di tenant B2C a livello di programmazione. `B2CGraphClient`utilizza la propria identità di applicazione per eseguire l'autenticazione all'API di Azure Active Directory grafico. Acquista anche i token utilizzando un segreto del client. Come si incorporano questa funzionalità nell'applicazione, tenere presenti alcuni punti chiave di B2C App:

- È necessario concedere le autorizzazioni appropriate nel tenant dell'applicazione.
- Per ora, è necessario utilizzare v2 ADAL per ottenere i token di accesso. (È anche possibile inviare messaggi di protocollo direttamente, senza utilizzare una raccolta.)
- Quando si chiama l'API di grafico, utilizzare `api-version=1.6`.
- Quando si crea e aggiornare gli utenti consumer, alcune proprietà sono obbligatorie, come descritto in precedenza.

Se si dispone delle domande o richieste di azioni che si desidera eseguire tramite l'API di grafico nel tenant di B2C, lasciare un commento su questo articolo o un problema di file di archivio di esempio di codice GitHub.
