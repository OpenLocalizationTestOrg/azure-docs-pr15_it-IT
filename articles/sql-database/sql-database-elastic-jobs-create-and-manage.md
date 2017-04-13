<properties
    pageTitle="Creare e gestire in scala i database di SQL Azure con processi flessibile | Microsoft Azure"
    description="Scorrere la creazione e gestione di un processo di database flessibile."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Creare e gestire in scala i database di SQL Azure con processi flessibile (preview)

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Database flessibile processi** semplificare la gestione dei gruppi di database eseguendo le operazioni amministrative, ad esempio le modifiche allo schema, la gestione delle credenziali, gli aggiornamenti dei dati di riferimento, raccolta dati sulle prestazioni o raccolta di telemetria tenant (cliente). Non è attualmente disponibile tramite il portale Azure e i cmdlet di PowerShell flessibile processi di Database. Tuttavia, le superfici portale Azure a funzionalità ridotte limitata per l'esecuzione in tutti i database in un [Database flessibile pool (preview)](sql-database-elastic-pool.md). Per accedere a caratteristiche aggiuntive e l'esecuzione di script in un gruppo di database, ad esempio un insieme personalizzato o un set di condiviso (creato tramite [libreria client Database flessibile](sql-database-elastic-scale-introduction.md)), vedere [creazione e la gestione dei processi tramite PowerShell](sql-database-elastic-jobs-powershell.md). Per ulteriori informazioni sui processi, vedere [Panoramica di processi di Database flessibile](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Prerequisiti

* Un abbonamento Azure. Per una versione di valutazione gratuita, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Un pool di database flessibile. Vedere [sulle flessibile pool di database](sql-database-elastic-pool.md)
* Installazione di componenti di database flessibile processo del servizio. Vedere [installazione del servizio di processo database flessibile](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>La creazione di processi

1. Tramite il [portale di Azure](https://portal.azure.com), da un pool di processo flessibile database esistente, fare clic su **Crea processo**.
2. Digitare il nome utente e la password dell'amministratore del database (creato al momento dell'installazione di processi) per il database di controllo di processi (spazio di archiviazione dei metadati per i processi).

    ![Assegnare un nome il processo, digitare o incollare codice e fare clic su Esegui][1]
2. In e il **Processo di creazione** , digitare un nome per il processo.
3. Digitare il nome utente e password per connettersi al database di destinazione con autorizzazioni sufficienti per l'esecuzione di script di esito positivo.
4. Incollare o digitare lo script T-SQL.
5. Fare clic su **Salva** e quindi fare clic su **Esegui**.

    ![Creare processi ed eseguire][5]

## <a name="run-idempotent-jobs"></a>Eseguire i processi idempotenti

Quando si esegue uno script su un set di database, assicurarsi che lo script sia idempotenti. Vale a dire lo script deve essere in grado di eseguire più volte, anche se non riesce prima in incompleto. Ad esempio, quando uno script non riesce, il processo verrà automaticamente riprovato fino a quando non ha avuto esito positivo (compreso tra limiti, come il tentativo logica infine interrompa la ripetizione). Il modo per eseguire questa operazione consiste nell'utilizzare la clausola "IF EXISTS" ed eliminare tutte le istanze trovate prima di creare un nuovo oggetto. Come illustrato di seguito:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

In alternativa, è possibile utilizzare una clausola "Se non esiste" prima di creare una nuova istanza:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Questo script quindi aggiorna la tabella creata in precedenza.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Verifica dello stato di processo

Dopo aver iniziato un processo, è possibile controllare lo stato di avanzamento.

1. Nella pagina pool di database flessibile, fare clic su **Gestisci processi**.

    ![Fare clic su "Gestire processi"][2]

2. Fare clic sul nome (a) di un processo. Lo **stato** può essere "Completato" o "Failed". Dettagli del processo vengono visualizzati (b) con la data e ora di creazione e l'esecuzione. L'elenco (c) sotto l'oggetto che mostra lo stato di avanzamento dello script in tutti i database nel pool di fornisce i dettagli di data e ora.

    ![Verifica di un processo finito][3]


## <a name="checking-failed-jobs"></a>Controllo non riuscito processi

Se un processo non riesce, possono trovare un file di log di esecuzione. Fare clic sul nome del processo non riuscito per visualizzarne i dettagli.

![Selezionare un processo non riuscito][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
