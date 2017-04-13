<properties
    pageTitle="Ripristinare i database abilitato zoom | Microsoft Azure"
    description="Informazioni su come ripristinare zoom\-abilitato database."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Ripristinare i database abilitato zoom

Ripristinare un database quando è necessario ripristinare uno dei numerosi tipi di errori, errori e guasti.

Per ulteriori informazioni sulla copia di backup, vedere [database abilitati per i Backup zoom](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] Copia di backup è solo una parte di una disponibilità completa e soluzione di continuità aziendale. Per ulteriori informazioni sulla disponibilità, vedere [Le soluzioni di disponibilità elevato](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Ripristinare i dati di SQL Server
Per risolvere un errore hardware o danni, ripristinare l'estensione\-database di SQL Server da un backup abilitato. È possibile continuare a utilizzare i metodi di ripristino di SQL Server in uso. Per ulteriori informazioni, vedere [Panoramica sul ripristino e ripristino](https://msdn.microsoft.com/library/ms191253.aspx).

Dopo avere ripristinato il database di SQL Server, è necessario eseguire la stored procedure **sys.sp_rda_reauthorize_db** per ristabilire la connessione tra l'estensione\-abilitato database SQL Server e il database remoto Azure. Per ulteriori informazioni, vedere [ripristinare la connessione tra il database di SQL Server e il database remoto Azure](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Ripristinare i dati di Azure remoti

### <a name="recover-a-live-azure-database"></a>Ripristinare un database di Azure live
Il Database di SQL Server zoom servizio su Azure istantanee tutti i dati reali almeno ogni 8 ore tramite snapshot di spazio di archiviazione Azure. Questi snapshot vengono mantenuti per 7 giorni. In questo modo è possibile ripristinare i dati in uno dei punti di almeno 21 ora compresa negli ultimi sette giorni fino al momento quando l'ultima creazione dello snapshot.

Per ripristinare un database di Azure attivo a un punto precedente nel tempo tramite il portale di Azure, eseguire le operazioni seguenti.

1. Accedere al portale di Azure.
2. Sul lato sinistro dello schermo selezionare **Sfoglia** e quindi selezionare **Database SQL**.
3. Passare al database e selezionarlo.
4. Nella parte superiore e il database, fare clic su **Ripristina**.
5. Specificare un nuovo **nome del Database**, selezionare un **Punto di ripristino** e quindi fare clic su **Crea**.
6. Il processo di ripristino del database, verrà avviata e può essere controllato tramite **le notifiche**.

### <a name="recover-a-deleted-azure-database"></a>Ripristinare un database di Azure eliminato
Il servizio di Database di SQL Server zoom in Azure effettua un'istantanea del database prima di un database viene eliminato e conserva per 7 giorni. Quando questo accade mantiene non è più istantanee da database attivo. Consente di ripristinare un database eliminato e il punto di quando è stato eliminato.

Per ripristinare un database di Azure eliminato al punto di quando è stata eliminata dal portale di Azure, eseguire le operazioni seguenti.

1. Accedere al portale di Azure.
2. Sul lato sinistro dello schermo selezionare **Sfoglia** e quindi selezionare **SQL Server**.
3. Passare al server e selezionarlo.
4. Scorrere fino a operazioni su blade del server, fare clic sul riquadro **Database eliminati** .
5. Selezionare il database eliminato da ripristinare.
5. Specificare un nuovo **nome del Database** e fare clic su **Crea**.
6. Il processo di ripristino del database, verrà avviata e può essere controllato tramite **le notifiche**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Ripristinare la connessione tra il database di SQL Server e il database remoto Azure

1.  Se prevede di connettersi a un database di Azure ripristinato con un nome diverso o in un'area diversa, eseguire la stored procedure [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) la disconnessione dal database di Azure precedente.  

2.  Eseguire la stored procedure [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) per riconnettersi l'estensione locale\-database al database di Azure abilitato.  

    -   Specificare le credenziali di database nell'ambito esistente come un sysname o un varchar\(128\) valore. \(Non utilizzare varchar\(max\).\) È possibile cercare il nome delle credenziali nella visualizzazione **sys.database\_nell'ambito\_credenziali**.  

    -   Specificare se si desidera creare una copia dei dati remoti e collegare la copia (scelta consigliata).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Vedere anche

[Gestire e risolvere i problemi di Database di zoom](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Eseguire il backup e ripristino del database di SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
