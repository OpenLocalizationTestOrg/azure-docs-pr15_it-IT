<properties
   pageTitle="Architettura di Azure riferimento - IaaS: Estensione di Active Directory in Azure | Microsoft Azure"
   description="Come implementare un'architettura di rete sicura ibrido con autorizzazione di Active Directory in Azure."
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
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Estensione servizi Directory Active Directory (somma) per Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per l'estensione l'ambiente di Active Directory (AD) in Azure, i servizi di autenticazione distribuita tramite [Servizi di dominio Active Directory (AD DS)][active-directory-domain-services]. Questa architettura estende che descritta negli articoli di [implementare un'architettura di rete ibrido sicura in Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. L'architettura di riferimento utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Utilizzare Active Directory per eseguire l'autenticazione delle identità. Queste identità a cui possono appartenere gli utenti, computer, applicazioni o altre risorse che fanno parte di un dominio di sicurezza. È possibile ospitare Active Directory locale, ma in uno scenario ibrido dove si trovano gli elementi di un'applicazione in Azure può essere più efficiente replicare questa funzionalità e archivio di Active Directory nel cloud. Questo approccio può aiutare a ridurre la latenza causata inviando l'autenticazione e le richieste di autorizzazione locale dal cloud di nuovo in Active Directory in esecuzione in locale. 

Esistono due modi per ospitare i servizi directory in Azure:

1. È possibile utilizzare [Azure Active Directory (AAD)] [ azure-active-directory] per creare un nuovo dominio di Active Directory nel cloud e collegarlo a un locale dominio di Active Directory. Per l'installazione di [Azure AD Connect] [ azure-ad-connect] grado replicare identità conservate nell'archivio locale nel cloud. Si noti che la directory nel cloud **non** un'estensione del sistema locale, ma è una copia che contiene le stesse identità. Le modifiche apportate a queste identità locale verranno copiate cloud, ma le modifiche apportate nel cloud **non** essere replicate al dominio locale. Ad esempio la reimpostazione della password devono essere eseguito in locale e utilizzare Azure AD Connect per copiare la modifica nel cloud. Si noti inoltre che la stessa istanza di AAD può essere collegata a più di una sola istanza di Active Directory; AAD conterrà le identità di ogni repository Active Directory a cui è collegato.

    AAD è utile per situazioni in cui la rete locale e Azure virtuali che ospita le risorse cloud non sono direttamente collegate mediante un tunnel VPN o ExpressRoute circuito. Anche se questa soluzione è semplice, potrebbe non essere adatto per i sistemi in componenti Impossibile eseguire la migrazione di oltre il limite in locale/cloud come questo potrebbe è necessario configurare nuovamente di AAD. Inoltre, AAD gestisce solo autenticazione utente anziché computer. Alcune applicazioni e servizi, ad esempio SQL Server possono richiedere l'autenticazione del computer nel qual caso questa soluzione non è appropriata.

2. È possibile distribuire una macchina virtuale Active Directory è in esecuzione come controller di dominio in Azure, che si estende l'infrastruttura di Active Directory esistente dal Data Center locale. Questo approccio è più comune per gli scenari in cui la rete locale e Azure virtuali sono connessi mediante una connessione VPN e/o ExpressRoute. Questa soluzione supporta anche bidirezionale replica che consente di apportare modifiche nel cloud e in locale, nel punto in cui è più appropriato. In base ai propri requisiti di sicurezza, può essere l'installazione di Active Directory nel cloud:
    - parte dello stesso dominio tale tenuto locale
    - un nuovo dominio all'interno di una foresta condivisa
    - una foresta separata

<!-- we might want to add info on how to choose from the three options above -->

Questa architettura è incentrata su soluzione 2, con lo stesso dominio di Active Directory in Azure e locale.

Casi di utilizzo tipico per questa architettura includono:

- Applicazioni ibride in carichi di lavoro Esegui parzialmente locale e in parte in Azure.

- Applicazioni e servizi di autenticazione tramite Active Directory.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura (*fare clic su per applicare lo zoom avanti*). Per ulteriori informazioni sugli elementi inattivo, leggere [l'implementazione di un'architettura di rete ibrido sicura in Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Rete locale.** La rete locale include server Active Directory locale che può eseguire l'autenticazione e l'autorizzazione per componenti che si trovano in locale.

- **Server di Active Directory.** Questi sono controller di dominio l'implementazione di servizi directory (AD DS) in esecuzione come macchine virtuali nel cloud. Questi server possono fornire l'autenticazione dei componenti in esecuzione in rete virtuale Azure.

- **Subnet Directory attiva.** I server Active Directory sono ospitati in una subnet separata. Le regole NSG proteggere i server Active Directory e offrono un firewall contro il traffico da origini non previsti.

- **La sincronizzazione di azure Gateway e Active Directory.**. Il gateway Azure fornisce una connessione tra la rete locale e VNet Azure. Può trattarsi di una [connessione VPN] [ azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Tutte le richieste di sincronizzazione tra i server di Active Directory nel cloud e locale passano attraverso il gateway. Indirizza definite dall'utente (UDRs) Gestione il routing per il traffico di sincronizzazione che passa direttamente al server Active Directory nel cloud e non attraversano gli esistente virtuale dispositivi di rete (NVAs) in questo scenario.

    Per ulteriori informazioni sulla configurazione di UDRs e la NVAs, vedere [implementazione di un'architettura di rete ibrido sicura in Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Consigli

In questa sezione vengono riepilogati i suggerimenti per l'implementazione di dominio Active Directory in Azure, che copre:

- Consigli macchine Virtuali.

- Suggerimenti di rete.

- Suggerimenti per la sicurezza. 

- Consigli sito Directory attivare.

- Consigli sul posizionamento FSMO Directory attivare.

>[AZURE.NOTE] Documento di [istruzioni per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure] [ ad-azure-guidelines] contiene informazioni più dettagliate sulle molti questi punti.

### <a name="vm-recommendations"></a>Consigli macchine Virtuali

Creare macchine virtuali con risorse sufficienti per gestire il volume di traffico previsto. Utilizzare le dimensioni del computer host di Active Directory locale come punto di partenza. Monitorare l'utilizzo delle risorse; è possibile ridimensionare le macchine virtuali e ridimensionare verso il basso se sono troppo grande. Per ulteriori informazioni sulle dimensioni controller di dominio Active Directory, vedere [Pianificazione delle capacità per servizi di dominio Active Directory][capacity-planning-for-adds].

Creare un disco dati virtuale separati per l'archiviazione di database, i registri e SYSVOL per Active Directory. Non memorizzare questi elementi nello stesso disco del sistema operativo. Si noti che per impostazione predefinita, dischi dati collegati a una macchina virtuale utilizzano scrittura tramite la memorizzazione nella cache. Tuttavia, questo modulo di memorizzazione nella cache può generare conflitti con i requisiti di dominio Active Directory. Per questo motivo, impostare l'impostazione delle *Preferenze di Cache Host* sul disco dati su *Nessuno*. Per ulteriori informazioni, vedere [il posizionamento del database di Windows Server Active Directory e SYSVOL][adds-data-disks].

Distribuire almeno due macchine virtuali in esecuzione Active Directory come controller di dominio per la rete virtuale Azure per motivi di [disponibilità](#Availability-considerations) .

### <a name="networking-recommendations"></a>Suggerimenti di rete

Configurare l'interfaccia di rete per ognuna delle macchine virtuali di dominio Active Directory con indirizzi IP privati statici di hosting. Questa configurazione supporta meglio DNS su ciascuna delle macchine virtuali di Active Directory. Per ulteriori informazioni, vedere [come impostare un indirizzo IP statico privato nel portale di Azure][set-a-static-ip-address].

> [AZURE.NOTE] Non fornire macchine virtuali di dominio Active directory Active Directory di indirizzi IP. Vedere [Considerazioni sulla protezione] [ security-considerations] per altri dettagli.

È necessario verificare che il traffico liberamente tra i server di Active Directory nel cloud e in locale:

- Aggiungere regole di NSG alla subnet Active Directory che consentire il traffico in ingresso da locale. Per informazioni dettagliate sulle porte che si avvalgono di Active Directory, vedere [Active Directory e Active Directory dominio servizi porta requisiti][ad-ds-ports].

- Assicurarsi che le tabelle UDR indirizzare il traffico di Active Directory mediante NVAs utilizzati in questo scenario. Per il proprio distribuzioni, a seconda di quali NVAs si utilizza, è consigliabile reindirizzare il traffico.

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza

Server di dominio Active directory Active Directory gestire l'autenticazione e sono pertanto elementi molto sensibili. Evitare l'esposizione diretta dei server Active Directory a Internet. Posizionare i server Active Directory in una subnet distinta, con il proprio firewall. Assicurarsi che le porte necessarie per l'utilizzo di dominio Active Directory per l'autenticazione e l'autorizzazione e i server synchronzing aperti, ma chiudere tutte le altre porte. Per ulteriori informazioni, vedere [Active Directory e Active Directory dominio servizi porta requisiti][ad-ds-ports]. I [componenti della soluzione] [ solution-components] sezione più avanti in questo documento vengono illustrate le NSG di regole che la soluzione di esempio viene utilizzato per aprire porte.

È possibile utilizzare le regole NSG per creare un semplice firewall. Se si richiede la protezione più completa, è possibile implementare un perimetro rafforzare la sicurezza all'interno di server con una coppia di subnet e NVAs, come descritto dal documento [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Utilizzare la crittografia disco BitLocker o Azure per crittografare il disco che ospita i database di Active Directory.

### <a name="active-directory-site-recommendations"></a>Suggerimenti di Active Directory siti

È possibile utilizzare i siti di dominio Active Directory per controller di dominio insieme di gruppo che sono connessi mediante un collegamento veloce. Controller di dominio nello stesso sito di Active Directory replicare automaticamente le modifiche di directory e alcune operazioni di configurazione è necessaria per gestire la replica.

Per controllare il traffico di replica tra Azure e la data center locale, è consigliabile aggiungere un sito di Active Directory separato per rappresentare lo spazio di indirizzi usato da Azure. È possibile configurare un collegamento di sito tra le in locale di dominio Active Directory siti e controllare la replica intersito in modo più efficiente.

Utilizzare la separazione dei siti per applicare diversi oggetti Criteri gruppo () combinare computer in Azure e usufruire di applicazioni "sito presente", ad esempio System Center Configuration Manager.

### <a name="active-directory-fsmo-placement-recommendations"></a>Consigli sul posizionamento di Active Directory FSMO

I server singola operazione FSMO (Master) flessibili sono controller di dominio specifico, reposnsible per la coerenza dei dati tra diverse impostazioni in Active Directory elencate di seguito.

- **Master Schema**. Si tratta di un ruolo di strutture che gestisce la struttura dello schema utilizzato da Active Directory.

- **Master dei nomi di dominio**. Si tratta di un ruolo di strutture che gestisce le informazioni sui nomi di dominio all'interno della foresta.

- **Controller primario ()**. Si tratta di un ruolo a livello di dominio. Controller di dominio primario a gestire gli aggiornamenti delle password e degli account. Viene consultato da altri controller di dominio quando le richieste di autenticazione servizio hanno password non corrispondenti. Controller di dominio primario inoltre gestisce gli aggiornamenti di criteri di gruppo e controller di dominio di destinazione per le applicazioni legacy e alcuni strumenti di amministrazione che eseguono alcune operazioni scrivibile.

- **Master ID relativo (RID)**. RID vengono utilizzati per identificare in modo univoco gli oggetti all'interno di una directory. Il server è responsabile per la generazione di un pool di dominio di RID per l'utilizzo da tutti i server di Active Directory all'interno del dominio.

- **Ruolo infrastruttura**. Un oggetto in un dominio può fare riferimento a un oggetto in un altro dominio. Questo livello di dominio è responsabile per l'aggiornamento di un oggetto SID e il nome distinto in un riferimento all'oggetto di domini. Si noti che un server per l'implementazione di questo ruolo non è possibile anche fungere da server (catalogo globale).

Per ulteriori informazioni, vedere [ruoli di Active Directory FSMO in Windows][AD-FSMO-roles-in-windows].

Per questo scenario, è consigliabile che evitare di distribuzione dei ruoli FSMO ai controller di dominio in Azure. 

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Creare una disponibilità impostare per il server di Active Directory. Assicurarsi che siano almeno due server nella finestra di impostazione. Server di annunci nel cloud dovrebbe essere controller di dominio nello stesso dominio. In questo modo la replica automatica tra server.

Inoltre, è necessario designare server come [master operazioni in standby] [ standby-operations-masters] nel caso in cui si verifica un errore di connettività a un server che svolge un ruolo.

## <a name="security-considerations"></a>Considerazioni sulla protezione

Se tutte le attività di amministrazione di dominio sono probabile che deve essere eseguita con controller di dominio locale, si consiglia di eseguire controller di dominio nel cloud sola lettura. Un controller di dominio di sola lettura solo mantiene un sottoinsieme di credenziali degli utenti (sufficiente per eseguire l'autenticazione in locale) e possono essere configurate nella cache informazioni solo per utenti specifici. Pertanto, se un controller di dominio di sola lettura è compromessa, solo le informazioni per gli utenti memorizzati nella cache avrà effetto, piuttosto che i dettagli di ogni account del dominio. Per ulteriori informazioni, vedere [Controller di dominio di sola lettura][read-only-dc].

Per ridurre al minimo la vulnerabilità dei singoli account utente ed evitare tentativi di violazione, attenersi alla procedura consigliata per l'impostazione e gestione di password degli utenti in Active Directory. Per ulteriori informazioni, vedere [Procedure consigliate per l'applicazione di criteri Password][best_practices_ad_password_policy]. Inoltre, prestare attenzione a gruppi a cui assegnare gli utenti. Ad esempio, non rendere i normali utenti membri del gruppo di amministratori dell'organizzazione, gruppo Schema Admins e gruppo Domain Admins.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Active Directory è automaticamente scalable controller di dominio che fanno parte dello stesso dominio. Le richieste vengono distribuite in tutti i controller all'interno di un dominio. È possibile aggiungere un altro controller di dominio e verranno sincronizzate automaticamente con il dominio. Non si configura un bilanciamento del carico separata per indirizzare il traffico in controller all'interno del dominio. Assicurarsi che tutti i controller di dominio siano sufficiente risorse di memoria e spazio di archiviazione per gestire il database di dominio. Se possibile, rendere tutti controller di dominio macchine virtuali le stesse dimensioni.

## <a name="management-considerations"></a>Considerazioni sulla gestione

Copiare i file disco rigido virtuale controller di dominio anziché esecuzione di backup regolari poiché sul ripristino di utenti può provocare incoerenze nello stato tra controller di dominio.

Arrestare e riavviare una macchina virtuale che esegue il ruolo di controller di dominio in Azure all'interno del sistema operativo guest invece di usare l'opzione di arresto nel portale di Azure. Tramite il portale di Azure arrestare una macchina virtuale fa sì che la macchina virtuale essere rilasciato. Questa azione Reimposta macchine Virtuali-GenerationID, che è indesiderato per un controller di dominio. Quando si reimposta la macchina virtuale GenerationID, l'ID dell'archivio Active Directory viene reimpostato, viene eliminato il pool RID e SYSVOL è contrassegnato come non attendibile.

## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio

La mancata monitorare e gestire una rete di server Active Directory può causare problemi ad esempio:

- **Errore di accesso**. Errore di accesso può verificarsi in tutto il dominio o foresta se una risoluzione relazione o il nome di protezione ha esito negativo o se un server di catalogo globale non è possibile determinare l'appartenenza al gruppo universale.

- **Blocco dell'account**. Account utente e servizio viene bloccato controller di dominio primario non è disponibile, se si verifica un errore di replica tra diversi controller di dominio.

- **Errore del Controller di dominio**. Se l'unità che contiene il file DIT viene eseguito spazio su disco, controller di dominio può smettere di funzionare.

- **Errore dell'applicazione**. Le applicazioni di importanza cruciale per le aziende, ad esempio Microsoft Exchange o un'altra applicazione di posta elettronica, possono non riuscire se indirizzo libro query nella directory di esito negativo.

- **Dati incoerenze nelle directory**. Se la replica non riesce per un periodo di tempo prolungato, duplicare gli oggetti possono essere creati nella directory e potrebbero richiedere diagnostiche e l'ora in modo da eliminare.

- **Errore durante la creazione di account**. Un controller di dominio non è possibile creare account utente o computer se esaurisce la fornitura di ID relativi e master RID non è disponibile.

- **Errore di criteri di sicurezza**. Se la cartella condivisa SYSVOL non replica correttamente, oggetti Criteri di gruppo e criteri di sicurezza non vengono correttamente applicati ai client.

Monitorare con attenzione i server di Active Directory e prepararsi a correttive rapidamente. Creare un elenco di controllo di Monitoraggio attività da eseguire in un determinato intervallo di tempo. Ad esempio si potrebbero giornalmente le attività critiche seguenti:

- Esaminare e risolvere gli avvisi segnalati dai controller di dominio 

- Verificare che tutti i controller di dominio possono comunicare e funzioni che la replica.

- Assicurarsi che SYSVOL rimanga condiviso.

- Risolvere i problemi di sincronizzazione dell'ora.

- Verificare lo spazio su disco in unità fisiche utilizzato da Active Directory.

È possibile eseguire altre operazioni meno frequente. Ad esempio, è Impossibile esaminare le relazioni di trust e verificare la presenza di trust obsoleto o interrotti settimanale e verificare che tutti i controller di dominio sono in esecuzione con la stessa service pack e patch di correzione mensile.

Per ulteriori informazioni, vedere [Monitoraggio di Active Directory][monitoring_ad]. È possibile installare strumenti, ad esempio [Microsoft Systems Center] [ microsoft_systems_center] del server di monitoraggio (vedere il [Diagramma architettura][architecture]) per eseguire queste attività. 

## <a name="solution-components"></a>Componenti della soluzione

Soluzione previste questa architettura consente di creare una rete sicura ibrido come descritto dai documenti di [implementare un'architettura di rete ibrido sicura in Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], ma con l'aggiunta degli elementi seguenti:

- Un gruppo di risorse Azure denominato dns-rg di *base*, in *base* è un prefisso specificare quando si distribuisce la soluzione.

- Due macchine virtuali di Azure chiamato *base*-ad1-macchine virtuali e *base*-ad2-macchine virtuali, nel gruppo di risorse dns rg di *base*. Queste macchine virtuali sono configurate come server Active Directory con i servizi Directory e DNS installato e configurato.

- Un NSG denominato *base*-Active Directory-nsg in un gruppo di risorse Azure rg - ntwk di *base*. In questo gruppo di risorse fa parte dell'infrastruttura che costituiscono la rete ibrido sicura, ma NSG nuovo è un componente aggiuntivo che consente di definire le regole di protezione in ingresso per il server di Active Directory, come illustrato nella tabella riportata di seguito:


    Priorità|Nome|Origine|Destinazione|Servizio|Azione|
    --------|----|------|-----------|-------|------|
    170|vnet-port53|10.0.0.0/16|Qualsiasi|Custom(Any/53)|Consenti|
    180|vnet-port88|10.0.0.0/16|Qualsiasi|Custom(Any/88)|Consenti|
    190|vnet-port135|10.0.0.0/16|Qualsiasi|Custom(Any/135)|Consenti|
    200|vnet a port137 9|10.0.0.0/16|Qualsiasi|Custom(Any/137-139)|Consenti|
    210|vnet-port389|10.0.0.0/16|Qualsiasi|Custom(Any/389)|Consenti|
    220|vnet-port464|10.0.0.0/16|Qualsiasi|Custom(Any/464)|Consenti|
    230|vnet a rpc dinamiche|10.0.0.0/16|Qualsiasi|Custom(Any/49152-65535)|Consenti|
    240|onprem Active Directory per port53|192.168.0.0/24|Qualsiasi|Custom(Any/53)|Consenti|
    250|onprem Active Directory per port88|192.168.0.0/24|Qualsiasi|Custom(Any/88)|Consenti|
    260|onprem Active Directory per port135|192.168.0.0/24|Qualsiasi|Custom(Any/135)|Consenti|
    270 gradi|onprem Active Directory per port389|192.168.0.0/24|Qualsiasi|Custom(Any/389)|Consenti|
    280|onprem Active Directory per port464|192.168.0.0/24|Qualsiasi|Custom(Any/464)|Consenti|
    290|consentire a gestione rdp|10.0.0.128/25|Qualsiasi|Custom(Any/3389)|Consenti|
    300|consentire a gateway|10.0.255.224/27|Qualsiasi|Custom(Any/Any)|Consenti|
    310|Consenti automatica|10.0.255.192/27|Qualsiasi|Custom(Any/Any)|Consenti|
    320|vnet-negare|Qualsiasi|Qualsiasi|Custom(Any/Any)|Consenti|

    Active Directory utilizza le porte 53, 89, 135, 389 e 464 per accettare la replica in ingresso e il traffico di autenticazione. In questa tabella controller di dominio locale è in 192.168.0.0/24 di spazio indirizzo (può variare lo spazio degli indirizzi - specificare queste informazioni come un parametro per i modelli distribuiti dalla soluzione.

    Il NSG definisce anche le seguenti regole di protezione in abilitare la sincronizzazione e l'autorizzazione per il traffico alla rete locale:

    Priorità|Nome|Origine|Destinazione|Servizio|Azione|
    --------|----|------|-----------|-------|------|
    100|fuori port53|Qualsiasi|192.168.0.0/24|Custom(Any/53)|Consenti|
    110|fuori port88|Qualsiasi|192.168.0.0/24|Custom(Any/88)|Consenti|
    120|fuori port135|Qualsiasi|192.168.0.0/24|Custom(Any/135)|Consenti|
    130|fuori port389|Qualsiasi|192.168.0.0/24|Custom(Any/389)|Consenti|
    140|fuori port445|Qualsiasi|192.168.0.0/24|Custom(Any/445)|Consenti|
    150|fuori port464|Qualsiasi|192.168.0.0/24|Custom(Any/464)|Consenti|
    160|fuori rpc dinamici|Qualsiasi|192.168.0.0/24|Custom(Any/49152-65535)|Consenti|

Lo script fornito con la soluzione anche le seguenti operazioni:

- Aggiunge *base*-ad1-macchine virtuali e *base*-ad2-macchine virtuali server come controller di dominio al dominio. È possibile visualizzare tali server nella console di *Active Directory utenti e computer* nel controller di dominio locale:

![[1]][1]

- Crea una nuova subnet (10.0.0.0/16) per un sito di Active Directory denominato VNet di Azure-sito di Active Directory per il dominio. In questo sito sono *base*-ad1-macchine virtuali e *base*-ad2-macchine virtuali server. 

- Aggiunge le impostazioni di trasporto tra siti IP che consentono di configurare l'intervallo di replica tra il sito locale e controller di dominio nel cloud. È possibile visualizzare le impostazioni per la subnet, siti e impostazioni di trasporto nella console di *Active Directory siti e dei server* nel controller di dominio locale:

![[2]][2]

## <a name="deployment"></a>Distribuzione

Soluzione di esempio sono previsti i prerequisiti seguenti:

- È già stato configurato il dominio locale e di avere configurato DNS e installato servizi Routing e accesso remoto per supportare una connessione VPN connettersi al gateway VPN Azure.


- È stata installata la versione più recente di CLI Azure. [Seguire queste istruzioni per informazioni dettagliate][cli-install].

- Se si distribuisce la soluzione da Windows, è necessario installare uno strumento che fornisce una shell bash, ad esempio [GitHub Desktop][github-desktop].

>[AZURE.NOTE] Se non si dispone di accesso a un dominio locale esistente, è possibile creare un ambiente di testing utilizzando [onpremdeploy.sh] [ onpremdeploy] bash script. Questo script crea una rete e SV nel cloud che simula un'installazione locale di base. Modificare lo script prima dell'esecuzione e impostare le seguenti variabili definite all'inizio del file:
>
> - **BASE_NAME**. Prefisso definite dall'utente per il gruppo di risorse e SV creato dallo script. Questo elemento deve contenere **non più di 5 caratteri** in caso contrario che lo script tenterà di generare una macchina virtuale con un nome non valido e avere esito negativo.
> 
> - **Abbonamento**. L'ID di abbonamento Azure. Verrà creato un gruppo di risorse in questa suscription.
> 
> - **Posizione**. Azure posizione in cui creare il gruppo di risorse, ad esempio *eastus* o *westus*.
> 
> - **ADMIN_USER_NAME**. Il nome da utilizzare per l'account di amministratore nella macchina virtuale.
> 
> - **ADMIN_PASSWORD**. La password per l'account di amministratore.

Per creare la soluzione di esempio, procedere come segue:

1. scaricare e modificare [azuredeploy.sh] [ azuredeploy] script e impostare i parametri seguenti all'inizio del file:

    - **BASE_NAME**. Prefisso definite dall'utente per i gruppi di risorse e macchine virtuali create dallo script. Come prima, questo elemento deve contenere **non più di 5 caratteri**.

    - **Abbonamento**. L'ID di abbonamento Azure.

    - **Tipo_so**. Il sistema operativo*Windows* o *Linux*da usare per il livello di accesso al web, business e dati macchine virtuali. Si noti che tutti i server di Active Directory creati dallo script eseguono Windows Server 2012, indipendentemente da questa impostazione.

    - **Nome_dominio**. Il nome del dominio locale. Si noti che se si utilizza l'ambiente creato tramite lo script onpremdeploy.sh, questo deve essere *contoso.com*.

    - **Posizione**. Azure posizione in cui creare i gruppi di risorse.

    - **ADMIN_USER_NAME**. Il nome da utilizzare per gli account di amministratore in macchine virtuali diversi.

    - **ADMIN_PASSWORD**. La password per l'account di amministratore.

    - **ON_PREMISES_PUBLIC_IP**. L'indirizzo IP pubblico del computer locale VPN.

    - **ON_PREMISES_ADDRESS_SPACE**. Spazio indirizzo interno della rete locale. Se si utilizza l'ambiente creato tramite lo script onpremdeploy.sh, deve essere 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. Chiave condivisa IPSec utilizzata per stabilire la connessione VPN tra la rete locale e il gateway VPN Azure.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. L'indirizzo IP del server DNS locali. Se si utilizza l'ambiente creato tramite lo script onpremdeploy.sh, deve essere 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** Il prefisso della subnet locale. Se si utilizza l'ambiente creato tramite lo script onpremdeploy.sh, deve essere 192.168.0.0/24.

    >[AZURE.NOTE] Per risparmiare tempo e risorse, lo script non crea i livelli di accesso dati aziendali o. Se si richiedono questi elementi, è possibile rimuovere il commento la sezione seguente di script azuredeploy.sh:
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Aprire un prompt dei comandi shell bash e passare alla cartella contenente lo script azuredeploy.sh.

3. Accedere al proprio account Azure. Nella shell bash immettere il comando seguente:

    ```
    azure login
    ```

    Seguire le istruzioni per la connessione a Azure.

4. Eseguire il comando `./azuredeploy.sh`e quindi attendere che lo script crea l'infrastruttura di rete.

5. Al prompt di *verificare che sia stata creata la VNet*utilizzare il portale di Azure per verificare che sia stato creato un gruppo di risorse denominato *base*- ntwk-rg e che contiene elementi simili a quelli visualizzati nell'immagine seguente:

    ![[3]][3]

    >[AZURE.NOTE] Negli esempi riportati, *base* è stata impostata su *cloud* durante l'esecuzione di script.

    Fare clic su *base*- vnet VNet, fare clic su *subnet*e verificare che siano state create le subnet illustrate di seguito:

    ![[4]][4]

6. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere che il livello web e bilanciamento del carico vengono creati.

7. Al prompt di *verificare che il livello Web sia stato creato correttamente*, utilizzare il portale di Azure per verificare che sia stato creato un gruppo di risorse denominato web livello-rg di *base*e che contiene elementi simili a quelli riportati di seguito:

    ![[5]][5]

8. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere la NVAs vengono creati.

9. Al prompt di *verificare che la NVA sia stato creato correttamente*, utilizzare il portale Azure per verificare che sia stato creato un gruppo di risorse denominato *base*- gestione-rg con il contenuto seguente:

    ![[6]][6]

10. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere che venga creato il jumpbox.

11. Al prompt di *verificare che il jumpbox sia stato creato correttamente*, utilizzare il portale di Azure per controllare gli elementi seguenti sono stati aggiunti al gruppo di risorse rg - gestione di *base*:

    - Un set di disponibilità chiamato *base*- jb-come.

    - Una macchina virtuale denominata *base*- jb-macchine virtuali.

    - Un'interfaccia di rete denominata *base*- jb-NIC.

12. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere durante la creazione del gateway VPN Azure e della connessione. Si noti che questo passaggio può richiedere fino a 30 minuti.

13. Al prompt di *verificare che il gateway VPN sia stato creato correttamente*, utilizzare il portale di Azure per controllare gli elementi seguenti sono stati aggiunti al gruppo di risorse rg - ntwk di *base*:

    - Un gateway di rete locale chiamato lgw in locale.
    
    - Gateway virtuali chiamato *base*- vpngw.

    - Connessione di gateway denominata *base*- vnet-vpnconn. Si noti che lo stato della connessione potrebbe essere *non connesso* se non si è ancora configurato estremità locale della connessione. si riguarderà seguente in un secondo momento.

14. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere durante la creazione di macchine virtuali e altre risorse per la rete Perimetrale.

15. Al prompt di *verificare che la rete Perimetrale sia stato creato correttamente*, utilizzare il portale Azure per verificare che sia stato creato un gruppo di risorse denominato rg di rete perimetrale - *base*con il contenuto seguente:

    ![[7]][7]

16. Al prompt dei comandi nella finestra della shell bash, premere un tasto. Dovrebbero essere visualizzate le istruzioni seguenti:

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Accedere al computer locale che si connette al gateway Azure e configurare la connessione in modo appropriato. Aggiungere route statiche al dispositivo gateway locale che indirizza l'intervallo di indirizzi 10.0.0.0/16 attraverso il gateway per il VNet requestsfor. I passaggi per eseguire questa operazione variano in base a come si esegue la connessione. Vedere [implementazione di un'architettura di rete ibrido con Azure e VPN locale] [ implementing-a-hybrid-network-architecture-with-vpn] per ulteriori informazioni.

    Nota per trovare l'indirizzo IP pubblico del gateway VPN Azure, tramite il portale di Azure per esaminare il gateway - vpngw di *base*nel gruppo di risorse rg - ntwk *base*:

    ![[8]][8]

    È possibile determinare se la connessione è stata stabilita correttamente esaminando lo stato della connessione - vnet vpnconn *base*. È necessario impostarla *connesso*.

    Per verificare la connessione, aprire una connessione desktop remoto per jumpbox (10.0.0.254) da un computer nella rete locale.

17. Al prompt dei comandi nella finestra della shell bash, premere un tasto. Al successivo prompt dei comandi, *premere un tasto per aggiornare l'impostazione VNet per VNet in modo che puntino a DNS locale*, premere un tasto e attendere che le impostazioni di DNS per il VNet vengono aggiornate per il valore specificato come parametro **ON_PREMISES_DNS_SERVER_ADDRESS** dello script azuredeploy.sh.

18. Al prompt dei comandi, *verificare che l'impostazione del server DNS nel VNet è stato aggiornato*, utilizzare il portale di Azure per esaminare l'impostazione *server DNS* la *base*- vnet VNet nel gruppo di risorse rg - ntwk *base*. Deve essere impostata su *Custom DNS*e il *server DNS primario* dovrebbe essere l'indirizzo del server DNS locali:

    ![[9]][9]

19. Al prompt nella finestra della shell bash *premere qualsiasi tasto per creare il gruppo di risorse per il server di Active Directory* , premere un tasto e attendere mentre viene creato il gruppo di risorse per l'esenzione server di annunci nel cloud.

20. Al prompt nella finestra della shell bash *premere qualsiasi tasto per creare macchine virtuali per il server di Active Directory* , premere un tasto e attendere che macchine virtuali di creare e aggiungere al gruppo di risorse.

21. Quando *premere qualsiasi tasto per partecipare a macchine virtuali al dominio locale* viene visualizzata, accedere al portale di Azure e verificare che sia stato creato un gruppo denominato dns-rg di *base*e che sono presenti due macchine Virtuali (*base*-ad1-macchine virtuali e *base*-ad2-macchine virtuali):

    ![[10]][10]

22. Nel gruppo di risorse rg - ntwk di *base*, controllo che è stato creato un NSG chiamato *base*-Active Directory-nsg. Esaminare le regole di protezione in ingresso e in uscita per questo NSG. Assicurarsi che corrispondano quelli elencati nelle tabelle nei [componenti della soluzione] [ solution-components] sezione.

23. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere macchine virtuali vengono aggiunti al dominio locale.

24. Nel *visitare locale nel server Active Directory per verificare che i computer sono state aggiunte ai domini* richiesta, connettersi al computer locale e utilizzare la console *Active Directory Users e computer* per verificare che sia macchine virtuali sono stati aggiunti al dominio:

    ![[11]][11]

25. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere che il sito di replica di Active Directory verrà creato nel dominio.

26. Nel *visitare locale nel server Active Directory per verificare che sia stato creato il sito di replica* Chiedi conferma, utilizzare la console di *Active Directory siti e servizi* a per controllare che un sito di replica denominato *Vnet di Azure-sito di Active Directory* è stato creato correttamente, insieme a un collegamento tra siti trasporto IP denominato *AzureToOnpremLink*e una subnet che fa riferimento il VNet:

    ![[12]][12]

27. Al prompt dei comandi nella finestra della shell bash, premere un tasto e attendere che lo script installa servizi Directory e DNS su ciascuna delle macchine virtuali di Active Directory.

28. Quando viene visualizzata la richiesta di *accesso effettuare a ogni server Azure Active Directory per verificare che i servizi Directory è stato configurato correttamente* , aprire una connessione desktop remoto da un computer locale a jumpbox (*base*- jb-macchine virtuali) e quindi aprire un'altra connessione desktop remoto dalla jumpbox al primo server Active Directory (*base*-ad1-macchine virtuali). Accedere usando il **nome_dominio**, **ADMIN_USER_NAME**e **ADMIN_PASSWORD** specificato nello script azuredeploy.sh. Utilizzando Server Manager, verificare che i ruoli di dominio Active Directory e DNS siano entrambi stati aggiunti. Ripetere questa procedura per il secondo server Active Directory (*base*-ad2-macchine virtuali).

29. Al prompt dei comandi nella finestra della shell bash, premere un tasto. Quando viene visualizzato il messaggio *premere un tasto per configurare le impostazioni di Azure VNet DNS in modo che puntino a record DNS in Azure* , premere un tasto e lo script aggiornare le impostazioni di DNS per il VNet.

30. Quando viene richiesto *verificare che il DNS VNet impostazione è stato aggiornato riferimento il DNS macchine Virtuali di Azure server* viene visualizzata, tramite il controllo del portale Azure l'impostazione *Server DNS* la *base*- vnet VNet nel gruppo di risorse rg - ntwk *base*. I server DNS primari e secondari ora devono fare riferimento a macchine virtuali di Active Directory due:

    ![[13]][13]

31. Ricomincia ad ogni delle macchine virtuali di Active Directory prima di continuare. Questo passaggio è necessario assicurarsi che ognuna di esse sollevare i dati corretti DNS comuni. Attendere che entrambi macchine virtuali in esecuzione prima di continuare.

32. Al prompt dei comandi nella finestra della shell bash, premere un tasto. Al prompt successivo, *premere un tasto per applicare il gateway UDR alla subnet gateway (potrebbe essere stato rimosso)*, premere un tasto e consentire lo script aggiornare il gateway UDR.

33. Verificare che lo script viene completata correttamente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle procedure consigliate per la [creazione di un insieme di strutture risorsa aggiunge] [ adds-resource-forest] in Azure.

- Informazioni sulle procedure consigliate per la [creazione di un'infrastruttura ADFS] [ adfs] in Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Architettura della rete ibrido con Active Directory protetta"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "Console di Active Directory utenti e computer voce macchine virtuali di Azure due come server"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "Console di Active Directory siti e servizi Microsoft che mostra le impostazioni di replica per il sito nel cloud"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "Il contenuto del gruppo di risorse di base-ntwk-rg"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "Le subnet VNet vnet di base"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Gli elementi nel livello web"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "NVAs nel gruppo di risorse rg di gestione base"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Le risorse nel gruppo di risorse rg di rete perimetrale base"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Trovare l'indirizzo IP pubblico del gateway VPN Azure"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "Le impostazioni del server DNS per il * base *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "Il * base *-dns-rg gruppo di risorse del server di Active Directory macchine virtuali"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "Console di Active Directory utenti e computer server AD macchine virtuali di elenco come membri del dominio"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "Console di Active Directory siti e servizi Microsoft che mostra il sito di replica per il server di Azure Active Directory"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "Le impostazioni del server DNS per VNet base vnet riferimento al server di annunci nel cloud"