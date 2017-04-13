<properties
    pageTitle="Gestire l'accesso anonimo di lettura per i contenitori e BLOB | Microsoft Azure"
    description="Informazioni su come rendere disponibili per l'accesso anonimo contenitori ed BLOB e sulle modalità di accesso a livello di programmazione."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gestire l'accesso anonimo di lettura per i contenitori e BLOB

## <a name="overview"></a>Panoramica

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse di archiviazione all'interno di tale account. Per l'archiviazione Blob solo, è possibile impostare le autorizzazioni del contenitore per consentire l'accesso in lettura anonimo il contenitore e il relativo BLOB in modo che è possibile concedere l'accesso alle risorse senza condividere la chiave account.

Accesso anonimo più adatto per scenari in cui si desidera determinati BLOB in modo che sia sempre disponibile per l'accesso anonimo di lettura. Per un controllo specifico, è possibile creare una firma di accesso condiviso, che consente l'accesso delegato con restrizioni con autorizzazioni diverse e in un intervallo di tempo specificato. Per ulteriori informazioni sulla creazione di firme accesso condiviso, vedere [Utilizzo condiviso accesso firme (SA)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Concedere autorizzazioni agli utenti anonimi per i contenitori e BLOB

Per impostazione predefinita, un contenitore e qualsiasi BLOB all'interno possono accedere solo il proprietario dell'account di archiviazione. Per consentire a utenti anonimi autorizzazioni di lettura per un contenitore e il relativo BLOB, è possibile impostare autorizzazioni per i contenitore per consentire l'accesso pubblico. Gli utenti anonimi leggere BLOB in un contenitore accessibile al pubblico senza autenticazione la richiesta.

I contenitori di forniscono le seguenti opzioni per la gestione dell'accesso contenitore:

- **Pubblico lettura accesso completo:** Contenitore e blob dati possono essere letti tramite richiesta anonima. Client possibile enumerare BLOB all'interno del contenitore tramite richiesta anonima, ma non è possibile enumerare contenitori in account di archiviazione.

- **Accesso per BLOB solo in lettura pubblico:** Dati BLOB in questo contenitore possono essere letti tramite richiesta anonima, ma contenitore dati non sono disponibili. Client non è possibile enumerare BLOB all'interno del contenitore tramite richiesta anonima.

- **Accesso in lettura non pubblico:** Dati contenitore e blob possono essere letti da solo il proprietario dell'account.

È possibile impostare le autorizzazioni del contenitore nei modi seguenti:

- Dal [portale di Azure](https://portal.azure.com).
- Livello di programmazione, utilizzando la libreria di client lo spazio di archiviazione o API REST.
- Tramite PowerShell. Per informazioni sull'impostazione di autorizzazioni del contenitore da Azure PowerShell, vedere [Uso di PowerShell Azure con lo spazio di archiviazione di Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Impostazione delle autorizzazioni contenitore dal portale di Azure

Per impostare le autorizzazioni del contenitore dal [Portale di Azure](https://portal.azure.com), procedere come segue:

1. Passare a dashboard per l'account di archiviazione.
2. Selezionare il nome del contenitore dall'elenco. Se si sceglie il nome esposto BLOB nel contenitore selezionato
3. Selezionare **il criterio di accesso** sulla barra degli strumenti.
4. Nel campo **tipo di accesso** selezionare il livello di autorizzazioni desiderato, come illustrato nella figura riportata di seguito.

    ![Finestra di dialogo di metadati contenitore modifica](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Impostazione delle autorizzazioni contenitore a livello di programmazione con .NET

Per impostare le autorizzazioni per un contenitore utilizzando la libreria client .NET, recuperare le autorizzazioni esistenti del contenitore chiamando il metodo **GetPermissions** . Impostare la proprietà **PublicAccess** per l'oggetto **BlobContainerPermissions** restituito dal metodo **GetPermissions** . Infine, chiamare il metodo **SetPermissions** con le autorizzazioni aggiornate.

Nell'esempio seguente imposta le autorizzazioni del contenitore di accesso in lettura pubblica completa. Per impostare le autorizzazioni pubblico accesso in lettura per BLOB solo, impostare la proprietà **PublicAccess** **BlobContainerPublicAccessType.Blob**. Per rimuovere tutte le autorizzazioni per gli utenti anonimi, impostare la proprietà su **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Accedere a contenitori e BLOB in modalità anonima

Un client che accede in modalità anonima contenitori ed BLOB può utilizzare costruttori che non richiedono credenziali. Negli esempi seguenti sono disponibili diversi modi di fare riferimento alle Blob servizio risorse in modalità anonima.

### <a name="create-an-anonymous-client-object"></a>Creare un oggetto client anonimo

È possibile creare un nuovo oggetto client di servizio per l'accesso anonimo fornendo l'endpoint del servizio di Blob per l'account. È inoltre necessario conoscere il nome di un contenitore di tale account è disponibile per l'accesso anonimo.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Fare riferimento a un contenitore in modalità anonima

Se si dispone dell'URL in un contenitore che è disponibile in modalità anonima, è possibile utilizzare per fare riferimento direttamente il contenitore.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Fare riferimento a un blob in modalità anonima

Se si dispone l'URL per un blob che è disponibile per l'accesso anonimo, è possibile fare riferimento blob direttamente tramite l'URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Funzionalità disponibili per gli utenti anonimi

La tabella seguente mostra le operazioni che possono essere chiamate da utenti anonimi quando ACL del contenitore è impostato su Consenti accesso pubblico.

| Operazione resto                                         | Autorizzazione di accesso in lettura pubblica completa | Autorizzazione di accesso in lettura pubblico per BLOB solo |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Contenitori di elenco                                        | Proprietario solo                              | Proprietario solo                                        |
| Creare contenitore                                       | Proprietario solo                              | Proprietario solo                                        |
| Ottenere le proprietà contenitore                               | Tutti                                     | Proprietario solo                                        |
| Ottenere i metadati contenitore                                 | Tutti                                     | Proprietario solo                                        |
| Impostare i metadati contenitore                                 | Proprietario solo                              | Proprietario solo                                        |
| Ottenere contenitore ACL                                      | Proprietario solo                              | Proprietario solo                                        |
| Impostare ACL contenitore                                      | Proprietario solo                              | Proprietario solo                                        |
| Elimina contenitore                                       | Proprietario solo                              | Proprietario solo                                        |
| Elenco BLOB                                             | Tutti                                     | Proprietario solo                                        |
| Inserire Blob                                               | Proprietario solo                              | Proprietario solo                                        |
| Ottenere Blob                                               | Tutti                                     | Tutti                                               |
| Ottenere le proprietà Blob                                    | Tutti                                     | Tutti                                               |
| Impostare le proprietà Blob                                    | Proprietario solo                              | Proprietario solo                                        |
| Ottenere Blob metadati                                      | Tutti                                     | Tutti                                               |
| Impostare Blob metadati                                      | Proprietario solo                              | Proprietario solo                                        |
| Inserire blocco                                              | Proprietario solo                              | Proprietario solo                                        |
| Ottenere l'elenco di blocco (solo blocchi approvati)                 | Tutti                                     | Tutti                                               |
| Ottenere l'elenco di blocco (solo blocchi commii o tutti i blocchi) | Proprietario solo                              | Proprietario solo                                        |
| Inserire l'elenco a blocchi                                         | Proprietario solo                              | Proprietario solo                                        |
| Eliminare Blob                                            | Proprietario solo                              | Proprietario solo                                        |
| Copia di Blob                                              | Proprietario solo                              | Proprietario solo                                        |
| Snapshot Blob                                          | Proprietario solo                              | Proprietario solo                                        |
| Archivio Blob di leasing                                             | Proprietario solo                              | Proprietario solo                                        |
| Inserire una pagina                                               | Proprietario solo                              | Proprietario solo                                        |
| Ottenere gli intervalli di pagine                                        | Tutti                                     | Tutti                                                  |
| Accodare Blob                                            | Proprietario solo                              | Proprietario solo                                                  |


## <a name="see-also"></a>Vedere anche

- [Autenticazione per i servizi di archiviazione Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Uso delle firme accesso condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md)
- [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/ee395415.aspx)
