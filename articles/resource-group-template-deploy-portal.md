<properties 
    pageTitle="Usare il portale di Azure per distribuire le risorse Azure | Microsoft Azure" 
    description="Portale di Azure e la gestione delle risorse di Azure consente di distribuire le risorse." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuire le risorse con i modelli di Manager delle risorse e portale Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portale](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

In questo argomento viene illustrato come utilizzare il [portale di Azure](https://portal.azure.com) con [Gestione risorse di Azure](azure-resource-manager/resource-group-overview.md) per distribuire le risorse Azure. Per informazioni sulla gestione delle risorse, vedere [gestire Azure risorse tramite portale](./azure-portal/resource-group-portal.md).

Non tutti i servizi attualmente supporta il portale e Manager delle risorse. Per i servizi, è necessario usare il [portale classica](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere [grafico Azure della disponibilità al portale](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Creare gruppo di risorse

1. Per creare un gruppo di risorse vuoto, selezionare **Nuovo** > **Management** > **Gruppo di risorse**.

    ![creare il gruppo di risorse vuoto](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Assegnarle un nome e un percorso e, se necessario, selezionare un abbonamento. È necessario specificare un percorso per il gruppo di risorse perché il gruppo di risorse vengono archiviati i metadati le risorse. Per motivi di conformità, può essere necessario specificare in cui sono archiviati i metadati. In generale, è consigliabile specificare un percorso in cui verrà salvato la maggior parte delle risorse. Nella stessa posizione consente di semplificare il modello.

    ![impostare i valori di gruppo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Distribuire le risorse da Marketplace

Dopo aver creato un gruppo di risorse, è possibile distribuire risorse da Marketplace. Il Marketplace fornisce soluzioni predefinite per gli scenari comuni.

1. Per avviare una distribuzione, selezionare **Nuovo** e il tipo di risorsa che si desidera distribuire. Quindi, cercare la versione specifica della risorsa che si desidera distribuire.

    ![la distribuzione delle risorse](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Se non viene visualizzata la soluzione particolare che si desidera distribuire, è possibile cercare il Marketplace.

    ![marketplace di ricerca](./media/resource-group-template-deploy-portal/search-resource.png)

3. A seconda del tipo di risorsa selezionata, si dispone di un insieme di proprietà rilevanti per impostare prima della distribuzione. Queste opzioni non verranno visualizzate in questo caso, come variano in base al tipo di risorsa. Per tutti i tipi, è necessario selezionare un gruppo di risorse di destinazione. Nella figura seguente viene illustrato come creare un'app web e distribuire al gruppo di risorse che è stato creato.

    ![creare gruppo di risorse](./media/resource-group-template-deploy-portal/select-existing-group.png)

    In alternativa, è possibile creare un gruppo di risorse per la distribuzione delle risorse. Selezionare **Crea nuovo** e specificare un nome per il gruppo di risorse.

    ![Crea nuovo gruppo di risorse](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Verrà avviata la distribuzione. La distribuzione potrebbe richiedere alcuni minuti. Al termine di distribuzione, viene visualizzata una notifica.

    ![notifica di visualizzazione](./media/resource-group-template-deploy-portal/view-notification.png)

5. Dopo la distribuzione delle risorse, è possibile aggiungere altre risorse al gruppo di risorse tramite il comando **Aggiungi** su e il gruppo delle risorse.

    ![aggiunta di risorse](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuire risorse dal modello personalizzato

Se si desidera eseguire una distribuzione ma non usare uno dei modelli in Marketplace, è possibile creare un modello personalizzato che consente di definire l'infrastruttura per la soluzione. Per ulteriori informazioni sulla creazione di modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).

1. Per distribuire un modello personalizzato tramite il portale, selezionare **Nuovo**e avviare la ricerca per **La distribuzione dei modelli** fino a quando non è possibile selezionare una delle opzioni.

    ![distribuzione dei modelli di ricerca](./media/resource-group-template-deploy-portal/search-template.png)

2. Selezionare **La distribuzione dei modelli** di risorse disponibili.

    ![Selezionare la distribuzione dei modelli](./media/resource-group-template-deploy-portal/select-template.png)

3. Dopo l'avvio della distribuzione di modello, aprire il modello vuoto che è disponibile per la personalizzazione.

    ![Crea modello](./media/resource-group-template-deploy-portal/show-custom-template.png)

    Nell'editor di aggiungere la sintassi JSON che consente di definire le risorse che si desidera distribuire. Selezionare **Salva** al termine. Per indicazioni su come scrivere la sintassi JSON, vedere [procedura dettagliata modello Manager delle risorse](resource-manager-template-walkthrough.md).

    ![Modifica modello](./media/resource-group-template-deploy-portal/edit-template.png)

4. In alternativa, è possibile selezionare un modello preesistente dai [modelli Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/). Questi modelli vengono forniti dalla community. Vengono illustrate molti scenari comuni e un utente può essere aggiunti a un modello simile a quello che si sta tentando di distribuzione. È possibile cercare i modelli per trovare il testo corrispondente proprio scenario.

    ![Selezionare il modello di Guida introduttiva](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    È possibile visualizzare il modello selezionato nell'editor.

5. Dopo avere fornito tutti gli altri valori, selezionare **Crea** per distribuire il modello. 

    ![distribuire modello](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Distribuire le risorse da un modello salvato al proprio account

Il portale consente di salvare un modello al proprio account Azure e ridistribuire in un secondo momento. Per ulteriori informazioni sull'uso di questi modelli, [iniziare con i modelli personali nel portale di Azure](./marketplace-consumer/mytemplates-getstarted.md)salvato.

1. Per trovare i modelli salvati, selezionare **Sfoglia** > **modelli**.

    ![Sfoglia modelli](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Nell'elenco di modelli salvati al proprio account, selezionare quello che si desidera utilizzare.

    ![modelli salvati](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Selezionare **Distribuisci** per ridistribuire il modello salvato.

    ![distribuire modello salvato](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare i log di controllo, vedere [controllare le operazioni con Gestione risorse](resource-group-audit.md).
- Per risolvere gli errori di distribuzione, vedere [risoluzione dei problemi delle risorse gruppo il portale di Azure](resource-manager-troubleshoot-deployments-portal.md).
- Per recuperare un modello da un gruppo di risorse o di distribuzione, vedere [esportare Manager delle risorse di Azure modello dalle risorse esistenti](resource-manager-export-template.md).





