<properties
    pageTitle="Diversi modi per creare una macchina virtuale di Windows | Microsoft Azure"
    description="Sono elencati i diversi metodi di creazione di una macchina virtuale di Windows con Gestione risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Diversi modi per creare una macchina virtuale di Windows con Gestione risorse

Azure offre diversi modi per creare una macchina virtuale perché macchine virtuali sono adatte per diversi utenti e scopi. Questo significa che è necessario apportare alcune opzioni sul computer virtuale e su come crearlo. In questo articolo offre un riepilogo di queste opzioni e collegamenti a istruzioni.

## <a name="azure-portal"></a>Portale di Azure

Tramite il portale di Azure è un modo semplice per provare una macchina virtuale, soprattutto se sta pochi con Azure. 

[Creare una macchina virtuale che esegue Windows tramite il portale](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Modello

Macchine virtuali richiedono una combinazione di risorse (ad esempio una disponibilità set e gli account di archiviazione). Invece di distribuzione e gestione di ogni risorsa separatamente, è possibile creare un modello di gestione risorse Azure che distribuisce ed esegue il provisioning di tutte le risorse in un'operazione su una singola e coordinata.

- [Creare una macchina virtuale di Windows con un modello di Manager delle risorse](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>PowerShell Azure

Se si desidera utilizzare in una shell comandi, è possibile usare PowerShell Azure.

- [Creare una macchina virtuale di Windows tramite PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Utilizzare Visual Studio per creare, gestire e distribuire macchine virtuali con Azure Tools per Visual Studio e Azure SDK.

[Azure Tools per Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

