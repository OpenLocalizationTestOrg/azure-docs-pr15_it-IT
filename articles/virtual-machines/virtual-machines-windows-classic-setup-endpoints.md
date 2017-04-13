<properties
    pageTitle="Configurare i punti finali in una macchina virtuale Windows classico | Microsoft Azure"
    description="Informazioni su come configurare i punti finali per una macchina virtuale di Windows nel portale di classica Azure per consentire la comunicazione con una macchina virtuale di Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Come configurare i punti finali in un computer virtuale di Windows classico in Azure


Tutte le finestre macchine virtuali che si crea in Azure utilizzando il modello di distribuzione classica automaticamente è possibile comunicano tramite private canale di rete con altre macchine virtuali nel servizio cloud stesso o virtuali. Computer su Internet o altre reti virtuali richiedono tuttavia i punti finali per indirizzare il traffico di rete in ingresso a una macchina virtuale. In questo articolo è anche disponibile per [macchine virtuali Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Nel modello di distribuzione di **Manager delle risorse** , i punti finali sono configurati con i **Gruppi di sicurezza di rete (NSGs)**. Per ulteriori informazioni, vedere [Consenti accesso esterno di una macchina virtuale tramite il portale di Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Quando si crea una macchina virtuale di Windows nel portale di classica Azure, endpoint comuni come quelli per Desktop remoto e Windows PowerShell remota vengono in genere creato automaticamente. È possibile configurare altri endpoint durante la creazione di macchina virtuale o in un secondo momento in base alle esigenze.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per usare un cmdlet di PowerShell Azure per configurare un endpoint macchine Virtuali, vedere [Aggiungere AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Per usare un cmdlet di Azure PowerShell per gestire un ACL su un endpoint, vedere [gestione dell'accesso elenchi di controllo () per gli endpoint tramite PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Se è stato creato una macchina virtuale nel modello di distribuzione Manager delle risorse, è possibile utilizzare Azure PowerShell per [creare gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-ps.md) per controllare il traffico per la macchina virtuale.
