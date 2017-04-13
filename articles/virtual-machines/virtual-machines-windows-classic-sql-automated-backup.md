<properties
    pageTitle="Backup automatizzato per macchine virtuali SQL Server (classico) | Microsoft Azure"
    description="Illustra la caratteristica di Backup automatico per SQL Server in esecuzione in macchine virtuali di Azure Gestione risorse. "
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
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatico per SQL Server in macchine virtuali Azure (classica)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-automated-backup.md)
- [Classica](virtual-machines-windows-classic-sql-automated-backup.md)

Backup automatico configura automaticamente [Backup gestiti da Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi o esistenti in una macchina virtuale Azure che esegue SQL Server 2014 Standard o Enterprise. In questo modo è possibile configurare backup periodici del database che utilizzano permanente archiviazione blob Azure. Backup automatico dipende dall' [Estensione agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per visualizzare la versione di Manager delle risorse di questo articolo, vedere [Copia di Backup automatico per SQL Server in Gestione risorse macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare il Backup automatico, considerare i prerequisiti seguenti:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versione/edizione di SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

>[AZURE.NOTE] SQL Server 2016 non è ancora supportate per il Backup automatico.

**Configurazione del database**:

- Database di destinazione devono utilizzare il modello di recupero completo.

**Azure PowerShell**:

- [Installare i comandi di PowerShell Azure più recente](../powershell-install-configure.md).

**Estensione IaaS SQL Server**:

- [Installare l'estensione IaaS SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni

Nella tabella seguente descrive le opzioni che possono essere configurate per il Backup automatico. Per le macchine virtuali classiche, è necessario utilizzare PowerShell per configurare queste impostazioni.

|Impostazione|Intervallo (impostazione predefinita)|Descrizione|
|---|---|---|
|**Backup automatico**|Attivare/disattivare (disattivato)|Abilita o disabilita il Backup automatico per una macchina virtuale Azure che esegue SQL Server 2014 Standard o Enterprise.|
|**Periodo di conservazione**|1-30 giorni (30 giorni)|Il numero di giorni per la conservazione di una copia di backup.|
|**Account di archiviazione**|Spazio di archiviazione Azure account (lo spazio di archiviazione creati per la macchina virtuale specificata)|Un account di archiviazione Azure da utilizzare per l'archiviazione dei file di Backup automatico in archiviazione blob. In questa posizione per l'archiviazione di tutti i file di backup viene creato un contenitore. La convenzione di denominazione di file di backup include la data, ora e il nome del computer.|
|**Crittografia**|Attivare/disattivare (disattivato)|Attiva o disattiva la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup si trovano in account di archiviazione specificato nello stesso contenitore automaticbackup utilizzando la stessa convenzione di denominazione. Se viene modificata la password, viene generato un nuovo certificato con la password, ma il certificato precedente rimane per ripristinare backup precedenti.|
|**Password**|Casella di testo password (nessuno)|Una password per le chiavi di crittografia. Questo è richiesto se è attivata la crittografia. Per ripristinare una crittografati, è necessario disporre certificato correlato che è stato utilizzato al momento in cui che è stato eseguito il backup e la password corretta.|

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell

Nell'esempio di PowerShell riportato di seguito Backup automatico è configurato per un esistente 2014 macchine Virtuali di SQL Server. Il comando **Nuovo AzureVMSqlServerAutoBackupConfig** Configura le impostazioni di Backup automatico per la memorizzazione di backup nella finestra account di archiviazione Azure specificato dalla variabile $storageaccount. Questi backup verranno mantenuti per 10 giorni. Il comando **Set AzureVMSqlServerExtension** Aggiorna la macchina virtuale Azure specificato con queste impostazioni.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Potrebbe richiedere alcuni minuti per installare e configurare l'agente IaaS di SQL Server.

Per abilitare la crittografia, modificare lo script precedente per passare il parametro EnableEncryption con una password (stringa protetta) per il parametro CertificatePassword. Il seguente script abilita le impostazioni di Backup automatico dell'esempio precedente e aggiunge la crittografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Per disattivare il backup automatico, eseguire lo stesso script senza il **-abilitare** parametro per il **Nuovo AzureVMSqlServerAutoBackupConfig**. Con l'installazione, è possibile richiedere alcuni minuti per disabilitare il Backup automatico.

>[AZURE.NOTE] La disattivazione e la disinstallazione di SQL Server IaaS Agent non rimuove le impostazioni di Backup gestiti configurate in precedenza. È consigliabile disattivare il Backup automatico prima di disabilitare o disinstallare l'agente IaaS di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

Backup automatico Configura Backup gestiti in macchine virtuali di Azure. È importante [esaminare la documentazione per il Backup gestiti](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare ulteriori backup e ripristino indicazioni per SQL Server in macchine virtuali di Azure nell'argomento seguente: [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

Per informazioni su altre attività di automazione disponibili, vedere [Estensione agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
