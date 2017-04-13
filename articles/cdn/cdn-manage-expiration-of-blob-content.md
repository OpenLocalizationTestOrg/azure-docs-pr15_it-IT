<properties
 pageTitle="Gestire la scadenza del contenuto di blob Azure lo spazio di archiviazione in Azure CDN | Microsoft Azure"
 description="Informazioni sulle opzioni per il controllo time to live per BLOB in Azure CDN memorizzazione nella cache."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Gestire la scadenza del contenuto di blob Azure lo spazio di archiviazione in rete CDN di Azure

> [AZURE.SELECTOR]
- [Azure Web App/Cloud Services ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Servizio di archiviazione blob di Azure](cdn-manage-expiration-of-blob-content.md)

[Blob servizio](../storage/storage-introduction.md#blob-storage) di [Archiviazione Azure](../storage/storage-introduction.md) è una delle diverse origini diverse basate su Azure integrati con Azure CDN.  Per qualsiasi contenuto blob accessibili può memorizzati nella cache in Azure CDN fino allo scadere il time to live (TTL).  Il valore TTL viene determinato in base all' [intestazione *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) nella risposta HTTP dallo spazio di archiviazione Azure.

>[AZURE.TIP] È possibile impostare nessun TTL blob.  In questo caso, Azure CDN applica automaticamente un TTL predefinito di sette giorni.
>
>Per ulteriori informazioni sul funzionamento di Azure CDN per velocizzare l'accesso a BLOB e altri file, vedere la [Panoramica CDN Azure](./cdn-overview.md).
>
>Per ulteriori informazioni sul servizio blob Azure lo spazio di archiviazione, vedere [Concetti relativi al servizio Blob](https://msdn.microsoft.com/library/dd179376.aspx). 

In questa esercitazione vengono illustrate alcune modalità che è possibile impostare il valore TTL su un blob di archiviazione Azure.  

## <a name="azure-powershell"></a>PowerShell Azure

[Azure PowerShell](../powershell-install-configure.md) corrisponde a uno dei modi più veloce ed efficienti per amministrare il provider di servizi Azure.  Utilizzare la `Get-AzureStorageBlob` cmdlet per ottenere un riferimento a blob, quindi impostare la `.ICloudBlob.Properties.CacheControl` proprietà. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] È anche possibile usare PowerShell per [gestire i profili di rete CDN e i punti finali](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Raccolta di Client di archiviazione Azure per .NET

Per impostare TTL del blob utilizzando .NET, utilizzare la [Libreria di Client di spazio di archiviazione di Azure per .NET](../storage/storage-dotnet-how-to-use-blobs.md) per impostare la proprietà [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Sono disponibili molti altri esempi di codice .NET negli [Esempi di spazio di archiviazione Blob Azure per .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Altri metodi

- [Azure interfaccia della riga di comando](../xplat-cli-install.md)

    Quando si caricano i blob, è possibile impostare la proprietà *cacheControl* utilizzando il `-p` passare.  In questo esempio imposta il valore TTL a un'ora (3600 secondi).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Impostare in modo esplicito la proprietà *x-ms-blob-controllo della cache* in una richiesta di [Inserire Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Inserire elenco a blocchi](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)o [Impostare proprietà Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Strumenti di gestione di archiviazione di terze parti

    Alcuni strumenti di gestione di archiviazione Azure di terze parti consentono di impostare la proprietà *CacheControl* BLOB. 

## <a name="testing-the-cache-control-header"></a>Test intestazione *Cache-Control*

Si può verificare la durata del BLOB.  Utilizzo del browser [gli strumenti di sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), verificare che il blob è inclusa l'intestazione di risposta di *Controllo della Cache* .  È anche possibile usare uno strumento come **wget**, [Postman](https://www.getpostman.com/)o [Fiddler](http://www.telerik.com/fiddler) per esaminare le intestazioni di risposta.

## <a name="next-steps"></a>Passaggi successivi

- [Leggere le informazioni relative a intestazione *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Informazioni su come gestire la scadenza del contenuto di servizio Cloud in rete CDN di Azure](./cdn-manage-expiration-of-cloud-service-content.md)

