<properties
   pageTitle="Implementazione di ADFS in Azure | Microsoft Azure"
   description="Come implementare un'architettura di rete sicura ibrido con autorizzazione di Active Directory Federation Services in Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Implementazione di Active Directory Federation Services (ADFS) in Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per l'implementazione di una rete sicura ibrido che si estende la rete locale in Azure e che utilizza [Active Directory Federation Services (ADFS)] [ active-directory-federation-services] per eseguire l'autenticazione federato e l'autorizzazione per i componenti in esecuzione nel cloud. Questa architettura estende la struttura descritta [dall'Estensione Active Directory per Azure][implementing-active-directory].

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. L'architettura di riferimento utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

ADFS può essere eseguito in locale, ma in uno scenario ibrido dove si trovano le applicazioni di Azure può essere più efficiente per implementare questa funzionalità nel cloud. Casi di utilizzo tipico per questa architettura includono:

- Applicazioni ibride in carichi di lavoro Esegui parzialmente locale e in parte in Azure.

- Soluzioni che utilizzano autorizzazioni federati per esporre applicazioni web in modo che le organizzazioni partner.

- Sistemi che supportano l'accesso dal web browser in esecuzione all'esterno del firewall dell'organizzazione.

- Sistemi che consentono agli utenti di accedere alle applicazioni web mediante la connessione da dispositivi esterni autorizzati ad esempio computer remoti, i blocchi appunti e altri dispositivi mobili. 

Per ulteriori informazioni sul funzionamento di ADFS, vedere [Panoramica di Active Directory Federation Services][active-directory-federation-services-overview]. Inoltre, l'articolo [distribuzione di ADFS in Azure] [ adfs-intro] contiene un'introduzione dettagliata dettagliata all'implementazione di ADFS in Azure.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura (*fare clic su per applicare lo zoom avanti*). Per ulteriori informazioni su qualsiasi elemento non correlato all'ADFS, leggere [l'implementazione di un'architettura di rete ibrido sicura in Azure][implementing-a-secure-hybrid-network-architecture], [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]e [implementare un'architettura di rete sicura ibrido con identità di Active Directory in Azure][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] Figura vengono indicate le seguenti casi di utilizzo:
>
>- Il codice dell'applicazione in esecuzione all'interno di un'organizzazione partner accede a un'applicazione web ospitata all'interno del VNet Azure.
>
>- Un utente esterno, registrato (con le credenziali memorizzate all'interno aggiunge) accesso a un'applicazione web ospitata all'interno del VNet Azure.
>
>- La connessione al VNet usando un dispositivo autorizzato e esecuzione di un'applicazione web ospitata all'interno del VNet Azure.
>
>Inoltre, questa architettura è incentrata su federazione passiva, in cui il server federativi decidere come e quando per eseguire l'autenticazione utente. L'utente deve fornire informazioni di accesso all'avvio dell'applicazione in esecuzione. Si tratta meccanismo più comunemente usato dal web browser e prevede un protocollo che reindirizza il browser a un sito in cui l'utente può fornire le proprie credenziali. ADFS supporta anche la federazione attiva in base al quale un'applicazione assume responsabilità per fornire le credenziali senza ulteriore interazione dell'utente, ma in questo caso è all'esterno dell'ambito di questa architettura.

- **Aggiunge subnet.** I server aggiunge sono contenuti in subnet propria. Le regole NSG consentono di proteggere il server aggiunge e offrono un firewall contro il traffico da origini non previsti.

- **Consente di aggiungere server.** Questi sono controller di dominio in esecuzione come macchine virtuali nel cloud. Questi server possono fornire l'autenticazione delle identità locali all'interno del dominio.

- **Subnet ADFS.** Server ADFS può trovarsi all'interno delle proprie subnet, con le regole NSG funge da un firewall.

- **Server ADFS.** Server ADFS fornire l'autenticazione e autorizzazione federati. In questa architettura, sono le seguenti operazioni:

    - Ricevono token di sicurezza contenente dichiarazioni da un server federativo partner per conto di un utente partner. ADFS è possibile verificare che i token siano validi prima di passare alle domande di applicazione web in esecuzione in Azure. L'applicazione web aziendale (in Azure) è possibile utilizzare le richieste per autorizzare le richieste. In questo scenario, l'applicazione web aziendale è il *componente di applicazione*ed è responsabilità del server federativo partner da emettere dichiara che vengano interpretati dall'applicazione web aziendale. I server federativi partner sono definiti per *partner account* poiché l'invio di richieste di accesso per conto di un account autenticato nell'organizzazione partner. Server ADFS sono denominate *partner risorse* perché forniscono accesso alle risorse (le applicazioni web, in questo caso).

    - È possibile eseguire l'autenticazione (tramite somma e il [Servizio di registrazione di Active Directory dispositivo][ADDRS]) e autorizzare le richieste in arrivo dagli utenti esterni che esegue un browser web o un dispositivo che richiede l'accesso alle applicazioni web aziendale. 

    Server ADFS sono configurati come una farm, accedere tramite un servizio di bilanciamento del carico Azure. La struttura consente una maggiore disponibilità e scalabilità. Si noti inoltre che il server ADFS non viene esposto direttamente a Internet, anziché tutto il traffico Internet viene filtrato tramite server proxy di applicazioni web ADFS e una rete Perimetrale.

- **Subnet proxy ADFS.** I server proxy ADFS possono essere contenuti in subnet propria con regole di NSG fornire la protezione. I server subnet sono esposto su Internet tramite un insieme di rete dispositivi di rete che forniscono un firewall tra Azure virtuale rete e Internet.

- **Server di proxy (WAP) dell'applicazione web ADFS.** Questi computer fungono da server ADFS per le richieste in arrivo da dispositivi esterni e organizzazioni partner. I server WAP fungono da un filtro, schermatura server ADFS dall'accesso diretto da Internet pubblica. Come con server ADFS distribuzione nel WAP server farm con bilanciamento del carico offre maggiore disponibilità e scalabilità della distribuzione di un insieme di server autonomo.

    >[AZURE.NOTE] Per informazioni dettagliate sull'installazione server WAP, vedere [installare e configurare il Server Proxy di applicazione Web][install_and_configure_the_web_application_proxy_server]

- **Organizzazione partner.** Si tratta di un'organizzazione partner di esempio, che viene eseguita un'applicazione web che richiede l'accesso all'applicazione web in esecuzione in Azure. Il server federativo dell'organizzazione partner autentica le richieste in locale e invia token di sicurezza contenente attestazioni per ADFS in esecuzione in Azure. ADFS in Azure convalida i token di sicurezza e se sono validi può passare attestazioni per l'applicazione web in esecuzione in Azure per fornire le autorizzazioni.

    >[AZURE.NOTE] È inoltre possibile configurare un tunnel VPN con Azure Gateway per fornire accesso diretto alla ADFS per i partner attendibili. Richieste ricevute da tali partner non passano attraverso il server WAP.

## <a name="recommendations"></a>Consigli

In questa sezione vengono riepilogati i suggerimenti per l'implementazione di ADFS in Azure, che copre:

- Consigli macchine Virtuali.

- Suggerimenti di rete.

- Suggerimenti di disponibilità.

- Suggerimenti per la sicurezza.

- Raccomandazioni di installazione di ADFS.

- Consigli ADFS attendibile.

### <a name="vm-recommendations"></a>Consigli macchine Virtuali

Creare macchine virtuali con risorse sufficienti per gestire il volume di traffico previsto. Utilizzare le dimensioni del computer esistenti hosting ADFS locale come punto di partenza. Monitorare l'utilizzo delle risorse. È possibile ridimensionare le macchine virtuali e ridimensionare verso il basso se sono troppo grande.

Seguire le indicazioni elencate in [esecuzione una macchina virtuale di Windows in Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Suggerimenti di rete

Configurare l'interfaccia di rete per ognuna delle macchine virtuali hosting server ADFS e WAP con indirizzi IP privati statici.

Non fornire macchine virtuali di ADFS di indirizzi IP. Per ulteriori informazioni, vedere [Considerazioni sulla protezione][security-considerations].

Impostare l'indirizzo IP dei server DNS preferito e secondario per le interfacce di rete per ogni ADFS e WAP VM fare riferimento a macchine virtuali aggiunge (che deve essere in esecuzione DNS). Questa operazione è necessaria attivare ogni macchina virtuale al dominio.

### <a name="availability-recommendations"></a>Suggerimenti di disponibilità

Creare una farm ADFS con almeno due server per aumentare la disponibilità del servizio di ADFS.

Usare gli account di archiviazione diverso per ogni VM ADFS nella farm. Consente di questo approccio per garantire che un errore in un account di archiviazione non effettuare l'intera farm inaccessibile.

Creare set di disponibilità di Azure separata per ADFS e macchine virtuali WAP. Assicurarsi che siano almeno due macchine virtuali in ogni set. Ogni set di disponibilità deve avere almeno due domini di aggiornamento e due guasto.

Configurare i servizi di bilanciamento del carico per le macchine virtuali di ADFS e macchine virtuali WAP come indicato di seguito:

- Utilizzare un Azure bilanciamento del carico per consentire l'accesso esterno alle macchine virtuali WAP e un interno bilanciamento del carico per distribuire il carico nei server ADFS all'interno della farm ADFS.

- Passare solo il traffico nella porta 443 (HTTPS) ai server ADFS/WAP.

- Assegnare il bilanciamento del carico un indirizzo IP statico.

- Creare una verifica dell'integrità utilizzando il protocollo TCP anziché HTTPS. È possibile effettuare il ping la porta 443 per verificare il funzionamento di un server ADFS.

    >[AZURE.NOTE] Server ADFS usano il protocollo indicazione di nome Server (SNI), pertanto il tentativo di verifica utilizzando un endpoint HTTPS dal non riesce bilanciamento del carico.

- Aggiungere un record DNS *A* per il dominio per il servizio di bilanciamento del carico ADFS. 

    Specificare l'indirizzo IP del servizio di bilanciamento del carico e assegnarle un nome del dominio (ad esempio adfs.contoso.com). Questo è il nome per il quale client e server WAP accedere alla farm di server ADFS.

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza

Evitare l'esposizione diretta dei server ADFS a Internet. Server ADFS sono computer dominio in cui completo autorizzazione da concedere a token di sicurezza. Se un server ADFS è compromessa, un utente non autorizzato può inviare i token di accesso completo a tutte le applicazioni web e ai server federativi che sono protetti tramite ADFS. Se il sistema è necessario gestire le richieste provenienti da utenti esterni non necessariamente la connessione da siti attendibili partner, utilizzare server WAP per gestire le richieste. Per ulteriori informazioni, vedere [dove posizionare un Proxy del Server federativo][where-to-place-an-fs-proxy].

Server ADFS sul posto e i server WAP subnet separate con le proprie firewall. È possibile utilizzare le regole NSG per definire le regole del firewall. Se si richiede la protezione più completa, è possibile implementare un perimetro rafforzare la sicurezza all'interno di server con una coppia di subnet e NVAs, come descritto dal documento [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Si noti che tutti i firewall devono consentire il traffico sulla porta 443 (HTTPS).

Limitare l'accesso diretto ai server ADFS e WAP. Solo personale attrezzi dovrebbe essere possibile connettersi.

Non partecipare server WAP al dominio.

### <a name="adfs-installation-recommendations"></a>Raccomandazioni di installazione di ADFS

L'articolo [implementazione di una Farm di Server federativo] [ Deploying_a_federation_server_farm] vengono fornite istruzioni dettagliate per l'installazione e configurazione di ADFS. Prima di configurare il primo server ADFS nella farm, eseguire le attività seguenti:

1. Ottenere un certificato pubblicamente attendibile per l'esecuzione di autenticazione server. Il *nome del soggetto* deve contenere il nome per il quale i client accedono al servizio federativo. È possibile utilizzare il nome DNS registrato per il bilanciamento del carico, ad esempio, *adfs.contoso.com* (evitare l'utilizzo di nomi con caratteri jolly come **. contoso.com*, per motivi di sicurezza). Usare lo stesso certificato in macchine virtuali di server ADFS tutti. È possibile acquistare un certificato emesso da un'autorità di certificazione attendibile, ma se l'organizzazione utilizza Servizi certificati Active Directory è possibile creare il proprio. 

    Il *nome alternativo oggetto* viene utilizzato il servizio DRS per consentire l'accesso da dispositivi esterni. Deve trattarsi del modulo *enterpriseregistration.contoso.com*.

    Per ulteriori informazioni, vedere [ottenere e configurare un certificato SSL per ADFS][adfs_certificates].

2. Sul controller di dominio, generare una nuova chiave principale per il servizio di distribuzione di chiavi. Impostare il periodo di effettivo sia l'ora corrente meno 10 ore (questa configurazione riduce il ritardo che può verificarsi in la distribuzione e la sincronizzazione dei tasti all'interno del dominio). Questa operazione è necessaria per supportare la creazione di account del servizio di gruppo che vengono usate per eseguire il servizio ADFS. Il comando Powershell seguente viene illustrato un esempio di come procedere come segue:

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Aggiungere ogni server ADFS macchine Virtuali al dominio.

>[AZURE.NOTE] Per installare ADFS, controller di dominio se l'emulatore ruolo per il dominio deve essere in esecuzione e accessibili da macchine virtuali di ADFS.

### <a name="adfs-trust-recommendations"></a>Consigli ADFS attendibile

Stabilire l'attendibilità federazione tra l'installazione di ADFS e server federativi di qualsiasi organizzazioni partner. Configurare le attestazioni filtro e il mapping necessari. Questa procedura è necessario:

- Attrezzi personale **all'ogni organizzazione partner** per aggiungere un componente trust di terze parti per le applicazioni web accessibili tramite il server ADFS.

- Attrezzi per il personale **dell'organizzazione** per configurare l'attendibilità provider di attestazioni per abilitare il server ADFS considerare attendibile sulle attestazioni che forniscono organizzazioni partner.

- Attrezzi per il personale **dell'organizzazione** per configurare ADFS per passare reclami su applicazioni web dell'organizzazione.

    Per ulteriori informazioni, vedere [Definizione federazione dell'attendibilità][establishing-federation-trust].

Pubblicare applicazioni web dell'organizzazione e renderli disponibili per i partner esterni tramite preliminare tramite il server WAP. Per ulteriori informazioni, vedere [pubblicare applicazioni utilizzando autenticazione preliminare ADFS][publish_applications_using_AD_FS_preauthentication]

Si noti che ADFS supporta potenziare e trasformazione token. Azure Active Directory non è disponibile questa caratteristica. Con ADFS, quando si configura le relazioni di trust, è possibile:

- Configurare le trasformazioni di richiesta di rimborso per le regole di autorizzazione. Ad esempio, è possibile eseguire il mapping protezione gruppo da una rappresentazione utilizzata da un'organizzazione non Microsoft partner a un elemento che aggiunge possibile autorizzare all'interno dell'organizzazione.

- Trasformare sulle attestazioni da un formato a un altro. Ad esempio, è possibile eseguire il mapping da SAML 2.0 a SAML 1.1 se l'applicazione supporta solo sulle attestazioni SAML 1.1. 

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

È possibile usare SQL Server o Windows interno Database (ID) per contenere le informazioni sulla configurazione di ADFS. WID fornisce ridondanza base. Le modifiche vengono scritte direttamente a solo uno dei database ADFS cluster ADFS mentre gli altri server consente di mantenere aggiornati i database di replica pull. Utilizzo di SQL Server può fornire la ridondanza completo del database e disponibilità tramite failover cluster o che rispecchiano.

## <a name="security-considerations"></a>Considerazioni sulla protezione

ADFS utilizza il protocollo HTTPS, dunque, verificare che le regole NSG per subnet contenente il web livello richieste HTTPS licenza macchine virtuali. Queste richieste possono provenire da rete locale subnet contenente il livello di web, il livello aziendale, livello dati, rete Perimetrale privato, rete Perimetrale pubblico e subnet contenente i server ADFS.

È consigliabile utilizzare un set di rete dispositivi di rete che registra informazioni dettagliate sulle traffico in transito sul bordo della rete virtuale ai fini del controllo.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Le considerazioni seguenti riepilogate dal documento di [pianificare la distribuzione di ADFS][plan-your-adfs-deployment], inviare un punto di partenza per il ridimensionamento farm ADFS:

- Se si dispone di meno di 1000 utenti, non creare server ADFS dedicato, ma installare ADFS in ogni server aggiunge nel cloud (accertarsi di avere almeno due server consente di aggiungere, per mantenere la disponibilità). Creare un singolo server WAP.

- Se si dispone tra 1000 e 15000 utenti, creare due server ADFS dedicato e due dedicati WAP.

- Se si trovano tra gli utenti 15000 e 60000, creare tra tre e cinque server ADFS dedicato e almeno due dedicati WAP.

Tali cifre presuppongono che si usano due macchine virtuali di processori (D4_v2 Standard o migliore) per ospitare i server Azure.

Si noti che se si utilizza il Database interno di Windows per archiviare i dati di configurazione di ADFS, è limitato a otto server ADFS nella farm. Se si prevede che più, quindi usare SQL Server. Per ulteriori informazioni, vedere [Il ruolo del Database di configurazione ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Considerazioni sulla gestione

Attrezzi personale deve essere preparato per eseguire le operazioni seguenti:

- Gestione dei server federativi (la gestione della farm ADFS, gestione dei criteri di protezione nei server federativo e la gestione dei certificati usati da federation services).

- Gestione dei server WAP (la gestione della farm WAP, la gestione dei certificati WAP).

- Gestire applicazioni web (configurazione parti componente, i metodi di autenticazione e i mapping delle attestazioni).

- Eseguire il backup dei componenti ADFS.

## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio

[Microsoft System Center Management Pack per Active Directory Federation Services 2012 R2] [ oms-adfs-pack] fornisce preventivo e riattivare il monitoraggio della distribuzione di ADFS per il server federativo. Consente di controllare questo management pack:

- Eventi che il ADFS del servizio record nei registri eventi ADFS.

- Dati sulle prestazioni raccolti contatori ADFS. 

- Lo stato generale delle ADFS sistema e web applicazioni (parti componente) e fornisce gli avvisi per problemi critici e gli avvisi.

## <a name="solution-components"></a>Componenti della soluzione

Soluzione uno script di esempio, [Distribuisci ReferenceArchitecture.ps1][solution-script], è disponibile che è possibile utilizzare per implementare l'architettura di seguito i suggerimenti descritti in questo articolo. Questo script utilizza Gestione risorse Azure modelli. I modelli sono disponibili come un set di elementi costitutivi, ognuno dei quali esegua un'azione specifica, ad esempio creare un VNet o configurare un NSG. Scopo dello script è gestire la distribuzione dei modelli.

I modelli sono parametri, con i parametri contenuti nel file JSON separati. È possibile modificare i parametri in questi file per configurare la distribuzione in base alle proprie esigenze. Non è necessario modificare i modelli stessi. Si noti che non è possibile modificare gli schemi degli oggetti nei file di parametro.

Quando si modificano i modelli, creare oggetti che seguono le convenzioni di denominazione descritte in [Consigliati convenzioni di denominazione per le risorse di Azure][naming-conventions].

Soluzione di esempio crea e configura l'ambiente nel cloud che comprende la rete Perimetrale subnet e server, subnet ADFS e server, subnet proxy ADFS e server, aggiungere, livello web, livello aziendale e componenti di livello di accesso ai dati, gateway VPN e livello di gestione. Soluzione di esempio include anche una configurazione facoltativa per la creazione di un ambiente locale simulata.

Nelle sezioni seguenti vengono descrivono gli elementi di in locale e cloud configurazioni.

### <a name="on-premises-components"></a>Componenti locale

>[AZURE.NOTE] Questi componenti non sono lo stato attivo principale dell'architettura descritta in questo documento e vengono forniti semplicemente per assegnare un'opportunità per testare l'ambiente cloud in modo sicuro, piuttosto che utilizzare un ambiente di produzione reale. Per questo motivo, in questa sezione sono elencati solo i file di parametro chiave. È possibile modificare le impostazioni, ad esempio gli indirizzi IP o le dimensioni delle macchine virtuali, ma è consigliabile lasciare molti degli altri parametri invariati.

Questo ambiente comprende un insieme di strutture di Active Directory per un dominio denominato contoso.com. Il dominio contiene due server aggiunge con gli indirizzi IP 192.168.0.4 e 192.168.0.5. Questi due server anche eseguire il servizio DNS. L'account di amministratore locale in entrambe le macchine virtuali è denominato `testuser` con password `AweS0me@PW`. Inoltre, la configurazione configura automaticamente un gateway VPN per la connessione a VNet nel cloud. È possibile modificare la configurazione modificando i seguenti file JSON disponibili in [**parametri/locale**] [ on-premises-folder] cartella:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Questo file definisce lo spazio di indirizzi di rete per l'ambiente locale.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Il file contiene la configurazione per le macchine virtuali locale aggiunge servizi di hosting. Per impostazione predefinita, vengono create due macchine virtuali *v2 di DS3 Standard* .

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** e ** [connection.parameters.json][on-premises-connection-parameters]**. Questi file contengono le impostazioni per la connessione VPN al gateway VPN Azure nel cloud, tra cui la chiave condivisa da utilizzare per proteggere il traffico passando il gateway.

Gli altri file nella cartella contengono le informazioni di configurazione usate per creare il dominio locale utilizzando l'infrastruttura. Si usarli per installare somma, configurazione DNS, creare un insieme di strutture e configurare i siti di replica della foresta.

### <a name="cloud-components"></a>Componenti del cloud

Questi componenti il nucleo di questa architettura. [**Parametri/azure**] [ azure-folder] cartella contiene i file di parametro seguenti per la configurazione di questi componenti:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Questo file definisce struttura del VNet per macchine virtuali e altri componenti nel cloud. Include impostazioni, ad esempio il nome, spazio di indirizzi, subnet e gli indirizzi dei server DNS necessari. Si noti che gli indirizzi DNS illustrati nell'esempio seguente riferimento gli indirizzi IP dei server DNS locali e il server DNS Azure predefinito. Modificare questi indirizzi per fare riferimento a installazione DNS se non si sta utilizzando l'ambiente locale di esempio:

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. In questo file Configura macchine virtuali in esecuzione aggiunge nel cloud. La configurazione è costituito da due macchine virtuali. È consigliabile modificare il nome dell'utente amministratore e la password nel `virtualMachineSettings` sezione ed è facoltativamente possibile modificare le dimensioni di macchine Virtuali in base ai requisiti del dominio:

    Per ulteriori informazioni, vedere [Estensione Active Directory in Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [aggiungere-aggiunge-dominio-controller.parameters.json][add-adds-domain-controller-parameters]**. Il file contiene le impostazioni per la creazione del dominio CONTOSO che si estendono per il server aggiunge. Utilizza le estensioni personalizzate che stabilire il dominio e aggiungere i server aggiunge. A meno che non è necessario creare ulteriori server aggiunge (nel qual caso è necessario aggiungerle alla `vms` in forma di matrice), modificare i nomi da quello predefinito o per creare un dominio con un nome diverso non è necessario modificare il file.

- ** [bilanciatore del carico adfs.parameters.json] [loadbalancer-adfs-parameters] ** Il file contiene due set di parametri di configurazione. Il `virtualMachineSettings` sezione definisce macchine virtuali che ospitano il servizio ADFS nel cloud. Per impostazione predefinita, lo script crea due di queste macchine virtuali nello stesso set di disponibilità:

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    Il `loadBalancerSettings` sezione vengono fornite la descrizione del servizio di bilanciamento del carico per queste macchine virtuali. Bilanciamento del carico passa il traffico che viene visualizzata la porta 443 (HTTPS) a uno o l'altro macchine virtuali:

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-farm-dominio-join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Il file contiene le impostazioni usate per aggiungere i server ADFS al dominio CONTOSO. È necessario modificare questo file se sono stati creati altri server ADFS (aggiornare la `vms` matrice in questo caso), o è stato modificato il nome di dominio.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [first.parameters.json di farm adfs][adfs-farm-first-parameters]**, e ** [rest.parameters.json di farm adfs][adfs-farm-rest-parameters]**. Lo script utilizza le impostazioni in questi file per creare la farm di server ADFS. 

    Il file *gmsa.parameters.json* contiene le impostazioni per l'account di servizio gruppo gestita utilizzato dal servizio di ADFS. Se si desidera modificare il nome dell'account o il dominio, è possibile modificare il file.

    Il file *first.parameters.json di farm adfs* contiene le informazioni necessarie per creare la farm di server ADFS e aggiungere il primo server. Se è stato modificato il nome dell'account del servizio gruppo gestito o il dominio, è necessario aggiornare il file.

    Il file *rest.parameters.json di farm adfs* viene utilizzato per aggiungere altri server ADFS alla farm. Anche se è stato modificato il nome dell'account del servizio gruppo gestito o il dominio, è necessario aggiornare il file. Aggiornamento di `vms` matrice se sono stati creati altri server ADFS.

- ** [bilanciatore del carico adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Il file è simile in struttura e il contenuto del file *adfs.parameters.json bilanciatore del carico* . Contiene i dati per la creazione di server proxy ADFS e di bilanciamento del carico.

- ** [first.parameters.json di farm adfsproxy][adfsproxy-farm-first-parameters]**, e ** [rest.parameters.json di farm adfsproxy][adfsproxy-farm-rest-parameters]**. Lo script utilizza le impostazioni in questi file per creare la farm di server proxy ADFS. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Il file contiene le impostazioni usate per creare il gateway VPN Azure nel cloud usati per connettersi alla rete locale. È necessario modificare il `sharedKey` valore nel `connectionsSettings` in modo che corrispondano che il dispositivo VPN locale. Per ulteriori informazioni, vedere [implementazione di un'architettura di rete ibrido con Azure e VPN locale][hybrid-azure-on-prem-vpn].

- ** [private.parameters.json di rete perimetrale] [ dmz-private-parameters] ** e ** [public.parameters.json di rete perimetrale ] [ dmz-public-parameters] **. Questi file configurare in ingresso (pubblico) e in uscita (privati) lati delle macchine virtuali che costituiscono la rete Perimetrale, protezione dei server nel cloud. Per ulteriori informazioni su questi elementi e la configurazione, vedere [implementazione di una rete Perimetrale tra Azure e Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [json di web.parameters bilanciatore del carico][loadBalancer-web-parameters]**, ** [json di biz.parameters bilanciatore del carico][loadBalancer-biz-parameters]**, e ** [json di data.parameters bilanciatore del carico][loadBalancer-data-parameters]**. Questi file parametri contengono specifiche macchine Virtuali per i livelli di accesso web, business e dati e configurare servizi di bilanciamento del carico per ogni livello. Si tratta di macchine virtuali che ospitano i web App e database ed eseguono i carichi di lavoro di business per l'organizzazione. È possibile modificare le dimensioni e il numero di macchine virtuali in ogni livello in base alle proprie esigenze.

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Il file contiene la configurazione della casella ponticello/gestione macchine virtuali. È possibile solo ottenere l'accesso e accesso amministrativo alle macchine virtuali nel web, business e i livelli dati nella finestra di spostamento. Per impostazione predefinita, lo script crea una singola *Standard_DS1_v2* macchine Virtuali, ma è possibile modificare il file per creare macchine virtuali di ingrandire o aggiuntive se il carico di lavoro gestione è probabile che sia significativa.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

La soluzione prende in considerazione i prerequisiti seguenti:

- Si ha un abbonamento di Azure esistente in cui è possibile creare gruppi di risorse.

- Scaricato e installato build più recente di Azure Powershell. Vedere [qui] [ azure-powershell-download] per le istruzioni.

Per eseguire lo script che consente di distribuire la soluzione:

1. Spostare la cartella desiderata nel computer locale e creare sottocartelle seguenti:

    - Script

    - Script/parametri

    - Script/parametri/locale

    - Script/parametri/Azure

2. Scaricare [Distribuisci ReferenceArchitecture.ps1] [ solution-script] file nella cartella di script

3. Scaricare il contenuto di [parametri/locale] [ on-premises-folder] alla cartella script/parametri/locale:

4. Scaricare il contenuto di [parametri/azure] [ azure-folder] alla cartella script/parametri/Azure.

5. Modificare il file di distribuzione ReferenceArchitecture.ps1 nella cartella script e modificare le righe seguenti per specificare i gruppi di risorse che devono essere creati o utilizzati per contenere le risorse create dallo script:

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Modificare i file di parametro nelle cartelle di script/parametri/Azure e script/parametri/locale. Aggiornare i riferimenti di gruppo di risorse in questi file in modo che corrispondano i nomi dei gruppi di risorse assegnati alle variabili nel file ReferenceArchitecture.ps1 Distribuisci. La tabella seguente mostra i file di parametro fanno riferimento a quale gruppo di risorse. Si noti che i gruppi *ra-adfs carico di lavoro rg* *ra-adfs sicurezza rg*, *ra-adfs-aggiunge-rg*, *ra-adfs adfs rg*e *ra-adfs proxy rg* vengono utilizzati solo dello script di PowerShell e non si verificano nei file di parametro.

  	|Gruppo di risorse|File specifici parametro|
  	|--------------|--------------|
  	|Ra-adfs locale rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|Ra-adfs rete rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-with-OnPremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*due occorrenze*)

    Inoltre, impostare la configurazione per i locali e cloud componenti, come descritto nella sezione soluzione Components [componenti della soluzione].

7. Aprire una finestra di PowerShell di Azure, passare alla cartella script ed eseguire il comando seguente:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Sostituire `<subscription id>` con l'ID di abbonamento Azure.

    Per `<location>`, specificare un'area di Azure, ad esempio `eastus` o `westus`.

    Il `<mode>` parametro può avere uno dei seguenti valori:

    - `Onpremise`, per creare l'ambiente locale simulata.

    - `Infrastructure`, creare l'infrastruttura di VNet e passare casella nel cloud.

    - `CreateVpn`, creare gateway virtuali Azure e connettersi alla rete locale.

    - `AzureADDS`, per costruire macchine virtuali funge da server aggiunge, installare Active Directory a queste macchine virtuali e creare il dominio nel cloud.

    - `AdfsVm`Per creare le macchine virtuali ADFS e aggiungerli al dominio nel cloud.

    - `ProxyVm`Per creare il proxy ADFS macchine virtuali e aggiungerli al dominio nel cloud.

    - `Prepare`, che consente di eseguire tutte le attività precedente. **Questo è consigliabile se si sta creando una distribuzione completamente nuova e non si dispone di un'infrastruttura locale.**

    >[AZURE.NOTE] È anche possibile eseguire lo script con un `<mode>` parametro di `Workload` per creare il web, business e il livello di dati macchine virtuali e di rete. Il programma di installazione non è incluso come parte della `Prepare` modalità.

    Se si usa il `Prepare` opzione, lo script accetta alcune ore e termina con il messaggio *preparazione viene completata. Installare certificati per tutti i ADFS e proxy macchine virtuali.*

8.  Riavviare la casella ponticello (*vm1 ra-adfs-gestione* nel gruppo *ra-adfs sicurezza rg* ) per consentire le impostazioni di DNS rendere effettive.

9.  [Ottenere un certificato SSL per ADFS] [ adfs_certificates] e installare il certificato in macchine virtuali di ADFS. Si noti che sia possibile connettersi alle macchine virtuali di ADFS nella finestra di spostamento. Gli indirizzi IP sono *10.0.5.4* e *10.0.5.5*. Il nome utente predefinito è *contoso\testuser* con password *AweSome@PW*.

    >[AZURE.NOTE] I commenti in script Distribuisci ReferenceArchitecture.ps1 a questo punto istruzioni dettagliate per la creazione di un certificato di prova con la propria firma e autorità utilizzando il `makecert` comando. Tuttavia, non utilizzare i certificati generati da makecert in un ambiente di produzione.

10. Eseguire il seguente comando di Powershell per configurare la server farm ADFS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. Nella casella collegamento passare *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* per testare l'installazione di ADFS (potrebbe venire visualizzato un avviso di certificato, è possibile ignorare per questo test). Verificare che la pagina di accesso di Contoso Corporation visualizzata. Accedere come *contoso\testuser* con password *AweS0me@PW*.

12. Installare il certificato SSL sul proxy ADFS macchine virtuali. Gli indirizzi IP sono *10.0.6.4* e *10.0.6.5*.

13. Eseguire il seguente comando di Powershell per configurare il primo server proxy ADFS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Seguire le istruzioni visualizzate per lo script per testare l'installazione del primo server proxy ADFS.

15. Eseguire il seguente comando di Powershell per configurare il server di proxy ADFS prima secondo:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Seguire le istruzioni visualizzate per lo script per testare la configurazione di proxy ADFS completa.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Architettura della rete ibrido con Active Directory protetta"
