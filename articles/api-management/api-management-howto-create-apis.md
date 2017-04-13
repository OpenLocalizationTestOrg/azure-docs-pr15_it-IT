<properties 
    pageTitle="Come creare API in Azure API Management" 
    description="Informazioni su come creare e configurare API in Azure API Management." 
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

# <a name="how-to-create-apis-in-azure-api-management"></a>Come creare API in Azure API Management

Un'API in Gestione API rappresenta un insieme di operazioni che è possibile richiamare dalle applicazioni client. Nuove API vengono create nel portale di publisher e aggiungere le operazioni desiderate. Dopo avere aggiungono le operazioni, l'API viene aggiunto a un prodotto e può essere pubblicato. Dopo aver pubblicata una API, può essere sottoscritto e utilizzato dagli sviluppatori.

Questa guida viene visualizzato il primo passaggio del processo: come creare e configurare una nuova API in Gestione API. Per ulteriori informazioni sull'aggiunta di operazioni e la pubblicazione di un prodotto, vedere [come aggiungere operazioni a un'API][] e su [come creare e pubblicare un prodotto][].

## <a name="create-new-api"> </a>Creare una nuova API

API sono create e configurate nel portale di publisher. Per accedere al portale di publisher, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Fare clic su **API** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Aggiungi API**.

![Creare API][api-management-create-api]

Utilizzare la finestra **Aggiungi nuova API** per configurare la nuova API.

![Aggiungi nuova API][api-management-add-new-api]

I campi seguenti vengono utilizzati per configurare la nuova API.

-   **Nome dell'API Web** fornisce un nome univoco e descrittivo per l'API. Se si portali sviluppo e publisher.
-   **URL del servizio Web** fa riferimento il servizio HTTP l'implementazione dell'API. Gestione dell'API inoltra le richieste a questo indirizzo.
-   **URL dell'API Web suffisso** viene aggiunto all'URL di base per il servizio di gestione dell'API. La base URL è comuni per tutte le API ospitate da un'istanza di servizio di gestione delle API. Gestione dell'API distingue API dal loro suffisso e pertanto il suffisso deve essere univoco per ogni API per un determinato autore.
-   **Schema URL API Web** determina quali protocolli possono essere utilizzati per accedere all'API. HTTPs viene specificato per impostazione predefinita.
-   Per aggiungere facoltativamente questa nuova API a un prodotto, fare clic su **prodotti (facoltativi)** elenco a discesa e scegliere un prodotto. Questo passaggio può essere ripetuto più volte per aggiungere l'API a più prodotti.

Una volta configurati i valori desiderati, fare clic su **Salva**. Dopo aver creata la nuova API, verrà visualizzata la pagina di riepilogo per l'API nel portale di publisher.

![Riepilogo API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Le impostazioni di configurazione API

È possibile utilizzare la scheda **Impostazioni** verificare e modificare la configurazione per un'API. **Nome dell'API Web**, **URL del servizio Web**e **suffisso URL API Web** verranno inizialmente impostate quando l'API verrà creato e possono essere modificata. **Descrizione** fornisce una descrizione facoltativa e **schema URL API Web** determina quali protocolli possono essere utilizzati per accedere all'API.

![Impostazione delle API][api-management-api-settings]

Per configurare l'autenticazione di gateway per il servizio di back-end l'implementazione dell'API, selezionare la scheda **sicurezza** . **Con le credenziali** dell'elenco a discesa può essere utilizzato per configurare l'autenticazione di **base HTTP** o **certificati Client** . Per utilizzare l'autenticazione di base HTTP, è sufficiente immettere le credenziali desiderate. Per informazioni sull'uso di autenticazione certificato client, vedere [come proteggere i servizi di back-end mediante l'autenticazione certificato client in Azure API Management][].

Sulla scheda **sicurezza** è utilizzabile anche per configurare le **autorizzazioni utente** tramite OAuth 2.0. Per ulteriori informazioni, vedere [come consentire agli account di sviluppo utilizzando OAuth 2.0 in Azure API Management][].

![Impostazioni di autenticazione di base][api-management-api-settings-credentials]

Fare clic su **Salva** per salvare le modifiche apportate alle impostazioni API.

## <a name="next-steps"> </a>Passaggi successivi

Quando viene creata una API e le impostazioni di configurazione, effettuare le seguenti per aggiungere le operazioni all'API, aggiungere l'API a un prodotto e pubblicarlo in modo che sia disponibile per gli sviluppatori. Per ulteriori informazioni, vedere gli articoli seguenti.

-   [Come aggiungere operazioni a un'API][]
-   [Come creare e pubblicare un prodotto][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md

[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Come proteggere i servizi di back-end tramite client autenticazione certificato in Azure API Management]: api-management-howto-mutual-certificates.md
[Come consentire agli account di sviluppo utilizzando OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md