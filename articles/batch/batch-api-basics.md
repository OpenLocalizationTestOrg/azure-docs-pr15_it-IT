<properties
    pageTitle="Panoramica delle funzionalità Batch Azure per gli sviluppatori | Microsoft Azure"
    description="Informazioni su funzionalità del servizio Batch e le relative API dal punto di vista sviluppo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Panoramica delle funzionalità batch per sviluppatori

In questa panoramica dei componenti principali del servizio di Azure Batch, verranno illustrate le caratteristiche di servizio principale e risorse Batch è possibile utilizzare per generare parallelo su larga scala calcolano le soluzioni.

Se si sviluppa soluzioni per un'applicazione di calcolo distribuita o servizio che problemi di accesso diretto [API REST] [ batch_rest_api] chiamate o si usa uno dei [Batch SDK](batch-technical-overview.md#batch-development-apis), occorre usare molte delle risorse e le funzionalità descritte in questo articolo.

> [AZURE.TIP] Per una livello superiore Introduzione al servizio di Batch, vedere [Nozioni di base di Azure Batch](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Flusso di lavoro servizio batch

Il flusso di lavoro di alto livello seguente è tipico di quasi tutte le applicazioni e servizi che utilizzano il servizio di Batch per l'elaborazione in parallelo carichi di lavoro:

1. Per caricare i **file di dati** che si desidera elaborare a un' [Archiviazione Azure] [ azure_storage] account. Batch include il supporto per l'accesso all'archiviazione Blob Azure e le attività possono scaricare questi file [nodi di calcolo](#compute-node) quando vengono eseguite le attività.

2. Caricare i **file dell'applicazione** che verranno eseguite le attività. Questi file possono essere file binari o script e le relative dipendenze e vengono eseguiti per le attività di processi. Le attività possono scaricare questi file dal proprio account di archiviazione oppure è possibile utilizzare la caratteristica di [pacchetti di applicazioni](#application-packages) del Batch per la distribuzione e gestione applicazioni.

3. Creare un [pool](#pool) dei nodi di calcolo. Quando si crea un pool, specificare il numero dei nodi di calcolo per il pool, le dimensioni e il sistema operativo. Quando viene eseguita ogni attività nel processo, ha assegnato da eseguire in uno dei nodi nel pool.

4. Creare un [processo](#job). Un processo gestisce un insieme di attività. Associare ogni processo per uno specifico gruppo in cui si eseguiranno le attività del processo.

5. Aggiungere [attività](#task) al processo. Ogni attività viene eseguita l'applicazione o script caricati per elaborare i file di dati che vengono scaricati dal proprio account di archiviazione. Durante il completamento di ogni attività, è possibile caricare l'output allo spazio di archiviazione Azure.

6. Monitorare l'avanzamento di processo e recuperare l'output attività dallo spazio di archiviazione Azure.

Nelle sezioni seguenti vengono descritte queste e altre risorse Batch di cui è abilitato lo scenario di calcolo distribuito.

> [AZURE.NOTE] È necessario un [account di Batch](batch-account-create-portal.md) per utilizzare il servizio di Batch. Inoltre, quasi tutte le soluzioni di usare un' [Archiviazione Azure] [ azure_storage] account per lo spazio di archiviazione di file e il recupero. Batch attualmente supporta solo **generiche** dello spazio di archiviazione tipo di account, come descritto nel passaggio 5 della [creazione di un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [account di archiviazione su Azure](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Risorse del servizio batch

Alcune delle risorse seguenti: account, calcolare nodi, pool, processi e attività, ossia sono necessari per tutte le soluzioni che utilizzano il servizio di Batch. Gli altri utenti, come pianificazioni e di pacchetti di applicazioni, sono caratteristiche utili, ma facoltativi.

- [Account](#account)
- [Nodo calcolo](#compute-node)
- [Pool](#pool)
- [Processo](#job)

  - [Pianificazioni](#scheduled-jobs)

- [Attività](#task)

  - [Avviare attività](#start-task)
  - [Attività di gestione di processo](#job-manager-task)
  - [Attività di preparazione e rilascio di processo](#job-preparation-and-release-tasks)
  - [Attività di più istanze (MPI)](#multi-instance-tasks)
  - [Relazioni tra attività](#task-dependencies)

- [Pacchetti di applicazioni](#application-packages)

## <a name="account"></a>Account

Un account di Batch è un'entità identificata in modo univoco all'interno del servizio di Batch. Tutta l'elaborazione è associata a un account di Batch. Quando si eseguono operazioni con il servizio di Batch, è necessario sia il nome dell'account e uno dei relativi tasti account. È possibile [creare un account Azure Batch tramite il portale di Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Nodo calcolo

Un nodo di calcolo è una Azure macchine () che si impegna costantemente per l'elaborazione di una parte del carico di lavoro dell'applicazione. Le dimensioni di un nodo determinano il numero di core CPU, capacità di memoria e le dimensioni di sistema file locale allocata per il nodo. È possibile creare pool dei nodi di Windows o Linux mediante servizi Cloud Windows Azure o Marketplace macchine virtuali di immagini. Vedere la sezione [Pool](#pool) seguente per ulteriori informazioni su queste opzioni.

I nodi possono eseguire qualsiasi eseguibile o script supportata dall'ambiente del sistema operativo del nodo. Sono inclusi \*.exe \*cmd, \*bat e gli script di PowerShell per Windows, ossia e binari, pilastri e Python script per Linux.

Tutti calcolare nodi in Batch inoltre includano:

- Un standard [struttura delle cartelle](#files-and-directories) e associato [variabili](#environment-settings-for-tasks) disponibili per il riferimento da attività.
- Impostazioni del **firewall** che sono configurati per controllare l'accesso.
- [Accesso remoto](#connecting-to-compute-nodes) per Windows (RDP Remote Desktop Protocol ()) e i nodi Linux (Secure Shell (SSH)).

## <a name="pool"></a>Pool

Un pool è un insieme di nodi eseguita nell'applicazione. Pool di può essere creato manualmente dall'utente o automaticamente dal servizio Batch quando si specifica il lavoro da eseguire. È possibile creare e gestire un pool che soddisfi i requisiti di risorse dell'applicazione. Un pool di può essere utilizzato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

Azure pool Batch creare nella parte superiore della piattaforma di elaborazione Azure core. Forniscono allocazione di grandi dimensioni, Installazione applicazioni, la distribuzione dei dati, il monitoraggio dell'integrità e flessibile regolazione del numero dei nodi di calcolo di un pool ([proporzioni dei caratteri](#scaling-compute-resources)).

Tutti i nodi che viene aggiunto a un pool viene assegnato un nome univoco e l'indirizzo IP. Quando un nodo viene rimosso da un pool, eventuali modifiche apportate al sistema operativo o ai file vengono perse e il relativo nome e indirizzo IP vengono rilasciati per utilizzi futuri. Quando un nodo lascia un pool, la durata supera.

Quando si crea un pool, è possibile specificare gli attributi seguenti:

- Calcolare il nodo **del sistema operativo** e **versione**

    Sono disponibili due opzioni quando si seleziona un sistema operativo per i nodi del pool: **macchina virtuale** e **Configurazione di servizi Cloud**.

    **Configurazione delle macchine virtuali** fornisce immagini Linux e Windows per calcolano i nodi di [Macchine virtuali di Azure Marketplace][vm_marketplace].
    Quando si crea un pool che contiene i nodi di configurazione della macchina virtuale, è necessario specificare non solo le dimensioni dei nodi, ma anche il **riferimento all'immagine di macchina virtuale** e installazione nei nodi di **agente nodo SKU** di Batch. Per ulteriori informazioni su come specificare le proprietà del pool, vedere [disposizione Linux nodi in Batch Azure pool](batch-linux-nodes.md).

    **Configurazione di servizi cloud** consente di che calcolare nodi *solo*di Windows. Sistemi operativi disponibili per i pool di configurazione di servizi Cloud sono elencati [i sistemi operativi Guest Azure versioni e nella matrice compatibilità SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando si crea un pool che contiene i nodi servizi Cloud, è necessario specificare solo la dimensione del nodo e la *Famiglia di sistemi*. Quando si crea pool di Windows nodi di calcolo, utilizzare più comunemente servizi Cloud.

    - La *Famiglia di sistemi* determina anche le versioni di .NET vengono installate con il sistema operativo.
    - Come con ruoli di lavoro all'interno di servizi Cloud, è possibile specificare una *Versione del sistema operativo* (per ulteriori informazioni sui ruoli di lavoro, vedere la sezione [Avvisa sui servizi cloud](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) nella [Panoramica di servizi Cloud](../cloud-services/cloud-services-choose-me.md)).
    - Come con ruoli di lavoro, è consigliabile specificare `*` per la *Versione del sistema operativo* in modo che i nodi vengono aggiornati automaticamente e non è disponibile una soluzione necessaria tenere in considerazione appena versioni. Il caso di utilizzo principale per la selezione di una versione del sistema operativo specifica è garantire la compatibilità delle applicazioni, che consente di test da eseguire prima di consentire la versione per l'aggiornamento di compatibilità con le versioni precedenti. Dopo la convalida, è possibile aggiornare la *Versione del sistema operativo* per il pool e la nuova immagine del sistema operativo può essere installato, qualsiasi attività in esecuzione vengono interrotti e riaccodata.

- **Dimensioni dei nodi**

    **Configurazione di servizi cloud** calcolo nodo dimensioni è elencata nelle [dimensioni per i servizi Cloud](../cloud-services/cloud-services-sizes-specs.md). Batch supporta tutte le dimensioni di servizi Cloud ad eccezione di `ExtraSmall`.

    **Configurazione delle macchine virtuali** calcolare nodo dimensioni sono elencate [le dimensioni per macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [dimensioni per macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Batch supporta tutte le dimensioni di macchine Virtuali di Azure ad eccezione di `STANDARD_A0` e quelle ottenute con lo spazio di archiviazione premium (`STANDARD_GS`, `STANDARD_DS`, e `STANDARD_DSV2` serie).

    Quando si seleziona una dimensione di nodo di calcolo, considerare le caratteristiche e i requisiti delle applicazioni che si eseguirà nei nodi. Aspetti come se l'applicazione è multithread e la quantità di memoria utilizza consente di determinano le dimensioni di nodo più adatto e conveniente. Si tratta di una tipica per selezionare una dimensione nodo presupponendo che verrà eseguita un'attività su un nodo alla volta. Tuttavia, è possibile disporre di più attività (e pertanto più istanze dell'applicazione) [in parallelo](batch-parallel-node-tasks.md) su nodi di calcolo durante l'esecuzione del processo. In questo caso è comune per scegliere una dimensione maggiore per il nodo per soddisfare la domanda maggiore dell'esecuzione delle attività in parallelo. Per ulteriori informazioni, vedere [programmazione delle attività dei criteri](#task-scheduling-policy) .

    Tutti i nodi di un pool sono le stesse dimensioni. Se si intende eseguire applicazioni con diversi requisiti di sistema e/o caricare livelli, è consigliabile utilizzare pool diversi.

- **Numero di destinazione dei nodi**

    Questo è il numero dei nodi di calcolo che si desiderano distribuire nel pool. In questo caso a come *destinazione* poiché, in alcuni casi, il pool potrebbero non raggiungere il numero di nodi desiderato. Un pool di potrebbe non raggiungere il numero desiderato di nodi se si raggiunge la [quota di base](batch-quota-limit.md#batch-account-quotas) per l'account Batch - o se è presente una formula il ridimensionamento automatico è stato applicato al pool di che limita il numero massimo di nodi (vedere la sezione seguente "Proporzioni dei caratteri criteri").

- **Criteri di scala**

    Oltre che specifica un numero statico dei nodi, è invece possibile scrivere e applicare una [formula il ridimensionamento automatico](#scaling-compute-resources) a un pool. Il servizio Batch periodicamente restituisce la formula e modifica il numero dei nodi del pool in base a vari pool di processo e i parametri di attività che è possibile specificare.

- **Pianificazione dei criteri**

    L'opzione di configurazione [max attività per nodo](batch-parallel-node-tasks.md) determina il numero massimo di attività che possono essere eseguiti in parallelo su ogni nodo di calcolo del pool.

    La configurazione predefinita è un'attività alla volta eseguito in un nodo, ma non vi sono scenari in cui è opportuno avere eseguito su un nodo contemporaneamente più attività. Vedere lo [scenario di esempio](batch-parallel-node-tasks.md#example-scenario) in questo articolo [attività simultanee nodo](batch-parallel-node-tasks.md) per vedere come è possibile trarre vantaggio da più attività per nodo.

    È inoltre possibile specificare un *tipo di riempimento* che determina il Batch distribuisce uniformemente le attività in tutti i nodi di un pool o Pack ogni nodo con il numero massimo di attività prima di assegnare attività a un altro nodo.

- **Stato comunicazione** dei nodi di calcolo

    Nella maggior parte dei casi attività funzionano in modo indipendente e non è necessario comunicare tra loro. Esistono tuttavia alcune applicazioni in cui devono comunicare le attività, ad esempio [scenari MPI](batch-mpi.md).

    È possibile configurare un pool per consentire la comunicazione tra i nodi all'interno di it, ossia**le comunicazioni**. Quando sono attivata le comunicazioni, nodi nel pool di configurazione di servizi Cloud possono comunicare con loro su porte maggiore 1100 e pool di configurazione della macchina virtuale non limitare il traffico su qualsiasi porta.

    Si noti che anche le comunicazioni per consentire influisce sulla posizione dei nodi del cluster e potrebbe limitare il numero massimo di nodi di un pool a causa di limitazioni di distribuzione. Se l'applicazione non richiede la comunicazione tra nodi, il servizio Batch può allocare una grande quantità di nodi al pool in molti cluster diversi e Data Center per consentire maggiore potenza di elaborazione in parallelo.

- **Avviare l'operazione** per i nodi di calcolo

    Facoltativo *avviare attività* di venga eseguito su ciascun nodo come tale nodo viene aggiunto il pool e ogni volta che un nodo viene riavviato o ricostruire l'immagine. L'inizio dell'operazione è particolarmente utile per la preparazione nodi di calcolo per l'esecuzione di attività, come ad esempio installare le applicazioni che le attività eseguite sui nodi di calcolo.

- **Pacchetti di applicazioni**

    È possibile specificare i [pacchetti di applicazioni](#application-packages) per la distribuzione dei nodi di elaborazione del pool. Pacchetti di applicazioni offrono distribuzione semplificata e controllo delle versioni delle applicazioni che eseguono le attività. Pacchetti di applicazioni che si specifica un pool vengono installati in ogni nodo che eseguono l'accesso del pool e ogni volta che un nodo viene riavviato o ricostruire l'immagine. Pacchetti di applicazioni non sono attualmente supportati su nodi di calcolo Linux.

- **Configurazione della rete**

    È possibile specificare l'ID di un Azure [virtuali (VNet)](../virtual-network/virtual-networks-overview.md) in cui il pool nodi di calcolo deve essere creata. Requisiti per specificare un VNet per il pool di sono disponibili nella finestra [Aggiungi un pool di un account di] [ vnet] nel riferimento all'API REST Batch.

> [AZURE.IMPORTANT] Tutti gli account di Batch sono predefinito **quota** che limita il numero di **Core** (e pertanto nodi di calcolo) in un account di Batch. In [quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md), è possibile trovare le quote per impostazione predefinita e istruzioni su come per [aumentare la quota](batch-quota-limit.md#increase-a-quota) (ad esempio il numero massimo di core nell'account Batch). Se è necessario richiedere "Perché non è possibile il pool di raggiungere più valori X nodi?" la quota di core potrebbe essere la causa.

## <a name="job"></a>Processo

Un processo è un insieme di attività. Gestisce la modalità di esecuzione di calcolo da attività sui nodi di calcolo in un pool.

- Il processo specifica il **pool** in cui è possibile eseguire il lavoro. È possibile creare un nuovo pool per ogni processo o utilizzare un pool per molti processi. È possibile creare un pool per ogni processo associato a una pianificazione di processo o per tutti i processi associati alla programmazione di un processo.

- È possibile specificare un facoltativo **priorità del processo**. Quando un processo viene inviato con priorità più alta processi in corso, le attività per il processo di priorità più alta vengono inserite nella coda prima attività per i processi di priorità più bassa. Attività in priorità processi già in esecuzione non vengono interrotti.

- È possibile utilizzare il processo **vincoli** per specificare i limiti per i processi di:

    È possibile impostare **il tempo massimo wallclock**, in modo che se un processo viene eseguito per più tempo massimo wallclock specificato, il processo e tutte le attività vengono terminate.

    Batch vengono rilevate e quindi ripetere l'attività non riuscite. È possibile specificare il **numero massimo di tentativi di attività** come vincolo, ad esempio se un'attività è *sempre* o *mai* ripetuta. La ripetizione di un'attività significa che l'attività è riaccodato per eseguire di nuovo.

- L'applicazione client può aggiungere attività a un processo oppure è possibile specificare un' [attività di gestione di processo](#job-manager-task). Un'attività di gestione del processo contiene le informazioni necessarie creare attività necessarie per un processo con manager mansione che viene eseguita su uno dei nodi di calcolo nel pool. L'attività di gestione del processo viene gestito in modo specifico dal Batch - è coda durante il processo verrà creato e si riavvia in caso di errore. Un'attività di gestione del processo è *necessario* per i processi creati da una [pianificazione processo](#scheduled-jobs) perché è l'unico modo per definire le attività prima che il processo di creazione di un'istanza.

- Per impostazione predefinita, i processi rimangono lo stato attivo dopo aver complete tutte le attività all'interno del processo. È possibile modificare questo comportamento in modo che il processo è terminato automaticamente dopo aver complete tutte le attività nel processo. Impostare proprietà **onAllTasksComplete** del processo ([OnAllTasksComplete] [ net_onalltaskscomplete] in Batch .NET) a *terminatejob* terminare automaticamente il processo dopo aver configurato tutte le attività in stato completato.

    Si noti che il servizio Batch considera un processo *senza* attività per tutte le attività completate. Di conseguenza, questa opzione viene utilizzata più comunemente con un' [attività di gestione di processo](#job-manager-task). Se si desidera utilizzare terminazione processo automatico senza un responsabile di processo, è necessario impostare proprietà di un nuovo processo **onAllTasksComplete** su *noaction*inizialmente quindi impostarlo su *terminatejob* solo dopo aver aggiunto attività al processo.

### <a name="job-priority"></a>Priorità del processo

È possibile assegnare una priorità ai processi creati in Batch. Il servizio Batch utilizza il valore di priorità del processo per determinare l'ordine dei processi all'interno di un account (questa non operazione deve essere confuse con un [processo pianificato](#scheduled-jobs)). La priorità intervallo compreso tra -1000 e 1000, con-1000 vengano la priorità più bassa e 1000 come il valore più alto. È possibile aggiornare la priorità di un processo utilizzando [le proprietà di un processo di aggiornamento] [ rest_update_job] operazione (Batch resto) o modificando [CloudJob.Priority] [ net_cloudjob_priority] proprietà (Batch .NET).

Nello stesso account con priorità più alta processi presentano pianificazione la precedenza su processi di priorità più bassa. Un processo con un valore di priorità più alta in un account non dispone di pianificazione la precedenza su un altro processo con un valore di priorità in un altro account.

Pianificazione delle attività da una serie è indipendente. Tra i pool diversi, non è possibile garantire che una priorità più alta è programmata prima di tutto se il proprio pool associato è inferiore a nodi inattivi. Nello stesso pool processi con lo stesso livello di priorità sono un uguale probabilità di pianificate.

### <a name="scheduled-jobs"></a>Programmate

[Processo pianificazioni] [ rest_job_schedules] consentono di creare processi ricorrenti all'interno del servizio di Batch. Programmazione di un processo specifica quando eseguire processi, le specifiche per i processi da eseguire. È possibile specificare la durata della programmazione, ossia il periodo di tempo e quando la programmazione è attiva e con quale frequenza durante il periodo di tempo da creare processi.

## <a name="task"></a>Attività

Un'attività è un'unità di calcolo è associata a un processo. Viene eseguito su un nodo. Le attività assegnate a un nodo per l'esecuzione o vengono accodate fino a un nodo diventa disponibile. In breve, un'attività viene eseguita una o più programmi o script su un nodo di calcolo per svolgere un'attività che è necessario completato.

Quando si crea un'attività, è possibile specificare:

- **Riga di comando** dell'attività. Ecco la riga di comando che viene eseguita l'applicazione o script sul nodo di calcolo.

    È importante tenere presente che la riga di comando non viene effettivamente eseguita in una shell. Di conseguenza, non è possibile accettare a livello nativo i vantaggi offerti dalle funzionalità shell quali espansione [variabile di ambiente](#environment-settings-for-tasks) (inclusi i `PATH`). Per sfruttare le funzionalità, è necessario richiamare shell nella riga di comando, ad esempio avviando `cmd.exe` su nodi di Windows o `/bin/sh` su Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se le attività necessarie per l'esecuzione di un'applicazione o script che non si trova il nodo `PATH` o fanno riferimento a variabili, richiamare shell in modo esplicito nella riga di comando attività.

- **File di risorse** che contengono i dati per l'elaborazione. Questi file vengono copiati automaticamente al livello di nodo dallo spazio di archiviazione Blob in un account di archiviazione Azure **generiche** prima dell'esecuzione di una riga di comando dell'attività. Per ulteriori informazioni, vedere le sezioni [avviare attività](#start-task) e [file e directory](#files-and-directories).

- Le **variabili di ambiente** necessarie per l'applicazione. Per ulteriori informazioni, vedere la sezione [impostazioni dell'ambiente per le attività](#environment-settings-for-tasks) .

- I **vincoli** in cui è necessario eseguire l'attività. Per esempio, il tempo massimo che l'attività è consentita l'esecuzione, il numero massimo di volte in cui che ripetere un'operazione non riuscita, e il numero massimo ora che i file nella cartella di lavoro dell'attività vengono mantenuti.

- **Pacchetti di applicazioni** per distribuire il nodo di calcolo in cui l'attività viene programmata per l'esecuzione. [Pacchetti di applicazioni](#application-packages) offrono distribuzione semplificata e controllo delle versioni delle applicazioni che eseguono le attività. Pacchetti di applicazioni a livello di attività sono particolarmente utili in ambienti pool condiviso, in processi diversi vengono eseguiti su un pool e il pool non viene eliminato al termine dell'esecuzione un processo. Se il processo ha meno attività più nodi nel pool, pacchetti di applicazioni attività possono ridurre a icona il trasferimento di dati poiché l'applicazione viene distribuito solo per i nodi che eseguono attività.

Oltre alle attività che è possibile definire per eseguire calcolo su un nodo anche sono disponibili le seguenti attività speciale per il servizio Batch:

- [Avviare attività](#start-task)
- [Attività di gestione di processo](#job-manager-task)
- [Attività di preparazione e rilascio di processo](#job-preparation-and-release-tasks)
- [Attività di più istanze (MPI)](#multi-instance-tasks)
- [Relazioni tra attività](#task-dependencies)

### <a name="start-task"></a>Avviare attività

Se si associa un **avviare attività** un pool, è possibile preparare l'ambiente operativo dei nodi. Ad esempio, è possibile eseguire azioni come l'installazione delle applicazioni che eseguono le attività e avvio di processi in background. L'inizio dell'operazione viene eseguita ogni volta che si avvia un nodo, per come rimane nel pool, incluso quando il nodo è prima aggiunta a pool di e quando si riavvia o ricostruire l'immagine.

Dei vantaggi principali di attività start è che può contenere le informazioni necessarie configurare un nodo di calcolo e installare le applicazioni che sono necessari per l'esecuzione di attività. Di conseguenza, è sufficiente aumentare il numero dei nodi in un pool di che specifica il nuovo conteggio dei nodi destinazione - Batch già contiene le informazioni necessarie per configurare i nuovi nodi e ottenerli pronti per accettare le attività.

Come con tutte le attività Batch Azure, è possibile specificare un elenco di **file di risorse** in [Archiviazione Azure][azure_storage], oltre a una **riga di comando** da eseguire. Batch vengono innanzitutto copiati i file di risorse al livello di nodo dallo spazio di archiviazione di Azure e quindi viene eseguita la riga di comando. Per un'attività di inizio del pool, l'elenco dei file contiene in genere l'applicazione di attività e le dipendenze.

Tuttavia, oltre che i dati di riferimento che verrà utilizzato da tutte le attività che eseguono sul nodo di calcolo. Ad esempio, è possibile eseguire la riga di comando dell'attività avvia una `robocopy` operazione per copiare la [cartella condivisa](#files-and-directories)file dell'applicazione (che sono stati specificati come file di risorse e download per il nodo) tra [cartella di lavoro dell'inizio dell'operazione](#files-and-directories) e quindi eseguire un file MSI o `setup.exe`.

> [AZURE.IMPORTANT] Batch attualmente supporta *solo* il tipo di account archiviazione **Generale** , come descritto nel passaggio 5 della [creazione di un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [account di archiviazione su Azure](../storage/storage-create-storage-account.md). Le attività Batch (inclusi attività standard, inizio attività, mansioni di preparazione e rilascio mansioni) specificare i file di risorse che si trovano *solo* in **Generale** gli account di archiviazione.

Si tratta in genere utile per il servizio di Batch necessario attendere che l'attività di inizio completare prima di considerare il nodo pronto per l'assegnazione attività, ma è possibile configurare questa.

Se un'attività iniziale ha esito negativo su un nodo di calcolo, quindi lo stato del nodo viene aggiornato per riflettere l'errore e il nodo non è assegnato alcuna attività. Un'operazione avvia potrebbe non riuscire se si verifica un problema copiare i file di risorse dallo spazio di archiviazione o se il processo eseguito da una riga di comando restituisce un codice di uscita diverso da zero.

Se si aggiungono o si aggiorna l'attività di inizio per un pool *esistente* , è necessario riavviare i nodi di calcolo per l'attività di inizio da applicare ai nodi.

### <a name="job-manager-task"></a>Attività di gestione di processo

In genere utilizzare un **manager mansione** per controllare e/o monitorare l'esecuzione del processo, ad esempio, per creare e inviare le attività per un processo, determinare altre attività da eseguire per determinare quando lavoro è stato completato. Tuttavia, un'attività di gestione del processo non è limitata a queste attività. È un'attività completamente competente che può eseguire le azioni che sono necessari per il processo. Ad esempio, un'attività di gestione del processo potrebbe scaricare un file specificato come parametro, analizzare il contenuto del file e inviare attività aggiuntive in base al contenuto.

Un'attività di gestione del processo viene avviata prima di tutte le altre attività. Fornisce le caratteristiche seguenti:

- Viene automaticamente inviata come attività dal servizio Batch quando viene creato il processo.

- È programmata per eseguire prima di altre attività di un processo.

- Il nodo associato è l'ultimo da rimuovere da un pool quando il pool è da downsized.

- Entro possa essere collegato l'interruzione di tutte le attività nel processo.

- Un'attività di gestione del processo viene assegnata la massima priorità quando deve essere riavviato. Se un nodo inattivo non è disponibile, il servizio Batch può terminare una delle altre attività in esecuzione nel pool per creare spazio per l'esecuzione dell'attività di gestione di processo.

- Un'attività di gestione del processo in un processo non ha priorità sulle attività di altri processi. Tra i processi, si osservano solo processo a livello di priorità.

### <a name="job-preparation-and-release-tasks"></a>Attività di preparazione e rilascio di processo

Batch fornisce mansioni preparazione per la configurazione di esecuzione del processo precedente. Versione mansioni sono per post-processo manutenzione o pulizia.

- **L'attività di preparazione processo**: un'attività di preparazione processo viene eseguito su tutti i nodi di calcolo programmata per eseguire le attività, prima dell'esecuzione di una delle altre attività di processo. È possibile utilizzare un'attività di preparazione del processo per copiare i dati che sono condiviso da tutte le attività, ma sono univoco per il processo, ad esempio.
- **Mansione di rilascio**: al termine di un processo, una versione mansione viene eseguito su ogni nodo nel pool di esecuzione almeno un'attività. Per eliminare i dati copiati da parte dell'attività di preparazione processo o per comprimere e caricare i dati log diagnostici, ad esempio, è possibile utilizzare una versione mansione.

Entrambe processo di preparazione e le attività di rilascio consentono di specificare una riga di comando per eseguire quando si richiama l'attività. Offrono funzioni come download dei file, esecuzione con privilegi elevati, variabili di ambiente personalizzate, durata massima esecuzione, numero di tentativi e ora di conservazione dei file.

Per ulteriori informazioni sulle attività di preparazione e rilascio di processo, vedere [eseguire attività di preparazione e completamento mansioni nel Batch di Azure nodi di calcolo](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Attività di più istanze

Un' [attività di più istanze](batch-mpi.md) è configurato per eseguire contemporaneamente su più di un nodo di calcolo. Le attività di più istanze, è possibile abilitare gli scenari di elaborazione prestazioni elevate che richiedono un gruppo di nodi di calcolo assegnati per elaborare un carico di lavoro singola (ad esempio messaggio passando Interface (MPI)).

Per informazioni dettagliate sui processi MPI di esecuzione in Batch utilizzando la libreria .NET Batch, vedere [attività di più istanze di usare per eseguire le applicazioni di messaggio passando Interface (MPI) nel Batch di Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Relazioni tra attività

[Relazioni tra attività](batch-task-dependencies.md), come indicato il nome, consentono di specificare che un'attività dipende il completamento di altre attività prima dell'esecuzione. Questa funzionalità fornisce supporto per situazioni in cui un'attività "downstream" utilizza l'output di un'attività "padre" - o quando un'attività padre esegue un'inizializzazione richiesta da un'attività downstream. Per utilizzare questa caratteristica, è necessario abilitare le relazioni tra attività la procedura batch. Quindi, per ogni attività che dipende da un altro o molti altri, specificare le attività che dipende da tale attività.

Con le relazioni tra attività, è possibile configurare scenari simile al seguente:

* *taskB* dipende *taskA* (*taskB* non verrà avviata esecuzione fino al completamento *taskA* ).
* *taskC* dipende *taskA* e *taskB*.
* *taskD* dipende da un intervallo di attività, ad esempio attività *1* e *10*, prima di eseguire.

Controllare le [relazioni tra attività nel Batch di Azure](batch-task-dependencies.md) e [TaskDependencies] [ github_sample_taskdeps] esempio negli [esempi di batch azure] [ github_samples] archivio GitHub per ulteriori informazioni dettagliate su questa caratteristica.

## <a name="environment-settings-for-tasks"></a>Impostazioni dell'ambiente per le attività

Ogni attività eseguita dal servizio Batch ha accesso a variabili di ambiente che verrà impostato su nodi di calcolo. Sono inclusi le variabili di ambiente definite dal servizio Batch ([servizio definito][msdn_env_vars]) e le variabili di ambiente personalizzato che è possibile definire per le proprie attività. Le applicazioni e gli script che eseguire le attività hanno accesso a tali variabili durante l'esecuzione.

È possibile impostare variabili di ambiente personalizzate a livello di attività o un processo tramite la proprietà di *impostazioni dell'ambiente* per queste entità. Ad esempio, vedere [aggiungere un'attività a un processo] [ rest_add_task] operazione (API REST Batch) o [CloudTask.EnvironmentSettings] [ net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings] [ net_job_env] proprietà in Batch .NET.

L'applicazione client o servizio ottenere le variabili di ambiente dell'attività, sia servizio definito e personalizzate, utilizzando le [informazioni relative a un'attività] [ rest_get_task_info] operazione (Batch resto) o accedendo [CloudTask.EnvironmentSettings] [ net_cloudtask_env] proprietà (Batch .NET). Processi in esecuzione su un nodo di calcolo possono accedere a queste e altre variabili di ambiente sul nodo, ad esempio, usando i consueti `%VARIABLE_NAME%` (Windows) o `$VARIABLE_NAME` sintassi (Linux).

È disponibile un elenco completo di tutte le variabili di ambiente servizio definito [calcolare le variabili di ambiente nodo][msdn_env_vars].

## <a name="files-and-directories"></a>File e directory

Ogni attività è una *cartella di lavoro* in cui viene creato zero o più file e directory. Questa cartella di lavoro può essere utilizzato per archiviare il programma che eseguita l'attività, i dati che vengono elaborati e l'output di elaborazione che esegue. Tutti i file e directory di un'attività sono proprietà dell'utente di attività.

Il servizio Batch presenta una parte del file system in un nodo come la *directory radice*. Attività possono accedere alla directory radice facendo riferimento il `AZ_BATCH_NODE_ROOT_DIR` variabile di ambiente. Per ulteriori informazioni sull'utilizzo delle variabili di ambiente, vedere [impostazioni dell'ambiente per le attività](#environment-settings-for-tasks).

La directory radice contiene la struttura di directory seguente:

![Calcolare la struttura di directory nodo][1]

- **condiviso**: questa directory consente l'accesso in lettura/scrittura in *tutte* le attività in esecuzione su un nodo. Tutte le attività che viene eseguita sul consente di creare, leggere, aggiornare ed eliminare file nella directory. Attività di accesso alla directory facendo riferimento il `AZ_BATCH_NODE_SHARED_DIR` variabile di ambiente.

- **avvio**: questa directory è utilizzata da un'attività di inizio come cartella di lavoro. Tutti i file scaricati da parte dell'attività avvia il nodo sono memorizzati qui. L'inizio dell'operazione possibile creare, leggere, aggiornare ed eliminare i file nella directory. Attività di accesso alla directory facendo riferimento il `AZ_BATCH_NODE_STARTUP_DIR` variabile di ambiente.

- **Attività**: viene creata una directory di ogni attività viene eseguito sul nodo. È possibile accedervi facendo riferimento il `AZ_BATCH_TASK_DIR` variabile di ambiente.

    All'interno di ciascuna directory di attività, il servizio di Batch crea una cartella di lavoro (`wd`) viene specificato il cui percorso univoco per il `AZ_BATCH_TASK_WORKING_DIR` variabile di ambiente. Questa directory fornisce l'accesso in lettura/scrittura all'attività. L'attività consente di creare, leggere, aggiornare ed eliminare i file nella directory. Questa directory viene mantenuta in base a vincolo *RetentionTime* specificato per l'attività.

    `stdout.txt`e `stderr.txt`: questi file vengono archiviati nella cartella delle attività durante l'esecuzione dell'attività.

>[AZURE.IMPORTANT] Quando un nodo viene rimosso dal pool, vengono rimossi *tutti* i file memorizzati sul nodo.

## <a name="application-packages"></a>Pacchetti di applicazioni

La caratteristica di [pacchetti di applicazioni](batch-application-packages.md) fornisce facile gestione e distribuzione di applicazioni dei nodi di elaborazione nei pool. È possibile caricare e gestire più versioni delle applicazioni, eseguire le attività, inclusi i propri file binari e file di supporto. Quindi è possibile distribuire automaticamente una o più di queste applicazioni dei nodi di elaborazione del pool.

È possibile specificare i pacchetti di applicazioni a livello di pool e attività. Quando si specificano i pacchetti di applicazioni pool, l'applicazione viene distribuito in tutti i nodi del pool. Quando si specificano pacchetti di applicazioni di attività, l'applicazione viene distribuito solo ai nodi pianificate per l'esecuzione almeno una delle attività del processo, solo prima riga di comando dell'attività viene eseguita.

Batch gestisce i dettagli dell'utilizzo di archiviazione di Azure per archiviare i pacchetti di applicazioni e distribuire in modo da nodi di calcolo, in modo che è possibile semplificare il sovraccarico di codice e gestione.

Per maggiori informazioni sulle funzionalità di pacchetto dell'applicazione, consultare [la distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md).

>[AZURE.NOTE] Se si aggiungono pacchetti di applicazioni pool a un pool *esistente* , è necessario riavviare i nodi di calcolo per i pacchetti di applicazione essere distribuito ai nodi.

## <a name="pool-and-compute-node-lifetime"></a>Durata nodo pool e calcolo

Quando si progetta soluzione Batch Azure, è necessario prendere una decisione progettazione su come e quando pool vengono creati e per quanto tempo calcolano nodi contenuti in tali pool vengono mantenuti disponibili.

A un'estremità dello spettro, è possibile creare un pool per ogni processo che invia ed eliminare il pool di esecuzione di fine delle attività. In questo modo si ottimizza utilizzo poiché i nodi sono allocati solo quando necessario e chiuso come inattivo. Sebbene ciò significa che il processo necessario attendere i nodi da allocare, è importante tenere presente che le attività vengono programmate per l'esecuzione come nodi singolarmente, allocata, sono disponibili l'inizio dell'operazione completata. Batch significa *non* attendere fino a quando tutti i nodi di un pool sono disponibili prima di assegnare attività ai nodi. In questo modo sfruttare al massimo tutti i nodi disponibili.

L'altra estremità dello spettro, se i processi di avviare immediatamente la massima priorità, è possibile creare un pool di anticipo e rendere disponibili i nodi prima di inviare processi. In questo scenario, le attività possono iniziare immediatamente, ma nodi potrebbero rimanere inattive durante l'attesa che vengano assegnati.

Un approccio combinato viene utilizzato in genere per gestire un carico di variabili, ma in corso. È possibile creare un pool di più processi vengono inviati al, ma è possano ridimensionare il numero dei nodi verso l'alto o verso il basso in base alle responsabilità Carica (vedere [proporzioni calcolare risorse](#scaling-compute-resources) nella sezione seguente). È possibile eseguire questa operazione in, basato su carico corrente oppure tempestiva, se carico può essere previsto.

## <a name="scaling-compute-resources"></a>Ridimensionamento delle risorse di elaborazione

Con [il ridimensionamento automatico](batch-automatic-scaling.md), è possibile impostare il servizio Batch regolare automaticamente il numero dei nodi di calcolo in un pool secondo il carico di lavoro e delle risorse l'utilizzo corrente dello scenario di calcolo. In questo modo è possibile ridurre il costo totale di eseguire l'applicazione usando solo le risorse che necessarie e il rilascio di quelli che non sia necessario.

Attivare il ridimensionamento automatico per scrivere una [formula di ridimensionamento automatico](batch-automatic-scaling.md#automatic-scaling-formulas) , tale formula viene associato a un pool. Il servizio Batch utilizza la formula per determinare il numero dei nodi nel pool di destinazione per l'intervallo di ridimensionamento successivo (un intervallo che è possibile configurare). È possibile specificare le impostazioni di ridimensionamento automatico per un pool quando crearlo o abilitare il ridimensionamento in un pool in un secondo momento. È inoltre possibile aggiornare le impostazioni di ridimensionamento in un pool di abilitati per il ridimensionamento.

Ad esempio, ad esempio un processo richiede l'invio di un numero elevato di attività da eseguire. È possibile assegnare una formula di ridimensionamento per il pool che consente di modificare il numero dei nodi nel pool in base a numero corrente di attività in coda e la frequenza di completamento delle attività nel processo. Il servizio Batch periodicamente restituisce la formula e ridimensiona pool, in base a carico di lavoro (aggiungere nodi per molte attività in coda e rimuovere nodi per nessuna attività in coda o è in esecuzione) e le altre impostazioni della formule.

Una formula proporzioni può essere basata su quelle riportate di seguito:

- **Metrica ora** si basano le statistiche raccolte cinque minuti il numero di ore specificato.

- L'utilizzo della CPU, l'utilizzo della larghezza di banda, l'utilizzo di memoria e numero dei nodi si basano **metriche di risorse** .

- **Metrica attività** si basano sullo stato delle attività, ad esempio *attivo* (in coda), *in esecuzione*o *completate*.

Durante il ridimensionamento automatico diminuisce il numero dei nodi di calcolo in un pool, è necessario considerare in che modo gestire le attività che eseguono al momento dell'operazione di riduzione. A tale scopo, Batch include un' *opzione di rimozione nodo* che è possibile includere nelle formule. Ad esempio, è possibile specificare che le attività in esecuzione sono interrotto immediatamente, interrotto immediatamente e quindi riaccodate per l'esecuzione in un altro nodo o consentite completare prima che il nodo viene rimosso dal pool di.

Per ulteriori informazioni sul ridimensionamento automaticamente un'applicazione, vedere [nodi in un pool di Azure Batch di calcolo scala automaticamente](batch-automatic-scaling.md).

> [AZURE.TIP] Per ottimizzare l'utilizzo delle risorse di calcolo, impostare il numero di destinazione dei nodi da zero alla fine di un processo, ma consentire l'esecuzione di attività per terminare.

## <a name="security-with-certificates"></a>Protezione mediante i certificati

In genere è necessario utilizzare i certificati quando crittografare o decrittografare informazioni riservate per le attività, ad esempio la chiave per un [account di archiviazione di Azure][azure_storage]. A tale scopo, è possibile installare certificati nei nodi. Dati riservati crittografati vengono passati alle attività tramite i parametri della riga di comando o incorporati in una delle risorse di attività e i certificati installati possono essere utilizzati per decrittografare i file.

Usare [Aggiungi certificato] [ rest_add_cert] operazione (Batch resto) o [CertificateOperations.CreateCertificate] [ net_create_cert] metodo (Batch .NET) per aggiungere un certificato per un account di Batch. Associare il certificato per un pool di nuovo o esistente. Quando un certificato è associato a un pool, il servizio Batch installa il certificato su ciascun nodo del pool. Il servizio di Batch installa i certificati appropriati all'avvio il nodo, prima di avviare le attività (tra cui l'attività di inizio e gestione mansione).

Se si aggiunge certificati a un pool *esistente* , è necessario riavviare i nodi di calcolo per i certificati da applicare ai nodi.

## <a name="error-handling"></a>Gestione degli errori

Potrebbe essere necessario per gestire gli errori di attività e applicazione all'interno della soluzione Batch.

### <a name="task-failure-handling"></a>Gestione degli errori di attività
Errori di attività possono essere suddivise in queste categorie:

- **Errori di programmazione**

    Se il trasferimento di file che siano specificati per un'attività non riesce per qualsiasi motivo, "errore programmazione" è impostato per l'attività.

    Pianificazione errori può verificarsi se il file di risorse dell'attività sono state spostate, l'account di archiviazione non è più disponibile o un altro problema è stato rilevato che impedisce la copia corretta dei file per il nodo.

- **Errori dell'applicazione**

    Il processo specificato dalla riga di comando di un'attività può inoltre esito negativo. Il processo è considerato errato quando viene restituito un codice di uscita diverso da zero dal processo che viene eseguito da parte dell'attività (vedere *attività di codici di uscita* nella sezione seguente).

    Per gli errori dell'applicazione, è possibile configurare Batch per automaticamente ritentare l'operazione fino a un numero di volte specificato.

- **Errori di vincolo**

    È possibile impostare un vincolo che specifica la durata massima esecuzione per un processo o attività, *maxWallClockTime*. Può essere utile per terminare l'attività "bloccato".

    Quando è stato superato il tempo massimo, l'attività viene contrassegnata come *completato*, ma il codice di uscita è impostato su `0xC000013A` e il campo *schedulingError* è contrassegnato come `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Il debug degli errori dell'applicazione

- `stderr`e`stdout`

    Durante l'esecuzione, un'applicazione produca output di diagnostica che è possibile utilizzare per la risoluzione dei problemi. Come indicato nella sezione precedente, [file e directory](#files-and-directories), il servizio di Batch scrive output standard e output di errore standard per `stdout.txt` e `stderr.txt` file nella cartella delle attività sul nodo di calcolo. È possibile utilizzare il portale di Azure o una delle SDK Batch per scaricare questi file. Ad esempio, è possibile recuperare i file per la risoluzione dei problemi tramite [ComputeNode.GetNodeFile] [ net_getfile_node] e [CloudTask.GetNodeFile] [ net_getfile_task] nella libreria di .NET Batch.

- **Codici di uscita attività**

    Come descritto in precedenza, un'attività è contrassegnata come non riuscita dal servizio Batch se il processo che viene eseguito dall'attività restituisce un codice di uscita diverso da zero. Quando un'attività viene eseguito un processo, Batch compila la proprietà dell'attività uscita codice con *restituito codice del processo*. È importante tenere presente che il codice di uscita dell'attività è **non** è determinato dal servizio Batch - è determinato dallo stesso processo o il sistema operativo in cui è stato eseguito il processo.

### <a name="accounting-for-task-failures-or-interruptions"></a>Contare le interruzioni di attività o delle interruzioni

Le attività in alcuni casi possono avere esito negativo o essere interrotte. L'applicazione di attività può avere esito negativo, il nodo in cui l'attività è in esecuzione potrebbe essere riavviato o il nodo potrebbe essere rimosso dal pool di durante un'operazione di ridimensionamento se il criterio di rimozione del pool è impostato per rimuovere nodi immediatamente senza attendere il completamento delle attività. In tutti i casi, l'attività possa essere automaticamente riaccodato da Batch per l'esecuzione in un altro nodo.

È inoltre possibile che causano il blocco di richiedono troppo tempo per eseguire un'attività di un problema intermittente. È possibile impostare il tempo massimo di esecuzione per un'attività. Se viene superato, Batch interrompe l'applicazione di attività.

### <a name="connecting-to-compute-nodes"></a>Connessione per nodi di calcolo

È possibile eseguire il debug e risoluzione dei problemi per l'accesso a un nodo di calcolo in remoto aggiuntive. È possibile usare il portale di Azure per scaricare un file remoto protocollo RDP (Desktop) per i nodi di Windows e ottenere informazioni di connessione sicura Shell (SSH) per i nodi Linux. Effettuare questa operazione utilizzando le API Batch, ad esempio, con [.NET Batch] [ net_rdpfile] o [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Per connettersi a un nodo tramite RDP o SSH, è innanzitutto necessario creare un utente sul nodo. A questo scopo è possibile utilizzare la Azure portale, [aggiungere un account utente a un nodo] [ rest_create_user] tramite l'API REST Batch chiamare [ComputeNode.CreateComputeNodeUser] [ net_create_user] metodo in Batch .NET o una chiamata [add_user] [ py_add_user] metodo nel modulo Batch Python.

### <a name="troubleshooting-bad-compute-nodes"></a>Risoluzione dei problemi "mai" nodi di calcolo

In situazioni in cui alcune attività hanno esito negativo, l'applicazione client Batch o servizio possibile esaminare i metadati di attività non riuscite per identificare un nodo malfunzionante. Ogni livello di nodo di un pool viene assegnato un ID univoco e il nodo sul quale viene eseguito un'attività viene incluso nei metadati attività. Dopo aver identificato un nodo problema, è possibile eseguire diverse operazioni cui:

- **Riavviare il nodo** ([REST][rest_reboot] | [.NET][net_reboot])

    Riavviare il nodo in alcuni casi può liberare affidabili problemi come processi bloccati o bloccati. Nota Se il pool utilizza un'attività di inizio o il processo utilizza un'attività di preparazione del processo, vengono eseguiti quando si riavvia il nodo.

- **Creare una nuova immagine il nodo** ([REST][rest_reimage] | [.NET][net_reimage])

    Consente di reinstallare il sistema operativo sul nodo. Come con il riavvio di un nodo, avviare attività e attività di preparazione processo vengono eseguiti nuovamente dopo il nodo è stata ricreata l'immagine.

- **Rimuovere il nodo del pool** ([REST][rest_remove] | [.NET][net_remove])

    A volte è necessario rimuovere completamente il nodo del pool.

- **Disattivare la programmazione del nodo di attività** ([REST][rest_offline] | [.NET][net_offline])

    Questo in modo efficace accetta il nodo "offline" in modo che nessun ulteriore attività vengono assegnate a tale, ma consente il nodo rimanga in esecuzione e del pool. In questo modo è possibile eseguire ulteriori analisi della causa errori di senza perdere i dati dell'attività non riuscita - e senza il nodo causa errori di altre attività. Ad esempio, è possibile disattivare la programmazione del nodo, quindi [accedere in remoto](#connecting-to-compute-nodes) per esaminare i registri eventi del nodo o l'esecuzione di risoluzione dei problemi di attività. Dopo aver completato l'analisi, è possibile importare quindi il nodo in linea, consentendo la programmazione delle attività ([resto][rest_online] | [.NET][net_online]), oppure eseguire una delle azioni descritte in precedenza.

> [AZURE.IMPORTANT] Con ogni azione descritte in questa sezione: riavviare il computer, reimage, rimuovere e disabilitare la programmazione delle attività, è possibile specificare la modalità di Gestione attività attualmente in esecuzione sul nodo quando si esegue l'azione. Ad esempio, quando si disattiva la programmazione delle attività in un nodo utilizzando la libreria client .NET Batch, è possibile specificare un [DisableComputeNodeSchedulingOption] [ net_offline_option] valore di enumerazione per specificare se a **Termina** attività in esecuzione, **riaccodare** di pianificazione su altri nodi o consentire l'esecuzione di attività da completare prima di eseguire l'azione (**TaskCompletion**).

## <a name="next-steps"></a>Passaggi successivi

- Scorrere di un'applicazione di esempio Batch dettagliata [nell'iniziare a utilizzare la libreria di Batch Azure per .NET](batch-dotnet-get-started.md). È inoltre disponibile una [versione di Python](batch-python-tutorial.md) dell'esercitazione che esegue un carico di lavoro su nodi di calcolo di Linux.

- Scaricare e creare il [Batch Explorer] [ github_batchexplorer] progetto di esempio per l'utilizzo mentre si sviluppano soluzioni Batch. Usa Esplora aree di Batch, è possibile eseguire le operazioni seguenti e altro ancora:
  - Monitorare e gestire pool, processi e le attività incluse account Batch
  - Scaricare `stdout.txt`, `stderr.txt`e altri file da nodi
  - Creare utenti in nodi e scaricare file RDP per l'accesso remoto

- Informazioni su come [creare pool Linux dei nodi di calcolo](batch-linux-nodes.md).

- Visitare il [forum Azure Batch] [ batch_forum] su MSDN. Il forum è un ottimo strumento per porre domande, se è semplice apprendimento o un esperto con Batch.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
