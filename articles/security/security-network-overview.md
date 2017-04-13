<properties
   pageTitle="Cenni preliminari sulla protezione di rete Azure | Microsoft Azure"
   description=" In questo articolo semplifica la comprensione Microsoft Azure sono riportate le novità per l'offerta nell'area di protezione della rete. Sono disponibili base spiegazioni per requisiti e concetti di sicurezza rete e informazioni su Azure è riportate le novità per l'offerta in ognuna di queste aree. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Cenni preliminari sulla protezione di rete Azure

Microsoft Azure include un'infrastruttura di rete affidabile per supportare l'applicazione e i requisiti di connettività di servizio. La connettività di rete è possibile tra le risorse disponibili in Azure, tra locale e Azure ospitato risorse e per e da Internet e Azure.

L'obiettivo di questo articolo è per semplificare la comprensione Microsoft Azure sono riportate le novità per l'offerta nell'area di protezione della rete. Qui sono disponibili base spiegazioni per requisiti e concetti di sicurezza rete. È possibile utilizzare per informazioni su Azure è riportate le novità per l'offerta in ognuna di queste aree. Sono disponibili numerosi collegamenti ad altri contenuti in modo da ottenere una maggiore comprensione relativi alle aree in cui si è interessati.

In questo articolo informazioni generali sulla protezione di rete di Azure riguarderanno le operazioni seguenti:

- Rete Azure
- Controllo accesso alla rete
- Proteggere la connettività remota di accesso e cross-locale
- Disponibilità
- Registrazione
- Risoluzione dei nomi
- Architettura di rete Perimetrale
- Centro protezione di Azure

## <a name="azure-networking"></a>Rete Azure

Macchine virtuali richiedono connettività di rete. Per supportare tale requisito, Azure richiede macchine virtuali di essere connessi a una rete virtuale Azure. Una rete virtuale Azure è un costrutto logico integrato nella parte superiore dell'infrastruttura di rete di Azure fisica. Ogni virtuali Azure logico è isolata da tutte le altre reti virtuale di Azure. In questo modo accertarsi che il traffico di rete di una distribuzione non è accessibile per gli altri utenti di Microsoft Azure.

Ulteriori informazioni:

- [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controllo accesso alla rete
Controllo accesso alla rete comporta la limitazione la connettività dei dispositivi specifici o subnet all'interno di una rete virtuale Azure. L'obiettivo di controllo accesso alla rete è per assicurarsi che le macchine virtuali e i servizi possono accedere solo gli utenti e dispositivi in cui si desidera accessibili. Accedere ai controlli basati su consentire o meno decisioni per le connessioni da e verso la macchina virtuale o servizio.

Azure supporta diversi tipi di controllo accesso alla rete. Sono inclusi:

- Controllo del livello di rete
- Inoltro di controllo e forzata tunneling
- Apparecchiature di sicurezza di rete virtuale

### <a name="network-layer-control"></a>Controllo del livello di rete
Distribuzione di sicuro richiede alcune misure di controllo accesso alla rete. L'obiettivo di controllo accesso alla rete è per assicurarsi che le macchine virtuali e i servizi di rete in esecuzione su tali macchine virtuali possono comunicare solo con altri dispositivi di rete che hanno bisogno di comunicare e tutti gli altri tentativi di connessione vengono bloccati.

Se è necessario controllo di accesso a livello di rete di base (in base all'indirizzo IP e i protocolli TCP o UDP), è possibile utilizzare i gruppi di sicurezza di rete. Un gruppo di sicurezza di rete (NSG) è un firewall di filtraggio dei pacchetti base e consente di controllare l'accesso in base a una [tupla 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs non forniscono ispezione del livello di applicazione o l'autenticazione di accedere ai controlli.

Ulteriori informazioni:

- [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Controllare la distribuzione e Tunneling forzata
La possibilità di controllare il comportamento del routing di una rete virtuale Azure è una funzionalità di controllo accesso e la sicurezza di rete critiche. Se il routing è configurato in modo non corretto, applicazioni e servizi ospitati sul computer virtuale possono connettersi a dispositivi per impedire a cui connettersi, inclusi i dispositivi di proprietà e gestito da utenti malintenzionati potenziali.

Azure networking supporta la possibilità di personalizzare il comportamento di routing per il traffico di rete di una rete virtuale Azure. In questo modo è possibile modificare le voci della tabella routing predefinito della rete virtuale Azure. Controllo del processo di routing consente di verificare che tutto il traffico da un determinato dispositivo o gruppo di dispositivi attiva o disattiva la rete virtuale Azure tramite una posizione specifica.

Ad esempio, potrebbe essere un dispositivo di protezione virtuali nella rete virtuale Azure. Si desidera assicurarsi che tutto il traffico da e verso rete virtuale Azure attraversa tale dispositivo di protezione virtuale. È possibile eseguire questa operazione configurando [Route definiti dall'utente](../virtual-network/virtual-networks-udr-overview.md) in Azure.

[Forzata tunneling](https://www.petri.com/azure-forced-tunneling) è un meccanismo che è possibile utilizzare per assicurarsi che il provider di servizi non sono consentiti per avviare una connessione ai dispositivi su Internet. Si noti che questa è diversa da accettare le connessioni in ingresso e rispondere ad essi. Server web front-end devono rispondere alla richiesta dagli host Internet e pertanto origine Internet il traffico consentito in ingresso a questi server web e i server web sono consentiti per rispondere.

Quali non si vuole consentire è un server web front-end per avviare una richiesta in uscita. Richieste possono rappresentare un rischio perché questi collegamenti potrebbero essere utilizzati per scaricare malware. Anche se si desidera questi server front-end per avviare le richieste in uscita a Internet, è consigliabile imporre elaborata il proxy web locale in modo che è possibile trarre vantaggio dell'URL, filtro e la registrazione.

Se, tuttavia, desiderata usare tunneling forzata per evitare questo problema. Quando si abilita tunneling forzata, tutte le connessioni a Internet risulteranno tramite il gateway locale. È possibile configurare tunneling forzata sfruttando route definiti dall'utente.

Ulteriori informazioni:

- [Cosa sono route definiti dall'utente e indirizzi IP inoltro](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Apparecchiature di sicurezza di rete virtuale
Sebbene i gruppi di sicurezza di rete, route definiti dall'utente e tunneling forzata fornire un livello di sicurezza a livello di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), a volte può essere quando si desidera attivare la sicurezza a livello superiore rispetto alla rete.

Ad esempio, ai propri requisiti di sicurezza possono includere:

- Autenticazione e l'autorizzazione prima di consentire l'accesso all'applicazione
- Intrusione e risposta intrusione
- Ispezione del livello di applicazione per i protocolli di alto livello
- Filtro degli URL
- Antimalware e antivirus livello di rete
- Protezione contro bot
- Controllo di accesso dell'applicazione
- Protezione DDoS aggiuntiva (in precedenza DDoS protezione fornito infrastruttura di Azure stesso)

È possibile accedere a queste funzionalità di protezione di rete avanzata utilizzando una soluzione di Azure partner. È possibile trovare il partner Azure più recente soluzioni di protezione di rete, esplorazione di [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "protezione di rete".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Accesso remoto protetto e connettività Cross locale
Il programma di installazione, configurazione e gestire le risorse Azure deve essere eseguita in modalità remota. Inoltre, si desidera distribuire soluzioni [IT ibridi](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) contenenti componenti locale e nel cloud pubblica Azure. Questi scenari richiedono l'accesso remoto protetto.

Azure networking supporta gli scenari di accesso remoto protetto seguenti:

- Connettere singole workstation a una rete virtuale Azure
- Connettere la rete locale a una rete virtuale Azure con una connessione VPN
- Connettere la rete locale a una rete virtuale Azure con un collegamento WAN dedicato
- Connettersi tra loro reti virtuali Azure

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Connettere singole workstation a una rete virtuale Azure
A volte può essere quando si desidera abilitare singoli personale gli sviluppatori o operazioni di gestione di macchine virtuali e i servizi di Azure. Ad esempio, è necessario accedere a una macchina virtuale in una rete virtuale Azure e criteri di sicurezza non consentono l'accesso remoto RDP o SSH per singole macchine virtuali. In questo caso, è possibile utilizzare una connessione VPN punto al sito.

La connessione VPN punto al sito utilizza il protocollo [VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) che consentono di configurare una connessione sicura e privata tra l'utente e la rete virtuale Azure. Dopo la connessione VPN, l'utente sarà possibile RDP o SSH attraverso il collegamento VPN a qualsiasi macchina virtuale sulla rete virtuale Azure (presupponendo che l'utente può eseguire l'autenticazione e autorizzato).

Ulteriori informazioni:

- [Configurare una connessione punto a sito a una rete virtuale tramite PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Connettere la rete locale a una rete virtuale Azure con una connessione VPN
Può essere necessario connettere l'intera rete aziendale o parte di esso, a una rete virtuale Azure. Si tratta di comune ibride IT scenari in cui società [estendere relativo Data Center locale in Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). In molti casi società verranno ospitare parti di un servizio di Azure e parti in locale, ad esempio quando una soluzione include server web front-end in Azure e database back-end locale. Questi tipi di connessioni "cross locale" anche sfruttare la gestione di Azure Trova risorse più sicura e scenari, ad esempio che si estende controller di dominio Active Directory Azure.

È possibile eseguire questa operazione consiste nell'utilizzare una [VPN da sito](https://www.techopedia.com/definition/30747/site-to-site-vpn). La differenza tra una connessione VPN da sito e una connessione VPN punto al sito è che una connessione VPN punto al sito connette un unico dispositivo a una rete virtuale di Azure, mentre una connessione VPN da sito connette un'intera rete (ad esempio la rete locale) a una rete virtuale Azure. VPN da sito a una rete virtuale Azure utilizzare la modalità di tunnel IPsec altamente protetta protocollo VPN.

Ulteriori informazioni:

- [Creare un Manager delle risorse VNet con una connessione VPN da sito tramite il portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Pianificazione e progettazione per gateway VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Connettere la rete locale a una rete virtuale Azure con un collegamento WAN dedicato
Connessioni VPN punto al sito e da sito inefficaci per abilitare la connettività tra locale. Tuttavia, in alcune organizzazioni è consigliabile per i seguenti svantaggi:

- Connessioni VPN spostare i dati su Internet: espone le connessioni a potenziali problemi di protezione con spostamento dei dati in una rete pubblica. Inoltre, l'affidabilità e la disponibilità per le connessioni Internet non è possibile garantire.
- Connessioni a reti virtuali Azure possono essere considerate vincolato della larghezza di banda per alcune applicazioni e scopi, man mano che si max in intorno a 200Mbps.

Organizzazioni che richiedono il massimo livello di sicurezza e la disponibilità per le connessioni tra locale in genere utilizzano collegamenti WAN dedicati per connettersi a siti remoti. Azure offre la possibilità di utilizzare un collegamento WAN dedicato che è possibile utilizzare per connettere la rete locale a una rete virtuale Azure. Questa opzione è attivata tramite Azure ExpressRoute.

Ulteriori informazioni:

- [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Connettersi tra loro reti virtuali Azure
È possibile usare molte reti virtuale Azure per le distribuzioni. Esistono diversi motivi per cui è possibile farlo. Uno dei motivi potrebbe essere per semplificare la gestione; un altro potrebbe essere per motivi di sicurezza. Indipendentemente dalla motivazione o spiegazione per l'immissione di risorse in diverse reti virtuali di Azure, a volte può essere utile risorse in ognuna delle reti di connettersi tra loro.

Un'opzione potrebbe essere per i servizi in una rete virtuale Azure per connettersi ai servizi in un'altra rete virtuale Azure scorrendo"Indietro" tramite Internet. La connessione iniziare in una rete virtuale di Azure, accedere tramite Internet e quindi tornare alla destinazione virtuali Azure. Questa opzione espone la connessione ai problemi di protezione inerente per le comunicazioni basate su Internet.

Potrebbe essere preferibile di creare un Azure virtuale virtuale rete-Azure la rete VPN da sito. Questo Azure virtuale virtuale rete-Azure la rete VPN da sito il protocollo stesso [modalità tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) come connessione VPN da sito tra locale indicata in precedenza.

Il vantaggio derivante dall'uso un Azure virtuale virtuale rete-Azure la rete VPN da sito è che la connessione VPN viene utilizzata su infrastruttura di rete Azure; non si connette tramite Internet. Questo fornisce un ulteriore livello di protezione rispetto a VPN da sito che si connettono tramite Internet.

Ulteriori informazioni:

- [Configurare una connessione VNet a VNet tramite Gestione risorse di Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilità
Disponibilità è un componente chiave di qualsiasi programma di protezione. Se gli utenti e sistemi non è possibile accedere a devono accedere tramite la rete, il servizio può essere considerato compromesso. Azure è tecnologie di rete che supportano i meccanismi di disponibilità seguenti:

- Bilanciamento del carico basato su HTTP
- Livello bilanciamento
- Bilanciamento del carico globale

Bilanciamento del carico è un meccanismo progettato per distribuire uniformi per le connessioni tra più dispositivi. Gli obiettivi di bilanciamento del carico sono:

- Aumentare la disponibilità: quando si caricano le connessioni di bilanciamento in più dispositivi, uno o più dei dispositivi può diventare non disponibile e servizi in esecuzione su dispositivi di online rimanenti è possono continuare a gestire il contenuto del servizio
- Migliorare le prestazioni, quando si caricano saldo connessioni tra più dispositivi, un singolo dispositivo non è necessario eseguire l'occorrenza processore. Se, tuttavia, le richieste di elaborazione e della memoria per la gestione del contenuto è distribuiti in più dispositivi.

### <a name="http-based-load-balancing"></a>Bilanciamento del carico basato su HTTP
Organizzazioni che eseguono spesso i servizi basati su web desiderano disporre di un basate su HTTP bilanciamento del carico davanti tali servizi web per garantire adeguati livelli di disponibilità e prestazioni elevate. A differenza di servizi di bilanciamento del carico basato su rete tradizionale decisioni basate su HTTP di bilanciamento del carico servizi di bilanciamento del carico seguono si basano sulle caratteristiche del protocollo HTTP, non su protocolli a livello di rete e di trasporto.

Per fornire il HTTP bilanciamento del carico per i servizi basati sul web, Azure fornisce al Gateway di applicazione Azure. Il Gateway di applicazione Azure supporta:

- Basata su HTTP il bilanciamento del carico-decisioni di bilanciamento del carico vengono create in base caratteristiche speciali per il protocollo HTTP
- Affinità sessione basata su cookie: questa funzionalità garantisce che connessioni a uno dei server supporto tale bilanciamento del carico rimane intatta tra il client e server. In questo modo si assicura stabilità delle transazioni.
- Offload SSL: quando un client connessione con bilanciamento del carico che sessione tra il client e il servizio di bilanciamento del carico è crittografata tramite l'HTTPS (SSL /) protocollo. Tuttavia, per migliorare le prestazioni, è possibile che la connessione tra il servizio di bilanciamento del carico e il server web dietro l'utilizzo di bilanciamento del carico il protocollo HTTP (non crittografato). Questo è definito "SSL trasferire" perché il server web supporto bilanciamento del carico non subiranno sovraccaricare processore con la crittografia e pertanto dovrebbe essere possibile richieste di assistenza più rapidamente.
- Basato su URL distribuzione del contenuto: questa caratteristica consente di bilanciamento del carico decidere dove inoltrare connessioni basate su URL di destinazione. Questa operazione molto più flessibile soluzioni che rendono caricare bilanciamento del carico decisioni in base a indirizzi IP.

Ulteriori informazioni:

- [Panoramica del Gateway di applicazioni](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Livello bilanciamento
A differenza di bilanciamento del carico basato su HTTP, livello bilanciamento rende decisioni di bilanciamento del carico in base a IP indirizzo e la porta (numeri TCP o UDP).
È possibile ottenere vantaggi di livello bilanciamento in Azure utilizzando il bilanciamento del carico di Azure. Alcune caratteristiche chiave di sistema di bilanciamento del carico Azure includono:

- Livello bilanciamento basato sui numeri di porta e l'indirizzo IP
- Supporto per il protocollo di qualsiasi livello di applicazione
- Caricare i saldi Azure macchine virtuali e cloud services istanze del ruolo
- Può essere usata per esposto a Internet (bilanciamento del carico esterno) e Internet non affiancate macchine virtuali e applicazioni (interno bilanciamento del carico)
- Endpoint monitoraggio, che consente di determinare se uno dei servizi di supporto bilanciamento del carico sono più disponibile

Ulteriori informazioni:

- [Internet affiancate di bilanciamento del carico tra più macchine virtuali o servizi](../load-balancer/load-balancer-internet-overview.md)
- [Panoramica di bilanciamento carico interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Bilanciamento del carico globale
In alcune organizzazioni saranno necessario il massimo livello di disponibilità possibili. Per raggiungere l'obiettivo è possibile ospitare applicazioni nei data center distribuita a livello globale. Quando un'applicazione è ospitata in data center situati in tutto il mondo, è possibile per un'intera regione geopolitiche non sono più disponibili e comunque dispone dell'applicazione e in esecuzione.

Oltre ai vantaggi di disponibilità per ottenere l'hosting di applicazioni nei data center distribuita a livello globale, è anche possibile ottenere prestazioni. Per ottenere le prestazioni utilizzando un meccanismo che indirizza le richieste di assistenza al Data Center più vicino al dispositivo che effettua la richiesta.

Bilanciamento del carico globale può fornire entrambi i seguenti vantaggi. In Azure, è possibile ottenere vantaggi globale di bilanciamento del carico tramite Gestione il traffico di Azure.

Ulteriori informazioni:

- [Che cos'è il traffico Manager?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Registrazione
La registrazione a un livello di rete è una funzione chiave per qualsiasi scenario di sicurezza di rete. In Azure, è possibile registrare informazioni relative a gruppi di sicurezza di rete ottenere il livello di rete la registrazione di informazioni. Con la registrazione NSG, viene visualizzato le informazioni provenienti da:

- Log di controllo: i registri vengono utilizzati per visualizzare tutte le operazioni presentate le proprie sottoscrizioni Azure. Questi registri attivati per impostazione predefinita e possono essere utilizzati all'interno del portale Azure.
- I registri eventi: i registri vengono fornite informazioni sulle regole NSG sono state applicate.
- Contatore registri: i registri consentono di sapere quante volte ogni regola NSG è stato applicato per impedire o consentire il traffico.

È anche possibile utilizzare [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uno strumento di visualizzazione di dati potenti, per visualizzare e analizzare i registri.

Ulteriori informazioni:

- [Registro Analitica per i gruppi di sicurezza di rete (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Risoluzione dei nomi
Risoluzione dei nomi è una funzione critica per tutti i servizi ospitato in Azure. Da una prospettiva di sicurezza, può causare violazione della funzione di risoluzione dei nomi di reindirizzamento delle richieste dal siti a un sito. Risoluzione dei nomi sicura è un requisito per tutti i servizi cloud ospitato.

Esistono due tipi di risoluzione dei nomi da considerare:

- Risoluzione dei nomi interni: risoluzione dei nomi interni viene utilizzato da servizi le reti virtuali Azure, la rete locale o entrambe. Nomi utilizzati per la risoluzione dei nomi interni non sono accessibili via Internet. Per maggiore sicurezza, è importante che la combinazione di risoluzione nome interno non è accessibile agli utenti esterni.
- Risoluzione dei nomi esterni: risoluzione dei nomi esterni viene utilizzato da utenti e i dispositivi di fuori del locale e reti virtuali Azure. Questi sono i nomi che sono visibili a Internet e vengono utilizzati per indirizzare connessione per i servizi basati su cloud.

Risoluzione dei nomi interno, sono disponibili due opzioni:

- Un server Azure virtuale rete DNS: quando si crea una nuova rete virtuale Azure, viene creato un server DNS dell'utente. Il server DNS può consentire di risolvere i nomi dei computer che si trovano in rete virtuale Azure. Il server DNS non che è possibile e gestito dal manager tessuti Azure, consentendo con una soluzione di risoluzione nome sicuro.
- Porta server DNS: disponibile l'opzione di inserimento di un server DNS di propria scelta nella rete virtuale Azure. Il server DNS potrebbe essere che un server Active Directory integrata DNS server o una soluzione server DNS fornito da un partner Azure, è possibile ottenere da Azure Marketplace.

Ulteriori informazioni:

- [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md)
- [Gestire i server DNS utilizzato da una rete virtuale (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

Per la risoluzione DNS esterna, sono disponibili due opzioni:

- Ospitare il proprio esterni DNS server in locale
- Ospitare server DNS esterno con un provider di servizi

Molte organizzazioni di grandi dimensioni conterrà le proprie DNS server locale. È possibile eseguire questa perché hanno l'esperienza social network e presenza globale per farlo.

Nella maggior parte dei casi è preferibile ospitare i servizi di risoluzione DNS presso un provider di servizi. I provider di servizi hanno l'esperienza di rete e presenza globale per assicurare la disponibilità molto elevata per i servizi di risoluzione dei nomi. Disponibilità è fondamentale per provider di servizi DNS perché se i servizi di risoluzione non riescono, nessuno sarà in grado di raggiungere i servizi è connessa a Internet.

Azure offre una disponibilità e soluzione DNS esterno efficace nel modulo di Azure DNS. Questa soluzione di risoluzione dei nomi esterni consente di sfruttare l'infrastruttura di Azure DNS in tutto il mondo. È possibile ospitare il dominio in Azure utilizzando gli stessi strumenti credenziali, API e la fatturazione come degli altri servizi Azure. Come parte di Azure, eredita anche i controlli di protezione avanzata incorporati nella piattaforma.

Ulteriori informazioni:

- [Panoramica del DNS Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Architettura di rete Perimetrale
Molte organizzazioni enterprise usare demilitarizzate per segmento reti per creare un'area di buffer tra Internet e i servizi. La parte di rete Perimetrale della rete viene considerata un'area di protezione bassa e nessun bene alto valore vengono inserito in quel segmento di rete. Si noterà che in genere dispositivi di sicurezza di rete con un'interfaccia di rete sul segmento di rete Perimetrale e un'altra interfaccia di rete connessi a una rete con macchine virtuali e servizi che accettano le connessioni in ingresso da Internet.

Sono disponibili numerose varianti di progettazione di rete Perimetrale e la decisione di distribuire una rete Perimetrale, e quindi il tipo di rete Perimetrale da utilizzare se si decide di usare uno, è in base ai requisiti di sicurezza della rete.

Ulteriori informazioni:

- [Servizi Cloud Microsoft e la sicurezza della rete](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Centro protezione di Azure
Nel Centro sicurezza consente di impedire, rilevare e rispondere a rischi e offre che maggiore visibilità e controllare i la protezione delle risorse di Azure. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

Centro protezione di Azure consente di ottimizzare e monitorare la sicurezza di rete per:

- Fornire suggerimenti per la sicurezza di rete
- Monitorare lo stato della configurazione di sicurezza di rete
- Avvisa rete in base a minacce entrambi i livelli di endpoint e rete

Ulteriori informazioni:

- [Introduzione al Centro protezione di Azure](../security-center/security-center-intro.md)
