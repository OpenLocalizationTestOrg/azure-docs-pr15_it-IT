<properties
    pageTitle="Ripristinare un'unica tabella da un backup Database SQL Azure | Microsoft Azure"
    description="Informazioni su come ripristinare un'unica tabella di backup del Database di SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Come ripristinare un'unica tabella da un backup del Database di SQL Azure

È possibile riscontrare situazioni in cui è stato modificato accidentalmente alcuni dati in un database SQL e si vuole recuperare la singola tabella interessata. In questo articolo viene descritto come ripristinare un'unica tabella in un database da uno dei Database SQL [backup automatici](sql-database-automated-backups.md).

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Operazioni di preparazione: rinominare la tabella e il ripristino di una copia del database
1. Identificare la tabella nel database di SQL Azure che si desidera sostituire con la copia ripristinata. Utilizzare Microsoft SQL Management Studio per rinominare la tabella. Rinominare, ad esempio, la tabella come &lt;nome tabella&gt;suffisso old.

    **Nota** Per evitare di essere bloccati, assicurarsi che nessuna attività è in esecuzione sulla tabella da rinominare. Se si verificano problemi, assicurarsi che eseguire questa procedura durante una finestra di manutenzione.

2. Ripristinare una copia di backup del database in un punto nel tempo che si desidera ripristinare mediante la procedura di [Ripristino punto In_Time](sql-database-recovery-using-backups.md#point-in-time-restore) .

    **Note**:
    - Il nome del database ripristinato sarà nel formato DBName + TimeStamp; ad esempio, **Adventureworks2012_2016-01-01T22-12Z**. Questo passaggio non è possibile sovrascrivere il nome del database esistente nel server. Si tratta di una misura di sicurezza e sono destinati a consentono di verificare il database ripristinato prima di eliminare il database corrente e rinominare i database ripristinato per l'utilizzo di produzione.
    - Tutti i livelli di prestazioni di base a Premium vengono automaticamente sottoposti a backup dal servizio, con diverse metriche di conservazione backup, a seconda del livello:

| Ripristino di DB | Livello di base | Livelli standard | Livelli di Premium |
| :-- | :-- | :-- | :-- |
|  Ripristino di data e ora |  Qualsiasi punto di ripristino entro 7 giorni|Qualsiasi punto di ripristino all'interno di 35 giorni| Qualsiasi punto di ripristino all'interno di 35 giorni|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copiando la tabella dal database ripristinato utilizzando lo strumento di migrazione del Database SQL
1. Scaricare e installare la [Migrazione guidata Database di SQL](https://sqlazuremw.codeplex.com).

2. Aprire il Database di migrazione guidata SQL, nella pagina **Seleziona processo** , selezionare **Database in analizza/eseguire la migrazione**e quindi fare clic su **Avanti**.
![Procedura guidata di migrazione del Database di SQL - seleziona processo](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Nella finestra di dialogo **connessione al Server** applicare le impostazioni seguenti:
 - **Nome del server**: istanza di SQL Azure
 - **Autenticazione**: **autenticazione di SQL Server**. Immettere le credenziali di accesso.
 - **Database**: **DB schema (elencare tutti i database)**.
 - **Nota** Per impostazione predefinita la procedura guidata consente di salvare le informazioni di accesso. Se non si desidera venga, selezionare **Le informazioni di accesso dimenticato**.
![Passaggio della procedura guidata - origine selezionare - di migrazione del Database di SQL 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Nella finestra di dialogo **Seleziona origine** selezionare il nome del database ripristinato dalla sezione dei **passaggi di preparazione** come origine e quindi fare clic su **Avanti**.

    ![Passaggio 2 guidata - origine selezionare - la migrazione di Database SQL](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Nella finestra di dialogo **Seleziona oggetti** selezionare l'opzione **Seleziona oggetti di database specifici** e quindi selezionare table(or tables) che si desidera eseguire la migrazione al server di destinazione.
![Procedura guidata di migrazione del Database di SQL - seleziona oggetti](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Nella pagina **Riepilogo guidata Script** , fare clic su **Sì** quando verrà richiesto se si è pronti per generare uno script SQL. È inoltre possibile salvare lo Script TSQL per successivi utilizzi.
![Procedura guidata di migrazione del Database di SQL - riepilogo guidata Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Nella pagina **Riepilogo dei risultati** fare clic su **Avanti**.
![Procedura guidata di migrazione del Database di SQL - riepilogo dei risultati](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Nella pagina **Connessione al Server destinazione il programma di installazione** fare clic su **Connetti a Server**e quindi immettere i dettagli come indicato di seguito:
    - **Nome del server**: istanza server di destinazione
    - **Autenticazione**: **autenticazione di SQL Server**. Immettere le credenziali di accesso.
    - **Database**: **DB schema (elencare tutti i database)**. Questa opzione Elenca tutti i database nel server di destinazione.

    ![Creazione guidata la migrazione del Database di SQL - connessione al Server destinazione configurazione](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Fare clic su **Connetti**, selezionare il database di destinazione che si desidera spostare la tabella e quindi fare clic su **Avanti**. Questa operazione deve terminare esecuzione di script generato in precedenza e verrà visualizzata la tabella appena spostata copiata nel database di destinazione.

## <a name="verification-step"></a>Fase di verifica
1. Query e testare la tabella appena copiata per assicurarsi che i dati sono invariati. Dopo la conferma, è possibile eliminare il modulo tabella rinominato sezione **operazioni di preparazione** . (ad esempio &lt;nome tabella&gt;suffisso old).

## <a name="next-steps"></a>Passaggi successivi

[Backup automatici di Database SQL](sql-database-automated-backups.md)
