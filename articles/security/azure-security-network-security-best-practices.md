<properties
   pageTitle="Procedure consigliate per la protezione di rete Azure | Microsoft Azure"
   description="In questo articolo fornisce un set di procedure consigliate per l'uso di sicurezza rete Azure funzionalità integrate."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Procedure consigliate per la protezione di rete Azure

Microsoft Azure consente di connettersi macchine virtuali e accessori in altri dispositivi di rete posizionando tali reti Azure virtuale. Una rete virtuale Azure è un costrutto virtuali che è possibile connettere schede di rete virtuale a una rete per consentire le comunicazioni basate su TCP/IP tra i dispositivi di rete abilitata. Azure macchine virtuali connessi a una rete virtuale Azure sono in grado di connettersi ai dispositivi nella stessa Azure virtuale rete, Azure virtuale reti diverse, pari in rete locale o su Internet.

In questo articolo verranno illustrati un insieme di rete Azure le procedure consigliate. Queste procedure consigliate sono derivate dai esperienza con Azure rete e le esperienze dei clienti come se stessi.

Per ogni ottimale, spiegheremo:

- Che cos'è la procedura consigliata
- Perché si desidera attivare la procedura consigliata
- Cosa può essere il risultato se non si riesce ad attivare la procedura consigliata
- Possibili soluzioni alternative per la procedura consigliata
- Come è possibile informazioni su come abilitare la procedura consigliata

In questo articolo Azure rete le procedure consigliate si basa su un parere il consenso e set di caratteristiche e funzionalità di piattaforma Azure in cui si trovano in fase di che questo articolo è stato scritto. Selezionare una variante e le tecnologie cambiano nel tempo e in questo articolo verrà aggiornato regolarmente per riflettere le modifiche.

Azure rete le procedure consigliate descritte in questo articolo includono:

- In modo logico subnet segmento
- Controllare il comportamento di routing
- Abilitare Tunneling forzata
- Utilizzare i dispositivi di rete virtuale
- Distribuire demilitarizzate di suddivisione in zone di sicurezza
- Evitare l'esposizione a Internet con collegamenti WAN dedicati
- Ottimizzare le prestazioni e tempi di attività
- Utilizzare bilanciamento del carico globale
- Disabilitare l'accesso RDP a macchine virtuali di Azure
- Abilita Azure Security Center
- Estendere la Data Center in Azure


## <a name="logically-segment-subnets"></a>In modo logico subnet segmento

[Reti virtuali Azure](https://azure.microsoft.com/documentation/services/virtual-network/) sono simili a una rete LAN nella rete locale. L'idea di una rete virtuale Azure è creare una singola rete IP privata indirizzo spazio basato su cui è possibile inserire tutte le [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/). Gli spazi di indirizzo IP privati disponibili sono la classe (10.0.0.0/8), classe B (172.16.0.0/12) e classe C intervalli (192.168.0.0/16).

Simile a quanto è locale, è consigliabile segmento maggiore spazio di indirizzi in subnet. È possibile utilizzare [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) in base a subnet principi per creare le subnet.

Routing tra subnet verrà eseguito automaticamente e non è necessario configurare manualmente le tabelle di routing. Tuttavia, l'impostazione predefinita è che non sono presenti controlli di accesso di rete tra subnet che creare sulla rete virtuale Azure. Per creare i controlli di accesso di rete tra subnet, è necessario inserire un elemento tra le subnet.

Una delle operazioni che è possibile utilizzare per eseguire questa attività è un [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs sono dispositivi di ispezione pacchetti incluso semplice che utilizzano la tupla 5 (l'indirizzo IP di origine, porta di origine, IP di destinazione, porta di destinazione e protocollo di livello 4) approccio per creare/consentiti regole per il traffico di rete. È possibile consentire o impedire il traffico da e verso singolo indirizzo IP, da e in più indirizzi IP o anche per e da subnet intera.

Utilizzo NSGs per il controllo di accesso di rete tra subnet consente di inserire le risorse che appartengono al ruolo nella propria subnet o all'area di protezione stesso. Ad esempio, considerare un'applicazione di livello 3 semplice con un livello web, un livello di logica di applicazione e un database. Inserire macchine virtuali che appartengono a ognuno di questi livelli nelle proprie subnet. E quindi utilizzare NSGs per controllare il traffico tra le subnet:

- Macchine virtuali di livello Web possono iniziare solo le connessioni ai computer logica dell'applicazione e può accettare solo connessioni da Internet
- Macchine virtuali di applicazione logica può solo stabilire una connessione con livello di database e può accettare solo connessioni dal livello web
- Macchine virtuali di livello di database non è possibile avviare connessione con qualsiasi all'esterno di subnet propria e può accettare solo connessioni dal livello di logica applicazione

Per ulteriori informazioni sui gruppi di sicurezza di rete e come è possibile utilizzare per logicamente segmento le reti virtuali Azure, leggere l'articolo [che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Controllare il comportamento di routing

Quando si inserisce una macchina virtuale in una rete virtuale Azure, si noterà che la macchina virtuale è possibile connettersi a un altro computer virtuali nella stessa rete virtuale Azure, anche se altre macchine virtuali sono subnet diverse. Il motivo per cui è possibile è un insieme di route di sistema attivata per impostazione predefinita che consentono di questo tipo di comunicazione. Questi percorsi predefiniti consentono macchine virtuali nella stessa rete virtuale di Azure per avviare le connessioni tra loro e con Internet (per le comunicazioni in uscita a Internet solo).

Mentre le route di sistema predefinite sono utili per molti scenari di distribuzione, esistono casi in cui si desidera personalizzare la configurazione del routing per le distribuzioni. Queste personalizzazioni consentono di configurare l'indirizzo di hop successivo per raggiungere destinazioni specifiche.

È consigliabile configurare route definiti dall'utente quando si distribuisce un dispositivo di protezione virtuali, vedremo in una versione successiva ottimale.

> [AZURE.NOTE] Indirizza definiti dall'utente non sono obbligatorie e route di sistema predefinite funzionano nella maggior parte dei casi.

Sono disponibili ulteriori sulle route definite dall'utente e su come configurarli, leggere l'articolo [informazioni sui percorsi definiti dall'utente e indirizzi IP di inoltro](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Abilitare Tunneling forzata

Per comprendere meglio tunneling forzata, è utile per capire quali "divisa di tunneling".
L'esempio più comune di divisione tunneling viene visualizzato con le connessioni VPN. Si supponga di stabilire una connessione VPN dall'albergo alla rete aziendale. Questa connessione consente di connettersi alle risorse nella rete aziendale e tutte le comunicazioni alle risorse nella rete aziendale elaborata tunnel VPN.

Cosa accade quando si desidera connettersi alle risorse su Internet? Quando è abilitato la divisione tunneling, le connessioni passare direttamente a Internet e non attraverso il tunnel VPN. Alcuni esperti di sicurezza di prendere in considerazione questa opzione per essere potenziali rischi e pertanto consigliabile che divisa tunneling disabilitato e tutte le connessioni destinati a Internet e quelle destinati alle risorse aziendali, elaborata tunnel VPN. Il vantaggio di questa operazione è che le connessioni a Internet quindi risulteranno tramite i dispositivi di sicurezza alla rete aziendale, se il client VPN connesso a Internet all'esterno di tunnel VPN non sarebbero le maiuscole/minuscole.

A questo punto si riportare questo in macchine virtuali su una rete virtuale Azure. Indirizza predefinito per una rete virtuale Azure consentire macchine virtuali avviare il traffico a Internet. Questo troppo rappresentare un rischio, come le connessioni in uscita potrebbero aumentare la possibilità di attacchi di una macchina virtuale e utilizzate per attacchi.
Per questo motivo, è consigliabile attivare forzata tunneling nelle macchine virtuali quando è disponibile la connettività tra locale tra la rete virtuale Azure e la rete locale. Verrà discusso su cross connettività locale più avanti in questo Azure rete procedure consigliate.

Se non si dispone di una connessione locale cross, assicurarsi che è usufruire di gruppi di sicurezza di rete (descritto in precedenza) o Azure virtuale dispositivi di rete sicurezza (descritte Avanti) per evitare che le connessioni in uscita a Internet le macchine virtuali Azure.

Per ulteriori informazioni su come forzata tunneling e sull'attivazione, leggere l'articolo [configurare forzata Tunneling tramite PowerShell e gestione di risorse di Azure](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utilizzare i dispositivi di rete virtuale

Mentre gruppi di sicurezza di rete e il Routing definite dall'utente può fornire un certo grado di sicurezza di rete i livelli di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), sono sta per essere situazioni in cui si sarà utile o necessario attivare la protezione di alto livello dello stack. In tal caso, è consigliabile distribuire apparecchiature di sicurezza virtuali forniti da Azure partner.

Apparecchiature di sicurezza di rete Azure offrono livello di protezione in modo significativo avanzato rispetto ai quali viene fornito da controlli del livello di rete. Alcune delle funzionalità di sicurezza di rete fornite da apparecchiature di sicurezza virtuali includono:

- Il blocco
- Rilevamento di intrusione/intrusione prevenzione
- Gestione delle vulnerabilità
- Controllo dell'applicazione
- Rilevamento delle anomalie in rete
- Il filtro Web
- Antivirus
- Protezione indesiderati

Se si richiede un livello superiore della protezione di rete che è possibile ottenere con controlli di accesso a livello di rete, è consigliabile esaminare e distribuire apparecchiature di sicurezza di Azure virtuali.

Per ulteriori informazioni sulle quali apparecchiature di sicurezza di Azure virtuali disponibili e sulle loro funzionalità, visitare la [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "protezione di rete".

##<a name="deploy-dmzs-for-security-zoning"></a>Distribuire demilitarizzate di suddivisione in zone di sicurezza
Una rete Perimetrale o "rete perimetrale" è un segmento di rete fisico o logico è progettato per fornire un ulteriore livello di sicurezza tra le risorse e Internet. Lo scopo della rete Perimetrale consiste nel posizionare i dispositivi di rete specializzate accesso controllo sul bordo della rete rete Perimetrale, in modo che solo il traffico desiderato consentito oltre il dispositivo di protezione di rete e alla rete aziendale virtuale di Azure.

Demilitarizzate sono utili perché consente di tracciare Gestione controllo l'accesso alla rete, monitoraggio, registrazione e report sui dispositivi sul bordo della rete virtuale Azure. Di seguito si consentirebbe in genere prevenzione DDoS, intrusione rilevamento/intrusione sistemi (ID/IP), le regole firewall e criteri, il filtro web, antimalware di rete e altro ancora. I dispositivi di sicurezza di rete occupare tra Internet e di rete virtuale Azure e di avere un'interfaccia in entrambe le reti.

Mentre si tratta di progettazione di base di una rete Perimetrale, sono disponibili molti diversi modelli di rete Perimetrale, ad esempio in fronte/retro, ospitati tre, multi-ospitati e gli altri utenti.

Per tutte le distribuzioni di maggiore sicurezza è consigliabile prendere in considerazione la distribuzione di una rete Perimetrale per migliorare il livello di protezione di rete per le risorse di Azure.

Per ulteriori informazioni su demilitarizzate e su come distribuirle in Azure, leggere l'articolo [servizi Cloud Microsoft e la sicurezza della rete](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitare l'esposizione a Internet con collegamenti WAN dedicati
Molte organizzazioni hanno scelto route ibrido IT. In IT ibridi, alcuni dei beni informatici della società sono in Azure, mentre gli altri utenti rimangono locale. In molti casi alcuni componenti di un servizio verranno eseguite Azure mentre altri componenti rimangono in locale.

Scenario IT ibride è in genere alcuni tipi di connettività tra locale. Questo tra locale connettività consente la società a cui connettersi reti virtuali Azure reti locale. Esistono due soluzioni di connettività tra locale:

- VPN da sito
- ExpressRoute

[VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) rappresenta una connessione privata virtuale tra la rete locale e una rete virtuale Azure. La connessione viene eseguita tramite Internet e consente di informazioni "tunnel" all'interno di un collegamento tra la rete e Azure crittografato. VPN da sito è una tecnologia sicura, consolidata distribuiti imprese di tutte le dimensioni per molti anni. La crittografia tunnel viene eseguita modalità [tunnel](https://technet.microsoft.com/library/cc786385.aspx).

Mentre VPN da sito è una tecnologia attendibile, affidabile e Fondazione, il traffico all'interno del tunnel transita su Internet. Inoltre, la larghezza di banda relativamente è vincolato a un massimo di sulle 200Mbps.

Se si richiede un livello di sicurezza o prestazioni eccezionale per le connessioni tra locale, è consigliabile usare Azure ExpressRoute per la connessione tra locale. ExpressRoute è una WAN dedicato collegamento tra la posizione dell'utente locale o un provider di hosting di Exchange. Poiché si tratta di una connessione di telecomunicazione, i dati non trasmessi tramite Internet e non viene esposto a potenziali rischi inerenti le comunicazioni Internet.

Per ulteriori informazioni sul funzionamento di Azure ExpressRoute e su come distribuire, leggere l'articolo [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Ottimizzare le prestazioni e tempi di attività
Riservatezza, integrità e la disponibilità (CIA) comprendono tre del modello di protezione più importante odierna. Livello di riservatezza sia sulla crittografia e privacy, integrità piuttosto assicurandosi che dati non viene modificati da utenti non autorizzati possano e la disponibilità si riferisce assicurandosi che gli utenti autorizzati siano in grado di accedere alle informazioni che sono autorizzati ad accedere. Errore in uno qualsiasi di queste aree rappresenta un potenziale violazione della protezione.

Disponibilità possa essere considerata come sui tempi di attività e le prestazioni. Se un servizio non è attivo, non è possibile accedere alle informazioni. Se le prestazioni scadente in modo da rendere inutilizzabile i dati, è possibile prendere in considerazione i dati da non essere accessibili. Da una prospettiva di sicurezza, pertanto è necessario eseguire tutti i valori che è possibile per assicurarsi che i servizi dispongono prestazioni e tempi di attività ottimali.
Un metodo più diffuso ed efficace di migliorare la disponibilità e delle prestazioni consiste nell'utilizzare il bilanciamento del carico. Bilanciamento del carico è un metodo per la distribuzione del traffico di rete server che fanno parte di un servizio. Ad esempio, se si dispone di server web front-end come parte del servizio, è possibile utilizzare il bilanciamento del carico per distribuire il traffico tra più server web front-end.

La distribuzione del traffico aumenta disponibilità poiché se uno dei server web non è disponibile, il servizio di bilanciamento del carico sarà interrompere l'invio il traffico al server e reindirizzare il traffico al server che sono ancora in linea. Bilanciamento del carico consente inoltre prestazioni ottimali, perché il processore, rete e della memoria il sovraccarico per la gestione delle richieste è distribuito in tutto il carico bilanciato server.

È consigliabile l'utilizzo di bilanciamento del carico ogni volta che è possibile e in base alle esigenze per i servizi. È necessario indirizzo opportunità nelle sezioni seguenti.
Livello di Azure virtuali Azure fornisce che si con tre principale opzioni di bilanciamento del carico:

- Bilanciamento del carico basato su HTTP
- Bilanciamento del carico esterno
- Bilanciamento del carico interno

## <a name="http-based-load-balancing"></a>Bilanciamento del carico basato su HTTP
Bilanciamento del carico basato su HTTP basi decidere quali server per l'invio delle connessioni mediante le caratteristiche del protocollo HTTP. Azure ha un bilanciamento del carico HTTP che passa dal nome del Gateway di applicazioni.

È consigliabile che si Contattaci Azure applicazione Gateway quando:

- Applicazioni che richiedono richieste dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end. Esempi di questo caso tipico App carrello e i server di posta elettronica web.
- Applicazioni per sbloccare il server web farm dal sovraccarico di terminazione SSL grazie alla caratteristica [SSL trasferire](https://f5.com/glossary/ssl-offloading) del Gateway dell'applicazione.
- Applicazioni, ad esempio una rete di distribuzione di contenuti che richiedono più richieste HTTP sulla stessa connessione TCP lunga per essere distribuiti o si carica bilanciato nei diversi server di back-end.

Per ulteriori informazioni su come funziona il Gateway di applicazioni Azure e come è possibile utilizzare di una distribuzione, leggere l'articolo [Application Gateway Overview](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Bilanciamento del carico esterno
Bilanciamento del carico esterno entrerà in vigore quando le connessioni in ingresso da Internet sono il bilanciamento del carico tra i server presenti in una rete virtuale Azure. Servizio di bilanciamento del carico esterno Azure può fornire questa funzionalità e si consiglia di utilizzarla quando non è richiesta sessioni permanenti o trasferire SSL.

A differenza di bilanciamento del carico basato su HTTP, il bilanciamento caricare esterni utilizza informazioni i livelli di rete e trasporto del modello di rete OSI per prendere decisioni quali server in cui caricare saldo connessione.

È consigliabile utilizzare Bilanciamento del carico esterno ogni volta che si dispone di [scalabilità delle applicazioni](http://whatis.techtarget.com/definition/stateless-app) accettare le richieste in arrivo da Internet.

Per ulteriori informazioni su come funziona il bilanciamento del carico esterno di Azure e come è possibile distribuirlo, informazioni leggere l'articolo [per iniziare la creazione di un Internet affiancate di bilanciamento del carico in Gestione risorse tramite PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Bilanciamento del carico interno
Bilanciamento del carico interno è simile all'esterno bilanciamento del carico e utilizza lo stesso meccanismo per caricare il saldo connessioni ai server su cui si basano. L'unica differenza è che il servizio di bilanciamento del carico in questo caso accetta le connessioni da macchine virtuali che non sono in Internet. Nella maggior parte dei casi, le connessioni vengono accettate bilanciamento del carico vengono avviate dai dispositivi su una rete virtuale Azure.

È consigliabile utilizzare interno bilanciamento del carico per scenari che verranno trarre vantaggio da questa funzionalità, ad esempio quando è necessario caricare saldo connessioni a SQL Server o server web interno.

Per ulteriori informazioni sul funzionamento di bilanciamento del carico Azure interni e come è possibile distribuire, leggere l'articolo [per iniziare la creazione di un interno di bilanciamento del carico tramite PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Utilizzare bilanciamento del carico globale
Consente di elaborazione cloud pubblica è possibile distribuire globalmente distribuito applicazioni che dispongono di componenti che si trovano nei data center tutto il mondo. Questo è possibile in Microsoft Azure a causa di presenza di Azure Data Center globale. A differenza di bilanciamento del carico tecnologie indicate in precedenza, bilanciamento del carico globale consente di rendere i servizi disponibili anche quando intero Data Center potrebbero non essere disponibili.

È possibile ottenere questo tipo di globale bilanciamento del carico in Azure sfruttando [Azure il traffico Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). Gestore del traffico modo è possibile caricare saldo connessioni ai servizi in base all'ubicazione dell'utente.

Ad esempio, se l'utente è in corso una richiesta al servizio da dell'Unione europea, la connessione viene indirizzata per i servizi che si trovano in un Data Center dell'Unione europea. La parte di traffico gestione globale caricare bilanciamento del carico consente di migliorare le prestazioni perché la connessione al Data Center più vicino è più velocemente di quanto la connessione al Data Center di lontano.

Sul lato disponibilità bilanciamento del carico globale garantisce che il servizio è disponibile anche se un intero Data Center deve diventano disponibili.

Ad esempio, se un Data Center Azure deve essere disponibile a causa di motivi ambientale o a causa di interruzioni (ad esempio errori di rete locale), le connessioni al servizio verrà reindirizzate al commerciali online. Questo bilanciamento del carico globale, è possibile sfruttare i vantaggi dei criteri DNS che è possibile creare in gestore del traffico.

È consigliabile usare gestore del traffico per qualsiasi soluzione cloud si sviluppano con un ambito ampiamente distribuito tra più aree che richiede il massimo livello di disponibilità possibili.

Per ulteriori informazioni su come gestore del traffico Azure e su come distribuirlo, leggere l'articolo [che cos'è il traffico Manager](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Disabilitare l'accesso RDP/SSH a macchine virtuali di Azure
È possibile raggiungere macchine virtuali di Azure utilizzando [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e i protocolli [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Questi protocolli consentono di gestire macchine virtuali da postazioni remote e sono standard in Data Center di elaborazione.

Potenziale problema di protezione con l'uso di questi protocolli tramite Internet è che gli utenti malintenzionati di utilizzare varie tecniche di [forzare](https://en.wikipedia.org/wiki/Brute-force_attack) per accedere a macchine virtuali di Azure. Dopo che gli utenti malintenzionati riescano ad accedere, possono utilizzare la macchina virtuale come punto di avvio per compromettere altri computer della rete virtuale Azure o persino attacchi dispositivi di rete all'esterno di Azure.

Per questi motivi, è consigliabile disabilitare l'accesso diretto RDP e SSH in macchine virtuali Azure da Internet. Dopo aver disabilitato accesso diretto RDP e SSH da Internet, sono disponibili altre opzioni che è possibile usare per accedere a queste macchine virtuali per la gestione remota:

- Punto to a site VPN
- VPN da sito
- ExpressRoute

[Punto-sito VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) è sinonimo di una connessione VPN client/server di accesso remoto. Una rete VPN punto al sito consente un singolo utente a connettersi a una rete virtuale Azure tramite Internet. Dopo avere stabilita la connessione punto a sito, l'utente saranno in grado di utilizzare RDP o SSH per connettersi a qualsiasi macchine virtuali nella rete virtuale Azure che l'utente è connesso a tramite VPN punto al sito. Si presuppone che l'utente è autorizzato a raggiungere tali macchine virtuali.

Punto to a site VPN è più sicura connessioni RDP o SSH dirette perché è necessario eseguire l'autenticazione due volte prima di connettersi a una macchina virtuale dell'utente. Prima di tutto, è necessario eseguire l'autenticazione (e autorizzato) per stabilire la connessione VPN punto al sito; in secondo luogo, l'utente deve eseguire l'autenticazione (e autorizzato) per stabilire la sessione RDP o SSH.

Una [VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) si connette un'intera rete a un'altra rete tramite Internet. È possibile utilizzare una connessione VPN da sito per connettere la rete locale a una rete virtuale Azure. Se si distribuisce una rete VPN da sito, gli utenti nella rete locale sarà in grado di connettersi macchine virtuali nella rete virtuale Azure mediante il protocollo RDP o SSH tramite una connessione VPN da sito e non è necessario consentire l'accesso diretto RDP o SSH tramite Internet.

È anche possibile utilizzare un collegamento WAN dedicato per fornire funzionalità simile a VPN da sito. Le principali differenze sono 1. collegamento WAN dedicato non attraversano Internet e 2. collegamenti WAN dedicati sono in genere più stabile e prestazioni. Azure offre una soluzione di collegamento WAN dedicato sotto forma di [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Abilita Azure Security Center
Azure Security Center consente di impedire, rilevare e rispondere a rischi e offre che maggiore visibilità e controllare i la protezione delle risorse di Azure. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

Centro protezione di Azure consente di ottimizzare e monitorare la sicurezza di rete per:

- Fornire suggerimenti per la sicurezza di rete
- Monitorare lo stato della configurazione di sicurezza di rete
- Avvisa rete in base a minacce entrambi i livelli di endpoint e rete

È consigliabile attivare Azure Security Center per tutte le distribuzioni Azure.

Per ulteriori informazioni su Centro protezione di Azure e su come abilitare per le distribuzioni, leggere l'articolo [Introduzione al Centro protezione di Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>In modo sicuro prolungare il Data Center Azure
IT aziendali molte organizzazioni desiderano per espandere nel cloud anziché crescita propri data center locale. Questa espansione rappresenta un'estensione dell'infrastruttura IT nel cloud pubblico. Sfruttando locale tra le opzioni di integrazione applicativa è possibile gestire le reti virtuali Azure come altro subnet dell'infrastruttura di rete locale.

C'è però molti problemi di pianificazione e progettazione che dovranno essere risolti prima di tutto. Questa operazione è particolarmente importante nell'area di protezione della rete. Uno dei modi migliori per capire come si avvicina a una progettazione consiste nel visualizzare un esempio.

Microsoft ha creato il [Data Center estensione riferimento architettura diagramma](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) e materiale di supporto per facilitare la comprensione aspetto un'estensione di Data Center. In questo modo un esempio di implementazione di riferimento che è possibile utilizzare per pianificare e progettare un'estensione data center aziendale protetto nel cloud. È consigliabile rivedere il documento per avere un'idea dei principali componenti di una soluzione protetta.

Per ulteriori informazioni su come estendere in modo sicuro il Data Center in Azure, visualizzare il video [Si estende il Data Center di Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
