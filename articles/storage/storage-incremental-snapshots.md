<properties
    pageTitle="Utilizzare snapshot incrementali per eseguire il backup e ripristino di Azure macchine virtuali | Microsoft Azure"
    description="Creare una soluzione personalizzata per eseguire il backup e ripristino dei dischi Azure macchina virtuale tramite snapshot incrementale."
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
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Eseguire il backup dischi Azure macchina virtuale con snapshot incrementale

## <a name="overview"></a>Panoramica

Spazio di archiviazione Azure offre la possibilità di istantanee di BLOB. Snapshot acquisiscono lo stato di blob quel momento. In questo articolo illustrate di uno scenario di come è possibile gestire i backup dei dischi macchina virtuale utilizzando snapshot. È possibile utilizzare questa metodologia quando si sceglie di non utilizzare Azure Backup e ripristino servizio e si desidera creare una strategia di backup personalizzata per i dischi macchina virtuale.

Azure macchina virtuale dischi vengono archiviati come BLOB di pagine in archiviazione Azure. Poiché si parla strategia di backup per dischi macchina virtuale in questo articolo, si verrà riferimento a snapshot nel contesto di BLOB di pagine. Per ulteriori informazioni sugli snapshot, fare riferimento alla [creazione di un'istantanea del Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>Che cos'è uno snapshot?

Uno snapshot blob è una versione di sola lettura di un blob archiviate in un punto nel tempo. Dopo aver creato uno snapshot, è è possibile leggere, copiata, o eliminato ma non modificarli. Snapshot consentono di eseguire il backup blob come appare in un momento. Fino a resto versione 04/2015/05 era la possibilità di copiare snapshot completo. Con il resto versione 2015-07-08 e versioni successive, è anche possibile copiare istantanee incrementale.

## <a name="full-snapshot-copy"></a>Copia istantanea completo

Snapshot possono essere copiati a un altro account di archiviazione come blob per mantenere copie di backup di base blob. È anche possibile copiare uno snapshot sopra il blob di base, è ad esempio il ripristino del blob a una versione precedente. Quando uno snapshot viene copiato da un account di archiviazione a un'altra, occupano lo stesso spazio blob della pagina di base. Pertanto, copia intero snapshot da un account di archiviazione a un'altra sarà lento e utilizzerà anche molto spazio nell'account di archiviazione di destinazione.

>[AZURE.NOTE] Se si copia il blob di base a un'altra destinazione, snapshot del blob non vengono copiati insieme a essa. Analogamente, se si sovrascrive un blob di base con una copia, snapshot associato blob base non sono interessati e non subiranno alcuna modifica sotto il nome di base blob.

### <a name="back-up-disks-using-snapshots"></a>Eseguire il backup dischi tramite snapshot

Come una strategia di backup per i dischi macchina virtuale, è possibile istantanee periodiche del BLOB della pagina o su disco e copiarli in un altro account di archiviazione con strumenti come operazione [Copia Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o [AzCopy](storage-use-azcopy.md). È possibile copiare uno snapshot in un blob di pagine di destinazione con un nome diverso. Il blob di pagina di destinazione risultante è un blob di pagine modificabili e non uno snapshot. Più avanti in questo articolo verranno descritti i passaggi per effettuare il backup di dischi macchina virtuale utilizzando snapshot.

### <a name="restore-disks-using-snapshots"></a>Ripristinare dischi utilizzando istantanee

Quando è necessario ripristinare il disco a una versione precedente di stabile archiviata in uno degli snapshot di backup, è possibile copiare uno snapshot su blob della pagina di base. Dopo lo snapshot è alzati di livello alla pagina di base blob rimane snapshot ma origine viene sovrascritta con una copia che può essere sia di lettura e scrittura. Più avanti in questo articolo verranno descritti i passaggi per ripristinare una versione precedente del disco dello snapshot corrispondente.

### <a name="implementing-full-snapshot-copy"></a>Implementazione copia istantanea completo

È possibile implementare una copia completa eseguendo le operazioni seguenti

-   Prima di tutto, creare uno snapshot del blob base utilizzando l'operazione [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   Copiare lo snapshot a un account di archiviazione di destinazione utilizzando [Copia Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Ripetere questa procedura per mantenere copie di backup il BLOB di base.

## <a name="incremental-snapshot-copy"></a>Copia istantanea incrementale

La nuova caratteristica [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API consente di eseguire il backup snapshot del BLOB di pagine o dischi migliore. L'API restituisce l'elenco delle modifiche tra blob base e le istantanee. Consente di ridurre la quantità di spazio di archiviazione utilizzato per l'account di backup. L'API supporta BLOB di pagine in Premium lo spazio di archiviazione, nonché lo spazio di archiviazione Standard. Usa questa API, è possibile creare soluzioni di backup più veloce ed efficiente per macchine virtuali di Azure. Questo sarà disponibile con la versione di resto 2015-07-08 e versioni successive.

Incrementale Snapshot copia consente di copiare da un account di archiviazione a un'altra la differenza tra,

-   Blob di base e il relativo Snapshot o
-   Le due istantanee blob base

Condizione che vengano soddisfatte le condizioni seguenti,

- Il blob è stato creato in gen-1-2016 o versioni successive.
- Il blob non sovrascritta con [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) o [Blob copia](https://msdn.microsoft.com/library/azure/dd894037.aspx) tra i due snapshot.


**Nota**: questa caratteristica è disponibile per Premium e BLOB pagina Standard.

Quando si dispone di una strategia di backup personalizzata che utilizza istantanee, copiare le istantanee da un account di archiviazione può essere molto lento e richiede una maggiore quantità di spazio di archiviazione. Anziché la copia l'intero snapshot in un account di archiviazione di backup, è possibile scrivere la differenza tra errori consecutivi snapshot a un blob di pagine di backup. In questo modo, l'ora di copia e lo spazio di archiviazione dei backup sostanzialmente ridotti.

### <a name="implementing-incremental-snapshot-copy"></a>Implementazione delle istantanea incrementale copia

È possibile implementare copia istantanea incrementale eseguendo le operazioni seguenti

-   Creare uno snapshot del blob base utilizzando [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copiare lo snapshot all'account di archiviazione di backup di destinazione utilizzando [Copia Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx). Questo sarà blob pagina di backup. Creare uno snapshot di questo blob di pagine di backup e memorizzare nell'account di backup.
-   Creare un altro snapshot del blob base utilizzando Snapshot Blob.
-   È possibile ottenere la differenza tra prima e la seconda snapshot di base blob tramite [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Consente di specificare il valore DateTime dello snapshot di che cui si desidera visualizzare la differenza con nuovo parametro **prevsnapshot** . Quando questo parametro è presenta, la risposta resto includerà solo le pagine sono state modificate tra snapshot di destinazione e precedente incluse pagine cancellare.
-   Utilizzare [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) per applicare le modifiche a blob pagina di backup.
-   Infine, creare uno snapshot del BLOB della pagina di backup e archiviarlo nella finestra account di archiviazione di backup.

Nella sezione successiva verranno illustrate in modo più dettagliato come è possibile gestire i backup dei dischi utilizzando Copia istantanea incrementale

## <a name="scenario"></a>Scenario:

In questa sezione illustrate di uno scenario che include una strategia di backup personalizzata per dischi macchina virtuale utilizzando snapshot.

Valutare la possibilità di una macchina virtuale Azure serie DS con un disco P30 di spazio di archiviazione premium collegato. Il disco P30 chiamato *mypremiumdisk* verrà archiviato in un account di archiviazione premium denominato *mypremiumaccount*. Un account di archiviazione standard denominato *mybackupstdaccount* verrà utilizzato per l'archiviazione di backup del *mypremiumdisk*. Si desidera mantenere uno snapshot della *mypremiumdisk* ogni 12 ore.

Per ulteriori informazioni sulla creazione di account di archiviazione e dischi, consultare [gli account di archiviazione su Azure](storage-create-storage-account.md).

Per informazioni sui backup delle macchine virtuali di Azure, fare riferimento a [pianificare macchine Virtuali di Azure backup](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Procedura per mantenere backup di un disco utilizzando snapshot incrementale

La procedura descritta di seguito venga istantanee di *mypremiumdisk* e gestire i backup *mybackupstdaccount*. Il backup sarà un blob pagina standard denominato *mybackupstdpageblob*. Blob pagina backup rifletteranno sempre lo stesso stato dell'ultimo snapshot di *mypremiumdisk*.

1.  Creare innanzitutto il blob di pagina di backup per il disco di archiviazione premium. A tale scopo, creare uno snapshot della *mypremiumdisk* chiamato *mypremiumdisk_ss1*.
2.  Copiare questa snapshot mybackupstdaccount come blob pagina denominato *mybackupstdpageblob*.
3.  Creare uno snapshot della *mybackupstdpageblob* chiamato *mybackupstdpageblob_ss1*, utilizzando [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) e archiviarlo in *mybackupstdaccount*.
4.  Durante l'intervallo di backup, creare un altro snapshot del *mypremiumdisk*, pronunciare *mypremiumdisk_ss2*e archiviarlo in *mypremiumaccount*.
5.  È possibile ottenere le modifiche incrementali tra due istantanee, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, con parametro **prevsnapshot** impostato a quello del *mypremiumdisk_ss1* [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) su *mypremiumdisk_ss2* . Scrivere il blob pagina backup *mybackupstdpageblob* in *mybackupstdaccount*queste modifiche incrementali. Se sono presenti intervalli eliminati in modifiche incrementali, deve essere deselezionate del BLOB di pagina di backup. Utilizzare [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) per scrivere le modifiche incrementali blob pagina di backup.
6.  Creare uno snapshot della pagina di backup blob *mybackupstdpageblob*, denominata *mybackupstdpageblob_ss2*. Eliminare precedente snapshot *mypremiumdisk_ss1* dall'account di archiviazione premium.
7.  Ripetere i passaggi da 4 a 6 tutte le finestre di backup. In questo modo, è possibile mantenere backup di *mypremiumdisk* in un account di archiviazione standard.

![Eseguire il backup del disco tramite snapshot incrementale](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Procedura per ripristinare un disco da istantanee

La procedura descritta di seguito consente di ripristinare disco premium, *mypremiumdisk* di uno snapshot precedente dal account di archiviazione di backup *mybackupstdaccount*.

1.  Identificare il punto in volta che si desidera ripristinare il disco premium. Supponiamo che è snapshot *mybackupstdpageblob_ss2*, che viene archiviato nel account di archiviazione di backup *mybackupstdaccount*.
2.  In mybackupstdaccount, alzare di livello la snapshot *mybackupstdpageblob_ss2* come nuovo di blob backup della pagina di base *mybackupstdpageblobrestored*.
3.  Creare uno snapshot di questo blob di pagine di backup ripristinato, denominato *mybackupstdpageblobrestored_ss1*.
4.  Copiare il blob pagina ripristinato *mybackupstdpageblobrestored* *mybackupstdaccount* a *mypremiumaccount* come nuovo di disco premium *mypremiumdiskrestored*.
5.  Creare uno snapshot della *mypremiumdiskrestored*, chiamato *mypremiumdiskrestored_ss1* per eseguire un backup incrementale futuri.
6.  Punto DS serie macchine Virtuali di ripristinato su disco *mypremiumdiskrestored* e disconnettere il vecchio *mypremiumdisk* da macchina virtuale.
7.  Avviare il processo di Backup descritto nella sezione precedente per il disco ripristinato *mypremiumdiskrestored*, utilizzando *mybackupstdpageblobrestored* come blob pagina di backup.

![Ripristinare un disco da istantanee](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla creazione di snapshot di un blob e pianificazione dell'infrastruttura di backup macchine Virtuali mediante i collegamenti seguenti.

- [Creazione di un'istantanea del Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Pianificare l'infrastruttura di Backup macchine Virtuali](../backup/backup-azure-vms-introduction.md)
