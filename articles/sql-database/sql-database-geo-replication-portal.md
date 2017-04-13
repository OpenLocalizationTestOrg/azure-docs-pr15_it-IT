<properties 
    pageTitle="Configurare la replica geografico per Database di SQL Azure con il portale di Azure | Microsoft Azure" 
    description="Configurare la replica geografico per Database di SQL Azure tramite il portale di Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurare la replica geografico per Database di SQL Azure con il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-replication-overview.md)
- [Portale di Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

In questo articolo viene illustrato come configurare la replica di geografico Active per Database di SQL [Azure portal](http://portal.azure.com).

Per avviare failover con il portale di Azure, vedere [avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con il portale di Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Replica geografico attiva (leggibile dei secondari) è ora disponibile per tutti i database in tutti i livelli di servizio. In aprile 2017, il tipo secondario non leggibile ritiro e database non leggibile esistenti verranno aggiornati automaticamente a database secondari leggibili.

Per configurare replica geografico tramite il portale di Azure, è necessario le risorse seguenti:

- Un database SQL Azure - database primario che si desidera replicare in un'area geografica diversa.

## <a name="add-secondary-database"></a>Aggiungere database secondario

La procedura seguente crea un nuovo database secondario in una relazione di replica geografico.  

Per aggiungere un secondario, è necessario essere il proprietario della sottoscrizione o comproprietario. 

Il database secondario avrà lo stesso nome del database primario e avrà, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere un solo database o un database flessibile. Per ulteriori informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md).
Dopo il secondario creato, definito dati verranno avviata la replica dal database primario per il nuovo database secondario. 

> [AZURE.NOTE] Il comando non riesce se il database partner esiste già (ad esempio, in seguito terminare una relazione geografico replica precedente).

### <a name="add-secondary"></a>Aggiungere secondario

1. Nel [portale di Azure](http://portal.azure.com), individuare il database che si desidera configurare per la replica geografico.
2. Nella pagina database SQL, selezionare **Replica geografico**e quindi selezionare l'area per creare il database secondario. È possibile selezionare un'area diversa da quella l'area geografica che ospita i database principale, ma è consigliabile l' [area geografica accoppiata](../best-practices-availability-paired-regions.md) .

    ![configurare la replica geografico](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Selezionare o configurare il server e livello prezzo per il database secondario.

    ![configurare secondario](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Facoltativamente, è possibile aggiungere un database secondario a un pool di database flessibile:

 Per creare il database secondario in un pool, fare clic su **database flessibile pool** e selezionare un pool nel server di destinazione. Un pool deve esistere già nel server di destinazione come questo flusso di lavoro non viene creato un pool.

6. Fare clic su **Crea** per aggiungere secondario.
 
6. Verrà creato il database secondario e il processo di seeding inizia. 
 
    ![configurare secondario](./media/sql-database-geo-replication-portal/seeding0.png)

7. Una volta completato il processo di seeding, il database secondario verrà visualizzato il relativo stato.

    ![seeding completata](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Rimuovere database secondario

L'operazione in modo permanente termina replica al database secondario e il ruolo di secondario assume l'aspetto di un normale database di sola lettura. Se viene interrotta la connessione al database secondario, il comando ha avuto esito positivo ma viene ripristinata la verranno secondaria non diventa di sola lettura finché la connettività.  

1. Nel [portale di Azure](http://portal.azure.com), individuare il database principale in collaborazione replica geografico.
2. Nella pagina Database SQL selezionare **Replica geografico**.
3. Nell'elenco **dei secondari** , selezionare il database che si desidera rimuovere la relazione di replica geografico.
4. Fare clic su **Interrompi replica**.

    ![rimuovere secondario](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Fare clic su **Interrompi replica** apre una finestra di conferma pertanto fare clic su **Sì** per rimuovere il database della collaborazione geografico replica (impostato su un database di sola lettura non fa parte del processo di replica).


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulle Active geografico replica, vedere - [Replica di geografico Active](sql-database-geo-replication-overview.md)
- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)

