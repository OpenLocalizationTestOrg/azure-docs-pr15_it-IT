<properties
    pageTitle="Proteggere l'API Gestione API Azure | Microsoft Azure"
    description="Informazioni su come proteggere l'API con quote e limitazioni criteri (limitazione della velocità)."
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

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteggere l'API con limiti di velocità con Azure API Management

Questa guida illustra quanto sia semplice per aggiungere la protezione per il back-end API configurando i criteri di limite e le quote di velocità con Azure API Management.

In questa esercitazione si creerà un prodotto "Versione di valutazione gratuita" API che consente agli sviluppatori per le chiamate fino a 10 al minuto e fino a un massimo di 200 chiamate alla settimana per l'API utilizzando il [tasso di chiamata limite per un abbonamento](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e criteri di [quota di utilizzo Set per abbonamento](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) . Verranno quindi l'API di pubblicazione e testare i criteri di limite di frequenza.

Per scenari di limitazioni più avanzati con i criteri di [frequenza limite da chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota da chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , vedere [limitazione con Azure API di gestione delle richieste avanzate](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Per creare un prodotto

In questo passaggio si creerà un prodotto di versione di valutazione gratuita che non richiedono l'approvazione di sottoscrizione.

>[AZURE.NOTE] Se è già installato un prodotto configurato e da usare per questa esercitazione, è possibile passare direttamente a [Configura chiamate tasso criteri limite e le quote][] e seguire l'esercitazione da tale posizione utilizzando il prodotto al posto del prodotto di versione di valutazione gratuita.

Per iniziare, fare clic su **Gestisci** di Azure classico per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Gestisci la prima API Azure API Management][] .

Fare clic su **prodotti** dal menu **Gestione API** sul lato sinistro per visualizzare la pagina **prodotti** .

![Aggiungere prodotto][api-management-add-product]

Fare clic su **Aggiungi prodotto** per visualizzare la finestra di dialogo **Aggiungi nuovo prodotto** .

![Aggiungere nuovo prodotto][api-management-new-product-window]

Nella casella **titolo** digitare **Versione di valutazione gratuita**.

Nella casella **Descrizione** digitare quanto segue:  **sottoscrizione saranno in grado di eseguire chiamate 10/minuto fino a un massimo di 200 chiamate/settimana trascorso il quale accesso negato.**

I prodotti in Gestione API possono essere protetto o aprono. Prodotti protetti devono aver sottoscritto prima che possono essere utilizzati. Apri prodotti possono essere utilizzati senza un abbonamento. Assicurarsi che **richiedono l'abbonamento** sia selezionata per creare un prodotto protetto che richiede una sottoscrizione. Questo è l'impostazione predefinita.

Se si desidera un amministratore di esaminare e accettare o rifiutare tentativi di abbonamento a questo prodotto, selezionare **richiedere l'approvazione dell'abbonamento**. Se la casella di controllo non è selezionata, tentativi di abbonamento sarà approvato automaticamente. In questo esempio, gli abbonamenti vengono approvati automaticamente, in modo che non si seleziona la casella.

Per consentire agli account di sviluppo di sottoscrivere più volte sul nuovo prodotto, selezionare la casella di controllo **Consenti più abbonamenti simultanei** . In questa esercitazione non utilizzare più abbonamenti simultanei, pertanto lasciare deselezionata.

Dopo l'immettono di tutti i valori, fare clic su **Salva** per creare il prodotto.

![Prodotto aggiunto][api-management-product-added]

Per impostazione predefinita, i nuovi prodotti sono visibili agli utenti del gruppo **Administrators** . Occorre aggiungere il gruppo **sviluppatori** . Fare clic su **Versione di valutazione gratuita**e quindi fare clic sulla scheda **visibilità** .

>In Gestione API gruppi vengono utilizzati per gestire la visibilità dei prodotti per gli sviluppatori. Prodotti concedono visibilità ai gruppi e sviluppatori di visualizzare e sottoscrivere i prodotti che sono visibili ai gruppi di cui appartengono. Per ulteriori informazioni, vedere [come creare e utilizzare gruppi di Azure API Management][].

![Aggiungere gli sviluppatori gruppo][api-management-add-developers-group]

Selezionare la casella di controllo **gli sviluppatori** e quindi fare clic su **Salva**.

## <a name="add-api"> </a>Per aggiungere un'API per il prodotto

In questo passaggio dell'esercitazione si aggiungerà API eco al nuovo prodotto di versione di valutazione gratuita.

>Ogni istanza del servizio di gestione delle API include preconfigurato con un'API eco che può essere utilizzato per sperimentare e informazioni sulla gestione dei API. Per ulteriori informazioni, vedere [gestire la prima API Azure API Management][].

Fare clic su **prodotti** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Versione di valutazione gratuita** per configurare il prodotto.

![Configurare prodotto][api-management-configure-product]

Fare clic su **Aggiungi API al prodotto**.

![Aggiunta di API per prodotto][api-management-add-api]

Selezionare **Gli echi API**e quindi fare clic su **Salva**.

![Aggiungere API eco][api-management-add-echo-api]

## <a name="policies"> </a>Per configurare i criteri di limite e le quote di tariffe chiamata

Limiti di velocità e le quote vengono configurate nell'editor criteri. Fare clic su **criteri** del menu **Gestione API** sul lato sinistro. Nell'elenco di **prodotti** , fare clic su **Versione di valutazione gratuita**.

![Criteri di prodotto][api-management-product-policy]

Fare clic su **Aggiungi criteri** per importare il modello criteri e iniziare a creare il tasso di criteri di limite e le quote.

![Aggiungere criteri][api-management-add-policy]

Per inserire i criteri, posizionare il cursore nella sezione **in ingresso** o **in uscita** del modello di criteri. I criteri di limite e le quote di frequenza vengono in ingresso, quindi posizionare il cursore nell'elemento in ingresso.

![Editor Criteri][api-management-policy-editor-inbound]

Due criteri che si aggiungono in questa esercitazione sono i criteri di [sottoscrizione tariffa chiamata limite](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Imposta quota di utilizzo per abbonamento](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Istruzioni di criteri][api-management-limit-policies]

Dopo il cursore viene posizionato nell'elemento di criteri **in ingresso** , fare clic sulla freccia **abbonamento tariffa chiamata limite** per inserire il modello di criteri.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Tasso di chiamata limite per un abbonamento** può essere utilizzato il livello di prodotto e può essere utilizzato anche all'API e livelli di nome singola operazione. In questa esercitazione vengono usati solo criteri a livello di prodotto, quindi eliminare gli elementi **api** e **operazione** dall'elemento **limite di velocità** , in modo che solo rimane elemento esterno **limite di velocità** , come illustrato nell'esempio seguente.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Prodotto versione di valutazione gratuita il tasso di chiamata massimo consentito è 10 chiamate al minuto, pertanto digitare **10** come valore dell'attributo **chiamate** e **60** per l'attributo **periodo di rinnovo** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Per configurare i criteri **Imposta quota di utilizzo per abbonamento** , posizionare il cursore immediatamente sotto l'elemento appena aggiunto **limite di velocità** all'interno dell'elemento **in ingresso** e quindi fare clic sulla freccia a sinistra della virgola **Imposta quota di utilizzo per abbonamento**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Perché questo criterio anche deve essere al livello del prodotto, eliminare gli elementi di nome **api** e **operazione** , come illustrato nell'esempio seguente.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Le quote possono essere in base al numero di chiamate per intervallo, della larghezza di banda o entrambe. In questa esercitazione, non è in corso limitazione della larghezza di banda in base, quindi eliminare l'attributo **della larghezza di banda** .

    <quota calls="number" renewal-period="seconds">
    </quota>

Nella casella prodotto versione di valutazione gratuita la quota è 200 chiamate alla settimana. Specificare **200** come valore dell'attributo **chiamate** e quindi specificare **604800** come valore dell'attributo **periodo di rinnovo** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalli di criteri vengono specificati in secondi. Per calcolare l'intervallo di una settimana, è possibile moltiplicare il numero di giorni (7) per il numero di ore in un giorno (24) per il numero di minuti in un'ora (60), il numero di secondi in un minuto (60): 7 *24* 60 * 60 = 604800.

Dopo aver configurato il criterio, deve corrispondere nell'esempio seguente.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Una volta configurati i criteri desiderati, fare clic su **Salva**.

![Salvare i criteri][api-management-policy-save]

## <a name="publish-product"></a> Per pubblicare il prodotto

Ora che l'API vengono aggiunti e i criteri sono configurati, il prodotto deve essere pubblicato in modo che può essere utilizzato dagli sviluppatori. Fare clic su **prodotti** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Versione di valutazione gratuita** per configurare il prodotto.

![Configurare prodotto][api-management-configure-product]

Fare clic su **pubblica**e quindi fare clic su **Sì, pubblicarlo** per confermare.

![Pubblicare prodotto][api-management-publish-product]

## <a name="subscribe-account"> </a>Sottoscrivere un account per sviluppatori per il prodotto

Ora che il prodotto è stato pubblicato, è disponibile per essere sottoscritto e utilizzata dagli sviluppatori.

>Gli amministratori di un'istanza di gestione delle API sono sottoscritti automaticamente a tutti i prodotti. In questo passaggio dell'esercitazione verranno Sottoscrivi uno degli account per sviluppatori non amministratori per il prodotto di versione di valutazione gratuita. Se l'account di sviluppo fa parte del ruolo di amministratore, è possibile seguire insieme questo passaggio, anche se è già stata effettuata la sottoscrizione.

Fare clic su **utenti** nel menu **Gestione API** sul lato sinistro e quindi fare clic sul nome del proprio account di sviluppo. In questo esempio si utilizza l'account di sviluppo **Gragg pica** .

![Configurare per sviluppatori][api-management-configure-developer]

Fare clic su **Aggiungi sottoscrizione**.

![Aggiungere sottoscrizione][api-management-add-subscription-menu]

Selezionare **Versione di valutazione gratuita**e quindi fare clic su **Sottoscrivi**.

![Aggiungere sottoscrizione][api-management-add-subscription]

>[AZURE.NOTE] In questa esercitazione più abbonamenti simultanei non sono abilitati per il prodotto di versione di valutazione gratuita. In questo caso, sarebbe richiesto per assegnare un nome dell'abbonamento, come illustrato nell'esempio seguente.

![Aggiungere sottoscrizione][api-management-add-subscription-multiple]

Dopo aver fatto clic **Sottoscrivi**, il prodotto viene visualizzato nell'elenco delle **sottoscrizioni** per l'utente.

![Abbonamento aggiunto][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Per chiamare un'operazione e verificare il limite di velocità

Ora che il prodotto di versione di valutazione gratuita configurato e pubblicato, è possibile chiamare alcune operazioni e verificare i criteri di limite di frequenza.
Passare al portale di sviluppo facendo **del portale per sviluppatori** nel menu angolo superiore destro.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore e quindi fare clic su **API eco**.

![Portale per sviluppatori][api-management-developer-portal-api-menu]

Fare clic su **Risorsa visualizzato**e quindi fare clic su **Provalo**.

![Console aperta][api-management-open-console]

Mantenere l'impostazione predefinita i valori dei parametri e quindi selezionare la chiave di sottoscrizione per il prodotto di versione di valutazione gratuita.

![Chiave sottoscrizione][api-management-select-key]

>[AZURE.NOTE] Se si hanno più abbonamenti, assicurarsi di selezionare la chiave per **Versione di valutazione gratuita**, altrimenti i criteri configurati nei passaggi precedenti non sono attiva.

Fare clic su **Invia**e quindi visualizzare la risposta. Si noti lo **stato della risposta** di **200 OK**.

![Risultati di un'operazione][api-management-http-get-results]

Fare clic su **Invia** una velocità maggiore i criteri di limite di velocità di 10 chiamate al minuto. Dopo il criterio di velocità di limite viene superato, viene restituito lo stato risposta **429 troppo numerose richieste** .

![Risultati di un'operazione][api-management-http-get-429]

Il **contenuto di risposta** indica l'intervallo di rimanente prima tentativi verranno eseguita correttamente.

Quando i criteri di limite di velocità di 10 chiamate al minuto sono attiva, le chiamate successive avrà esito negativo fino a 60 secondi dal primo di 10 chiamate ha esito positivo per il prodotto prima è stato superato il limite di frequenza. In questo esempio, l'intervallo rimanente è 54 secondi.

## <a name="next-steps"> </a>Passaggi successivi

-   Offre una dimostrazione di come impostare la velocità e le quote nel video seguente.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Gestire la prima API Azure API Management]: api-management-get-started.md
[Come creare e usare i gruppi di gestione delle API di Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurare i criteri di limite e le quote di tariffe chiamata]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
