<properties
    pageTitle="Estensione di agente SQL Server per macchine virtuali SQL Server (Manager delle risorse) | Microsoft Azure"
    description="In questo argomento viene descritto come gestire l'estensione di agente SQL Server, che consente di automatizzare le attività amministrative specifiche di SQL Server. Sono incluse Backup automatico, automatico patch e l'integrazione di archivio di Azure chiave. Questo argomento viene utilizzata la modalità di distribuzione di Manager delle risorse."
    services="virtual-machines-windows"
    documentationCenter=""
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
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>Estensione di agente SQL Server per macchine virtuali SQL Server (Manager delle risorse)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-server-agent-extension.md)
- [Classica](virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione di SQL Server IaaS agente (SQLIaaSExtension) viene eseguito su Azure macchine virtuali di automatizzare le attività amministrative. In questo argomento viene fornita una panoramica di servizi supportati dall'estensione, nonché le istruzioni per l'installazione, stato e la rimozione.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica. Per visualizzare la versione classica di questo articolo, vedere [L'estensione agente SQL Server per SQL Server macchine virtuali classica](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servizi supportati

L'estensione agente di SQL Server IaaS supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
|---------------------|-------------------------------|
| **SQL automatizzato di Backup** | Consente di automatizzare la programmazione di backup per tutti i database per l'istanza predefinita di SQL Server nella macchina virtuale. Per ulteriori informazioni, vedere [copia di backup automatico per SQL Server in macchine virtuali di Azure (Manager delle risorse)](virtual-machines-windows-sql-automated-backup.md).|
| **SQL automatizzato patch** | Consente di configurare una finestra di manutenzione durante il quale gli aggiornamenti per la macchina virtuale avvengono, in modo che è possibile evitare gli aggiornamenti durante le ore di punta per il carico di lavoro. Per ulteriori informazioni, vedere [automatizzato patch per SQL Server in macchine virtuali di Azure (Manager delle risorse)](virtual-machines-windows-sql-automated-patching.md).|
| **Integrazione di Azure archivio chiave** | Consente di installare e configurare Azure chiave archivio in di una macchina SQL Server virtuale automaticamente. Per ulteriori informazioni, vedere [Configurare l'integrazione di archivio di Azure chiave per SQL Server in macchine virtuali di Azure (Manager delle risorse)](virtual-machines-windows-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Prerequisiti

Requisiti per l'estensione agente IaaS di SQL Server, scegliere la macchina virtuale:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versioni di SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Scaricare e configurare i comandi di PowerShell Azure più recente](../powershell-install-configure.md)

## <a name="installation"></a>Installazione

L'estensione agente di SQL Server IaaS viene installato automaticamente quando viene effettuato il provisioning di una delle immagini di raccolta macchina virtuale di SQL Server.

Se si crea una macchina virtuale solo del sistema operativo Windows Server, è possibile installare manualmente l'estensione utilizzando il cmdlet di PowerShell **Set-AzureVMSqlServerExtension** . Ad esempio, il comando seguente installa l'estensione in una sola del sistema operativo Windows Server macchina virtuale e nomi "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Se si aggiorna la versione più recente dell'estensione agente IaaS SQL, è necessario riavviare il computer virtuale dopo l'aggiornamento dell'estensione.

>[AZURE.NOTE] Se si installa l'estensione agente di SQL Server IaaS manualmente in una macchina virtuale Server di Windows, è necessario utilizzare e gestire le funzionalità usando i comandi di PowerShell. L'interfaccia portale è disponibile solo per SQL Server raccolta immagini.

## <a name="status"></a>Stato

Un modo per verificare che sia installato l'estensione consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **tutte le impostazioni** in e il computer virtuale e quindi fare clic su **estensioni**. Verrà visualizzata l'estensione **SQLIaaSExtension** elencato.

![Estensione SQL Server agente IaaS nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile utilizzare il cmdlet di Azure Powershell **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Il comando precedente conferma l'agente è installato e vengono fornite informazioni generali. È anche possibile ottenere specifiche informazioni sullo stato eseguire il Backup automatico e applicazione di patch con i comandi seguenti.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Rimozione   

Nel portale di Azure, è possibile disinstallare l'estensione facendo clic sui puntini di sospensione nella e **estensioni** delle proprietà di macchina virtuale. Fare clic su **Elimina**.

![Disinstallare l'estensione di IaaS agente SQL Server nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile utilizzare il cmdlet di Powershell **AzureRmVMSqlServerExtension Rimuovi** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Passaggi successivi

Iniziare a utilizzare uno dei servizi supportati dall'estensione. Per ulteriori informazioni, vedere gli argomenti nella sezione [servizi supportati](#supported-services) alla fine di questo articolo.

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
