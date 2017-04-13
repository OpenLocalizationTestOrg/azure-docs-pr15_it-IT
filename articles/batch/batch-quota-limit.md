<properties
    pageTitle="Batch le quote di servizio e limiti | Microsoft Azure"
    description="Informazioni sui vincoli, limiti e le quote di Azure Batch predefinito e aumenta per la richiesta di quota"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Le quote e i limiti per il servizio di Azure Batch

Come con altri servizi di Azure, esistono limiti di determinate risorse associate al servizio di Batch. Molte di queste limitazioni siano quote predefinite applicate da Azure l'abbonamento o il livello di account. In questo articolo vengono illustrati i valori predefiniti e come è possibile richiedere quota aumenta.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario aumentare una o più delle quote sopra il valore predefinito. Se si desidera generare una quota, è possibile aprire un online [richiesta di assistenza clienti](#increase-a-quota) senza costi aggiuntivi.

>[AZURE.NOTE] Una quota è un limite di credito, non una garanzia capacità. Se si hanno esigenze di capacità di grandi dimensioni, contattare il supporto di Azure.

## <a name="subscription-quotas"></a>Quote di abbonamento
**Risorsa**|**Limite predefinito**|**Limite massimo**
---|---|---
Account batch per ogni area geografica per abbonamento | 1 | 50

## <a name="batch-account-quotas"></a>Quote di account batch
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Altri limiti
**Risorsa**|**Limite massimo**
---|---
[Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo | 4 numero x di core nodo
[Applicazioni](batch-application-packages.md) per conto di Batch        | 20
Pacchetti di applicazioni per ogni applicazione  | 40
Dimensioni pacchetto dell'applicazione (ciascuno)       | Circa 195GB<sup>1</sup>

Limite di archiviazione di azure <sup>1</sup> per massima blob dimensione blocco

## <a name="view-batch-quotas"></a>Visualizzare le quote di Batch

Visualizzare le quote di account Batch nel [portale di Azure][portal].

1. Selezionare **account Batch** nel portale, quindi selezionare l'account Batch che interessa.

2. Scegliere **proprietà** dal blade menu dell'account Batch

3. E il proprietà consente di visualizzare **le quote** attualmente applicato all'account Batch

    ![Quote di account batch][account_quotas]

## <a name="increase-a-quota"></a>Aumentare la quota

Seguire la procedura seguente per richiedere una quota di aumenta tramite il [portale di Azure][portal].

1. Selezionare il riquadro **Guida + supporto** nel dashboard del portale o il punto interrogativo (**?**) nell'angolo superiore sinistro del portale.

2. Selezionare **nuova richiesta di assistenza** > **Nozioni di base**.

3. In e il **Nozioni di base** :

    un. **Tipo di problema** > **Quota**

    b. Selezionare l'abbonamento.

    c. **Tipo di quota** > **Batch**

    d. **Piano di supporto** > **Quota incluso il supporto per -**

    Fare clic su **Avanti**.

4. In e il **problema** :

    un. Selezionare una **gravità** in base [impatto aziendale][support_sev].

    b. In **Dettagli**, specificare ogni quota che si desidera modificare il nome dell'account Batch e il limite di nuovo.

    Fare clic su **Avanti**.

5. In e **l'informazioni di contatto** :

    un. Selezionare un **metodo di contatto preferito**.

    b. Verificare e immettere le informazioni di contatto necessari.

    Fare clic su **Crea** per inviare la richiesta di assistenza.

Dopo avere inviato alla richiesta di assistenza, si verrà contattati supporto Azure. Si noti che possono impiegare fino a 2 giorni lavorativi completamento della richiesta.

## <a name="related-topics"></a>Argomenti correlati

* [Creare un account Azure Batch tramite il portale di Azure](batch-account-create-portal.md)

* [Panoramica delle funzionalità Batch Azure](batch-api-basics.md)

* [Limiti di servizio, Azure sottoscrizione e le quote e vincoli](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
