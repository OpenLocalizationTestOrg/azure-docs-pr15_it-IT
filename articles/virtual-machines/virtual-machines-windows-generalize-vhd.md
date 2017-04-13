<properties
    pageTitle="Generalizzare un disco rigido virtuale di Windows | Microsoft Azure"
    description="Informazioni su come utilizzare Sysprep per generalizzare una macchina virtuale di Windows per l'uso con il modello di distribuzione di Manager delle risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizzare una macchina virtuale di Windows utilizzando Sysprep

In questa sezione viene illustrato come generalizzare la macchina virtuale di Windows da usare come immagine. Sysprep rimuove tutte le informazioni personali, ad esempio e consente di preparare il computer da utilizzare come immagine. Per informazioni dettagliate su Sysprep, vedere [come utilizzare Sysprep: un'introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Verificare che i ruoli del server in esecuzione nel computer sono supportate da Sysprep. Per ulteriori informazioni, vedere [Supporto di Sysprep per ruoli del Server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Se si esegue Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, accertarsi di avere [preparato la macchina virtuale](virtual-machines-windows-prepare-for-upload-vhd-image.md) prima di eseguire Sysprep. 

1. Accedere alla macchina virtuale di Windows.

2. Aprire la finestra prompt dei comandi come amministratore. Passare alla directory **%WINDIR%\system32\sysprep.**e quindi eseguire `sysprep.exe`.

3. Nella finestra di dialogo **Utilità** selezionare **sistema immettere pronte della configurazione guidata**e assicurarsi che sia selezionata la casella di controllo **Generalize** .

4. Nelle **Opzioni di arresto**, selezionare **arresto**.

5. Fare clic su **OK**.

    ![Avviare Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Al termine, Sysprep arresta la macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi

- Se la macchina virtuale è locale, è possibile [caricare il disco rigido virtuale in Azure](virtual-machines-windows-upload-image.md).
- Se la macchina virtuale è già in Azure, è possibile [creare un'immagine da macchina virtuale GRG](virtual-machines-windows-capture-image.md).