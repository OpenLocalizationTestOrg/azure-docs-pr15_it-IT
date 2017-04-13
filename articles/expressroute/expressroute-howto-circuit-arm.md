<properties
   pageTitle="Creare e modificare un circuito ExpressRoute tramite Gestione risorse e PowerShell | Microsoft Azure"
   description="In questo articolo viene descritto come creare, eseguire il provisioning, verificare, aggiornare, eliminare e il deprovisioning un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
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
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Creare e modificare un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portale di Azure - Gestione risorse](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestione risorse](expressroute-howto-circuit-arm.md)
[PowerShell - classico](expressroute-howto-circuit-classic.md)


In questo articolo viene descritto come creare un circuito Azure ExpressRoute utilizzando cmdlet di Windows PowerShell e il modello di distribuzione di Manager delle risorse di Azure. In questo articolo anche illustra come controllare lo stato del circuito, aggiornarla, o eliminare e il deprovisioning.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Prima di iniziare


- Ottenere la versione più recente di moduli Azure PowerShell (almeno versione 1.0). Per istruzioni dettagliate su come configurare il computer per usare i moduli di PowerShell, seguire le istruzioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- Esaminare i [Prerequisiti](expressroute-prerequisites.md) e [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.

## <a name="create-and-provision-an-expressroute-circuit"></a>Creare ed eseguire il provisioning di un circuito ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. accedere al proprio account Azure e selezionare l'abbonamento

Per iniziare la configurazione, accedere al proprio account Azure. Per ulteriori informazioni su PowerShell, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md). Utilizzare gli esempi seguenti consentono di connettersi:

    Login-AzureRmAccount

Controllare le sottoscrizioni per l'account:

    Get-AzureRmSubscription

Selezionare l'abbonamento a cui si desidera creare un circuito ExpressRoute per:

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. ottenere l'elenco di provider supportati, posizioni e larghezze di banda

Prima di creare un circuito ExpressRoute, è necessario l'elenco dei provider di integrazione applicativa supportati, percorsi e opzioni della larghezza di banda.

Il cmdlet di PowerShell `Get-AzureRmExpressRouteServiceProvider` restituisce queste informazioni, occorre usare nei passaggi successivi:

    Get-AzureRmExpressRouteServiceProvider

Verificare se è elencato il proprio provider di integrazione applicativa. Annotare le informazioni seguenti poiché sarà necessario in seguito quando si crea un circuito:

- Nome

- PeeringLocations

- BandwidthsOffered

A questo punto si è pronti creare un circuito ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. creare un circuito ExpressRoute

Se si dispone già di un gruppo di risorse, è necessario crearne uno prima di creare il circuito ExpressRoute. È possibile farlo eseguendo il comando seguente:


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


Nell'esempio seguente viene illustrato come creare una Mbps 200 circuito ExpressRoute tramite Equinix in silicio picchi. Se si usa un provider diverso e impostazioni diverse, sostituire tali informazioni quando si effettua la richiesta. Di seguito è una richiesta di esempio per una nuova chiave di servizio:

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Assicurarsi di specificare il livello SKU corretto e famiglia SKU:

- Livello SKU determina se uno standard di ExpressRoute o un componente aggiuntivo premium ExpressRoute è abilitato. È possibile specificare *Standard* per ottenere il SKU o *Premium* standard per il componente aggiuntivo premium.

- Famiglia SKU determina il tipo di fatturazione. È possibile specificare *Metereddata* per un piano dati a consumo e *Unlimiteddata* per un piano dati illimitato. Si noti che è possibile modificare il tipo di fatturazione da *Metereddata* in *Unlimiteddata*, ma non è possibile modificare il tipo di *Unlimiteddata* a *Metereddata*.


>[AZURE.IMPORTANT] Il circuito ExpressRoute per la fatturazione dal momento in cui che viene inviata una chiave di servizio. Assicurarsi di eseguire questa operazione quando il provider di servizi di integrazione applicativa è pronta per eseguire il provisioning di circuito.

La risposta contiene la chiave del servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo le operazioni seguenti:


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. elenco tutti i circuiti ExpressRoute

Per ottenere un elenco di tutti i circuiti ExpressRoute è stato creato, eseguire la `Get-AzureRmExpressRouteCircuit` comando:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

La risposta sarà simile alla seguente:


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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

È possibile recuperare questa informazione in qualsiasi momento usando la `Get-AzureRmExpressRouteCircuit` cmdlet. Effettuare la chiamata senza parametri sono elencati tutti i circuiti. La chiave del servizio sarà elencata nel campo *ServiceKey* :


    Get-AzureRmExpressRouteCircuit


La risposta sarà simile alla seguente:


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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo le operazioni seguenti:


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. inviare la chiave del servizio per il proprio provider di integrazione applicativa per il provisioning

*ServiceProviderProvisioningState* fornisce informazioni sullo stato corrente di provisioning sul lato provider di servizi. Stato fornisce lo stato sul lato Microsoft. Per ulteriori informazioni su circuito il provisioning di stati, vedere l'articolo di [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito sarà nello stato seguente:


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Il circuito verrà modificato in stato seguente quando il provider di servizi di integrazione applicativa è il processo di attivazione dell'utente:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Per poter essere in grado di utilizzare un circuito ExpressRoute, deve essere nello stato seguente:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periodicamente verificare lo stato e lo stato del tasto circuito

Controllare lo stato e lo stato del tasto circuito consente di sapere quando il provider ha abilitato il circuito. Dopo il circuito è stato configurato, *ServiceProviderProvisioningState* viene visualizzata come *Provisioned*, come illustrato nell'esempio seguente:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La risposta sarà simile alla seguente:


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

### <a name="7-create-your-routing-configuration"></a>7. creare la configurazione di routing

Per istruzioni dettagliate, vedere l'articolo [configurazione routing circuito ExpressRoute](expressroute-howto-routing-arm.md) per creare e modificare circuito peerings.


>[AZURE.IMPORTANT] Queste istruzioni vengono applicate solo alle circuiti creato con provider di servizi che offrono servizi di integrazione applicativa 2 layer. Se si usa un provider di servizi che offre gestito livello 3 servizi (in genere un IP VPN, ad esempio MPLS), il provider di integrazione applicativa verrà configurare e gestire il routing dell'utente.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. collegamento virtuali a un circuito ExpressRoute

Successivamente, collegare una rete virtuale il circuito ExpressRoute. Usare l'articolo [Linking reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si lavora con il modello di distribuzione di Manager delle risorse.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute

È possibile recuperare questa informazione in qualsiasi momento usando la `Get-AzureRmExpressRouteCircuit` cmdlet. Effettuare la chiamata senza parametri sono elencati tutti i circuiti.

    Get-AzureRmExpressRouteCircuit


La risposta sarà simile alla seguente:


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


È possibile ottenere informazioni su un circuito ExpressRoute specifico passando il nome del gruppo di risorse e circuito come parametro alla chiamata:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La risposta sarà simile alla seguente:


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


È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo le operazioni seguenti:

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modifica di un circuito ExpressRoute

È possibile modificare alcune proprietà di un circuito ExpressRoute senza influenzare la connettività.

È possibile eseguire le operazioni seguenti con senza tempi di inattività:

- Abilitare o disabilitare un componente aggiuntivo premium ExpressRoute per il circuito ExpressRoute.
- Aumentare la larghezza di banda del circuito ExpressRoute. Si noti che il downgrade la larghezza di banda di un circuito non è supportato.
- Modificare il piano di misurazione dai dati a consumo ai dati illimitato. Si noti che modifica il piano di misurazione dai dati illimitato a consumo dati non è supportata.
-  È possibile attivare e disattivare *Consenti operazioni classica*.

Per ulteriori informazioni su limiti e limitazioni, vedere la sezione [Domande frequenti su ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Per abilitare il componente aggiuntivo premium ExpressRoute

È possibile attivare il componente aggiuntivo premium ExpressRoute per il circuito esistente usando frammento di PowerShell riportato di seguito:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Il circuito avranno le caratteristiche di componente aggiuntivo premium ExpressRoute abilitate. Si noti che si inizierà fatturazione è in grado di componente aggiuntivo premium non appena il comando è eseguito correttamente.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Per disabilitare il componente aggiuntivo premium ExpressRoute

>[AZURE.IMPORTANT] Questa operazione potrebbe non riuscire se si sta utilizzando risorse sono superiori a quelli che cos'è consentito per circuito standard.

Tenere presente quanto segue:

- Prima eseguire il downgrade da premium a standard, è necessario assicurarsi che il numero di reti virtuali collegato al circuito è inferiore a 10. In caso contrario, la richiesta di aggiornamento ha esito negativo e si verrà a pagamento a tassi premium.

- È necessario scollegare tutte le reti virtuali in altri paesi geopolitici. In caso contrario, la richiesta di aggiornamento avrà esito negativo e si verrà a pagamento a tassi premium.

- Tabella di routing deve essere inferiore a 4.000 route per peering privato. Se le dimensioni della tabella route sono maggiore di 4.000 indirizza, la sessione BGP inserisce e non è possibile attivare fino a quando il numero di prefissi annunciati passa sotto 4.000.

È possibile disabilitare il componente aggiuntivo premium ExpressRoute per circuito esistente utilizzando il seguente cmdlet di PowerShell:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Per aggiornare la larghezza di banda circuito ExpressRoute

Per le opzioni della larghezza di banda supportati relative al provider, selezionare le [Domande frequenti su ExpressRoute](expressroute-faqs.md). È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente.

>[AZURE.IMPORTANT] Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda è necessario il deprovisioning circuito ExpressRoute e quindi reprovision un nuovo circuito ExpressRoute.

Dopo aver deciso quali dimensioni è necessario, usare il comando seguente per ridimensionare il circuito:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Il circuito verrà ridimensionata sul lato Microsoft. Quindi è necessario contattare il provider di integrazione applicativa per aggiornare le configurazioni sul lato in modo che corrispondano questa modifica. Dopo aver apportato questa notifica, si inizierà si fatturazione per l'opzione di aggiornamento della larghezza di banda.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Per spostare lo SKU da a consumo su illimitato

È possibile modificare lo SKU di un circuito ExpressRoute utilizzando frammento di PowerShell riportato di seguito:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Per controllare l'accesso per la visualizzazione classica e ambienti Manager delle risorse  

Esaminare le istruzioni disponibili in [circuiti spostare ExpressRoute da classico per il modello di distribuzione di Manager delle risorse](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annullamento dell'implementazione e l'eliminazione di un circuito ExpressRoute

Tenere presente quanto segue:

- È necessario scollegare tutte le reti virtuali da circuito ExpressRoute. Se questa operazione non riesce, controllare se le reti virtuali sono collegate al circuito.

- Se lo stato provisioning con provider del servizio ExpressRoute circuito **Provisioning** o **Provisioned** è necessario collaborare con il provider di servizi per il deprovisioning circuito sul lato "i". Si continuerà a prenotare le risorse con addebito fino a quando il provider di servizi completa deprovisioning circuito e notifica Contattaci.

- Se il provider di servizi ha rimosso con deprovisioning circuito (lo stato di provisioning provider del servizio è impostato su **non viene completato il provisioning**) è possibile eliminare il circuito. Non verranno più fatturazione per il circuito

È possibile eliminare il circuito ExpressRoute eseguendo il comando seguente:

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, assicurarsi di eseguire le operazioni seguenti:

- [Creare e modificare il routing per il circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Collegare il circuito ExpressRoute rete virtuale](expressroute-howto-linkvnet-arm.md)
