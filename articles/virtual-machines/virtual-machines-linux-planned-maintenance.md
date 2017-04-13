<properties
    pageTitle="Manutenzione pianificata per macchine virtuali Linux | Microsoft Azure"
    description="Informazioni su quali interventi di manutenzione pianificata Azure è e come influisce macchine virtuali Linux in esecuzione in Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Manutenzione pianificata per macchine virtuali Linux in Azure

Informazioni su quali interventi di manutenzione pianificata Azure è e come può influire la disponibilità di macchine virtuali Linux. In questo articolo è disponibile anche per [macchine virtuali di Windows](virtual-machines-windows-planned-maintenance.md). 

In questo articolo offre uno sfondo per il processo di Azure interventi di manutenzione pianificata. Se si desiderano risolvere i problemi perché la macchina virtuale riavviato, è possibile [leggere il blog post dettaglio visualizzazione macchine Virtuali riavviare registri](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Perché Azure esegue la manutenzione pianificata

Microsoft Azure esegue periodicamente gli aggiornamenti in tutto il mondo per migliorare l'affidabilità, prestazioni e protezione dell'infrastruttura di host sottostante macchine virtuali. Molti degli aggiornamenti vengono eseguiti senza un impatto al macchine virtuali o servizi Cloud, inclusi gli aggiornamenti mantenimento della memoria.

Tuttavia, alcuni aggiornamenti necessario riavviare alle macchine virtuali per applicare gli aggiornamenti all'infrastruttura. Macchine virtuali arrestate mentre è patch l'infrastruttura e riavviare le macchine virtuali.

Si noti che sono disponibili due tipi di manutenzione che possono influire sulla disponibilità di macchine virtuali: pianificato e non pianificato. Questa pagina illustra come Microsoft Azure esegue la manutenzione pianificata. Per ulteriori informazioni sulla manutenzione non pianificata, vedere [informazioni pianificato e la manutenzione non pianificata](virtual-machines-linux-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
