<properties 
   pageTitle="Che cos'è StorSimple? | Microsoft Azure" 
   description="Vengono descritti StorSimple collegamento, il dispositivo, dispositivo virtuale, servizi e la gestione degli archivi e introduce termini principali utilizzati in StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida

## <a name="overview"></a>Panoramica

Introduzione a Microsoft Azure StorSimple, una soluzione di archiviazione integrata che consente di gestire le attività di archiviazione tra i dispositivi in locale e lo spazio di archiviazione cloud Microsoft Azure. StorSimple è un'utilizzo efficiente, conveniente e facilmente gestibile (SAN) soluzione SAN che elimina molte i problemi e le spese associate enterprise lo spazio di archiviazione e protezione dei dati. Usa il dispositivo di serie 8000 StorSimple proprietario, si integra con servizi cloud e fornisce un set di strumenti di gestione per una visualizzazione ottimale dell'archiviazione enterprise, tra cui lo spazio di archiviazione cloud. (Le informazioni di distribuzione StorSimple pubblicate nel sito Web di Microsoft Azure si applicano a 8000 StorSimple ai dispositivi delle serie. Se si utilizza un dispositivo di serie StorSimple 5000/7000, vedere [Assistenza](http://onlinehelp.storsimple.com/)per il StorSimple.)

StorSimple utilizza [il collegamento a livelli lo spazio di archiviazione](#automatic-storage-tiering) per gestire i dati archiviati in vari supporti di archiviazione. Il set di lavoro corrente è archiviati in locale nel (SSD), dati utilizzata raramente sono archiviati in unità disco rigido (dischi rigidi) e archiviazione dati vengono spostati nel cloud. Inoltre, StorSimple utilizza deduplication e la compressione per ridurre la quantità di spazio di archiviazione che utilizza i dati. Per ulteriori informazioni, visitare [Deduplication e la compressione](#deduplication-and-compression). Le definizioni di altri termini e concetti chiave utilizzati nella documentazione della serie 8000 StorSimple, passare alla [terminologia StorSimple](#storsimple-terminology) alla fine di questo articolo.

Con StorSimple aggiornamento 2, è possibile identificare volumi appropriati come *localmente aggiunte* alla assicurarsi che i dati principali rimangono locali nel dispositivo e non a più livelli nel cloud. In questo modo è possibile eseguire carichi di lavoro che fanno sensibili a latenza cloud, ad esempio SQL e macchina virtuale carichi di lavoro, volumi aggiunte in locale, pur continuando a usare il cloud per il backup. Per ulteriori informazioni sui volumi bloccati in locale, vedere [usare il servizio di gestione di StorSimple per gestire i volumi](storsimple-manage-volumes-u2.md). 

Aggiornamento 2 è inoltre possibile creare dispositivi StorSimple virtuali sfruttano la latenza bassa e prestazioni di alto per lo spazio di archiviazione di Azure premium. Per ulteriori informazioni sui dispositivi virtuale premium StorSimple, vedere [Distribuisci e gestire un dispositivo virtuale StorSimple in Azure](storsimple-virtual-device-u2.md). Per ulteriori informazioni sull'archiviazione premium Azure, passare a [lo spazio di archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md).

Oltre a gestione di archiviazione, funzioni di protezione dei dati StorSimple consentono di creare su richiesta e pianificato l'esecuzione di backup e quindi archiviarli in locale o nel cloud. Backup sotto forma di istantanee incrementali, vale a dire che può essere creati e ripristinati rapidamente. Snapshot cloud può essere importanza di emergenza poiché sostituire i sistemi di archiviazione secondaria (ad esempio backup su nastro) e consentono di ripristinare i dati per il Data Center o ai siti alternativi se necessario.

![icona del video](./media/storsimple-overview/video_icon.png) Guardare il video per una breve introduzione a Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>Perché usare StorSimple?

Nella tabella seguente descrive alcuni dei vantaggi principali di Microsoft Azure StorSimple fornisce.

| Caratteristica | Vantaggi |
|---------|---------|
|Integrazione trasparente | Microsoft Azure StorSimple utilizza il protocollo iSCSI per creare un collegamento in modo invisibile strutture di spazio di archiviazione di dati. Questo garantisce che i dati archiviati nel cloud, in Data Center, o su server remoti risulta archiviato in un'unica posizione.|
|Costi di archiviazione ridotto|Microsoft Azure StorSimple assegni sufficienti locale o lo spazio di archiviazione cloud per soddisfare le esigenze corrente e si estende lo spazio di archiviazione cloud solo quando necessario. Ulteriormente, riduce i requisiti di spazio di archiviazione e spese eliminando ridondanti versioni degli stessi dati (deduplication) e utilizzando la compressione.|
|Gestione semplificata archiviazione|Microsoft Azure StorSimple fornisce sistema gli strumenti di amministrazione che è possibile utilizzare per configurare e gestire i dati archiviati in locale, su un server remoto e nel cloud. Inoltre, è possibile gestire backup e ripristino funzioni uno snap-in Microsoft Management Console (MMC). StorSimple offre un'interfaccia distinta, facoltativa che è possibile utilizzare per estendere i servizi di protezione di gestione e dati StorSimple per il contenuto archiviato in SharePoint Server. |
|Ripristino di emergenza migliorato e conformità|Microsoft Azure StorSimple non richiede tempi di ripristino esteso. Se, tuttavia, Ripristina dati secondo le necessità. Questo errore indica le normali operazioni è possono continuare con un'interruzione minima. Inoltre, è possibile configurare i criteri per specificare le pianificazioni di backup e la conservazione dei dati.
|Dei dati|Accedere ai dati caricati ai servizi cloud Microsoft Azure da altri siti ai fini della migrazione e ripristino. Inoltre, è possibile utilizzare StorSimple per configurare i dispositivi virtuali StorSimple su macchine () in esecuzione in Microsoft Azure. Macchine virtuali quindi possono utilizzare i dispositivi virtuali per accedere ai dati memorizzati scopi test o ripristino.|
|Supporto per altri provider di servizi cloud |La serie 8000 StorSimple con un software per aggiornare 1 o versioni successive supportano S3 Amazon con record di risorse, HP e OpenStack cloud services, come Microsoft Azure. (Sarà comunque necessario un account di archiviazione di Microsoft Azure fini della gestione dei dispositivi.) Per ulteriori informazioni, passare alla [posizione di aggiornamento 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuità aziendale | Aggiornamento 1 o versione successiva disponibile una nuova caratteristica di migrazione che consente agli utenti di serie 5000 7000 di eseguire la migrazione dei dati in un dispositivo di serie 8000 StorSimple StorSimple.|
|Disponibilità nel portale di Azure per la pubblica amministrazione | È disponibile nel portale per enti pubblici di Azure StorSimple aggiornamento 1 o versione successiva. Per ulteriori informazioni, vedere [distribuire il dispositivo di StorSimple locale nel portale per enti pubblici](storsimple-deployment-walkthrough-gov.md).|
|Disponibilità e protezione dei dati | La serie 8000 StorSimple con l'aggiornamento 1 o versioni successive supporta zona ridondanti dello spazio di archiviazione (ZRS), oltre a localmente ridondanti dello spazio di archiviazione (LRS) e geografico ridondanti dello spazio di archiviazione (GRS). Fare riferimento all' [articolo sulle opzioni di archiviazione Azure ridondanza](https://azure.microsoft.com/documentation/articles/storage-redundancy/) per informazioni dettagliate ZRS.|
|Supporto per le applicazioni | Con StorSimple aggiornamento 2, è possibile identificare volumi appropriati aggiunti in locale. Questa funzionalità garantisce che dati richiesti dalle applicazioni critiche non a più livelli nel cloud. Volumi localmente bloccati non sono soggetti a latenza cloud o problemi di connettività. Per ulteriori informazioni sui volumi bloccati in locale, vedere [usare il servizio di gestione di StorSimple per gestire i volumi](storsimple-manage-volumes-u2.md).|
|Bassa latenza e prestazioni elevate | StorSimple aggiornamento 2 consente di creare dispositivi virtuali che si avvalgono delle prestazioni, caratteristiche di bassa latenza di spazio di archiviazione di Azure premium. Per ulteriori informazioni sui dispositivi virtuale premium StorSimple, vedere [Distribuisci e gestire un dispositivo virtuale StorSimple in Azure](storsimple-virtual-device-u2.md).|

![icona del video](./media/storsimple-overview/video_icon.png) guardare [questo video](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) per una panoramica delle funzionalità di serie 8000 StorSimple e vantaggi.

## <a name="storsimple-components"></a>Componenti StorSimple

La soluzione Microsoft Azure StorSimple include i componenti seguenti:

- **Dispositivo di Microsoft Azure StorSimple** – una matrice di spazio di archiviazione ibrido locale contenente SSDs e dischi rigidi, insieme alla controller ridondanti e funzionalità di failover automatico. Controller di gestiscono l'archiviazione livelli, immissione i dati usati (o attivo) attualmente sull'archiviazione locale (in locale o dispositivo server), mentre si sposta meno dati utilizzati di frequente nel cloud.
- **Dispositivo virtuale StorSimple** , noto anche come il dispositivo virtuale StorSimple, si tratta di una versione del software del dispositivo StorSimple che replica l'architettura e la maggior parte delle funzionalità del dispositivo di archiviazione ibrido fisica. Il dispositivo virtuale StorSimple viene eseguito su un singolo nodo in una macchina virtuale Azure. Dispositivi virtuali Premium, che è possibile sfruttare lo spazio di archiviazione di Azure premium, sono disponibili nell'aggiornamento 2 e versioni successive.
- **Servizio di gestione di StorSimple** : un'estensione del portale di classica Azure che consente di gestire un dispositivo StorSimple o un dispositivo virtuale StorSimple da un'interfaccia web singola. È possibile utilizzare il servizio di gestione di StorSimple per creare e gestire servizi, visualizzare e gestire i dispositivi, visualizzare gli avvisi, gestire i volumi e visualizzare e gestire i criteri di backup e il catalogo di backup.
- **Windows PowerShell per StorSimple** -interfaccia della riga che è possibile utilizzare la gestione del dispositivo StorSimple. Windows PowerShell per StorSimple include caratteristiche che consentono di eseguire la registrazione del dispositivo StorSimple, configurare l'interfaccia di rete nel dispositivo, installare determinati tipi di aggiornamenti, risolvere i problemi del dispositivo, accedere alla sessione di supporto e modificare lo stato del dispositivo. È possibile accedere Windows PowerShell per StorSimple connettendosi a console seriale oppure usando Windows PowerShell remota.
- **Cmdlet di azure PowerShell StorSimple** – un insieme di cmdlet di Windows PowerShell che consentono di automatizzare le attività di migrazione e a livello di servizio dalla riga di comando. Per ulteriori informazioni sui cmdlet di PowerShell Azure per StorSimple, visitare il [riferimento sui cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **StorSimple Snapshot Manager** – snap-in MMC che utilizza gruppi volume e il servizio di copia di Windows Volume ombreggiatura generi backup coerenti con l'applicazione. Inoltre, è possibile utilizzare StorSimple Snapshot Manager per creare duplicato e pianificazioni di backup o ripristino volumi. 
- **Scheda StorSimple per SharePoint** : uno strumento che si estende in modo trasparente Microsoft Azure StorSimple lo spazio di archiviazione e protezione dei dati in SharePoint Server farm, rendendo StorSimple archiviazione visualizzabili e gestibile dal portale di amministrazione centrale SharePoint.

La figura seguente viene fornita una panoramica dei componenti e architettura di Microsoft Azure StorSimple.

![Architettura StorSimple](./media/storsimple-overview/overview-big-picture.png)

Nelle sezioni seguenti descrivono ognuno di essi in modo più dettagliato e spiegano come la soluzione dispone i dati, allocazione dello spazio di archiviazione e facilita la gestione degli archivi e protezione dei dati. L'ultima sezione vengono fornite le definizioni per alcuni termini importanti e i concetti relativi ai componenti StorSimple e la gestione.

## <a name="storsimple-device"></a>Dispositivo StorSimple

Il dispositivo di Microsoft Azure StorSimple è una matrice di spazio di archiviazione ibrido locale che consente l'archiviazione principale e iSCSI accesso ai dati archiviati in cui è. Gestisce la comunicazione con lo spazio di archiviazione cloud e garantire la sicurezza e la riservatezza di tutti i dati memorizzati sulla soluzione Microsoft Azure StorSimple.

Il dispositivo StorSimple include SSDs e dischi rigidi unità disco rigido, nonché il supporto per failover cluster e automatico. Contiene un processore condiviso, l'archiviazione condivisa e due controller speculare. Ogni controller offre quanto segue:

- Connessione a un computer host
- Fino a sei porte di rete per connettersi alla rete locale (LAN)
- Monitoraggio dei componenti hardware
- Non volatili RAM (NVRAM), che mantiene le informazioni anche se power viene interrotta
- Aggiornamento per gestire gli aggiornamenti software su server in un cluster di failover in modo che gli aggiornamenti abbiano minimo di supporto cluster o alcun effetto sulla disponibilità del servizio
- Servizio cluster, che funziona come un cluster di back-end, disponibilità elevata e ridurre eventuali effetti negativi che possono verificarsi se un disco rigido SSD ha esito negativo o è in linea

Un solo controller è attivo in qualsiasi punto nel tempo. Se non riesce controller attivo, il secondo controller diventa attivo automaticamente. 

Per ulteriori informazioni, passare a [componenti hardware StorSimple e sullo stato](storsimple-monitor-hardware-status.md).

## <a name="storsimple-virtual-device"></a>Dispositivo virtuale StorSimple

È possibile utilizzare StorSimple per creare un dispositivo virtuale replica l'architettura e funzionalità del dispositivo di archiviazione fisica ibrida. Il dispositivo virtuale StorSimple (noto anche come StorSimple virtuale accessorio) viene eseguito su un singolo nodo in una macchina virtuale Azure. (È possibile creare un dispositivo virtuale solo in un computer virtuale Azure. È possibile creare uno in un dispositivo StorSimple o su un server locale.) 

Il dispositivo virtuale è le caratteristiche seguenti:

- Si comporta come un dispositivo fisico e offre un'interfaccia iSCSI in macchine virtuali nel cloud. 
- È possibile creare un numero illimitato di dispositivi virtuali nel cloud e riattivarle attivato e disattivato in base alle esigenze. 
- Consente di simulare ambienti locale di emergenza, sviluppo e scenari di prova e consente di con recupero a livello di elemento da un backup. 

Con aggiornamento 2 e versioni successive, il dispositivo virtuale StorSimple è disponibile in due modelli: il dispositivo di 8010 (precedentemente noto come modello 1100) e il dispositivo 8020. Il dispositivo 8010 ha una capacità massima di 30 TB. Il dispositivo 8020, che consente di sfruttare lo spazio di archiviazione di Azure premium, ha una capacità massima di 64 TB. (In locali livelli, lo spazio di archiviazione di Azure premium archivia i dati sugli SSDs mentre lo spazio di archiviazione standard archivia i dati sugli dischi rigidi.) Nota è necessario che un account di archiviazione Azure premium da utilizzare lo spazio di archiviazione premium. Per ulteriori informazioni sull'archiviazione premium, passare a [lo spazio di archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md).

Per ulteriori informazioni sul dispositivo virtuale StorSimple, passare a [Distribuisci e gestire un dispositivo virtuale StorSimple in Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Servizio di gestione StorSimple

Microsoft Azure StorSimple offre un'interfaccia utente basata sul web, il servizio di gestione StorSimple, che consente di gestire Data Center e cloud lo spazio di archiviazione. È possibile utilizzare il servizio di gestione di StorSimple per eseguire le operazioni seguenti:

- Configurare le impostazioni di sistema per i dispositivi StorSimple.
- Configurare e gestire le impostazioni di sicurezza per i dispositivi StorSimple.
- Configurare le proprietà e le credenziali cloud.
- Configurare e gestire i volumi su un server.
- Configurare i gruppi di volume.
- Eseguire il backup e ripristino dei dati.
- Monitoraggio delle prestazioni.
- Esaminare le impostazioni di sistema e identificare possibili problemi.

È possibile utilizzare il servizio di gestione di StorSimple per eseguire tutte le attività amministrative ad eccezione di quelli che richiedono il sistema tempi di inattività, ad esempio con l'installazione degli aggiornamenti di installazione e configurazione iniziali.

Per ulteriori informazioni, passare a [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell per StorSimple

Windows PowerShell per StorSimple offre un'interfaccia della riga di comando che è possibile utilizzare per creare e gestire il servizio di Microsoft Azure StorSimple e configurare e monitorare StorSimple dispositivi. È un'interfaccia della riga di comando basato su Windows PowerShell, che include i cmdlet dedicati per la gestione del dispositivo StorSimple. Windows PowerShell per StorSimple include caratteristiche che consentono di:

- Registrare un dispositivo.
- Configurare l'interfaccia di rete in un dispositivo.
- Installare determinati tipi di aggiornamenti.
- Risoluzione dei problemi del dispositivo, accedere alla sessione di supporto.
- Modificare lo stato dispositivo.

È possibile accedere Windows PowerShell per StorSimple da una console seriale (in un computer host connesso direttamente al dispositivo) o in modalità remota tramite Windows PowerShell remota. Si noti che alcune Windows PowerShell per le attività StorSimple, ad esempio la registrazione di dispositivo iniziale, può essere eseguita solo sulla console seriale. 

Per ulteriori informazioni, vedere [Usare Windows PowerShell per StorSimple amministrare il dispositivo](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Cmdlet di PowerShell StorSimple Azure

I cmdlet di Azure PowerShell StorSimple sono un insieme di cmdlet di Windows PowerShell che consentono di automatizzare le attività di migrazione e a livello di servizio dalla riga di comando. Per ulteriori informazioni sui cmdlet di PowerShell Azure per StorSimple, visitare il [riferimento sui cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>Gestione di Snapshot StorSimple

StorSimple Snapshot Manager è uno snap-in Microsoft Management Console (MMC) che è possibile utilizzare per creare coerenti, nel momento copie locali e cloud dati. Snap-in viene eseguito su un host basato su Windows Server. È possibile utilizzare Gestione Snapshot StorSimple per:

- Configurare, eseguire il backup ed eliminare volumi.
- Configurare i gruppi di volume per assicurarsi che il backup dei dati sia coerente con l'applicazione.
- Gestire i criteri di backup in modo che il backup su una pianificazione predeterminata e archiviati in un percorso definito (in locale o nel cloud) dei dati.
- Ripristinare i volumi e singoli file.

Backup vengono identificati come snapshot, che registrare solo le modifiche apportate dall'ultimo snapshot è stato creato e richiede molto meno spazio di archiviazione rispetto al backup completo. È possibile creare pianificazioni di backup o eseguire backup immediato in base alle esigenze. Inoltre, è possibile utilizzare StorSimple Snapshot Manager per definire i criteri di conservazione tale controllo quante istantanee verranno salvate. Se successivamente è necessario ripristinare i dati da un backup, consente di StorSimple Snapshot Manager selezionare dal catalogo di locale o istantanee cloud. 

In caso di emergenza o se è necessario ripristinare i dati di un altro motivo, StorSimple Snapshot Manager ripristina in modo incrementale secondo le necessità. Il ripristino dei dati non è necessario arrestare il sistema intero mentre si ripristina un file, sostituire le apparecchiature o spostare le operazioni in un altro sito.

Per ulteriori informazioni, passare a [che cos'è Gestione Snapshot StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Scheda StorSimple per SharePoint

Microsoft Azure StorSimple include la scheda StorSimple per SharePoint, un componente aggiuntivo che si estende in modo trasparente caratteristiche di protezione di spazio di archiviazione e dati StorSimple per server farm di SharePoint. La scheda funziona con un provider di archiviazione Blob remoto (RBS) e la funzionalità di SQL Server RBS consente di spostare BLOB a un server di backup dal sistema di Microsoft Azure StorSimple. Microsoft Azure StorSimple quindi Archivia i dati BLOB in locale o nel cloud, in base all'utilizzo.

Scheda StorSimple per SharePoint viene gestita all'interno del portale di amministrazione centrale SharePoint. Di conseguenza, gestione SharePoint rimane centralizzata e tutto lo spazio di archiviazione verrà visualizzato come della farm di SharePoint.

Per ulteriori informazioni, passare alla [Scheda StorSimple per SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologie di gestione di archiviazione
 
Oltre al dispositivo StorSimple dedicato, dispositivo virtuale e altri componenti, Microsoft Azure StorSimple utilizza le seguenti tecnologie di software per consentono di accedere rapidamente ai dati e ridurre il consumo di spazio di archiviazione:

- [Collegamento a livelli di archiviazione automatica](#automatic-storage-tiering) 
- [Provisioning sottile](#thin-provisioning) 
- [Deduplication e compressione](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Collegamento a livelli di archiviazione automatica

Microsoft Azure StorSimple dispone automaticamente i dati in livelli logici in base a utilizzo corrente, l'età e relazione ad altri dati. Dati che sono più attivi vengono archiviati in locale, mentre meno dati attivi e inattivi automaticamente la migrazione nel cloud. Il diagramma seguente illustra questo approccio per l'archiviazione.
 
![Livelli di spazio di archiviazione StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Per abilitare la barra di accesso rapido, StorSimple archivia dati molto attivi (dati attivo) su SSDs nel dispositivo StorSimple. Archivia dati utilizzati in alcuni casi (medio dati) su dischi rigidi nel dispositivo o nei server presso il Data Center. Sposta dati inattivi, backup e i dati mantenuti per archiviazione o a scopo di conformità nel cloud. 

>[AZURE.NOTE] Nell'aggiornamento 2 o versioni successive, è possibile specificare un volume aggiunta in locale, nel qual caso i dati rimangono nel dispositivo locale e non a più livelli nel cloud. 

StorSimple regolate e Riorganizza dati e modificare le assegnazioni di spazio di archiviazione come criteri di utilizzo. Ad esempio, alcune informazioni potrebbero verificarsi meno attivi nel tempo. Appena gradualmente meno attivo, viene definita da SSDs a dischi rigidi e quindi nel cloud. Se gli stessi dati viene riattivato, viene eseguita la migrazione al dispositivo di archiviazione.

Il processo di collegamento a livelli di spazio di archiviazione è il seguente:

1. Un amministratore di sistema configura automaticamente un account di archiviazione cloud di Microsoft Azure.
2. L'amministratore utilizza la console seriale e il servizio di gestione StorSimple (in esecuzione nel portale di classica Azure) per configurare il dispositivo e file server, la creazione di criteri di protezione volumi e i dati. Computer locale (ad esempio file server) consente di accedere al dispositivo StorSimple Internet Small Computer System Interface (iSCSI).
3. Inizialmente, StorSimple archivia i dati nel livello SSD fast del dispositivo.
4. Quando il livello SSD si avvicina capacità, StorSimple deduplicates comprime meno recente blocchi di dati e li sposta il livello del disco rigido.
5. Come la capacità di approcci livello del disco rigido, StorSimple crittografa meno recente blocchi di dati e li invia in modo sicuro per l'account di archiviazione di Microsoft Azure tramite HTTPS.
6. Microsoft Azure crea più repliche dei dati nella relativa data center e in un Data Center remoto, verificare che i dati possono essere ripristinati in caso di emergenza. 
7. Quando il file server richiede i dati archiviati nel cloud, StorSimple restituisce diretta e memorizza una copia nel livello SSD del dispositivo StorSimple.

### <a name="thin-provisioning"></a>Provisioning sottile

Il provisioning sottile è una tecnologia di virtualizzazione in cui lo spazio di archiviazione disponibile sembra superano risorse fisiche. Anziché la prenotazione di sufficiente spazio di archiviazione in anticipo, StorSimple utilizza il provisioning sottile allocare sufficiente spazio per soddisfare requisiti corrente. Natura flessibile di spazio di archiviazione cloud semplifica l'esecuzione di questo approccio perché StorSimple possono aumentare o diminuire lo spazio di archiviazione cloud per soddisfare le esigenze. 

>[AZURE.NOTE] Volumi localmente bloccati non vengono eseguono effettuato il provisioning. Viene eseguito il provisioning archiviazione allocato a un volume locale solo interamente quando viene creato il volume.

### <a name="deduplication-and-compression"></a>Deduplication e compressione

Microsoft Azure StorSimple utilizza deduplication e compressione dei dati per ridurre ulteriormente i requisiti di spazio di archiviazione.

Deduplication consente di ridurre la quantità totale di dati memorizzati, eliminando la ridondanza nel set di dati archiviate. Durante la modifica delle informazioni, StorSimple consente di ignorare i dati non modificati e acquisisce solo le modifiche. Inoltre, StorSimple riduce la quantità di dati memorizzati identificando e la rimozione di informazioni non necessarie. 

>[AZURE.NOTE] I dati in volumi localmente bloccati non deduplicated o compressi. Tuttavia, backup di volumi localmente bloccati sono deduplicated e compressa.

## <a name="storsimple-workload-summary"></a>StorSimple il carico di lavoro riepilogo

Un riepilogo dei carichi di lavoro StorSimple supportati è inserito sotto.

| Scenario:                  | Carico di lavoro                | Supportati |  Restrizioni                                  | Versione              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Collaborazione             | La condivisione di file            | Sì       |                                                | Tutte le versioni         |
| Collaborazione             | La condivisione di file distribuito| Sì       |                                                | Tutte le versioni         |
| Collaborazione             | SharePoint              | Sì *      |È supportata solo con volumi localmente bloccati      | Aggiornamento 2 e versioni successiva   |
| Archiviazione                  | Archiviazione di file semplice   | Sì       |                                                | Tutte le versioni         |
| Virtualizzazione            | Macchine virtuali        | Sì *      |È supportata solo con volumi localmente bloccati      | Aggiornamento 2 e versioni successiva   |
| Database                  | SQL                     | Sì *      |È supportata solo con volumi localmente bloccati      | Aggiornamento 2 e versioni successiva   |
| Controllo video        | Controllo video      | Sì *       |Supportati in seguito al dispositivo StorSimple si impegna costantemente solo per il carico di lavoro| Aggiornamento 2 e versioni successiva   |
| Copia di backup                    | Copia di backup di destinazione principale | Sì *      |Supportati in seguito al dispositivo StorSimple si impegna costantemente solo per il carico di lavoro| Aggiornamento 3 e versioni successive |
| Copia di backup                    | Copia di backup di destinazione secondaria | Sì *      |Supportati in seguito al dispositivo StorSimple si impegna costantemente solo per il carico di lavoro| Aggiornamento 3 e versioni successive |

*Sì & #42; -Applicare restrizioni e linee guida soluzione.*

I carichi di lavoro seguenti non sono supportate dai dispositivi serie 8000 StorSimple. Se distribuiti in StorSimple, questi carichi di lavoro comporterà una configurazione non supportata.

-  Immagini medicina
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Dati
-  Distribuzione del contenuto
-  Avvio da SCSI

Seguito è riportato un elenco dei componenti dell'infrastruttura StorSimple supportati. 

| Scenario: | Carico di lavoro      | Supportati |  Restrizioni                                 | Versione      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Generale  | Strada | Sì       |                                               |Tutte le versioni |
| Generale  | DataCore FC   | Sì *       |È supportata con DataCore SANsymphony            | Tutte le versioni |
| Generale  | CHE IL SERVIZIO          | Sì *      |È supportata solo con volumi localmente bloccati     | Tutte le versioni |
| Generale  | L'indicizzazione      | Sì *       |Per indicare i volumi a più livelli, è supportata solo metadati indicizzazione (nessun dato).<br>Per indicare i volumi localmente bloccati, l'indicizzazione completa è supportato.| Tutte le versioni |
| Generale  | Antivirus    | Sì *       |Per indicare i volumi a più livelli, è supportata solo la scansione Apri e Chiudi.<br> Per indicare i volumi localmente bloccati, analisi completa sono supportato.| Tutte le versioni |

*Sì & #42; -Applicare restrizioni e linee guida soluzione.*

## <a name="storsimple-terminology"></a>Terminologia StorSimple 

Prima di distribuire la soluzione Microsoft Azure StorSimple, si consiglia di esaminare i seguenti termini e le definizioni.

### <a name="key-terms-and-definitions"></a>Termini e definizioni fondamentali

| Termine (acronimo o abbreviazione) | Descrizione |
| ------------------------------ | ---------------- |
| record di controllo di accesso (ACR)    | Un record associato a un volume in un dispositivo Microsoft Azure StorSimple che determina quali host è possibile connettersi a essa. La determinazione si basa su iSCSI completo nome iSCSI degli host (all'interno di ACR) che si connette a un dispositivo StorSimple.|
| AES 256                        | Algoritmo di crittografia AES (Advanced Standard) 256 bit per crittografare i dati durante lo spostamento da e verso il cloud. |
| dimensione dell'unità di assegnazione (Australia)     | La quantità minima di spazio su disco da allocare per contenere un file presenti nel file System. Se un formato di file non è un multiplo della dimensione del cluster, è necessario utilizzare spazio aggiuntivo per la memorizzazione di file (per eccesso al multiplo successivo della dimensione del cluster) risultante nello spazio perso e frammentazione del disco rigido. <br>Australia consigliato per indicare i volumi StorSimple Azure è 64 KB poiché funziona bene con algoritmi deduplication.|
| collegamento a livelli di spazio di archiviazione automatica      | Spostare automaticamente i dati meno attivi da SSDs dischi rigidi e quindi a un livello nel cloud e quindi per consentire la gestione di tutto lo spazio di archiviazione da un'interfaccia utente centrale.|
| catalogo di backup | Insieme di backup, dal tipo di applicazione che è stato utilizzato in genere correlate. Questa raccolta viene visualizzata nella pagina catalogo di Backup del servizio di gestione StorSimple dell'interfaccia utente.|
| file di backup catalogo             | Un file contenente un elenco di snapshot disponibili attualmente archiviati nel database di backup di StorSimple Snapshot Manager. |
| criteri di backup                   | Selezione di volumi, tipo di backup e il calendario che consente di creare backup in un programma predefinito.|
| oggetti binari di grandi dimensioni (BLOB)    | Raccolta di dati binari memorizzati come una singola entità in un sistema di gestione di database. BLOB sono in genere le immagini, audio o altri oggetti multimediali, anche se il codice eseguibile binario in alcuni casi è archiviato come BLOB.|
| Richiesta di verifica Handshake Authentication Protocol (CHAP) | Protocollo utilizzato per eseguire l'autenticazione peer di una connessione, in base a peer condivisione di una password o segreto. CHAP può essere unidirezionale o reciproca. Con questo protocollo unidirezionale, la destinazione autentica iniziatore. CHAP reciproco richiede che la destinazione autenticazione dell'iniziatore e che l'iniziatore autentica la destinazione. | 
| duplicato                          | Una copia di un volume. |
|Cloud come livello (CaaT)          | Il cloud archiviazione integrata come livello all'interno dell'architettura di archiviazione in modo che viene visualizzato tutto lo spazio di archiviazione da parte di rete di archiviazione di un'organizzazione.|
| provider di servizi cloud (CSP)   | Provider del cloud computing services.|
| snapshot cloud                 | Copia in un momento del volume i dati archiviati nel cloud. Uno snapshot cloud corrisponde a uno snapshot replicato in un sistema di archiviazione diverso, fuori sede. Snapshot cloud sono particolarmente utili di emergenza.|
| chiave di crittografia di spazio di archiviazione cloud   | Una password o con un tasto utilizzato dal dispositivo StorSimple per accedere ai dati crittografati inviati dal dispositivo in uso nel cloud.|
| aggiornamento compatibile         | Gestione degli aggiornamenti software su server in un cluster di failover in modo che gli aggiornamenti abbiano minimo o alcun effetto sulla disponibilità del servizio.|
| DataPath                       | Insieme di unità funzionali che eseguono operazioni di trasformazione dati collegati tra loro.|
| disattivare                     | Azione permanente che si interrompe la connessione tra il dispositivo StorSimple e il servizio cloud associato. Snapshot cloud del dispositivo rimangono dopo il processo e può essere duplicati o usato per il ripristino di emergenza.|
| disco che rispecchiano                 | Replica di volumi logici sul disco rigido separato unità in tempo reale per assicurare la disponibilità continua.|
| che rispecchiano disco dinamico         | Replica di volumi logici su dischi dinamici.|
| dischi dinamici                  | Formato di volume del disco che utilizza il disco Manager LOGICI per archiviare e gestire i dati tra più dischi fisici. Dischi dinamici possono ingranditi per fornire più spazio.|
| Simbolo di "Extended" insieme di dischi (EBOD) | Simbolo secondario del dispositivo Microsoft Azure StorSimple contenente dischi unità disco rigido aggiuntiva per l'ulteriore spazio di archiviazione.|
| provisioning FAT               | Un'archiviazione convenzionale il provisioning in cui lo spazio di archiviazione allocato in base a previsto alle esigenze ed è in genere oltre la necessità di corrente. Vedere anche *il provisioning sottile*.|
| unità disco rigido (sul disco rigido)          | Unità che utilizza flessibili rotazione per archiviare i dati.|
| spazio di archiviazione cloud ibrida           | Architettura di archiviazione che utilizza le risorse locali e fuori sede, tra cui lo spazio di archiviazione cloud.|
| Internet Small Computer System Interface (iSCSI) | Uno standard di rete archiviazione basato su protocollo IP per il collegamento delle apparecchiature di spazio di archiviazione di dati o strutture.|
| iniziatore                 | Componente di software che consente a un computer host che esegue Windows per connettersi a una rete esterna di archiviazione basato su iSCSI.|
| iSCSI nome completo (IQN)      | Un nome univoco che identifica una destinazione iSCSI o iniziatore.|
| destinazione iSCSI                    | Componente di software che fornisce iSCSI centralizzata sottosistema disco SAN.|
| Live archiviazione                  | Un approccio di spazio di archiviazione in cui archiviazione dati sono accessibili sempre questa impostazione (non viene memorizzato fuori sede su nastro, ad esempio). Microsoft Azure StorSimple utilizza archiviazione live.|
|volume locale bloccata | volume che si trova nel dispositivo e mai a più livelli nel cloud. |
| snapshot locale                  | Copia in un momento del volume i dati archiviati nel dispositivo Microsoft Azure StorSimple.|
| StorSimple di Microsoft Azure      | Una soluzione efficace costituita da un dispositivo di archiviazione Data Center e software che consente alle organizzazioni di IT per utilizzare al meglio lo spazio di archiviazione cloud come se fosse lo spazio di archiviazione di Data Center. StorSimple semplifica la protezione dei dati e la gestione di dati e ridurre i costi. La soluzione consolida principale dello spazio di archiviazione, archiviazione, backup e il ripristino (DR) tramite l'integrazione perfetta con il cloud. Grazie alla combinazione gestione di dati di spazio di archiviazione e cloud SAN su una piattaforma aziendale, dispositivi StorSimple abilitare velocità, semplicità e affidabilità per tutte le esigenze di spazio di archiviazione.|
| Alimentazione e raffreddamento modulo (PCM)  | Componenti hardware del dispositivo StorSimple composto di alimentazione elettrica e ventilatori, pertanto il nome Power e raffreddamento modulo. Simbolo principale del dispositivo ha due 764W PCMs mentre il simbolo EBOD è due 580W PCMs.|
| simbolo principale               | Simbolo principale del dispositivo StorSimple che contiene i controller di piattaforma dell'applicazione.|
| obiettivo di tempo di ripristino (RTO)   | La maggior quantità di tempo che deve essere comprensivo di un processo aziendale o un sistema viene ripristinata completamente dopo un'emergenza.| 
|seriale collegato SCSI (SA)       | Tipo di unità disco rigido (sul disco rigido).|
| chiave di crittografia di servizio dati     | Chiave resa disponibile per i nuovi prodotti StorSimple che registra con il servizio di gestione StorSimple. I dati di configurazione trasferiti tra il servizio di gestione StorSimple e il dispositivo sono crittografati tramite una chiave pubblica e quindi possano decrittografare solo sul dispositivo utilizzando una chiave privata. Chiave di crittografia di servizio dati consente al servizio ottenere le chiavi private per la decrittografia.|
| chiave di registrazione del servizio        | Una chiave che consente di registrare il dispositivo StorSimple con il servizio di gestione di StorSimple in modo che venga visualizzato nel portale di classica Azure per ulteriori operazioni di gestione.|
| Small Computer System Interface (SCSI) | Una serie di standard per fisica la connessione di computer e passare dati tra di esse.|
| stato a tinta unita unità SSD         | Un disco che non contiene spostamento parti; ad esempio un'unità flash.|
| account di archiviazione                 | Un set di credenziali di accesso collegato al proprio account di archiviazione per un provider di servizi cloud specificato.| 
| Scheda StorSimple per SharePoint| Componente di Microsoft Azure StorSimple che trasparente estende StorSimple lo spazio di archiviazione e protezione dei dati in SharePoint server farm.|
| Servizio di gestione StorSimple      | Estensione del portale di classica Azure che consente di gestire i dispositivi virtuali e Azure StorSimple locale.|
| Gestione di Snapshot StorSimple     | Microsoft Management Console (MMC) snap-in per gestione di backup e ripristinare le operazioni di Microsoft Azure StorSimple.|
| eseguire backup                     | Funzionalità che consente all'utente di eseguire un backup interattivo di un volume. È un modo alternativo prendere un backup manuale di un volume anziché eseguire un backup automatico tramite un criterio definito.|
| provisioning sottile               | Metodo di ottimizzazione dell'efficienza con cui lo spazio di archiviazione disponibile viene utilizzato in sistemi di archiviazione. Il provisioning sottile, lo spazio di archiviazione allocato tra più utenti in base allo spazio minimo necessario per ogni utente in un determinato momento. Vedere anche *fat il provisioning*.|
| collegamento a livelli | Disposizione dei dati in raggruppamenti logici sulla base di utilizzo corrente, l'età e relazione ad altri dati. StorSimple dispone automaticamente i dati in livelli.   |
| volume                          | Aree di archiviazione logica presentate in forma di unità. StorSimple volumi corrispondono ai volumi installati dall'host, compresi quelli individuati mediante l'utilizzo di iSCSI e un dispositivo StorSimple.|
 | contenitore volume                | Raggruppamento di volumi e le impostazioni applicate a tali. Tutti i volumi del dispositivo StorSimple sono raggruppati in contenitori volume. Le impostazioni del contenitore volume includono gli account di archiviazione, le impostazioni di crittografia per i dati inviati nel cloud con chiavi di crittografia associato e larghezza di banda utilizzata per le operazioni relative nel cloud.|
| gruppo volume                    | In Gestione StorSimple Snapshot un gruppo di volume è un insieme di volumi configurati per semplificare l'elaborazione di backup.|
| Volume ombreggiatura copia servizio| Servizio di sistema operativo Windows Server che facilita la coerenza applicazione per la comunicazione con le applicazioni compatibili con VSS per coordinare la creazione di istantanee incrementale. VSS garantisce che le applicazioni siano temporaneamente inattive quando vengono registrate istantanee.|
| Windows PowerShell per StorSimple | Basato su Windows PowerShell interfaccia della riga utilizzata per eseguire e gestire il dispositivo StorSimple. Mantenendo alcune delle funzionalità di base di Windows PowerShell, l'interfaccia dispone aggiuntive cmdlet dedicato rivolto la gestione di un dispositivo StorSimple.|


## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [sicurezza StorSimple](storsimple-security.md).




 

 
