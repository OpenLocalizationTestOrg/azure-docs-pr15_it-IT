<properties
   pageTitle="Linee guida per la distribuzione di Windows Server Active Directory in macchine virtuali di Azure | Microsoft Azure"
   description="Se si conosce come distribuire servizi di dominio Active Directory e Active Directory Federation Services in locale, informazioni su come funzionano in macchine virtuali di Azure."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure

Questo articolo illustra le differenze tra la distribuzione Windows Server servizi di dominio (AD DS) e Active Directory Federation Services (ADFS) locale e distribuirle in macchine virtuali di Microsoft Azure.

## <a name="scope-and-audience"></a>Limitare l'ambito e gruppo di destinatari

L'articolo è destinato a quelle già verificati con la distribuzione di Active Directory locale. Vengono illustrate le differenze tra la distribuzione di Active Directory su reti virtuali di Microsoft Azure macchine virtuali/Azure e distribuzioni di Active Directory locale tradizionale. Macchine virtuali di Azure e Azure reti virtuali fanno parte di un infrastruttura-come-a-Service (IaaS) per le organizzazioni che offre la possibilità di sfruttare le risorse di elaborazione nel cloud.

Per coloro che non hanno familiarità con la distribuzione di Active Directory, vedere la [Guida alla distribuzione di Active Directory](https://technet.microsoft.com/library/cc753963) o [pianificare la distribuzione di ADFS](https://technet.microsoft.com/library/dn151324.aspx) in base alle esigenze.

In questo articolo si presuppone che il lettore sia familiarità con i concetti seguenti:

- Gestione e la distribuzione di Windows Server Active Directory
- Distribuzione e la configurazione del DNS per il supporto di un'infrastruttura di Windows Server Active Directory
- Gestione e la distribuzione di Windows Server AD FS
- Distribuire, configurare e gestire componente applicazioni di terze parti (siti Web e servizi web) che possono essere utilizzati i token di Windows Server AD FS
- Concetti generali macchina virtuale, ad esempio come configurare una macchina virtuale, dischi virtuali e reti virtuali

In questo articolo vengono evidenziati i requisiti per uno scenario di distribuzione ibrida in cui Windows Server Active Directory o AD FS sono parzialmente distribuito in locale e parzialmente distribuito in macchine virtuali di Azure. Il documento prima di tutto illustrate le differenze importanti tra che eseguono Windows Server Active Directory e ADFS su Azure macchine virtuali e locale e i fattori che influiscono sulla progettazione e distribuzione. Il resto della carta descritte le linee guida per ognuno dei punti di decisione in modo più dettagliato e applicare le linee guida per diversi scenari di distribuzione.

In questo articolo vengono illustrate la configurazione di [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), che è un servizio basato su REST che fornisce la gestione delle identità e funzionalità di controllo di accesso per applicazioni cloud. Azure Active Directory (Azure Active Directory) e Windows Server Active Directory vengono, tuttavia, coordinati per rendere disponibile una soluzione di gestione delle identità e accesso ibrido odierna IT ambienti e applicazioni moderne. Per capire le differenze e le relazioni tra Windows Server Active Directory e Azure Active Directory, tenere presente quanto segue:

1. È possibile eseguire Windows Server Active Directory nel cloud in macchine virtuali di Azure quando si usa Azure per estendere il data center locale nel cloud.
2. È possibile utilizzare Azure Active Directory per assegnare agli utenti il single sign-on per le applicazioni Software-come-a-servizio. Ad esempio Microsoft Office 365 utilizza la tecnologia e applicazioni in esecuzione su Azure o altre piattaforme cloud possono usarlo anche.
3. È possibile utilizzare Azure Active Directory (il servizio controllo di accesso) per consentire agli utenti di accedere con l'identità da Facebook, Google, Microsoft e altri provider di identità alle applicazioni ospitate nel cloud o locale.

Per ulteriori informazioni su queste differenze, vedere [Identità Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Risorse correlate

Si può scaricare ed eseguire la [Valutazione di Azure macchina virtuale](https://www.microsoft.com/download/details.aspx?id=40898). Controllare l'ambiente locale e generare un report personalizzato in base alle indicazioni disponibili in questo argomento che consentono di eseguire la migrazione dell'ambiente in Azure la valutazione automaticamente.

È consigliabile esaminare anche innanzitutto le esercitazioni, guide e video, che vengono trattati i seguenti argomenti:

- [Configurare una rete virtuale basata solo su Cloud nel portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Configurare una connessione VPN da sito nel portale di Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Installare una nuova foresta di Active Directory in una rete virtuale Azure](active-directory-new-forest-virtual-machine.md)
- [Installare un controller di dominio Active Directory replica in Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IaaS professionisti IT: nozioni fondamentali (01) macchina virtuale](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IaaS professionisti IT: (05) reti virtuali creazione e alla connettività tra locale](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduzione

I requisiti fondamentali per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure differiscono poco da distribuirlo in macchine virtuali locale (e in alcuni casi, computer fisici). Ad esempio, nel caso di Windows Server Active Directory, se il dominio controller di cui distribuire in macchine virtuali di Azure sono repliche in un oggetto esistente locale aziendale/delle strutture, quindi la distribuzione di Azure può essere considerata ampiamente nello stesso modo come potrebbero considerare qualsiasi altro sito di Windows Server Active Directory aggiuntivo. Vale a dire subnet devono essere definite in Windows Server Active Directory, un sito creato, subnet collegato al sito e connesso ad altri siti con collegamenti a siti appropriati. Esistono tuttavia alcune differenze comuni a tutte le distribuzioni Azure e alcuni che variano in base allo scenario di distribuzione specifico. Differenze fondamentali due sono le seguenti:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure macchine virtuali potrebbe essere necessario connettività alla rete aziendale in locale.

La connessione di Azure macchine virtuali di tornare a una rete aziendale locale richiede virtuali Azure, che includono un da sito o sito-to-point virtual private network (VPN) componente grado di connettersi facilmente macchine virtuali di Azure e computer locale. Questo componente VPN potrebbe anche consentire a computer membri del dominio locale accedere a un dominio di Windows Server Active Directory cui controller di dominio sono ospitati esclusivamente in macchine virtuali di Azure. È importante tenere presente però che se la connessione VPN non riesce, l'autenticazione e altre operazioni che dipendono da Windows Server Active Directory anche non riuscirà. Quando gli utenti potranno a Accedi utilizzando esistente cache delle credenziali, tutti peer-to-peer o tentativi di autenticazione client e server per il quale i ticket devono ancora rilasciato o avere diventano obsoleti avrà esito negativo.

Per una dimostrazione video e un elenco di esercitazioni pratiche, tra cui [configurare una connessione VPN da sito nel portale di Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md), vedere [Virtuali](http://azure.microsoft.com/documentation/services/virtual-network/) .

> [AZURE.NOTE] È inoltre possibile distribuire Active Directory di Windows Server in una rete virtuale Azure che non dispone di connettività con una rete locale. La in questo argomento, tuttavia, si presuppone che viene utilizzata una rete virtuale Azure perché offre funzionalità essenziali per Windows Server di indirizzi IP.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Indirizzi IP statici devono essere configurati con PowerShell Azure.

Indirizzi dinamici vengono assegnati per impostazione predefinita, ma utilizzano il cmdlet Set-AzureStaticVNetIP per assegnare un indirizzo IP statico invece. Che consente di impostare un indirizzo IP statico che verrà mantenuti tramite correzione servizio e macchine Virtuali arresto/riavvia. Per ulteriori informazioni, vedere [indirizzo IP statico interno per macchine virtuali](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termini e le definizioni

Di seguito è un elenco non esaustivo delle condizioni di licenza per diverse tecnologie Azure cui vengono fatto riferimento in questo articolo.

- **Azure macchine virtuali**: IaaS l'offerta di Azure che consente di distribuire macchine virtuali in esecuzione quasi tutti in genere carico di lavoro server in locale.

- **Azure virtuali**: il servizio di social network in Azure che consente di creare e gestire reti virtuali in Azure e un collegamento in modo sicuro ai propri clienti locale un'infrastruttura di rete utilizzando una rete privata virtuale (VPN).

- **Indirizzo IP virtuale**: un indirizzo IP esposto a internet non è associato a una scheda specifica di computer o in rete. Servizi cloud viene assegnato un indirizzo IP virtuale per la ricezione di traffico di rete che viene reindirizzato a una macchina virtuale Azure. Un indirizzo IP virtuale è una proprietà di un servizio cloud che può contenere uno o più macchine virtuali Azure. Si noti inoltre che una rete virtuale Azure può contenere uno o più servizi cloud. Indirizzi IP virtuali offrono funzionalità di bilanciamento del carico native.

- **Indirizzo IP dinamico**: questo è l'indirizzo IP che è solo interno. Questo deve essere configurato come un indirizzo IP statico (utilizzando il cmdlet Set-AzureStaticVNetIP) per le macchine virtuali che ospitano i ruoli del server di controller di dominio/DNS.

- **Servizio correzione**: il processo in cui Azure restituisce automaticamente un servizio a uno stato in esecuzione nuovamente dopo rileva che il servizio non è riuscita. Servizio correzione corrisponde a uno degli aspetti di Azure che supporta la disponibilità e adattabilità. Mentre poco probabile, il risultato seguendo un servizio correzione incidente per un controller di dominio in esecuzione in una macchina virtuale è simile a un riavviare il computer non pianificato, ma ha alcuni effetti:

 - Scheda di rete virtuale nella macchina virtuale verrà modificato
 - L'indirizzo MAC della scheda di rete virtuale cambierà
 - ID processore/CPU della macchina virtuale verrà modificato
 - La configurazione IP della scheda di rete virtuale, non cambiano, purché la macchina virtuale è collegata a una rete e l'indirizzo IP di Virtual Machine è statico.

 Nessuno di questi comportamenti influisce su Windows Server Active Directory perché non ha alcuna dipendenza l'indirizzo MAC o un ID processore/CPU e tutte le distribuzioni di Windows Server Active Directory in Azure consigliate per l'esecuzione in una rete virtuale Azure come descritto sopra.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>È consigliabile virtualizzazione controller di dominio Active Directory di Windows Server?

La distribuzione di Active Directory controller di Windows Server in macchine virtuali di Azure sono soggette le stesse linee guida per l'esecuzione di controller di dominio locale in un computer virtuale. Esecuzione virtualizzato controller di dominio è norma attendibile come linee guida per il backup e ripristino controller di dominio sono seguite. Per ulteriori informazioni sui vincoli e linee guida per l'esecuzione virtualizzato controller di dominio, vedere [Esecuzione Hyper-V dei controller di dominio](https://technet.microsoft.com/library/dd363553).

Hypervisor forniscono o trivialize tecnologie che possono causare problemi relativi a molti sistemi distribuiti, tra cui Windows Server Active Directory. Ad esempio, in un server fisico, è possibile duplicare un disco o utilizzare metodi non supportati per ripristinare lo stato di un server, incluso l'utilizzo di SAN e così via, ma farlo in un server fisico è molto più difficile del ripristino dello snapshot di una macchina virtuale in un hypervisor. Azure offre funzionalità che possono causare la stessa condizione indesiderata. Ad esempio, non è necessario copiare file disco rigido virtuale dei controller di dominio anziché esecuzione di backup regolari poiché sul ripristino di utenti può provocare una situazione simile all'utilizzo delle funzionalità di ripristino snapshot.

Ad esempio ripristini introducono bolle USN che possono comportare stati definitivamente divergenti tra controller di dominio. Ad esempio, che possono causare problemi:

- Oggetti in sospeso
- Password non coerente
- Valori degli attributi non coerente
- Schemi non corrispondenti se lo schema di schema verrà ripristinato

Per ulteriori informazioni su come vengono applicati anche ai controller di dominio, vedere [USN e il ripristino USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

A partire da Windows Server 2012, [garanzie aggiuntive sono incorporati in Active Directory](https://technet.microsoft.com/library/hh831734.aspx). Queste garanzie proteggere controller di dominio virtualizzato dai problemi sopra, a condizione che la piattaforma hypervisor sottostante supporti GenerationID macchine Virtuali. Azure supporta macchine Virtuali GenerationID, ossia controller di dominio che eseguono Windows Server 2012 o versioni successive su Azure macchine virtuali di avere le garanzie aggiuntive.

> [AZURE.NOTE] È necessario chiudere e riavviare una macchina virtuale che esegue il ruolo di controller di dominio in Azure all'interno del sistema operativo guest invece di usare l'opzione di **Arresto** nel portale di classica Azure. Oggi, tramite il portale classico arrestare una macchina virtuale fa sì che la macchina virtuale essere rilasciato. Una macchina virtuale deallocata ha il vantaggio di non richiede in base alle tariffe, ma reimposta inoltre macchine Virtuali-GenerationID, ovvero indesiderato per un controller di dominio. Quando si reimposta la macchina virtuale GenerationID, ID del database di Active Directory viene reimpostato, viene eliminato il pool RID e SYSVOL è contrassegnato come non attendibile. Per ulteriori informazioni, vedere [Introduzione alla virtualizzazione di servizi di dominio Active Directory (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) e [In modo sicuro che il servizio virtualizzazione](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Perché distribuire Windows Server Active Directory in macchine virtuali di Azure?

Molti scenari di distribuzione di Windows Server Active Directory sono ideali per la distribuzione come macchine virtuali in Azure. Si supponga ad esempio, che si dispone di una società europei che richiede l'autenticazione degli utenti in un percorso remoto in Asia. La società non distribuita in precedenza Windows Server Active Directory controller di dominio in Asia a causa di costo per distribuire competenze loro e limitata per gestire i post-distribuzione server. Di conseguenza, le richieste di autenticazione da Asia sono gestite dalla controller di dominio in Europa con i risultati non ottimali. In questo caso, è possibile distribuire un controller di dominio in una macchina virtuale specificata devono essere eseguiti in Data Center Azure in Asia. Associare tale controller di dominio a una rete virtuale Azure che sia connesso direttamente alla posizione remota miglioreranno l'autenticazione.

Azure è anche adatto come sostituto ai siti di emergenza in caso contrario costosa ripristino (). Il relativamente basso costo di hosting di un numero limitato di controller di dominio e una singola rete virtuale in Azure rappresenta un'alternativa interessante.

Infine, si desidera distribuire un'applicazione di rete in Azure, ad esempio SharePoint richiede Windows Server Active Directory, ma non ha alcuna dipendenza in rete locale o Windows Server Active Directory della società. In questo caso, la distribuzione di un insieme di strutture sono isolati in Azure incontrarsi di SharePoint è ottimale requisiti del server. Di nuovo la distribuzione delle applicazioni di rete che richiedono la connettività di rete locale e Active Directory aziendale è anche possibile.

> [AZURE.NOTE] Poiché fornisce una connessione di livello 3, il componente VPN che fornisce la connettività tra una rete virtuale Azure e una rete locale inoltre possibile abilitare server membri che eseguono locale per sfruttare controller di dominio che eseguono come Azure macchine virtuali di Azure rete virtuale. Ma se non è disponibile la rete VPN, le comunicazioni tra computer in locale e controller di dominio basate su Azure non funzionerà, risultante in vari altri errori e l'autenticazione.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Contrasti tra la distribuzione di controller di dominio Active Directory di Windows Server in macchine virtuali di Azure e locale

- Per ogni scenario di distribuzione di Windows Server Active Directory che include più di una macchina virtuale singola, è necessario utilizzare una rete virtuale Azure per la coerenza indirizzo IP. Si noti che questa guida presuppone che controller di dominio sono in esecuzione in una rete virtuale Azure.

- Come con controller di dominio locale, sono consigliabile indirizzi IP statici. Un indirizzo IP statico può essere configurato solo tramite PowerShell Azure. Per ulteriori informazioni, vedere [l'indirizzo IP statico interno per macchine virtuali](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Se si dispone di monitoraggio sistemi o altre soluzioni di controllo per la configurazione di indirizzo IP statica all'interno del sistema operativo guest, è possibile assegnare lo stesso indirizzo IP statico per le proprietà della scheda di rete di macchina virtuale. Ma tenere presente che la scheda di rete verrà ignorata se la macchina virtuale subisca servizio correzione o arresta nel portale di classica e ha rilasciato il relativo indirizzo. In tal caso, sarà necessario reimpostare l'indirizzo IP statico all'interno del guest.

- Distribuzione di macchine virtuali in una rete virtuale non implicano (o richiedono) connettività tornare a una rete locale. la rete virtuale consente semplicemente tale possibilità. È necessario creare una rete virtuale per la comunicazione privata tra Azure e la rete locale. È necessario distribuire un endpoint VPN alla rete locale. La connessione VPN viene aperto da Azure alla rete locale. Per ulteriori informazioni, vedere [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md) e [configurare una connessione VPN da sito nel portale di Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Un'opzione per [creare una connessione VPN punto al sito](../vpn-gateway/vpn-gateway-point-to-site-create.md) è disponibile per la connessione di singoli computer basato su Windows direttamente a una rete virtuale Azure.

- Indipendentemente dal fatto che si crea un virtuale rete o meno, in base alle tariffe Azure per il traffico in uscita, ma non in ingresso. Varie scelte di progettazione di Windows Server Active Directory possono influire sulla quantità il traffico in uscita viene generato da una distribuzione. In (controller), ad esempio, la distribuzione di un controller di dominio di sola lettura limita il traffico in uscita in quanto non replica in uscita. Ma la decisione di distribuire un controller deve essere valutata a fronte la necessità di eseguire operazioni di scrittura su controller di dominio e la [compatibilità](https://technet.microsoft.com/library/cc755190) con le applicazioni e servizi nel sito con lettura. Per ulteriori informazioni sulle spese di traffico, vedere [Azure prezzi in immediatamente](http://azure.microsoft.com/pricing/).

- Mentre si è completata controllare quali server risorse da utilizzare per macchine virtuali in locale, ad esempio RAM, disco dimensioni e così via, in Azure è necessario selezionare da un elenco di formati server preconfigurato. Per un controller di dominio, è necessario un disco dati oltre il disco di sistema operativo per archiviare il database di Windows Server Active Directory.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>È possibile distribuire Windows Server AD FS in macchine virtuali di Azure?

Sì, è possibile distribuire Windows Server AD FS in macchine virtuali di Azure e le [procedure consigliate per la distribuzione di ADFS](https://technet.microsoft.com/library/dn151324.aspx) locale vengono applicate alla distribuzione di ADFS in Azure. Ma alcune procedure consigliate come il bilanciamento del carico e disponibilità tecnologie oltre cosa ADFS offre stesso. Si deve essere forniti da infrastruttura. Di seguito descritte alcune di queste procedure consigliate per scoprire come può essere ottenuti utilizzando macchine virtuali di Azure e una rete virtuale Azure.

1. **Esporre il server di servizio token () security direttamente a Internet.**

    È importante perché il servizio token di sicurezza rilascia token di sicurezza. Di conseguenza, servizio token di sicurezza, ad esempio server ADFS devono essere considerati con lo stesso livello di protezione come controller di dominio. Se un servizio token di sicurezza è compromessa, gli utenti malintenzionati hanno la possibilità di rilasciare il token di accesso potenzialmente contenente reclami relativi a loro scelta componente applicazioni di terze parti e altri server servizio token di sicurezza ritenuti attendibili organizzazioni.

2. **Distribuire controller di dominio Active Directory per tutti i domini utente nella stessa rete server ADFS.**

    Server ADFS annunci consente l'autenticazione degli utenti di servizi di dominio Active Directory. È consigliabile distribuire controller di dominio nella stessa rete server ADFS. In questo modo la continuità aziendale nel caso in cui il collegamento tra la rete Azure e la rete locale viene interrotto e consente di migliorare le prestazioni per gli account di accesso e latenza inferiore.

3. **Distribuire più nodi di ADFS per disponibilità e bilanciamento del carico.**

    Nella maggior parte dei casi, l'errore di un'applicazione che consente di ADFS è accettabile perché le applicazioni che richiedono token di sicurezza vengono spesso strategica. Di conseguenza, e perché ADFS trova nel percorso critico per l'accesso alle applicazioni critiche, il servizio di ADFS deve essere disponibilità in server ADFS e proxy ADFS. Per ottenere una distribuzione di richieste, servizi di bilanciamento del carico vengono in genere distribuiti che precede il proxy ADFS Active Directory e i server ADFS.

4. **Distribuire uno o più nodi Proxy di applicazione Web per l'accesso a internet.**

    Quando gli utenti devono accedere alle applicazioni protette dal servizio ADFS, è necessario che il servizio di ADFS sia disponibile da internet. Questa operazione viene eseguita per la distribuzione del servizio Proxy di applicazione Web. Per distribuire più di un nodo ai fini della disponibilità e il bilanciamento del carico si consiglia.

5. **Limitare l'accesso dei nodi di Proxy di applicazione Web alle risorse di rete interna.**

    Per consentire agli utenti esterni di accedere AD FS da internet, è necessario distribuire i nodi Proxy di applicazione Web (o Proxy ADFS nelle versioni precedenti di Windows Server). I nodi di proxy applicazione Web sono esposte direttamente a Internet. Non sono necessarie per essere aggiunti a un dominio e è necessario accedere solo ai server ADFS porte TCP 80 e 443. Si consiglia di blocco di comunicazione per tutti gli altri computer (in particolare controller di dominio).

    Si tratta in genere ottenute locale tramite una rete Perimetrale. Firewall utilizzano una modalità di proprietà consentite operativa per impedire il traffico di rete Perimetrale alla rete locale (vale a dire solo il traffico da determinati indirizzi IP e porte specificato è consentito e tutti gli altri il traffico viene bloccato).

Il diagramma seguente viene illustrato un tradizionale locale la distribuzione di ADFS.

![Diagramma di una distribuzione di Active Directory Federation Services locale tradizionale](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Tuttavia, poiché Azure non fornisce nativo, funzionalità complete firewall, altre opzioni necessario essere usata per limitare il traffico. Nella tabella seguente mostra ciascuna opzione e relativi vantaggi e svantaggi.

| Opzione | Vantaggi offerti dalle | Svantaggio |
| ------ | --------- | ------------ |
| [ACL di rete Azure](virtual-networks-acl.md) | Meno costosa e più semplice configurazione iniziale | Configurazione di rete aggiuntive ACL obbligatorio se eventuali nuove macchine virtuali vengono aggiunti alla distribuzione |
| [Firewall barracuda gas](https://www.barracuda.com/products/ngfirewall) | Modalità di proprietà consentite dell'operazione e non richiede alcuna configurazione ACL di rete | Aumento dei costi e complessità per la configurazione iniziale |

La procedura generale per la distribuzione di ADFS in questo caso è i seguenti:

1. Creare una rete virtuale con la connettività tra locale, utilizzando una rete VPN o [ExpressRoute](http://azure.microsoft.com/services/expressroute/).

2. Distribuire controller di dominio della rete virtuale. Questo passaggio è facoltativo, ma consigliato.

3. Distribuire dominio server ADFS della rete virtuale.

4. Creare un [set di bilanciamento del carico interno](http://azure.microsoft.com/blog/internal-load-balancing/) che include i server ADFS e utilizza un nuovo indirizzo IP privato all'interno della rete virtuale (un indirizzo IP dinamico).

  1. Aggiornare il DNS per creare il FQDN in modo che puntino all'indirizzo IP privato (dinamico) del set di bilanciamento del carico interno.

5. Creare un servizio cloud (o una rete virtuale separata) per i nodi Proxy di applicazione Web.

6. Distribuire i nodi Proxy di applicazione Web nel servizio cloud o virtuali

  1. Creare un set di bilanciamento del carico esterno che include i nodi Proxy di applicazione Web.

  2. Aggiornare il nome DNS esterno (FQDN) in modo che puntino al cloud servizio indirizzo IP pubblico (all'indirizzo IP).

  3. Configurare i proxy ADFS per usare il nome di dominio completo che corrisponde al set di bilanciamento del carico interno per il server ADFS.

  4. Aggiornare i siti web basata sulle attestazioni per usare il FQDN esterno per i provider di attestazioni.

7. Limitare l'accesso tra Proxy di applicazione Web a qualsiasi computer della rete virtuale ADFS.

Per limitare il traffico, il set di bilanciamento del carico per servizio di bilanciamento del carico interno Azure deve essere configurato per il traffico solo per le porte TCP 80 e 443 e tutto il traffico all'indirizzo IP dinamico interno del set di bilanciamento del carico viene eliminato.

![Diagramma di ADFS ACL di rete con TCP 443 + 80 consentita.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Il traffico ai server ADFS sarebbe consentito solo con i seguenti:

- Servizio di bilanciamento del carico interno Azure.
- L'indirizzo IP di amministratore di rete locale.

> [AZURE.WARNING] La struttura deve impedire nodi di un Proxy di applicazione Web di raggiungere altre macchine virtuali di Azure virtuali o tutti i percorsi di rete locale. Che può essere eseguita configurando le regole firewall accessorio locale per le connessioni di strada o il dispositivo VPN per le connessioni VPN da sito.

Uno svantaggio di questa opzione è necessario configurare la rete ACL per più dispositivi, inclusi di bilanciamento del carico interno, il server ADFS e qualsiasi altro server che venga aggiunta alla rete virtuale. Se qualsiasi dispositivo viene aggiunto alla distribuzione senza configurare ACL per limitare il traffico di rete, rischio può essere l'intera distribuzione. Se gli indirizzi IP dei nodi del Proxy di applicazione Web cambiano mai, necessario reimpostare la rete ACL (ovvero che i proxy devono essere configurati per utilizzare [indirizzi IP dinamici statici](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS su Azure con rete ACL.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

È inoltre possibile utilizzare l'accessorio [Barracuda gas Firewall](https://www.barracuda.com/products/ngfirewall) per controllare il traffico tra server proxy ADFS e i server ADFS. Questa opzione è conforme alle procedure consigliate per la sicurezza e disponibilità e richiede meno amministrazione dopo l'installazione e configurazione iniziali perché accessorio Barracuda gas Firewall offre una modalità di proprietà consentite dell'amministrazione firewall e può essere installato direttamente in una rete virtuale Azure. Che non è più necessario configurare ACL di rete ogni volta che viene aggiunto un nuovo server per la distribuzione. Ma questa opzione consente di aggiungere costi e la complessità di distribuzione iniziale.

In questo caso, due reti virtuali vengono distribuite anziché uno. È necessario chiamate VNet1 e VNet2. VNet1 contiene i proxy e VNet2 contiene la STSs e la connessione di rete alla rete aziendale. VNet1 è pertanto fisica (nonostante praticamente) isolate da VNet2 e a sua volta, dalla rete aziendale. VNet1 quindi sia connesso a VNet2 utilizzando la tecnologia tunneling speciale come trasporto indipendentemente dalla rete architettura (TINA). Tunnel TINA è collegato a ciascuna delle reti virtuali mediante un firewall Barracuda gas, ovvero una Barracuda su ciascuna delle reti virtuali.  Per disponibilità, si consiglia di distribuire due barracuda ogni rete virtuale. uno attivare, altri passivo. Offrono complessi le funzionalità che consente di simulare il funzionamento di una rete Perimetrale tradizionale in locale in Azure.

![ADFS su Azure con firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Per ulteriori informazioni, vedere [ADFS: estendere un'applicazione front-end locale attestazioni a Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>In alternativa alla distribuzione di ADFS se l'obiettivo è Office 365 Single Sign-on solo

In alternativa alla distribuzione di Active Directory è completamente ADFS se l'obiettivo è solo per consentire di accesso di per Office 365. In questo caso, è possibile semplicemente distribuire DirSync con sincronizzazione password in locale e ottenere lo stesso risultato con complessità della distribuzione minime perché questo approccio non richiede ADFS o Azure.

Nella tabella seguente contiene un confronto di funzionano dei processi di accesso con e senza la distribuzione di ADFS.

| Office 365 single sign-on con ADFS e DirSync | Office 365 stesso sign-on mediante DirSync + la sincronizzazione delle Password |
| ------------- | ------------- |
| 1. l'utente accede a una rete aziendale e viene autenticato per Windows Server Active Directory. | 1. l'utente accede a una rete aziendale e viene autenticato per Windows Server Active Directory. |
| 2. l'utente tenta di accedere a Office 365 (mi @contoso.com). | 2. l'utente tenta di accedere a Office 365 (mi @contoso.com). |
| 3. office 365 reindirizza l'utente di Azure Active Directory. | 3. office 365 reindirizza l'utente di Azure Active Directory. |
| 4. poiché Azure Active Directory non è possibile autenticare l'utente e comprende che esiste un trust con ADFS in locale, reindirizza l'utente ADFS. | 4. azure Active Directory non è possibile accettare ticket Kerberos direttamente ed è presente alcuna relazione di attendibilità in modo che richiede l'immissione di credenziali. |
| 5. l'utente invia un ticket Kerberos al servizio token di sicurezza ADFS Active Directory. | 5. l'utente immette la stessa password locale e Azure Active Directory le convalida in base al nome utente e password che è stata sincronizzata DirSync. |
| 6. ADFS Trasforma i ticket Kerberos per il formato di token necessari/sulle attestazioni e reindirizza l'utente Azure Active Directory. | 6. azure Active Directory reindirizza l'utente a Office 365. |
| 7. l'utente viene autenticato in Azure Active Directory (si verifica un'altra trasformazione). |  7. l'utente può accedere a Office 365 e OWA utilizzando il token di Azure Active Directory. |
| 8. azure Active Directory reindirizza l'utente a Office 365. |  |
| 9. l'utente automaticamente sottoscritto a Office 365. |  |

In Office 365 con DirSync con uno scenario di sincronizzazione di password (nessun AD FS), il single sign-on viene sostituito da "stesso sign-on" in "stessa" significa che gli utenti devono immettere nuovamente le stesse credenziali locale quando si accede a Office 365. Si noti che è possano ricordati questi dati con il browser dell'utente per ridurre le successive istruzioni.

### <a name="additional-food-for-thought"></a>Lavoro per alimenti aggiuntivo

- Se si distribuisce un proxy ADFS in un computer virtuale Azure, è necessaria una connessione al server ADFS. Se sono in locale, è consigliabile ottimizzare la connessione VPN da sito fornita da virtuali per consentire ai nodi Proxy di applicazione Web comunicare con i server ADFS.

- Se si distribuisce un server ADFS in un computer virtuale Azure, è necessaria la connettività a controller di dominio Active Directory di Windows Server, archivi attributo e database di configurazione e può richiedere un ExpressRoute o una connessione VPN da sito tra la rete virtuale Azure e la rete locale.

- In base alle tariffe vengono applicate a tutto il traffico da Azure macchine virtuali (traffico in uscita). Se il costo è il fattore di Guida, è consigliabile per distribuire i nodi Proxy di applicazione Web su Azure, lasciare l'ADFS server locale. Se il server ADFS sono distribuiti in macchine virtuali Azure anche, costi aggiuntivi dovrà essere sostenuti per l'autenticazione utenti locali. Il traffico in uscita costo indipendentemente dal fatto che attraversa il ExpressRoute o la connessione da sito VPN.

- Se si decide di usare le funzionalità di disponibilità dei server ADFS di bilanciamento del carico di server nativa di Azure, si noti che il bilanciamento del carico fornisce le ricerche che vengono utilizzate per determinare lo stato di macchine virtuali all'interno del servizio cloud. In caso di macchine virtuali Azure (anziché ruoli web o lavoro), è necessario utilizzare una ricerca personalizzata poiché l'agente che risponde alle ricerche predefinita non è presenta in macchine virtuali di Azure. Per semplicità, è possibile utilizzare una verifica TCP personalizzata, è necessario solo stabilire che una connessione TCP (un segmento SYN TCP inviati e risposto a con un segmento TCP SYN ACK) per determinare l'integrità del computer virtuale. È possibile configurare la ricerca personalizzata per l'utilizzo di qualsiasi porta TCP a cui macchine virtuali sono attivamente in attesa.

> [AZURE.NOTE] Computer che necessitano per esporre lo stesso set di porte direttamente a Internet (ad esempio, le porte 80 e 443) non è possibile condividere lo stesso servizio cloud. È pertanto consigliabile creare un'area dedicata servizi per i server ADFS di Windows Server Active Directory per evitare potenziali si sovrappone tra i requisiti di porta per un'applicazione e Windows Server AD FS.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

La sezione seguente illustra gli scenari di distribuzione comune per attirare l'attenzione su considerazioni importanti che devono essere preso in considerazione. Ogni scenario include collegamenti a ulteriori informazioni sulle decisioni e fattori da prendere in considerazione.

1. [Active Directory: Distribuire un'applicazione che supportano AD DS e non richiede la connettività di rete aziendale](#BKMK_CloudOnly)

    Ad esempio, un servizio di SharePoint esposto a Internet è distribuito in un computer virtuale Azure. L'applicazione ha alcuna dipendenza risorse della rete aziendale. L'applicazione richiedono Windows Server Active Directory, ma non richiede di Active Directory aziendale di Windows Server.

2. [ADFS: Estendere un'applicazione front-end locale attestazioni a Internet](#BKMK_CloudOnlyFed)

    Ad esempio, un'applicazione di attestazioni che è stati distribuiti correttamente in locale e utilizzato per gli utenti aziendali deve diventare accessibili da Internet. L'applicazione è necessario accedere direttamente tramite Internet, business partner usando le proprie identità aziendale e per gli attuali utenti aziendali.

3. [Active Directory: Distribuire un'applicazione di Windows Server Active Directory-tenere presente che richiede una connessione alla rete aziendale](#BKMK_HybridExt)

    Ad esempio, un'applicazione con supporto LDAP che supporta l'autenticazione integrata di Windows e utilizza Windows Server Active Directory come un archivio per i dati di configurazione e il profilo utente viene distribuita in un computer virtuale Azure. È utile per l'applicazione sfruttare esistente DS Windows Server Active Directory aziendale e fornire il single sign-on. L'applicazione non è attestazioni.

### <a name="BKMK_CloudOnly"></a>1. Active Directory: Distribuire un'applicazione che supportano AD DS e non richiede la connettività di rete aziendale

![Distribuzione di Active Directory basata solo su cloud](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figura 1**

#### <a name="description"></a>Descrizione

SharePoint viene distribuito in un computer virtuale Azure e l'applicazione ha alcuna dipendenza risorse della rete aziendale. L'applicazione richiedono Windows Server Active Directory ma non *non* richiedono DS Windows Server Active Directory aziendale. Non Kerberos federati trust sono necessari o poiché gli utenti self-provisioning tramite l'applicazione in Windows Server Active directory Active Directory del dominio ospitato anche nel cloud in macchine virtuali di Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerazioni scenario e come vengono applicate le aree di tecnologia per lo scenario

- [La topologia della rete](#BKMK_NetworkTopology): creare una rete virtuale Azure senza connessione tra locale (noto anche come da sito connectivity).

- [Configurazione di distribuzione cc](#BKMK_DeploymentConfig): distribuire un nuovo controller di dominio in un nuovo dominio singolo, Windows Server Active Directory insieme di strutture. Devono essere distribuito insieme al server DNS di Windows.

- [La topologia del sito di Windows Server Active Directory](#BKMK_ADSiteTopology): utilizzare il sito di Windows Server Active Directory predefinito (tutti i computer sarà nel nome del sito di primo predefinita).

- [Indirizzi IP e DNS](#BKMK_IPAddressDNS):

 - Impostare un indirizzo IP statico per il controller di dominio utilizzando il cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Installare e configurare Windows Server DNS nei controller di dominio in Azure.
 - Configurare le proprietà di rete virtuale con il nome e indirizzo IP del macchine Virtuali che ospita i ruoli server controller di dominio e DNS.

- [Catalogo globale](#BKMK_GC): primo controller di dominio dell'insieme di strutture deve essere un server di catalogo globale. Altro controller dovrebbe essere configurato anche come cataloghi poiché un insieme di strutture singolo dominio catalogo globale non richiede ulteriori operazioni del controller di dominio.

- [Posizione del database di Windows Server Active Directory e SYSVOL](#BKMK_PlaceDB): aggiungere un disco dati controller di dominio per archiviare database Windows Server Active Directory, i registri e SYSVOL sia in esecuzione come macchine virtuali di Azure.

- [Eseguire il backup e ripristino](#BKMK_BUR): determinare nel punto in cui si desidera memorizzare i backup dello stato di sistema. Se necessario, aggiungere un altro disco di dati per la macchina virtuale controller di dominio per archiviare i backup.

### <a name="BKMK_CloudOnlyFed"></a>2 ADFS: estendere un'applicazione front-end locale attestazioni a Internet

![La federazione con la connettività tra locale](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figura 2**

#### <a name="description"></a>Descrizione

È necessario un'applicazione attestazioni che è stati distribuiti correttamente in locale e utilizzato per gli utenti aziendali sono disponibili direttamente da Internet. L'applicazione viene utilizzato come un front-end web a un database SQL in cui sono memorizzati i dati. SQL Server utilizzato dall'applicazione anche si trovano nella rete aziendale. Due Windows Server AD FS STSs e un bilanciamento del carico sono stati distribuiti locale per consentire l'accesso agli utenti aziendali. A questo punto l'applicazione deve inoltre possibile accedervi direttamente da Internet per partner usando le proprie identità aziendale e per gli attuali utenti aziendali.

Allo scopo di semplificare e soddisfare le esigenze di distribuzione e configurazione di questo nuovo requisito, si decide che vengono visualizzate due web frontends e due server proxy ADFS Windows Server Active Directory installato in macchine virtuali di Azure. Tutti e quattro macchine virtuali saranno esposti direttamente a Internet e saranno fornite connettività alla rete locale utilizzando la funzionalità VPN da sito Azure virtuale della rete aziendale.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerazioni scenario e come vengono applicate le aree di tecnologia per lo scenario

- [La topologia della rete](#BKMK_NetworkTopology): creare un Azure virtuali e [configurare la connettività tra locale](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Per ogni i certificati di Windows Server AD FS, assicurarsi che l'URL definito all'interno del modello di certificato e i certificati risultanti può essere raggiunto dalle istanze di ADFS Windows Server Active Directory in esecuzione in Azure. Potrebbe essere necessario alla connettività tra locale a parti dell'infrastruttura infrastruttura a chiave pubblica. Per esempio se endpoint del CRL da LDAP ed è ospitato in modo esclusivo in locale, quindi tra locale devono avvenire la connettività. Se non è consigliabile, potrebbe essere necessario utilizzare i certificati rilasciati da un'autorità di certificazione cui CRL è accessibile tramite Internet.

- [Configurazione di servizi cloud](#BKMK_CloudSvcConfig): assicurarsi che si dispone di due servizi cloud in ordine fornire due bilanciamento del carico indirizzi IP virtuali. Indirizzo IP del servizio cloud prima verrà indirizzato al macchine due virtuali proxy ADFS Windows Server Active Directory sulle porte 80 e 443. Proxy di ADFS macchine virtuali di Windows Server Active Directory verrà configurato in modo che puntino all'indirizzo IP di bilanciamento locale che risulterà STSs di ADFS Windows Server Active Directory. Indirizzo IP del servizio cloud secondo verrà indirizzato al due macchine virtuali rieseguire front-end web sulle porte 80 e 443. Configurare una ricerca personalizzata per garantire che il bilanciamento del carico solo indirizza il traffico al funzionante Windows Server AD FS proxy e web front-end macchine virtuali.

- [Configurazione del server federativo](#BKMK_FedSrvConfig): configurare Windows Server AD FS come server federativo servizio (token di sicurezza) per generare i token di sicurezza per le strutture di Windows Server Active Directory creato nel cloud. Impostare la federazione relazioni di trust provider attestazioni con diversi partner che si desidera accettare le identità e configurare componente relazioni di trust di terze parti con diverse applicazioni che si desidera generare token da.

    Nella maggior parte dei casi, server proxy ADFS Windows Server Active Directory vengono distribuiti in una capacità esposto a Internet per motivi di sicurezza mantenendo isolate dalla connettività Internet diretta controparti federazione ADFS Windows Server Active Directory. Indipendentemente dal proprio scenario di distribuzione, è necessario configurare il servizio cloud con un indirizzo IP virtuale che fornisce un indirizzo IP e porta in grado di bilanciamento del carico tra le due istanze di ADFS servizio token di sicurezza di Windows Server Active Directory o istanze proxy esposti pubblicamente.

- [Configurazione della disponibilità di Windows Server ADFS](#BKMK_ADFSHighAvail): è consigliabile distribuire una farm di Windows Server AD FS con almeno due server per il failover e il bilanciamento del carico. Si potrebbe scegliere di utilizzare Windows interno Database (ID) per dati di configurazione di Windows Server ADFS e utilizzare la funzionalità di bilanciamento del carico interno di Azure per distribuire le richieste in arrivo su server della farm.

Per ulteriori informazioni, vedere la [Guida alla distribuzione di Active Directory](https://technet.microsoft.com/library/cc753963).


### <a name="BKMK_HybridExt"></a>3. Active Directory: Distribuire un'applicazione di Windows Server Active Directory-tenere presente che richiede una connessione alla rete aziendale

![Distribuzione di Active Directory locale tra](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figura 3**

#### <a name="description"></a>Descrizione

Un'applicazione con supporto LDAP viene distribuita in un computer virtuale Azure. Supporta l'autenticazione integrata di Windows e utilizza Windows Server Active Directory come archivio per i dati di profilo di configurazione e dell'utente. L'obiettivo è per l'applicazione sfruttare aziendale Windows Server Active Directory esistenti e fornire il single sign-on. L'applicazione non è attestazioni. Anche gli utenti devono accedere all'applicazione direttamente da Internet. Per ottimizzare le prestazioni e il costo, si decide di distribuire due controller di dominio aggiuntivi che fanno parte del dominio aziendale insieme all'applicazione in Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerazioni scenario e come vengono applicate le aree di tecnologia per lo scenario

- [La topologia della rete](#BKMK_NetworkTopology): creare una rete virtuale Azure con [connettività tra locale](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Metodo di installazione](#BKMK_InstallMethod): distribuire replica controller di dominio Active Directory di Windows Server aziendale. Per una replica controller di dominio, è possibile installare Windows Server Active Directory nella macchina virtuale e facoltativamente è possibile usare la caratteristica di installare da Media, laddove per ridurre la quantità di dati che devono essere replicate in nuovo controller di dominio durante l'installazione. Per un'esercitazione, vedere [installare un controller di dominio Active Directory replica in Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Anche se si usa IFM, può risultare più efficiente per creare controller di dominio virtuale in locale e spostare l'intera virtuale disco rigido nel cloud anziché la replica di Windows Server Active Directory durante l'installazione. Per la sicurezza, è consigliabile eliminare il disco rigido virtuale dalla rete locale dopo che sono stati copiato in Azure.

- [La topologia del sito di Windows Server Active Directory](#BKMK_ADSiteTopology): creare un nuovo sito di Azure Active Directory siti e servizi. Creare un oggetto subnet di Windows Server Active Directory per rappresentare la rete virtuale Azure e aggiungere alla subnet al sito. Creare un nuovo collegamento di sito che include il nuovo sito di Azure e il sito in cui si trova per controllare e ottimizzare il traffico di Windows Server Active Directory e da Azure virtuali Azure endpoint VPN.

- [Indirizzi IP e DNS](#BKMK_IPAddressDNS):

 - Impostare un indirizzo IP statico per il controller di dominio utilizzando il cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Installare e configurare Windows Server DNS nei controller di dominio in Azure.
 - Configurare le proprietà di rete virtuale con il nome e indirizzo IP del macchine Virtuali che ospita i ruoli server controller di dominio e DNS.

- [Distribuito geografico controller di dominio](#BKMK_DistributedDCs): configurare le reti virtuali aggiuntive in base alle esigenze. Se la topologia del sito di Active Directory richiede controller di dominio in aree geografiche che corrispondono a più aree di Azure, quelle che si desidera creare siti Active Directory a seconda dei casi.

- [Controller di dominio di sola lettura](#BKMK_RODC): si potrebbe distribuire un controller nel sito di Azure, a seconda del tipo ai propri requisiti per l'esecuzione di operazioni di scrittura contro controller di dominio e la compatibilità di applicazioni e servizi nel sito con lettura. Per ulteriori informazioni sulla compatibilità delle applicazioni, vedere la [Guida alla compatibilità di applicazione controller di dominio di sola lettura](https://technet.microsoft.com/library/cc755190).

- [Catalogo globale](#BKMK_GC): cataloghi sono necessari per gestire le richieste di accesso in insiemi di più domini. Se non si distribuisce un catalogo globale nel sito di Azure, è necessario pianificare i costi di traffico in uscita durante le richieste di autenticazione causano query cataloghi in altri siti. Per ridurre al minimo il traffico, è possibile abilitare la memorizzazione nella cache appartenenza gruppo universale per il sito di Azure Active Directory siti e servizi.

    Se si distribuisce un catalogo globale, configurare collegamenti al sito e i costi di collegamenti di sito in modo che il catalogo globale nel sito di Azure non preferito come origine controller di dominio da altri cataloghi che è necessario replicare le stesse partizioni di dominio parziali.

- [Posizione del database di Windows Server Active Directory e SYSVOL](#BKMK_PlaceDB): aggiungere un disco dati controller di dominio in esecuzione in macchine virtuali di Azure per archiviare database Windows Server Active Directory, i registri e SYSVOL.

- [Eseguire il backup e ripristino](#BKMK_BUR): determinare nel punto in cui si desidera memorizzare i backup dello stato di sistema. Se necessario, aggiungere un altro disco di dati per la macchina virtuale controller di dominio per archiviare i backup.

## <a name="deployment-decisions-and-factors"></a>Fattori e delle decisioni di distribuzione

In questa tabella sono riepilogate le aree di tecnologia Windows Server Active Directory che sono interessate in scenari precedenti e le corrispondenti decisioni da prendere in considerazione, con collegamenti a informazioni più dettagliate riportata di seguito. Alcune aree tecnologia potrebbero non essere applicabile per ogni scenario di distribuzione e alcune aree tecnologia potrebbero essere più importante a uno scenario di distribuzione altre aree di tecnologia.

Ad esempio, se si distribuisce una replica controller di dominio in una rete virtuale e le strutture ha un unico dominio, quindi scegliendo di distribuire un server di catalogo globale in questo caso non saranno fondamentale per lo scenario di distribuzione poiché non creerà i requisiti di replica aggiuntive. Mano, insieme di strutture dispone di più domini, quindi la decisione di distribuire un catalogo globale in una rete virtuale può influire sul larghezza di banda disponibile, le prestazioni, autenticazione, ricerche nella directory e così via.

| Area grazie alla tecnologia di Windows Server Active Directory | Decisioni | Fattori |
| ---- | ---- | ---- |
| [La topologia della rete](#BKMK_NetworkTopology) | Si crea una rete virtuale? | <li>Requisiti per accedere alle risorse Corp</li> <li>Autenticazione</li> <li>Gestione degli account</li> |
| [Configurazione di distribuzione controller di dominio](#BKMK_DeploymentConfig) | <li>Distribuire un insieme di strutture separato senza alle relazioni di trust?</li> <li>Distribuire un nuovo insieme di strutture con la federazione?</li> <li>Distribuire un nuovo insieme di strutture con relazione di trust Windows Server Active Directory o Kerberos?</li> <li>Estendere foresta Corp distribuendo una replica controller di dominio?</li> <li>Estendere foresta Corp distribuendo un nuovo dominio figlio o struttura di dominio?</li> | <li>Sicurezza</li> <li>Conformità</li> <li>Costo</li> <li>Adattabilità e tolleranza di errore</li> <li>Compatibilità delle applicazioni</li> |
| [Topologia del sito di Windows Server Active Directory](#BKMK_ADSiteTopology) | Come si configura subnet, siti e collegamenti al sito con Azure virtuali per ottimizzare il traffico e ridurre i costi? | <li>Definizioni di sito e subnet</li> <li>Proprietà collegamento del sito e la notifica delle modifiche</li> <li>Compressione di replica</li> |
| [Indirizzi IP e DNS](#BKMK_IPAddressDNS) | Come è possibile configurare gli indirizzi IP e risoluzione dei nomi? | <li>Utilizzare il cmdlet Set-AzureStaticVNetIP uso per assegnare un indirizzo IP statico</li> <li>Installare Windows Server DNS server e configurare le proprietà di rete virtuale con il nome e indirizzo IP del macchine Virtuali che ospita i ruoli server controller di dominio e DNS</li> |
| [Distribuito geografico controller di dominio](#BKMK_DistributedDCs) | Come è possibile replicare controller di dominio su reti virtuale separate? | Se la topologia del sito di Active Directory richiede controller di dominio in aree geografiche che corrisponde a più aree di Azure, quelle che si desidera creare siti Active Directory a seconda dei casi. [Configura virtuali alla connettività di rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) per la replica tra controller di dominio su reti virtuale separate. |
| [Controller di dominio di sola lettura](#BKMK_RODC) | Utilizzare controller di dominio scrivibile o di sola lettura? | <li>Filtrare gli attributi di alto impatto aziendale/personali</li> <li>Filtrare informazioni riservate</li> <li>Limitare il traffico in uscita</li> |
| [Catalogo globale](#BKMK_GC) | Installare il catalogo globale? | <li>Per foresta con dominio singolo, rendere tutti i cataloghi globali controller di dominio</li> <li>Per foresta con più domini, cataloghi sono necessari per l'autenticazione</li> |
| [Metodo di installazione](#BKMK_InstallMethod) | Come è possibile installare controller di dominio in Azure? | Uno dei due: <li>Installare Active Directory mediante Windows PowerShell o Dcpromo</li> <li>Spostare disco rigido virtuale di un controller di dominio locale virtuale</li> |
| [Posizione del database di Windows Server Active Directory e SYSVOL](#BKMK_PlaceDB) | Posizione di archiviazione Windows Server Active Directory database, i registri e SYSVOL? | Modificare i valori predefiniti di Dcpromo.exe. Questi critiche Active Directory file *deve* trovarsi sui dischi di dati di Azure anziché dischi del sistema operativo in cui la cache di scrittura. |
| [Eseguire il backup e ripristino](#BKMK_BUR) | Come è possibile proteggere e ripristinare i dati? | Creare backup dello stato di sistema |
| [Configurazione del server federativo](#BKMK_FedSrvConfig) | <li>Distribuire un nuovo insieme di strutture con la federazione nel cloud?</li> <li>Distribuire ADFS locale ed esporre un proxy nel cloud?</li> | <li>Sicurezza</li> <li>Conformità</li> <li>Costo</li> <li>Accesso alle applicazioni, business partner</li> |
| [Configurazione di servizi cloud](#BKMK_CloudSvcConfig) | Un servizio cloud viene distribuito in modo implicito la prima volta che si crea una macchina virtuale. È necessario distribuire servizi cloud aggiuntive? | <li>Una macchina virtuale o macchine virtuali richiede esposizione diretta a Internet?</li> <li> Il servizio richiede bilanciamento del carico?</li> |
| [Requisiti del server federativo per indirizzi (IP dinamico e IP virtuale) IP pubblici e privati](#BKMK_FedReqVIPDIP) | <li>L'istanza di Windows Server AD FS necessario raggiungibili direttamente da Internet?</li> <li>L'applicazione distribuita nel cloud richiede il proprio indirizzo IP esposto a Internet e la porta?</li> | Creare un servizio cloud per ogni indirizzo IP virtuale necessaria per la distribuzione |
| [Configurazione di Windows Server AD FS disponibilità](#BKMK_ADFSHighAvail) | <li>Quanti nodi la server farm di Windows Server AD FS?</li> <li>Quanti nodi per distribuire la farm proxy ADFS Windows Server Active Directory?</li> | Adattabilità e tolleranza di errore |

### <a name="BKMK_NetworkTopology"></a>La topologia della rete

Per soddisfare i requisiti di DNS di Windows Server Active Directory e la coerenza indirizzo IP, è necessario prima di tutto creare una [rete virtuale Azure](../virtual-network/virtual-networks-overview.md) e collegare le macchine virtuali. Durante la creazione, è necessario decidere se facoltativamente estendere la connettività per la rete aziendale in locale che connetta macchine virtuali di Azure al computer locale, questo viene eseguito mediante le tecnologie VPN tradizionale e richiede che vengano esposti un endpoint VPN sul bordo della rete aziendale. La rete VPN, ovvero viene avviata da Azure alla rete aziendale non viceversa.

Si noti che le spese aggiuntive valide per l'estensione di una rete virtuale per la rete locale in base alle tariffe standard che si applicano a ogni macchina virtuale. In particolare, sono disponibili in base alle tariffe per il tempo CPU del gateway virtuali Azure e per il traffico in uscita generati da ogni macchina virtuale che comunica con computer locale attraverso la rete VPN. Per ulteriori informazioni in base alle tariffe il traffico di rete, vedere [Azure prezzi in immediatamente](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configurazione di distribuzione controller di dominio

La modalità di configurazione del controller di dominio dipende dai requisiti per il servizio da eseguire in Azure. Ad esempio, si potrebbe distribuire una nuova foresta isolata dalla propria foresta aziendale, per il test di verifica dei concetti, una nuova applicazione o un altro progetto a breve termine che richiede servizi directory ma non specifica accesso alle risorse aziendali interne.

Come dei vantaggi, un insieme di strutture isolato che controller di dominio non replica con locale controller di dominio, risultante del traffico di rete meno in uscita generato dal sistema, direttamente riduzione dei costi. Per ulteriori informazioni in base alle tariffe il traffico di rete, vedere [Azure prezzi in immediatamente](http://azure.microsoft.com/pricing/).

Se ad esempio si supponga che si hanno esigenze di informativa sulla privacy di un servizio, ma il servizio dipende da access per l'interno Windows Server Active Directory. Se sono consentiti per ospitare dati per il servizio nel cloud, si potrebbe distribuire un nuovo dominio figlio per l'insieme di strutture interno in Azure. In questo caso, è possibile distribuire un controller di dominio per il nuovo dominio figlio (senza catalogo globale per risolvere i problemi di privacy). Questo scenario, insieme a una distribuzione di controller di dominio, replica richiede una rete virtuale per la connettività con controller di dominio locale.

Se si crea una nuova foresta, scegliere se si desidera utilizzare [e trust di Active Directory](https://technet.microsoft.com/library/cc771397) o [federazione trust](https://technet.microsoft.com/library/dd807036). Saldo totale requisiti determinati compatibilità, sicurezza, conformità, costo e adattabilità. Ad esempio, per usufruire di [autenticazione selettiva](https://technet.microsoft.com/library/cc755844) è possibile distribuire un nuovo insieme di strutture in Azure e creare un trust di Active Directory di Windows Server tra le strutture locali e cloud. Se l'applicazione è attestazioni, tuttavia, si potrebbe distribuire trust federazione anziché trust di Active Directory. Un altro fattore sarà il costo per replicare altri dati aumentando l'in Windows Server Active Directory locale nel cloud o generare traffico in uscita più di autenticazione e il carico di query.

Requisiti per la disponibilità e tolleranza di errore inoltre influire sul preferito. Ad esempio, se il collegamento interrotto applicazioni sfruttando un trust Kerberos o una federazione attendibile sono tutte probabilmente interamente non funziona a meno che non è stato distribuito infrastruttura sufficiente in Azure. Configurazioni di distribuzione alternativo, ad esempio controller di dominio di replica (scrivibile sola lettura) aumentare la probabilità di essere in grado di tollerare interruzioni del collegamento.

### <a name="BKMK_ADSiteTopology"></a>Topologia del sito di Windows Server Active Directory

È necessario definire correttamente i siti e collegamenti al sito per ottimizzare il traffico e ridurre al minimo costo. Siti, collegamenti a siti e subnet influire sulla topologia di replica tra controller di dominio e il flusso di traffico di autenticazione. Considerare le seguenti spese di traffico e distribuire e configurare controller di dominio in base ai requisiti dello scenario di distribuzione:

- Esiste una tariffa nominale orarie per il gateway stesso:

 - Può essere avviato e arrestato per adattarlo

 - Se arrestato, macchine virtuali di Azure sono isolate dalla rete aziendale

- Il traffico in ingresso è gratuito

- Viene addebitato il traffico in uscita in base alle [Azure prezzi in immediatamente](http://azure.microsoft.com/pricing/). È possibile ottimizzare le proprietà di collegamento di sito tra siti locale e il cloud come indicato di seguito:

 - Se si usano più reti virtuali, configurare collegamenti a siti e i costi in modo appropriato per impedire che Windows Server Active Directory priorità sito Azure su uno in grado di fornire gli stessi livelli di servizio senza costi aggiuntivi. È anche possibile disattivare il Bridge del collegamento (BASL) opzione (attivata per impostazione predefinita). In questo modo che solo collegamento diretto siti replicano tra loro. Controller di dominio in siti transitivamente connessi non è più possibile replicare direttamente tra loro, ma deve essere replicato tramite uno o più siti comuni. Se siti intermedi sono più disponibili per qualche motivo non si verificherà replica tra controller di dominio in siti transitivamente connessi anche se è disponibile la connettività tra i siti. Infine, in cui le sezioni del processo di replica transitivi rimangono appropriate, creare siti ponti che contengono i collegamenti a siti appropriati e i siti, ad esempio in locale, siti di rete aziendale.

 - [Costi dei collegamenti di siti Configura](https://technet.microsoft.com/library/cc794882) in modo appropriato per evitare il traffico impreviste. Ad esempio, se è abilitata l'impostazione di **Provare successivo sito più vicino** , assicurarsi che i siti virtuali non siano al successivo aumentando il costo associato dell'oggetto collegamento di sito che si connette al sito Azure alla rete aziendale.

 - Configurare siti collegamento [intervalli](https://technet.microsoft.com/library/cc794878) e [le pianificazioni](https://technet.microsoft.com/library/cc816906) in base ai requisiti di uniformità e frequenza delle modifiche di oggetto. Allineare pianificazione della replica con tolleranza latenza. Controller di dominio replicare solo l'ultimo stato di un valore, in modo decrescente l'intervallo di replica è possibile salvare i costi se esiste un oggetto sufficiente modificare tasso.

- Se la riduzione a icona costi è una priorità, verificare la replica è pianificata e la notifica delle modifiche non è abilitata. Si tratta della configurazione predefinita quando la replica tra siti. Non è importante se si distribuisce una sola in una rete virtuale perché quest'ultimo non verrà replicate le modifiche in uscita. Ma se si distribuisce un controller di dominio scrivibile, assicurarsi che il collegamento di sito non è configurato per replicare gli aggiornamenti con frequenza non necessarie. Se si distribuisce un server di catalogo globale (catalogo globale), verificare che ogni altro sito che contiene un catalogo globale replica partizioni del dominio da un'origine cc in un sito è connesso con un collegamento di sito o collegamenti a siti che hanno un costo inferiore rispetto nel catalogo globale nel sito di Azure.


- È possibile ridurre ulteriormente ancora generato da replica tra siti modificando l'algoritmo di compressione replica il traffico di rete. Algoritmo di compressione dipende dalle algoritmo di compressione REG_DWORD del Registro di sistema voce HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator. Il valore predefinito è 3, che mette in correlazione all'algoritmo Xpress Comprimi. È possibile modificare il valore di 2, che diventa l'algoritmo MSZip. Nella maggior parte dei casi, aumenterà la compressione, ma ciò avviene spese utilizzo della CPU. Per ulteriori informazioni, vedere [topologia di replica di Active Directory come funziona](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Indirizzi IP e DNS

Azure macchine virtuali sono assegnate "dedicata DHCP indirizzi" per impostazione predefinita. Perché gli indirizzi dinamici Azure virtuali mantenuti con una macchina virtuale per la durata della macchina virtuale, vengono soddisfatti i requisiti di Windows Server Active Directory.

Di conseguenza, quando si utilizza un indirizzo dinamico su Azure, si è in effetto utilizzando un indirizzo IP statico perché si tratta routing per il periodo del contratto e il periodo del contratto è uguale alla durata del servizio cloud.

Tuttavia, l'indirizzo dinamico viene rilasciato la macchina virtuale caso di arresto. Per impedire che l'indirizzo IP viene rilasciato, è possibile [utilizzare Set-AzureStaticVNetIP per assegnare un indirizzo IP statico](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Risoluzione dei nomi, distribuire il proprio (o uso della) infrastruttura di server DNS; Condizione Azure DNS non soddisfa le esigenze di risoluzione avanzata nome di dominio Active Directory Windows Server Active Directory. Ad esempio, essa non supporta i record SRV dinamici e così via. Risoluzione dei nomi è un elemento di configurazione critica per controller di dominio e i client di dominio. Controller di dominio deve essere in grado di registrazione di record di risorse e la risoluzione dei record di risorse altri controller di dominio.
Per motivi di prestazioni e tolleranza di errore, è preferibile installare il servizio Windows Server DNS sul controller di dominio in esecuzione in Azure. Configurare le proprietà di Azure virtuali con il nome e indirizzo IP del server DNS. Avvio di altre macchine virtuali sulla rete virtuale, le impostazioni di sistema di risoluzione DNS client verranno configurate con server DNS come parte dell'allocazione di indirizzi IP dinamico.

> [AZURE.NOTE] In un dominio di Windows Server Active directory Active Directory che è ospitato su Azure direttamente da Internet non è possibile partecipare computer locale. Requisiti relativi alle porte per Active Directory e l'operazione di aggiunta al dominio trasformarlo pratico direttamente esporre le porte necessarie e di fatto un controller di dominio intero a Internet.

Macchine virtuali registrare il proprio nome DNS automaticamente all'avvio o quando viene apportata una modifica nome.

Per ulteriori informazioni in questo esempio e un altro esempio che illustra come effettuare il provisioning di macchina virtuale prima e installare Active Directory, vedere [installare un nuovo insieme di strutture in Microsoft Azure Active Directory](active-directory-new-forest-virtual-machine.md). Per ulteriori informazioni sull'uso di Windows PowerShell, vedere [Installare PowerShell Azure](../powershell-install-configure.md) e [Azure cmdlet per la gestione](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Distribuito geografico controller di dominio

Azure offre vantaggi per l'hosting più controller di dominio su reti virtuali diverse:

- Tolleranza più siti

- Distanza fisica per uffici ramo (latenza inferiore)

Per informazioni sulla configurazione di comunicazione diretta tra reti virtuali, vedere [configurare virtuali alla connettività di rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controller di dominio di sola lettura

È necessario scegliere se si desidera distribuire controller di dominio di sola lettura o scrivibile. Potrebbe inclinato per distribuire lettura perché non è preciso fisica ma lettura è progettati per essere distribuito nelle posizioni in cui la protezione fisica rischio, ad esempio uffici diramazione.

Azure non presentare rischi per la sicurezza fisico di filiale ma lettura potrebbe comunque rivelarsi più conveniente perché le caratteristiche che forniscono sono appropriate per tali ambienti che per motivi molto diversi. Ad esempio lettura non dispone di alcun replica in uscita e sono in grado di popolare in modo selettivo informazioni riservate (password). Lo svantaggio, la mancanza di queste informazioni riservate può implicare anche il traffico in uscita su richiesta per convalidare i loro come utente o computer autentica. Informazioni riservate possono essere prepopolati di cache in modo selettivo.

Perché non è possibile aggiungere gli attributi che contengono dati riservati in quest'ultimo filtrata set attributo (/FAs) offre un altro vantaggio nella e intorno preoccupazioni alto impatto aziendale e informazioni personali. Il FAS è personalizzabile degli attributi che non replicate in lettura. È possibile utilizzare il FAS una misura nel caso in cui non è consentito o non si desidera memorizzare informazioni personali o alto impatto aziendale in Azure. Per ulteriori informazioni, vedere [controller filtrato attributo. impostare [(https://technet.microsoft.com/library/cc753459)]

Assicurarsi che le applicazioni sia compatibile con lettura si prevede di utilizzare. Molte applicazioni basate su Windows Server Active Directory funzionano bene con lettura, ma alcune applicazioni possono garantire prestazioni efficaci o non riuscire se non hanno accesso a un controller di dominio scrivibile. Per ulteriori informazioni, vedere [Guida alla compatibilità applicazione controller di dominio di sola lettura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catalogo globale

È necessario scegliere se si desidera installare un globale catalogo. In una singola delle strutture, è necessario configurare tutti controller di dominio come server di catalogo globale. Non aumenterà costi perché non ci sarà alcuna traffico aggiuntive.

Un insieme di strutture più domini, cataloghi sono necessari per espandere l'appartenenza al gruppo universale durante il processo di autenticazione. Se non si distribuisce un catalogo globale, carichi di lavoro in rete virtuale che autenticare un controller di dominio in Azure indirettamente genererà il traffico di autenticazione in uscita per eseguire una query cataloghi locale durante un tentativo di autenticazione.

I costi associati cataloghi sono meno prevedibili perché è ospitato da ogni dominio (nella parte). Se il carico di lavoro contiene un servizio esposto a Internet e autentica gli utenti in Windows Server Active Directory, i costi potrebbero essere completamente imprevisti. Per ridurre le query di catalogo globale esterni al sito cloud durante l'autenticazione, è possibile [attivare la cache di appartenenza al gruppo universale](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Metodo di installazione

È necessario scegliere la modalità di installazione controller di dominio della rete virtuale:

- Alzare di livello nuovi controller di dominio. Per ulteriori informazioni, vedere [installare un nuovo insieme di strutture in una rete virtuale Azure Active Directory](active-directory-new-forest-virtual-machine.md).

- Spostare il disco rigido virtuale di un controller di dominio locale virtuale nel cloud. In questo caso, è necessario assicurarsi che il controller di dominio locale virtuale viene "spostata," non "copiati" o "duplicato".

Utilizzare solo Azure macchine virtuali di controller di dominio (anziché "web" o "lavoro" ruolo Azure macchine virtuali). Sono permanenti e durata dello stato di un controller di dominio è necessaria. Azure macchine virtuali sono progettate per carichi di lavoro, ad esempio controller di dominio.

Non utilizzare SYSPREP per distribuire o duplicare controller di dominio. La possibilità di duplicare controller di dominio è solo disponibile a partire in Windows Server 2012. La caratteristica duplicazione richiede il supporto per VMGenerationID in hypervisor sottostante. Hyper-V in Windows Server 2012 e Azure reti virtuali supportano entrambi VMGenerationID, come fornitori di software di virtualizzazione di terze parti.

### <a name="BKMK_PlaceDB"></a>Posizione del database di Windows Server Active Directory e SYSVOL

Selezionare dove si trova il database di Windows Server Active Directory, i registri e SYSVOL. Devono essere distribuiti sui dischi di dati di Azure.

> [AZURE.NOTE] Azure dischi dati sono limitati a 1 TB.

Unità disco dati eseguire non cache scrive per impostazione predefinita. Unità disco dati collegati a una macchina virtuale usare scrittura tramite la memorizzazione nella cache. Scrittura-through memorizzazione nella cache rende che la scrittura è approvata allo spazio di archiviazione Azure permanente prima del completamento dal punto di vista del sistema operativo della macchina virtuale la transazione. Fornisce durata spese scrive leggermente più lenti.

È importante per Windows Server Active Directory poiché la cache del disco scrittura-behind invalida ipotesi dal controller di dominio. Windows Server Active Directory tenta di disattivare la cache di scrittura, ma in questo caso, il disco di sistema di IO per accettarlo. Errore per disabilitare la cache di scrittura, in alcuni casi, possono presentare il ripristino USN conseguente il tempo di ritardo oggetti e altri problemi.

Si consiglia di virtuale controller di dominio, eseguire le operazioni seguenti:

- Impostare l'impostazione delle preferenze di Cache Host sul disco dati Azure per nessuno. Per evitare problemi con la cache di scrittura per le operazioni di Active Directory.

- Archiviare il database, i registri e SYSVOL sullo stesso uno dei due dati o più dischi separati i dati. In genere si tratta di un disco diverso da disco utilizzato per il sistema operativo stesso. Il punto chiave è che il database di Windows Server Active Directory e SYSVOL devono venire archiviati non su un tipo di disco rigido del sistema operativo Azure. Per impostazione predefinita, il processo di installazione di Active Directory installa questi componenti nella cartella % systemroot %, che non è consigliabile Azure.

### <a name="BKMK_BUR"></a>Eseguire il backup e ripristino

Tenere presente che cos'è e non è supportato per eseguire il backup e ripristino di un controller di dominio in generale e in particolare quelli in esecuzione in una macchina virtuale. Vedere [eseguire il Backup e ripristino considerazioni virtualizzato controller di dominio](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Creare backup dello stato di sistema usando solo software di backup in particolare tenere presenti i requisiti di backup per Windows Server Active Directory, ad esempio Windows Server Backup.

Non copiare o duplicare i file disco rigido virtuale dei controller di dominio anziché esecuzione di backup regolari. Dovrebbe mai essere necessario, così utilizzando dischi rigidi virtuali duplicati o copiati senza Windows Server 2012 e un hypervisor supportato verrà presentate le bolle USN un ripristino.

### <a name="BKMK_FedSrvConfig"></a>Configurazione del server federativo

La configurazione del server federativi ADFS di Windows Server Active Directory (STSs) dipende in parte se le applicazioni che si desidera distribuire in Azure necessaria accedere alle risorse sulla rete locale.

Se le applicazioni soddisfano i criteri seguenti, è possibile distribuire le applicazioni di isolamento dalla rete locale.

- Aver accettato token di sicurezza SAML

- Sono exposable a Internet

- Non accedere alle risorse locali

In questo caso, configurare Windows Server AD FS STSs come segue:

1. Configurare un'isolato foresta con dominio singolo in Azure.

2. Consentire l'accesso federativo alla foresta configurando una server farm federazione ADFS Windows Server.

3. Configurare Windows Server AD FS (federazione server farm e federazione server proxy farm) della foresta locale.

4. Stabilire una relazione di attendibilità federazione tra il locale e Azure istanze di Windows Server ADFS.

Mano, se le applicazioni richiedono l'accesso alle risorse locale, è possibile configurare Windows Server ADFS con l'applicazione in Azure come indicato di seguito:

1. Configurare la connettività tra reti locale e Azure.

2. Configurare una server farm federazione ADFS Windows Server Active Directory della foresta locale.

3. Configurare una server proxy farm di Windows Server ADFS federazione in Azure.

Questa configurazione ha il vantaggio di ridurre l'esposizione di risorse locale, simile alla configurazione di Windows Server AD FS con le applicazioni di una rete perimetrale.

Si noti che in entrambi gli scenari, è possibile stabilire relazioni di trust con altri provider di identità, se è necessaria una collaborazione business-to-business.

### <a name="BKMK_CloudSvcConfig"></a>Configurazione di servizi cloud

Servizi cloud sono necessari se si desidera esporre una macchina virtuale direttamente a Internet o per esporre un'applicazione di bilanciamento del carico esposto a Internet. Questo è possibile perché ogni servizio cloud offre un solo indirizzo IP virtuale configurabile.

### <a name="BKMK_FedReqVIPDIP"></a>Requisiti del server federativo per indirizzi (IP dinamico e IP virtuale) IP pubblici e privati

Ogni macchina virtuale Azure riceve un indirizzo IP dinamico. Un indirizzo IP dinamico è un indirizzo privato accessibile solo all'interno di Azure. Nella maggior parte dei casi, tuttavia, sarà necessario configurare un indirizzo IP virtuale per le distribuzioni di Windows Server ADFS. L'indirizzo IP virtuale è necessaria per esporre i punti finali di Windows Server ADFS a Internet e da utilizzare per i clienti e partner federati per l'autenticazione e la gestione continuativa. Un indirizzo IP virtuale è una proprietà di un servizio cloud che contiene uno o più macchine virtuali Azure. Se l'applicazione attestazioni distribuito su Azure e Windows Server AD FS esposto a Internet e condivisione comuni porte, ogni richiederà un indirizzo IP del proprio e pertanto sarà necessario creare un servizio cloud per l'applicazione e un altro per Windows Server AD FS.

Le definizioni delle condizioni per l'indirizzo IP virtuale e l'indirizzo IP dinamico, vedere [termini e le definizioni](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configurazione di Windows Server AD FS disponibilità

Sebbene sia possibile distribuire i servizi di federazione ADFS Windows Server autonoma, è consigliabile distribuire una farm con almeno due nodi di servizio token di sicurezza di Active Directory ADFS e proxy per gli ambienti di produzione.

Vedere la sezione [Considerazioni sulla topologia di ADFS 2.0 distribuzione](https://technet.microsoft.com/library/gg982489) nel [Active Directory Guida di ADFS 2.0](https://technet.microsoft.com/library/dd807036) decidere quali opzioni di configurazione di distribuzione più esigenze specifiche.

> [AZURE.NOTE] Per ottenere il bilanciamento del carico per gli endpoint Windows Server AD FS su Azure, configurare tutti i membri della farm di Windows Server ADFS nel servizio cloud stesso e utilizzare la funzionalità di bilanciamento del carico di Azure per HTTP (impostazione predefinita 80) e porte HTTPS (impostazione predefinita 443). Per ulteriori informazioni, vedere [ricerca di bilanciamento del carico Azure](https://msdn.microsoft.com/library/azure/jj151530).
Windows Server rete carico bilanciamento non è supportato in Azure.
