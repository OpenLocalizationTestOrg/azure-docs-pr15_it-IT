### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP è disponibile in tutti gli SKU di Gateway VPN Azure?

No, BGP è supportato in Azure **Standard** e **HighPerformance** VPN i gateway. **Base** SKU non è supportata.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>È possibile usare BGP con gateway VPN Azure Policy-Based?

No, BGP è supportato in solo gateway VPN con la distribuzione.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>È possibile usare ASN privato (numeri sistema autonomo)?

Sì, è possibile usare il proprio ASN pubblico o privato ASN per le reti locali e reti virtuali Azure.

#### <a name="are-there-asns-reserved-by-azure"></a>Esistono ASN riservato da Azure?

Sì, ASN seguenti sono riservate da Azure per peerings interne ed esterne:

- Pubblico ASN: 8075, 8076, 12076
- Privato ASN: 65515, 65517, 65518, 65519, 65520

Non è possibile specificare questi ASN per le locale sui dispositivi VPN per la connessione al gateway VPN Azure.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>È possibile utilizzare la stessa ASN per reti VPN locale e Azure VNets?

No, è necessario assegnare ASN diversi tra le reti locale e il VNets Azure se ci si connette loro insieme BGP. Azure gateway VPN predefinito ASN di 65515 assegnate, utilizzare che possono essere BGP è abilitato per non per la connessione tra locale. È possibile ignorare l'impostazione predefinita mediante l'assegnazione di una diversa ASN durante la creazione dei gateway VPN o modificare il ASN dopo aver creato il gateway. È necessario assegnare il ASN locale a gateway in rete locale Azure corrispondente.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quali prefissi segnala gateway VPN Azure per uno specifico utente?

Azure gateway VPN segnala indirizza seguenti per i dispositivi BGP locale:

- I prefissi VNet
- Prefissi per ogni gateway di rete locale connessi al gateway VPN Azure
- Indirizza acquisite da altre sessioni peering BGP connessione al gateway VPN Azure, **ad eccezione di predefinito o più route sovrapposte con qualsiasi prefisso VNet**.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>È possibile annunciare route predefinita (0.0.0.0/0) al gateway VPN Azure?

Non al momento.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>È possibile annunciare prefissi esattamente come i prefissi virtuali?

No, pubblicità prefissi stesso come uno qualsiasi dei rete virtuale prefissi degli indirizzi bloccati o filtrati in base alla piattaforma Azure. È tuttavia possibile annunciare un prefisso che rappresenta un superset di avere all'interno della rete virtuale. Impossibile annunciare 10.0.0.0/8, ad esempio, la rete virtuale potrebbe utilizzare il 10.10.0.0/16 spazio indirizzo.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>È possibile usare BGP con connessioni VNet-VNet?

Sì, è possibile utilizzare BGP per le connessioni tra locale e VNet a VNet connessioni.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>È possibile combinare BGP con le connessioni non BGP per il gateway VPN Azure?

Sì, è possibile combinare BGP e non BGP connessioni per lo stesso gateway VPN Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>La distribuzione di Azure VPN gateway supporto BGP transito?

Sì, BGP transito routing supportato, con l'eccezione che verranno gateway VPN Azure **non** annuncia route predefinite per gli altri peer BGP. Per abilitare il routing transito tra più gateway VPN Azure, è necessario abilitare BGP su tutte le connessioni VNet-VNet intermedie.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>Si possono aggiungere più tunnel tra gateway VPN Azure e la rete locale?

Sì, è possibile stabilire più tunnel VPN S2S tra un gateway VPN Azure e la rete locale. Si noti che tutti questi tunnel verranno calcolati in base al numero totale di tunnel per il gateway VPN Azure. Ad esempio, se si dispone di due tunnel ridondanti tra il gateway VPN Azure e una rete locale, si utilizzerà 2 tunnel fuori la quota totale per il gateway VPN Azure (10 per Standard) e 30 per HighPerformance.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Si possono aggiungere più tunnel tra due VNets Azure con BGP?

No, ridondanti tunnel tra una coppia di reti virtuali non sono supportati.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>È possibile usare BGP per S2S VPN in una configurazione di coesistenza ExpressRoute/S2S VPN?

Non al momento.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Quale indirizzo utilizzare per BGP Peer IP gateway VPN Azure?

Il gateway VPN Azure allocazione di un solo indirizzo IP dell'intervallo GatewaySubnet definito per la rete virtuale. Per impostazione predefinita, è il secondo indirizzo ultimo dell'intervallo. Ad esempio, se il GatewaySubnet 10.12.255.0/27, compreso tra 10.12.255.0 e 10.12.255.31, quindi l'indirizzo IP Peer BGP nel gateway VPN Azure sarà 10.12.255.30. È possibile trovare queste informazioni quando si elencano le informazioni di gateway VPN Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quali sono i requisiti per gli indirizzi IP di Peer BGP sul dispositivo VPN?

Il BGP locale peer indirizzo **Non deve** essere uguale all'indirizzo IP pubblico del dispositivo VPN. Utilizzare un indirizzo IP diverso sul dispositivo VPN per l'indirizzo IP Peer BGP. Può essere un indirizzo assegnato all'interfaccia di loopback sul dispositivo. Specificare l'indirizzo nella corrispondente Gateway rete locale che rappresenta la posizione.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Che cos'è opportuno possibile specificare come i prefissi degli indirizzi per il Gateway di rete locale quando si utilizzano BGP?

Gateway di rete locale Azure specifica prefissi indirizzo iniziale per la rete locale. Con BGP, è necessario allocare il prefisso host (/ prefisso 32) dell'indirizzo IP Peer BGP come spazio di indirizzi per la rete locale. Se l'indirizzo IP Peer BGP 10.52.255.254, è necessario specificare "10.52.255.254/32" come localNetworkAddressSpace del Gateway di rete locale che rappresenta la rete locale. In questo modo viene che il gateway VPN Azure stabilisce la sessione BGP attraverso il tunnel S2S VPN.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Cosa si devono aggiungere al dispositivo VPN locale per la sessione peering BGP?

Aggiungere una route di host dell'indirizzo IP di Azure BGP Peer nel dispositivo VPN che puntano alle tunnel IPsec S2S VPN. Ad esempio, se l'indirizzo IP Peer di Azure VPN è "10.12.255.30", è necessario aggiungere una route di host per "10.12.255.30" con un'interfaccia hop successivo dell'interfaccia di tunnel IPsec corrispondente nel dispositivo VPN.
