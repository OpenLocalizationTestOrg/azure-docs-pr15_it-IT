<properties
   pageTitle="Domande frequenti tramonto Business Edition e Web di Database SQL Azure | Microsoft Azure"
   description="Scoprire quando i database Web di SQL Azure e Business ritiro e informazioni sulle caratteristiche e funzionalità dei livelli di servizio nuovo."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>Domande frequenti su tramonto Web e Business Edition

A questo punto vengano ritirati Azure SQL Web nei database aziendali. I livelli Basic, Standard, Premium ed elastici sostituiranno i database Web e Business ritirare.

Per agevolare la con l'aggiornamento dei database Web e Business, il servizio di Database SQL consiglia un appropriato livello e prestazioni livello di servizio (prezzi livello) per ogni database in base al carico di lavoro Cronologia.

**Per ottenere prezzi consigli livello:**

- [Eseguire l'aggiornamento a SQL Database V12 tramite il portale di Azure](sql-database-upgrade-server-portal.md)
- [Eseguire l'aggiornamento a SQL Database V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)
- [Modificare il livello di prezzo di un database Web o Business](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>Perché il portale di Azure viene visualizzato i database edition Web e Business come ritirata?

Perché Web e Business database edition non saranno più disponibili dopo settembre 2015, il portale di etichette Web nei database aziendali come ritirata. L'etichetta ritirato vengono forniti anche un promemoria che tutti i database Web e Business devono essere aggiornati a Standard, Basic o Premium. Per informazioni dettagliate sull'aggiornamento di database Web o Business esistenti ai nuovi livelli di servizio, vedere [eseguire l'aggiornamento a V12 di Database SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>Il nuovo livello di servizio è la scelta migliore per aggiornare i database Web o Business esistente da?

Selezione di un nuovo servizio livello e prestazioni livello appropriato per il database Web o commerciale esistente dipende particolari caratteristiche e alle prestazioni di base per l'applicazione.

Utilizzare i suggerimenti di livello prezzi descritti in precedenza, oppure per informazioni dettagliate per facilitare la scelta di un nuovo livello di servizio appropriato, vedere [eseguire l'aggiornamento a V12 di Database SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Perché Microsoft sono stati introdotti nuovi livelli di servizio?

In base al feedback dei clienti, Database SQL Azure sono stati introdotti nuovi livelli di servizio per assistenza con i clienti più facilmente carichi di lavoro di database relazionale. Livelli di nuovo sono progettati per offrire prestazioni prevedibili in spettro di sette livelli per leggera alle richieste di applicazione transazione grave. Inoltre, i livelli di nuovi offrono delle caratteristiche di continuità aziendale, contratto di servizio un tempo più complessa, di dimensioni più grandi database per ridotti e una migliore esperienza di fatturazione.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>Dove sono disponibili informazioni sui livelli di servizio nuovo?

Per informazioni dettagliate sui livelli di servizio nuovo modello di prestazioni, vedere [livelli di servizio](sql-database-service-tiers.md). Per dettagliate prezzi informazioni relative ai nuovi livelli di servizio, vedere [Database SQL di prezzi](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>Quali funzionalità non saranno disponibile in Basic, Standard e Premium?

La caratteristica federazione ritiro con le versioni Web e Business. I clienti che hanno l'esigenza di scalabilità i database si consiglia di utilizzare invece o eseguire la migrazione a [Strumenti database flessibile](sql-database-elastic-scale-get-started.md) per [Il Database di SQL Azure](sql-database-elastic-scale-get-started.md), che semplifica la creazione e gestione di un'applicazione che utilizza sharding. Una raccolta di client .NET consente di definire come dati siano associati alle richieste OLTP shards e indirizza a database appropriati. Per supportare le operazioni di riconfigurare come dati sono distribuiti su shards gestione, un modello di servizio cloud Azure è incluso che è possibile ospitare nell'abbonamento Azure. Oltre a [Strumenti database flessibile](sql-database-elastic-scale-get-started.md)Microsoft continuerà a creare e pubblicare [indicazioni di modelli e procedure consigliate sharding personalizzato](https://msdn.microsoft.com/library/azure/dn764977.aspx) in base alle informazioni provenienti da impegni cliente complete. Nuovi clienti che hanno l'esigenza scalabilità funzionalità devono estrarre [Strumenti database flessibile](sql-database-elastic-scale-get-started.md) e/o contattare il supporto Microsoft per valutare le relative opzioni.

Microsoft sta modificando anche esperienza copia del database con i database Premium. In precedenza quota database premium era limitata, creare DATABASE... Una copia in Transact-SQL creato un database sospesa Premium senza le risorse riservate che è stato addebitato con la stessa velocità come un database di Business. Come quota premium è ora disponibile più gratuitamente, sospesa Premium non è più supportata. Copia del database verrà ora creato con la stessa edizione e il livello di prestazioni come origine e per la fatturazione di conseguenza. I clienti possono scegliere di eseguire il downgrade copiati database a un livello di livello o le prestazioni di servizi diversa per ridurre i costi, se lo si desidera. Database sospesa Premium esistenti vengono convertiti in Business edition come parte di questa versione. Si prevede che l'introduzione di [Ripristinare In un momento](sql-database-recovery-using-backups.md#point-in-time-restore) ridurrà la necessità di copie di backup del database.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>Come Basic, Standard e Premium migliorare la mia esperienza fatturazione?

Base, Standard, database SQL Azure Premium vengono addebitati per ora e avere la possibilità di scalare ogni database verso l'alto o verso il basso. Vengono addebitate un tasso fisso in base al più alto livello di livello e le prestazioni del servizio che scelto per ogni ora. Inoltre, i livelli di prestazioni (esempio: Basic, S1 e P2) non vengono visualizzati nella fattura per rendere più semplice visualizzare il numero di giorni di database/ore sostenuti in un solo mese per ogni livello di prestazioni. Database Web e Business continuano a sarà effettuata l'utilizzo di unità di Database in base alle dimensioni del database. Visitare la pagina [Database SQL di prezzi pagina](https://azure.microsoft.com/pricing/details/sql-database/) per altre informazioni sui prezzi e le differenze tra i livelli di servizio nuovo.


## <a name="see-also"></a>Vedere anche

[Database SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Web e prezzi Business](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Livelli di servizio](sql-database-service-tiers.md)

[Eseguire l'aggiornamento a V12 di Database SQL Azure](sql-database-upgrade-server-portal.md)
