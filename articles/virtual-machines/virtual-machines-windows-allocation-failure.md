<properties
    pageTitle="Risoluzione degli errori di allocazione macchine Virtuali di Windows | Microsoft Azure"
    description="Risolvere gli errori di allocazione quando si crea, riavviare o ridimensiona una macchina virtuale di Windows in Azure"
    services="virtual-machines-windows, azure-resource-manager"
    documentationCenter=""
    authors="JiangChen79"
    manager="felixwu"
    editor=""
    tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cjiang"/>

# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Risolvere gli errori di allocazione quando si crea, riavviare o ridimensionare macchine virtuali di Windows in Azure

Quando si crea una macchina virtuale, riavviare ha smesso di macchine virtuali (deallocate) o ridimensiona una macchina virtuale, Microsoft Azure Assegna risorse di elaborazione all'abbonamento. In alcuni casi, possono verificarsi errori durante l'esecuzione di queste operazioni, anche prima che vengano raggiunti i limiti di Azure abbonamento. In questo articolo vengono illustrate le cause di alcuni degli errori di allocazione comuni e vengono suggerite possibili rimedi. Le informazioni potrebbero essere utili quando si pianifica la distribuzione dei servizi. È inoltre possibile [risolvere gli errori di allocazione quando si crea, riavviare o ridimensionare macchine virtuali Linux in Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]
