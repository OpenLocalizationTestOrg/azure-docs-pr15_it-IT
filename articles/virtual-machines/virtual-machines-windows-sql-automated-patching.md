<properties
    pageTitle="Automatico patch per macchine virtuali SQL Server (Manager delle risorse) | Microsoft Azure"
    description="Illustra la caratteristica automatizzato patch per SQL Server macchine virtuali in esecuzione in Azure Gestione risorse."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatico patch per SQL Server in macchine virtuali Azure (Manager delle risorse)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-automated-patching.md)
- [Classica](virtual-machines-windows-classic-sql-automated-patching.md)

Applicazione di patch automatizzato stabilisce una finestra di manutenzione per una macchina di virtuale di Azure che esegue SQL Server. Aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, questo rescriction garantisce che gli aggiornamenti del sistema e qualsiasi riavvio associato si verifica a intervalli di tempo meglio possibile per il database. Applicazione di patch automatizzato dipende dall' [Estensione agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica. Per visualizzare la versione classica di questo articolo, vedere [Automatizzato patch per SQL Server in macchine virtuali di Azure classica](virtual-machines-windows-classic-sql-automated-patching.md).

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare automatizzato patch, considerare i prerequisiti seguenti:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versione di SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- Se si intende configurare automatizzato patch con PowerShell per [installare i comandi di PowerShell Azure più recente](../powershell-install-configure.md) .

>[AZURE.NOTE] Applicazione di patch automatica si basa sull'estensione agente IaaS di SQL Server. Immagini di raccolta macchina virtuale SQL corrente aggiungere l'estensione per impostazione predefinita. Per ulteriori informazioni, vedere [Estensione agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni

Nella tabella seguente descrive le opzioni che possono essere configurate per l'aggiornamento automatico. I passaggi di configurazione effettivo variano a seconda che si utilizzi il portale di Azure o i comandi di PowerShell di Windows Azure.

|Impostazione|Valori possibili|Descrizione|
|---|---|---|
|**Automatico patch**|Attivare/disattivare (disattivato)|Abilita o Disabilita aggiornamento automatico per una macchina virtuale Azure.|
|**Pianificazione della manutenzione**|Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica|Pianificazione per il download e installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale.|
|**Ora di inizio di manutenzione**|0-24|Ora di inizio locale per aggiornare la macchina virtuale.|
|**Durata finestra manutenzione**|30-180|Il numero di minuti consentito per completare il download e installazione degli aggiornamenti.|
|**Categoria patch**|Importante|Categoria degli aggiornamenti di scaricare e installare.|

## <a name="configuration-in-the-portal"></a>Configurazione del portale
È possibile usare il portale di Azure per configurare automatizzato patch durante il provisioning o per macchine virtuali esistenti.

### <a name="new-vms"></a>Nuove macchine virtuali
Usare il portale di Azure per configurare l'aggiornamento automatico quando si crea una nuova macchina virtuale di SQL Server nel modello di distribuzione Manager delle risorse.

Selezionare **aggiornamento automatico**e **l'impostazioni di SQL Server** . Nella schermata portale Azure che segue mostra e **l'Automatizzato patch SQL** .

![SQL automatizzato patch nel portale di Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Per il contesto, vedere l'argomento completo sul [provisioning una macchina virtuale di SQL Server Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Macchine virtuali esistenti
Per macchine virtuali di SQL Server esistenti, selezionare la macchina virtuale di SQL Server. Quindi selezionare la sezione **configurazione SQL Server** della stessa e **Impostazioni** .

![Aggiornamento automatico SQL per macchine virtuali esistenti](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Selezionare il pulsante **Modifica** automatica patch sezione e il **configurazione SQL Server** .

![Configurazione automatica patch SQL per macchine virtuali esistenti](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Al termine, fare clic sul pulsante **OK** nella parte inferiore della stessa e **configurazione di SQL Server** per salvare le modifiche.

Se si desidera consentire l'aggiornamento automatico per la prima volta, l'agente IaaS SQL Azure configura in background. Durante questo periodo, il portale di Azure potrebbe non essere visualizzati che automatizzato patch sia configurato. Attendere alcuni minuti per l'agente per l'installazione, configurazione. Dopo che il portale di Azure riflette le nuove impostazioni.

>[AZURE.NOTE] È inoltre possibile configurare automatizzato patch utilizzando un modello. Per ulteriori informazioni, vedere [Guida introduttiva di Azure modello per automatizzato patch](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell

Dopo il provisioning del VM SQL, usare PowerShell per configurare l'aggiornamento automatico.

Nell'esempio seguente viene utilizzato PowerShell per configurare automatizzato patch su una macchina virtuale Server SQL esistente. Il comando **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig** consente di configurare una nuova finestra di manutenzione per gli aggiornamenti automatici.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

In base a questo esempio, la tabella seguente descrive l'effetto pratico nella destinazione macchine Virtuali di Azure:

|Parametro|Effetto|
|---|---|
|**DayOfWeek**|Patch installate ogni giovedì.|
|**MaintenanceWindowStartingHour**|Inizio Aggiorna alle 11:00.|
|**MaintenanceWindowsDuration**|Patch devono essere installate all'interno di 120 minuti. In base alla data di inizio, devono completare da 1:00 pm.|
|**PatchCategory**|L'impostazione possibile solo per questo parametro è **importante**.|

Potrebbe richiedere alcuni minuti per installare e configurare l'agente IaaS di SQL Server.

Per disattivare l'aggiornamento automatico, eseguire lo stesso script senza il **-abilitare** parametro **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**. Assenza del **-abilitare** parametro segnala il comando per disattivare la caratteristica.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre attività di automazione disponibili, vedere [Estensione agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
