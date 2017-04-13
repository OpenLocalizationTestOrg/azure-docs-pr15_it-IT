<properties 
    pageTitle="Concetti di gestione delle API" 
    description="Informazioni sulle API, prodotti, ruoli, gruppi e altri concetti chiave dell'API gestione." 
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

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Come importare la definizione di un'API con le operazioni di gestione delle API di Azure

Gestione delle API, è possono creare nuove API e le aggiunte manualmente o l'API possono essere importati insieme operazioni in un unico passaggio.

API e le relative operazioni possono essere importate nei formati seguenti.

-   WADL
-   Swagger

Questa guida viene illustrato come creare una nuova API e importare le operazioni in un unico passaggio. Per informazioni su come creare manualmente un'API e aggiunta di operazioni, vedere [come creare API][] e [su come aggiungere operazioni a un'API][].

## <a name="import-api"> </a>Importare un'API

API sono create e configurate nel portale di publisher. Per accedere al portale di publisher, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

![Portale di Publisher][api-management-management-console]

Fare clic su **API** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Importa API**.

![API di importazione][api-management-import-apis]

Nella finestra **Importazione API** sono tre schede che corrispondono a tre modi per fornire le specifiche API.

-   **Dagli Appunti** consente di incollare specifica API nella casella di testo definito.
-   **Da file** consente di individuare e selezionare il file che contiene la specifica dell'API.
-   **Da URL** , è possibile fornire l'URL per le specifiche per l'API.

![Formato di importazione API][api-management-import-api-clipboard]

Dopo avere fornito specifica API, utilizzare i pulsanti di opzione sulla destra per indicare il formato specifica. Sono supportati i formati seguenti.

-   WADL
-   Swagger

Quindi, immettere un **suffisso URL API Web**. Viene aggiunto l'URL di base per il servizio di gestione dell'API. La base URL è comuni per tutte le API ospitate in ogni istanza di un servizio di gestione delle API. Gestione dell'API distingue API dal loro suffisso e pertanto il suffisso deve essere univoco per ogni API in un'istanza del servizio di gestione dell'API specifica.

Una volta tutti i valori, fare clic su **Salva** per creare l'API e operazioni associate. 

>[AZURE.NOTE] Per un'esercitazione come importare un calcolo di base API in formato Swagger, vedere [gestire la prima API Azure API Management](api-management-get-started.md).

## <a name="export-api"></a> Un'API di esportazione

Oltre a importare nuove API, è possibile esportare le definizioni dell'API dal portale di publisher. A tale scopo, fare clic su **Esporta API** dalla **scheda Riepilogo** **API**.

![API di esportazione][api-management-export-api]

API possono essere esportate con WADL o Swagger. Selezionare il formato desiderato, fare clic su **Salva**e scegliere la posizione in cui salvare il file.

![Formato dell'API di esportazione][api-management-export-api-format]

## <a name="next-steps"> </a>Passaggi successivi

Una volta che viene creata una API e le operazioni di importazione, è possibile esaminare e configurare ulteriori impostazioni, aggiungere l'API a un prodotto e pubblicarlo in modo che sia disponibile per gli sviluppatori. Per ulteriori informazioni, vedere le seguenti guide.

-   [Come configurare le impostazioni di API][]
-   [Come creare e pubblicare un prodotto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance

[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md
[Come creare API]: api-management-howto-create-apis.md
[Come configurare le impostazioni di API]: api-management-howto-create-apis.md#configure-api-settings
