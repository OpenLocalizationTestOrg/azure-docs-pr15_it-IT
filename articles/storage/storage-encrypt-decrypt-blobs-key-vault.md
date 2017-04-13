<properties
    pageTitle="Esercitazione: Crittografare e decrittografare BLOB di archiviazione di Microsoft Azure utilizzando Azure chiave archivio | Microsoft Azure"
    description="In questa esercitazione viene illustrato come crittografare e decrittografare un blob tramite la crittografia lato client per lo spazio di archiviazione di Microsoft Azure con Azure chiave archivio."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Esercitazione: Crittografare e decrittografare BLOB di archiviazione di Microsoft Azure utilizzando Azure chiave archivio

## <a name="introduction"></a>Introduzione

In questa esercitazione viene illustrato come apportare uso della crittografia di archiviazione sul lato client con Azure chiave archivio. Illustra come crittografare e decrittografare un blob in un'applicazione console mediante queste tecnologie.

**Durata stimata per completare:** 20 minuti

Per informazioni generali su Azure chiave archivio, vedere [Novità Azure chiave archivio?](../key-vault/key-vault-whatis.md).

Per informazioni generali sulla crittografia lato client per l'archiviazione di Azure, vedere [la crittografia lato Client e Azure chiave archivio per lo spazio di archiviazione di Microsoft Azure](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

- Un account di archiviazione Azure
- Visual Studio 2013 o versione successiva
- PowerShell Azure


## <a name="overview-of-client-side-encryption"></a>Panoramica di crittografia lato client

Per una panoramica di crittografia lato client per l'archiviazione di Azure, vedere [Azure chiave archivio per lo spazio di archiviazione di Microsoft Azure e la crittografia lato Client](storage-client-side-encryption.md)

Ecco una breve descrizione del funzionamento di crittografia lato client:

1. Il client di archiviazione Azure SDK genera una chiave di crittografia contenuto (CEK), ovvero una chiave simmetrica un utilizzo.
2. Dati dei clienti è crittografati tramite questo CEK.
3. Il CEK quindi va a capo (crittografati) utilizzando la chiave di crittografia (KEK). Il KEK è identificato da un identificatore di chiave e può essere una coppia di chiavi asimmetrica o con un tasto simmetrico e possono essere gestiti a livello locale o memorizzati in Azure chiave archivio. Il client di spazio di archiviazione non ha accesso alla KEK. Richiama solo l'algoritmo di ritorno a capo chiave fornito dall'archivio di chiave. I clienti è possono scegliere di utilizzare provider personalizzati per la chiave ritorno a capo/unwrapping se preferiscono.
4. I dati crittografati viene quindi caricati al servizio di archiviazione Azure.


## <a name="set-up-your-azure-key-vault"></a>Configurare l'archivio di chiave Azure
Per continuare con questa esercitazione, è necessario eseguire i passaggi seguenti, che sono descritti nell'esercitazione [Introduzione Azure chiave archivio](../key-vault/key-vault-get-started.md):

- Creare un archivio di chiave.
- Aggiungere una chiave o segreta per l'archivio di chiave.
- Registrare un'applicazione di Azure Active Directory.
- Autorizzare l'applicazione per utilizzare il tasto o segreto.

Prendere nota del ClientID e ClientSecret generati durante la registrazione di un'applicazione con Azure Active Directory.

Creare entrambe le chiavi nell'archivio di chiave. Per il resto dell'esercitazione si presuppone che si utilizzano i nomi seguenti: ContosoKeyVault e TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Creare un'applicazione console di pacchetti e AppSettings

In Visual Studio, creare una nuova applicazione console.

Aggiungere pacchetti nuget necessari nella Console di gestione pacchetti.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Aggiungere l'App AppSettings.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Aggiungere quanto segue `using` istruzioni e assicurarsi di aggiungere un riferimento a Configuration al progetto.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Aggiungere un metodo per ottenere un token all'applicazione console

Il metodo seguente viene utilizzato da classi archivio chiave che è necessario eseguire l'autenticazione per l'accesso per l'archivio di chiave.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Accedere a spazio di archiviazione e l'archivio di chiave nel programma

Funzione principali, aggiungere il codice riportato di seguito.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modelli di oggetti archivio chiave
>
>È importante tenere presente che in realtà due modelli a oggetti archivio di chiave da tenere presenti: uno si basa sull'API REST (spazio dei nomi KeyVault) e l'altra è un'estensione per la crittografia lato client.

> Il Client di archivio di chiave interagisce con l'API REST e riconosce JSON Web chiavi e informazioni riservate per i due tipi di elementi contenuti nell'archivio di chiave.

> Le estensioni di archivio di chiave sono classi sembrano create specificamente per la crittografia lato client in archiviazione Azure. Contengono un'interfaccia per chiavi (IKey) e le classi basate sul concetto di un sistema di risoluzione chiave. Esistono due implementazioni di IKey che è necessario conoscere: RSAKey e SymmetricKey. A questo punto si verificano a coincide con i cambiamenti sono contenuti in un archivio di chiave, ma a questo punto sono classi indipendente (in modo che la chiave e il segreto recuperato dal Client archivio chiave non implementare IKey).


## <a name="encrypt-blob-and-upload"></a>Crittografare blob e caricare
Aggiungere il codice seguente per crittografare un blob e caricare al proprio account di archiviazione Azure. Il metodo **ResolveKeyAsync** utilizzato restituisce un IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


Di seguito è una schermata dal [Portale classica Azure](https://manage.windowsazure.com) per un blob crittografati mediante la crittografia lato client con una chiave memorizzata nella chiave archivio. La proprietà **ID chiave** è l'URI per la chiave nell'archivio di chiave che funge dalla KEK. La proprietà **EncryptedKey** contiene la versione crittografata della CEK.

![Schermata che mostra metadati Blob che includono i metadati di crittografia][1]

> [AZURE.NOTE] Se osserva il costruttore BlobEncryptionPolicy, si vedrà che può accettare una chiave e/o un sistema di risoluzione. Tenere presente che questo momento non è possibile utilizzare un sistema di risoluzione per la crittografia perché attualmente non supportano una chiave predefinita.



## <a name="decrypt-blob-and-download"></a>Decrittografare blob e scaricare
Decrittografia è quando è stata possano utilizzando le classi del Risolutore. L'ID della chiave utilizzata per la crittografia è associata al blob nei relativi metadati, in modo che non è possibile recuperare la chiave, tenendo presente che l'associazione tra chiave e blob. È sufficiente per assicurarsi che la chiave rimane nell'archivio di chiave.   

La chiave privata di una chiave RSA rimane nell'archivio di chiave, in modo che per la decrittografia che venga eseguita, il tasto crittografati dai metadati blob che contiene il CEK viene inviato all'archivio di chiave per la decrittografia.

Aggiungere le operazioni seguenti per decrittografare il blob appena caricati.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Esistono un paio di altri tipi di sistemi di risoluzione per semplificare la gestione delle chiavi, tra cui: AggregateKeyResolver e CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Utilizzare informazioni riservate chiave archivio
Il modo per utilizzare un segreto con la crittografia lato client consiste nell'utilizzare la classe SymmetricKey poiché un segreto è essenzialmente una chiave simmetrica. Tuttavia, come illustrato in precedenza, un segreto nell'archivio di chiave non eseguire il mapping esattamente a un SymmetricKey. Esistono alcuni aspetti da comprendere:


- Il tasto in un SymmetricKey deve essere una lunghezza fissa: 128, 192, 256, 384 o 512 bit.
- Il tasto in un SymmetricKey dovrebbe essere codificato base 64.
- Un segreto archivio chiave che verrà utilizzato come un SymmetricKey deve disporre di un tipo di contenuto di "application/octet-stream" nell'archivio di chiave.

Ecco un esempio di PowerShell di creazione di un segreto nell'archivio di chiave che può essere utilizzato come un SymmetricKey.
Nota: Il valore hardcoded $key, è solo a scopo di dimostrazione. Nel codice si desidera generare questo tasto.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Nell'applicazione console, è possibile utilizzare la stessa chiamata come prima per recuperare il segreto come un SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Questo è tutto. Buona!

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'uso dello spazio di archiviazione di Microsoft Azure con c#, vedere [Libreria Client di Microsoft Azure lo spazio di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Per ulteriori informazioni sull'API REST Blob, vedere [API REST servizio Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Per informazioni aggiornate sullo spazio di archiviazione di Microsoft Azure, visitare il [Blog del Team di Microsoft Azure lo spazio di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
