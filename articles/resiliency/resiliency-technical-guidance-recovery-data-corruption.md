<properties
   pageTitle="Indicazioni tecniche adattabilità per il ripristino da danneggiamento dei dati o eliminazioni accidentali | Microsoft Azure"
   description="Articolo relativi a come recuperare il danneggiamento dei dati di dati o eliminazione accidentale dei dati per la progettazione di applicazioni tolleranza di errore flessibili, disponibilità, nonché strategia di emergenza"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Indicazioni tecniche sulla resilienza Azure: ripristino di danneggiamento dei dati o eliminazioni accidentali

Parte di un piano di continuità aziendale affidabile ha un piano se i dati ottiene danneggiati o eliminati accidentalmente. Di seguito è informazioni sul ripristino dopo dati sono stati danneggiati o eliminati per errore, a causa di un errore dell'operatore o gli errori dell'applicazione.

##<a name="virtual-machines"></a>Macchine virtuali

Per proteggere le macchine virtuali di Azure (a volte chiamato macchine virtuali di infrastruttura come servizio) da errori dell'applicazione o eliminazioni accidentali, utilizzare [Il Backup di Azure](https://azure.microsoft.com/services/backup/). Backup Azure consente la creazione di backup sono coerenti in più dischi macchine Virtuali. Inoltre, l'archivio di Backup possono essere replicata in aree geografiche per fornire il ripristino da perdita area.

##<a name="storage"></a>Spazio di archiviazione

Si noti che lo spazio di archiviazione di Azure offre flessibilità dati tramite repliche automatizzate, questo non impedisce il codice dell'applicazione (o gli sviluppatori/utenti) comprometta dati tramite l'eliminazione indesiderata o accidentale, aggiornamento e così via. Gestione di fedeltà di dati in caso di errore dell'utente o applicazione richiede tecniche più avanzate, ad esempio copiare i dati in una posizione di archiviazione secondario con un log di controllo. Gli sviluppatori possono trarre vantaggio della blob [funzionalità snapshot](https://msdn.microsoft.com/library/azure/ee691971.aspx), che è possibile creare snapshot momento in sola lettura del contenuto di blob. Può essere utilizzato come base di una soluzione di fedeltà non significativa di dati per lo spazio di archiviazione di Azure BLOB.

###<a name="blob-and-table-storage-backup"></a>BLOB e Backup di spazio di archiviazione tabella

Benché BLOB e tabelle siano grande durature, rappresentano sempre lo stato corrente dei dati. Ripristino da modifiche indesiderate o eliminazione di dati potrebbe essere necessario ripristinare i dati in uno stato precedente. Questa operazione può essere ottenuta sfruttare le funzionalità offerte da Azure per archiviare e conservare le copie in un momento.

Per i BLOB Azure, è possibile eseguire backup in un momento usando la [funzionalità di istantanea blob](https://msdn.microsoft.com/library/ee691971.aspx). Per ogni snapshot solo addebitate per lo spazio di archiviazione necessario per memorizzare le differenze all'interno del blob poiché l'ultimo snapshot stato. Snapshot dipendono l'esistenza di blob originale che si basano, in modo che un'operazione di copia in un'altra blob o anche un altro account di archiviazione è consigliabile. In questo modo che i dati di backup sono protetto correttamente la cancellazione accidentale. Per le tabelle di Azure, è possibile creare copie nel momento in un'altra tabella o per BLOB Azure. Ulteriori istruzioni ed esempi di backup a livello di applicazione delle tabelle e BLOB sono disponibili qui:

  * [Proteggere le tabelle e gli errori dell'applicazione](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Proteggere il BLOB contro gli errori dell'applicazione](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Database

Sono disponibili diverse opzioni di [continuità aziendale](../sql-database/sql-database-business-continuity.md) (backup, ripristino) per il Database di SQL Azure. È possibile copiare i database utilizzando la funzionalità [Copia del Database](../sql-database/sql-database-copy.md) o [esportazione](../sql-database/sql-database-export.md) e [importazione di](https://msdn.microsoft.com/library/hh710052.aspx) un file di bacpac di SQL Server. Copia del database offre risultati coerenti, mentre non bacpac (tramite il servizio di importazione/esportazione). Entrambe le opzioni eseguite come servizi basati su coda all'interno del centro di dati e attualmente non fornisce un contratto di servizio ora di completamento.

>[AZURE.NOTE]Opzioni di importazione/esportazione e copia del database posizionare una parte notevole del carico sul database di origine. Possono attivare conflitti tra le risorse o la limitazione per gli eventi.

###<a name="sql-database-backup"></a>Backup del Database SQL

Backup in un momento per Database SQL di Microsoft Azure e ottenere [la copia del database di SQL Azure](../sql-database/sql-database-copy.md). È possibile utilizzare questo comando per creare una copia coerente a livello di un database nello stesso server di database logico o a un server diverso. In entrambi i casi, la copia del database è completamente funzionale e completamente indipendenti del database di origine. Ogni copia create rappresenta un'opzione di ripristino in un momento. È possibile ripristinare lo stato del database completamente rinominando il nuovo database con il nome di database di origine. In alternativa, è possibile ripristinare un sottoinsieme di dati specifico dal nuovo database utilizzando query Transact-SQL. Per ulteriori informazioni sul Database SQL, vedere [Panoramica della continuità aziendale con Database di SQL Azure](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server in macchine virtuali copia di Backup

Per SQL Server utilizzata con infrastruttura di Azure come un macchine virtuali di servizio (spesso denominate IaaS o macchine virtuali IaaS), sono disponibili due opzioni: backup tradizionale e la distribuzione dei log. Il backup tradizionale consente di ripristinare in un punto specifico nel tempo, ma il processo di ripristino è lento. Ripristino di backup tradizionale richiede a partire da un backup completo iniziale e applicando un backup in seguito. La seconda opzione consiste nel configurare una sessione di posticipare il ripristino di backup del log (ad esempio, da due ore) di distribuzione dei log. In questo modo una finestra per correggere gli errori apportati nel server primario.

##<a name="other-azure-platform-services"></a>Altri servizi di piattaforma Azure

Alcuni servizi della piattaforma Azure memorizzare le informazioni in un account di archiviazione controllata dall'utente o un Database SQL Azure. Se la risorsa account o lo spazio di archiviazione viene eliminata o danneggiata, ciò potrebbe causare errori con il servizio. In questi casi, è importante mantenere backup che consente di creare di nuovo queste risorse se sono stati eliminati o danneggiati.

Per i siti Web Azure e servizi di dispositivi mobili Windows Azure, è necessario eseguire il backup e gestire i database associati. Per il servizio di supporto Azure e macchine virtuali, è necessario mantenere l'account di archiviazione Azure associato e tutte le risorse in tale account. Ad esempio, per macchine virtuali, è necessario eseguire il backup e gestire i dischi macchine Virtuali di archiviazione blob Azure.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Elenchi di controllo per il danneggiamento dei dati o eliminazioni accidentali

##<a name="virtual-machines-checklist"></a>Elenco di controllo macchine virtuali

  1. Nella sezione macchine virtuali di questo documento.
  2. Eseguire il backup e gestire i dischi macchine Virtuali con Azure Backup (o sistema di backup con archiviazione blob Azure e snapshot disco rigido virtuale).

##<a name="storage-checklist"></a>Elenco di controllo per lo spazio di archiviazione

  1. Nella sezione dello spazio di archiviazione di questo documento.
  2. Regolarmente backup delle risorse di archiviazione critico.
  3. Valutare la possibilità di utilizzo della funzionalità di snapshot per BLOB.

##<a name="database-checklist"></a>Elenco di controllo di database

  1. Nella sezione Database di questo documento.
  2. Creazione di backup in un momento tramite il comando copia del Database.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server nell'elenco di controllo macchine virtuali di Backup

  1. Esaminare SQL Server nella sezione Backup macchine virtuali di questo documento.
  2. Utilizzare tradizionale backup e ripristino tecniche.
  3. Creare un file di log ritardato sessione di spedizione.

##<a name="web-apps-checklist"></a>Elenco di controllo Web App

  1. Eseguire il backup e mantenere il database associato, se presenti.

##<a name="media-services-checklist"></a>Elenco di controllo servizi multimediali

  1. Eseguire il backup e gestire le risorse di archiviazione associato.

##<a name="more-information"></a>Ulteriori informazioni

Per ulteriori informazioni sulle caratteristiche di backup e ripristino di Azure, vedere [lo spazio di archiviazione, scenari di backup e ripristino](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie basata su [indicazioni tecniche sulla resilienza Azure](./resiliency-technical-guidance.md). Se si sta cercando ulteriori adattabilità di emergenza e risorse di disponibilità, vedere le indicazioni tecniche di Azure adattabilità [risorse aggiuntive](./resiliency-technical-guidance.md#additional-resources).