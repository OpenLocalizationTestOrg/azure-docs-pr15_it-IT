
Per configurare il dispositivo VPN, è necessario l'indirizzo IP pubblico del gateway di rete virtuale per la configurazione del dispositivo VPN locale. Usare il produttore del dispositivo per informazioni sulla configurazione specifica e configurare il dispositivo. Fare riferimento ai [Dispositivi VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) per ulteriori informazioni sui dispositivi VPN che funzionano bene con Azure.

Per trovare l'indirizzo IP pubblico del gateway virtuali con PowerShell, usare nell'esempio seguente:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

È anche possibile visualizzare l'indirizzo IP pubblico per il gateway virtuali tramite il portale di Azure. Passare al **gateway di rete virtuale**, quindi fare clic sul nome del gateway.