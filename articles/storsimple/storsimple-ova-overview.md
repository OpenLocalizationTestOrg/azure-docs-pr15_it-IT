<properties
   pageTitle="Cenni preliminari sulle matrici virtuale StorSimple | Microsoft Azure"
   description="Vengono illustrati la matrice virtuale StorSimple, una soluzione di archiviazione integrata che consente di gestire le attività di archiviazione tra un dispositivo virtuale locale e lo spazio di archiviazione cloud Microsoft Azure."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduzione alla matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

Introduzione a Microsoft Azure StorSimple virtuale matrice di, una soluzione di archiviazione integrata che consente di gestire le attività di archiviazione tra un dispositivo virtuale locale in esecuzione in una riunione hypervisor e lo spazio di archiviazione cloud Microsoft Azure. Matrice virtuale (noto anche come il StorSimple locale virtuale dispositivo) è un server di più, facilmente gestibili e conveniente file o una soluzione di server iSCSI che elimina molte i problemi e le spese associate enterprise lo spazio di archiviazione e protezione dei dati. La matrice virtuale è adatta in particolar modo per gli scenari di office (ROBO) di office remote/diramazione.

In questo argomento è incentrato sui matrice virtuale. 

- Per una panoramica delle serie di StorSimple 8000, passare a [serie 8000 StorSimple: una soluzione di cloud ibrida](storsimple-overview.md). 

- Per informazioni relative al dispositivo di serie StorSimple 5000/7000, accedere alla [Guida in linea StorSimple](http://onlinehelp.storsimple.com/).

Matrice virtuale supporta la iSCSI o protocollo (Server Message Block). Funziona con l'infrastruttura hypervisor e fornisce il collegamento a livelli al cloud, backup cloud, ripristino rapido, ripristino a livello di elemento e funzionalità di ripristino di emergenza.

Nella tabella seguente sono riepilogate le caratteristiche più importanti della matrice virtuale.

| Caratteristica | Matrice virtuale |
| ------- | ------------- |
|Requisiti per l'installazione | Utilizza l'infrastruttura di virtualizzazione (Hyper-V o VMware)|
| Disponibilità | Singolo nodo |
| Capacità totale (inclusi cloud) |Fino a 64 TB di capacità utilizzabile per dispositivo virtuale |
| Capacità locale | 390GB a 6,4 utilizzabili TB per dispositivo virtuale (la necessità di effettuare il provisioning di 500 GB a 8 TB di spazio su disco)|
| Protocolli | iSCSI o piccole e medie imprese |
| Obiettivo di tempo di ripristino (RTO) | iSCSI: meno di 2 minuti indipendentemente dalle dimensioni |
| Obiettivo punto di ripristino (rilasciato) | Backup giornalieri e backup su richiesta |
| Collegamento a livelli di spazio di archiviazione | Usa termica mapping per determinare quali dati devono essere a più livelli avanti o indietro |
| Supporto tecnico | Infrastruttura di virtualizzazione supportato dal fornitore |
| Prestazioni | Varia a seconda dell'infrastruttura sottostante |
| Dei dati | Ripristinare lo stesso dispositivo o a livello di elemento ripristino (server file) |
| Livelli di spazio di archiviazione | Archiviazione hypervisor locali e cloud |
| Condividere dimensioni |Livelli: fino a 20 TB; aggiunte in locale: fino a 2 TB |
| Dimensione del volume |Livelli: fino a 5 TB; aggiunte in locale: fino a 500 GB |
| Snapshot | Arresto anomalo coerenza |
| Ripristino a livello di elemento | Sì; gli utenti possano ripristinare da condivisioni |

## <a name="why-use-storsimple"></a>Perché usare StorSimple?

StorSimple si connette utenti e server per lo spazio di archiviazione Azure in minuti, senza alcuna modifica dell'applicazione.

Nella tabella seguente descrive alcuni dei vantaggi principali che fornisce la soluzione matrice virtuale.

| Caratteristica | Vantaggi |
|---------|---------|
| Integrazione trasparente | Matrice virtuale supporta iSCSI o il protocollo piccole e medie imprese. Lo spostamento dei dati tra livelli locali e il cloud è semplice e trasparente all'utente.|
| Costi di archiviazione ridotta | Con StorSimple, viene effettuato il provisioning archiviazione locale sufficiente per soddisfare le esigenze correnti per i dati sulle più comuni. Quando lo spazio di archiviazione esigenze Ingrandisci, StorSimple livelli freddo dati conveniente cloud lo spazio di archiviazione. I dati sono deduplicated e compressi prima dell'invio nel cloud per ridurre ulteriormente i requisiti di spazio di archiviazione e spese.|
| Gestione semplificata archiviazione | StorSimple offre la gestione centralizzata nel cloud con StorSimple Manager per gestire più dispositivi.| 
| Ripristino di emergenza migliorato e conformità | StorSimple semplifica l'esecuzione di emergenza processore ripristinando immediatamente i metadati e i dati in base alle esigenze. Questo errore indica le normali operazioni è possono continuare con un'interruzione minima.|
| Dei dati | Dati a più livelli nel cloud accessibili da altri siti ai fini della migrazione e ripristino. Si noti che è possibile ripristinare dati solo alla matrice virtuale originale. È tuttavia possibile utilizzare funzioni di ripristino di emergenza per ripristinare l'intera matrice virtuale a un'altra matrice virtuale.|

## <a name="storsimple-workload-summary"></a>StorSimple il carico di lavoro riepilogo

Un riepilogo dei carichi di lavoro StorSimple supportati è inserito sotto.

| Scenario:                | Carico di lavoro              | Supportati |  Restrizioni                                  | Versione              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| Collaborazione ROBO      | La condivisione di file          | Sì       | Vedere [limiti massimi per file server](storsimple-ova-limits.md). <br>Vedere [requisiti di sistema per le versioni supportate piccole e medie imprese](storsimple-ova-system-requirements.md).   | Tutte le versioni      |


## <a name="workflows"></a>Flussi di lavoro

La matrice virtuale StorSimple è particolarmente adatta per i flussi di lavoro seguenti:

- [Gestione di archiviazione basato sul cloud](#cloud-based-storage-management)
- [Copia di backup indipendente dalla posizione](#location-independent-backup)
- [Dati di protezione e ripristino di emergenza](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestione di archiviazione basato sul cloud

È possibile utilizzare il servizio di gestione di StorSimple in esecuzione nel portale di classica Azure per gestire i dati archiviati in più dispositivi e in più posizioni. Questa operazione è particolarmente utile in scenari di diramazione distribuito. Si noti che è necessario creare istanze separate del servizio di gestione StorSimple per gestire matrici virtuali e i dispositivi StorSimple fisici. 

![gestione di archiviazione basato sul cloud](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Copia di backup indipendente dalla posizione

Con la matrice virtuale snapshot cloud fornire una copia indipendente dalla posizione, nel momento del volume o condivisione. Snapshot cloud sono attivate per impostazione predefinita e non può essere disabilitato. Tutti i volumi e nelle condivisioni sono il backup nello stesso momento tramite un singolo criterio di backup giornaliero e che è possibile eseguire backup aggiuntive ad-hoc in ogni volta che è necessario.

### <a name="data-protection-and-disaster-recovery"></a>Dati di protezione e ripristino di emergenza

Matrice virtuale supporta i dati di protezione ed emergenza ripristino scenari seguenti:

- **Ripristino volume o condivisione** : usare il ripristino come nuovo flusso di lavoro per recuperare un volume o la condivisione. Usare questo approccio per recuperare il volume intero o la condivisione.
- **Ripristino a livello di elemento** : condivisioni consentono l'accesso semplificato a backup recenti. È possibile recuperare facilmente un singolo file da una cartella speciale .backup disponibile nel cloud. Questa funzionalità di ripristino è dall'utente ed è necessario alcun intervento amministrativo.
- **Ripristino di emergenza** : utilizzo della funzione di failover per recuperare tutti i volumi o condivisioni in una nuova matrice virtuale. Creare la nuova matrice virtuale e la registrazione con il servizio di gestione StorSimple quindi esito negativo su originale virtuale in forma di matrice. Nella nuova matrice virtuale assumerà quindi le risorse di provisioning. 

## <a name="virtual-array-components"></a>Componenti di matrice virtuale

Matrice virtuale include i componenti seguenti:

- [Matrice virtuale](#virtual-array) – un dispositivo di archiviazione cloud ibrida in base a una macchina virtuale effettuato il provisioning in un ambiente virtualizzato o hypervisor.  
- [Servizio di gestione di StorSimple](#storsimple-manager-service) : un'estensione del portale di classica Azure che consente di gestire uno o più dispositivi StorSimple da un'interfaccia web singolo che è possibile accedere da diverse aree geografiche. È possibile utilizzare il servizio di gestione di StorSimple per creare e gestire servizi, visualizzare e gestire i dispositivi e gli avvisi e gestire volumi, condivisioni e snapshot esistenti.
- [Interfaccia utente web locale](#local-web-user-interface) : un'interfaccia utente basata sul web che viene utilizzata per configurare il dispositivo in modo che è possibile connettersi alla rete locale e quindi registrare il dispositivo con il servizio di gestione StorSimple. 
- [Interfaccia riga di comando](#command-line-interface) : un'interfaccia di Windows PowerShell che è possibile utilizzare per avviare una sessione di supporto nella matrice virtuale.
Nelle sezioni seguenti descrivono ognuno di essi in modo più dettagliato e spiegano come la soluzione dispone i dati, allocazione dello spazio di archiviazione e facilita la gestione degli archivi e protezione dei dati.

### <a name="virtual-array"></a>Matrice virtuale

La matrice virtuale è una soluzione di archiviazione di nodo singolo che consente l'archiviazione principale, gestisce la comunicazione con lo spazio di archiviazione cloud e garantisce la sicurezza e la riservatezza di tutti i dati archiviati nel dispositivo.

Matrice virtuale è disponibile in un modello che è disponibile per il download. Matrice di spazio di archiviazione ha una capacità massima di 6,4 TB sul dispositivo (con un requisito di spazio di archiviazione sottostante dell'8 TB) e TB 64 inclusi cloud lo spazio di archiviazione. 

Matrice virtuale sono le caratteristiche seguenti:

- È conveniente. L'uso dell'infrastruttura di virtualizzazione esistente e può essere distribuito sull'hypervisor Hyper-V o VMware esistente.
- Si trova nel centro dati e può essere configurato come un server iSCSI o un file. 
- È integrato con il cloud.
- I backup sono archiviati nel cloud, che consente di semplificare il ripristino di emergenza e semplificare il livello di elemento ripristino (ILR). 
- È possibile applicare gli aggiornamenti a matrice virtuale nello stesso modo in cui si applicano a un dispositivo fisico.

>[AZURE.NOTE] Non è possibile espandere una matrice virtuale. È importante effettuare il provisioning di spazio di archiviazione adeguato quando si crea il dispositivo virtuale. 

### <a name="storsimple-manager-service"></a>Servizio di gestione StorSimple

Microsoft Azure StorSimple offre un'interfaccia utente basata sul web, il servizio di gestione StorSimple, che consente di gestire Data Center e cloud lo spazio di archiviazione. È possibile utilizzare il servizio di gestione di StorSimple per eseguire le operazioni seguenti:

- Gestire più matrici virtuali StorSimple da un singolo servizio. 
- Configurare e gestire le impostazioni di sicurezza per i dispositivi StorSimple. (La crittografia nel cloud è dipende da Microsoft Azure API).
- Configurare le proprietà e le credenziali dell'account di archiviazione.
- Configurare e gestire volumi o condivisioni.
- Eseguire il backup e ripristinare i dati su volumi o condivisioni.
- Monitoraggio delle prestazioni.
- Esaminare le impostazioni di sistema e identificare possibili problemi.

Utilizzare il servizio di gestione di StorSimple per eseguire l'amministrazione giornaliera della matrice virtuale.

Per ulteriori informazioni, passare a [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interfaccia utente web locale

Matrice virtuale include un'interfaccia utente basata sul web che verrà usata per la configurazione unica e la registrazione del dispositivo con il servizio di gestione StorSimple. È possibile utilizzare per arrestare e riavviare la matrice virtuale, eseguire test di diagnostica, aggiornare il software, cambiare la password di amministratore del dispositivo, visualizzare registri eventi di sistema e contattare il supporto Microsoft per inviare una richiesta di assistenza. 

Per informazioni sull'utilizzo di interfaccia utente basata sul web, passare a [usare l'interfaccia utente basata sul web per amministrare la matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfaccia riga di comando

L'interfaccia di Windows PowerShell incluso consente di avviare una sessione di supporto con supporto Microsoft in modo che consentano di individuare e risolvere i problemi che possono verificarsi in un dispositivo virtuale.

## <a name="storage-management-technologies"></a>Tecnologie di gestione di archiviazione

Oltre alle matrice virtuale e altri componenti, la soluzione StorSimple utilizza le seguenti tecnologie di software di accesso rapido ai dati importanti, ridurre il consumo di spazio di archiviazione e proteggere i dati archiviati nella matrice virtuale:

- [Collegamento a livelli di archiviazione automatica](#automatic-storage-tiering) 
- [Locale bloccate condivisioni e volumi](#locally-pinned-shares-and-volumes)
- [Deduplication e compressione di dati a più livelli o eseguire il backup nel cloud](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Backup pianificato e su richiesta](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Collegamento a livelli di archiviazione automatica

Matrice virtuale utilizza un nuovo collegamento a livelli meccanismo per la gestione dati archiviati in forma di matrice virtuale e nel cloud. Sono disponibili solo due livelli: la matrice virtuale e Azure locale cloud lo spazio di archiviazione. Matrice virtuale StorSimple dispone automaticamente i dati in livelli in base a una mappa termica, che consente di tenere traccia dell'utilizzo corrente, l'età e relazioni ad altri dati. Dati più attivi (inferiore) viene archiviato in locale, mentre meno dati attivi e inattivi automaticamente la migrazione nel cloud. (Tutti i backup archiviati nel cloud). StorSimple regolate e Riorganizza dati e modificare le assegnazioni di spazio di archiviazione come criteri di utilizzo. Ad esempio, alcune informazioni potrebbero verificarsi meno attivi nel tempo. Appena gradualmente meno attivo, è livelli indietro nel cloud. Se gli stessi dati viene riattivato, è livelli alla matrice lo spazio di archiviazione.

Dati per una condivisione a più livelli particolare o il volume è il proprio spazio livello locale. (circa 10% dello spazio di provisioning totale per la condivisione o volume). Mentre si riduce dello spazio di archiviazione sul dispositivo virtuale per la condivisione o volume, garantisce che il collegamento a livelli per una condivisione o volume non interessato dalle esigenze collegamento a livelli di altre azioni o i volumi. In questo modo un carico di lavoro molto impegnato in una condivisione o volume non è possibile forzare tutti gli altri carichi di lavoro nel cloud. 

![collegamento a livelli di archiviazione automatica](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] È possibile specificare un volume aggiunta in locale, nel qual caso i dati rimangono attivate in forma di matrice virtuale e non sono mai a più livelli nel cloud. Per ulteriori informazioni, visitare [localmente aggiunte condivisioni e volumi](#locally-pinned-shares-and-volumes).

### <a name="locally-pinned-shares-and-volumes"></a>Locale bloccate condivisioni e volumi

È possibile creare volumi aggiunti in locale e nelle condivisioni appropriate. Questa funzionalità garantisce che dati richiesti dalle applicazioni critiche rimarranno nella matrice virtuale e mai a più livelli nel cloud. Locale bloccate condivisioni e volumi hanno le caratteristiche seguenti: 

- Non sono soggetti a cloud latenza o problemi di connettività.
- Vengono comunque trarre vantaggio da StorSimple cloud backup e il ripristino le funzionalità.

È possibile ripristinare una condivisione locale bloccata o volume come a più livelli o da una condivisione a più livelli o aggiunte volume in locale. 

Per ulteriori informazioni sui volumi localmente bloccati, passare a [usare il servizio di gestione di StorSimple per gestire i volumi](storsimple-manage-volumes-u2.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplication e compressione di dati a più livelli o eseguire il backup nel cloud

StorSimple utilizza deduplication e compressione dei dati per ridurre ulteriormente i requisiti di spazio di archiviazione nel cloud. Deduplication consente di ridurre la quantità totale di dati memorizzati, eliminando la ridondanza nel set di dati archiviate. Durante la modifica delle informazioni, StorSimple consente di ignorare i dati non modificati e acquisisce solo le modifiche. Inoltre, StorSimple riduce la quantità di dati memorizzati identificando e la rimozione di informazioni duplicate. 

>[AZURE.NOTE] Dati archiviati nella matrice virtuale non deduplicated o compressi. Tutti deduplication e compressione si verifica prima che i dati vengono inviati nel cloud.

### <a name="scheduled-and-on-demand-backups"></a>Backup pianificato e su richiesta

Funzioni di protezione dei dati StorSimple consentono di creare backup su richiesta. Inoltre, una pianificazione di backup predefinita garantisce che backup quotidianamente. Backup sotto forma di snapshot incrementali, che vengono archiviati nel cloud. Snapshot, che registra solo le modifiche apportate dall'ultimo backup, può essere creato e ripristinato rapidamente. Questi snapshot possono essere importanza di emergenza poiché sostituire i sistemi di archiviazione secondaria (ad esempio backup su nastro) e consentono di ripristinare i dati per il Data Center o ai siti alternativi se necessario.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [preparare il portale di matrice virtuale](storsimple-ova-deploy1-portal-prep.md).


