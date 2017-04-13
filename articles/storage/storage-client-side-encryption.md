<properties
    pageTitle="Crittografia lato client con .NET per lo spazio di archiviazione di Microsoft Azure | Microsoft Azure"
    description="La raccolta di Client Azure lo spazio di archiviazione per .NET supporta la crittografia lato client e l'integrazione con Azure chiave archivio per la massima protezione per le applicazioni di archiviazione Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>La crittografia lato client e Azure archivio chiave per lo spazio di archiviazione di Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Panoramica

La [Libreria di Client di spazio di archiviazione di Azure per pacchetto Nuget .NET](https://www.nuget.org/packages/WindowsAzure.Storage) supporta la crittografia dei dati nelle applicazioni client prima di caricare allo spazio di archiviazione di Azure e decrittografare i dati durante il download al client. La raccolta supporta anche l'integrazione con [Azure chiave archivio](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi account lo spazio di archiviazione.

Per un'esercitazione dettagliata che illustra il processo di crittografia tramite la crittografia lato client e Azure chiave archivio BLOB, vedere [crittografare e decrittografare BLOB di archiviazione di Microsoft Azure utilizzando Azure chiave archivio](storage-encrypt-decrypt-blobs-key-vault.md).

Per la crittografia lato client con Java, vedere [Crittografia lato Client con Java per lo spazio di archiviazione di Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Crittografia e decrittografia tramite tecnica busta

I processi di crittografia e decrittografia seguono tecnica della busta.

### <a name="encryption-via-the-envelope-technique"></a>Crittografia tramite tecnica busta

La crittografia tramite tecnica busta funziona nel modo seguente:

1. La raccolta di client di archiviazione Azure genera una chiave di crittografia contenuto (CEK), ovvero una chiave simmetrica un utilizzo.
2. Dati utente sono crittografati tramite questo CEK.
3. il CEK quindi va a capo (crittografati) utilizzando la chiave di crittografia (KEK). Il KEK è identificato da un identificatore di chiave e possono essere una coppia di chiavi asimmetrica o con un tasto simmetrico e possono essere gestiti a livello locale o memorizzati negli archivi chiave Azure.

    La stessa libreria di client lo spazio di archiviazione non ha accesso a KEK. La raccolta richiama l'algoritmo di ritorno a capo chiave fornito dall'archivio di chiave. Gli utenti possono scegliere di utilizzare provider personalizzati per ritorno a capo/unwrapping chiave bene accolta.

4. I dati crittografati viene quindi caricati al servizio di archiviazione Azure. Il tasto insieme alcuni metadati crittografia aggiuntiva archiviato come metadati (in un blob) o interpolazione con i dati crittografati (messaggi coda ed entità della tabella).

### <a name="decryption-via-the-envelope-technique"></a>Decrittografia tramite tecnica busta

Decrittografia tramite tecnica busta funziona nel modo seguente:

1. La libreria client presuppone che l'utente gestisce la chiave di crittografia (KEK) locale o in Azure chiave archivi. L'utente non è necessario conoscere la chiave specifica utilizzati per la crittografia. Se, tuttavia, è possibile configurare e utilizzare un sistema di risoluzione chiave che risolve diversi identificatori chiavi ai tasti.
2. La libreria client scarica i dati crittografati insieme a qualsiasi materiale di crittografia che è archiviato il servizio.
3. La chiave di crittografia contenuto incluso (CEK) è estratta (decrittografare) con la chiave crittografia chiave (KEK). Di seguito nuovamente la libreria client non dispone dell'accesso a KEK. Richiama semplicemente algoritmo di wrapping archivio chiave del provider del servizio o personalizzate.
4. La chiave di crittografia del contenuto (CEK) viene quindi utilizzata per decrittografare i dati utente crittografato.

## <a name="encryption-mechanism"></a>Meccanismo di crittografia

La libreria di client lo spazio di archiviazione utilizza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) per crittografare i dati degli utenti. In particolare, modalità di [Concatenazione blocco crittografia (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. Ogni servizio works in modo diverso, in modo che verranno illustrate ciascuno di essi qui.

### <a name="blobs"></a>BLOB

La raccolta di client attualmente supporta la crittografia dell'intero BLOB solo. In particolare, supporta la crittografia quando gli utenti utilizzano **UploadFrom** * metodi o * *OpenWrite** metodo. Per download, completate e download intervallo sono supportati.

Durante la crittografia, la libreria client verrà generare un vettore casuale inizializzazione (IV) di 16 byte, insieme a una chiave di crittografia contenuto casuale (CEK) di 32 byte ed eseguire la crittografia busta dei dati blob utilizzando queste informazioni. Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi archiviati come blob metadati insieme blob crittografato il servizio.

> [AZURE.WARNING] Se si modificano o durante il caricamento dei metadati personalizzata per il blob, è necessario assicurarsi che questi metadati vengono mantenuti. Se si carica nuovi metadati senza metadati, incluso CEK, IV e altri metadati verranno persi e il contenuto di blob non possano essere recuperato.

Download blob crittografato implica il recupero del contenuto dell'intero blob utilizzando **DownloadTo***/**BlobReadStream** metodi pratici. CEK incluso è aperto e utilizzata insieme a IV (archiviato in questo caso come blob metadati) per restituire i dati decrittografare agli utenti.

Scaricare un intervallo non autorizzato (**DownloadRange*** metodi) nel blob crittografato a altro comporta il regolare l'intervallo inviato dagli utenti per ottenere una piccola quantità di dati aggiuntivi che possono essere usati per decrittografare correttamente l'intervallo richiesto.

Tutti blob tipi (blocco BLOB, pagina BLOB e accoda BLOB) possono crittografare/decrittografare utilizzando questo schema.

### <a name="queues"></a>Code

Poiché i messaggi possono essere di qualsiasi formato, la libreria client definisce un formato personalizzato che include il vettore di inizializzazione (IV) e la chiave di crittografia contenuto crittografato (CEK) del testo del messaggio.

Durante la crittografia, la raccolta di client genera un IV casuali di 16 byte insieme a un casuali CEK 32 byte ed esegue la crittografia del testo del messaggio coda utilizzando le informazioni della busta. Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi aggiunti al messaggio crittografato coda. Questo messaggio modificato (mostrato sotto) viene memorizzato il servizio.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante la decrittografia, inclusa chiave estratte dal messaggio coda ed estratta. Il IV viene anche estratta dal messaggio di coda e utilizzato insieme al tasto estratta per decrittografare i dati del messaggio coda. Si noti che i metadati di crittografia small (in 500 byte), in modo mentre contare verso il limite di 64KB per un messaggio di coda, l'impatto dovrebbe essere gestibile.

### <a name="tables"></a>Tabelle

La raccolta di client supporta la crittografia delle proprietà di entità per l'inserimento e quelle di sostituzione.

>[AZURE.NOTE] Unione non è attualmente supportato. Poiché un sottoinsieme di proprietà è stato crittografato in precedenza utilizzando una chiave diversa, è sufficiente unione le nuove proprietà e aggiornare i metadati genereranno la perdita di dati. Unione di uno richiede chiamate a servizi aggiuntivi per leggere l'entità esistente dal servizio o con una nuova chiave ogni proprietà, che non sono adatti per migliorare le prestazioni.

La crittografia dei dati di tabella funziona nel modo seguente:  

1. Gli utenti specificare le proprietà di crittografare.
2. La libreria client genera un vettore casuale inizializzazione (IV) di 16 byte insieme una chiave di crittografia contenuto casuale (CEK) di 32 byte per ogni entità ed esegue la crittografia busta in singole proprietà crittografare mediante la derivazione di un nuovo IV ogni proprietà. La proprietà crittografata verrà archiviata come dati binari.
3. CEK capo e alcuni metadati crittografia aggiuntive vengono quindi archiviati come due proprietà riservata aggiuntive. La prima proprietà riservata (_ClientEncryptionMetadata1) è una proprietà stringa che contiene le informazioni relative a IV, versione e il tasto. La proprietà secondo riservata (_ClientEncryptionMetadata2) è una proprietà binaria che contiene le informazioni sulle proprietà che vengono crittografati. Le informazioni contenute in questa proprietà seconda (_ClientEncryptionMetadata2) sono crittografata.
4. A causa di queste proprietà aggiuntive riservate necessari per la crittografia, gli utenti possono hanno ora solo 250 proprietà personalizzate anziché 252. Le dimensioni totali dell'entità devono essere inferiore a 1 MB.

Si noti che è possibile crittografare solo proprietà stringa. Se altri tipi di proprietà devono essere crittografati, devono essere convertiti in stringhe. Le stringhe crittografate vengono memorizzate il servizio come proprietà binaria e vengono convertiti al stringhe dopo la decrittografia.

Per le tabelle, oltre ai criteri di crittografia, gli utenti devono specificare le proprietà di crittografare. Ciò è possibile specificando un attributo [EncryptProperty] (per entità POCO che derivano da TableEntity) o un sistema di risoluzione crittografia nelle opzioni di richiesta. Una risoluzione di crittografia è un delegato che accetta una chiave di partizione, chiave di riga e il nome di proprietà e restituisce un valore Boolean che indica se è necessario crittografare tale proprietà. Durante la crittografia, la libreria client utilizzerà queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura nel collegamento. Il delegato vengono forniti anche la possibilità di logica intorno la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografare proprietà A; in caso contrario Crittografa proprietà A e B.) Si noti che non è necessario fornire queste informazioni durante la lettura o query su entità.

### <a name="batch-operations"></a>Operazioni batch

Le operazioni di batch KEK stesso verrà utilizzato per tutte le righe di tale operazione batch poiché la libreria client consente solo un oggetto di opzioni (e pertanto un criterio/KEK) per operazione batch. Tuttavia, la raccolta di client internamente genererà un nuovo IV casuali e CEK casuale per ogni riga nel batch. Gli utenti possono anche scegliere di crittografare proprietà diverse per ogni operazione nel batch di mediante la definizione di questo comportamento in risoluzione crittografia.

### <a name="queries"></a>Query

Per eseguire operazioni di query, è necessario specificare un sistema di risoluzione chiave che è possibile risolvere tutti i tasti di set di risultati. Se non è possibile risolvere un'entità contenuta nel risultato della query a un provider, la raccolta di client verrà generato un errore. Per le query che esegue previsioni sul lato server, la libreria client aggiungerà le proprietà dei metadati di crittografia speciale (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) per impostazione predefinita per le colonne selezionate.

## <a name="azure-key-vault"></a>Archivio chiave Azure

Archivio chiave Azure consente di crittografia per proteggere e informazioni riservate utilizzati da cloud applicazioni e servizi. Utilizzando Azure chiave archivio, gli utenti possano crittografare chiavi e informazioni riservate (ad esempio chiavi di autenticazione, lo spazio di archiviazione account chiavi, le chiavi di crittografia di dati. File PFX e la password) utilizzando i tasti che sono protetti tramite moduli di protezione hardware (HSM). Per ulteriori informazioni, vedere [Novità Azure chiave archivio?](../key-vault/key-vault-whatis.md).

La libreria di client lo spazio di archiviazione utilizza la libreria di base di archivio di chiave per fornire un quadro comune tra Azure per la gestione delle chiavi. Gli utenti che si verifichino il vantaggio di utilizzo della libreria di estensioni chiave archivio. La libreria di estensioni offre funzionalità utili intorno semplice e continua simmetrico (Symmetric) / RSA locale e cloud chiave provider oltre che con l'aggregazione e la memorizzazione nella cache.

### <a name="interface-and-dependencies"></a>Interfaccia e dallo stesso dipendenze

Esistono tre pacchetti di archivio di chiave:

- Microsoft.Azure.KeyVault.Core contiene la IKey e IKeyResolver. È un pacchetto small senza dipendenze. La raccolta di client lo spazio di archiviazione per .NET definisce come dipendenza.
- Microsoft.Azure.KeyVault contiene il client di chiave archivio resto.
- Codice di estensione che include implementazioni di algoritmi e un RSAKey e un SymmetricKey Microsoft.Azure.KeyVault.Extensions. IT dipende gli spazi dei nomi di base e KeyVault e consente di definire un sistema di risoluzione aggregata (quando gli utenti desiderano utilizzare più provider chiave) e un tasto Risolutore. Anche se la raccolta di client lo spazio di archiviazione non dipende direttamente il pacchetto se gli utenti desiderano utilizzare Azure chiave archivio per archiviare le chiavi o usare le estensioni di archivio di chiave per utilizzare locale e cloud cryptographic provider, occorrerà il pacchetto.

Archivio di chiave è progettata per valore elevato schema chiavi, e limiti di limitazioni per singolo archivio di chiave sono il presente. Quando si esegue la crittografia lato client con l'archivio di chiave, il modello preferito consiste nell'usare simmetriche chiavi master archiviate come informazioni riservate nell'archivio di chiave e memorizzati nella cache locale. Gli utenti devono eseguire le operazioni seguenti:

1. Creare un segreto offline e caricare in archivio chiave.
2. Utilizzare identificatore di base del segreto come parametro per risolvere la versione corrente del segreto per la crittografia e memorizzare nella cache le informazioni in locale. Utilizzare CachingKeyResolver per la memorizzazione nella cache; gli utenti non sono previsti a implementare le proprie memorizzazione nella cache logica.
3. Utilizzare il Risolutore come input durante la creazione di criteri di crittografia.

Sono disponibili ulteriori informazioni sull'utilizzo di archivio di chiave negli [esempi di codice la crittografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Procedure consigliate

Supporto per la crittografia è disponibile solo nella libreria di client lo spazio di archiviazione per .NET. Windows Phone e Windows Runtime attualmente non supporta la crittografia.

>[AZURE.IMPORTANT] Prestare particolare attenzione questi punti importanti quando si usa la crittografia lato client:
>
>- Durante la lettura o la scrittura di blob crittografato, usare i comandi Carica intero blob e intervallo/intero blob download comandi. Evitare di scrivere nella blob crittografato tramite operazioni di protocollo, ad esempio blocco mettere, inserire elenco a blocchi, scrivere pagine, Cancella pagine o accodare blocco; in caso contrario è possibile danneggiata blob crittografato e renderlo più leggibile.
>- Per le tabelle, è presente un vincolo simile. Prestare attenzione a non vengono aggiornate le proprietà crittografate senza aggiornare i metadati di crittografia.
>- Se si impostano metadati blob crittografato, si potrebbero sovrascrivere i metadati relativi a crittografia necessari per la decrittografia, in quanto l'impostazione di metadati non additivo. Questo vale anche per snapshot; evitare di specificare i metadati durante la creazione di un'istantanea del blob crittografato. Se è necessario configurare i metadati, assicurarsi di chiamare il metodo **FetchAttributes** per ottenere i metadati di crittografia corrente ed evitare scrive simultanee durante l'impostazione di metadati.
>- Attivare la proprietà **RequireEncryption** nelle opzioni di richiesta predefinito per gli utenti che devono funzionano solo con i dati crittografati. Per ulteriori informazioni, vedere di seguito.


## <a name="client-api--interface"></a>API client / interfaccia

Durante la creazione di un oggetto EncryptionPolicy, gli utenti possono fornire solo un tasto (implementazione IKey), solo un sistema di risoluzione (implementazione di IKeyResolver) o entrambe. IKey è il tipo di chiave base identificato tramite un identificatore di chiave e che fornisce la logica per/unwrapping ritorno a capo. IKeyResolver viene utilizzato per risolvere un tasto durante il processo di decrittografia. Definisce un metodo ResolveKey che restituisce un IKey dato un identificatore chiave. In questo modo gli utenti la possibilità di scegliere tra più tasti che vengono gestiti in più posizioni.

- Per la crittografia, viene utilizzata sempre la chiave e assenza di una chiave verrà generato un errore.
- Per la decrittografia:
    - Il sistema di risoluzione chiave viene richiamato se specificato per ottenere la chiave. Se il sistema di risoluzione specificato ma non dispone di un mapping per identificatore di chiave, viene generato un errore.
    - Se non viene specificato in risoluzione ma viene specificato un tasto, la chiave è utilizzata se l'identificatore corrisponde all'identificatore chiave necessario. Se l'identificatore non corrisponde, viene generato un errore.

[Esempi di crittografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) illustrare uno scenario più dettagliato-to-end per BLOB, code e tabelle, oltre l'integrazione di archivio di chiave.

### <a name="requireencryption-mode"></a>RequireEncryption modalità

Facoltativamente, è un modo di funzionamento in cui devono essere crittografati tutti i caricamenti e download. In questa modalità tenta di caricare dati senza un criterio di crittografia o il download dei dati che non è crittografati il servizio non viene eseguito sul client. La proprietà **RequireEncryption** dell'oggetto options richiesta consente di controllare questo comportamento. Se l'applicazione verrà crittografare tutti gli oggetti archiviati in archiviazione Azure, è possibile impostare la proprietà **RequireEncryption** sulle opzioni di richiesta predefinita per l'oggetto client di servizio. Ad esempio, impostare **CloudBlobClient.DefaultRequestOptions.RequireEncryption** su **true** per richiedere la crittografia per tutte le operazioni di blob eseguite tramite l'oggetto client.

### <a name="blob-service-encryption"></a>Crittografia di BLOB del servizio

Creare un oggetto **BlobEncryptionPolicy** e impostare le opzioni di richiesta (per API o a un livello di client tramite **DefaultRequestOptions**). Operazioni che verranno gestite dalla libreria client internamente.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Upload the encrypted contents to the blob.
    blob.UploadFromStream(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    MemoryStream outputStream = new MemoryStream();
    blob.DownloadToStream(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Crittografia del servizio di coda

Creare un oggetto **QueueEncryptionPolicy** e impostare le opzioni di richiesta (per API o a un livello di client tramite **DefaultRequestOptions**). Operazioni che verranno gestite dalla libreria client internamente.


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
    queue.AddMessage(message, null, null, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### <a name="table-service-encryption"></a>Crittografia del servizio di tabella

Oltre a creazione di un criterio di crittografia e impostare le opzioni di richiesta, è necessario specificare un **EncryptionResolver** in **TableRequestOptions**o impostata l'attributo [EncryptProperty] per l'entità.

#### <a name="using-the-resolver"></a>Utilizzando il sistema di risoluzione


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    {
        EncryptionResolver = (pk, rk, propName) =>
        {
            if (propName == "foo")
            {
                return true;
            }
            return false;
        },
        EncryptionPolicy = policy
    };

    // Insert Entity
    currentTable.Execute(TableOperation.Insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    {
        EncryptionPolicy = policy
    };

    TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
    TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### <a name="using-attributes"></a>Utilizzo degli attributi

Come detto in precedenza, se l'entità implementa TableEntity, le proprietà possono decorate con l'attributo [EncryptProperty] anziché specificando **EncryptionResolver**.

    [EncryptProperty]
    public string EncryptedProperty1 { get; set; }

## <a name="encryption-and-performance"></a>Crittografia e prestazioni

Si noti che la crittografia i risultati di dati di spazio di archiviazione in prestazioni. La chiave del contenuto e il vettore deve essere generato, il contenuto deve essere crittografato e metadati aggiuntivi devono essere formattato e caricati. Questo sovraccarico variano a seconda della quantità di dati vengono crittografati. È consigliabile che i clienti verificare sempre le applicazioni per le prestazioni in fase di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Crittografare e decrittografare BLOB di archiviazione di Microsoft Azure utilizzando Azure chiave archivio](storage-encrypt-decrypt-blobs-key-vault.md)
- Scaricare la [Libreria di Client Azure lo spazio di archiviazione per pacchetto NuGet .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
- Scaricare i pacchetti di Azure chiave archivio NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/)e [le estensioni](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
- Visitare la [chiave Azure archivio documentazione](../key-vault/key-vault-whatis.md)

