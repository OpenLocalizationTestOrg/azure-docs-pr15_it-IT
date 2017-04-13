<properties
   pageTitle="Configurare le connessioni VPN Expressroute e al sito che possono coesistere per il modello di distribuzione di gestione risorse | Microsoft Azure"
   description="In questo articolo illustra la configurazione ExpressRoute e una connessione VPN a siti che può coesistere per il modello di Manager delle risorse."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charleywen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-resource-manager-deployment-model"></a>Configurare le connessioni dominio ExpressRoute e al sito per il modello di distribuzione di Manager delle risorse

> [AZURE.SELECTOR]
- [PowerShell - Manager delle risorse](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - classico](expressroute-howto-coexist-classic.md)

Possibilità di configurare VPN da sito ed ExpressRoute offre molti vantaggi. È possibile configurare la rete VPN da sito come percorso sicuro failover per ExressRoute oppure utilizzare VPN da sito per connettersi ai siti che non si è connessi a ExpressRoute. La procedura per configurare entrambi gli scenari in questo articolo verranno illustrate le caratteristiche. Questo articolo si applica al modello di distribuzione di Manager delle risorse. Questa configurazione non è disponibile nel portale di Azure.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Circuiti ExpressRoute devono essere già configurati prima di seguire le istruzioni riportate di seguito. Assicurarsi di aver seguito le guide per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [configurare il routing](expressroute-howto-routing-arm.md) prima di eseguire la procedura seguente.

## <a name="limits-and-limitations"></a>Limiti e limitazioni

- **Transito routing non è supportato.** È possibile instradare (tramite Azure) tra la rete locale connesse tramite VPN da sito e la rete locale connessa tramite ExpressRoute.
- **Base gateway SKU non è supportata.** È necessario utilizzare un gateway non base SKU per il [gateway ExpressRoute](expressroute-about-virtual-network-gateways.md) e il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Gateway VPN solo basati su route è supportata.** È necessario utilizzare un basati su route [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Route statica deve essere configurato per il gateway VPN.** Se la rete locale sia connesso a ExpressRoute e una connessione VPN da sito, è necessario disporre di una route statica configurata la rete locale per instradare la connessione VPN da sito a Internet.
- **Prima di tutto è necessario configurare gateway ExpressRoute.** Prima di aggiungere il gateway VPN da sito, è necessario creare il gateway ExpressRoute prima di tutto.


## <a name="configuration-designs"></a>Strutture di configurazione

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurare una connessione VPN da sito come percorso failover per ExpressRoute

È possibile configurare una connessione VPN da sito come una copia di backup per ExpressRoute. Si applica solo alle reti virtuali collegate al percorso peering privato Azure. Non esiste nessuna soluzione failover basata su VPN per i servizi accessibili Azure pubblico e peerings Microsoft. Circuito ExpressRoute è sempre il collegamento principale. Dati scorrerà aiutano VPN da sito solo se si verifica un errore di circuito ExpressRoute.
>[AZURE.NOTE] Mentre ExpressRoute circuito è preferita su VPN da sito se entrambe le route sono le stesse, Azure utilizzerà la corrispondenza di prefisso longuest per scegliere la route verso la destinazione del pacchetto.

![Coesistere](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurare una connessione VPN da sito a cui connettersi siti non è connessi tramite ExpressRoute

È possibile configurare la rete in cui alcuni siti connettono direttamente al Azure attraverso VPN da sito e alcuni siti connettano tramite ExpressRoute. 

![Coesistere](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Non è possibile configurare una rete virtuale come router transito.

## <a name="selecting-the-steps-to-use"></a>Selezionare i passaggi da seguire

Sono disponibili due diversi insiemi di procedure da scegliere per configurare le connessioni che possono coesistere. La procedura di configurazione si seleziona dipenderà se si dispone di una rete virtuale esistente che si desidera connettersi o si desidera creare una nuova rete virtuale.


- Non dispone di un VNet ed è necessario crearne uno.
    
    Se non si dispone di una rete virtuale, questa procedura consentono all'utente la creazione di una nuova rete virtuale utilizzando il modello di distribuzione di Manager delle risorse e la creazione di nuove connessioni VPN ExpressRoute e al sito. Per configurare, seguire i passaggi indicati nella sezione articolo [per creare una nuova rete virtuale e connessioni dominio](#new).

- Dispone già di un modello di distribuzione VNet Manager delle risorse.

    In posizione con una connessione VPN da sito esistente o ExpressRoute già si dispone di una rete virtuale. La sezione [per configurare le connessioni coexsiting un VNet già esistente](#add) fornisce istruzioni dettagliate per eliminare il gateway e quindi la creazione di nuove connessioni VPN ExpressRoute e al sito. Si noti che durante la creazione di nuove connessioni, i passaggi da completare in un ordine specifico. Non usare le istruzioni in altri articoli per creare il gateway e connessioni.

    In questa procedura, la creazione di connessioni che possono coesistere sarà necessario eliminare il gateway e quindi configurare nuovo gateway. Di conseguenza, che sarà necessario del tempo di inattività per le connessioni tra locale mentre eliminare e ricreare il gateway e connessioni, ma non è necessario eseguire la migrazione di uno qualsiasi dei servizi macchine virtuali in una nuova rete virtuale. Le macchine virtuali e i servizi saranno comunque possibile comunicare tramite il servizio di bilanciamento del carico mentre si configura il gateway se sono stati configurati per eseguire questa operazione.


## <a name="new"></a>Per creare una nuova rete virtuale e connessioni dominio

Questa procedura verrà guidano l'utente nella creazione di un VNet e creare da sito e connessioni ExpressRoute che potranno coesistere.
    
1. È necessario installare l'ultima versione dei cmdlet di PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Si noti che potrebbero essere leggermente diversi da quello di cosa potrebbe avere familiarità con i cmdlet che verrà utilizzato per la configurazione. Assicurarsi di utilizzare i cmdlet specificati in queste istruzioni.

2. Accesso l'account e configurare l'ambiente.
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Creare una rete virtuale inclusi Subnet Gateway. Per ulteriori informazioni sulla configurazione di rete virtuale, vedere [configurazione della rete virtuale Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

    >[AZURE.IMPORTANT] Subnet Gateway deve essere /27 o un prefisso più breve (ad esempio /26 o /25).
    
    Creare un nuovo VNet.

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    Aggiungere subnet.

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Salvare la configurazione di VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Creare un gateway ExpressRoute. Per ulteriori informazioni sulla configurazione di gateway ExpressRoute, vedere [configurazione di gateway ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Il GatewaySKU deve essere *Standard*, *HighPerformance*o *UltraPerformance*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Collegare il gateway ExpressRoute al circuito ExpressRoute. Dopo aver completato questo passaggio è stabilire la connessione tra la rete locale e Azure tramite ExpressRoute. Per ulteriori informazioni sull'operazione di collegamento, vedere [Collegamento VNets a ExpressRoute](expressroute-howto-linkvnet-arm.md).

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>Creare il gateway VPN da sito. Per ulteriori informazioni sulla configurazione di gateway VPN, vedere [configurare un VNet con una connessione a siti](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). Il GatewaySKU deve essere *Standard*, *HighPerformance*o *UltraPerformance*. Il VpnType deve *RouteBased*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

    Azure gateway VPN supporta il BGP. È possibile specificare - EnableBgp il comando seguente.

        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true

    È possibile trovare BGP peering IP e il numero come utilizzato Azure per il gateway VPN in $azureVpn.BgpSettings.BgpPeeringAddress e $azureVpn.BgpSettings.Asn. Per ulteriori informazioni, vedere [Configurare BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) per gateway VPN Azure.

7. Creare un sito locale entità gateway VPN. Questo comando non configura il gateway VPN locale. Invece consente di specificare le impostazioni del gateway locale, ad esempio l'indirizzo IP pubblico e i locale di spazio di indirizzi, in modo che il gateway VPN Azure possibile collegarsi.

    Se il dispositivo VPN locale supporta solo il routing statico, è possibile configurare le route statiche nel modo seguente.

        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress

    Se il dispositivo VPN locale supporta il BGP e si desidera abilitare il routing dinamico, è necessario conoscere BGP peering IP e il numero come utilizzato dispositivo VPN locale.

        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN

8. Configurare il dispositivo VPN locale per connettersi al nuovo gateway VPN Azure. Per ulteriori informazioni sulla configurazione dei dispositivi VPN, vedere [Configurazione di dispositivi VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Collegamento del gateway VPN da sito su Azure al gateway locale.

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Per configurare le connessioni coexsiting un VNet già esistente

Se si dispone di una rete virtuale esistente, selezionare le dimensioni di subnet gateway. Se la subnet gateway /28 o /29, è necessario eliminare il gateway virtuali e aumentare le dimensioni di subnet gateway. La procedura descritta in questa sezione illustra come eseguire questa operazione.

Se il gateway iè /27 o superiore e la rete virtuale è connesso tramite ExpressRoute, è possibile ignorare la procedura seguente e andare al ["Passaggio 6 - creare un gateway VPN da sito"](#vpngw) nella sezione precedente. 

>[AZURE.NOTE] Quando si elimina il gateway esistente, le locale locale perderà la connessione alla rete virtuale mentre si lavora in questa configurazione. 

1. È necessario installare l'ultima versione dei cmdlet di PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Si noti che potrebbero essere leggermente diversi da quello di cosa potrebbe avere familiarità con i cmdlet che verrà utilizzato per la configurazione. Assicurarsi di utilizzare i cmdlet specificati in queste istruzioni. 

2. Eliminare il gateway ExpressRoute o VPN da sito esistente. 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Eliminare Subnet Gateway.
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Aggiungere una Subnet Gateway /27 pollici.
    >[AZURE.NOTE] Se non si dispongono di indirizzi IP sufficiente a sinistra della rete virtuale per aumentare la dimensione di subnet gateway, è necessario aggiungere più spazio di indirizzi IP.

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Salvare la configurazione di VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. A questo punto, sarà necessario VNet con nessun gateway. Per creare nuovo gateway e completare le connessioni, è possibile procedere con [il passaggio 4 - creare un gateway ExpressRoute](#gw), disponibili nella procedura precedente.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Per aggiungere configurazione punto al sito per il gateway VPN
È possibile eseguire la procedura seguente per aggiungere il gateway VPN in una configurazione coesistenza con la configurazione del punto al sito.

1. Aggiungere il pool di indirizzi Client VPN. 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Caricare il certificato di autenticazione VPN Azure per il gateway VPN. In questo esempio, si presuppone che il certificato è archiviato nel computer locale in cui vengono eseguiti i cmdlet di PowerShell seguenti. 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Per ulteriori informazioni sul punto to a Site VPN, vedere [configurare una connessione punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
