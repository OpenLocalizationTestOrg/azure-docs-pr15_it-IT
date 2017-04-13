<properties 
   pageTitle="Configurare la modalità tunnel forzata nelle connessioni da sito utilizzando il modello di distribuzione classica | Microsoft Azure"
   description="Come reindirizzare o forzare tutto il traffico Internet associato alla propria posizione locale."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurare il tunneling forzata tramite il modello di distribuzione classica

> [AZURE.SELECTOR]
- [PowerShell - classico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Manager delle risorse](vpn-gateway-forced-tunneling-rm.md)

Tunneling forzata consente di reindirizzamento o "forza" tutto il traffico Internet associazione alla propria posizione locale tramite un tunnel VPN da sito per ispezione e il controllo. Questo è un requisito di protezione critici per IT aziendali la maggior parte dei criteri. 

Senza tunneling forzata, il traffico Internet associazione da nelle macchine virtuali di Azure sarà sempre scorrere dall'infrastruttura di rete Azure direttamente fuori a Internet, senza l'opzione per consentire di esaminare o controllare il traffico. Accesso a Internet non autorizzato può comportare potenzialmente divulgazione o altri tipi di violazioni della protezione.

In questo articolo verrà è illustrata la configurazione forzata tunnel nelle reti virtuali create con il modello di distribuzione classica. 

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelli di distribuzione e strumenti per tunneling forzata**

Una connessione tunneling forzata può essere configurata per il modello di distribuzione classica e il modello di distribuzione di Manager delle risorse. Nella tabella seguente per ulteriori informazioni, vedere. In questa tabella viene aggiornata appena nuovi articoli, nuovi modelli di distribuzione e altri strumenti disponibili per la configurazione. Quando un articolo è disponibile, è collega direttamente dalla tabella.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Requisiti e le informazioni

Tunnel forzata in Azure viene configurato tramite route definite dall'utente virtuali (UDR). Reindirizzare il traffico a un sito locale è espresso come Route predefinita per il gateway VPN Azure. La sezione seguente sono elencate la limitazione corrente della tabella e indirizza routing per una rete virtuale Azure:


-  Ogni subnet virtuali ha una tabella di routing predefinito, di sistema. Tabella di routing sistema è i tre gruppi seguenti percorsi:

    - **VNet locale indirizza:** Direttamente alla destinazione macchine virtuali nella stessa rete virtuale
    
    - **Su indirizza locale:** Per il gateway VPN Azure
    
    - **Route predefinita:** Direttamente a Internet. Pacchetti destinati agli indirizzi IP privati non rientranti due route precedente verranno rimosse.


-  Con la versione di route definite dall'utente, è possibile creare una tabella di routing per aggiungere una route predefinita e quindi associare la tabella di routing per la subnet VNet per abilitare il tunneling forzata su subnet.

- È necessario impostare un "sito predefinito" tra i siti locali di croce locale connesso alla rete virtuale.

- Tunneling forzata devono essere associate a un VNet contenente un gateway VPN routing dinamico (non un gateway statico).
 
- ExpressRoute forzata tunneling non è configurato tramite questo meccanismo, ma se, tuttavia, è attivata per pubblicizzare una route predefinita tramite le sessioni di peering ExpressRoute BGP. Vedere la [Documentazione ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) per ulteriori informazioni.



## <a name="configuration-overview"></a>Cenni preliminari sulla configurazione

Nell'esempio seguente Frontend subnet non viene forzata tunnel. I carichi di lavoro in subnet front-end è possibile continuare a accetta e rispondere alle richieste dei clienti direttamente da Internet. Subnet medio livello e back-end risulteranno tunnel. Verranno forzate o reindirizzare nuovamente un sito locale tramite uno dei tunnel VPN S2S tutte le connessioni in uscita da queste due subnet a Internet.

In questo modo è possibile limitare e controllare l'accesso a Internet da macchine virtuali o nel cloud i servizi di Azure, pur continuando a abilitare l'architettura multilivello servizio obbligatorio. È anche possibile applicare forzata connettersi intere reti virtuale se le reti virtuali carichi di lavoro non esposto a Internet.


![Forzata Tunneling](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre gli elementi seguenti prima di iniziare la configurazione.

- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- Una rete virtuale configurata. 

- La versione più recente dei cmdlet di PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .


## <a name="configure-forced-tunneling"></a>Configurare tunneling forzata

La procedura seguente consente di specificare la modalità tunnel forzata in una rete virtuale. I passaggi di configurazione corrispondono ai file di configurazione di rete VNet.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

In questo esempio, la rete virtuale "Multilivello VNet" sono previsti tre subnet: subnet *front-end*, *Midtier*e *back-end* , con le connessioni a quattro locale cross: *DefaultSiteHQ*e tre *diramazioni*. 

La procedura impostare *DefaultSiteHQ* come la connessione di sito predefiniti per forzata tunneling e configurare il Midtier e subnet back-end da utilizzare forzata tunneling.


1. Creare una tabella di routing. Utilizzare il cmdlet seguente per creare la tabella route.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Aggiungere una route predefinita alla tabella di routing. 

    Nell'esempio seguente aggiunge una route predefinita alla tabella di routing creata nel passaggio 1. Si noti che è supportata sola route sono il prefisso di destinazione di "0.0.0.0/0" a "VPNGateway" hop successivo.
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Associare la tabella di routing alle subnet. 

    Dopo la creazione di una tabella di routing e aggiunta una route, utilizzare l'esempio seguente per aggiungere o associare della tabella di una subnet VNet. Nell'esempio aggiunge la tabella di route "MyRouteTable" per le subnet Midtier e back-end di VNet multilivello-VNet.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Assegnare un sito predefinito per forzata tunneling. 

    Nel passaggio precedente, gli script cmdlet di esempio creata la tabella di routing e associata della tabella di due subnet VNet. Il passaggio rimanente consiste nel selezionare un sito locale tra le connessioni di più siti della rete virtuale come sito predefinito o tunnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Cmdlet di PowerShell aggiuntive

### <a name="to-delete-a-route-table"></a>Per eliminare una tabella di route

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>Per visualizzare un elenco di una tabella di routing

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Per eliminare una route da una tabella di routing

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Per rimuovere una route da una subnet

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Per visualizzare un elenco di tabella di route associata a una subnet
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Per rimuovere un sito predefinito da un gateway VNet VPN

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






