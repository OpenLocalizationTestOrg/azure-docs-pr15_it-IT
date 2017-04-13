<properties
    pageTitle="Gestire immagini personalizzate Azure DevTest esercitazioni per creare macchine virtuali | Microsoft Azure"
    description="Informazioni su come creare un'immagine personalizzata da un file disco rigido virtuale o da una macchina virtuale esistente in Azure DevTest esercitazioni"
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
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="tarcher"/>

# <a name="manage-azure-devtest-labs-custom-images-to-create-vms"></a>Gestire immagini personalizzate Azure DevTest esercitazioni per creare macchine virtuali

In serie di esercitazioni DevTest Azure, immagini personalizzate consentono di creare rapidamente macchine virtuali senza attendere che il software necessario sia installato nel computer di destinazione. Immagini personalizzate consentono di installare il software è necessario in un file disco rigido virtuale e quindi utilizzano il file disco rigido virtuale per creare una macchina virtuale. Poiché il software è già installato, l'ora di creazione di macchine Virtuali è molto più veloce. Inoltre, immagini personalizzate vengono utilizzate per duplicare macchine virtuali con la creazione di un'immagine personalizzata da una macchina virtuale e quindi creare macchine virtuali da tale immagine personalizzata.

In questo articolo descritto come:

- [Creare un'immagine personalizzata da un file disco rigido virtuale](#create-a-custom-image-from-a-vhd-file) in modo che è possibile creare una macchina virtuale da tale immagine personalizzata. 
- [Creare un'immagine personalizzata da una macchina virtuale](#create-a-custom-image-from-a-vm) per la duplicazione macchine Virtuali rapido.

## <a name="create-a-custom-image-from-a-vhd-file"></a>Creare un'immagine personalizzata da un file disco rigido virtuale

In questa sezione, viene visualizzato come creare un'immagine personalizzata da un file disco rigido virtuale.
È necessario accedere a un file disco rigido virtuale valido per eseguire tutti i passaggi di questa sezione.   


1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **altri servizi**e quindi selezionare **Esercitazioni DevTest** dall'elenco.

1. Selezionare il laboratorio desiderato dall'elenco di esercitazioni.  

1. In blade del laboratorio, selezionare **la configurazione**. 

1. Scegliere **immagini personalizzato**e il **configurazione** laboratorio.

1. Scegliere **+ immagine personalizzata**e **l'immagini personalizzato** .

    ![Aggiungere l'immagine personalizzata](./media/devtest-lab-create-template/add-custom-image.png)

1. Immettere il nome dell'immagine personalizzata. Questo nome viene visualizzato nell'elenco delle immagini di base, durante la creazione di una macchina virtuale.

1. Immettere una descrizione dell'immagine personalizzata. Questa descrizione viene visualizzata nell'elenco delle immagini di base, durante la creazione di una macchina virtuale.

1. Selezionare **il File disco rigido virtuale**.

1. Se si dispone dell'accesso a un file disco rigido virtuale che non è elencato, aggiungerlo seguendo le istruzioni nella sezione [caricare un file disco rigido virtuale](#upload-a-vhd-file) e tornare in questa posizione al termine.

1. Selezionare il file disco rigido virtuale desiderato.

1. Fare clic su **OK** per chiudere e il **File disco rigido virtuale** .

1. Selezionare **la configurazione del sistema operativo**.

1. Nella scheda **Configurazione del sistema operativo** selezionare **Windows** o **Linux**.

1. Se è selezionata l'opzione di **Windows** , specificare tramite la casella di controllo se è stato eseguito *Sysprep* nel computer.

1. Fare clic su **OK** per chiudere e il **Sistema operativo configurazione** .

1. Fare clic su **OK** per creare l'immagine personalizzata.

1. Passare alla sezione [Passaggi successivi](#next-steps) .

###<a name="upload-a-vhd-file"></a>Caricare un file disco rigido virtuale

Per aggiungere un'immagine personalizzata, è necessario disporre di un file disco rigido virtuale.

1. Scegliere **caricare un file di disco rigido virtuale tramite PowerShell**e il **File disco rigido virtuale** .

    ![Carica immagine](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. E il successivo visualizzerà le istruzioni per la modifica e l'esecuzione di uno script di PowerShell che consente di caricare Azure sottoscrizione a un file disco rigido virtuale. 
**Nota:** Questo processo può essere lungo a seconda delle dimensioni del file disco rigido virtuale e la velocità di connessione.

## <a name="create-a-custom-image-from-a-vm"></a>Creare un'immagine personalizzata da una macchina virtuale
Se si dispone di una macchina virtuale che è già configurata, è possibile creare un'immagine personalizzata da tale macchine Virtuali e successivamente utilizzare tale immagine personalizzata per creare altre macchine virtuali identici. La procedura seguente viene illustrato come creare un'immagine personalizzata da una macchina virtuale:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **altri servizi**e quindi selezionare **Esercitazioni DevTest** dall'elenco.

1. Selezionare il laboratorio desiderato dall'elenco di esercitazioni.  

1. Scegliere **le macchine virtuali**blade del laboratorio.
 
1. Scegliere la macchina virtuale da cui si desidera creare l'immagine personalizzata e il **computer virtuali** .

1. Pala della macchina virtuale, selezionare **Crea immagine personalizzata (disco rigido virtuale)**.

    ![Crea voce di menu immagine personalizzata](./media/devtest-lab-create-template/create-custom-image.png)

1. Nella e **Crea immagine** , immettere un nome e una descrizione per l'immagine personalizzata. Queste informazioni viene visualizzate nell'elenco delle basi quando si crea una macchina virtuale.

    ![Creare blade immagine personalizzata](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selezionare se è stato eseguito sysprep nella macchina virtuale. Se non è stato eseguito il sysprep nella macchina virtuale, specificare se si desidera sysprep eseguire quando viene creata una macchina virtuale da questa immagine personalizzata.

1. Fare clic su **OK** dopo aver completato per creare l'immagine personalizzata.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati

- [Immagini personalizzate o formule?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copia di immagini personalizzate tra esercitazioni DevTest Azure](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto un'immagine personalizzata da utilizzare durante la creazione di una macchina virtuale, il passaggio successivo consiste [nell'aggiungere una macchina virtuale per l'esercitazione](./devtest-lab-add-vm-with-artifacts.md).