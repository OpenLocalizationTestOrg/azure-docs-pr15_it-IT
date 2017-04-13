<properties 
    pageTitle="Come personalizzare Azure API sviluppo portale di gestione di uso dei modelli | Microsoft Azure" 
    description="Informazioni su come personalizzare Azure API sviluppo portale di gestione di uso dei modelli." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Come personalizzare Azure API sviluppo portale di gestione di uso dei modelli

Gestione API Azure include alcune funzionalità di personalizzazione per consentono agli amministratori di [personalizzare l'aspetto del portale per sviluppatori](api-management-customize-portal.md), nonché personalizzare il contenuto delle pagine del portale per sviluppatori utilizzando una serie di modelli che consentono di configurare il contenuto delle pagine stesse. Usa sintassi [DotLiquid](http://dotliquidmarkup.org/) e un set fornito di risorse di tipo stringa localizzati, le icone e controlli di pagina, è necessario flessibilità per configurare il contenuto delle pagine in base alle esigenze con questi modelli.

## <a name="developer-portal-templates-overview"></a>Panoramica di modelli di portale per sviluppatori

Modelli di portale per sviluppatori vengono gestiti nel portale per sviluppatori dagli amministratori dell'istanza del servizio di gestione delle API. Per gestire i modelli di sviluppo, individuare l'istanza del servizio di gestione delle API nel portale classica di Azure e fare clic su **Sfoglia**.

![Portale per sviluppatori][api-management-browse]

Se si ha già nel portale di publisher, è possibile accedere portale per sviluppatori di facendo clic sul **portale per sviluppatori**.

![Menu del portale per sviluppatori][api-management-developer-portal-menu]

Per accedere ai modelli di portale per sviluppatori, fare clic sull'icona Personalizza a sinistra per visualizzare il menu di personalizzazione e fare clic su **modelli**.

![Modelli di portale per sviluppatori][api-management-customize-menu]

L'elenco di modelli visualizza diverse categorie di modelli che copre le diverse pagine del portale per sviluppatori. Ogni modello è diverso, ma per modificarli e pubblicare le modifiche, seguire la stessa. Per modificare un modello, fare clic sul nome del modello.

![Modelli di portale per sviluppatori][api-management-templates-menu]

Fare clic su un modello consente di accedere alla pagina del portale per sviluppatori che personalizzabile per tale modello. In questo esempio l' **elenco dei prodotti** modello viene visualizzato. Il modello di **elenco prodotti** controlla l'area della schermata contrassegnata con il rettangolo rosso. 

![Modello di elenco di prodotti][api-management-developer-portal-templates-overview]

Alcuni modelli, ad esempio i modelli di **Profilo utente** , personalizzare diverse parti della stessa pagina. 

![Modelli di profilo utente][api-management-user-profile-templates]

Nell'editor per ogni modello portale per sviluppatori sono presenti due sezioni visualizzate nella parte inferiore della pagina. Sul lato sinistro Visualizza riquadro di modifica per il modello e sul lato destro il modello di dati per il modello. 

Il modello modifica riquadro contiene i commenti che consente di controllare l'aspetto e comportamento della pagina corrispondente nel portale per sviluppatori. I commenti nel modello utilizza la sintassi [DotLiquid](http://dotliquidmarkup.org/) . Un editor comuni per DotLiquid è [DotLiquid delle finestre di progettazione](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). In tempo reale vengono visualizzate le modifiche apportate al modello durante la modifica nel browser, ma non sono visibili ai clienti fino a quando non si [Salva](#to-save-a-template) e [pubblica](#to-publish-a-template) il modello.

![Markup del modello][api-management-template]

Riquadro **proprietà modello** viene fornita una Guida al modello di dati per le entità disponibili per l'utilizzo in un modello specifico. Fornisce questa guida per la visualizzazione dei dati dinamici attualmente visualizzati nel portale per sviluppatori. È possibile espandere i riquadri di modello, fare clic su rettangolo in alto a destra del riquadro **dati del modello** .

![Modello di dati][api-management-template-data]

Nell'esempio precedente sono disponibili due prodotti visualizzati nel portale per sviluppatori di recuperati dai dati visualizzati nel riquadro **dati del modello** , come illustrato nell'esempio seguente.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

I commenti nel modello di **elenco prodotti** elabora i dati per fornire output desiderato scorrendo la raccolta di prodotti per visualizzare informazioni e un collegamento a ogni singolo prodotto. Nota la `<search-control>` e `<page-control>` elementi nel markup. Questi controllare la visualizzazione della ricerca e spostamento controlli della pagina. `ProductsStrings|PageTitleProducts`è un riferimento di stringa localizzata che contiene il `h2` testo dell'intestazione della pagina. Per un elenco di stringa risorse, i controlli di pagina e le icone disponibili per utilizzare nei modelli del portale per sviluppatori, vedere [Gestione API di riferimento modelli portale per sviluppatori](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Per salvare un modello

Per salvare un modello, fare clic su Salva nell'editor dei modelli.

![Salva modello][api-management-save-template]

Le modifiche salvate non sono live nel portale per sviluppatori finché la pubblicazione.

## <a name="to-publish-a-template"></a>Per pubblicare un modello

Modelli salvati possono essere pubblicati singolarmente o tutti insieme. Per pubblicare un modello di singoli, fare clic su pubblica nell'editor dei modelli.

![Pubblicare modello][api-management-publish-template]

Fare clic su **Sì** per confermare e rendere il modello live nel portale per sviluppatori.

![Confermare la pubblicazione][api-management-publish-template-confirm]

Per pubblicare tutte le versioni di modello attualmente non pubblicati, fare clic su **pubblica** nell'elenco modelli. I modelli non pubblicati sono contrassegnati da un asterisco dopo il nome del modello. In questo esempio vengono pubblicati i modelli di **elenco di prodotti** e **prodotto** .

![Pubblicare i modelli][api-management-publish-templates]

Fare clic su **pubblica personalizzazioni** per confermare.

![Confermare la pubblicazione][api-management-publish-customizations]

Modelli appena pubblicati inefficaci immediatamente nel portale per sviluppatori.

## <a name="to-revert-a-template-to-the-previous-version"></a>Per ripristinare un modello per la versione precedente

Per ripristinare un modello per la versione pubblicata precedente, fare clic su Ripristina nell'editor dei modelli.

![Ripristinare modello][api-management-revert-template]

Fare clic su **Sì** per confermare.

![Confermare][api-management-revert-template-confirm]

La versione pubblicata precedentemente di un modello è attivo nel portale per sviluppatori di una volta completata l'operazione di ripristino.

## <a name="to-restore-a-template-to-the-default-version"></a>Per ripristinare la versione predefinita di un modello

Ripristinare la versione predefinita di modelli è un processo in due passaggi. Prima di tutto necessario ripristinare i modelli e quindi le versioni ripristinate devono essere pubblicate.

Per ripristinare un solo modello per la versione predefinita fare clic su Ripristina nell'editor dei modelli.

![Ripristinare modello][api-management-reset-template]

Fare clic su **Sì** per confermare.

![Confermare][api-management-reset-template-confirm]

Per ripristinare tutti i modelli per le versioni predefinito, fare clic su **Ripristina modelli predefiniti** nell'elenco modello.

![Ripristinare i modelli][api-management-restore-templates]

I modelli di ripristinato devono quindi essere pubblicati singolarmente o in un'unica eseguendo la procedura descritta in [per pubblicare un modello](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Riferimento modelli portale per sviluppatori

Per informazioni di riferimento per sviluppatori portale modelli, risorse di tipo stringa, icone e controlli di pagina, vedere [Gestione API di riferimento modelli portale per sviluppatori](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo

Guardare il video seguente illustrano come aggiungere un'area discussioni e valutazioni e in quella API operazione nel portale per sviluppatori di utilizzare i modelli.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







