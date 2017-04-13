<properties
    pageTitle="Documentazione per la pubblica amministrazione Azure | Microsoft Azure"
    description="In questo modo un confronto delle caratteristiche e indicazioni sullo sviluppo di applicazioni per la pubblica amministrazione Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Database per la pubblica amministrazione Azure

##  <a name="sql-database"></a>Database SQL

Per ulteriori istruzioni sulla configurazione di visibilità dei metadati e le procedure consigliate di protezione, consultare il<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centro protezione di Microsoft per motore di Database SQL</a> e [Documentazione pubblica relativa al Database SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/) .

### <a name="variations"></a>Varianti

Database SQL di V12 è generalmente disponibile in Azure per enti pubblici.

L'indirizzo per i server Azure SQL Azure Government è diverso:

Tipo di servizio|Pubblica Azure|Azure per la pubblica amministrazione
---|---|---
Database SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considerazioni

Le informazioni seguenti identificano il limite per la pubblica amministrazione Azure per SQL Azure:

| Dati regolata/controllati consentiti | Dati regolata/controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati archiviati ed elaborati in Microsoft Azure SQL possono contenere dati regolata per enti pubblici Azure. È necessario utilizzare strumenti database per il trasferimento di dati di Azure Government regolata dati. | Metadati SQL Azure non può contenere dati di esportazione. Metadati includono tutti i dati di configurazione immessi durante la creazione e gestione del prodotto lo spazio di archiviazione.  Non richiesta l'immissione di dati regolata controllati nei campi seguenti: nome, nome dell'abbonamento, gruppi di risorse, nome del Server, accesso amministrazione Server, i nomi di distribuzione, i nomi delle risorse, tag di risorse del Database

## <a name="azure-redis-cache"></a>Cache Redis Azure

Per informazioni dettagliate su questo servizio e su come utilizzarla, vedere [documentazione pubblica Azure Redis Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Varianti

URL di accesso e la gestione di Azure Redis Cache per enti pubblici Azure sono diverse:

Tipo di servizio|Pubblica Azure|Azure per la pubblica amministrazione
---|---|---
Endpoint cache|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Portale di Azure|https://Portal.Azure.com|https://Portal.Azure.us

>[AZURE.NOTE] Tutti gli script e codice devono a prendere in considerazione gli ambienti ed endpoint appropriati. Per ulteriori informazioni, vedere [come connettersi a Azure Government Cloud](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


### <a name="considerations"></a>Considerazioni

Le informazioni seguenti consente di identificare il limite per la pubblica amministrazione Azure per Azure Redis Cache:

| Dati regolata/controllati consentiti | Dati regolata/controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati archiviati ed elaborati nella Cache Redis Azure possono contenere dati regolata per enti pubblici Azure. | Azure Redis i metadati della Cache non può contenere dati di esportazione. Non richiesta l'immissione di dati regolata controllati nei campi seguenti: memorizzare nella Cache nome, nome VNET, nome dell'abbonamento, gruppi di risorse, tag di risorse, proprietà Redis.  

##  <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni e aggiornamenti sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
