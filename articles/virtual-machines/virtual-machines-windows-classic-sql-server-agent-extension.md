<properties
    pageTitle="Estensione di agente SQL Server per macchine virtuali SQL Server (classico) | Microsoft Azure"
    description="In questo argomento viene descritto come gestire l'estensione di agente SQL Server, che consente di automatizzare le attività amministrative specifiche di SQL Server. Sono incluse Backup automatico, automatico patch e l'integrazione di archivio di Azure chiave. Questo argomento viene utilizzata la modalità di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Estensione di agente SQL Server per macchine virtuali SQL Server (classica)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-server-agent-extension.md)
- [Classica](virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione di SQL Server IaaS agente (SQLIaaSAgent) viene eseguito su Azure macchine virtuali di automatizzare le attività amministrative. In questo argomento viene fornita una panoramica di servizi supportati dall'estensione, nonché le istruzioni per l'installazione, stato e la rimozione.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per visualizzare la versione di Manager delle risorse di questo articolo, vedere [Gestione delle risorse per SQL Server macchine virtuali l'estensione agente SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servizi supportati

L'estensione agente di SQL Server IaaS supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
|---------------------|-------------------------------|
| **SQL automatizzato di Backup** | Consente di automatizzare la programmazione di backup per tutti i database per l'istanza predefinita di SQL Server nella macchina virtuale. Per ulteriori informazioni, vedere [automatizzato di backup per SQL Server in macchine virtuali di Azure (classico)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatizzato patch** | Consente di configurare una finestra di manutenzione durante il quale gli aggiornamenti per la macchina virtuale avvengono, in modo che è possibile evitare gli aggiornamenti durante le ore di punta per il carico di lavoro. Per ulteriori informazioni, vedere [automatizzato patch per SQL Server in macchine virtuali di Azure (classico)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integrazione di Azure archivio chiave** | Consente di installare e configurare Azure chiave archivio in di una macchina SQL Server virtuale automaticamente. Per ulteriori informazioni, vedere [Configurare l'integrazione di archivio di Azure chiave per SQL Server in macchine virtuali di Azure (classico)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Prerequisiti

Requisiti per l'estensione agente IaaS di SQL Server, scegliere la macchina virtuale:

### <a name="operating-system"></a>Sistema operativo:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Versioni di SQL Server:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:

[Scaricare e configurare i comandi di PowerShell Azure più recente](../powershell-install-configure.md).

Avviare Windows PowerShell e connetterlo al proprio abbonamento Azure con il comando **Aggiungi AzureAccount** .

    Add-AzureAccount

Se si hanno più abbonamenti, usare **Selezionare AzureSubscription** per selezionare l'abbonamento che contiene la destinazione macchine Virtuali classica.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

A questo punto è possibile ottenere un elenco di macchine virtuali classiche e i relativi nomi di servizio associato con il comando **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installazione

Per le macchine virtuali classiche, è necessario utilizzare PowerShell per installare l'estensione agente IaaS di SQL Server e configurare i servizi associati. Utilizzare il cmdlet di PowerShell **Set AzureVMSqlServerExtension** per installare l'estensione. Ad esempio, il comando seguente installa l'estensione in una macchina virtuale Server di Windows (classico) e nomi "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se si aggiorna la versione più recente dell'estensione agente IaaS SQL, è necessario riavviare il computer virtuale dopo l'aggiornamento dell'estensione.

>[AZURE.NOTE] Macchine virtuali classiche non dispone di un'opzione per installare e configurare l'estensione agente IaaS SQL Server tramite il portale.

## <a name="status"></a>Stato

Un modo per verificare che sia installato l'estensione consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **tutte le impostazioni** in e il computer virtuale e quindi fare clic su **estensioni**. Verrà visualizzata l'estensione **SQLIaaSAgent** elencato.

![Estensione SQL Server agente IaaS nel portale di Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

È anche possibile utilizzare il cmdlet di Azure Powershell **Get-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Rimozione   

Nel portale di Azure, è possibile disinstallare l'estensione facendo clic sui puntini di sospensione nella e **estensioni** delle proprietà di macchina virtuale. Fare clic su **Elimina**.

![Disinstallare l'estensione di IaaS agente SQL Server nel portale di Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

È anche possibile utilizzare il cmdlet di Powershell **AzureVMSqlServerExtension Rimuovi** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi

Iniziare a utilizzare uno dei servizi supportati dall'estensione. Per ulteriori informazioni, vedere gli argomenti nella sezione [servizi supportati](#supported-services) alla fine di questo articolo.

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
