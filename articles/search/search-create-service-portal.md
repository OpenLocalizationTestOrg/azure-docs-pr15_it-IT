<properties
    pageTitle="Creare un servizio di ricerca di Azure tramite il portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Informazioni su come effettuare il provisioning di un servizio di ricerca di Azure tramite il portale di Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Creare un servizio di ricerca di Azure tramite il portale di Azure

Questa guida consentono all'utente il processo di creazione (o provisioning) un servizio di ricerca di Azure tramite il [Portale di Azure](https://portal.azure.com/).

Questa guida presuppone che sia già disponibile un abbonamento a pagamento Azure e possa accedere al portale di Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Trovare ricerca Azure nel portale di Azure
1. Accedere al [Portale di Azure](https://portal.azure.com/) e accedere.
1. Fare clic sul segno più ("+") nell'angolo superiore sinistro.
2. Selezionare **dati + spazio di archiviazione**.
3. Selezionare **la ricerca di Azure**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Scegliere un nome di servizio e un endpoint di URL per il servizio
1. Il nome del servizio sarà parte dell'URL endpoint del servizio di ricerca di Azure rispetto al quale si effettuano le chiamate API per gestire e utilizzare il servizio di ricerca.
2. Digitare il nome del servizio nel campo **URL** . Il nome del servizio:
  * deve contenere solo lettere minuscole, cifre o trattini ("-")
  * non è possibile usare un trattino ("-") come i primi 2 caratteri o l'ultimo carattere singolo
  * non possono contenere trattini consecutivi ("-")
  * è limitata tra 2 e 60 caratteri


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Selezionare una sottoscrizione in cui si manterranno del servizio
Se si dispone di più di una sottoscrizione, è possibile selezionare quale sarà incluso il servizio di ricerca di Azure.

## <a name="select-a-resource-group-for-your-service"></a>Selezionare un gruppo di risorse per il servizio
Creare un nuovo gruppo di risorse o selezionarne uno esistente. Un gruppo di risorse è un insieme di servizi di Azure e le risorse utilizzate insieme. Ad esempio, se si utilizza ricerca Azure a un database SQL di indice, entrambi i servizi deve essere parte dello stesso gruppo di risorse.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Selezionare il percorso in cui verrà ospitato del servizio
Come servizio Azure Azure ricerca è disponibile per essere ospitati nei Data Center in tutto il mondo. Si noti che [i prezzi possono essere diversi](https://azure.microsoft.com/pricing/details/search/) per area geografica.

## <a name="select-your-pricing-tier"></a>Selezionare il livello prezzo
[Ricerca di Azure è attualmente disponibile in più livelli prezzi](https://azure.microsoft.com/pricing/details/search/): gratuita, base o Standard. Ognuno dei quali dispone proprie [capacità e limiti](search-limits-quotas-capacity.md). Per istruzioni, vedere [scegliere un livello di prezzi o SKU](search-sku-tier.md) .

In questo caso, avendo scelto il livello Standard per il servizio.

## <a name="select-the-create-button-to-provision-your-service"></a>Selezionare il pulsante "Crea" per eseguire il provisioning del servizio

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Ridimensionare il servizio

Dopo che viene completato il provisioning del servizio, è possibile proporzioni secondo le proprie esigenze. Se si è scelto il livello Standard per il servizio di ricerca di Azure, è possibile ridurre il servizio in due dimensioni: repliche e partizioni. Se si è scelto il livello di base, è possibile aggiungere solo repliche.

*__Le partizioni__* consentire il servizio archiviare e cercare all'interno di più documenti.

*__Repliche__* consentire al servizio per gestire un carico superiore delle query di ricerca - [un servizio richiede 2 di replica da ottenere un contratto di servizio di sola lettura e 3 di replica da ottenere un contratto di servizio di lettura/scrittura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Passare a blade di gestione del servizio di ricerca di Azure nel portale di Azure.
2. Nella finestra e **Impostazioni** selezionare **scala**.
3. È possibile ridimensionare il servizio mediante l'aggiunta di repliche o partizioni.
  * Non è possibile ridimensionare il servizio oltre 36 ricerca unità. Il numero totale di unità di ricerca è il prodotto delle partizioni repliche (repliche * partizioni = totale unità di ricerca).
  * Se si è scelto il livello di base, è possibile ridimensionarlo solo alle 3 repliche. Servizi di base sono associati a una singola partizione.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Successivo
Dopo il provisioning di un servizio di ricerca di Azure, sarà possibile definire [un indice di ricerca di Azure](search-what-is-an-index.md) in modo da caricare e cercare i dati.

Per un'esercitazione rapida, vedere [Guida introduttiva di Azure ricerca nel portale](search-get-started-portal.md) .
