<properties 
    pageTitle="Creare un'API per App logica" 
    description="Creazione di una API personalizzata da utilizzare con le applicazioni di logica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Creazione di una API personalizzata da utilizzare con le applicazioni di logica

Se si vuole estendere la piattaforma App logica, sono disponibili molti modi, che è possibile chiamare API o sistemi che non sono disponibili in uno dei nostro molti connettori della casella.  Uno di questi modi per creare un'App API è possibile chiamare con un flusso di lavoro logica App.

## <a name="helpful-tools"></a>Strumenti utili

Per le API per l'utilizzo con logica App, si consiglia di generazione di un documento [swagger](http://swagger.io) dettaglio le operazioni supportate e i parametri per l'API.  Sono disponibili molte librerie (ad esempio [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) che generano automaticamente swagger dell'utente.  È anche possibile utilizzare [TRex](https://github.com/nihaue/TRex) per annotare swagger che funzionino con App logica (visualizzare i nomi, tipi di proprietà e così via).  Per alcuni esempi di App API progettate per App logica, assicurarsi di estrarre il nostro [archivio GitHub](http://github.com/logicappsio) o nel [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Azioni

Azione di base per un'App di logica è un controller che verrà accettare una richiesta HTTP e restituire una risposta (in genere 200).  Tuttavia sono disponibili diversi modelli che è possibile eseguire per estendere azioni in base alle proprie esigenze.

Per impostazione predefinita il motore di logica App verrà timeout una richiesta dopo 1 minuto.  Tuttavia, si possono disporre l'API eseguire le azioni su che richiedono più tempo, in modo che il motore di attendere il completamento, seguendo un asincrono o webhook motivo descritto di seguito.

Per le azioni standard, è sufficiente scrivere un metodo di richiesta HTTP l'API esposta tramite swagger.  È possibile visualizzare esempi delle App API che funzionano con logica App il nostro [archivio GitHub](https://github.com/logicappsio).  Di seguito sono metodi per eseguire modelli comuni con un connettore personalizzato.

### <a name="long-running-actions---async-pattern"></a>Azioni esecuzione prolungata - modello asincrono

Quando si esegue un passaggio lungo o un'attività, la prima cosa che occorre fare è assicurarsi che il motore sa che ancora stato scaduto. È necessario comunicare con il motore come saprà dopo aver terminato con l'attività e infine, è necessario ripristinare il motore di dati rilevanti per continuare con il flusso di lavoro. È possibile completare che tramite un'API seguendo il flusso riportata di seguito. Questa procedura è il punto di vista dell'API personalizzato:

1. Quando si riceve una richiesta, tornare immediatamente una risposta (prima di lavoro). Questa risposta sarà un `202 ACCEPTED` risposta, indicando il motore ottenuto i dati, accettato payload e ora di elaborazione. La risposta 202 deve contenere le intestazioni delle operazioni seguenti: 
 * `location`intestazione (obbligatorio): si tratta di un assoluto percorso alle App logica URL consente di controllare lo stato del processo.
 * `retry-after`(facoltativo, verranno predefinito su 20 per azioni). Questo è il numero di secondi che il motore di attesa prima di polling l'URL della posizione dell'intestazione per controllare lo stato.

2. Quando è selezionato un stato dei processi, eseguire i controlli seguenti: 
 * Se il processo è stato completato: restituire un `200 OK` risposta con il payload di risposta.
 * Se il processo è in corso l'elaborazione: restituire un altro `202 ACCEPTED` risposta, con le stesse intestazioni risposta iniziale

Questo modello consente di eseguire attività molto lunga all'interno di un thread dell'API personalizzato, ma mantenere attiva una connessione attiva con il motore di logica App in modo che non timeout oppure continuare prima del completamento lavoro. Quando si aggiungono questa in un'applicazione di logica, è importante tenere presente che non occorre fare nulla nella definizione per l'App di logica di continuare a polling e controllare lo stato. Non appena il motore rileva una risposta accettato 202 con un'intestazione di percorso valido, verrà rispettare schema asincrono e continuare a sondaggio intestazione percorso finché non viene restituito un non 202.

È possibile visualizzare un esempio di questo modello di GitHub [qui](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Azioni Webhook

Durante il flusso di lavoro, è possibile utilizzare l'App logica posizionare il puntatore e attendere "callback" continuare.  Questo callback viene fornito in forma di un POST HTTP.  Per implementare questo modello, è necessario fornire due endpoint sul controller: sottoscrivere e annullare la sottoscrizione.

Nel 'sottoscritti' App logica verrà creato e registrare un URL di callback che l'API consente di archiviare e callback con pronto in un POST HTTP.  Qualsiasi contenuto/intestazioni verranno passati App logica e può essere utilizzati nel resto del flusso di lavoro.  Motore di logica App eseguiranno una chiamata il punto di Sottoscrivi in esecuzione come arriva eseguirli.

Se è stata annullata Esegui il motore di logica App effettua una chiamata all'endpoint 'annullare la sottoscrizione'.  L'API può quindi annullare la registrazione l'URL di callback in base alle esigenze.

Attualmente la progettazione di applicazioni logica non supporta scoprono un endpoint webhook tramite swagger, in modo che per l'utilizzo di questo tipo di azione è necessario aggiungere l'azione "Webhook" e specificare l'URL, intestazioni e corpo della convocazione.  È possibile utilizzare il `@listCallbackUrl()` funzione del flusso di lavoro in uno qualsiasi di questi campi in base alle esigenze di passare all'URL callback.

È possibile visualizzare un esempio di un modello di webhook in GitHub [qui](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Trigger

Oltre alle azioni, è possibile impostare il act API personalizzato come trigger per un'App di logica.  Ci sono due motivi, che è possibile seguire sotto per attivare un'App di logica:

### <a name="polling-triggers"></a>Trigger di polling

I trigger di polling agiscono molto simile a quelle lungo asincrono in esecuzione.  Motore di logica App eseguiranno una chiamata endpoint trigger dopo un certo periodo di tempo trascorso (dipendono dal SKU, 15 secondi per Premium, 1 minuto per Standard e 1 ora per gratuito).

Se non è disponibile alcun dato, restituisce il trigger un `202 ACCEPTED` risposta, con un `location` e `retry-after` intestazione.  Tuttavia, per i trigger si consiglia di `location` intestazione contiene un parametro di query di `triggerState`.  Si tratta di un identificatore per l'API utili quando l'ora dell'ultima App logica generato.  Se dati disponibili, il trigger restituisce un `200 OK` risposta con il contenuto payload.  Verrà avviata l'App logica.

Ad esempio se è stato polling per verificare se un file è stato disponibile, è possibile creare un trigger di polling che, eseguire le operazioni seguenti:

* Se è stata ricevuta una richiesta con non triggerState l'API restituirebbe un `202 ACCEPTED` con un `location` intestazione che contiene un triggerState dell'ora corrente e un `retry-after` di 15.
* Se è stata ricevuta una richiesta con un triggerState:
 * Verificare se i file sono stati aggiunti dopo triggerState DateTime. 
  * Se 1 file, restituisce un `200 OK` risposta con il contenuto payload incrementare triggerState a DateTime del file restituito e impostare il `retry-after` a 15.
  * Se sono presenti più file, è possibile tornare 1 alla volta con un `200 OK`, incrementare la triggerState nel `location` intestazione e impostare `retry-after` su 0.  Questa operazione verrà comunicare il motore di altri dati è disponibile e richiederà immediatamente sul `location` intestazione specificata.
  * Se non sono presenti file, restituire un `202 ACCEPTED` risposta e lasciare il `location` triggerState lo stesso.  Impostare `retry-after` a 15.

È possibile visualizzare un esempio di un trigger di polling in GitHub [qui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Trigger Webhook

Trigger Webhook agire molto simile a quello azioni Webhook sopra.  Motore di logica App eseguiranno una chiamata endpoint "sottoscritti" ogni volta che viene aggiunto e salvato un trigger webhook.  L'API consente di registrare l'URL webhook e chiamarlo tramite HTTP POST ogni volta che i dati sono disponibili.  Le intestazioni e contenuto payload verranno passate App logica di esecuzione.

Se un trigger webhook mai è stato eliminato (App logica interamente o solo il trigger webhook), il motore verrà effettuare una chiamata all'URL 'annullare la sottoscrizione' in cui l'API è possibile annullare la registrazione l'URL di callback e arrestare i processi in base alle esigenze.

Attualmente la progettazione di applicazioni logica non supporta scoprono un trigger webhook mediante swagger, in modo che per l'utilizzo di questo tipo di azione è necessario aggiungere il trigger "Webhook" e specificare l'URL, intestazioni e corpo della convocazione.  È possibile utilizzare il `@listCallbackUrl()` funzione del flusso di lavoro in uno qualsiasi di questi campi in base alle esigenze di passare all'URL callback.

È possibile visualizzare un esempio di un trigger webhook in GitHub [qui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)