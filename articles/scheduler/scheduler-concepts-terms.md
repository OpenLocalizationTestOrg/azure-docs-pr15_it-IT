<properties
 pageTitle="Concetti di utilità di pianificazione, le condizioni ed entità | Microsoft Azure"
 description="Azure utilità di pianificazione fondamentali, terminologia e gerarchia di entità, inclusi i processi e le raccolte di processo.  Viene illustrato un esempio completo di un processo pianificato."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Concetti di utilità di pianificazione, terminologia + gerarchia entità

## <a name="scheduler-entity-hierarchy"></a>Gerarchia di entità utilità di pianificazione

Nella tabella seguente vengono descritte le risorse principale esposte o utilizzate dall'API utilità di pianificazione:

|Risorsa | Descrizione |
|---|---|
|**Insieme di processo**|Un insieme di lavoro contiene un gruppo di processi e conserva le impostazioni, le quote e limita condivisi dai processi all'interno della raccolta. Un insieme di processo creato da un proprietario della sottoscrizione e gruppi di processi in base ai limiti di utilizzo o dell'applicazione. È vincolato a un'area. Consente inoltre l'applicazione delle quote per limitare l'uso di tutti i processi di tale raccolta. Le quote includono MaxJobs e MaxRecurrence.|
|**Processo**|Un processo definisce una singola azione ricorrente con semplice o complesse strategie per l'esecuzione. Azioni possono includere HTTP, coda di spazio di archiviazione, coda bus del servizio o le richieste di servizi bus argomento.|
|**Cronologia dei processi**|La cronologia dei processi rappresenta i dettagli per un'esecuzione di un processo. La presentazione contiene successo e errore, nonché i dettagli di risposta.|

## <a name="scheduler-entity-management"></a>Gestione di entità Scheduler

Un alto livello, l'utilità di pianificazione e la gestione dei servizi API esporre le seguenti operazioni sulle risorse:

|Funzionalità|Descrizione e URI indirizzo|
|---|---|
|**Gestione del processo insieme**|OTTENERE, spostare ed eliminare il supporto per la creazione e la modifica delle raccolte di processo e processi in esso contenuti. Un insieme di processo è un contenitore di processi e mappe alle quote e impostazioni condivise. Esempi di quote, descritte più avanti sono numero massimo di processi e più piccolo intervallo di ricorrenza. <p>SPOSTARE ed eliminare:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>Ottieni:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Gestione dei processi**|OTTENERE, spostare, registrare, PATCH ed eliminare il supporto per la creazione e modifica di processi. Tutti i processi devono appartenere a un insieme di processo che esiste già, quindi non esiste alcun creazione implicita. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Gestione della cronologia del processo**|È possibile ottenere supporto per il recupero di 60 giorni della cronologia di esecuzione di processi, ad esempio il tempo trascorso processo e risultati dell'esecuzione processo. Aggiunge il supporto di parametro stringa query per filtrare in base a stato e lo stato. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Tipi di processo

Sono disponibili più tipi di processi: processi HTTP (inclusi i processi HTTPS che supportano SSL), lo spazio di archiviazione coda processi, processi bus di servizio e processi argomento bus del servizio. Processi HTTP sono ideali se si dispone di un punto finale di un carico di lavoro o un servizio esistente. È possibile utilizzare processi di spazio di archiviazione per inviare messaggi a code di spazio di archiviazione, in modo che i processi sono ideali per carichi di lavoro che utilizzano le code di spazio di archiviazione. Analogamente, processi del servizio bus sono ideali per carichi di lavoro che utilizza gli argomenti e servizio bus code.

## <a name="the-job-entity-in-detail"></a>L'entità "processo" in modo dettagliato

Livello di base, un processo pianificato è diverse parti:

- L'azione da eseguire quando viene generato il timer di processo  

- (Facoltativo) Il tempo necessario per eseguire il processo  

- (Facoltativo) Quando e la frequenza con cui ripetere il processo  

- (Facoltativo) Un'azione per generare se l'azione principale non riesce  

Internamente, un processo pianificato contiene anche dati forniti dal sistema, ad esempio il tempo di esecuzione pianificato successivo.

Il codice seguente viene fornito un esempio completo di un processo pianificato. Nelle sezioni successive vengono fornite informazioni dettagliate.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Come illustrato nell'esempio programmata sopra, una definizione di processo è diverse parti:

- Ora di inizio ("Start")  

- Azione (""), che include azione errore ("errorAction")

- Ricorrenza ("ricorrenza")  

- Stato ("")  

- Stato ("status")  

- Riprovare criteri ("retryPolicy")  

Esaminiamo ciascuno di essi in modo dettagliato:

## <a name="starttime"></a>ora di inizio

Ora di inizio"" è l'ora di inizio e il chiamante può specificare un fuso orario in transito nel [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>azione ed errorAction

"Azioni" sono l'azione richiamato su ogni occorrenza e descrive un tipo di chiamata del servizio. L'azione è cosa verrà eseguito in base alla pianificazione fornito. Utilità di pianificazione supporta HTTP, coda di spazio di archiviazione, argomento bus di servizio e servizio bus coda azioni.

L'azione in questo esempio è un'azione HTTP. Di seguito è illustrato un esempio di un'azione coda lo spazio di archiviazione:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Di seguito è illustrato un esempio di un'azione argomento bus di servizio.

  "azioni": {"tipo": "serviceBusTopic", "serviceBusTopicMessage": {"topicPath": "t1"  
      "spazio dei nomi": "mySBNamespace", "transportType": "netMessaging" / / può essere netMessaging o AMQP "autenticazione": {"sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "messaggio": "Alcune messaggio", "brokeredMessageProperties": {}, "customMessageProperties": {"nomeapplicazione": "FromScheduler"}},}

Di seguito è illustrato un esempio di un'azione di coda bus del servizio:


  "azioni": {"serviceBusQueueMessage": {"coda": "T1"  
      "spazio dei nomi": "mySBNamespace", "transportType": "netMessaging" / / può essere netMessaging o AMQP "autenticazione": {  
        "sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "messaggio": "Alcune messaggio"  
      "brokeredMessageProperties": {}, "customMessageProperties": {"nomeapplicazione": "FromScheduler"}}, "tipo": "serviceBusQueue"}

"errorAction" è il gestore di errore, l'azione richiamato quando l'azione principale ha esito negativo. È possibile utilizzare questa variabile per chiamare un endpoint di gestione degli errori o invia una notifica utente. Questa operazione può essere usata per raggiungere un endpoint secondario nel caso che primario non sono disponibile (ad esempio, nel caso di emergenza nel sito dell'endpoint) o può essere usata per la notifica di un endpoint di gestione degli errori. Come l'azione principale, l'azione errore può essere semplice o composta logica in base alle altre azioni. Per informazioni su come creare un token di associazioni di protezione, fare riferimento a [creare e utilizzare una firma di Access condiviso](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>ricorrenza

Ricorrenza è diverse parti:

- Frequenza: Uno dei minuti, ore, giorno, settimana, mese, anno  

- Intervallo: Intervallo frequenza specificata per la ricorrenza  

- Programmazione previsti: specificare minuti, ore, giorni, mesi e monthdays della ricorrenza  

- Conteggio: Numero di occorrenze  

- Ora di fine: nessun processo verrà eseguito dopo l'ora di fine specificato  

Un processo è ricorrente se dispone di un oggetto ricorrente specificato nella definizione JSON. Se sono specificati conteggio e ora fine, la regola di completamento che si verifichi prima è valido.

## <a name="state"></a>stato

Lo stato del processo corrisponde a uno dei quattro valori: abilitato, disabilitato, completata o errori. È possibile inserire o PATCH processi in modo da aggiornarli allo stato attivato o disattivato. Se un processo è stato completato o contiene un errore, ovvero uno stato finale non può essere aggiornato (anche se il processo è comunque possibile eliminare). Un esempio della proprietà stato è come indicato di seguito:


        "state": "disabled", // enabled, disabled, completed, or faulted
Processi di errore e completati vengono eliminati dopo 60 giorni.

## <a name="status"></a>stato

Una volta avviato un processo di pianificazione, verranno restituite informazioni sullo stato corrente del processo. L'oggetto non è possibile impostare dall'utente, ovvero è impostato dal sistema. Tuttavia, è incluso nell'oggetto processo (piuttosto che una risorsa collegata separata) in modo che uno può ottenere lo stato di un processo semplice.

Lo stato dei processi include l'ora dell'esecuzione precedente (se presente), l'ora dell'esecuzione programmata successiva (per i processi in corso) e il conteggio di esecuzione del processo.

## <a name="retrypolicy"></a>retryPolicy

Se un processo di pianificazione non riesce, è possibile specificare un criterio di tentativi per determinare se e come l'azione viene ripetuta. Ciò è dovuto l'oggetto **retryType** , ovvero è impostato su **Nessuno** se alcun criterio Riprova, come illustrato sopra. Impostare su **fisse** se esiste un criterio di tentativi.

Per impostare un criterio di tentativi, è possibile specificare impostazioni aggiuntive due: un intervallo di tentativi (**retryInterval**) e il numero di tentativi (**retryCount**).

L'intervallo tentativi specificato con l'oggetto **retryInterval** è l'intervallo tra tentativi. Il valore predefinito è 30 secondi, il relativo valore configurabile minimo è 15 secondi e il valore massimo è 18 mesi. Processi in raccolte processo gratuito dispone di un valore minimo configurabile di 1 ora.  Viene definita nel formato ISO 8601. Analogamente, il valore del numero di tentativi è specificato con l'oggetto **retryCount** . è il numero di volte in cui che un tentativo viene eseguito. Valore predefinito è 4 e il valore massimo è 20\. entrambi **retryInterval** e **retryCount** sono facoltativi. Sono fornite valori predefiniti valori se **retryType** è impostato su **fisse** e non vengono specificati in modo esplicito.

## <a name="see-also"></a>Vedere anche

 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Modalità di compilazione di pianificazioni complesse e ricorrenza avanzata con utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a Azure dei cmdlet di PowerShell utilità di pianificazione](scheduler-powershell-reference.md)

 [Azure utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Limiti di Azure utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)

 [Azure autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)
