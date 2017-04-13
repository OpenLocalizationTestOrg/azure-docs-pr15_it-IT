<properties
    pageTitle="Come utilizzare code Bus di servizio con trascrizione | Microsoft Azure"
    description="Informazioni su come usare code Bus di servizio in Azure. Esempi di codice scritti in trascrizione."
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

# <a name="how-to-use-service-bus-queues"></a>Come usare code Bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questa guida viene descritto come utilizzare code Bus di servizio. Gli esempi scritto in trascrizione e utilizzano l'indicatore di Azure. Gli scenari coperti includono **la creazione di code, inviare e ricevere messaggi**e **all'eliminazione di code**. Per ulteriori informazioni sulle code Bus di servizio, vedere la sezione [Passaggi successivi](#next-steps) .

## <a name="what-are-service-bus-queues"></a>Quali sono le code Bus di servizio?

Le code Bus di servizio supportano un modello di comunicazione *gestito messaggistica* . Con le code, componenti di un'applicazione distribuita non comunicano direttamente tra loro. Se, tuttavia scambiati messaggi tramite una coda, che opera a livello intermedio. Un produttore di messaggio (mittente) passa alla coda di un messaggio e quindi continua l'elaborazione.
In modo asincrono, consumer messaggio (ricevitore) recupera il messaggio dalla coda ed elabora. Il produttore non è necessario attendere una risposta dal consumer per continuare a elaborare e inviare altri messaggi. Code offrono il recapito dei messaggi **del primo FIFO (First Out)** a uno o più consumer concorrenti. Ovvero i messaggi vengono in genere ricevuti ed elaborati da ricevitori nell'ordine in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuta ed elaborata da consumer solo un messaggio.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Servizio Bus code sono una tecnologia generica che può essere usata per una vasta gamma di scenari:

-   Comunicazioni tra i ruoli web e di lavoro in un' [applicazione Azure a più livelli](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Comunicazioni tra App locale e Azure ospitato App in una [soluzione ibrida](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Comunicazione tra i componenti di un'applicazione distribuita in esecuzione in locale in diverse organizzazioni o reparti di un'organizzazione.

Utilizzo di code abilitare è possibile distribuire le applicazioni migliori e attivare ulteriori resilienza alla architettura.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi

Per iniziare a utilizzare code Bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione. Poiché il portale di Azure non crea lo spazio dei nomi con una connessione ACS, è necessario creare lo spazio dei nomi tramite l'interfaccia della riga di comando.

Per creare uno spazio dei nomi:

1. Aprire una console di Azure Powershell.

2. Digitare il comando seguente per creare uno spazio dei nomi Bus di servizio. Specificare il valore dello spazio dei nomi e la stessa area dell'applicazione.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Ottenere le credenziali di gestione per lo spazio dei nomi

Per eseguire operazioni di gestione, ad esempio la creazione di una coda nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi.

Il cmdlet di PowerShell che è stato eseguito per creare lo spazio dei nomi di servizio Azure bus Visualizza la chiave che è possibile utilizzare per gestire lo spazio dei nomi. Copiare il valore di **DefaultKey** . Si utilizzerà il valore nel codice più avanti in questa esercitazione.

![Tasto di copia](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] È possibile trovare questo tasto anche se si accede al [portale di Azure](https://portal.azure.com/) e individuare le informazioni di connessione dello spazio dei nomi Bus di servizio.

## <a name="create-a-ruby-application"></a>Creare un'applicazione di trascrizione

Creare un'applicazione trascrizione. Per ulteriori informazioni, vedere [creare un'applicazione di trascrizione in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione di utilizzare Bus di servizio

Per utilizzare Bus di servizio Azure, scaricare e usare il pacchetto di Azure trascrizione, che include un set di raccolte semplicità di utilizzo che comunicare con i servizi di resto dello spazio di archiviazione.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix).

2. Digitare "azure installa indicatore" nella finestra di comando per installare il simbolo e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto

Usare un editor di testo, aggiungere quanto segue nella parte superiore del file trascrizione nel punto in cui si prevede di utilizzare lo spazio di archiviazione:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurare una connessione Bus di servizio Azure

Modulo di Azure legge le variabili di ambiente **AZURE\_SERVICEBUS\_spazio dei nomi** e **AZURE\_SERVICEBUS\_ACCESS_KEY** le informazioni necessarie per la connessione allo spazio dei nomi Bus di servizio. Se non sono tali variabili, è necessario specificare le informazioni dello spazio dei nomi prima di utilizzare **Azure::ServiceBusService** con il codice seguente:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Impostare il valore dello spazio dei nomi per il valore che è stato creato, piuttosto che l'intero URL. Usare, ad esempio, **"yourexamplenamespace"**, non "yourexamplenamespace.servicebus.windows.net".

## <a name="how-to-create-a-queue"></a>Come creare una coda

L'oggetto **Azure::ServiceBusService** consente di utilizzare code. Per creare una coda, utilizzare il metodo **create_queue()** . Nell'esempio seguente viene creata una coda o stampato eventuali errori.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

È anche possibile passare un oggetto **Azure::ServiceBus::Queue** con ulteriori opzioni, che consente di ignorare le impostazioni di coda predefinito, ad esempio durata messaggio o le dimensioni massime coda. Nell'esempio seguente viene illustrato come impostare le dimensioni massime coda 5GB e l'ora a live al minuto:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Come inviare messaggi a una coda

Per inviare un messaggio a una coda di servizio Bus applicazione chiama il **inviare\_coda\_message()** metodo sull'oggetto **Azure::ServiceBusService** . I messaggi inviate a (e ricevute da) code Bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage** e dispongono di un insieme di proprietà standard (ad esempio **etichette** e **ora\_a\_live**), un dizionario che viene utilizzata per contenere proprietà specifiche di applicazione personalizzata e un corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo del messaggio passando un valore stringa del messaggio e proprietà standard richieste verrà popolata con i valori predefiniti.

Nell'esempio seguente viene illustrato come inviare un messaggio di prova alla coda denominata "coda di prova" utilizzando **inviare\_coda\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Code Bus del servizio supporto tecnico una dimensione massima di 256 KB nel [livello Standard](service-bus-premium-messaging.md) e 1 MB nel [livello Premium](service-bus-premium-messaging.md). L'intestazione, che include le proprietà dell'applicazione standard e personalizzati, può avere una dimensione massima di 64 KB. Illimitato al numero di messaggi contenuti in una coda ma c'è un'estremità alla dimensione totale dei messaggi contenute in una coda. Questa dimensione coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Come ricevere i messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il **ricevere\_coda\_message()** metodo sull'oggetto **Azure::ServiceBusService** . Per impostazione predefinita, i messaggi sono leggere e bloccati senza da eliminare dalla coda. Tuttavia, è possibile eliminare i messaggi dalla coda quando vengono letti mediante l'impostazione di **: peek_lock** opzione su **false**.

Il comportamento predefinito consente la lettura e l'eliminazione di un'operazione di due fasi, che consente inoltre supportare le applicazioni che non è possibile tollerare messaggi mancanti. Quando Bus di servizio riceve una richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, che venga completato la seconda fase del processo di ricezione chiamando **eliminare\_coda\_message()** metodo e fornire il messaggio da eliminare come parametro. Il **eliminare\_coda\_message()** metodo contrassegnare il messaggio come utilizzata e rimuoverlo dalla coda.

Se il **: anteprima\_blocco** parametro è impostato su **false**, la lettura, eliminare il messaggio diventa il modello più semplice e adatta per gli scenari in cui un'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché Bus di servizio verrà contrassegnato il messaggio come consumate, quando l'applicazione riavvia e inizia a utilizzare nuovamente i messaggi, essa verrà perse il messaggio che è stato utilizzato prima l'arresto anomalo.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi che utilizzano **ricevere\_coda\_message()**. Nell'esempio innanzitutto riceve ed elimina un messaggio usando **: anteprima\_blocco** impostato su **false**, quindi riceve un altro messaggio e quindi Elimina il messaggio utilizzando **eliminare\_coda\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire un blocco delle applicazioni e i messaggi non leggibili

Servizio Bus fornisce funzionalità che ne faciliti normalmente correggere gli errori nell'applicazione o difficoltà elaborazione di un messaggio. Se un'applicazione del destinatario non è possibile elaborare il messaggio per qualche motivo e quindi possono chiamare il **sbloccare\_coda\_message()** metodo sull'oggetto **Azure::ServiceBusService** . In questo modo Bus di servizio sbloccare il messaggio all'interno della coda e renderlo disponibile per la ricezione di nuovo dalla stessa applicazione di richiedere o da un'altra applicazione di tempo.

È inoltre disponibile un timeout associato a un messaggio protetto all'interno della coda, e se l'applicazione ha esito negativo per l'elaborazione dei messaggi prima il timeout di blocco scadenza (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio sblocca automaticamente il messaggio e rende disponibile al ricevuto nuovamente.

Nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione ma prima che il messaggio di **eliminare\_coda\_message()** metodo, quindi il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Si tratta spesso **Almeno una volta elaborazione**; vale a dire ogni messaggio viene elaborato almeno una volta, ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, gli sviluppatori di applicazioni sia necessario aggiungere logica aggiuntiva alla domanda per gestire il recapito dei messaggi duplicati. Questa operazione spesso viene eseguita mediante la **messaggio\_id** proprietà del messaggio, rimane costante tra tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di code Bus di servizio, seguire questi collegamenti per altre informazioni.

-   Panoramica di [code, argomenti e le sottoscrizioni](service-bus-queues-topics-subscriptions.md).
-   Visitare l'archivio di [Azure SDK per trascrizione](https://github.com/Azure/azure-sdk-for-ruby) su GitHub.

Per un confronto tra le code Bus di servizio Azure descritto in questo articolo e code Azure descritta in questo articolo [come usare lo spazio di archiviazione coda da trascrizione](../storage/storage-ruby-how-to-use-queue-storage.md) , vedere [code Azure e Azure servizio Bus code - Compared e Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
