### <a name="noconnection"></a>Come aggiungere o rimuovere prefissi - alcuna connessione gateway

- **Per aggiungere** ulteriori prefissi locale rete gateway che è stato creato, ma che non è ancora dispongono una connessione di gateway, utilizzare l'esempio seguente. Assicurarsi di modificare i valori a quello personalizzato.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Per rimuovere** un indirizzo prefisso da un gateway di rete locale che non dispone di una connessione VPN, utilizzare l'esempio seguente. Omettere prefissi che non è più necessario. In questo esempio abbiamo non è più base prefisso 20.0.0.0/24 (dell'esempio precedente), in modo che Microsoft aggiornerà locale gateway di rete ed escludere il prefisso.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Come aggiungere o rimuovere prefissi - esistenti connessione gateway

Se è stata creata la connessione di gateway e per aggiungere o rimuovere prefissi di indirizzi IP contenuti nel gateway di rete locale, è necessario eseguire la procedura seguente in ordine. Verrà creato un tempo di inattività per la connessione VPN. Quando si aggiornano i prefissi, sarà innanzitutto rimuovere la connessione, modificare i prefissi e quindi si crea una nuova connessione. Negli esempi seguenti, assicurarsi di modificare i valori a quello personalizzato.

>[AZURE.IMPORTANT] Non eliminare il gateway VPN. In tal caso, sarà necessario tornare i passaggi per doverlo ricreare, nonché riconfigurare il router locale con le nuove impostazioni.
 
1. Rimuovere la connessione.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modificare i prefissi indirizzo per il gateway di rete locale.

    Impostare la variabile per il LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modificare i prefissi.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Creare la connessione. In questo esempio, è in corso la configurazione di un tipo di connessione IPsec. Quando si ricrea la connessione, utilizzare il tipo di connessione specificata per la configurazione. Per i tipi di connessione aggiuntive, vedere la pagina [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .

    Impostare la variabile per il VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Creare la connessione. Si noti che in questo esempio viene usata la variabile $local impostate nel passaggio precedente.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
