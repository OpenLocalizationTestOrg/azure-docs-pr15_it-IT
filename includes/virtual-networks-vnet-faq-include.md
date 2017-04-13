## <a name="virtual-network-basics"></a>Nozioni fondamentali sulla rete virtuale

### <a name="what-is-an-azure-virtual-network-vnet"></a>Che cos'è una rete Azure virtuale (VNet)?

È possibile utilizzare VNets effettuare il provisioning e gestire le reti private virtuali (VPN) in Azure e, facoltativamente, è possibile creare un collegamento VNets con altri VNets in Azure o con le locale infrastruttura IT per creare soluzioni ibrido o tra locale. Ogni VNet create con il proprio blocco CIDR e possono essere collegati ad altre reti VNets e locale, purché i blocchi CIDR non sono in conflitto. È inoltre i controlli di impostazioni del server DNS per VNets e segmentazione del VNet in subnet.

Utilizzare VNets per:

- Creare una rete privata dedicata basata solo su cloud virtuale

    In alcuni casi si non è richiesta una configurazione tra locale per la soluzione. Quando si crea un VNet, i servizi e macchine virtuali all'interno del VNet possono comunicare direttamente e sicuro nel cloud. Mantiene il traffico in modo sicuro all'interno di VNet, ma è comunque possibile configurare le connessioni endpoint le macchine virtuali e i servizi che richiedono comunicazioni Internet come parte della soluzione.

- In modo sicuro estendere il nuovo centro di dati

    Con VNets, è possibile creare tradizionale connessioni VPN (S2S) al sito per sicuro ridimensionare la capacità di Data Center. S2S VPN utilizzare IPSEC per fornire una connessione protetta tra il gateway VPN aziendale e Azure.

- Scenari di cloud ibrida

    VNets per ottenere la flessibilità necessaria per supportare una gamma di scenari cloud ibrida. È possibile connettersi in modo sicuro applicazioni basate su cloud di qualsiasi tipo di sistema locale, ad esempio mainframe e sistemi Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Come sapere se è necessario un virtuali?

Visitare [Virtuale panoramica di rete](../articles/virtual-network/virtual-networks-overview.md) per visualizzare una tabella di decisione che consentirà di stabilire l'opzione di progettazione di rete ottimale dell'utente.

### <a name="how-do-i-get-started"></a>Come iniziare?

Visitare [la documentazione della rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per iniziare. Questa pagina contiene collegamenti a comuni operazioni di configurazione, nonché informazioni utili per comprendere le operazioni che è necessario prendere in considerazione per la progettazione di rete virtuale.

### <a name="what-services-can-i-use-with-vnets"></a>A quali servizi è possibile usare con VNets?

VNets utilizzabili con una varietà di servizi di Azure diversi, ad esempio servizi Cloud (PaaS), macchine virtuali e Web Apps. Tuttavia, esistono alcuni servizi che non sono supportati in una VNet. Selezionare il servizio specifico che si desidera utilizzare e verificare che sia compatibile.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>È possibile usare VNets senza connessione tra locale?

Sì. È possibile utilizzare una VNet senza utilizzare la connettività al sito. Questa operazione è particolarmente utile se si desidera eseguire controller di dominio e farm di SharePoint in Azure.

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quali strumenti è necessario usare per creare un VNet?

È possibile utilizzare gli strumenti seguenti per creare o configurare una rete virtuale:

- Portale di Azure (per classica e Manager delle risorse VNets).

- Un file di configurazione della rete (netcfg - per solo VNets classica). Vedere [configurare una rete virtuale con un file di configurazione della rete](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (per classica e Manager delle risorse VNets).

- Azure CLI (per classica e Manager delle risorse VNets).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quali intervalli di indirizzi è possibile usare nel mio VNets

È possibile utilizzare qualsiasi indirizzo IP definiti nella [RFC 1918](http://tools.ietf.org/html/rfc1918)e intervalli di indirizzi IP pubblici.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Sono inclusi gli indirizzi IP pubblici nel mio VNets?

Sì. Per ulteriori informazioni sugli intervalli di indirizzi IP pubblici, vedere [lo spazio indirizzo IP pubblico in una rete virtuale (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Tenere presente che il pubblico IP non sarà possibile accedere direttamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>È previsto un limite per il numero di subnet della rete virtuale?

Non esiste alcun limite al numero di subnet che utilizzare all'interno di un VNet. Tutte le subnet devono essere contenute completamente nello spazio di indirizzi virtuali e non si sovrappongono tra loro.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Esistono restrizioni sull'utilizzo di indirizzi IP all'interno di queste subnet?

Azure riserva alcuni indirizzi IP all'interno di ciascuna subnet. Gli indirizzi IP di nome e il cognome delle subnet sono riservati per conformità protocollo, insieme a 3 altri indirizzi usati per i servizi di Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Come le dimensioni e di piccole dimensioni possono essere VNets e subnet?

Il più piccolo subnet che è supportato è un /29 e il valore più grande è un /8 (con le definizioni di subnet CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>È possibile importare le VLAN in Azure utilizzando VNets?

No. VNets sono sovrapposizioni di livello 3. Azure non supporta qualsiasi semantica di livello 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>È possibile specificare criteri di routing personalizzati personale VNets e subnet?

Sì. È possibile utilizzare utente definito Routing (UDR). Per ulteriori informazioni su UDR, visitare [route definiti dall'utente e indirizzi IP di inoltro](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Supporto tecnico VNets multicast o broadcast?

No. Non è supportata multicast o broadcast.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quali protocolli è utilizzare all'interno di VNets?

È possibile utilizzare protocolli standard basato su IP all'interno di VNets. Tuttavia, all'interno di VNets vengono bloccati pacchetti encapsulated multicast, trasmissione, IP in IP e pacchetti (generico Routing Encapsulation). Protocolli standard che funzionano includono:

- TCP
- UDP
- PACCHETTI ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>È possibile eseguire il ping il router predefinito all'interno di un VNet?

No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>È possibile utilizzare tracert per la diagnosi di connettività?

No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>È possibile aggiungere subnet dopo aver creato la VNet?

Sì. Come indirizzo subnet non fa parte di un'altra subnet il VNet, subnet possono essere aggiunti a VNets in qualsiasi momento.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>È possibile modificare le dimensioni del personale subnet dopo aver creato?

È possibile aggiungere, rimuovere, espandere o ridurre una subnet, se non esistono macchine virtuali o servizi distribuiti all'interno di essa mediante i cmdlet di PowerShell o il file NETCFG. È anche possibile aggiungere, rimuovere, espandere o ridurre eventuali prefissi come subnet che contengono macchine virtuali o servizi non sono interessate dalla modifica.

### <a name="can-i-modify-subnets-after-i-created-them"></a>È possibile modificare subnet dopo aver creato?

Sì. È possibile aggiungere, rimuovere e modificare blocchi CIDR utilizzati da un VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>È possibile connettersi a internet se si esegue miei servizi in un VNet?

Sì. Tutti i servizi distribuiti all'interno di una VNet possono connettersi a internet. Un VIP pubblicamente utilizzabili assegnato ogni servizio cloud distribuita in Azure. È necessario definire i punti finali di input per i ruoli PaaS e gli endpoint per macchine virtuali di abilitare questi servizi accettare le connessioni da internet.

### <a name="do-vnets-support-ipv6"></a>VNets supporta IPv6?

No. È possibile utilizzare IPv6 con VNets al momento.

### <a name="can-a-vnet-span-regions"></a>Un VNet interessare aree?

No. Un VNet è limitato a una singola regione.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>È possibile collegare un VNet a un'altra VNet in Azure?

Sì. È possibile creare VNet per la comunicazione VNet utilizzando API REST o Windows PowerShell. È anche possibile connettersi VNets tramite VNet Peering. Visualizzare altri dettagli peering [qui.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Risoluzione dei nomi (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quali sono le opzioni di DNS per VNets?

Utilizzare la tabella di decisione nella pagina [Risoluzione dei nomi per macchine virtuali e istanze del ruolo](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) in modo semplificato tutte le opzioni di DNS disponibili.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>È possibile specificare i server DNS per un VNet?

Sì. Indirizzi IP di server DNS è possibile specificare le impostazioni di VNet. Questa operazione verrà applicata come i server DNS predefinito per tutte le macchine virtuali nel VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Il numero di server DNS è specificare?

È possibile specificare fino a 12 server DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>È possibile modificare i server DNS dopo aver creato la rete?

Sì. È possibile modificare l'elenco dei server DNS per il VNet in qualsiasi momento. Se si cambia l'elenco dei server DNS, sarà necessario riavviare ognuna delle macchine virtuali nel VNet affinché per sollevare il nuovo server DNS.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Che cos'è DNS forniti Azure e con VNets funziona?

Condizione Azure DNS è un servizio DNS multi-tenant offerto da Microsoft. Azure registra tutte le macchine virtuali e istanze del ruolo per il servizio. Questo servizio fornisce la risoluzione dei nomi per nome host per macchine virtuali e istanze del ruolo del servizio cloud stesso e per FQDN per macchine virtuali e istanze del ruolo in VNet stesso.

> [AZURE.NOTE] Esiste un limite al momento ai servizi 100 cloud prima della rete virtuale per la risoluzione dei nomi tra tenant utilizzando il DNS forniti Azure. Se si usa il proprio server DNS, questa limitazione non è valida.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>È possibile ignorare le impostazioni DNS in una macchina per virtuale / service base?

Sì. È possibile impostare i server DNS in base al cloud servizio per ignorare le impostazioni predefinite di rete. Tuttavia, si consiglia di utilizzare DNS tutta la rete il più possibile.

### <a name="can-i-bring-my-own-dns-suffix"></a>È possibile visualizzare il proprio suffisso DNS?

No. È possibile specificare un suffisso DNS personalizzato per il VNets.

## <a name="vnets-and-vms"></a>VNets e macchine virtuali

### <a name="can-i-deploy-vms-to-a-vnet"></a>È possibile distribuire macchine virtuali a un VNet?

Sì.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>È possibile distribuire macchine virtuali Linux a un VNet?

Sì. È possibile distribuire qualsiasi distro di Linux supportati da Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>Che cos'è la differenza tra un VIP pubblico e un indirizzo IP interno?

- Un indirizzo IP interno è un indirizzo IP assegnati a ogni macchina virtuale all'interno di un VNet da DHCP. Non è esposto pubblico. Se è stato creato un VNet, nell'intervallo specificato nelle impostazioni subnet del VNet viene assegnato l'indirizzo IP interno. Se non si dispone di un VNet, verrà comunque assegnato un indirizzo IP interno. L'indirizzo IP interno rimarrà con la macchina virtuale per la relativa durata, a meno che non che vengono rilasciato macchine Virtuali.

- Un VIP pubblico è l'indirizzo IP pubblico che è stata assegnata il bilanciamento del servizio o per caricarli cloud. Non è stato assegnato direttamente al NIC. VM L'indirizzo VIP rimane con il servizio cloud che viene assegnato a fino a tutte le macchine virtuali in servizio cloud sono rilasciato o eliminato. A questo punto, viene rilasciato.

### <a name="what-ip-address-will-my-vm-receive"></a>Quale indirizzo IP riceveranno le macchine Virtuali?

- **Indirizzo IP interno-** Se si distribuisce una macchina virtuale per un VNet, la macchina virtuale riceve un indirizzo IP interno di un pool di indirizzi IP interni specificate. Macchine virtuali comunicano all'interno di VNets mediante gli indirizzi IP interni. Sebbene Azure viene assegnato un indirizzo IP interno dinamico, è possibile richiedere un indirizzo per la macchina virtuale. Per ulteriori informazioni sugli indirizzi IP interni statici, vedere [come impostare un indirizzo IP interno statico](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** La macchina virtuale è associata a un VIP anche anche se un VIP non è mai stato assegnato la macchina virtuale direttamente. Un VIP è un indirizzo IP pubblico che può essere assegnato al servizio cloud. Facoltativamente, è possibile prenotare un indirizzo VIP del servizio cloud.

- **ILPIP-** È anche possibile configurare un indirizzo IP a livello di istanza pubblico (ILPIP). ILPIPs sono direttamente associati a macchina virtuale, anziché il servizio cloud. Per ulteriori informazioni su ILPIPs, visitare il [Livello di istanza pubblico IP Panoramica](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>È possibile prenotare un indirizzo IP interno per una macchina virtuale che creerà in un secondo momento?

No. Non è possibile prenotare un indirizzo IP interno. Se è disponibile un indirizzo IP interno verranno assegnata a un'istanza macchine Virtuali o ruolo dal server DHCP. Quella macchina virtuale potrebbe o potrebbe non essere quello che si desidera l'indirizzo IP interno da assegnare a. È tuttavia possibile, modificare l'indirizzo IP interno di una macchina virtuale già creata a qualsiasi indirizzo IP interno disponibile.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Eseguire interno cambiamento di indirizzi IP per macchine virtuali in un VNet?

Sì. Indirizzi IP interni rimangono con la macchina virtuale per la durata a meno che non viene rilasciata la macchina virtuale. Quando una macchina virtuale operazione, l'indirizzo IP interno verrà rilasciato a meno che non è definito un indirizzo IP interno per la macchina virtuale. Se la macchina virtuale semplicemente arrestata (e non inserire in stato **arrestato (Deallocated)**) l'indirizzo IP rimarrà assegnato a macchina virtuale.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>È possibile assegnare manualmente indirizzi IP a schede in macchine virtuali?

No. Non è necessario modificare le proprietà dell'interfaccia di macchine virtuali. Le modifiche che potrebbero rimandare a potenzialmente perso la connettività per la macchina virtuale.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>Cosa succede ai miei indirizzi IP se arresta una macchina virtuale?

Nessun elemento. Indirizzi IP (VIP pubblico e indirizzo IP interno) rimarranno con il servizio cloud o la macchina virtuale.

> [AZURE.NOTE] Se si vuole semplicemente arrestare la macchina virtuale, non usare il portale di gestione per farlo. Attualmente, il pulsante di chiusura verrà rilasciare la macchina virtuale.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>È possibile spostare macchine virtuali da una subnet a un'altra subnet in un VNet senza nuovamente la distribuzione?

Sì. È possibile trovare altre informazioni [di seguito](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>È possibile configurare un indirizzo MAC statico per la macchina virtuale?

No. Un indirizzo MAC non è configurato in modo statico.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>L'indirizzo MAC rimarrà invariato per la macchina virtuale dopo che è stata creata?

Sì, l'indirizzo MAC rimarrà invariato per una macchina virtuale anche se la macchina virtuale è stata interrotta (deallocato) e riproposto.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso effettuare la connessione internet da una macchina virtuale in un VNet?

Sì. Tutti i servizi distribuiti all'interno di una VNet possono connettersi a Internet. Inoltre, ogni servizio cloud distribuita in Azure un VIP pubblicamente utilizzabili assegnato. È necessario definire i punti finali di input per i ruoli PaaS e gli endpoint per macchine virtuali di abilitare questi servizi accettare le connessioni da Internet.

## <a name="vnets-and-services"></a>VNets e servizi

### <a name="what-services-can-i-use-with-vnets"></a>A quali servizi è possibile usare con VNets?

È possibile utilizzare solo i servizi di calcolo all'interno di VNets. Calcolare i servizi sono limitati ai servizi Cloud (ruoli web e di lavoro) e macchine virtuali.

### <a name="can-i-use-web-apps-with-virtual-network"></a>È possibile utilizzare Web Apps con virtuali?

Sì. È possibile distribuire Web Apps all'interno di un VNet utilizzando ASE (ambiente servizio App). Aggiunta a quello, Web Apps possibile sicuro connettersi e accedere alle risorse il VNet Azure se hai configurato per il VNet punto al sito. Per ulteriori informazioni, vedere le operazioni seguenti:


- [Creazione di applicazioni Web in un ambiente App](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Integrazione di rete virtuale Web App](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Utilizzo di integrazione VNet e connessioni ibrido con Web Apps](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrare un'app web con una rete virtuale Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>È possibile distribuire i servizi cloud con ruoli web e lavoro (PaaS) in un VNet?

Sì. È possibile distribuire i servizi PaaS VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Come è possibile distribuire PaaS ruoli in un VNet?

È possibile eseguire questa operazione specificando il nome VNet e i mapping di /subnet ruolo nella sezione Configurazione della rete della configurazione del servizio. Non è necessario aggiornare i file binari.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>È possibile spostare i miei servizi e disconnettersi VNets?

No. Non è possibile spostare i servizi e disconnettersi VNets. È necessario eliminare e ridistribuire il servizio per spostarlo VNet un'altra.

## <a name="vnets-and-security"></a>VNets e sicurezza

### <a name="what-is-the-security-model-for-vnets"></a>Che cos'è il modello di sicurezza per VNets?

VNets sono completamente isolate da tra loro e altri servizi di infrastruttura di Azure. Un VNet è un limite di protezione.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>È possibile definire ACL o NSGs nel mio VNets?

No. Non è possibile associare ACL o NSGs a VNets. Tuttavia, ACL possono essere definiti sugli endpoint di input per macchine virtuali distribuiti a una VNets e NSGs possono essere associati alla subnet o NIC.

### <a name="is-there-a-vnet-security-whitepaper"></a>Esiste un white paper sulla protezione VNet?

Sì. È possibile scaricare [qui](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API, schemi e strumenti

### <a name="can-i-manage-vnets-from-code"></a>È possibile gestire VNets dal codice?

Sì. È possibile utilizzare le API REST per gestire la connettività VNets e cross-locale. Altre informazioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>È disponibile il supporto degli strumenti per VNets?

Sì. È possibile utilizzare gli strumenti di PowerShell e riga di comando per un'ampia varietà di piattaforme. Altre informazioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=317721).
