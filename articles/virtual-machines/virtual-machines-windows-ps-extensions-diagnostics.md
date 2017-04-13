<properties
    pageTitle="Usare PowerShell per attivare la diagnostica Azure in un computer virtuale che esegue Windows | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Informazioni su come usare PowerShell per attivare la diagnostica Azure in un computer virtuale che esegue Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Consente di attivare la diagnostica Azure in un computer virtuale che esegue Windows PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnostica Windows Azure è la capacità all'interno di Azure che consente l'insieme di dati di diagnostica in un'applicazione distribuita. È possibile utilizzare l'estensione di diagnostica per la raccolta di dati di diagnostica come registri applicazioni o contatori da una Azure macchine () che esegue Windows. In questo articolo viene descritto come utilizzare Windows PowerShell per attivare l'estensione di diagnostica per una macchina virtuale. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per i prerequisiti necessari per questo articolo.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Attivare l'estensione di diagnostica se si usa il modello di distribuzione di Manager delle risorse

È possibile attivare l'estensione di diagnostica durante la creazione di una macchina virtuale di Windows tramite il modello di distribuzione di gestione di risorse Azure mediante l'aggiunta di configurazione dell'estensione al modello di Manager delle risorse. Vedere [creare una macchina virtuale di Windows con il monitoraggio e diagnostica utilizzando il modello di gestione risorse di Azure](virtual-machines-windows-extensions-diagnostics-template.md).

Per abilitare l'estensione di diagnostica per una macchina virtuale esistente creato mediante il modello di distribuzione di Manager delle risorse, è possibile utilizzare il cmdlet [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell, come illustrato di seguito.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* è il percorso del file che contiene la configurazione di diagnostica in XML, come illustrato nell' [esempio](#sample-diagnostics-configuration) seguente.  

Se il file di configurazione di diagnostica specifica un elemento **StorageAccount** con un nome di account di archiviazione, lo script *AzureRMVMDiagnosticsExtension Set* verrà automaticamente impostare l'estensione di diagnostica per inviare dati di diagnostica per account di archiviazione. Per questa, l'account di archiviazione deve essere nello stesso abbonamento macchina virtuale.

Se non **StorageAccount** è stato specificato nella configurazione di diagnostica, è necessario passare nel parametro *StorageAccountName* al cmdlet. Se il parametro *StorageAccountName* viene specificato, il cmdlet verrà sempre utilizzato l'account di archiviazione specificato nel parametro e non quello specificato nel file di configurazione di diagnostica.

Se l'account di archiviazione di diagnostica in una sottoscrizione diversa da macchina virtuale, è necessario passare in modo esplicito i parametri *StorageAccountName* e *StorageAccountKey* al cmdlet. Il parametro *StorageAccountKey* non è necessario quando l'account di archiviazione di diagnostica nello stesso abbonamento, come il cmdlet possibile automaticamente query e impostare il valore di chiave quando si abilita l'estensione di diagnostica. Tuttavia, se l'account di archiviazione di diagnostica in una sottoscrizione diversa, il cmdlet potrebbe non essere possibile ottenere la chiave automaticamente e si è necessario specificare la chiave tramite il parametro *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Dopo aver abilitato l'estensione di diagnostica su una macchina virtuale, è possibile ottenere le impostazioni correnti utilizzando il cmdlet [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) .

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Il cmdlet restituisce *PublicSettings*, che contiene la configurazione di XML in un formato di codifica base 64. Per leggere il codice XML, è necessario decodificare essa.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

Per rimuovere l'estensione di diagnostica macchina virtuale, è possibile utilizzare il cmdlet [AzureRMVmDiagnosticsExtension Rimuovi](https://msdn.microsoft.com/library/mt603782.aspx) .  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Attivare l'estensione di diagnostica se si usa il modello di distribuzione classica

È possibile utilizzare il cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) per abilitare un'estensione diagnostica su una macchina virtuale creati tramite il modello di distribuzione classica. Nell'esempio seguente viene illustrato come creare una nuova macchina virtuale tramite il modello di distribuzione classica con l'estensione di diagnostica abilitato.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Per abilitare l'estensione di diagnostica per una macchina virtuale esistente creato mediante il modello di distribuzione classica, utilizzare il cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) per ottenere la configurazione di macchine Virtuali. Aggiornare la configurazione di macchine Virtuali per includere l'estensione di diagnostica utilizzando il cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) . Infine, applicare configurazione aggiornata la macchina virtuale tramite [AzureVM di aggiornamento](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Configurazione di diagnostica di esempio

Per la configurazione di pubblico diagnostica con gli script indicati, è possibile utilizzare il seguente codice XML. Questo esempio di configurazione verrà trasferiti vari contatori nell'account di archiviazione di diagnostica, insieme a eventuali errori dall'applicazione, sicurezza e i canali di sistema nei registri eventi di Windows e dai registri infrastruttura diagnostica.

La configurazione deve essere aggiornata per includere le operazioni seguenti:

- L'attributo *resourceID* dell'elemento **metriche** deve essere aggiornato con l'ID di risorsa per la macchina virtuale.
    - ID risorsa è possibile costruire utilizzando il modello seguente: "/resourceGroups//abbonamenti / {*ID abbonamento per la sottoscrizione con la macchina virtuale*} {*nome resourcegroup per la macchina virtuale*} / {*nome macchine Virtuali*} providers/Microsoft.Compute/virtualMachines/".
    - Ad esempio, se l'ID di abbonamento per l'abbonamento a cui si sta eseguendo la macchina virtuale è **11111111-1111-1111-1111-111111111111**, il nome del gruppo di risorse del gruppo di risorse è **MyResourceGroup**e il nome di macchine Virtuali è **MyWindowsVM**, il valore di *resourceID* sarà:

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Per ulteriori informazioni su come metriche vengono generate in base alla configurazione di contatori e metriche di prestazioni, vedere la [tabella metriche diagnostica Azure in archiviazione](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- L'elemento **StorageAccount** deve essere aggiornati con il nome dell'account di archiviazione di diagnostica.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori istruzioni sull'uso di funzionalità diagnostica Azure e altre tecniche per la risoluzione dei problemi, vedere [Attivazione di diagnostica in macchine virtuali e servizi Cloud Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Schema configurazioni diagnostica](https://msdn.microsoft.com/library/azure/mt634524.aspx) illustra le varie opzioni di configurazioni XML per l'estensione di diagnostica.
