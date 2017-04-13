<properties 
    pageTitle="Come aggiungere operazioni a un'API in Azure API Management | Microsoft Azure" 
    description="Informazioni su come aggiungere operazioni a un'API in Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Come aggiungere operazioni a un'API di gestione delle API di Azure

Prima di poter utilizzare un'API di gestione delle API, è necessario aggiungere operazioni. Questa guida viene illustrato come aggiungere e configurare i diversi tipi di operazioni a un'API in Gestione API.

## <a name="add-operation"> </a>Aggiungere un'operazione

Operazioni sono aggiunto e configurate per un'API nel portale di publisher. Per accedere al portale di publisher, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Selezionare l'API desiderato nel portale di publisher e quindi selezionare la scheda **operazioni** . 

![Operazioni][api-management-operations]

Fare clic su **Aggiungi operazione** per aggiungere una nuova operazione. **Operazione nuova** verrà visualizzata e la scheda **firma** verrà selezionata per impostazione predefinita.

![Operazione di aggiunta][api-management-add-operation]

Specificare il **verbo HTTP** , selezionare un'opzione nell'elenco a discesa.

![Metodo HTTP][api-management-http-method]

<a name="url-template"></a>

Definire il modello di URL digitando un frammento di URL composta da uno o più segmenti di percorso URL e zero o più parametri di stringa di query. Il modello di URL, insieme all'URL di base dell'API, identifica una singola operazione HTTP. Può contenere uno o più denominati parti variabili identificato le parentesi graffe. Queste parti variabili vengono chiamate i parametri del modello e in modo dinamico vengono assegnate valori estratti da URL della richiesta durante l'elaborazione della richiesta della piattaforma API gestione.

![Modello di URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Se lo si desidera, specificare il **modello di riscrittura URL**. In questo modo è possibile utilizzare il modello di URL standard per elaborare le richieste in arrivo sul front-end, durante la chiamata back-end tramite un URL convertito in base al modello di riscrittura. I parametri del modello dal modello di URL da utilizzare nel modello di riscrittura. Nell'esempio seguente mostra il tipo di contenuto come codificato come tracciato nel servizio web dell'esempio precedente può essere fornito come un parametro di query dell'API pubblicate tramite la piattaforma di gestione dell'API utilizzando i modelli di URL.

![Riscrittura modello URL][api-management-url-template-rewrite]

I chiamanti per l'operazione vengono utilizzato il formato `/customers?customerid=ALFKI` e verrà eseguire il mapping a `/Customers('ALFKI')` quando si richiama il servizio di back-end.


**Il** nome e **Descrizione** fornire una descrizione dell'operazione e vengono utilizzati per fornire la documentazione per sviluppatori che utilizzano questa API nel portale per sviluppatori.

![Descrizione][api-management-description]

Descrizione dell'operazione può essere specificata come testo normale o HTML nella casella di testo **Descrizione** .

## <a name="operation-caching"> </a>Operazione di memorizzazione nella cache

Cache delle risposte riduce la latenza percepita dal consumatori API, larghezza di banda favorisce e delle riduzioni delle tariffe il carico sul web HTTP service implementazione dell'API. 

Per semplice e rapido abilitare la memorizzazione nella cache per l'operazione, selezionare la scheda di **memorizzazione nella cache** e selezionare **la casella di controllo** .

![La memorizzazione nella cache][api-management-caching-tab]

**Durata** specifica il periodo di tempo durante il quale risposta dell'operazione rimane nella cache. Il valore predefinito è 3.600 secondi o 1 ora.

Chiavi della cache vengono utilizzate per distinguere tra le risposte in modo che la risposta corrispondente a ogni chiave diversa per la cache otterrà valore memorizzati nella cache separata. Facoltativamente, immettere i parametri di stringa di query specifica e/o intestazioni HTTP da utilizzare nel calcolo rispettivamente i valori di chiave cache nelle caselle di testo **variabile in base a parametri stringa di query** e **variabile in base a intestazioni** . Quando non verrà specificato, completo richiesta URL e i valori di intestazione HTTP seguenti vengono utilizzati la generazione delle chiavi cache: **accetta** e **Accetta set di caratteri**.

>Per ulteriori informazioni sulla memorizzazione nella cache e la memorizzazione nella cache dei criteri, vedere [come risultati di un'operazione di cache in Azure API Management][].


## <a name="request-parameters"> </a>Richiesta parametri

Parametri dell'operazione vengono gestiti nella scheda parametri. I parametri specificati in **URL modello** nella scheda **firma** di vengono automaticamente aggiunti e possono essere modificati solo modificando il modello di URL. Altri parametri possono essere immesse manualmente.

Per aggiungere un nuovo parametro di query, fare clic su **Aggiungi parametro di Query** e immettere le informazioni seguenti:

-   **Nome** - nome parametro.
-   **Descrizione** - una breve descrizione del parametro (facoltativo).
-   **Tipo** - tipo di parametro selezionato nell'elenco a discesa verso il basso.
-   **Valori** - valori che possono essere assegnati a questo parametro. Uno dei valori può essere contrassegnato come impostazione predefinita (facoltativo).
-   **Obbligatorio** - rendere obbligatorio il parametro selezionando la casella di controllo. 

![Parametri della richiesta][api-management-request-parameters]

## <a name="request-body"> </a>Corpo richiesta

Se l'operazione consente (ad esempio, inserire, POST) e richiede un corpo è possibile fornire un esempio di essa in tutti i formati supportati rappresentazione (ad esempio, json, XML). 

>Nel corpo della richiesta viene utilizzato solo ai fini della documentazione e non viene convalidato.

Per immettere un corpo della richiesta, passare alla scheda **corpo** .

Fare clic su **Aggiungi rappresentazione**, iniziare a digitare il nome di tipo di contenuto desiderato (ad esempio applicazione/json), selezionarlo nell'elenco a discesa e incollare l'esempio di corpo richiesta desiderata nel formato selezionato nella casella di testo. 

![Corpo della richiesta][api-management-request-body]

In altre rappresentazioni, è inoltre possibile specificare una descrizione facoltativa nella casella di testo **Descrizione** .

## <a name="responses"> </a>Le risposte

È buona norma forniti esempi di risposte per tutti i codici di stato che l'operazione può generare. Ogni codice di stato potrebbe essere più di un esempio corpo di risposta, uno per ciascuno dei tipi di contenuto supportati. 

Per aggiungere una risposta, fare clic su **Aggiungi** e iniziare a digitare il codice di stato desiderato. In questo esempio è il codice di stato **200 OK**. Dopo il codice viene visualizzato nell'elenco a discesa, selezionarlo e il codice di risposta viene aggiunta l'operazione.

![Codice di risposta][api-management-response-code]

Fare clic su **Aggiungi rappresentazione**, iniziare a digitare il nome del tipo di contenuto desiderato (ad esempio applicazione/json) e quindi selezionarlo nell'elenco a discesa verso il basso.

![Tipo di contenuto del corpo][api-management-response-body-content-type]

Incollare l'esempio corpo risposta nel formato selezionato nella casella di testo. 

![Risposta][api-management-response-body]

Se lo si desidera, aggiungere una descrizione facoltativa nella casella di testo **Descrizione** .

Dopo aver configurato l'operazione, fare clic su **Salva**.


## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato le operazioni vengono aggiunte a un'API, il passaggio successivo consiste per associare l'API di un prodotto e pubblicarla in modo che gli sviluppatori possono chiamare le operazioni.

-   [Come creare e pubblicare un prodotto][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md
[Come risultati di un'operazione di cache in Azure API Management]: api-management-howto-cache.md