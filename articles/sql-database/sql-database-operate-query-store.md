<properties
   pageTitle="Operativo archivio Query nel Database SQL Azure"
   description="Informazioni su come utilizzare l'archivio di Query di Database SQL Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Operativo l'archivio di Query di Database SQL Azure 

Archivio di query in Azure è una funzionalità di database completamente gestita che consente di raccogliere e vengono fornite informazioni dettagliate cronologiche informazioni su tutte le query continuamente. È possibile pensare sull'archivio Query simili alle registratore di dati di volo di aereo che in modo significativo semplifica la risoluzione dei problemi di entrambe le opzioni per cloud alle prestazioni delle query e i clienti in locale. In questo articolo vengono illustrati aspetti specifici di operativo archivio di Query in Azure. Usare questi dati pre-raccolti query, è possibile diagnosticare rapidamente e risolvere i problemi di prestazioni e pertanto dedicare più tempo sulla loro attività. 

Archivio di query è stato [disponibile a livello globale](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) nel Database di SQL Azure dal novembre 2015. Archivio di query sono alla base per l'analisi delle prestazioni e ottimizzazione delle caratteristiche, ad esempio [Dashboard prestazioni e preparazione di Database SQL](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Al momento della pubblicazione in questo articolo, Query archivio è in esecuzione in più di 200.000 database dell'utente in Azure, la raccolta di informazioni correlate alla query per diversi mesi, senza interruzioni.

> [AZURE.IMPORTANT] Microsoft è in corso l'attivazione di archivio di Query per tutti i database di SQL Azure (nuovi o esistenti). 

## <a name="optimal-query-store-configuration"></a>Configurazione ottimale dell'archivio di Query

In questa sezione descrive le impostazioni predefinite di configurazione ottimale progettati per garantire il funzionamento affidabile sulle Query Store e le caratteristiche dipendenti, ad esempio [Dashboard prestazioni e preparazione di Database SQL](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configurazione predefinita è ottimizzata per raccolta dati continua che minimo il tempo trascorso negli Stati OFF/READ_ONLY.

| Configurazione | Descrizione | Impostazione predefinita | Commento |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Specifica il limite per lo spazio di dati di archivio di Query può richiedere all'interno di database clienti z | 100 | Stabilite per i nuovi database |
| INTERVAL_LENGTH_MINUTES | Consente di definire dimensioni dell'intervallo di tempo durante il quale le statistiche di runtime raccolti per i piani di query sono aggregate e mantenute. Ogni piano query attiva include almeno una riga per un periodo di tempo definito con questa configurazione | 60   | Stabilite per i nuovi database |
| STALE_QUERY_THRESHOLD_DAYS | Criteri di pulizia basate sul tempo che controllano il periodo di conservazione delle statistiche di runtime persistente e query inattiva | 30 | Applicati a nuovi database e database con predefinito precedente (367) |
| SIZE_BASED_CLEANUP_MODE | Specifica se pulizia automatico dei dati viene eseguita quando dimensioni dei dati di archivio di Query si avvicina al limite | Automatico | Applicata per tutti i database |
| QUERY_CAPTURE_MODE | Specifica se vengono rilevate tutte le query o solo un sottoinsieme di query | Automatico | Applicata per tutti i database |
| FLUSH_INTERVAL_SECONDS | Specifica che durata massima acquisito runtime statistiche vengono mantenute in memoria, prima di trasferire su disco | 900 | Stabilite per i nuovi database |
||||||

> [AZURE.IMPORTANT] Queste impostazioni predefinite vengono applicate automaticamente nel passaggio finale dell'attivazione di archivio di Query in tutti i database di SQL Azure (vedere la precedente nota importante). Dopo questa ravvivare, Database SQL Azure non è possibile modificare valori di configurazione impostati dai clienti, a meno che non sono influire negativamente sulle operazioni affidabile dell'archivio di Query o carico di lavoro principale.

Se si vogliono mantenere con le impostazioni personalizzate, utilizzare [ALTER DATABASE con le opzioni di archiviazione di Query](https://msdn.microsoft.com/library/bb522682.aspx) per ripristinare configurazione allo stato precedente. Per informazioni su come scelta parametri di configurazione ottimale per superiore, vedere [Procedure consigliate con l'archivio di Query](https://msdn.microsoft.com/library/mt604821.aspx) .

## <a name="next-steps"></a>Passaggi successivi

[Comprendere le prestazioni di Database SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni il controllo gli articoli seguenti:

- [Un registratore di dati di volo per il database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Monitoraggio delle prestazioni utilizzando l'archivio di Query](https://msdn.microsoft.com/library/dn817826.aspx)

- [Scenari di utilizzo di archivio di query](https://msdn.microsoft.com/library/mt614796.aspx)

- [Monitoraggio delle prestazioni utilizzando l'archivio di Query](https://msdn.microsoft.com/library/dn817826.aspx) 
