<properties
    pageTitle="Modificare il livello di livello e le prestazioni del servizio di un database SQL Azure | Microsoft Azure"
    description="Modificare il livello di servizio e livello di prestazioni di un database di SQL Azure viene illustrato come ridimensionare il database SQL verso l'alto o verso il basso. Modificare il livello di prezzo di un database di SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL tramite il portale di Azure


> [AZURE.SELECTOR]
- [**Portale di Azure**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Livelli di servizio e livelli di prestazioni descrivono le caratteristiche e le risorse disponibili per il database SQL e possono essere aggiornati come le esigenze di apportare modifiche all'applicazione. Per informazioni dettagliate, vedere [Livelli di servizio](sql-database-service-tiers.md).

Si noti che la modifica del livello di servizio e/o il livello di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni e poi passa connessioni alla replica. Dati non andranno persi durante questo processo ma durante il breve attimo quando si passa la replica sono disabilitate connessioni al database, quindi alcune operazioni in volo possono essere riportati. Questa finestra varia, ma è in Media in 4 secondi e in oltre il 99% dei casi è inferiore a 30 secondi. Molto raramente, soprattutto se sono presenti grandi sono disabilitati i numeri delle transazioni in volo in connessioni momento, questa finestra può contenere più.  

La durata dell'intero processo scalabilità dipende dal livello service e dimensione del database prima e dopo la modifica. Ad esempio un database di 250 GB in fase di modifica, da o all'interno di un livello di servizio Standard, deve completare 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, è necessario completare all'interno di 3 ore.


Usare le informazioni in [livelli del servizio di Database SQL Azure e livelli di prestazioni](sql-database-service-tiers.md) per determinare il livello di livello e le prestazioni di servizio appropriato per il Database di SQL Azure.

- Per eseguire il downgrade un database, il database deve essere inferiore alle dimensioni massime consentite del livello di servizio di destinazione. 
- Quando si aggiorna un database con [Replica geografico](sql-database-geo-replication-overview.md) abilitato, è necessario aggiornare prima di tutto i database secondari al livello di prestazioni desiderato prima dell'aggiornamento del database primario.
- Quando il downgrade un livello di servizio, è prima di tutto necessario terminare tutte le relazioni di replica geografico. 
- Le offerte di servizi di ripristino sono diverse per i vari livelli di servizio. Se si downgrade si potrebbero perdere la possibilità di ripristinare in un punto nel tempo o hanno un periodo di conservazione backup inferiore. Per ulteriori informazioni, vedere [Backup del Database SQL Azure e ripristino](sql-database-business-continuity.md).
- Modifica del database prezzi livello non cambia la dimensione massima del database. Per modificare il database di dimensioni massime di utilizzare [Transact-SQL (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Le nuove proprietà per il database non vengono applicate finché non sono state completate le modifiche.



**Per completare in questo articolo è necessario quanto segue:**

- Un database di SQL Azure. Se non si dispone di un database SQL, creare una seguendo i passaggi descritti in questo articolo: [creare il primo Database di SQL Azure](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Modificare il livello di livello e le prestazioni di servizio del database


Aprire e il Database di SQL per il database che si desidera ridimensionare verso l'alto o verso il basso:

1.  Nel [portale di Azure](https://portal.azure.com), fare clic su **altri servizi** > **database SQL**.
2.  Fare clic sul database che si desidera modificare.
3.  Scegliere **il livello di prezzo (scala DTUs)**e il **database di SQL** :

    ![prezzi livello][1]

1.  Scegliere un nuovo livello e fare clic su **Seleziona**:

    Fare clic su **Selezionare** invia una richiesta di scala per modificare il livello dei prezzi. A seconda delle dimensioni del database operazione di ridimensionamento può richiedere del tempo di completamento (vedere le informazioni nella parte superiore di questo articolo).

    > [AZURE.NOTE] Modifica del database prezzi livello non cambia la dimensione massima del database. Per modificare il database di dimensioni massime di utilizzare [Transact-SQL (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Selezionare il livello prezzo][2]

3.  Fare clic sull'icona di notifica (campanello) nell'angolo superiore destro:

    ![notifiche][3]

    Fare clic sul testo di notifica per aprire il riquadro dei dettagli in cui è possibile visualizzare lo stato della richiesta.




## <a name="next-steps"></a>Passaggi successivi

- Modificare le dimensioni massime di database utilizzando [Transact-SQL (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Ridimensionare e in](sql-database-elastic-scale-get-started.md)
- [Connettere e query in un database SQL di SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Esportare un database SQL Azure](sql-database-export.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- [Documentazione di Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
