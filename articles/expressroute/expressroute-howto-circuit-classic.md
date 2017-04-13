<properties
   pageTitle="Creare e modificare un circuito ExpressRoute utilizzando il modello di distribuzione classica e PowerShell | Microsoft Azure"
   description="In questo articolo sono illustrati i passaggi per la creazione e il provisioning di un circuito ExpressRoute. In questo articolo viene anche come controllare di stato, aggiornamento o eliminazione e il deprovisioning il circuito."
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
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Creare e modificare un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portale di Azure - Gestione risorse](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestione risorse](expressroute-howto-circuit-arm.md)
[PowerShell - classico](expressroute-howto-circuit-classic.md)

In questo articolo sono illustrati i passaggi per creare un circuito Azure ExpressRoute utilizzando i cmdlet di PowerShell e il modello di distribuzione classica. In questo articolo anche illustra come controllare di stato, aggiornamento o eliminazione e il deprovisioning un circuito ExpressRoute.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Prima di iniziare

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. esaminare i prerequisiti e articoli del flusso di lavoro

Assicurarsi che siano state esaminate le [flussi di lavoro](expressroute-workflows.md) e i [Prerequisiti](expressroute-prerequisites.md) prima di iniziare la configurazione.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. installare le versioni più recenti dei moduli PowerShell Azure 

Seguire le istruzioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per istruzioni dettagliate su come configurare il computer per usare i moduli di Azure PowerShell.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. accedere al proprio account Azure e selezionare una sottoscrizione

1. Eseguire il seguente cmdlet usando un prompt di Windows PowerShell con privilegi elevato:

        Add-AzureAccount
2. Nella schermata di accesso che viene visualizzata, accedere al proprio account.

3. È possibile ottenere un elenco delle sottoscrizioni.

        Get-AzureSubscription
4. Selezionare l'abbonamento a cui si desidera utilizzare.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Creare ed eseguire il provisioning di un circuito ExpressRoute

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. importare i moduli di PowerShell per ExpressRoute

 Se non è già stato fatto, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell per iniziare a utilizzare i cmdlet ExpressRoute. Importare i moduli nella posizione in cui sono stati installati sul computer locale. A seconda del metodo usato per installare i moduli, il percorso potrebbe essere diverso da nell'esempio seguente. Se necessario, modificare l'esempio.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. ottenere l'elenco di provider supportati, posizioni e larghezze di banda

Prima di creare un circuito ExpressRoute, è necessario l'elenco dei provider di integrazione applicativa supportati, percorsi e opzioni della larghezza di banda.

Il cmdlet di PowerShell `Get-AzureDedicatedCircuitServiceProvider` restituisce queste informazioni, occorre usare nei passaggi successivi:

    Get-AzureDedicatedCircuitServiceProvider

Verificare se è elencato il proprio provider di integrazione applicativa. Annotare le informazioni seguenti poiché sarà necessario in seguito quando si crea un circuito:

- Nome

- PeeringLocations

- BandwidthsOffered

A questo punto si è pronti creare un circuito ExpressRoute.         

### <a name="3-create-an-expressroute-circuit"></a>3. creare un circuito ExpressRoute

Nell'esempio seguente viene illustrato come creare una Mbps 200 circuito ExpressRoute tramite Equinix in silicio picchi. Se si usa un provider diverso e impostazioni diverse, sostituire tali informazioni quando si effettua la richiesta.

>[AZURE.IMPORTANT] Il circuito ExpressRoute per la fatturazione dal momento in cui che viene inviata una chiave di servizio. Assicurarsi di eseguire questa operazione quando il provider di servizi di integrazione applicativa è pronta per eseguire il provisioning di circuito.


Di seguito è una richiesta di esempio per una nuova chiave di servizio:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

In alternativa, se si desidera creare un circuito ExpressRoute con il componente aggiuntivo premium, utilizzare l'esempio seguente. Vedere la sezione [Domande frequenti su ExpressRoute](expressroute-faqs.md) per altri dettagli sul componente aggiuntivo premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La risposta conterrà la chiave del servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo le operazioni seguenti:

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. elenco tutti i circuiti ExpressRoute

È possibile eseguire il `Get-AzureDedicatedCircuit` comando per visualizzare un elenco di tutti i circuiti ExpressRoute creato:


    Get-AzureDedicatedCircuit

La risposta sarà simile alla seguente:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

È possibile recuperare questa informazione in qualsiasi momento usando la `Get-AzureDedicatedCircuit` cmdlet. Effettuare la chiamata senza parametri sono elencati tutti i circuiti. La chiave del servizio sarà elencata nel campo *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo le operazioni seguenti:

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. inviare la chiave del servizio per il proprio provider di integrazione applicativa per il provisioning


*ServiceProviderProvisioningState* fornisce informazioni sullo stato corrente di provisioning sul lato provider di servizi. *Stato* fornisce lo stato sul lato Microsoft. Per ulteriori informazioni su circuito il provisioning di stati, vedere l'articolo di [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito sarà nello stato seguente:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Il circuito andrà allo stato seguente quando il provider di servizi di integrazione applicativa è il processo di attivazione dell'utente:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuito ExpressRoute deve essere lo stato di essere in grado di usarlo seguente:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periodicamente verificare lo stato e lo stato del tasto circuito

Consente di sapere quando il provider ha abilitato il circuito. Dopo aver configurato il circuito, *ServiceProviderProvisioningState* verrà visualizzato come *Provisioned* come illustrato nell'esempio seguente:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. creare la configurazione di routing

Fare riferimento alla [ExpressRoute circuito configurazione routing (creare e modificare circuito peerings)](expressroute-howto-routing-classic.md) articolo per istruzioni dettagliate.

>[AZURE.IMPORTANT] Queste istruzioni vengono applicate solo alle circuiti creato con provider di servizi che offrono servizi di integrazione applicativa 2 layer. Se si usa un provider di servizi che offre gestito livello 3 servizi (in genere un IP VPN, ad esempio MPLS), il provider di integrazione applicativa verrà configurare e gestire il routing dell'utente.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. collegamento virtuali a un circuito ExpressRoute

Successivamente, collegare una rete virtuale il circuito ExpressRoute. Per istruzioni dettagliate, consultare [il collegamento ExpressRoute circuiti a reti virtuali](expressroute-howto-linkvnet-classic.md) . Se è necessario creare una rete virtuale utilizzando il modello di distribuzione classico per ExpressRoute, vedere [creare una rete virtuale per ExpressRoute](expressroute-howto-vnet-portal-classic.md) per le istruzioni.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute

È possibile recuperare questa informazione in qualsiasi momento usando la `Get-AzureCircuit` cmdlet. Effettuare la chiamata senza parametri sono elencati tutti i circuiti.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

È possibile ottenere informazioni su un circuito ExpressRoute specifico passando la chiave del servizio come parametro alla chiamata:

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo le operazioni seguenti:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modifica di un circuito ExpressRoute

È possibile modificare alcune proprietà di un circuito ExpressRoute senza influenzare la connettività.

È possibile eseguire le operazioni seguenti con senza tempi di inattività:

- Abilitare o disabilitare un componente aggiuntivo premium ExpressRoute per il circuito ExpressRoute.
- Aumentare la larghezza di banda del circuito ExpressRoute. Si noti che il downgrade la larghezza di banda di un circuito non è supportato.
- Modificare il piano di misurazione dai dati a consumo ai dati illimitato. Si noti che modifica il piano di misurazione dai dati illimitato a consumo dati non è supportata.
- È possibile attivare e disattivare *Consenti operazioni classica*.

Vedere la sezione [Domande frequenti su ExpressRoute](expressroute-faqs.md) per ulteriori informazioni sui limiti e le limitazioni.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Per abilitare il componente aggiuntivo premium ExpressRoute

È possibile attivare il componente aggiuntivo premium ExpressRoute per il circuito esistente utilizzando il seguente cmdlet di PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Il circuito avranno le caratteristiche di componente aggiuntivo premium ExpressRoute abilitate. Si noti che si avviano fatturazione è in grado di componente aggiuntivo premium non appena il comando è eseguito correttamente.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Per disabilitare il componente aggiuntivo premium ExpressRoute

>[AZURE.IMPORTANT] Questa operazione potrebbe non riuscire se si sta utilizzando risorse sono superiori a quelli che cos'è consentito per circuito standard.

Tenere presente quanto segue:

- È necessario assicurarsi che il numero di reti virtuali collegato al circuito è inferiore a 10 prima eseguire il downgrade da premium a standard. Se non si noterà, la richiesta di aggiornamento avrà esito negativo e sarà effettuata le tariffe.

- È necessario scollegare tutte le reti virtuali in altri paesi geopolitici. Se non si noterà, la richiesta di aggiornamento avrà esito negativo e sarà effettuata le tariffe.

- Tabella di routing deve essere inferiore a 4.000 route per peering privato. Se le dimensioni della tabella route sono maggiore di 4.000 indirizza, la sessione BGP eliminerà e non è possibile attivare fino a quando il numero di prefissi annunciati passa sotto 4.000.

È possibile disabilitare il componente aggiuntivo premium ExpressRoute per il circuito esistente utilizzando il seguente cmdlet di PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Per aggiornare la larghezza di banda circuito ExpressRoute

Controllare le [Domande frequenti su ExpressRoute](expressroute-faqs.md) per le opzioni della larghezza di banda supportati relative al provider. È possibile scegliere qualsiasi dimensione è maggiore delle dimensioni del circuito esistente come consente la porta fisica (in cui viene creato il circuito).

>[AZURE.IMPORTANT] Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda è necessario per il deprovisioning circuito ExpressRoute e quindi reprovision un nuovo circuito ExpressRoute.

Dopo aver deciso quali dimensioni necessarie, è possibile utilizzare il comando seguente per ridimensionare il circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Il circuito verrà hanno stato ridimensionato base sul lato Microsoft. È necessario contattare il provider di integrazione applicativa per aggiornare le configurazioni sul lato in modo che corrispondano questa modifica. Si noti che si avviano si fatturazione per l'opzione di aggiornamento della larghezza di banda da questo punto nel.

Se viene visualizzato l'errore seguente quando aumentare la larghezza di banda circuito, significa che non esiste alcun larghezza di banda sufficiente a sinistra nella porta fisica in cui è stato creato il circuito esistente. È necessario eliminare questo circuito e creare un nuovo circuito le dimensioni che necessarie. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annullamento dell'implementazione e l'eliminazione di un circuito ExpressRoute

Tenere presente quanto segue:

- È necessario scollegare tutte le reti virtuali da circuito ExpressRoute per eseguire questa operazione. Verificare la presenza di eventuali reti virtuali collegato al circuito se questa operazione non riesce.

- Se lo stato provisioning con provider del servizio ExpressRoute circuito **Provisioning** o **Provisioned** è necessario collaborare con il provider di servizi per il deprovisioning circuito sul lato "i". Si continuerà a prenotare le risorse con addebito fino a quando il provider di servizi completa deprovisioning circuito e notifica Contattaci.

- Se il provider di servizi ha rimosso con deprovisioning circuito (lo stato di provisioning provider del servizio è impostato su **non viene completato il provisioning**) è possibile eliminare il circuito. Questa operazione verrà interrotto fatturazione per il circuito.

È possibile eliminare il circuito ExpressRoute eseguendo il comando seguente:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, assicurarsi di eseguire le operazioni seguenti:

- [Creare e modificare il routing per il circuito ExpressRoute](expressroute-howto-routing-classic.md)
- [Collegare il circuito ExpressRoute rete virtuale](expressroute-howto-linkvnet-classic.md)
