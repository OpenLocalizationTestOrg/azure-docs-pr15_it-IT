<properties
    pageTitle="Gestire la disponibilità di macchine virtuali Linux | Microsoft Azure"
    description="Informazioni su come utilizzare più macchine virtuali per garantire disponibilità per l'applicazione di Linux in Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/25/2016"
    ms.author="cynthn"/>

# <a name="manage-the-availability-of-virtual-machines"></a>Gestire la disponibilità di macchine virtuali

Informazioni su come configurare e gestire più macchine virtuali per garantire disponibilità per l'applicazione di Linux in Azure. È inoltre possibile [gestire la disponibilità di macchine virtuali di Windows](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Per istruzioni sulla creazione di una disponibilità impostata utilizzando CLI nel modello di distribuzione Manager delle risorse, vedere [availset azure: comandi per gestire i set di disponibilità](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[AZURE.INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle macchine virtuali di bilanciamento del carico, vedere [il bilanciamento del carico macchine virtuali](virtual-machines-linux-load-balance.md).
