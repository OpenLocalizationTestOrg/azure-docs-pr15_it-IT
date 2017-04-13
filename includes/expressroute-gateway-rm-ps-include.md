Procedura per l'attività viene usata una VNet in base a valori riportati di seguito. Questo elenco sono indicati anche i nomi e impostazioni aggiuntive. Abbiamo non utilizzare questo elenco direttamente in uno qualsiasi dei passaggi, anche se è la procedura per aggiungere variabili in base ai valori in questo elenco. È possibile copiare l'elenco da utilizzare come riferimento, sostituire i valori con uno personalizzato.

Elenco di referenze configurazione:
    
- Nome di rete virtuale = "TestVNet"
- Spazio di indirizzi di rete virtuale = 192.168.0.0/16
- Gruppo di risorse = "TestRG"
- Nome Subnet1 = "Front-end" 
- Spazio di indirizzi Subnet1 = "192.168.0.0/16"
- Nome del gateway Subnet: "GatewaySubnet" è sempre necessario assegnare un nome a una subnet gateway *GatewaySubnet*.
- Spazio di indirizzi di gateway Subnet = "192.168.200.0/26"
- Area = "US orientale"
- Nome del gateway = "GW"
- Nome del gateway IP = "GWIP"
- Configurazione di gateway IP nome = "gwipconf"
-  Tipo = "ExpressRoute" è necessario per una configurazione ExpressRoute questo tipo.
- Nome del gateway pubblico IP = "gwpip"


## <a name="add-a-gateway"></a>Aggiungere un gateway

1. Connettersi al proprio abbonamento Azure. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Dichiarare le variabili per questa esercitazione. In questo esempio utilizzerà l'utilizzo di variabili nell'esempio seguente. Assicurarsi di modificare questa in base alle impostazioni che si desidera utilizzare. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Memorizzare l'oggetto virtuali come una variabile.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Aggiungere una subnet gateway alla rete virtuale. Subnet gateway deve essere denominata "GatewaySubnet". È consigliabile creare un gateway in /27 pollici (/ 26, / 25, ecc.).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Impostare la configurazione.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Archiviare subnet gateway come una variabile.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Richiedere un indirizzo IP pubblico. L'indirizzo IP è necessario prima di creare il gateway. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. allocata in modo dinamico. Utilizzare questo indirizzo IP nella sezione Configurazione successiva. Il AllocationMethod deve essere dinamiche.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Creare la configurazione per il gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. In questo passaggio si specifica la configurazione che verrà utilizzata quando si crea il gateway. Questo passaggio non comporta la creazione dell'oggetto gateway. Utilizzare l'esempio riportato di seguito per creare la configurazione del gateway. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Creare il gateway. In questo passaggio è particolarmente importante **-GatewayType** . È necessario usare il valore **ExpressRoute**. Dopo l'esecuzione di questi cmdlet, il gateway può richiedere 20 minuti o altri elementi per creare una nota.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Ridimensionare un gateway

Esistono diversi [SKU di Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile utilizzare il comando seguente per modificare lo SKU di Gateway in qualsiasi momento.

>[AZURE.IMPORTANT] Questo comando non funziona per UltraPerformance gateway. Per modificare il gateway a un gateway UltraPerformance, rimuovere il gateway ExpressRoute esistente e quindi si crea un nuovo gateway UltraPerformance. Per eseguire il downgrade del gateway da un gateway UltraPerformance, rimuovere innanzitutto il gateway UltraPerformance e quindi si crea un nuovo gateway.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Rimuovere un gateway

Usare il comando seguente per rimuovere un gateway

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
