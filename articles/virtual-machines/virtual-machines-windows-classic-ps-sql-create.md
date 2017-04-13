<properties
    pageTitle="Creare una macchina virtuale SQL Server Azure PowerShell (classico) | Microsoft Azure"
    description="Vengono fornite procedure e gli script di PowerShell per la creazione di una macchina virtuale Azure con immagini raccolta macchina virtuale di SQL Server. Questo argomento viene utilizzata la modalità di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Effettuare il provisioning di una macchina virtuale di SQL Server tramite PowerShell Azure (classica)

## <a name="overview"></a>Panoramica

In questo articolo viene descritta la procedura creare una macchina virtuale di SQL Server in Azure tramite i cmdlet di PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per la versione di Manager delle risorse di questo argomento, vedere [disposizione una macchina virtuale di SQL Server tramite Gestione risorse PowerShell Azure](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installare e configurare PowerShell:

1. Se non si dispone di un account Azure, visitare il [periodo di prova gratuito di Azure](https://azure.microsoft.com/pricing/free-trial/).

2. [Scaricare e installare i comandi di PowerShell Azure più recente](../powershell-install-configure.md).

3. Avviare Windows PowerShell e connetterlo al proprio abbonamento Azure con il comando **Aggiungi AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Determinare la destinazione area Azure

Il computer di SQL Server virtuale verrà ospitata in un servizio cloud che si trova un'area specifica di Azure. La procedura seguente consente di determinare l'account di archiviazione, l'area geografica e cloud servizio utilizzato per il resto dell'esercitazione.

1. Determinare il centro di dati che si desidera utilizzare per ospitare la macchina virtuale Server SQL. I comandi di PowerShell seguenti verranno visualizzate le aree disponibili nei dettagli di un elenco di riepilogo al termine.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Dopo aver identificato il percorso preferito, impostare una variabile denominata **$dcLocation** a tale area.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Configurare l'account di sottoscrizione e lo spazio di archiviazione

1. Determinare la sottoscrizione di Azure da utilizzare per la nuova macchina virtuale.

        (Get-AzureSubscription).SubscriptionName

1. Assegnare alla destinazione Azure abbonamento della variabile **$subscr** . Impostare come abbonamento Azure corrente.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. Cercare gli account di archiviazione esistenti. Il seguente script visualizzati tutti gli account di archiviazione presenti nella propria area geografica scelta:

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Se si richiede un nuovo account di archiviazione, creare innanzitutto il nome di un account di archiviazione tutte minuscole con il comando nuovo AzureStorageAccount come illustrato nell'esempio seguente: **AzureStorageAccount New - StorageAccountName "<storage account name>"-percorso $dcLocation**

1. Assegnare il nome dell'account destinazione di archiviazione a **$staccount**. Utilizzare **Set AzureSubscription** per impostare la sottoscrizione e l'account di archiviazione corrente.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Selezionare un'immagine di macchina virtuale di SQL Server

1. Individuare l'elenco di immagini di macchine virtuali di SQL Server disponibili nella raccolta. Queste immagini che tutti dispongono di una proprietà **ImageFamily** che inizia con "SQL". La query seguente consente di visualizzare la famiglia di immagine disponibile per l'utente che dispone di SQL Server preinstallato.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Dopo avere individuato la famiglia di immagine macchina virtuale, in questo gruppo potrebbe essere più immagini pubblicate. Utilizzare il seguente script per trovare il nome dell'immagine macchina virtuale pubblicato più recente per la tua famiglia immagine selezionata (ad esempio **SQL Server 2016 RTM Enterprise in Windows Server 2012 R2**):

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Infine, creare la macchina virtuale con PowerShell:

1. Creare un servizio cloud per ospitare la nuova macchina virtuale. Si noti che è anche possibile utilizzare un servizio cloud esistente. Creare una nuova variabile **$svcname** con il nome breve del servizio cloud.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Specificare il nome e una dimensione. Per ulteriori informazioni sulle dimensioni dei macchina virtuale, vedere [Le dimensioni di macchina virtuale per Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Specificare l'account dell'amministratore locale e la password.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Eseguire il seguente script per creare la macchina virtuale.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Per ulteriori informazioni e opzioni di configurazione, vedere la sezione **creare l'insieme di comandi** PowerShell di [Azure usare per creare e preconfigurare macchine virtuali basato su Windows](virtual-machines-windows-classic-create-powershell.md).

## <a name="example-powershell-script"></a>Script di PowerShell di esempio

Fornisce il seguente script ed esempio di uno script completo che crea una macchina virtuale di **SQL Server 2016 RTM Enterprise in Windows Server 2012 R2** . Se si usa questo script, è necessario personalizzare le variabili iniziale in base ai passaggi precedenti in questo argomento.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Connettersi con desktop remoto

1. Creare il. File RDP nella cartella documenti dell'utente corrente a queste macchine virtuali per completare la configurazione di avvio:

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Nella cartella documenti, avviare il file RDP. Connettersi con il nome utente e la password forniti in precedenza (ad esempio, se il proprio nome utente è stato VMAdmin, specificare "\VMAdmin" come l'utente e la password).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Completare la configurazione del computer SQL Server per l'accesso remoto

Dopo l'accesso al computer con desktop remoto, configurare SQL Server in base alle istruzioni fornite in [passaggi per la configurazione di connettività di SQL Server in macchine Virtuali un Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare istruzioni aggiuntive per il provisioning di macchine virtuali con PowerShell nella [documentazione macchine virtuali](virtual-machines-windows-classic-create-powershell.md). Per script aggiuntivi relativi a SQL Server e lo spazio di archiviazione Premium, vedere [Usare l'archiviazione Premium Azure con SQL Server in macchine virtuali](virtual-machines-windows-classic-sql-server-premium-storage.md).

In molti casi, il passaggio successivo consiste per eseguire la migrazione dei database per la nuova macchina virtuale di SQL Server. Per istruzioni sulla migrazione di database, vedere [migrazione di un Database di SQL Server in macchine Virtuali un Azure](virtual-machines-windows-migrate-sql.md).

Se si desidera anche tramite il portale di Azure per creare macchine virtuali di SQL, vedere [il Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md). Si noti che l'esercitazione che illustra il portale crea macchine virtuali utilizzando il modello di gestione risorse consigliato, anziché il modello classico utilizzati in questo argomento PowerShell.

Oltre a queste risorse, è consigliabile esaminare [altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
