<properties
    pageTitle="Modificare un set di disponibilità macchine virtuali | Microsoft Azure"
    description="Informazioni su come modificare la disponibilità impostare per macchine virtuali tramite PowerShell Azure e il modello di distribuzione di Manager delle risorse."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="drewm"/>



# <a name="change-the-availability-set-for-a-windows-vm"></a>Modificare la disponibilità impostare per una macchina virtuale di Windows

La procedura seguente viene descritto come modificare la disponibilità di una macchina virtuale tramite PowerShell Azure. Una macchina virtuale può essere aggiunti solo a una disponibilità impostata quando viene creato. Per modificare la disponibilità impostare, è necessario eliminare e ricreare la macchina virtuale. 

## <a name="change-the-availability-set-using-powershell"></a>Modificare la disponibilità set mediante PowerShell

1. Acquisire i seguenti dettagli chiave da macchine Virtuali da modificare.

    Nome della macchina virtuale
    
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
    $vm.Name
    ```
 
    Dimensione memoria virtuale
    
    ```powershell
    $vm.HardwareProfile.VmSize
    ```

    Interfaccia di rete principale di rete facoltativo interfacce di rete e se sono presenti nella macchina virtuale
    
    ```powershell
    $vm.NetworkProfile.NetworkInterfaces[0].Id
    ```

    Profilo del sistema operativo disco

    ```powershell
    $vm.StorageProfile.OsDisk.OsType
    $vm.StorageProfile.OsDisk.Name
    $vm.StorageProfile.OsDisk.Vhd.Uri
    ```

    Profili di disco per ogni disco di dati 
    
    ```powershell
    $vm.StorageProfile.DataDisks[<index>].Lun
    $vm.StorageProfile.DataDisks[<index>].Vhd.Uri
    ```

    Estensioni macchine Virtuali installate 
    
    ```powershell
    $vm.Extensions
    ```

2. Eliminare la macchina virtuale senza eliminare uno qualsiasi dei dischi o le interfacce di rete.

    ```powershell
    Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
    ```

3. Creare la disponibilità impostata se non esiste già

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
    ```

4. Ricreare la macchina virtuale utilizzando il nuovo set di disponibilità

    ```powershell
    $vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>

    Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]

    Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 

    New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
    ``` 

5. Aggiungere le estensioni e dischi di dati. Per ulteriori informazioni, vedere [Allegare dati disco in macchine Virtuali](virtual-machines-windows-attach-disk-portal.md) ed [Esempi di configurazione di estensione](virtual-machines-windows-extensions-configuration-samples.md). Estensioni e dischi dati possono essere aggiunti a macchine Virtuali tramite PowerShell o CLI Azure.

## <a name="example-script"></a>Script di esempio

Il seguente script viene fornito un esempio di raccogliere le informazioni richieste, eliminare la macchina virtuale originale e quindi ricreare in un nuovo set di disponibilità.

```powershell
    #set variables
    $rg = "demo-resource-group"
    $vmName = "demo-vm"
    $newAvailSetName = "demo-as"
    $outFile = "C:\temp\outfile.txt"

    #Get VM Details
    $OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

    #Output VM details to file
    "VM Name: " | Out-File -FilePath $outFile 
    $OriginalVM.Name | Out-File -FilePath $outFile -Append

    "Extensions: " | Out-File -FilePath $outFile -Append
    $OriginalVM.Extensions | Out-File -FilePath $outFile -Append

    "VMSize: " | Out-File -FilePath $outFile -Append
    $OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

    "NIC: " | Out-File -FilePath $outFile -Append
    $OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

    "OSType: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

    "OS Disk: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

    if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
    }

    #Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

    #Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
    if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
    }

    #Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
    Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

    #Add Data Disks
    foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
    }

    #Add NIC(s)
    foreach ($nic in $OriginalVM.NetworkInterfaceIDs) {
        Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic
    }

    #Create the VM
    New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## <a name="next-steps"></a>Passaggi successivi

Aggiungere ulteriore spazio di archiviazione per le macchine Virtuali mediante l'aggiunta di un [disco dati](virtual-machines-windows-attach-disk-portal.md).

