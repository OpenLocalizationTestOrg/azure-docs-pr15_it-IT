<properties
   pageTitle="Come configurare le connessioni VPN S2S attivo attivo con Azure VPN gateway usando Gestione risorse di Azure e PowerShell | Microsoft Azure"
   description="In questo articolo illustra la configurazione delle connessioni attivo attivo con Azure VPN gateway usando Gestione risorse di Azure e PowerShell."
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
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Configurare le connessioni VPN S2S attivo attivo con Azure VPN gateway usando Gestione risorse di Azure e PowerShell

In questo articolo sono illustrati i passaggi per creare attivo attivo tra locale e connessioni VNet a VNet utilizzando il modello di distribuzione di Manager delle risorse e PowerShell.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>Informazioni sulle connessioni locali tra disponibilità

Per ottenere disponibilità per la connettività VNet a VNet e cross-locale, occorre distribuire più gateway VPN e stabilire più connessioni in parallelo tra Azure e le reti. Vedere [altamente disponibili Cross-locale e la connettività di VNet a VNet](./vpn-gateway-highlyavailable.md) per una panoramica delle opzioni di connettività e della topologia.

In questo articolo vengono fornite istruzioni per configurare una connessione VPN locale tra attivo attivo e attivo attivo connessione tra due reti virtuali:

- [Parte 1 - creare e configurare il gateway VPN Azure in modalità attivo attivo](#aagateway)

- [Parte 2: stabilire connessioni tra locale attivo attivo](#aacrossprem)

- [Parte 3 - stabilire connessioni VNet-VNet attivo attivo](#aav2v)

- [Parte 4 - aggiornamento gateway esistente tra attivo attivo e standby attivo](#aaupdate)

È possibile combinare questi insieme per creare una topologia di rete più complesse, disponibilità che soddisfa le proprie esigenze.

>[AZURE.IMPORTANT] Si noti che la modalità di attivo attivo funziona solo in HighPerformance SKU


## <a name ="aagateway"></a>Parte 1 - creare e configurare gateway VPN attivo attivo

La procedura seguente configurerà il gateway VPN Azure in modalità attivo attivo. Principali differenze tra i gateway attivo attivo e attivo standby:

- È necessario creare due configurazioni di Gateway IP con due indirizzi IP pubblici
- È necessario impostare il flag EnableActiveActiveFeature
- Il gateway SKU deve essere HighPerformance

Altre proprietà sono diverso da quello di gateway attivo attivo. 

### <a name="before-you-begin"></a>Prima di iniziare

- Verificare di avere un abbonamento a Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
    
- È necessario installare i cmdlet PowerShell di Azure Manager delle risorse. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

### <a name="step-1---create-and-configure-vnet1"></a>Passaggio 1 - creare e configurare VNet1

#### <a name="1-declare-your-variables"></a>1. dichiarare le variabili

Per questa esercitazione, iniziamo mediante la dichiarazione le variabili. Nell'esempio seguente dichiara le variabili utilizzando i valori per questa esercitazione. Assicurarsi di sostituire i valori con il proprio durante la configurazione di produzione. Se si sta eseguendo i passaggi per acquisire familiarità con questo tipo di configurazione, è possibile utilizzare queste variabili. Modificare le variabili e quindi copiare e incollare la console di PowerShell.

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
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
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Passaggio 2: creare il gateway VPN per TestVNet1 con la modalità attivo attivo

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. creare gli indirizzi IP e gateway pubbliche le configurazioni IP

Richiedere due indirizzi IP da allocare per il gateway che si creerà per il VNet. Verrà inoltre definire la subnet e le configurazioni IP obbligatorie. 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. creare il gateway VPN con configurazione attivo attivo

Creare il gateway di rete virtuale per TestVNet1. Si noti che sono presenti due voci GatewayIpConfig e il flag EnableActiveActiveFeature è impostato. Modalità attivo attivo richiede un gateway VPN con la distribuzione di HighPerformance SKU. Creazione di un gateway può richiedere un po' di tempo (30 minuti o più).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. ottenere gli indirizzi IP pubblici gateway e l'indirizzo IP Peer BGP

Dopo aver creato il gateway, sarà necessario ottenere l'indirizzo IP Peer BGP nel Gateway VPN Azure. Questo indirizzo è necessarie per configurare il Gateway VPN Azure come Peer BGP per i dispositivi VPN locale.

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Utilizzare i cmdlet seguenti per visualizzare gli indirizzi IP pubblici due allocati per il gateway VPN e indirizzi IP Peer BGP corrispondenti per ogni istanza di gateway:

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

L'ordine dell'indirizzo IP pubblico indirizzi per le istanze di gateway e gli indirizzi di Peering BGP corrispondenti sono uguali. In questo esempio, il gateway macchina virtuale con indirizzo IP pubblico di 40.112.190.5 utilizzerà 10.12.255.4 come indirizzo Peering BGP e il gateway con 138.91.156.129 utilizzerà 10.12.255.5. Queste informazioni sono necessarie quando si configura sui dispositivi VPN locale la connessione al gateway attivo attivo. Il gateway è illustrato nella figura seguente con tutti gli indirizzi:

![gateway attivo attivo](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Dopo aver creato il gateway, è possibile utilizzare il gateway per stabilire VNet a VNet connessione o locale tra attivo attivo. Nelle sezioni seguenti illustrano i passaggi per completare l'esercizio.


## <a name ="aacrossprem"></a>Parte 2: stabilire una connessione locale tra attivo attivo

Per stabilire una connessione tra locale, è necessario creare un Gateway di rete locale per rappresentare il dispositivo VPN locale e una connessione per connettere il gateway VPN Azure con il gateway di rete locale. In questo esempio, il gateway VPN Azure è in modalità attivo attivo. Di conseguenza, anche se esiste un solo locale dispositivi VPN (gateway di rete locale) e risorse di una connessione, entrambe le istanze di gateway VPN Azure definiranno tunnel VPN S2S con il dispositivo locale.

Prima di procedere, verificare che è stata completata [parte 1](#aagateway) del presente esercizio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passaggio 1 - creare e configurare il gateway di rete locale

#### <a name="1-declare-your-variables"></a>1. dichiarare le variabili

Questo esercizio continueranno a creare la configurazione illustrata nella figura. Assicurarsi di sostituire i valori con quelli che si desidera utilizzare per la configurazione.

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

Alcuni aspetti da tenere presente circa i parametri di gateway di rete locale:

- Il gateway di rete locale può essere la stessa o di un'altra posizione e il gruppo di risorse del gateway VPN. In questo esempio viene loro in gruppi di risorse diversi ma nella stessa posizione Azure.

- Se esiste un solo dispositivo VPN locale come illustrato sopra, la connessione attivo attivo lavorare con o senza protocollo BGP. In questo esempio viene usata BGP per la connessione tra locale.

- Se è abilitato BGP, il prefisso che è necessario dichiarare per il gateway di rete locale è l'indirizzo host dell'indirizzo IP Peer BGP nel dispositivo VPN. In questo caso, è un /32 prefisso "10.52.255.253/32".

- Come promemoria, è necessario utilizzare diverse ASN BGP tra le reti locale e Azure VNet. Se sono uguali, è necessario modificare il ASN VNet se il dispositivo VPN locale utilizza già il ASN a peer con altri elementi adiacenti BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. creare il gateway di rete locale per Site5
    
Prima di continuare, verificare che si è ancora connessi alla sottoscrizione 1. Creare il gruppo di risorse se non è ancora stato creato.

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passaggio 2 - connettersi al gateway VNet e al gateway di rete locale

#### <a name="1-get-the-two-gateways"></a>1. ottenere due gateway

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. creare TestVNet1 a Site5 connessione

In questo passaggio, è necessario creare la connessione da TestVNet1 a Site5_1 con "EnableBGP" impostato su $True.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN e BGP parametri del dispositivo VPN locale

Nell'esempio seguente vengono elencati i parametri che entrerà nella sezione Configurazione BGP nel dispositivo VPN locale per questa esercitazione:

    - Site5 ASN: 65050
    - IP BGP Site5: 10.52.255.253
    - Prefissi di annunciare: (ad esempio) 10.51.0.0/16 e 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 per tunnel a 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 per tunnel a 138.91.156.129
    - Route statiche: 10.12.255.4/32 destinazione, hop successivo tunnel VPN interfaccia per 40.112.190.5 10.12.255.5/32 destinazione, hop successivo tunnel VPN interfaccia per 138.91.156.129
    - eBGP Multihop: assicurarsi che l'opzione "multihop" per eBGP è stato attivato nel dispositivo se necessario

Stabilire la connessione dopo alcuni minuti e viene avviata la sessione di peering BGP dopo la connessione IPsec. In questo esempio finora ha configurato un solo dispositivo VPN locale, risultante nel diagramma riportato di seguito:

![crossprem di attivo attivo](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Passaggio 3 - collegare due dispositivi VPN locale al gateway VPN attivo attivo

Se si dispone di due dispositivi VPN alla stessa rete locale, è possibile ottenere ridondanza doppia connettendosi gateway VPN Azure il secondo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. creare secondo gateway di rete locale per Site5

Nota l'indirizzo IP del gateway, prefisso dell'indirizzo e l'indirizzo di peering BGP per il secondo gateway di rete locale deve essere non sovrapposto al gateway di rete locale precedente della stessa rete locale. 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. collegare il gateway VNet e il secondo gateway di rete locale

Creare la connessione da TestVNet1 a Site5_2 con "EnableBGP" impostato su $True

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN e BGP parametri per il secondo dispositivo VPN locale

Allo stesso modo, sotto gli elenchi dei parametri entrerà nel secondo dispositivo VPN:

    - Site5 ASN: 65050
    - IP BGP Site5: 10.52.255.254
    - Prefissi di annunciare: (ad esempio) 10.51.0.0/16 e 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 per tunnel a 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 per tunnel a 138.91.156.129
    - Route statiche: 10.12.255.4/32 destinazione, hop successivo tunnel VPN interfaccia per 40.112.190.5 10.12.255.5/32 destinazione, hop successivo tunnel VPN interfaccia per 138.91.156.129
    - eBGP Multihop: assicurarsi che l'opzione "multihop" per eBGP è stato attivato nel dispositivo se necessario

Una volta stabilite la connessione (tunnel), sarà necessario tra duali dispositivi VPN ridondanti e tunnel connessione di rete locale Azure:

![crossprem di ridondanza doppio](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Parte 3 - stabilire una connessione di VNet-VNet attivo attivo

In questa sezione consente di creare una connessione di VNet-VNet attivo attivo con BGP. 

Continuano con le istruzioni riportate di seguito dalla procedura precedente nell'elenco precedente. È necessario completare [parte 1](#aagateway) per creare e configurare il Gateway VPN e TestVNet1 con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passaggio 1 - creare TestVNet2 e il gateway VPN

È importante per assicurarsi che lo spazio di indirizzi IP di nuova rete virtuale, TestVNet2, non si con qualsiasi dell'intervallo di VNet.

In questo esempio, le reti virtuali appartengono alla stessa sottoscrizione. È possibile configurare le connessioni VNet a VNet tra i diversi abbonamenti; [configurare una connessione VNet-VNet](./vpn-gateway-vnet-vnet-rm-ps.md) per ulteriori informazioni, vedere. Verificare che si aggiunge il "-EnableBgp $True" quando si creano le connessioni per abilitare BGP.

#### <a name="1-declare-your-variables"></a>1. dichiarare le variabili

Assicurarsi di sostituire i valori con quelli che si desidera utilizzare per la configurazione.

    $RG2           = "TestAARG2"
    $Location2     = "East US"
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
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. creare TestVNet2 nel nuovo gruppo di risorse

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. creare il gateway VPN attivo attivo per TestVNet2

Richiedere due indirizzi IP da allocare per il gateway che si creerà per il VNet. Verrà inoltre definire la subnet e le configurazioni IP obbligatorie. 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Creare il gateway VPN con il numero di AS e il contrassegno "EnableActiveActiveFeature". Si noti che è necessario ignorare predefinito ASN sul gateway VPN Azure. Gli ASN per VNets connessi devono essere diversi per abilitare BGP e transito routing.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passaggio 2 - connettersi gateway TestVNet1 e TestVNet2

In questo esempio, entrambi i gateway sono nello stesso abbonamento. È possibile completare questo passaggio nella stessa sessione di PowerShell.

#### <a name="1-get-both-gateways"></a>1. ottenere entrambi gateway

Verificare che l'accesso e connettersi all'abbonamento a 1.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. creare entrambe le connessioni

In questo passaggio si creerà la connessione da TestVNet1 a TestVNet2 e la connessione da TestVNet2 a TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Assicurarsi di abilitare BGP per entrambe le connessioni.

Al termine della procedura, la connessione definiranno in pochi minuti e il BGP sessione peering sarà la volta completata la connessione VNet a VNet con ridondanza tra due:

![v2v di attivo attivo](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Parte 4 - aggiornamento gateway esistente tra attivo attivo e standby attivo

L'ultima sezione illustrerà come è possibile configurare un gateway VPN Azure esistente standby attivo in modalità attivo attivo o viceversa.

>[AZURE.IMPORTANT] Si noti che la modalità di attivo attivo funziona solo in HighPerformance SKU

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configurare un gateway standby attivo al gateway attivo attivo

#### <a name="1-gateway-parameters"></a>1. parametri di gateway

Nell'esempio seguente converte un gateway standby attivo in un gateway attivo attivo. È necessario creare un altro indirizzo IP pubblico e quindi aggiungere una seconda configurazione di Gateway IP. Di seguito mostra i parametri utilizzati:

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. creare l'indirizzo IP pubblico, quindi aggiungere la seconda configurazione IP gateway

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. attivare la modalità attivo attivo e aggiornare il gateway

È necessario impostare l'oggetto gateway PowerShell per avviare l'aggiornamento effettivo. SKU dell'oggetto gateway devono essere modificati per HighPerformance dal momento che è stato creato in precedenza come Standard.

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

L'aggiornamento può richiedere 30-45 minuti.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configurare un gateway attivo attivo al gateway standby attivo

#### <a name="1-gateway-parameters"></a>1. parametri di gateway

Utilizzare gli stessi parametri sopra riportata, ottenere il nome della configurazione IP che si desidera rimuovere.

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. rimuovere la configurazione di gateway IP e disattivare la modalità di attivo attivo

Analogamente, è necessario impostare l'oggetto gateway PowerShell per avviare l'aggiornamento effettivo.

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

L'aggiornamento può richiedere fino a 30 su 45 minuti.


## <a name="next-steps"></a>Passaggi successivi

Una volta completata la connessione, è possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.

