<properties 
    pageTitle="Guida introduttiva: Machine Learning consigli API | Microsoft Azure" 
    description="Azure apprendimento consigli - Guida introduttiva" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>Guida introduttiva per l'API di consigli risorse computer

>[AZURE.NOTE]È necessario iniziare a usare il servizio di Cognitive API consigli invece di questa versione. Il servizio Cognitive consigli andrà a sostituire il servizio e tutte le nuove funzionalità da sviluppare sono. Offre nuove funzionalità, come il batch di supporto, un migliore API Explorer, un chiaro esperienza di superficie, più coerente l'iscrizione/fatturazione API e così via.
> Ulteriori informazioni sulla [migrazione al nuovo servizio cognitiva](http://aka.ms/recomigrate)


In questo documento viene come incorporata a un servizio o applicazione per l'utilizzo di Microsoft Azure Machine Learning consigli. Sono disponibili ulteriori informazioni sull'API consigli nella [raccolta](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>Informazioni generali

Per utilizzare Azure Machine Learning raccomandazioni, è necessario eseguire le operazioni seguenti:

* Creare un modello, un modello è un contenitore di dati di utilizzo, catalogo dati e il modello di recommendation.
* Importazione del catalogo dati, un catalogo contengano metadati informazioni sugli elementi. 
* Importa dati di utilizzo - dati di utilizzo possono essere caricati in uno dei 2 modi (o entrambe):
    * Per caricare un file che contiene i dati di utilizzo.
    * Tramite l'invio di eventi di acquisizione di dati.
    In genere si carica un file di utilizzo per poter creare un modello di recommendation iniziale (avvio) e usarla fino a quando il sistema raccoglie abbastanza dati utilizzando il formato di acquisizione di dati.
* Creazione di un modello di recommendation: questa è un'operazione asincrona in cui il sistema di recommendation accetta tutti i dati di utilizzo e viene creato un modello di recommendation. Questa operazione può richiedere alcuni minuti o alcune ore a seconda delle dimensioni dei dati e i parametri di configurazione di compilazione. Durante la compilazione di trigger, verrà visualizzato un ID di compilazione. Utilizzarla per verificare quando è terminato il processo di compilazione prima di iniziare a utilizzare i suggerimenti.
* Utilizzare consigli - ottenere suggerimenti per un elemento specifico o un elenco di elementi.

Tutti i passaggi indicati vengono eseguiti tramite l'API di Azure Machine Learning consigli.  È possibile scaricare un'applicazione di esempio che implementa ognuna di queste operazioni dal [raccolta nonché.](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>Limitazioni

* Il numero massimo di modelli per abbonamento è 10.
* Il numero massimo di elementi che può contenere un catalogo è 100.000.
* Il numero massimo di punti in cui vengono mantenute è ~ 5.000.000. Meno recente verrà eliminato verranno caricati o segnalati nuovi file.
* Le dimensioni massime dei dati che è possono inviare post (ad esempio importazione catalogo dati, importare dati di utilizzo) sono 200MB.
* Il numero delle transazioni al secondo di generazione modello recommendation che non è attiva è ~ 2TPS. Genera un modello recommendation attivo può contenere fino a 20TPS.

##<a name="integration"></a>Integrazione

###<a name="authentication"></a>Autenticazione
Microsoft Azure Marketplace supporta Basic o OAuth metodo di autenticazione. È possibile trovare facilmente i tasti di account posizionandosi i tasti di marketplace nelle [impostazioni dell'account](https://datamarket.azure.com/account/keys). 
####<a name="basic-authentication"></a>Autenticazione di base
Aggiungere l'intestazione di autorizzazione:

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
Convertire in base 64 (c#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
Convertire in base 64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>URI del servizio
La radice di servizio URI per le API di Azure Machine Learning consigli è [qui.](https://api.datamarket.azure.com/amla/recommendations/v2/)

Il servizio completo URI è espresso con gli elementi della specifica OData.

###<a name="api-version"></a>Versione API
Al termine, ogni chiamata API sarà disponibile un parametro di query denominato apiVersion che deve essere impostata su "1.0".

###<a name="ids-are-case-sensitive"></a>ID sono tra maiuscole e minuscole
ID, restituiti da una delle API, tra maiuscole e minuscole e deve essere utilizzato come tale quando passate come parametri nelle chiamate API successive. Ad esempio gli ID di modello e del catalogo sono tra maiuscole e minuscole.

###<a name="create-a-model"></a>Creare un modello
Creazione di una richiesta di "creare modello":

| Metodo HTTP | URI |
|:--------|:--------|
|Inserisci     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
|   modelName   |   Solo lettere (a Z, a-z), numeri (0-9), trattini (-) e carattere di sottolineatura (_) consentiti.<br>La lunghezza massima consentita: 20 |
|   apiVersion      | 1.0 |
|||
| Corpo della richiesta | NESSUNO |


**Risposta**:

Codice di stato HTTP: 200

- `feed/entry/content/properties/id`-Contiene ID del modello.
**Nota**: l'ID di modello è tra maiuscole e minuscole.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>Importare dati del catalogo

Se si carica più file di catalogo allo stesso modello con più chiamate, si verranno inserito solo i nuovi elementi catalogo. Gli elementi esistenti rimangono con i valori originali.

| Metodo HTTP | URI |
|:--------|:--------|
|Inserisci     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
|   modelId |   Identificatore univoco del modello (maiuscole/minuscole)  |
| FileName | Identificatore testuale del catalogo.<br>Solo lettere (a Z, a-z), numeri (0-9), trattini (-) e carattere di sottolineatura (_) consentiti.<br>La lunghezza massima consentita: 50 |
|   apiVersion      | 1.0 |
|||
| Corpo della richiesta | Catalogo dati. Formato:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Nome</th><th>Obbligatorio</th><th>Tipo</th><th>Descrizione</th></tr><tr><td>Id elemento</td><td>Sì</td><td>Lunghezza alfanumerico, max 50</td><td>Identificatore univoco di un elemento</td></tr><tr><td>Nome elemento</td><td>Sì</td><td>Lunghezza alfanumerico, max 255</td><td>Nome elemento</td></tr><tr><td>Categoria articolo</td><td>Sì</td><td>Lunghezza alfanumerico, max 255</td><td>Categoria a cui appartiene questo elemento (ad esempio cucina libri, serie...)</td></tr><tr><td>Descrizione</td><td>No</td><td>Lunghezza alfanumerico, max 4000</td><td>Descrizione di questo articolo</td></tr></table><br>Dimensioni massime dei file sono 200MB.<br><br>Esempio:<br><pre>2406e770-c 769-4189-89de-1c9283f93a96, Clara Callan, della Rubrica<br>21bf8088-b6c0-4509-870C-e1c7ac78304a, la chat Room dimenticare: prenotare una fittizio (Byzantium libro)<br>Rubrica 3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework,<br>552a1940-21e4-4399-82bb-594b46d7ed54, ritenuta di Belve, della Rubrica</pre> |


**Risposta**:

Codice di stato HTTP: 200

- `Feed\entry\content\properties\LineCount`-Numero di righe accettate.
- `Feed\entry\content\properties\ErrorCount`-Numero di righe che non sono state inserite a causa di un messaggio di errore.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>Importare dati di utilizzo

####<a name="uploading-a-file"></a>Caricare un file
In questa sezione viene illustrato come caricare dati di utilizzo tramite un file. È possibile chiamare questa API più volte con dati di utilizzo. Verranno salvati tutti i dati di utilizzo per tutte le chiamate.

| Metodo HTTP | URI |
|:--------|:--------|
|Inserisci     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
|   modelId |   Identificatore univoco del modello (maiuscole/minuscole) |
| FileName | Identificatore testuale del catalogo.<br>Solo lettere (a Z, a-z), numeri (0-9), trattini (-) e carattere di sottolineatura (_) consentiti.<br>La lunghezza massima consentita: 50 |
|   apiVersion      | 1.0 |
|||
| Corpo della richiesta | Dati di utilizzo. Formato:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nome</th><th>Obbligatorio</th><th>Tipo</th><th>Descrizione</th></tr><tr><td>Id utente</td><td>Sì</td><td>Alfanumerico</td><td>Identificatore univoco dell'utente</td></tr><tr><td>Id elemento</td><td>Sì</td><td>Lunghezza alfanumerico, max 50</td><td>Identificatore univoco di un elemento</td></tr><tr><td>Ora</td><td>No</td><td>Data in formato: aaaa/MM/GGOhh.mm.ss (ad esempio 2013/06/20T10:00:00)</td><td>Ora di dati</td></tr><tr><td>Evento</td><td>No, se fornito quindi inoltre necessario inserire data</td><td>Una delle operazioni seguenti:<br>Fare clic su •<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Acquisto</td><td></td></tr></table><br>Dimensioni massime dei file sono 200MB.<br><br>Esempio:<br><pre>149452, 1b3d95e2-84e4-c 414-bb38-be9cf461c347<br>6360, 1b3d95e2-84e4-c 414-bb38-be9cf461c347<br>50321, 1b3d95e2-84e4-c 414-bb38-be9cf461c347<br>71285, 1b3d95e2-84e4-c 414-bb38-be9cf461c347<br>224450, 1b3d95e2-84e4-c 414-bb38-be9cf461c347<br>236645, 1b3d95e2-84e4-c 414-bb38-be9cf461c347<br>107951, 1b3d95e2-84e4-c 414-bb38-be9cf461c347</pre> |

**Risposta**:

Codice di stato HTTP: 200

- `Feed\entry\content\properties\LineCount`-Numero di righe accettate.
- `Feed\entry\content\properties\ErrorCount`-Numero di righe che non sono state inserite a causa di un messaggio di errore.
- `Feed\entry\content\properties\FileId`-File identificatore.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>Utilizzo di acquisizione dei dati
In questa sezione viene illustrato come inviare gli eventi in tempo reale Azure Machine Learning consigli, in genere dal sito Web.

| Metodo HTTP | URI |
|:--------|:--------|
|Inserisci     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|Corpo della richiesta| Immissione di dati di evento per ogni evento a cui si desidera inviare. È necessario inviare per la stessa sessione utente o in un browser lo stesso ID nel campo ID sessione. (Vedere l'esempio del corpo evento riportata di seguito).|


- Esempio di evento 'Fare clic su':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Esempio di evento 'RecommendationClick':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Esempio di evento 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Esempio di evento 'RemoveShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Esempio di evento 'Acquisto':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- Esempio l'invio di 2, 'Fare clic su' ed eventi 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**Risposta**: codice di stato HTTP: 200

###<a name="build-a-recommendation-model"></a>Creazione di un modello di recommendation

| Metodo HTTP | URI |
|:--------|:--------|
|Inserisci     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
| modelId | Identificatore univoco del modello (maiuscole/minuscole)  |
| userDescription | Identificatore testuale del catalogo. Si noti che se si utilizzano spazi si deve codifica con 20% invece. Vedere l'esempio precedente.<br>La lunghezza massima consentita: 50 |
| apiVersion | 1.0 |
|||
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice di stato HTTP: 200

Verrà visualizzata un'API asincrona. Viene visualizzato un ID di compilazione come una risposta. Per sapere quando è terminata la compilazione, occorre chiamare l'API "Ottenere compila lo stato di un modello" e individuare l'ID di compilazione nella risposta. Si noti che una compilazione è possibile eseguire da minuti a ore a seconda delle dimensioni dei dati.

Non è possibile utilizzare consigli fino alla compilazione termina.

Stato di compilazione valida:

- Creare, modello è stato creato.
- In coda – Genera modello è stato attivato e viene accodata.
- Viene creato predefiniti-modello.
- Generazione di successo completato.
- Errore: generazione termina con un errore.
- Annullato: compilazione è stata annullata.
- Annullamento-compilazione viene annullato.


Si noti che l'ID di compilazione sono disponibili nel percorso seguente:`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>Ottenere lo stato di compilazione di un modello

| Metodo HTTP | URI |
|:--------|:--------|
|Ottieni     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
|   modelId         |   Identificatore univoco del modello (maiuscole/minuscole)    |
|   onlyLastBuild   |   Indica se restituire tutti la cronologia di generazione del modello oppure solo lo stato della build più recente. |
|   apiVersion      |   1.0                                 |


**Risposta**:

Codice di stato HTTP: 200

La risposta include una voce per ogni compilazione. Ogni voce è i seguenti:

- `feed/entry/content/properties/UserName`-Nome dell'utente.
- `feed/entry/content/properties/ModelName`-Nome del modello.
- `feed/entry/content/properties/ModelId`-Modello identificatore univoco.
- `feed/entry/content/properties/IsDeployed`– Se la distribuzione della compilazione (noto anche compilazione attiva).
- `feed/entry/content/properties/BuildId`– Creare identificatore univoco.
- `feed/entry/content/properties/BuildType`-Tipo di compilazione.
- `feed/entry/content/properties/Status`-Creare stato. Può essere una delle operazioni seguenti: errore, predefiniti, in coda, Cancelling, annullato, successo
- `feed/entry/content/properties/StatusMessage`: Messaggio di stato dettagliate (si applica solo a specifici stati).
- `feed/entry/content/properties/Progress`– Creare lo stato di avanzamento (%).
- `feed/entry/content/properties/StartTime`: Ora di inizio genera.
- `feed/entry/content/properties/EndTime`– Creare data/ora fine.
- `feed/entry/content/properties/ExecutionTime`-Durata compilazione.
- `feed/entry/content/properties/ProgressStep`-Informazioni dettagliate sulla fase corrente è in una generazione in corso.

Stato di compilazione valida:
- Voce di richiesta di compilazione creato – è stata creata.
- In coda-richiesta di compilazione è stato attivato e viene accodata.
- Predefinito: compilazione è in corso.
- Generazione di successo completato.
- Errore: generazione termina con un errore.
- Annullato: compilazione è stata annullata.
- Annullamento-compilazione viene annullato.

Valori validi per tipo di generazione:
- Rango - rango creare. (Per rango dettagli compilazione, fare riferimento al documento "Documentazione Machine Learning Recommendation API").
- Suggerimento - compilazione Recommendation.
- Delle rate delle imposte - spesso stato acquistato insieme genera.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>Consigli

| Metodo HTTP | URI |
|:--------|:--------|
|Ottieni     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
| modelId | Identificatore univoco del modello (maiuscole/minuscole) |
| numeri oggetto | Elenco delimitato da virgole degli elementi di consigliare per.<br>La lunghezza massima consentita: 1024 |
| numberOfResults | Numero di risultati desiderati |
| includeMetatadata | Usi futuri sempre false |
| apiVersion | 1.0 |

**Risposta:**

Codice di stato HTTP: 200

La risposta include una voce per ogni elemento consigliato. Ogni voce è i seguenti:

- `Feed\entry\content\properties\Id`-ID elemento consigliati.
- `Feed\entry\content\properties\Name`-Nome dell'elemento.
- `Feed\entry\content\properties\Rating`-Valutazione suggerimento. numero elevato indica confidenza superiore.
- `Feed\entry\content\properties\Reasoning`-Recommendation ragionamento (ad esempio descrizioni recommendation).

OData XML

La risposta di esempio seguente include 10 elementi consigliati:

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>Modello di aggiornamento
È possibile aggiornare la descrizione del modello o l'ID di compilazione attiva.
*Compilazione attiva ID* - ogni compilazione per tutti i modelli con un ID di compilazione. L'ID di compilazione attiva è il completamento della compilazione di ogni nuovo modello. Dopo avere un ID di compilazione attiva e eseguire build aggiuntive per lo stesso modello, è necessario impostarlo in modo esplicito come l'ID di compilazione predefinito se si desidera. Quando si utilizzano raccomandazioni, se non si specifica l'ID di compilazione che si desidera utilizzare, quello predefinito verrà utilizzata automaticamente.

Questo meccanismo consente - dopo avere inserito un modello di recommendation di produzione - per creare nuovi modelli e testarli prima promozione di produzione.

| Metodo HTTP | URI |
|:--------|:--------|
|INSERIRE     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   Nome parametro  |   Valori validi                        |
|:--------          |:--------                              |
| ID | Identificatore univoco del modello (maiuscole/minuscole) |
| apiVersion | 1.0 |
|||
| Corpo della richiesta | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Si noti che i tag XML descrizione e ActiveBuildId facoltativi. Se non si desidera impostare descrizione o ActiveBuildId, rimuovere il tag. |

**Risposta**:

Codice di stato HTTP: 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>Legali
In questo documento viene fornito "come-è". Modifica informazioni e visualizzazioni espresse in questo documento, inclusi gli URL e altri riferimenti a siti Web, senza preavviso. Alcuni esempi derivante forniti solo a titolo esemplificativo e sono fittizi. Nessun reali connessione luoghi o reali. In questo documento non fornito alcun diritto legale su alcuna proprietà intellettuale i prodotti Microsoft. È possibile copiare e utilizzare questo documento per uso interno scopi di consultazione. © Microsoft 2014. Tutti i diritti riservati. 
 
