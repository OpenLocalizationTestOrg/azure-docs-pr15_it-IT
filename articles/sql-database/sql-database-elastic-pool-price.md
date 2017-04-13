<properties
    pageTitle="Prestazioni e il prezzo del pool flessibile Database SQL"
    description="Informazioni sui prezzi specifiche per i pool di database flessibile."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Database flessibile pool fatturazione e informazioni sui prezzi

Pool di database flessibile vengono addebitate per le caratteristiche seguenti:

- Un pool flessibile viene effettuato quando viene creato, anche quando non esistono database nel pool.
- Un pool flessibile viene effettuato ogni ora. Verrà visualizzata la stessa frequenza di controllo delle licenze per i livelli di prestazioni di singoli database.
- Se un pool flessibile viene ridimensionato per un nuovo importo eDTUs, quindi il pool non è effettuato in base alla nuova quantità di eDTUS fino al completamento dell'operazione di ridimensionamento. Questo segue dello stesso modello di modifica del livello di prestazioni dei database autonomo.


- Il prezzo di un pool flessibile è in base al numero di eDTUs del pool. Il prezzo di un pool flessibile è indipendente dal numero e l'utilizzo dei database flessibile all'interno di essa.
- Prezzo viene calcolato (numero di pool eDTUs) x (prezzo unitario per eDTU).

Prezzo unitario eDTU per un pool flessibile è superiore al prezzo unitario DTU per un database autonomo nello stesso livello di servizio. Per informazioni dettagliate, vedere [Database SQL di prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 


Per comprendere i livelli eDTUs e servizio, vedere [prestazioni e le opzioni del Database di SQL](sql-database-service-tiers.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un pool di database flessibile](sql-database-elastic-pool-create-portal.md)
- [Monitorare, gestire e ridimensionare un pool di database flessibile](sql-database-elastic-pool-manage-portal.md)
- [Opzioni di Database SQL e prestazioni: informazioni sulle funzionalità disponibili in ogni livello di servizio](sql-database-service-tiers.md)
- [Script di PowerShell per identificare i database adatti per un pool di database flessibile](sql-database-elastic-pool-database-assessment-powershell.md)
