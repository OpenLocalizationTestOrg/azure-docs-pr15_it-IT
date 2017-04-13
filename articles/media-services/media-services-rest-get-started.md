<properties 
    pageTitle="Iniziare con la distribuzione di contenuti su richiesta utilizzando resto | Microsoft Azure" 
    description="In questa esercitazione sono illustrati i passaggi dell'implementazione di un'applicazione di distribuzione di contenuti su richiesta con servizi multimediali di Windows Azure tramite API REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Iniziare con la distribuzione di contenuti su richiesta utilizzando resto 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Questa Guida introduttiva viene illustrata la procedura di implementazione di un'applicazione di distribuzione di contenuti punti (VoD) utilizzando le API REST servizi multimediali di Azure (AMS). 

L'esercitazione introduce il flusso di lavoro di servizi multimediali base e gli oggetti di programmazione più comuni e attività necessarie per lo sviluppo di servizi di supporto. Al termine dell'esercitazione, sarà possibile flusso o il download gradualmente un file multimediale di esempio caricati, con codifica e scaricati.  

## <a name="prerequisites"></a>Prerequisiti
Prerequisiti seguenti sono necessari per iniziare a sviluppare con i servizi di supporto con le API REST.

- Informazioni su come sviluppare con API REST di servizi di supporto. Per ulteriori informazioni, vedere [Panoramica resto di servizi multimediali](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- Un'applicazione di propria scelta che è possibile inviare risposte. In questa esercitazione utilizza [Fiddler](http://www.telerik.com/download/fiddler). 

Le attività seguenti vengono visualizzate in questa Guida introduttiva.

1.  Creare un account di servizi multimediali (tramite il portale di Azure).
2.  Configurare endpoint flusso (tramite il portale di Azure).
1.  Connettersi all'account di servizi multimediali con API REST.
1.  Creare un nuovo bene e caricare un file video con API REST.
1.  Configurare le unità di flusso con API REST.
2.  Codificare il file di origine in una serie di file MP4 velocità adattata con API REST.
1.  Pubblicare le risorse e get streaming e gli URL di download graduale con API REST. 
1.  Riprodurre il contenuto. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creare un account di servizi multimediali di Windows Azure tramite il portale di Azure

La procedura descritta in questa sezione viene illustrato come creare un account AMS.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ nuova** > **Media + CDN** > **Servizi multimediali**.

    ![Creazione di servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Creare** account servizi multimediali immettere valori obbligatori.

    ![Creazione di servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. In **Nome Account**immettere il nome del nuovo account AMS. Il nome di un account di servizi multimediali è tutto minuscole caratteri alfanumerici senza spazi e caratteri da 3 a 24.
    2. In abbonamento, scegliere tra i diversi abbonamenti Azure che è possibile accedere a.
    
    2. Nel **Gruppo di risorse**, selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono ciclo di vita, autorizzazioni e criteri. Altre informazioni [di seguito](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. In **posizione**, selezionare la località geografica viene usata per archiviare i record dei metadati ed elementi multimediali per il proprio account di servizi di supporto. Quest'area viene utilizzata per elaborare e trasmettere file multimediali. Solo le aree di servizi multimediali disponibili vengono visualizzate nella casella di riepilogo a discesa. 
    
    3. In **Account di archiviazione**, selezionare un account di archiviazione per fornire l'archiviazione blob del contenuto multimediale dal proprio account di servizi di supporto. È possibile selezionare un account di archiviazione esistente nella stessa area geografica dell'account di servizi multimediali oppure è possibile creare un account di archiviazione. Viene creato un nuovo account di archiviazione nella stessa regione. Le regole per i nomi degli account di archiviazione sono gli stessi account servizi di supporto.

        Altre informazioni sull'archiviazione [di seguito](storage-introduction.md).

    4. Selezionare **Aggiungi a dashboard** per visualizzare l'avanzamento della distribuzione account.
    
7. Fare clic su **Crea** nella parte inferiore del modulo.

    Una volta l'account è stata creata, viene modificato lo stato sia in **esecuzione**. 

    ![Impostazioni di servizi di supporto](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Per gestire il proprio account AMS (ad esempio, caricare video, codificare risorse, monitorare l'avanzamento di processo) utilizzare la finestra **Impostazioni** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurare i punti finali flussi tramite il portale di Azure

Quando si lavora con servizi multimediali di Windows Azure uno i motivi più comuni è l'esecuzione di video tramite velocità adattata streaming per i clienti. Servizi multimediali sono supportati i seguenti velocità adattata streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

Servizi multimediali di fornisce imballaggio dinamico, che consente di eseguire la velocità in bit adattata contenuto MP4 codificato in streaming formati supportati da servizi multimediali (trattino MPEG, HLS, Smooth Streaming, unità 10K) just-in-time, senza che sia necessario archiviare versioni pre-nel pacchetto di ciascuno di questi formati di flusso.

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare il file mezzanine (origine) in una serie di file di velocità adattata MP4 (codifica passaggi illustrati più avanti in questa esercitazione).  
- Creare almeno un'unità di flusso per la *trasmissione endpoint* da cui si prevede di recapito del contenuto. La procedura seguente viene illustrato come cambiare il numero di unità di trasmissione.

Con dinamico imballaggio, è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali consente di creare e serve la risposta appropriata in base a richieste da un client.

Per creare e modificare il numero di unità riservate di flusso, eseguire le operazioni seguenti:


1. Nella finestra **Impostazioni** fare clic sui **punti finali flusso**. 

2. Fare clic su predefinito streaming endpoint. 

    Viene visualizzata la finestra **Predefinito STREAMING informazioni relative all'ENDPOINT** .

3. Per specificare il numero di unità di trasmissione, spostare il cursore **Streaming unità** .

    ![Unità di trasmissione](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Fare clic sul pulsante **Salva** per salvare le modifiche.

    >[AZURE.NOTE]L'allocazione di qualsiasi unità nuova può richiedere fino a 20 minuti.

## <a id="connect"></a>Connettersi all'account di servizi multimediali con API REST

Due sono necessari quando si accede ai servizi multimediali di Windows Azure: token di accesso forniti da Azure Access Control Services (ACS) e dei servizi multimediali URI stesso. È possibile utilizzare qualsiasi mezzo desiderato per la creazione di queste richieste, purché si specificare i valori di intestazione corretta e si passa nel token di accesso correttamente quando si chiama in servizi di supporto.

La procedura seguente descrive il flusso di lavoro più comune quando si utilizza l'API REST di servizi multimediali di connettersi ai servizi di supporto:

1. Ottenere un token di accesso. 
2. Connessione a servizi multimediali di URI.  

    Tenere presente che dopo aver completato la connessione a https://media.windows.net, si riceve un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI. È inoltre possibile ricevere una risposta HTTP/1.1 200 che contiene la descrizione di metadati API ODATA.
3. Le successive chiamate API per il nuovo URL di registrazione. 
    
    Ad esempio, se, dopo il tentativo di connessione, ottenuto le operazioni seguenti:
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Si consiglia di registrare le chiamate API successive alla https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>Ottenere un token di accesso

Per accedere a servizi multimediali direttamente tramite l'API REST, recuperare un token di accesso da ACS e usarla durante ogni richiesta HTTP apportate nel servizio. Altri prerequisiti non è necessario prima di connettersi direttamente ai servizi di supporto.

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

È necessario provato i valori client_id e client_secret nel corpo della convocazione; client_id e client_secret corrispondono ai valori nome account e AccountKey. Questi valori vengono forniti tramite servizi multimediali quando si configura l'account. 

AccountKey per l'account di servizi multimediali deve essere con codifica URL viene utilizzato come valore client_secret nella richiesta di token di accesso.

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
È consigliabile nella cache i valori "Access" e "expires_in" a un'archiviazione esterni. I dati token Impossibile in un secondo momento sull'archiviazione e riutilizzati nelle chiamate all'API REST di servizi di supporto. Questa operazione è particolarmente utile per gli scenari in cui il token può essere sicuro condivisi tra più processi o computer.

Assicurarsi di controllare il valore di "expires_in" del token di accesso e aggiornare le chiamate API REST con nuovi token in base alle esigenze.

###<a name="connecting-to-the-media-services-uri"></a>Connessione a servizi multimediali di URI

La radice URI per i servizi di supporto è https://media.windows.net/. È consigliabile connettersi inizialmente a questo URI e se si riceve un reindirizzamento 301 di nuovo in risposta, è necessario effettuare chiamate successive per il nuovo URI. Inoltre, non utilizzare una logica di auto-reindirizzamento/Segui nelle convocazioni. Verbi HTTP ed enti richiesta non essere inoltrati al nuovo URI.

La radice URI per il caricamento e download dei file di risorse è https://yourstorageaccount.blob.core.windows.net/ nel punto in cui il nome dell'account di archiviazione corrisponde a quella usata durante l'installazione e configurazione account servizi multimediali.

Nell'esempio seguente viene richiesta HTTP a servizi multimediali radice URI (https://media.windows.net/). La richiesta ottiene un reindirizzamento 301 indietro nella risposta. Richiesta successiva utilizza il nuovo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Richiesta HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] Successivamente il nuovo URI viene utilizzato in questa esercitazione.

## <a id="upload"></a>Creare un nuovo bene e caricare un file video con API REST

Servizi di supporto, il caricamento dei file digitali in una risorsa. Entità **Asset** possono contenere video, audio, immagini, raccolte anteprime, testo tracce e sottotitoli codificati file (e i metadati relativi a questi file.)  Una volta i file vengono caricati in asset, il contenuto è archiviato in modo sicuro nel cloud per l'elaborazione di altre e streaming. 

Uno dei valori che è necessario fornire durante la creazione di un bene è opzioni per la creazione di risorse. La proprietà **Opzioni** è un valore di enumerazione che descrive le opzioni di crittografia che è possibile creare con una risorsa. Un valore valido è uno dei valori nell'elenco seguente non è una combinazione di valori da questo elenco:

 
- **Nessuna** = **0** - crittografia non viene utilizzato. Quando si utilizza questa opzione il contenuto non è protetta durante il transito o inattivi in archiviazione.
    Se si prevede di eseguire un MP4 tramite download graduale, usare questa opzione. 
- **StorageEncrypted** = **1** - crittografa il contenuto Cancella localmente utilizzando la crittografia AES 256 bit e quindi caricarlo in Azure lo spazio di archiviazione in cui è archiviato crittografati inattivi. Risorse è protetti con crittografia di archiviazione sono automaticamente in formato non crittografati e inseriti in un file system crittografato prima di codifica e facoltativamente nuovamente crittografati prima di caricare nuovamente come una nuova risorsa di output. Il caso di utilizzo principale per la crittografia di spazio di archiviazione è quando si desidera proteggere i file multimediali di input di alta qualità con crittografia inattivi su disco.
- **CommonEncryptionProtected** = **2** - usare questa opzione se si sta caricando contenuto già crittografato e protetto con PlayReady DRM (ad esempio Smooth Streaming protetto con PlayReady DRM) o la crittografia comuni.
- **EnvelopeEncryptionProtected** = **4** : usare questa opzione se si sta caricando HLS crittografate con AES. I file di essere stati codificati e crittografati da trasformare Manager.

### <a name="create-an-asset"></a>Creazione di un bene

Una risorsa è un contenitore per più tipi o i gruppi di oggetti in servizi di supporto, ivi compresi video, audio, immagini, raccolte anteprime, le tracce di testo e i file di sottotitoli codificati. In all'API REST la creazione di un bene è necessario inviare richiesta POST ai servizi di supporto e inserire le informazioni di proprietà le risorse nel corpo della richiesta.

Nell'esempio seguente viene illustrato come creare una risorsa.

**Richiesta HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

**Risposta HTTP**

In caso contrario, viene restituito quanto segue:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Creare un AssetFile

L'entità [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) rappresenta un file audio o video archiviata in un contenitore di blob. Un file di risorse è sempre associato a una risorsa e una risorsa può contenere uno o più AssetFiles. L'attività codificatore di servizi di supporto non riesce se un oggetto di file di risorse non è associato a un file digitale in un contenitore di blob.

Dopo il caricamento di file multimediali in un contenitore di blob, utilizzare la richiesta HTTP **UNIRE** per aggiornare il AssetFile con le informazioni sui file multimediale (come illustrato in un secondo momento nell'argomento). 

**Richiesta HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Risposta HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Creazione di AccessPolicy con autorizzazione di scrittura. 

Prima di caricare i file in archiviazione blob, impostare l'accesso diritti per i criteri per la scrittura di un bene. A tale scopo, pubblicare una richiesta HTTP al set di entità AccessPolicies. Definire un valore DurationInMinutes al momento della creazione o si riceve un messaggio di errore Server interno 500 indietro nella risposta. Per ulteriori informazioni sulla AccessPolicies, vedere [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

Nell'esempio seguente viene illustrato come creare un AccessPolicy:
        
**Richiesta HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Risposta HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Ottenere l'URL di caricamento

Per ricevere l'URL di caricamento effettivo, creare un indicatore di posizione SA. Indicatori definiscono ora di inizio e il tipo di endpoint di connessione per i client che desidera accedere ai file in una risorsa. È possibile creare più entità Locator per una determinata coppia AccessPolicy e risorse gestire le richieste di client diverso e alle esigenze. Ognuna di queste Locator utilizza il valore di inizio oltre il valore di DurationInMinutes il AccessPolicy per determinare l'intervallo di tempo che è possibile utilizzare un URL. Per ulteriori informazioni, vedere [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Un URL SA sono nel formato seguente:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Alcune considerazioni:

- Non può contenere più di cinque Locator univoco associato a una determinata attività contemporaneamente. Per ulteriori informazioni, vedere Locator.
- Se è necessario caricare i file immediatamente, impostare il valore di inizio a cinque minuti prima dell'ora corrente. In questo modo orologio potrebbe essere asimmetria tra il computer client e servizi multimediali. Il valore di inizio deve essere nel formato DateTime seguente: YYYY-MM-DDTHH:mm:ssZ (ad esempio, "2014-05-23T17:53:50Z").   
- È possibile che un secondo 30-40 ritardo dopo la creazione di un indicatore di posizione per quando è disponibile per l'uso. Questo problema riguarda SA URL e indicatori di origine.

Nell'esempio seguente viene illustrato come creare un indicatore di percorso URL SA, come definito dalla proprietà tipo nel corpo della richiesta ("1" per un indicatore di posizione SA) e "2" per un indicatore di posizione di origine su richiesta. La proprietà **Path** restituita include l'URL che è necessario utilizzare per caricare il file.
    
**Richiesta HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Risposta HTTP**

In caso contrario, viene restituita la risposta seguente:
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Caricare un file in un contenitore di spazio di archiviazione blob
    
Dopo avere creato la AccessPolicy e Locator impostare, il file viene caricato un contenitore di archiviazione Blob Azure utilizzando le API REST di spazio di archiviazione Azure. È possibile caricare nella pagina o bloccare BLOB. 

>[AZURE.NOTE] È necessario aggiungere il nome del file per il file che si desidera caricare il valore di Locator **percorso** ricevuto nella sezione precedente. Ad esempio https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Per ulteriori informazioni sull'utilizzo di BLOB Azure dello spazio di archiviazione, vedere [API REST servizio Blob](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Aggiornare il AssetFile 

Ora che è stato caricato il file, aggiornare le informazioni FileAsset dimensioni (e altro). Per esempio:
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Risposta HTTP**

Se ha esito positivo, viene restituito quanto segue: HTTP/1.1 204 Nessun contenuto

## <a name="delete-the-locator-and-accesspolicy"></a>Eliminare l'individuazione e AccessPolicy 

**Richiesta HTTP**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**Risposta HTTP**

In caso contrario, viene restituito quanto segue:

    HTTP/1.1 204 No Content 
    ...

**Richiesta HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Risposta HTTP**

In caso contrario, viene restituito quanto segue:

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Configurare le unità di flusso con API REST

Quando si lavora con servizi multimediali di Windows Azure uno i motivi più comuni è l'esecuzione di velocità adattata streaming per i clienti. Con lo streaming velocità adattata, il client possibile passare a un flusso di velocità superiori o inferiori come viene visualizzato il video in base a larghezza di banda di rete corrente, l'utilizzo della CPU e altri fattori. Servizi multimediali sono supportati i seguenti velocità adattata streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso). 

Servizi multimediali di fornisce imballaggio dinamico, che consente di distribuire il contenuto di velocità adattata MP4 o Smooth Streaming codificato in streaming formati supportati da servizi multimediali (trattino MPEG, HLS, Smooth Streaming, unità 10K) senza dover ricreare il pacchetto in questi formati di flusso. 

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- è possibile ottenere almeno un'unità di flusso per la **trasmissione endpoint **da cui si intende distribuire il contenuto (descritto in questa sezione).
- codificare o trascodifica il mezzanine (origine) archiviarlo in una serie di velocità adattata MP4 file o velocità adattata Smooth Streaming (codifica passaggi illustrati più avanti in questa esercitazione),  

Con dinamico imballaggio, è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali consente di creare e serve la risposta appropriata in base a richieste da un client. 


>[AZURE.NOTE] Per informazioni sui prezzi informazioni dettagliate, vedere [Dettagli prezzi servizi di supporto](http://go.microsoft.com/fwlink/?LinkId=275107).

Per modificare il numero di unità riservate di flusso, eseguire le operazioni seguenti:
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Ottenere l'endpoint di flusso che si desidera aggiornare

Iniziamo, ad esempio, il primo endpoint flusso nell'account (può includere fino a due punti finali flussi in stato di esecuzione nello stesso momento.)

**Richiesta HTTP**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Risposta HTTP**
    
In caso contrario, viene restituito quanto segue:
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>Ridimensionare l'endpoint di flusso
 
**Richiesta HTTP**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**Risposta HTTP**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Controllare lo stato di un'operazione di esecuzione prolungata

L'allocazione di qualsiasi unità nuova richiesta circa il 20 minuti per il completamento. Per controllare lo stato dell'operazione, utilizzare il metodo di **operazioni** e specificare l'Id dell'operazione. L'operazione Id stato restituito in risposta alla richiesta di **scala** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Richiesta HTTP**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**Risposta HTTP**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Codificare il file di origine in una serie di file MP4 adattata velocità

Dopo il caricamento di che codifica di risorse in servizi di supporto, media, transmuxed dal apposto filigrana e così via, prima che viene recapitato al client. Queste attività sono pianificate ed eseguite su più istanze del ruolo di sfondo per garantire prestazioni e disponibilità elevate. Queste attività sono denominate processi e ogni [processo](http://msdn.microsoft.com/library/azure/hh974289.aspx) è costituito da attività atomica che il lavoro effettivo nel file di risorse. 

Come detto in precedenza, quando utilizzo dei servizi multimediali di Azure uno dei motivi più comuni è l'esecuzione di velocità adattata streaming per i clienti. Servizi multimediali in modo dinamico possibile comprimere una serie di file di velocità adattata MP4 in uno dei formati seguenti: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso). 

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- codificare o trascodifica il mezzanine (origine) archiviarlo in una serie di file Smooth Streaming velocità adattata, o velocità adattata MP4  
- è possibile ottenere almeno un'unità di trasmissione per l'endpoint flusso da cui si intende distribuire il contenuto. 

La sezione seguente viene illustrato come creare un processo che contiene un'attività di codifica. L'attività specifica effettuare il file mezzanine in un set di velocità adattata MP4s utilizza **Media Encoder Standard**. La sezione inoltre viene illustrato come eseguire il monitoraggio di elaborazione lo stato di avanzamento del lavoro. Una volta completato il processo, sarà creare indicatori necessari per ottenere l'accesso alle risorse. 

### <a name="get-a-media-processor"></a>Ottenere un processore di elementi multimediali

Servizi di supporto, un processore media è un componente che gestisce un'attività di elaborazione specifico, ad esempio la codifica, conversione di formato, contenuti multimediali crittografia o decrittografia. Per l'attività di codifica visualizzate in questa esercitazione, occorre usare Media Encoder Standard.

Il codice seguente richiede l'id del codificatore. 

**Richiesta HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Risposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Creare un processo

Ogni processo può avere una o più attività in base al tipo di elaborazione che si desidera eseguire. Tramite l'API REST, è possibile creare processi e le attività correlate in uno dei due modi: attività possono essere definito all'interno del testo tramite la proprietà di spostamento di attività sulle entità processo o durante l'elaborazione batch OData. Media Services SDK utilizza l'elaborazione batch. Tuttavia, per migliorare la leggibilità degli esempi di codice in questo argomento, le attività sono definito all'interno del testo. Per informazioni sull'elaborazione batch, vedere [Elaborazione Batch Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Nell'esempio seguente viene illustrato come creare e pubblicare un processo con un che attività impostata su codificare un video in una specifica risoluzione e qualità. Nella sezione documentazione seguente contiene l'elenco di tutte le [impostazioni predefinite attività](http://msdn.microsoft.com/library/mt269960) supportato dal processore Media Encoder Standard.  

**Richiesta HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Risposta HTTP**

In caso contrario, viene restituita la risposta seguente:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Esistono alcuni aspetti importanti da tenere presente le eventuali richieste di processo:

- Proprietà TaskBody necessario utilizzare XML letterale per definire il numero di input o output risorse utilizzate dall'attività. L'argomento attività contiene la definizione dello Schema XML per i dati XML.
- Nella definizione TaskBody ogni interna valore <inputAsset> e <outputAsset> devono essere impostate come JobInputAsset(value) o JobOutputAsset(value).
- Un'attività può contenere più risorse di output. Uno JobOutputAsset(x) può essere usata solo una volta come esito di un'attività in un processo.
- È possibile specificare JobInputAsset o JobOutputAsset come una risorsa di input di un'attività.
- Attività non devono formano un ciclo.
- Il parametro del valore che si passa a JobInputAsset o JobOutputAsset rappresenta il valore di indice per una risorsa. Le attività correnti sono definite nelle proprietà di navigazione InputMediaAssets e OutputMediaAssets nella definizione di entità del processo. 

>[AZURE.NOTE] Poiché servizi multimediali di si basa su OData v3, i singoli beni insiemi di proprietà di spostamento InputMediaAssets e OutputMediaAssets vengono fatto riferimento tramite un "__metadata: uri" coppia nome-valore. 

- InputMediaAssets corrisponde a uno o più attività è stato creato in servizi di supporto. OutputMediaAssets vengono creati dal sistema. Una risorsa esistente non fanno riferimento.
- OutputMediaAssets può essere denominata utilizzando l'attributo assetName. Se l'attributo non è presente, quindi sul nome del OutputMediaAsset è qualsiasi il valore di testo interno del <outputAsset> elemento è con un suffisso del valore di nome processo o il valore di Id di processo (nel caso in cui non è definita la proprietà Name). Ad esempio, se si imposta un valore per assetName a "Campione", la proprietà di nome OutputMediaAsset da essere impostata su "Esempio". Tuttavia, se non è stato impostato un valore per assetName, ma il nome del processo "NewJob" è stata impostata, il nome OutputMediaAsset sarebbe "_NewJob JobOutputAsset (valore)". 

    Nell'esempio seguente viene illustrato come impostare l'attributo assetName:
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Per abilitare concatenazione attività:

    - Un processo deve avere almeno due attività
    - Devono essere presenti almeno un'attività a cui input è output di un'altra attività nel processo.

Per ulteriori informazioni, vedere [creazione di un processo di codifica con all'API REST di servizi di supporto](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Monitorare lo stato di avanzamento di elaborazione

È possibile recuperare lo stato del processo utilizzando la proprietà stato, come illustrato nell'esempio seguente. 

**Richiesta HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Risposta HTTP**

In caso contrario, viene restituita la risposta seguente:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Annullare un processo

Servizi multimediali consente di annullare processi in esecuzione tramite la funzione CancelJob. La chiamata restituisce un codice di errore 400 se si tenta di annullare un processo quando il relativo stato viene annullato, annullare, errore o completato.

Nell'esempio seguente viene illustrato come chiamare CancelJob.


**Richiesta HTTP**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


In caso contrario, viene restituito un codice di 204 risposta senza corpo del messaggio.

>[AZURE.NOTE] È necessario codificare URL l'id di processo (in genere nb:jid:UUID: somevalue) quando passando a CancelJob un parametro.


### <a name="get-the-output-asset"></a>Ottenere la risorsa di output 

Il codice seguente viene illustrato come richiedere la risorsa di output ID. 


**Richiesta HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Risposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Pubblicare le risorse e get streaming e gli URL di download graduale con API REST

Per flusso o il download di un bene, è innanzitutto necessario "pubblicarlo" mediante la creazione di un indicatore di posizione. Indicatori consentono di accedere ai file contenuti nel bene. Servizi multimediali sono supportati due tipi di indicatori: OnDemandOrigin Locator, utilizzato per il flusso multimediale (ad esempio MPEG trattino, HLS o Smooth Streaming) e indicatori di Access della firma (SA), utilizzato per il download di file multimediali.

Dopo aver creato il Locator, è possibile creare gli URL che consentono di flusso o scaricare i file. 


Un URL di trasmissione per Smooth Streaming è nel formato seguente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Un URL di trasmissione HLS è nel formato seguente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Un URL di trasmissione per MPEG trattino è nel formato seguente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Un URL SA utilizzato per scaricare i file sono nel formato seguente:

    {blob container name}/{asset name}/{file name}/{SAS signature}

In questa sezione viene illustrato come le seguenti operazioni necessarie per l'attività "pubblicare".  

- Creazione di AccessPolicy l'autorizzazione di lettura 
- Creazione di un URL SA per il download di contenuto 
- Creazione di un URL di origine per il contenuto del flusso 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Creazione di AccessPolicy l'autorizzazione di lettura

Prima di scaricare o qualsiasi contenuto multimediale in streaming, definire un AccessPolicy con autorizzazioni di lettura e creare l'entità Locator appropriato che specifica il tipo di meccanismo di recapito che si desidera abilitare per i clienti. Per ulteriori informazioni sulle proprietà disponibili, vedere [Le proprietà delle entità AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

Nell'esempio seguente viene illustrato come specificare AccessPolicy per le autorizzazioni di lettura per una determinata risorsa.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

In caso contrario, viene restituito un codice di 201 successo che descrive l'entità AccessPolicy creato. È quindi possibile utilizzare l'AccessPolicy Id insieme Id risorsa dell'asset che contiene il file che si desidera offrire (ad esempio, una risorsa di output) per creare l'entità Locator.

>[AZURE.NOTE]
Questo flusso di lavoro di base è diverso da quello di caricare un file durante il caricamento di un bene (come illustrato in precedenza in questo argomento). Inoltre, ad esempio il caricamento di file, se si (o i client) devono poter accedere ai file immediatamente, impostare il valore di inizio a cinque minuti prima dell'ora corrente. Questa operazione è necessaria in quanto potrebbero esserci orologio asimmetria tra il client e servizi multimediali. Il valore di ora di inizio deve essere nel formato DateTime seguente: YYYY-MM-DDTHH:mm:ssZ (ad esempio, "2014-05-23T17:53:50Z").


###<a name="creating-a-sas-url-for-downloading-content"></a>Creazione di un URL SA per il download di contenuto 

Il codice seguente viene illustrato come ottenere un URL che può essere utilizzato per scaricare un file multimediale creato e caricato in precedenza. Il AccessPolicy dispone di set di autorizzazioni di lettura e il percorso Locator fa riferimento a un URL di download SA.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

In caso contrario, viene restituita la risposta seguente:

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


La proprietà **percorso** restituita contiene l'URL di SA.

>[AZURE.NOTE]
Se si scarica contenuto archiviazione crittografati, si deve decrittografare prima di eseguirne il rendering manualmente o usare MediaProcessor di decrittografia dello spazio di archiviazione in un'attività di elaborazione per i file trasformati in chiaro per un OutputAsset di output e quindi scaricare da tale attività. Per ulteriori informazioni sull'elaborazione, vedere Creazione di un processo di codifica con all'API REST di servizi di supporto. Inoltre, sa URL Locator non possono essere aggiornati dopo che sono state create. Ad esempio, non è possibile riutilizzare Locator stesso con un valore di ora di inizio aggiornato. Ciò è dovuto la modalità di che creazione di associazioni di protezione URL. Se si desidera accedere a una risorsa per il download dopo un indicatore di posizione è scaduto, è necessario creare una nuova con una nuova ora di inizio.

###<a name="download-files"></a>Scaricare i file

Dopo avere creato la AccessPolicy e Locator impostata, è possibile scaricare i file usando le API REST di spazio di archiviazione Azure.  

>[AZURE.NOTE] È necessario aggiungere il nome del file per il file da scaricare per il valore di Locator **percorso** ricevuto nella sezione precedente. Ad esempio https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Per ulteriori informazioni sull'utilizzo di BLOB Azure dello spazio di archiviazione, vedere [API REST servizio Blob](http://msdn.microsoft.com/library/azure/dd135733.aspx).

Risultato del processo di codifica eseguite precedenti (codifica nel set di adattamento MP4), si dispone di più file MP4 che è possibile scaricare gradualmente. Per esempio:    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Creazione di un URL di trasmissione per il contenuto del flusso


Il codice seguente viene illustrato come creare un flusso Locator URL:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

In caso contrario, viene restituita la risposta seguente:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Per eseguire il flusso di un URL di origine Smooth Streaming in un lettore multimediale streaming, è necessario aggiungere il percorso di proprietà con il nome del Smooth Streaming manifesto file, seguito da "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Per eseguire il flusso HLS, accodare (formato = m3u8 aapl) dopo il "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Per eseguire il flusso trattino MPEG, accodare (formato = csf di tempo mpd) dopo il "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Riprodurre il contenuto  

Per eseguire il flusso video, utilizzare [i servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Per testare il download graduale, incollare un URL in un browser (ad esempio, Internet Explorer, Chrome, Safari).


##<a name="next-steps-media-services-learning-paths"></a>Passaggi successivi: Servizi multimediali percorsi formativi

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>Non che si stava cercando?

Se questo argomento non contiene risultati desiderati, non è un elemento o in un altro modo non soddisfa le proprie esigenze, specificare un feedback utilizzando il thread Disqus riportata di seguito.



