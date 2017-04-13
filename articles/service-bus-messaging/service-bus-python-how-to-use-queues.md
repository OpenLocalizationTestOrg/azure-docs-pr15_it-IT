<properties 
    pageTitle="Come utilizzare code Bus di servizio con Python | Microsoft Azure" 
    description="Informazioni sull'utilizzo di Python Code Bus di servizio Azure." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>Come usare code Bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In questo articolo viene descritto come utilizzare code Bus di servizio. Gli esempi scritto in Python e utilizzano il [pacchetto di Python Azure servizio Bus][]. Gli scenari coperti includono **la creazione di code, inviare e ricevere messaggi**e **all'eliminazione di code**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Per installare il [pacchetto di Python Azure servizio Bus][]o Python, vedere la [Guida all'installazione di Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Creare una coda

L'oggetto **ServiceBusService** consente di utilizzare code. Aggiungere il codice seguente nella parte superiore di qualsiasi file Python in cui si desidera accedere a livello di programmazione Bus di servizio:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

Il codice seguente viene creato un oggetto **ServiceBusService** . Sostituire `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` con spazio dei nomi, nome chiave di accesso condiviso firma (SA) e valore.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

I valori per il nome della chiave SA e valore sono disponibili le informazioni sulla connessione [portale classica Azure][] o nel riquadro di **proprietà** di Visual Studio quando si seleziona lo spazio dei nomi Bus di servizio in Esplora Server (come illustrato nella sezione precedente).

```
bus_service.create_queue('taskqueue')
```

**create_queue** supporta anche altre opzioni che consentono di ignorare le impostazioni di coda predefinite, ad esempio durata messaggio (TTL) o le dimensioni massime coda. Nell'esempio seguente imposta le dimensioni massime coda 5GB e il valore TTL al minuto:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

Per inviare un messaggio a una coda Bus di servizio, l'applicazione chiama il **inviare\_coda\_messaggio** metodo sull'oggetto **ServiceBusService** .

Nell'esempio seguente viene illustrato come inviare un messaggio di prova alla coda denominata *taskqueue utilizzando* **inviare\_coda\_messaggio**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Code Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in una coda ma c'è un'estremità alla dimensione totale dei messaggi contenute in una coda. Questa dimensione coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per ulteriori informazioni sulle quote, vedere [le quote di Bus di servizio][].

## <a name="receive-messages-from-a-queue"></a>Ricevere i messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il **ricevere\_coda\_messaggio** metodo sull'oggetto **ServiceBusService** :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

I messaggi eliminati dalla coda quando vengono letti quando il parametro **Anteprima\_blocco** è impostato su **False**. È possibile leggere (anteprima) e bloccare il messaggio senza eliminare dalla coda impostando il parametro **Anteprima\_blocco** su **True**.

Il comportamento di lettura e l'eliminazione del messaggio come parte dell'operazione di ricezione è il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Se il **Anteprima\_blocco** parametro è impostato su **True**, la ricezione diventa un'operazione in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando il metodo **delete** sull'oggetto del **messaggio** . Il metodo **eliminare** contrassegnare il messaggio come utilizzata e rimuoverlo dalla coda.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **sbloccare** sull'oggetto del **messaggio** . In questo modo Bus di servizio sbloccare il messaggio all'interno della coda e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della coda, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima che venga chiamato il metodo di **eliminare** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Spesso detta **Almeno una volta elaborazione**, vale a dire ogni messaggio verrà elaborato almeno una volta ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando la proprietà **ID messaggio** del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso le nozioni di base di code Bus di servizio, seguire questi collegamenti per altre informazioni.

-   Vedere [code, argomenti e le sottoscrizioni][].

[Portale classica Azure]: https://manage.windowsazure.com
[Pacchetto di Python Azure servizio Bus]: https://pypi.python.org/pypi/azure-servicebus  
[Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
[Quote di Bus di servizio]: service-bus-quotas.md
 
