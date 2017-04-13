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

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Il mapping di un servizio web esistente a OData tramite CSDL

>[AZURE.IMPORTANT] **In questo momento sono onboarding qualsiasi nuovi autori di servizio dati. Nuovo dataservices non ottenere approvato per l'elenco.** Se si dispone di un'applicazione aziendale SaaS si desidera pubblicare nella AppSource è possibile trovare altre informazioni [di seguito](https://appsource.microsoft.com/partners). Se si dispone di un'applicazione IaaS o servizio sviluppo da pubblicare in Azure Marketplace è possibile trovare ulteriori informazioni [qui](https://azure.microsoft.com/marketplace/programs/certified/).

In questo articolo viene fornita una panoramica su come utilizzare un CSDL per eseguire il mapping di un servizio esistente a un servizio compatibile OData. Viene spiegato come creare il documento di mapping (CSDL) che trasforma la richiesta di input dal client tramite una chiamata del servizio e output (dati) nuovamente al client tramite OData compatibile feed. Microsoft Azure Marketplace viene esposta servizi per gli utenti finali mediante il protocollo OData. Servizi che vengono esposte dal provider di contenuti (dati proprietari) vengono esposti in vari modi, ad esempio resto, SOAP e così via.

## <a name="what-is-a-csdl-and-its-structure"></a>Che cos'è un file CSDL e la struttura?
Un CSDL (concettuale Schema Definition Language) è una specifica definizione come descrivere servizio web o database in comune lettera XML a Azure Marketplace.

Panoramica semplice il **richiedere flusso:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Il **flusso di dati** è nella direzione opposta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figura 1** diagrammi come un client otterrebbe i dati da un provider di contenuti (servizio) tramite l'interfaccia di Azure Marketplace.  File CSDL viene utilizzato il componente del Mapping/trasformazione per gestire la richiesta e passano dati tra servizi del provider di contenuti e il client richiedente.

*Figura 1: Flusso dettagliato dal client richiedente al provider di contenuti tramite Azure Marketplace*

  ![disegno](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Per informazioni generali su Atom, Pub Atom e il protocollo OData in base alla quale le estensioni di Azure Marketplace creano, informazioni revisione: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Estratto da sopra collegamento:     *"lo scopo del protocollo ODS (di seguito denominato OData) è fornire un protocollo basato su resto per le operazioni CRUD stile (creazione, lettura, aggiornamento ed eliminazione) rispetto a risorse esposte come servizi di dati. Un "servizio dati" è un endpoint dove non c'è dati esposti da uno o più "raccolte" ogni con zero o più "voci" costituiti digitato coppie di valori denominati. OData viene pubblicato da Microsoft in standard OASIS (organizzazione per l'avanzamento di strutturati informazioni standard) in modo che tutti gli utenti che dovrebbe essere possibile creare server, client o strumenti senza restrizioni o royalty."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Tre parti importanti che devono essere definite da CSDL sono:

- L' **endpoint** del servizio Provider nel Web indirizzo (URI) del servizio
- I **parametri di dati** passati come input al Provider del servizio le definizioni dei parametri inviato al servizio del Provider di contenuti per difetto il tipo di dati.
- **Schema** per i dati restituiti al servizio che richiede lo schema dei dati recapitati dal servizio del Provider di contenuti, inclusi contenitore, raccolte e alle tabelle, variabili/colonne e tipi di dati.

Nel diagramma seguente mostra una panoramica del flusso di nel punto in cui il client immette l'istruzione OData (chiamata al servizio web del provider di contenuti) per ottenere i risultati del/indietro.

  ![disegno](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Procedura:

1. Client invia richiesta tramite chiamata del servizio completa con i parametri di Input definiti in XML a Azure Marketplace
2. CSDL viene utilizzato per convalidare la chiamata del servizio.
    - Chiamare servizio formattato viene quindi inviato al servizio di provider di contenuto da Azure Marketplace
3. Il servizio esegue ed esegue l'azione del verbo Http (ad esempio GET) i dati vengono restituiti a Azure Marketplace nel punto in cui i dati richiesti (se presente) sono espone in formato XML al Client utilizzando il Mapping definito nel file CSDL.
4. Il Client viene inviato i dati (se presente) in formato XML o JSON

## <a name="definitions"></a>Definizioni

### <a name="odata-atom-pub"></a>Pub ATOM OData

Imposta un'estensione pub ATOM nel punto in cui ogni voce rappresenta un'unica riga di un risultato. La parte del contenuto della voce è stata migliorata per contengono i valori della riga, come coppie di parole chiave valore. Altre informazioni sono disponibili qui: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - Schema concettuale Definition Language

Consente di definire le funzioni (stored procedure) ed entità che vengono esposti tramite un database. Altre informazioni sono disponibili qui: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Fare clic sull'elenco a discesa **altre versioni** e selezionare una versione, se non è presente articolo.

### <a name="edm---entry-data-model"></a>EDM - modello di immissione dati
- Panoramica: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Anteprima: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Tipi di dati: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

Eseguire il seguente mostra la dettagliate da sinistra a destra del flusso dal quale il client immette l'istruzione OData (chiamata al servizio web del provider di contenuti) per ottenere i risultati/dati:

  ![disegno](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Nozioni fondamentali sul CSDL

Un CSDL (concettuale Schema Definition Language) è una specifica definizione come descrivere servizio web o database in comune lettera XML a Azure Marketplace. CSDL descrive critici pezzi che **consente la trasmissione dei dati dall'origine dati di Azure Marketplace.** Le parti principali sono descritti di seguito:

- Informazioni sull'interfaccia che descrive tutte le funzioni disponibile al pubblico (FunctionImport nodo)
- Informazioni sul tipo di dati per tutti i messaggi requests(input) e responses(outputs) messaggio (EntityContainer/EntitySet/EntityType nodi)
- Associazione informazioni il protocollo di trasporto per essere utilizzato (intestazione nodo)
- Informazioni sull'indirizzo per individuare il servizio specificato (BaseURI attributo)

In breve, CSDL rappresenta un contratto indipendente dalla piattaforma e lingua tra il richiedente di servizio e il provider di servizi. Usa il CSDL, un client può individuare un servizio di assistenza/database web e richiamare una delle relative funzioni disponibile al pubblico.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Relative un CSDL a un Database o una raccolta
**Specifica CSDL**

CSDL è grammatica XML per la descrizione di un servizio web. Specifica se stesso è diviso in 4 elementi principali: EntitySet, FunctionImport; Spazio dei nomi ed EntityType.

Per rendere più semplice da comprendere consente di questa astrazione correlare un CSDL a una tabella.

Ricordare;

  CSDL rappresenta un contratto indipendente dalla piattaforma e lingua tra il **richiedente del servizio** e il **provider di servizi**. Uso dei CSDL, un **client** può individuare un **servizio di assistenza/database web** e richiamare uno dei relativi disponibile al pubblico **funzioni.**

Per un servizio dati quattro parti di un CSDL possono essere considerate in termini di un Database, una tabella, una colonna e Stored Procedure.

Relative come indicato di seguito per un servizio dati:

- EntityContainer ~ = Database
- EntitySet ~ = tabella
- EntityType ~ = colonne
- FunctionImport ~ = Stored Procedure

**Verbi HTTP consentiti**
- GET-restituisce i valori da db (restituisce una raccolta)
- POST-utilizzato per trasferire dati e i valori restituiti facoltativi da db (Crea una nuova voce nella raccolta restituito id/URI)
- Elimina: Elimina dati dal database (Elimina una raccolta)
- INSERIRE, aggiornare i dati in una DB (sostituire una raccolta o crearne uno)

## <a name="metadatamapping-document"></a>Documento di metadati/Mapping

Il documento di metadati/mapping viene utilizzato per eseguire il mapping di servizi web del provider di contenuti in modo che possono essere esposti come un servizio web OData dal sistema di Azure Marketplace. Basato su CSDL e implementa alcune estensioni a CSDL in base alle esigenze di RIPOSO in base a servizi web esposti tramite Azure Marketplace. Si trovano le estensioni dello spazio dei nomi [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Segue un esempio di CSDL: (copia e Incolla il seguente esempio CSDL in un editor XML e modificare in modo che corrispondano al servizio.  Quindi incollare CSDL Mapping in base alla tabulazione DataService durante la creazione del servizio nel [Portale di pubblicazione di Azure Marketplace](https://publish.windowsazure.com)).

**Termini:** Relative termini CSDL le condizioni per il [Portale di pubblicazione](https://publish.windowsazure.com) dell'interfaccia utente (PPUI).
- Offrire "Titolo" il PPUI si riferisce a MyWebOffer
- Società nel PPUI si riferisce al **Nome dell'autore visualizzato** al [Centro per sviluppatori Microsoft](http://dev.windows.com/registration?accountprogram=azure) dell'interfaccia utente
- L'API si riferisce al Web o al servizio dati (un piano di PPUI)

**Gerarchia:** 
 una società (Provider di contenuti) possiede delle offerte che hanno piani, vale a dire service(s), quali riga verso l'alto con un'API.

### <a name="webservice-csdl-example"></a>Esempio di servizio CSDL

Si connette a un servizio che esporre un endpoint di applicazione web (ad esempio un'applicazione c#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Visualizzare altri esempi di servizio Web CSDL vedere l'articolo [esempi di mapping di un servizio web esistente a OData tramite CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Esempio DataService CSDL

Si connette a un servizio che esposizione una tabella di database o visualizzazione di un estremo esempio seguente mostra che due API per i dati di base in base all'API CSDL (possono utilizzare viste anziché tabelle).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Vedere anche
- Se si è interessati apprendimento e la comprensione nodi specifici e i parametri, leggere questo articolo [Nodi di Mapping OData servizio dati](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) per le definizioni e spiegazioni, esempi e utilizzare contesto maiuscole e minuscole.
- Se si è interessati a esaminare gli esempi, leggere questo articolo [Esempi di Mapping OData servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md) per visualizzare il codice di esempio e informazioni di contesto e sintassi dei codici.
- Per tornare al percorso stabilito per la pubblicazione di un servizio di dati di Azure Marketplace, leggere questo articolo [Guida alla pubblicazione di servizio dati](marketplace-publishing-data-service-creation.md).
