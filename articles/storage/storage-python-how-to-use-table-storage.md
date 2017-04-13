<properties
    pageTitle="Come utilizzare lo spazio di archiviazione tabella da Python | Microsoft Azure"
    description="Archiviare i dati strutturati nel cloud mediante archiviazione tabella Azure, un archivio di dati NoSQL."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Come utilizzare lo spazio di archiviazione tabella da Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire scenari comuni tramite il servizio di archiviazione tabella Azure. Gli esempi scritto in Python e utilizzano [Microsoft Azure archiviazione SDK per Python]. Gli scenari coperti includono creazione ed eliminazione di una tabella, oltre alle modalità di inserimento e query su entità in una tabella.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Creare una tabella

L'oggetto **TableService** consente di lavorare con i servizi di tabella. Il codice seguente viene creato un oggetto **TableService** . Aggiungere il codice nella parte superiore di qualsiasi file Python in cui si desidera accedere a livello di programmazione archiviazione Azure:

    from azure.storage.table import TableService, Entity

Il codice seguente crea un oggetto **TableService** utilizzando la chiave account e nome account lo spazio di archiviazione.  Sostituire "account personale" e 'MiaChiave' con il nome dell'account e la chiave.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un dizionario o entità che definisce l'entità proprietà nomi e i valori. Si noti che per ogni entità, è necessario specificare **PartitionKey** e **RowKey**. Si tratta di identificatori univoci delle entità. È possibile eseguire una query utilizzando questi valori più veloce è possibile eseguire una query le altre proprietà. Il sistema utilizza **PartitionKey** per distribuire automaticamente le entità della tabella molti nodi di archiviazione.
Entità che hanno la stessa **PartitionKey** vengono memorizzate nello stesso nodo. **RowKey** corrisponde all'ID univoco dell'entità all'interno della partizione a cui appartiene.

Per aggiungere un'entità alla tabella, passare un oggetto di dizionario per il **inserire\_entità** metodo.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

È anche possibile passare un'istanza della classe di **entità** per il **inserire\_entità** metodo.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Aggiornare un'entità

Questo codice viene illustrato come sostituire la versione precedente di un'entità esistente con una versione aggiornata.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Se non esiste l'entità che viene aggiornata, l'operazione di aggiornamento avrà esito negativo. Se si desidera archiviare un'entità indipendentemente dal fatto esistenti prima di utilizzare **inserire\_o\_replace_entity**.
Nell'esempio seguente la prima chiamata sostituirà l'entità esistente. La seconda chiamata verrà inserita una nuova entità dall'alcuna entità con specificato **PartitionKey** e **RowKey** è presente nella tabella.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Modificare un gruppo di entità

In alcuni casi è opportuno inviare più operazioni in un batch per garantire atomica elaborazione dal server. Per eseguire questa operazione, utilizzare la classe **TableBatch** . Quando si desidera inviare il batch, si chiama **commit\_batch**. Si noti che tutte le entità devono trovarsi nella stessa partizione per essere modificate in blocco. Nell'esempio seguente somma due entità in un batch.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Batch possono essere utilizzate anche con la sintassi di gestione di contesto:

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Query per un'entità

Per ottenere un'entità in una tabella, utilizzare la **ottenere\_entità** metodo passando **PartitionKey** e **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Un set di entità della query

In questo esempio per trovare che tutte le attività Seattle in base a **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Un sottoinsieme di entità proprietà della query

Una query in una tabella è possibile recuperare solo alcune proprietà di un'entità.
Questa tecnica, denominata *proiezione*riduce la larghezza di banda e migliorare le prestazioni di query, in particolare per entità di grandi dimensioni. Utilizzare il parametro **Selezionare** e passare i nomi delle proprietà che si desidera visualizzare al client.

La query nel codice seguente restituisce solo le descrizioni di entità nella tabella.

[AZURE.NOTE] Il frammento di codice seguente funziona solo con il servizio di archiviazione cloud. Non è supportata dall'emulatore lo spazio di archiviazione.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Eliminare un'entità

È possibile eliminare un'entità tramite la relativa chiave partizione e riga.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Eliminare una tabella

Il codice seguente elimina una tabella da un account di archiviazione.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione tabella, seguire questi collegamenti per altre informazioni.

- [Centro per sviluppatori Python](/develop/python/)
- [Servizi di archiviazione Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del Team di archiviazione Azure]
- [Spazio di archiviazione di Microsoft Azure SDK per Python]

[Blog del Team di spazio di archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Spazio di archiviazione di Microsoft Azure SDK per Python]: https://github.com/Azure/azure-storage-python
