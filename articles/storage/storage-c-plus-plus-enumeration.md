<properties
    pageTitle="Elenco risorse di archiviazione Azure con la raccolta di Client di Microsoft Azure lo spazio di archiviazione per C++ | Microsoft Azure"
    description="Informazioni su come utilizzare l'elenco API nella libreria Client di Microsoft Azure lo spazio di archiviazione per C++ enumerare contenitori, BLOB, code, tabelle ed entità."
    documentationCenter=".net"
    services="storage"
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

# <a name="list-azure-storage-resources-in-c"></a>Elenco risorse di archiviazione Azure in C++

Elenco operazioni sono fondamentali per molti scenari di sviluppo con lo spazio di archiviazione di Azure. In questo articolo viene descritto come in modo più efficiente consente di enumerare gli oggetti in archiviazione Azure con l'elenco API fornite nella libreria di Client di Microsoft Azure lo spazio di archiviazione per C++.

>[AZURE.NOTE] Questa guida è destinato alla libreria di Client Azure lo spazio di archiviazione per la versione C++ 2. x, è disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).

La raccolta di Client lo spazio di archiviazione offre diversi metodi per gli oggetti di elenco o una query in archiviazione Azure. Questo articolo riguarda gli scenari seguenti:

-   Contenitori di elenco in un account
-   BLOB di elenco in un contenitore o una directory di blob virtuale
-   Elenco code in un account
-   Elencare le tabelle in un account
-   Entità di query in una tabella

Ognuno di questi metodi viene visualizzato utilizzando overload diversi per diversi scenari.

## <a name="asynchronous-versus-synchronous"></a>Asincrono rispetto a icona del

Poiché la libreria di Client lo spazio di archiviazione per C++ si basa nella parte superiore della [raccolta C++ resto](https://github.com/Microsoft/cpprestsdk), è progettato per supportare operazioni asincrone utilizzando [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Per esempio:

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Icona del operazioni a capo asincrone operazioni corrispondenti:

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Se si lavora con più applicazioni o servizi thread, è consigliabile usare asincrono API direttamente invece di creare un thread per chiamare la sincronizzazione API, in modo significativo impatto sulle prestazioni.

## <a name="segmented-listing"></a>Elenco segmentato

La scala di spazio di archiviazione cloud richiede segmentato voce. Ad esempio, è su un milione BLOB in un contenitore di blob Azure o su un miliardi entità in una tabella di Azure. Non sono numeri teorici, ma casi di utilizzo di clienti reali.

È quindi possibile per visualizzare un elenco di tutti gli oggetti in una singola risposta. Elencare invece gli oggetti con suddivisione in pagine. Ogni elenco API dispone di un *segmentato* overload.

La risposta per un'operazione su una voce segmentato include:

-   <i>_segment</i>, che contiene il set di risultati restituiti per una singola chiamata all'API di elenco.
-   *continuation_token*, che vengono inviate a partecipare alla chiamata successiva per ottenere la pagina dei risultati. Quando non sono presenti più risultati da restituire, il token di continuazione è null.

Ad esempio, una tipica chiamata per visualizzare un elenco di tutti i BLOB in un contenitore di potrebbe apparire come il frammento di codice seguenti. Il codice è disponibile il nostro [esempi](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Si noti che il numero di risultati restituiti in una pagina può essere controllato mediante il parametro *max_results* in overload di ogni API, ad esempio:

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Se non si specifica il parametro *max_results* , viene restituito il valore massimo predefinito di un massimo di 5000 risultati in una singola pagina.

Si noti inoltre che una query su archivio tabelle Azure può restituire alcun record, o meno rispetto al valore del parametro *max_results* che è stata specificata, anche se il token di continuazione non è vuoto. Potrebbe essere uno dei motivi che la query Impossibile completare in cinque secondi. Come il token di continuazione non è vuoto, la query deve continuare e il codice non certo le dimensioni dei risultati di segmento.

Il modello di codifica consigliato maggior parte dei casi è segmentato voce, che fornisce esplicito stato di avanzamento dell'elenco o esecuzione di query e come il servizio risponde a ogni richiesta. In particolare per applicazioni C++ o servizi, controllo di livello inferiore sullo stato di avanzamento voce può contribuire controllo memoria e prestazioni.

## <a name="greedy-listing"></a>Elenco generici

Le versioni precedenti di libreria Client lo spazio di archiviazione per C++ (versioni 0.5.0 visualizzare in anteprima e versioni precedenti) incluso non segmentato voce API per le tabelle e code, come illustrato nell'esempio seguente:

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Questi metodi sono stati implementati come wrapper delle API segmentate. Per ogni risposta dell'elenco segmentata, il codice aggiunto i risultati in un vettore e restituiti tutti i risultati dopo i contenitori completi digitalizzati.

Questo approccio potrebbe funzionare quando l'account di archiviazione o la tabella contiene un numero limitato di oggetti. Tuttavia, con un aumento del numero di oggetti, la memoria necessaria potrebbe aumentare senza limitazioni, perché tutti i risultati rimanevano in memoria. Una sola voce operazione può richiedere molto tempo, in cui il chiamante non dispone di alcun informazioni dello stato di avanzamento.

Questa voce generici API in SDK non sono presenti nel c#, Java o l'ambiente JavaScript Node. Per evitare potenziali problemi dell'uso di queste API generici, è stato rimosso nella versione 0.6.0 anteprima.

Se il codice chiama queste API generici:

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

È quindi necessario modificare il codice per usare l'elenco segmentato API:

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

Se si specifica il parametro *max_results* del segmento, è possibile saldare tra i numeri delle richieste e l'utilizzo di memoria in base alle prestazioni considerazioni per l'applicazione.

Inoltre, se si usa elenco segmentato API ma archiviare i dati in una raccolta locale in uno stile "generici", è anche consigliabile refactoring il codice per gestire l'archiviazione dei dati in una raccolta locale con attenzione in scala.

## <a name="lazy-listing"></a>Elenco lenta

Sebbene elenco generici generato potenziali problemi, è utile se sono presenti troppi oggetti nel contenitore.

Se si usano anche c# o Oracle linguaggio SDK, è necessario avere familiarità con il modello di programmazione enumerabile, che offre un prato stile elenco, in cui vengono recuperati solo i dati da un determinato offset se necessario. In C++, il modello di iteratore vengono forniti anche un approccio simile.

Un elenco di Prato tipico API, utilizzando **list_blobs** come esempio, è simile alla seguente:

    list_blob_item_iterator list_blobs() const;

Un frammento di codice tipica che utilizza il modello di elenco Prato essere simile al seguente:

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Si noti che Prato voce è disponibile solo in modalità icona del.

Confronto con elenco generici, elenco Prato recupera i dati solo quando necessario. Dietro le quinte, il recupero dei dati dallo spazio di archiviazione di Azure solo quando l'iteratore successiva entra nello segmento successivo. Pertanto, l'utilizzo di memoria verrà controllata con dimensioni limitate e l'operazione è veloce.

Elenco Prato API sono incluse nella libreria Client lo spazio di archiviazione per C++ nella versione 2.2.0.

## <a name="conclusion"></a>Conclusioni

In questo articolo sono illustrati overload diversi per elencare le API per diversi oggetti nella libreria Client lo spazio di archiviazione per C++. Per riepilogare:

-   È consigliabile Async APIs in più scenari thread.
-   Elenco segmentato è consigliabile maggior parte dei casi.
-   Elenco Prato viene fornito nella libreria come un pratico spaziale in scenari icona del.
-   Elenco generici non è consigliabile ed è stata rimossa dalla raccolta.

##<a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'archiviazione di Azure e libreria Client per C++, vedere le risorse seguenti.

-   [Come usare archiviazione Blob da C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Come utilizzare lo spazio di archiviazione tabella da C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Come usare coda lo spazio di archiviazione da C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Raccolta Azure Client di spazio di archiviazione per la documentazione API C++.](http://azure.github.io/azure-storage-cpp/)
-   [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
