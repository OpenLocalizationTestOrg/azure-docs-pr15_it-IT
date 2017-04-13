<properties
    pageTitle="Introduzione a backup Data Protection Manager Azure | Microsoft Azure"
    description="Introduzione al backup dei server Data Protection Manager mediante il servizio di Backup di Azure"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="System Center Data Protection Manager, gestione di protezione di dati, Data Protection Manager backup"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparazione del backup carichi di lavoro in Azure con Data Protection Manager

> [AZURE.SELECTOR]
- [Server di Backup Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Server di Backup Azure (classica)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (classico)](backup-azure-dpm-introduction-classic.md)


In questo articolo viene fornita un'introduzione all'utilizzo di Microsoft Azure Backup per proteggere i server System Center Data Protection Manager (DPM) e i carichi di lavoro. Da leggerlo, è possibile descrivere:

- Come funziona il backup di server Azure Data Protection Manager
- I prerequisiti per ottenere un'esperienza uniforme backup
- Errori tipici e su come gestirli
- Scenari supportati

System Center Data Protection Manager backup dei dati di file e delle applicazioni. Dati sottoposti a backup a Data Protection Manager possono essere memorizzati su nastro su disco, o il backup di Azure con Microsoft Azure Backup. Data Protection Manager interagisce con Azure Backup come indicato di seguito:

- **Data Protection Manager distribuito come macchina virtuale fisica server o locale** , ovvero se distribuzione come un server fisico o una macchina virtuale di Hyper-V in locale è possibile eseguire il backup dei dati in un archivio Azure Backup oltre al disco e nastro backup.
- **Data Protection Manager distribuito come una macchina virtuale Azure** , ovvero da System Center 2012 R2 con 3 di aggiornamento, è possibile distribuire Data Protection Manager come una macchina virtuale Azure. Se distribuzione come una macchina virtuale Azure, che è possibile eseguire il backup dei dati in Azure dischi collegato al computer virtuale Azure Data Protection Manager o è possibile trasferire lo spazio di archiviazione dati eseguendo il backup fino a un archivio di Backup di Azure.

## <a name="why-backup-your-dpm-servers"></a>Perché eseguire il backup dei server Data Protection Manager?

Vantaggi dell'utilizzo di Backup di Azure per il backup dei server Data Protection Manager includono:

- Per la distribuzione di Data Protection Manager locale, è possibile utilizzare backup Azure un'alternativa alla distribuzione a lungo termine su nastro.
- Per distribuzioni di Data Protection Manager in Azure, Azure Backup consente di scaricare lo spazio di archiviazione dal disco di Azure, che consente di scalare archiviazione dei dati nei formati precedenti in Azure eseguire il Backup e nuovi dati nel disco.

## <a name="how-does-dpm-server-backup-work"></a>Funzionamento di backup del server di Data Protection Manager
Per eseguire il backup una macchina virtuale, è necessario innanzitutto uno snapshot nel momento dei dati. Il servizio di Azure Backup avvia il processo di backup pianificata e attiva l'estensione di backup per creare uno snapshot. L'estensione backup coordinate con il servizio VSS in guest per ottenere la coerenza e richiama l'API di snapshot blob del servizio di archiviazione Azure quando viene raggiunta la coerenza. In questo modo per ottenere uno snapshot coerente dei dischi della macchina virtuale senza che sia necessario chiuderlo.

Dopo l'istantanea, i dati vengono trasferiti mediante il servizio di Azure Backup per l'archivio di backup. Il servizio si occupa di identificazione e trasferimento soltanto i blocchi che sono state modificate dall'ultimo backup aumentare l'efficienza dello spazio di archiviazione di backup e di rete. Al termine del trasferimento, lo snapshot viene rimossa e viene creato un punto di ripristino. In questo punto di ripristino può essere visualizzato nel portale di classica Azure.

>[AZURE.NOTE] Per macchine virtuali Linux, è possibile coerente solo file di backup.

## <a name="prerequisites"></a>Prerequisiti
Preparare il Backup di Azure per eseguire il backup dei dati di Data Protection Manager come indicato di seguito:

1. **Creare un archivio di Backup** , creare un archivio nella console di Azure Backup.
2. **Le credenziali dell'archivio di download** , backup Azure, caricare il certificato di gestione è stato creato per l'archivio.
3. **Installare l'agente di Backup di Azure e registrare il server** , ovvero da Azure Backup, installare l'agente in ogni server Data Protection Manager e registrare il server Data Protection Manager nell'archivio di backup.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Requisiti (e limitazioni)

- Data Protection Manager può essere eseguito come un server fisico o una macchina virtuale Hyper-V installati System Center 2012 SP1 o System Center 2012 R2. È possibile eseguire anche come una macchina virtuale Azure in esecuzione su System Center 2012 R2 con almeno Data Protection Manager 2012 R2 aggiornamento cumulativo 3 o una macchina virtuale di Windows in esecuzione in System Center 2012 R2 con almeno VMWare aggiornamento cumulativo 5.
- Se si esegue Data Protection Manager con System Center 2012 SP1, è necessario installare l'aggiornamento cumulativo 2 per System Center Data Protection Manager SP1. Questa operazione è necessaria prima di installare l'agente di Backup di Azure.
- Il server Data Protection Manager deve disporre di Windows PowerShell e .net Framework 4.5 installato.
- Data Protection Manager è possibile eseguire il backup la maggior parte dei carichi di lavoro eseguire il Backup di Azure. Per un elenco completo delle quali sono supportati vedere il Backup di Azure supporta elementi riportata di seguito.
- Dati archiviati in Azure Backup non possono essere recuperati con l'opzione "copia su nastro".
- È necessario un account Azure con la funzionalità di Backup di Azure attivata. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Informazioni sui [Backup di Azure prezzi](https://azure.microsoft.com/pricing/details/backup/).
- Utilizzo del Backup Azure richiede l'agente di Backup Azure installato nei server che si desidera eseguire il backup. Ogni server deve avere almeno 10% delle dimensioni dei dati che viene eseguito il backup, disponibile come spazio di archiviazione gratuito locale. Ad esempio, il backup dei 100 GB di dati richiede un minimo di 10 GB di spazio disponibile nella posizione iniziale. Mentre il valore minimo è 10%, è consigliabile 15% dello spazio di archiviazione locale da utilizzare per il percorso della cache.
- Dati vengono archiviati in archiviazione archivio Azure. Illimitato per la quantità di dati che è possibile eseguire il backup di una copia di Backup di Azure archivio, ma le dimensioni di un'origine dati (ad esempio una macchina virtuale o database) non devono superare 54,400 GB.

Questi tipi di file supportati per eseguire il backup di Azure:

- Crittografati (solo backup completi)
- Compressi (backup incrementali supportato)
- Sparse (backup incrementali supportato)
- Compresso e caricato in memoria bassa (considerato Sparse)

E questi non sono supportati:

- Server di maiuscole e minuscole file System non sono supportati.
- Collegamenti fissi (ignorato)
- Punti (ignorato) di analisi
- Crittografati e compressi (ignorato)
- Crittografati e caricato in memoria bassa (ignorata)
- Flusso compresso
- Flusso caricato in memoria bassa

>[AZURE.NOTE] Da in System Center 2012 DPM con SP1 in avanti, è possibile eseguire il backup dei carichi di lavoro protetti da Data Protection Manager di Azure con Microsoft Azure Backup.
