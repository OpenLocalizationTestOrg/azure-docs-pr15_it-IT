Per modificare l'indirizzo IP del gateway, utilizzare la `New-AzureRmVirtualNetworkGatewayConnection` cmdlet. Come si mantenere il nome del gateway rete locale esattamente come il nome esistente, verranno sovrascritti le impostazioni. In questo momento il cmdlet "Impostazione" non supporta Modifica indirizzo IP del gateway.

### <a name="gwipnoconnection"></a>Come modificare l'indirizzo IP del gateway - alcuna connessione gateway

Per aggiornare l'indirizzo IP del gateway per il gateway di rete locale che non dispone ancora di una connessione, utilizzare l'esempio seguente. È inoltre possibile aggiornare prefissi degli indirizzi nello stesso momento. Le impostazioni specificate sovrascriverà le impostazioni esistenti. Assicurarsi di usare il nome del gateway rete locale esistente. In caso contrario, si sta creando un nuovo gateway di rete locale, non sostituisce quella esistente.

Utilizzare l'esempio seguente, sostituendo i propri valori.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Come modificare l'indirizzo IP del gateway - esistenti connessione gateway

Se esiste già una connessione di gateway, è necessario prima di tutto rimuovere la connessione. Quindi, è possibile modificare l'indirizzo IP del gateway e ricreare una nuova connessione. Comporterà dei tempi di inattività per la connessione VPN.


>[AZURE.IMPORTANT] Non eliminare il gateway VPN. In tal caso, sarà necessario tornare i passaggi per ricreare l'oggetto, nonché riconfigurare il router locale con l'indirizzo IP assegnato al gateway appena creato.
 

1. Rimuovere la connessione. È possibile trovare il nome della connessione utilizzando il `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Modificare il valore di GatewayIpAddress. È inoltre possibile modificare i prefissi degli indirizzi al momento, se necessario. Si noti che questa sovrascriverà le impostazioni del gateway di rete locale esistente. Usare il nome del gateway rete locale esistente quando si modificano in modo che le impostazioni verranno sovrascritti. In caso contrario, si sta creando un nuovo gateway di rete locale, non modificare quello esistente.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Creare la connessione. In questo esempio, è in corso la configurazione di un tipo di connessione IPsec. Quando si ricrea la connessione, utilizzare il tipo di connessione specificata per la configurazione. Per i tipi di connessione aggiuntive, vedere la pagina [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Per ottenere il nome VirtualNetworkGateway, è possibile eseguire il `Get-AzureRmVirtualNetworkGateway` cmdlet.

    Impostare le variabili:

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Creare la connessione:
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

