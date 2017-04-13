<properties
    pageTitle="Come usare archivio tabelle Azure da trascrizione | Microsoft Azure"
    description="Archiviare i dati strutturati nel cloud mediante archiviazione tabella Azure, un archivio di dati NoSQL."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Come usare archivio tabelle Azure da trascrizione

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire gli scenari comuni utilizzando il servizio di Azure tabella. Gli esempi sono scritti con l'API trascrizione. Gli scenari coperti includono **la creazione e l'eliminazione di una tabella, inserimento e query su entità in una tabella**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creare un'applicazione di trascrizione

Per istruzioni su come creare un'applicazione di trascrizione, vedere [trascrizione in applicazione Web guide su una macchina virtuale Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per accedere all'archiviazione

Per utilizzare lo spazio di archiviazione di Azure, è necessario scaricare e usare il pacchetto di azure trascrizione che include un set di raccolte semplicità di utilizzo che comunicare con i servizi resto dello spazio di archiviazione.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix).

2. Nella finestra di comando per installare il simbolo e le dipendenze, digitare **indicatore installare azure** .

### <a name="import-the-package"></a>Importare il pacchetto

Utilizzare un editor di testo, aggiungere quanto segue nella parte superiore del file trascrizione nel punto in cui si prevede di utilizzare lo spazio di archiviazione:

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione Azure

Modulo di azure leggerà le variabili di ambiente **AZURE\_lo spazio di archiviazione\_ACCOUNT** e **AZURE\_lo spazio di archiviazione\_accesso\_chiave** le informazioni necessarie per connettersi al proprio account di archiviazione Azure. Se non sono tali variabili, è necessario specificare le informazioni sull'account prima di utilizzare **Azure::TableService** con il codice seguente:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Per ottenere i valori da un classico o account di archiviazione di Manager delle risorse nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Selezionare **I tasti di scelta**e l'impostazioni sulla destra.
4. In e il chiavi di accesso che viene visualizzata, verrà visualizzato il tasto 1 e 2 tasto di scelta. È possibile usare uno di questi elementi. 
5. Fare clic sull'icona di copia per copiare la chiave negli Appunti. 

Per ottenere i valori da un account di archiviazione classica nel portale di Azure classico:

1. Accedere al [portale di Azure classica](https://manage.windowsazure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Fare clic su **GESTISCI i tasti di scelta** nella parte inferiore del riquadro di spostamento.
4. Nella finestra di dialogo popup vengono visualizzati il nome dell'account di archiviazione, tasto primario e secondario tasto di scelta. Per il tasto di scelta, è possibile utilizzare quello principale o quello secondario. 
5. Fare clic sull'icona di copia per copiare la chiave negli Appunti.

## <a name="create-a-table"></a>Creare una tabella

L'oggetto **Azure::TableService** consente di utilizzare le tabelle ed entità. Per creare una tabella, utilizzare la **creare\_table()** metodo. Nell'esempio seguente viene creata una tabella o stampare l'errore eventuale.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un oggetto hash che consente di definire le proprietà di entità. Si noti che per ogni entità è necessario specificare un **PartitionKey** e **RowKey**. Questi sono gli identificatori univoci delle entità e sono valori che è possono eseguire una query più velocemente rispetto alle altre proprietà del. Spazio di archiviazione Azure utilizza **PartitionKey** per distribuire automaticamente entità della tabella molti nodi di archiviazione. Entità con la stessa **PartitionKey** vengono memorizzate nello stesso nodo. **RowKey** corrisponde all'ID univoco dell'entità all'interno della partizione che a cui appartiene.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Aggiornare un'entità

Sono disponibili diversi metodi disponibili per aggiornare un'entità esistente:

* **aggiornare\_entity():** Aggiornare un'entità esistente sostituendolo.
* **unione\_entity():** Aggiorna un'entità esistente mediante l'unione di nuovi valori di proprietà dell'entità esistente.
* **inserire\_o\_unione\_entity():** Aggiorna un'entità esistente sostituendolo. Se è presente alcuna entità, verrà inserito un nuovo PIN:
* **inserire\_o\_sostituire\_entity():** Aggiorna un'entità esistente mediante l'unione di nuovi valori di proprietà dell'entità esistente. Se è presente alcuna entità, verrà inserito uno nuovo.

Nell'esempio riportato di seguito viene illustrato l'aggiornamento un'entità utilizzando **aggiornare\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Con **aggiornare\_entity()** e **unione\_entity()**, se l'entità che si sta aggiornando non esiste l'operazione di aggiornamento avrà esito negativo. Pertanto se si desidera archiviare un'entità indipendentemente se esiste già, utilizzare invece **inserire\_o\_sostituire\_entity()** o **inserire\_o\_unione\_entity()**.

## <a name="work-with-groups-of-entities"></a>Uso dei gruppi di entità

In alcuni casi è opportuno inviare più operazioni in un batch per garantire atomica elaborazione dal server. Per eseguire questa operazione, è prima di tutto creare un oggetto **Batch** e quindi usare il **eseguire\_batch()** metodo su **TableService**. Nell'esempio seguente viene illustrato l'invio di due entità con RowKey 2 e 3 in batch. Si noti che funziona solo per le entità con la stessa PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Query per un'entità

Per ottenere un'entità in una tabella, utilizzare la **ottenere\_entity()** metodo passando il nome della tabella, **PartitionKey** e **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Un set di entità della query

Per eseguire una query una serie di entità in una tabella, creare un oggetto hash query e utilizzare la **query\_entities()** metodo. Nell'esempio seguente viene illustrato come ottenere tutte le entità con la stessa **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Se il risultato è troppo grande per una singola query restituire, verrà restituito un token di continuazione che è possibile utilizzare per recuperare le pagine successive.

## <a name="query-a-subset-of-entity-properties"></a>Un sottoinsieme di entità proprietà della query

Una query in una tabella è possibile recuperare solo alcune proprietà di un'entità. Questa tecnica, denominata "proiezione" riduce la larghezza di banda e migliorare le prestazioni di query, in particolare per entità di grandi dimensioni. Utilizzare la clausola select e passare i nomi delle proprietà che si desidera visualizzare al client.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Eliminare un'entità

Per eliminare un'entità, utilizzare la **eliminare\_entity()** metodo. È necessario passare il nome della tabella che contiene l'entità, la PartitionKey e RowKey dell'entità.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Eliminare una tabella

Per eliminare una tabella, usare il **eliminare\_table()** metodo e passare il nome della tabella che si desidera eliminare.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle attività di archiviazione più complesse, seguire questi collegamenti:

- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Archivio di [Azure SDK per trascrizione](http://github.com/WindowsAzure/azure-sdk-for-ruby) in GitHub
