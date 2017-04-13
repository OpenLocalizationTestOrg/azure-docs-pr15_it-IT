<properties
   pageTitle="Visualizzare le operazioni di distribuzione con portale | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare il portale di Azure per trovare gli errori di distribuzione di Manager delle risorse."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Visualizza le operazioni di distribuzione con portale Azure

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

È possibile visualizzare le operazioni per una distribuzione tramite il portale di Azure. Si desidera più visualizzare le operazioni quando si riceve un messaggio di errore durante la distribuzione in modo che questo articolo è incentrato sulla visualizzazione delle operazioni che non sono riuscita. Il portale offre un'interfaccia che consente di trovare gli errori e determinare potenziali correzioni facilmente.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizzare le operazioni di distribuzione per la risoluzione

Per visualizzare le operazioni di distribuzione, utilizzare la procedura seguente:

1. Per il gruppo di risorse necessarie per la distribuzione, si noterà lo stato dell'ultima distribuzione. È possibile selezionare questo stato per maggiori dettagli.

    ![stato di distribuzione](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Verrà visualizzata la cronologia delle distribuzioni recenti. Selezionare la distribuzione che non è riuscita.

    ![stato di distribuzione](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Non è possibile selezionare **. Fare clic qui per informazioni dettagliate** per visualizzare una descrizione del motivo del mancato di distribuzione. Nell'immagine seguente, il record DNS non sia univoco.  

    ![visualizzare distribuzione non riuscita](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Questo messaggio di errore dovrebbe essere sufficiente per iniziare la risoluzione dei problemi a. Tuttavia, se si desiderano maggiori dettagli sulle quali attività sono state completate, è possibile visualizzare le operazioni come illustrato di seguito.

4. È possibile visualizzare tutte le operazioni di distribuzione e il **distribuzione** . Selezionare qualsiasi operazione per visualizzare ulteriori dettagli.

    ![operazioni di visualizzazione](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    In questo caso, viene visualizzato l'account di archiviazione, virtuali e set di disponibilità sono stati creati correttamente. L'indirizzo IP pubblico non è riuscita e non si è tentati di altre risorse.

5. È possibile visualizzare gli eventi per la distribuzione selezionando **eventi**.

    ![visualizzare gli eventi](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Vedere tutti gli eventi per la distribuzione e selezionare uno per altri dettagli.

    ![vedere gli eventi](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>I registri di controllo per la risoluzione

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, utilizzare la procedura seguente:

1. Visualizzare il log di controllo per un gruppo di risorse selezionando **I registri di controllo**.

    ![Selezionare dei registri](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. In e il **Log di controllo** , verrà visualizzato un riepilogo delle operazioni recenti per tutti i gruppi di risorse nell'abbonamento. Include una rappresentazione grafica del tempo e stato delle operazioni, oltre che un elenco delle operazioni.

    ![Mostra azioni](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. È possibile filtrare la visualizzazione dei registri di controllo per concentrarsi su determinate condizioni. Selezionare **filtro** nella parte superiore e il **log di controllo** .

    ![registri di filtro](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. Selezionare le condizioni per limitare la visualizzazione dei registri di controllo a solo le operazioni che si desidera visualizzare e il **filtro** . Ad esempio, è possibile filtrare le operazioni per visualizzare solo gli errori del gruppo di risorse.

    ![impostare le opzioni di filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. È possibile filtrare ulteriormente operazioni mediante l'impostazione di un intervallo di tempo. Nella figura seguente consente di filtrare la visualizzazione per un determinato intervallo di tempo di 20 minuti.

    ![Imposta data/ora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. È possibile selezionare una delle operazioni nell'elenco. Selezionare l'operazione che contiene l'errore che si desidera cercare.

    ![Selezionare operazione](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Verranno visualizzati tutti gli eventi per tale operazione. Si noti l' **ID di correlazione** nel riepilogo. Questo ID viene utilizzato per tenere traccia degli eventi correlati. Può essere utile quando si lavora con supporto tecnico per la risoluzione dei problemi. È possibile selezionare qualsiasi dell'evento per visualizzare i dettagli sull'evento.

    ![Selezionare l'evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Verranno visualizzati i dettagli sull'evento. In particolare attenzione alle **proprietà** per informazioni sull'errore.

    ![visualizzare i dettagli del log di controllo](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Il filtro che è applicato il log di controllo viene mantenuto la volta successiva che si visualizza, in modo che potrebbe essere necessario modificare i valori per ampliare la visualizzazione delle operazioni.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come risolvere gli errori di distribuzione specifico, vedere [risolvere errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md).
- Per informazioni sull'utilizzo dei log di controllo per monitorare altri tipi di azioni, vedere [controllare le operazioni con Gestione risorse](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy.md).
