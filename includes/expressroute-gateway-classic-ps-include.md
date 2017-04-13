È necessario creare un VNet e una subnet gateway prima di tutto, per eseguire le operazioni seguenti. Vedere l'articolo [configurare una rete virtuale tramite il portale classico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) per ulteriori informazioni.   

## <a name="add-a-gateway"></a>Aggiungere un gateway

Usare il comando seguente per creare un gateway. Assicurarsi di sostituire i valori per uso personale.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato. Questo comando recupera anche l'ID di gateway, è necessario per altre operazioni.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Ridimensionare un gateway

Esistono diversi [SKU di Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile utilizzare il comando seguente per modificare lo SKU di Gateway in qualsiasi momento.

>[AZURE.IMPORTANT] Questo comando non funziona per UltraPerformance gateway. Per modificare il gateway a un gateway UltraPerformance, rimuovere il gateway ExpressRoute esistente e quindi si crea un nuovo gateway UltraPerformance. Per eseguire il downgrade del gateway da un gateway UltraPerformance, rimuovere innanzitutto il gateway UltraPerformance e quindi si crea un nuovo gateway. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Rimuovere un gateway

Usare il comando seguente per rimuovere un gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>