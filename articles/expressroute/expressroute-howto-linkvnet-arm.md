<properties 
   pageTitle="Creare un collegamento a un circuito ExpressRoute a una rete virtuale tramite PowerShell | Microsoft Azure"
   description="Questo documento offre una panoramica su come collegare reti virtuali (VNets) per circuiti ExpressRoute utilizzando il modello di distribuzione di Manager delle risorse e PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Creare un collegamento a un circuito ExpressRoute una rete virtuale

> [AZURE.SELECTOR]
- [Portale di Azure - Gestione risorse](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Manager delle risorse](expressroute-howto-linkvnet-arm.md)
- [PowerShell - classico](expressroute-howto-linkvnet-classic.md)


Questo articolo viene spiegato come collegare reti virtuali (VNets) circuiti Azure ExpressRoute utilizzando il modello di distribuzione di Manager delle risorse e PowerShell. Reti virtuali possono consistere nella stessa sottoscrizione o parte di un'altra sottoscrizione.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

- È necessaria la versione più recente di moduli Azure PowerShell (almeno versione 1.0). Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .
- È necessario esaminare [i prerequisiti](expressroute-prerequisites.md), i [requisiti routing](expressroute-routing.md)e i [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
- È necessario avere un circuito ExpressRoute attivo. 
    - Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e disporre circuito abilitato per il proprio provider di integrazione applicativa. 
    - Assicurarsi di avere Azure privato peering configurato per il circuito. Vedere l'articolo [configurare il routing](expressroute-howto-routing-arm.md) per instradare le istruzioni. 
    - Assicurarsi che peering privato Azure è configurato e il peering BGP tra la rete e Microsoft è in modo che è possibile abilitare la connettività-to-end.
    - Verificare di disporre di una rete virtuale e un gateway di rete virtuale creata e completamente il provisioning. Seguire le istruzioni per creare un [gateway VPN](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), ma accertarsi di usare `-GatewayType ExpressRoute`.

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuale devono essere nella stessa regione geopolitiche quando si utilizza un circuito ExpressRoute standard. 

È possibile collegare un reti virtuali all'esterno dell'area geopolitiche di circuito ExpressRoute o connettersi un numero maggiore di reti virtuali il circuito ExpressRoute se è stato attivato il componente aggiuntivo premium ExpressRoute. Controllare le [domande frequenti](expressroute-faqs.md) per altre informazioni sul componente aggiuntivo premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connettere una rete virtuale nello stesso abbonamento a un circuito

È possibile connettersi a un circuito ExpressRoute un gateway virtuali utilizzando il seguente cmdlet. Verificare che il gateway virtuali viene creato ed è pronto per il collegamento prima di eseguire il cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connettere una rete virtuale in una sottoscrizione diversa a un circuito

È possibile condividere un circuito ExpressRoute tra più abbonamenti. Nella figura seguente mostra un semplice schema di funzionamento della condivisione per circuiti ExpressRoute per più abbonamenti.

Ognuna delle aree più piccole all'interno dell'area di grandi dimensioni viene utilizzato per rappresentare le sottoscrizioni che appartengono a diversi reparti all'interno dell'organizzazione. Di ciascun reparto all'interno dell'organizzazione possono usare proprio abbonamento per distribuire i servizi, ma possono condividere un singolo circuito ExpressRoute per connettersi alla rete locale. Un singolo reparto (in questo esempio: IT) possono essere proprietari circuito ExpressRoute. Gli altri abbonamenti all'interno dell'organizzazione possono usare circuito ExpressRoute.

>[AZURE.NOTE] Le spese di connettività e della larghezza di banda per circuito dedicato verranno applicate al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.

![Connettività per la sottoscrizione](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Amministrazione

Il *proprietario di circuito* è un utente autorizzato power della risorsa circuito ExpressRoute. Il proprietario circuito può creare autorizzazioni che possono essere utilizzate dagli *utenti circuito*. *Gli utenti di circuito* sono i proprietari dei gateway virtuali (che non sono contenuti lo stesso abbonamento circuito ExpressRoute). *Gli utenti circuito* possono riscattare autorizzazioni (un'autorizzazione a virtuali).

Il *proprietario del circuito* ha la possibilità di modificare e revocare le autorizzazioni in qualsiasi momento. Revoca dei risultati un'autorizzazione in tutte le connessioni di collegamento da eliminare dall'abbonamento a cui l'accesso è stato revocato.

### <a name="circuit-owner-operations"></a>Operazioni di proprietario circuito 

#### <a name="creating-an-authorization"></a>Creazione di un'autorizzazione
    
Il proprietario circuito crea un'autorizzazione. Il risultato per la creazione di una chiave di autorizzazione che può essere utilizzata da un utente circuito per connettere i propri gateway virtuali al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

Frammento di cmdlet riportato di seguito viene illustrato come creare un'autorizzazione:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

La risposta conterrà la chiave di autorizzazione e lo stato:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>Revisione delle autorizzazioni

Il proprietario del circuito possibile esaminare tutte le autorizzazioni che vengono generate su un particolare circuito eseguendo il seguente cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>Aggiunta di autorizzazioni

Il proprietario di circuito è possibile aggiungere autorizzazioni utilizzando il seguente cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>Eliminazione delle autorizzazioni

Il proprietario del circuito possibile revoke/eliminare le autorizzazioni per l'utente eseguendo il seguente cmdlet:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Operazioni utente circuito

L'utente circuito deve ID peer e una chiave di autorizzazione dal proprietario del circuito. La chiave di autorizzazione è un GUID.

ID peer è, può essere controllato dal comando seguente.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Utilizzare le autorizzazioni di connessione

L'utente circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento:

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Rilascio di autorizzazioni di connessione

È possibile rilasciare un'autorizzazione eliminando la connessione che collega circuito ExpressRoute per la rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
