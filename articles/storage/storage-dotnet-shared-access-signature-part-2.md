<properties
    pageTitle="Creare e utilizzare un sa con archiviazione Blob | Microsoft Azure"
    description="In questa esercitazione viene illustrato come creare firme di accesso condiviso per l'utilizzo con archiviazione Blob e come utilizzarli dalle applicazioni client."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Condividere le firme di Access, parte 2: Creare e utilizzare un sa con archiviazione Blob

## <a name="overview"></a>Panoramica

[Parte 1](storage-dotnet-shared-access-signature-part-1.md) di questa esercitazione sono stata illustrata condiviso accesso firme (SA) e dettagliate sulle procedure consigliate per l'uso di loro. Parte 2 viene illustrato come generare e quindi utilizzare le firme di accesso condiviso con archiviazione Blob. Gli esempi scritto in c# e utilizzano la libreria di Client lo spazio di archiviazione di Azure per .NET. Gli scenari coperti includono questi aspetti dell'utilizzo di firme accesso condiviso:

- Generazione di una firma di accesso condiviso in un contenitore
- Generazione di una firma di accesso condiviso in un blob
- Creazione di un criterio di accesso archiviate per gestire le firme su risorse del contenitore
- Test firme accesso condiviso da un'applicazione client

## <a name="about-this-tutorial"></a>Su questa esercitazione

In questa esercitazione viene illustrato come creare firme di accesso condiviso per i contenitori e BLOB creando due applicazioni console. L'applicazione console prima genera firme accesso condiviso in un contenitore e in un blob. Conoscenza delle chiavi account archiviazione questa applicazione. La seconda applicazione console che verrà utilizzato come applicazione client, accede contenitore e le risorse blob tramite le firme di accesso condiviso create con la prima applicazione. Questa applicazione utilizza le firme di accesso condiviso solo per autenticare l'accesso al contenitore e blob risorse - non è conoscenza delle chiavi account.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Creare un'applicazione Console per generare firme accesso condiviso

Prima di tutto, verificare di disporre la libreria di Client lo spazio di archiviazione di Azure per .NET installato. È possibile installare il [pacchetto NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "pacchetto NuGet") contenente assembly più aggiornate per libreria client. Questo è il metodo consigliato per verificare di disporre le correzioni più recenti. È anche possibile scaricare la libreria client come parte della versione più recente di [Azure SDK per .NET](https://azure.microsoft.com/downloads/).

In Visual Studio, creare una nuova applicazione console di Windows e il nome **GenerateSharedAccessSignatures**. Aggiungere riferimenti a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, utilizzando uno dei metodi seguenti:

-   Se si desidera installare il pacchetto NuGet, installare il [NuGet Client](https://docs.nuget.org/consume/installing-nuget). In Visual Studio, selezionare **progetto | Gestire pacchetti NuGet**, eseguire una ricerca online per **l'Archiviazione di Azure**e seguire le istruzioni per installare.
-   In alternativa, individuare l'assembly nell'installazione di Azure SDK e aggiungere riferimenti a essi.

Nella parte superiore del file Program.cs, aggiungere le istruzioni **using** seguenti:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Modificare il file App in modo che contenga un'impostazione di configurazione con una stringa di connessione che punti al proprio account di archiviazione. Il file app dovrebbe essere simile alla seguente:

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Generare una firma di accesso condiviso URI per un contenitore

Per iniziare, verrà aggiunto un metodo per generare una firma di accesso condiviso in un nuovo contenitore. In questo caso la firma non è associata a un criterio di accesso archiviate, in modo che in essa contenute sull'URI le informazioni che indica l'ora di scadenza e le autorizzazioni concesse.

Prima di tutto, aggiungere codice al metodo **Main ()** per eseguire l'autenticazione di accesso al proprio account di archiviazione e creare un nuovo contenitore:

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Aggiungere un nuovo metodo che genera la firma di accesso condiviso per il contenitore e restituisce la firma URI:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Aggiungere le seguenti righe nella parte inferiore del metodo **Main ()** , prima di chiamare **Console.ReadLine()**per chiamare **GetContainerSasUri()** e scrivere la firma URI nella finestra della console:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilare ed eseguire per generare l'output della firma accesso condiviso URI per il nuovo contenitore. URI sarà simile alla seguente URI:       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Dopo aver eseguito il codice, la firma di accesso condiviso che è stato creato per il contenitore sarà più valida per la successive 24 ore. La firma l'autorizzazione un client a BLOB elenco nel contenitore e come scrivere un blob di nuovo al contenitore.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generare una firma di accesso condiviso URI per un Blob

Successivamente, creiamo codice simile per creare un nuovo blob all'interno del contenitore e generare una firma di accesso condiviso. Questa firma accesso condiviso non è associata a un criterio di accesso archiviate, in modo da includere l'ora di inizio, ora di scadenza e le informazioni sulle autorizzazioni sull'URI.

Aggiungere un nuovo metodo che crea un nuovo blob e scrivere testo su di esso, quindi genera una firma di accesso condiviso e restituisce la firma URI:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Nella parte inferiore del metodo **Main ()** , aggiungere le seguenti righe per chiamare **GetBlobSasUri()**prima la chiamata a **Console.ReadLine()**e scrivere la firma di accesso condiviso URI nella finestra della console:    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compilare ed eseguire per la firma di accesso condiviso URI per il nuovo blob di output. URI sarà simile alla seguente URI:

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Creare un criterio di accesso archiviate nel contenitore di

A questo punto creare un criterio di accesso archiviate nel contenitore, in modo da determinare i vincoli per le firme di accesso condiviso che esso associati.

Negli esempi precedenti sono specificati l'ora di inizio, in modo implicito o in modo esplicito, l'ora di scadenza e le autorizzazioni sulla firma accesso condiviso URI stesso. Negli esempi seguenti viene specificare questi sul criterio di accesso archiviato e non sulla firma accesso condiviso. In questo modo ci consente di modificare questi vincoli senza emettere nuovamente la firma di accesso condiviso.

È possibile avere uno o più dei vincoli sulla firma accesso condiviso e il resto sul criterio di accesso archiviate. Tuttavia, è possibile specificare solo l'ora di inizio, ora di scadenza e le autorizzazioni in un'unica posizione o altro. è ad esempio, non è possibile specificare autorizzazioni per la firma di accesso condiviso e anche specificarli sul criterio di accesso archiviate.

Si noti che quando si aggiunge un criterio di accesso a un contenitore, è necessario ottenere le autorizzazioni esistenti del contenitore, aggiungere il nuovo criterio di accesso e quindi impostare le autorizzazioni del contenitore.

Aggiungere un nuovo metodo che crea un nuovo criterio di accesso archiviate in un contenitore e restituisce il nome del criterio:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Nella parte inferiore del metodo **Main ()** , prima di chiamare **Console.ReadLine()**, aggiungere le seguenti linee chiaro prima eventuali criteri di accesso e quindi il metodo **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Si noti che quando si cancellano i criteri di accesso in un contenitore, è necessario innanzitutto ottenere le autorizzazioni esistenti del contenitore, quindi deselezionare le autorizzazioni, quindi impostare le autorizzazioni di nuovo.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generare una firma di accesso condiviso URI sul contenitore che utilizza un criterio di accesso

Successivamente, è necessario creare un'altra firma accesso condiviso sul contenitore creata in precedenza, ma questa volta che è necessario associare alla firma con il criterio di accesso creata nell'esempio precedente.

Aggiungere un nuovo metodo per generare un'altra firma accesso condiviso sul contenitore:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Nella parte inferiore del metodo **Main ()** , prima di chiamare **Console.ReadLine()**, aggiungere le seguenti righe per chiamare il metodo **GetContainerSasUriWithPolicy** :

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generare una firma di accesso condiviso URI nel Blob che utilizza un criterio di accesso

Infine, verranno aggiunte un metodo simile per creare un'altra blob e generare una firma di accesso condiviso associato a un criterio di accesso.

Aggiungere un nuovo metodo per creare un blob e generare una firma di accesso condiviso:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Nella parte inferiore del metodo **Main ()** , prima di chiamare **Console.ReadLine()**, aggiungere le seguenti righe per chiamare il metodo **GetBlobSasUriWithPolicy** :    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Il metodo **Main ()** dovrebbe essere simile interamente. Eseguire per scrivere la firma di accesso condiviso URI nella finestra console, quindi copiarli e incollarli in un file di testo per l'utilizzo nella seconda parte di questa esercitazione.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Quando si esegue l'applicazione di console GenerateSharedAccessSignatures, si noterà output simile al seguente nella finestra della console. Queste sono le firme di accesso condiviso che userà nella parte 2 dell'esercitazione.

![SA-console-output-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Creare un'applicazione Console per verificare le firme di accesso condiviso

Per verificare le firme di accesso condiviso create negli esempi precedenti, è necessario creare una seconda applicazione console che utilizza le firme per eseguire operazioni sul contenitore e in un blob.

> [AZURE.NOTE] Se più di 24 ore sono trascorsi è completata la prima parte dell'esercitazione, le firme creato non sarà non è più valide. In questo caso, eseguire il codice nell'applicazione console prima di generare firme aggiornato accesso condiviso per l'utilizzo nella seconda parte dell'esercitazione.

In Visual Studio, creare una nuova applicazione console di Windows e il nome **ConsumeSharedAccessSignatures**. Aggiungere riferimenti a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, come in precedenza.

Nella parte superiore del file Program.cs, aggiungere le istruzioni **using** seguenti:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Nel corpo del metodo **Main ()** , aggiungere le seguenti costanti e aggiornare i valori per le firme di accesso condiviso generati nella parte 1 dell'esercitazione.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Aggiungere un metodo per eseguire operazioni contenitore utilizzando una firma di accesso condiviso

Successivamente, è necessario aggiungere un metodo testa alcune operazioni contenitore representative utilizzando una firma di accesso condiviso sul contenitore. Si noti che la firma di accesso condiviso viene utilizzata per restituire un riferimento al contenitore di autenticazione dell'accesso al contenitore in base alla firma singolarmente.

Aggiungere il metodo seguente Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Aggiornare il metodo **Main ()** per chiamare **UseContainerSAS()** con entrambe le firme di accesso condiviso che è stato creato per il contenitore:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Aggiungere un metodo per eseguire operazioni di Blob utilizzando una firma di accesso condiviso

Infine, verranno aggiunte un metodo di verifica alcune operazioni blob representative utilizzare una firma di accesso condiviso su blob. In questo caso è utilizzare il costruttore **CloudBlockBlob(String)**, passando alla firma accesso condiviso, per restituire un riferimento a quello. Altri autenticazione non è richiesta; si basa sulla firma da soli.

Aggiungere il metodo seguente Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Aggiornare il metodo **Main ()** per chiamare **UseBlobSAS()** con entrambe le firme di accesso condiviso che è stato creato nel blob:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Eseguire l'applicazione console e osservare l'output per visualizzare le operazioni consentite per le firme. L'output nella finestra della console sarà simile al seguente:

![SA-console-output-2][sas-console-output-2]

## <a name="next-steps"></a>Passaggi successivi

[Condividere le firme di Access, parte 1: Informazioni sul modello di SA](storage-dotnet-shared-access-signature-part-1.md)

[Gestire l'accesso anonimo di lettura per i contenitori e BLOB](storage-manage-access-to-resources.md)

[Delega dell'accesso con una firma di accesso condiviso (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introduzione a tabella e coda SA](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
