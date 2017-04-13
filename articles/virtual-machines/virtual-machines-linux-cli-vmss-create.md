<properties
    pageTitle="Quali sono scala macchine Virtuali imposta? | Microsoft Azure"
    description="Informazioni sui set di scala macchine Virtuali."
    keywords="Imposta scala macchina virtuale macchina virtuale Linux," 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>Quali sono scala macchina virtuale imposta?

Macchina virtuale scala set consentono di gestire più macchine virtuali come set. Un alto livello, scala set hanno seguenti Pro e contro:

Professionisti IT:

1. Disponibilità. Ogni set scala inserisce la macchine virtuali in un Set di disponibilità con 5 guasto domini (FDs) e 5 aggiornamento (UDs) per assicurare la disponibilità (per ulteriori informazioni sui FDs e UDs, vedere [la disponibilità di macchine Virtuali](./virtual-machines-linux-manage-availability.md)). 
2. Integrazione con Azure di bilanciamento del carico e al Gateway di App.
3. Integrazione con Azure Autoscale.
4. La distribuzione, gestione e pulire di macchine virtuali.
5. Supporta caratteristiche comuni di Windows e Linux, come immagini personalizzate.

Svantaggi:

1. Non è possibile allegare dischi di dati a istanze di macchine Virtuali in un set di scala. È necessario utilizzare lo spazio di archiviazione Blob, file di Azure, tabelle Azure o altra soluzione di archiviazione.

## <a name="quick-create-using-azure-cli"></a>Creazione rapida utilizzando CLI Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni generali, visitare la [pagina principale per i set di scala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Per ulteriori informazioni, vedere [Imposta pagina della documentazione principale per scala](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Ad esempio modelli Manager delle risorse utilizzando set scala, cercare "vmss" in [modelli di Guida introduttiva di Azure github repo](https://github.com/Azure/azure-quickstart-templates).

