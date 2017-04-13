<properties
   pageTitle="Creare un collegamento a un circuito ExpressRoute a una rete virtuale utilizzando il modello di distribuzione classica e PowerShell | Microsoft Azure"
   description="Questo documento offre una panoramica su come collegare reti virtuali (VNets) per circuiti ExpressRoute utilizzando il modello di distribuzione classica e PowerShell."
   services="expressroute"
   documentationCenter="na"
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
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Creare un collegamento a un circuito ExpressRoute una rete virtuale

> [AZURE.SELECTOR]
- [Portale di Azure - Gestione risorse](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Manager delle risorse](expressroute-howto-linkvnet-arm.md)
- [PowerShell - classico](expressroute-howto-linkvnet-classic.md)



Questo articolo viene spiegato come collegare reti virtuali (VNets) circuiti Azure ExpressRoute utilizzando il modello di distribuzione classica e PowerShell. Reti virtuali possono essere nello stesso abbonamento o possono far parte di un'altra sottoscrizione.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

1. È necessario l'ultima versione di moduli Azure PowerShell. È possibile scaricare i moduli di PowerShell più recenti dalla sezione PowerShell della [pagina di download di Azure](https://azure.microsoft.com/downloads/). Seguire le istruzioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per istruzioni dettagliate su come configurare il computer per usare i moduli di Azure PowerShell.
2. È necessario esaminare [i prerequisiti](expressroute-prerequisites.md), i [requisiti routing](expressroute-routing.md)e i [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
3. È necessario avere un circuito ExpressRoute attivo.
    - Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e di avere il proprio provider di connettività di abilitare il circuito.
    - Assicurarsi di avere Azure privato peering configurato per il circuito. Vedere l'articolo [Configura il routing](expressroute-howto-routing-classic.md) per instradare le istruzioni.
    - Assicurarsi che peering privato Azure è configurato e il peering BGP tra la rete e Microsoft è in modo che è possibile abilitare la connettività-to-end.
    - È necessario disporre di una rete virtuale e un gateway di rete virtuale creata e completamente il provisioning. Seguire le istruzioni per [configurare una rete virtuale per ExpressRoute](expressroute-howto-vnet-portal-classic.md).

È possibile collegare un circuito ExpressRoute fino a 10 reti virtuali. Tutte le reti virtuale devono essere presenti nell'area geopolitiche stesso. È possibile collegare un numero maggiore di reti virtuali per il circuito ExpressRoute o reti virtuali collegamento disponibili in altre aree geopolitici se è stato attivato il componente aggiuntivo premium ExpressRoute. Controllare le [domande frequenti](expressroute-faqs.md) per altre informazioni sul componente aggiuntivo premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connettere una rete virtuale nello stesso abbonamento a un circuito

È possibile collegare una rete virtuale per un circuito ExpressRoute utilizzando il seguente cmdlet. Assicurarsi che il gateway virtuali viene creato ed è pronto per il collegamento prima di eseguire il cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connettere una rete virtuale in una sottoscrizione diversa a un circuito

È possibile condividere un circuito ExpressRoute tra più abbonamenti. Nella figura seguente mostra un semplice schema di funzionamento della condivisione per circuiti ExpressRoute per più abbonamenti.

Ognuna delle aree più piccole all'interno dell'area di grandi dimensioni viene utilizzato per rappresentare le sottoscrizioni che appartengono a diversi reparti all'interno dell'organizzazione. Di ciascun reparto all'interno dell'organizzazione possono usare proprio abbonamento per distribuire i servizi, ma i reparti possono condividere un singolo circuito ExpressRoute per connettersi alla rete locale. Un singolo reparto (in questo esempio: IT) possono essere proprietari circuito ExpressRoute. Gli altri abbonamenti all'interno dell'organizzazione possono usare circuito ExpressRoute.

>[AZURE.NOTE] Le spese di connettività e della larghezza di banda per circuito dedicato verranno applicate al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.

![Connettività per la sottoscrizione](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Amministrazione

Il *proprietario di circuito* è amministratore/coadministrator della sottoscrizione in cui viene creato il circuito ExpressRoute. Il proprietario del circuito possibile autorizzare amministratori/coadministrators degli altri abbonamenti, denominati *utenti circuito*, per usare il circuito dedicato che possiedono. Circuito gli utenti autorizzati a usare circuito ExpressRoute dell'organizzazione possono visitare virtuali in abbonamento circuito ExpressRoute dopo che sono autorizzati.

Il proprietario circuito ha la possibilità di modificare e revocare le autorizzazioni in qualsiasi momento. Revoca dell'autorizzazione verrà generato in tutti i collegamenti da eliminare dall'abbonamento a cui l'accesso è stato revocato.

### <a name="circuit-owner-operations"></a>Operazioni di proprietario circuito

#### <a name="creating-an-authorization"></a>Creazione di un'autorizzazione

Il proprietario del circuito consente gli amministratori di altri abbonamenti per utilizzare il circuito specificato. Nell'esempio seguente, l'amministratore del circuito (IT di Contoso) consente all'amministratore di abbonamento a un altro (Dev Test) a cui collegarsi fino a due reti virtuali il circuito. L'amministratore IT di Contoso abilita questa operazione specificando l'ID Microsoft Dev Test. Il cmdlet non invia posta elettronica per l'ID Microsoft specificato. Il proprietario del circuito deve comunicare in modo esplicito il proprietario della sottoscrizione altri che l'autorizzazione è stata completata.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

#### <a name="reviewing-authorizations"></a>Revisione delle autorizzazioni

Il proprietario del circuito possibile esaminare tutte le autorizzazioni che vengono generate su un particolare circuito eseguendo il seguente cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


#### <a name="updating-authorizations"></a>Aggiornamento delle autorizzazioni

Il proprietario circuito può modificare le autorizzazioni utilizzando il seguente cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


#### <a name="deleting-authorizations"></a>Eliminazione delle autorizzazioni

Il proprietario del circuito possibile revoke/eliminare le autorizzazioni per l'utente eseguendo il seguente cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operazioni utente circuito

#### <a name="reviewing-authorizations"></a>Revisione delle autorizzazioni

L'utente circuito possibile esaminare le autorizzazioni utilizzando il seguente cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

#### <a name="redeeming-link-authorizations"></a>Utilizzare le autorizzazioni di collegamento

L'utente circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
