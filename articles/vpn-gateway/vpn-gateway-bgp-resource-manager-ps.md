<properties
   pageTitle="Come configurare BGP in Azure VPN gateway usando Gestione risorse di Azure e PowerShell | Microsoft Azure"
   description="In questo articolo illustra la configurazione BGP con Azure VPN gateway usando Gestione risorse di Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Come configurare BGP in Azure VPN gateway usando Gestione risorse di Azure e PowerShell

In questo articolo vengono illustrati i passaggi per abilitare BGP in una connessione VPN a siti (S2S) tra locale e una connessione di VNet a VNet utilizzando il modello di distribuzione di Manager delle risorse e PowerShell.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>Sulle BGP

BGP è il protocollo routing standard comunemente usato in Internet per lo scambio di informazioni su routing e accessibilità tra due o più reti. BGP Abilita i gateway VPN Azure e i dispositivi VPN locali, denominati BGP peer o vicini per exchange "indirizza" che informano entrambi gateway sulla disponibilità e accessibilità per tali prefissi elaborata i gateway o router correlate. BGP è inoltre possibile abilitare il routing transito tra più reti mediante propagazione indirizza che un gateway BGP le apprende da un peer BGP a tutti gli altri peer BGP.

Per ulteriori informazioni sui vantaggi del BGP e da comprendere i requisiti tecnici e le considerazioni dell'utilizzo BGP, vedere [Panoramica di BGP con Azure VPN gateway](./vpn-gateway-bgp-overview.md) .

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Guida introduttiva a BGP su gateway VPN Azure

In questo articolo fornisce istruzioni dettagliate tramite i passaggi da eseguire le operazioni seguenti:

- [Parte 1 - abilita BGP sul gateway VPN Azure](#enablebgp)

- [Parte 2: stabilire una connessione tra locale con BGP](#crossprembgp)

- [Parte 3 - stabilire una connessione VNet a VNet con BGP](#v2vbgp)

Ogni parte delle istruzioni dei moduli di un blocco predefinito di base per l'attivazione BGP nella connettività di rete. Dopo aver completato tutte le tre parti, si creerà la topologia come illustrato nella figura seguente:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

È possibile combinare questi insieme per creare una rete transito più complesse, più pensa, in base alle esigenze.

## <a name ="enablebgp"></a>Parte 1: configurare BGP nel Gateway VPN Azure

La procedura di configurazione seguente verrà impostazione parametri di BGP del gateway VPN Azure come illustrato nella figura seguente:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Prima di iniziare

- Verificare di avere un abbonamento a Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
    
- È necessario installare i cmdlet PowerShell di Azure Manager delle risorse. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

### <a name="step-1---create-and-configure-vnet1"></a>Passaggio 1 - creare e configurare VNet1 

#### <a name="1-declare-your-variables"></a>1. dichiarare le variabili

Per questa esercitazione, iniziamo mediante la dichiarazione le variabili. Nell'esempio seguente dichiara le variabili utilizzando i valori per questa esercitazione. Assicurarsi di sostituire i valori con il proprio durante la configurazione di produzione. Se si sta eseguendo i passaggi per acquisire familiarità con questo tipo di configurazione, è possibile utilizzare queste variabili. Modificare le variabili e quindi copiare e incollare la console di PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. connettersi al proprio abbonamento e creare un nuovo gruppo di risorse

Verificare che si passa alla modalità di PowerShell per utilizzare i cmdlet Gestione risorse. Per ulteriori informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi all'account. Utilizzare nell'esempio seguente viene per la connessione:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. creare TestVNet1

Nell'esempio seguente viene creato un virtuali denominati TestVNet1 e tre subnet, una chiamata GatewaySubnet, FrontEnd chiamato un e back-end si chiama un. La sostituzione di valori, è importante denominare sempre subnet gateway specificamente GatewaySubnet. Se si denominarlo un altro elemento, non verrà creato il gateway. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passaggio 2: creare il Gateway VPN per TestVNet1 con i parametri BGP

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. creare le configurazioni di indirizzi IP e subnet

Richiedere un indirizzo IP pubblico da allocare per il gateway che si creerà per il VNet. Verrà inoltre definire la subnet e le configurazioni IP obbligatorie. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. creare il gateway VPN con il numero AS

Creare il gateway di rete virtuale per TestVNet1. Notare che BGP richiede un gateway VPN con la distribuzione e il parametro di addizione, - Asn, impostare ASN (numero AS) per TestVNet1. Creazione di un gateway può richiedere un po' di tempo (30 minuti o più).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. ottenere l'indirizzo IP di Peer BGP Azure

Dopo aver creato il gateway, sarà necessario ottenere l'indirizzo IP Peer BGP nel Gateway VPN Azure. Questo indirizzo è necessarie per configurare il Gateway VPN Azure come Peer BGP per i dispositivi VPN locale.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

L'ultimo comando illustra le configurazioni BGP corrispondente nel Gateway VPN Azure; Per esempio:

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Dopo aver creato il gateway, è possibile utilizzare il gateway per stabilire una connessione tra locale o VNet a VNet con BGP. Nelle sezioni seguenti illustrano i passaggi per completare l'esercizio.

## <a name ="crossprembbgp"></a>Parte 2: stabilire una connessione tra locale con BGP

Per stabilire una connessione tra locale, è necessario creare un Gateway di rete locale per rappresentare il dispositivo VPN locale e una connessione per connettere il gateway VPN Azure con il gateway di rete locale. La differenza tra le istruzioni fornite in questo articolo è proprietà aggiuntive è necessaria specificare i parametri di configurazione BGP.

![BGP per tra locale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Prima di procedere, verificare che è stata completata [parte 1](#enablebgp) del presente esercizio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passaggio 1 - creare e configurare il gateway di rete locale

#### <a name="1-declare-your-variables"></a>1. dichiarare le variabili

Questo esercizio continueranno a creare la configurazione illustrata nella figura. Assicurarsi di sostituire i valori con quelli che si desidera utilizzare per la configurazione.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Alcuni aspetti da tenere presente circa i parametri di gateway di rete locale:

- Il gateway di rete locale può essere la stessa o di un'altra posizione e il gruppo di risorse del gateway VPN. In questo esempio viene loro in gruppi di risorse diversi in un'altra posizione.

- Il prefisso minimo che è necessario dichiarare per il gateway di rete locale è l'indirizzo host dell'indirizzo IP Peer BGP nel dispositivo VPN. In questo caso, è un /32 prefisso "10.52.255.254/32".

- Come promemoria, è necessario utilizzare diverse ASN BGP tra le reti locale e Azure VNet. Se sono uguali, è necessario modificare il ASN VNet se il dispositivo VPN locale utilizza già il ASN per peer con altri elementi adiacenti BGP.
    
Prima di continuare, verificare che si è ancora connessi alla sottoscrizione 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. creare il gateway di rete locale per Site5

Assicurarsi di creare il gruppo di risorse se che non è stata creata, prima di creare il gateway di rete locale. Si noti due parametri aggiuntivi per il gateway di rete locale: Asn e BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passaggio 2 - connettersi al gateway VNet e al gateway di rete locale

#### <a name="1-get-the-two-gateways"></a>1. ottenere due gateway

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. creare TestVNet1 a Site5 connessione

In questo passaggio, è necessario creare la connessione da TestVNet1 a Site5. È necessario specificare "-EnableBGP $True" attivare BGP per questa connessione. Come descritto in precedenza, è possibile avere BGP e non BGP connessioni per lo stesso Gateway VPN Azure. A meno che non BGP è abilitato la proprietà di connessione, Azure non abilita BGP per questa connessione anche se i parametri BGP già configurati per entrambi i gateway.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


Nell'esempio seguente vengono elencati i parametri che entrerà nella sezione Configurazione BGP nel dispositivo VPN locale per questa esercitazione:

    - Site5 ASN: 65050
    - IP BGP Site5: 10.52.255.254
    - Prefissi di annunciare: (ad esempio) 10.51.0.0/16 e 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP: 10.12.255.30
    - Route statica: aggiungere una route per 10.12.255.30/32, con hop successivo viene l'interfaccia di tunnel VPN nel dispositivo
    - eBGP Multihop: assicurarsi che l'opzione "multihop" per eBGP è stato attivato nel dispositivo se necessario

Stabilire la connessione dopo alcuni minuti e viene avviata la sessione di peering BGP dopo la connessione IPsec.
 
## <a name ="v2vbgp"></a>Parte 3 - stabilire una connessione VNet a VNet con BGP

In questa sezione consente di aggiungere una connessione VNet a VNet con BGP, come illustrato nella figura seguente. 

![BGP per VNet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Continuano con le istruzioni riportate di seguito dalla procedura precedente nell'elenco precedente. È necessario completare [parte I](#enablebgp) per creare e configurare il Gateway VPN e TestVNet1 con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passaggio 1 - creare TestVNet2 e il gateway VPN

È importante per assicurarsi che lo spazio di indirizzi IP di nuova rete virtuale, TestVNet2, non si con qualsiasi dell'intervallo di VNet.

In questo esempio, le reti virtuali appartengono alla stessa sottoscrizione. È ora possibile configurare le connessioni VNet a VNet tra i diversi abbonamenti; [configurare una connessione VNet-VNet](./vpn-gateway-vnet-vnet-rm-ps.md) per ulteriori informazioni, vedere. Verificare che si aggiunge il "-EnableBgp $True" quando si creano le connessioni per abilitare BGP.

#### <a name="1-declare-your-variables"></a>1. dichiarare le variabili

Assicurarsi di sostituire i valori con quelli che si desidera utilizzare per la configurazione.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. creare TestVNet2 nel nuovo gruppo di risorse

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. creare il gateway VPN per TestVNet2 con i parametri BGP

Richiedere un indirizzo IP pubblico da allocare per il gateway che si creerà per il VNet. Verrà inoltre definire la subnet e le configurazioni IP obbligatorie. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Creare il gateway VPN con il numero AS. Si noti che è necessario ignorare predefinito ASN sul gateway VPN Azure. Gli ASN per VNets connessi devono essere diversi per abilitare BGP e transito routing.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passaggio 2 - connettersi gateway TestVNet1 e TestVNet2

In questo esempio, entrambi i gateway sono nello stesso abbonamento. È possibile completare questo passaggio nella stessa sessione di PowerShell.

#### <a name="1-get-both-gateways"></a>1. ottenere entrambi gateway

Assicurarsi che accedere e connettersi all'abbonamento a 1.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. creare entrambe le connessioni

In questo passaggio si creerà la connessione da TestVNet1 a TestVNet2 e la connessione da TestVNet2 a TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Assicurarsi di abilitare BGP per entrambe le connessioni.

Dopo aver completato la procedura seguente, la connessione verranno stabilite in pochi minuti e la sessione peering BGP sarà una volta completata la connessione VNet a VNet.

Se è stata completata tutte le tre parti del presente esercizio, verrà stabilita una topologia di rete come illustrato di seguito:

![BGP per VNet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Passaggi successivi

Una volta completata la connessione, è possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.

