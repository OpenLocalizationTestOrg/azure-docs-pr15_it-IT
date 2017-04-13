<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito tramite Gestione risorse di Azure e PowerShell | Microsoft Azure"
   description="In questo articolo illustra la creazione di un VNet utilizzando il modello di distribuzione di Manager delle risorse e la connessione alla rete locale locale tramite una connessione di gateway S2S VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Creare un VNet con una connessione al sito tramite PowerShell

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Classico: portale classica](vpn-gateway-site-to-site-create.md)

In questo articolo illustra la creazione di una rete virtuale e una connessione di gateway da sito VPN alla rete locale utilizzando il modello di distribuzione di Manager delle risorse di Azure. Le connessioni al sito possono essere usate per tra locali e ibride configurazioni.

![Diagramma del sito al sito] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "da sito") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelli di distribuzione e i metodi per le connessioni al sito

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra i metodi per le configurazioni di siti e modelli di distribuzione attualmente disponibile. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurazioni aggiuntive

Se si desidera connettere VNets, ma non viene creato una connessione a una posizione locale, vedere [configurare una connessione VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se si desidera aggiungere una connessione da sito a un VNet che contiene già una connessione, vedere [aggiungere una connessione S2S a VNet con una connessione di gateway VPN esistente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre gli elementi seguenti prima di iniziare la configurazione.

- Un dispositivo VPN e chi è in grado di configurarlo. Vedere [informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non ha familiarità con la configurazione del dispositivo VPN o si ha familiarità con l'indirizzo IP intervalli nella configurazione di rete le in locale, è necessario per la coordinazione con qualcuno in grado di fornire i dettagli dell'utente.

- Esternamente esposto pubblico indirizzo IP del dispositivo VPN. Non è possibile individuare l'indirizzo IP dietro al NAT.
    
- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
    
- La versione più recente dei cmdlet di Manager delle risorse PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .


## <a name="Login"></a>1. connettersi al proprio abbonamento 

Verificare che si passa alla modalità di PowerShell per utilizzare i cmdlet Gestione risorse. Per ulteriori informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi all'account. Utilizzare nell'esempio seguente viene per la connessione:

    Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription 

Specificare l'abbonamento a cui si desidera utilizzare.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. creare una rete virtuale e una subnet gateway

Gli esempi utilizzano una subnet di gateway di /28. Sebbene sia possibile creare una subnet gateway assuma /29, è consigliabile creare una subnet più grande contenente più indirizzi selezionando almeno /28 o /27. In questo modo per indirizzi sufficiente contenere possibili configurazioni aggiuntive che può essere utile in futuro.

Se si dispone già di una rete virtuale con una subnet gateway/29 o maggiore, è possibile passare direttamente per [aggiungere il gateway di rete locale](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Per creare una rete virtuale e una subnet gateway

Consente di creare una rete virtuale e una subnet gateway nell'esempio seguente. Sostituire i valori per uso personale. 

Prima di tutto, creare un gruppo di risorse:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Creare la rete virtuale. Verificare che gli spazi indirizzo specificato non si sovrappongono uno qualsiasi degli spazi di indirizzo che si sono nella rete locale.

Nell'esempio seguente viene creata una rete virtuale denominato *testvnet* con due subnet, una chiamata *GatewaySubnet* e l'altra denominata *Subnet1*. È importante creare una subnet denominata specificamente *GatewaySubnet*. Se si denominarlo altro, la configurazione della connessione avrà esito negativo. 

Impostare le variabili.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Creare il VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Per aggiungere una subnet gateway a una rete che è già stato creato

Questo passaggio è necessario solo se è necessario aggiungere una subnet gateway a un VNet creato in precedenza.

È possibile creare subnet gateway mediante l'esempio seguente. Assicurarsi di assegnare un nome alla subnet gateway 'GatewaySubnet'. Se si denominarlo un altro elemento, si crea una subnet, ma Azure non viene considerato una subnet gateway.

Impostare le variabili.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Creare subnet gateway.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Impostare la configurazione. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>aggiungere il gateway di rete locale

In una rete virtuale il gateway di rete locale indica in genere la posizione dell'utente locale. Il sito a un nome per il quale Azure possa fare riferimento a tale e anche specificare il prefisso di spazio di indirizzi per il gateway di rete locale. 

Azure utilizza il prefisso dell'indirizzo IP specificato per identificare il tipo di traffico per inviare la posizione dell'utente locale. Questo significa che è necessario specificare ogni prefisso indirizzo che si desidera associare il gateway di rete locale. È possibile aggiornare facilmente questi prefissi se cambia la rete locale. 

Quando si usano gli esempi di PowerShell, tenere presente quanto segue:
    
- *GatewayIPAddress* è l'indirizzo IP del dispositivo VPN locale. Non è possibile individuare il dispositivo VPN dietro al NAT. 
- *AddressPrefix* è lo spazio degli indirizzi locale.

Per aggiungere un gateway di rete locale con un prefisso singolo indirizzo:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Per aggiungere un gateway di rete locale con più prefissi di indirizzi:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Per modificare l'indirizzo IP prefissi per il gateway di rete locale

Accadere che i prefissi di gateway di rete locale. Le procedure per modificare i prefissi di indirizzi IP dipendono dal fatto di aver creato una connessione di gateway VPN. Vedere la sezione [prefissi di indirizzi IP di modifica per un gateway di rete locale](#modify) di questo articolo.


## <a name="PublicIP"></a>4. richiedere un indirizzo IP pubblico per il gateway VPN

Successivamente, richiedere un indirizzo IP pubblico da allocare per il gateway Azure VNet VPN. Questa non è lo stesso indirizzo IP assegnato al dispositivo VPN; è assegnata al gateway VPN Azure stesso. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. In modo dinamico allocata per il gateway. Utilizzare questo indirizzo IP quando si configura il dispositivo VPN locale per la connessione al gateway.

Il gateway VPN Azure per il modello di distribuzione Manager delle risorse attualmente supporta solo indirizzi IP utilizzando il metodo di allocazione dinamica. Tuttavia, ciò significa che verrà modificato l'indirizzo IP. Solo le modifiche di indirizzi IP di gateway VPN Azure è quando il gateway viene eliminato e ricreato. Non è possibile modificare l'indirizzo IP pubblico gateway tra il ridimensionamento, reimpostare o altri interni/aggiornamenti di manutenzione del gateway VPN Azure.

Utilizzare nell'esempio seguente viene PowerShell:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. creare la configurazione di indirizzi IP di gateway

La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Nell'esempio seguente consente di creare la configurazione del gateway.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. creare il gateway di rete virtuale

In questo passaggio è creare il gateway di rete virtuale. La creazione di un gateway può richiedere molto tempo. Spesso 45 minuti o più. 

Usare i valori seguenti:

- *-GatewayType* per una configurazione da sito è *Vpn*. Il tipo di gateway è sempre specifico per la configurazione per l'implementazione. Altre configurazioni di gateway, ad esempio, potrebbe essere necessario - GatewayType ExpressRoute. 

- *-VpnType* può essere *RouteBased* (definiti un Gateway dinamica in parte della documentazione) o *PolicyBased* (definiti un Gateway statico in parte della documentazione). Per ulteriori informazioni sui tipi di gateway VPN, vedere [Su gateway VPN](vpn-gateway-about-vpngateways.md#vpntype).
- *-GatewaySku* può essere *base*, *Standard*o *HighPerformance*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. configurare il dispositivo VPN

A questo punto, è necessario l'indirizzo IP pubblico del gateway di rete virtuale per la configurazione del dispositivo VPN locale. Usare il produttore del dispositivo per informazioni sulla configurazione specifica. È possibile fare riferimento ai [Dispositivi VPN](vpn-gateway-about-vpn-devices.md) per altre informazioni.

Per trovare l'indirizzo IP pubblico del gateway virtuali, usare nell'esempio seguente:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. creare una connessione VPN

Creare la connessione VPN da sito tra il gateway virtuali e il dispositivo VPN. Assicurarsi di sostituire i valori con uno personalizzato. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione di dispositivi VPN. Si noti che la `-ConnectionType` per da sito è *IPsec*. 

Impostare le variabili.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Creare la connessione.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Dopo un breve durante la connessione viene stabilito. 

## <a name="toverify"></a>Per verificare una connessione VPN

Esistono diversi modi per verificare la connessione VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Per modificare l'indirizzo IP prefissi per un gateway di rete locale

Se è necessario modificare i prefissi per il gateway di rete locale, attenersi alle istruzioni seguenti. Sono disponibili due set di istruzioni. Le istruzioni che si sceglie dipendono se è già stato creato la connessione di gateway. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Per modificare l'indirizzo IP del gateway per un gateway di rete locale

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- È possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.

- Per informazioni su BGP, vedere la [Panoramica BGP](vpn-gateway-bgp-overview.md) e [su come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).

