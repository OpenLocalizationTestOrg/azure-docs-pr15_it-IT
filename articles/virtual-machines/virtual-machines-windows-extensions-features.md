<properties
 pageTitle="Caratteristiche e le estensioni macchina virtuale | Microsoft Azure"
 description="Informazioni sulle estensioni disponibili per le macchine virtuali di Azure, raggruppati in base informazioni forniscono o migliorare."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Informazioni sulle funzionalità e le estensioni macchina virtuale

## <a name="azure-vm-extensions"></a>Estensioni macchine Virtuali di Azure

Azure macchina virtuale estensioni sono piccole applicazioni che forniscono attività di automazione e configurazione di distribuzione post in macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede software per la protezione antivirus e installata o configurazione Docker, un'estensione macchine Virtuali utilizzabile per completare queste attività. Azure estensioni macchine Virtuali possono essere eseguite tramite CLI Azure, PowerShell, modelli di gestione delle risorse e il portale Azure. Estensioni dotate di una nuova distribuzione macchina virtuale, o eseguire qualsiasi sistema esistente.

Questo documento offre i prerequisiti per estensione macchina virtuale Azure e indicazioni su come rilevare estensioni macchine Virtuali disponibili. 

## <a name="azure-vm-agent"></a>Agente di macchine Virtuali di Azure

Agente di macchine Virtuali di Azure gestisce interazione tra una macchina virtuale Azure e il Controller dell'infrastruttura di Azure. Agente di macchine Virtuali è tenuto a molti aspetti funzionali di distribuzione e gestione di macchine virtuali di Azure, incluse l'esecuzione di macchine Virtuali estensioni. L'agente di macchine Virtuali di Azure è preinstallato in Azure raccolta immagini e può essere installato nei sistemi operativi supportati. 

Per informazioni su sistemi operativi supportati e le istruzioni di installazione, vedere [Agente di Azure macchina virtuale](./virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Alla scoperta di macchine Virtuali estensioni

Sono disponibili per l'utilizzo con macchine virtuali di Azure molti estensioni macchine Virtuali diversi. Per visualizzare un elenco completo, eseguire il comando seguente con CLI Azure, sostituendo il percorso con il percorso desiderato.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Estensioni macchine Virtuali comuni

|Estensione   |Descrizione   |Ulteriori informazioni   |
|---|---|---|
|Estensione di Script personalizzati per Windows  | Eseguire gli script rispetto a una macchina virtuale Azure  |[Estensione di Script personalizzati per Windows](./virtual-machines-windows-extensions-customscript.md)   |
|Estensione DSC per Windows | Estensione di PowerShell DSC (configurazione stata desiderata).  | [Estensione macchine Virtuali docker](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Estensione di diagnostica Windows Azure | Gestire diagnostica Windows Azure |[Estensione di diagnostica Windows Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
