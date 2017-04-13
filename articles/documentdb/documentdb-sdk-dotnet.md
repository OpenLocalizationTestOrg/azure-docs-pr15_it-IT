<properties 
    pageTitle="API di .NET DocumentDB & SDK | Microsoft Azure" 
    description="Tutte le informazioni di API .NET SDK e date di rilascio, pensionistico date e le modifiche apportate tra ogni versione di .NET SDK DocumentDB." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB e SDK 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>SDK e API .NET DocumentDB

<table>
<tr><td>**Download SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentazione dell'API**</td><td>[Documentazione di riferimento API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Esempi**</td><td>[Esempi di codice .NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Guida introduttiva**</td><td>[Guida introduttiva a DocumentDB .NET SDK](documentdb-get-started.md)</td></tr>
<tr><td>**Esercitazione app Web**</td><td>[Sviluppo di applicazioni Web con DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**Framework supportati corrente**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

> [AZURE.IMPORTANT] A partire dalla versione 1.9.2, potrebbe essere visualizzato System. NotSupportedException quando le query di raccolte partizionate. Per evitare questo errore, verificare che il processo di host è a 64 bit. Per i progetti eseguibili, ciò è possibile se si deseleziona l'opzione "Mantieni versione a 32 bit" nella finestra delle proprietà del progetto, nella scheda Crea.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Connettività diretta aggiunto il supporto per raccolte partizionate.
  - Miglioramento delle prestazioni per il livello di coerenza delimitata obsolescenza.
  - Aggiunte poligono e LineString DataTypes durante la specifica raccolta indicizzazione criteri per le query spaziale recinzione geografico.
  - Aggiunta LINQ del supporto per StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter durante la conversione predicati.
  - Varie correzioni di bug SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Fissa un problema che causa NotFoundException seguenti: la sessione di lettura non è disponibile per il token di input sessione. Questa eccezione si è verificato in alcuni casi, quando si cercano l'area di lettura di un account distribuito geografico.
  - Proprietà ResponseStream nella classe ResourceResponse, che consente l'accesso diretto al flusso sottostante da una risposta esposta.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Modificare le classi ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse per implementare l'interfaccia pubblico corrispondente, in modo che possono essere esempio per test basata sulle risorse di distribuzione (TDD).
  - Fissa un problema che causa un'intestazione di chiave partizione non valido quando si utilizza un oggetto JsonSerializerSettings personalizzato per serializzazione dei dati.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Fissa un problema che causa l'esecuzione delle query lunga sulle con errore: token di autorizzazione non è valido al momento corrente.
  - È stato risolto un problema che rimossi SqlParameterCollection originale da cross partizione superiore/order by query.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Aggiunta del supporto per query parallele per gli insiemi partizionati.
  - Aggiunta del supporto per cross partizione query ORDER BY e superiore per le raccolte partizionate.
  - Fissa i riferimenti mancanti DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll necessari quando si fa riferimento un progetto DocumentDB con un riferimento il pacchetto DocumentDB Nuget.
  - Corretta la possibilità di utilizzare i parametri di tipo diverso quando si utilizza le funzioni definite dall'utente in LINQ. 
  - Correzione di un problema per gli account globalmente replicati nel punto in cui sono state rivolti Upsert chiamate leggere percorsi anziché posizioni di scrittura.
  - Metodi aggiunti all'interfaccia IDocumentClient che erano presenti: 
      - Metodo UpsertAttachmentAsync che accetta mediaStream e opzioni come parametri
      - Metodo CreateAttachmentAsync che entrerà in opzioni come parametro
      - Metodo CreateOfferQuery che accetta querySpec come parametro.
  - Classi pubbliche resi pubblici esposti nell'interfaccia di IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Il supporto per gli account di database più aree.
  - Aggiunta del supporto per Riprova sulle richieste limitate.  Utente può personalizzare il numero di tentativi e il tempo di attesa max configurando la proprietà ConnectionPolicy.RetryOptions.
  - Aggiungere una nuova interfaccia IDocumentClient che consente di definire le firme di tutti i metodi e proprietà DocumenClient.  Come parte di questa modifica, modificare anche i metodi di estensione che creare IQueryable e IOrderedQueryable a metodi sulla classe DocumentClient stessa.
  - Aggiunta l'opzione di configurazione per impostare ServicePoint.ConnectionLimit per un determinato endpoint DocumentDB Uri.  Per modificare il valore predefinito è impostato su 50, usare ConnectionPolicy.MaxConnectionLimit.
  - IPartitionResolver obsoleto e l'implementazione.  Supporto per IPartitionResolver è obsoleto. Si consiglia di utilizzare raccolte suddivisi per velocità e l'archiviazione superiore.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Aggiunta in base a un overload URI metodo ExecuteStoredProcedureAsync che accetta RequestOptions come parametro.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Maggiore tempo di assistenza in tempo reale (TTL) per i documenti.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Correzione di un problema nella confezione Nuget di .NET SDK per creare il pacchetto come parte di una soluzione di servizio Cloud di Azure.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementato [raccolte partizionate](documentdb-partition-data.md) e [livelli di prestazioni definite dall'utente](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fisso]** Query genera endpoint DocumentDB: ' System.Net.Http.HttpRequestException: errore durante la copia di contenuto in un flusso.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - LINQ espanso supportano inclusi nuovi operatori per le espressioni condizionali, spostamento e l'intervallo di confronto.
    - Scrivere operatore per abilitare il comportamento selezionare superiore in LINQ
    - Operatore di CompareTo per attivare i confronti intervallo stringa
    - Condizionale (?) e riunire operatori (?)
  - **[Fisso]** ArgumentOutOfRangeException quando si combinano proiezione modello con la posizione In query linq.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fisso]** Se selezionare non è l'ultima espressione Provider LINQ non presunto proiezione e prodotti selezionare * in modo non corretto.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementato, i metodi di aggiunta UpsertXXXAsync
 - Miglioramenti delle prestazioni per tutte le richieste
 - Supporto del Provider LINQ per condizionale, riunire e i metodi CompareTo per stringhe
 - **[Fisso]** Provider LINQ--> implementare contiene metodo nell'elenco per generare il codice SQL stessa come IEnumerable e in forma di matrice
 - **[Fisso]** BackoffRetryUtility utilizza la stessa HttpRequestMessage nuovamente invece di creare una nuova in un tentativo successivo
 - **[Obsoleto]** --> UriFactory.CreateCollection è ora necessario utilizzare UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fisso]** Problemi di localizzazione quando si usa informazioni sulle impostazioni cultura en non, ad esempio nl-NL e così via. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Il Routing basato su ID
    - Nuovo supporto UriFactory per agevolare la costruzione ID in base a collegamenti a risorse
    - Nuovi overload su DocumentClient per scrivere in URI
  - Aggiunte IsValid() e IsValidDetailed() in LINQ per geospaziali
  - Supporto di Provider LINQ avanzato
    - ARCTAN **matematiche** - ARCSEN Ass, ARCCOS,, ARROTONDA. eccesso, Cos, Exp, planimetrie, Log, Log10, Pow, Round, accedi, Sin, Sqrt Tan, tronca
    - **Stringa** - concatena, contiene, EndsWith, IndexOf, conteggio, ToLower, TrimStart, sostituire, invertire, TrimEnd, StartsWith, sottostringa, ToUpper
    - **Matrice** - concatena, contiene, conta. numeri
    - Operatore **IN**

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Aggiunta del supporto per la modifica dei criteri di indicizzazione
    - Nuovo metodo di ReplaceDocumentCollectionAsync in DocumentClient
    - Nuova proprietà IndexTransformationProgress in ResourceResponse<T> per verificare l'avanzamento percentuale delle modifiche ai criteri indice
    - DocumentCollection.IndexingPolicy è modificabile
  - Aggiunta del supporto per la query e la reindicizzazione spaziale
    - Nuovo spazio dei nomi Microsoft.Azure.Documents.Spatial per serializzazione/deserializzazione tipi spaziale come punto e poligono
    - Nuova classe SpatialIndex per l'indicizzazione GeoJSON dati archiviati in DocumentDB
  - **[Fisso]** : query SQL non corretti generate da linq espressione [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dipendenza da Newtonsoft.Json v5.0.7 
- Modifiche per supportare Order By
  - Supporto del provider LINQ per OrderBy() o OrderByDescending()
  - IndexingPolicy per supportare Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Supporto per divisione dati utilizzando le nuove classi HashPartitionResolver e RangePartitionResolver e il IPartitionResolver
- Serializzazione DataContract
- Supporto GUID nel provider LINQ
- Supporto di utente in LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
È stata apportata una modifica del nome pacchetto NuGet tra anteprima e GA. Siamo passati da **Microsoft.Azure.Documents.Client** a **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Anteprima SDK [obsoleto]

## <a name="release--retirement-dates"></a>Rilascio e ritiro dei date
Microsoft fornirà notifica almeno **12 mesi** prima ritirare un SDK per ridurre la transizione a una versione più recente/supportati.

Nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunti solo a SDK corrente, pertanto è consigliabile sempre eseguire l'aggiornamento alla versione più recente SDK il prima possibile. 

Qualsiasi richiesta di DocumentDB utilizzando un SDK ritirati verrà rifiutato dal servizio.

> [AZURE.WARNING]
Tutte le versioni di Azure DocumentDB SDK per .NET prima versione **1.0.0** verranno ritirate su **29 febbraio 2016**. 
 
<br/>
 
| Versione | Data di rilascio | Data pensionistico 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 27 settembre 2016 |---
| [1.9.5](#1.9.5) | 01 settembre 2016 |---
| [1.9.4](#1.9.4) | 24 agosto 2016 |---
| [1.9.3](#1.9.3) | 15 agosto 2016 |---
| [1.9.2](#1.9.2) | 23 luglio 2016 |---
| 1.9.1 | Obsoleta |---
| 1.9.0 | Obsoleta |---
| [1.8.0](#1.8.0) | 14 giugno 2016 |---
| [1.7.1](#1.7.1) | 06 maggio 2016 |---
| [1.7.0](#1.7.0) | 26 aprile 2016 |---
| [1.6.3](#1.6.3) | 08 aprile 2016 |---
| [1.6.2](#1.6.2) | 29 marzo 2016 |---
| [1.5.3](#1.5.3) | 19 febbraio 2016 |---
| [1.5.2](#1.5.2) | 14 dicembre 2015 |---
| [1.5.1](#1.5.1) | 23 novembre 2015 |---
| [1.5.0](#1.5.0) | 05 ottobre 2015 |---
| [1.4.1](#1.4.1) | 25 agosto 2015 |---
| [1.4.0](#1.4.0) | 13 agosto 2015 |---
| [1.3.0](#1.3.0) | 05 agosto 2015 |---
| [1.2.0](#1.2.0) | 06 luglio 2015 |---
| [1.1.0](#1.1.0) | 30 aprile 2015 |---
| [1.0.0](#1.0.0) | 08 aprile 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 marzo 2015 | 29 febbraio 2016
| [0.9.2-prelease](#0.9.x-preview) | Gennaio 2015 | 29 febbraio 2016
| [.9.1-prelease](#0.9.x-preview) | 13 ottobre 2014 | 29 febbraio 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 agosto 2014 | 29 febbraio 2016

## <a name="faq"></a>Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni su DocumentDB, vedere [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) servizio pagina. 
