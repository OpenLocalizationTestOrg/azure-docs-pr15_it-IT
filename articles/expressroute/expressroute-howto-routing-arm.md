<properties
   pageTitle="Come configurare la distribuzione per un circuito ExpressRoute | Microsoft Azure"
   description="In questo articolo sono illustrati i passaggi per la creazione e il provisioning privato, pubblico e Microsoft peering di un circuito ExpressRoute. In questo articolo viene inoltre descritto controllare lo stato, aggiornare o eliminare peerings per il circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Creare e modificare il routing per un circuito ExpressRoute


> [AZURE.SELECTOR]
[Portale di Azure - Gestione risorse](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gestione risorse](expressroute-howto-routing-arm.md)
[PowerShell - classico](expressroute-howto-routing-classic.md)



In questo articolo sono illustrati i passaggi per creare e gestire la configurazione di routing per un circuito ExpressRoute tramite PowerShell e il modello di distribuzione di Manager delle risorse di Azure.  La procedura seguente anche illustra come controllare di stato, aggiornamento o eliminazione e il deprovisioning peerings per un circuito ExpressRoute. 


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

- È necessario l'ultima versione di moduli Azure PowerShell 1.0 o versione successiva. 
- Assicurarsi che siano state esaminate pagina [Prerequisiti](expressroute-prerequisites.md) , la pagina [routing requisiti](expressroute-routing.md) e la pagina [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
- È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e disporre circuito abilitato per il proprio provider di integrazione applicativa prima di procedere. Circuito ExpressRoute deve essere in uno stato provisioning e abilitato per poter essere in grado di eseguire i cmdlet descritti di seguito.

Queste istruzioni vengono applicate solo alle circuiti creati con provider di servizi che offre servizi di integrazione applicativa di livello 2. Se si utilizza un provider di servizi che offre servizi di livello 3 gestiti (in genere un IPVPN, ad esempio MPLS), il proprio provider di integrazione applicativa verranno configurare e gestire il routing dell'utente.

>[AZURE.IMPORTANT] È attualmente non annunciare peerings configurata dal provider di servizi tramite il portale di gestione dei servizi. Stiamo lavorando a breve per consentire questa funzionalità. Rivolgersi al provider del servizio prima di configurare peerings BGP.

È possibile configurare uno, due oppure tre peerings tutti (pubblico privato, Azure Azure e Microsoft) per un circuito ExpressRoute. È possibile configurare peerings nell'ordine desiderato. Tuttavia, è necessario assicurarsi completare la configurazione di ciascuno di essi peering alla volta. 

## <a name="azure-private-peering"></a>Peering privato Azure

In questa sezione fornisce istruzioni su come creare, visualizzare, aggiornare ed eliminare la configurazione di peering privata Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Per creare peering privato Azure

1. Importare il modulo PowerShell per ExpressRoute.
    
    È necessario installare la versione più recente di PowerShell installer dalla [Raccolta di PowerShell](http://www.powershellgallery.com/) e importare i moduli di gestione di risorse Azure nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. È necessario eseguire PowerShell come amministratore.

        Install-Module AzureRM

        Install-AzureRM

    Importare tutti i moduli AzureRM.* all'interno dell'intervallo versione semantic nota

        Import-AzureRM

    È anche possibile importare un modulo Seleziona all'interno dell'intervallo versione semantic nota 
        
        Import-Module AzureRM.Network 

    Accesso al proprio account

        Login-AzureRmAccount

    Selezionare l'abbonamento a cui che si desidera creare circuito ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Creare un circuito ExpressRoute.
    
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e ha fornito dal provider di integrazione applicativa. 

    Se il provider di integrazione applicativa offre servizi di livello 3 gestiti, è possibile richiedere al provider di integrazione applicativa per consentire peering privato Azure dell'utente. In questo caso, non sarà necessario attenersi alle istruzioni riportate nelle sezioni successive. Tuttavia, se il proprio provider di integrazione applicativa non gestisce il routing, dopo aver creato il circuito, seguire le istruzioni seguenti. 

3. Controllare il circuito ExpressRoute per garantire che viene eseguito il provisioning.

    Prima di tutto è necessario verificare il circuito ExpressRoute viene effettuato il provisioning e che anche sia abilitato. Vedere l'esempio seguente.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    La risposta sarà simile a nell'esempio seguente:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Configurare la peering Azure privata per il circuito.

    Verificare di disporre gli elementi seguenti prima di procedere con la procedura successiva:

    - Un /30 subnet per il collegamento principale. Questa operazione non deve essere parte di uno spazio indirizzo riservato per le reti virtuali.
    - Un /30 subnet per il collegamento secondario. Questa operazione non deve essere parte di uno spazio indirizzo riservato per le reti virtuali.
    - Un ID VLAN valido per stabilire questo peering su. Assicurarsi che nessun altro peering del circuito utilizzi lo stesso ID di VLAN.
    - COME numero per peering. È possibile usare 2 byte e 4 byte come numeri. È possibile utilizzare una private come numero per questo peering. Assicurarsi che non si utilizza 65515.
    - Hash MD5, se si sceglie di utilizzare uno. **Questa operazione è facoltativa**.
    
    È possibile eseguire il cmdlet seguente per configurare la peering Azure privata per il circuito.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    È possibile utilizzare il cmdlet seguente se si sceglie di utilizzare un hash MD5.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Assicurarsi di specificare il numero come come peering ASN, non customer ASN.

### <a name="to-view-azure-private-peering-details"></a>Per visualizzare i dettagli peering privati Azure

È possibile ottenere i dettagli di configurazione utilizzando il seguente cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>Per aggiornare Azure configurazione peering privata

È possibile aggiornare qualsiasi parte della configurazione utilizzando il seguente cmdlet. Nell'esempio seguente, l'ID VLAN del circuito aggiornamento da 100 e 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Per eliminare peering privato Azure

È possibile rimuovere la configurazione peering eseguendo il seguente cmdlet.

>[AZURE.WARNING] È necessario assicurarsi che tutte le reti virtuali siano scollegate dalla circuito ExpressRoute prima di eseguire questo cmdlet. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Peering pubblica Azure

In questa sezione fornisce istruzioni su come creare, visualizzare, aggiornare ed eliminare la configurazione di peering pubblica Azure per un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Per creare peering pubblica Azure

1. Importare il modulo PowerShell per ExpressRoute.
    
    È necessario installare la versione più recente di PowerShell installer dalla [Raccolta di PowerShell](http://www.powershellgallery.com/) e importare i moduli di gestione di risorse Azure nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. È necessario eseguire PowerShell come amministratore.

        Install-Module AzureRM

        Install-AzureRM

    Importare tutti i moduli AzureRM.* all'interno dell'intervallo versione semantic nota

        Import-AzureRM

    È anche possibile importare un modulo Seleziona all'interno dell'intervallo versione semantic nota 
        
        Import-Module AzureRM.Network 

    Accesso al proprio account

        Login-AzureRmAccount

    Selezionare l'abbonamento a cui che si desidera creare circuito ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Creare un circuito ExpressRoute.
    
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e ha fornito dal provider di integrazione applicativa. 

    Se il provider di integrazione applicativa offre servizi di livello 3 gestiti, è possibile richiedere al provider di integrazione applicativa per consentire peering pubblico Azure dell'utente. In questo caso, non sarà necessario attenersi alle istruzioni riportate nelle sezioni successive. Tuttavia, se il proprio provider di integrazione applicativa non gestisce il routing, dopo aver creato il circuito, seguire le istruzioni seguenti.

3. Selezionare circuito ExpressRoute per garantire che viene eseguito il provisioning.

    Prima di tutto è necessario verificare il circuito ExpressRoute viene effettuato il provisioning e che anche sia abilitato. Vedere l'esempio seguente.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    La risposta sarà simile a nell'esempio seguente:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Configurare peering pubblico Azure per il circuito.

    Assicurarsi di avere le informazioni seguenti prima di procedere ulteriormente.

    - Un /30 subnet per il collegamento principale. Deve trattarsi di un prefisso IPv4 pubblico valido.
    - Un /30 subnet per il collegamento secondario. Deve trattarsi di un prefisso IPv4 pubblico valido.
    - Un ID VLAN valido per stabilire questo peering su. Assicurarsi che nessun altro peering del circuito utilizzi lo stesso ID di VLAN.
    - COME numero per peering. È possibile usare 2 byte e 4 byte come numeri.
    - Hash MD5, se si sceglie di utilizzare uno. **Questa operazione è facoltativa**.
    
    È possibile eseguire il cmdlet seguente per configurare peering pubblico Azure per il circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    È possibile utilizzare il cmdlet seguente se si sceglie di utilizzare un hash MD5

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Assicurarsi di specificare il numero come come ASN peering e non customer ASN.

### <a name="to-view-azure-public-peering-details"></a>Per visualizzare i dettagli peering pubblici Azure

È possibile ottenere i dettagli di configurazione utilizzando il seguente cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Per aggiornare Azure configurazione peering pubblica

È possibile aggiornare qualsiasi parte della configurazione utilizzando il seguente cmdlet

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

L'ID VLAN del circuito aggiornamento da 200 a 600 nell'esempio precedente.

### <a name="to-delete-azure-public-peering"></a>Per eliminare peering pubblica Azure

È possibile rimuovere la configurazione peering eseguendo il seguente cmdlet

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft peering

In questa sezione fornisce istruzioni su come creare, visualizzare, aggiornare ed eliminare la configurazione di peering Microsoft per un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Per creare Microsoft peering

1. Importare il modulo PowerShell per ExpressRoute.
    
    È necessario installare la versione più recente di PowerShell installer dalla [Raccolta di PowerShell](http://www.powershellgallery.com/) e importare i moduli di gestione di risorse Azure nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. È necessario eseguire PowerShell come amministratore.

        Install-Module AzureRM

        Install-AzureRM

    Importare tutti i moduli AzureRM.* all'interno dell'intervallo versione semantic nota

        Import-AzureRM

    È anche possibile importare un modulo Seleziona all'interno dell'intervallo versione semantic nota 
        
        Import-Module AzureRM.Network 

    Accesso al proprio account

        Login-AzureRmAccount

    Selezionare l'abbonamento a cui che si desidera creare circuito ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Creare un circuito ExpressRoute.
    
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e ha fornito dal provider di integrazione applicativa. 

    Se il provider di integrazione applicativa offre servizi di livello 3 gestiti, è possibile richiedere al provider di integrazione applicativa per consentire peering privato Azure dell'utente. In questo caso, non sarà necessario attenersi alle istruzioni riportate nelle sezioni successive. Tuttavia, se il proprio provider di integrazione applicativa non gestisce il routing, dopo aver creato il circuito, seguire le istruzioni seguenti.

3. Selezionare circuito ExpressRoute per garantire che viene eseguito il provisioning.

    Prima di tutto è necessario verificare il circuito ExpressRoute viene effettuato il provisioning e che anche sia abilitato. Vedere l'esempio seguente.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    La risposta sarà simile a nell'esempio seguente:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Configurare Microsoft peering per il circuito.

    Assicurarsi di avere le informazioni seguenti prima di procedere.

    - Un /30 subnet per il collegamento principale. Deve trattarsi di un pubblico IPv4 prefisso valido di proprietà dell'utente ed è registrato in un RIR / TIR.
    - Un /30 subnet per il collegamento secondario. Deve trattarsi di un pubblico IPv4 prefisso valido di proprietà dell'utente ed è registrato in un RIR / TIR.
    - Un ID VLAN valido per stabilire questo peering su. Assicurarsi che nessun altro peering del circuito utilizzi lo stesso ID di VLAN.
    - COME numero per peering. È possibile usare 2 byte e 4 byte come numeri.
    - Annunciato prefissi: È necessario specificare un elenco di tutti i prefissi si intende per pubblicizzare nella sessione BGP. Solo prefissi indirizzo IP pubblici vengono accettati. Se si intende inviare un set di prefissi, è possibile inviare un elenco di valori separati da virgola. Questi prefissi devono essere registrati per l'utente in un RIR / TIR.
    - Cliente ASN: Se si è prefissi relative alla pubblicità che non sono registrati per peering come numero, è possibile specificare il numero di AS a cui sono registrate. **Questa operazione è facoltativa**.
    - Nome del Registro di sistema routing: È possibile specificare il RIR / IRR rispetto al quale il numero e prefissi registrati.
    - Un MD5 hashing se si sceglie di utilizzare uno. **Questo è facoltativo.**
    
    È possibile eseguire il cmdlet seguente per configurare Microsoft peering per il circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>Per ottenere informazioni dettagliate peering Microsoft

È possibile ottenere i dettagli di configurazione utilizzando il seguente cmdlet.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Per aggiornare configurazione peering Microsoft

È possibile aggiornare qualsiasi parte della configurazione utilizzando il seguente cmdlet.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>Per eliminare peering Microsoft

È possibile rimuovere la configurazione peering eseguendo il seguente cmdlet.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Passaggi successivi

Passaggio successivo, [collegamento VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

-  Per ulteriori informazioni sui flussi di lavoro ExpressRoute, vedere [ExpressRoute flussi di lavoro](expressroute-workflows.md).

-  Per ulteriori informazioni su circuito peering, vedere [ExpressRoute circuiti e domini di routing](expressroute-circuit-peerings.md).

-  Per ulteriori informazioni sull'uso di reti virtuali, vedere [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md).

