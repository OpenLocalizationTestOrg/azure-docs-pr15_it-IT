<properties
    pageTitle="Accedere a una macchina virtuale Azure classica | Microsoft Azure"
    description="Utilizzare il portale classico Azure per accedere a una macchina virtuale Windows creata con il modello di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Accedere a una macchina virtuale di Windows utilizzando il portale classico Azure

Nel portale classico Azure, il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale di Windows.

Si desidera connettersi a un VM Linux? Scopri [come accedere a una macchina virtuale in esecuzione Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Informazioni su come [eseguire questa procedura utilizzando nuovo portale di Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>Procedura dettagliata video

Di seguito è una procedura dettagliata video delle procedure in questa esercitazione. Viene inoltre illustrato endpoint e pubbliche e private porte utilizzate per la connessione a una macchina virtuale di Windows in Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

1. Accedere al portale di classica Azure.

2. Fare clic su **macchine virtuali**e quindi selezionare la macchina virtuale.

3. Sulla barra dei comandi nella parte inferiore della pagina, fare clic su **Connetti**.

    ![Accedere alla macchina virtuale](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] Se il pulsante di **connessione** non è disponibile, vedere i suggerimenti sulla risoluzione dei problemi alla fine di questo articolo.

## <a name="log-on-to-the-virtual-machine"></a>Accedere alla macchina virtuale

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passaggi successivi

-   Se il pulsante **Connetti** è inattivo oppure si sono verificati altri problemi di connessione Desktop remoto, provare a reimpostare la configurazione. Dal dashboard macchina virtuale in **Riepilogo veloce**, fare clic su **Reimposta configurazione remota**.
-   Per problemi con la password, è possibile reimpostarlo. Dal dashboard macchina virtuale in **Riepilogo veloce**, fare clic su **reimpostazione della password**.

Se i suggerimenti non funzionano o non è necessario, vedere [le connessioni di Desktop remoto risoluzione dei problemi relativi a una basata su Windows Azure macchina virtuale](virtual-machines-windows-troubleshoot-rdp-connection.md). In questo articolo viene descritto come diagnosticare e risolvere i problemi comuni.


