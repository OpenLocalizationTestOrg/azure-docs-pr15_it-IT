<properties
    pageTitle="Crittografia lato client con linguaggio per lo spazio di archiviazione di Microsoft Azure | Microsoft Azure"
    description="La raccolta di Client Azure lo spazio di archiviazione per Java supporta la crittografia lato client e l'integrazione con Azure chiave archivio per la massima protezione per le applicazioni di archiviazione Azure."
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-java-for-microsoft-azure-storage"></a>Crittografia lato client con linguaggio per lo spazio di archiviazione di Microsoft Azure   

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Panoramica  

La [Libreria di Client di spazio di archiviazione di Azure per Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) supporta la crittografia dei dati nelle applicazioni client prima di caricare allo spazio di archiviazione di Azure e decrittografare i dati durante il download al client. La raccolta supporta anche l'integrazione con [Azure chiave archivio](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi account lo spazio di archiviazione.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Crittografia e decrittografia tramite tecnica busta    
I processi di crittografia e decrittografia seguono tecnica della busta.  

### <a name="encryption-via-the-envelope-technique"></a>Crittografia tramite tecnica busta  
La crittografia tramite tecnica busta funziona nel modo seguente:  

1.  La raccolta di client di archiviazione Azure genera una chiave di crittografia contenuto (CEK), ovvero una chiave simmetrica un utilizzo.  

2.  Dati utente sono crittografati tramite questo CEK.   

3.  il CEK quindi va a capo (crittografati) utilizzando la chiave di crittografia (KEK). Il KEK identificate da un identificatore di chiave e possono essere un'asimmetrici, una coppia di chiavi o con un tasto simmetrico e possono essere gestiti a livello locale o memorizzati negli archivi chiave Azure.  
La stessa libreria di client lo spazio di archiviazione non ha accesso a KEK. La raccolta richiama l'algoritmo di ritorno a capo chiave fornito dall'archivio di chiave. Gli utenti possono scegliere di utilizzare provider personalizzati per ritorno a capo/unwrapping chiave bene accolta.  

4.  I dati crittografati viene caricati quindi il servizio di archiviazione Azure. Il tasto insieme alcuni metadati crittografia aggiuntiva archiviato come metadati (in un blob) o interpolazione con i dati crittografati (messaggi coda ed entità della tabella).

### <a name="decryption-via-the-envelope-technique"></a>Decrittografia tramite tecnica busta  
Decrittografia tramite tecnica busta funziona nel modo seguente:  

1.  La raccolta di client presuppone che l'utente usata per la gestione della chiave di crittografia chiave (KEK) in locale o in Azure chiave archivi. L'utente non è necessario conoscere la chiave specifica utilizzati per la crittografia. Se, tuttavia, è possibile configurare e utilizzare un sistema di risoluzione chiave che risolve diversi identificatori chiavi ai tasti.  

2.  La libreria client scarica i dati crittografati insieme a qualsiasi materiale di crittografia che è archiviato il servizio.  

3.  La chiave di crittografia contenuto incluso (CEK) è estratta (decrittografare) utilizzando la crittografia chiave principali (KEK). Di seguito nuovamente la libreria client non dispone dell'accesso a KEK. Richiama semplicemente algoritmo di wrapping archivio chiave del provider del servizio o personalizzate.  

4.  La chiave di crittografia del contenuto (CEK) quindi è utilizzata per decrittografare i dati utente crittografati.

## <a name="encryption-mechanism"></a>Meccanismo di crittografia  
La libreria di client lo spazio di archiviazione utilizza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) per crittografare i dati degli utenti. In particolare, modalità di [Concatenazione blocco crittografia (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. Ogni servizio works in modo diverso, in modo che verranno illustrate ciascuno di essi qui.

### <a name="blobs"></a>BLOB  
La raccolta di client attualmente supporta la crittografia dei solo BLOB intera. In particolare, supporta la crittografia quando gli utenti utilizzano **caricare** * metodi o * *openOutputStream** metodo. Per download, completate e download intervallo supportati.  

Durante la crittografia, la libreria client genererà un vettore casuale inizializzazione (IV) di 16 byte, insieme a una chiave di crittografia contenuto casuale (CEK) di 32 byte ed effettuare una busta crittografia dei dati blob utilizzando queste informazioni. CEK capo e alcuni metadati crittografia aggiuntive vengono quindi archiviati come blob metadati insieme blob crittografati il servizio.

>[AZURE.WARNING] Se si modificano o durante il caricamento dei metadati personalizzata per il blob, è necessario assicurarsi che questi metadati vengono mantenuti. Se si caricano nuovi metadati senza metadati, incluso CEK, IV e altri metadati andranno perse e il contenuto di blob mai possano essere recuperato.

Download blob crittografato implica il recupero del contenuto dell'intero blob tramite * *download*/openInputStream** metodi pratici. Estratta CEK incluso è utilizzata insieme a IV (archiviato in questo caso come blob metadati) per restituire i dati decrittografare agli utenti.

Scaricare un intervallo non autorizzato (**downloadRange*** metodi) nel blob crittografato a altro comporta il regolare l'intervallo inviato dagli utenti per ottenere una piccola quantità di dati aggiuntivi che possono essere usati per decrittografare correttamente l'intervallo richiesto.  

Tutti i tipi di blob (blocco BLOB, pagina BLOB e accoda BLOB) possono crittografare/decrittografare utilizzando questo schema.

### <a name="queues"></a>Code  
Dal momento che i messaggi possono essere di qualsiasi formato, la libreria di client definisce un formato personalizzato che include il vettore di inizializzazione (IV) e la chiave di crittografia del contenuto crittografata (CEK) del testo del messaggio.  

Durante la crittografia, la libreria client genera un IV casuali di 16 byte insieme a un casuale CEK 32 byte ed esegue la crittografia busta del testo del messaggio coda utilizzando queste informazioni. Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi aggiunti al messaggio crittografato coda. Questo messaggio modificato (come illustrato di seguito) è archiviato il servizio.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante la decrittografia, il tasto viene estratta dal messaggio di coda ed estratta. Il IV viene anche estratta dal messaggio di coda e utilizzato insieme al tasto estratta per decrittografare i dati del messaggio coda. Si noti che i metadati di crittografia small (in 500 byte), in modo mentre contare verso il limite di 64KB per un messaggio di coda, l'impatto dovrebbe essere gestibile.

### <a name="tables"></a>Tabelle  
La raccolta di client supporta la crittografia delle proprietà delle entità per l'inserimento e quelle di sostituzione.

>[AZURE.NOTE] Unione non è attualmente supportato. Poiché un sottoinsieme di proprietà è stato crittografato in precedenza utilizzando una chiave diversa, è sufficiente unione le nuove proprietà e aggiornare i metadati genereranno la perdita di dati. Unione di uno richiede chiamate a servizi aggiuntivi per leggere l'entità esistente dal servizio o con una nuova chiave ogni proprietà, che non sono adatti per migliorare le prestazioni.

La crittografia dei dati di tabella funziona nel modo seguente:  

1.  Gli utenti specificare le proprietà di crittografare.  

2.  La libreria client genera un vettore casuale inizializzazione (IV) di 16 byte insieme una chiave di crittografia contenuto casuale (CEK) di 32 byte per ogni entità ed esegue la crittografia busta in singole proprietà crittografare mediante la derivazione di un nuovo IV ogni proprietà. La proprietà crittografata verrà archiviata come dati binari.  

3.  Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi archiviati come due proprietà riservate aggiuntive. La prima proprietà riservata (_ClientEncryptionMetadata1) è una proprietà stringa che contiene le informazioni relative a IV, versione e il tasto. La proprietà secondo riservata (_ClientEncryptionMetadata2) è una proprietà binaria contenente le informazioni sulle proprietà delle crittografati. Le informazioni contenute in questa proprietà seconda (_ClientEncryptionMetadata2) sono crittografata.  

4.  A causa di queste proprietà aggiuntive riservate necessari per la crittografia, gli utenti possono hanno ora solo 250 proprietà personalizzate al posto di 252. Le dimensioni totali dell'entità devono essere inferiore a 1MB.  

    Si noti che è possibile crittografare solo proprietà stringa. Se altri tipi di proprietà devono essere crittografati, devono essere convertiti in stringhe. Le stringhe crittografate vengono memorizzate il servizio come proprietà binaria e vengono convertiti al stringhe dopo la decrittografia.

    Per le tabelle, oltre ai criteri di crittografia, gli utenti devono specificare le proprietà di crittografare. Ciò è possibile specificando un attributo [crittografa] (per entità POCO che derivano da TableEntity) o un sistema di risoluzione di crittografia nelle opzioni di richiesta. Una risoluzione crittografia è un delegato che accetta una chiave di partizione, chiave riga e il nome di proprietà e restituisce un valore boolean che indica se è necessario crittografare tale proprietà. Durante la crittografia, la libreria client utilizzerà queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura nel collegamento. Il delegato vengono forniti anche la possibilità di logica intorno la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografare proprietà A; in caso contrario crittografare le proprietà A e B.) Si noti che non è necessario fornire queste informazioni durante la lettura o query su entità.

### <a name="batch-operations"></a>Operazioni batch  
Le operazioni di batch KEK stessa verranno usati per tutte le righe di tale operazione batch poiché la libreria client consentito solo un oggetto di opzioni (e pertanto un criterio/KEK) per operazione batch. Tuttavia, la libreria client internamente genererà un IV casuali nuovo e CEK casuale per ogni riga nel batch. Gli utenti inoltre possono scegliere di crittografare proprietà diverse per ogni operazione nel batch definendo il problema nel sistema di risoluzione crittografia.

### <a name="queries"></a>Query  
Per eseguire operazioni di query, è necessario specificare un sistema di risoluzione chiave che è possibile risolvere tutti i tasti di set di risultati. Se non è possibile risolvere un'entità contenuta nel risultato della query a un provider, la libreria client verrà generato un errore. Per le query che esegue previsioni sul lato server, la raccolta di client aggiungerà le proprietà dei metadati crittografia speciale (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) per impostazione predefinita per le colonne selezionate.

## <a name="azure-key-vault"></a>Archivio chiave Azure  
Archivio chiave Azure consente di crittografia per proteggere e informazioni riservate utilizzati da cloud applicazioni e servizi. Utilizzando Azure chiave archivio, gli utenti possano crittografare tasti e informazioni riservate (ad esempio chiavi di autenticazione, memorizzazione account chiavi, chiavi di crittografia. File PFX e la password) utilizzando i tasti che sono protetti tramite moduli di protezione hardware (HSM). Per ulteriori informazioni, vedere [Novità Azure chiave archivio?](../key-vault/key-vault-whatis.md).

La libreria di client lo spazio di archiviazione utilizza la libreria di base di archivio di chiave per fornire un quadro comune tra Azure per la gestione delle chiavi. Gli utenti che si verifichino il vantaggio di utilizzo della libreria di estensioni chiave archivio. La libreria di estensioni offre funzionalità utili intorno semplice e continua simmetrico (Symmetric) / RSA locale e cloud chiave provider oltre che con l'aggregazione e la memorizzazione nella cache.

### <a name="interface-and-dependencies"></a>Interfaccia e dipendenze  
Esistono tre pacchetti di archivio di chiave:  

- Azure keyvault core contiene la IKey e IKeyResolver. È un pacchetto small senza dipendenze. La raccolta di client lo spazio di archiviazione per Java definisce come dipendenza.
- Azure keyvault contiene il client chiave archivio resto.  
- codice di estensione che include implementazioni di algoritmi e un RSAKey e un SymmetricKey estensioni di keyvault Azure. IT dipende gli spazi dei nomi di base e KeyVault e consente di definire un sistema di risoluzione aggregata (quando gli utenti desiderano utilizzare più provider chiave) e un tasto Risolutore. Anche se la raccolta di client lo spazio di archiviazione non dipende direttamente il pacchetto se gli utenti desiderano utilizzare Azure chiave archivio per archiviare le chiavi o usare le estensioni di archivio di chiave per utilizzare locali e cloud cryptographic provider, occorrerà il pacchetto.  

  Chiave archivio è progettata per le chiavi master di valore elevato, e limiti di limitazioni per singolo archivio di chiave sono il in considerazione. Quando si esegue la crittografia lato client con chiave archivio, il modello preferito consiste nell'usare simmetriche chiavi master archiviate come informazioni riservate nell'archivio di chiave e cache in locale. Gli utenti devono eseguire le operazioni seguenti:  

1.  Creare un segreto offline e caricare in archivio chiave.  

2.  Utilizzare identificatore di base del segreto come parametro per risolvere la versione corrente del segreto per la crittografia e memorizzare nella cache locale queste informazioni. Utilizzare CachingKeyResolver per la memorizzazione nella cache; gli utenti non dovrebbero implementare le proprie memorizzazione nella cache logica.  

3.  Utilizzare il Risolutore come input durante la creazione di criteri di crittografia.
Sono disponibili ulteriori informazioni sull'utilizzo di archivio di chiave negli esempi di codice di crittografia. <fix URL>  

## <a name="best-practices"></a>Procedure consigliate  
Supporto per la crittografia è disponibile solo nella libreria di client lo spazio di archiviazione per Java.

>[AZURE.IMPORTANT] Prestare particolare attenzione questi punti importanti quando si usa la crittografia lato client:
>  
>- Durante la lettura o la scrittura di blob crittografato, usare i comandi Carica intero blob e intervallo/intero blob download comandi. Evitare di scrivere nella blob crittografato tramite operazioni di protocollo, ad esempio blocco mettere, mettere bloccati, scrivere pagine, Cancella pagine o accodare blocco; in caso contrario è possibile danneggiata blob crittografato e renderlo più leggibile.
>
>- Per le tabelle, è presente un vincolo simile. Prestare attenzione a non vengono aggiornate le proprietà crittografate senza aggiornare i metadati di crittografia.  
>
>- Se si impostano metadati blob crittografato, si potrebbero sovrascrivere i metadati relativi a crittografia necessari per la decrittografia, in quanto l'impostazione di metadati non additivo. Questo vale anche per snapshot; evitare di specificare i metadati durante la creazione di un'istantanea del blob crittografato. Se è necessario configurare i metadati, assicurarsi di chiamare il metodo **downloadAttributes** per ottenere i metadati di crittografia corrente ed evitare scrive simultanei durante l'impostazione di metadati.  
>
>- Attivare il contrassegno **requireEncryption** nelle opzioni di richiesta di predefinito per gli utenti che devono funzionano solo con i dati crittografati. Per ulteriori informazioni, vedere di seguito.  

## <a name="client-api--interface"></a>API client / interfaccia  
Durante la creazione di un oggetto EncryptionPolicy, gli utenti possono specificare solo un tasto (implementazione IKey), solo un sistema di risoluzione (implementazione IKeyResolver) o entrambe. IKey è il tipo di chiave base identificato tramite un identificatore di chiave e che fornisce la logica per/unwrapping ritorno a capo. IKeyResolver viene utilizzato per risolvere un tasto durante il processo di decrittografia. Definisce un metodo di ResolveKey che restituisce un IKey fornito un identificatore di chiave. In questo modo gli utenti la possibilità di scegliere tra più tasti che vengono gestiti in più posizioni.

- Per la crittografia, viene utilizzata sempre la chiave e assenza di una chiave verrà generato un errore.  
- Per la decrittografia:  
    - Il sistema di risoluzione chiave viene richiamato se specificato per ottenere la chiave. Se il sistema di risoluzione specificato ma non dispone di un mapping per identificatore di chiave, viene generato un errore.  
    - Se non viene specificato in risoluzione specificata di una chiave, il tasto viene utilizzato se l'identificatore corrisponde all'identificatore chiave necessario. Se l'identificatore non corrisponde, viene generato un errore.  

      [Esempi di crittografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>illustrare insieme uno scenario più dettagliato-to-end per BLOB, code e tabelle, con l'integrazione di archivio di chiave.

### <a name="requireencryption-mode"></a>Modalità RequireEncryption  
Facoltativamente, è un modo di funzionamento in cui devono essere crittografati tutti i caricamenti e download. In questa modalità tenta di caricare dati senza un criterio di crittografia o il download dei dati che non è crittografati il servizio non viene eseguito sul client. Flag **requireEncryption** dell'oggetto options richiesta consente di controllare questo comportamento. Se l'applicazione consentirà di crittografare tutti gli oggetti archiviati in archiviazione Azure, è possibile impostare la proprietà **requireEncryption** nell'elenco Opzioni richiesta predefinito per l'oggetto di client di assistenza.   

Ad esempio, utilizzare **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** per richiedere la crittografia per tutte le operazioni di blob eseguite tramite l'oggetto client.

### <a name="blob-service-encryption"></a>Crittografia del servizio di BLOB  
Creare un oggetto **BlobEncryptionPolicy** e impostare le opzioni di richiesta (per API o a un livello di client tramite **DefaultRequestOptions**). Operazioni che verranno gestite dalla libreria client internamente.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions();
    options.setEncryptionPolicy(policy);

    // Upload the encrypted contents to the blob.
    blob.upload(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    blob.download(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Crittografia del servizio di coda  
Creare un oggetto **QueueEncryptionPolicy** e impostare le opzioni di richiesta (per API o a un livello di client tramite **DefaultRequestOptions**). Operazioni che verranno gestite dalla libreria client internamente.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions();
    options.setEncryptionPolicy(policy);

    queue.addMessage(message, 0, 0, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);

### <a name="table-service-encryption"></a>Crittografia del servizio di tabella  
Oltre la creazione di un criterio di crittografia e impostare opzioni di richiesta, è necessario specificare un **EncryptionResolver** in **TableRequestOptions**o impostare l'attributo [crittografa] richiamo e di impostazione dell'entità.

### <a name="using-the-resolver"></a>Utilizzando il sistema di risoluzione  

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    options.setEncryptionPolicy(policy);
    options.setEncryptionResolver(new EncryptionResolver() {
        public boolean encryptionResolver(String pk, String rk, String key) {
            if (key == "foo")
            {
                return true;
            }
            return false;
        }
    });

    // Insert Entity
    currentTable.execute(TableOperation.insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    retrieveOptions.setEncryptionPolicy(policy);

    TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
    TableResult result = currentTable.execute(operation, retrieveOptions, null);

### <a name="using-attributes"></a>Utilizzo degli attributi  
Come detto in precedenza, se l'entità implementa TableEntity, il richiamo di proprietà e di impostazione possibile decorate con l'attributo [crittografa] anziché specificare **EncryptionResolver**.

    private string encryptedProperty1;

    @Encrypt
    public String getEncryptedProperty1 () {
        return this.encryptedProperty1;
    }

    @Encrypt
    public void setEncryptedProperty1(final String encryptedProperty1) {
        this.encryptedProperty1 = encryptedProperty1;
    }

## <a name="encryption-and-performance"></a>Crittografia e prestazioni  
Si noti che la crittografia i risultati di dati di spazio di archiviazione in prestazioni. La chiave del contenuto e il vettore deve essere generato, il contenuto deve essere crittografato e metadati aggiuntivi devono essere formattato e caricati. Questo sovraccarico variano a seconda della quantità di dati vengono crittografati. È consigliabile che i clienti verificare sempre le applicazioni per le prestazioni in fase di sviluppo.

## <a name="next-steps"></a>Passaggi successivi  

- Scaricare la [Libreria di Client Azure lo spazio di archiviazione per Maven linguaggio pacchetto](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
- Scaricare la [raccolta di Client di archiviazione Azure per codice sorgente Java da GitHub](https://github.com/Azure/azure-storage-java)   
- Scaricare la raccolta di Azure chiave archivio Maven per i pacchetti Java Maven:
    - Pacchetto di [base](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
    - Pacchetto [client](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
- Visitare la [chiave Azure archivio documentazione](../key-vault/key-vault-whatis.md)  
