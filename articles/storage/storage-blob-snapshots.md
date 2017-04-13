<properties
    pageTitle="Creare uno snapshot di sola lettura di un blob | Microsoft Azure"
    description="Informazioni su come creare uno snapshot di un blob per eseguire il backup dei dati blob in un determinato momento. Informazioni su come vengono addebitate istantanee e sul loro utilizzo per ridurre a icona in base alle tariffe capacità."
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

# <a name="create-a-blob-snapshot"></a>Creare uno snapshot blob

## <a name="overview"></a>Panoramica

Uno snapshot è una versione di sola lettura di un blob che viene eseguita in un punto nel tempo. Snapshot sono utili per il backup dei BLOB. Dopo aver creato uno snapshot, leggere, copiare o eliminarlo, ma non modificarlo.

Uno snapshot di un blob corrisponde alla relativa blob di base, ad eccezione del fatto che blob URI è contenuto un valore **DateTime** aggiunto al blob URI per indicare il momento in cui istantanea. Ad esempio una pagina blob URI è `http://storagesample.core.blob.windows.net/mydrives/myvhd`, snapshot URI è simile a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Tutte le istantanee condividono URI del blob base. L'unica distinzione tra blob base e lo snapshot è il valore **DateTime** aggiunto.

Un blob può contenere più di snapshot. Snapshot persistono fino a quando non vengono eliminati in modo esplicito. Uno snapshot non è possibile outlive il blob di base. È possibile enumerare le istantanee associate il blob di base per tenere traccia degli snapshot correnti.

Quando si crea un'istantanea del blob, proprietà-sistema di blob vengono copiate snapshot con gli stessi valori. Metadati di base blob viene copiato anche a snapshot, a meno che non si specificare i metadati separato per lo snapshot quando si crea.

Qualsiasi leasing associato blob base non influiscono snapshot. Non è possibile acquistare un leasing su uno snapshot.

## <a name="create-a-snapshot"></a>Creare uno snapshot

Nell'esempio seguente viene illustrato come creare uno snapshot in .NET. In questo esempio specifica metadati separata per lo snapshot quando viene creato.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Copiare istantanee

Operazioni di copia riguardanti BLOB e istantanee seguono queste regole:

- È possibile copiare uno snapshot sopra il blob di base. Per promuovere uno snapshot alla posizione del blob base, è possibile ripristinare una versione precedente di un blob. Rimane snapshot, ma la base blob viene sovrascritta con una copia modificabile dello snapshot.

- È possibile copiare uno snapshot in un blob di destinazione con un nome diverso. Il blob di destinazione risultante è un blob scrivibile e non uno snapshot.

- Quando un blob di origine viene copiato, snapshot del blob origine non vengono copiati alla destinazione. Quando un blob di destinazione viene sovrascritta con una copia, snapshot associato blob destinazione originale rimarrà invariato.

- Quando si crea un'istantanea del blob blocco, elenco a blocchi approvata del blob viene copiata anche allo snapshot. Tutti i blocchi non salvati non vengono copiati.

## <a name="specify-an-access-condition"></a>Specificare una condizione di access

È possibile specificare una condizione di accesso in modo che lo snapshot viene creato solo se viene soddisfatta una condizione. Per specificare una condizione di access, utilizzare la proprietà **AccessCondition** . Se non è soddisfatta la condizione specificata, lo snapshot non è stato creato e il servizio Blob restituisce il codice di stato HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Eliminare istantanee

Non è possibile eliminare un blob con snapshot a meno che non vengono eliminati anche gli snapshot. È possibile eliminare uno snapshot singolarmente o consente di eliminare tutte le istantanee quando il blob di origine viene eliminato. Se si tenta di eliminare un blob che è ancora disponibili istantanee, verrà generato un errore.

Nell'esempio seguente viene illustrato come eliminare un blob e relativi snapshot in .NET, in cui `blockBlob` è una variabile di tipo **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Snapshot con lo spazio di archiviazione di Azure Premium

Con snapshot completamento dello spazio di archiviazione Premium queste regole:

- Il numero massimo di istantanee per blob di pagine in un account di archiviazione premium è 100. Se si supera questo limite, l'operazione Snapshot Blob restituisce il codice di errore 409 (**SnapshotCountExceeded**).

- È possibile creare uno snapshot di un blob di pagine in un account di archiviazione premium ogni 10 minuti. Se si supera la velocità, l'operazione Snapshot Blob restituisce il codice di errore 409 (**SnaphotOperationRateExceeded**).

- Non è possibile chiamare ottenere Blob per leggere uno snapshot di un blob di pagine in un account di archiviazione premium. La chiamata ottenere Blob su uno snapshot in un account di archiviazione premium restituisce il codice di errore 400 (**InvalidOperation**). Tuttavia, è possibile chiamare Ottieni proprietà Blob e ottenere metadati Blob rispetto a uno snapshot in un account di archiviazione premium.

- Per leggere uno snapshot, è possibile utilizzare l'operazione copia Blob per copiare uno snapshot in un'altra blob di pagina nella finestra account. Il blob di destinazione per copiare il file non deve avere una snapshot esistenti. Nel caso blob destinazione istantanee, l'operazione di copia Blob restituisce il codice di errore 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Ottenere l'URI assoluto a uno snapshot

In questo esempio di codice c# crea uno snapshot e scrive URI assoluto per la posizione principale.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Comprendere l'attribuzione dei costi istantanee

Creazione di un'istantanea, è una copia di sola lettura di un blob, causando in base alle tariffe lo spazio di archiviazione dati aggiuntivi al proprio account. Quando si progetta l'applicazione, è importante tenere presente potrebbero attribuzione le spese in modo che è possibile ridurre i costi non necessari.

### <a name="important-billing-considerations"></a>Importanti considerazioni sulla fatturazione

L'elenco seguente include punti più importanti da considerare quando si creano uno snapshot.

- Account di archiviazione di si comporta le spese per blocchi univoci o pagine, che sono nel blob o dello snapshot. L'account non è soggetta alle spese aggiuntive per istantanee associate a un blob finché non si aggiorna il blob in cui sono basati. Dopo aver aggiornato il blob di base, essa si discostino dal relativi snapshot. In questo caso, addebitate per le pagine in ogni blob o uno snapshot blocchi univoci.

- Quando si sostituisce un blocco all'interno di un blob di blocco, tale blocco viene addebitato successivamente come blocco univoco. Ciò avviene anche se il blocco ha lo stesso blocco ID e gli stessi dati in quanto non dispone dello snapshot. Dopo il blocco viene eseguito il commit nuovamente, diversamente dalla corrispondente in qualsiasi snapshot e viene addebitata per i propri dati. Lo stesso vale per una pagina in un blob di pagina che viene aggiornato con dati identici.

- Sostituzione di un blob blocco chiamando il metodo **UploadFile**, **UploadText**, **UploadStream**o **UploadByteArray** sostituisce tutti i blocchi nel blob. Se si dispone di uno snapshot associato a tale blob, tutti i blocchi nella base blob e snapshot differenziare ora e verrà addebitato per tutti i blocchi in entrambi BLOB. Ciò avviene anche se i dati in base blob e lo snapshot restano identici.

- Il servizio Blob Azure non dispone di un metodo per determinare se due blocchi contengano dati identici. Ogni blocco e su cui verrà caricato approvata viene considerato come univoco, anche se ha gli stessi dati e lo stesso ID di blocco. Poiché le spese accumulano per blocchi univoci, è importante tenere presente che l'aggiornamento di un blob che contiene un risultati snapshot in altri blocchi univoci e costi aggiuntivi.

> [AZURE.NOTE] Procedure consigliate determinano gestire istantanee con attenzione per evitare i costi aggiuntivi. È consigliabile gestire snapshot nel modo seguente:

> - Eliminare e ricreare istantanee associate a un blob ogni volta che si aggiorna il blob, anche se si sta aggiornando con dati identici, a meno che non la progettazione dell'applicazione è necessario mantenere istantanee. Eliminare e ricreare snapshot del blob, è possibile assicurarsi che il blob e snapshot non differenziare.

> - Se si mantiene snapshot per un blob, evitare di chiamare **UploadFile**, **UploadText**, **UploadStream**o **UploadByteArray** per aggiornare il blob. I metodi sostituiscono tutto dei blocchi in blob, in modo che il blob di base e istantanee differenziare in modo significativo. Se, tuttavia, aggiornare il minor numero possibile di blocchi utilizzando i metodi **PutBlock** e **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Snapshot gli scenari di fatturazione


Gli scenari seguenti dimostrano l'attribuzione dei costi per un blob di blocco e relativi snapshot.

Nello scenario 1 blob base non è stato aggiornato dopo della creazione dello snapshot, in modo che in base alle tariffe addebitate solo per blocchi univoci 1, 2 e 3.

![Risorse di archiviazione Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Lo scenario 2 blob base è stato aggiornato ma non lo snapshot. 3 blocco è stato aggiornato e anche se contiene gli stessi dati e lo stesso ID, non è lo stesso blocco 3 dello snapshot. Di conseguenza, l'account verrà addebitata quattro blocchi.

![Risorse di archiviazione Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Nello scenario 3 blob base è stato aggiornato ma non lo snapshot. Blocco 3 è stata sostituita con blocco 4 BLOB base, ma lo snapshot riflette ancora blocco 3. Di conseguenza, l'account verrà addebitata quattro blocchi.

![Risorse di archiviazione Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

In uno scenario di 4, blob base è stata completamente aggiornato e sono presenti nessuno dei relativi blocchi originali. Di conseguenza, l'account viene addebitata per tutti i blocchi univoci otto. Questo scenario può verificarsi se si utilizza un metodo di aggiornamento, ad esempio **UploadFile**, **UploadText**, **UploadFromStream**o **UploadByteArray**, perché questi metodi sostituiscono tutto il contenuto di un blob.

![Risorse di archiviazione Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori esempi sull'utilizzo di archiviazione Blob, vedere [Gli esempi di codice Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). È possibile scaricare un'applicazione di esempio ed eseguirlo o esplorare il codice in GitHub. 
