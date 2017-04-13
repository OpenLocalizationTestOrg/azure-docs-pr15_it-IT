<properties
    pageTitle="Eseguire la migrazione allo spazio di archiviazione di Azure Premium | Microsoft Azure"
    description="Eseguire la migrazione di macchine virtuali esistenti per lo spazio di archiviazione di Azure Premium. Spazio di archiviazione Premium offre prestazioni ad alta, bassa latenza disco supporto per I/O-che richiede significative attività carichi di lavoro in esecuzione in macchine virtuali di Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Eseguire la migrazione allo spazio di archiviazione di Azure Premium

## <a name="overview"></a>Panoramica

Lo spazio di archiviazione di Azure Premium offre supporto disco prestazioni ad alta, bassa latenza per macchine virtuali in esecuzione è/O-che richiede significative attività carichi di lavoro. È possibile sfruttare la velocità e le prestazioni di questi dischi migrazione dischi macchine Virtuali dell'applicazione per lo spazio di archiviazione di Azure Premium.

Lo scopo di questa guida è prepararsi nuovi utenti di spazio di archiviazione Premium Azure migliore effettuare una transizione graduale dal sistema corrente allo spazio di archiviazione Premium. La Guida indirizzi tre componenti principali di questa procedura: 

  - [Pianificare la migrazione allo spazio di archiviazione Premium](#plan-the-migration-to-premium-storage)
  - [Preparare l'ambiente e copiare virtuale rigido dischi rigidi allo spazio di archiviazione Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Creare macchina virtuale Azure mediante archiviazione Premium](#create-azure-virtual-machine-using-premium-storage)

È possibile eseguire la migrazione di macchine virtuali da altre piattaforme allo spazio di archiviazione di Azure Premium o eseguire la migrazione di macchine virtuali di Azure esistenti dallo spazio di archiviazione Standard allo spazio di archiviazione Premium. Questa guida illustra i passaggi per entrambi gli scenari di due. Seguire i passaggi indicati nella sezione pertinenti in base al proprio scenario.

>[AZURE.NOTE] È possibile trovare una panoramica delle funzionalità e prezzi di spazio di archiviazione Premium nello spazio di memorizzazione Premium: [High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md). È consigliabile eseguire la migrazione di qualsiasi disco macchina virtuale che richiede di alta IOPS allo spazio di archiviazione di Azure Premium per ottimizzare le prestazioni dell'applicazione. Se il disco non richiede IOPS elevato, è possibile limitare i costi per mantenere nel sistema di archiviazione Standard, in cui sono archiviati i dati sul disco macchina virtuale sulle unità disco rigido (dischi rigidi) anziché SSDs.

Completare il processo di migrazione interamente può richiedere altre azioni prima e dopo la procedura descritta in questa Guida. Ad esempio la configurazione di reti virtuali o i punti finali o apportare modifiche al codice applicazione che può richiedere alcuni tempo di inattività dell'applicazione. Queste azioni sono univoche per ogni applicazione ed è necessario completarle insieme la procedura descritta in questa guida per ottenere la transizione completa allo spazio di archiviazione Premium il più agevole possibili.


## <a name="plan-the-migration-to-premium-storage"></a>Pianificare la migrazione a Premium archiviazione

In questa sezione garantisce pronti a seguire i passaggi di migrazione in questo articolo e consente di prendere una decisione ottima sui tipi di macchine Virtuali e disco.

### <a name="prerequisites"></a>Prerequisiti di
- È necessario un abbonamento a Azure. Se non si dispone di uno, è possibile creare una sottoscrizione di mese di [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) o visitare [Azure prezzi](https://azure.microsoft.com/pricing/) per visualizzare altre opzioni.
- Per eseguire i cmdlet di PowerShell, sarà necessario il modulo di Microsoft Azure PowerShell. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per le istruzioni di installazione e punto di installazione.
- Quando si prevede di utilizzare macchine virtuali di Azure in esecuzione in archiviazione Premium, è necessario utilizzare le macchine virtuali in grado di Premium lo spazio di archiviazione. È possibile usare dischi Standard e lo spazio di archiviazione Premium con macchine virtuali in grado di spazio di archiviazione Premium. È saranno disponibili con altri tipi di macchine Virtuali dischi di archiviazione Premium in futuro. Per ulteriori informazioni su tutti i tipi di disco macchine Virtuali di Azure e i formati di disponibili, vedere [formati per i servizi Cloud](../cloud-services/cloud-services-sizes-specs.md)e [le dimensioni massime per macchine virtuali](../virtual-machines/virtual-machines-windows-sizes.md) .

### <a name="considerations"></a>Considerazioni

Una macchina virtuale Azure supporta associare più dischi Premium lo spazio di archiviazione in modo che le applicazioni possono includere fino a 64 TB di spazio di archiviazione per macchine Virtuali. Con Premium lo spazio di archiviazione, è possono utilizzare le applicazioni ottenere 80.000 IOPS (operazioni di input/output al secondo) macchine Virtuali e 2000 MB al secondo disco per macchine Virtuali con scarsa latenza per operazioni di lettura. Sono disponibili opzioni in una vasta gamma di macchine virtuali e dischi. In questa sezione è utile per trovare un'opzione che meglio si adatta il carico di lavoro.

#### <a name="vm-sizes"></a>Dimensioni di macchine Virtuali
Le specifiche di dimensioni macchine Virtuali di Azure sono elencate nella [dimensioni per macchine virtuali](../virtual-machines/virtual-machines-windows-sizes.md). Esaminare le prestazioni di macchine virtuali che utilizzano lo spazio di archiviazione Premium e scegliere le dimensioni di macchine Virtuali più appropriata che meglio si adatta il carico di lavoro. Assicurarsi che sia larghezza di banda sufficiente disponibile la macchina virtuale per indirizzare il traffico disco.


#### <a name="disk-sizes"></a>Dimensioni del disco
Esistono tre tipi di dischi che possono essere usati con le macchine Virtuali e ogni sono specifici al secondo e velocità limiti. Prendere in considerazione questi limiti quando si sceglie il tipo di disco per la macchina virtuale in base alle esigenze dell'applicazione in termini di capacità, prestazioni e scalabilità e carichi di picco.

|Tipo di disco Premium lo spazio di archiviazione|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Dimensione del disco|128 GB|512 GB|1024 GB (1 TB)|
|IOPS disco|500|2300|5000|
|Produttività per disco|100 MB al secondo|150 MB al secondo|200 MB al secondo|

A seconda del carico di lavoro, determinare se dischi dati aggiuntivi sono necessari per la macchina virtuale. È possibile collegare più dischi di dati permanenti per le macchine Virtuali. Se necessario, è possibile stripe su dischi per migliorare le prestazioni del volume e la capacità. (Vedere che cos'è striping del disco [qui](storage-premium-storage-performance.md#disk-striping).) Se si stripe Premium di archiviazione dati dischi utilizzando [Spazi di archiviazione][4], è necessario configurarlo con una colonna per ogni disco utilizzato. In caso contrario, le prestazioni complessive del volume strisce possono essere inferiore a previsto a causa di distribuzione uniforme del traffico tra i dischi. Per le macchine virtuali Linux è possibile utilizzare l'utilità *mdadm* per ottenere lo stesso. Vedere l'articolo [Configurare RAID Software su Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) per informazioni dettagliate.

#### <a name="storage-account-scalability-targets"></a>Siti di destinazione di archiviazione account scalabilità
Gli account di archiviazione Premium sono i seguenti obiettivi scalabilità oltre alle [destinazioni le prestazioni e scalabilità dello spazio di archiviazione Azure](storage-scalability-targets.md). Se i requisiti dell'applicazione superano le destinazioni scalabilità di un account di archiviazione singola, compilare l'applicazione per utilizzare più account di archiviazione e dividere i dati tra gli account di archiviazione.

|Capacità conto totale|Larghezza di banda totale per un Account di archiviazione in locale ridondanti|
|:--|:---|
|Disco capacità: 35TB<br />Snapshot capacità: TB 10|Fino a 50 Gigabit al secondo per entrata + in uscita|

Per ulteriori informazioni sulle specifiche Premium lo spazio di archiviazione, vedere [destinazioni le prestazioni quando si usa lo spazio di archiviazione Premium e scalabilità](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Disco criteri memorizzazione nella cache
Per impostazione predefinita, disco criterio di cache è *Sola lettura* per tutti i dati di Premium dischi e *Lettura e scrittura* per il disco di sistema operativo Premium collegate a macchina virtuale. Questa impostazione di configurazione consigliata per ottenere prestazioni ottimali per IOs dell'applicazione. Per dischi dati auto scrittura o in sola lettura (ad esempio un file di log di SQL Server), disattivare la cache del disco, in modo che è possibile ottenere prestazioni migliori delle applicazioni. Le impostazioni della cache per dischi dati esistenti possono essere aggiornate tramite [Portal Azure](https://portal.azure.com) o il parametro *- HostCaching* del cmdlet *Set-AzureDataDisk* .

#### <a name="location"></a>Posizione
Selezionare un percorso in cui lo spazio di archiviazione di Azure Premium è disponibile. Per informazioni aggiornate sui posizioni disponibili, vedere [Servizi di Windows Azure per area geografica](https://azure.microsoft.com/regions/#services) . Macchine virtuali nella stessa regione come account di archiviazione che archivia dischi per la macchina virtuale per ottenere prestazioni nettamente superiori rispetto se sono in aree separate.

#### <a name="other-azure-vm-configuration-settings"></a>Altre impostazioni di configurazione di macchine Virtuali di Azure
Quando si crea una macchina virtuale Azure, verrà richiesto di configurare alcune impostazioni macchina virtuale. Tenere presente che alcune impostazioni corretti per la durata del macchine Virtuali, mentre è possibile modificare o aggiungere altri utenti in un secondo momento. Esaminare le impostazioni di configurazione di macchine Virtuali di Azure e verificare che queste siano configurate correttamente in modo che corrispondano ai propri requisiti di carico di lavoro.

### <a name="optimization"></a>Ottimizzazione

[Lo spazio di archiviazione di azure Premium: struttura per garantire prestazioni elevate](storage-premium-storage-performance.md) vengono fornite indicazioni per la creazione di applicazioni le massime prestazioni utilizzando lo spazio di archiviazione di Azure Premium. È possibile seguire le istruzioni combinate con prestazioni ottimali aggiuntivi applicabili ad tecnologie utilizzate dall'applicazione in uso.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparare l'ambiente e copiare virtuali rigido dischi rigidi allo spazio di archiviazione Premium

La sezione seguente vengono fornite indicazioni per la preparazione dischi rigidi virtuali dalle macchine Virtuali e copiare dischi rigidi virtuali archiviazione Azure.

- [Scenario 1: "sono la migrazione macchine virtuali di Azure esistenti allo spazio di archiviazione di Azure Premium."](#scenario1)
- [Scenario 2: "sono migrazione macchine virtuali da altre piattaforme allo spazio di archiviazione di Azure Premium".](#scenario2)

### <a name="prerequisites"></a>Prerequisiti di

Per preparare i dischi rigidi virtuali per la migrazione, è necessario:

- Una sottoscrizione di Azure, un account di archiviazione e un contenitore di account di archiviazione a cui è possibile copiare il disco rigido virtuale. Si noti che l'account di archiviazione di destinazione può essere un account Standard o lo spazio di archiviazione Premium a seconda delle esigenze.
- Strumento per generalizzare il disco rigido virtuale, se si intende il nome di più istanze di macchine Virtuali. Ad esempio sysprep per Windows o virt sysprep per Ubuntu.
- Strumento per caricare il file disco rigido virtuale per l'account di archiviazione. Vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) o utilizzare un [Esplora archivi Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Questa guida viene descritto come copiare il disco rigido virtuale tramite lo strumento AzCopy.

> [AZURE.NOTE] Se si sceglie l'opzione di copia icona del con AzCopy, per ottenere prestazioni ottimali, copiare il disco rigido virtuale eseguendo uno di questi strumenti da una macchina virtuale Azure che si trova nella stessa regione come account di archiviazione di destinazione. Se si sta copiando una macchina virtuale Azure in un'area diversa un disco rigido virtuale, le prestazioni potrebbero essere più lenta.
>
> Per copiare una grande quantità di dati della larghezza di banda limitata, si consiglia di [utilizzare il servizio di Azure Importa/Esporta per trasferire i dati a archiviazione Blob](storage-import-export-service.md); In questo modo è possibile trasferire i dati da dischi rigidi spedizione in un Data Center di Azure. È possibile utilizzare il servizio di Azure Importa/Esporta per copiare i dati a un account di archiviazione standard solo. Una volta account standard dello spazio di archiviazione dei dati, è possibile utilizzare [l'API Blob copia](https://msdn.microsoft.com/library/azure/dd894037.aspx) o AzCopy per trasferire i dati al proprio account di archiviazione premium.
>
> Si noti che Microsoft Azure supporta solo i file di dimensioni fisse disco rigido virtuale. File VHDX o dischi rigidi virtuali dinamici non sono supportati. Se si dispone di un disco rigido virtuale dinamico, è possibile convertire in dimensioni fisse utilizzando il cmdlet [Disco rigido virtuale Converti](http://technet.microsoft.com/library/hh848454.aspx) .

### <a name="scenario1"></a>Scenario 1: "sono migrazione macchine virtuali di Azure esistente allo spazio di archiviazione di Azure Premium".

Se si esegue la migrazione di macchine virtuali di Azure esistente, interrompere la macchina virtuale preparare dischi rigidi virtuali per il tipo di disco rigido virtuale desiderato e quindi copiare il disco rigido virtuale con AzCopy o PowerShell.

La macchina virtuale deve essere completamente verso il basso per eseguire la migrazione lo stato originale. Si verificherà un tempo di inattività fino a quando non viene completata la migrazione.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passaggio 1. Preparare dischi rigidi virtuali per la migrazione

Se si esegue la migrazione esistente macchine virtuali di Azure allo spazio di archiviazione Premium, il disco rigido virtuale potrebbe essere:

- Immagine di un sistema operativo GRG
- Un disco univoci del sistema operativo
- Un disco dati

Di seguito è dettagliatamente gli 3 scenari per preparare il disco rigido virtuale.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Utilizzare un disco rigido virtuale del sistema operativo GRG per creare più istanze di macchine Virtuali

Se si sta caricando un disco rigido virtuale che verrà utilizzato per creare più istanze di macchine Virtuali di Azure generiche, è innanzitutto necessario generalizzare disco rigido virtuale con un'utilità sysprep. Si applica a un disco rigido virtuale che è locale o nel cloud. Tutte le informazioni specifiche del computer rimossi dal disco rigido virtuale.

>[AZURE.IMPORTANT] Creare uno snapshot o eseguirne il backup di una macchina virtuale prima della generalizzazione essa. Esecuzione sysprep interrompe e rilasciare l'istanza di macchine Virtuali. Seguire i passaggi sotto sysprep un disco rigido virtuale del sistema operativo Windows. Si noti che esegue il comando Sysprep sarà necessario arrestare la macchina virtuale. Per ulteriori informazioni su Sysprep, vedere [Panoramica di Sysprep](http://technet.microsoft.com/library/hh825209.aspx) o [Documentazione tecnica su Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Aprire una finestra del prompt dei comandi come amministratore.
2. Immettere il comando seguente per aprire Sysprep:

        %windir%\system32\sysprep\sysprep.exe

3. Nello strumento di preparazione del sistema, selezionare sistema immettere pronte della configurazione guidata, selezionare la casella di controllo Generalize, selezionare **arresto**e quindi fare clic su **OK**, come illustrato nell'immagine seguente. Sysprep verrà generalizzare il sistema operativo e arrestare il sistema.

    ![][1]

Per un VM Ubuntu, utilizzare virt sysprep per ottenere lo stesso. Vedere [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) per altri dettagli. Vedere anche alcune delle Apri origine [Linux Server di Provisioning software](http://www.cyberciti.biz/tips/server-provisioning-software.html) per altri sistemi operativi Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilizzare un disco rigido virtuale del sistema operativo univoci per creare una singola istanza di macchine Virtuali

Se si dispone di un'applicazione eseguita in macchine Virtuali che richiede dati specifici del computer, non generalizzare il disco rigido virtuale. Un disco rigido virtuale non generalized possa essere usato per creare un'istanza di macchine Virtuali di Azure univoca. Ad esempio, se è installato il disco rigido virtuale Controller di dominio, eseguire sysprep renderà inefficace come Controller di dominio. Esaminare le applicazioni eseguono le macchine Virtuali e l'impatto di esecuzione di sysprep su di essi prima della generalizzazione il disco rigido virtuale.

##### <a name="register-data-disk-vhd"></a>Registrare dati disco disco rigido virtuale

Se si dispone di dischi di dati di Azure eseguire la migrazione, è necessario assicurarsi macchine virtuali che utilizzano dischi questi dati vengono chiuse verso il basso.

Seguire la procedura descritta di seguito per copiare disco rigido virtuale per lo spazio di archiviazione di Azure Premium e registrare come un disco dati provisioning.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passaggio 2. Creare la destinazione per il disco rigido virtuale

Creare un account di archiviazione per la gestione del dischi rigidi virtuali. Quando si pianifica la posizione in cui memorizzare i dischi rigidi virtuali, tenere presente quanto segue:

- Account di archiviazione Premium di destinazione.
- Il percorso di account di archiviazione deve essere uguale Premium archiviazione macchine virtuali di Azure dotati verrà creato nel passaggio finale. Impossibile copiare in un nuovo account di archiviazione, o si intende utilizzare lo stesso account di archiviazione in base alle proprie esigenze.
- Copiare e salvare la chiave account di archiviazione dell'account di archiviazione di destinazione per la fase successiva.

Per dischi di dati, è possibile scegliere di mantenere alcuni dischi dati in un account di archiviazione standard (ad esempio i dischi più spazio di archiviazione), ma è consigliabile si sposta tutti i dati di carico di lavoro di produzione all'utilizzo dell'archiviazione premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Passaggio 3. Copia disco rigido virtuale con AzCopy o PowerShell

È necessario trovare la chiave contenitore account percorso e lo spazio di archiviazione per l'elaborazione di una di queste due opzioni. Contenitore percorso e lo spazio di archiviazione account nella chiave sono disponibili nel **Portale di Azure** > **lo spazio di archiviazione**. L'URL contenitore sarà ad esempio "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opzione 1: Copiare un disco rigido virtuale con AzCopy (copia asincrono)

Usa AzCopy, è possibile caricare il disco rigido virtuale facilmente tramite Internet. A seconda delle dimensioni dei dischi rigidi virtuali, questa potrebbe richiedere tempo. È necessario ricordare di verificare i limiti di ingresso/uscita archiviazione account quando si utilizza questa opzione. Per informazioni dettagliate, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](storage-scalability-targets.md) .

1. Scaricare e installare AzCopy da questa posizione: [versione più recente di AzCopy](http://aka.ms/downloadazcopy)
2. Aprire PowerShell Azure e passare alla cartella in cui è installato AzCopy.
3. Utilizzare il comando seguente per copiare il file disco rigido virtuale "Origine" "Destinazione".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Esempio:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Ecco le descrizioni dei parametri utilizzati nel comando AzCopy:

 - * */Origine: * &lt;origine&gt;:** * percorso della cartella o URL contenitore dello spazio di archiviazione che contiene il disco rigido virtuale.
 - * */SourceKey: * &lt;chiave di account di origine&gt;:** * chiave account lo spazio di archiviazione dell'account di archiviazione di origine.
 - * */Dest: * &lt;destinazione&gt;:** * URL contenitore di archiviazione per copiare il disco rigido virtuale a.
 - * */DestKey: * &lt;chiave di account destinazione&gt;:** * chiave account lo spazio di archiviazione dell'account di archiviazione di destinazione.
 - * */Modello: * &lt;nome file&gt;:** * specificare il nome del file da copiare.

Per informazioni dettagliate sull'uso dello strumento AzCopy, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opzione 2: Copiare un disco rigido virtuale con PowerShell (copia sincronizzata)

È anche possibile copiare il file del disco rigido virtuale mediante il cmdlet di PowerShell AzureStorageBlobCopy Start. Usare il comando seguente su Azure PowerShell per copiare il disco rigido virtuale. Sostituire i valori in <> con valori corrispondenti dal proprio account di archiviazione di origine e destinazione. Per utilizzare questo comando, è necessario disporre di un contenitore denominato dischi rigidi virtuali nell'account di archiviazione di destinazione. Se non è presente il contenitore, crearne uno prima di eseguire il comando.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Esempio:

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Scenario 2: "sono migrazione macchine virtuali da altre piattaforme allo spazio di archiviazione di Azure Premium."

Se si esegue la migrazione disco rigido virtuale da non Azure Cloud archiviazione in Azure, è innanzitutto necessario esportare il disco rigido virtuale di una directory locale. Il percorso di origine completo della directory locale in cui è archiviato comodo disco rigido virtuale e usando quindi AzCopy di caricare lo spazio di archiviazione di Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passaggio 1. Esportare una directory locale disco rigido virtuale

##### <a name="copy-a-vhd-from-aws"></a>Copiare un disco rigido virtuale AWS

1. Se si utilizza AWS, esportare l'istanza di EC2 in un disco rigido virtuale in un intervallo di Amazon S3. Seguire i passaggi descritti nella documentazione di Amazon per l'esportazione delle istanze di Amazon EC2 installare lo strumento di interfaccia della riga di comando (CLI) Amazon EC2 ed eseguire il comando attività Crea-istanza Esporta per esportare l'istanza di EC2 in un file disco rigido virtuale. Assicurarsi di usare **disco rigido virtuale** per il disco #95; immagine & #95; Variabile di formato quando si esegue il comando **Crea-istanza-esportazione-attività** . Il file esportato disco rigido virtuale viene salvato nell'intervallo di Amazon S3 che designare durante il processo.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Scaricare il file disco rigido virtuale dall'intervallo S3. Selezionare il file del disco rigido virtuale, quindi **Azioni** > **Download**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiare un disco rigido virtuale altri cloud non Azure

Se si esegue la migrazione disco rigido virtuale da non Azure Cloud archiviazione in Azure, è innanzitutto necessario esportare il disco rigido virtuale di una directory locale. Copiare il percorso di origine completo della directory locale in cui è archiviato disco rigido virtuale.

##### <a name="copy-a-vhd-from-on-premise"></a>Copiare un disco rigido virtuale locale

Se si esegue la migrazione disco rigido virtuale da un ambiente locale, è necessario il percorso di origine completo in cui è archiviato disco rigido virtuale. Il percorso di origine potrebbe essere una condivisione di file o posizione server.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passaggio 2. Creare la destinazione per il disco rigido virtuale

Creare un account di archiviazione per la gestione del dischi rigidi virtuali. Quando si pianifica la posizione in cui memorizzare i dischi rigidi virtuali, tenere presente quanto segue:

- Account di archiviazione di destinazione potrebbero essere lo spazio di archiviazione standard o premium a seconda delle esigenze dell'applicazione.
- L'area geografica account di archiviazione deve essere uguale Premium archiviazione macchine virtuali di Azure dotati verrà creato nel passaggio finale. Impossibile copiare in un nuovo account di archiviazione, o si intende utilizzare lo stesso account di archiviazione in base alle proprie esigenze.
- Copiare e salvare la chiave account di archiviazione dell'account di archiviazione di destinazione per la fase successiva.

È consigliabile si sposta tutti i dati di carico di lavoro di produzione all'utilizzo dell'archiviazione premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passaggio 3. Caricare il disco rigido virtuale allo spazio di archiviazione Azure

Dopo aver creato il disco rigido virtuale nella directory locale, è possibile utilizzare AzCopy o AzurePowerShell per caricare file con estensione vhd allo spazio di archiviazione Azure. Entrambe le opzioni sono disponibili qui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opzione 1: Utilizzo di AzureVhd con Azure PowerShell Aggiungi per caricare file con estensione vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Un esempio <Uri> potrebbe essere **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Un esempio <FileInfo> potrebbe essere **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opzione 2: Usare AzCopy per caricare file con estensione vhd

Usa AzCopy, è possibile caricare il disco rigido virtuale facilmente tramite Internet. A seconda delle dimensioni dei dischi rigidi virtuali, questa potrebbe richiedere tempo. È necessario ricordare di verificare i limiti di ingresso/uscita archiviazione account quando si utilizza questa opzione. Per informazioni dettagliate, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](storage-scalability-targets.md) .

1. Scaricare e installare AzCopy da questa posizione: [versione più recente di AzCopy](http://aka.ms/downloadazcopy)
2. Aprire PowerShell Azure e passare alla cartella in cui è installato AzCopy.
3. Utilizzare il comando seguente per copiare il file disco rigido virtuale "Origine" "Destinazione".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Esempio:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Ecco le descrizioni dei parametri utilizzati nel comando AzCopy:

 - * */Origine: * &lt;origine&gt;:** * percorso della cartella o URL contenitore dello spazio di archiviazione che contiene il disco rigido virtuale.
 - * */SourceKey: * &lt;chiave di account di origine&gt;:** * chiave account lo spazio di archiviazione dell'account di archiviazione di origine.
 - * */Dest: * &lt;destinazione&gt;:** * URL contenitore di archiviazione per copiare il disco rigido virtuale a.
 - * */DestKey: * &lt;chiave di account destinazione&gt;:** * chiave account lo spazio di archiviazione dell'account di archiviazione di destinazione.
 - **/BlobType: pagina:** Specifica che la destinazione è un blob di pagine.
 - * */Modello: * &lt;nome file&gt;:** * specificare il nome del file da copiare.

Per informazioni dettagliate sull'uso dello strumento AzCopy, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Altre opzioni per il caricamento di un disco rigido virtuale

È inoltre possibile caricare un disco rigido virtuale al proprio account di archiviazione usando uno dei modi seguenti:

- [Copia di Azure dello spazio di archiviazione Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [BLOB durante il caricamento di archiviazione Azure Esplora risorse](https://azurestorageexplorer.codeplex.com/)
- [Riferimento all'API di resto servizio di archiviazione Importa/Esporta](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] È consigliabile utilizzare il servizio di importazione/esportazione se stimata caricare ora è più di sette giorni. È possibile utilizzare [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) per stimare la durata dall'unità di dimensioni e il trasferimento di dati.
>
> Importa/Esporta può essere utilizzato per copiare in un account di archiviazione standard. È necessario copiare dallo spazio di archiviazione standard per account di archiviazione premium utilizzando uno strumento come AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Creare macchine virtuali di Azure mediante archiviazione Premium

Dopo il disco rigido virtuale caricato o copiato nell'account di archiviazione desiderato, seguire le istruzioni disponibili in questa sezione per registrare il disco rigido virtuale come un'immagine del sistema operativo o su disco del sistema operativo in base al proprio scenario e quindi il nome di un'istanza di macchine Virtuali. Il disco di dati disco rigido virtuale può essere collegato a macchina virtuale dopo averla creata. Al termine di questa sezione viene fornito un script di migrazione di esempio. Questo semplice script non corrisponde a tutti gli scenari. Potrebbe essere necessario aggiornare lo script per la corrispondenza con il proprio scenario specifico. Per verificare se questo script al proprio scenario, vedere di seguito [Uno Script di migrazione di esempio](#a-sample-migration-script).

### <a name="checklist"></a>Elenco di controllo

1.  Attendere dischi disco rigido virtuale la copia è stata completata.
2.  Verificare che lo spazio di archiviazione Premium è disponibile nell'area a che la migrazione.
3.  Decidere la nuova serie di macchine Virtuali che si prevede di utilizzare. Deve essere un dotati di spazio di archiviazione Premium e le dimensioni devono essere a seconda del tipo di disponibilità nell'area e in base alle proprie esigenze.
4.  Decidere la dimensione macchine Virtuali esatta che verrà utilizzato. Dimensione memoria virtuale deve essere sufficiente per supportare il numero di dischi dati che si dispone. Ad esempio Se si dispone di 4 dischi di dati, la macchina virtuale deve avere 2 o più core. Inoltre, è consigliabile potenza, della memoria di elaborazione e della larghezza di banda di rete deve.
5.  Creare un account di archiviazione Premium presenti nell'area di destinazione. Questo è l'account da utilizzare per la nuova macchina virtuale.
6.  Disporre i dettagli di macchine Virtuali correnti comodo, inclusi l'elenco dei dischi e BLOB disco rigido virtuale corrispondenti.

Preparare l'applicazione per il tempo di inattività. Per eseguire una migrazione parziale, è necessario interrompere tutta l'elaborazione nel sistema corrente. Solo allora è possibile ottenere allo stato coerenza che è possibile eseguire la migrazione alla nuova piattaforma. Durata del tempo di inattività dipende dalla quantità di dati nei dischi per eseguire la migrazione.

>[AZURE.NOTE] Se si sta creando un VM Manager delle risorse Azure da un disco disco rigido virtuale speciali, fare riferimento a [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) per la distribuzione VM Manager delle risorse utilizzando un disco esistente.

### <a name="register-your-vhd"></a>Registrare il disco rigido virtuale

Per creare una macchina virtuale dal disco rigido virtuale del sistema operativo o per allegare un disco dati a una nuova macchina virtuale, è prima necessario registrare tali. Procedura sotto a seconda di uno scenario il disco rigido del virtuale.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalized disco rigido virtuale del sistema operativo per creare più istanze di macchine Virtuali di Azure

Immagine del sistema operativo GRG disco rigido virtuale è caricati in account di archiviazione, registrato come un' **Immagine di macchine Virtuali di Azure** in modo che è possibile creare uno o più istanze di macchine Virtuali da essa. Utilizzare i cmdlet di PowerShell seguenti per registrare il disco rigido virtuale come un'immagine del sistema operativo macchine Virtuali di Azure. Fornire l'URL completo contenitore in cui è stato copiato disco rigido virtuale.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copiare e salvare il nome di questa nuova immagine macchine Virtuali di Azure. Nell'esempio precedente, è *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disco rigido virtuale del sistema operativo univoci per creare una singola istanza di macchine Virtuali di Azure

Dopo il disco rigido virtuale OS univoco viene caricato in account di archiviazione, la registrazione come **Azure OS disco** in modo che è possibile creare un'istanza di macchine Virtuali da essa. Utilizzare i cmdlet di PowerShell per registrare il disco rigido virtuale come disco OS Azure. Fornire l'URL completo contenitore in cui è stato copiato disco rigido virtuale.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copiare e salvare il nome del nuovo disco OS Azure. Nell'esempio precedente, è *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dati disco disco rigido virtuale a nuove istanze di macchine Virtuali di Azure

Dopo il disco di dati disco rigido virtuale viene caricato in account di archiviazione, la registrazione come un disco di dati di Azure, in modo che per la nuova serie di dominio Active Directory, DSv2 serie o istanza macchine Virtuali di Azure serie GS l'associazione.

Utilizzare i cmdlet di PowerShell per registrare il disco rigido virtuale come un disco di dati di Azure. Fornire l'URL completo contenitore in cui è stato copiato disco rigido virtuale.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copiare e salvare il nome del nuovo disco dati Azure. Nell'esempio precedente, è *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Creare una macchina virtuale in grado di spazio di archiviazione Premium

Una volta all'immagine del sistema operativo o su disco del sistema operativo sono registrate, creare una nuova serie di dominio Active Directory, DSv2 serie o macchine Virtuali di serie GS. Si utilizzerà l'immagine del sistema operativo o il nome di disco rigido del sistema operativo che è stata registrata. Selezionare il tipo di macchine Virtuali dal livello Premium lo spazio di archiviazione. Nell'esempio seguente, si utilizza la dimensione di memoria virtuale *Standard_DS2* .

>[AZURE.NOTE] Aggiornare la dimensione del disco per assicurarsi che corrisponda alle dimensioni della capacità e requisiti relativi alle prestazioni e le dimensioni disponibile su disco Azure.

Eseguire i cmdlet di PowerShell passo dopo passo riportata di seguito per creare la nuova macchina virtuale. Prima di tutto, impostare i parametri comuni:

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Creare innanzitutto un servizio cloud in cui dovranno essere ospitati nelle macchine virtuali di nuove.

    New-AzureService -ServiceName $serviceName -Location $location

Successivamente, a seconda dello scenario, creare l'istanza di macchine Virtuali di Azure dall'immagine del sistema operativo o su disco del sistema operativo che è stata registrata.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalized disco rigido virtuale del sistema operativo per creare più istanze di macchine Virtuali di Azure

Creare uno o più nuove macchine Virtuali di Azure serie DS varianti mediante l' **Immagine del sistema operativo Azure** registrata. Specificare il nome del sistema operativo immagine nella configurazione macchine Virtuali durante la creazione di nuove macchine Virtuali, come illustrato di seguito.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disco rigido virtuale del sistema operativo univoci per creare una singola istanza di macchine Virtuali di Azure

Creare una nuova istanza macchine Virtuali di Azure serie DS utilizzando il **Disco rigido del sistema operativo Azure** registrata. Specificare il nome del sistema operativo disco nella configurazione macchina virtuale quando si creano la nuova macchina virtuale, come illustrato di seguito.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Specificare altre informazioni macchine Virtuali di Azure, ad esempio un servizio cloud, l'area geografica, account di archiviazione, set di disponibilità e criteri memorizzazione nella cache. Si noti che all'istanza macchine Virtuali deve trovarsi condivisa con sistema operativo associate o dischi di dati, in modo che l'account di servizio, l'area geografica e lo spazio di archiviazione cloud selezionato deve essere nella stessa posizione come dischi rigidi virtuali di sottostante di tali dischi.

### <a name="attach-data-disk"></a>Collegare dati disco

Infine, se è stato registrato il disco di dati dischi rigidi virtuali, collegarli a nuovo archiviazione Premium dotati di Azure macchina virtuale.

Utilizzare le seguenti cmdlet di PowerShell per associare la nuova macchina virtuale disco dati e specificare i criteri di memorizzazione nella cache. Nell'esempio seguente il memorizzazione nella cache criterio è impostato su *sola lettura*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Potrebbero esserci altre operazioni aggiuntive per supportare l'applicazione che non rientrano in questa Guida.

### <a name="checking-and-plan-backup"></a>Verifica e pianificazione di backup

Dopo la nuova macchina virtuale sia in esecuzione, di accedervi usando lo stesso id di accesso e la password è possibile eseguire la macchina virtuale originale e verificare che tutto funzioni come previsto. Tutte le impostazioni, inclusi i volumi a strisce con è presente nella nuova macchina virtuale.

L'ultimo passaggio consiste nel piano di backup e pianificazione della manutenzione per la nuova macchina virtuale in base alle esigenze dell'applicazione.

### <a name="a-sample-migration-script"></a>Uno script di migrazione di esempio

Se si dispone di più macchine virtuali per eseguire la migrazione, automazione mediante gli script di PowerShell saranno utile. Di seguito è riportato un esempio di script che consente di automatizzare la migrazione di una macchina virtuale. Nota sotto script è solo un esempio e sono disponibili alcuni ipotesi sui dischi macchine Virtuali correnti. Potrebbe essere necessario aggiornare lo script per la corrispondenza con il proprio scenario specifico.

Le ipotesi sono:

- Si sta creando macchine virtuali di Azure classica.
- Origine dati dischi e il sistema operativo origine riguardano stesso account di archiviazione e stesso contenitore. Se il sistema operativo dischi e dati non sono presenti nella stessa posizione, è possibile utilizzare AzCopy o PowerShell Azure vengano sovrascritti dischi rigidi virtuali contenitori e gli account di archiviazione. Fare riferimento al passaggio precedente: [Copia disco rigido virtuale con AzCopy o PowerShell](#copy-vhd-with-azcopy-or-powershell). Modifica questo script per soddisfare lo scenario è un'altra scelta, ma si consiglia di utilizzare AzCopy o PowerShell poiché è più facile e veloce.

Di seguito è lo script di automazione. Sostituire il testo con le informazioni e aggiornare lo script per la corrispondenza con il proprio scenario specifico.

>[AZURE.NOTE] Lo script esistente non consente di mantenere la configurazione di rete dell'origine macchine Virtuali. Sarà necessario re config le impostazioni di rete nelle macchine virtuali di migrazione.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Ottimizzazione

Può essere personalizzata alla configurazione corrente di macchine Virtuali specificamente per funzionano correttamente con dischi Standard. Ad esempio, per migliorare le prestazioni utilizzando molti dischi in un volume strisce. Ad esempio, invece di usare 4 dischi separatamente allo spazio di memorizzazione Premium, è possibile ottimizzare il costo mediante la ricezione di un singolo disco. Le ottimizzazioni, ad esempio la necessità di essere gestito nel caso per caso e richiedono istruzioni personalizzate dopo la migrazione. Si noti inoltre questo processo potrebbe non funzionare correttamente per database e applicazioni che dipendono dal layout disco definito nell'impostazione.

##### <a name="preparation"></a>Preparazione

1.  Eseguire la migrazione semplice come descritto nella sezione precedente. Verranno eseguite le ottimizzazioni nella nuova macchina virtuale dopo la migrazione.
2.  Definire le nuove dimensioni su disco necessarie per ottimizzare la configurazione.
3.  Determinare il mapping dei dischi/volumi correnti per le nuove specifiche del disco.

##### <a name="execution-steps"></a>Procedura di esecuzione

1.  Creare nuovi dischi con dimensioni destra in macchine Virtuali di spazio di archiviazione Premium.
2.  Accesso alle macchine Virtuali e copiare i dati da volume corrente per il nuovo disco che esegue il mapping a tale volume. Ripetere l'operazione per tutti i volumi correnti che devono essere associati a un nuovo disco.
3.  Successivamente, modificare le impostazioni di applicazione per passare a nuovi dischi e scollegare volumi precedenti.

Per ottimizzare l'applicazione per migliorare le prestazioni del disco, fare riferimento a [Ottimizzare le prestazioni dell'applicazione](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrazioni delle applicazioni

Database e altre applicazioni complesse possono richiedere passaggi speciali come definito dal provider dell'applicazione per la migrazione. Fare riferimento alla documentazione dell'applicazione corrispondente. Ad esempio in genere i database possono essere migrati tramite copia di backup e ripristino.

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse seguenti per scenari specifici per macchine virtuali migrazione:

- [Eseguire la migrazione di Azure macchine virtuali tra gli account di archiviazione](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Creare e caricare un disco rigido Server virtuale di Windows Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Creare e caricare un disco rigido virtuale che contiene il sistema operativo Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Eseguire la migrazione di macchine virtuali da Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Inoltre, vedere le risorse seguenti per altre informazioni sull'archiviazione di Azure e macchine virtuali di Azure:

- [Spazio di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Spazio di archiviazione Premium: Spazio di archiviazione High-Performance per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
