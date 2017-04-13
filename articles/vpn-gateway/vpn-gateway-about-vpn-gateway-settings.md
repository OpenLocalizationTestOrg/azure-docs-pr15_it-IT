<properties 
   pageTitle="Informazioni sulle impostazioni di Gateway VPN per i gateway virtuali | Microsoft Azure"
   description="Informazioni sulle impostazioni del Gateway VPN per virtuali Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>Informazioni sulle impostazioni di Gateway VPN

Una soluzione di connessione VPN gateway si basa sulla configurazione di più risorse per inviare il traffico di rete tra reti virtuali e posizioni locale. Ogni risorsa contiene configurabili. La combinazione di risorse e impostazioni determina il risultato di connessione.

Le sezioni in questo articolo illustrano le risorse e le impostazioni relative a un gateway VPN nel modello di distribuzione **Manager delle risorse** . Potrebbero risultare utili per visualizzare le configurazioni disponibili tramite diagrammi della topologia di connessione. Per ogni soluzione di connessione vedere l'articolo [Su Gateway VPN](vpn-gateway-about-vpngateways.md) , è possibile trovare le descrizioni e diagrammi della topologia. 

## <a name="gwtype"></a>Tipi di gateway

Ogni virtuali consentito solo un gateway virtuali di ogni tipo. Quando si crea un gateway virtuali, assicurarsi che il tipo di gateway sia corretto per la configurazione.

I valori disponibili per - GatewayType sono: 

- VPN
- ExpressRoute

Un gateway VPN richiede la `-GatewayType` *Vpn*.  

Esempio:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>SKU di gateway


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>La configurazione di gateway SKU

**Specificare il gateway SKU nel portale di Azure**

Se si usa il portale di Azure per creare un gateway di gestione risorse virtuali, è possibile selezionare il gateway SKU utilizzando l'elenco a discesa. Le opzioni che sono disponibili corrispondono al tipo di Gateway e al tipo di VPN selezionato.

Ad esempio, se si seleziona il tipo di gateway "VPN" e il tipo 'criteri basato su VPN', risulta la "Base" SKU perché è SKU solo disponibile per le connessioni VPN PolicyBased. Se si seleziona 'Basate su Route', è possibile selezionare da Basic, Standard e HighPerformance SKU. 


**Specificare il gateway SKU tramite PowerShell**


Nell'esempio seguente viene PowerShell specifica la `-GatewaySku` come *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Modificare un gateway SKU**

Se si desidera aggiornare il gateway SKU a uno SKU più potente (da Basic Standard a HighPerformance) è possibile utilizzare il `Resize-AzureRmVirtualNetworkGateway` cmdlet di PowerShell. È anche possibile eseguire il downgrade gateway dimensioni SKU questo cmdlet.

Nell'esempio di PowerShell seguente mostra un gateway che viene ridimensionata a HighPerformance SKU.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Stimata tipo e la velocità effettiva aggregata da gateway SKU

Nella tabella seguente mostra i tipi di gateway e la velocità di aggregata stimata. In questa tabella si applica alle Manager delle risorse e modelli di distribuzione classica.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Tipi di connessione

Nel modello di distribuzione di Manager delle risorse, ogni configurazione richiede un tipo di connessione di rete virtuale specifico gateway. I valori di Manager delle risorse PowerShell disponibili per `-ConnectionType` sono:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Nell'esempio di PowerShell riportato di seguito viene creata una connessione S2S che richiede il tipo di connessione *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipi di VPN

Quando si crea il gateway di rete virtuale per una configurazione di gateway VPN, è necessario specificare un tipo di VPN. Il tipo VPN che si è scelto dipende dalla topologia di connessione che si desidera creare. Ad esempio una connessione P2S richiede un tipo di RouteBased VPN. Un tipo di VPN può dipendere anche da hardware che si intende utilizzare. Configurazioni S2S richiedono un dispositivo VPN. Alcuni dispositivi VPN supportano solo un particolare tipo VPN.

Il tipo VPN selezionato deve soddisfare tutti i requisiti di connessione per la soluzione che si desidera creare. Ad esempio, se si desidera creare una connessione di gateway S2S VPN e una connessione di gateway VPN P2S per la rete virtuale stesso, utilizzare il tipo VPN *RouteBased* perché P2S richiede un tipo di RouteBased VPN. È inoltre necessario verificare che il dispositivo VPN supportato una connessione VPN RouteBased. 

Dopo aver creato un gateway virtuali, non è possibile modificare il tipo di VPN. È necessario eliminare il gateway virtuali e crearne uno nuovo. Esistono due tipi VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


Nell'esempio seguente viene PowerShell specifica la `-VpnType` come *RouteBased*. Quando si crea un gateway, assicurarsi che VpnType - sia corretto per la configurazione. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisiti del gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Subnet gateway

Per configurare un gateway virtuali, è innanzitutto necessario creare una subnet gateway per il VNet. Subnet gateway deve essere denominata *GatewaySubnet* per il corretto funzionamento. Questo nome consente di Azure che questa subnet deve essere utilizzata per il gateway.

La dimensione minima di subnet gateway dipende interamente la configurazione che si desidera creare. Sebbene sia possibile creare una subnet gateway assuma /29, è consigliabile creare una subnet di gateway di /28 pollici (/ 28, /27, /26, ecc.). 

Creazione di una dimensione maggiore per il gateway non è possibile esecuzione limitazione delle dimensioni di gateway. Un gateway virtuali, ad esempio, si potrebbe essere creato con una dimensione subnet gateway /29 per una connessione S2S. È ora si desidera configurare S2S/ExpressRoute coesistere configurazione. La configurazione richiede una dimensione minima /28 subnet di gateway. Per creare la configurazione, è necessario modificare subnet gateway per soddisfare i requisiti minimi per la connessione, ossia /28.

L'esempio di gestione risorse PowerShell seguente mostra una subnet gateway denominata GatewaySubnet. È possibile visualizzare che la notazione CIDR specifica /27, che consente di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Gateway di rete locale

Quando si crea una configurazione di gateway VPN, il gateway di rete locale rappresenta spesso la posizione dell'utente locale. Nel modello di distribuzione classico, il gateway di rete locale è stato definito un sito locale. 

Si assegnare un nome, l'indirizzo IP pubblico del dispositivo VPN locale, il gateway di rete locale e specificare i prefissi indirizzo che si trovano nella posizione locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e instrada pacchetti di conseguenza. È inoltre possibile specificare il gateway di rete locale per le configurazioni VNet-VNet che utilizzano una connessione di gateway VPN.

Nell'esempio di PowerShell seguente viene creato un nuovo gateway di rete locale:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

A volte è necessario modificare le impostazioni del gateway di rete locale. Ad esempio, quando si aggiungono o si modifica l'intervallo di indirizzi o se l'indirizzo IP del dispositivo VPN cambia. Per un VNet classico, è possibile modificare queste impostazioni nel portale di classica nella pagina reti locale. Per Gestione risorse, vedere [Modifica impostazioni di gateway di rete locale tramite PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlet di PowerShell e le API REST

Per ulteriori risorse tecniche e requisiti di sintassi specifico quando si utilizzano le API REST e i cmdlet di PowerShell per le configurazioni Gateway VPN, vedere le pagine seguenti:

|**Classica** | **Manager delle risorse**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni di connessione disponibili, vedere [Su Gateway VPN](vpn-gateway-about-vpngateways.md) . 







 
