<properties
   pageTitle="Come configurare la distribuzione per un circuito ExpressRoute per il modello di distribuzione classica tramite PowerShell | Microsoft Azure"
   description="In questo articolo sono illustrati i passaggi per la creazione e il provisioning privato, pubblico e Microsoft peering di un circuito ExpressRoute. In questo articolo viene inoltre descritto controllare lo stato, aggiornare o eliminare peerings per il circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Creare e modificare il routing per un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portale di Azure - Gestione risorse](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gestione risorse](expressroute-howto-routing-arm.md)
[PowerShell - classico](expressroute-howto-routing-classic.md)



In questo articolo sono illustrati i passaggi per creare e gestire la configurazione del routing per un circuito ExpressRoute tramite PowerShell e il modello di distribuzione classica. La procedura seguente anche illustra come controllare di stato, aggiornamento o eliminazione e il deprovisioning peerings per un circuito ExpressRoute.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

- È necessario l'ultima versione di moduli Azure PowerShell. È possibile scaricare il modulo PowerShell più recente dalla sezione PowerShell della [pagina di download di Azure](https://azure.microsoft.com/downloads/). Seguire le istruzioni nella pagina [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per istruzioni dettagliate su come configurare il computer per usare i moduli di Azure PowerShell. 
- Assicurarsi che siano state esaminate pagina [Prerequisiti](expressroute-prerequisites.md) , la pagina [routing requisiti](expressroute-routing.md) e la pagina [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
- È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e disporre circuito abilitato per il proprio provider di integrazione applicativa prima di procedere. Circuito ExpressRoute deve essere in uno stato provisioning e abilitato per poter essere in grado di eseguire i cmdlet descritti di seguito.

>[AZURE.IMPORTANT] Queste istruzioni vengono applicate solo alle circuiti creati con provider di servizi che offre servizi di integrazione applicativa di livello 2. Se si utilizza un provider di servizi che offre servizi di livello 3 gestiti (in genere un IPVPN, ad esempio MPLS), il proprio provider di integrazione applicativa verranno configurare e gestire il routing dell'utente.

È possibile configurare uno, due oppure tre peerings tutti (pubblico privato, Azure Azure e Microsoft) per un circuito ExpressRoute. È possibile configurare peerings nell'ordine desiderato. Tuttavia, è necessario assicurarsi completare la configurazione di ciascuno di essi peering alla volta. 

## <a name="azure-private-peering"></a>Peering privato Azure

In questa sezione fornisce istruzioni su come creare, visualizzare, aggiornare ed eliminare la configurazione di peering privata Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Per creare peering privato Azure

1. **Importare il modulo PowerShell per ExpressRoute.**
    
    Importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. Eseguire i comandi seguenti per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Creare un circuito ExpressRoute.**
    
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e ha fornito dal provider di integrazione applicativa. Se il provider di integrazione applicativa offre servizi di livello 3 gestiti, è possibile richiedere al provider di integrazione applicativa per consentire peering privato Azure dell'utente. In questo caso, non sarà necessario attenersi alle istruzioni riportate nelle sezioni successive. Tuttavia, se il proprio provider di integrazione applicativa non gestisce il routing, dopo aver creato il circuito, seguire le istruzioni seguenti. 

3. **Controllare il circuito ExpressRoute per garantire che viene eseguito il provisioning.**

    Prima di tutto è necessario verificare il circuito ExpressRoute viene effettuato il provisioning e che anche sia abilitato. Vedere l'esempio seguente.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assicurarsi che il circuito Mostra come Provisioned e abilitata. In caso contrario, interagire con il provider di integrazione applicativa per ottenere il circuito con lo stato e dello stato obbligatorio.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurare la peering Azure privata per il circuito.**

    Verificare di disporre gli elementi seguenti prima di procedere con la procedura successiva:

    - Un /30 subnet per il collegamento principale. Questa operazione non deve essere parte di uno spazio indirizzo riservato per le reti virtuali.
    - Un /30 subnet per il collegamento secondario. Questa operazione non deve essere parte di uno spazio indirizzo riservato per le reti virtuali.
    - Un ID VLAN valido per stabilire questo peering su. Assicurarsi che nessun altro peering del circuito utilizzi lo stesso ID di VLAN.
    - COME numero per peering. È possibile usare 2 byte e 4 byte come numeri. È possibile utilizzare una private come numero per questo peering. Assicurarsi che non si utilizza 65515.
    - Hash MD5, se si sceglie di utilizzare uno. **Questa operazione è facoltativa**.
    
    È possibile eseguire il cmdlet seguente per configurare la peering Azure privata per il circuito.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

    È possibile utilizzare il cmdlet seguente se si sceglie di utilizzare un hash MD5.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Assicurarsi di specificare il numero come come peering ASN, non customer ASN.

### <a name="to-view-azure-private-peering-details"></a>Per visualizzare i dettagli peering privati Azure

È possibile ottenere i dettagli di configurazione utilizzando il seguente cmdlet

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Per aggiornare Azure configurazione peering privata

È possibile aggiornare qualsiasi parte della configurazione utilizzando il seguente cmdlet. Nell'esempio seguente, l'ID VLAN del circuito aggiornamento da 100 e 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Per eliminare peering privato Azure

È possibile rimuovere la configurazione peering eseguendo il seguente cmdlet.

>[AZURE.WARNING] È necessario assicurarsi che tutte le reti virtuali siano scollegate dalla circuito ExpressRoute prima di eseguire questo cmdlet. 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Peering pubblica Azure

In questa sezione fornisce istruzioni su come creare, visualizzare, aggiornare ed eliminare la configurazione di peering pubblica Azure per un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Per creare peering pubblica Azure

1. **Importare il modulo PowerShell per ExpressRoute.**
    
    Importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. Eseguire i comandi seguenti per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. 

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Creare un circuito ExpressRoute**
    
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e ha fornito dal provider di integrazione applicativa. Se il provider di integrazione applicativa offre servizi di livello 3 gestiti, è possibile richiedere al provider di integrazione applicativa per consentire peering pubblico Azure dell'utente. In questo caso, non sarà necessario attenersi alle istruzioni riportate nelle sezioni successive. Tuttavia, se il proprio provider di integrazione applicativa non gestisce il routing, dopo aver creato il circuito, seguire le istruzioni seguenti.

3. **Controllare circuito ExpressRoute per garantire che viene eseguito il provisioning**

    Prima di tutto è necessario verificare il circuito ExpressRoute viene effettuato il provisioning e che anche sia abilitato. Vedere l'esempio seguente.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assicurarsi che il circuito Mostra come Provisioned e abilitata. In caso contrario, interagire con il provider di integrazione applicativa per ottenere il circuito con lo stato e dello stato obbligatorio.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled

    

4. **Configurare peering pubblico Azure per il circuito**

    Assicurarsi di avere le informazioni seguenti prima di procedere ulteriormente.

    - Un /30 subnet per il collegamento principale. Deve trattarsi di un prefisso IPv4 pubblico valido.
    - Un /30 subnet per il collegamento secondario. Deve trattarsi di un prefisso IPv4 pubblico valido.
    - Un ID VLAN valido per stabilire questo peering su. Assicurarsi che nessun altro peering del circuito utilizzi lo stesso ID di VLAN.
    - COME numero per peering. È possibile usare 2 byte e 4 byte come numeri.
    - Hash MD5, se si sceglie di utilizzare uno. **Questa operazione è facoltativa**.
    
    È possibile eseguire il cmdlet seguente per configurare peering pubblico Azure per il circuito

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

    È possibile utilizzare il cmdlet seguente se si sceglie di utilizzare un hash MD5

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Assicurarsi di specificare il numero come come ASN peering e non customer ASN.

### <a name="to-view-azure-public-peering-details"></a>Per visualizzare i dettagli peering pubblici Azure

È possibile ottenere i dettagli di configurazione utilizzando il seguente cmdlet

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Per aggiornare Azure configurazione peering pubblica

È possibile aggiornare qualsiasi parte della configurazione utilizzando il seguente cmdlet

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

L'ID VLAN del circuito aggiornamento da 200 a 600 nell'esempio precedente.

### <a name="to-delete-azure-public-peering"></a>Per eliminare peering pubblica Azure

È possibile rimuovere la configurazione peering eseguendo il seguente cmdlet

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Microsoft peering

In questa sezione fornisce istruzioni su come creare, visualizzare, aggiornare ed eliminare la configurazione di peering Microsoft per un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Per creare Microsoft peering

1. **Importare il modulo PowerShell per ExpressRoute.**
    
    Importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. Eseguire i comandi seguenti per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Creare un circuito ExpressRoute**
    
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e ha fornito dal provider di integrazione applicativa. Se il provider di integrazione applicativa offre servizi di livello 3 gestiti, è possibile richiedere al provider di integrazione applicativa per consentire peering privato Azure dell'utente. In questo caso, non sarà necessario attenersi alle istruzioni riportate nelle sezioni successive. Tuttavia, se il proprio provider di integrazione applicativa non gestisce il routing, dopo aver creato il circuito, seguire le istruzioni seguenti.

3. **Controllare circuito ExpressRoute per garantire che viene eseguito il provisioning**

    È innanzitutto necessario verificare se il circuito ExpressRoute è stato Provisioned e abilitata.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assicurarsi che il circuito Mostra come Provisioned e abilitata. In caso contrario, interagire con il provider di integrazione applicativa per ottenere il circuito con lo stato e dello stato obbligatorio.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurare Microsoft peering per il circuito**

    Assicurarsi di avere le informazioni seguenti prima di procedere.

    - Un /30 subnet per il collegamento principale. Deve trattarsi di un pubblico IPv4 prefisso valido di proprietà dell'utente ed è registrato in un RIR / TIR.
    - Un /30 subnet per il collegamento secondario. Deve trattarsi di un pubblico IPv4 prefisso valido di proprietà dell'utente ed è registrato in un RIR / TIR.
    - Un ID VLAN valido per stabilire questo peering su. Assicurarsi che nessun altro peering del circuito utilizzi lo stesso ID di VLAN.
    - COME numero per peering. È possibile usare 2 byte e 4 byte come numeri.
    - Annunciato prefissi: È necessario specificare un elenco di tutti i prefissi si intende per pubblicizzare nella sessione BGP. Solo prefissi indirizzo IP pubblici vengono accettati. Se si intende inviare un set di prefissi, è possibile inviare un elenco di valori separati da virgola. Questi prefissi devono essere registrati per l'utente in un RIR / TIR.
    - Cliente ASN: Se si è prefissi relative alla pubblicità che non sono registrati per peering come numero, è possibile specificare il numero di AS a cui sono registrate. **Questa operazione è facoltativa**.
    - Nome del Registro di sistema routing: È possibile specificare il RIR / IRR rispetto al quale il numero e prefissi registrati.
    - Un MD5 hashing se si sceglie di utilizzare uno. **Questo è facoltativo.**
    
    È possibile eseguire il cmdlet seguente per configurare pering Microsoft per il circuito

        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### <a name="to-view-microsoft-peering-details"></a>Per visualizzare i dettagli peering Microsoft

È possibile ottenere i dettagli di configurazione utilizzando il seguente cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Per aggiornare configurazione peering Microsoft

È possibile aggiornare qualsiasi parte della configurazione utilizzando il seguente cmdlet.

        Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Per eliminare peering Microsoft

È possibile rimuovere la configurazione peering eseguendo il seguente cmdlet.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Passaggi successivi

Quindi, [collegamento VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md).


-  Per ulteriori informazioni sui flussi di lavoro, vedere [ExpressRoute flussi di lavoro](expressroute-workflows.md).
-  Per ulteriori informazioni su circuito peering, vedere [ExpressRoute circuiti e domini di routing](expressroute-circuit-peerings.md).

