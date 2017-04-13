<properties
   pageTitle="Estensione di Script personalizzati in una macchina virtuale di Windows | Microsoft Azure"
   description="Automatizzare le attività di configurazione di macchine Virtuali di Azure utilizzando l'estensione di Script personalizzato per l'esecuzione di script di PowerShell in una macchina virtuale Windows remoto"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Estensione di Script personalizzati per macchine virtuali di Windows

In questo articolo viene fornita una panoramica su come utilizzare l'estensione di Script personalizzato in macchine virtuali di Windows tramite i cmdlet di PowerShell Azure con API di gestione del servizio di Azure.

Estensioni macchine () vengono create da Microsoft e attendibili gli autori di terze parti per estendere la funzionalità della macchina virtuale. Per una panoramica delle estensioni macchine Virtuali, vedere [caratteristiche ed estensioni macchine Virtuali di Azure](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Panoramica di estensione Script personalizzato

Con estensione Script personalizzato per Windows, è possibile eseguire gli script di PowerShell in una macchina virtuale remota senza eseguire l'accesso a tale. È possibile eseguire gli script dopo il provisioning di macchina virtuale o in qualsiasi momento durante il ciclo di vita della macchina virtuale senza aprire le porte aggiuntive. Maggior parte dei casi di utilizzo per l'esecuzione di Script personalizzato estensione sono in esecuzione, installare e configurare software aggiuntivo su macchina virtuale dopo che viene eseguito il provisioning.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Prerequisiti per l'esecuzione l'estensione di Script personalizzato

1. Installare <a href="http://azure.microsoft.com/downloads" target="_blank">i cmdlet di PowerShell Azure</a> versione 0.8.0 o versione successiva.
2. Se si desidera che gli script per l'esecuzione in una macchina virtuale esistente, verificare che l'agente macchine Virtuali è attivata la macchina virtuale. Se non è installato, seguire questa [procedura](virtual-machines-windows-classic-agents-and-extensions.md). Se la macchina virtuale viene creata dal portale di Azure, macchine Virtuali agente viene installato per impostazione predefinita.
3. Caricare gli script che si desidera eseguire nella macchina virtuale allo spazio di archiviazione Azure. Gli script possono provenire da un singolo contenitore o più contenitori di archiviazione.
4. Lo script deve essere creato in modo che lo script voce, che viene avviato dall'estensione, inizio altri script.

## <a name="custom-script-extension-scenarios"></a>Scenari di estensione Script personalizzati

### <a name="upload-files-to-the-default-container"></a>Caricare i file per il contenitore predefinito

Nell'esempio seguente mostra come è possibile eseguire gli script nella macchina virtuale se si trovano nel contenitore di spazio di archiviazione dell'account predefinito del proprio abbonamento. Caricare gli script nomecontenitore. È possibile verificare l'account di archiviazione predefinito utilizzando il **AzureSubscription Get-predefinito** comando.

Nell'esempio seguente viene creata una macchina virtuale, ma è anche possibile eseguire lo stesso scenario su una macchina virtuale esistente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Caricare file in un contenitore di spazio di archiviazione non predefinito

Questo scenario viene illustrato come utilizzare un contenitore di spazio di archiviazione predefinito non all'interno della stessa sottoscrizione o in una sottoscrizione diversa per il caricamento di script e file. In questo esempio mostra una macchina virtuale esistente, ma le stesse operazioni possono essere eseguite mentre si sta creando una macchina virtuale.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Caricare gli script in più contenitori tra gli account di archiviazione diversi

  Se i file di script sono archiviati in più contenitori, è necessario specificare l'URL di accesso completo condivisa firme (SA) per i file per eseguire gli script.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Aggiungere l'estensione di Script personalizzato dal portale di Azure

Passare a macchine Virtuali nel <a href="https://portal.azure.com/ " target="_blank">portale di Azure</a> e aggiungere l'estensione specificando l'esecuzione del file di script.

  ![Specificare il file di script][5]


### <a name="uninstall-the-custom-script-extension"></a>Disinstallare l'estensione di Script personalizzato

È possibile disinstallare l'estensione di Script personalizzato da macchina virtuale utilizzando il comando seguente.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Utilizzare l'estensione di Script personalizzato con i modelli

Per informazioni su come usare l'estensione di Script personalizzato con i modelli di Manager delle risorse di Azure, vedere [utilizzando l'estensione di Script personalizzato per macchine virtuali di Windows con i modelli di gestione risorse di Azure](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
