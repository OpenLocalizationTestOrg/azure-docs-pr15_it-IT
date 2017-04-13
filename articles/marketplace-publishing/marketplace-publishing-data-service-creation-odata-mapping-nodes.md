<properties
   pageTitle="Guida alla creazione di un servizio dati per il Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare, certificare e distribuire un servizio dati per acquistare in Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Informazioni sullo schema di nodi per il mapping di un servizio web esistente a OData tramite CSDL

>[AZURE.IMPORTANT] **In questo momento sono onboarding qualsiasi nuovi autori di servizio dati. Nuovo dataservices non ottenere approvato per l'elenco.** Se si dispone di un'applicazione aziendale SaaS si desidera pubblicare nella AppSource è possibile trovare altre informazioni [di seguito](https://appsource.microsoft.com/partners). Se si dispone di un'applicazione IaaS o servizio sviluppo da pubblicare in Azure Marketplace è possibile trovare ulteriori informazioni [qui](https://azure.microsoft.com/marketplace/programs/certified/).

In questo documento aiuta a rendere più chiara la struttura dei nodi per il mapping di un protocollo OData CSDL. È importante tenere presente che la struttura dei nodi è ben formato XML. Pertanto schema radice, padre e figlio è applicabile durante la progettazione del mapping OData.

## <a name="ignored-elements"></a>Elementi ignorati
Di seguito sono riepilogati CSDL gli elementi (nodi XML) che non verrà utilizzato da back-end Azure Marketplace durante l'importazione di metadati del servizio web. Possono essere presenti, ma verranno ignorati.

| Elemento | Ambito |
|----|----|
| Utilizzando l'elemento | Il nodo, nodi sub e tutti gli attributi |
| Elemento documentazione | Il nodo, nodi sub e tutti gli attributi |
| ComplexType | Il nodo, nodi sub e tutti gli attributi |
| Elemento di associazione | Il nodo, nodi sub e tutti gli attributi |
| Proprietà estesa | Il nodo, nodi sub e tutti gli attributi |
| EntityContainer | Solo gli attributi seguenti vengono ignorati: *estende* e *AssociationSet* |
| Schema | Solo gli attributi seguenti vengono ignorati: *Namespace* |
| FunctionImport | Solo gli attributi seguenti vengono ignorati: *modalità* (valore predefinito di ln utilizzata) |
| EntityType | I seguenti nodi sub vengono ignorati: *chiave* e *PropertyRef* |

Di seguito vengono descritte le modifiche (aggiunto e ignorati elementi) a vari nodi CSDL XML in modo dettagliato.

## <a name="functionimport-node"></a>Nodo FunctionImport
Nodo FunctionImport rappresenta un URL (punto di ingresso) esposti un servizio per l'utente finale. Il nodo consente che descrive come risulta l'URL, sono disponibili i parametri disponibili per l'utente finale e come tali parametri.

Informazioni dettagliate su questo nodo vengono trovati [qui] [MSDNFunctionImportLink]

[MSDNFunctionImportLink]: (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Di seguito sono riportati ulteriori attributi (o aggiunte agli attributi) che vengono esposti mediante il nodo FunctionImport:

**d:baseUri** -
modello URI per la risorsa resto esposto a Marketplace. Marketplace utilizza il modello per creare query del servizio web REST. Il modello URI contiene i segnaposto per i parametri in forma di {parameterName}, dove parameterName è il nome del parametro. Ex. apiVersion = {apiVersion}.
Parametri potranno essere visualizzati come URI parametri o come parte del percorso URI. Per quanto riguarda l'aspetto del percorso siano sempre obbligatorie (non possono essere contrassegnati come nullable). *Esempio:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nome** - il nome della funzione importata.  Non può essere diverso da quello di altri nomi definiti in CSDL.  Ex. Nome = "GetModelUsageFile"

**EntitySet** *(facoltativo)* - se la funzione restituisce un insieme di tipi di entità, il valore di **EntitySet** deve essere set di entità a cui appartiene la raccolta. In caso contrario, l'attributo **EntitySet** non deve essere utilizzato. *Esempio:*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(Facoltativo)* - specifica il tipo di elementi restituiti dall'URI.  Non utilizzare questo attributo se la funzione non restituisce un valore. Di seguito sono supportati:

 - **Insieme (<Entity type name>)**: specifica un insieme di tipi di entità definito. Il nome è presenta nell'attributo Name del nodo EntityType. Un esempio è insieme (WXC. HourlyResult).
 - **Materie (<mime type>)**: specifica un documento/blob non elaborato che viene restituito all'utente. Un esempio è Raw(image/jpeg) altri esempi:

  - ReturnType="Raw(text/plain)"
  - ReturnType = "insieme (sage. DeleteAllUsageFilesEntity) "*

**d:Paging** - specifica la modalità di gestione di suddivisione in pagine dalla risorsa resto. Vengono utilizzati i valori dei parametri all'interno di parentesi rami d'attività della pagina, ad esempio = {$page} & itemsperpage = {$size} le opzioni disponibili sono:

- **Nessuno:** suddivisione in pagine non è disponibile
- **Ignora:** suddivisione in pagine è espresso logico "Ignora" e "Scrivi" (in alto). Ignora passa sugli elementi M e Scrivi restituisce gli elementi successivi N. Valore del parametro: $skip
- **Eseguire:** Scrivi restituisce gli elementi N successivi. Valore del parametro: $take
- **PageSize:** suddivisione in pagine è espressa tramite una pagina logica e le dimensioni (elementi per pagina). Pagina rappresenta la pagina corrente viene restituita. Valore del parametro: $page
- **Dimensioni:** dimensioni rappresentano il numero di elementi restituiti per ogni pagina. Valore del parametro: $size

**d:AllowedHttpMethods** *(Facoltativo)* - consente di specificare quale verbo viene gestito dalla risorsa resto. Inoltre, limita verbo accettata per il valore specificato.  Predefinito = POST.  *Esempio:* `d:AllowedHttpMethods="GET"` Le opzioni disponibili sono:

- **Ottenere:** in genere usate per restituire i dati
- **POST:** in genere utilizzato per inserire i nuovi dati
- **Inserire:** in genere utilizzati per aggiornare i dati
- **Eliminare:** consente di eliminare dati

Altri elementi figlio (non rientrano la documentazione CSDL) all'interno del nodo FunctionImport è:

**d:RequestBody** *(Facoltativo)* - nel corpo della richiesta viene utilizzata per indicare che la richiesta prevede un corpo da inviare. Parametri possono essere specificati all'interno del corpo della convocazione. In cui vengono espressi all'interno di parentesi graffe, ad esempio {parameterName}. Questi parametri sono mappati dagli utenti nel corpo del trasferimento del servizio del provider di contenuti. L'elemento requestBody ha un attributo con nome httpMethod. L'attributo consente a due valori:

- **POST:** Utilizzato se la richiesta HTTP POST
- **Ottenere:** Utilizzato se la richiesta HTTP GET

    Esempio:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** e **d:Namespace** - questo nodo vengono descritti gli spazi dei nomi definiti nel XML restituito dall'importazione di funzione (endpoint URI). Il codice XML restituito dal servizio back-end può contenere qualsiasi numero di spazi dei nomi per distinguere tra il contenuto viene restituito. **Tutti questi spazi dei nomi, se utilizzato nelle query XPath d:Map o d:Match devono essere elencati.** Il nodo d:Namespaces contiene un elenco o un set di nodi d:Namespace. Ognuno di essi sono elencati uno spazio dei nomi utilizzata nella risposta servizio back-end. L'attributo del nodo d:Namespace sono i seguenti:

-   **d:Prefix:** Prefisso per lo spazio dei nomi, come illustrato nei risultati XML restituiti dal servizio, ad esempio f:FirstName, f:LastName, dove f è il prefisso.
- **d:Uri:** URI completo dello spazio dei nomi usati nel documento risultato. Rappresenta il valore che il prefisso viene risolto in fase di esecuzione.

**d:ErrorHandling** *(Facoltativo)* - questo nodo contiene le condizioni per la gestione degli errori. Ciascuna delle condizioni viene convalidata sul risultato restituito dal servizio del provider di contenuti. Se il codice di errore HTTP proposto corrisponde a una condizione di un messaggio di errore viene restituito all'utente finale.

**d:ErrorHandling** *(Facoltativo)* e **d:Condition** *(facoltativo)* - un nodo condizione contiene una condizione che è stata archiviata il risultato restituito dal servizio del provider di contenuti. Gli attributi **necessari** sono i seguenti:

- **d:Match:** Espressione XPath che verifica se un nodo/valore specificato è presenta nel file XML di output del provider di contenuti. L'espressione XPath eseguita sull'output e deve restituire VERO se la condizione è una corrispondenza o false.
- **d:HttpStatusCode:** Il codice di stato HTTP che deve essere restituito dalla Marketplace nel caso la condizione corrisponde. Marketplace signalizes errori all'utente tramite codici di stato HTTP. Un elenco dei codici di stato HTTP sono disponibili all'http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** Messaggio di errore restituito: con il codice di stato HTTP: consente all'utente finale. Deve trattarsi di un messaggio di errore brevi che non contenga le informazioni riservate.

**d:Title** *(Facoltativo)* - consente che descrive il titolo della funzione. Valore per il titolo proviene da

- L'attributo mappa facoltativo (un'espressione xpath) che specifica dove cercare il titolo nella risposta restituita dalla richiesta di servizio.
- - Oppure - titolo specificato come valore del nodo.

**d:Rights** *(Facoltativo)* - (ad esempio copyright) i diritti associati con la funzione. Il valore per i diritti proviene da:

- L'attributo mappa facoltativo (un'espressione xpath) che specifica dove cercare i diritti nella risposta restituita dalla richiesta di servizio.
-   - Oppure - diritti specificati come valore del nodo.

**d:Description** *(Facoltativo)* - una breve descrizione della funzione. Proviene il valore per la descrizione

- L'attributo mappa facoltativo (un'espressione xpath) che specifica come trovare la descrizione nella risposta restituita dalla richiesta di servizio.
- - Oppure -descrizione specificato come valore del nodo.

**d:EmitSelfLink** - *esempio precedente "FunctionImport per 'Spostamento' restituiti dati"*

**d:EncodeParameterValue** - estensione facoltativa a OData

**d:QueryResourceCost** - estensione facoltativa a OData

**d:Map** - estensione facoltativa a OData

**d:Headers** - estensione facoltativa a OData

**d:Headers** - estensione facoltativa a OData

**d:Value** - estensione facoltativa a OData

**d:HttpStatusCode** - estensione facoltativa a OData

**d:ErrorMessage** - estensione facoltativa a OData

## <a name="parameter-node"></a>Nodo del parametro

Questo nodo rappresenta un parametro che è esposto come parte del modello di URI / corpo specificata nel nodo FunctionImport richiesta.

Pagina di un documento di molto utile dettagli relativi al nodo "Elemento di parametro" si trova in [seguito](http://msdn.microsoft.com/library/ee473431.aspx) (utilizzare l'elenco a discesa **Versione altre** per selezionare una versione diversa, se necessario, per visualizzare la documentazione). *Esempio:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Attributo parametro | È necessario | Valore |
|----|----|----|
| Nome | Sì | Il nome del parametro. Maiuscole / minuscole.  Maiuscole / minuscole BaseUri. **Esempio:**`<Property Name="IsDormant" Type="Byte" />` |
| Tipo | Sì | Il tipo di parametro. Il valore deve essere un **EDMSimpleType** o un tipo complesso nell'ambito del modello. Per ulteriori informazioni, vedere "6/proprietà di parametro supportati tipi".  (Maiuscole/minuscole. Primo carattere è maiuscola, resto sono lettere minuscole).  Inoltre, vedere [concettuale modello tipi (CSDL)] [MSDNParameterLink]. **Esempio:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Modalità | No | **In**, o ingresso a seconda se il parametro è un input, output o parametro di input/output. ("IN" è disponibile solo in Azure Marketplace.) **Esempio:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | No | Lunghezza del parametro massima consentita. **Esempio:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precisione | No | Precisione del parametro. **Esempio:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Scala | No | La scala del parametro. **Esempio:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Gli attributi che sono state aggiunte alla specifica CSDL sono i seguenti:

| Attributo parametro | Descrizione |
|----|----|
| **d:Regex** *(Facoltativo)* | Istruzione regex utilizzata per convalidare il valore di input per il parametro. Se il valore di input non corrisponda all'istruzione il valore viene rifiutato. In questo modo per specificare anche un set di valori possibili, ad esempio ^ [0-9] +? $ per consentire solo i numeri. **Esempio:** ' < nome parametro = "nome" modalità = "In" tipo = "Stringa" d: Nullable = "false" d:Regex = "^ [a-zA-Z] * $" d:Description = "un nome che non può contenere spazi o caratteri non alfanumerici localizzata" d:SampleValues = "Giorgio|John|Thomas|James "/ >' |
| **d:enum** *(Facoltativo)* | Elenco di valori validi per il parametro separati da una barra verticale. Il tipo dei valori deve corrispondere al tipo del parametro definito. Esempio: ' inglese|unità di misura metriche|non elaborato`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< nome parametro = "Durata" tipo = "Stringa" modalità = "In" Nullable = "true" d:Enum = "1 anno|5years|10years "/ >' |
| **d: Nullable** *(Facoltativo)* | Consente di definire se il parametro può essere null. Il valore predefinito è: true. Tuttavia, i parametri che vengono esposti come parte del percorso nel modello di URI non possono essere null. Quando l'attributo è impostato su false per questi parametri: l'input dell'utente viene ignorato. **Esempio:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Facoltativo)* | Un valore di esempio da visualizzare come una nota al Client nell'interfaccia utente.  È possibile aggiungere più valori utilizzando un elenco di barra verticale separati, vale a dire "un|b|c` **Example:** `< nome parametro = "BikeOwner" tipo = "Stringa" modalità = "In" d:SampleValues = "Giorgio|John|Thomas|James "/ >' |

## <a name="entitytype-node"></a>Nodo EntityType

Questo nodo rappresenta uno dei tipi di restituiti da Marketplace per l'utente finale. Sono inoltre disponibili il mapping tra l'output restituito dal servizio del provider di contenuti per i valori restituiti all'utente finale.

Informazioni dettagliate su questo nodo si trovano in [seguito](http://msdn.microsoft.com/library/bb399206.aspx) (utilizzare l'elenco a discesa **Versione altre** per selezionare una versione diversa, se necessario, per visualizzare la documentazione.)

| Nome attributo | È necessario | Valore |
|----|----|----|
| Nome | Sì | Il nome del tipo di entità. **Esempio:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | No | Il nome di un altro tipo di entità che rappresenta il tipo di base del tipo di entità che viene definito. **Esempio:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Gli attributi che sono state aggiunte alla specifica CSDL sono i seguenti:

**d:Map** - espressione XPath eseguita sull'output di servizio. Il presupposto è che l'output di servizio contiene un set di elementi che si ripetono, come un ATOM feed dove esiste un set di nodi voce che si ripetono. Ognuna di queste ripetuta nodi contiene un record. L'espressione XPath viene quindi specificato in modo che puntino in corrispondenza del nodo ripetuto singoli nel risultato di servizio del provider di contenuti che contiene i valori di un singolo record. Esempio di output dal servizio:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

Espressione XPath da essere /foo/barre in quanto ogni della barra di nodo è il nodo ripetuto nell'output e contiene il contenuto effettivo viene restituito all'utente finale.

**Chiave** - questo attributo viene ignorato dal Marketplace. RESTO in base a servizi web, in genere non esporre una chiave primaria.


## <a name="property-node"></a>Nodo della proprietà

Questo nodo contiene una proprietà del record.

Informazioni dettagliate su questo nodo si trovano in [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (per selezionare una versione diversa, se necessario, per visualizzare la documentazione, utilizzare l'elenco a discesa **Versione altre** ). *Esempio:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Obbligatorio | Valore |
|----|----|----|
| Nome | Sì | Il nome della proprietà. |
| Tipo | Sì | Il tipo del valore della proprietà. Il tipo di valore di proprietà deve essere un **EDMSimpleType** o un tipo complesso (indicato da un nome completo) che si trova all'interno dell'ambito del modello. Per ulteriori informazioni, vedere tipi di modello concettuale (CSDL). |
| Nullable | No | **Vero** (il valore predefinito) o **False** a seconda che la proprietà può avere un valore null. Nota: Nella versione di CSDL indicato dallo spazio dei nomi [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) , una proprietà di tipo complesso deve essere Nullable = "False". |
| DefaultValue | No | Il valore predefinito della proprietà. |
|MaxLength | No | La lunghezza massima del valore della proprietà. |
| FixedLength | No | **True** o **False** a seconda che il valore della proprietà verrà memorizzato come una stringa di lunghezza fiexed. |
| Precisione | No | Indica il numero massimo di cifre per mantenere il valore numerico. |
| Scala | No | Numero massimo di posizioni decimali per mantenere il valore numerico. |
| Unicode | No | **True** o **False** a seconda che il valore di proprietà essere archiviati come stringa Unicode. |
| Regole di confronto | No | Stringa che specifica la sequenza di confronto da utilizzare nell'origine dati. |
| ConcurrencyMode | No | **Nessuno** (il valore predefinito) o **fisso**. Se il valore è impostato su **fisso**, verrà utilizzato il valore della proprietà ottimistica controlli. |

Gli attributi aggiuntivi che sono state aggiunte alla specifica CSDL sono i seguenti:

**d:Map** - espressione XPath eseguita sul servizio di output ed estrae una proprietà di output. Il percorso XPath specificato è relativo il nodo ripetuto selezionata in XPath del nodo EntityType. È inoltre possibile specificare un'espressione XPath assoluta per consentire tra cui una risorsa statica in ciascuno dei nodi di output, ad esempio, ad esempio una dichiarazione di copyright disponibili solo una volta nel servizio originale di output ma dovrebbe essere presente in ognuna delle righe di output OData. Esempio del servizio:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

L'espressione XPath sarebbe ./bar/baz0 per ottenere il nodo baz0 dal servizio del provider di contenuti.

**d:CharMaxLength** - per tipo di stringa, è possibile specificare la lunghezza massima. Vedere Esempio DataService CSDL

**d:IsPrimaryKey** - indica se la colonna è la chiave primaria nella tabella/visualizzazione. Vedere Esempio DataService CSDL.

**d:isExposed** - determina se lo schema della tabella viene esposta (in genere true). Vedere Esempio DataService CSDL

**d:IsView** *(Facoltativo)* - true se questo è basato su una visualizzazione anziché una tabella.  Vedere Esempio DataService CSDL

**d:Tableschema** - vedere DataService CSDL esempio

**d:ColumnName** - è il nome della colonna nella tabella/visualizzazione.  Vedere Esempio DataService CSDL

**d:IsReturned** - è Boolean che determina se il servizio presenta questo valore per il client.  Vedere Esempio DataService CSDL

**d:IsQueryable** - è Boolean che determina se la colonna può essere utilizzata in una query di database.   Vedere Esempio DataService CSDL

**d:OrdinalPosition** - è posizione numerica della colonna di aspetto, x, nella tabella o la visualizzazione, dove x è compreso tra 1 e il numero di colonne della tabella.  Vedere Esempio DataService CSDL

**d:DatabaseDataType** - è il tipo di dati della colonna nel database, ad esempio tipo di dati SQL. Vedere Esempio DataService CSDL

## <a name="supported-parametersproperty-types"></a>Tipi di parametri/proprietà supportati
Di seguito sono supportati per i parametri e proprietà. (Maiuscole/minuscole)

| Tipi di base | Descrizione |
|----|----|
| Null | Rappresenta l'assenza di un valore |
| Valore booleano | Rappresenta il concetto di matematico di logica valori binario|
| Byte | Valore intero a 8 bit|
|DateTime| Rappresenta data e ora con valori compresi tra 12:00:00 del 1 gennaio 1753 D.C. tramite 11:59:59 formato, dicembre 9999 D.C.|
|Decimale | Rappresenta valori numerici con precisione e scala fisse. Questo tipo è possibile descrivere un valore numerico che vanno dal 10 negativo ^ 255 + 1 a 10 positivo ^ 255 -1|
| Effettuare un doppio | Rappresenta una numerica con precisione di 15 cifre che rappresenta valori con intervallo approssimativo di + 2.23e-308 da a + 1, 79E a virgola mobile +308. **Utilizzare Decimal a causa di problemi di esportazione Exel**|
| Singolo | Rappresenta una numerica con precisione di 7 cifre che rappresenta valori con intervallo approssimativo di + a +-1 18E-38 3.40 a virgola mobile + 38|
|GUID |Rappresenta un valore identificatore univoco di 16 byte (128 bit) |
|Int16|Rappresenta un valore intero a 16 bit con segno |
|Int32|Rappresenta un valore intero con segno a 32 bit |
|Int64|Rappresenta un valore intero con segno a 64 bit |
|Stringa | Rappresenta i dati di carattere fisso o variabile lunghezza |


## <a name="see-also"></a>Vedere anche
- Se si è interessati comprendere il processo di mapping OData e lo scopo complessivo, leggere questo articolo [Mapping OData del servizio di dati](marketplace-publishing-data-service-creation-odata-mapping.md) per verificare le definizioni, strutture e le istruzioni.
- Se si è interessati a esaminare gli esempi, leggere questo articolo [Esempi di Mapping OData servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md) per visualizzare il codice di esempio e informazioni di contesto e sintassi dei codici.
- Per tornare al percorso stabilito per la pubblicazione di un servizio di dati di Azure Marketplace, leggere questo articolo [Guida alla pubblicazione di servizio dati](marketplace-publishing-data-service-creation.md).
