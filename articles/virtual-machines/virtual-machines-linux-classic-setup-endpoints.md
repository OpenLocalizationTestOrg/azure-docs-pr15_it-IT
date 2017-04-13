<properties
    pageTitle="Configurare gli endpoint su un VM Linux classica | Microsoft Azure"
    description="Informazioni su come configurare i punti finali per un VM Linux nel portale di classica Azure consente la comunicazione con una macchina virtuale Linux in Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Come configurare i punti finali in un computer virtuale classico Linux in Azure

Tutte le macchine virtuali di Linux creati in Azure utilizzando il modello di distribuzione classica automaticamente è possibile comunicare tramite un canale di rete privata con altre macchine virtuali nel servizio cloud stesso o virtuali. Computer su Internet o altre reti virtuali richiedono tuttavia i punti finali per indirizzare il traffico di rete in ingresso a una macchina virtuale. In questo articolo è disponibile anche per [macchine virtuali di Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Nel modello di distribuzione di **Manager delle risorse** , i punti finali sono configurati con i **Gruppi di sicurezza di rete (NSGs)**. Per ulteriori informazioni, vedere [apertura delle porte e i punti finali] (virtual-macchine-linux-nsg-quickstart.md).

Quando si crea una macchina virtuale Linux nel portale di classica Azure, un endpoint per Secure Shell (SSH) è in genere creato automaticamente. È possibile configurare altri endpoint durante la creazione di macchina virtuale o in un secondo momento in base alle esigenze.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passaggi successivi

* È anche possibile creare un endpoint macchine Virtuali utilizzando l' [interfaccia di riga di comando di Azure](../virtual-machines-command-line-tools.md). Eseguire il comando **Crea endpoint macchine virtuali di azure** .

* Se è stato creato una macchina virtuale nel modello di distribuzione Manager delle risorse, è possibile usare CLI Azure in modalità di gestione risorse per [creare gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-cli.md) per controllare il traffico per la macchina virtuale.