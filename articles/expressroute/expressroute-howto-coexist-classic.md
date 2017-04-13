<properties
   pageTitle="Configurare le connessioni VPN Expressroute e al sito che possono coesistere | Microsoft Azure"
   description="In questo articolo illustra la configurazione ExpressRoute e una connessione VPN a siti che può coesistere per il modello di distribuzione classica."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-classic-deployment-model"></a>Configurare le connessioni dominio ExpressRoute e al sito per il modello di distribuzione classica


> [AZURE.SELECTOR]
- [PowerShell - Manager delle risorse](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - classico](expressroute-howto-coexist-classic.md)

Possibilità di configurare VPN da sito ed ExpressRoute offre molti vantaggi. È possibile configurare la rete VPN da sito come percorso sicuro failover per ExressRoute oppure utilizzare VPN da sito per connettersi ai siti che non si è connessi a ExpressRoute. La procedura per configurare entrambi gli scenari in questo articolo verranno illustrate le caratteristiche. Questo articolo si applica al modello di distribuzione classica. Questa configurazione non è disponibile nel portale.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Circuiti ExpressRoute devono essere già configurati prima di seguire le istruzioni riportate di seguito. Assicurarsi di aver seguito le guide per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e [configurare il routing](expressroute-howto-routing-classic.md) prima di eseguire la procedura seguente.

## <a name="limits-and-limitations"></a>Limiti e limitazioni

- **Transito routing non è supportato.** È possibile instradare (tramite Azure) tra la rete locale connesse tramite VPN da sito e la rete locale connessa tramite ExpressRoute.
- **Punto di sito non è supportato.** È possibile attivare le connessioni VPN punto al sito per la stessa VNet che sia connesso a ExpressRoute. Punto to a site VPN ed ExpressRoute non possono coesistere per VNet stesso.
- **Tunneling forzata non è possibile attivare il gateway VPN da sito.** È possibile solo "forzare" tutto il traffico Internet associato alla rete locale tramite ExpressRoute.
- **Base gateway SKU non è supportata.** È necessario utilizzare un gateway non base SKU per il [gateway ExpressRoute](expressroute-about-virtual-network-gateways.md) e il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Gateway VPN solo basati su route è supportata.** È necessario utilizzare un basati su route [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Route statica deve essere configurato per il gateway VPN.** Se la rete locale sia connesso a ExpressRoute e una connessione VPN da sito, è necessario disporre di una route statica configurata la rete locale per instradare la connessione VPN da sito a Internet.
- **Prima di tutto è necessario configurare gateway ExpressRoute.** Prima di aggiungere il gateway VPN da sito, è necessario creare il gateway ExpressRoute prima di tutto.

## <a name="configuration-designs"></a>Strutture di configurazione

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurare una connessione VPN da sito come percorso failover per ExpressRoute

È possibile configurare una connessione VPN da sito come una copia di backup per ExpressRoute. Si applica solo alle reti virtuali collegate al percorso peering privato Azure. Non esiste nessuna soluzione failover basata su VPN per i servizi accessibili Azure pubblico e peerings Microsoft. Circuito ExpressRoute è sempre il collegamento principale. Dati scorrerà aiutano VPN da sito solo se si verifica un errore di circuito ExpressRoute. 

![Coesistere](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurare una connessione VPN da sito a cui connettersi siti non è connessi tramite ExpressRoute

È possibile configurare la rete in cui alcuni siti connettono direttamente al Azure attraverso VPN da sito e alcuni siti connettano tramite ExpressRoute. 

![Coesistere](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] Non è possibile configurare una rete virtuale come router transito.

## <a name="selecting-the-steps-to-use"></a>Selezionare i passaggi da seguire

Sono disponibili due diversi insiemi di procedure da scegliere per configurare le connessioni che possono coesistere. La procedura di configurazione si seleziona dipenderà se si dispone di una rete virtuale esistente che si desidera connettersi o si desidera creare una nuova rete virtuale.


- Non dispone di un VNet ed è necessario crearne uno.
    
    Se non si dispone di una rete virtuale, questa procedura consentono all'utente la creazione di una nuova rete virtuale utilizzando il modello di distribuzione classica e la creazione di nuove connessioni VPN ExpressRoute e al sito. Per configurare, seguire i passaggi indicati nella sezione articolo [per creare una nuova rete virtuale e connessioni dominio](#new).

- Dispone già di un modello di distribuzione classica VNet.

    In posizione con una connessione VPN da sito esistente o ExpressRoute già si dispone di una rete virtuale. La sezione articolo [per configurare le connessioni coexsiting un VNet già esistente](#add) fornisce istruzioni dettagliate per eliminare il gateway e quindi la creazione di nuove connessioni VPN ExpressRoute e al sito. Si noti che durante la creazione di nuove connessioni, i passaggi da completare in un ordine specifico. Non usare le istruzioni in altri articoli per creare il gateway e connessioni.

    In questa procedura, la creazione di connessioni che possono coesistere sarà necessario eliminare il gateway e quindi configurare nuovo gateway. Di conseguenza, che sarà necessario del tempo di inattività per le connessioni tra locale mentre eliminare e ricreare il gateway e connessioni, ma non è necessario eseguire la migrazione di uno qualsiasi dei servizi macchine virtuali in una nuova rete virtuale. Le macchine virtuali e i servizi saranno comunque possibile comunicare tramite il servizio di bilanciamento del carico mentre si configura il gateway se sono stati configurati per eseguire questa operazione.


## <a name="new"></a>Per creare una nuova rete virtuale e connessioni dominio

Questa procedura verrà guidano l'utente nella creazione di un VNet e creare da sito e connessioni ExpressRoute che potranno coesistere.

1. È necessario installare l'ultima versione dei cmdlet di PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Si noti che potrebbero essere leggermente diversi da quello di cosa potrebbe avere familiarità con i cmdlet che verrà utilizzato per la configurazione. Assicurarsi di utilizzare i cmdlet specificati in queste istruzioni. 

2. Creare uno schema per la rete virtuale. Per ulteriori informazioni sullo schema di configurazione, vedere [schema di configurazione virtuali Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

    Quando si crea lo schema, assicurarsi di utilizzare i valori seguenti:

    - Subnet gateway per la rete virtuale deve essere /27 o un prefisso più breve (ad esempio /26 o /25).
    - Il tipo di connessione di gateway "dedicato".

              <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
                <AddressSpace>
                  <AddressPrefix>10.17.159.192/26</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Subnet-1">
                    <AddressPrefix>10.17.159.192/27</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.17.159.224/27</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>

3. Dopo la creazione e configurazione di file di schema xml, caricare il file. Per creare la rete virtuale.

    Utilizzare il cmdlet seguente per caricare un file, il valore sostituito con il proprio.

        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. <a name="gw"></a>Creare un gateway ExpressRoute. Assicurarsi di specificare il GatewaySKU come *Standard*, *HighPerformance*o *UltraPerformance* e GatewayType come *DynamicRouting*.

    Utilizzare l'esempio seguente, sostituendo i valori per il proprio.

        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Collegare il gateway ExpressRoute al circuito ExpressRoute. Dopo aver completato questo passaggio è stabilire la connessione tra la rete locale e Azure tramite ExpressRoute.

        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. <a name="vpngw"></a>Creare il gateway VPN da sito. Il GatewaySKU deve essere *Standard*, *HighPerformance*o *UltraPerformance* e il GatewayType deve essere *DynamicRouting*.

        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

    Per recuperare le impostazioni del gateway virtuali, incluso l'ID di gateway e l'indirizzo IP pubblico, utilizzare la `Get-AzureVirtualNetworkGateway` cmdlet.

        Get-AzureVirtualNetworkGateway

        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded

7. Creare un sito locale entità gateway VPN. Questo comando non configura il gateway VPN locale. Invece consente di specificare le impostazioni del gateway locale, ad esempio l'indirizzo IP pubblico e i locale di spazio di indirizzi, in modo che il gateway VPN Azure possibile collegarsi.

    >[AZURE.IMPORTANT] Nel sito locale per la rete VPN da sito non è definito nel netcfg. Se, tuttavia, è necessario utilizzare questo cmdlet per specificare i parametri di sito locale. È possibile definire tramite il portale o il file netcfg.

    Utilizzare l'esempio seguente, sostituire i valori con il proprio.

        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>

    > [AZURE.NOTE] Se la rete locale ha più route, è possibile passare esse tutto sotto forma di matrice.  $MyLocalNetworkAddress =@("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  


    Per recuperare le impostazioni del gateway virtuali, incluso l'ID di gateway e l'indirizzo IP pubblico, utilizzare la `Get-AzureVirtualNetworkGateway` cmdlet. Vedere nell'esempio seguente.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


8. Configurare il dispositivo VPN locale per connettersi al nuovo gateway. Usare le informazioni recuperati nel passaggio 6 quando si configura il dispositivo VPN. Per ulteriori informazioni sulla configurazione dei dispositivi VPN, vedere [Configurazione di dispositivi VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Collegamento del gateway VPN da sito su Azure al gateway locale.

    In questo esempio, connectedEntityId è l'ID di gateway locale, è possibile trovare eseguendo `Get-AzureLocalNetworkGateway`. È possibile trovare virtualNetworkGatewayId utilizzando il `Get-AzureVirtualNetworkGateway` cmdlet. Dopo questo passaggio è stabilire la connessione tra la rete locale e Azure tramite una connessione VPN da sito.


        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>Per configurare le connessioni coexsiting un VNet già esistente

Se si dispone di una rete virtuale esistente, selezionare le dimensioni di subnet gateway. Se la subnet gateway /28 o /29, è necessario eliminare il gateway virtuali e aumentare le dimensioni di subnet gateway. La procedura descritta in questa sezione illustra come eseguire questa operazione.

Se il gateway iè /27 o superiore e la rete virtuale è connesso tramite ExpressRoute, è possibile ignorare la procedura seguente e andare al ["Passaggio 6 - creare un gateway VPN da sito"](#vpngw) nella sezione precedente.

>[AZURE.NOTE] Quando si elimina il gateway esistente, le locale locale perderà la connessione alla rete virtuale mentre si lavora in questa configurazione.

1. È necessario installare l'ultima versione dei cmdlet di Manager delle risorse PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Si noti che potrebbero essere leggermente diversi da quello di cosa potrebbe avere familiarità con i cmdlet che verrà utilizzato per la configurazione. Assicurarsi di utilizzare i cmdlet specificati in queste istruzioni. 

2. Eliminare il gateway ExpressRoute o VPN da sito esistente. Utilizzare il cmdlet seguente sostituendo i valori con uno personalizzato.

        Remove-AzureVNetGateway –VnetName MyAzureVNET

3. Esportare lo schema di rete virtuale. Usare il seguente cmdlet di PowerShell, sostituire i valori con uno personalizzato.

        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”

4. Modificare lo schema di file di configurazione della rete in modo che il gateway iè /27 o un prefisso più breve (ad esempio /26 o /25). Vedere nell'esempio seguente. 
>[AZURE.NOTE] Se non si dispongono di indirizzi IP sufficiente a sinistra della rete virtuale per aumentare la dimensione di subnet gateway, è necessario aggiungere più spazio di indirizzi IP. Per ulteriori informazioni sullo schema di configurazione, vedere [schema di configurazione virtuali Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

5. Se il gateway precedente è stato collegato tramite VPN da sito, è anche necessario modificare il tipo di connessione su **dedicate**.

                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>

6. A questo punto, sarà necessario VNet con nessun gateway. Per creare nuovo gateway e completare le connessioni, è possibile procedere con [il passaggio 4 - creare un gateway ExpressRoute](#gw), disponibili nella procedura precedente.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md)
