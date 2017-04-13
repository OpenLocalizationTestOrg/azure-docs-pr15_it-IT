<properties
    pageTitle="Gestire macchine virtuali in un Set di scala macchina virtuale | Microsoft Azure"
    description="Gestire macchine virtuali in una scala di macchina virtuale set mediante Azure PowerShell."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gestire macchine virtuali in un set di scala macchina virtuale

Utilizzare le attività in questo articolo per gestire macchine virtuali nel set di scala macchina virtuale.

La maggior parte delle attività che includono la gestione di una macchina virtuale in un set di scala richiedono si conosce l'ID dell'istanza del computer in cui si vuole gestire. È possibile usare [Esplora risorse Azure](https://resources.azure.com) per trovare l'ID dell'istanza di una macchina virtuale in un set di scala. È anche possibile usare Esplora risorse per verificare lo stato delle attività da completare.

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.

## <a name="display-information-about-a-scale-set"></a>Visualizzare informazioni su un set di scala

È possibile ottenere informazioni generali su un set di scala, è anche detta visualizzazione istanza. In alternativa, è possibile ottenere informazioni più specifiche, ad esempio informazioni sulle risorse nel set di scala.

Sostituire i valori tra virgolette con il nome o il gruppo di risorse e la scala impostare ed eseguire il comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Viene restituito simile al seguente:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Sostituire i valori tra virgolette il nome del set di gruppo e la scala di risorsa. Sostituire *#* con l'identificatore dell'istanza della macchina virtuale che si desidera visualizzare informazioni e quindi eseguire:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Restituisce un elemento come in questo esempio:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Avviare una macchina virtuale in un set di scala

Sostituire i valori tra virgolette con il nome del set di gruppo e la scala di risorsa. Sostituire *#* con l'identificatore della macchina virtuale che si desidera iniziare e quindi eseguire:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

In Esplora risorse, è possibile vedere che lo stato dell'istanza sia **in esecuzione**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

È possibile iniziare tutte le macchine virtuali in scala impostata se non si utilizza il parametro - ID istanza.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Interrompere una macchina virtuale in un set di scala

Sostituire i valori tra virgolette con il nome del set di gruppo e la scala di risorsa. Sostituire *#* con l'identificatore della macchina virtuale che si desidera arrestare e quindi eseguire:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

In Esplora risorse, è possibile vedere che lo stato dell'istanza sia **rilasciato**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Per interrompere una macchina virtuale e non rilasciare, utilizzare il parametro - StayProvisioned. È possibile interrompere tutte le macchine virtuali nel set di se non si utilizza il parametro - ID istanza.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Riavviare una macchina virtuale in un set di scala

Sostituire i valori tra virgolette con il nome del gruppo di risorse e la scala. Sostituire *#* con l'identificatore della macchina virtuale che si desidera riavviare e quindi eseguire:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
È possibile riavviare tutte le macchine virtuali nel set di se non si utilizza il parametro - ID istanza.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Rimuovere una macchina virtuale da un set di scala

Sostituire i valori tra virgolette con il nome del gruppo di risorse e la scala. Sostituire *#* con l'identificatore della macchina virtuale che si desidera rimuovere e quindi eseguire:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

È possibile rimuovere il set di scala macchina virtuale in un'unica non utilizzando il parametro - ID istanza.

## <a name="change-the-capacity-of-a-scale-set"></a>Modificare la capacità di un set di scala

È possibile aggiungere o rimuovere macchine virtuali modificando la capacità del gruppo. È possibile ottenere il set di scala che si desidera modificare, impostare la capacità a quello che si vuol essere e quindi aggiornare la scala impostare con la nuova capacità. In questi comandi, sostituire i valori tra virgolette con il nome del gruppo di risorse e la scala.

  $vmss = AzureRmVmss get - ResourceGroupName "nome gruppo di risorse" - VMScaleSetName "scala impostare nome" $vmss.sku.capacity = 5 aggiornamento AzureRmVmss - ResourceGroupName "nome gruppo di risorse"-nome "scalare nome set" - VirtualMachineScaleSet $vmss 

Se si desidera rimuovere macchine virtuali relativi al set di scala, macchine virtuali gli ID più alti vengono rimossi prima di tutto.
