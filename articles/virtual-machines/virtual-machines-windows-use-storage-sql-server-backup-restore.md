<properties
    pageTitle="Come usare lo spazio di archiviazione Azure per SQL Server backup e ripristino | Microsoft Azure"
    description="Informazioni su come eseguire il backup di SQL Server per lo spazio di archiviazione di Azure. Vengono illustrati i vantaggi del backup dei database SQL di archiviazione Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Usare lo spazio di archiviazione Azure per SQL Server Backup e ripristino

## <a name="overview"></a>Panoramica

A partire da SQL Server 2012 SP1 CU2, è possibile scrivere backup di SQL Server direttamente al servizio di archiviazione Blob Azure. È possibile utilizzare questa funzionalità di backup e ripristino del servizio Blob Azure con un database di SQL Server locale o in un database di SQL Server in una macchina virtuale Azure. Copia di backup nel cloud offre i vantaggi della disponibilità, infinito archiviazione fuori sede replicato geografico e facilità di migrazione di dati da e dal cloud. È possibile inviare le istruzioni di BACKUP o ripristino utilizzando Transact-SQL o SMO.

SQL Server 2016 introduce nuove funzionalità; è possibile utilizzare [il backup di file istantanea](http://msdn.microsoft.com/library/mt169363.aspx) eseguire quasi istantaneo backup e ripristino estremamente rapido.

In questo argomento viene illustrato il motivo per cui è possibile scegliere di utilizzare lo spazio di archiviazione Azure per i backup SQL e viene quindi descritta componenti coinvolti. È possibile utilizzare le risorse fornite alla fine dell'articolo per accedere a informazioni aggiuntive per iniziare a usare questo servizio con il backup di SQL Server e procedure dettagliate.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Vantaggi dell'utilizzo del servizio di archivio Blob Azure per i backup SQL Server

Esistono diversi problemi riscontrati durante il backup di SQL Server. Questi problemi includono gestione di archiviazione, rischio di errore di spazio di archiviazione, accesso a archiviazione fuori sede e configurazione hardware. Molti di questi problemi sono trattate tramite il servizio di archiviazione Blob Azure per i backup di SQL Server. Considerare i seguenti vantaggi:

- **Facilità di utilizzo**: archiviare i backup BLOB Azure può essere un comodo e flessibile facile accesso opzione fuori sede. Creazione di archiviazione fuori sede per i backup di SQL Server può essere semplice come modifica esistenti script/processi di utilizzare la sintassi di **BACKUP all'URL** . Archiviazione fuori sede in genere dovrebbe essere abbastanza dalla posizione del database di produzione per evitare che una singola emergenza che potrebbe rallentare percorsi database fuori sede e di produzione. Se si sceglie di [geografico replica il Azure BLOB](../storage/storage-redundancy.md), è necessario un ulteriore livello di protezione in caso di emergenza che può influire sull'intera area.
- **Archivio di backup**: servizio di archiviazione Blob di Azure offre un'alternativa migliore all'opzione nastro spesso utilizzate per archiviare i backup. Spazio di archiviazione nastro potrebbe richiedere trasporti fisico per una funzione fuori sede e misure per proteggere gli elementi multimediali. Archiviazione dei backup in archiviazione Blob Azure fornisce un'istantanea, disponibilità, e un permanenti archiviazione opzione.
- **Hardware gestita**: un sovraccarico di gestione dell'hardware con i servizi di Azure. Servizi di Azure gestire i componenti hardware e forniscono replica geografico per ridondanza e protezione contro gli errori hardware.
- **Archiviazione illimitata**: abilitando una copia di backup diretto a BLOB Azure, è possibile accedere allo spazio di archiviazione praticamente illimitata. In alternativa, il backup su un disco Azure macchina virtuale sono previsti limiti in base alle dimensioni del computer. Esiste un limite per il numero di dischi che è possibile allegare a una macchina virtuale Azure per i backup. Questo limite è 16 dischi per un'istanza di dimensioni molto grande e meno per le istanze di dimensioni inferiori.
- **Disponibilità di backup**: backup archiviati nei blob Azure sono disponibili ovunque e in qualsiasi momento e facilmente accessibile per le operazioni di ripristino di un SQL Server locale o un altro SQL Server in esecuzione in una Azure macchina virtuale, senza la necessità di collegare o scollegare database o il download e associare il disco rigido virtuale.
- **Costo**: pagare solo per il servizio utilizzato. Può essere conveniente come opzione archivio fuori sede e backup. Vedere la [Calcolatrice prezzo Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calcolatrice prezzi")e i [prezzi Azure articolo](http://go.microsoft.com/fwlink/?LinkId=277059 "prezzi articolo") per ulteriori informazioni.
- **Snapshot di spazio di archiviazione**: quando i file di database vengono memorizzati in un archivio blob Azure e si utilizza SQL Server 2016, è possibile utilizzare [snapshot di file di backup](http://msdn.microsoft.com/library/mt169363.aspx) per eseguire quasi istantaneo backup e ripristino estremamente rapido.

Per ulteriori informazioni, vedere [SQL Server Backup e ripristino con il servizio di archiviazione Blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Due sezioni seguenti verranno illustrati il servizio di archiviazione Blob Azure, inclusi i componenti necessari di SQL Server. È importante conoscere i componenti e la loro interazione per utilizzare backup e ripristino del servizio di archiviazione Blob Azure.

## <a name="azure-blob-storage-service-components"></a>Componenti del servizio di archiviazione Blob Azure

I seguenti componenti di Azure vengono utilizzati per eseguire il backup per il servizio di archiviazione Blob Azure.

| Componente               | Descrizione                          |
|---------------------|-------------------------------|
| **Account di archiviazione** | L'account di archiviazione è il punto di partenza per tutti i servizi di archiviazione. Per accedere a un servizio di archiviazione Blob Azure, è necessario creare un account di archiviazione Azure. Per ulteriori informazioni sul servizio di archiviazione Blob Azure, vedere [come utilizzare il servizio di archiviazione Blob Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contenitore** | Un contenitore offre un raggruppamento di un insieme di BLOB e consentono di memorizzare un numero illimitato di BLOB. Per scrivere un Server SQL backup in un servizio Blob Azure, è necessario disporre almeno del contenitore radice creato. |
| **BLOB** | File di qualsiasi tipo e dimensioni. BLOB sono indirizzati tramite il seguente formato URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Per ulteriori informazioni sui blob di pagine, vedere [informazioni sui blocco e BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componenti di SQL Server

I seguenti componenti di SQL Server vengono utilizzati per eseguire il backup per il servizio di archiviazione Blob Azure.

| Componente               | Descrizione                          |
|---------------------|-------------------------------|
| **URL** | Un URL specifica una risorsa identificatore URI (Uniform) a un file di backup univoco. L'URL viene utilizzato per specificare il percorso e il nome del file di backup di SQL Server. L'URL deve puntare a un blob effettivo, non solo un contenitore. Se il blob non esiste, viene creato. Se si specifica un blob esistente, BACKUP non riesce, a meno che non la > con formato opzione specificata. Nell'esempio è illustrato un esempio di URL è possibile specificare il comando copia di BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS è consigliabile ma non sono necessarie. |
| **Credenziali** | Le informazioni necessarie per connettersi e autenticazione al servizio di archiviazione Blob Azure viene memorizzate come credenziali.  Per SQL Server scrivere i backup in un archivio Blob Azure o ripristino del file, è necessario creare credenziali SQL Server. Per ulteriori informazioni, vedere [Credenziali di SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Se si sceglie di copiare e caricare un file di backup per il servizio di archiviazione Blob Azure, è necessario utilizzare un tipo di blob pagina come opzione di spazio di archiviazione se si prevede di utilizzare questo file per le operazioni di ripristino. RIPRISTINO da un tipo di blob blocco potrebbe non riuscire con un errore.

## <a name="next-steps"></a>Passaggi successivi

1. Creare un account Azure se non si dispone già uno. Se si sta valutando Azure, è consigliabile la [versione di valutazione gratuita](https://azure.microsoft.com/free/).

1. Quindi eseguire una delle seguenti esercitazioni che guidano l'utente tramite la creazione di un account di archiviazione ed eseguire un ripristino.

    - **SQL Server 2014**: [Esercitazione: SQL Server 2014 Backup e ripristino a Microsoft Azure Blob il servizio di archiviazione](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [Esercitazione: usare il servizio di archiviazione Blob di Microsoft Azure con database di SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

1. Esaminare la documentazione aggiuntiva a partire da [SQL Server Backup e ripristino con il servizio di archiviazione Blob di Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

In caso di problemi, consultare l'argomento [Backup di SQL Server a URL procedure consigliate e risoluzione dei problemi](https://msdn.microsoft.com/library/jj919149.aspx).

Per altri SQL Server eseguire il backup e ripristino opzioni, vedere [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
