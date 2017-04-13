<properties
   pageTitle="Protezione di rete e servizi cloud Microsoft | Microsoft Azure"
   description="Informazioni su alcune delle funzionalità chiave disponibili in Azure consentono di creare ambienti di rete protetti"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Protezione di rete e servizi cloud Microsoft

Servizi cloud Microsoft offrono servizi superscalari e infrastruttura, funzionalità aziendale e numerose opzioni per la connettività ibrida. I clienti possono scegliere accedere ai servizi tramite Internet o con Azure ExpressRoute, che offre la connettività di rete privata. La piattaforma Microsoft Azure consente ai clienti di facile estendere la propria infrastruttura nel cloud e creare architetture a più livelli. Terze parti è inoltre possibile abilitare la funzionalità avanzate all'offerta di servizi di sicurezza e dispositivi di rete. In questo articolo viene fornita una panoramica dei problemi di protezione e all'architettura che è consigliabile quando si utilizza servizi cloud Microsoft ExpressRoute, accessibili. Inoltre, è descritta la creazione di servizi più sicuri in Azure reti virtuali.

## <a name="fast-start"></a>Avvio veloce
Il grafico di logica seguenti possa comunicare di un esempio specifico di molte tecniche di sicurezza disponibili con la piattaforma Azure. Per farvi riferimento, individuare l'esempio più adatto alle maiuscole/minuscole. Per una descrizione più completare, continuare a leggere attraverso la carta.
![Diagramma di flusso opzioni di sicurezza][0]

[Esempio 1: Creare una rete perimetrale (noto anche come zona e subnet schermata) per proteggere le applicazioni di rete i gruppi di sicurezza (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, route definite dall'utente (UDR) e NSG.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Esempio 4: Aggiungere una connessione ibrido con un accessorio da sito, virtual private virtuali (VPN).](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Esempio 5: Aggiungere una connessione ibrido con un gateway a siti, Azure VPN.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Esempio 6: Aggiungere una connessione ibrido con ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Esempi per l'aggiunta di collegamenti tra reti virtuali, disponibilità e servizio concatenazione verranno aggiunto al documento nei mesi successivi.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Protezione dell'infrastruttura e la conformità di Microsoft
Microsoft ha intrapreso una posizione di leadership iniziative di conformità richieste per clienti aziendali di supporto. Di seguito sono riportati alcuni i certificati di conformità per Azure: ![badge conformità Azure][1]

Per ulteriori informazioni, vedere informazioni sulla conformità nel [Centro protezione di Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft ha un approccio completo per la protezione dell'infrastruttura cloud necessario per eseguire superscalari servizi globali. Infrastruttura cloud Microsoft include hardware, software, reti e amministrativi e operatori, oltre al Data Center fisici.

![Funzionalità di sicurezza di Azure][2]

Questo approccio fornisce una base più sicura per i clienti distribuire i servizi cloud Microsoft. Il passaggio successivo consiste per i clienti progettare e creare un'architettura di sicurezza per proteggere i servizi.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Protezione tradizionale architetture e reti perimetrali
Sebbene Microsoft investimento molto protezione dell'infrastruttura cloud, clienti necessario proteggere anche i servizi cloud e gruppi di risorse. Un approccio più livelli di sicurezza offre la miglior difesa. Un'area di protezione di rete perimetrale protegge le risorse di rete interna da una rete non attendibile. Una rete perimetrale si intende i bordi o le parti della rete che si trovano tra Internet e di infrastruttura IT aziendale protetta.

Nelle reti aziendali tipico, l'infrastruttura di base in modo rilevante viene aggiunto al perimetro, a più livelli dei dispositivi di sicurezza. Il limite di ogni livello è costituito da dispositivi e punti di imposizione di criteri. Dispositivi possono includere le operazioni seguenti: firewall, prevenzione distribuito di DOS (DDoS), intrusione o sistemi di protezione (ID/IP) e dispositivi VPN. L'applicazione dei criteri può consistere in Criteri firewall, elenchi di controllo di accesso (ACL) o ciclo specifico. La prima riga di difesa della rete direttamente accettare il traffico in ingresso da Internet, è una combinazione di questi meccanismi di blocco degli attacchi e il traffico dannoso consentendo legittime richieste ulteriormente la rete aziendale. Indirizza il traffico direttamente alle risorse nella rete perimetrale. La risorsa può quindi "parlare" alle risorse più in basso nella rete, in transito successivo limite per la convalida prima. Il livello più esterno è denominato rete perimetrale, perché questa parte della rete è esposto a Internet, in genere con una forma di protezione su entrambi i lati. La figura seguente mostra un esempio di una rete perimetrale singola subnet in una rete aziendale, con due limiti di sicurezza.

![Una rete perimetrale in una rete aziendale][3]

Sono disponibili molti architetture utilizzate per implementare una rete perimetrale, da un bilanciamento del carico semplice che precede una farm web a una rete perimetrale subnet più con meccanismi di variabili in ciascuno dei limiti per bloccare il traffico e proteggere i livelli più approfonditi della rete aziendale. La modalità di compilazione di rete perimetrale dipende dalle esigenze specifiche dell'organizzazione e il relativo tolleranza rischio globale.

Come clienti spostano i carichi di lavoro al cloud pubblici, è fondamentale per supportare funzionalità simili per architettura di rete perimetrale in Azure per soddisfare i requisiti di sicurezza e conformità. In questo documento vengono fornite istruzioni su come gli utenti possono creare un ambiente di rete protetta in Azure. È incentrata su rete perimetrale, ma include anche informazioni complete di molti aspetti della protezione di rete. Gli aspetti seguenti determinano la discussione:

- Come è possibile creare una rete perimetrale in Azure?
- Quali sono alcune delle caratteristiche Azure disponibile per la compilazione di rete perimetrale?
- Come è possibile proteggere carichi di lavoro di back-end?
- Come vengono controllate le comunicazioni Internet a carichi di lavoro di Azure
- Come è possono proteggere le reti locale da distribuzioni di Azure?
- Quando deve essere utilizzata funzionalità di sicurezza di Azure nativi e dispositivi di terze parti o servizi?

Nel diagramma seguente mostra diversi livelli di sicurezza che Azure offre ai clienti. I livelli sono nativi della piattaforma Azure stesso e caratteristiche definiti dall'utente:

![Architettura di sicurezza di Azure][4]

In ingresso da Internet, DDoS Azure consente la protezione da attacchi su larga scala Azure. Il livello successivo è definito dall'utente endpoint pubbliche, cui vengono utilizzati per determinare il tipo di traffico possono essere inoltrate servizio cloud per la rete virtuale. Azure nativo virtual isolamento della rete consente di isolamento completo da tutte le altre reti e che il traffico solo attraversa metodi e percorsi utente configurato. Questi percorsi e i metodi sono il livello successivo, in cui NSGs, UDR e rete dispositivi di rete può essere utilizzati per creare limiti di protezione per proteggere le distribuzioni di applicazioni di rete protetta.

La sezione successiva viene fornita una panoramica delle reti virtuali Azure. Queste reti virtuali create dai clienti e sono quali i carichi di lavoro distribuiti si è connessi. Reti virtuali rappresentano la base di tutte le funzionalità di sicurezza rete necessaria per stabilire una rete perimetrale per proteggere distribuzioni in Azure.

## <a name="overview-of-azure-virtual-networks"></a>Panoramica delle reti virtuali Azure
Prima che il traffico Internet accessibile per le reti virtuali Azure, esistono due livelli di sicurezza inerente alla piattaforma Azure:

1.  **Protezione DDoS**: protezione DDoS è un livello della rete fisica Azure che impedisce la piattaforma Azure stesso su larga scala attacchi via Internet. Questi attacchi consente di più nodi "bot" nel tentativo di sovraccaricare un servizio Internet. Azure ha una protezione DDoS ed efficace trama in tutta la connettività Internet in ingresso. Questo livello di protezione DDoS non dispone di utente configurabili attributi e non è possibile accedere al cliente. Consente di proteggere Azure come piattaforma da attacchi di grandi dimensioni, ma non protegge direttamente applicazione singolo cliente. Ulteriori livelli di adattabilità possono essere configurati dal cliente da attacchi localizzati. Ad esempio, se il cliente è stato un attacco con un attacco DDoS su larga scala su un endpoint pubblico, Azure blocca le connessioni al servizio. Il cliente potrebbe non riuscire a un'altra rete virtuale o servizio endpoint non correlate all'attacco per ripristinare il servizio. Si noti che anche se il cliente può essere influenzato relative a tale endpoint, non pertanto interessato nessun altro servizio di fuori dell'endpoint. Inoltre, altri servizi e clienti non vedrà alcun effetto da tale attacco.
2.  **Endpoint del servizio**: endpoint consentono cloud services o gruppi di risorse che pubblico indirizzi Internet IP e porte esposte. L'endpoint utilizzerà Network Address Translation (NAT) per indirizzare il traffico verso l'interno indirizzo e la porta della rete virtuale Azure. Si tratta del percorso principale per il traffico esterno passare alla rete virtuale. Gli endpoint del servizio sono passato configurabile dall'utente per determinare il tipo di traffico e come e dove viene convertita in rete virtuale.

Quando il traffico raggiunge la rete virtuale, sono disponibili numerose caratteristiche che entrano in gioco. Reti virtuali Azure sono alla base per i clienti allegare i carichi di lavoro e in cui si applica la protezione a livello di rete di base. È una rete privata (una sovrapposizione virtuali) in Azure per i clienti con le funzionalità seguenti caratteristiche:
 
- **Isolamento di traffico**: una rete virtuale è il limite di isolamento il traffico sulla piattaforma Azure. Macchine () in una rete virtuale non è possibile comunicare direttamente alle macchine virtuali in una rete virtuale diversa, anche se entrambe le reti virtuali vengono create allo stesso cliente. Si tratta di una proprietà critica che assicura macchine virtuali di cliente e comunicazione rimanga privata all'interno di una rete virtuale.
- **Topologia multilivello**: reti virtuali consentono agli utenti di definire multilivello topologia assegnando subnet e se si designa spazi di indirizzi separati per diversi elementi o "livelli" dei carichi di lavoro. Questi raggruppamenti logici topologie consentiranno ai clienti di definire i criteri di accesso diverso in base ai tipi di carico di lavoro e controllano flussi di traffico tra i livelli.
- **La connettività tra locale**: i clienti possono stabilire la connessione tra locale tra una rete virtuale e più siti locale o altre reti virtuali in Azure. A tale scopo, è possono utilizzare i clienti gateway VPN Azure o accessori virtuale di rete di terze parti. Azure supporta connessioni VPN (S2S) da sito utilizzando protocolli IPsec/IKE standard e connettività privata ExpressRoute.
- **NSG** gli utenti possono creare regole (ACL) a livello di dettaglio desiderato: interfacce, singole macchine virtuali o subnet virtuale di rete. Controllare l'accesso tramite consente o si nega comunicazioni tra i carichi di lavoro all'interno di una rete virtuale da sistemi su reti del cliente attraverso la connettività tra locale, clienti o indirizzare le comunicazioni Internet.
- **UDR** e **Inoltro IP** consentono agli utenti di definire i percorsi di comunicazione tra diversi livelli all'interno di una rete virtuale. I clienti possono distribuire un firewall, ID/IP e altri dispositivi di rete e instradare il traffico di rete attraverso le apparecchiature di sicurezza per l'applicazione dei criteri di sicurezza limite, il controllo e ispezione.
- **Rete dispositivi di rete** in Azure Marketplace: apparecchiature di sicurezza, ad esempio firewall, servizi di bilanciamento del carico e ID/IP sono disponibili nella raccolta di immagini macchine Virtuali e Azure Marketplace. È possibile installare questi accessori nelle proprie reti virtuale e in particolare, i limiti di protezione (comprese le subnet di rete perimetrale) per completare un ambiente di rete protetta più livelli.

Con queste caratteristiche e funzionalità, un esempio di come è possibile creare un'architettura di rete perimetrale in Azure è il seguente:

![Una rete perimetrale in una rete virtuale Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Requisiti e caratteristiche di rete perimetrale
Rete perimetrale è progettata per essere front-end della rete, l'interazione direttamente comunicazioni da Internet. I pacchetti in ingresso devono passano attraverso le apparecchiature di sicurezza, ad esempio il firewall, ID e indirizzi IP, prima di raggiungere il server di back-end. Pacchetti associati a Internet da carichi di lavoro possono inoltre passare tra le apparecchiature di sicurezza della rete perimetrale per motivi di controllo, ispezione e applicazione di criteri prima di uscire dalla rete. Rete perimetrale è inoltre possibile ospitare gateway VPN tra locale tra locale e delle reti virtuale cliente.

### <a name="perimeter-network-characteristics"></a>Caratteristiche di rete perimetrale
Fare riferimento a nella figura precedente, alcune delle caratteristiche di una rete perimetrale buona sono i seguenti:

- Per Internet:
    - Subnet della rete perimetrale stesso è esposto a Internet, direttamente la comunicazione con Internet.
    - IP di pubblico, VIP e/o endpoint del servizio passano il traffico Internet alla rete front-end e dispositivi.
    - Il traffico in ingresso da Internet attraversa dispositivi di sicurezza prima di altre risorse sulla rete front-end.
    - Se è abilitata la protezione in uscita, il traffico attraversa dispositivi di protezione, come il passaggio finale, prima di passare a Internet.
- Rete protetta:
    - Non è un percorso diretto da Internet per l'infrastruttura di base.
    - I canali dall'infrastruttura di base devono scorrere i dispositivi di sicurezza, ad esempio NSGs, firewall o dispositivi VPN.
    - Altri dispositivi non è necessario bridge Internet e l'infrastruttura di base.
    - Dispositivi di sicurezza esposto a Internet e di rete protetta affiancate limiti della rete perimetrale (ad esempio, le due firewall icone mostrate nella figura precedente) sia effettivamente un unico accessorio virtuale con regole di applicazione o interfacce per ciascuno dei limiti. (Ovvero, un dispositivo, in modo logico separato, la gestione del carico per entrambi i limiti della rete perimetrale.)
- Altre procedure consigliate e vincoli comuni:
    - Carichi di lavoro non deve memorizzare le informazioni critiche dell'azienda.
    - Accesso e gli aggiornamenti a configurazioni di rete perimetrale e distribuzioni sono limitati a solo gli amministratori autorizzati.

### <a name="perimeter-network-requirements"></a>Requisiti di rete perimetrale
Per abilitare le caratteristiche, seguire queste linee guida sui requisiti di rete virtuale per implementare una rete perimetrale corretta:

- **Architettura subnet:** Specificare la rete virtuale in modo che un'intera subnet si impegna costantemente come rete perimetrale, separata da altre subnet nella stessa rete virtuale. In questo modo il traffico tra la rete perimetrale e altri flussi di livelli subnet interno o privato attraverso un firewall o un dispositivo virtuale ID/IP nei limiti della subnet con route definite dall'utente.
- **NSG:** Subnet della rete perimetrale stesso deve essere aperta per consentire la comunicazione con Internet, ma che non è stato clienti devono ignorare NSGs. Seguire le procedure di protezione comuni per ridurre al minimo le superfici rete esposte a Internet. Bloccare gli intervalli di indirizzi remoto è consentiti l'accesso le distribuzioni o l'applicazione specifica protocolli e porte aperte. È possibile casi, tuttavia, in cui questo non è sempre possibile. Ad esempio, se i clienti dispone di un sito Web esterno in Azure, rete perimetrale devono consentire le richieste in arrivo web da un indirizzo IP pubblico, ma consigliabile aprire solo le porte di applicazione web: TCP:80 e TCP:443.
- **Tabella ciclo:** Subnet della rete perimetrale stesso dovrebbe essere possibile comunicare direttamente a Internet, ma non diretto le comunicazioni tra le reti back-end o locale senza scorrere un accessorio firewall o protezione.
- **Configurazione accessorio sicurezza:** Per instradare ed esaminare i pacchetti tra la rete perimetrale e il resto delle reti protette, le apparecchiature di sicurezza, ad esempio firewall, ID e indirizzi IP dispositivi possono essere ospitati multipla. Potrebbe essere separata NIC per la rete perimetrale e subnet back-end. NIC nella rete perimetrale comunicare direttamente da e verso Internet, con la tabella di routing di rete perimetrale e NSGs corrispondente. NIC connette alla subnet back-end con più limitate NSGs e le tabelle di routing delle subnet back-end corrispondente.
- **Funzionalità di sicurezza del dispositivo:** I dispositivi di protezione distribuiti nella rete perimetrale, in genere effettuare le seguenti funzionalità:
    - Firewall: Applicare le regole del firewall o criteri di controllo di accesso per le richieste in arrivo.
    - Prevenzione e rilevamento delle minacce: rilevamento e attenuazione attacchi da Internet.
    - Controllo e registrazione: Gestione registri dettagliati per analisi e il controllo.
    - Il proxy inverso: reindirizzare le richieste in arrivo per il server di back-end corrispondente. Questo comporta mapping e la traduzione gli indirizzi di destinazione in dispositivi front-end, in genere firewall, agli indirizzi di server di back-end.
    - Inoltrare proxy: fornire NAT ed eseguire il controllo per la comunicazione avviata da all'interno della rete virtuale a Internet.
    - Router: Inoltro del traffico in arrivo e cross-subnet all'interno della rete virtuale.
    - Dispositivo VPN: funge da gateway VPN tra locale per la connettività VPN tra locale tra clienti in locale e delle reti Azure virtuale.
    - Server VPN: accettazione client VPN che si connettono a reti virtuali Azure.

>[AZURE.TIP] Separare i due gruppi seguenti: gli utenti autorizzati ad per accedere di apparecchiature di sicurezza di rete perimetrale e le persone autorizzate come amministratori di sviluppo, distribuzione o operazioni dell'applicazione. Mantenere separati questi gruppi consente di separazione dei compiti e impedisce che una sola persona la sicurezza delle applicazioni e controlli di protezione di rete.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Domande a chiesto durante la creazione di limiti di rete
In questa sezione, a meno che non specificamente menzionate, il termine "reti" si riferisce a reti private virtuali Azure create da un amministratore della sottoscrizione. Il termine non fa riferimento a reti fisiche sottostanti all'interno di Azure.

Inoltre, le reti virtuali Azure spesso vengono utilizzate per estendere reti locale tradizionale. È possibile incorporare da sito o soluzioni di rete ibrido ExpressRoute con architetture di rete perimetrale. Questo è importante per la creazione dei limiti di protezione di rete.

Tre domande seguenti sono fondamentali per rispondere quando si crea una rete con una rete perimetrale e più limiti di sicurezza.

#### <a name="1-how-many-boundaries-are-needed"></a>1) limiti di quanti sono necessari?
Il primo punto di decisione è decidere il numero dei limiti di protezione sono necessari in un determinato scenario:

- Un solo limite: una rete perimetrale front-end, tra la rete virtuale e Internet.
- Due limiti: uno sul lato Internet della rete perimetrale e un altro tra subnet della rete perimetrale e le subnet back-end le reti virtuali Azure.
- Tre limiti: uno sul lato Internet della rete perimetrale, uno tra la rete perimetrale e subnet back-end e uno tra le subnet back-end e la rete locale.
- Limiti di N: un numero variabile. A seconda di requisiti di sicurezza, è molto qualsiasi numero dei limiti di sicurezza che possono essere applicati in una determinata rete.

Il numero e il tipo di limiti necessari variano a tolleranza al rischio dell'azienda e lo scenario specifico in fase di implementazione. Si tratta spesso decisione comune effettuata da più gruppi all'interno di un'organizzazione, inclusi spesso un rischio e conformità team, una rete e team piattaforma e un team di sviluppo di applicazioni. Le persone con conoscenza di protezione, le informazioni richieste e le tecnologie in uso devono avere una pronunciare questa decisione per garantire l'orientamento di protezione appropriato per ogni implementazione.

>[AZURE.TIP] Utilizzare il numero più piccolo dei limiti che soddisfano i requisiti di sicurezza per una determinata situazione. Con altri limiti più difficile operazioni e risoluzione dei problemi può essere, oltre al tipo sovraccaricare gestione come gestire i criteri di limite di tempo. Tuttavia, limiti insufficiente per aumentano il rischio. Trovare il saldo è fondamentale.

![Rete ibrido con tre limiti di sicurezza][6]

Nella figura precedente mostra una panoramica di una rete limite di tre sicurezza. I limiti sono compresi tra la rete perimetrale e Internet, Azure front-end e back-end subnet private e Azure subnet di back-end e alla rete aziendale in locale.

#### <a name="2-where-are-the-boundaries-located"></a>2) dove si trovano i limiti?
Dopo il numero dei limiti è scelto, dove di implementazione è il punto di decisione successivo. In genere, sono disponibili tre opzioni:
- Utilizzo di un servizio intermediario basati su Internet (ad esempio, un basate su cloud Web applicazione firewall, che non viene descritto in questo documento)
- Uso di funzionalità native e/o rete dispositivi di rete in Azure
- Utilizzo di dispositivi fisici sulla rete locale

Reti puramente Azure, le opzioni sono funzionalità Azure native (ad esempio Azure bilanciamento) o rete dispositivi di rete da ecosistema partner RTF di Azure (ad esempio, il punto di controllo firewall).

Se è necessario un limite tra Azure e una rete locale, i dispositivi di sicurezza possono trovarsi sul lato "uno" della connessione (o entrambi i lati). In questo modo un occorre all'ubicazione per posizionare a forma di ingranaggio di sicurezza.

Nella figura precedente, la rete per perimetro Internet e i limiti di primo piano di back-end interamente racchiusi Azure e devono essere funzionalità Azure native o rete dispositivi di rete. Sul lato Azure o lato locale o una combinazione dei dispositivi su entrambi i lati, potrebbero essere dispositivi di sicurezza sul bordo tra Azure (back-end subnet) e la rete aziendale. Può essere significative vantaggi e svantaggi entrambe le opzioni che deve essere considerato seriamente.

Ad esempio, utilizzo esistente a forma di ingranaggio protezione fisica sul lato rete locale ha il vantaggio che non è necessario alcun ingranaggio nuovo. Ma è sufficiente riconfigurazione. Tuttavia, invece che tutto il traffico deve torni comuni per la rete locale da ingranaggio protezione cartelle. In questo modo il traffico di Azure a Azure potrebbe risultare latenza significativa e influenzano le prestazioni dell'applicazione utente possono verificarsi e, se è stata forzata alla rete locale per l'applicazione dei criteri di sicurezza.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) sono i limiti implementazione?
Ogni limite di protezione avrà probabilmente requisiti diverse capacità (ad esempio ID e le regole del firewall sul lato Internet della rete perimetrale, ma solo ACL tra la rete perimetrale e back-end subnet). Decidere quali dispositivi che verranno usati dipende dai requisiti scenario e sicurezza. Nella sezione seguente esempi 1, 2 e 3 discutere alcune opzioni potrebbero essere utilizzate. Esaminare le caratteristiche di rete nativo Azure e i dispositivi disponibili in Azure ecosistema partner Mostra le opzioni miriade disponibili per risolvere praticamente qualsiasi scenario.

Un altro punto di decisione di implementazione della chiave viene spiegato come per la connessione di rete locali con Azure. Il gateway virtuale Azure o da un dispositivo virtuale di rete, è necessario utilizzare? Queste opzioni sono illustrate in dettaglio nella sezione seguente (esempi 4, 5 e 6).

Inoltre, il traffico tra reti virtuali all'interno di Azure potrebbe essere necessarie. Gli scenari seguenti verranno aggiunti in un secondo momento.

Dopo aver appreso le risposte alle domande precedente, la sezione [Avvio veloce](#fast-start) consente di identificare quali esempi sono più adatti per un determinato scenario.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Esempi: Creazione di limiti di protezione con Azure reti virtuali
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Esempio 1: Creare una rete perimetrale per proteggere le applicazioni con NSGs
[Torna all'inizio veloce](#fast-start) | [istruzioni per questo esempio di compilazione dettagliato][Example1]

![Rete perimetrale in ingresso con NSG][7]

#### <a name="environment-description"></a>Descrizione di ambiente
In questo esempio è un abbonamento che contiene le operazioni seguenti:

- Due servizi cloud: "FrontEnd001" e "BackEnd001"
- Una rete virtuale "CorpNetwork", con due subnet: "Front-end" e "Back-end"
- Un gruppo di sicurezza di rete che viene applicata a entrambi subnet
- Windows server che rappresenta un server web di applicazioni ("IIS01")
- Due server di Windows che rappresentano server di back-end dell'applicazione ("AppVM01", "AppVM02")
- Windows server che rappresenta un server DNS ("DNS01")

Per gli script e un modello di gestione risorse di Azure, vedere le [istruzioni dettagliate][Example1].

#### <a name="nsg-description"></a>Descrizione NSG
In questo esempio, un gruppo NSG è integrato e caricato con sei regole.

>[AZURE.TIP] In generale, è necessario creare regole di "Consenti" specifiche prima di tutto, quindi le regole "Nega" più generiche. La priorità specificata determina le regole vengono valutate per prime. Una volta trovato il traffico da applicare a una regola specifica, non vengono valutate ulteriormente regole. NSG regole possono essere applicate nella direzione in ingresso o in uscita (dal punto di vista della subnet).

Per il traffico in ingresso in modo dichiarativo, vengono create le regole seguenti:

1.  È consentito il traffico DNS interno (porta 53).
2.  È consentito il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale.
3.  È consentito il traffico HTTP (porta 80) da Internet al server web (IIS01).
4.  Tutto il traffico (tutte le porte) da IIS01 a AppVM1 è consentito.
5.  Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) negato.
6.  Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end negato.

Con queste regole associato a ogni subnet, se una richiesta HTTP è stato in ingresso da Internet al server web, sia le regole di 3 (Consenti) e 5 (negare) da applicare. Poiché tuttavia regola 3 ha una priorità più alta, solo da applicare e regola 5 non si presenterà nei Riproduci. In questo modo la richiesta HTTP è autorizzata al server web. Se il traffico stesso ha tentato di raggiungere il server DNS01, regola 5 (negare) è la prima di applicare e il traffico da non sia possibile passare al server. Regola 6 (negare) Blocca subnet front-end da parlando alla subnet back-end (ad eccezione di traffico consentito nelle regole 1 e 4). Consente di proteggere la rete back-end nel caso in cui un pirata informatico compromettere l'applicazione web nel front-end. Il pirata informatico sarebbe dispongono di accesso limitato alla rete "protetta" back-end (solo alle risorse esposti nel server AppVM01).

Esiste una regola in uscita predefinito che consente il traffico su Internet. In questo esempio abbiamo stiamo consentire il traffico in uscita e non modificare le regole in uscita. Per bloccare il traffico in entrambe le direzioni, routing definite dall'utente è richiesto (vedere esempio 3).

#### <a name="conclusion"></a>Conclusioni
Questo è un modo relativamente semplice e semplice di isolamento subnet back-end dal traffico in ingresso. Per ulteriori informazioni, vedere le [istruzioni dettagliate][Example1]. Queste istruzioni includono:

- Modalità di compilazione di questa rete perimetrale con gli script di PowerShell.
- Modalità di compilazione di questa rete perimetrale con un modello di gestione di risorse Azure.
- Descrizioni dettagliate di ogni comando NSG.
- Scenari di flusso del traffico dettagliate, che mostra come il traffico sia consentito o negato in ogni livello.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e NSGs
[Torna all'inizio veloce](#fast-start) | [istruzioni per questo esempio di compilazione dettagliato][Example2]

![Rete perimetrale in ingresso con NVA e NSG][8]

#### <a name="environment-description"></a>Descrizione di ambiente
In questo esempio è un abbonamento che contiene le operazioni seguenti:

- Due servizi cloud: "FrontEnd001" e "BackEnd001"
- Una rete virtuale "CorpNetwork", con due subnet: "Front-end" e "Back-end"
- Un gruppo di sicurezza di rete che viene applicata a entrambi subnet
- Un dispositivo virtuale di rete, in questo caso un firewall, connesso alla subnet front-end
- Windows server che rappresenta un server web di applicazioni ("IIS01")
- Due server di Windows che rappresentano server di back-end dell'applicazione ("AppVM01", "AppVM02")
- Windows server che rappresenta un server DNS ("DNS01")

Per gli script e un modello di gestione risorse di Azure, vedere le [istruzioni dettagliate][Example2].

#### <a name="nsg-description"></a>Descrizione NSG
In questo esempio, un gruppo NSG è integrato e caricato con sei regole.

>[AZURE.TIP] In generale, è necessario creare regole di "Consenti" specifiche prima di tutto, quindi le regole "Nega" più generiche. La priorità specificata determina le regole vengono valutate per prime. Una volta trovato il traffico da applicare a una regola specifica, non vengono valutate ulteriormente regole. NSG regole possono essere applicate nella direzione in ingresso o in uscita (dal punto di vista della subnet).

Per il traffico in ingresso in modo dichiarativo, vengono create le regole seguenti:

1.  È consentito il traffico DNS interno (porta 53).
2.  È consentito il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale.
3.  È consentito tutto il traffico Internet (tutte le porte) per il dispositivo virtuale di rete (firewall).
4.  Tutto il traffico (tutte le porte) da IIS01 a AppVM1 è consentito.
5.  Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) negato.
6.  Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end negato.

Con queste regole associato a ogni subnet, se una richiesta HTTP è stato in ingresso da Internet per il firewall sia le regole di 3 (Consenti) e 5 (negare) da applicare. Poiché tuttavia regola 3 ha una priorità più alta, solo da applicare e regola 5 non si presenterà nei Riproduci. In questo modo la richiesta HTTP sarà in grado del firewall. Se il traffico stesso ha tentato di raggiungere il server IIS01, anche se è disponibile nel subnet front-end, regola 5 (negare) da applicare, il traffico da non è consentito per passare al server. Regola 6 (negare) Blocca subnet front-end da parlando alla subnet back-end (ad eccezione di traffico consentito nelle regole 1 e 4). Consente di proteggere la rete back-end nel caso in cui un pirata informatico compromettere l'applicazione web nel front-end. Il pirata informatico sarebbe dispongono di accesso limitato alla rete "protetta" back-end (solo alle risorse esposti nel server AppVM01).

Esiste una regola in uscita predefinito che consente il traffico su Internet. In questo esempio abbiamo stiamo consentire il traffico in uscita e non modificare le regole in uscita. Per bloccare il traffico in entrambe le direzioni, routing definite dall'utente è richiesto (vedere esempio 3).

#### <a name="firewall-rule-description"></a>Descrizione della regola firewall
Sul firewall, è necessario creare regole di inoltro. Poiché questo esempio viene distribuito solo il traffico Internet in ingresso per il firewall e quindi al server web, l'inoltro di un solo indirizzo conversione rete (NAT) non è necessario regola.

La regola di inoltro accetta qualsiasi indirizzo di origine in entrata raggiunti firewall tentando di contattare HTTP (porta 80 o 443 per HTTPS). Ha inviato dall'interfaccia locale del firewall e reindirizzato al server web con l'indirizzo IP del 10.0.1.5.

#### <a name="conclusion"></a>Conclusioni
Questo è un modo semplice di protezione dell'applicazione con il firewall e isolare subnet back-end dal traffico in ingresso. Per ulteriori informazioni, vedere le [istruzioni dettagliate][Example2]. Queste istruzioni includono:

- Modalità di compilazione di questa rete perimetrale con gli script di PowerShell.
- Come compilare questo esempio con un modello di gestione di risorse Azure.
- Descrizioni dettagliate di ogni regola NSG comando e del firewall.
- Scenari di flusso del traffico dettagliate, che mostra come il traffico sia consentito o negato in ogni livello.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, UDR e NSG
[Torna all'inizio veloce](#fast-start) | [istruzioni per questo esempio di compilazione dettagliato][Example3]

![Rete perimetrale bidirezionale con NVA NSG e UDR][9]

#### <a name="environment-description"></a>Descrizione di ambiente
In questo esempio è un abbonamento che contiene le operazioni seguenti:

- Tre servizi cloud: "SecSvc001", "FrontEnd001" e "BackEnd001"
- Una rete virtuale "CorpNetwork", con tre subnet: "SecNet", "Front-end" e "Back-end"
- Un dispositivo virtuale di rete, in questo caso un firewall, connesso alla subnet SecNet
- Windows server che rappresenta un server web di applicazioni ("IIS01")
- Due server di Windows che rappresentano server di back-end dell'applicazione ("AppVM01", "AppVM02")
- Windows server che rappresenta un server DNS ("DNS01")

Per gli script e un modello di gestione risorse di Azure, vedere le [istruzioni dettagliate][Example3].

#### <a name="udr-description"></a>Descrizione UDR
Per impostazione predefinita, le route di sistema seguenti sono definite come:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Il VNETLocal è sempre prefix(es) definito indirizzo della rete virtuale per la rete specifica (ovvero, vengono modificate da virtuali in virtuali, a seconda di come viene definito ogni specifico virtuali). Le rimanenti route di sistema sono statiche e predefiniti come indicato nella tabella.

In questo esempio vengono create due tabelle di routing, uno relativo alla subnet front-end e back-end. Ogni tabella viene caricata con route statiche appropriate per la subnet specificata. In questo esempio, ogni tabella con tre route che indirizzare tutto il traffico (0.0.0.0/0) attraverso il firewall (hop successivo = indirizzo IP del dispositivo virtuale):

1. Il traffico subnet locale con alcun Hop successivo definito per consentire il traffico subnet locale ignorare il firewall.
2. Traffico di rete virtuale con un Hop successivo definito come firewall; esegue l'override della regola predefinita che consente di locale virtuale il traffico di rete instradare direttamente.
3. Tutto rimanente il traffico (0/0) con un Hop successivo definito come il firewall.

>[AZURE.TIP] Non verificati voce subnet locale la UDR verranno interrotte comunicazioni subnet locale. 
> - In questo esempio, 10.0.1.0/24 che puntano alle VNETLocal è fondamentale come uscire dal pacchetto in caso contrario, il Server Web (10.0.1.4) destinati a un altro server locale (ad esempio) 10.0.1.25 avrà esito negativo come verrà inviate in a NVA, che invia essa alla subnet, e alla subnet verrà nuovamente inviarlo per la NVA e così via.
> - Probabilità di un ciclo routing sono in genere superiore per elettrodomestici multi-nic connessi direttamente a ogni subnet che sono comunica con, ossia spesso tradizionale, in locale, accessori. 

Dopo aver create le tabelle di routing, vengano associati alle loro subnet. La tabella routing front-end, una volta creato e associato alla subnet, sarà simile alla seguente:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] Ora è possibile applicare UDR alla subnet gateway in cui è connesso circuito ExpressRoute.
>
> Negli esempi 3 e 4 sono riportati alcuni esempi di come abilitare la rete perimetrale con ExpressRoute o reti da sito.


#### <a name="ip-forwarding-description"></a>Descrizione inoltro IP
Inoltro IP è una caratteristica correlata a UDR. Questa è un'impostazione in un dispositivo virtuale che consente di ricevere il traffico non specificamente indirizzato all'accessorio e quindi inoltrare il traffico alla destinazione finale.

Ad esempio, se il traffico da AppVM01 effettua una richiesta al server DNS01, UDR indirizza seguente per il firewall. Con IP inoltro abilitato, il traffico verso la destinazione DNS01 (10.0.2.4) viene accettato dall'accessorio (10.0.0.4) e quindi inviato alla destinazione ultimate (10.0.2.4). Senza IP inoltro attivato il firewall, il traffico non sarà stato accettato dall'accessorio, anche se la tabella route include il firewall come hop successivo. Per utilizzare un dispositivo virtuale, è importante ricordare di attivare inoltro IP in combinazione con UDR.

#### <a name="nsg-description"></a>Descrizione NSG
In questo esempio, un gruppo NSG è integrato e quindi caricato con una singola regola. In questo gruppo viene quindi associato solo alle subnet front-end e back-end (non SecNet). In modo dichiarativo viene creata la regola seguente:

- Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (tutte le subnet) negato.

Sebbene NSGs vengono utilizzati in questo esempio, lo scopo principale è come un livello secondario di difesa errata configurazione manuale. L'obiettivo è bloccare tutto il traffico in ingresso da Internet alla subnet front-end o back-end. Il traffico deve solo scorrono subnet SecNet per il firewall (e quindi, se necessario, è possibile subnet front-end o back-end). Inoltre, con le regole UDR in posizione, il traffico che trasformarlo in subnet front-end o back-end da indirizzato fuori al firewall (ringraziamento UDR). Il firewall vedrà come un flusso di asimmetrico e da rilasciare il traffico in uscita. In questo modo sono disponibili tre livelli di sicurezza: protezione subnet:
- Servizi non cloud punti finali aperti sul FrontEnd001 e BackEnd001.
- NSGs si nega il traffico da Internet.
- Il traffico asimmetrici eliminazione firewall.

Un aspetto interessante relative alle NSG in questo esempio è che contiene solo una regola, consiste nel negare il traffico Internet all'intera rete virtuale, tra cui la subnet di sicurezza. Poiché il NSG solo è associata a subnet front-end e back-end, la regola non viene elaborata per il traffico in ingresso alla subnet di sicurezza. Di conseguenza, il traffico scorrerà alla subnet di sicurezza.

#### <a name="firewall-rules"></a>Regole firewall
Sul firewall, è necessario creare regole di inoltro. Poiché il firewall blocca o inoltro tutti in entrata, in uscita e il traffico di rete virtuale all'interno di un', sono necessari più regole del firewall. Inoltre, tutto il traffico verrà raggiunto l'indirizzo IP pubblico del servizio di sicurezza (su porte diverse), per essere elaborati dal firewall. Procedura consigliata consiste nel diagramma flussi logici prima di configurare le subnet e le regole firewall, per evitare rielaborare in un secondo momento. Nella figura seguente è una visualizzazione logica di regole del firewall per questo esempio:
 
![Visualizzazione logica di regole del firewall][10]

>[AZURE.NOTE] In base al dispositivo di rete virtuale utilizzato, le porte gestione variano. In questo esempio, un Firewall di NextGen Barracuda viene fatto riferimento, che utilizza le porte 22, 801 e 807. Consultare la documentazione del produttore del dispositivo per individuare le porte esatte utilizzate per la gestione del dispositivo in uso.

#### <a name="firewall-rules-description"></a>Descrizione regole firewall
Nel diagramma precedente, alla subnet sicurezza non viene visualizzata. Ciò avviene perché il firewall è l'unica risorsa nella subnet e il diagramma è che mostra le regole del firewall e come logicamente Consenti o Nega flussi di traffico, non il percorso indirizzato effettivo. Inoltre, le porte esterne selezionate per il traffico RDP superiori erano tra porte (8014-8026) e selezionate per allineare un po' con gli ultimi due ottetti dell'indirizzo IP locale per facilitarne la lettura (ad esempio, indirizzo del server locale 10.0.1.4 è associato a porta esterna 8014). Le porte superiore non in conflitto, tuttavia, potrebbero essere utilizzate.

In questo esempio, è necessario sette tipi di regole:

- Regole esterne (per il traffico in ingresso):
  1.    Regola di gestione del firewall: reindirizzare App questa regola consente il traffico passare alle porte di gestione dell'accessorio virtuale rete.
  2.    Le regole RDP (per ogni server di Windows): questi quattro regole (uno per ogni server) consentono la gestione dei singoli server tramite RDP. Questo potrebbe anche essere inserito in una regola in base alle funzionalità dell'accessorio virtuale rete in uso.
  3.    Regole di traffico applicazione: esistono due di queste regole, il primo per il traffico web front-end e il secondo per il traffico di back-end (ad esempio server web a livello di dati). La configurazione di queste regole dipende l'architettura di rete (in cui vengono inseriti i server) e il traffico flussi (che indica la direzione in cui vengono utilizzati i flussi di traffico e le porte).
      - La prima regola consente il traffico delle applicazioni effettivo raggiungere il server di applicazione. Mentre le altre regole consentono di sicurezza e gestione, applicazione il traffico regole sono operazioni consentite agli utenti esterni o servizi per le applicazioni di accedere. In questo esempio, è un server web singolo sulla porta 80. In questo modo una regola di applicazione firewall singolo reindirizza il traffico in ingresso all'indirizzo IP esterno, per l'indirizzo IP interno del server web. La sessione di traffico reindirizzato da tradurre tramite NAT al server interno.
      - La seconda regola è la regola di back-end per consentire al server web di comunicare con il server AppVM01 (ma non AppVM02) tramite qualsiasi porta.
- Regole interne (per il traffico di rete all'interno della stessa virtuale)
  4.    In uscita a regola Internet: questa regola consente il traffico da qualsiasi rete per passare alle reti selezionate. Si tratta in genere una regola predefinita già sul firewall, ma in stato disabilitato. Questa regola deve essere abilitata per questo esempio.
  5.    Regola DNS: questa regola consente solo il traffico DNS (porta 53) passare al server DNS. Per questo ambiente la maggior parte del traffico da front-end back-end è bloccato. Questa regola consente in particolare DNS da qualsiasi subnet locale.
  6.    Subnet alla regola subnet: questa regola è per consentire a tutti i server su subnet back-end per connettersi a qualsiasi server front-end subnet (ma non viceversa).
- In modalità provvisoria regola (per il traffico che non soddisfa una delle precedente):
  7.    Tutto il traffico regola di negazione: deve sempre essere la regola finale (in termini di priorità) e pertanto se il flusso di traffico non riesce a corrisponde a uno dei precedente regole verrà rimosso da questa regola. Si tratta di una regola predefinita che in genere attivata. Nessuna modifica in genere è necessari.

>[AZURE.TIP] Nella seconda regola il traffico di applicazione, per semplificare l'esempio è consentita qualsiasi porta. In uno scenario reale, la porta più specifica e intervalli di indirizzi devono essere utilizzati per ridurre la possibilità di attacchi di questa regola.

Dopo aver create tutte le regole precedenti, è importante rivedere la priorità di ogni regola per assicurarsi che il traffico verrà consentito o negato in base alle esigenze. In questo esempio, le regole sono in ordine di priorità.

#### <a name="conclusion"></a>Conclusioni
Questo è un modo più complesso ma più completo di protezione e isolare la rete da quella degli esempi precedenti. Esempio 2 protegge solo l'applicazione, ed esempio 1 isola solo subnet. Questo modello consente di controllare il traffico in entrambe le direzioni e protegge non solo il server di applicazione in ingresso ma Applica criteri di sicurezza di rete per tutti i server della rete. Inoltre, a seconda del dispositivo utilizzato, il controllo completo del traffico e informazioni sulla presenza è possibile ottenere. Per ulteriori informazioni, vedere le [istruzioni dettagliate][Example3]. Queste istruzioni includono:

- Modalità di compilazione di questa rete perimetrale di esempio con gli script di PowerShell.
- Come compilare questo esempio con un modello di gestione di risorse Azure.
- Le descrizioni dettagliate di ogni UDR NSG comando e regola firewall.
- Scenari di flusso del traffico dettagliate, che mostra come il traffico sia consentito o negato in ogni livello.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Esempio 4: Aggiungere una connessione ibrido con un accessorio da sito, virtual private virtuali (VPN)
[Torna all'inizio rapido](#fast-start) | Istruzioni di compilazione disponibili dettagliate breve

![Rete perimetrale con NVA connessi rete ibrido][11]

#### <a name="environment-description"></a>Descrizione di ambiente
Rete ibrido usando un dispositivo virtuale di rete (NVA) può essere aggiunti a uno dei tipi di rete perimetrale descritti negli esempi 1, 2 o 3.

Come mostrato nella figura precedente, una connessione VPN tramite Internet (da-sito) viene utilizzata per la connessione di una rete locale a una rete virtuale Azure tramite un NVA.

>[AZURE.NOTE] Se si utilizza ExpressRoute con l'opzione Azure pubblico Peering attivata, è necessario creare una route statica. Questa operazione deve instradare all'indirizzo IP VPN a fuori rete aziendale Internet e non tramite WAN di ExpressRoute. NAT necessari dell'opzione Peering pubblico Azure ExpressRoute possibile interrompere la sessione VPN.

Una volta la rete VPN, il NVA diventa hub centrale per tutte le reti e subnet. Le regole di inoltro firewall determinano quali flussi di traffico consentiti, vengono convertite tramite NAT, vengono reindirizzate o vengono rimossi (anche ai flussi di traffico tra la rete locale e Azure).

Flussi di traffico devono essere considerati attentamente, man mano che può essere ottimizzate o ridotto da questo modello di progettazione, in base a specifiche utilizzare maiuscole/minuscole.

Utilizzo dell'ambiente creata nell'esempio 3 e quindi aggiungere una connessione VPN da sito: network ibrido, viene prodotta la struttura seguente:

![Connessione di rete perimetrale con NVA utilizzando una connessione VPN da sito][12]

Al router locale o ad altri dispositivi di rete compatibile con la NVA per VPN, sarà il client VPN. Questo dispositivo fisico è responsabile per avviare e mantenere la connessione VPN con il NVA.

In modo logico a NVA, la rete è simile alla quattro separato "aree di protezione" con le regole in NVA vengano direttore principale del traffico tra queste aree:

![Rete logica dal punto di vista NVA][13]

#### <a name="conclusion"></a>Conclusioni
L'aggiunta di una connessione di rete ibrido VPN da sito a una rete virtuale Azure può estendere la rete locale in Azure in modo sicuro. Durante l'uso di una connessione VPN, il traffico è crittografato e indirizza tramite Internet. NVA in questo esempio offre una posizione centrale per attivare e gestire i criteri di sicurezza. Per ulteriori informazioni, vedere le istruzioni dettagliate (misura). Queste istruzioni includono:

- Modalità di compilazione di questa rete perimetrale di esempio con gli script di PowerShell.
- Come compilare questo esempio con un modello di gestione di risorse Azure.
- Scenari di flusso il traffico dettagliata, che mostra il modo in cui il traffico attraversa questo schema.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Esempio 5: Aggiungere una connessione ibrido con un gateway a siti, Azure VPN
[Torna all'inizio rapido](#fast-start) | Istruzioni di compilazione disponibili dettagliate breve

![Rete perimetrale con rete ibrido connesso gateway][14]

#### <a name="environment-description"></a>Descrizione di ambiente
Rete ibrido utilizzando un gateway VPN Azure può essere aggiunto al tipo di rete perimetrale descritto negli esempi 1 o 2.

Come mostrato nella figura precedente, una connessione VPN tramite Internet (da-sito) viene utilizzata per la connessione di una rete locale a una rete virtuale Azure tramite un gateway VPN Azure.

>[AZURE.NOTE] Se si utilizza ExpressRoute con l'opzione Azure pubblico Peering attivata, è necessario creare una route statica. Questa operazione deve instradare all'indirizzo IP VPN a fuori rete aziendale Internet e non tramite WAN di ExpressRoute. NAT necessari dell'opzione Peering pubblico Azure ExpressRoute possibile interrompere la sessione VPN.

La figura seguente mostra i due bordi di rete con questa opzione. Sul bordo prima di NVA NSGs controllare flussi di traffico per le reti all'interno della stessa Azure e tra Azure e Internet. Il secondo bordo è il gateway VPN Azure, ossia il bordo di rete completamente separata e isolate tra locale e Azure.

Flussi di traffico devono essere considerati attentamente, man mano che può essere ottimizzate o ridotto da questo modello di progettazione, in base a specifiche utilizzare maiuscole/minuscole.

Utilizzo dell'ambiente creata nell'esempio 1 e quindi aggiungere una connessione VPN da sito: network ibrido, viene prodotta la struttura seguente:

![Rete perimetrale con gateway connessi mediante una connessione ExpressRoute][15]

#### <a name="conclusion"></a>Conclusioni
L'aggiunta di una connessione di rete ibrido VPN da sito a una rete virtuale Azure può estendere la rete locale in Azure in modo sicuro. Utilizzo del gateway VPN Azure nativo, il traffico IPSec crittografati e indirizza tramite Internet. Inoltre, tramite il gateway VPN Azure, è possibile fornire un'opzione di costo inferiore (alcuna licenza aggiuntiva costi come con NVAs di terze parti). Si tratta più economico dell'esempio 1, in cui non viene utilizzata NVA. Per ulteriori informazioni, vedere le istruzioni dettagliate (misura). Queste istruzioni includono:

- Modalità di compilazione di questa rete perimetrale di esempio con gli script di PowerShell.
- Come compilare questo esempio con un modello di gestione di risorse Azure.
- Scenari di flusso il traffico dettagliata, che mostra il modo in cui il traffico attraversa questo schema.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Esempio 6: Aggiungere una connessione ibrido con ExpressRoute
[Torna all'inizio rapido](#fast-start) | Istruzioni di compilazione disponibili dettagliate breve

![Rete perimetrale con rete ibrido connesso gateway][16]

#### <a name="environment-description"></a>Descrizione di ambiente
Rete ibrido usi una connessione di peering privata ExpressRoute può essere aggiunto al tipo di rete perimetrale descritto negli esempi 1 o 2.

Come mostrato nella figura precedente, peering privato ExpressRoute fornisce un collegamento diretto tra la rete locale e la rete virtuale Azure. Il traffico transito sulla solo la rete del provider di servizi e la rete Microsoft Azure, non toccare mai Internet.

>[AZURE.NOTE] Esistono alcune limitazioni quando si usa UDR con ExpressRoute, a causa di complessità del routing dinamico usato in Azure gateway virtuale. Questi sono i seguenti:
>
>- UDR non devono essere applicati alla subnet gateway in cui è connesso ExpressRoute collegate Azure virtuale gateway.
>- ExpressRoute collegate Azure virtuale gateway non può essere che il dispositivo di hop successivo per altri UDR associato subnet.
>
>
<br />

>[AZURE.TIP] Utilizzo di ExpressRoute mantiene il traffico di rete aziendale su Internet per maggiore sicurezza e prestazioni migliorate in modo significativo. Consente inoltre contratti di servizio dal provider ExpressRoute. Il Gateway di Azure possibile passare fino a 2 Gb/s con ExpressRoute, mentre con VPN da sito e la velocità massima Gateway Azure è 200 Mb/s.

Come illustrato nella figura seguente, con questa opzione l'ambiente è ora disponibile due bordi di rete. La NVA e NSG controllare flussi di traffico per le reti all'interno della stessa Azure e tra Azure e Internet, mentre il gateway è un bordo rete completamente separata e isolate tra locale e Azure.

Flussi di traffico devono essere considerati attentamente, man mano che può essere ottimizzate o ridotto da questo modello di progettazione, in base a specifiche utilizzare maiuscole/minuscole.

Utilizzando l'ambiente integrato in esempio 1 e quindi aggiungendo una connessione di rete ExpressRoute ibrido, viene prodotta la struttura seguente:

![Rete perimetrale con gateway connessi mediante una connessione ExpressRoute][17]

#### <a name="conclusion"></a>Conclusioni
L'aggiunta di una connessione di rete privata ExpressRoute Peering può estendere la rete locale in Azure in una latenza sicura, inferiore, superiore esecuzione modo. Inoltre, usando il Gateway di Azure nativo, come in questo esempio include un'opzione di costo inferiore (non ulteriore licenze come con NVAs di terze parti). Per ulteriori informazioni, vedere le istruzioni dettagliate (misura). Queste istruzioni includono:

- Modalità di compilazione di questa rete perimetrale di esempio con gli script di PowerShell.
- Come compilare questo esempio con un modello di gestione di risorse Azure.
- Scenari di flusso il traffico dettagliata, che mostra il modo in cui il traffico attraversa questo schema.

## <a name="references"></a>Riferimenti
### <a name="helpful-websites-and-documentation"></a>Documentazione e siti Web utili
- Accesso Azure Azure Gestione risorse:
- Accesso Azure con PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentazione di rete virtuale: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentazione di gruppo di sicurezza di rete: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentazione di routing definite dall'utente: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure gateway virtuale: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- VPN da sito: [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- Documentazione ExpressRoute (assicurarsi di estrarre le sezioni "Guida introduttiva" e "How To"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagramma di flusso opzioni di sicurezza"
[1]: ./media/best-practices-network-security/compliancebadges.png "Conformità Azure badge"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Funzionalità di sicurezza di Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Una rete Perimetrale in una rete aziendale"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architettura di sicurezza di Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Una rete Perimetrale in una rete virtuale Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rete ibrido con tre limiti di sicurezza"
[7]: ./media/best-practices-network-security/example1design.png "Rete Perimetrale in ingresso con NSG"
[8]: ./media/best-practices-network-security/example2design.png "Rete Perimetrale in ingresso con NVA e NSG"
[9]: ./media/best-practices-network-security/example3design.png "Rete Perimetrale bidirezionale con NVA NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Visualizzazione logica di regole del Firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "Rete Perimetrale con NVA connessi rete ibrido"
[12]: ./media/best-practices-network-security/example4designs2s.png "Rete Perimetrale con NVA connesso utilizzando una connessione VPN da sito"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rete logica dal punto di vista NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "Rete Perimetrale con Azure Gateway connessi rete da sito ibrido"
[15]: ./media/best-practices-network-security/example5designs2s.png "Rete Perimetrale con Azure Gateway tramite VPN da sito"
[16]: ./media/best-practices-network-security/example6designoptions.png "Rete Perimetrale con Azure Gateway connessi rete ExpressRoute ibrido"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Rete Perimetrale con Azure Gateway mediante una connessione ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
