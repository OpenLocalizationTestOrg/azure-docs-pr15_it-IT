<properties
    pageTitle="Come usare Fiddler per valutare e testare l'API REST di Azure ricerca | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Utilizzare Fiddler di un approccio senza codice per verificare la disponibilità di ricerca di Azure e valutando le API REST."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utilizzare Fiddler per valutare e testare le API REST di Azure ricerca
> [AZURE.SELECTOR]
- [Panoramica](search-query-overview.md)
- [Soluzioni di ricerca](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

In questo articolo viene illustrato come utilizzare Fiddler disponibili come [download gratuito Telerik](http://www.telerik.com/fiddler), le richieste di HTTP problema e Visualizza risposte utilizzando API REST di ricerca Azure, senza dover scrivere codice. Ricerca Azure è completamente gestito, ospitato servizio cloud di ricerca in Microsoft Azure, facilmente programmabile mediante .NET e le API REST. Il servizio di ricerca di Azure che vengono descritte le API REST su [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nei passaggi seguenti sarà di creare un indice, caricare documenti, l'indice della query e quindi eseguire una query di sistema per informazioni sui servizi.

Per completare questa procedura, è necessario un servizio di ricerca di Azure e `api-key`. Per istruzioni su come iniziare, vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) .

## <a name="create-an-index"></a>Creare un indice

1. Avviare Fiddler. Nel menu **File** , disattivare **Il traffico acquisire** nascondere estranei attività HTTP non correlati per l'attività corrente.

3. Nella scheda **compositore** è necessario formulare una richiesta di è simile alla schermata seguente.

    ![][1]

2. Selezionare **inserire**.

3. Immettere l'URL che specifica l'URL del servizio, gli attributi di richiesta e la versione dell'api. Alcuni puntatori da tenere presenti:
   + Utilizzare HTTPS come il prefisso.
   + Richiedere l'attributo è "/ indici/alberghi". In questo modo la ricerca per creare un indice denominato 'alberghi'.
   + Versione API è minuscola, specificato come "? versione api = 2015-02-28". Versioni di API sono importanti perché ricerca Azure distribuisce aggiornamenti regolarmente. In rari casi, un aggiornamento del servizio potrebbe introdurre una modifica all'API. Per questo motivo, Azure ricerca richiede una versione dell'api per ogni richiesta in modo che ci si trova il controllo completo sul quale viene usato.

    L'URL completo dovrebbe essere simile al seguente.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Specificare l'intestazione richiesta, sostituendo l'host e chiave api con valori validi per il servizio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  Nel corpo richiedere incollare i campi che costituiscono la definizione dell'indice.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Fare clic su **Esegui**.

In pochi secondi, verrà visualizzata una risposta HTTP 201 nell'elenco sessione che indica che l'indice è stato creato correttamente.

Se viene visualizzato 504 HTTP, verificare che l'URL specifica HTTPS. Se viene visualizzato HTTP 400 o 404, selezionare il corpo della richiesta per la verifica che nessun errore di copia e Incolla. Un HTTP 403 indica in genere un problema con la chiave dell'api (una chiave non valida o un problema di sintassi con specificata come la chiave dell'api).

## <a name="load-documents"></a>Caricare documenti

Nella scheda **compositore** la richiesta per inviare i documenti un aspetto simile al seguente. Corpo della convocazione contiene i dati di ricerca per 4 alberghi.

   ![][2]

1. Selezionare **Inserisci**.

2.  Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/ <'indexname ' >/documenti/indice? versione api = 2015-02-28". L'URL completo dovrebbe essere simile al seguente.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  Intestazione richiesta dovrebbe essere diverso da quello di prima. È necessario ricordare sostituito l'host e chiave api con valori validi per il servizio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Il corpo richiedere contiene quattro documenti da aggiungere all'indice alberghi.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
                "hotelName": "Fancy Stay",
                "category": "Luxury",
                "tags": ["pool", "view", "wifi", "concierge"],
                "parkingIncluded": false,
                "smokingAllowed": false,
                "lastRenovationDate": "2010-06-27T00:00:00Z",
                "rating": 5,
                "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
              },
              {
                "@search.action": "upload",
                "hotelId": "2",
                "baseRate": 79.99,
                "description": "Cheapest hotel in town",
                "hotelName": "Roach Motel",
                "category": "Budget",
                "tags": ["motel", "budget"],
                "parkingIncluded": true,
                "smokingAllowed": true,
                "lastRenovationDate": "1982-04-28T00:00:00Z",
                "rating": 1,
                "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
              },
              {
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Fare clic su **Esegui**.

In pochi secondi, verrà visualizzata una risposta HTTP 200 nell'elenco sessione. Indica che i documenti sono stati creati correttamente. Se si riceve un 207, almeno un documento non è possibile caricare. Se si riceve un 404, si verifica un errore di sintassi nell'intestazione o nel corpo della convocazione.

## <a name="query-the-index"></a>Ricerca dell'indice

Ora che un indice e i documenti vengono caricati, è possibile eseguire query in base a tali.  Nella scheda **compositore** un comando **GET** che esegue una query al servizio sarà simile alla schermata seguente.

   ![][3]

1.  Selezionare **OTTIENI**.

2.  Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/ <'indexname ' > / docs?", seguiti dai parametri di query. Titolo di esempio, utilizzare l'URL seguente e sostituire il nome host di esempio con una password è valida per il servizio.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Questa query cerca il termine "modelli" e recupera categorie facet per valutazioni.

3.  Intestazione richiesta dovrebbe essere diverso da quello di prima. È necessario ricordare sostituito l'host e chiave api con valori validi per il servizio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

Il codice di risposta deve essere 200 e l'output della risposta dovrebbe essere simile alla schermata seguente.

   ![][4]

La query di esempio seguente viene generato [operazione di indice di ricerca (API di ricerca Azure)](http://msdn.microsoft.com/library/dn798927.aspx) su MSDN. Molte delle query di esempio in questo argomento includere spazi, non è consentiti in Fiddler. Sostituisci ognuna in base allo spazio con il carattere + prima di incollarlo nella query stringa prima di eseguire la query in Fiddler.

**Prima di spazi vengono sostituiti:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Dopo che gli spazi vengono sostituiti con +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>Il sistema della query

È anche possibile richiedere il sistema per ottenere conteggi dei documenti e il consumo di spazio di archiviazione. Nella scheda **compositore** la richiesta sia un aspetto simile al seguente e la risposta restituirà un numero per il numero di documenti e spazio utilizzato.

 ![][5]

1.  Selezionare **OTTIENI**.

2.  Immettere l'URL che include l'URL del servizio, seguito da "/ indici/alberghi/Stat? versione api = 2015-02-28":

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Specificare l'intestazione richiesta, sostituendo l'host e chiave api con valori validi per il servizio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Lasciare vuoto il corpo della richiesta.

5.  Fare clic su **Esegui**. Verrà visualizzato un codice di stato HTTP 200 nell'elenco sessione. Selezionare la voce inserita per il comando.

6.  Fare clic sulla scheda **controlli** , fare clic sulla scheda **intestazioni** e quindi selezionare il formato JSON. Verrà visualizzata la dimensione di conta. numeri e lo spazio di archiviazione di documenti (in KB).

## <a name="next-steps"></a>Passaggi successivi

Vedere [gestire il servizio di ricerca in Azure](search-manage.md) per senza codice approccio per la gestione e funzionalità di ricerca di Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
