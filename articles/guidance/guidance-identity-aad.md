<properties
   pageTitle="Implementazione di Azure Active Directory | Microsoft Azure"
   description="Come implementare un'architettura di rete sicura ibrido con Azure Active Directory."
   services="guidance,virtual-network,active-directory"
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
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Implementazione di Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per l'integrazione di domini di Active Directory (AD) locale e strutture di Azure Active Directory per l'autenticazione basata su cloud identità.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. L'architettura di riferimento utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

È possibile utilizzare i servizi directory e l'identità, ad esempio quelli forniti da servizi Active Directory (AD DS) per eseguire l'autenticazione delle identità. Queste identità a cui possono appartenere gli utenti, computer, applicazioni o altre risorse che fanno parte di un limite di protezione. È possibile ospitare directory e servizi di identità locale, ma in uno scenario ibrido dove si trovano gli elementi di un'applicazione in Azure può essere più efficiente per estendere questa funzionalità nel cloud. Questo approccio può aiutare a ridurre la latenza causata inviando l'autenticazione e le richieste di autorizzazione locale dal cloud indietro per i servizi di identità e directory in esecuzione in locale.

Azure fornisce due soluzioni per l'implementazione di servizi directory e identità nel cloud:

1. È possibile utilizzare [Azure Active Directory (AAD)] [ azure-active-directory] per creare un dominio di Active Directory nel cloud e collegarlo a un locale dominio di Active Directory. AAD consente di configurare il single sign-on (SSO) per gli utenti che eseguono applicazioni accessibili tramite il cloud. AAD utilizza [Azure AD Connect] [ azure-ad-connect] esecuzione locale replicare identità e gli oggetti contenuti nell'archivio locale nel cloud.

    È anche possibile implementare AAD senza utilizzare una directory locale. In questo caso, AAD funge da origine principale di tutte le informazioni sull'identità anziché contenente i dati replicati da una directory locale.

    Si noti che la directory nel cloud **non** un'estensione di una directory locale. È invece una copia che include gli stessi oggetti e identità. Le modifiche apportate a questi elementi locale vengono copiate nel cloud, ma le modifiche apportate nel cloud **non** replicato sul dominio locale.

    >[AZURE.NOTE] Le edizioni di Azure Active Directory Premium supportano writeback delle password degli utenti, consentendo agli utenti locali eseguire la reimpostazione della password self-service nel cloud. Questo è l'unica informazione che AAD Sincronizza alla directory locale. Per ulteriori informazioni sulle versioni diverse di AAD e le relative funzionalità, vedere [Edizioni di Azure Active Directory][aad-editions].

2. È possibile distribuire una macchina virtuale in esecuzione Active Directory come controller di dominio in Azure, estensione dell'infrastruttura di Active Directory esistente (inclusi Active Directory e AD FS) dal Data Center locale. Questo approccio è più comune per gli scenari in cui la rete locale e Azure virtuali sono connessi mediante una connessione VPN e/o ExpressRoute. Questa soluzione supporta anche bidirezionale replica che consente di apportare modifiche nel cloud e in locale, nel punto in cui è più appropriato.

Questa architettura è incentrata su soluzione 1. Per ulteriori informazioni sulla seconda soluzione, vedere [Estensione Active Directory in Azure][guidance-adds].

Casi di utilizzo tipico per questa architettura includono:

- Fornire Single Sign-on per gli utenti che utilizzano SaaS e altre applicazioni nel cloud, utilizzando le stesse credenziali che specificano Applications locale.

- Pubblicazione locale applicazioni web tramite il cloud per consentire l'accesso agli utenti remoti che appartengono all'organizzazione.

- Implementazione delle funzionalità di self-service per gli utenti finali, ad esempio la reimpostazione password e delegare la gestione di gruppo. 

    >[AZURE.NOTE] Queste funzionalità possono essere controllate dall'amministratore tramite criteri di gruppo.

- Situazioni in cui la rete locale e Azure virtuali hosting di applicazioni cloud non sono direttamente collegate mediante un tunnel VPN o ExpressRoute circuito.

Si noti che AAD non fornisce tutte le funzionalità di Active Directory. Ad esempio, AAD attualmente solo gestisce autenticazione utente anziché computer. Alcune applicazioni e servizi, ad esempio SQL Server possono richiedere l'autenticazione del computer nel qual caso questa soluzione non è appropriata. Inoltre, AAD potrebbe non essere adatto per i sistemi in componenti Impossibile eseguire la migrazione di oltre il limite in locale/cloud anziché solo la copia.

> [AZURE.NOTE] Per una descrizione dettagliata del funzionamento di Azure Active Directory, guardare [Microsoft Active Directory spiegazione][aad-explained].

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura. Per ulteriori informazioni sugli elementi carico di lavoro di Azure, leggere [Macchine virtuali in esecuzione per un'architettura di livelli in Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Per semplicità, questo diagramma solo Mostra le connessioni direttamente correlate all'AAD e non è illustrata reindirizzamento delle richieste di web browser o altro protocollo traffico correlato che può verificarsi come parte del processo di federazione di autenticazione e identità. Ad esempio, un utente (locale o remoto) accede in genere un'app web tramite un browser e il web app in modo trasparente potrebbe reindirizzare nel web browser per l'autenticazione la richiesta tramite AAD. Dopo l'autenticazione, passare la richiesta di tornare all'app web insieme le informazioni sull'identità appropriato.

[! [0]][0]

- **Tenant di azure Active Directory**. Si tratta di un'istanza di AAD creati dall'organizzazione. Funge da un servizio directory semplice per le applicazioni cloud (contiene oggetti copiati da locale in Active Directory) e offre servizi di identità.

- **Subnet livello web**. Subnet contiene macchine virtuali che implementano un'applicazione personalizzata basata su cloud sviluppata dall'organizzazione e per cui AAD può essere utilizzato come un gestore di identità.

- **Locale server Active Directory**. L'organizzazione che ha già un servizio directory esistente, ad esempio Active Directory. È possibile sincronizzare molti degli elementi nella directory Active Directory (ad esempio utente e le informazioni di gruppo) con AAD per abilitare AAD da utilizzare queste informazioni per l'autenticazione delle identità.

- **Server di sincronizzazione di azure AD Connect**. Si tratta di un computer locale che esegue il servizio di sincronizzazione di Azure AD Connect. Per installare questo servizio, utilizzando il software di Azure AD Connect. Il servizio di sincronizzazione di Azure AD Connect Sincronizza le informazioni (utenti, gruppi, contatti, e così via) contenute in locale in Active Directory per AAD nel cloud. Ad esempio, è possibile eseguire il provisioning o il deprovisioning locale utenti e gruppi e le modifiche vengono propagate a AAD. Il servizio di sincronizzazione di Azure AD Connect passa informazioni nel tenant di AAD.

    >[AZURE.NOTE] Per motivi di sicurezza, la password dell'utente non vengono memorizzata direttamente in AAD. Invece AAD contiene un hash ogni password. Questo è sufficiente per verificare la password dell'utente. Se un utente richiede la reimpostazione della password, deve essere eseguito in locale che il nuovo per inviata a AAD. Edizioni Premium AAD includono caratteristiche in grado di automatizzare questa operazione per consentire agli utenti di reimpostare la propria password.

## <a name="recommendations"></a>Consigli

In questa sezione vengono riepilogati i suggerimenti per l'implementazione AAD come indicato di seguito.

- Connettersi a Active Directory
- Sicurezza

### <a name="azure-ad-connect-sync-service-recommendations"></a>Consigli su servizi di sincronizzazione Azure AD Connect

Sincronizzazione riguarda per assicurarsi che le informazioni sull'identità di utenti nel cloud sia coerente con quello contenute in locale. Lo scopo del servizio di sincronizzazione di Azure AD Connect è la coerenza. I seguenti punti di riepilogano i suggerimenti per l'implementazione del servizio di sincronizzazione di Azure AD Connect:

- Prima di implementare sincronizzazione di Azure AD Connect, è necessario determinare i requisiti di sincronizzazione dell'organizzazione (cosa da sincronizzare, tra cui domini e con quale frequenza. L'articolo [requisiti di sincronizzazione directory determinazione] [ aad-sync-requirements] vengono descritti i punti da considerare.

- È possibile eseguire il servizio di sincronizzazione di Azure AD Connect utilizzando una macchina virtuale o un computer ospitato in locale. A seconda della volatilità delle informazioni nella directory Active Directory, dopo la sincronizzazione iniziale con AAD è stata eseguita il carico il servizio di sincronizzazione di Azure AD Connect è difficilmente elevato. Utilizzando una macchina virtuale consente di ridimensionare più facilmente il server (monitorare l'attività come descritto nella sezione [monitoraggio considerazioni](#monitoring-considerations) per determinare se è necessario).

- Se si hanno più domini in locale in un insieme di strutture, è possibile archiviare e sincronizzare le informazioni per l'intera foresta in un singolo tenant AAD (è consigliabile). Filtrare le informazioni per le identità che si verificano in più di un dominio in modo che ogni identità viene visualizzata una sola volta in AAD invece di essere duplicato come ciò potrebbe causare incoerenze durante la sincronizzazione dei dati. Questo approccio richiede l'implementazione di più server di sincronizzazione di Azure AD Connect. Per ulteriori informazioni, vedere lo scenario più AAD nella sezione [Considerazioni sulla topologia](#topology-considerations) . 

- Usare i filtri per limitare i dati archiviati in AAD a solo quello che sono necessari. Ad esempio, l'organizzazione non consigliabile memorizzare le informazioni sugli account inattivo o non personali in AAD. Applicazione di filtri può essere basate su gruppo, basato sul dominio, in base a unità Organizzativa o basato su attributi ed è possibile combinare i filtri per generare regole più complesse. Ad esempio, è possibile selezionare per sincronizzare solo gli oggetti contenuti in un dominio che dispongono di un valore specifico in un attributo selezionato. Per informazioni dettagliate, vedere [sincronizzazione di Azure AD Connect: configurare il filtro][aad-filtering].

- Per implementare disponibilità per il servizio di sincronizzazione AD Connect, eseguire un server di gestione temporaneo secondario. Per ulteriori informazioni, vedere la sezione [Considerazioni sulla topologia](#topology-considerations) .

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza

Gli elementi seguenti riepilogano i suggerimenti sulla protezione principale per l'implementazione AAD, a seconda delle esigenze dell'organizzazione:

- Gestire le password degli utenti.

    Se si utilizza una versione premium di AAD, è possibile abilitare password writeback da AAD alla directory locale eseguendo un'installazione personalizzata di Azure AD Connect:

    [! [9]][9]

    Questa caratteristica consente agli utenti di reimpostare la propria password all'interno del portale Azure, ma deve essere attivata solo dopo aver esaminato i criteri di sicurezza dell'organizzazione. Ad esempio, è possibile limitare gli utenti che possono modificare la password ed è possibile personalizzare l'esperienza di gestione password. Per ulteriori informazioni, vedere [Personalizzazione di gestione delle Password in base alle esigenze dell'organizzazione][aad-password-management].

- Mantenere la protezione per le applicazioni locale che è possibile accedervi esternamente.

    Utilizzare il Proxy di applicazione Azure Active Directory per fornire un accesso limitato alle applicazioni web locale dagli utenti esterni tramite AAD. Questo approccio protegge le applicazioni esporle direttamente a Internet. Solo gli utenti che hanno credenziali valide nella directory di Azure sono in grado di raggiungere le applicazioni. Per ulteriori informazioni, vedere l'articolo [Abilitare Proxy di applicazione nel portale di Azure][aad-application-proxy].

- Monitoraggio AAD per segni di attività sospetto.

    È possibile utilizzare AAD Premium P2 edition, che include la protezione dell'identità AAD. Protezione dell'identità utilizza algoritmi adattata apprendimento ed euristica per rilevare alterazioni e gli eventi che possono indicare che è stata compromessa un'identità di rischio. Ad esempio, è possibile rilevare potenzialmente anomala attività, ad esempio irregolari accesso attività, gli accessi da origini sconosciute o da indirizzi IP con attività sospetta o componenti aggiuntivi di accesso da dispositivi che potrebbero contenere. Usare questi dati, la protezione dell'identità genera report e avvisi che consente di analizzare questi eventi di rischio e non viene accettato rimedio appropriate o attenuazione. Per ulteriori informazioni, vedere [La protezione di Azure Active Directory identità][aad-identity-protection].

    È anche possibile utilizzare la funzionalità di creazione di report di AAD nel portale di Azure per monitorare sospetta e altre attività correlate alla sicurezza che si verificano all'interno del sistema. AAD può generare una serie di report di riepilogo:

    [! [17]][17]

    Per ulteriori informazioni sull'uso di questi report, vedere [Azure Active Directory Reporting Guide][aad-reporting-guide].

## <a name="topology-considerations"></a>Considerazioni sulla topologia

Se si integra una directory locale con AAD, configurare Azure AD Connect per implementare una topologia che corrisponde maggiormente ai requisiti dell'organizzazione. Topologie Azure AD Connect supporta i seguenti:

- **Singolo insieme di strutture, singola AAD directory**. In questa topologia utilizzare Azure AD Connect per sincronizzare gli oggetti e informazioni personali contenute in uno o più domini in un unico locale insieme di strutture con un singolo tenant AAD. Si tratta della topologia predefinito implementata tramite l'installazione rapida di Azure AD Connect.

    [! [1]][1]

    Non creare più server di sincronizzazione di Azure AD Connect per connettere domini diversi nella stessa foresta locale stesso tenant AAD a meno che non si esegue un server di sincronizzazione di Azure AD Connect in modalità di gestione temporanea (vedere Server di gestione temporanea riportata di seguito).

- **Più insiemi di strutture, singola directory AAD**. Utilizzare questa topologia se si dispone di più di un insieme di strutture locale. È possibile consolidare le informazioni sull'identità in modo che ogni utente univoco viene rappresentato una sola volta nella directory AAD, anche se lo stesso nome utente è presente più di un insieme di strutture. Tutti gli insiemi di strutture utilizzare lo stesso server di sincronizzazione di Azure AD Connect. Il server di sincronizzazione di Azure AD Connect non devono essere parte di qualsiasi dominio, ma deve essere raggiungibile da tutti gli insiemi di strutture:

    [! [2]][2]

    In questa topologia, non usare separata Azure AD Connect sincronizzare server a cui connettersi un singolo tenant AAD ogni foresta locale. Questo può comportare le informazioni sull'identità duplicati in AAD se gli utenti sono presenti più di un insieme di strutture.

- **Più insiemi di strutture, topologie separate**. Questo approccio consente di unire le informazioni sull'identità da insiemi di strutture separati in un singolo tenant AAD. Questa strategia è utile se si desidera combinare gli insiemi di strutture delle diverse aziende (dopo una fusione o acquisizione, ad esempio) e le informazioni sull'identità per ogni utente viene mantenuti solo un insieme di strutture:

    Se gli elenchi globali in ogni foresta sono sincronizzati, un utente in un insieme di strutture potrebbe essere presenta in un'altra come contatto. Questo può verificarsi se, ad esempio, l'organizzazione ha implementato GALSync con Forefront Identity manager 2010 o Microsoft Identity Manager 2016. In questo scenario, è possibile specificare che gli utenti devono essere identificati dall'attributo di *posta elettronica* . È anche possibile associare identità con gli attributi *ObjectSID* e *msExchMasterAccountSID* . Questo è utile se si dispone di uno o più strutture di risorse con account disabilitati.

- **Server di gestione temporanea**. In questa configurazione, eseguire una seconda istanza del server di sincronizzazione di Azure AD Connect in parallelo con la prima. Questa struttura supporta gli scenari, ad esempio:

    - Disponibilità.

    - Test e distribuzione di una nuova configurazione del server di sincronizzazione di Azure AD Connect.

    - Introduzione a un nuovo server e rimuovere una configurazione precedente. 

    In questi scenari, la seconda istanza viene eseguito in *modalità di gestione temporanea*. Il server registra gli oggetti importati e sincronizzazione dei dati nel database, ma non i dati a AAD. Solo quando si disattiva modalità di gestione temporanea server iniziare a scrivere dati AAD e avvii password esecuzione scrittura miniatura nelle directory locale eventualmente:

    [! [4]][4]

    Per ulteriori informazioni, vedere [sincronizzazione di Azure AD Connect: considerazioni e attività operative][aad-connect-sync-operational-tasks].

- **AAD più directory**. È consigliabile che si crea una singola directory AAD per un'organizzazione, ma si possono verificare situazioni in cui è necessario informazioni partizione tra directory AAD distinte. In questo caso, è necessario assicurarsi che ogni oggetto dall'insieme di strutture locale viene visualizzato solo in una directory AAD, per evitare problemi di writeback di sincronizzazione e la password.

    Per implementare questo scenario, configurare separata Azure AD Connect sincronizzare server per ogni directory AAD e usare i filtri in modo che ogni server di sincronizzazione di Azure AD Connect opera su un insieme di oggetti che si escludono a vicenda: 

    [! [5]][5]

Per ulteriori informazioni su queste topologie, vedere [topologie per Azure AD Connect][aad-topologies].

## <a name="user-sign-in-considerations"></a>Considerazioni di accesso utente

Per impostazione predefinita, il servizio AAD presuppone che gli utenti accedono fornendo la stessa password che utilizzano locale e il server di sincronizzazione di Azure AD Connect consente di configurare la sincronizzazione delle password tra il dominio locale e AAD. Per molte organizzazioni, si tratta appropriata, ma è necessario prendere in considerazione i criteri esistenti e infrastruttura dell'organizzazione. Per esempio:

- I criteri di sicurezza dell'organizzazione potrebbero non essere possibile sincronizzare hash delle password nel cloud.

- È possibile richiedere che gli utenti riscontrare perfetta Single Sign-On (senza password aggiuntiva viene visualizzato un prompt) quando accesso alle risorse cloud da dominio aggiunto computer della rete aziendale.

- L'organizzazione dispone già di ADFS o una terza parte federazione provider distribuito. È possibile configurare AAD per utilizzare questa infrastruttura per implementare l'autenticazione e Single Sign-on anziché con informazioni relative alla password contenute nel cloud.

Per ulteriori informazioni, vedere [Azure Active Directory la connessione di accesso dell'utente opzioni][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Considerazioni sui proxy di applicazione Azure Active Directory

Utilizzare Azure Active Directory per consentire l'accesso alle applicazioni locale.

- Esporre le applicazioni web locale mediante proxy applicazione connettori gestire dal componente proxy applicazione Azure Active Directory. Il connettore proxy applicazione apre una connessione di rete in uscita al proxy di applicazione di Azure Active Directory. Le richieste di utenti remoti vengono inviate nuovamente dal AAD mediante la connessione a web apps. Questo meccanismo rimuove la necessità di aprire in ingresso porte nel firewall locale, riducendo la possibilità di attacchi esposto dall'organizzazione.

Per ulteriori informazioni, vedere [applicazioni pubblica mediante proxy di Azure Active Directory applicazione][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Considerazioni sulla sincronizzazione di oggetto

La configurazione predefinita di Azure AD Connect Sincronizza oggetti dalla directory Active Directory locale in base al set di regole specificato nell'articolo [sincronizzazione di Azure AD Connect: informazioni sulla configurazione predefinita][aad-connect-sync-default-rules]. Solo gli oggetti che soddisfano le regole vengono sincronizzate, altri vengono ignorati. Ad esempio, gli oggetti utente devono avere un attributo univoco *sourceAnchor* e l'attributo *accounEnabled* deve essere compilato. Gli oggetti utente che non ha un attributo *sAMAccountName* o che iniziano con il testo *AAD_* o *MSOL_* non vengono sincronizzati. Azure AD Connect applica le regole diversi oggetti utente, contatto, gruppo, ForeignSecurityPrincipal e Computer. Se è necessario modificare il set di regole predefinito, utilizzare l'Editor di regole di sincronizzazione installata con Azure AD Connect (anche descritte in [sincronizzazione di Azure AD Connect: informazioni sulla configurazione predefinita][aad-connect-sync-default-rules]).

È possibile definire filtri per limitare gli oggetti da sincronizzare, dominio o unità Organizzativa. O implementare più complessi il filtro personalizzato, come descritto in [sincronizzazione di Azure AD Connect: configurare il filtro][aad-filtering].

## <a name="security-considerations"></a>Considerazioni sulla protezione

Utilizzare il controllo dell'accesso condizionale per negare le richieste di autenticazione da origini non previsti:

- Attivare [L'autenticazione a più fattori Azure (MFA)] [ azure-multifactor-authentication] se si tenta di effettuare la connessione da un percorso non attendibile (ad esempio in Internet) invece di una rete attendibile.

- Utilizzare il tipo di piattaforma dispositivo dell'utente (iOS, Android, Windows Mobile, Windows) per determinare i criteri di accesso alle funzionalità e le applicazioni.

- Registrare lo stato di attivazione/disattivazione dei dispositivi degli utenti e incorporare i controlli di criteri di accesso di queste informazioni. Ad esempio, se il telefono dell'utente perdito o furto devono essere registrato come disattivato per evitare che venga utilizzato per accedere.

- Controllare il livello di accesso a un utente in base all'appartenenza. Usare [le regole di appartenenza dinamico AAD] [ aad-dynamic-membership-rules] per semplificare l'amministrazione di gruppo. Per una breve panoramica del funzionamento, vedere [Introduzione alle appartenenze dinamico per i gruppi di][aad-dynamic-memberships].

- Utilizzare criteri di accesso condizionato dei rischi con protezione dell'identità AAD per fornire protezione avanzata in base alle attività di accesso insolita o altri eventi.

Per ulteriori informazioni, vedere [accesso condizionale Azure Active Directory][aad-conditional-access].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Scalabilità indirizzata dal servizio AAD e la configurazione del server di sincronizzazione di Azure AD Connect:

- Per il servizio AAD, non è necessario configurare le opzioni per implementare scalabilità. Il servizio AAD supporta scalabilità in base a repliche. AAD implementata una singola replica principale, che gestisce le operazioni di scrittura, e più replica di secondario di sola lettura. AAD reindirizza trasparente scrive tentativo effettuate repliche secondarie alla replica di principale. AAD offre la coerenza finale. Tutte le modifiche apportate replica primaria vengono propagate alle repliche secondarie. Come la maggior parte delle operazioni su AAD legge invece di scrittura, questa architettura scalabilità.

    Per ulteriori informazioni, vedere [Azure Active Directory: dietro le quinte la directory cloud geografico ridondanti, disponibilità, distribuito][aad-scalability].

- Per il server di sincronizzazione di Azure AD Connect, è necessario determinare il numero di oggetti, è probabile che per la sincronizzazione dalla directory locale. Se si hanno meno di 100.000 oggetti, è possibile utilizzare il software di SQL Server Express LocalDB predefinito fornito con Azure AD Connect. Se si dispone di un numero maggiore di oggetti, è necessario installare una versione di produzione di SQL Server ed eseguire un'installazione personalizzata di Azure AD Connect specificando che è necessario utilizzare un'istanza di SQL Server.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Come nel caso di problemi di scalabilità, disponibilità si estende il servizio AAD e la configurazione di Azure AD Connect:

- Il servizio AAD è progettato per fornire disponibilità. Non sono disponibili opzioni disponibilità configurabili dall'utente. È distribuito geografico e verrà eseguito in più dati Centra diffusione tutto il mondo, con failover automatico. Se non è disponibile un data center, AAD garantisce che i dati di directory sono disponibili per l'accesso istanza di almeno due ulteriori nella regione dispersione data center.

    >[AZURE.NOTE] Contratto di servizio disponibilità AAD base e Premium servizi garanzie almeno 99,9%. Non esiste alcun contratto di servizio per il livello di AAD gratuito. Per ulteriori informazioni, vedere [contratto di servizio per Azure Active Directory][sla-aad].

- Per aumentare la disponibilità del server di sincronizzazione di Azure AD Connect è possibile eseguire una seconda istanza in modalità di gestione temporanea come descritto nella sezione [Considerazioni sulla topologia](#topology-considerations) . 

    Inoltre, se non si utilizza l'istanza di SQL Server Express LocalDB fornito con Azure AD Connect, quindi è consigliabile disponibilità per SQL Server. Si noti che la soluzione solo disponibilità supportata SQL cluster. Soluzioni, ad esempio che rispecchiano e sempre su non supportate da Azure AD Connect.

    Per altre considerazioni sulla gestione della disponibilità del server di sincronizzazione di Azure AD Connect e come recuperare dopo un errore, vedere [sincronizzazione di Azure AD Connect: considerazioni - ripristino di emergenza e attività operative][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Considerazioni sulla gestione

Esistono due aspetti della gestione dei AAD:

1. Amministrazione AAD nel cloud.

2. Gestire i server di sincronizzazione di Azure AD Connect.

AAD fornisce le seguenti opzioni per la gestione dei domini e directory nel cloud:

- [Modulo di Azure Active Directory PowerShell][aad-powershell]. Usare questo modulo se è necessario creare uno script comuni attività amministrative di Azure Active Directory, ad esempio gestione degli utenti, gestione dei domini e per configurare il single sign-on.

- Azure blade di gestione di Active Directory nel portale di Azure. Questo blade fornisce una visualizzazione interattiva di gestione della directory e consente di controllare e configurare molti aspetti della AAD.

    [! [10]][10]

Azure AD Connect installa i seguenti strumenti che consente di mantenere i servizi di sincronizzazione di Azure AD Connect dal computer locale:

- Console di Azure Active Directory connettersi. Questo strumento consente di modificare la configurazione del server di sincronizzazione di Azure Active Directory, personalizzare come viene eseguita la sincronizzazione, abilitare o disabilitare la modalità di gestione temporanea e cambiare l'accesso in modalità utente (è possibile abilitare ADFS accesso in utilizzo dell'infrastruttura locale).

- Gestione di servizi di sincronizzazione. Usare la scheda *operazioni* in questo strumento per gestire il processo di sincronizzazione e rilevare se una o più parti del processo non sono riuscita. È possibile attivare la sincronizzazione dei manualmente questo strumento. 

    [! [12]][12]

    Nella scheda *connettori* consente di controllare le connessioni per i domini (in locale e nel cloud) a cui è collegato il motore di sincronizzazione:

    [! [13]][13]

-  L'Editor di regole di sincronizzazione. Utilizzare questo strumento per personalizzare il modo in cui gli oggetti vengono trasformati quando vengono copiate tra una directory locale e AAD nel cloud. Questo strumento consente di specificare altri attributi e oggetti per la sincronizzazione e per determinare quali istanze o i filtri non devono essere sincronizzati.

    Per ulteriori informazioni, vedere la sezione Editor di regole di sincronizzazione nel documento [sincronizzazione di Azure AD Connect: informazioni sulla configurazione predefinita][aad-connect-sync-default-rules].

La pagina [sincronizzazione di Azure AD Connect: procedure consigliate per la modifica della configurazione predefinita] [ aad-sync-best-practices] contiene informazioni e suggerimenti per la gestione di Azure AD Connect.

## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio

Il monitoraggio dell'integrità viene eseguito mediante una serie di agenti installati in locale:

- Azure AD Connect installa un agente che vengono acquisite informazioni sulla sincronizzazione. Utilizzare e il Azure Active Directory connettersi integrità nel portale di Azure per monitorare l'integrità e le prestazioni di Azure AD Connect. Per ulteriori informazioni, vedere [Integrità connettersi con Azure Active Directory per la sincronizzazione][aad-health].

- Per monitorare l'integrità dei domini di Active Directory e delle directory comuni, installare lo stato di connettersi AD Azure per agente di Active Directory in un computer all'interno del dominio locale. Utilizzare e il Azure Active Directory connettersi integrità nel portale di Azure monitor Active Directory. Per ulteriori informazioni, vedere [Integrità connettersi con Azure Active Directory con Active Directory][aad-health-adds]

- Installare lo stato di connettersi AD Azure per agente di ADFS monitorare lo stato di ADFS in esecuzione in locale e utilizzare e il Azure Active Directory connettersi integrità nel portale di Azure per eseguire il monitoraggio di dominio Active Directory. Per ulteriori informazioni, vedere [Integrità connettersi con Azure Active Directory con ADFS][aad-health-adfs]

Per ulteriori informazioni sull'installazione di agenti dell'integrità della connessione di Active Directory e i rispettivi requisiti, vedere [Azure Active Directory connettersi integrità agente di installazione di][aad-agent-installation].

## <a name="sample-solution"></a>Soluzione di esempio

Questa sezione viene illustrata la procedura per la creazione di una soluzione di esempio che è possibile utilizzare per testare la configurazione di AAD. La soluzione comprende gli elementi seguenti:

- Un'applicazione web a più livelli, seguendo la struttura descritta dalla [Macchine virtuali in esecuzione per un'architettura di livelli in Azure][implementing-a-multi-tier-architecture-on-Azure].

- Un computer client. È consigliabile utilizzare un altro macchine Virtuali per il computer. La configurazione di questa macchina virtuale non è importante, purché si dispone dell'accesso come amministratore e possibile connettersi all'applicazione web a più livelli.

- Un ambiente locale simulata che contiene il proprio dominio costruita Active Directory.

Sono gli scenari in cui vengono illustrate le operazioni seguenti:

- Abilitazione dell'accesso all'applicazione web a più livelli in esecuzione nel cloud per gli utenti esterni, con AAD fornire autenticazione tramite password.

- Abilitazione dell'accesso all'applicazione web a più livelli in esecuzione nel cloud per gli utenti che eseguono all'interno dell'organizzazione con AAD fornire autenticazione tramite password.

### <a name="prerequisites"></a>Prerequisiti

I passaggi che seguono presuppongono i prerequisiti seguenti:

- Si ha un abbonamento di Azure esistente in cui è possibile creare gruppi di risorse.

- È stato installato l' [interfaccia di riga di comando Azure][azure-cli].

- Scaricato e installato build più recente. Vedere [qui] [ azure-powershell-download] per le istruzioni.

- È stata installata una copia di [makecert] [ makecert] utilità nel computer client di prova.

- È possibile accedere a un computer di sviluppo che ha installato Visual Studio.

### <a name="create-the-n-tier-web-application-architecture"></a>Creare l'architettura di applicazione web a più livelli

1. Creare una cartella denominata `Scripts` che contiene una sottocartella denominata `Parameters`.

2. Scaricare [Distribuisci ReferenceArchitecture.ps1] [ solution-script] script di PowerShell per la cartella script.

3. Nella cartella parametri per creare un'altra sottocartella denominata Windows.

4. Scaricare i file seguenti nella cartella parametri/Windows:

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Modificare il file di **Distribuzione ReferenceArchitecture.ps**1 nella cartella script e modificare la riga seguente per specificare il gruppo di risorse che deve essere creato o utilizzato per contenere le macchine Virtuali e le risorse create dallo script:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Modificare i file seguenti nella cartella s **parametri/finestra**e impostare il `resourceGroup` valore nel `virtualNetworkSettings` sezione in ognuna di questi file per essere lo stesso specificato nel file di script **ReferenceArchitecture.ps1 Distribuisci** .

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Aprire una finestra di PowerShell di Azure, passare alla cartella script ed eseguire il comando seguente:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Sostituire **<subscription id>** con l'ID di abbonamento Azure.

    Per **<location>**, specificare un'area di Azure, ad esempio *eastus* o *westus*.

8. Al termine di script, utilizzare il portale di Azure per ottenere l'indirizzo IP pubblico del livello web carico (*ra aad a più livelli-web-kg*):

    [! [18]][18]

9. Accedere a test client computer (o macchine Virtuali) e verificare che sia possibile accedere a livello web utilizzando Internet Explorer per cercare l'indirizzo IP pubblico di bilanciamento del carico il livello di web. La pagina IIS predefinita dovrebbero essere visualizzate.

### <a name="simulate-configuration-of-a-public-web-site"></a>Consente di simulare la configurazione di un sito web pubblico

>[AZURE.NOTE] La procedura seguente consente di simulare associazione del livello web con il nome DNS www.contoso.com modificando il file hosts nel computer client di prova. Inoltre, le macchine virtuali di livello web sono configurate con certificati e FALSO autorità di hosting. Si tratta di testing solo a scopo e **che non è consigliabile utilizzare queste tecniche in un ambiente di produzione**.

1. Nel computer client test, modificare il file **c:\Windows\System32\drivers\etc\hosts.** utilizzando il blocco note e aggiungere una voce che associa www.contoso.com nome con l'indirizzo IP pubblico del carico livello web:

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Verificare che a questo punto è possibile passare a www.contoso.com dal computer client di prova. La stessa pagina IIS predefinita dovrebbero essere visualizzate come prima.

3. Nel computer client di prova, aprire un prompt dei comandi come amministratore e utilizzare l'utilità makecert a c
4. Crea un certificato radice falsi:

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Verificare che vengano creati i file seguenti:

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Eseguire il comando seguente per installare l'autorità di certificazione test:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Per generare un certificato per www.contoso.com, utilizzare l'autorità di certificazione di test:

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Eseguire il comando di **mmc** e aggiungere snap-in certificati per l'account del computer per il computer locale.

7. Nel */Certificates (Computer locale) / personale/certificato/* archiviare, esportare il certificato www.contoso.com con la chiave privata in un file denominato www.contoso.com.pfx:

    [! [20]][20]

8. Tornare al portale di Azure e connettersi al livello di gestione delle macchine Virtuali (*ra-aad a più livelli-gestione-vm1*). Il nome utente predefinito è *testuser* con password *AweS0me@PW*:

    [! [21]][21]
    
9. Dal livello di gestione delle macchine Virtuali, connettersi a ogni livello web macchine virtuali, a sua volta con il nome utente *testuser* e la password *AweS0me@PW*ed eseguire le operazioni seguenti. Si noti che le macchine virtuali essere privato indirizzi IP 10.0.1.4, 10.0.1.5 e 10.0.1.6:

    >[AZURE.NOTE] Il livello di web macchine virtuali contenere solo gli indirizzi IP privati. È possibile connettersi a tali solo tramite il livello di gestione delle macchine Virtuali.

    1. Copiare il file *www.contoso.com.pfx* e *MyFakeRootCertificateAuthority.cer* dal computer client di prova.
    
    2. Aprire un prompt dei comandi come amministratore, passare alla cartella contenente i file che sono stati copiati ed eseguire i comandi seguenti:
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Eseguire il `mmc` comando, aggiungere snap-in certificati per l'account del computer per il computer locale e verificare che siano installati i certificati seguenti:

        - Www.contoso.com \Personal\Certificates\ \Certificates (Computer locale)

        - Authorities\Certificates\MyFakeRootCertificateAuthority di certificazione radice \Trusted \Certificates (Computer locale)

    4. Avviare la console di Gestione Internet Information Services (IIS) e passare al *sito Web Sites\Default* nel computer.

    5. Nel riquadro *Azioni* , fare clic su *associazioni*e aggiungere un'associazione https utilizzando il certificato SSL www.contoso.com:

        [! [22]][22]

10. Tornare al computer client di prova e verificare che a questo punto è possibile passare a https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Creare un tenant di Azure Active Directory

1. Tramite il portale di Azure, creare un tenant di Azure Active Directory, ad esempio *myaadname*. onmicrosoft.com, dove *myaadname* è un nome di directory selezionato dall'utente.

2. Aggiungere un utente con il nome *amministratore* con il ruolo di GlobalAdmin alla directory. Prendere nota della password appena generata.

3. In Internet Explorer, individuare la https://account.activedirectory.windowsazure.com/ e accedere come admin@ *myaadname*. onmicrosoft.com. Cambiare la password quando richiesto.

### <a name="create-and-deploy-a-test-web-application"></a>Creare e distribuire un'applicazione web di test

1. Utilizzo di Visual Studio nel computer di sviluppo, creare un'applicazione Web ASP.NET denominata ContosoWebApp1 (utilizzare .NET Framework 4.5.2):

    [! [23]][23]

2. Selezionare il modello *MVC* , modificare l'autenticazione agli *account di lavoro e dell'istituto di istruzione*e specificare il nome del tenant di AAD. Non lo si crea un servizio di App nel cloud.

    [! [24]][24]

3. Creare ed eseguire l'applicazione per verificare l'autenticazione. Nella pagina di accesso immettere il nome dell'account admin@ *myaadname*. onmicrosoft.com, immettere la password e quindi fare clic su *accesso*:

    [! [25]][25]

4. Verificare che AAD chiede l'autorizzazione di accesso e leggere il profilo e quindi viene avviato l'applicazione web con l'identità dell'amministratore.

5. Chiudere Internet Explorer e tornare a Visual Studio.

6. Aprire il file config e il `<appSettings>` delle sezioni, modificare il valore della chiave *ida: PostLogoutRedirectUri* *https://www.contoso.com:443 /*. Salvare il file.

7. Nella finestra *Esplora soluzioni* fare clic sul progetto ContosoWebApp1, fare clic su *pubblica*.

8. Nella finestra *Pubblica sito Web* fare clic su *personalizzato*. Creare un profilo personalizzato denominato *ContosoWebApp1*.

9. Nella pagina *connessione* , impostare il *metodo di pubblicazione* al *Sistema di File* e specificare una cartella denominata *ContosoWebApp*, disponibile nella posizione desiderata nel computer di sviluppo.

10. Nella pagina *Impostazioni* impostare la *configurazione* di *rilascio*.

11. Nella pagina di *Anteprima* , fare clic su *pubblica*.

12. Connettersi a ogni server web, a sua volta (tramite il livello di gestione delle macchine Virtuali) e le seguenti operazioni:

    1. Copiare la cartella *ContosoWebApp* e il relativo contenuto dal computer di sviluppo *C:\inetpub* nella cartella.

    2. Tramite la console di Gestione Internet Information Services (IIS), passare al *sito Web Sites\Default* nel computer.

    3. Nel riquadro *Azioni* , fare clic su *Impostazioni di base*e modificare il percorso fisico del sito web di *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Pubblicare l'applicazione web di test tramite AAD

1. Accedere al portale di Azure e passare alla directory AAD.

2. Nella scheda *applicazioni* fare clic sull'applicazione ContosoWebApp1.

3. Verificare che l'applicazione viene aggiunto all'elenco e quindi fare clic sulla scheda *Configura* .

4. Ripristinare l' *URL di accesso via* https://www.contoso.com:443 e impostare l' *URL di risposta* su https://www.contoso.com:443 (lo stesso URL).

5. Salvare la configurazione.

6. Nel computer client di prova, passare alla https://www.contoso.com. Verificare che AAD verranno richieste le credenziali e quindi accedere.

>[AZURE.NOTE] Questo è il punto finale per il primo scenario: abilitazione dell'accesso all'applicazione web a più livelli in esecuzione nel cloud per gli utenti esterni, con AAD fornire autenticazione tramite password.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Creare un ambiente locale simulata con Active Directory

L'ambiente locale contiene due controller di dominio per il `contoso.com` dominio e due server per l'hosting Azure AD Connect sincronizzare servizio. Il server per l'hosting Azure AD Connect non vengono unite dominio.

1. In Esplora File, tornare alla cartella script che contiene lo script utilizzato per creare l'applicazione web a più livelli.

2. Nella cartella parametri per aggiungere un'altra cartella sub denominata `Onpremise`.

3. Scaricare i file seguenti nella cartella parametri/locale:

    - [aggiungere-aggiunge-dominio-controller.parameters.json][add-adds-domain-controller-parameters]

    - [creare-aggiunge-foresta-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines adc.parameters.json][virtualMachines-adc-parameters]

    - [joindomain.parameters.json di adc virtualMachines][virtualMachines-adc-joindomain-parameters]

    - [virtualMachines adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [dns.parameters.json aggiunge virtualNetwork][virtualNetwork-adds-dns-parameters]

5. Modificare il file di distribuzione ReferenceArchitecture.ps1 nella cartella script e modificare la riga seguente per specificare il gruppo di risorse che deve essere creato o utilizzato per contenere le macchine Virtuali e le risorse create dallo script:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Modificare i file seguenti nella cartella parametri/locale e impostare il `resourceGroup` valore nel `virtualNetworkSettings` sezione in ognuna di questi file per essere lo stesso specificato nel file di script ReferenceArchitecture.ps1 Distribuisci.

    - virtualMachines adds.parameters.json

    - virtualMachines adc.parameters.json

    - virtualNetwork.parameters.json

    - dns.parameters.json aggiunge virtualNetwork

7. Aprire una finestra di PowerShell di Azure, passare alla cartella script ed eseguire il comando seguente:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Sostituire `<subscription id>` con l'ID di abbonamento Azure.

    Per `<location>`, specificare un'area di Azure, ad esempio `eastus` o `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Installare e configurare il servizio di sincronizzazione di Azure AD Connect

La configurazione illustrata in questa procedura è costituito da due istanze del servizio di sincronizzazione di Azure AD Connect. Il primo è attivo, mentre il secondo è configurato in modalità di gestione temporanea per fornire failover rapido e disponibilità, se il primo non funziona.

1. Tramite il portale di Azure, passare al gruppo di risorse contenente macchine virtuali per i servizi di sincronizzazione di Azure AD Connect (*ra-aad locale rg* per impostazione predefinita) e connettersi a *ra-aad-locale-adc-vm1* macchine Virtuali. Eseguire l'accesso come *testuser* con password *AweS0me@PW*.

2. Scaricare Azure AD Connect da [qui][aad-connect-download].

3. Avviare il programma di installazione di Azure AD Connect ed eseguire un'installazione tramite l'opzione *Personalizza* .

    >[AZURE.NOTE] È possibile utilizzare l'opzione *Impostazioni rapide* perché la macchina virtuale che ospita il servizio di sincronizzazione di Azure AD Connect non è dominio.

4. Nella pagina *Installa componenti necessari* lasciare vuoto per accettare le opzioni predefinite le impostazioni di configurazione facoltativo.

5. Nella pagina *account utente* , selezionare *La sincronizzazione delle Password*.

6. Nella pagina *connessione a Azure Active Directory* immettere admin@ *myaadname*. onmicrosoft.com, nel punto in cui *myaadname* è il nome del tenant di AAD. Immettere la password per l'account dell'amministratore.

7. Nella pagina *connettere le directory* specificare contoso.com per l'insieme di strutture (digitare il valore in quanto non compare nell'elenco a discesa), immettere CONTOSO\testuser per il nome utente, specificare AweS0me@PW per la password, quindi fare clic su *Aggiungi Directory*.

8. Nella pagina *configurazione di accesso Azure AD* accettare il valore predefinito per il nome dell'entità utente. Selezionare *Continua senza eventuali domini verificati*.

    >[AZURE.NOTE] Directory contoso.com sono elencati come *Non verificati*. Per verificare un nome di dominio, è necessario creare un record TXT per il registrar di nomi di dominio. In questo esempio, il dominio locale non è registrato esternamente. Per ulteriori informazioni, vedere [aggiungere un nome di dominio personalizzato in Azure Active Directory][aad-custom-directory].

9. Nella pagina *Domain e unità Organizzativa filtro* selezionare *sincronizzare tutti i domini e unità organizzative* (impostazione predefinita).

10. Nella pagina *identifica gli utenti* , accettare i valori predefiniti.

11. Nella pagina *filtrare gli utenti e dispositivi* selezionare *sincronizzare tutti gli utenti e dispositivi* (impostazione predefinita).

12. Nella pagina *caratteristiche facoltative* selezionare *writeback di Password*.

13. Nella pagina *pronto per la configurazione* , selezionare *Avvia il processo di sincronizzazione al termine della configurazione*, ma lasciare *abilitare la modalità di gestione temporanea* deselezionata.

14. Verificare che il processo di configurazione viene completata senza errori e quindi chiudere il programma di installazione.

15. Dal portale di Azure, connettersi a *ra-aad-locale-adc-vm2* macchine Virtuali. Eseguire l'accesso come *testuser* con password *AweS0me@PW*.

16. Scaricare Azure AD Connect e quindi eseguire il programma di installazione.

17. Eseguire la procedura guidata e rispondere come descritto nei passaggi da 3 a 12.

18. Nella pagina *pronto per la configurazione* , selezionare *Avvia il processo di sincronizzazione dopo avere completato la configurazione*e **selezionare anche** *attivare la modalità di gestione temporanea*. In questo modo il servizio di sincronizzazione di Azure AD Connect recuperare informazioni sugli oggetti e gli account da tale dominio e archiviarli nel database, ma non trasmette questi dettagli al tenant di AAD.

14. Verificare che il processo di configurazione viene completata senza errori e quindi chiudere il programma di installazione.

### <a name="test-the-aad-configuration"></a>Testare la configurazione di AAD

1. Tramite il portale di Azure, passare alla directory AAD, aprire e il Azure Active Directory, fare clic su *utenti e gruppi*e quindi fare clic su *tutti gli utenti* per visualizzare l'elenco di utenti e gruppi sincronizzati con il servizio directory. Verrà visualizzato agli utenti per gli account seguenti:

    - amministrazione (admin@ *myaadname*. onmicrosoft.com)

    - Account del servizio di sincronizzazione della Directory locale (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Account del servizio di sincronizzazione della Directory locale (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. Nel portale di Azure, passare al gruppo di risorse contenente macchine virtuali per i controller di dominio Active Directory (*ra-aad locale rg* per impostazione predefinita) e connettersi a *ra-aad-locale-Active Directory-vm1* macchine Virtuali. Eseguire l'accesso come *testuser* con password *AweS0me@PW*.

3. Tramite la console di *Active Directory Users e computer* , aggiungere un nuovo utente di dominio denominato Diane Tibbot, con nome di accesso dianet@contoso.com. Specificare una password di propria scelta. Rendere un membro del gruppo Administrators locale l'utente (è solo in modo che è possibile accedere localmente come questo utente in un secondo momento, l'unico computer del dominio sono controller di dominio).

4. Passare alla *ra-aad-locale-adc-vm1* macchine Virtuali, aprire una finestra di PowerShell e digitare quanto segue:

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Verificare che il comando restituisce *esito positivo*.

    >[AZURE.NOTE] Questa operazione è necessaria in quanto per impostazione predefinita è programmato il processo di sincronizzazione a intervalli di 30 minuti. Questi comandi forzare una sincronizzazione venga eseguita immediatamente.

5. Tornare al portale di Azure, passare alla directory di AAD, e il Azure Active Directory, fare clic su *utenti e gruppi*e quindi fare clic su *tutti gli utenti*. Verrà visualizzata Diane Tibbot (dianet@ *myaadname*. onmicrosoft.com) vengono visualizzati nell'elenco degli utenti.

6. In e il Azure Active Directory, fare clic su *Applicazioni aziendali*e quindi fare clic su *tutte le applicazioni*. Fare clic sull'applicazione di *ContosoWebApp1* e quindi fare clic su *utenti e gruppi*. Nella barra degli strumenti fare clic su *Aggiungi*. Fare clic su *utenti e gruppi*e selezionare *Sofia Tibbot*. Fare clic su *Assegna*.

7. Uso di Internet Explorer, passare a https://account.activedirectory.windowsazure.com del sito. Eseguire l'accesso come dianet@ *myaadname*. onmicrosoft.com con la password specificata in precedenza.

    >[AZURE.NOTE] Non fare clic sull'icona ContosoWebApp nell'elenco delle applicazioni. AAD tenta di trovare l'applicazione web all'indirizzo DNS pubblicamente elencato per www.contoso.com, che è diverso dall'indirizzo di bilanciamento del carico il livello di web.

8. Fare clic sulla scheda *profilo* . Devono essere visualizzati i dettagli dell'utente (se si specifica una quando è stata creata).

    >[AZURE.NOTE] Se si fa clic su *Cambia password*, non è consentito eseguire questa operazione come questa operazione deve essere attivata dall'amministratore prima di tutto. Per ulteriori informazioni, vedere [Guida introduttiva alla gestione delle Password][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Consentire agli utenti locali eseguire l'applicazione tramite l'autenticazione tramite AAD

1. Tornare al controller di dominio *ra-aad-locale-Active Directory-vm1* macchine Virtuali.

2. Aprire la console *DNS Manager* e aggiungere un nuovo record di host www.contoso.com. Specificare l'indirizzo IP pubblico del carico livello web.

3. Copiare il file *MyFakeRootCertificateAuthority.cer* dal computer client di prova (creato questo file la procedura per [simulare la configurazione di un sito web pubblico](#simulate-configuration-of-a-public-web-site)
    
4. Aprire un prompt dei comandi come amministratore, passare alla cartella che contiene il file copiato ed eseguire il comando seguente:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Uso di Internet Explorer, passare a https://www.contoso.com. Verificare che la pagina di accesso AAD per l'applicazione web ContosoWebApp1 visualizzata.

6. Eseguire l'accesso come dianet@ *myaadname*. onmicrosoft.com. Eseguire l'applicazione e di accesso in modo corretto.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle procedure consigliate per [l'estensione del dominio aggiunge locale in Azure][adds-extend-domain]

- Informazioni sulle procedure consigliate per la [creazione di un insieme di strutture risorsa aggiunge] [ adds-resource-forest] in Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Architettura di identità del cloud usando Azure Active Directory"
[1]: ./media/guidance-identity-aad/figure2.png "Singola foresta, singolo topologia directory AAD"
[2]: ./media/guidance-identity-aad/figure3.png "Più insiemi di topologia di directory AAD singola"
[4]: ./media/guidance-identity-aad/figure5.png "Topologia di server di gestione temporanea"
[5]: ./media/guidance-identity-aad/figure6.png "Topologia a più AAD directory"
[6]: ./media/guidance-identity-aad/figure7.png "Selezione di un'installazione personalizzata di Azure sincronizzazione Active Directory connettersi con una specifica istanza di SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Specificare il metodo di Single Sign-on per l'accesso utente"
[8]: ./media/guidance-identity-aad/figure9.png "Specificando dominio e unità Organizzativa opzioni di filtro"
[9]: ./media/guidance-identity-aad/figure10.png "Attivazione writeback di password"
[10]: ./media/guidance-identity-aad/figure11.png "E il management Azure Active Directory nel portale"
[11]: ./media/guidance-identity-aad/figure12.png "Console di Azure AD Connect"
[12]: ./media/guidance-identity-aad/figure13.png "Scheda operazioni di Gestione servizio di sincronizzazione"
[13]: ./media/guidance-identity-aad/figure14.png "Nella scheda connettori in Gestione servizi di sincronizzazione"
[14]: ./media/guidance-identity-aad/figure15.png "L'Editor di regole di sincronizzazione"
[15]: ./media/guidance-identity-aad/figure16.png "E il Azure Active Directory connettersi integrità nel portale di Azure con dello stato di sincronizzazione"
[16]: ./media/guidance-identity-aad/figure17.png "E il Azure Active Directory connettersi integrità nel portale di Azure con integrità di Active Directory"
[17]: ./media/guidance-identity-aad/figure18.png "Report di sicurezza disponibili nel portale di Azure"
[18]: ./media/guidance-identity-aad/figure19.png "Portale di Azure evidenziazione l'indirizzo IP pubblico del carico livello web"
[19]: ./media/guidance-identity-aad/figure20.png "Uso di Internet Explorer per esplorare l'indirizzo IP pubblico del carico livello web"
[20]: ./media/guidance-identity-aad/figure21.png "Snap-in certificati con il certificato www.contoso.com"
[21]: ./media/guidance-identity-aad/figure22.png "Connessione a livello di gestione delle macchine Virtuali"
[22]: ./media/guidance-identity-aad/figure23.png "Creazione di associazione HTTPS per il sito web predefinito"
[23]: ./media/guidance-identity-aad/figure24.png "Creazione dell'applicazione web ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Impostare le proprietà di autenticazione dell'applicazione web ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "Accesso a AAD Azure dall'applicazione web ContosoWebApp1"
[26]: ./media/guidance-identity-aad/figure27.png "Modificare la cartella per il sito web predefinito"