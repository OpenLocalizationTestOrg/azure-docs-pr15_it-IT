### <a name="to-verify-your-connection-by-using-powershell"></a>Per verificare la connessione tramite PowerShell

È possibile verificare che la connessione ha avuto esito positivo mediante il `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet, con o senza `-Debug`. 

1. Utilizzare l'esempio seguente cmdlet, configurazione dei valori in modo che corrispondano personalizzato. Se richiesto, selezionare 'A' per eseguire "All". Nell'esempio, `-Name` fa riferimento al nome della connessione che si è creato e si desidera verificare.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Al termine dell'operazione il cmdlet consente di visualizzare i valori. Nell'esempio seguente viene visualizzato lo stato di connessione come "Connessi" ed è possibile visualizzare byte ingresso e in uscita.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>Per verificare la connessione tramite il portale di Azure

Nel portale di Azure, è possibile visualizzare lo stato della connessione passando alla connessione. Esistono diversi modi per eseguire questa operazione. La procedura seguente mostra un modo per passare alla connessione e la verifica.

1. Nel [portale di Azure](http://portal.azure.com), fare clic su **tutte le risorse** e passare al gateway di rete virtuale.
2. In blade per il gateway virtuali, fare clic su **connessioni**. È possibile visualizzare lo stato di ogni connessione.
3. Fare clic sul nome della connessione che si desidera verificare per aprire **Nozioni di base**. In Nozioni di base, è possibile visualizzare ulteriori informazioni sulla connessione. Lo **stato** è "Riuscita" e "Connessi" dopo aver effettuato una connessione.

    ![Verificare di connessione](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)