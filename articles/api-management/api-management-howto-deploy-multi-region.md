<properties
    pageTitle="Come distribuire un'istanza di servizio di gestione delle API di Azure per più paesi Azure"
    description="Informazioni su come distribuire un'istanza di servizio di gestione delle API di Azure per più paesi Azure." 
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

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Come distribuire un'istanza di servizio di gestione delle API di Azure per più paesi Azure

Gestione dell'API supporta la distribuzione di più aree che consente di autori di API distribuire un servizio di gestione dell'API singolo in qualsiasi numero di aree di Azure desiderate. Consente di ridurre la richiesta latenza percepita da aree geografiche distribuito consumatori API e migliora la disponibilità del servizio se una regione in linea. 

Quando viene creato un servizio di gestione delle API inizialmente, contiene solo un' [unità][] e si trova in un'area di Azure singola, come l'area principale. Ulteriori regioni possono essere aggiunti facilmente tramite il portale classica di Azure. Server di gateway di gestione delle API viene distribuito in ogni regione e il traffico chiamata instradato al gateway più vicino. Se un'area in linea, il traffico è automaticamente reindirizzato al successivo gateway più vicino. 

> [AZURE.IMPORTANT] Distribuzione di più paesi è disponibile solo in livello **[Premium][]** .

## <a name="add-region"> </a>Distribuire un'istanza di servizio di gestione delle API in una nuova area

Per iniziare, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Passare alla scheda **scala** nel portale classica Azure per l'istanza del servizio di gestione dell'API. 

![Scheda scala][api-management-scale-service]

La distribuzione a una nuova area, fare clic su riepilogo sotto l'area principale e selezionare un'area dall'elenco.

![Aggiungere l'area geografica][api-management-add-region]

Dopo aver selezionato l'area geografica, selezionare il numero di unità per la nuova area dall'elenco a discesa unità.

![Specificare le unità][api-management-select-units]

Dopo aver configurate le aree geografiche desiderate e le unità, fare clic su **Salva**.

## <a name="remove-region"> </a>Eliminare un'istanza di servizio di gestione delle API da un'area geografica

Per rimuovere un'istanza di servizio di gestione delle API da un'area geografica, passare alla scheda **scala** nel portale classica Azure per l'istanza del servizio di gestione dell'API. 

![Scheda scala][api-management-scale-service]

Fare clic sulla **X** a destra dell'area desiderata da rimuovere.  

![Rimuovere l'area geografica][api-management-remove-region]

Dopo aver rimosso le aree desiderate, fare clic su **Salva**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Guida introduttiva di Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unità]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

