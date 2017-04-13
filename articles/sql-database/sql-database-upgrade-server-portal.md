<properties
    pageTitle="Eseguire l'aggiornamento a V12 di Database SQL Azure tramite il portale di Azure | Microsoft Azure"
    description="Spiega come eseguire l'aggiornamento a V12 di Database SQL Azure compresa la procedura aggiornare i database Web e Business e come eseguire l'aggiornamento di un server V11 la migrazione dei database direttamente in un pool di database flessibile tramite il portale di Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Eseguire l'aggiornamento a V12 di Database SQL Azure tramite il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL Database V12 è la versione più recente consigliabile aggiornamento a V12 Database di SQL Server esistente.
SQL Database V12 sono inclusi molti [vantaggi rispetto alla versione precedente](sql-database-v12-whats-new.md) :

- Maggiore compatibilità con SQL Server.
- Prestazioni migliorate premium e nuovi livelli di prestazioni.
- [Pool di database flessibile](sql-database-elastic-pool.md).

In questo articolo vengono fornite istruzioni per l'aggiornamento esistente V11 Database di SQL Server e database a V12 di Database SQL.

Durante il processo di aggiornamento a V12 verrà illustrato come aggiornare i database Web e Business a un nuovo livello di servizio in modo che siano inclusi indicazioni per l'aggiornamento dei database Web e Business.

Inoltre, può essere più economico che l'aggiornamento a singole prestazioni (prezzi livelli) per i database singoli esecuzione della migrazione a un [pool di database flessibile](sql-database-elastic-pool.md) . Pool inoltre semplificano la gestione dei database perché è necessario gestire le impostazioni delle prestazioni per il pool anziché separatamente sulla gestione di livelli di prestazioni di singoli database. Se si dispone di più server database è consigliabile spostarli in stesso server e usufruire di suddivisione in un pool. È possibile [eseguire la migrazione database dal server V11 direttamente nel pool di database flessibile tramite PowerShell automatico](sql-database-upgrade-server-powershell.md). Utilizzare il portale per eseguire la migrazione V11 database in un pool ma nel portale è necessario avere già un server V12 per creare un pool. Indicazioni vengono fornite più avanti in questo articolo per creare il pool dopo l'aggiornamento del server, se si dispone di [database che possono trarre vantaggio da un pool](sql-database-elastic-pool-guidance.md).

Si noti che i database rimarrà connesso e continuare a lavorare nel corso dell'operazione di aggiornamento. Al momento della transizione effettiva per il nuovo livello di prestazioni temporaneo eliminazione delle connessioni al database può verificarsi per una durata molto piccola che è in genere circa 90 secondi, ma può essere quanto più 5 minuti. Se l'applicazione ha [temporaneo la gestione degli errori per interruzioni di connessione](sql-database-connectivity-issues.md) è sufficiente proteggere le connessioni interrotte al termine dell'aggiornamento.

L'aggiornamento a SQL Database V12 non può essere annullata. Dopo l'aggiornamento non è possibile ripristinare il server a V11.

Dopo l'aggiornamento a V12, [consigli livelli di servizio](sql-database-service-tier-advisor.md) e [le considerazioni sulle prestazioni del pool flessibile](sql-database-elastic-pool-guidance.md) non immediatamente sarà disponibile fino a quando il servizio è stata eseguita per valutare i carichi di lavoro nel nuovo server. Cronologia recommendation server V11 non si applica ai server V12 in modo che non viene mantenuta.

## <a name="prepare-to-upgrade"></a>Preparazione dell'aggiornamento

- **Aggiornare tutti i database Web e aziendali**: vedere [aggiornare tutti i database Web e Business](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) sezione o [monitorare e gestire un pool di database flessibile (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Revisione e sospendere la replica geografico**: se il database di SQL Azure è configurato per la replica geografico è necessario documentare la configurazione corrente e [interrompere la replica geografico](sql-database-geo-replication-portal.md#remove-secondary-database). Al termine dell'aggiornamento riconfigurare il database per la replica geografico.
- **Aprire le porte se si hanno clienti in una macchina virtuale Azure**: se il programma client si connette a SQL Database V12 mentre il client viene eseguito su una Azure macchine (), è necessario aprire gli intervalli di porta 11000 11999 e 14999 14000 nella macchina virtuale. Per informazioni dettagliate, vedere [porte per V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Avviare l'aggiornamento

1. Nella finestra di Sfoglia [portale Azure](https://portal.azure.com/) al server si desidera aggiornare selezionando **Sfoglia** > **SQL Server**e selezionando il server 2.0 è necessario eseguire l'aggiornamento.
2. Selezionare **l'Ultimo aggiornamento di Database SQL**, quindi selezionare **aggiornare il server**.

      ![Aggiorna server][1]

3. L'aggiornamento di un server per l'aggiornamento più recente di Database SQL è permanente e irreversibile. Per confermare l'aggiornamento, digitare il nome del server e fare clic su **OK**.

## <a name="upgrade-all-web-and-business-databases"></a>Aggiornare tutti i database Web e Business

Se il server include tutti i database Web o Business è necessario aggiornarli. Durante il processo di aggiornamento a SQL Database V12 si aggiorna tutti i database Web e Business a un nuovo livello di servizio.    

Per agevolare la con l'aggiornamento, il servizio di Database SQL consiglia un appropriato livello e prestazioni livello di servizio (prezzi livello) per ogni database. Il servizio consiglia il livello migliore per l'esecuzione di carico di lavoro del database esistente da analisi utilizzo cronologico per il database.

3. In e **l'aggiornare il server** selezionare ogni database da rivedere e selezionare prezzi consigliati livello per eseguire l'aggiornamento a. È sempre Sfoglia i livelli di prezzi disponibili e selezionare quello che si adatta meglio l'ambiente.


     ![database][2]


7. Dopo aver selezionato il livello suggerito verrà visualizzata e lo **scegliere il livello prezzo** nel punto in cui è possibile selezionare un livello e quindi fare clic sul pulsante **Seleziona** per modificare a livello. Selezionare un nuovo livello per ogni database Web o azienda.

    È importante tenere presente che il database SQL non sia bloccato in qualsiasi livello di livello o prestazioni servizio specifico, in modo che i requisiti di modifica al database è possibile modificare facilmente tra i livelli di servizio disponibili e livelli di prestazioni. Infatti, Basic, Standard e database di SQL Premium vengono addebitati per ora e avere la possibilità di ridimensionamento verso l'alto o verso il basso ogni database di 4 volte all'interno di un periodo di 24 ore.

    ![consigli][6]


Dopo che tutti i database nel server sono idonei si è pronti avviare l'aggiornamento

## <a name="confirm-the-upgrade"></a>Confermare l'aggiornamento

3. Quando tutti i database nel server sono idonei per l'aggiornamento è necessario **Digitare il nome del SERVER** per verificare che si desidera eseguire l'aggiornamento e quindi fare clic su **OK**.

    ![Verificare l'aggiornamento][3]


4. L'aggiornamento viene avviato e viene visualizzato lo stato di avanzamento in notifica. Viene avviato il processo di aggiornamento. A seconda dei dettagli dei database specifici l'aggiornamento a V12 può richiedere del tempo. Durante questo periodo rimarranno online tutti i database nel server ma server e azioni di gestione database saranno limitate.

    ![eseguire l'aggiornamento in corso][4]

    Al momento della transizione effettiva per il nuovo prestazioni livello temporaneo eliminazione delle connessioni al database può verificarsi per una durata molto piccola (in genere espressa in secondi). Se dispone di un'applicazione di gestione degli errori temporaneo (Riprova logica) per interruzioni di connessione è sufficiente proteggere le connessioni interrotte al termine dell'aggiornamento.

5. Al termine dell'operazione di aggiornamento e il **Più recente aggiornamento** verrà visualizzato **attivato**.

    ![V12 abilitata][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Spostare i database in un pool di database flessibile

Nel [portale di Azure](https://portal.azure.com/) individuare il server V12 e fare clic su **Aggiungi pool**.

- oppure -

Se viene visualizzato un messaggio **fare clic qui per visualizzare i pool di database flessibile consigliati per questo server**, fare clic su esso per creare facilmente un pool ottimizzate per i database del server. Per informazioni dettagliate, vedere [considerazioni di prezzo e prestazioni di un pool di database flessibile](sql-database-elastic-pool-guidance.md).

![Aggiungere pool a un server][7]

Seguire le istruzioni vedere l'articolo [creare un pool di database flessibile](sql-database-elastic-pool.md) per completare la creazione del pool.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Monitorare i database dopo l'aggiornamento a SQL Database V12

>[AZURE.IMPORTANT] Eseguire l'aggiornamento alla versione più recente di SQL Server Management Studio (SQL Server Management Studio) in modo da sfruttare le nuove funzionalità v12. [Scarica SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Dopo l'aggiornamento, monitoraggio attivo il database per assicurarsi che le applicazioni vengono eseguite le prestazioni desiderato e quindi ottimizzare le impostazioni in base alle esigenze.

Oltre a monitoraggio singoli database, è possibile monitorare database flessibile pool [Monitor, gestire e la dimensione di un pool di database flessibile con il portale di Azure](sql-database-elastic-pool-manage-portal.md) o con [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Dati consumo delle risorse:** Per i database di base, Standard e Premium dati consumo delle risorse sono disponibili tramite il [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV nel database utente. Questa DMV fornisce vicino informazioni in tempo reale risorsa consumo 15 granularità secondo ora precedente dell'operazione. Il consumo di percentuale DTU per un intervallo viene calcolato come consumi percentuale massima delle dimensioni della CPU, IO e registro. Ecco una query per calcolare il consumo di percentuale DTU Media più ora ultima:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Ulteriori informazioni monitoraggio:

- [Indicazioni di prestazioni di Database SQL azure per singoli database](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerazioni di prezzo e prestazioni di un pool di database flessibile](sql-database-elastic-pool-guidance.md).
- [Database di SQL Azure monitoraggio usando le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)




**Avvisi:** Impostare gli avvisi di' ' nel portale di Azure per ricevere una notifica quando il consumo DTU per un database aggiornato si avvicina a determinate alto livello. Gli avvisi di database possono essere il programma di installazione nel portale di Azure per diverse metriche di prestazioni come DTU, CPU, IO e registro. Individuare il database e selezionare **le regole di avviso** e **l'Impostazioni** .

Ad esempio, è possibile impostare un avviso di posta elettronica "DTU percentuale" se il valore percentuale DTU medio supera il 75% su 5 minuti. Per ulteriori informazioni su come configurare le notifiche di avviso, fare riferimento a [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .





## <a name="next-steps"></a>Passaggi successivi

- [Controlla consigli del pool e creare un pool](sql-database-elastic-pool-create-portal.md).
- [Modificare il livello di livello e le prestazioni di servizio del database](sql-database-scale-up.md).



## <a name="related-links"></a>Collegamenti correlati

- [Quali sono le novità in SQL Database V12](sql-database-v12-whats-new.md)
- [Pianificare e preparare l'aggiornamento a SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
