<properties
    pageTitle="Spostare dati da e verso lo spazio di archiviazione Azure | Microsoft Azure"
    description="In questo articolo viene fornita una panoramica dei diversi metodi per spostare i dati da e verso lo spazio di archiviazione di Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Spostare dati da e verso lo spazio di archiviazione di Azure

Se si desidera spostare i dati in locale allo spazio di archiviazione di Azure (o viceversa), esistono diversi modi per eseguire questa operazione. L'approccio più adatto alle proprie dipenderà dal proprio scenario. In questo articolo fornisce una panoramica dei diversi scenari e offerte appropriate per ognuna di esse.

## <a name="building-applications"></a>Creazione di applicazioni

Se si crea un'applicazione, lo sviluppo rispetto all'API REST o uno dei nostro molte librerie client è un'ottima soluzione per spostare i dati da e verso lo spazio di archiviazione di Azure.

Spazio di archiviazione Azure fornisce raccolte rich client per .NET, iOS, Java, Android, universale Windows piattaforma (UWP), Xamarin, C++, Node, PHP, trascrizione e Python. Le raccolte di client offrono funzionalità avanzate, ad esempio ritentare, registrazione e i caricamenti in parallelo. È anche possibile sviluppare direttamente rispetto all'API REST, che possono essere chiamati da qualsiasi linguaggio che effettua richieste di HTTP/HTTPS.

Vedere [Guida introduttiva a archiviazione Blob Azure](storage-dotnet-how-to-use-blobs.md) per altre informazioni.

È inoltre offrono la [Raccolta di spostamento dei dati di Azure lo spazio di archiviazione](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) che è una raccolta progettata per prestazioni elevate copia di dati di Azure. Fare riferimento per la raccolta di dati movimento [documentazione](https://github.com/Azure/azure-storage-net-data-movement) per altre informazioni. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rapidamente la visualizzazione/interazione con i dati

Se si desidera un modo semplice per visualizzare i dati di archiviazione Azure mantenendo anche la possibilità di caricare e scaricare i dati, è consigliabile utilizzare un elenco di cartelle di archiviazione di Azure.

Consultare l'elenco delle [Finestre di esplorazione di Azure lo spazio di archiviazione](storage-explorers.md) per altre informazioni.

## <a name="system-administration"></a>Amministrazione del sistema

Se si richiede o più pratico con un'utilità della riga di comando (ad esempio gli amministratori di sistema), ecco alcune opzioni da prendere in considerazione:

### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità della riga di comando di Windows progettata per prestazioni elevate la copia di dati da e verso lo spazio di archiviazione di Azure. È anche possibile copiare i dati all'interno di un account di archiviazione o tra gli account di archiviazione diversi.

Per altre informazioni, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) .

### <a name="azure-powershell"></a>PowerShell Azure

PowerShell Azure è un modulo che fornisce i cmdlet per la gestione dei servizi in Azure. È una shell della riga di comando basato su attività e linguaggio di script progettato per amministrazione del sistema.

Vedere [Usare PowerShell Azure con lo spazio di archiviazione di Azure](storage-powershell-guide-full.md) per altre informazioni.

### <a name="azure-cli"></a>CLI Azure

Azure CLI fornisce un set di Apri origine multipiattaforma comandi per l'utilizzo con i servizi di Azure. Azure CLI è disponibile in Windows, OSX e Linux.

Vedere [Usa CLI Azure con lo spazio di archiviazione di Azure](storage-azure-cli.md) per ottenere ulteriori informazioni.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Spostamento di grandi quantità di dati con una rete lenta

Una delle operazioni più importanti relative allo spostamento di grandi quantità di dati è il tempo di trasferimento. Se si vuole recuperare dati da/verso lo spazio di archiviazione di Azure senza preoccuparsi i costi di reti o scrivere codice, Importa/Esporta Azure è una soluzione appropriata.

Vedere [Azure Importa/Esporta](storage-import-export-service.md) per altre informazioni.

## <a name="backing-up-your-data"></a>Backup dei dati

Se occorre semplicemente il backup dei dati per lo spazio di archiviazione di Azure, Azure Backup è possibile passare. Si tratta di una soluzione potente per il backup dei dati locali e macchine virtuali di Azure.

[Copia di Backup di Azure](../backup/backup-introduction-to-azure-backup.md) per altre informazioni, vedere.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Accesso ai propri dati locali e dal cloud

Se è necessaria una soluzione per l'accesso ai propri dati locali e dal cloud, è necessario considerare con soluzione di archiviazione cloud ibrida di Azure, StorSimple. Questa soluzione è costituito da un dispositivo StorSimple fisico che in maniera intelligente stores frequenza dati utilizzati in SSDs, utilizzati dati su dischi rigidi e inattivo/backup/archiviazione dati in archiviazione Azure.

Vedere [StorSimple](../storsimple/storsimple-overview.md) per altre informazioni.

## <a name="recovering-your-data"></a>Ripristino dei dati

Quando si dispone di applicazioni e carichi di lavoro locale, è necessario una soluzione che consente l'azienda di continuare l'esecuzione in caso di emergenza. Il ripristino del sito Azure gestisce replica, failover e ripristino di macchine virtuali e server fisici. Replicato dati vengono archiviati in archiviazione di Azure, che consente di eliminare la necessità di un Data Center Site secondario.

[Il ripristino del sito di Azure](../site-recovery/site-recovery-overview.md) per altre informazioni, vedere.
