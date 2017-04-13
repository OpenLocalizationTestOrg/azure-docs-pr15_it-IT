<properties
   pageTitle="Aprire le porte a una macchina virtuale tramite il portale di Azure | Microsoft Azure"
   description="Informazioni su come aprire una porta / creare un endpoint per l'utilizzo del modello di distribuzione di manager delle risorse nel portale di Azure macchine Virtuali di Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Apertura delle porte per una macchina virtuale in Azure tramite il portale di Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandi rapidi
È anche possibile [eseguire questa procedura tramite PowerShell Azure](virtual-machines-windows-nsg-quickstart-powershell.md).

Creare innanzitutto il gruppo di sicurezza di rete. Selezionare un gruppo di risorse nel portale, fare clic su **Aggiungi**, quindi cercare e selezionare 'Gruppo di sicurezza di rete':

![Aggiungere un gruppo di sicurezza di rete](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Immettere un nome per il gruppo di sicurezza di rete, selezionare o creare un gruppo di risorse e selezionare un percorso. Fare clic su **Crea** termine:

![Creare un gruppo di sicurezza di rete](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Selezionare il nuovo gruppo di sicurezza di rete. Selezionare 'regole di protezione in ingresso', quindi fare clic sul pulsante **Aggiungi** per creare una regola:

![Aggiungere una regola in entrata](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Specificare un nome per la nuova regola. Porte 80 sono già stato immesso per impostazione predefinita. Questo blade è necessario modificare l'origine, protocollo e la destinazione durante l'aggiunta di regole aggiuntive per il gruppo di sicurezza di rete. Fare clic su **OK** per creare la regola:

![Creare una regola in entrata](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete con una subnet o un'interfaccia di rete specifico. Provare ora ad associare il gruppo di sicurezza di rete con subnet. Selezionare "Subnet", quindi fare clic su **Associa**:

![Associare un gruppo di sicurezza di rete con subnet](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Selezionare la rete virtuale e quindi selezionare la subnet appropriata:

![Associazione di un gruppo di sicurezza di rete con la rete virtuale](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

A questo punto è stato creato un gruppo di sicurezza di rete, creare una regola in entrata che consente il traffico sulla porta 80 e associato a una subnet. Macchine virtuali che si connette a tale subnet raggiungibili sulla porta 80.


## <a name="more-information-on-network-security-groups"></a>Ulteriori informazioni sui gruppi di sicurezza di rete
I comandi di rapidi consentono di iniziare a utilizzarlo con traffico per la macchina virtuale. Gruppi di sicurezza di rete offrono molte caratteristiche e granularità per controllare l'accesso alle risorse. È possibile leggere informazioni sulla [creazione di un gruppo di sicurezza di rete e le regole ACL qui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

È possibile definire gruppi di sicurezza di rete e le regole ACL come parte dei modelli di gestione di risorse Azure. Per ulteriori informazioni sulla [creazione di gruppi di sicurezza di rete con i modelli](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se è necessario utilizzare inoltro porta per eseguire il mapping di una porta univoca esterna a una porta interna di una macchina virtuale, utilizzare un bilanciamento del carico e le regole di Network Address Translation (NAT). Ad esempio, può essere necessario esporre la porta TCP 8080 esternamente in modo che il traffico diretto alla porta TCP 80 in una macchina virtuale. Per informazioni sulla [creazione di un servizio di bilanciamento del carico esposto a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio è creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati nei seguenti articoli:

- [Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md)
- [Che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Panoramica di gestione risorse Azure per servizi di bilanciamento del carico](../load-balancer/load-balancer-arm.md)
