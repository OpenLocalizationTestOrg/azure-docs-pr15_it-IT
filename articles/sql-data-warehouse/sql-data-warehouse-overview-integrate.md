<properties
   pageTitle="Creazione di soluzioni integrate con SQL Data Warehouse | Microsoft Azure"
   description="Strumenti e partner con soluzioni che si integrano con SQL Data Warehouse. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Sfruttare altri servizi con SQL Data Warehouse
Oltre a funzionalità principale SQL Data Warehouse consente agli utenti di sfruttare molti altri servizi in Azure insieme a essa.  In particolare, abbiamo adottato attualmente passaggi per l'integrazione eccessivamente con le operazioni seguenti:

+ Power BI
+ Dati di Azure Factory
+ Apprendimento Azure
+ Flusso Azure Analitica

Stiamo lavorando per connettersi con altri servizi ecosistema Azure.

##<a name="power-bi"></a>Power BI
Integrazione di Power BI consente di sfruttare la potenza di elaborazione di SQL Data Warehouse con la creazione di report dinamici e visualizzazione di Power BI. Integrazione di Power BI attualmente include:

+ **Connessione diretta**: più avanzate connessione con impostare la logica contro SQL Data Warehouse.  In questo modo più veloce analisi su scala più grande.
+ **Apri in Power BI**: il pulsante 'Apri ripiegato' passa informazioni istanza di Power BI, consentendo per una connessione più semplice.

[Integrazione con Power BI](./sql-data-warehouse-integrate-power-bi.md) o la [documentazione di Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) per ulteriori informazioni, vedere.

##<a name="azure-data-factory"></a>Dati di Azure Factory
Azure Data Factory consente agli utenti una piattaforma gestita per creare pipeline estratto carico complesse.  Integrazione del SQL Data Warehouse con Azure Data Factory include le operazioni seguenti:

+ **Stored procedure**: progettare l'esecuzione di stored procedure in SQL Data Warehouse.
+ **Copia**: Usa alimentatore automatico per spostare i dati nell'archivio di dati SQL.  Questa operazione è possibile usare meccanismo di spostamento dei dati standard del alimentatore automatico o PolyBase dietro le quinte. 

Vedere [integrazione con Azure Data Factory](./sql-data-warehouse-integrate-azure-data-factory.md) o la [documentazione di Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) per altre informazioni.

##<a name="azure-machine-learning"></a>Apprendimento Azure
Apprendimento Azure è un servizio analitica completamente gestita che consente agli utenti di creare modelli più complessi sfruttando un ampio set di strumenti di previsione.  SQL Data Warehouse è supportato come origine e destinazione per i modelli con le funzionalità seguenti:

+ **Leggere i dati:** Unità modelli scala utilizzando T-SQL su SQL Data Warehouse.
+ **La scrittura dei dati:** Eseguire il commit delle modifiche da qualsiasi modello alla SQL Data Warehouse.

Vedere [integrazione con apprendimento Azure](./sql-data-warehouse-integrate-azure-machine-learning.md) o la [documentazione per l'apprendimento Azure](https://azure.microsoft.com/services/machine-learning/) per altre informazioni.

##<a name="azure-stream-analytics"></a>Flusso Azure Analitica
Azure flusso Analitica è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati di eventi generati dall'Hub evento Azure.  Integrazione con SQL Data Warehouse consente la trasmissione dei dati per elaborati e archiviati insieme a dati relazionali attivazione dell'analisi più approfondita, più avanzate in modo efficace.  

+ **Processo Output:** Inviare l'output della processi flusso Analitica direttamente nell'archivio di dati SQL.

[Integrazione con Azure flusso Analitica](./sql-data-warehouse-integrate-azure-stream-analytics.md) o la [documentazione di Azure flusso Analitica](https://azure.microsoft.com/documentation/services/stream-analytics/) per ulteriori informazioni, vedere.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
