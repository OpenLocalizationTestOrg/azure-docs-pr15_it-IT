<properties
    pageTitle="Come utilizzare lo spazio di archiviazione coda da Python | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di Azure coda di Python per creare ed eliminare code e inserire, ottenere ed eliminare i messaggi."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Come utilizzare lo spazio di archiviazione coda da Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire gli scenari comuni utilizzando il servizio di archiviazione di Azure coda. Gli esempi scritto in Python e utilizzano [Microsoft Azure archiviazione SDK per Python]. Gli scenari coperti includono **l'inserimento**, **la visualizzazione**, **ottenere**e **l'eliminazione** coda messaggi, nonché **Creazione ed eliminazione di code**. Per ulteriori informazioni sulle code, vedere la sezione [passaggi successivi].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Procedura: Creare una coda

L'oggetto **QueueService** consente di utilizzare code. Il codice seguente viene creato un oggetto **QueueService** . Aggiungere quanto segue nella parte superiore di qualsiasi file Python in cui si desidera accedere a livello di programmazione archiviazione Azure:

    from azure.storage.queue import QueueService

Il codice seguente crea un oggetto **QueueService** utilizzando la chiave account e nome account lo spazio di archiviazione. Sostituire "account personale" e 'MiaChiave' con il nome dell'account e la chiave.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: Inserire una coda di un messaggio

Per inserire un messaggio in una coda, utilizzare la **inserire\_messaggio** per creare un nuovo messaggio e aggiungerlo alla coda.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Procedura: Leggere il messaggio successivo

È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda chiamando il **Anteprima\_messaggi** metodo. Per impostazione predefinita, **Anteprima\_messaggi** legge un singolo messaggio.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Procedura: Annullare l'accodamento di messaggi

Il codice rimuove un messaggio da una coda in due passaggi. Quando si chiama **ottenere\_messaggi**, viene visualizzato il messaggio successivo in una coda per impostazione predefinita. Un messaggio restituito da **ottenere\_messaggi** diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare **eliminare\_messaggio**. Questo processo in due fasi di rimozione di un messaggio garantisce che quando il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Le chiamate di codice **eliminare\_messaggio** subito dopo il messaggio è stato elaborato.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Esistono due modi per personalizzare il recupero dei messaggi da una coda.
Prima di tutto, è possibile ottenere un batch di messaggi (fino a 32). In secondo luogo, è possibile impostare un timeout invisibility o allungare, consentendo al codice più o meno tempo per l'elaborazione di ogni messaggio completamente. Nell'esempio di codice viene illustrato come utilizzare il **ottenere\_messaggi** metodo per ottenere 16 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo for. Imposta inoltre il timeout invisibility a cinque minuti per ogni messaggio.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: Modificare il contenuto di un messaggio in coda

È possibile modificare il contenuto di un messaggio sul posto nella coda. Se il messaggio rappresenta un'operazione di lavoro, è possibile utilizzare questa caratteristica per aggiornare lo stato dell'attività di lavoro. Il codice seguente viene utilizzata la **aggiornare\_messaggio** metodo per aggiornare un messaggio. Il timeout di visibilità è impostato su 0, ovvero il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Procedura: Ottenere la lunghezza della coda

È possibile ottenere una stima del numero di messaggi in una coda. Il **ottenere\_coda\_metadati** metodo richiede il servizio di coda restituire i metadati sulla coda e **approximate_message_count**. Il risultato è solo approssimativo perché i messaggi possono essere aggiunti o rimossi dopo il servizio di coda risponde a una convocazione.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Procedura: Eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il **eliminare\_coda** metodo.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione coda, seguire questi collegamenti per altre informazioni.

- [Centro per sviluppatori Python](/develop/python/)
- [Servizi di archiviazione Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del Team di archiviazione Azure]
- [Spazio di archiviazione di Microsoft Azure SDK per Python]

[Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Spazio di archiviazione di Microsoft Azure SDK per Python]: https://github.com/Azure/azure-storage-python
