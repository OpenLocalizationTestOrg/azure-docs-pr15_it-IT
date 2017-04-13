<properties
   pageTitle="Aprire le porte e i punti finali a una VM Linux | Microsoft Azure"
   description="Informazioni su come aprire una porta / creare un endpoint per la VM Linux utilizzando il modello di distribuzione di manager delle risorse Azure e CLI Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Apertura delle porte e i punti finali a una VM Linux in Azure
Si apre una porta o crea un endpoint per un macchine () in Azure mediante la creazione di un filtro di rete su una rete macchine Virtuali subnet. Inserire questi filtri, che consentono di controllare il traffico in ingresso e in uscita, in un gruppo di sicurezza di rete associati alla risorsa che riceve il traffico. Utilizzare un tipico esempio di traffico web sulla porta 80.

## <a name="quick-commands"></a>Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole sono necessarie [CLI Azure](../xplat-cli-install.md) installato e l'utilizzo della modalità di gestione risorse:

```bash
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. I nomi dei parametri di esempio inclusi `myResourceGroup`, `myNetworkSecurityGroup`, e `myVnet`.

Creare il gruppo di sicurezza di rete, immettere il proprio nomi e il percorso in modo appropriato. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup` nel `WestUS` percorso:

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Aggiungere una regola per consentire il traffico HTTP al server Web (o modificare per il proprio scenario, ad esempio SSH access o database connectivity). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRule` per consentire il traffico TCP sulla porta 80:

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Associare il gruppo di sicurezza di rete con l'interfaccia di rete il Virtual Machine (NIC). Nell'esempio seguente associa una scheda di rete esistente denominata `myNic` con il gruppo di sicurezza di rete denominata `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

In alternativa, è possibile associare il gruppo di sicurezza di rete con una subnet virtuali anziché solo all'interfaccia di rete in una singola macchina virtuale. Nell'esempio seguente associa una subnet esistente denominata `mySubnet` nel `myVnet` virtuali con il gruppo di sicurezza di rete denominata `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Ulteriori informazioni sui gruppi di sicurezza di rete
I comandi di rapidi consentono di iniziare a utilizzarlo con traffico per la macchina virtuale. Gruppi di sicurezza di rete offrono molte caratteristiche e granularità per controllare l'accesso alle risorse. È possibile leggere informazioni sulla [creazione di un gruppo di sicurezza di rete e le regole ACL qui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

È possibile definire gruppi di sicurezza di rete e le regole ACL come parte dei modelli di gestione di risorse Azure. Per ulteriori informazioni sulla [creazione di gruppi di sicurezza di rete con i modelli](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se è necessario utilizzare inoltro porta per eseguire il mapping di una porta univoca esterna a una porta interna di una macchina virtuale, utilizzare un bilanciamento del carico e le regole di Network Address Translation (NAT). Ad esempio, può essere necessario esporre la porta TCP 8080 esternamente in modo che il traffico diretto alla porta TCP 80 in una macchina virtuale. Per informazioni sulla [creazione di un servizio di bilanciamento del carico esposto a Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio è creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati nei seguenti articoli:

- [Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md)
- [Che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Panoramica di gestione risorse Azure per servizi di bilanciamento del carico](../load-balancer2    /load-balancer-arm.md)