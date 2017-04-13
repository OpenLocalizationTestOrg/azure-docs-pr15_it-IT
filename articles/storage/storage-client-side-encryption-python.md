<properties
    pageTitle="Crittografia lato client con Python per lo spazio di archiviazione di Microsoft Azure | Microsoft Azure"
    description="La raccolta di Client Azure lo spazio di archiviazione per Python supporta la crittografia lato client per la massima protezione per le applicazioni di archiviazione Azure."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Crittografia lato client con Python per lo spazio di archiviazione di Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Panoramica

La [Libreria di Client di spazio di archiviazione di Azure per Python](https://pypi.python.org/pypi/azure-storage) supporta la crittografia dei dati nelle applicazioni client prima di caricare allo spazio di archiviazione di Azure e decrittografare i dati durante il download al client.

>[AZURE.NOTE] La raccolta di Azure archiviazione Python è in anteprima.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Crittografia e decrittografia tramite tecnica busta
I processi di crittografia e decrittografia seguono tecnica della busta.

### <a name="encryption-via-the-envelope-technique"></a>Crittografia tramite tecnica busta
La crittografia tramite tecnica busta funziona nel modo seguente:

1.  La raccolta di client di archiviazione Azure genera una chiave di crittografia contenuto (CEK), ovvero una chiave simmetrica un utilizzo.

2.  Dati utente sono crittografati tramite questo CEK.

3.  Il CEK quindi va a capo (crittografati) utilizzando la chiave di crittografia (KEK). Il KEK è identificato da un identificatore di chiave e può essere una coppia di chiavi asimmetrica o con un tasto simmetrico, viene gestito in locale.
La stessa libreria di client lo spazio di archiviazione non ha accesso a KEK. La raccolta richiama l'algoritmo di ritorno a capo chiave fornito con il KEK. Gli utenti possono scegliere di utilizzare provider personalizzati per ritorno a capo/unwrapping chiave bene accolta.

4.  I dati crittografati viene quindi caricati al servizio di archiviazione Azure. Il tasto insieme alcuni metadati crittografia aggiuntiva archiviato come metadati (in un blob) o interpolazione con i dati crittografati (messaggi coda ed entità della tabella).

### <a name="decryption-via-the-envelope-technique"></a>Decrittografia tramite tecnica busta
Decrittografia tramite tecnica busta funziona nel modo seguente:

1.  La libreria client presuppone che l'utente gestisce la chiave di crittografia (KEK) in locale. L'utente non è necessario conoscere la chiave specifica utilizzati per la crittografia. Se, tuttavia, è possibile configurare e utilizzare un sistema di risoluzione chiave, che consente di risolvere diversi identificatori chiavi tasti.

2.  La libreria client scarica i dati crittografati insieme a qualsiasi materiale di crittografia che è archiviato il servizio.

3.  La chiave di crittografia contenuto incluso (CEK) è estratta (decrittografare) utilizzando la crittografia chiave principali (KEK). Di seguito nuovamente la libreria client non dispone dell'accesso a KEK. Richiama semplicemente algoritmo di wrapping del provider personalizzato.

4.  La chiave di crittografia del contenuto (CEK) viene quindi utilizzata per decrittografare i dati utente crittografato.

## <a name="encryption-mechanism"></a>Meccanismo di crittografia  
La libreria di client lo spazio di archiviazione utilizza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) per crittografare i dati degli utenti. In particolare, modalità di [Blocco concatenazione di crittografia (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. Ogni servizio works in modo diverso, in modo che verranno illustrate ciascuno di essi qui.

### <a name="blobs"></a>BLOB
La libreria client attualmente supporta la crittografia dell'intero BLOB solo. In particolare, supporta la crittografia quando gli utenti utilizzano **creare*** metodi. Per i download, completate e download intervallo sono supportati, la parallelizzazione di caricamento e download è disponibile.

Durante la crittografia, la libreria client verrà generare un vettore casuale inizializzazione (IV) di 16 byte, insieme a una chiave di crittografia contenuto casuale (CEK) di 32 byte ed eseguire la crittografia busta dei dati blob utilizzando queste informazioni. Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi archiviati come blob metadati insieme blob crittografato il servizio.

>[AZURE.WARNING] Se si modificano o durante il caricamento dei metadati personalizzata per il blob, è necessario assicurarsi che questi metadati vengono mantenuti. Se si caricano nuovi metadati senza metadati, incluso CEK, IV e altri metadati andranno perse e il contenuto di blob mai possano essere recuperato.

Download blob crittografato implica il recupero del contenuto dell'intero blob utilizzando **ottenere*** metodi pratici. Il CEK è estratta e utilizzata insieme a IV (archiviato in questo caso come blob metadati) per restituire i dati decrittografare agli utenti.

Scaricare un intervallo non autorizzato (**ottenere*** metodi con i parametri di intervallo passato) nel blob crittografato a altro comporta il regolare l'intervallo inviato dagli utenti per ottenere una piccola quantità di dati aggiuntivi che possono essere usati per decrittografare correttamente l'intervallo richiesto.

Blocco BLOB BLOB di pagine solo possibile e crittografia/decrittografia usando questo schema. Non è attualmente disponibile alcun supporto per la crittografia accodare BLOB.

### <a name="queues"></a>Code
Poiché i messaggi possono essere di qualsiasi formato, la libreria client definisce un formato personalizzato che include il vettore di inizializzazione (IV) e la chiave di crittografia contenuto crittografato (CEK) del testo del messaggio.

Durante la crittografia, la libreria client genera un IV casuali di 16 byte insieme a un casuale CEK 32 byte ed esegue la crittografia busta del testo del messaggio coda utilizzando queste informazioni. Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi aggiunti al messaggio crittografato coda. Questo messaggio modificato (come illustrato di seguito) è archiviato il servizio.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante la decrittografia, il tasto viene estratta dal messaggio di coda ed estratta. Il IV viene anche estratta dal messaggio di coda e utilizzato insieme al tasto estratta per decrittografare i dati del messaggio coda. Si noti che i metadati di crittografia small (in 500 byte), in modo mentre contare verso il limite di 64KB per un messaggio di coda, l'impatto dovrebbe essere gestibile.

### <a name="tables"></a>Tabelle
La libreria client supporta la crittografia proprietà dell'entità per l'inserimento e quelle di sostituzione.

>[AZURE.NOTE] Unione non è attualmente supportato. Poiché un sottoinsieme di proprietà è stato crittografato in precedenza utilizzando una chiave diversa, è sufficiente unione le nuove proprietà e aggiornare i metadati genereranno la perdita di dati. Unione di uno richiede chiamate a servizi aggiuntivi per leggere l'entità esistente dal servizio o con una nuova chiave ogni proprietà, che non sono adatti per migliorare le prestazioni.

La crittografia dei dati di tabella funziona nel modo seguente:

1.  Gli utenti specificare le proprietà di crittografare.

2.  La libreria client genera un vettore casuale inizializzazione (IV) di 16 byte insieme una chiave di crittografia contenuto casuale (CEK) di 32 byte per ogni entità ed esegue la crittografia busta in singole proprietà crittografare mediante la derivazione di un nuovo IV ogni proprietà. La proprietà crittografata verrà archiviata come dati binari.

3.  Il CEK e alcuni metadati di crittografia aggiuntive vengono quindi archiviati come due proprietà riservate aggiuntive. La prima proprietà riservata (\_ClientEncryptionMetadata1) è una proprietà stringa che contiene le informazioni relative a IV, versione e il tasto. La seconda proprietà riservata (\_ClientEncryptionMetadata2) è una proprietà binaria che contiene le informazioni sulle proprietà che vengono crittografati. Le informazioni contenute in questa proprietà seconda (\_ClientEncryptionMetadata2) è crittografata.

4.  A causa di queste proprietà aggiuntive riservate necessari per la crittografia, gli utenti possono hanno ora solo 250 proprietà personalizzate anziché 252. Le dimensioni totali dell'entità devono essere inferiore a 1MB.

    Si noti che è possibile crittografare solo proprietà stringa. Se altri tipi di proprietà devono essere crittografati, devono essere convertiti in stringhe. Le stringhe crittografate vengono memorizzate il servizio come proprietà binaria e vengono convertiti al stringhe (non elaborate stringhe, non EntityProperties con tipo EdmType.STRING) dopo la decrittografia.

    Per le tabelle, oltre ai criteri di crittografia, gli utenti devono specificare le proprietà di crittografare. Questa operazione può essere eseguita archiviando entrambi queste proprietà negli oggetti TableEntity con il set di tipo a EdmType.STRING e crittografare impostato su true o l'impostazione di encryption_resolver_function sull'oggetto tableservice. Una risoluzione di crittografia è una funzione che accetta una chiave di partizione, chiave di riga e il nome di proprietà e restituisce un valore boolean che indica se è necessario crittografare tale proprietà. Durante la crittografia, la libreria client utilizzerà queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura nel collegamento. Il delegato vengono forniti anche la possibilità di logica intorno la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografare proprietà A; in caso contrario Crittografa proprietà A e B.) Si noti che non è necessario fornire queste informazioni durante la lettura o query su entità.

### <a name="batch-operations"></a>Operazioni batch
Un criterio di crittografia si applica a tutte le righe nel batch. La libreria client internamente genererà un nuovo IV casuali e CEK casuale per ogni riga nel batch. Gli utenti possono anche scegliere di crittografare proprietà diverse per ogni operazione nel batch di definendo questo comportamento nella risoluzione la crittografia.
Se un responsabile di contesto viene creato un batch tramite il metodo di batch() tableservice, criteri di crittografia del tableservice verranno applicato automaticamente al batch. Se viene creato un batch in modo esplicito chiamando il costruttore, i criteri di crittografia devono essere passato come parametro e invariato a sinistra per la durata del batch.
Si noti che vengono crittografate entità come vengono inseriti in batch con criterio di crittografia del batch (entità non vengono crittografate al momento dell'esecuzione batch tramite criteri di crittografia del tableservice).

### <a name="queries"></a>Query
Per eseguire operazioni di query, è necessario specificare un sistema di risoluzione chiave che è possibile risolvere tutti i tasti di set di risultati. Se non è possibile risolvere un'entità contenuta nel risultato della query a un provider, la libreria client verrà generato un errore. Per le query che esegue previsioni sul lato server, la libreria client aggiungerà le proprietà dei metadati di crittografia speciale (\_ClientEncryptionMetadata1 e \_ClientEncryptionMetadata2) per impostazione predefinita per le colonne selezionate.

>[AZURE.IMPORTANT] Prestare particolare attenzione questi punti importanti quando si usa la crittografia lato client:
>
>- Durante la lettura o la scrittura di blob crittografato, usare i comandi Carica intero blob e intervallo/intero blob download comandi. Evitare di scrivere nella blob crittografato tramite operazioni di protocollo, ad esempio inserire blocco, inserire elenco a blocchi, scrivere pagine o Cancella pagine. in caso contrario è possibile danneggiata blob crittografato e renderlo più leggibile.
>
>- Per le tabelle, è presente un vincolo simile. Prestare attenzione a non vengono aggiornate le proprietà crittografate senza aggiornare i metadati di crittografia.
>
>- Se si impostano metadati blob crittografato, si potrebbero sovrascrivere i metadati relativi a crittografia necessari per la decrittografia, in quanto l'impostazione di metadati non additivo. Questo vale anche per snapshot; evitare di specificare i metadati durante la creazione di un'istantanea del blob crittografato. Se è necessario configurare i metadati, assicurarsi di chiamare il metodo **get_blob_metadata** per ottenere i metadati di crittografia corrente ed evitare scrive simultanee durante l'impostazione di metadati.
>
>- Attivare il contrassegno **require_encryption** oggetto del servizio per gli utenti che devono funzionano solo con i dati crittografati. Per ulteriori informazioni, vedere di seguito.

La libreria di client lo spazio di archiviazione prevede che i valori KEK fornito e risoluzione chiave per implementare l'interfaccia seguente. Supporto di [Archivio di Azure chiave](https://azure.microsoft.com/services/key-vault/) per la gestione di KEK Python è in attesa e verrà integrato in questa raccolta al termine.


## <a name="client-api--interface"></a>API client / interfaccia
Dopo la creazione di un oggetto di servizio di archiviazione (ad esempio blockblobservice), l'utente può assegnare valori ai campi che costituiscono un criterio di crittografia: key_encryption_key, key_resolver_function e require_encryption. Gli utenti possono fornire solo KEK, un sistema di risoluzione o entrambe. key_encryption_key è il tipo di chiave base identificato tramite un identificatore di chiave e che fornisce la logica per/unwrapping ritorno a capo. key_resolver_function viene utilizzato per risolvere un tasto durante il processo di decrittografia. Restituisce un KEK valido dato un identificatore chiave. In questo modo gli utenti la possibilità di scegliere tra più tasti che vengono gestiti in più posizioni.

Il KEK deve implementare i metodi seguenti per crittografare i dati:
- wrap_key(cek): vada a capo CEK specificata (byte) utilizzando un algoritmo della scelta dell'utente. Restituisce il tasto a capo.
- get_key_wrap_algorithm(): restituisce l'algoritmo utilizzato per disporre i tasti.
- get_kid(): restituisce l'id chiave di stringa per questo KEK.
Il KEK deve implementare i metodi seguenti per decrittografare i dati:
- unwrap_key (cek, algoritmo): restituisce il blocco del CEK specificato utilizzando l'algoritmo di stringa specificata.
- get_kid(): restituisce un id chiave di stringa per questo KEK.

Il sistema di risoluzione chiave almeno necessario implementare un metodo che, dato un id chiave restituisce KEK corrispondente implementazione dell'interfaccia precedente. Solo questo metodo viene assegnato alla proprietà key_resolver_function oggetto del servizio.

- Per la crittografia, viene utilizzata sempre la chiave e assenza di una chiave verrà generato un errore.
- Per la decrittografia:
    - Il sistema di risoluzione chiave viene richiamato se specificato per ottenere la chiave. Se il sistema di risoluzione specificato ma non dispone di un mapping per identificatore di chiave, viene generato un errore.
    - Se non viene specificato in risoluzione ma viene specificato un tasto, la chiave è utilizzata se l'identificatore corrisponde all'identificatore chiave necessario. Se l'identificatore non corrisponde, viene generato un errore.

      Negli esempi di crittografia azure.storage.samples <fix URL>dimostrare scenario-to-end più dettagliato per BLOB, code e tabelle.
        Esempi di implementazione delle KEK del Risolutore chiave vengono forniti nei file di esempio come KeyWrapper e KeyResolver rispettivamente.

### <a name="requireencryption-mode"></a>Modalità RequireEncryption
Facoltativamente, è un modo di funzionamento in cui devono essere crittografati tutti i caricamenti e download. In questa modalità tenta di caricare dati senza un criterio di crittografia o il download dei dati che non è crittografati il servizio non viene eseguito sul client. Il contrassegno **require_encryption** oggetto del servizio consente di controllare questo comportamento.

### <a name="blob-service-encryption"></a>Crittografia del servizio di BLOB
Impostare la crittografia campi criteri sull'oggetto blockblobservice. Operazioni che verranno gestite dalla libreria client internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Crittografia del servizio di coda
Impostare la crittografia campi criteri sull'oggetto queueservice. Operazioni che verranno gestite dalla libreria client internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Crittografia del servizio di tabella
Oltre a creazione di un criterio di crittografia e impostare le opzioni di richiesta, è necessario specificare un **encryption_resolver_function** **tableservice**o impostare l'attributo crittografa il EntityProperty.

### <a name="using-the-resolver"></a>Utilizzando il sistema di risoluzione

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>Utilizzo degli attributi
Come detto in precedenza, una proprietà può essere contrassegnata per la crittografia archiviare i dati in un oggetto EntityProperty e impostando il campo Crittografa.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Crittografia e prestazioni
Si noti che la crittografia i risultati di dati di spazio di archiviazione in prestazioni. La chiave del contenuto e il vettore deve essere generato, il contenuto deve essere crittografato e metadati aggiuntivi devono essere formattato e caricati. Questo sovraccarico variano a seconda della quantità di dati vengono crittografati. È consigliabile che i clienti verificare sempre le applicazioni per le prestazioni in fase di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

- Scaricare la [Libreria di Client Azure lo spazio di archiviazione per pacchetto PyPi Java](https://pypi.python.org/pypi/azure-storage)
- Scaricare la [raccolta di Client di archiviazione Azure per Python origine codice da GitHub](https://github.com/Azure/azure-storage-python)
