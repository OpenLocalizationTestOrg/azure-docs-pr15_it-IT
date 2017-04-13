<properties 
    pageTitle="Connessione all'Account di servizi multimediali utilizzando l'API REST | Microsoft Azure" 
    description="In questo argomento viene illustrato come connettersi a servizi multimediali abbonamento API REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Connessione all'Account di servizi multimediali utilizzando l'API REST di servizi di supporto

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [RESTO](media-services-rest-connect-programmatically.md)

In questo argomento viene descritto come ottenere una livello di programmazione connessione ai servizi multimediali di Microsoft Azure durante la programmazione con l'API REST di servizi multimediali.

Due sono necessari quando si accede ai servizi multimediali di Microsoft Azure: token di accesso forniti da Azure Access Control Services (ACS) e dei servizi multimediali URI stesso. È possibile utilizzare qualsiasi mezzo desiderato per la creazione di queste richieste, purché si specificare i valori di intestazione corretta e si passa nel token di accesso correttamente quando si chiama in servizi di supporto.

La procedura seguente descrive il flusso di lavoro più comune quando si utilizza l'API REST di servizi multimediali di connettersi ai servizi di supporto:

1. Ottenere un token di accesso 
2. Connessione a servizi multimediali di URI 

    >[AZURE.NOTE] Dopo aver completato la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI.
È inoltre possibile ricevere una risposta HTTP/1.1 200 che contiene la descrizione di metadati API ODATA.

3. Registrare le successive chiamate API per il nuovo URL. 

    Ad esempio, se, dopo il tentativo di connessione, ottenuto le operazioni seguenti:

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Si consiglia di registrare le chiamate API successive alla https://wamsbayclus001rest-hs.cloudapp.net/api/.

##<a name="access-control-address"></a>Indirizzo MAC

Servizi multimediali indirizzo MAC è https://wamsprodglobal001acs.accesscontrol.windows.net, ad eccezione di area Cina Nord America, in cui è https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##<a name="getting-an-access-token"></a>Ottenere un token di accesso

Per accedere a servizi multimediali direttamente tramite l'API REST, recuperare un token di accesso da ACS e usarla durante ogni richiesta HTTP apportate nel servizio. Questo token è simile a altri token fornito da ACS in base a reclami access specificati nell'intestazione di una richiesta HTTP utilizzando il protocollo v2 OAuth. Altri prerequisiti non è necessario prima di connettersi direttamente ai servizi di supporto.

Nell'esempio seguente mostra l'intestazione della richiesta HTTP e il corpo utilizzato per recuperare un token.

**Intestazione**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Corpo**:

Per verificare i valori client_id e client_secret nel corpo della convocazione; client_id e client_secret corrispondono ai valori nome account e AccountKey. Questi valori vengono forniti tramite servizi multimediali quando si configura l'account. 

Si noti che AccountKey per l'account di servizi multimediali deve essere con codifica URL (vedere [Codifica percentuale](http://tools.ietf.org/html/rfc3986#section-2.1) quando si utilizza come valore client_secret nella richiesta di token di accesso.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Per esempio: 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


Nell'esempio seguente mostra la risposta HTTP che contiene l'accesso token nel corpo della risposta.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
È consigliabile nella cache i valori "Access" e "expires_in" a un'archiviazione esterni. I dati token Impossibile in un secondo momento sull'archiviazione e riutilizzati nelle chiamate all'API REST di servizi di supporto. Questa operazione è particolarmente utile per gli scenari in cui il token può essere sicuro condivisi tra più processi o computer.

Assicurarsi di controllare il valore di "expires_in" del token di accesso e aggiornare le chiamate API REST con nuovi token in base alle esigenze.

###<a name="connecting-to-the-media-services-uri"></a>Connessione a servizi multimediali di URI

La radice URI per i servizi di supporto è https://media.windows.net/. È consigliabile connettersi inizialmente a questo URI e se si riceve un reindirizzamento 301 di nuovo in risposta, è necessario effettuare chiamate successive per il nuovo URI. Inoltre, non utilizzare una logica di auto-reindirizzamento/Segui nelle convocazioni. Verbi HTTP ed enti richiesta non essere inoltrati al nuovo URI.

Si noti che la radice URI per il caricamento e download dei file di risorse https://yourstorageaccount.blob.core.windows.net/ nel punto in cui il nome dell'account di archiviazione corrisponde a quella usata durante l'installazione e configurazione account servizi multimediali.

Nell'esempio seguente viene richiesta HTTP a servizi multimediali radice URI (https://media.windows.net/). La richiesta ottiene un reindirizzamento 301 indietro nella risposta. Richiesta successiva utilizza il nuovo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Richiesta HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**Risposta HTTP**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Richiesta HTTP** (con il nuovo URI):
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**Risposta HTTP**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] Una volta che viene visualizzato il nuovo URI, che è l'URI che deve essere utilizzato per comunicare con i servizi di supporto. 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
