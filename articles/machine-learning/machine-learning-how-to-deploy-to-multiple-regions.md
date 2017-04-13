<properties
    pageTitle="Come distribuire un servizio Web per più paesi | Microsoft Azure"
    description="Procedure per la distribuzione delle aree geografiche (copia) un nuovo servizio Web ad altri."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Come distribuire un servizio Web per più paesi

I nuovi servizi Web di Azure consente di distribuire facilmente un servizio web per più paesi senza che sia più abbonamenti o le aree di lavoro. 

Prezzi è area specifica, che pertanto è necessario definire un piano di fatturazione per ogni area geografica in cui distribuire il servizio web.

## <a name="to-create-a-plan-in-another-region"></a>Per creare un piano in un'altra area

1. Eseguire l'accesso a [Microsoft Azure apprendimento servizi Web](https://services.azureml.net/).
2. Fare clic sull'opzione di menu **plan di messaggistica unificata** .
3. Nel piano sulla pagina, fare clic su **Nuovo**.
4. Nell'elenco a discesa **abbonamento** , selezionare l'abbonamento in cui si trova il nuovo piano.
5. Nell'elenco a discesa **area geografica** , selezionare un'area per il nuovo piano. Opzioni del piano per l'area selezionata verrà visualizzato nella sezione **Opzioni piano** della pagina.
6. Nell'elenco a discesa **Gruppo di risorse** , selezionare un gruppo di risorse per il piano. Per ulteriori informazioni sui gruppi di risorse, vedere [gestire Azure risorse tramite portale](../azure-portal/resource-group-portal.md).
7. In **Nome piano** digitare il nome del piano.
8. In **Opzioni del piano**, fare clic sul livello di fatturazione per il nuovo piano.
9. Fare clic su **Crea**.


## <a name="deploying-the-web-service-to-another-region"></a>Distribuzione del servizio web a un'altra area geografica

1. Fare clic sull'opzione di **Servizi Web** .
2. Selezionare il servizio Web per distribuire una nuova area.
3. Fare clic su **Copia**.
4. In **Nome del servizio Web**, digitare un nuovo nome per il servizio web.
5. In **descrizione del servizio Web**, digitare una descrizione per il servizio web.
6. Nell'elenco a discesa **abbonamento** , selezionare l'abbonamento in cui si trova il nuovo servizio web.
7. Nell'elenco a discesa **Gruppo di risorse** , selezionare un gruppo di risorse per il servizio web. Per ulteriori informazioni sui gruppi di risorse, vedere [gestire Azure risorse tramite portale](../azure-portal/resource-group-portal.md).
8. Nell'elenco a discesa **area geografica** , selezionare l'area in cui si desidera distribuire il servizio web.
9. Nell'elenco a discesa **account di archiviazione** , selezionare un account di archiviazione in cui archiviare il servizio web.
10. Nell'elenco a discesa **Prezzo piano** , selezionare un piano presenti nell'area selezionata nel passaggio 8.
11. Fare clic su **Copia**.

