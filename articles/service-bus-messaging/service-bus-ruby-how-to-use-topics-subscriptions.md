<properties
    pageTitle="Come usare argomenti Bus di servizio (trascrizione) | Microsoft Azure"
    description="Informazioni su come usare gli argomenti Bus di servizio e le sottoscrizioni in Azure. Esempi di codice vengono scritte Applications trascrizione."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topicssubscriptions"></a>Come usare Bus argomenti/le sottoscrizioni di assistenza

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In questo articolo viene descritto come utilizzare gli argomenti Bus di servizio e le sottoscrizioni da applicazioni trascrizione. Gli scenari coperti includono **la creazione di argomenti e le sottoscrizioni, creazione di filtri di abbonamento, l'invio di messaggi** a un argomento, **la ricezione di messaggi da una sottoscrizione**ed **eliminare gli argomenti e le sottoscrizioni**. Per ulteriori informazioni su argomenti e le sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Le sottoscrizioni e gli argomenti della Bus di servizio

Supportano per sottoscrizioni e argomenti Bus di servizio una *pubblicazione/sottoscrizione* messaggistica modello di comunicazione. Quando si utilizza gli argomenti e le sottoscrizioni, componenti di un'applicazione distribuita non comunicano direttamente tra loro. vengono invece scambiare messaggi tramite un argomento che si comporta come valore intermedio.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

A differenza di code Bus di servizio, ogni messaggio in cui viene elaborato da un singolo utente, argomenti e le sottoscrizioni specificare una maschera **uno-a-molti** di comunicazione, utilizzando un modello di pubblicazione/sottoscrizione. È possibile registrare più abbonamenti a un argomento. Quando un messaggio viene inviato a un argomento, quindi renderlo disponibile per le sottoscrizioni per l'elaborazione in modo indipendente.

Una sottoscrizione argomento è simile a una coda virtuale che riceve le copie dei messaggi inviati all'argomento. Facoltativamente, è possibile registrare le regole di filtro per un argomento in base al richiede la sottoscrizione, in modo da poter filtro/limitare i messaggi a un argomento vengono ricevuti dalle quali sottoscrizioni argomento.

Le sottoscrizioni e servizio Bus argomenti consentono di scalare per l'elaborazione di un numero elevato di messaggi su un numero elevato di utenti e applicazioni.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi

Per iniziare a utilizzare code Bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione. Poiché il [portale di Azure][] non creare lo spazio dei nomi con una connessione ACS, è necessario creare lo spazio dei nomi tramite l'interfaccia della riga di comando.

Per creare uno spazio dei nomi:

1. Aprire una finestra di console di Azure Powershell.

2. Digitare il comando seguente per creare uno spazio dei nomi. Specificare il valore dello spazio dei nomi e la stessa area dell'applicazione.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Creare Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Ottenere le credenziali di gestione per lo spazio dei nomi predefiniti

Per eseguire operazioni di gestione, ad esempio la creazione di una coda nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi.

Il cmdlet di PowerShell che eseguire per creare spazio dei nomi del servizio Bus Visualizza la chiave che è possibile utilizzare per gestire lo spazio dei nomi. Copiare il valore di **DefaultKey** . Si utilizzerà il valore nel codice più avanti in questa esercitazione.

![Tasto di copia](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> È possibile trovare questo tasto anche se si accede al [portale di Azure][] e individuare le informazioni di connessione dello spazio dei nomi.

## <a name="create-a-ruby-application"></a>Creare un'applicazione di trascrizione

Per ulteriori informazioni, vedere [creare un'applicazione di trascrizione in Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione in uso servizio Bus

Per utilizzare Bus di servizio, scaricare e usare il pacchetto di Azure trascrizione, che include un set di raccolte semplicità di utilizzo che comunicare con i servizi di resto dello spazio di archiviazione.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix).

2. Digitare "azure installa indicatore" nella finestra di comando per installare il simbolo e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto

Usare un editor di testo, aggiungere quanto segue nella parte superiore del file trascrizione in cui si intende utilizzare lo spazio di archiviazione:

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurare una connessione Bus di servizio

Modulo di Azure legge le variabili di ambiente **AZURE\_SERVICEBUS\_spazio dei nomi** e **AZURE\_SERVICEBUS\_accesso\_chiave** le informazioni necessarie per la connessione allo spazio dei nomi. Se non sono tali variabili, è necessario specificare le informazioni dello spazio dei nomi prima di utilizzare **Azure::ServiceBusService** con il codice seguente:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Impostare il valore dello spazio dei nomi per il valore che è stato creato anziché l'intero URL. Usare, ad esempio, **"yourexamplenamespace"**, non "yourexamplenamespace.servicebus.windows.net".

## <a name="create-a-topic"></a>Creare un argomento

L'oggetto **Azure::ServiceBusService** consente di utilizzare gli argomenti. Il codice seguente viene creato un oggetto **Azure::ServiceBusService** . Per creare un argomento, utilizzare la **creare\_topic()** metodo. Nell'esempio seguente viene creato un argomento o stampato gli errori, se presenti.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

È anche possibile passare un oggetto **Azure::ServiceBus::Topic** con ulteriori opzioni, che consentono di ignorare le impostazioni predefinite argomento, ad esempio il tempo di messaggi a live o dimensione massima della coda. Nell'esempio seguente è illustrata l'impostazione le dimensioni massime coda 5GB e l'ora a live al minuto:

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Creare le sottoscrizioni

Sottoscrizioni argomento vengono anche create con l'oggetto **Azure::ServiceBusService** . Le sottoscrizioni sono denominate e possono avere un filtro facoltativo che limita l'insieme di messaggi recapitati a coda virtuale dell'abbonamento.

Le sottoscrizioni sono permanenti e continueranno a esistere fino a quando uno dei due abbiano o l'argomento sono associate, vengono eliminati. Se l'applicazione contiene la logica per creare una sottoscrizione, deve verificare se la sottoscrizione esiste già utilizzando il metodo getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare un abbonamento con il filtro predefinito (MatchAll)

Il filtro **MatchAll** è il filtro predefinito che viene utilizzato se viene specificato alcun filtro quando viene creato un nuovo abbonamento. Quando viene utilizzato il filtro **MatchAll** , tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale dell'abbonamento. Nell'esempio seguente viene creata una sottoscrizione denominata "tutti messaggi" e utilizza il filtro di **MatchAll** predefinito.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Creare gli abbonamenti con i filtri

È inoltre possibile definire filtri che consentono di specificare che i messaggi inviati a un argomento devono apparire all'interno di una sottoscrizione specifica.

Il tipo di filtro supportata dagli abbonamenti più flessibile è **Azure::ServiceBus::SqlFilter**, che implementa un sottoinsieme di SQL92. Filtri SQL funzionano con le proprietà dei messaggi pubblicati all'argomento. Per ulteriori informazioni sulle espressioni che possono essere usate con un filtro SQL, controllare la sintassi [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

È possibile aggiungere filtri a un abbonamento con il **creare\_rule()** metodo dell'oggetto **Azure::ServiceBusService** . Questo metodo consente di aggiungere nuovi filtri a un abbonamento esistente.

Poiché il filtro predefinito viene applicato automaticamente a tutti i nuovi abbonamenti, è necessario rimuovere il filtro predefinito o **MatchAll** sovrascrivono gli altri filtri che è possibile specificare. È possibile rimuovere la regola predefinita utilizzando il **eliminare\_rule()** metodo sull'oggetto **Azure::ServiceBusService** .

Nell'esempio seguente viene creata una sottoscrizione denominata "ad alta-messaggi" con un **Azure::ServiceBus::SqlFilter** che consente di selezionare solo i messaggi che hanno una personalizzata **messaggio\_numero** proprietà maggiore di 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Allo stesso modo, nell'esempio seguente viene creata una sottoscrizione denominata "basso-messaggi" con un **Azure::ServiceBus::SqlFilter** che consente di selezionare solo i messaggi che hanno una proprietà di **message_number** minore o uguale a 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Quando un messaggio a questo punto viene inviato a "argomento di prova", è sempre recapitati a ricevitori sottoscritto l'abbonamento argomento "tutti messaggi" e in modo selettivo recapitati ricevitori sottoscritti le sottoscrizioni argomento "ad alta-messaggi" e "basso-" (in base al contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

Per inviare un messaggio a un argomento Bus di servizio, è necessario usare l'applicazione di **inviare\_argomento\_message()** metodo sull'oggetto **Azure::ServiceBusService** . I messaggi inviati ad argomenti Bus di servizio sono istanze di oggetti **Azure::ServiceBus::BrokeredMessage** . Gli oggetti **Azure::ServiceBus::BrokeredMessage** è un insieme di proprietà standard (ad esempio **etichette** e **ora\_a\_live**), un dizionario che viene utilizzata per contenere proprietà specifiche di applicazione personalizzata e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa per il **inviare\_argomento\_message()** metodo e qualsiasi richiesto verrà proprietà standard per valori predefiniti.

Nell'esempio seguente viene illustrato come inviare messaggi a "argomento di prova" cinque prova. Si noti che il valore di proprietà personalizzate **message_number** di ogni messaggio varia a iterazione del ciclo (questa impostazione determina quale abbonamento riceve):

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Argomenti Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in un argomento ma c'è un'estremità alla dimensione totale dei messaggi contenute in un argomento. Dimensione in questo argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ricezione di messaggi da una sottoscrizione

I messaggi vengono ricevuti da un abbonamento tramite il **ricevere\_sottoscrizione\_message()** metodo sull'oggetto **Azure::ServiceBusService** . Per impostazione predefinita, i messaggi vengono read(peak) e bloccato senza eliminare dalla sottoscrizione. È possibile leggere ed eliminare il messaggio dalla sottoscrizione impostando la **Anteprima\_blocco** opzione su **false**.

Il comportamento predefinito consente la lettura e l'eliminazione di un'operazione di due fasi, che consente inoltre supportare le applicazioni che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, che venga completato la seconda fase del processo di ricezione chiamando **eliminare\_sottoscrizione\_message()** metodo e fornire il messaggio da eliminare come parametro. Il **eliminare\_sottoscrizione\_message()** metodo contrassegnare il messaggio come utilizzata e rimuovere dalla sottoscrizione.

Se il **: anteprima\_blocco** parametro è impostato su **false**, la lettura, eliminare il messaggio diventa il modello più semplice e adatta per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus verrà contrassegnato il messaggio come utilizzata, quindi quando l'applicazione riavvia e inizia a utilizzare di nuovo i messaggi non verrà dispone di aver ricevuto il messaggio che è stato utilizzato prima l'arresto anomalo.

Nell'esempio seguente viene illustrato come è possono ricevere messaggi e l'utilizzo di trasformati **ricevere\_sottoscrizione\_message()**. Nell'esempio innanzitutto riceve ed elimina un messaggio dalla sottoscrizione "bassa messaggi" utilizzando **: anteprima\_blocco** impostato su **false**, quindi riceve un altro messaggio da "ad alta-messaggi" e quindi Elimina il messaggio utilizzando **eliminare\_sottoscrizione\_message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non è possibile elaborare il messaggio per qualche motivo e quindi possono chiamare il **sbloccare\_sottoscrizione\_message()** metodo sull'oggetto **Azure::ServiceBusService** . In questo modo Bus di servizio sbloccare il messaggio all'interno della sottoscrizione e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della sottoscrizione, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare automaticamente il messaggio e renderlo disponibile per la ricezione di nuovo.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione ma prima che il messaggio di **eliminare\_sottoscrizione\_message()** metodo, quindi il messaggio viene recapitato nuovamente dell'applicazione quando si riavvia. Si tratta spesso **Almeno una volta elaborazione**; vale a dire ogni messaggio verrà elaborato almeno una volta, ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. Questa logica viene spesso eseguita mediante la **messaggio\_id** proprietà del messaggio, rimarrà costante tra tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare gli argomenti e le sottoscrizioni

Gli argomenti e le sottoscrizioni sono permanenti e devono essere eliminate in modo esplicito tramite il [portale di Azure][] o a livello di programmazione. Nell'esempio seguente viene illustrato come eliminare l'argomento relativo alla "argomento di prova".

```
azure_service_bus_service.delete_topic("test-topic")
```

Eliminazione di un argomento elimina anche le sottoscrizioni che sono registrate con l'argomento. Le sottoscrizioni possono anche essere eliminate in modo indipendente. Il codice seguente viene illustrato come eliminare la sottoscrizione denominata "messaggi ad alta" nell'argomento "argomento di prova":

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base degli argomenti relativi a Bus di servizio, seguire questi collegamenti per altre informazioni.

- Vedere [code, argomenti e le sottoscrizioni](service-bus-queues-topics-subscriptions.md).
- Guida di riferimento API per [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Visitare l'archivio di [Azure SDK per trascrizione](https://github.com/Azure/azure-sdk-for-ruby) su GitHub.
 
[Portale di Azure]: https://portal.azure.com
