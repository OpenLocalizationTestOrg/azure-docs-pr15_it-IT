<properties
    pageTitle="Creare un ambiente di lavoro in Azure DevTest esercitazioni | Microsoft Azure"
    description="Creare un ambiente di lavoro in Azure DevTest esercitazioni per macchine virtuali"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/12/2016"
    ms.author="tarcher"/>

# <a name="create-a-lab-in-azure-devtest-labs"></a>Creare un ambiente di lavoro in Azure DevTest esercitazioni

## <a name="prerequisites"></a>Prerequisiti

Per creare un ambiente di lavoro, è necessario:

- Un abbonamento Azure. Per informazioni sulle opzioni di acquisto Azure, vedere [come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). È necessario essere il proprietario della sottoscrizione a creare l'ambiente di lavoro.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Procedura per creare un ambiente di lavoro in Azure DevTest esercitazioni

La procedura seguente viene illustrato come utilizzare il portale Azure per creare un ambiente di lavoro in Azure DevTest esercitazioni. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **altri servizi**e quindi selezionare **Esercitazioni DevTest** dall'elenco.

1. Scegliere **Aggiungi**e il **DevTest esercitazioni** .

    ![Aggiungere un ambiente di lavoro](./media/devtest-lab-create-lab/add-lab-button.png)

1. In e il **Crea un laboratorio DevTest** :

    1. Immettere un **Nome di laboratorio** per il nuovo laboratorio.
    
    1. Selezionare l' **abbonamento** da associare il laboratorio.
    
    1. Selezionare un **percorso** in cui memorizzare l'esercitazione.
    
    1. Selezionare **spegnimento automatico** per specificare se si desidera abilitare - e definire i parametri - la chiusura automatica del laboratorio della macchine virtuali.
    
    1. Selezionare il **tipo di archiviazione** per indicare il tipo di disco lo spazio di archiviazione per le macchine virtuali dell'ambiente di test. 
    
    1. Selezionare **Crea**.

    ![Creare una pala laboratorio](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato l'ambiente, ecco alcuni passaggi successivi da considerare:

- [Proteggere l'accesso a un ambiente di lavoro](devtest-lab-add-devtest-user.md).

- [Impostare i criteri di laboratorio](devtest-lab-set-lab-policy.md).

- [Creare un modello di laboratorio](devtest-lab-create-template.md).

- [Creare elementi personalizzati per le macchine virtuali](devtest-lab-artifact-author.md).

- [Aggiungi una macchina virtuale con gli elementi da un ambiente di lavoro](devtest-lab-add-vm-with-artifacts.md).