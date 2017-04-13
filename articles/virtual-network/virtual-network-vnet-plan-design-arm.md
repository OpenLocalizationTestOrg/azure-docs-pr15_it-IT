<properties
   pageTitle="Piano di Azure virtuali (VNet) e Guida alla progettazione | Microsoft Azure"
   description="Informazioni su come pianificare e progettare reti virtuali in Azure in base ai propri requisiti di isolamento, connettività e posizione."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Pianificare e progettare reti virtuali Azure

Creazione di un VNet per sperimentare è difficile, ma è molto probabile che verrà distribuito VNets più tempo per supportare le esigenze di produzione dell'organizzazione. Con alcuni pianificazione e progettazione, sarà possibile distribuire VNets e connettere le risorse che necessarie in modo più efficiente. Se non ha familiarità con VNets, è consigliabile che si [informazioni VNets](virtual-networks-overview.md) e su [come distribuire](virtual-networks-create-vnet-arm-pportal.md) uno prima di continuare. 

## <a name="plan"></a>Piano

Approfondita delle sottoscrizioni di Azure, aree geografiche e risorse di rete è fondamentale per il successo. È possibile utilizzare l'elenco delle considerazioni sotto come punto di partenza. Dopo aver compreso tali considerazioni, è possibile definire i requisiti per la progettazione di rete.

### <a name="considerations"></a>Considerazioni

Prima di ricezione di pianificazione domande sotto, tenere presente quanto segue:

- Tutti gli elementi creati in Azure è composto da uno o più risorse. Una macchine () è una risorsa, la scheda di rete (NIC) utilizzata da una macchina virtuale è una risorsa, l'indirizzo IP pubblico utilizzato da una scheda di rete è una risorsa è VNet la scheda di rete sia connesso a una risorsa.
- Si creano risorse all'interno di [un'area geografica Azure](https://azure.microsoft.com/regions/#services) e sottoscrizione. E le risorse possono essere connesse solo a un VNet che è presente nella stessa area geografica e siano nell'abbonamento. 
- È possibile connettersi VNets tra loro utilizzando un [Gateway VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)Azure. È anche possibile connettersi VNets in più aree geografiche e sottoscrizioni in questo modo.
- È possibile connettersi VNets alla rete locale mediante una delle [Opzioni di connettività](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibili in Azure. 
- Diverse risorse possono essere raggruppate in [gruppi di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups), rendendo più semplice gestire la risorsa in un'unità. Un gruppo di risorse può contenere risorse da più aree, purché appartengono le risorse per lo stesso abbonamento.

### <a name="define-requirements"></a>Definizione dei requisiti

Utilizzare le domande sotto come punto di partenza per la progettazione di rete Azure.  

1. Le posizioni Azure verrà utilizzata all'host VNets?
2. È necessario fornire le comunicazioni tra questi percorsi Azure?
3. È necessario fornire le comunicazioni tra i VNet(s) Azure e il datacenter(s) locale?
4. Quanti come macchine virtuali di un servizio (IaaS), cloud servizi di infrastruttura ruoli e web App non che è necessario per la soluzione?
5. È necessario isolare il traffico in base ai gruppi di macchine virtuali (server web ovvero front-end e back-end server di database)?
6. È necessario controllare il flusso di traffico tramite dispositivi di rete?
7. Gli utenti hanno bisogno diversi set di autorizzazioni per le risorse di Azure diverse?

### <a name="understand-vnet-and-subnet-properties"></a>Comprendere le proprietà VNet e subnet

Risorse VNet e subnet consentono di definire un limite di protezione per carichi di lavoro in esecuzione in Azure. Un VNet è caratterizzato da una raccolta di spazi indirizzo, definito come blocchi CIDR. 

>[AZURE.NOTE] Gli amministratori di rete hanno familiari con la notazione CIDR. Se non conoscono CIDR, [informazioni su ulteriori informazioni](http://whatismyipaddress.com/cidr).

VNets contengono le proprietà seguenti.

|Proprietà|Descrizione|Vincoli|
|---|---|---|
|**nome**|Nome VNet|Stringa di caratteri fino a 80. Può contenere lettere, numeri, sottolineatura, punti o trattini. Deve iniziare con una lettera o un numero. Deve terminare con una lettera, un numero o un carattere di sottolineatura. Può contenere superiore o lettere minuscole.|  
|**posizione**|Posizione Azure (denominato anche l'area geografica).|Deve essere uno dei percorsi di Azure validi.|
|**addressSpace**|Insieme di prefissi di indirizzi che compongono la VNet in notazione CIDR.|Deve essere una matrice di blocchi di indirizzi CIDR validi, inclusi gli intervalli di indirizzi IP pubblici.|
|**subnet**|Insieme di subnet che costituiscono il VNet|vedere la tabella di proprietà subnet riportata di seguito.||
|**dhcpOptions**|Oggetto contenente una singola proprietà obbligatoria denominato **dnsServers**.||
|**dnsServers**|Matrice di server DNS utilizzati per la VNet. Se non viene specificato alcun server, viene utilizzata la risoluzione dei nomi interno Azure.|Deve essere una matrice di fino a 10 server DNS, in base all'indirizzo IP.| 

Una subnet è una risorsa figlio di un VNet e consente di definiscono segmenti di spazi di indirizzi in un blocco CIDR utilizzando prefissi degli indirizzi IP. NIC possono essere aggiunto alla subnet e connessi a macchine virtuali, fornire la connettività per diversi carichi di lavoro.

Subnet contengono le proprietà seguenti. 

|Proprietà|Descrizione|Vincoli|
|---|---|---|
|**nome**|Nome subnet|Stringa di caratteri fino a 80. Può contenere lettere, numeri, sottolineatura, punti o trattini. Deve iniziare con una lettera o un numero. Deve terminare con una lettera, un numero o un carattere di sottolineatura. Può contenere superiore o lettere minuscole.|
|**posizione**|Posizione Azure (denominato anche l'area geografica).|Deve essere uno dei percorsi di Azure validi.|
|**addressPrefix**|Prefisso singolo indirizzo che costituiscono la subnet in notazione CIDR|Deve essere un singolo blocco CIDR che fa parte di uno degli spazi di indirizzi della VNet.|
|**networkSecurityGroup**|NSG applicati alla subnet|vedere [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Tabella di routing applicata alla subnet|vedere [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Insieme di oggetti di configurazione IP usati da NIC connessa alla subnet|vedere [configurazione di indirizzi IP](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Risoluzione dei nomi

Per impostazione predefinita, viene utilizzata la VNet [la risoluzione dei nomi forniti Azure.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) Per risolvere i nomi all'interno di VNet e su Internet. Tuttavia, se ci si connette il VNets ai data center locale, è necessario fornire [server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) per la risoluzione dei nomi tra le reti.  

### <a name="limits"></a>Limiti

Esaminare i limiti nell'articolo [Azure limiti](../azure-subscription-service-limits.md#networking-limits) per assicurarsi che la struttura non sono in conflitto con ai limiti di rete. È possibile aumentare alcuni limiti aprendo un ticket di supporto.

### <a name="role-based-access-control-rbac"></a>Controllo dell'accesso basato sui ruoli (RBAC)

È possibile utilizzare [RBAC Azure](../active-directory/role-based-access-built-in-roles.md) per controllare il livello di accesso utenti diversi potrebbero essere necessario diverse risorse in Azure. In questo modo è possibile isolare il lavoro svolto dal team in base alle proprie esigenze. 

Per quanto riguarda le reti virtuali, gli utenti il ruolo di **Collaboratore rete** hanno controllo completo sulle risorse virtuali Manager delle risorse di Azure. Allo stesso modo, gli utenti il ruolo di **Collaboratore rete classica** hanno controllo completo sulle risorse virtuali classica.

>[AZURE.NOTE] È anche possibile [creare ruoli](../active-directory/role-based-access-control-configure.md) per separare le proprie esigenze di amministrazione.

## <a name="design"></a>Struttura

Dopo aver appreso le risposte alle domande della sezione di [pianificare](#Plan) , rivedere le operazioni seguenti prima di definire il VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Numero di sottoscrizioni e VNets

È consigliabile creare più VNets negli scenari seguenti:

- **Macchine virtuali che devono essere inseriti in un'altra posizione Azure**. VNets in Azure sono internazionali. Non corrispondano posizioni. Pertanto è necessario VNet almeno una per ogni posizione di Azure che si desidera macchine virtuali di host in.
- **Carichi di lavoro che devono essere completamente isolato rispetto a altro**. È possibile creare VNets separato che utilizzano anche gli spazi degli indirizzi IP stesso per isolare carichi di lavoro diversi rispetto a altro. 

Tenere presente che i limiti che viene visualizzato sopra sono regione, per abbonamento. Ciò significa che è possibile utilizzare più abbonamenti per aumentare il limite delle risorse che è possibile gestire in Azure. È possibile utilizzare una connessione VPN da sito o un circuito ExpressRoute per connettersi VNets in diversi abbonamenti.

### <a name="subscription-and-vnet-design-patterns"></a>Abbonamento e modelli di progettazione VNet

La tabella seguente illustra alcuni modelli di progettazione comuni per l'uso di sottoscrizioni e VNets.

|Scenario:|Diagramma|Professionisti IT|Contro|
|---|---|---|---|
|Abbonamento singolo, due VNets per app|![Abbonamento singola](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Un solo abbonamento a gestire.|Numero massimo di VNets regione Azure. Dopo che è necessario più sottoscrizioni. Vedere l'articolo [Azure limiti](../azure-subscription-service-limits.md#networking-limits) per informazioni dettagliate.|
|Con una sottoscrizione per app, due VNets per app|![Abbonamento singola](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Utilizza solo due VNets per abbonamento.|Più difficile da gestire quando sono presenti troppi app.|
|Una sottoscrizione per ogni business unit due VNets per app.|![Abbonamento singola](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Saldo totale tra numero degli abbonamenti e VNets.|Numero massimo di VNets per ogni business unit (abbonamento). Vedere l'articolo [Azure limiti](../azure-subscription-service-limits.md#networking-limits) per informazioni dettagliate.|
|Una sottoscrizione per ogni business unit due VNets per ogni gruppo di App.|![Abbonamento singola](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Saldo totale tra numero degli abbonamenti e VNets.|App devono essere isolate utilizzando subnet e NSGs.|


### <a name="number-of-subnets"></a>Numero di subnet

È necessario considerare più subnet in VNet negli scenari seguenti:

- **Non è sufficiente indirizzi IP privati per tutte le NIC una subnet**. Se lo spazio degli indirizzi di subnet non contiene indirizzi IP sufficiente per il numero di schede di rete nella subnet, è necessario creare più subnet. Tenere presente che Azure riserva 5 indirizzi IP privati da ogni subnet che non possono essere utilizzati: gli indirizzi nome e il cognome dello spazio di indirizzi (per l'indirizzo subnet e multicast) e 3 indirizzi da utilizzare internamente (ai fini della DHCP e DNS). 
- **Sicurezza**. È possibile utilizzare subnet per separare i gruppi di macchine virtuali rispetto a altro per carichi di lavoro che hanno una struttura più livelli e applicare diversi [gruppi di sicurezza di rete (NSGs)](virtual-networks-nsg.md#subnets) per subnet.
- **Connettività ibrida**. È possibile usare gateway VPN e circuiti ExpressRoute per [connettere](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) il VNets tra loro e center(s) i dati in locale. Gateway VPN e circuiti ExpressRoute richiedono una subnet della propria da creare.
- **Accessori virtuale**. È possibile utilizzare un dispositivo virtuale, ad esempio un firewall, acceleratori WAN o gateway VPN in un VNet Azure. Quando si esegue questa operazione, è necessario [instradare il traffico](virtual-networks-udr-overview.md) a tali elettrodomestici, isolarli nella propria subnet.

### <a name="subnet-and-nsg-design-patterns"></a>Modelli di progettazione NSG e subnet

La tabella seguente illustra alcuni modelli di progettazione comuni per l'uso di subnet.

|Scenario:|Diagramma|Professionisti IT|Contro|
|---|---|---|---|
|Singola subnet, NSGs per ogni livello di applicazione, per app|![Subnet singola](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Sola subnet per gestire.|NSGs più necessario isolare le applicazioni.|
|Una subnet per app, NSGs per ogni livello di applicazione|![Subnet per app](./media/virtual-network-vnet-plan-design-arm/figure6.png)|Meno NSGs per gestire.|Più subnet per gestire.|
|Una subnet per ogni livello di applicazione, NSGs per app.|![Subnet per livello](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Saldo totale tra numero di subnet e NSGs.|Numero massimo di NSGs per abbonamento. Vedere l'articolo [Azure limiti](../azure-subscription-service-limits.md#networking-limits) per informazioni dettagliate.|
|Una subnet per ogni livello di applicazione, per app, NSGs per ogni subnet|![Subnet per ogni layer per app](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Eventualmente numero minore di NSGs.|Più subnet per gestire.|

## <a name="sample-design"></a>Struttura di esempio

Per illustrare l'applicazione delle informazioni in questo articolo, è consigliabile lo scenario seguente.

Si lavora per individuare una società che dispone di 2 data center in Nord America e due centri dati Europa. Che sono stati identificati 6 cliente diverso esposto le applicazioni gestite da 2 diverse business unit che si desidera eseguire la migrazione a Azure come un progetto pilota. L'architettura di base per le applicazioni sono i seguenti:

- App1, App2, App3 e App4 sono applicazioni web ospitate nel server Linux che eseguono Ubuntu. Ogni applicazione si connette a un server delle applicazioni separato che ospita REST pagina servizi nel server Linux. Servizi REST connettersi a un database MySQL back-end.
- App5 e App6 sono ospitate su server Windows in esecuzione Windows Server 2012 R2 le applicazioni web. Ogni applicazione si connette a un database di SQL Server di back-end.
- Tutte le app sono attualmente ospitate in uno dei centri di dati dell'azienda in Nord America.
- Data center locale utilizzare lo spazio di indirizzi 10.0.0.0/8.

È necessario progettare una soluzione di rete virtuale che soddisfi i requisiti seguenti:

- Ogni business unit deve non influenzato dall'utilizzo delle risorse delle altre divisioni.
- È necessario ridurre la quantità di VNets e subnet per semplificare la gestione.
- Ogni business unit deve avere un singolo test/sviluppo VNet utilizzato per tutte le applicazioni.
- Ogni applicazione è ospitata in 2 centri di dati di Azure diversi per continente (Nord America ed Europe).
- Ogni applicazione è completamente isolate tra loro.
- Ogni applicazione è accessibile dai clienti tramite Internet con HTTP.
- Ogni applicazione è accessibile per gli utenti connessi al data center locale utilizzando un tunnel crittografato.
- Connessione al data center locale deve essere utilizzata dispositivi VPN esistenti.
- Gruppo di rete della società deve avere controllo completo sulla configurazione VNet.
- Gli sviluppatori in ogni business unit solo dovrebbero essere possibile distribuirle in subnet esistente.
- Tutte le applicazioni verranno migrate così come sono in Azure (ascensore e MAIUSC).
- I database in ogni posizione necessario replicare in altre posizioni Azure una volta al giorno.
- Ogni applicazione deve utilizzare 5 server front-end, 2 server applicazione (se necessario) e 2 server di database.

### <a name="plan"></a>Piano

È consigliabile iniziare la progettazione di pianificazione per rispondere alla domanda nella sezione [Definisci requisiti](#Define-requirements) , come illustrato di seguito.

1. Le posizioni Azure verrà utilizzata all'host VNets?

    2 posizioni nel Nord America e 2 posizioni in Europa. Selezionare quelle in base all'ubicazione fisica del data center locale esistente. In questo modo la connessione dalle posizioni fisiche Azure avrà una latenza migliore.

2. È necessario fornire le comunicazioni tra questi percorsi Azure?

    Sì. Poiché i database devono essere replicati in tutti i percorsi.

3. È necessario fornire le comunicazioni tra i VNet(s) Azure e le locale center(s) dati?

    Sì. Dal momento che gli utenti connessi al data center locale devono avere la possibilità di accedere alle applicazioni tramite un tunnel crittografato.
 
4. Quanti macchine virtuali IaaS è necessario per la soluzione?

    200 macchine virtuali IaaS. App1 e App2 App3 richiedono 5 server web ogni, 2 applicazioni server ogni e 2 server di database. Questo è un totale di macchine virtuali IaaS 9 per ogni applicazione o 36 macchine virtuali IaaS. App5 e App6 richiedono server web 5 e 2 server di database. Questo è un totale di macchine virtuali IaaS 7 per ogni applicazione o 14 macchine virtuali IaaS. Di conseguenza, è necessario 50 macchine virtuali IaaS per tutte le applicazioni in ogni area Azure. Dal momento che è necessario utilizzare le aree di 4, si verificherà 200 macchine virtuali IaaS.

    Sarà anche necessario fornire i server DNS in ogni VNet o in data center locale per risolvere il nome tra le macchine virtuali IaaS Azure e la rete locale. 

5. È necessario isolare il traffico in base ai gruppi di macchine virtuali (server web ovvero front-end e back-end server di database)?

    Sì. Ogni applicazione deve essere completamente isolati tra loro e ogni livello di applicazione dovrebbe essere isolato. 

6. È necessario controllare il flusso di traffico tramite dispositivi di rete?

    No. Dispositivi di rete può essere utilizzati per fornire un maggiore controllo del flusso del traffico, tra cui la registrazione di piano più dettagliata dei dati. 

7. Gli utenti hanno bisogno diversi set di autorizzazioni per le risorse di Azure diverse?

    Sì. Il team di rete deve controllo completo sulle impostazioni di rete virtuale, mentre gli sviluppatori solo dovrebbero essere possibile distribuirle loro subnet già esistente. 

### <a name="design"></a>Struttura

È necessario seguire la struttura che specifica le sottoscrizioni, VNets, subnet e NSGs. NSGs verranno illustrate di seguito, ma è necessario acquisire familiarità con [NSGs](virtual-networks-nsg.md) prima di completare la struttura.

**Numero di sottoscrizioni e VNets**

I requisiti seguenti relativi a sottoscrizioni e VNets:

- Ogni business unit deve non influenzato dall'utilizzo delle risorse delle altre divisioni.
- È necessario ridurre la quantità di VNets e subnet.
- Ogni business unit deve avere un singolo test/sviluppo VNet utilizzato per tutte le applicazioni.
- Ogni applicazione è ospitata in 2 centri di dati di Azure diversi per continente (Nord America ed Europe).

In base a tali requisiti, è necessario un abbonamento per ogni business unit. In questo modo, consumo di risorse da una business unit verrà non considerato ai fini limiti per altre divisioni. E dal momento che si desidera ridurre il numero di VNets, è consigliabile utilizzare il modello di **una sottoscrizione per ogni business unit due VNets per ogni gruppo di applicazioni** , come indicato di seguito.

![Abbonamento singola](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Inoltre, è necessario specificare lo spazio di indirizzi per ogni VNet. Dal momento che è necessario connettività tra i dati locali centri aree Azure lo spazio di indirizzi usato per Azure VNets non essere in conflitto con la rete locale e lo spazio di indirizzi usato da ogni VNet non deve essere in conflitto con altre VNets esistente. È possibile utilizzare gli spazi indirizzo nella tabella seguente per tali requisiti.  

|**Abbonamento**|**VNet**|**Area Azure**|**Spazio di indirizzi**|
|---|---|---|---|
|REP1|ProdBU1US1|Usa ovest|172.16.0.0/16|
|REP1|ProdBU1US2|Stati Uniti orientali|172.17.0.0/16|
|REP1|ProdBU1EU1|Europa Nord America|172.18.0.0/16|
|REP1|ProdBU1EU2|Europa ovest|172.19.0.0/16|
|REP1|TestDevBU1|Usa ovest|172.20.0.0/16|
|REP2|TestDevBU2|Usa ovest|172.21.0.0/16|
|REP2|ProdBU2US1|Usa ovest|172.22.0.0/16|
|REP2|ProdBU2US2|Stati Uniti orientali|172.23.0.0/16|
|REP2|ProdBU2EU1|Europa Nord America|172.24.0.0/16|
|REP2|ProdBU2EU2|Europa ovest|172.25.0.0/16|

**Numero di subnet e NSGs**

I requisiti seguenti relativi a subnet e NSGs:

- È necessario ridurre la quantità di VNets e subnet.
- Ogni applicazione è completamente isolate tra loro.
- Ogni applicazione è accessibile dai clienti tramite Internet con HTTP.
- Ogni applicazione è accessibile per gli utenti connessi al data center locale utilizzando un tunnel crittografato.
- Connessione al data center locale deve essere utilizzata dispositivi VPN esistenti.
- I database in ogni posizione necessario replicare in altre posizioni Azure una volta al giorno.

In base a tali requisiti, è possibile utilizzare una subnet per ogni livello di applicazione e utilizzare NSGs per il traffico di filtro per ogni applicazione. In questo modo, è sufficiente 3 subnet in ogni VNet (front-end, i livelli applicazioni e livello di dati) e uno NSG per ogni applicazione per ogni subnet. In questo caso, è necessario prendere in considerazione utilizzando il modello di progettazione di **una subnet per ogni livello di applicazione, NSGs per app** . Nella figura seguente viene illustrato come utilizzare il modello di progettazione che rappresenta la VNet **ProdBU1US1** .

![Una subnet per ogni livello, uno NSG per ogni applicazione per livello](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Tuttavia, è necessario creare una subnet aggiuntive per la connessione VPN tra il VNets e i data center locale. Ed è necessario specificare lo spazio di indirizzi per ciascuna subnet. La figura seguente mostra una soluzione di esempio per **ProdBU1US1** VNet. È necessario replicare questo scenario per ogni VNet. Ogni colore rappresenta un'altra applicazione.

![Esempio VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Controllo dell'accesso**

I requisiti seguenti sono relativi al controllo dell'accesso:

- Gruppo di rete della società deve avere controllo completo sulla configurazione VNet.
- Gli sviluppatori in ogni business unit solo dovrebbero essere possibile distribuirle in subnet esistente.

In base a tali requisiti, è possibile aggiungere gli utenti dal team di rete per il ruolo di **Collaboratore rete** incorporato in ciascuna sottoscrizione; creare un ruolo personalizzato per gli sviluppatori di applicazioni di ciascuna sottoscrizione assegnando le autorizzazioni per aggiungere macchine virtuali alla subnet esistente.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire una rete virtuale](virtual-networks-create-vnet-arm-template-click.md) in uno scenario di base.
- Capire come [il bilanciamento del carico](../load-balancer/load-balancer-overview.md) macchine virtuali IaaS e [gestire il routing tramite più aree di Azure](../traffic-manager/traffic-manager-overview.md).
- Ulteriori informazioni su [NSGs e su come pianificare e progettare](virtual-networks-nsg.md) una soluzione NSG.
- Altre informazioni relative agli [tra locale e opzioni di connettività VNet](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
