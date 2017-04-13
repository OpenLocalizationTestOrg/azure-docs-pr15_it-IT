<properties 
   pageTitle="Virtuali domande frequenti su Gateway VPN | Microsoft Azure"
   description="Il Gateway VPN domande frequenti. Domande frequenti per le connessioni tra locale virtuali di Microsoft Azure, le connessioni di configurazione ibrida e gateway VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>Gateway VPN domande frequenti

## <a name="connecting-to-virtual-networks"></a>Connessione a reti virtuali

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>È possibile collegare le reti virtuali in diverse aree geografiche Azure?
Sì. Infatti, non esiste alcun vincolo area geografica. Una rete virtuale è possibile connettersi a un altro virtuali nella stessa regione o in un'area di Azure diversa.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>È possibile collegare le reti virtuali in diversi abbonamenti?
Sì.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>È possibile collegare a più siti da un'unica rete virtuale?

È possibile connettersi a più siti usando Windows PowerShell e le API REST di Azure. Vedere la sezione [più siti e alla connettività di VNet a VNet](#multi-site-and-vnet-to-vnet-connectivity) domande frequenti.
## <a name="what-are-my-cross-premises-connection-options"></a>Quali sono le opzioni di connessione tra locale?

Sono supportate le connessioni tra locale seguenti:

- [Da sito](vpn-gateway-site-to-site-create.md) : connessione VPN su IPsec (IKE v1 e v2 IKE). Questo tipo di connessione richiede un dispositivo VPN o configurazione.

- [Al sito di punto](vpn-gateway-point-to-site-create.md) : connessione VPN su SSTP (Secure Socket Tunneling Protocol). La connessione non richiede un dispositivo VPN.

- [VNet-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) -questo tipo di connessione è diverso da quello di configurazione di un sito al sito. VNet a VNet è una connessione VPN su IPsec (IKE v1 e v2 IKE). Non è necessario un dispositivo VPN.

- [Più siti](vpn-gateway-multi-site.md) – si tratta di una variazione di una configurazione da sito che è possibile connettere più siti locale a una rete.

- [ExpressRoute](../expressroute/expressroute-introduction.md) -ExpressRoute è connesso direttamente a Azure dalle WAN, non su Internet. Vedere la [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md) e [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) per ulteriori informazioni.

Per ulteriori informazioni sulle connessioni, vedere [Su Gateway VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Che cos'è la differenza tra una connessione a siti e al sito?

Le connessioni **Al sito** consentono di stabilire la connessione tra i computer situati in locale a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale, a seconda di come si decide di configurare la distribuzione. È un'ottima soluzione per una connessione locale tra sempre disponibile ed è ideale per le configurazioni ibride. Questo tipo di connessione si basa su un accessorio VPN IPsec (hardware o contorni accessorio), che deve essere distribuito sul bordo della rete. Per creare questo tipo di connessione, è necessario disporre i componenti hardware VPN necessari e un indirizzo IPv4 esposto esternamente.

**Punto al sito** connessioni consentono di collegare da un singolo computer da qualsiasi posizione su qualsiasi elemento che si trova nella rete virtuale. Viene utilizzato il client VPN nella casella di Windows. Durante la configurazione del punto di sito, installare un certificato e un pacchetto di configurazione di client VPN, che contiene le impostazioni che consentono al computer di connettersi a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale. È ideale quando si desidera connettersi a una rete, ma non si trova in locale. È inoltre opportuno quando non si dispone di accesso a un indirizzo IPv4 esternamente esposto, che sono necessari per una connessione a siti o hardware VPN. 

È possibile configurare la rete virtuale all'uso di siti e al sito simultaneo, a condizione che si crea la connessione al sito usando un tipo VPN basato su route per il gateway. Tipi VPN basati su route sono denominati gateway dinamico nel modello di distribuzione classica.

### <a name="what-is-expressroute"></a>Che cos'è ExpressRoute?

ExpressRoute consente di creare connessioni private tra Data Center di Microsoft e infrastruttura che non è in locale o in un ambiente di posizione condivisa. Con ExpressRoute, è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure e in Office 365 in una struttura di co-ubicazione partner ExpressRoute o connettersi direttamente dalla rete WAN esistente (ad esempio un VPN MPLS fornito da un provider di servizi di rete).

Connessioni ExpressRoute in maggiore sicurezza, più affidabilità, larghezza di banda superiore e inferiore latenza rispetto alle connessioni tipiche tramite Internet. In alcuni casi, con le connessioni ExpressRoute per trasferire dati tra la rete locale e Azure può offrire anche vantaggi significativi. Se già stata creata una connessione tra locale dalla rete locale in Azure, è possibile eseguire la migrazione a una connessione ExpressRoute mantenendo invariata la rete virtuale.

Vedere le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) per altri dettagli.

## <a name="site-to-site-connections-and-vpn-devices"></a>Le connessioni al sito e i dispositivi VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Che cos'è opportuno considerare quando si seleziona un dispositivo VPN?

È stato convalidati un set di dispositivi VPN da sito standard in collaborazione con fornitori di dispositivo. Un elenco di noti dispositivi VPN compatibili, le istruzioni di configurazione corrispondente o esempi e specifiche di dispositivo sono disponibili [qui](vpn-gateway-about-vpn-devices.md). Tutti i dispositivi le famiglie di dispositivo elencate come compatibile con noti devono usare virtuali. Per informazioni su come configurare il dispositivo VPN, fare riferimento all'esempio Configurazione dispositivo o al collegamento corrispondente alla famiglia di dispositivo appropriato.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Cosa fare se dispone di un dispositivo VPN non incluso nell'elenco dei dispositivi compatibili noti?

Se non è presente il dispositivo elencato come un dispositivo VPN noto e si desidera utilizzare per la connessione VPN, è necessario verificare che soddisfi i supportati IPsec/IKE configurazione opzioni e parametri elencati [di seguito](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Dispositivi che soddisfi i requisiti minimi funziona bene con gateway VPN. Per ulteriori istruzioni di configurazione e supporto tecnico, contattare il produttore del dispositivo.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Perché personale in base a criteri tunnel VPN scendere quando il traffico è inattivo?

Si tratta di un comportamento previsto per basate sui criteri (noto anche come routing statico) gateway VPN. Quando il traffico attraverso il tunnel è inattivo per più di cinque minuti, il tunnel verrà eliminato verso il basso. Quando il traffico inizia che scorre in entrambe le direzioni, tunnel verrà ristabilire immediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>È possibile utilizzare software VPN a cui connettersi Azure?

Server di accesso remoto (RRAS) e Windows Server 2012 Routing è supportato per una configurazione da sito tra locale.

Altre soluzioni VPN software devono lavorare con il gateway come rispondono alle implementazioni IPsec standard di settore. Contattare il produttore del software per le istruzioni di configurazione e supporto tecnico.

## <a name="point-to-site-connections"></a>Connessioni punto al sito

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Sistemi operativi è possibile usare con punto al sito?

Sono supportati i sistemi operativi seguenti:

- Windows 7 (32 bit e 64 bit)

- Windows Server 2008 R2 (solo 64 bit)

- Windows 8 (32 bit e 64 bit)

- Windows 8.1 (32 bit e 64 bit)

- Windows Server 2012 (solo 64 bit)

- Windows Server 2012 R2 (solo 64 bit)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>È possibile utilizzare qualsiasi client VPN software per punto al sito che supporta SSTP?

No. Il supporto è limitato solo per le versioni di sistemi operativi Windows elencate sopra.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quanti endpoint del client VPN possono aggiungere della configurazione punto al sito?

È supportato fino a 128 client VPN per essere in grado di connettersi a una rete virtuale nello stesso momento.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>È possibile utilizzare il proprio CA radice interna infrastruttura a chiave pubblica per la connessione punto a sito?

Sì. In precedenza, è possibile utilizzare solo autofirmato certificati. È comunque possibile caricare 20 certificati.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>È possibile scorrere proxy e firewall utilizzando la funzionalità punto al sito

Sì. Microsoft utilizza SSTP (Secure Socket Tunneling Protocol) per tunnel attraverso firewall. Questo tunnel verrà visualizzato come una connessione HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se riavvia un computer client configurato per punto al sito, la rete VPN verrà riconnettersi automaticamente?

Per impostazione predefinita, il computer client consentirà non di ristabilire la connessione VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Supporto punto al sito riconnessione automatica e DNS dinamico nel client VPN?

Riconnessione automatica e DNS dinamico attualmente non è supportati in VPN punto al sito.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>È possibile aggiungere a siti e coesistono configurazioni punto al sito per la stessa rete virtuale?

Sì. Entrambe queste soluzioni funziona se si dispone di un tipo di RouteBased VPN per il gateway. Per il modello di distribuzione classica, è necessario un gateway dinamico. Facciamo non punto-a-sito di supporto per statico gateway VPN routing o gateway tramite - VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>È possibile configurare un client punto al sito per connettersi a più reti virtuali nello stesso momento?

Sì, è possibile. Ma non è possibile specificare le reti virtuali sovrapposti prefissi di indirizzi IP e gli spazi indirizzo punto al sito non devono sovrapposizione tra le reti virtuali.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quantità effettiva è previsto tramite connessioni a siti o punto al sito

È difficile da gestire la velocità esatta di tunnel VPN. IPsec e SSTP sono protocolli VPN crypto Incolla. Velocità è limitata anche la latenza e la larghezza di banda tra le locale e Internet.

## <a name="gateways"></a>Gateway

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Che cos'è un gateway (routing statica) in base a criteri?

In base a criteri gateway implementare VPN basate sui criteri. In base a criteri VPN crittografare e diretta pacchetti attraverso tunnel IPsec in base alla combinazione dei prefissi degli indirizzi tra la rete locale e VNet Azure. Criteri (o il traffico selettore) in genere è definito come un elenco di accesso nella configurazione VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Che cos'è un gateway basate su route (routing dinamico)?

Gateway basate su route implementare connessioni VPN basate su route. Connessioni VPN basate su route utilizzare "route" IP inoltro o il routing di tabella per i pacchetti diretti nelle interfacce tunnel corrispondenti. Le interfacce tunnel quindi crittografare o decrittografare i pacchetti da e verso il tunnel. Selettore di criteri o il traffico per la distribuzione in base a VPN sono configurati come qualsiasi-to-any (o i caratteri jolly).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>È possibile ottenere l'indirizzo IP di gateway VPN prima creare?

No. È necessario creare il gateway per ottenere l'indirizzo IP. L'indirizzo IP cambia se eliminare e ricreare il gateway VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Come ottenere autenticato personale tunnel VPN?

Azure VPN utilizza l'autenticazione PSK (chiave già condivisa). Abbiamo generare una chiave già condivisa (PSK) quando si crea tunnel VPN. È possibile modificare PSK generato automaticamente a quello personalizzato con il cmdlet Set PowerShell chiave già condivisa o API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>È possibile utilizzare l'API di chiave già condivisa impostare per configurare i criteri basati (routing statico) gateway VPN?

Sì, è possibile utilizzare il cmdlet Set API chiave già condivisa e PowerShell per configurare Azure VPN (statica) basata su criteri e basate su route VPN routing (dinamica).

### <a name="can-i-use-other-authentication-options"></a>È possibile utilizzare altre opzioni di autenticazione?

Si sono limitate all'utilizzo di chiavi già condivise (PSK) per l'autenticazione.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>Che cos'è "subnet gateway" e perché è necessaria?

Si dispone di un servizio gateway eseguite per abilitare la connettività tra locale. 

È necessario creare una subnet gateway per il VNet configurare un gateway VPN. Tutte le subnet gateway devono essere denominate GatewaySubnet per il corretto funzionamento. Non assegnare un nome subnet gateway un altro elemento. E non distribuire macchine virtuali o qualsiasi altro elemento alla subnet gateway.

La dimensione minima di gateway subnet dipende interamente la configurazione che si desidera creare. Sebbene sia possibile creare una subnet gateway assuma /29 per alcune configurazioni, è consigliabile creare una subnet di gateway di /28 pollici (/ 28, /27, /26 ecc.). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>È possibile distribuire macchine virtuali o istanze del ruolo alla subnet gateway?

No.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Come si può specificare quale del traffico attraverso il gateway VPN?

Se si utilizza il portale classica di Azure, aggiungere ogni intervallo che si desidera inviato tramite il gateway per la rete virtuale nella pagina reti in reti locali.

### <a name="can-i-configure-forced-tunneling"></a>È possibile configurare forzata Tunneling?

Sì. Vedere [configurare forzata tunneling](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>È possibile impostare il proprio server VPN in Azure e usarlo per connettersi alla rete locale?

Sì, è possibile distribuire il proprio server in Azure da Azure Marketplace o creazione router VPN o gateway VPN. È necessario configurare le route definite dall'utente della rete virtuale per assicurarsi che il traffico indirizzato correttamente tra le reti locale e le subnet virtuali.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Perché vengono determinate porte aperte nel mio gateway VPN?

Sono necessari per la comunicazione infrastruttura Azure. Sono protetti (bloccato) da Azure certificati. Senza certificati corretti, entità esterne, tra cui i clienti di tali gateway, non sarà possibile causare alcun effetto su tali endpoint.

Un gateway VPN è sostanzialmente un dispositivo con più ospitati con un tocco di NIC tutta la rete privata dei clienti e una scheda NIC affiancate rete pubblica. Entità dell'infrastruttura di Azure non è possibile accedere istantaneamente reti private del cliente per motivi di conformità, in modo che sia necessario utilizzare endpoint pubblico per la comunicazione dell'infrastruttura. Endpoint pubblico sono periodicamente analizzati dal controllo di sicurezza di Azure.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Ulteriori informazioni sui tipi di gateway, requisiti e la velocità di trasmissione

Per ulteriori informazioni, vedere [Sulle impostazioni del Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Più siti e alla connettività di VNet-VNet

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Il tipo di gateway supportino più siti e alla connettività di VNet-VNet?

Solo in base route VPN (routing dinamico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>È possibile collegare un VNet con un tipo di VPN RouteBased a un'altra VNet a un tipo di PolicyBased VPN?

No, entrambi le reti virtuali devono utilizzare basate su route (routing dinamico) VPN.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>È protetto il traffico VNet-VNet?

Sì, è protetto da crittografia IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>Il traffico VNet a VNet trasmesse dorsale Azure?

Sì.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Numero di siti locale e reti virtuali possibile una rete virtuale effettuare la connessione?

Max. 10 combinata per i gateway Basic e il Routing dinamico Standard; 30 per i gateway VPN prestazioni elevato.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>È possibile usare punto to a Site VPN con la rete virtuale con tunnel VPN più?

Sì, è possibile utilizzare VPN punto al sito (P2S) con i gateway VPN la connessione a più siti locale e altre reti virtuale.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>È possibile configurare più tunnel tra la rete virtuale e sito personale locale tramite VPN più siti?

No, ridondanti tunnel tra una rete virtuale Azure e un sito locale non sono supportati.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Possibile che ci siano sovrapposti spazi indirizzo tra le reti connesse virtuale e siti locali locale?

No. Spazi degli indirizzi sovrapposti causeranno il caricamento di file di configurazione di rete o "Creazione virtuali" errore di.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>È possibile ottenere maggiore larghezza di banda con altre VPN da sito più per una singola rete virtuale?

No, tutti i tunnel VPN, inclusi VPN punto al sito e condividono lo stesso gateway VPN Azure e della larghezza di banda.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>È possibile usare gateway VPN Azure per il traffico di transito tra i siti personali in locale o a un'altra rete virtuale?

**Modello di distribuzione classica**<br>
Il traffico di transito tramite gateway VPN Azure, è possibile utilizzare il modello di distribuzione classico, ma si basa su spazi indirizzo statico definito nel file di configurazione di rete. BGP non è ancora supportato con reti virtuali Azure e VPN gateway utilizzando il modello di distribuzione classica. Senza BGP, definizione manuale di transito indirizzo spazi è molto errore soggetto e scelta non consigliata.<br>
**Modello di distribuzione di Manager delle risorse**<br>
Se si usa il modello di distribuzione di Manager delle risorse, vedere la sezione [BGP](#bgp) per altre informazioni.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure genera la stessa chiave già condivisa IPsec/IKE per tutte le connessioni VPN per la stessa rete virtuale?

No, Azure per impostazione predefinita genera diversi chiavi condivise per le connessioni VPN diversi. Tuttavia, è possibile utilizzare il cmdlet Set VPN Gateway chiave API REST o PowerShell per impostare il valore di chiave che si preferisce. Il tasto deve essere alfanumerici stringa di lunghezza da 1 a 128 caratteri.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure applicare costi per il traffico tra reti virtuali?

Per il traffico tra diverse reti virtuali Azure, Azure costi solo per il traffico passando da un'area di Azure per un'altra. La velocità di carica viene elencata nella pagina di Azure [Prezzi Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) .


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>È possibile collegare una rete virtuale con IPsec VPN per il circuito ExpressRoute?

Sì, supportata. Per ulteriori informazioni, vedere [configurare ExpressRoute e connessioni VPN da sito che coesistono](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>La connettività tra locale e macchine virtuali

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se la macchina virtuale è in una rete virtuale e si dispone di una connessione tra locale, come è consigliabile connettersi alla macchina virtuale?

Sono disponibili alcune opzioni. Se si dispone di RDP abilitato e aver creato un endpoint, è possibile connettersi alla macchina virtuale tramite l'indirizzo VIP. In questo caso, specificare l'indirizzo VIP e la porta che si desidera connettersi. È necessario configurare la porta sul computer virtuale per il traffico. In genere, si vuole accedere al portale classica di Azure e salvare le impostazioni per la connessione RDP nel computer in uso. Le impostazioni contengono le informazioni di connessione necessarie.

Se si dispone di una rete virtuale con la connettività tra locale configurata, è possibile connettersi alla macchina virtuale utilizzando il DIP interno o l'indirizzo IP privato. È anche possibile connettersi alla macchina virtuale da DIP interno da un altro computer virtuale che si trova nella stessa rete virtuale. Non è possibile RDP alla macchina virtuale utilizzando l'indirizzo DIP se si esegue la connessione da una posizione di fuori rete virtuale. Ad esempio, se si dispone di una rete virtuale punto al sito configurata e non stabilisce una connessione dal computer, non è possibile connettersi alla macchina virtuale da DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se la macchina virtuale si trova in una rete virtuale con la connettività tra locale, tutto il traffico da personale macchine Virtuali elaborata la connessione?

No. Solo il traffico che ha una destinazione IP contenuta in intervalli di indirizzi IP di rete locale virtuali specificata passerà attraverso il gateway di rete virtuale. Il traffico ha una destinazione IP si trova all'interno della rete virtuale rimane all'interno della rete virtuale. Altri tipi di traffico viene inviato tramite il servizio di bilanciamento del carico reti pubbliche o se si utilizza tunneling forzata, inviati tramite il gateway VPN Azure. Se si sta risolvendo, è importante verificare di soddisfare tutti gli intervalli elencati nella rete locale che si desidera inviare attraverso il gateway. Verificare che gli intervalli di indirizzi di rete locale non si sovrappongono con gli intervalli di indirizzi della rete virtuale. Inoltre, si desidera verificare che il server DNS in uso è la risoluzione del nome per l'indirizzo IP appropriato.


## <a name="virtual-network-faq"></a>Virtuali domande frequenti

Visualizzare informazioni aggiuntive virtuali nelle [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md).
 
