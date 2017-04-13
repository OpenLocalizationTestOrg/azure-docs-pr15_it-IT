<properties 
    pageTitle="Come creare e pubblicare un prodotto in Azure API Management" 
    description="Informazioni su come creare e pubblicare i prodotti in Azure API Management." 
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

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Come creare e pubblicare un prodotto in Azure API Management

In Gestione API di Azure, un prodotto contiene uno o più API, nonché una quota di utilizzo e le condizioni di utilizzo. Dopo aver pubblicato un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a utilizzare le API del prodotto. L'argomento fornisce una Guida per la creazione di un prodotto, aggiungendo un'API e la pubblicazione per gli sviluppatori.

## <a name="create-product"> </a>Creare un prodotto

Operazioni sono aggiunto e configurate per un'API nel portale di publisher. Per accedere al portale di publisher, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Fare clic su **prodotti** nel menu a sinistra per visualizzare la pagina di **prodotti** e fare clic su **Aggiungi prodotto**.

![Prodotti][api-management-products]

![Nuovo prodotto][api-management-add-new-product]

Immettere un nome descrittivo per il prodotto nel campo **nome** e una descrizione del prodotto nel campo **Descrizione** .

I prodotti in Gestione API possono essere **aperto** o **protetta**. Prodotti protetti necessario sottoscritto prima che possono essere utilizzati, anche se aperto prodotti possono essere utilizzati senza un abbonamento. Selezionare **Richiedi abbonamento** per creare un prodotto protetto che richiede una sottoscrizione. Questo è l'impostazione predefinita.

Controllare **che richiedono l'approvazione di sottoscrizione** se si desidera un amministratore di esaminare e accettare o rifiutare tentativi di abbonamento a questo prodotto. Se la casella è deselezionata, tentativi di abbonamento sarà approvato automaticamente. Per ulteriori informazioni sugli abbonamenti, vedere [gli abbonati a visualizzazione di un prodotto][].

Per consentire agli account di sviluppo di sottoscrivere più volte il prodotto, selezionare la casella di controllo **Consenti più abbonamenti** . Se questa casella non è selezionata, ogni account sviluppatore è possibile effettuare la sottoscrizione di una sola volta sul prodotto.

![Illimitato più abbonamenti][api-management-unlimited-multiple-subscriptions]

Per limitare il numero di più abbonamenti contemporaneamente, selezionare la casella di controllo **Limita il numero di sottoscrivere simultanei** e immettere il limite di sottoscrizione. Nell'esempio seguente, le sottoscrizioni simultanee sono limitate a quattro per conto di sviluppo.

![Quattro più abbonamenti][api-management-four-multiple-subscriptions]

Dopo aver configurate tutte le nuove opzioni di prodotto, fare clic su **Salva** per creare il nuovo prodotto.

![Prodotti][api-management-products-page]

>Per impostazione predefinita nuovi prodotti non pubblicati e sono visibili solo al gruppo **Administrators** .

Per configurare un prodotto, fare clic sul nome del prodotto nella scheda **prodotti** .

## <a name="add-apis"> </a>API aggiungere a un prodotto

La pagina **prodotti** include quattro collegamenti per una configurazione: **Riepilogo**, **Impostazioni**, **visibilità**e **sottoscrizione**. La scheda **Riepilogo** è nel punto in cui è possibile aggiungere API e pubblicare o annullare la pubblicazione di un prodotto.

![Riepilogo][api-management-new-product-summary]

Prima di pubblicare il prodotto è necessario aggiungere uno o più API. A tale scopo, fare clic su **Aggiungi API al prodotto**.

![Aggiungere API][api-management-add-apis-to-product]

Selezionare le API desiderate e fare clic su **Salva**.

## <a name="add-description"> </a>è possibile aggiungere informazioni a un prodotto

Scheda **Impostazioni** consente di fornire informazioni dettagliate sul prodotto, ad esempio allo scopo, API consente l'accesso a e altre informazioni utili. Il contenuto è riservato agli sviluppatori che verranno utilizzato l'API e possono essere scritte in testo normale o tag HTML.

![Impostazioni del prodotto][api-management-product-settings]

Selezionare **Richiedi abbonamento** per creare un prodotto protetto che richiede una sottoscrizione da utilizzare o deselezionare la casella di controllo per creare un prodotto aperto che può essere chiamato senza un abbonamento.

Se si desidera approvare manualmente tutte le richieste di sottoscrizione di prodotto, selezionare **richiedere l'approvazione dell'abbonamento** . Per impostazione predefinita vengono concessi automaticamente tutte le sottoscrizioni di prodotto.

Per consentire agli account di sviluppo di sottoscrivere più volte il prodotto, selezionare la casella di controllo **Consenti più abbonamenti** e se si desidera specificare un limite. Se questa casella non è selezionata, ogni account sviluppatore è possibile effettuare la sottoscrizione di una sola volta sul prodotto.

Se lo si desidera compilare il campo di **condizioni di utilizzo** che descrive le condizioni di utilizzo per il prodotto quali utenti è necessario accettare per utilizzare il prodotto.

## <a name="publish-product"> </a>Pubblicare un prodotto

Prima di possono chiamare le API in un prodotto, è necessario pubblicare il prodotto. Nella scheda **Riepilogo** per il prodotto, fare clic su **pubblica**e quindi fare clic su **Sì, pubblicarlo** per confermare. Per rendere privato un prodotto pubblicato in precedenza, fare clic su **Annulla pubblicazione**.

![Pubblicare prodotto][api-management-publish-product]

## <a name="make-visible"> </a>Rendere visibili agli sviluppatori di un prodotto

La scheda **visibilità** consente di scegliere i ruoli sono in grado di vedere il prodotto nel portale di sviluppo e sottoscrivere il prodotto.

![Visibilità prodotto][api-management-product-visiblity]

Per attivare o disattivare la visibilità di un prodotto per gli sviluppatori in un gruppo, selezionare o deselezionare la casella di controllo accanto al gruppo e quindi fare clic su **Salva**.

>Per ulteriori informazioni, vedere [come creare e utilizzare gruppi per gestire gli account di sviluppo in Azure API Management][].

## <a name="view-subscribers"> </a>Consente di visualizzare gli abbonati a un prodotto

La scheda **gli abbonati a** Elenca gli sviluppatori che hanno sottoscritto il prodotto. I dettagli e impostazioni per ogni sviluppatore che possono essere visualizzate facendo clic sul nome dello sviluppatore. In questo esempio gli sviluppatori di non aver sottoscritto ancora il prodotto.

![Sviluppatori][api-management-developer-list]

## <a name="next-steps"> </a>Passaggi successivi

Una volta aggiunti API desiderate e il prodotto è stato pubblicato, gli sviluppatori possono sottoscrivere il prodotto e iniziare a chiamare le API. Per un'esercitazione che illustra che questi elementi, nonché la configurazione avanzata prodotto illustra [come creare e configurare impostazioni avanzate del prodotto nella gestione di API di Azure][].

Per ulteriori informazioni sull'uso di prodotti, vedere il video seguente.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Abbonati a visualizzazione di un prodotto]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Come creare e utilizzare i gruppi per gestire gli account di sviluppo in Azure API Management]: api-management-howto-create-groups.md
[Come creare e configurare impostazioni avanzate del prodotto in Azure API Management]: api-management-howto-product-with-rules.md 