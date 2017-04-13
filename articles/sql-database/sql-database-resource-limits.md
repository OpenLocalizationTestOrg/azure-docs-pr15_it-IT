<properties
    pageTitle="Limiti di risorse del Database SQL Azure"
    description="Questa pagina illustra alcuni limiti delle risorse comuni per il Database di SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Limiti delle risorse di Database SQL Azure

## <a name="overview"></a>Panoramica

Database SQL Azure gestisce le risorse disponibili per un database utilizzando due diversi meccanismi: **Risorse Governance** e **Imposizione di limiti**. In questo argomento illustra questi due aree principali di gestione delle risorse.

## <a name="resource-governance"></a>Governance delle risorse
Uno degli obiettivi della progettazione dei livelli di servizio Basic, Standard e Premium riguarda il Database di SQL Azure si comporta come se il database è in esecuzione nel proprio computer, completamente isolato da altri database. Risorse emula il comportamento. Se l'utilizzo delle risorse aggregato raggiunge il numero massimo di CPU disponibile, risorse di memoria, i/o Log e i/o dati assegnate al database, governance delle risorse verrà accodare query in esecuzione e assegnare risorse alle query in coda gratuitamente su.

Come su un computer dedicato che utilizzano tutte le risorse disponibili comporterà un'esecuzione più di esecuzione query, causando timeout di comando nel client. Applicazioni con una logica riprova aggressive e che eseguono query sul database con una frequenza alta possono verificarsi errori messaggi quando si tenta di eseguire nuove query quando è stato raggiunto il limite di richieste simultanee.

### <a name="recommendations"></a>Suggerimenti:
Monitorare l'utilizzo delle risorse, nonché i tempi di risposta medio di query quando si avvicina l'utilizzo massimo di un database. In presenza di maggiore latenza di query sono in genere tre opzioni:

1.  Ridurre la quantità di richieste in arrivo per il database per evitare che timeout e pila backup delle richieste.

2.  Assegnare un livello di prestazioni superiore al database.

3.  Ottimizzare le query per ridurre l'utilizzo delle risorse di ogni query. Per ulteriori informazioni, vedere la sezione Query regolazione/Hinting nell'articolo indicazioni sulle prestazioni di Database SQL Azure.

## <a name="enforcement-of-limits"></a>Applicazione dei limiti
Risorse diversa da quella della CPU, memoria, i/o Log e i/o dati vengono applicate da negare nuove convocazioni quando vengono raggiunti i limiti. Client verranno visualizzato un [messaggio di errore](sql-database-develop-error-messages.md) a seconda che è stato raggiunto il limite di.

Ad esempio, il numero di connessioni a un database SQL e il numero di richieste simultanee che possono essere elaborati è limitato. Database SQL di consente il numero di connessioni al database per essere maggiore del numero di richieste simultanee per supportare il pool di connessioni. Mentre è possibile controllare la quantità di connessioni disponibili agevolmente dall'applicazione, la quantità di richieste parallele è spesso volte più difficile stimare e controllare. In particolare durante carichi di picco se l'applicazione di uno invia troppe richieste o il database raggiunge la risorsa limiti e inizia accumulare un thread a causa di query più lunghe, è possano verificati errori.

## <a name="service-tiers-and-performance-levels"></a>Livelli di servizio e livelli di prestazioni

Sono disponibili i livelli di servizio e livelli di prestazioni per i pool di database ed elastici entrambi autonomo.

### <a name="standalone-databases"></a>Database autonomo

Per un database autonomo i limiti di un database sono definiti dal livello di livello e le prestazioni del servizio di database. Nella tabella seguente descrive le caratteristiche di base, Standard e Premium database a diversi livelli di prestazioni.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pool flessibile

[Pool flessibile](sql-database-elastic-pool.md) Condividi risorse tra i database nel pool. Nella tabella seguente descrive le caratteristiche di base, Standard e Premium pool database flessibile.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Per una definizione espansa di ogni risorsa elencata nella tabella precedente, vedere le descrizioni dei [limiti](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)e funzionalità di servizi di livello. Per una panoramica dei livelli di servizio, vedere [livelli di servizio di Database SQL Azure e livelli di prestazioni](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Altri limiti del Database SQL

| Area | Limite | Descrizione |
|---|---|---|
| Esportare i database che utilizzano automatico per abbonamento | 10 | Esportazione automatica consente di creare una pianificazione personalizzata per il backup dei database SQL. Per ulteriori informazioni, vedere [database SQL: supporto per l'esportazione di Database SQL automatico](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Database per server | Fino a 5000 | Fino a 5000 database sono consentiti per server su server V12. |  
| DTUs per server | 45000 | 45000 DTUs sono disponibili per server su V12 server di provisioning di database, pool flessibile e data warehouse. |



## <a name="resources"></a>Risorse

[Limiti di servizio, Azure sottoscrizione e le quote e vincoli](../azure-subscription-service-limits.md)

[Livelli di servizio di Database SQL Azure e livelli di prestazioni](sql-database-service-tiers.md)

[Messaggi di errore per i programmi di Database SQL client](sql-database-develop-error-messages.md)
