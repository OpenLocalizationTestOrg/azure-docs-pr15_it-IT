<properties
   pageTitle="Creare un server Analysis Services in Azure | Microsoft Azure"
   description="Informazioni su come creare un'istanza del server Analysis Services in Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="create-an-analysis-services-server"></a>Creare un server Analysis Services
In questo articolo illustra la creazione di una nuova risorsa server Analysis Services in abbonamento Azure.

## <a name="before-you-begin"></a>Prima di iniziare
Per iniziare, è necessario:

- **Abbonamento Azure**: visitare [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) per creare un account.
- **Gruppo risorse**: usare un gruppo di risorse si dispone già di o [crearne uno nuovo](../azure-resource-manager/resource-group-overview.md).

> [AZURE.NOTE] Può comportare la creazione di un server Analysis Services in un nuovo servizio fatturabile. Per ulteriori informazioni, vedere prezzi Analysis Services.

## <a name="create-an-analysis-services-server"></a>Creare un server Analysis Services

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **+ Nuovo** > **Intelligence + analitica** > **Analysis Services**.

3. In e il **Analysis Services** , compilare i campi obbligatori e quindi premere **Crea**.

    ![Creare server](./media/analysis-services-create-server/aas-create-server-blade.png)

    - **Nome del server**: digitare un nome univoco utilizzato per fare riferimento nel server.

    - **Abbonamento**: selezionare l'abbonamento a questo server effetti a.

    - **Gruppo risorse**: si tratta di contenitori che consentono di gestire una raccolta di risorse Azure. Per ulteriori informazioni, vedere [gruppi di risorse](../resource-group-overview.md).

    - **Posizione**: percorso di Azure questa data center ospita il server. Scegliere un percorso più vicino di utenti più grande.

    - **Livello di prezzo**: selezionare un livello prezzo. Modelli tabulari fino a 100 GB sono supportati. È sempre possibile cambiare il livello prezzo in un secondo momento.

4. Fare clic su **Crea**.

Creare solitamente in un minuto; spesso pochi secondi. Se è selezionata l'opzione **Aggiungi al portale**, passare al portale per visualizzare il nuovo server. In alternativa, passare a **più servizi** > **Analysis Services** per controllare se il server è pronto. Se non viene visualizzata aggiornare l'elenco.

 ![Dashboard](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il server, è possibile [distribuire un modello](analysis-services-deploy.md) in essa mediante la funzione SSDT o con SQL Server Management Studio.

Se un modello di che distribuire sul server si connette a origini dati locali, è necessario installare un [gateway di dati in locale](analysis-services-gateway.md) in un computer della rete.
