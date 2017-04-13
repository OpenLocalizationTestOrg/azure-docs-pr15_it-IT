<properties 
    pageTitle="Spostare i dati a un Database di SQL Azure per l'apprendimento Azure | Azure" 
    description="Creare tabella SQL e Carica dati alla tabella di SQL" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016"
    ms.author="bradsev" /> 

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Spostare i dati a un Database di SQL Azure per l'apprendimento Azure

In questo argomento vengono illustrate le opzioni per spostare i dati da un file flat (formati TSV o CSV) o dai dati memorizzati in un Server SQL locale a un database SQL Azure. Le attività seguenti per spostare i dati nel cloud fanno parte del processo di scienze di dati del Team.

Per un argomento che illustra le opzioni per spostare i dati a un Server SQL locale per l'apprendimento, vedere [spostare i dati di SQL Server in un computer virtuale Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

I collegamenti seguenti **menu** ad argomenti che descrivono come l'acquisizione di dati in ambienti di destinazione in cui i dati vengono memorizzati e durante il processo di scienza dati (TDSP) del Team, di elaborazione.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Nella tabella seguente vengono riepilogate le opzioni per spostare i dati a un Database di SQL Azure.

<b>ORIGINE</b> |<b>DESTINAZIONE: Database SQL Azure</b> |
-------------- |--------------------------------|
<b>File flat (CSV o TSV formattato)</b> |<a href="#bulk-insert-sql-query">Query SQL di inserimento in blocco |
<b>SQL Server locale</b> | 1. <a href="#export-flat-file">esportare in File Flat<br> 2. <a href="#insert-tables-bcp">migrazione guidata Database di SQL<br> 3. <a href="#db-migration">database back backup e ripristino<br> 4. <a href="#adf">Factory dati azure |


## <a name="prereqs"></a>Prerequisiti
Le procedure descritte di seguito è necessario disporre:

* Un **abbonamento Azure**. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un **account di archiviazione Azure**. Si utilizza un account di archiviazione Azure per archiviare i dati in questa esercitazione. Se non si dispone di un account di archiviazione Azure, vedere l'articolo [creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) . Dopo aver creato l'account di archiviazione, è necessario ottenere la chiave account usata per accedere allo spazio di archiviazione. Vedere [visualizzazione, copia e lo spazio di archiviazione rigenera i tasti di scelta](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accesso a un **Database SQL Azure**. Se è necessario impostare un Database di SQL Azure, [Guida introduttiva di Database SQL di Microsoft Azure](../sql-database/sql-database-get-started.md) fornisce informazioni su come effettuare il provisioning di una nuova istanza di un Database di SQL Azure.
* Installato e configurato **Azure PowerShell** in locale. Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

**Dati**: I processi di migrazione vengono illustrati utilizzando il [set di dati Taxi Roma](http://chriswhong.com/open-data/foil_nyc_taxi/). Il set di dati di Roma Taxi contiene informazioni sui dati di viaggio e fiere ed è disponibile, come indicato di archiviazione blob Azure tale post: [Roma Taxi dati](http://www.andresmh.com/nyctaxitrips/). Un esempio e una descrizione di questi file vengono fornite in [Roma Taxi trip set di dati descrizione](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
È possibile adattare le procedure descritte di seguito per un set di dati personalizzati o seguire i passaggi come descritto utilizzando il set di dati Taxi Roma. Per caricare il set di dati Taxi Roma nel database di SQL Server in locale, seguire la procedura descritta in [Blocco Importa dati nel Database di SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Queste istruzioni siano relativi a SQL Server una macchina virtuale Azure, ma la procedura per il caricamento in SQL Server locale è la stessa.


## <a name="file-to-azure-sql-database"></a>Spostare dati da un'origine file flat a un database SQL Azure

A un database di SQL Azure utilizzando una Query SQL inserire in blocco, è possono spostare i dati in file flat (formattato TSV o CSV).

### <a name="bulk-insert-sql-query"></a>Query SQL di inserimento in blocco

La procedura per la procedura di utilizzo della Query SQL inserire in blocco è simile a quelli descritti in sezioni per spostare i dati da un'origine file flat a SQL Server in macchine Virtuali un Azure. Per informazioni dettagliate, vedere [Query SQL di inserimento di massa](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Spostare dati da SQL Server locale a un database SQL Azure

Se i dati di origine sono archiviati in un Server SQL locale, sono disponibili diverse possibilità per spostare i dati in un database SQL Azure:

1. [Esportare in File Flat](#export-flat-file) 
2. [Migrazione guidata Database di SQL](#insert-tables-bcp)
3. [Database back backup e ripristino](#db-migration)
4. [Dati di Azure Factory](#adf)

La procedura per i primi tre è molto simile alle sezioni coinvolte [spostare i dati di SQL Server in un computer virtuale Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) che illustrate le stesse procedure. Nelle istruzioni seguenti vengono forniti collegamenti alle sezioni appropriate in questo argomento.

###<a name="export-flat-file"></a>Esportare in File Flat

La procedura per questo esportazione in un file flat è simile a quelle descritte in [Esporta in File Flat](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Migrazione guidata Database di SQL

La procedura per usare la migrazione guidata Database di SQL è simile a quelle descritte in [Migrazione guidata Database di SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Database back backup e ripristino

La procedura per l'utilizzo di database di backup e ripristino è simile a quelle descritte nel [Database di backup e ripristino](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Dati di Azure Factory

Procedura per spostare i dati a un database di SQL Azure con Azure dati Factory (alimentatore automatico) è disponibile nella sezione [spostare i dati da un server SQL locale di SQL Azure con Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md). In questo argomento viene illustrato come spostare i dati da un database di SQL Server locale a un database SQL Azure tramite archiviazione Blob Azure con alimentatore automatico. 

Valutare la possibilità di utilizzo alimentatore automatico quando i dati devono essere continuamente eseguita in uno scenario ibrido che accede alle risorse locali e cloud e quando i dati viene gestiti o devono essere modificate o una regola di business aggiunto quando viene eseguita la migrazione. Consente di alimentatore automatico per la pianificazione e il monitoraggio dei processi utilizzando semplici script JSON che gestiscono lo spostamento dei dati periodicamente. Alimentatore automatico è installato anche altre funzionalità, ad esempio il supporto per operazioni complesse.




