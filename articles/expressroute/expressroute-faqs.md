<properties
   pageTitle="ExpressRoute domande frequenti"
   description="Domande frequenti su ExpressRoute contiene informazioni sui servizi di Windows Azure supportati, costo, dati e connessioni, contratto di servizio, provider e posizioni, della larghezza di banda e altri dettagli tecnici."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-faq"></a>ExpressRoute domande frequenti


## <a name="what-is-expressroute"></a>Che cos'è ExpressRoute?
ExpressRoute è un servizio di Azure che consente di creare connessioni private tra Data Center di Microsoft e infrastruttura che non è in locale o in una struttura di installazione. Connessioni ExpressRoute passare su Internet e non offre maggiore sicurezza, l'affidabilità e velocità con latenza inferiore rispetto alle connessioni tipiche tramite Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quali sono i vantaggi dell'utilizzo ExpressRoute e connessioni di rete privata?
Connessioni ExpressRoute passare su Internet e non offre maggiore sicurezza, l'affidabilità e velocità con latenza inferiore e coerente rispetto alle connessioni tipiche tramite Internet. In alcuni casi, con le connessioni ExpressRoute per trasferire i dati tra locale dispositivi e Azure è possibile ottenere vantaggi significativi.

### <a name="what-microsoft-cloud-services-are-supported-over-expressroute"></a>A quali servizi cloud Microsoft sono supportati su ExpressRoute?
ExpressRoute supporta la maggior parte dei servizi di Microsoft Azure oggi ivi compresi a Office 365.  Cercare gli aggiornamenti sulla disponibilità generale breve.

### <a name="where-is-the-service-available"></a>Dove si trova il servizio?
Leggere questa pagina per la posizione del servizio e la disponibilità: [posizioni ed ExpressRoute partner](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Come è possibile usare ExpressRoute per connettersi a Microsoft, se non si dispone di partnership con uno dei partner ExpressRoute vettori?
È possibile selezionare un vettore internazionali e pagina iniziale di connessioni Ethernet a uno di exchange supportata posizioni provider. È quindi possibile peer con Microsoft in corrispondenza della posizione del provider. Selezionare l'ultima sezione di [posizioni e partner ExpressRoute](expressroute-locations.md) per controllare se il provider di servizi è presenta in uno dei percorsi di exchange. È quindi possibile ordinare un circuito ExpressRoute tramite il provider di servizi a cui connettersi Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto costa ExpressRoute costo?
Selezionare [ulteriori informazioni sui prezzi](https://azure.microsoft.com/pricing/details/expressroute/) per informazioni sui prezzi.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se paga per un circuito ExpressRoute di una determinata larghezza di banda, la connessione VPN che si acquista con il provider di servizi deve essere la stessa velocità?
No. È possibile acquistare una connessione VPN di qualsiasi velocità dal provider di servizi. Tuttavia, la connessione a Azure saranno limitata per la larghezza di banda circuito ExpressRoute acquistati.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required"></a>Se paga per un circuito ExpressRoute di una determinata larghezza di banda, è la possibilità di burst fino a velocità più elevate, se necessario?
Sì. Circuiti ExpressRoute sono configurati per supportare casi in cui è possibile burst fino a due volte il limite di larghezza di banda acquistati per senza costi aggiuntivi. Contattare il provider di servizi che supportano questa funzionalità.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>È possibile utilizzare la stessa connessione di rete privata con virtuali e altri servizi Azure contemporaneamente?
Sì. Un circuito ExpressRoute una volta configurazione consentono di accedere ai servizi all'interno di una rete virtuale e altri servizi di Azure contemporaneamente. Si è connessi a reti virtuali del percorso di peering privato e altri servizi tramite il percorso di peering pubblico.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute offre un livello di contratto di servizio?
Fare riferimento alla [pagina Contratto di servizio ExpressRoute](https://azure.microsoft.com/support/legal/sla/) per ulteriori informazioni.

## <a name="supported-services"></a>Servizi supportati
Servizi più Azure sono supportati su ExpressRoute.

- Connettività a macchine virtuali e i servizi cloud distribuiti in reti virtuali sono supportate sul percorso peering privato.
- Siti Web Azure sono supportate sul percorso peering pubblico.
- IoT Hub è supportato sul percorso peering pubblico.
- Office 365 è supportata sul percorso peering Microsoft.
- Tutti gli altri servizi sono accessibili tramite il percorso di peering pubblico. Le eccezioni sono i seguenti:

    **Non sono supportati i servizi seguenti:**

    - RETE CDN
    - Servizi di Visual Studio Team test di carico
    - Autenticazione a più fattori
    - Gestore del traffico

## <a name="data-and-connections"></a>Dati e connessioni

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Esistono limiti alla quantità di dati che è possibile trasferire utilizzando ExpressRoute?
Non è impostato un limite sulla quantità di trasferimento di dati. Fare riferimento a [ulteriori informazioni sui prezzi](https://azure.microsoft.com/pricing/details/expressroute/) per informazioni sulle tariffe della larghezza di banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quali velocità di connessione sono supportate da ExpressRoute?
Supportati offerte di larghezza di banda:

| 50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 GB/s, 5 GB/s, 10Gbps |

### <a name="which-service-providers-are-available"></a>Il provider di servizi sono disponibili?
Vedere [posizioni e partner ExpressRoute](expressroute-locations.md) per l'elenco di provider di servizi e posizioni.

## <a name="technical-details"></a>Dettagli tecnici

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quali sono i requisiti tecnici per la connessione posizione locale a Azure?
Vedere [pagina Prerequisiti ExpressRoute](expressroute-prerequisites.md) per requisiti.

### <a name="are-connections-to-expressroute-redundant"></a>Le connessioni a ExpressRoute sono ridondanti?
Sì. Ogni circuito strada ha una coppia di ridondanti cross connessioni configurata per la disponibilità.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Verranno perse connettività se uno dei collegamenti ExpressRoute non riesce?
Se una delle connessioni croce ha esito negativo, non andranno perse connettività. Una connessione ridondante è disponibile per supportare il carico di rete. È inoltre possibile creare più circuiti in un percorso diverso peering per ottenere la resilienza errore.

### <a name="onep2plink"></a>Se non si è condivisa si trova in un exchange cloud e il provider di servizi offre connessione punto a punto, è necessario ordinare due connessioni fisiche tra la rete locale e Microsoft? 
No, è sufficiente una connessione fisica se il provider di servizi è possibile stabilire due circuiti virtuali Ethernet per la connessione fisica. La connessione fisica (ad esempio un ottiche) viene interrotta su un livello 1 (L1) dispositivo (vedere l'immagine riportata di seguito). Le due circuiti virtuali Ethernet vengono contrassegnati con ID VLAN diversi, uno per circuito primario e uno per secondario. Tali ID VLAN sono 802.1 q basata su esterno intestazione Ethernet. 802.1 q basata su interna intestazione Ethernet (non illustrata) sia associati a uno specifico [dominio di routing ExpressRoute](expressroute-circuit-peerings.md). 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)


### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>È possibile estendere una personale VLAN in Azure utilizzando ExpressRoute?
No. Estensioni di livello 2 connettività non è supportata in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Si può aggiungere più ExpressRoute circuito nel proprio abbonamento?
Sì. È possibile impostare più ExpressRoute circuito nell'abbonamento. Il limite predefinito per il numero di circuiti dedicati è impostato su 10. È possibile contattare il supporto Microsoft per aumentare il limite, se necessario.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>È possibile avere circuiti ExpressRoute dal provider di servizi diversa?
Sì. È possibile impostare ExpressRoute circuiti con molti provider di servizi. Ogni circuito ExpressRoute sarà associato a un provider di servizi solo.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Come collegare le reti virtuali per un circuito ExpressRoute
Di seguito sono descritti i passaggi di base.

- È necessario stabilire un circuito ExpressRoute e disporre il provider di servizi abilitarla.
- Si o il provider di servizi necessario configurare il BGP peering (s).
- È necessario collegare la rete virtuale a circuito ExpressRoute.

Per ulteriori informazioni, vedere [ExpressRoute flussi di lavoro per il provisioning di circuito e stati circuito](expressroute-workflows.md) .

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Esistono limiti di connettività per la circuito ExpressRoute?
Sì. Pagina [percorsi e partner ExpressRoute](expressroute-locations.md) viene fornita una panoramica dei limiti di connettività per un circuito ExpressRoute. Connettività per un circuito ExpressRoute è limitata a una singola regione geopolitiche. È possibile espandere la connettività per incrociato aree geopolitiche abilitando la funzione premium ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>È possibile collegare più di una rete virtuale per un circuito ExpressRoute?
Sì. È possibile collegare un circuito ExpressRoute fino a 10 reti virtuali.

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Hanno più abbonamenti Azure che contengono le reti virtuali. È possibile collegare le reti virtuali presenti in sottoscrizioni separate per un singolo circuito ExpressRoute?
Sì. È possibile autorizzare fino a 10 altri abbonamenti Azure usare un unico circuito ExpressRoute. È possibile aumentare questo limite per attivare la funzionalità di premium ExpressRoute.

Per ulteriori informazioni, vedere [condivisione di un circuito ExpressRoute tra più abbonamenti](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Reti virtuali connessi allo stesso circuito isolato da altro?
No. Tutte le reti virtuali collegate allo stesso circuito ExpressRoute fanno parte dello stesso dominio di routing e non sono isolate tra loro dal punto di vista routing. Se è necessario route isolamento, sarà necessario creare un circuito ExpressRoute diverso.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>È possibile usare una rete virtuale connessa a più ExpressRoute circuito?
Sì. È possibile collegare una singola rete virtuale con circuiti ExpressRoute fino a 4. Si devono essere ordinati tramite 4 diverse [posizioni ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>È possibile accedere a internet dalle reti virtuali collegati a circuiti ExpressRoute?
Sì. Se non si dispone di annunciato route predefinite (0.0.0.0/0) o prefissi route internet attraverso la sessione BGP, sarà possibile connettersi a internet da una rete virtuale collegata a un circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>È possibile bloccare la connettività internet a reti virtuali connessi a circuiti ExpressRoute?
Sì. È possibile annunciare route predefinita (0.0.0.0/0) per bloccare tutte le connessioni internet a macchine virtuali distribuite all'interno di una rete virtuale e instradare tutto il traffico fuori attraverso il circuito ExpressRoute. Nota Se si annunciano route predefinite, si verrà forzare il traffico di ai servizi offerti su pubblico indietro peering (ad esempio lo spazio di archiviazione Azure e DB SQL) ai propri locali. È necessario configurare i router per restituire il traffico in Azure aiutano peering pubblico o su internet.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Reti virtuali collegate allo stesso circuito ExpressRoute possono comunicare con loro?
Sì. Macchine virtuali distribuiti in reti virtuali collegati allo stesso circuito ExpressRoute possibile comunicare con loro.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>È possibile utilizzare la connettività tra siti per le reti virtuali in combinazione con ExpressRoute?
Sì. ExpressRoute può coesistere con VPN da sito.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>È possibile spostare una rete virtuale dalla configurazione da sito / punto al sito da utilizzare ExpressRoute?
Sì. È necessario creare un gateway ExpressRoute all'interno della rete virtuale. Si verificherà un tempo di inattività small associato al processo.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Cosa necessario connettersi a archiviazione Azure ExpressRoute?
È necessario stabilire un circuito ExpressRoute e configurare le route per peering pubblico.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Esistono limiti al numero di route che è possibile annunciare?
Sì. Abbiamo accetta fino a 4000 prefissi route per peering privato e 200 per peering pubblico e Microsoft peering. È possibile aumentare a 10.000 route per peering privato se si abilita la caratteristica di premium ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Sono previste restrizioni in intervalli di indirizzi IP che è possibile annunciare nella sessione BGP?
La sessione BGP peering pubblico e Microsoft non vengono accettate prefissi privati (RFC1918).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Cosa succede se si supera il BGP limita?
Sessioni BGP verranno rimosse. Verranno reimpostati dopo il conteggio di prefisso è pari di sotto il limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Che cos'è il tempo di attesa ExpressRoute BGP? Apportare?
Il tempo di attesa è 180. Mantieni attivo messaggi vengono inviati ogni 60 secondi. Questi sono corretti impostazioni sul lato Microsoft che non possa essere modificato.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Dopo che annuncia la route predefinita (0.0.0.0/0) per le reti virtuali, Impossibile attivare Windows in esecuzione in macchine virtuali personale Azure. Come per risolvere il problema
La procedura seguente può essere utile Azure riconoscere la richiesta di attivazione:

1. Stabilire peering pubblico per il circuito ExpressRoute.
2. Eseguire una ricerca DNS e trovare l'indirizzo IP del **kms.core.windows.net**
3. Quindi eseguire una delle seguenti due elementi in modo che il servizio di gestione delle chiavi verrà riconosciuto che la richiesta di attivazione provenienti da Azure e rispetta la richiesta.
    - Nella rete locale, indirizzare il traffico per l'indirizzo IP (ottenuto nel passaggio 2) su Azure tramite peering pubblico.
    - Disporre il pin di pelo NSP provider il traffico su Azure tramite peering pubblico.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>È possibile modificare la larghezza di banda di un circuito ExpressRoute?
Sì. È possibile aumentare la larghezza di banda di un circuito ExpressRoute senza dover rimuovere verso il basso. È necessario procedere con il provider di integrazione applicativa per assicurarsi di aggiornare limita all'interno delle reti per supportare l'aumento della larghezza di banda. Tuttavia non sarà possibile per ridurre la larghezza di banda di un circuito ExpressRoute. Dover inferiore che la larghezza di banda comporterà una tagliandi da verso il basso e per la creazione di un circuito ExpressRoute.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Come è possibile modificare la larghezza di banda di un circuito ExpressRoute?
È possibile aggiornare la larghezza di banda del circuito ExpressRoute utilizzando il cmdlet di PowerShell e API circuito aggiornamento dedicato.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Che cos'è ExpressRoute premium?
ExpressRoute premium è un insieme di caratteristiche elencate di seguito.

 - Viene aumentata routing limite di tabella da 4000 indirizza a 10.000 route per peering privato.
 - Maggiore numero di VNets che possono essere collegati al circuito ExpressRoute (valore predefinito è 10). Tabella riportata di seguito per altri dettagli, vedere.
 - Connettività globale sulla rete di base di Microsoft. A questo punto, sarà possibile collegare una VNet in un'area geopolitiche con un circuito ExpressRoute in un'altra area. **Esempio:** È possibile collegare un VNet creato in Europa occidentale a un circuito ExpressRoute creato in silicio picchi.
 - Connettività a servizi di Office 365 e CRM Online.

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a>VNets quanti è possibile creare un collegamento a un circuito ExpressRoute se è abilitata premium ExpressRoute?
Tabelle riportate di seguito mostrano i limiti di ExpressRoute e il numero di VNets al ExpressRoute circuito.


[AZURE.INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]


### <a name="how-do-i-enable-expressroute-premium"></a>Come è possibile abilitare ExpressRoute premium?
Caratteristiche premium ExpressRoute per attivare quando la caratteristica è attivata e può essere arrestata aggiornando lo stato di circuito. È possibile abilitare premium ExpressRoute al momento della creazione circuito o possono chiamare il circuito aggiornamento dedicato API / cmdlet di PowerShell per abilitare ExpressRoute premium.

### <a name="how-do-i-disable-expressroute-premium"></a>Come è possibile disattivare ExpressRoute premium?
È possibile disabilitare la premium ExpressRoute chiamando update dedicato circuito API / cmdlet di PowerShell è necessario assicurarsi che le prestazioni la connettività deve soddisfare i limiti predefiniti prima di disattivare ExpressRoute premium. È richiesta per disattivare ExpressRoute premium se la percentuale di utilizzo scale oltre i limiti predefiniti non riuscirà.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>È scegliere le caratteristiche che desidera ottenere dal set di caratteristiche premium?
No. Non sarà possibile scegliere le funzionalità che necessarie. Quando si attiva ExpressRoute premium è attiva tutte le caratteristiche.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto costa costo premium ExpressRoute?
Fare riferimento a [ulteriori informazioni sui prezzi](https://azure.microsoft.com/pricing/details/expressroute/) di costo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Pagare premio ExpressRoute oltre standard in base alle tariffe ExpressRoute?
Sì. In base alle tariffe premium ExpressRoute applicare nella parte superiore di ExpressRoute circuito spese e spese richieste dal provider di integrazione applicativa.

## <a name="expressroute-and-office-365-services-and-crm-online"></a>ExpressRoute e servizi di Office 365 e CRM Online

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-crm-online"></a>Creazione di un circuito ExpressRoute per connettersi ai servizi di Office 365 e CRM Online

1. Rivedere la pagina di [pagina Prerequisiti ExpressRoute](expressroute-prerequisites.md) per assicurarsi che siano soddisfatti i requisiti.
2. Esaminare l'elenco di provider di servizi e posizioni di [percorsi e partner ExpressRoute](expressroute-locations.md) per assicurarsi che siano soddisfatti i requisiti di connettività.
3. Pianificare i requisiti di capacità consultando [pianificazione della rete e ottimizzazione delle prestazioni per Office 365](http://aka.ms/tune/).
4. Seguire i passaggi elencati nei flussi di lavoro riportata di seguito per impostare la connettività [ExpressRoute flussi di lavoro per il provisioning di circuito e stati circuito](expressroute-workflows.md).

>[AZURE.IMPORTANT] Assicurarsi di aver attivato componente aggiuntivo premium ExpressRoute quando si configura la connettività a servizi di Office 365 e CRM Online.

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-crm-online"></a>È necessario abilitare Azure Peering pubblico per connettersi ai servizi di Office 365 e CRM Online?
No, è sufficiente abilitare Peering Microsoft. Il traffico di autenticazione di Azure Active Directory verrà inviato tramite Microsoft Peering. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-crm-online"></a>I circuiti ExpressRoute esistenti supportino connettività a servizi di Office 365 e CRM Online?
Sì. Il circuito ExpressRoute esistente può essere configurato per supportare la connettività a servizi di Office 365. Assicurarsi di avere capacità sufficiente a connettersi ai servizi di Office 365 e assicurarsi di aver attivato componente aggiuntivo premium. [Pianificazione della rete e ottimizzazione delle prestazioni per Office 365](http://aka.ms/tune/) consente di pianificare le esigenze di integrazione applicativa. Vedere anche [creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Quali servizi sono accessibili tramite una connessione ExpressRoute di Office 365?

Fare riferimento alla pagina di [Office 365 URL e intervalli di indirizzi IP](http://aka.ms/o365endpoints) per un elenco di servizi supportati su ExpressRoute aggiornato.

### <a name="how-much-does-expressroute-for-office-365-services-and-crm-online-cost"></a>Quanto costa ExpressRoute per i servizi di Office 365 e costo CRM Online?
Servizi di Office 365 e CRM Online richiede componente aggiuntivo premium essere abilitate. [Prezzi pagina dei dettagli](https://azure.microsoft.com/pricing/details/expressroute/) fornisce dettagli dei costi per ExpressRoute.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Quali aree è supportata in ExpressRoute per Office 365?
Fare riferimento alle [posizioni e partner ExpressRoute](expressroute-locations.md) per ulteriori informazioni sull'elenco di posizioni in cui è supportato ExpressRoute e partner.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>È accedere a Office 365 tramite internet anche se è stato configurato ExpressRoute per la propria organizzazione?
Sì. I punti finali di Office 365 servizio raggiungibili tramite internet anche se è stata configurata ExpressRoute per la rete. Se trovano in una posizione in cui è configurata per connettersi ai servizi di Office 365 tramite ExpressRoute, si è connessi a ExpressRoute.

### <a name="can-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Dynamics AX Online sono accessibili tramite una connessione ExpressRoute?
No, non è supportata.
