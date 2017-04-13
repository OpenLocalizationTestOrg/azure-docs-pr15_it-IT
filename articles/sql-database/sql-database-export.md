<properties
    pageTitle="Archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure"
    description="Archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

In questo articolo vengono fornite istruzioni per l'archiviazione del database di SQL Azure in un file BACPAC (archiviato in archiviazione blob Azure) tramite il [portale di Azure](https://portal.azure.com).

Quando è necessario creare un archivio di un database di SQL Azure, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è semplicemente un file ZIP con estensione BACPAC. Un file BACPAC in un secondo momento può essere archiviato in archiviazione blob Azure o in archivio locale in una posizione locale e in un secondo momento importati indietro nel Database di SQL Azure o in un server SQL Server locale installazione. 

***Considerazioni***

- Per un archivio per coerenti, è non necessario assicurarsi che nessuna scrittura di attività viene eseguita durante l'esportazione o che desidera esportare i dati di una [copia coerente a livello](sql-database-copy.md) del database SQL Azure.
- La dimensione massima di un file BACPAC archiviato in archiviazione Blob Azure è 200 GB. Per archiviare un file BACPAC più grande nell'archivio locale, l'utilità [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) prompt dei comandi. Questa utilità viene fornito con Visual Studio e SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) l'ultima versione di SQL Server Data Tools per ottenere questa utilità.
- Archiviazione allo spazio di archiviazione di Azure premium tramite un file BACPAC non è supportata.
- Se l'operazione di esportazione supera 20 ore, potrebbero essere annullata. Per migliorare le prestazioni durante l'esportazione, è possibile:
 - Aumentare temporaneamente il livello di servizio.
 - Cessazione tutti leggere e scrivere attività durante l'esportazione.
 - Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, un'esportazione potrebbe non riuscire se sono necessari più di 6-12 ore. Ciò avviene perché il servizio di esportazione deve completare un'analisi della tabella per tentare di esportare l'intera tabella. Un buon modo per stabilire se le tabelle sono ottimizzate per l'esportazione è eseguire **DBCC SHOW_STATISTICS** e assicurarsi che *RANGE_HI_KEY* non null e il relativo valore ha distribuzione efficace. Per informazioni dettagliate, vedere [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] BACPACs non devono essere utilizzato per il backup e ripristino. Database SQL Azure viene creato automaticamente backup per ogni database utente. Per informazioni dettagliate, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md).

Per completare in questo articolo è necessario quanto segue:

- Un abbonamento Azure.
- Un Database SQL Azure. 
- Un [account di archiviazione Standard Azure](../storage/storage-create-storage-account.md) con un contenitore di blob per archiviare la BACPAC nello spazio di memorizzazione standard.

## <a name="export-your-database"></a>Esportare il database

Aprire e il Database di SQL per il database che si desidera esportare.

> [AZURE.IMPORTANT] Per garantire un file BACPAC coerente è necessario prima [creare una copia del database](sql-database-copy.md) e quindi esportare la copia del database. 

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Fare clic su **database SQL**.
3.  Fare clic su database da archiviare.
4.  In e il Database SQL, fare clic su **Esporta** per aprire e il **database di esportare** :

    ![pulsante Esporta][1]

5.  Fare clic su **archiviazione** e selezionare il contenitore di account e blob di spazio di archiviazione in cui verrà archiviata la BACPAC:

    ![esportazione di database][2]

6. Selezionare il tipo di autenticazione. 
7.  Immettere le credenziali di autenticazione appropriata per il server di SQL Azure contenente il database che si esegue l'esportazione.
8.  Fare clic su **OK** per archiviare il database. Fare clic su **OK** crea una richiesta di database di esportazione e invia al servizio. L'intervallo di tempo che richiederà l'esportazione cambiano in base alle dimensioni e alla complessità del database e il livello di servizio. Si riceverà una notifica.

    ![notifica di esportazione][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorare l'avanzamento dell'operazione di esportazione

1.  Fare clic su **SQL Server**.
2.  Fare clic su server che contiene il database di (origine) appena archiviate.
3.  Scorrere fino a operazioni.
4.  In e il server SQL fare clic su **Importa/Esporta Cronologia**:

    ![Importa Esporta cronologia][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Verificare la BACPAC nel contenitore di spazio di archiviazione

1.  Fare clic su **account di archiviazione**.
2.  Fare clic sull'account di archiviazione in cui è archiviato nell'archivio BACPAC.
3.  Fare clic su **contenitori** e selezionare il contenitore che è stata esportata il database in per informazioni dettagliate (è possibile scaricare e salvare BACPAC da qui).

    ![Dettagli file .bacpac][5]  

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'importazione di un BACPAC a un Database di SQL Azure, vedere [importare BACPCAC a un database SQL Azure](sql-database-import.md)
- Per ulteriori informazioni sull'importazione di un BACPAC a un database di SQL Server, vedere [importare BACPCAC a un database di SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

