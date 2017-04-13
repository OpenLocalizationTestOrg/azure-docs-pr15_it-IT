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
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>Che cos'è Gestione API?

Gestione dell'API consente alle organizzazioni di pubblicare API per esterni, partner e sviluppatori interni per sbloccare le potenzialità dei dati e i servizi. Imprese ovunque la ricerca per estendere la loro attività come piattaforma digitale, la creazione di nuovi canali, cercare nuovi clienti e favorire coinvolgimento più approfondita con quelli esistenti. Gestione dell'API fornisce le competenze fondamentali per garantire un programma API successo tramite coinvolgimento di sviluppo, informazioni aziendali, analitica, sicurezza e protezione.

In questo video per una panoramica di gestione delle API di Azure e imparare a utilizzare Gestione API per aggiungere molte caratteristiche per l'API, inclusi il controllo di accesso, limitazione della velocità, monitoraggio, registrazione eventi e risposta la memorizzazione nella cache, con il minimo da parte dell'utente.

> [AZURE.VIDEO azure-api-management-overview]

Per utilizzare Gestione API, gli amministratori di creare API. Ogni API è costituita da una o più operazioni e ogni API possono essere aggiunti a uno o più prodotti. Per utilizzare un'API, gli sviluppatori sottoscrivere un prodotto che contiene l'API e quindi chiamano operazione dell'API, soggette a eventuali criteri di utilizzo che potrebbero essere valide.

In questo argomento viene fornita una panoramica dei concetti fondamentali di gestione delle API.

>[AZURE.NOTE] Per ulteriori informazioni, vedere il [Gestione API basate su Cloud: tutta la potenza delle API](http://j.mp/ms-apim-whitepaper) white paper PDF. Questo white paper introduttivo sulla gestione delle API da CITO Research illustra: 
>
> - Problemi e requisiti API comuni
>     - Separazione API e presentare facciate
>     - Effettuare gli sviluppatori per eseguire rapidamente
>     - Protezione dell'accesso
>     - Analitica e metriche
> - Ottenere controllo e conoscenza con una piattaforma di gestione dell'API
> - Utilizzo di soluzioni di cloud vs locale
> - Gestione dell'API Azure

## <a name="apis"> </a>API e attività operative

API sono alla base di un'istanza di servizio di gestione delle API. Ogni API rappresenta un insieme di operazioni disponibili per gli sviluppatori. Ogni API contiene un riferimento al servizio di back-end che implementa l'API e la mappa operazioni alle operazioni implementata dal servizio back-end. Operazioni di gestione delle API sono configurabile di controllare l'URL mapping query e parametri del percorso, richiesta e risposta e di operazione cache delle risposte. Limite di velocità, le quote e criteri di restrizione IP possono anche essere implementati all'API o livello di singoli.

Per ulteriori informazioni, vedere [come creare API][] e [su come aggiungere operazioni a un'API][].


## <a name="products"></a> Prodotti

I prodotti siano come API vengono resi disponibili per gli sviluppatori. Prodotti in Gestione API dispongono di uno o più API e configurati con un titolo, descrizione e delle condizioni di utilizzo. Prodotti possono essere **aperto** o **protetta**. Prodotti protetti necessario sottoscritto prima che possono essere utilizzati, anche se aperto prodotti possono essere utilizzati senza un abbonamento. Quando un prodotto è pronto per essere utilizzato dagli sviluppatori può essere pubblicato. Dopo la pubblicazione, può essere visualizzata (e per i prodotti protetti sottoscritto) per gli sviluppatori. Approvazione di sottoscrizione è configurato al livello del prodotto e può richiedere l'approvazione dell'amministratore o approvazione automatica.

Gruppi vengono utilizzati per gestire la visibilità dei prodotti per gli sviluppatori. Prodotti concedono visibilità ai gruppi e gli sviluppatori possono visualizzare e sottoscrivere i prodotti che sono visibili ai gruppi di cui appartengono. 

Per ulteriori informazioni, vedere [come creare e pubblicare un prodotto][] e il video seguente.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Gruppi

Gruppi vengono utilizzati per gestire la visibilità dei prodotti per gli sviluppatori. Gestione dell'API sono i seguenti gruppi di sistema non modificabile.

-   **Gli amministratori** , gli amministratori di Azure abbonamento sono membri di questo gruppo. Gli amministratori gestire le istanze del servizio di gestione delle API, la creazione di API, operazioni e prodotti che vengono utilizzati dagli sviluppatori.
-   **Gli sviluppatori** - portale per sviluppatori autenticati gli utenti possono essere suddivise in questo gruppo. Gli sviluppatori sono i clienti che creano applicazioni usando l'API. Gli sviluppatori concesso l'accesso al portale di sviluppo e compilano di applicazioni che le operazioni di un'API di chiamata.
-   **Gli utenti guest** - gli utenti del portale per sviluppatori non autenticati, ad esempio clienti potenziali che visitano il portale per sviluppatori di un'istanza di gestione delle API rientrano in questo gruppo. È possibile fissato determinato accesso in sola lettura, ad esempio la possibilità di visualizzare API, ma non chiamata.

Oltre a questi gruppi di sistema, gli amministratori possono creare gruppi personalizzati o [sfruttare gruppi esterni nel tenant di Azure Active Directory associato](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Personalizza e gruppi esterni possono essere utilizzati insieme a gruppi di sistema in dato visibilità gli sviluppatori e accesso ai prodotti API. Ad esempio, si potrebbe creare un gruppo personalizzato per gli sviluppatori associati a un'organizzazione partner specifici e consentono di accedere alle API da un prodotto che contiene solo API pertinenti. Un utente può essere un membro di più di un gruppo.

Per ulteriori informazioni, vedere [come creare e utilizzare gruppi][].

## <a name="developers"></a> Gli sviluppatori

Gli sviluppatori rappresentano gli account utente in un'istanza di servizio di gestione delle API. Gli sviluppatori possono essere creati o invitati a partecipare dagli amministratori, o possono iscriversi dal [portale per sviluppatori][]. Ogni sviluppatore è un membro di uno o più gruppi e può essere rappresentato da sottoscrivere i prodotti che concedono visibilità ai gruppi.

Quando gli sviluppatori sottoscrivere un prodotto dispongono di chiave primaria e secondaria per il prodotto. Questo tasto viene utilizzato quando le chiamate in API del prodotto.

Per ulteriori informazioni, vedere [creare o invitare gli sviluppatori][] e [associare gruppi con gli sviluppatori][].

## <a name="policies"></a> Criteri

I criteri sono una potente funzionalità di gestione di API che consentono l'autore che si desidera modificare il comportamento dell'API tramite la configurazione. I criteri sono un insieme di istruzioni che vengono eseguite in sequenza nella richiesta o risposta di un'API. Istruzioni popolari includono conversione del formato XML JSON e limitazione della velocità di chiamata per limitare la quantità di chiamate in arrivo da uno sviluppatore e molti altri criteri sono disponibili.

Espressioni criterio possono essere utilizzate come valori degli attributi o valori di testo in uno dei criteri di gestione delle API, a meno che il criterio specifica in caso contrario. Alcuni criteri, ad esempio i criteri di [flusso di controllo](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) e [impostare variabile](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) seguono si basano sulle espressioni di criteri. Per ulteriori informazioni, vedere [Avanzate criteri](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [le espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx)e guardare il video seguente.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Per un elenco completo dei criteri di gestione delle API, vedere [informazioni di riferimento dei criteri][]. Per ulteriori informazioni sull'utilizzo e configurazione dei criteri, vedere [criteri di gestione delle API][]. Per un'esercitazione sulla creazione di un prodotto con frequenza limite e le quote dei criteri, vedere [come creare e configurare impostazioni avanzate del prodotto][]. Per una dimostrazione, vedere il video seguente.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Del portale per sviluppatori

Il portale per sviluppatori è nel punto in cui gli sviluppatori possono informazioni sulle operazioni API, di visualizzazione e di chiamata e sottoscrivere i prodotti. Clienti potenziali possono visitare Help portale per sviluppatori di visualizzare operazioni e le API e iscriversi. L'URL per il portale per sviluppatori si trova nel dashboard nel portale classica di Azure per l'istanza del servizio di gestione dell'API.

Aggiunta di contenuto personalizzato, personalizzare stili e aggiungendo le personalizzazioni, è possibile personalizzare l'aspetto del portale per sviluppatori.

## <a name="api-management-and-the-api-economy"></a>API di gestione e il risparmio di API

Per ulteriori informazioni sulla gestione di API, guardare la presentazione seguente dalla conferenza Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portale per sviluppatori]: #developer-portal

[Come creare API]: api-management-howto-create-apis.md
[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md
[Come creare e utilizzare i gruppi]: api-management-howto-create-groups.md
[Procedura per associare gruppi agli sviluppatori]: api-management-howto-create-groups.md#associate-group-developer
[Come creare e configurare impostazioni avanzate del prodotto]: api-management-howto-product-with-rules.md
[Come creare o invitare gli sviluppatori]: api-management-howto-create-or-invite-developers.md
[Guida di riferimento dei criteri]: api-management-policy-reference.md
[Criteri di gestione delle API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
