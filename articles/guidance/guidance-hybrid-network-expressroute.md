<properties
   pageTitle="Implementare un'architettura di rete ibrido con Azure ExpressRoute | Fare riferimento a architettura | Microsoft Azure"
   description="Come implementare un'architettura di rete protetta da sito che si estende su una rete virtuale Azure e una rete locale connesso tramite Azure ExpressRoute."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Implementare un'architettura di rete ibrido con Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per la connessione di una rete locale a reti virtuali in Azure tramite ExpressRoute. ExpressRoute delle connessioni mediante una connessione dedicata privata tramite un provider di connettività di terze parti. La connessione privata estende la rete locale in Azure garantendo l'accesso all'infrastruttura di IaaS in Azure, pubblico endpoint usati in PaaS e SaaS di Office 365 services. In questo documento è incentrata su utilizza ExpressRoute per connettersi a un singolo Azure virtuali (VNet) utilizzando cosiddetta peering privato.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. Questo progetto utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Casi di utilizzo tipico per questa architettura includono:

- Applicazioni ibride nel punto in cui vengono distribuite carichi di lavoro tra una rete locale e Azure.

- Applicazioni in esecuzione su larga scala, critici carichi di lavoro che richiedono un numero elevato di scalabilità.

- Su larga scala strutture di backup e ripristino per i dati che devono essere salvati fuori sede.

- Carichi di lavoro di gestione dati.

- Uso di Azure come un sito di ripristino di emergenza.

> [AZURE.NOTE] [Panoramica tecnica ExpressRoute] [ expressroute-technical-overview] viene fornita un'introduzione a ExpressRoute.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura:

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Questo è nella pagina "Ibrido di rete - cartelle".

![[0]][0]

- **Reti virtuali Azure (VNets).** Ogni VNet si trova in una singola regione Azure e può contenere più livelli dell'applicazione. Livelli dell'applicazione possono essere segmentati mediante subnet in ogni VNet e/o i gruppi di sicurezza (NSGs) di rete. 

- **Servizi pubblici Azure.** Si tratta di Azure servizi che possono essere utilizzati all'interno di un'applicazione ibrida. Questi servizi sono disponibili anche su Internet, ma accedervi tramite un circuito ExpressRoute offre prestazioni più prevedibili e bassa latenza poiché il traffico non va tramite Internet. Le connessioni vengono eseguite tramite **peering pubblico**, con indirizzi di proprietà dall'organizzazione o forniti dal provider di integrazione applicativa. 

- **Servizi di Office 365.** Queste sono le applicazioni di Office 365 disponibile al pubblico e i servizi forniti da Microsoft. Le connessioni vengono eseguite tramite **Microsoft peering**, con indirizzi di proprietà dall'organizzazione o forniti dal provider di integrazione applicativa.

>[AZURE.NOTE] È anche possibile connettersi direttamente a Microsoft CRM Online tramite un Microsoft peering.

- **Rete aziendale in locale.** Si tratta di una rete di computer e dispositivi, la connessione tramite una rete LAN privata in esecuzione all'interno dell'organizzazione.

- **Router bordo locale.** Questi sono router in grado di connettersi circuito gestito dal provider di rete locale. A seconda di come viene eseguito il provisioning la connessione, è necessario fornire indirizzi IP utilizzati dai router. 

- **Router bordo Microsoft.** Si tratta di due router in una configurazione di disponibilità attivo attivo. Questi router abilitare un provider di integrazione applicativa per connettersi loro circuiti direttamente al relativo Data Center. A seconda di come viene eseguito il provisioning la connessione, è necessario fornire indirizzi IP utilizzati dai router.

- **ExpressRoute circuito.** Si tratta di un livello 2 o circuito livello 3 forniti dal provider di connettività di rete join il locali con Azure attraverso router bordo. Il circuito utilizza l'infrastruttura hardware gestito dal provider di integrazione applicativa.

- **Provider di integrazione applicativa.** Ecco le società che forniscono una connessione a mediante la connettività di livello 3 tra la data center e un Data Center Azure o livello 2.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="connectivity-providers"></a>Provider di integrazione applicativa

Selezionare un provider di integrazione applicativa ExpressRoute appropriato per la posizione. Per ottenere un elenco di provider di integrazione applicativa disponibile nella propria posizione, utilizzare il comando PowerShell Azure seguente:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

ExpressRoute connettività connettere provider del data center Microsoft nei modi seguenti:

- **Presenti in un exchange cloud.** Se sta situati in una struttura con un exchange cloud, è possibile ordinare le connessioni tra virtuale nel cloud Microsoft tramite exchange Ethernet del provider di posizione condivisa. Provider di posizione condivisa può offrire cross di livello 2 per le connessioni o Layer 3 gestito tra connessioni tra l'infrastruttura la funzione di co-ubicazione e nel cloud Microsoft..

- **Connessioni Ethernet punto.** È possibile connettersi i Data Center/uffici locali al cloud Microsoft tramite collegamenti Ethernet punto. Provider Ethernet punto può offrire connessioni di livello 2 o gestito Layer 3 connessioni tra il sito e il cloud Microsoft.

- **Qualsiasi per le reti (IPVPN).** È possibile integrare il WAN con il cloud Microsoft. Provider IPVPN (in genere MPLS VPN) offrono qualsiasi a qualsiasi connessione tra nelle sedi e Data Center. Microsoft cloud può essere interconnessi per la rete WAN per conferirgli esattamente come qualsiasi altro filiale. Provider di servizi WAN offrono in genere gestita connettività di livello 3.

Per ulteriori informazioni sui provider di integrazione applicativa, vedere [ExpressRoute circuiti e domini di routing][connectivity-providers].

### <a name="expressroute-circuit"></a>ExpressRoute circuito

Verificare che l'organizzazione ha soddisfatti i [Prerequisiti ExpressRoute] [ expressroute-prereqs] per la connessione a Azure.

Se non è già stato fatto, aggiungere una subnet denominata `GatewaySubnet` per il VNet Azure e creare un gateway virtuali ExpressRoute mediante il servizio Gateway VPN Azure. Per ulteriori informazioni su questo processo, vedere [ExpressRoute flussi di lavoro per il provisioning di circuito e circuito stati][ExpressRoute-provisioning].

Creare un circuito ExpressRoute come segue:

1. Eseguire il comando PowerShell seguente:

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Inviare la `ServiceKey` per il nuovo circuito al provider del servizio.

3. Attendere che il provider di effettuare il provisioning di circuito. È possibile verificare lo stato di provisioning di un circuito utilizzando il comando PowerShell seguente:

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

Il `Provisioning state` campo la `Service Provider` sezione dell'output cambierà da `NotProvisioned` a `Provisioned` quando il circuito è pronto.

>[AZURE.NOTE]Se si usa una connessione di livello 3, il provider di servizi deve configurare e gestire il routing. fornire le informazioni necessarie per consentire al provider di implementare route appropriate.

Se si usa una connessione di livello 2, attenersi alla procedura indicata di seguito:

1. Prenotare due/30 subnet è composto da indirizzi IP pubblici validi per ogni tipo di peering che si desidera implementare. Questi/30 subnet verrà utilizzata per fornire indirizzi IP per router utilizzato per il circuito. Se si implementazione privato, pubblico e peering Microsoft, è necessario /30 6 subnet con indirizzi IP pubblici validi.     

2. Configurare la distribuzione per circuito ExpressRoute. È necessario eseguire il comando seguente per ogni tipo di peering si desidera configurare (privato, pubblico e Microsoft).

    >[AZURE.NOTE]Vedere [creare e modificare il routing per un circuito ExpressRoute] [ configure-expressroute-routing] per informazioni dettagliate. Utilizzare i comandi di PowerShell seguenti per aggiungere una peering per instradare il traffico di rete:

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Prenotare un altro pool di indirizzi IP pubblici validi da utilizzare per NAT per pubblico e Microsoft peering. Si consiglia di disporre di un pool diverso per ogni peering. Specificare il pool al proprio provider di integrazione applicativa, in modo che possono essere configurati annunci BGP per tali intervalli.

[Collegare il VNet(s) privato nel cloud circuito ExpressRoute][link-vnet-to-expressroute]. Usare i comandi di PowerShell seguenti:

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Noti quanto segue:

- ExpressRoute Usa bordo Gateway Protocol (BGP) per lo scambio di informazioni sul routing tra la rete e Azure.

- È possibile connettere più VNets disponibile in diverse aree geografiche per il circuito ExpressRoute, purché si trovano all'interno dell'area geopolitiche stesso VNets tutti e circuito ExpressRoute.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Circuiti ExpressRoute specificare un percorso di larghezza di banda elevata tra le reti. Generalmente, maggiore sarà la larghezza di banda maggiore dei costi. Anche se alcuni provider consente di modificare la larghezza di banda, assicurarsi che selezionare una larghezza di banda iniziale che supera le proprie esigenze e fornisce spazio per la crescita. Nel caso in cui è necessario aumentare la larghezza di banda in futuro, vengono lasciati con due opzioni.

Aumentare la larghezza di banda. Tenere presente che non tutti i provider consentono di eseguire questa operazione in modo dinamico. Ed evitare la necessità di eseguire questo il più possibile. Tuttavia, se necessario, dopo l'archiviazione con il provider di eseguito comandi riportati di seguito.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

È possibile aumentare la larghezza di banda senza perdita di connettività. Il downgrade la larghezza di banda genereranno disagi connettività. È necessario eliminare il circuito e ricrearlo con la nuova configurazione.

Se si usa lo SKU Standard ExpressRoute e necessità di eseguire l'aggiornamento a Premium o modificare le è i prezzi pianificare (a consumo o illimitato), eseguire i comandi seguenti. Il `Sku.Tier` proprietà può essere `Standard` o `Premium`; il `Sku.Name` proprietà può essere `MeteredData` o `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Assicurarsi che la `Sku.Name` proprietà corrispondenze di `Sku.Tier` e `Sku.Family`. Se si cambia la famiglia di prodotti e livello, ma non il nome, la connessione sarà disabilitata.

È possibile aggiornare lo SKU senza interruzioni, ma non è possibile passare dal piano prezzo illimitato per a consumo. Quando il downgrade lo SKU, la larghezza di banda deve rimanere entro il limite predefinito di SKU standard.

> [AZURE.NOTE] ExpressRoute offre due piani prezzi ai clienti, in base a dati di misurazione o illimitati. Vedere [ExpressRoute prezzi] [ expressroute-pricing] per informazioni dettagliate. In base alle tariffe variare a seconda della larghezza di banda circuito. Larghezza di banda disponibile sarà diversi dal provider al provider. Utilizzare la `Get-AzureRmExpressRouteServiceProvider` cmdlet di per vedere provider disponibili nella propria area geografica e larghezze di banda che offrono.

Un singolo circuito ExpressRoute supportino numerosi peerings e VNet collegamenti. Vedere [limiti ExpressRoute] [ expressroute-limits] per ulteriori informazioni.

Per ulteriori spese, sono disponibili componenti aggiuntivi Premium ExpressRoute:

- Fino a 10.000 indirizza per circuito. Senza componenti aggiuntivi Premium ExpressRoute, il limite è 4.000 indirizza per circuito.

- Connettività globale, per consentire un circuito ExpressRoute disponibile in un punto qualsiasi nel mondo di accedere alle risorse in qualsiasi area anziché solo aree nel continente stesso.

- Aumenta il numero di collegamenti VNet per circuito da 10 a un limite superiore, a seconda della larghezza di banda del circuito.

Circuiti ExpressRoute sono progettati per consentire picchi temporanei di rete fino a due volte il larghezza di banda limite acquistati per senza costi aggiuntivi. Questo è possibile utilizzare i collegamenti ridondanti. Tuttavia, non tutti i provider di integrazione applicativa supportano questa caratteristica; Verificare che il proprio provider di integrazione applicativa consente questa caratteristica prima a seconda.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

È possibile configurare disponibilità per la connessione Azure in diversi modi, a seconda del tipo di provider in uso e il numero di ExpressRoute circuiti e si desidera configurare le connessioni di rete virtuale gateway. Punti seguenti riepilogano le opzioni disponibili:

- ExpressRoute non supporta i protocolli ridondanza router, ad esempio HSRP e VRRP per implementare disponibilità. Se, tuttavia, utilizza una coppia di sessioni BGP per peering ridondanti. Per facilitare le connessioni altamente disponibili per la rete, Microsoft Azure disposizioni con due porte ridondanti in due router (parte di Microsoft edge) in una configurazione attivo attivo.

- Se si usa una connessione di livello 2, distribuire router ridondanti nella rete locale in una configurazione attivo attivo. Connettere circuito primario per un router e circuito secondario a altro. Fornirà una connessione disponibilità entrambe le estremità della connessione. Questa operazione è necessaria se si richiede il contratto di servizio ExpressRoute. Vedere [contratto di servizio per Azure ExpressRoute] [ sla-for-expressroute] per informazioni dettagliate.

Nel diagramma seguente una configurazione con router locale ridondanti connessa a circuiti primari e secondari. Ogni circuito gestisce il traffico verso un peering pubblico e un peering privato (ogni peering designato una coppia di /30 indirizzo spazi, come descritto nella sezione precedente).

![[1]][1]

Se si usa una connessione di livello 3, verificare che fornisce BGP ridondanti sessioni che gestiscono disponibilità dell'utente.

Reti virtuali possono essere collegate a più ExpressRoute circuiti e ogni circuiti possono essere fornito dal provider di servizi diversa. Questa strategia fornisce disponibilità aggiuntive e emergenza ripristino.

Configurare una connessione VPN da sito come percorso failover per ExpressRoute. Questa opzione è disponibile solo per peering privato. Per i servizi di Office 365 e Azure, Internet è il percorso solo failover.

Per impostazione predefinita, le sessioni BGP utilizzano un valore di timeout di inattività di 60 secondi. Una volta una sessione di timeout 3 volte, la route è contrassegnata come non disponibile e tutto il traffico viene reindirizzato al router rimanente. Il timeout di 180 secondo potrebbe essere troppo lungo per le applicazioni. In tal caso, è possibile modificare le impostazioni di timeout BGP sul router locale a un valore più piccolo.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

È possibile utilizzare il [Toolkit di connettività di Azure (AzureCT)] [ azurect] per controllare la connettività tra il data center locale e Azure.

## <a name="security-considerations"></a>Considerazioni sulla protezione

È possibile configurare le opzioni di protezione per la connessione Azure in diversi modi, in base al motivi di sicurezza e conformità alle esigenze. I punti seguenti riepilogano le opzioni di sicurezza.

ExpressRoute funziona nel livello 3. Per impedire rischi di livello di applicazione, è possono usando un dispositivo di protezione di rete che limita il traffico alle risorse legittime. Inoltre, è possono avviare ExpressRoute delle connessioni mediante peering pubblico solo locali. In questo modo un servizio di malintenzionati accedere e compromettere la dati locali da Internet pubblica.

Per ottimizzare la sicurezza, aggiungere apparecchiature di sicurezza di rete tra la rete locale e router provider bordo. In modo da impedire la VNet entrate del traffico non autorizzato:

![[2]][2]

Ai fini della conformità o il controllo, potrebbe essere necessario impedire l'accesso diretto componenti in esecuzione nel VNet a Internet e implementare [forzata tunneling][forced-tuneling]. In questo caso, il traffico Internet deve essere reindirizzato attraverso un proxy in esecuzione in locale in cui può essere controllato. Il proxy può essere configurato per bloccare il traffico non autorizzato provenienti e filtrare il traffico in ingresso potenzialmente dannoso.

![[3]][3]

Per impostazione predefinita, macchine virtuali di Azure esporre endpoint utilizzati per fornire l'accesso ai fini della gestione - RDP e Remote Powershell per Windows macchine virtuali e SSH per macchine virtuali basate su Linux quando distribuito tramite il portale di Azure. Per ottimizzare la sicurezza, non abilitare un indirizzo IP pubblico per le macchine virtuali e utilizzare NSGs per essere certi che tali macchine virtuali non sono accessibili. Macchine virtuali solo devono essere disponibili tramite l'indirizzo IP interno. Questi indirizzi possono essere eseguiti accessibili in rete ExpressRoute, consentendo il personale IT attrezzi locale eseguire qualsiasi configurazione necessarie o manutenzione.

Se è necessario esporre i punti finali gestione per macchine virtuali a una rete esterna, utilizzare NSGs e/o elenchi di controllo per limitare la visibilità di queste porte a proprietà consentite di indirizzi IP o reti di accesso.

## <a name="troubleshooting-considerations"></a>Considerazioni sulla risoluzione dei problemi

Se un circuito ExpressRoute precedentemente funziona ora non riesce a connettersi, in assenza di eventuali modifiche di configurazione in locale o all'interno del VNet privato, potrebbe essere necessario contattare il provider di integrazione applicativa e utilizzarli per risolvere il problema. Inoltre, è possibile utilizzare i comandi di PowerShell Azure seguenti per eseguire alcune limitato il controllo e consentono di determinare dove potrebbero essere problemi:

- Verificare che sia stato preparato il circuito:

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

L'output del comando Mostra diverse proprietà per il circuito, tra cui `ProvisioningState`, `CircuitProvisioningState`, e `ServiceProviderProvisioningState` come illustrato di seguito.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Se il `ProvisioningState` non è impostata su `Succeeded` dopo che si è tentato di creare un nuovo circuito, rimuovere il circuito tramite il comando seguente e provare a crearne uno nuovo.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Se il proprio provider di era già stato effettuato il provisioning circuito e la `ProvisioningState` è impostato su `Failed`, o `CircuitProvisioningState` non `Enabled`, contattare il provider per assistenza.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Se si dispone di un'infrastruttura locale già configurata con un dispositivo di rete adatto, è possibile distribuire l'architettura di riferimento procedendo come segue:

Se si dispone di un'infrastruttura locale già configurata con un dispositivo VPN, è possibile distribuire l'architettura di riferimento procedendo come segue:

1. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Attendere che il collegamento per aprire il portale di Azure, attenersi alla seguente procedura: 
  - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-hybrid-er-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

4. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Attendere che il collegamento per aprire il portale di Azure, attenersi alla seguente procedura: 
  - Selezionare **Usa esistente** nella sezione **gruppo di risorse** e immettere `ra-hybrid-er-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

6. Attendere che la distribuzione completare.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [implementazione di un'architettura di rete ibrido disponibilità] [ highly-available-network-architecture] per informazioni su come aumentare la disponibilità di una rete ibrido basato su ExpressRoute per il ripristino in una connessione VPN.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Architettura di rete ibrido con Azure ExpressRoute"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "Utilizzo di router ridondanti con circuiti primari e secondari ExpressRoute"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Aggiunta di dispositivi di sicurezza per la rete locale"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "Uso forzata tunneling per controllare il traffico Internet associazione"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Individuazione ServiceKey di un circuito ExpressRoute"
