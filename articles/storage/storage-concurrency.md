<properties
    pageTitle="Gestione della concorrenza nell'archivio di Microsoft Azure"
    description="Come gestire concorrenza per i servizi di Blob, code, tabella e File"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gestione della concorrenza nell'archivio di Microsoft Azure

## <a name="overview"></a>Panoramica

Più recenti applicazioni basati su Internet in genere utilizzato da più utenti la visualizzazione e aggiornamento dei dati contemporaneamente. È necessario agli sviluppatori di applicazioni valutare attentamente i metodi assicurare un'esperienza prevedibile agli utenti finali, in particolare per gli scenari in cui gli utenti più possano aggiornare gli stessi dati. Sono disponibili tre strategie di concorrenza dati principale gli sviluppatori in genere verranno preso in considerazione:  


1.  Ottimistica: un'applicazione eseguendo che un aggiornamento durante l'aggiornamento verificherà se i dati sono stati modificati dopo l'applicazione ultimo leggerà i dati. Ad esempio, se due utenti che visualizzano una pagina wiki effettuare un aggiornamento alla stessa pagina la piattaforma wiki necessario assicurarsi che il secondo aggiornamento non sovrascrive il primo aggiornamento – e che entrambi agli utenti di capire se l'aggiornamento avuto esito positivo o meno. Questo approccio più spesso è utilizzato nelle applicazioni web.
2.  Concorrenza pessimistica: un'applicazione di ricerca per eseguire un aggiornamento assumerà un blocco di un oggetto impedendo agli altri utenti di aggiornare i dati fino a quando non viene sbloccata. Ad esempio, in uno scenario di replica di dati principale/secondario in cui solo lo schema verrà eseguita automaticamente gli aggiornamenti lo schema in genere conterrà un blocco esclusivo per un periodo di tempo per assicurarsi che nessun utente può aggiornare i dati.
3.  Autore ultime-un approccio che consente di tutte le operazioni di aggiornamento continuare senza verificare se qualsiasi altra applicazione aggiornati i dati dell'applicazione prima di tutto la lettura dei dati. Questa strategia (o mancanza di una strategia formale) viene utilizzata in genere nel punto in cui la partizione dati in modo che non esiste alcun probabilità che più gli utenti potranno accedere gli stessi dati. Può inoltre essere utile in cui vengono elaborati flussi di dati breve.  

In questo articolo viene fornita una panoramica di come la piattaforma di archiviazione Azure semplifica lo sviluppo di fornendo il supporto di prima classe per tutti e tre queste strategie di concorrenza.  

## <a name="azure-storage--simplifies-cloud-development"></a>Spazio di archiviazione Azure – semplifica lo sviluppo Cloud
Il servizio di archiviazione Azure supporta tutte le tre strategie, sebbene sia distintivo capacità per fornire supporto completo per ottimistica e pessimistica poiché è stata progettata per basati su un modello di coerenza sicuro che garantisce che quando il servizio di archiviazione commit un inserimento di dati o operazione di aggiornamento tutte ulteriormente accede a che dati verranno visualizzato l'aggiornamento più recente. Piattaforme di archiviazione che utilizzano un modello di coerenza finale di avere un ritardo tra la scrittura viene eseguita da un utente e quando i dati aggiornati possono essere visualizzati da altri utenti in questo modo complessità sviluppo di applicazioni client per impedire che le incoerenze che interessano gli utenti finali.  

Oltre a scelta di una strategia di concorrenza appropriato gli sviluppatori devono essere importanti come una piattaforma di archiviazione isola le modifiche, in particolare modifiche allo stesso oggetto tra le transazioni. Il servizio di archiviazione Azure utilizza isolamento dello snapshot per consentire le operazioni di lettura decidere in contemporanea con le operazioni di scrittura all'interno di una singola partizione. Diversamente dagli altri livelli di isolamento isolamento dello snapshot garantisce che tutte le operazioni lettura visualizzati uno snapshot coerente dei dati anche quando si verificano aggiornamenti-essenzialmente restituendo gli ultimi valori approvati durante un aggiornamento transazione in fase di elaborazione.  

## <a name="managing-concurrency-in-blob-storage"></a>Gestione della concorrenza nell'archiviazione Blob
È possibile scegliere di utilizzare uno dei due modelli di concorrenza ottimistica e pessimistica per gestire l'accesso ai contenitori nel servizio blob e BLOB. Se non si specifica esplicitamente un wins scrive ultima strategia è il valore predefinito.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Ottimistica per BLOB e i contenitori  

Il servizio di archiviazione assegna un identificatore a ogni oggetto archiviato. Questo identificatore viene aggiornato ogni volta che viene eseguita un'operazione di aggiornamento su un oggetto. Viene restituito l'identificatore al client come parte di una risposta HTTP GET utilizzando l'intestazione (tag entità) ETag definiti all'interno il protocollo HTTP. Un utente che esegue un aggiornamento su un oggetto ad esempio possibile inviare ETag originale insieme a un'intestazione condizionale per garantire che un aggiornamento si verifica solo se è stata soddisfatta una determinata condizione: in questo caso la condizione è un'intestazione "If-Match" che richiede il servizio di archiviazione per assicurarsi che il valore di ETag specificato nella richiesta di aggiornamento è uguale a quello archiviato nel servizio di archiviazione.  

La struttura di questo processo è il seguente:  

1.  Recuperare un blob dal servizio di archiviazione, la risposta include un valore di intestazione ETag HTTP che identifica la versione corrente dell'oggetto all'interno del servizio di archiviazione.
2.  Quando si aggiorna il blob, includere il valore ETag ottenuto nel passaggio 1 nell'intestazione condizionale **If-Match** della richiesta di che inviare al servizio.
3.  Il servizio confronta il valore ETag nella richiesta di con il valore ETag corrente del blob.
4.  Se il valore ETag corrente del blob è una versione diversa rispetto ETag nell'intestazione condizionale **If-Match** nella richiesta, il servizio viene restituito un errore 412 al client. Indica che un altro processo è aggiornato il blob dopo il client è stato recuperato al client.
5.  Se il valore ETag corrente del blob è la stessa versione ETag nell'intestazione condizionale **If-Match** nella richiesta, il servizio esegue l'operazione e aggiorna il valore ETag corrente del blob per mostrare che ha creato una nuova versione.  

Il frammento c# seguente (usando la raccolta di spazio di archiviazione Client 4.2.0) Mostra un semplice esempio informazioni su come creare una **Corrispondenza se AccessCondition** in base al valore ETag accessibile dalle proprietà di un blob che è stata precedentemente recuperati o inserita. Utilizza quindi l'oggetto **AccessCondition** durante l'aggiornamento del blob: oggetto **AccessCondition** aggiunge l'intestazione **If-Match** alla richiesta. Se un altro processo è aggiornato il blob, il servizio blob restituisce un messaggio di stato HTTP 412 (preliminare non). È possibile scaricare l'esempio di completo: [Gestione della concorrenza usando archiviazione Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

Il servizio di archiviazione include anche il supporto per le intestazioni condizionale aggiuntive quali **If-modificato-poiché**, **Se-invariato-** dall'e **If-None-Match** come loro combinazioni. Per ulteriori informazioni vedere [Specificando condizionale intestazioni per operazioni di servizio Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) su MSDN.  

Nella tabella seguente sono riepilogate le operazioni di contenitore che accettano intestazioni condizionale, ad esempio **Se corrispondenza** della richiesta e un valore ETag da restituire nella risposta.  

| Operazione                | Restituisce il valore ETag contenitore | Accetta intestazioni condizionale |
|:-------------------------|:-----------------------------|:----------------------------|
| Creare contenitore         | Sì                          | No                          |
| Ottenere le proprietà contenitore | Sì                          | No                          |
| Ottenere i metadati contenitore   | Sì                          | No                          |
| Impostare i metadati contenitore   | Sì                          | Sì                         |
| Ottenere contenitore ACL        | Sì                          | No                          |
| Impostare ACL contenitore        | Sì                          | Sì (*)                     |
| Elimina contenitore         | No                           | Sì                         |
| Leasing contenitore          | Sì                          | Sì                         |
| Elenco BLOB               | No                           | No                          |

(*) Le autorizzazioni definite dal SetContainerACL vengono memorizzati nella cache e gli aggiornamenti a queste autorizzazioni necessari 30 secondi prima propagazione delle modifiche apportate durante il periodo di aggiornamenti non vengono necessariamente essere coerenti.  

Nella tabella seguente sono riepilogate le operazioni di blob che accettano intestazioni condizionale, ad esempio **If-Match** nella richiesta e che restituiscono un valore ETag nella risposta.

| Operazione           | Restituisce il valore ETag | Accetta intestazioni condizionale           |
|:--------------------|:-------------------|:--------------------------------------|
| Inserire Blob            | Sì                | Sì                                   |
| Ottenere Blob            | Sì                | Sì                                   |
| Ottenere le proprietà Blob | Sì                | Sì                                   |
| Impostare le proprietà Blob | Sì                | Sì                                   |
| Ottenere Blob metadati   | Sì                | Sì                                   |
| Impostare Blob metadati   | Sì                | Sì                                   |
| Leasing Blob (*)      | Sì                | Sì                                   |
| Blob di snapshot       | Sì                | Sì                                   |
| Blob di copia           | Sì                | Sì (per blob di origine e destinazione) |
| Interrompere Blob copia     | No                 | No                                    |
| Eliminare Blob         | No                 | Sì                                   |
| Inserire blocco           | No                 | No                                    |
| Inserire l'elenco a blocchi      | Sì                | Sì                                   |
| Ottenere l'elenco a blocchi      | Sì                | No                                    |
| Inserire una pagina            | Sì                | Sì                                   |
| Ottenere gli intervalli di pagine     | Sì                | Sì                                   |


(*) Leasing Blob non cambia ETag in un blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Concorrenza pessimistica per BLOB di
Per bloccare un blob in accesso esclusivo, è possibile acquistare un [leasing](http://msdn.microsoft.com/library/azure/ee691972.aspx) su di esso. Quando si acquisisce un leasing, è necessario specificare per quanto tempo occorre il leasing: può essere per tra 15-60 secondi o infinito che gli importi di un blocco esclusivo. È possibile rinnovare un leasing finito di estendere il e rilasciare qualsiasi leasing dopo aver terminato l'utilizzo. Il servizio blob rilascia leasing finito automaticamente alla scadenza.  

Leasing abilitare strategie di sincronizzazione diverso devono essere supportati, tra cui scrittura esclusivo / condiviso scrittura esclusivo, lettura / esclusiva lettura e scrittura condivisi / leggere estremi esclusi. Se esiste un leasing il servizio di archiviazione vengono applicate esclusiva scrive (inserire, configurare ed eliminare operazioni) tuttavia garantire esclusiva per operazioni di lettura richiede sviluppo garantire che tutte le applicazioni client utilizzano un ID leasing e che solo un client alla volta con un ID di leasing valido. Operazioni di lettura che non includono un risultato di ID leasing in legge condivisi.  

Il frammento c# riportato di seguito è illustrato un esempio di acquisizione di un leasing esclusivo per 30 secondi in un blob, aggiornare il contenuto del blob e quindi rilasciare il leasing. Se è già presente un leasing valido nel blob quando si tenta di acquisire una nuova leasing, il servizio blob restituisce un risultato di stato "Conflitto HTTP (409)". Il frammento di codice seguente utilizza un oggetto **AccessCondition** per racchiudere informazioni leasing quando si effettua una richiesta di aggiornamento del servizio di archiviazione blob.  È possibile scaricare l'esempio di completo: [Gestione della concorrenza usando archiviazione Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Se si tenta di un'operazione di scrittura su un blob dedicata senza passare l'ID di leasing, con un errore di 412 la richiesta. Si noti che se scadenza prima di chiamare il metodo **UploadText** , ma è comunque passare l'ID di leasing, la richiesta anche non riesce con un errore **412** . Per ulteriori informazioni sulla gestione leasing ID e ore di scadenza leasing, vedere la documentazione di resto [Leasing Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) .  

Le seguenti operazioni blob possono utilizzare leasing per gestire la concorrenza pessimistica:  


-   Inserire Blob
-   Ottenere Blob
-   Ottenere le proprietà Blob
-   Impostare le proprietà Blob
-   Ottenere Blob metadati
-   Impostare Blob metadati
-   Eliminare Blob
-   Inserire blocco
-   Inserire l'elenco a blocchi
-   Ottenere l'elenco a blocchi
-   Inserire una pagina
-   Ottenere gli intervalli di pagine
-   Snapshot Blob - ID leasing facoltativo se è presente un leasing
-   Copia di Blob - leasing ID richiesto se un leasing esiste nel blob destinazione
-   Interrompere Blob di copia - leasing ID necessario se è presente un leasing infinito su blob destinazione
-   Blob di leasing  

### <a name="pessimistic-concurrency-for-containers"></a>Concorrenza pessimistica per i contenitori
Leasing sui contenitori abilitare stesso strategie di sincronizzazione per il supporto come su BLOB (esclusiva scrivere / condiviso scrittura letto, estremi esclusivo / esclusiva lettura e scrittura condivisi / leggere esclusivo) tuttavia diversamente da quanto succede BLOB il servizio di archiviazione solo vengono applicate esclusiva in operazioni di eliminazione. Per eliminare un contenitore di con un leasing attivo, un client deve includere l'ID di leasing attivo con la richiesta di eliminazione. Tutte le altre operazioni contenitore ha esito positivo in un contenitore dedicato senza includere l'ID di leasing nel qual caso sono condivise operazioni. Se è obbligatoria l'esclusiva di aggiornamento (inserire o set) o operazioni di lettura gli sviluppatori devono assicurarsi quindi che tutti i client di utilizzano un ID leasing e che solo uno client alla volta dispone di un ID leasing valido.  

Le seguenti operazioni contenitore possono utilizzare leasing per gestire la concorrenza pessimistica:  

-   Elimina contenitore
-   Ottenere le proprietà contenitore
-   Ottenere i metadati contenitore
-   Impostare i metadati contenitore
-   Ottenere contenitore ACL
-   Impostare ACL contenitore
-   Leasing contenitore  

Per ulteriori informazioni, vedere:  

- [Specificare intestazioni condizionale per operazioni di servizio Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Leasing contenitore](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Blob di leasing](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gestione della concorrenza nel servizio di tabella
Il servizio di tabella utilizza ottimistica ottimistica come il comportamento predefinito quando si lavora con entità, a differenza di servizio blob in cui è necessario scegliere in modo esplicito per eseguire le verifiche ottimistica. Differenza tra i servizi di tabella e blob è che è possibile gestire il comportamento della concorrenza di entità solo mentre con il servizio blob è possibile gestire l'esecuzione simultanea di contenitori ed BLOB.  

Per utilizzare ottimistica e per verificare se un altro processo modificate un'entità è stato recuperato dal servizio di archiviazione tabella, è possibile usare il valore ETag che viene visualizzato quando il servizio di tabella restituisce un'entità. La struttura di questo processo è il seguente:  

1.  Recuperare un'entità dal servizio di archiviazione tabella, la risposta include un valore ETag che identifica l'identificatore corrente associata all'entità del servizio di archiviazione.
2.  Quando si aggiorna l'entità, includere il valore di ETag ottenuto nel passaggio 1 nell'intestazione **If-Match** obbligatoria della richiesta di che inviare al servizio.
3.  Il servizio Confronta valore ETag nella richiesta con il valore ETag corrente dell'entità.
4.  Se il valore ETag corrente dell'entità è diverso da quello ETag nell'intestazione **If-Match** obbligatorio nella richiesta, il servizio restituisce un errore 412 al client. Indica che un altro processo è aggiornato l'entità dal momento che è stato recuperato il client al client.
5.  Se il valore ETag corrente dell'entità è diverso da quello di ETag nell'intestazione **If-Match** obbligatorio nella richiesta o intestazione **If-Match** contiene l'asterisco (*), il servizio esegue l'operazione e aggiorna il valore ETag corrente dell'entità per mostrare che è stato aggiornato.  

Si noti che, a differenza di servizio blob servizio tabella richiede il client per includere un'intestazione **If-Match** in richieste di aggiornamento. Tuttavia, è possibile forzare una non condizionale aggiornare (ultima writer wins strategia) ed evitare ottimistica, se il client imposta l'intestazione **If-Match** per l'asterisco (*) nella richiesta.  

Il frammento c# seguente mostra un'entità cliente creata in precedenza uno o l'indirizzo di posta elettronica aggiornato da recuperare. Iniziali inserire o recuperare archivi operazione valore ETag nell'oggetto customer e poiché l'esempio viene utilizzata la stessa istanza di oggetto quando viene eseguita l'operazione di sostituzione, invia automaticamente il valore ETag al servizio tabella, attivazione del servizio verificare la presenza di violazioni della concorrenza. Se un altro processo è aggiornato l'entità in archiviazione tabella, il servizio restituisce un messaggio di stato HTTP 412 (preliminare non).  È possibile scaricare l'esempio di completo: [Gestione della concorrenza usando archiviazione Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Per disabilitare il controllo della concorrenza, è necessario impostare la proprietà **ETag** dell'oggetto **dipendente** da "*" prima di eseguire l'operazione di sostituzione.  

    customer.ETag = "*";  

Nella tabella seguente vengono riepilogate come le operazioni di entità tabella utilizzano ETag valori:

| Operazione                | Restituisce il valore ETag | Intestazione di richiesta di corrispondenza se è necessario |
|:-------------------------|:-------------------|:---------------------------------|
| Entità di query           | Sì                | No                               |
| Inserire entità            | Sì                | No                               |
| Aggiornare entità            | Sì                | Sì                              |
| Unire entità             | Sì                | Sì                              |
| Eliminare entità            | No                 | Sì                              |
| Inserire o sostituire entità | Sì                | No                               |
| Inserire o unire entità   | Sì                | No                               |

Si noti che operazioni **Inserisci o giuridiche sostituire** e **Inserisci o unire entità** *non* eseguire qualsiasi ottimistica poiché non inviano un valore ETag per il servizio di tabella.  

In generale gli sviluppatori che utilizzano tabelle devono basarsi su ottimistica quando si sviluppano applicazioni scalable. Se il blocco pessimistica è necessario, gli sviluppatori di un approccio possono richiedere quando l'accesso alle tabelle per assegnare un blob designato per ogni tabella e provare a impostare un leasing il blob prima della tabella. Questo approccio richiedono l'applicazione per assicurarsi che tutti i percorsi di access dati ottengano leasing prima di funzionamento della tabella. Si noti che ora leasing minimo è 15 secondi che richiede attenzione per scalabilità.  

Per ulteriori informazioni, vedere:  

- [Operazioni sulle entità](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gestione della concorrenza nel servizio di coda
Uno scenario che in cui concorrenza è un problema nel servizio di accodamento è nel punto in cui più client vengono recuperati i messaggi da una coda. Quando un messaggio viene recuperato dalla coda, la risposta include il messaggio e un valore di conferma pop, è necessario eliminare il messaggio. Il messaggio non viene eliminato dalla coda automaticamente, ma dopo il recupero, non è visibile agli altri client per l'intervallo di tempo specificato dal parametro visibilitytimeout. Per eliminare il messaggio dopo che è stata elaborata e prima di tempo specificato per il TimeNextVisible elemento della risposta, viene calcolata in base al valore del parametro visibilitytimeout è previsto il client che recupera il messaggio. Il valore di visibilitytimeout viene aggiunto al momento in cui il recupero del messaggio per determinare il valore di TimeNextVisible.  

Il servizio di coda non dispone di supporto per la concorrenza ottimistica e pessimistica e per questo client motivo elaborazione dei messaggi recuperati da una coda deve assicurarsi che i messaggi vengono elaborati in modo idempotenti. Una strategia di wins writer ultima viene usata per le operazioni di aggiornamento, ad esempio SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Per ulteriori informazioni, vedere:  

- [API REST servizio coda](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Ricevere i messaggi](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gestione della concorrenza nel servizio di File
Il servizio di file è possibile accedervi utilizzando due endpoint – protocollo diverso da piccole e medie imprese e altre applicazioni. Servizio REST non dispone di supporto per il blocco ottimistica o blocco pessimistiche e tutti gli aggiornamenti seguiranno una strategia di wins writer ultima. I client di piccole e medie imprese montaggio condivisioni file possono utilizzare i meccanismi di blocco file system per gestire l'accesso ai file condivisi, compresa la possibilità di eseguire il blocco pessimistica. Quando un client SMB si apre un file, consente di specificare l'accesso ai file e il Condividi modalità. L'impostazione di un'opzione di accesso ai File di "Redazione" o "Lettura/scrittura" insieme a una modalità di condivisione File di "Nessuna" determinerà il file è bloccato da un client SMB fino a quando il file è chiuso. Se si tenta di operazione di resto in un file in un client SMB ha bloccato il file servizio REST restituirà il codice di stato 409 (conflitto) con codice errore SharingViolation.  

Quando un client SMB si apre un file per l'eliminazione, contrassegna il file come in attesa di eliminazione fino a tutti gli altri client SMB vengono chiuse handle aperti su tale file. Mentre un file è segnato come in attesa di eliminazione, operazioni resto su tale file verranno restituito il codice di stato 409 (conflitto) con codice errore SMBDeletePending. Codice di stato 404 (non trovato) non viene restituito dal momento che è possibile che il client di piccole e medie imprese per rimuovere il contrassegno di eliminazione in sospeso prima di chiudere il file. In altre parole, il codice di stato 404 (non trovato) è previsto solo quando il file è stato rimosso. Nota mentre è un file in un pacchetto SMB in sospeso Elimina stato, verrà non inclusi nei risultati della file dell'elenco. Si noti inoltre le operazioni di resto Elimina tutti i File e resto eliminare Directory vengono approvate in modo separato che non vengano in sospeso Elimina stato.  

Per ulteriori informazioni, vedere:  

- [Gestione dei File bloccato](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Riepilogo e passaggi successivi
Il servizio di archiviazione Microsoft Azure è stato progettato per soddisfare le esigenze delle applicazioni online più complesse evitando agli sviluppatori di compromettere o riprogettare ipotesi chiave della progettazione, ad esempio la coerenza concorrenza e dati provenienti da eseguire concesso.  

Per l'applicazione di esempio completo a cui fa riferimento in questo blog:  

- [Gestione della concorrenza utilizzando l'archiviazione di Azure - applicazione di esempio](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Per ulteriori informazioni vedere lo spazio di archiviazione di Azure:  

- [Pagina iniziale di spazio di archiviazione di Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introduzione all'archiviazione Azure](storage-introduction.md)
- Guida introduttiva per [Blob](storage-dotnet-how-to-use-blobs.md), [tabella](storage-dotnet-how-to-use-tables.md), [code](storage-dotnet-how-to-use-queues.md)e [file](storage-dotnet-how-to-use-files.md) lo spazio di archiviazione
- Architettura di archiviazione: [Azure dello spazio di archiviazione: un servizio di archiviazione Cloud disponibilità con coerenza sicuro](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
