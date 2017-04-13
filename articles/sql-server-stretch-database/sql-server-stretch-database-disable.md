<properties
    pageTitle="Disabilitare il Database di zoom e riportare dati remoti | Microsoft Azure"
    description="Informazioni su come disabilitare il Database di zoom per una tabella e facoltativamente riportare dati remoti."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Disabilitare il Database di zoom e riportare dati remoti

Per disattivare il Database di zoom per una tabella, selezionare **Zoom** per una tabella di SQL Server Management Studio. Quindi selezionare una delle opzioni seguenti.

-   Disabilita **| Importare dati da Azure**. Copiare i dati remoti per la tabella da Azure indietro a SQL Server, quindi disabilitare il Database di zoom per la tabella. Questa operazione comporta costi di trasferimento di dati e non possono essere annullato.

-   Disabilita **| Lasciare i dati di Azure**. Disabilitare il Database di zoom per la tabella.  Abbandonare i dati remoti per la tabella in Azure.

È inoltre possibile utilizzare Transact\-SQL per disabilitare il Database di zoom per una tabella o per un database.

Dopo avere disattivato Database zoom per una tabella, si interrompe la migrazione di dati e i risultati della query non includono più risultati dalla tabella remota.

Se si desidera semplicemente sospendere la migrazione dei dati, vedere [sospendere e riprendere Database zoom](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] La disattivazione di Database di zoom per una tabella o per un database non eliminare l'oggetto remoto. Se si desidera eliminare la tabella remota o il database remoto, è necessario rilasciarla tramite il portale di gestione Azure. Gli oggetti remoti continuano a sostenere costi Azure finché non viene eliminato. Per ulteriori informazioni, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Disabilitare il Database di zoom per una tabella

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Usare SQL Server Management Studio per disabilitare il Database di zoom per una tabella

1.  In SQL Server Management Studio in Esplora oggetti, selezionare la tabella per cui si vuole disabilitare il Database di zoom.

2.  Destra\-fare clic su e selezionare **Zoom**e quindi selezionare una delle opzioni seguenti.

    -   Disabilita **| Importare dati da Azure**. Copiare i dati remoti per la tabella da Azure indietro a SQL Server, quindi disabilitare il Database di zoom per la tabella. Questo comando non può essere annullato.

        >   [AZURE.NOTE] Copiare i dati remoti per la tabella da Azure indietro per SQL Server comporta costi di trasferimento di dati. Per ulteriori informazioni, vedere [Dettagli prezzi trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

        Dopo che tutti i dati remoti sono stati copiati da Azure di nuovo in SQL Server, zoom è disattivata per la tabella.

    -   Disabilita **| Lasciare i dati di Azure**. Disabilitare il Database di zoom per la tabella.  Abbandonare i dati remoti per la tabella in Azure.

    >   [AZURE.NOTE] Disattivazione del Database di zoom per una tabella non viene eliminato i dati remoti o la tabella remota. Se si desidera eliminare la tabella remota, è necessario rilasciarla tramite il portale di gestione Azure. La tabella remota continua a sostenere costi Azure finché non viene eliminato. Per ulteriori informazioni, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Utilizzare Transact\-SQL per disabilitare il Database di zoom per una tabella

-   Per disattivare zoom per una tabella e copiare che i dati remoti per la tabella da Azure di nuovo in SQL Server, eseguire il comando seguente. Dopo che tutti i dati remoti sono stati copiati da Azure di nuovo in SQL Server, zoom è disattivata per la tabella.

    Questo comando non può essere annullato.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Copiare i dati remoti per la tabella da Azure indietro per SQL Server comporta costi di trasferimento di dati. Per ulteriori informazioni, vedere [Dettagli prezzi trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Per disabilitare zoom per una tabella e abbandonare i dati remoti, eseguire il comando seguente.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Disattivazione del Database di zoom per una tabella non viene eliminato i dati remoti o la tabella remota. Se si desidera eliminare la tabella remota, è necessario rilasciarla tramite il portale di gestione Azure. La tabella remota continua a sostenere costi Azure finché non viene eliminato. Per ulteriori informazioni, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Disabilitare il Database di zoom per un database
Prima che è possibile disattivare il Database di zoom per un database, è necessario disattivare Database zoom Zoom singoli\-abilitato le tabelle del database.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Consente di disabilitare il Database di zoom per un database SQL Server Management Studio

1.  In Esplora oggetti di SQL Server Management Studio selezionare il database per il quale si vuole disabilitare il Database di zoom.

2.  Destra\-fare clic su e selezionare **le attività**e quindi selezionare **Zoom**e quindi selezionare **Disabilita**.

>   [AZURE.NOTE] Disattivazione del Database di zoom per un database non viene eliminato il database remoto. Se si desidera eliminare il database remoto, è necessario rilasciarla tramite il portale di gestione Azure. Il database remoto continua a sostenere costi Azure finché non viene eliminato. Per ulteriori informazioni, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Utilizzare Transact\-SQL per disabilitare il Database di zoom per un database
Eseguire il comando seguente.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Disattivazione del Database di zoom per un database non viene eliminato il database remoto. Se si desidera eliminare il database remoto, è necessario rilasciarla tramite il portale di gestione Azure. Il database remoto continua a sostenere costi Azure finché non viene eliminato. Per ulteriori informazioni, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Vedere anche

[ALTER DATABASE impostare le opzioni (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Interrompere e riprendere Database zoom](sql-server-stretch-database-pause.md)
