<properties
    pageTitle="Creare un indice di ricerca di Azure tramite l'API REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare un indice analitico nel codice tramite l'API REST di Azure ricerca HTTP."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Creare un indice di ricerca di Azure tramite l'API REST
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-an-index.md)
- [Portale](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)


In questo articolo verrà descritto il processo di creazione di un [indice](https://msdn.microsoft.com/library/azure/dn798941.aspx) di ricerca Azure tramite l'API REST di Azure ricerca.

Prima di seguire questa Guida e creare un indice analitico, è necessario avere già [creato un servizio di ricerca di Azure](search-create-service-portal.md).

Per creare un indice di ricerca di Azure tramite l'API REST, consente di rilasciare una singola richiesta HTTP POST ai endpoint dell'URL del servizio di ricerca di Azure. La definizione di indice sarà contenuta nel corpo della richiesta come contenuto JSON corretto.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identificare chiave dell'api amministratore del servizio di ricerca di Azure
Ora che hanno effettuato il provisioning di un servizio di ricerca di Azure, è possibile inviare richieste HTTP contro endpoint dell'URL del servizio tramite l'API REST. Tuttavia, le richieste di *tutte le* API devono includere la chiave dell'api generati per il servizio di ricerca che è stato effettuato il provisioning. Con una chiave valida stabilisce protezione, in base a ogni richiesta, tra l'applicazione di inviare la richiesta e il servizio che viene gestita.

1. Per trovare api tasti di scelta rapida del servizio è necessario accedere al [Portale di Azure](https://portal.azure.com/)
2. Passare a blade del servizio di ricerca di Azure
3. Fare clic sull'icona "tasti visualizzati"

Il servizio avrà *chiavi di amministrazione* e *le query*.

 - Le principali e secondarie *tasti amministratore* diritti completo a tutte le operazioni, compresa la possibilità di gestire il servizio, creare ed eliminare gli indici, indicizzatori e origini dati. Esistono due metodi in modo che è possibile continuare a usare il tasto secondario se si decide di rigenerare la chiave primaria e viceversa.
 - Le *chiavi query* concedere l'accesso in sola lettura per gli indici e i documenti e vengono in genere distribuiti per le applicazioni client che inviano richieste di ricerca.

Per creare un indice analitico, è possibile utilizzare la chiave primaria o secondaria amministratore.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Definire l'indice di ricerca di Azure utilizzando JSON corretto
Una singola richiesta HTTP POST al servizio creerà l'indice. Il corpo della richiesta HTTP POST conterrà un singolo oggetto JSON che definisce l'indice di ricerca di Azure.

1. La prima proprietà dell'oggetto JSON è il nome dell'indice.
2. La seconda proprietà dell'oggetto JSON è una matrice JSON denominata `fields` che contiene un oggetto JSON distinto per ogni campo dell'indice di. Ciascuno di essi JSON contengono più coppie nome/valore per ogni gli attributi di campo incluse "nome", "tipo" e così via.

È importante tenere la ricerca utente esperienza e le esigenze aziendali presenti quando si progetta l'indice di ogni campo deve essere assegnata gli [attributi appropriati](https://msdn.microsoft.com/library/azure/dn798941.aspx). Questi controllo attributi ricerca caratteristiche (filtro, faceting, l'ordinamento di ricerca full-text e così via) applicare ai campi. Per tutti gli attributi che non si specifica, il valore predefinito sarà per abilitare la funzionalità di ricerca corrispondente a meno che non si disattiva in particolare.

Nel caso dell'esempio sono state denominato nostro indice "alberghi" e definita dei campi nel modo seguente:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Avendo attentamente scelto gli attributi di indice per ogni campo in base a come si ritiene che verranno utilizzati in un'applicazione. Ad esempio `hotelId` rappresenta una chiave univoca che gli utenti cercano alberghi probabile che non sapranno, in modo che la disattivazione di ricerca full-text per il campo mediante l'impostazione `searchable` a `false`, che consente di risparmiare spazio nell'indice analitico.

Si noti che esattamente un campo dell'indice del tipo di `Edm.String` deve essere designato come il campo 'chiave '.

La definizione dell'indice sopra utilizza un analizzatore del linguaggio personalizzato per il `description_fr` campo perché viene utilizzato per archiviare testo francese. Vedere [la lingua supportata argomento su MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) , nonché il corrispondente [il post di blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) per ulteriori informazioni sull'analisi del linguaggio.

## <a name="iii-issue-the-http-request"></a>III. Pubblicare la richiesta HTTP
1. Utilizzando la definizione di indice come corpo della richiesta, inviare una richiesta HTTP POST per l'URL endpoint del servizio di ricerca di Azure. Nell'URL, assicurarsi di usare il nome del servizio come il nome host e inserire corretta `api-version` come un parametro stringa query (la versione corrente di API `2015-02-28` al momento della pubblicazione di questo documento).
2. Nelle intestazioni di richiesta, specificare la `Content-Type` come `application/json`. È anche necessario fornire chiave di amministratore del servizio identificato nel passaggio I nel `api-key` intestazione.


È necessario specificare la chiave di nome e l'api di servizio per inviare la richiesta riportata di seguito:


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Per una richiesta ha esito positivo, verrà visualizzato il codice di stato 201 (creata). Per ulteriori informazioni sulla creazione di un indice tramite l'API REST, visitare il riferimento all'API su [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Per ulteriori informazioni su altri codici di stato HTTP che potrebbero essere restituite in caso di errore, vedere [codici di stato HTTP (Azure ricerca)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Quando si termine con un indice e si desidera eliminarlo, basta inviare una richiesta HTTP DELETE. Ad esempio, si tratta come consentirebbe di eliminare l'indice "alberghi":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Successivo
Dopo aver creato un indice di ricerca di Azure, sarà possibile caricare [il contenuto in corrispondenza dell'indice](search-what-is-data-import.md) in modo che è possibile avviare la ricerca dei dati.
