<properties
    pageTitle="Backup automatizzato per macchine virtuali SQL Server (Manager delle risorse) | Microsoft Azure"
    description="Illustra la caratteristica di Backup automatico per SQL Server in esecuzione in macchine virtuali di Azure Gestione risorse. "
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
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Backup automatizzato per SQL Server in macchine virtuali Azure (Manager delle risorse)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-automated-backup.md)
- [Classica](virtual-machines-windows-classic-sql-automated-backup.md)

Backup automatico configura automaticamente [Backup gestiti da Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi o esistenti in una macchina virtuale Azure che esegue SQL Server 2014 Standard o Enterprise. In questo modo è possibile configurare backup periodici del database che utilizzano permanente archiviazione blob Azure. Backup automatico dipende dall' [Estensione agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica. Per visualizzare la versione classica di questo articolo, vedere [Copia di Backup automatico per SQL Server in macchine virtuali di Azure classica](virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Prerequisiti di

Per utilizzare il Backup automatico, considerare i prerequisiti seguenti:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versione/edizione di SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

**Configurazione del database**:

- Database di destinazione devono utilizzare il modello di recupero completo

**Azure PowerShell**:

- Se si prevede di configurare il Backup automatico con PowerShell per [installare i comandi di PowerShell Azure più recente](../powershell-install-configure.md) .

>[AZURE.NOTE] Backup automatico si basa sull'estensione agente IaaS di SQL Server. Immagini di raccolta macchina virtuale SQL corrente aggiungere l'estensione per impostazione predefinita. Per ulteriori informazioni, vedere [Estensione agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni

Nella tabella seguente descrive le opzioni che possono essere configurate per il Backup automatico. I passaggi di configurazione effettivo variano a seconda che si utilizzi il portale di Azure o i comandi di PowerShell di Windows Azure.

|Impostazione|Intervallo (impostazione predefinita)|Descrizione|
|---|---|---|
|**Backup automatico**|Attivare/disattivare (disattivato)|Abilita o disabilita il Backup automatico per una macchina virtuale Azure che esegue SQL Server 2014 Standard o Enterprise.|
|**Periodo di conservazione**|1-30 giorni (30 giorni)|Il numero di giorni per la conservazione di una copia di backup.|
|**Account di archiviazione**|Spazio di archiviazione Azure account (lo spazio di archiviazione creati per la macchina virtuale specificata)|Un account di archiviazione Azure da utilizzare per l'archiviazione dei file di Backup automatico in archiviazione blob. In questa posizione per l'archiviazione di tutti i file di backup viene creato un contenitore. La convenzione di denominazione di file di backup include la data, ora e il nome del computer.|
|**Crittografia**|Attivare/disattivare (disattivato)|Attiva o disattiva la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup si trovano in account di archiviazione specificato nello stesso contenitore automaticbackup utilizzando la stessa convenzione di denominazione. Se viene modificata la password, viene generato un nuovo certificato con la password, ma il certificato precedente rimane per ripristinare backup precedenti.|
|**Password**|Casella di testo password (nessuno)|Una password per le chiavi di crittografia. Questo è richiesto se è attivata la crittografia. Per ripristinare una crittografati, è necessario disporre certificato correlato che è stato utilizzato al momento in cui che è stato eseguito il backup e la password corretta.|

## <a name="configuration-in-the-portal"></a>Configurazione del portale
È possibile utilizzare il portale di Azure per configurare il Backup automatico durante il provisioning o per macchine virtuali esistenti.

### <a name="new-vms"></a>Nuove macchine virtuali
Usare il portale di Azure per configurare il Backup automatico quando si crea una nuova macchina virtuale di SQL Server 2014 nel modello di distribuzione Manager delle risorse.

Selezionare **automatizzato di backup**e **l'impostazioni di SQL Server** . Nella schermata portale Azure che segue mostra e il **Backup automatico SQL** .

![Configurazione di Backup automatico di SQL Azure portale](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Per il contesto, vedere l'argomento completo sul [provisioning una macchina virtuale di SQL Server Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Macchine virtuali esistenti
Per macchine virtuali di SQL Server esistenti, selezionare la macchina virtuale di SQL Server. Quindi selezionare la sezione **configurazione SQL Server** della stessa e **Impostazioni** .

![Backup automatico SQL per macchine virtuali esistenti](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

In e il **configurazione SQL Server** , fare clic sul pulsante **Modifica** nella sezione backup automatico.

![Configurare il Backup automatico di SQL per macchine virtuali esistenti](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Al termine, fare clic sul pulsante **OK** nella parte inferiore della stessa e **configurazione di SQL Server** per salvare le modifiche.

Se si desidera consentire il Backup automatico per la prima volta, l'agente IaaS SQL Azure configura in background. Durante questo periodo, il portale di Azure non risulti che il Backup automatico sia configurato. Attendere alcuni minuti per l'agente per l'installazione, configurazione. Dopo che il portale di Azure rifletteranno le nuove impostazioni.

>[AZURE.NOTE] È inoltre possibile configurare il Backup automatico utilizzando un modello. Per ulteriori informazioni, vedere [Guida introduttiva di Azure modello per il Backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell

Dopo il provisioning del VM SQL, usare PowerShell per configurare il Backup automatico.

Nell'esempio di PowerShell riportato di seguito Backup automatico è configurato per un esistente 2014 macchine Virtuali di SQL Server. Il comando **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** Configura le impostazioni di Backup automatico per la memorizzazione di backup nella finestra account di archiviazione Azure associato alla macchina virtuale. Questi backup verranno mantenuti per 10 giorni. Il comando **Set AzureRmVMSqlServerExtension** Aggiorna la macchina virtuale Azure specificato con queste impostazioni.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Potrebbe richiedere alcuni minuti per installare e configurare l'agente IaaS di SQL Server.

Per abilitare la crittografia, modificare lo script precedente per passare il parametro **EnableEncryption** con una password (stringa protetta) per il parametro **CertificatePassword** . Il seguente script abilita le impostazioni di Backup automatico dell'esempio precedente e aggiunge la crittografia.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Per disattivare il backup automatico, eseguire lo stesso script senza il **-abilitare** parametro per il comando **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** . Assenza del **-abilitare** parametro segnala il comando per disattivare la caratteristica. Con l'installazione, è possibile richiedere alcuni minuti per disabilitare il Backup automatico.

>[AZURE.NOTE] Rimozione di SQL Server IaaS Agent non rimuove le impostazioni di Backup automatico configurate in precedenza. Prima di disabilitare o disinstallare l'agente IaaS di SQL Server, è necessario disattivare Backup automatico.

## <a name="next-steps"></a>Passaggi successivi

Backup automatico Configura Backup gestiti in macchine virtuali di Azure. È importante [esaminare la documentazione per il Backup gestiti](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare ulteriori backup e ripristino indicazioni per SQL Server in macchine virtuali di Azure nell'argomento seguente: [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

Per informazioni su altre attività di automazione disponibili, vedere [Estensione agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
