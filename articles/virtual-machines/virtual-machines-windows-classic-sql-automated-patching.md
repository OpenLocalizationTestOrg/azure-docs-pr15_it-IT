<properties
    pageTitle="Automatico patch per macchine virtuali SQL Server (classico) | Microsoft Azure"
    description="Illustra la caratteristica automatizzato patch per SQL Server macchine virtuali in esecuzione in Azure utilizzando la modalità di distribuzione classica."
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

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatico patch per SQL Server in macchine virtuali Azure (classica)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-automated-patching.md)
- [Classica](virtual-machines-windows-classic-sql-automated-patching.md)

Applicazione di patch automatizzato stabilisce una finestra di manutenzione per una macchina di virtuale di Azure che esegue SQL Server. Aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server in questo modo che gli aggiornamenti del sistema e qualsiasi riavvio associato si verifica a intervalli di tempo meglio possibile per il database. Applicazione di patch automatizzato dipende dall' [Estensione agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per visualizzare la versione di Manager delle risorse di questo articolo, vedere [Automatizzato patch per SQL Server in Gestione risorse macchine virtuali di Azure](virtual-machines-windows-sql-automated-patching.md).

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

- [Installare i comandi di PowerShell Azure più recente](../powershell-install-configure.md).

**Estensione IaaS SQL Server**:

- [Installare l'estensione IaaS SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni

Nella tabella seguente descrive le opzioni che possono essere configurate per l'aggiornamento automatico. Per le macchine virtuali classiche, è necessario utilizzare PowerShell per configurare queste impostazioni.

|Impostazione|Valori possibili|Descrizione|
|---|---|---|
|**Automatico patch**|Attivare/disattivare (disattivato)|Abilita o Disabilita aggiornamento automatico per una macchina virtuale Azure.|
|**Pianificazione della manutenzione**|Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica|Pianificazione per il download e installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale.|
|**Ora di inizio di manutenzione**|0-24|Ora di inizio locale per aggiornare la macchina virtuale.|
|**Durata finestra manutenzione**|30-180|Il numero di minuti consentito per completare il download e installazione degli aggiornamenti.|
|**Categoria patch**|Importante|Categoria degli aggiornamenti di scaricare e installare.|

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell

Nell'esempio seguente viene utilizzato PowerShell per configurare automatizzato patch su una macchina virtuale Server SQL esistente. Il comando **Nuovo AzureVMSqlServerAutoPatchingConfig** consente di configurare una nuova finestra di manutenzione per gli aggiornamenti automatici.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

In base a questo esempio, la tabella seguente descrive l'effetto pratico nella destinazione macchine Virtuali di Azure:

|Parametro|Effetto|
|---|---|
|**DayOfWeek**|Patch installate ogni giovedì.|
|**MaintenanceWindowStartingHour**|Inizio Aggiorna alle 11:00.|
|**MaintenanceWindowsDuration**|Patch devono essere installate all'interno di 120 minuti. In base alla data di inizio, devono completare da 1:00 pm.|
|**PatchCategory**|L'impostazione possibile solo per questo parametro è "Importante".|

Potrebbe richiedere alcuni minuti per installare e configurare l'agente IaaS di SQL Server.

Per disattivare l'aggiornamento automatico, eseguire lo stesso script senza - Abilita parametro per il nuovo AzureVMSqlServerAutoPatchingConfig. Con l'installazione, è possibile richiedere alcuni minuti a Disattiva automatizzato correzioni.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre attività di automazione disponibili, vedere [Estensione agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
