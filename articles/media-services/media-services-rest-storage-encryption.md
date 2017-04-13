<properties 
    pageTitle="La crittografia del contenuto con la crittografia di spazio di archiviazione mediante API REST AMS" 
    description="Informazioni su come crittografare il contenuto con la crittografia di spazio di archiviazione mediante le API REST AMS." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="encrypting-your-content-with-storage-encryption-using-ams-rest-api"></a>La crittografia del contenuto con la crittografia di spazio di archiviazione mediante API REST AMS

Si consiglia per crittografare il contenuto in locale utilizzando la crittografia AES 256 bit e quindi caricarlo su Azure lo spazio di archiviazione in cui verrà archiviata crittografati inattivi.

In questo articolo viene fornita una panoramica di crittografia di spazio di archiviazione AMS e viene illustrato come caricare il contenuto di spazio di archiviazione crittografato:

- Creare una chiave del contenuto.
- Creare una risorsa. Impostare il AssetCreationOption su StorageEncryption quando si creano le risorse.

     Le attività crittografate devono essere associati a tasti di contenuto.
- Collegare la chiave del contenuto per la risorsa.  
- Impostare la crittografia relativi parametri sulle entità AssetFile.
 
>[AZURE.NOTE]Se si desidera fornire una risorsa crittografato di spazio di archiviazione, è necessario configurare i criteri di recapito della risorsa. Prima che la risorsa può essere alterata, server di streaming rimuove la crittografia di spazio di archiviazione e flusso il contenuto utilizzando i criteri di recapito specificato. Per ulteriori informazioni, vedere [Configurazione dei criteri di recapito della risorsa](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE] Quando si utilizza l'API REST di servizi multimediali, le considerazioni seguenti:
>
>Quando si accede a entità in servizi di supporto, è necessario impostare i campi specifici dell'intestazione e i valori nelle convocazioni HTTP. Per ulteriori informazioni, vedere [il programma di installazione per lo sviluppo di API REST servizi multimediali](media-services-rest-how-to-use.md).

>Dopo aver completato la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI come descritto nella [connessione ai servizi multimediali utilizzando l'API REST](media-services-rest-connect-programmatically.md). 

##<a name="storage-encryption-overview"></a>Panoramica di crittografia di spazio di archiviazione 

La crittografia di spazio di archiviazione AMS si applica la crittografia **AES CTR** modalità per l'intero file.  Modalità AES CTR è blocchi in grado di crittografare i dati di lunghezza non autorizzato senza necessità di spaziatura interna. Funziona crittografando un blocco di contatore con algoritmo AES e quindi XOR are l'output di AES con i dati per crittografare o decrittografare.  Blocco di contatore utilizzato sia impostato copiando il valore di InitializationVector in byte da 0 a 7 del valore contatore e byte 8-15 del valore contatore vengono impostati su zero. Del blocco di contatore byte 16 byte 8-15 (ad esempio i byte meno significativi) vengono utilizzati come un intero senza segno a 64 bit semplice che viene incrementato di uno per ogni successivo blocco di dati di elaborazione e viene mantenuto in rete. Si noti che, se il valore integer raggiunge il valore massimo (0xFFFFFFFFFFFFFFFF) incremento quindi Reimposta contatore blocco a zero (byte 8-15) senza influenzare altri 64 bit del contatore (ad esempio byte 0-7).   Per mantenere la sicurezza della crittografia AES CTR modalità, il valore di InitializationVector per un determinato identificatore di chiave per ogni chiave contenuto deve essere univoco per ogni file e i file devono essere inferiore a 2 ^ 64 blocchi lunghezza.  In questo modo viene che un valore del contatore non viene mai riutilizzato con una determinata chiave. Per ulteriori informazioni sulla modalità CTR, vedere [la pagina wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (articolo wiki utilizza il termine "Nonce" anziché "InitializationVector").

Utilizzare **La crittografia di spazio di archiviazione** per crittografare il contenuto chiaro localmente utilizzando la crittografia AES 256 bit e quindi caricarlo su Azure lo spazio di archiviazione in cui è archiviato crittografati inattivi. Risorse è protetti con crittografia di archiviazione sono automaticamente in formato non crittografati e inseriti in un file system crittografato prima di codifica e facoltativamente nuovamente crittografati prima di caricare nuovamente come una nuova risorsa di output. Il caso di utilizzo principale per la crittografia di spazio di archiviazione è quando si desidera proteggere i file di supporto di input di alta qualità con crittografia inattivi su disco.

Per offrire una risorsa crittografato di spazio di archiviazione, è necessario configurare i criteri di recapito del bene in modo che servizi multimediali di sa come si desidera distribuire il contenuto. Prima che la risorsa può essere alterata, server di streaming rimuove la crittografia di spazio di archiviazione e flusso il contenuto utilizzando i criteri di consegna specificata (ad esempio AES, crittografia comune o senza crittografia).

##<a name="create-contentkeys-used-for-encryption"></a>Creare ContentKeys utilizzato per la crittografia

Le attività crittografate devono essere associate a chiave di crittografia di spazio di archiviazione. È necessario creare la chiave del contenuto da utilizzare per la crittografia prima di creare i file di risorse. In questa sezione viene descritto come creare una chiave di contenuto.

Di seguito sono passaggi generali per generare chiavi associati con l'opzione risorse che si desidera crittografare. 

1. Per la crittografia di spazio di archiviazione, in modo casuale generare una chiave AES 32 byte. 

    Questo sarà la chiave del contenuto per la risorsa, ovvero tutti i file associati a tale attività saranno necessario utilizzare la stessa chiave contenuto durante la decrittografia. 
2.  Chiamare i metodi [GetProtectionKeyId](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkeyid) e [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) per ottenere il certificato x. 509 corretto che deve essere utilizzato per crittografare la chiave del contenuto.
3.  Crittografare la chiave del contenuto con la chiave pubblica del certificato x. 509. 

    Media Services .NET SDK utilizza RSA OAEP quando si esegue la crittografia.  È possibile visualizzare un esempio di .NET nella [funzione EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4.  Creare un valore di checksum calcolato mediante l'identificatore di chiave e chiave del contenuto. Nell'esempio seguente viene .NET calcola il checksum utilizzando la parte GUID dell'identificatore di chiave e il tasto Cancella contenuto.
    

        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
        {
            const int ChecksumLength = 8;
            const int KeyIdLength = 16;

            byte[] encryptedKeyId = null;

            // Checksum is computed by AES-ECB encrypting the KID
            // with the content key.
            using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
            {
                rijndael.Mode = CipherMode.ECB;
                rijndael.Key = contentKey;
                rijndael.Padding = PaddingMode.None;

                ICryptoTransform encryptor = rijndael.CreateEncryptor();
                encryptedKeyId = new byte[KeyIdLength];
                encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
            }

            byte[] retVal = new byte[ChecksumLength];
            Array.Copy(encryptedKeyId, retVal, ChecksumLength);

            return Convert.ToBase64String(retVal);
        }


5. Creare la chiave del contenuto con **EncryptedContentKey** (convertito in stringa con codifica base 64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**e valori di **Checksum** ricevuto in precedenza.

    
    Per la crittografia di spazio di archiviazione, le proprietà seguenti dovrebbero essere incluso nel corpo della richiesta.
     
    Proprietà corpo richiesta   | Descrizione
    ---|---
    ID | il ContentKey Id che verranno generati noi utilizzando il seguente formato "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | Questo è il tipo di chiave contenuto come numero intero per questa chiave del contenuto. Passiamo il valore 1 per la crittografia di spazio di archiviazione.
    EncryptedContentKey | È creare un nuovo valore chiave contenuto che è un valore di 256 bit (32 byte). La chiave è crittografata tramite il certificato x. 509 di crittografia dello spazio di archiviazione che si recupera da servizi multimediali di Microsoft Azure eseguendo una richiesta HTTP GET per le GetProtectionKeyId e i metodi di GetProtectionKey. Ad esempio, vedere il seguente codice .NET: il metodo **EncryptSymmetricKeyData** definito [qui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Questo è l'id chiave di protezione per il certificato x. 509 di crittografia di spazio di archiviazione utilizzato per crittografare la chiave del contenuto.
    ProtectionKeyType | Questo è il tipo di crittografia per la chiave di protezione utilizzato per crittografare la chiave del contenuto. Questo valore è StorageEncryption(1) in questo esempio.
    Checksum |Il controllo calcolato MD5 per la chiave del contenuto. Viene calcolato mediante la crittografia del contenuto Id con la chiave del contenuto. Il codice riportato di seguito viene illustrato come il calcolo del checksum.
    

###<a name="retrieve-the-protectionkeyid"></a>Recuperare la ProtectionKeyId 
 

Nell'esempio seguente viene illustrato come recuperare ProtectionKeyId, un'identificazione personale certificato, per il certificato che è necessario utilizzare per crittografare la chiave del contenuto. Eseguire questo passaggio per assicurarsi di avere già il certificato appropriato nel computer in uso.


Richiesta:
    
    
    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    

Risposta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

###<a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recuperare la ProtectionKey per il ProtectionKeyId

Nell'esempio seguente viene illustrato come recuperare il certificato x. 509 utilizzando il ProtectionKeyId ottenuto nel passaggio precedente.

Richiesta:
        
    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net
    


Risposta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Creare la chiave del contenuto 

Dopo avere recuperato il certificato x. 509 e utilizzare la chiave pubblica per crittografare la chiave del contenuto, creare un'entità **ContentKey** e impostare i valori delle proprietà di conseguenza.

Uno dei valori che è necessario impostare quando creare il contenuto è il tipo. In caso di crittografia dello spazio di archiviazione, il valore è "1". 

Nell'esempio seguente viene illustrato come creare un **ContentKey** con un set di **ContentKeyType** per la crittografia di spazio di archiviazione ("1") e **ProtectionKeyType** impostata su "0" per indicare che la chiave di protezione Id è l'identificazione personale certificato x. 509.  


Richiesta

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }


Risposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Creazione di un bene

Nell'esempio seguente viene illustrato come creare una risorsa.

**Richiesta HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny" "Options":1}


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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
##<a name="associate-the-contentkey-with-an-asset"></a>Associare il ContentKey un'attività

Dopo aver creato il ContentKey, associarla le risorse utilizzando l'operazione $links, come illustrato nell'esempio seguente:
    
Richiesta:
    
    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Risposta:

    HTTP/1.1 204 No Content 

##<a name="create-an-assetfile"></a>Creare un AssetFile

L'entità [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) rappresenta un file audio o video archiviata in un contenitore di blob. Un file di risorse è sempre associato a una risorsa e una risorsa può contenere uno o più file di risorse. L'attività codificatore di servizi di supporto non riesce se un oggetto di file di risorse non è associato a un file digitale in un contenitore di blob.

Si noti che l'istanza **AssetFile** e sul file multimediale effettivo sono due oggetti distinti. L'istanza di AssetFile contenente metadati relativi a file multimediale, mentre il file multimediale contiene il contenuto multimediale effettivo.

Dopo aver caricato un file multimediali in un contenitore di blob, utilizzare la richiesta HTTP **UNIRE** per aggiornare il AssetFile con informazioni sui file multimediale (non illustrata in questo argomento). 

**Richiesta HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
