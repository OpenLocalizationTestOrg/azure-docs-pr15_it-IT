<properties 
   pageTitle="Configurare la modalità tunnel forzata nelle connessioni da sito utilizzando il modello di distribuzione di gestione risorse | Microsoft Azure"
   description="Come reindirizzare o forzare tutto il traffico Internet associato alla propria posizione locale."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurare il tunneling forzata tramite il modello di distribuzione di Manager delle risorse di Azure

> [AZURE.SELECTOR]
- [PowerShell - classico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Manager delle risorse](vpn-gateway-forced-tunneling-rm.md)

Tunneling forzata consente di reindirizzamento o "forza" tutto il traffico Internet associazione alla propria posizione locale tramite un tunnel VPN da sito per ispezione e il controllo. Questo è un requisito di protezione critici per IT aziendali la maggior parte dei criteri.

Senza tunneling forzata, il traffico Internet associazione da nelle macchine virtuali di Azure sarà sempre scorrere dall'infrastruttura di rete Azure direttamente fuori a Internet, senza l'opzione per consentire di esaminare o controllare il traffico. Può consentire l'accesso non autorizzato a Internet di intercettazione di informazioni personali o altri tipi di violazioni della protezione

In questo articolo illustra la configurazione forzata tunnel nelle reti virtuali create con il modello di distribuzione di Manager delle risorse.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelli di distribuzione e strumenti per tunneling forzata**

Una connessione tunneling forzata può essere configurata per il modello di distribuzione classica e il modello di distribuzione di Manager delle risorse. Nella tabella seguente per ulteriori informazioni, vedere. In questa tabella viene aggiornata appena nuovi articoli, nuovi modelli di distribuzione e altri strumenti disponibili per la configurazione. Quando un articolo è disponibile, è collega direttamente dalla tabella.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Informazioni su forzata tunneling


Il diagramma seguente illustra come forzata works tunneling. 

![Forzata Tunneling](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Nell'esempio precedente, Frontend subnet non viene forzata tunnel. I carichi di lavoro in subnet front-end è possibile continuare a accetta e rispondere alle richieste dei clienti direttamente da Internet. Subnet medio livello e back-end risulteranno tunnel. Verranno forzate o reindirizzare nuovamente un sito locale tramite uno dei tunnel VPN S2S tutte le connessioni in uscita da queste due subnet a Internet.

In questo modo è possibile limitare e controllare l'accesso a Internet da macchine virtuali o nel cloud i servizi di Azure, pur continuando a abilitare l'architettura multilivello servizio obbligatorio. È anche possibile applicare forzata connettersi intere reti virtuale se le reti virtuali carichi di lavoro non esposto a Internet.

## <a name="requirements-and-considerations"></a>Requisiti e le informazioni

Tunnel forzata in Azure viene configurato tramite percorsi di rete virtuale definite dall'utente. Reindirizzare il traffico a un sito locale è espresso come Route predefinita per il gateway VPN Azure. Per ulteriori informazioni su routing definite dall'utente e le reti virtuali, vedere [route e inoltro IP definite dall'utente](../virtual-network/virtual-networks-udr-overview.md).

- Ogni subnet virtuali ha una tabella di routing predefinito, di sistema. Tabella di routing sistema è i tre gruppi seguenti percorsi:

    - **VNet locale indirizza:** Direttamente alla destinazione macchine virtuali nella stessa rete virtuale
    
    - **Indirizza locale:** Per il gateway VPN Azure
    
    - **Route predefinita:** Direttamente a Internet. Pacchetti destinati agli indirizzi IP privati non rientranti due route precedente verranno rimosse.

-  Questa procedura utilizza route definite dall'utente (UDR) per creare una tabella di routing per aggiungere una route predefinita e quindi associare la tabella di routing per la subnet VNet per abilitare il tunneling forzata su subnet.

- Tunneling forzata devono essere associate a un VNet contenente un gateway VPN basate su route. È necessario impostare un "sito predefinito" tra i siti locali di croce locale connesso alla rete virtuale.

- ExpressRoute forzata tunneling non è configurato tramite questo meccanismo, ma se, tuttavia, è attivata per pubblicizzare una route predefinita tramite le sessioni di peering ExpressRoute BGP. Vedere la [Documentazione ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) per ulteriori informazioni.

## <a name="configuration-overview"></a>Cenni preliminari sulla configurazione

La procedura seguente consente di creare un gruppo di risorse e un VNet. È quindi necessario creare un gateway VPN e configurare tunneling forzata. In questa procedura, la rete virtuale "Multilivello VNet" contiene 3 subnet: *front-end*, *Midtier*e *back-end*con 4 tra locale connessioni: *DefaultSiteHQ*e 3 *diramazioni*.

La procedura *DefaultSiteHQ* come la connessione di sito predefiniti per forzata tunneling e configura il Midtier e subnet back-end da utilizzare forzata tunneling.

    
## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre gli elementi seguenti prima di iniziare la configurazione.

- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- È necessario installare l'ultima versione dei cmdlet di Manager delle risorse PowerShell Azure (1.0 o versioni successive). Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .


## <a name="configure-forced-tunneling"></a>Configurare tunneling forzata

1. Nella console di PowerShell, accedere al proprio account Azure. Questo cmdlet richiede le credenziali di accesso per il proprio Account Azure. Dopo avere effettuato l'accesso, il download delle impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

        Login-AzureRmAccount 

2. È possibile ottenere un elenco delle sottoscrizioni Azure.

        Get-AzureRmSubscription

2. Specificare l'abbonamento a cui si desidera utilizzare. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Creare un gruppo di risorse.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Creare una rete virtuale e specificare subnet. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Creare il gateway di rete locale.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Creare la tabella e regola la distribuzione.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Associare della tabella di subnet Midtier e back-end.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Creare il Gateway con un sito predefinito. Questo passaggio necessario del tempo per completare, a volte 45 minuti o più, la creazione e la configurazione del gateway.<br> Il `-GatewayDefaultSite` è il parametro cmdlet consente la configurazione di routing forzata di lavorare, pertanto fare attenzione per configurare questa impostazione in modo corretto. Questo parametro è disponibile in PowerShell 1.0 o versione successiva.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Stabilire connessioni VPN da sito.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



