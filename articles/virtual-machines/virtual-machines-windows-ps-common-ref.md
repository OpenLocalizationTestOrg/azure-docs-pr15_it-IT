<properties 
   pageTitle="Comandi di PowerShell comuni per le macchine virtuali | Microsoft Azure"
   description="Comandi di PowerShell comuni per iniziare a creare e gestire le macchine virtuali di Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandi di PowerShell comuni per creare e gestire macchine virtuali

In questo articolo vengono illustrati alcuni dei comandi PowerShell di Azure che è possibile utilizzare per creare e gestire macchine virtuali nell'abbonamento Azure.  Per informazioni più dettagliate con opzioni e parametri della riga di comando specifici, è possibile utilizzare **Get-Help** *comando*.

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Attività | Comando
-------------- | -------------------------
Creare una configurazione macchina virtuale | $vm = [Nuovo AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName "vm_name" - VMSize "vm_size"<BR></BR><BR></BR>La configurazione di macchine Virtuali viene utilizzata per definire o aggiornare le impostazioni per la macchina virtuale. La configurazione viene inizializzata con il nome della macchina virtuale e le relative [dimensioni](virtual-machines-windows-sizes.md).
Aggiungere le impostazioni di configurazione | $vm = [AzureRmVMOperatingSystem Set](https://msdn.microsoft.com/library/mt603843.aspx) - macchine Virtuali $vm-Windows - nomecomputer "nome_computer"-credenziali $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Impostazioni del sistema operativo, incluse [le credenziali](https://technet.microsoft.com/library/hh849815.aspx) vengono aggiunti all'oggetto configurazione che in precedenza è stato creato mediante AzureRmVMConfig di nuovo.
Aggiungere un'interfaccia di rete | $vm = [AzureRmVMNetworkInterface Aggiungi](https://msdn.microsoft.com/library/mt619351.aspx) - macchine Virtuali $vm-Id $NIC ID<BR></BR><BR></BR>Una macchina virtuale deve avere un' [interfaccia di rete](virtual-machines-windows-ps-create.md) per comunicare in una rete virtuale. È anche possibile utilizzare [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) per recuperare un oggetto di interfaccia di rete esistente.
Specificare un'immagine di piattaforma | $vm = [AzureRmVMSourceImage Set](https://msdn.microsoft.com/library/mt619344.aspx) - macchine Virtuali $vm - PublisherName "publisher_name"-offrono "publisher_offer" - SKU "product_sku"-"ultima" versione<BR></BR><BR></BR>[Informazioni sulle immagini](virtual-machines-windows-cli-ps-findimage.md) viene aggiunta alla configurazione dell'oggetto che in precedenza è stato creato mediante AzureRmVMConfig di nuovo. L'oggetto restituito da questo comando è utilizzato solo quando si imposta il disco del sistema operativo a utilizzare un'immagine della piattaforma.
Impostare su disco del sistema operativo a utilizzare un'immagine della piattaforma | $vm = [AzureRmVMOSDisk Set](https://msdn.microsoft.com/library/mt603746.aspx) - macchine Virtuali $vm-http://mystore1.blob.core.windows.net/vhds/disk_name.vhd"nome"disk_name"- VhdUri" - CreateOption FromImage<BR></BR><BR></BR>Il nome del disco del sistema operativo e il relativo percorso di [archiviazione](../storage/storage-powershell-guide-full.md) viene aggiunto alla configurazione dell'oggetto creato in precedenza.
Impostare su disco del sistema operativo a utilizzare un'immagine GRG | $vm = AzureRmVMOSDisk set - macchine Virtuali $vm-https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk"nome"disk_name"- SourceImageUri. {guid} VHD"- VhdUri"https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"- CreateOption FromImage-Windows<BR></BR><BR></BR>Il nome del disco del sistema operativo, la posizione dell'immagine di origine e percorso del disco di [archiviazione](../storage/storage-powershell-guide-full.md) viene aggiunto alla configurazione dell'oggetto.
Impostare su disco del sistema operativo a utilizzare un'immagine specializzata | $vm = AzureRmVMOSDisk set - macchine Virtuali $vm-http://mystore1.blob.core.windows.net/vhds/"nome"name_of_disk"- VhdUri" - CreateOption allegare - Windows
Creare una macchina virtuale | [Nuovo AzureRmVM]() - ResourceGroupName "resource_group_name"-posizione "location_name" - macchine Virtuali $vm<BR></BR><BR></BR>Tutte le risorse vengono create in un [gruppo di risorse](../powershell-azure-resource-manager.md). Nella stessa [posizione](https://msdn.microsoft.com/library/azure/dn495177.aspx) al gruppo di risorse, è necessario creare la macchina virtuale. Prima di eseguire questo comando, eseguire nuovo AzureRmVMConfig, AzureRmVMOperatingSystem Set, Set AzureRmVMSourceImage, Aggiungi AzureRmVMNetworkInterface e AzureRmVMOSDisk Set.

## <a name="get-information-about-vms"></a>Ottenere informazioni su macchine virtuali

Attività | Comando
-------------- | -------------------------
Macchine virtuali di elenco in una sottoscrizione| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Macchine virtuali di elenco in un gruppo di risorse | Get-AzureRmVM - ResourceGroupName "resource_group_name"<BR></BR><BR></BR>Per ottenere un elenco di gruppi di risorse nell'abbonamento, utilizzare [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Ottenere informazioni su una macchina virtuale | Get-AzureRmVM - ResourceGroupName "resource_group_name"-nome "vm_name"

## <a name="manage-vms"></a>Gestire macchine virtuali

Attività | Comando
-------------- | -------------------------
Avviare una macchina virtuale | [Inizio AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Interrompere una macchina virtuale | [Interrompi AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Riavviare una macchina virtuale in esecuzione | [Riavvio AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Eliminare una macchina virtuale | [Rimuovi AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Generalizzare una macchina virtuale | [Set AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-nome "vm_name"-Generalized<BR></BR><BR></BR>Eseguire questo comando prima di eseguire AzureRmVMImage Salva.
Acquisire una macchina virtuale | [Salva AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName "resource_group_name" - VMName "vm_name" - DestinationContainerName "image_container" - VHDNamePrefix "image_name_prefix"-percorso "C:\filepath\filename.json"<BR></BR><BR></BR>[Arrestare e generalizzato](virtual-machines-windows-generalize-vhd.md) da utilizzare per creare un'immagine, deve essere una macchina virtuale. Prima di eseguire questo comando, eseguire AzureRmVm Set.
Aggiornare una macchina virtuale | [Aggiornamento AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName "resource_group_name" - macchine Virtuali $vm<BR></BR><BR></BR>Ottenere la configurazione corrente di macchine Virtuali utilizzando Get-AzureRmVM, modificare le impostazioni di configurazione dell'oggetto macchine Virtuali e quindi eseguire il comando.
Aggiungere un disco dati a una macchina virtuale | [Aggiungi AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - macchine Virtuali $vm-https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"nome"disk_name"- VhdUri" - LUN #-memorizzazione nella cache ReadWrite - DiskSizeinGB # - CreateOption vuoto<BR></BR><BR></BR>Utilizzare Get-AzureRmVM per ottenere l'oggetto macchina virtuale. Specificare il numero LUN e le dimensioni del disco. Eseguire l'aggiornamento AzureRmVM per applicare le modifiche di configurazione per la macchina virtuale. Il disco è aggiungere non è inizializzato. Per informazioni sull'inizializzazione dischi quando vengono aggiunti, vedere [gestire macchine virtuali di Azure tramite Gestione risorse e PowerShell](virtual-machines-windows-ps-manage.md).
Rimuovere un disco dati da una macchina virtuale | [Rimuovi AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - macchine Virtuali $vm-nome "disk_name"<BR></BR><BR></BR>Utilizzare Get-AzureRmVM per ottenere l'oggetto macchina virtuale. Eseguire l'aggiornamento AzureRmVM per applicare le modifiche di configurazione per la macchina virtuale.
Aggiungere un'estensione una macchina virtuale | [Set AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName "resource_group_name"-vm_name"posizione"azure_location"- VMName"-nome "extension_name"-Publisher "publisher_name"-tipo "extension_type" - TypeHandlerVersion "#. #"-impostazioni $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Eseguire questo comando con le relative [informazioni di configurazione](virtual-machines-windows-extensions-configuration-samples.md) dell'estensione che si desidera installare.
Rimuovere un'estensione macchine Virtuali | [Rimuovi AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName "resource_group_name"-vm_name"nome"extension_name"- VMName"

## <a name="next-steps"></a>Passaggi successivi

- Vedere la procedura di base per la creazione di una macchina virtuale in [creare una macchina virtuale di Windows tramite Gestione risorse e PowerShell](virtual-machines-windows-ps-create.md).

