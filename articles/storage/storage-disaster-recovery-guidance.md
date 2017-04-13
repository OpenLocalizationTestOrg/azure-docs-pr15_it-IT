<properties
    pageTitle="Operazioni da eseguire in caso di interruzione archiviazione Azure | Microsoft Azure"
    description="Operazioni da eseguire in caso di interruzione archiviazione Azure"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Cosa fare se si verifica un'interruzione di archiviazione Azure

Microsoft, viene costantemente per assicurarsi che siano sempre disponibili i servizi. In alcuni casi forza oltre il suo impatto controllo IT in modo da causare interruzioni del servizio non pianificato in una o più aree. Per informazioni su come gestire queste poche occasioni, sono disponibili le seguenti indicazioni utili per i servizi di archiviazione Azure.

## <a name="how-to-prepare"></a>Come preparare 

È fondamentale per ogni cliente preparare il proprio piano di recupero. Il tentativo di recuperare da un servizio di archiviazione in genere prevede personale e procedure automatiche per riattivare le applicazioni in uno stato funziona. Fare riferimento alla documentazione del Azure riportata di seguito per creare il proprio piano di ripristino di emergenza:

-   [Ripristino di emergenza e disponibilità per applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Indicazioni tecniche sulla resilienza Azure](../resiliency/resiliency-technical-guidance.md)

-   [Servizio di Azure il ripristino del sito](https://azure.microsoft.com/services/site-recovery/)

-   [Azure replica di spazio di archiviazione](storage-redundancy.md)

-   [Servizio di Backup Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Come rilevare 

Il modo consigliato per determinare lo stato dei servizi Azure è per sottoscrivere il [Dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Cosa fare se si verifica un'interruzione di spazio di archiviazione

Se uno o più servizi di archiviazione sono temporaneamente non disponibili in una o più aree, sono disponibili due opzioni da prendere in considerazione. Se si desidera accesso immediato ai dati, è consigliabile opzione 2.

### <a name="option-1-wait-for-recovery"></a>Opzione 1: Attendere per il ripristino

In questo caso è richiesta alcuna azione da parte dell'utente. Stiamo lavorando attentamente per ripristinare la disponibilità di servizi Azure. È possibile monitorare lo stato dei servizi nel [Dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opzione 2: Copiare i dati da secondaria

Se si è scelto di [accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (scelta consigliata) per gli account di archiviazione, si avrà accesso in lettura ai dati dall'area di secondario. È possibile utilizzare strumenti quali [AzCopy](storage-use-azcopy.md), [PowerShell di Azure](storage-powershell-guide-full.md), [lo spostamento dei dati di Azure libreria](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) per copiare un altro account di archiviazione in un'area unimpacted dati dall'area di secondario e quindi scegliere le applicazioni per account di archiviazione per entrambi leggere e scrivere disponibilità.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Cosa aspettarsi se si verifica lo spazio di archiviazione caso di errore

Se si è scelto [lo spazio di archiviazione ridondanti geografico (GRS)](storage-redundancy.md#geo-redundant-storage) o [spazio di archiviazione ridondanti geografico di accesso in lettura (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (scelta consigliata), lo spazio di archiviazione di Azure consente di mantenere i dati permanenti in due aree (primario e secondario). In entrambe le aree dello spazio di archiviazione di Azure mantiene costantemente più repliche dei dati.

Quando un danno internazionali interessa l'area principale, si tenterà innanzitutto ripristinare il servizio in quell'area. Dipende dalla natura di emergenza e il relativo impatto, in alcuni casi rari è potremmo non essere possibile ripristinare l'area principale. A questo punto, si eseguirà geografico-caso di errore. La replica di dati dell'area tra è un processo asincrono che può implicare la un ritardo, pertanto è possibile che le modifiche che non ancora replicate all'area secondario possono essere perse. È possibile eseguire una query ["ora ultima sincronizzazione" del proprio account di archiviazione](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) per ottenere informazioni dettagliate sullo stato della replica.

Un paio di punti relativi l'esperienza di geografico failover dello spazio di archiviazione:

-   Spazio di archiviazione geografico-failover verrà attivato solo dal team di archiviazione Azure: non è richiesta alcuna azione cliente.

-   Gli endpoint servizio lo spazio di archiviazione esistente per BLOB, tabelle, code e file rimarrà invariato dopo il failover; la voce DNS sarà necessario aggiornarli per passare dall'area principale per l'area secondario.

-   Prima e durante il failover geografico non diritti di accesso al proprio account di archiviazione a causa l'impatto di emergenza ma è comunque possibile leggere da secondario se l'account di archiviazione è stata configurata come RA GRS.

-   Quando è stata completata la failover geografico e le modifiche DNS sono stati propagati, verrà ripresa l'accesso in lettura e scrittura al proprio account di archiviazione. È possibile eseguire una query ["geografico Failover ultima" del proprio account di archiviazione](https://msdn.microsoft.com/library/azure/ee460802.aspx) per ottenere maggiori dettagli.

-   Dopo il failover, l'account di archiviazione verrà completamente funzionante, ma in uno stato "peggiore", poiché è effettivamente ospitato in un'area di autonomo con i possibili senza replica geografico. Per ridurre il rischio, verrà ripristinare l'area principale originale e quindi eseguire un failback geografico per ripristinare lo stato originale. Se l'area principale originale è irreversibile, si dovrà allocare un'altra area secondaria.
Per ulteriori informazioni sull'infrastruttura di replica geografico lo spazio di archiviazione di Azure, fare riferimento all'articolo nel blog del team lo spazio di archiviazione sulle [Opzioni di ridondanza e RA GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##<a name="best-practices-for-protecting-your-data"></a>Procedure consigliate per la protezione dei dati

Esistono alcuni approcci consigliati per il backup dei dati di spazio di archiviazione a intervalli regolari.

-   Dischi macchine Virtuali: usare il [servizio di Backup di Azure](https://azure.microsoft.com/services/backup/) per eseguire il backup dischi macchine Virtuali utilizzati da macchine virtuali Azure.

-   Blocco BLOB: creare una [snapshot](https://msdn.microsoft.com/library/azure/hh488361.aspx) di ogni blob di blocchi o copia BLOB allo spazio di archiviazione un altro account in un'altra area utilizzando [AzCopy](storage-use-azcopy.md), [PowerShell Azure](storage-powershell-guide-full.md)o [una raccolta di spostamento di dati di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tabelle: consente di esportare i dati della tabella in un altro account di archiviazione in un'altra area [AzCopy](storage-use-azcopy.md) .

-   File: usare [AzCopy](storage-use-azcopy.md) o [Azure PowerShell](storage-powershell-guide-full.md) per copiare i file in un altro account di archiviazione in un'altra area.
