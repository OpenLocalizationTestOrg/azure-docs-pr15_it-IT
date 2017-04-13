<properties
   pageTitle="Applicazione dipendenza Monitor (ADM) nel gruppo Gestione operazioni (OMS) | Microsoft Azure"
   description="Applicazione dipendenza Monitor (ADM) è una soluzione di operazioni gestione famiglia di prodotti (OMS) che individua componenti di applicazioni nei sistemi Windows e Linux e mappe le comunicazioni tra servizi automaticamente.  Questo articolo fornisce informazioni dettagliate per la distribuzione ADM nel proprio ambiente e usarlo in diversi scenari."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Utilizzo soluzione applicazione dipendenza Monitor in operazioni di gestione famiglia di prodotti (OMS)
![Icona di avviso di gestione](media/operations-management-suite-application-dependency-monitor/icon.png) Applicazione dipendenza Monitor (ADM) automaticamente individua componenti di applicazioni nei sistemi Windows e Linux e mappe le comunicazioni tra i servizi. Consente di visualizzare i server come devono essere considerati – come sistemi interconnessi che offrono servizi critici.  Applicazione dipendenza Monitor mostra le connessioni tra server, processi, e porte in qualsiasi architettura connessi TCP senza configurazioni richieste ad eccezione di installazione di un agente.

In questo articolo vengono illustrati i dettagli di utilizzo Monitor dipendenza dell'applicazione.  Per informazioni su come configurare gli agenti ADM e l'adozione, vedere [configurazione dell'applicazione dipendenza Monitor soluzione in operazioni di gestione famiglia di prodotti (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]Applicazione dipendenza Monitor è attualmente in anteprima privato.  È possibile richiedere l'accesso all'anteprima privato ADM in [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante l'anteprima privato, tutti gli account OMS avere accesso illimitato per adm.  I nodi ADM sono gratuiti, ma verranno contatore dati Analitica Log per i tipi di AdmComputer_CL e AdmProcess_CL come qualsiasi altra soluzione.
>
>Dopo aver ADM immette anteprima pubblica, sarà disponibile solo per i clienti gratuiti e a pagamento di informazione e Analitica nel OMS prezzi piano.  Account di livello gratuito saranno limitati a 5 nodi ADM.  Se si partecipa nel riquadro di anteprima privato e non registrato nel piano di prezzi di OMS quando ADM entra anteprima pubblica, ADM saranno disabilitate in quel momento. 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casi di utilizzo: Rendere l'IT elabora dipendenza presente

### <a name="discovery"></a>Individuazione
ADM crea automaticamente una mappa di riferimento comuni delle dipendenze tra server, processi e servizi di terze parti 3 °.  Individua ed esegue il mapping di tutte le dipendenze TCP, identificando sorprese connessioni remote 3 ° sistemi di terze parti dipendono e dipendenze tra tradizionale scure aree della rete, ad esempio DNS e Active Directory.  ADM individua connessioni di rete non riuscito che i sistemi gestiti siano tentando di stabilire, consentono di identificare i potenziali errori di configurazione server, interruzioni dei servizi e problemi di rete.

### <a name="incident-management"></a>Incidente
ADM consente di eliminare le incognite di isolare il problema con la modalità di connessione sistemi e che interessano tra loro.  Oltre alle connessioni non riuscite, informazioni sui client connessi consentono di identificare i servizi di bilanciamento del carico non configurati correttamente, sorprendente o eccessivo carico sui servizi critici e malintenzionati client, ad esempio computer di sviluppo parlare con i sistemi di produzione.  Flussi di lavoro integrati con OMS modificare verifica è anche possibile verificare se un evento di modifica in un computer back-end o servizio illustra la causa del problema.

### <a name="migration-assurance"></a>Migrazione Assurance
ADM consente di pianificare, accelerare e convalidare le migrazioni Azure, in modo efficace controllando che tralasciare nulla e sono non disponibili interruzioni sorprese.  È possibile scoprire tutti i sistemi indipendenti che devono eseguire la migrazione insieme, valutare le capacità e configurazione di sistema e identificare un sistema in esecuzione viene ancora gli utenti o è adatto per disattivazione invece di migrazione.  Dopo lo spostamento, è possibile controllare carico di lavoro e identità per verificare che i sistemi di test e clienti connessi.  Se le definizioni di pianificazione e il firewall subnet sono verificati problemi, connessioni non riuscite nelle mappe di ADM indirizza l'utente in sistemi che richiedono connettività.

### <a name="business-continuity"></a>Continuità aziendale
Se si utilizza il ripristino del sito di Azure e informazioni della Guida che definisce la sequenza di ripristino per l'ambiente di applicazione ADM possibile automaticamente Mostra come sistemi si basano su altro per assicurarsi che il piano di ripristino è affidabile.  Scelta di un server critico e visualizzando suoi clienti, è possibile identificare i sistemi front-end devono essere ripristinati solo dopo tale server critiche vengono ripristinati e sono disponibili.  Esaminando le dipendenze back-end del server critico, al contrario, è possibile identificare i sistemi che devono essere ripristinati prima del ripristino del sistema lo stato attivo.

### <a name="patch-management"></a>Gestione delle patch
ADM migliora l'uso di valutazione di aggiornamento di sistema OMS, con indicazione altri team e server dipendono dal servizio di posta in modo che è possibile comunicare loro in anticipo prima di portare i sistemi verso il basso per l'applicazione patch.  ADM migliora anche la gestione delle patch in OMS mostrando se il provider di servizi sono disponibili e connesso correttamente dopo che sono corretti e si riavvia. 


## <a name="mapping-overview"></a>Panoramica di mapping
Gli agenti ADM raccolgono informazioni su tutti i processi connessi TCP su server in cui sono stati installati, nonché informazioni dettagliate sulle connessioni in ingresso e in uscita per ogni processo.  Utilizzando l'elenco di computer sul lato sinistro della soluzione ADM, macchine con agenti ADM possono essere selezionate per visualizzare le relative dipendenze in un intervallo di tempo selezionato.  Dipendenza Machine mapping lo stato attivo su un determinato computer e visualizzare tutti i computer client TCP diretti sono server del computer.

![Panoramica ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

È possibile espandere macchine della mappa per mostrare i processi in esecuzione con le connessioni di rete attiva durante l'intervallo di tempo selezionato.  Quando un computer remoto con un agente ADM viene espansa per visualizzare i dettagli di processo, vengono visualizzati solo i processi di comunicare con il computer lo stato attivo.  Sul lato sinistro di processi a che si connettono è indicato il numero di senza agenti front-end che si connettono al computer lo stato attivo.  Se il computer lo stato attivo sta effettuando una connessione a un computer back-end senza un agente che back-end viene rappresentato con un nodo della mappa che mostra il relativo indirizzo IPv4 e il nodo può essere espanso per visualizzare le singole porte e i servizi che comunica con il computer lo stato attivo.

Per impostazione predefinita, mappe ADM mostrano gli ultimi 10 minuti di informazioni sulle dipendenze.  Utilizzando i controlli di tempo nell'angolo superiore sinistro, mappe possono eseguire una query per gli intervalli di tempo cronologico, fino al livello di un'ora, per mostrare come dipendenze esaminato in precedenza, ad esempio durante un incidente o prima che è stata apportata una modifica.    ADM dati vengono archiviati per 30 giorni nelle aree di lavoro a pagamento e per 7 giorni nelle aree di lavoro gratuiti.

![Computer mapping con proprietà computer selezionato](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Connessioni non riuscite
Connessioni vengono visualizzate nelle mappe di ADM per computer e processi con con una linea rossa tratteggiata se un sistema client non riesce a raggiungere un processo o porta.  Connessioni non riuscite vengono segnalate da qualsiasi sistema con un agente ADM distribuito se tale sistema è un tentativo di connessione non riuscita.  ADM misura questo osservando socket TCP che non riescono a stabilire una connessione.  Può trattarsi a causa di un firewall, una configurazione errata nel client o server o un servizio remoto non disponibile. 

![Connessioni non riuscite](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Informazioni sulle connessioni non riuscite consente la risoluzione dei problemi, convalida di migrazione, l'analisi di sicurezza e understanding complessivo architettonica.  A volte non è riuscita connessioni non sia dannoso, ma spesso non fanno riferimento direttamente a un problema, ad esempio un ambiente failover improvvisamente diventando non è raggiungibile,... o due livelli di applicazione non è in grado di comunicare dopo la migrazione cloud.  Nell'immagine, IIS e WebSphere sia in esecuzione, ma non possono connettersi. 

## <a name="computer-and-process-properties"></a>Computer e le proprietà di processo
In caso di spostamento una mappa ADM, è possibile selezionare i computer e processi di ottenere informazioni di contesto aggiuntive sulle relative proprietà.  Computer forniscono informazioni sulle nome DNS, indirizzi IPv4, capacità CPU e memoria, tipo di macchine Virtuali, versione del sistema operativo, riavviare ultima volta e gli ID di loro agenti OMS e ADM.

Dettagli del processo sono raccolte dal sistema operativo metadati sui processi, inclusi il nome del processo, Descrizione processo, nome utente e dominio (Windows), il nome della società, il nome del prodotto, versione del prodotto, cartella di lavoro, riga di comando e ora di inizio processo in esecuzione.

![Proprietà del processo](media/operations-management-suite-application-dependency-monitor/process-properties.png)

Riquadro Riepilogo processo fornisce informazioni aggiuntive su connettività del processo, incluse le porte associate, le connessioni in ingresso e in uscita e connessioni non riuscite. 

![Riepilogo processo](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Integrazione OMS rilevamento
Integrazione di ADM con il rilevamento delle modifiche è automatica quando sono abilitate e configurate nell'area di lavoro OMS entrambe le soluzioni.

Il riquadro di riepilogo computer indica se il rilevamento delle modifiche si sono verificati eventi nel computer selezionato durante l'intervallo di tempo selezionato.

![Pannello riepilogo computer](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

Riquadro verifica cambia computer viene visualizzato un elenco di tutte le modifiche, con il primo più recente, insieme a un collegamento a drill-down dei Log ricerca per informazioni dettagliate.
![Pannello di rilevamento modifiche computer](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Di seguito è riportato un drill-down visualizzazione dell'evento di modifica della configurazione dopo aver selezionato **visualizzare nel Log Analitica**.
![Evento di modifica di configurazione](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Registro Analitica record
Dati di inventario del ADM computer e processo sono disponibili per la [ricerca](../log-analytics/log-analytics-log-searches.md) nel registro Analitica.  Possono essere applicata a scenari inclusi pianificazione della migrazione, analisi della capacità individuazione e risoluzione dei problemi di prestazioni ad hoc. 

Un record è orarie per ogni computer univoci e processo oltre a record generati quando che processo o computer viene avviato o su-aggiunta a adm.  Questi record hanno le proprietà nelle tabelle seguenti. 

Sono disponibili le proprietà internamente generate è possibile utilizzare per identificare i processi univoci e computer:

- PersistentKey_s in modo univoco definito dalla configurazione del processo, ad esempio riga di comando e utente ID.  Sia univoco per un determinato computer, ma può essere ripetuto su computer.
- ProcessId_s e ComputerId_s sono univoci nel modello ADM.



### <a name="admcomputercl-records"></a>Record AdmComputer_CL
Record con un tipo di **AdmComputer_CL** hanno dati di inventario per server con agenti ADM.  Questi record hanno le proprietà nella tabella seguente.  

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nome del computer Windows o Linux |
| CPUSpeed_d | Velocità delle CPU in MHz |
| DnsNames_s | Elenco di tutti i nomi DNS per il computer |
| IPv4s_s | Elenco di tutti gli indirizzi IPv4 di questo computer |
| IPv6s_s | Elenco di tutti gli indirizzi IPv6 utilizzato da questo computer.  (ADM identifica gli indirizzi IPv6 ma non rileva le dipendenze IPv6). |
| Is64Bit_b | true o false in base al tipo di sistema operativo |
| MachineId_s | Un GUID interno univoco in un'area di lavoro OMS  |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemVersion_s | Stringa di versione del sistema operativo lungo |
| TimeGenerated | Data e ora che il record è stato creato. |
| TotalCPUs_d | Numero di CPU Core |
| TotalPhysicalMemory_d | Capacità di memoria in MB |
| VirtualMachine_b | true o false a seconda che del sistema operativo sia guest macchine Virtuali |
| VirtualMachineID_g | ID macchina virtuale Hyper-V |
| VirtualMachineName_g | Nome macchina virtuale Hyper-V |
| VirtualMachineType_s | HyperV, Vmware, Xen, Kvm, Ldom, Lpar, Virtual PC utilizzeranno la |


### <a name="admprocesscl-type-records"></a>AdmProcess_CL tipo di record 
Record con un tipo di **AdmProcess_CL** hanno dati di inventario per i processi connessi TCP sul server con agenti ADM.  Questi record hanno le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Riga di comando completa del processo |
| CompanyName_s | Nome della società (da Windows PE o Linux RPM) |
| Description_s | Descrizione processo lungo (da Windows PE o Linux RPM) |
| FileVersion_s | Versione del file eseguibile (da Windows PE, solo Windows) |
| FirstPid_d | ID processo del sistema operativo |
| InternalName_s | Nome interno del file eseguibile (da Windows PE, solo Windows) |
| MachineId_s | GUID interno univoca in un'area di lavoro OMS  |
| Name_s | Il nome del file eseguibile di processo |
| Path_s | Percorso del file system dell'eseguibile di processo |
| PersistentKey_s | GUID interno univoco all'interno di questo computer |
| PoolId_d | ID interno per l'aggregazione di processi in base alle righe di comando simile. |
| ProcessId_s | GUID interno univoca in un'area di lavoro OMS  |
| ProductName_s | Stringa di nome prodotto (da Windows PE o Linux RPM) |
| ProductVersion_s | Stringa di versione del prodotto (da Windows PE o Linux RPM) |
| StartTime_t | Ora di inizio processo su orologio del computer locale |
| TimeGenerated | Data e ora che il record è stato creato. |
| UserDomain_s | Dominio di proprietario del processo (solo Windows) |
| UserName_s | Nome del proprietario di processo (solo Windows) |
| WorkingDirectory_s | Cartella di lavoro di processo |


## <a name="sample-log-searches"></a>Ricerche dei registri di esempio

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Elencare la capacità di memoria fisica di tutti i computer gestiti. 
Tipo = AdmComputer_CL | Selezionare TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>Nome del computer elenco, versione del sistema operativo DNS e indirizzi IP.
Tipo = AdmComputer_CL | Selezionare ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando
Tipo = AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Dopo la visualizzazione di dati dell'evento per un determinato processo, utilizzare l'ID di computer per recuperare il nome del computer
Tipo = "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" AdmComputer_CL | ComputerName_s distinti

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Elenco tutti i computer che esegue SQL
Tipo = AdmComputer_CL MachineId_s IN {tipo = AdmProcess_CL \*sql\* | DISTINCT MachineId_s} | ComputerName_s distinti

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Elenco di tutte le versioni di prodotto univoco di curvatura nel Data Center
Tipo = AdmProcess_CL Name_s = curvatura | ProductVersion_s distinti

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di Computer di tutti i computer che esegue CentOS

![Esempio di query ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e l'uso
Microsoft raccoglie automaticamente i dati di utilizzo e le prestazioni tramite l'utilizzo del servizio Monitor dipendenza dell'applicazione. Microsoft utilizza questi dati per fornire e migliorare la qualità, sicurezza e l'integrità del servizio Monitor dipendenza dell'applicazione. Dati includono le informazioni di configurazione del software del sistema operativo e versione e indirizzo IP, nome DNS e nome Workstation per fornire funzionalità di risoluzione dei problemi accurata ed efficiente. Raccolte non nomi, indirizzi o altre informazioni di contatto.

Per ulteriori informazioni sulla raccolta di dati e l'utilizzo, vedere l' [Informativa sulla Privacy di servizi Online Microsoft](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni [sull'accesso ricerche](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
