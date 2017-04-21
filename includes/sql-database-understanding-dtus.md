Il Database transazione unità (DTU) è l'unità di misura di Database SQL che rappresenta la potenza di database in base a una misura reali relativa: transazione del database. Si ha un insieme di operazioni che sono tipici per una transazione online elaborazione richiesta (OLTP) e quindi misurato quante transazioni essere completate in un secondo completamente caricato condizioni (la versione breve, è possibile leggere i dettagli esplorarne nella [Panoramica Benchmark](../articles/sql-database/sql-database-benchmark-overview.md)). 

Ad esempio, un database P11 Premium con 1750 DTUs fornisce 350 x DTU ulteriori calcolare power rispetto a un database di base con 5 DTUs. 

![Introduzione al Database SQL: singolo DTUs database dal livello e livello.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Se si esegue la migrazione di un database di SQL Server esistente, è possibile utilizzare uno strumento di terze parti, [La Calcolatrice di DTU Database SQL Azure](http://dtucalculator.azurewebsites.net/), per ottenere una stima del livello di prestazioni e fornire assistenza livello che del database potrebbe richiedere nel Database di SQL Azure.

### <a name="dtu-vs-edtu"></a>DTU e eDTU

DTU per i database singoli ruolo direttamente eDTU per i database flessibile. Ad esempio un database in un pool di base sul database flessibile offre eDTUs fino a 5. Ecco le stesse prestazioni come un unico database di base. La differenza è che il database flessibile non è possibile utilizzare qualsiasi eDTUs dal pool di fino a quando non è necessario. 

![Introduzione al Database SQL: pool flessibile dal livello.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Consente di un semplice esempio. Eseguire un pool di base sul database flessibile con 1000 DTUs e rilasciare 800 database al suo interno. Come solo 200 di 800 database in uso in qualsiasi punto nel tempo (5 DTU X 200 = 1000), non è possibile premere capacità del pool di e non è possibile ridurre le prestazioni del database. In questo esempio è stato semplificato per motivi di chiarezza. Matematiche reale è un po' più complesso. Il portale esegue le operazioni matematiche per l'utente e rende un suggerimento in base all'utilizzo di database cronologico. Vedere la sezione [considerazioni di prezzo e prestazioni di un pool di database flessibile](../articles/sql-database/sql-database-elastic-pool-guidance.md) per informazioni su come usare i suggerimenti o per eseguire le operazioni matematiche se stessi. 
