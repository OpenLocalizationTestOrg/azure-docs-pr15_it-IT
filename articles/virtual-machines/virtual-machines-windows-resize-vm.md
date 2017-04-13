<properties
    pageTitle="Ridimensionare una macchina virtuale di Windows | Microsoft Azure"
    description="Ridimensionare una macchina virtuale di Windows creata nel modello di distribuzione Manager delle risorse, tramite Powershell Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Ridimensionare una macchina virtuale di Windows

In questo articolo viene illustrato come ridimensionare una macchina virtuale di Windows, creati nel modello di distribuzione Manager delle risorse tramite Powershell Azure.

Dopo aver creato una macchine (), è possibile adattare la macchina virtuale verso l'alto o verso il basso modificando la dimensione di memoria virtuale. In alcuni casi, è necessario rendere nuovamente la macchina virtuale prima di tutto. Questo problema può verificarsi se la nuova dimensione non è disponibile nel cluster hardware che contiene la macchina virtuale.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ridimensionare una macchina virtuale di Windows non in un set di disponibilità

1. Elencare le dimensioni di macchine Virtuali disponibili nel cluster hardware ospita la macchina virtuale. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Se le dimensioni desiderate sono elencata, eseguire i comandi seguenti per ridimensionare la macchina virtuale. Se la dimensione desiderata non è elencata, andare al passaggio 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Se la dimensione desiderata non è elencata, eseguire i comandi seguenti per rilasciare la macchina virtuale, ridimensionarla e riavviare la macchina virtuale.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] Deallocazione macchina virtuale rilascia tutti gli indirizzi IP dinamici assegnati a macchina virtuale. Il disco del sistema operativo e dati non è interessato. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ridimensionare una macchina virtuale di Windows in un set di disponibilità

Se la nuova dimensione per una macchina virtuale in un set di disponibilità non è disponibile in cluster hardware che ospita la macchina virtuale, saranno necessario tutti macchine virtuali nel set di disponibilità essere rilasciato per ridimensionare la macchina virtuale. È inoltre potrebbe essere necessario aggiornare le dimensioni delle altre macchine virtuali di disponibilità impostare dopo il ridimensionamento di una macchina virtuale. Per ridimensionare una macchina virtuale in un set di disponibilità, procedere come segue.

1. Elencare le dimensioni di macchine Virtuali disponibili nel cluster hardware ospita la macchina virtuale.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Se le dimensioni desiderate sono elencata, eseguire i comandi seguenti per ridimensionare la macchina virtuale. Se non è elencato, andare al passaggio 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Se la dimensione desiderata non è elencata, continuare con la procedura seguente per rilasciare tutte le macchine virtuali nel set di disponibilità, macchine virtuali di ridimensionamento e riavviare i.

4.  Interrompere tutti macchine virtuali nel set di disponibilità.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Ridimensionare e riavviare le macchine virtuali nel set di disponibilità.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori scalabilità, eseguire più istanze di macchine Virtuali e scalabilità. Per ulteriori informazioni, vedere [automaticamente le dimensioni computer Windows in un Set di scala macchina virtuale](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



