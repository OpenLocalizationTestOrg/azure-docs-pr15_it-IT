<properties
    pageTitle="Comandi di rete comune PowerShell per macchine virtuali | Microsoft Azure"
    description="Comandi PowerShell comuni per iniziare a creare una rete virtuale e le risorse associate per macchine virtuali."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandi di PowerShell Azure rete comuni per le macchine virtuali

Se si desidera creare una macchina virtuale, è necessario creare una [rete virtuale](../virtual-network/virtual-networks-overview.md) o conoscere una rete virtuale esistente in cui è possibile aggiungere la macchina virtuale. In genere, quando si crea una macchina virtuale, è anche necessario prendere in considerazione la creazione di risorse descritte in questo articolo.

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.

## <a name="create-network-resources"></a>Creare le risorse di rete

Attività | Comando 
-------------- | -------------------------
Creare configurazioni subnet | $subnet1 = [AzureRmVirtualNetworkSubnetConfig nuovo](https://msdn.microsoft.com/library/mt619412.aspx) -nome "subnet_name" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = AzureRmVirtualNetworkSubnetConfig nuovo-nome "subnet_name" - AddressPrefix XX. X.X.X/XX<BR><BR>Una tipica rete potrebbe avere una subnet per un [internet affiancate di bilanciamento del carico](../load-balancer/load-balancer-internet-overview.md) e una subnet separata per un [sistema di bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md). |
Creare una rete virtuale | $vnet = [Nuovo AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -resource_group_name"nome"virtual_network_name"- ResourceGroupName"-posizione "location_name" - AddressPrefix XX. X.X.X/XX-Subnet $subnet1, $subnet2
Test per un nome di dominio univoco | [Test AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName "nome_dominio"-posizione "location_name"<BR><BR>È possibile specificare un nome di dominio DNS per una [risorsa IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md), che consente di creare un mapping per domainname.location.cloudapp.azure.com all'indirizzo IP pubblico nei server DNS gestiti Azure. Il nome può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere deve essere una lettera o numero e il nome di dominio deve essere univoci all'interno di posizione Azure. Se viene restituito **True** , il nome proposto è globalmente univoco.
Creare un indirizzo IP pubblico | $pip = [AzureRmPublicIpAddress New](https://msdn.microsoft.com/library/mt603620.aspx) -nome_dominio"nome"ip_address_name"- ResourceGroupName"resource_group_name"- DomainNameLabel"-posizione "location_name" - AllocationMethod dinamiche<BR><BR>L'indirizzo IP pubblico utilizza il nome di dominio testati e precedenza utilizzato dalla configurazione front-end di bilanciamento del carico.
Creare una configurazione IP front-end | $frontendIP = [AzureRmLoadBalancerFrontendIpConfig nuovo](https://msdn.microsoft.com/library/mt603510.aspx) -nome "frontend_ip_name" - PublicIpAddress $pip<BR><BR>La configurazione front-end include l'indirizzo IP pubblico creato in precedenza per il traffico di rete in ingresso.
Creare un pool di indirizzi back-end | $beAddressPool = [AzureRmLoadBalancerBackendAddressPoolConfig nuovo](https://msdn.microsoft.com/library/mt603791.aspx) -nome "backend_pool_name"<BR><BR>Fornisce gli indirizzi interni per back-end di sistema di bilanciamento del carico accessibili tramite un'interfaccia di rete.
Creare un sondaggio | $healthProbe = [AzureRmLoadBalancerProbeConfig nuovo](https://msdn.microsoft.com/library/mt603847.aspx) -nome "probe_name" - RequestPath 'HealthProbe.aspx'-protocollo http-porta 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Contiene le ricerche integrità utilizzate per verificare la disponibilità delle istanze di macchine virtuali nel pool di indirizzi back-end.
Creare una regola di bilanciamento del carico | $lbRule = [AzureRmLoadBalancerRuleConfig nuovo](https://msdn.microsoft.com/library/mt619391.aspx) -nome HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-verifica $healthProbe-protocollo Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Contiene regole che assegnare una porta pubblica del servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end.
Creare una regola NAT in entrata | $inboundNATRule = [AzureRmLoadBalancerInboundNatRuleConfig nuovo](https://msdn.microsoft.com/library/mt603606.aspx) -nome "rule_name" - FrontendIpConfiguration $frontendIP-protocollo TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Contiene regole il mapping di una porta pubblica del servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
Creare un bilanciamento del carico | $loadBalancer = "resource_group_name" [Nuovo AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName-nome "load_balancer_name"-posizione "location_name" - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-verifica $healthProbe
Creare un'interfaccia di rete | $nic1 = "resource_group_name" [Nuovo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName-nome "network_interface_name"-posizione "location_name" - PrivateIpAddress XX. X.X.X-Subnet subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] $loadBalancer.InboundNatRules[0 - LoadBalancerInboundNatRule]<BR><BR>Creare un'interfaccia di rete utilizzando l'indirizzo IP pubblico e subnet virtuali creato in precedenza.
    
## <a name="get-information-about-network-resources"></a>Ottenere informazioni sulle risorse di rete

Attività | Comando 
-------------- | -------------------------
Reti virtuali di elenco | [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Elenca tutte le reti virtuali nel gruppo di risorse.
Ottenere informazioni su una rete virtuale | Get-AzureRmVirtualNetwork-resource_group_name"nome"virtual_network_name"- ResourceGroupName"
Elenco subnet in una rete virtuale | Get-AzureRmVirtualNetwork-nome - ResourceGroupName "virtual_network_name" "resource_group_name" & #124; Selezionare subnet
Ottenere informazioni su una subnet | [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -nome "subnet_name" - VirtualNetwork $vnet<BR><BR>Ottiene informazioni relative alla subnet nella rete virtuale specificata. Il valore di $vnet rappresenta l'oggetto restituito da Get-AzureRmVirtualNetwork.
Elenco indirizzi | [Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Elenca gli indirizzi IP pubblici nel gruppo di risorse.
Servizi di bilanciamento del carico elenco | [Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Elenchi di bilanciamento del gruppo di risorse.
Elenca le interfacce di rete | [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Elenca tutte le interfacce di rete al gruppo di risorse.
Ottenere informazioni su un'interfaccia di rete | Get-AzureRmNetworkInterface-resource_group_name"nome"network_interface_name"- ResourceGroupName"<BR><BR>Ottiene informazioni su un'interfaccia di rete specifico.
Ottenere la configurazione IP un'interfaccia di rete | [Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -nome "ipconfiguration_name" - NetworkInterface $nic<BR><BR>Ottiene informazioni sulla configurazione IP dell'interfaccia di rete specificato. Il valore di $nic rappresenta l'oggetto restituito da Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Gestire le risorse di rete

Attività | Comando 
-------------- | -------------------------
Aggiungere una subnet a una rete | [Aggiungere AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-nome "subnet_name" - VirtualNetwork $vnet<BR><BR>Aggiunge una subnet a una rete virtuale esistente. Il valore di $vnet rappresenta l'oggetto restituito da Get-AzureRmVirtualNetwork.
Eliminare una rete virtuale | [Rimuovi AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -resource_group_name"nome"virtual_network_name"- ResourceGroupName"<BR><BR>Rimuove la rete virtuale specificata dal gruppo di risorse.
Eliminare un'interfaccia di rete | [Rimuovi AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -resource_group_name"nome"network_interface_name"- ResourceGroupName"<BR><BR>Rimuove l'interfaccia di rete specificato dal gruppo di risorse.
Eliminare un bilanciamento del carico | [Rimuovi AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -resource_group_name"nome"load_balancer_name"- ResourceGroupName"<BR><BR>Rimuove il servizio di bilanciamento del carico specificato dal gruppo di risorse.
Eliminare un indirizzo IP pubblico | [Rimuovi AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-resource_group_name"nome"ip_address_name"- ResourceGroupName"<BR><BR>Rimuove l'indirizzo IP pubblico specificato dal gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

- Utilizzare l'interfaccia di rete che appena creato quando si [Crea una macchina virtuale](virtual-machines-windows-ps-create.md).
- Informazioni su come [creare una macchina virtuale con più interfacce di rete](../virtual-network/virtual-networks-multiple-nics.md).
