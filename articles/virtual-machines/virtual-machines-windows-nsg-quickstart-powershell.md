<properties
   pageTitle="Aprire le porte a una macchina virtuale tramite PowerShell | Microsoft Azure"
   description="Informazioni su come aprire una porta / creare un endpoint per le macchine Virtuali di Windows con la modalità di distribuzione manager delle risorse Azure e Azure PowerShell"
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

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Apertura di porte e i punti finali a una macchina virtuale in Azure tramite PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandi rapidi
Per creare una regola gruppo di sicurezza di rete e ACL è necessario [l'ultima versione di Azure PowerShell installato](../powershell-install-configure.md). È anche possibile [eseguire questa procedura tramite il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Accedere al proprio account Azure:

```powershell
Login-AzureRmAccount
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. I nomi dei parametri di esempio inclusi `myResourceGroup`, `myNetworkSecurityGroup`, e `myVnet`.

Creare una regola. Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRule` per consentire il traffico TCP sulla porta 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Successivamente, creare il gruppo di sicurezza della rete e assegnare alla regola HTTP che appena creato come indicato di seguito. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

A questo punto si assegnare al gruppo di sicurezza di rete a una subnet. Nell'esempio seguente viene assegnato a una rete virtuale esistente denominata `myVnet` della variabile `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associare il gruppo di sicurezza di rete con subnet. Nell'esempio seguente associa subnet denominata `mySubnet` con il gruppo di sicurezza di rete:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Infine, aggiornare la rete virtuale in ordine rendere effettive le modifiche:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Ulteriori informazioni sui gruppi di sicurezza di rete
I comandi di rapidi consentono di iniziare a utilizzarlo con traffico per la macchina virtuale. Gruppi di sicurezza di rete offrono molte caratteristiche e granularità per controllare l'accesso alle risorse. È possibile leggere informazioni sulla [creazione di un gruppo di sicurezza di rete e le regole ACL qui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

È possibile definire gruppi di sicurezza di rete e le regole ACL come parte dei modelli di gestione di risorse Azure. Per ulteriori informazioni sulla [creazione di gruppi di sicurezza di rete con i modelli](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se è necessario utilizzare inoltro porta per eseguire il mapping di una porta univoca esterna a una porta interna di una macchina virtuale, utilizzare un bilanciamento del carico e le regole di Network Address Translation (NAT). Ad esempio, può essere necessario esporre la porta TCP 8080 esternamente in modo che il traffico diretto alla porta TCP 80 in una macchina virtuale. Per informazioni sulla [creazione di un servizio di bilanciamento del carico esposto a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio è creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati nei seguenti articoli:

- [Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md)
- [Che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Panoramica di gestione risorse Azure per servizi di bilanciamento del carico](../load-balancer/load-balancer-arm.md)