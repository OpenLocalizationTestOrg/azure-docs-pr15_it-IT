<properties 
   pageTitle="Che cos'è StorSimple Snapshot Manager? | Microsoft Azure"
   description="Descrive il responsabile di Snapshot StorSimple, l'architettura e le funzionalità."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="what-is-storsimple-snapshot-manager"></a>Che cos'è StorSimple Snapshot Manager?

## <a name="overview"></a>Panoramica

StorSimple Snapshot Manager è uno snap-in Microsoft Management Console (MMC) che semplifica la protezione dei dati e la gestione di backup in un ambiente di Microsoft Azure StorSimple. Con StorSimple Snapshot Manager, è possibile gestire dati di Microsoft Azure StorSimple nell'interfaccia di dati e nel cloud come una soluzione di archiviazione integrata singola, pertanto semplificare i processi di backup e ridurre i costi.

In questo argomento presenta gestore Snapshot StorSimple, descrive le funzionalità e spiega il proprio ruolo in Microsoft Azure StorSimple. 

Per una panoramica dell'intero sistema di Microsoft Azure StorSimple, inclusi il dispositivo StorSimple, servizio di gestione StorSimple, StorSimple Snapshot Manager e scheda StorSimple per SharePoint, vedere [StorSimple 8000 serie: una soluzione di archiviazione cloud ibrida](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- È possibile usare StorSimple Snapshot Manager per gestire Microsoft Azure StorSimple virtuale matrici (noto anche come StorSimple locale dispositivi virtuali).
>
>- Se si intende installare l'aggiornamento StorSimple 2 sul dispositivo StorSimple, assicurarsi di scaricare la versione più recente di gestione di Snapshot di StorSimple e installarlo **prima di installare StorSimple aggiornamento 2**. La versione più recente di StorSimple Snapshot Manager è compatibile con le versioni precedenti e interagisce con tutte le versioni di Microsoft Azure StorSimple. Se si utilizza la versione precedente di StorSimple Snapshot Manager, sarà necessario aggiornarli (non occorre prima di installare la nuova versione, disinstallare la versione precedente).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Architettura e lo scopo di StorSimple Snapshot Manager

StorSimple Snapshot Manager fornisce una console di gestione centrale che è possibile utilizzare per creare coerenti, nel momento copie locali e cloud dati. Ad esempio, è possibile utilizzare alla console di:

- Configurare, eseguire il backup ed eliminare volumi.
- Configurare i gruppi di volume per assicurarsi che il backup dei dati sia coerente con l'applicazione.
- Gestire i criteri di backup in modo che backup dei dati in una pianificazione predeterminata.
- Creare locali e cloud snapshot, che possono essere archiviati nel cloud e utilizzati per il ripristino di emergenza.

Gestione di Snapshot StorSimple recupera l'elenco delle applicazioni registrato con il provider VSS nell'host. Quindi, per creare backup coerenti con l'applicazione, controlla i volumi utilizzati da un'applicazione e suggerisce gruppi volume da configurare. Gestione di Snapshot StorSimple utilizza i gruppi di volume per generare le copie di backup sono coerenti con l'applicazione. (La coerenza applicazione esiste quando tutti i relativi file e database vengono sincronizzati e rappresentano lo stato dell'applicazione in un punto specifico in tempo reale). 

Backup StorSimple Snapshot Manager sotto forma di snapshot incrementale cui acquisire solo le modifiche apportate dall'ultimo backup. Di conseguenza, l'esecuzione di backup richiede meno spazio di archiviazione e può essere creati e ripristinati rapidamente. Gestione di Snapshot StorSimple utilizza il servizio di copia ombreggiatura (VSS) di Windows Volume per assicurarsi che istantanee acquisiscono i dati sono coerenti dell'applicazione. (Per ulteriori informazioni, andare all'integrazione con sezione servizio di copia di Windows Volume ombreggiatura) Con StorSimple Snapshot Manager, è possibile creare pianificazioni di backup o eseguire backup immediato in base alle esigenze. Se è necessario ripristinare i dati da un backup, consente di StorSimple Snapshot Manager selezionare da un catalogo di locale o istantanee cloud. Azure StorSimple Ripristina solo i dati necessari secondo le necessità, impedendo ritardi nella disponibilità dei dati durante le operazioni di ripristino.)

![Architettura StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architettura StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Supporto per più tipi di volume

È possibile utilizzare il responsabile di Snapshot StorSimple per configurare e drill-up i tipi di volumi seguenti: 

- **Volumi di base** : un volume di base è una singola partizione su un disco di base. 

- **Volumi semplici** – un volume semplice è un volume dinamico che contiene spazio su disco di un singolo disco dinamico. Un volume semplice è costituito da una singola regione su un disco o più aree che sono collegate tra loro sullo stesso disco. (È possibile creare volumi semplici solo su dischi dinamici). Volumi semplici non sono tolleranza.

- **Volumi dinamici** : un volume dinamico è un volume creato su un disco dinamico. Dischi dinamici utilizzano un database per tenere traccia delle informazioni relative ai volumi contenuti su dischi dinamici in un computer. 

- **Volumi dinamici con che rispecchiano** – volumi dinamici con che rispecchiano si basano su architettura RAID 1. Con RAID 1 dati identici scritto in due o più disco, la creazione di un set di speculare. Una richiesta di lettura può quindi essere gestita da qualsiasi disco che contiene i dati richiesti.

- **Volumi condivisi cluster** – con volumi condivisi cluster (CSVs), più nodi in un cluster di failover possono contemporaneamente leggere o scrivere allo stesso disco. Il controllo da un nodo a un altro nodo può verificarsi rapidamente, senza modificare la proprietà di unità o montaggio disinstallazione e la rimozione di un volume. 

>[AZURE.IMPORTANT] Non combinare CSVs e non CSVs nello stesso snapshot. La combinazione di CSVs e non CSVs in uno snapshot non è supportata. 
 
È possibile utilizzare StorSimple Snapshot Manager per ripristinare i gruppi di intero volume o duplicare singoli volumi e recuperare singoli file.

- [Volumi e i gruppi di volume](#volumes-and-volume-groups) 
- [Tipi di backup e criteri di backup](#backup-types-and-backup-policies) 

Per ulteriori informazioni sulle caratteristiche di gestione di Snapshot StorSimple e sul loro utilizzo, vedere [interfaccia utente StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumi e i gruppi di volume

Con StorSimple Snapshot Manager, creare volumi e configurarlo in gruppi volume. 

StorSimple Snapshot Manager utilizza gruppi volume per creare copie di backup sono coerenti con l'applicazione. La coerenza applicazione esiste quando tutti i relativi file e database vengono sincronizzati e rappresentano lo stato di un'applicazione in un punto specifico in tempo reale. Volume gruppi (noto anche come *gruppi coerenza*) costituiscono la base di una copia di backup o ripristino un processo.

Gruppi di volume non corrispondono contenitori volume. Un contenitore volume contiene uno o più volumi che condividono un account di archiviazione cloud e altri attributi, ad esempio consumo della larghezza di banda e la crittografia. Un contenitore singolo volume può contenere fino a 256 volumi StorSimple eseguono provisioning. Per ulteriori informazioni sui contratti contenitori, passare a [gestire i contenitori di volume](storsimple-manage-volume-containers.md). Volume gruppi sono insiemi di volumi configurati per semplificare le operazioni di backup. Se si selezionano due volumi che appartengono a contenitori volume diverso, collocarli in un gruppo singolo volume e quindi creare criteri di backup per tale gruppo volume, ogni volume verrà backup nel contenitore volume appropriato, usando l'account di archiviazione appropriato.

>[AZURE.NOTE] Tutti i volumi in un gruppo di volume devono provenire da un provider di servizi cloud singola.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrazione con il servizio di copia ombreggiatura Volume di Windows

StorSimple Snapshot Manager utilizza il servizio di copia ombreggiatura (VSS) di Windows Volume per acquisire i dati sono coerenti dell'applicazione. VSS facilita la coerenza applicazione, la comunicazione con le applicazioni compatibili con VSS per coordinare la creazione di istantanee incrementale. VSS garantisce che le applicazioni siano temporaneamente inattivi o inattivo, quando vengono registrate istantanee. 

L'implementazione di StorSimple Snapshot Manager di VSS opera con SQL Server e volumi generici. Il processo è come indicato di seguito: 

1. Un richiedente, in genere è una gestione dei dati e soluzione di protezione (ad esempio StorSimple Snapshot Manager) o un'applicazione di backup, richiama VSS e chiede di eseguire raccogliere informazioni da software writer nell'applicazione di destinazione.

2. VSS contatta il componente writer per recuperare una descrizione dei dati. Il writer restituisce la descrizione dei dati per il backup. 

3. VSS segnala writer per preparare l'applicazione per il backup. Il writer prepara i dati per il backup, completando le transazioni aperte, l'aggiornamento dei registri delle transazioni e così via, quindi avvisa VSS.

4. VSS indica il writer temporaneamente interrompere archivi dati dell'applicazione e assicurarsi che nessun dato è scritto il volume durante la copia di ombreggiatura viene creata. Questo passaggio assicura la coerenza dei dati e richiede non più di 60 secondi.

5. VSS indica il provider di creare una copia dell'ombreggiatura. Provider, che può essere software o hardware basate su, gestire i volumi che stanno attualmente in esecuzione e creare copie di essi su richiesta. Il provider crea la copia di ombreggiatura e notifica VSS sia stata completata.

6. VSS contatti writer per ricevere una notifica dell'applicazione in cui i/o riprendere e anche per verificare che i/o sospensione riuscita durante la creazione della copia di ombreggiatura. 

7. Se la copia è stata completata, VSS restituisce la posizione della copia al richiedente. 

8. Se i dati sono stati scritti durante la copia è stata creata, il backup non sarà coerente. Elimina la copia di ombreggiatura e informa il richiedente. Il richiedente può ripetere il processo di backup automatico o una notifica all'amministratore di Riprova in un secondo momento.

Vedere la figura seguente.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo del servizio Copia di Windows Volume ombreggiatura** 

## <a name="backup-types-and-backup-policies"></a>Tipi di backup e criteri di backup

Con StorSimple Snapshot Manager, è possibile eseguire il backup dei dati e archiviarlo in locale e nel cloud. È possibile utilizzare StorSimple Snapshot Manager per il backup dei dati immediatamente oppure è possibile utilizzare un criterio di backup per creare una pianificazione per eseguendo il backup automatico. Anche i criteri di backup consentono di specificare quante istantanee verranno mantenute. 

### <a name="backup-types"></a>Tipi di backup

È possibile utilizzare StorSimple Snapshot Manager per creare i tipi di backup seguenti:

- **Snapshot locale** – istantanee locale sono in un momento copie dei dati di volume che sono archiviate nel dispositivo StorSimple. In genere, è possibile creare e ripristinato rapidamente questo tipo di backup. Come si farebbe con una copia di backup locale, è possibile usare uno snapshot locale.

- **Snapshot cloud** – Cloud istantanee sono in un momento copie dei dati di volume archiviati nel cloud. Uno snapshot cloud corrisponde a uno snapshot replicato in un sistema di archiviazione diverso, fuori sede. Snapshot cloud sono particolarmente utili di emergenza.

### <a name="on-demand-and-scheduled-backups"></a>Backup su richiesta e pianificato

Con StorSimple Snapshot Manager, è possibile avviare un backup unico creare immediatamente oppure è possibile utilizzare un criterio di backup per pianificare le operazioni di backup ricorrente.

Criteri di backup sono un set di regole automatica che è possibile utilizzare per pianificare l'esecuzione di backup regolari. Criteri di backup consentono di definire la frequenza e parametri per l'esecuzione di snapshot di un gruppo di volume specifico. È possibile utilizzare criteri per specificare le date di inizio e scadenza, ore, frequenza e requisiti di conservazione, per sia locali e cloud istantanee. Un criterio viene applicato immediatamente dopo avere definito. 

È possibile utilizzare StorSimple Snapshot Manager per configurare o riconfigurare i criteri di backup ogni volta che è necessario. 

Configurare le informazioni seguenti per ogni criterio backup creati:

- **Nome** : il nome univoco del criterio di backup selezionato.

- **Tipo** : il tipo di criteri di backup. snapshot locale o uno snapshot cloud.

- **Gruppo volume** : gruppo volume a cui è assegnato il criterio di backup selezionato.

- **Criteri di conservazione** : il numero di copie di backup da mantenere. Se si seleziona la casella di **tutto** , tutte le copie di backup vengono mantenute fino a raggiunta il numero massimo di copie di backup per volume, quindi il criterio avrà esito negativo e generare un messaggio di errore. In alternativa, è possibile specificare un numero di backup per la conservazione (compreso tra 1 e 64).

- **Data** : data di creazione di criteri di backup.

Per informazioni sulla configurazione di criteri di backup, passare a [Usare StorSimple Snapshot Manager per creare e gestire i criteri di backup](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Gestione e il processo di backup monitoraggio

È possibile utilizzare il responsabile di Snapshot StorSimple per monitorare e gestire processi di backup in scadenza, pianificati e completati. Inoltre, StorSimple Snapshot Manager fornisce un catalogo di backup completato fino a 64. Per trovare e ripristinare volumi o singoli file, è possibile utilizzare il catalogo. 

Per informazioni sul monitoraggio processi di backup, passare a [Usare StorSimple Snapshot Manager per visualizzare e gestire processi di backup](storsimple-snapshot-manager-manage-backup-jobs.md).


## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni [sull'utilizzo di gestione di Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).

- Scarica [Snapshot StorSimple Manager](https://www.microsoft.com/download/details.aspx?id=44220).
