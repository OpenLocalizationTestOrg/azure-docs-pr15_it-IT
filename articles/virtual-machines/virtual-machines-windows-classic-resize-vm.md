<properties
    pageTitle="Ridimensionare una macchina virtuale Windows classico | Microsoft Azure"
    description="Ridimensionare una macchina virtuale di Windows creata nel modello di distribuzione classico, tramite Powershell Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Ridimensionare una macchina virtuale di Windows creati nel modello di distribuzione classica

In questo articolo viene illustrato come ridimensionare una macchina virtuale di Windows, creati nel modello di distribuzione classica tramite Powershell Azure.

Nel valutare la possibilità di ridimensionare una macchina virtuale, sono disponibili due concetti che consentono di controllare l'intervallo di dimensioni disponibili per ridimensionare la macchina virtuale. Il primo concetto è l'area geografica in cui viene distribuita di una macchina virtuale. Elenco dei formati di macchine Virtuali disponibili nell'area si trova sotto la scheda Services della pagina web aree Azure. Il concetto secondo è l'hardware che ospita la macchina virtuale. Fisico server che ospitano macchine virtuali sono raggruppate in cluster di hardware fisico comuni. Il metodo di modifica delle dimensioni una macchina virtuale varia a seconda se la nuova dimensione macchine Virtuali desiderata è supportata dal cluster hardware che ospita la macchina virtuale.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]È inoltre possibile [ridimensionare una macchina virtuale creata nel modello di distribuzione di Manager delle risorse](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Aggiungere l'account

È necessario configurare Azure PowerShell per gestire le risorse di Azure classiche. Seguire la procedura seguente per configurare Azure PowerShell per gestire le risorse classiche.

1. Al prompt dei comandi PowerShell, digitare `Add-AzureAccount` e premere **INVIO**. 
2. Digitare l'indirizzo di posta elettronica associato all'abbonamento Azure e fare clic su **Continua**. 
3. Digitare la password dell'account. 
4. Fare clic su **Accedi**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Ridimensionare nello stesso cluster hardware

Per ridimensionare una macchina virtuale di una dimensione disponibile in cluster hardware che ospita la macchina virtuale, eseguire i passaggi seguenti.

1. Eseguire il seguente comando di PowerShell per elencare le dimensioni di macchine Virtuali disponibili cluster hardware che include il servizio cloud che contiene la macchina virtuale.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Eseguire i comandi seguenti per ridimensionare la macchina virtuale.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Ridimensionare in un nuovo cluster hardware

Per ridimensionare una macchina virtuale di una dimensione non è disponibile in cluster hardware che ospita la macchina virtuale, nel cloud è necessario ricreare servizio e tutte le macchine virtuali nel servizio cloud. Ogni servizio cloud è ospitato in un cluster di hardware singola in modo che tutte le macchine virtuali nel servizio cloud devono essere una dimensione è supportata in un cluster di hardware. La procedura seguente viene descritto ridimensionare una macchina virtuale da eliminare e quindi ricreare il servizio cloud.

1. Eseguire il seguente comando di PowerShell per le dimensioni di macchine Virtuali disponibili nell'area dell'elenco. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Prendere nota di tutte le impostazioni di configurazione per ogni macchina virtuale nel servizio cloud che contiene la macchina virtuale da ridimensionare. 
3. Eliminare tutte le macchine virtuali nel servizio cloud selezionando l'opzione per mantenere i dischi per ogni macchina virtuale.
4. Ricreare la macchina virtuale per il ridimensionamento tramite le dimensioni di macchine Virtuali desiderate.
5. Ricreare tutte le altre macchine virtuali che erano presenti nel servizio cloud usando una dimensione di memoria virtuale disponibile in cluster hardware ora che include il servizio cloud.

Sono disponibili uno script di esempio per l'eliminazione e creazione di un servizio cloud usando una nuova dimensione macchine Virtuali [di seguito](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Passaggi successivi

- [Ridimensionare una macchina virtuale creata nel modello di distribuzione di Manager delle risorse](virtual-machines-windows-resize-vm.md).