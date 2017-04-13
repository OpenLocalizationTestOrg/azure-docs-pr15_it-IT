<properties
    pageTitle="Raccolta di dati di Azure dello spazio di archiviazione in panoramica Log Analitica | Microsoft Azure"
    description="Risorse Azure possono scrivere i registri e metriche a un account di archiviazione Azure, spesso tramite diagnostica Azure. Log Analitica possibile indicizzare questi dati e verificare che supportano le ricerche."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>La raccolta di dati di archiviazione Azure in panoramica Analitica Log

Molte risorse Azure sono able possibile scrivere metriche e registri a un account di archiviazione Azure. Registro Analitica grado di utilizzare questi dati e rendono più semplice monitorare le risorse Azure.

Per scrivere lo spazio di archiviazione Azure una risorsa può utilizzare diagnostica Windows Azure o hanno tragitto per scrivere i dati. Questi dati possono essere scritti in diversi formati a uno dei seguenti percorsi:

+ Tabelle di Azure
+ Archivio blob Azure
+ EventHub

Registro Analitica supporta i servizi Azure che scrivono dati utilizzando [Azure registri diagnostici](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Inoltre, Log Analitica supporta altri servizi che generano registri e metriche in diversi formati e posizioni.  

>[AZURE.NOTE] Addebitate normale Azure velocità per lo spazio di archiviazione e le transazioni quando si invia diagnostica a un account di archiviazione e per quando Analitica Log legge i dati dal proprio account di archiviazione.

![Diagramma di archiviazione Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Risorse Azure supportate

Registro Analitica può raccogliere dati per le risorse di Azure seguenti:

| Tipo di risorsa | Log (diagnostiche categorie) | Soluzione Analitica log |
| --------------------------------------- | -------------------------------- | --------------- |
| Informazioni dettagliate sui applicazione | Disponibilità <br> Eventi personalizzati <br> Eccezioni <br> Richieste di <br> | Applicazione approfondimenti (Preview) |
| Gestione dell'API | | *Nessuno* (Anteprima) |
| Automazione <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (Preview) |
| Archivio di chiave <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (Preview) |
| Gateway di applicazioni <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (Preview) |
| Gruppo di sicurezza di rete <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (Preview) |
| Infrastruttura di servizio                          | ETWEvent <br> Evento operativo <br> Evento attore affidabile <br> Evento Service affidabile| ServiceFabric (Preview) |
| Macchine virtuali | Registro di sistema Linux <br> Eventi di Windows <br> Registro IIS <br> ETWEvent di Windows | *Nessuno* |
| Ruoli Web <br> Ruoli di lavoro | Registro di sistema Linux <br> Eventi di Windows <br> Registro IIS <br> ETWEvent di Windows | *Nessuno* |

>[AZURE.NOTE] Per il monitoraggio Azure macchine virtuali (Linux e Windows), è consigliabile installare l' [estensione macchine Virtuali Analitica Log](log-analytics-azure-vm-extension.md). L'agente vengono fornite informazioni dettagliate sui più approfondita nelle macchine virtuali rispetto a se si utilizza la diagnostica scritta allo spazio di archiviazione.

È possibile evitare OMS analizzare base voto la [pagina di commenti](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy)di altri registri e definirne la priorità.


- Vedere [analizzare Azure registri diagnostici utilizzando Analitica Log](log-analytics-azure-storage-json.md) per ulteriori informazioni su come Analitica Log leggibili da Azure servizi che supportano [Azure log diagnostici](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  - Archivio chiave Azure
  - Automazione Azure
  - Gateway di applicazioni
  - Gruppi di sicurezza di rete
- Vedere [archiviazione blob di utilizzo per IIS e di archiviazione per gli eventi](log-analytics-azure-storage-iis-table.md) per ulteriori informazioni su come Analitica Log possibile leggere i log per Azure servizi tale diagnostica di scrittura per lo spazio di archiviazione tabella o ai registri IIS scritti blob lo spazio di archiviazione, tra cui:
  - Infrastruttura di servizio
  - Ruoli Web
  - Ruoli di lavoro
  - Macchine virtuali


Approfondimenti applicazione è in anteprima privato e Usa esportazione continua nell'archiviazione blob. Per partecipare anteprima privato, contattare il team di Account Microsoft o fare riferimento a informazioni dettagliate sul [sito di commenti e suggerimenti](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Passaggi successivi

- [Analizzare Azure registri diagnostici utilizzando Analitica Log](log-analytics-azure-storage-json.md) per leggere i log da Azure servizi tale diagnostica di scrittura per l'archiviazione blob in formato JSON.
- [Archiviazione blob di utilizzo per IIS e di archiviazione per gli eventi](log-analytics-azure-storage-iis-table.md) per leggere i log per Azure servizi tale diagnostica di scrittura per lo spazio di archiviazione tabella o ai registri IIS scritti nell'archiviazione blob.
- [Abilitare le soluzioni](log-analytics-add-solutions.md) per approfondire i dati.
- [Utilizzare query di ricerca](log-analytics-log-searches.md) per analizzare i dati.
