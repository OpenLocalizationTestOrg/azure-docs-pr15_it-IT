<properties
    pageTitle="Come utilizzare lo spazio di archiviazione tabella da PHP | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di tabella da PHP per creare ed eliminare una tabella e inserire, eliminare e query la tabella."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Come utilizzare lo spazio di archiviazione tabella da PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire gli scenari comuni utilizzando il servizio di Azure tabella. Gli esempi scritto in PHP e usare [Azure SDK per PHP][download]. Gli scenari coperti includono **la creazione di eliminazione di una tabella e inserire, eliminare e query su entità in una tabella**. Per ulteriori informazioni sul servizio tabella Azure, vedere la sezione [passaggi successivi](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP

L'unico requisito per la creazione di un'applicazione PHP che accede al servizio tabella Azure è il riferimento di classi di Azure SDK per PHP all'interno del codice. Per creare l'applicazione, inclusi il blocco note, è possibile utilizzare gli strumenti di sviluppo.

In questa Guida, utilizzare le caratteristiche del tabella che possono essere chiamate da un'applicazione PHP in locale o nel codice in esecuzione all'interno di un ruolo Azure web, un ruolo di lavoro o un sito Web.

## <a name="get-the-azure-client-libraries"></a>Ottenere le librerie Client Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurare l'applicazione di accedere al servizio di tabella

Per utilizzare le API del servizio di tabella di Azure, è necessario:

1. Fare riferimento al file caricatore utilizzando [require_once] [ require_once] istruzione, e
2. Fare riferimento a tutte le classi che è possibile utilizzare.

Nell'esempio seguente viene illustrato come includere il file caricatore e fare riferimento alla classe **ServicesBuilder** .

> [AZURE.NOTE] In questo esempio (e altri esempi in questo articolo) presuppongono che è stato installato librerie Client PHP per Azure tramite compositore. Se è stato installato manualmente le raccolte, è necessario fare riferimento il <code>WindowsAzure.php</code> file caricatore.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Negli esempi seguenti, il `require_once` istruzione viene sempre visualizzata, ma solo le classi necessarie, ad esempio eseguire fanno riferimento.

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione Azure

Per creare un'istanza di un client di servizi di tabella di Azure, è una stringa di connessione valida. Il formato della tabella servizio stringa di connessione è:

Per accedere al servizio live:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Per l'accesso a archiviazione emulatore:

    UseDevelopmentStorage=true


Per creare un client di servizi Azure, è necessario utilizzare la classe **ServicesBuilder** . Si può:

* passare la stringa di connessione direttamente a o
* Utilizzare **CloudConfigurationManager (CCM)** per selezionare più origini esterne per la stringa di connessione:
    * Per impostazione predefinita, è dotato di supporto per un'origine esterna - variabili di ambiente
    * è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi riportati di seguito, la stringa di connessione verrà passata direttamente.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Creare una tabella

Un oggetto **TableRestProxy** consente di creare una tabella con il metodo **createTable** . Quando si crea una tabella, è possibile impostare il timeout del servizio di tabella. (Per ulteriori informazioni sul timeout del servizio di tabella, vedere [Impostazione timeout per le operazioni di servizio tabella][table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Per informazioni sulle restrizioni sui nomi di tabella, vedere [informazioni sul modello di dati della tabella servizio][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità a una tabella, creare un nuovo oggetto **entità** e passare a **TableRestProxy -> insertEntity**. Si noti che quando si crea un'entità, è necessario specificare un `PartitionKey` e `RowKey`. Questi sono gli identificatori univoci per un'entità e i valori che è possono eseguire una query più velocemente rispetto alle altre proprietà entità. Il sistema utilizza `PartitionKey` per distribuire automaticamente entità della tabella molti nodi di archiviazione. Entità con lo stesso `PartitionKey` archiviati sullo stesso nodo. (Eseguono operazioni su più entità archiviate sullo stesso nodo migliore su entità archiviate in diversi nodi.) Il `RowKey` è l'ID univoco di un'entità all'interno di una partizione.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Per informazioni sui tipi e le proprietà della tabella, vedere [informazioni sul modello di dati di tabella servizio][table-data-model].

La classe **TableRestProxy** offre due metodi alternativi per l'inserimento di entità: **insertOrMergeEntity** e **insertOrReplaceEntity**. Per utilizzare questi metodi, creare una nuova **entità** e passare come parametro a uno dei metodi. Ogni metodo inserisce l'entità se non esiste. Se l'entità esiste già, **insertOrMergeEntity** Aggiorna valori di proprietà se le proprietà già esistono e aggiunge nuove proprietà se non sono presenti, mentre **insertOrReplaceEntity** sostituisce completamente un'entità esistente. Nell'esempio seguente viene illustrato come utilizzare **insertOrMergeEntity**. Se l'entità con `PartitionKey` "tasksSeattle" e `RowKey` "1" non esiste già, verrà inserito. Tuttavia, se è stato inserito in precedenza (come illustrato nell'esempio precedente), il `DueDate` proprietà verrà aggiornata e la `Status` proprietà verrà aggiunta. Il `Description` e `Location` proprietà vengono aggiornate anche, ma con valori che in modo efficace tenuti invariato. Se queste ultime due proprietà sono stati aggiunti come illustrato nell'esempio ma non presente nell'entità di destinazione, i rispettivi valori esistenti da rimangono invariati.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità

Il metodo **TableRestProxy -> getEntity** consente di recuperare una singola entità eseguendo una query per il `PartitionKey` e `RowKey`. Nell'esempio seguente, la chiave partizione `tasksSeattle` e come chiave riga `1` vengono passati al metodo **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione

Query di entità vengono create utilizzando filtri (per ulteriori informazioni, vedere [tabelle di query ed entità][filters]). Per recuperare tutte le entità partizione, utilizzare il filtro "PartitionKey eq *nome_partizione*". Nell'esempio seguente viene illustrato come recuperare tutte le entità di `tasksSeattle` partizione passando un filtro al metodo **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Recuperare un sottoinsieme di entità in una partizione

Lo stesso modello utilizzato nell'esempio precedente può essere utilizzato per recuperare un sottoinsieme di entità in una partizione. Il subset di entità si recuperano dipendono dal filtro utilizzare (per ulteriori informazioni, vedere [tabelle di query ed entità][filters]). Nell'esempio seguente viene illustrato come utilizzare un filtro per recuperare tutte le entità con uno specifico `Location` e `DueDate` inferiore a una data specificata.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Recuperare un sottoinsieme di proprietà dell'entità

Una query è possibile recuperare un sottoinsieme di proprietà di entità. Questa tecnica, denominata *proiezione*riduce la larghezza di banda e migliorare le prestazioni di query, in particolare per entità di grandi dimensioni. Per specificare una proprietà da recuperare, passare il nome della proprietà per il metodo **Query -> addSelectField** . È possibile chiamare questo metodo più volte per aggiungere altre proprietà. Dopo l'esecuzione **TableRestProxy -> queryEntities**, entità restituita sarà disponibile solo le proprietà selezionate. (Se si desidera restituire un sottoinsieme di entità della tabella, usare un filtro nella come illustrato nella query precedenti.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Aggiornare un'entità

Utilizzare i metodi di **entità -> setProperty** ed **entità -> addProperty** l'entità e quindi chiamando **TableRestProxy -> updateEntity**, è possibile aggiornare un'entità esistente. Nell'esempio seguente recupera un'entità, una proprietà di modifica, rimuove un'altra proprietà e aggiunge una nuova proprietà. Si noti che è possibile rimuovere una proprietà impostando il relativo valore su **null**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Eliminare un'entità

Per eliminare un'entità, passare il nome della tabella e l'entità `PartitionKey` e `RowKey` al metodo **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si noti che per ottimistica, è possibile impostare Etag per un'entità da eliminare, utilizzando il metodo **DeleteEntityOptions -> setEtag** e passare all'oggetto **DeleteEntityOptions** **deleteEntity** come un quarto parametro.

## <a name="batch-table-operations"></a>Operazioni batch in una tabella

Il metodo **TableRestProxy -> batch** consente di eseguire più operazioni in una singola richiesta. Modello di comporta l'aggiunta di operazioni oggetto **batchrequest seguito** e passare all'oggetto **batchrequest seguito** il metodo **TableRestProxy -> batch** . Per aggiungere un'operazione su un oggetto di **batchrequest seguito** , è possibile chiamare uno dei metodi seguenti più volte:

* **addInsertEntity** (aggiunge un'operazione di insertEntity)
* **addUpdateEntity** (aggiunge un'operazione di updateEntity)
* **addMergeEntity** (aggiunge un'operazione di mergeEntity)
* **addInsertOrReplaceEntity** (aggiunge un'operazione di insertOrReplaceEntity)
* **addInsertOrMergeEntity** (aggiunge un'operazione di insertOrMergeEntity)
* **addDeleteEntity** (aggiunge un'operazione di deleteEntity)

Nell'esempio seguente viene illustrato come eseguire le operazioni di **insertEntity** e **deleteEntity** in una singola richiesta:

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Per ulteriori informazioni sui batch operazioni con tabelle, vedere [Esecuzione di transazioni gruppo entità][entity-group-transactions].

## <a name="delete-a-table"></a>Eliminare una tabella

Infine, per eliminare una tabella, passare il nome della tabella per il metodo **TableRestProxy -> deleteTable** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base del servizio di tabella di Azure, seguire questi collegamenti a informazioni sulle attività di archiviazione più complesse.

- Visitare il [blog del team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
