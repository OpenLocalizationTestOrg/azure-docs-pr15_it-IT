<properties
    pageTitle="Gestire macchine virtuali utilizzando Manager delle risorse e PowerShell | Microsoft Azure"
    description="Gestire le macchine virtuali utilizzando Gestione risorse di Azure e PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Gestire le macchine virtuali di Azure utilizzando Manager delle risorse e PowerShell

## <a name="install-azure-powershell"></a>Installare PowerShell Azure
 
Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.

## <a name="set-variables"></a>Set di variabili

Tutti i comandi nell'articolo richiedono il nome del gruppo di risorse in cui si trova la macchina virtuale e il nome del computer virtuale per la gestione. Sostituire il valore di **$rgName** con il nome del gruppo di risorse contenente la macchina virtuale. Sostituire il valore di **$vmName** con il nome della macchina virtuale. Creare le variabili.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Visualizzare informazioni su una macchina virtuale

È possibile ottenere le informazioni di macchina virtuale.
  
    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Restituisce un elemento come in questo esempio:

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Interrompere una macchina virtuale

Interrompere la macchina virtuale in esecuzione.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Viene richiesto di confermare l'operazione:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Immettere **Y** per interrompere la macchina virtuale.

Dopo alcuni minuti, verrà restituito un elemento come in questo esempio:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Avviare una macchina virtuale

Avviare la macchina virtuale se è stato arrestato.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Dopo alcuni minuti, verrà restituito un elemento come in questo esempio:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Se si desidera riavviare una macchina virtuale che è già in esecuzione, utilizzare **AzureRmVM riavvio** descritto nelle sezioni successive.

## <a name="restart-a-virtual-machine"></a>Riavviare una macchina virtuale

Riavviare il computer virtuale in esecuzione.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Restituisce un elemento come in questo esempio:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Eliminare una macchina virtuale

Eliminare la macchina virtuale.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] È possibile utilizzare il **-forza** parametro per ignorare la richiesta di conferma.

Se non sono stati utilizzati specifica il parametro - forza, viene chiesto di confermare l'operazione:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Restituisce un elemento come in questo esempio:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Aggiornare una macchina virtuale

In questo esempio viene illustrato come aggiornare le dimensioni della macchina virtuale.
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
Restituisce un elemento come in questo esempio:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
Per un elenco di formati disponibili per una macchina virtuale, vedere [dimensioni per macchine virtuali in Azure](virtual-machines-windows-sizes.md) .

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Aggiungere un disco dati a una macchina virtuale

In questo esempio viene illustrato come aggiungere un disco dati a una macchina virtuale esistente.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Il disco è aggiungere non è inizializzato. Per inizializzare il disco, è possibile accedere a essa e utilizzare Gestione disco. Se si WinRM e un certificato in cui è installato quando è stata creata, è possibile utilizzare sessione remota di PowerShell per inizializzare il disco. È inoltre possibile utilizzare un'estensione script personalizzato: 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

Il file di script può contenere simile a questo codice inizializzare dischi:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Passaggi successivi

Se si sono verificati problemi con una distribuzione, potrebbe osservare [le distribuzioni di risoluzione dei problemi delle risorse gruppo il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
