<properties 
    pageTitle="Come utilizzare argomenti Bus di servizio con Python | Microsoft Azure" 
    description="Informazioni su come utilizzare Bus di servizio Azure argomenti e le sottoscrizioni da Python." 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare le sottoscrizioni e gli argomenti della Bus di servizio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In questo articolo viene descritto come utilizzare le sottoscrizioni e argomenti Bus di servizio. Gli esempi scritto in Python e utilizzano il [pacchetto di Azure Python][]. Scenari coperti includono **la creazione di argomenti e le sottoscrizioni**, **creazione di filtri di abbonamento**, **l'invio di messaggi a un argomento**, **la ricezione di messaggi da un abbonamento**ed **eliminazione argomenti e le sottoscrizioni**. Per ulteriori informazioni su argomenti e le sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Nota:** Se è necessario installare Python o il [pacchetto di Azure Python][], vedere la [Guida all'installazione di Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Creare un argomento

L'oggetto **ServiceBusService** consente di utilizzare gli argomenti. Aggiungere quanto segue nella parte superiore di qualsiasi file Python in cui si desidera accedere a livello di programmazione Bus di servizio:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Il codice seguente viene creato un oggetto **ServiceBusService** . Sostituire `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` con spazio dei nomi effettivo valore di nome e chiave della chiave condiviso accesso firma (SA).

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

È possibile ottenere i valori per il nome della chiave SA e valore dal [portale di Azure][].

```
bus_service.create_topic('mytopic')
```

**creare\_argomento** supporta anche altre opzioni che consentono di ignorare le impostazioni predefinite argomento, ad esempio durata messaggio o le dimensioni massime argomento. Nell'esempio seguente imposta le dimensioni massime argomento a 5 GB e un'ora in live valore (TTL) di 1 minuto:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Creare le sottoscrizioni

Gli abbonamenti a argomenti vengono anche creati con l'oggetto **ServiceBusService** . Le sottoscrizioni sono denominate e possono avere un filtro facoltativo che limita l'insieme di messaggi recapitati a coda virtuale dell'abbonamento.

> [AZURE.NOTE] Le sottoscrizioni sono permanenti e continueranno a esistere fino a o l'argomento a cui è stata effettuata la sottoscrizione, vengono eliminati.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare un abbonamento con il filtro predefinito (MatchAll)

Il filtro **MatchAll** è il filtro predefinito che viene utilizzato se viene specificato alcun filtro quando viene creato un nuovo abbonamento. Quando viene utilizzato il filtro **MatchAll** , tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale dell'abbonamento. Nell'esempio seguente viene creata una sottoscrizione denominata 'AllMessages' e viene utilizzato il filtro di **MatchAll** predefinito.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Creare gli abbonamenti con i filtri

È inoltre possibile definire filtri che consentono di specificare che i messaggi inviati a un argomento devono apparire all'interno di una sottoscrizione di un argomento specifico.

Il tipo di filtro supportata dagli abbonamenti più flessibile è un **SqlFilter**, che implementa un sottoinsieme di SQL92. Filtri SQL funzionano con le proprietà dei messaggi pubblicati all'argomento. Per ulteriori informazioni sulle espressioni che possono essere usate con un filtro SQL, vedere la sintassi [SqlFilter.SqlExpression][] .

È possibile aggiungere filtri a un abbonamento con il **creare\_regola** metodo dell'oggetto **ServiceBusService** . Questo metodo consente di aggiungere nuovi filtri a un abbonamento esistente.

> [AZURE.NOTE] Poiché il filtro predefinito viene applicato automaticamente a tutti i nuovi abbonamenti, è necessario rimuovere il filtro predefinito o **MatchAll** sovrascriverà i filtri che è possibile specificare. È possibile rimuovere la regola predefinita utilizzando il **eliminare\_regola** metodo dell'oggetto **ServiceBusService** .

Nell'esempio seguente viene creata una sottoscrizione denominata `HighMessages` con **SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà personalizzata **messagenumber** maggiore di 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Allo stesso modo, nell'esempio seguente viene creata una sottoscrizione denominata `LowMessages` con **SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà di **messagenumber** minore o uguale a 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

A questo punto, quando un messaggio viene inviato al `mytopic` sempre è stato recapitato a ricevitori sottoscritto l'abbonamento argomento **AllMessages** e in modo selettivo recapitati ricevitori sottoscritti le sottoscrizioni di argomento **HighMessages** e **LowMessages** (a seconda il contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

Per inviare un messaggio a un argomento Bus di servizio, è necessario usare l'applicazione di **inviare\_argomento\_messaggio** metodo dell'oggetto **ServiceBusService** .

Nell'esempio seguente viene illustrato come inviare cinque testare i messaggi a `mytopic`. Si noti che il valore della proprietà **messagenumber** di ogni messaggio varia a iterazione del ciclo (questa impostazione determina le sottoscrizioni che ricevano dal destinatario):

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Argomenti Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in un argomento ma c'è un'estremità alla dimensione totale dei messaggi contenute in un argomento. Dimensione in questo argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Per ulteriori informazioni sulle quote, vedere [le quote di Bus di servizio][].

## <a name="receive-messages-from-a-subscription"></a>Ricezione di messaggi da una sottoscrizione

I messaggi vengono ricevuti da un abbonamento tramite il **ricevere\_sottoscrizione\_messaggio** metodo sull'oggetto **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

I messaggi eliminati dalla sottoscrizione quando vengono letti quando il parametro **Anteprima\_blocco** è impostato su **False**. È possibile leggere (anteprima) e bloccare il messaggio senza eliminare dalla coda impostando il parametro **Anteprima\_blocco** su **True**.

Il comportamento di lettura e l'eliminazione del messaggio come parte dell'operazione di ricezione è il modello più semplice e funziona meglio per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Se il **Anteprima\_blocco** parametro è impostato su **True**, la ricezione diventa un'operazione in due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando il metodo **delete** sull'oggetto del **messaggio** . Il metodo **delete** contrassegna il messaggio come utilizzata e lo rimuove dalla sottoscrizione.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non riesce a elaborare il messaggio per qualche motivo, è possibile chiamare il metodo **sbloccare** sull'oggetto del **messaggio** . In questo modo Bus di servizio sbloccare il messaggio all'interno della sottoscrizione e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della sottoscrizione, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio sblocca automaticamente il messaggio e rende disponibile al ricevuto nuovamente.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima che venga chiamato il metodo di **eliminare** , quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Spesso detta **Almeno una volta elaborazione**, vale a dire ogni messaggio verrà elaborato almeno una volta ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. A tale scopo spesso utilizzando la proprietà **ID messaggio** del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare gli argomenti e le sottoscrizioni

Gli argomenti e le sottoscrizioni sono permanenti e devono essere eliminate in modo esplicito tramite il [portale di Azure][] o a livello di programmazione. Nell'esempio seguente viene illustrato come eliminare l'argomento relativo alla `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Eliminazione di un argomento elimina anche le sottoscrizioni che sono registrate con l'argomento. Le sottoscrizioni possono anche essere eliminate in modo indipendente. Il codice seguente viene illustrato come eliminare una sottoscrizione denominata `HighMessages` dalla `mytopic` argomento:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base degli argomenti relativi a Bus di servizio, seguire questi collegamenti per altre informazioni.

-   Vedere [code, argomenti e le sottoscrizioni][].
-   Guida di riferimento per [SqlFilter.SqlExpression][].

[Portale di Azure]: https://portal.azure.com
[Pacchetto di Azure Python]: https://pypi.python.org/pypi/azure  
[Code, argomenti e le sottoscrizioni]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Quote di Bus di servizio]: service-bus-quotas.md 
