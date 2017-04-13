<properties 
   pageTitle="Prezzi consigli livello per il Database di SQL Azure" 
   description="Quando si modificano i prezzi livelli nel portale di Azure, prezzi consigli livello vengono fornito consigliare il livello quello più adatto per l'esecuzione di carico di lavoro di esistente Azure Database SQL. Livelli prezzi descrivano il livello di livello e le prestazioni del servizio di un database SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>Consigli livello prezzi Database SQL

 Consigli livello prezzi suggeriscono il livello di livello e le prestazioni del servizio più adatto per l'esecuzione di carico di lavoro del database di SQL Azure un'esistente.

> [AZURE.NOTE] Consigli livello prezzi sono disponibili solo per i database Web e Business e pool di database flessibile - e disponibile solo nel [portale di Azure](https://portal.azure.com/).


Ottenere prezzi consigli livello durante le attività seguenti:

- [Modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL](sql-database-scale-up.md)
- [Aggiornare il server di SQL Azure a V12](sql-database-upgrade-server-portal.md)
- Passare al server V12. Vedere [Database SQL di prezzi consigli livello](sql-database-service-tier-advisor.md).
- [Creare un pool di database flessibile](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Panoramica

Il servizio di Database SQL analizza prestazioni correnti e i requisiti delle funzionalità valutando Uso risorse cronologici per un database SQL. Inoltre, il livello di servizio accettabile minimo viene determinato in base alle dimensioni del database e caratteristiche abilitate [continuità aziendale](sql-database-business-continuity.md) . 

Queste informazioni vengono analizzate e il livello di livello e le prestazioni del servizio quello più adatto per l'esecuzione di carico di lavoro tipico del database e la gestione che può risultare set di funzionalità corrente è consigliabile.

- Il servizio esamina i precedenti 15 e 30 giorni di dati cronologici (Uso risorse, le dimensioni del database e attività di database) ed esegue un confronto tra la quantità di risorse utilizzate e le limitazioni effettive dei livelli di servizio attualmente disponibili e livelli di prestazioni.
- Analisi dei dati in intervalli di 15 secondi e gruppo di risultati di ogni dell'intervallo è categorizzato nel livello di servizio esistente e livello di prestazioni quello più adatto per la gestione di carico di lavoro di tale gruppo di risultati.
- Questi 15 secondi esempi sono quindi aggregati nell'analisi 15-30 giorni più grande e consiglia il livello di livello e le prestazioni del servizio in grado di gestire in modo ottimale 95% del carico di lavoro Cronologia.

### <a name="recommendations"></a>Consigli

A seconda dell'utilizzo del database, sono disponibili attualmente 2 categorie di suggerimenti che possono essere rilevati:


| Suggerimento | Descrizione |
| :--- | :--- |
| Eseguire l'aggiornamento | Eseguire l'aggiornamento a un nuovo livello. |
| Non è disponibile | Un database richiede un carico di lavoro minimo o circa 35 giorni dell'attività. Non è sufficiente dati per fornire un suggerimento valido. |

## <a name="getting-pricing-tier-recommendations"></a>Guida prezzi consigli di livello

Ottenere prezzi consigli livello selezionando un database Web o commerciale esistente, fare clic su **tutte le impostazioni**e quindi fare clic su **livello prezzi (scala DTUs)**. (Consigli livello prezzi sono disponibili anche quando si [Aggiorna Azure SQL server per V12](sql-database-upgrade-server-portal.md).)

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Sfoglia** > **database SQL**.
4. Selezionare il database che si desidera visualizzare un suggerimento per e il **database SQL** :

    ![Selezionare il database][1]

5. Nella e database, selezionare **tutte le impostazioni** , quindi selezionare **il livello di prezzo (scala DTUs)**.


7. **Consigliato prezzi livelli** aprire nel punto in cui è possibile fare clic su livello suggerito e fare clic sul pulsante **Seleziona** per modificare a tale livello.

    ![Iscriversi per l'anteprima][4]

8. Facoltativamente, fare clic su **dettagli sull'utilizzo di visualizzazione** per aprire e il **Prezzo livello Recommendation dettagli** nel punto in cui è possibile visualizzare il livello consigliato per il database, un confronto delle caratteristiche tra i livelli di correnti e consigliati e un grafico di analisi di utilizzo delle risorse nella cronologia.

    ![Iscriversi per l'anteprima][5]



## <a name="summary"></a>Riepilogo

Consigli livello prezzi offrono un'esperienza automatica per la raccolta di dati di telemetria per ogni database di SQL e consigliare il servizio livello/prestazioni livello miglior in base alle esigenze dell'organizzazione le prestazioni effettive e requisiti delle funzionalità di un database. Scegliere **il livello di prezzo (scala DTUs)** e l'impostazioni per visualizzare prezzi consigli livello per i database Web e Business.



## <a name="next-steps"></a>Passaggi successivi

A seconda di dettagli del database specifico, eseguire un aggiornamento o downgrade in genere non avvenire immediatamente. Il portale fornirà le notifiche come le transizioni di database per il nuovo livello oppure è possibile monitorare lo stato degli aggiornamento eseguendo la visualizzazione di [sys.dm_operation_status (Database di SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) nel database master del Server di Database SQL.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
