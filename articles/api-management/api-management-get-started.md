<properties
    pageTitle="Gestire la prima API Azure API Management | Microsoft Azure"
    description="Informazioni su come creare API, operazioni di aggiunta e iniziare a utilizzare Gestione API."
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
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="manage-your-first-api-in-azure-api-management"></a>Gestire la prima API Azure API Management

## <a name="overview"> </a>Panoramica

Questa guida viene illustrato come iniziare a utilizzare Gestione API Azure ed effettuare la chiamata API prima rapidamente.

## <a name="concepts"> </a>Novità Azure API Management?

È possibile usare Azure API Management per eseguire qualsiasi back-end e avviare un programma API completo basato su di essa.

Scenari comuni includono:

* **Protezione dei dispositivi mobili infrastruttura** tramite il controllo dei access con i tasti di API, impedendo DOS attacchi mediante la limitazione o utilizzo dei criteri di sicurezza avanzate come la convalida token JWT.
* **Ecosistemi partner ISV attivazione** , che offre onboarding partner rapido tramite il portale di sviluppo e la creazione di una facciata API per separare dall'interni implementazione che non sono particolarmente adatto per il consumo partner.
* **Esecuzione di un'applicazione di API interna** offrendo una posizione centralizzata per l'organizzazione scambiare informazioni relative a verificare la disponibilità e modifiche più aggiornate alle API, accesso in base all'account dell'organizzazione, il controllo dei tutti in base a un canale protetto tra il gateway API e back-end.


Il sistema è costituito da componenti seguenti:

* Il **gateway API** è il punto finale che:
  * Accetta API chiamate e li indirizza il back-end.
  * Verifica dell'API tasti, JWT token, certificati e altre credenziali.
  * Vengono applicate le quote di uso e valutare limiti.
  * Trasforma l'API rapidamente, senza modifiche al codice.
  * Memorizza nel punto in cui configurare le risposte di back-end.
  * Registri delle chiamate metadati ai fini della analitica.

* Il **portale di publisher** è l'interfaccia di amministrazione in cui impostare il programma di API. Usarlo per:
    * Definire o importare API schema.
    * Pacchetto API nei prodotti.
    * Impostare i criteri ad esempio le quote o trasformazioni sulle API.
    * È possibile ottenere informazioni approfondite da analitica.
    * Gestire gli utenti.

* Il **portale per sviluppatori** viene utilizzato come la presenza di web principale per gli sviluppatori, in cui è possibile:
    * Documentazione dell'API di lettura.
    * Provare a usare un'API tramite la console interattiva.
    * Creare un account e la sottoscrizione per ottenere le chiavi API.
    * Analitica Access nel proprio utilizzo.


## <a name="create-service-instance"> </a>Creare un'istanza di gestione delle API

>[AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure][].

Utilizzo di gestione dell'API il primo passaggio consiste nel creare un'istanza del servizio. Accedere al [Portale classica Azure][] e fare clic su **Nuovo**, **App Services**, **Gestione API**, **Crea**.

![Nuova istanza di gestione delle API][api-management-create-instance-menu]

Per **URL**, specificare un nome univoco sottodominio da utilizzare per l'URL del servizio.

Scegliere la **sottoscrizione** e **l'area** desiderata per l'istanza del servizio. Dopo aver apportato le opzioni desiderate, fare clic sul pulsante **Avanti** .

![Nuovo servizio di gestione dell'API][api-management-create-instance-step1]

Immettere **Contoso Ltd.** per il **Nome dell'organizzazione**e immettere l'indirizzo di posta elettronica nel campo di **Posta elettronica di amministratore** .

>[AZURE.NOTE] Questo indirizzo di posta elettronica viene utilizzato per le notifiche del sistema di gestione dell'API. Per ulteriori informazioni, vedere [come configurare le notifiche e modelli di posta elettronica in Azure API Management][].

![Nuovo servizio di gestione dell'API][api-management-create-instance-step2]

Istanze del servizio di gestione delle API sono disponibili in tre livelli: sviluppo, Standard e Premium. Per impostazione predefinita, vengono create nuove istanze del servizio di gestione dell'API del livello di sviluppo. Per selezionare il livello Standard o Premium, selezionare la casella di controllo **Impostazioni avanzate** e selezionare il livello desiderato nella schermata seguente.

>[AZURE.NOTE] Il livello di sviluppo è per lo sviluppo, testing e programmi API pilota in cui disponibilità non è un problema. In livelli Standard e Premium, è possibile ridimensionare il numero di unità riservate per gestire più traffico. I livelli Standard e Premium forniscono il servizio di gestione dell'API con al massimo la potenza di elaborazione e prestazioni. È possibile completare questa esercitazione tramite qualsiasi livello. Per ulteriori informazioni sui livelli di gestione delle API, vedere [gestione di API prezzi][].

Fare clic sulla casella di controllo per creare l'istanza del servizio.

![Nuovo servizio di gestione dell'API][api-management-instance-created]

Dopo aver creato l'istanza del servizio, il passaggio successivo consiste nel creare o importare un'API.

## <a name="create-api"> </a>Importare un'API

Una API è costituita da un set di operazioni che è possibile richiamare da un'applicazione client. Operazioni API vengano inoltrate ai servizi web esistenti.

API possono essere create (e operazioni possono essere aggiunti) manualmente, o possono essere importati. In questa esercitazione si importerà l'API di un servizio web Calcolatrice di esempio forniti da Microsoft e ospitata in Azure.

>[AZURE.NOTE] Per ulteriori informazioni sulla creazione di un'API e aggiunta manuale di operazioni, vedere [come creare API](api-management-howto-create-apis.md) e [su come aggiungere operazioni a un'API](api-management-howto-add-operations.md).

API sono configurate dal portale di publisher, è possibile accedere tramite il portale classica Azure. Per accedere al portale di publisher, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API.

![Portale di Publisher][api-management-management-console]

Per importare la calcolatrice API, fare clic su **API** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Importa API**.

![Pulsante Importa API][api-management-import-api]

Per configurare la calcolatrice API, procedere come segue:

1. Fare clic su **Da URL**, immettere **http://calcapi.cloudapp.net/calcapi.json** nella casella di testo **URL specifica del documento** e fare clic sul pulsante di opzione **Swagger** .
2. Digitare nella casella di testo **URL API Web suffisso** **calc** .
3. Fare clic sulla casella **prodotti (facoltativi)** e scegliere **Starter**.
4. Fare clic su **Salva** per importare l'API.

![Aggiungi nuova API][api-management-import-new-api]

>[AZURE.NOTE] **Gestione dell'API** supporta attualmente versione 1.2 e 2.0 di Swagger documento per l'importazione. Verificare che l'opzione, anche se [specifica Swagger 2.0](http://swagger.io/specification) dichiara che `host`, `basePath`, e `schemes` proprietà sono facoltative, il documento Swagger 2.0 **deve** contenere le proprietà. in caso contrario è non è possibile ottenere importato. 

Una volta importato l'API, verrà visualizzata la pagina Riepilogo per l'API nel portale di publisher.

![Riepilogo API][api-management-imported-api-summary]

La sezione API è disponibili diverse schede. Consente di visualizzare la scheda **Riepilogo** metriche di base e informazioni sull'API. Scheda [Impostazioni](api-management-howto-create-apis.md#configure-api-settings) viene utilizzata per visualizzare e modificare la configurazione per un'API. Scheda [operazioni](api-management-howto-add-operations.md) viene utilizzata per gestire le operazioni dell'API. Per configurare l'autenticazione di gateway per il server di back-end mediante l'autenticazione di base o [autenticazione certificato reciproca](api-management-howto-mutual-certificates.md)e configurare [autorizzazioni utente mediante OAuth 2.0](api-management-howto-oauth2.md), è possibile utilizzare la scheda **sicurezza** .  La scheda **problemi** viene utilizzata per visualizzare i problemi segnalati dagli sviluppatori che utilizzano l'API. Scheda **prodotti** viene utilizzata per configurare i prodotti che contengono questa API.

Per impostazione predefinita, ogni istanza di gestione delle API viene fornito con due prodotti di esempio:

-   **Starter**
-   **Illimitato**

In questa esercitazione, le API Calcolatrice è stato aggiunto al prodotto Starter durante l'importazione dell'API.

Per effettuare chiamate a un'API, gli sviluppatori devono prima di tutto sottoscrivere un prodotto che concede l'accesso a tale. Gli sviluppatori possono eseguire la sottoscrizione prodotti nel portale di sviluppo, o gli amministratori possono agli sviluppatori di prodotti nel portale di publisher. Si è un amministratore poiché è stato creato l'istanza di gestione delle API in precedenza nell'esercitazione, in modo che sono già stata effettuata la sottoscrizione a tutti i prodotti per impostazione predefinita.

## <a name="call-operation"> </a>Chiamata un'operazione dal portale per sviluppatori

Operazioni possono essere chiamate direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API. In questo passaggio esercitazione chiamare operazione **Aggiungi due numeri interi** dell'API calcolatrice base. Fare clic su **portale per sviluppatori** dal menu nella parte superiore destra del portale di publisher.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** dal menu superiore e quindi fare clic su **Calcolo di base** per visualizzare le operazioni disponibili.

![Portale per sviluppatori][api-management-developer-portal-calc-api]

Nota le descrizioni di esempio e i parametri che sono stati importati insieme al API e operazioni, documentazione per gli sviluppatori che utilizzeranno questa operazione. Queste descrizioni possono essere aggiunti anche quando operazioni vengono aggiunte manualmente.

Per chiamare l'operazione di **aggiunta di due numeri interi** , fare clic su **Provalo**.

![Provare a][api-management-developer-portal-calc-api-console]

È possibile immettere alcuni valori per i parametri oppure mantenere le impostazioni predefinite e quindi fare clic su **Invia**.

![HTTP Get][api-management-invoke-get]

Dopo il richiamo di un'operazione, il portale per sviluppatori consente di visualizzare lo **stato della risposta**, le **intestazioni di risposta**e il **contenuto della risposta**.

![Risposta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Visualizzare analitica

Per visualizzare analitica per calcolo di base, tornare al portale di publisher, scegliere **Gestisci** dal menu nella parte superiore destra del portale per sviluppatori.

![Gestire][api-management-manage-menu]

La visualizzazione predefinita per il portale di publisher è **Dashboard**, che fornisce una panoramica dell'istanza di gestione delle API.

![Dashboard][api-management-dashboard]

Posizionare il mouse sul grafico per **Calcolo di base** visualizzare le metriche specifiche per l'uso dell'API per un periodo di tempo specificato.

>[AZURE.NOTE] Se tutte le righe non è visibile nel grafico, tornare al portale per sviluppatori di apportare alcune chiamate API, attendere qualche minuto e quindi tornare al dashboard.

Fare clic su **Visualizza dettagli** per visualizzare la pagina di riepilogo per l'API, tra cui una versione più grande di metriche visualizzate.

![Analitica][api-management-mouse-over]

![Riepilogo][api-management-api-summary-metrics]

Per informazioni dettagliate la metrica e report fare clic su **Analitica** dal menu **Gestione API** sul lato sinistro.

![Panoramica][api-management-analytics-overview]

La sezione **Analitica** include quattro schede seguenti:

-   **A colpo d'occhio** offre metriche di utilizzo e integrità generale, nonché gli sviluppatori superiore, prodotti principali, API superiore e operazioni superiore.
-   **L'uso** fornisce in modo approfondito API chiamate e la larghezza di banda, tra cui una rappresentazione geografica.
-   **Integrità** è incentrata sui codici di stato cache elevate, i tempi di risposta e API e fornire assistenza tempi di risposta.
-   **Attività** fornisce report drill-down attività specifiche per sviluppo, prodotto, API e operazione.

## <a name="next-steps"> </a>Passaggi successivi

- Informazioni su come [proteggere l'API con limiti di velocità](api-management-howto-product-with-rules.md).

[Versione di valutazione gratuita Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Come configurare le notifiche e i modelli di posta elettronica in Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Gestione dell'API prezzi]: http://azure.microsoft.com/pricing/details/api-management/

[Portale classica Azure]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
