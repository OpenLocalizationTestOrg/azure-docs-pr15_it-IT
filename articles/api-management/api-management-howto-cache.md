<properties
    pageTitle="Aggiungere la memorizzazione nella cache per migliorare le prestazioni di Azure API Management | Microsoft Azure"
    description="Informazioni su come migliorare la latenza, larghezza di banda e il caricamento di servizio web per le chiamate di servizio di gestione API."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Aggiungere la memorizzazione nella cache per migliorare le prestazioni di gestione delle API di Azure

Operazioni di gestione delle API possono essere configurate per la memorizzazione nella cache di risposta. Cache delle risposte in modo significativo consente di ridurre la latenza API, larghezza di banda e web carico di servizi per i dati che non vengono modificati spesso.

Questa guida viene illustrato come aggiungere la memorizzazione nella cache per l'API di risposta e configurare i criteri per le operazioni di eco API di esempio. È quindi possibile chiamare l'operazione dal portale per sviluppatori per verificare la memorizzazione nella cache in azione.

>[AZURE.NOTE] Per informazioni sulla memorizzazione di elementi da chiave utilizzando espressioni di criteri, vedere [la memorizzazione nella cache personalizzati in Azure API Management](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire la procedura descritta in questa Guida, è necessario disporre un'istanza di servizio di gestione delle API con un'API e configurato un prodotto. Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

## <a name="configure-caching"> </a>Configurare un'operazione per la memorizzazione nella cache

In questo passaggio, potrai controllare le impostazioni della cache dell'operazione di **Ottenere risorse (cache)** del campione API eco.

>[AZURE.NOTE] Ogni istanza del servizio di gestione delle API preconfigurato con un'API eco che può essere utilizzato per sperimentare e informazioni sulla gestione dei API. Per ulteriori informazioni, vedere [Guida introduttiva di Azure API Management][].

Per iniziare, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale di Publisher][api-management-management-console]

Fare clic su **API** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **API eco**.

![API eco][api-management-echo-api]

Fare clic sulla scheda **operazioni** e quindi fare clic su operazione **Risorsa ottenere (cache)** nell'elenco **operazioni** .

![Operazioni API eco][api-management-echo-api-operations]

Fare clic sulla scheda **memorizzazione nella cache** per visualizzare le impostazioni della cache per l'operazione.

![Scheda memorizzazione nella cache][api-management-caching-tab]

Per abilitare la memorizzazione nella cache per un'operazione, selezionare la casella di controllo **Attiva** . In questo esempio, la cache è attivata.

Ogni risposta operazione viene immesso, in base ai valori nei campi **variabile in base a parametri stringa di query** e **variabile in base a intestazioni** . Se si desidera memorizzare nella cache più risposte in base a intestazioni o i parametri di stringa di query, è possibile configurare loro in questi due campi.

**Durata** specifica l'intervallo di scadenza delle risposte memorizzate nella cache. In questo esempio, l'intervallo è **3600** secondi, che equivale a un'ora.

Tramite la configurazione di cache in questo esempio, la prima richiesta per il funzionamento di **Ottenere risorse (cache)** restituisce una risposta dal servizio di back-end. Questa risposta cache, ordinate per le intestazioni e specificate parametri stringa di query. Le successive chiamate per il funzionamento con i parametri corrispondenti, avrà la risposta nella cache restituita finché l'intervallo di durata della cache è scaduto.

## <a name="caching-policies"> </a>Esaminare i criteri di memorizzazione nella cache

In questo passaggio è esaminare le impostazioni della cache per l'operazione di **Ottenere risorse (cache)** del campione API eco.

Quando le impostazioni della cache sono configurate per un'operazione nella scheda **memorizzazione nella cache** , vengono aggiunti i criteri di memorizzazione nella cache per l'operazione. Questi criteri possono essere visualizzati e modificati nell'editor criteri.

Fare clic su **criteri** dal menu **Gestione API** sul lato sinistro e quindi selezionare **eco API / ottenere risorsa (cache)** dall'elenco a discesa **operazione** .

![Operazione di ambito][api-management-operation-dropdown]

Consente di visualizzare i criteri per questa operazione nell'editor criteri.

![Editor Criteri di gestione delle API][api-management-policy-editor]

La definizione di criteri per questa operazione include i criteri che definiscono la configurazione di cache che sono stati esaminati con la scheda **cache** nel passaggio precedente.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Rifletteranno le modifiche apportate ai criteri di memorizzazione nella cache nell'editor criteri nella scheda **memorizzazione nella cache** di un'operazione e viceversa.

## <a name="test-operation"> </a>Un'operazione di chiamata e testare la memorizzazione nella cache

Per visualizzare la memorizzazione nella cache in azione, è possibile chiamare l'operazione dal portale di sviluppo. Fare clic su **portale per sviluppatori** nel menu superiore destro.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore e quindi selezionare **API eco**.

![API eco][api-management-apis-echo-api]

>Se si dispone di un solo API configurata o visibile al proprio account, quindi fare clic su API consente di accedere direttamente alle operazioni di tale API.

Selezionare l'operazione di **Ottenere risorse (cache)** e quindi fare clic su **Apri la Console**.

![Console aperta][api-management-open-console]

Console di consente di richiamare operazioni direttamente dal portale di sviluppo.

![Console][api-management-console]

Mantenere i valori predefiniti per **param1** e **param2**.

Selezionare la chiave desiderata dall'elenco a discesa **chiave di sottoscrizione** . Se l'account ha un solo abbonamento, già essere selezionata.

Immettere **sampleheader:value1** nella casella di testo **le intestazioni di richiesta** .

Fare clic su **HTTP Get** e prendere nota delle intestazioni di risposta.

Immettere **sampleheader:value2** nella casella di testo **le intestazioni di richiesta** e quindi fare clic su **HTTP Get**.

Si noti che il valore di **sampleheader** ancora **val1** nella risposta. Provare alcuni valori diversi e notare che viene restituita la risposta nella cache dalla prima chiamata.

Immettere **25** nel campo **param2** e quindi fare clic su **HTTP Get**.

Si noti che il valore di **sampleheader** nella risposta a questo punto è **val2**. Perché i risultati di un'operazione sono ordinati per stringa di query, la risposta nella cache precedente non è stata restituita.

## <a name="next-steps"> </a>Passaggi successivi

-   Per ulteriori informazioni sui criteri di memorizzazione nella cache, vedere [la memorizzazione nella cache criteri][] nel [riferimento di criteri di gestione delle API][].
-   Per informazioni sulla memorizzazione di elementi da chiave utilizzando espressioni di criteri, vedere [la memorizzazione nella cache personalizzati in Azure API Management](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Guida introduttiva di Azure API Management]: api-management-get-started.md

[Riferimento di criteri di gestione delle API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Criteri di memorizzazione nella cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
