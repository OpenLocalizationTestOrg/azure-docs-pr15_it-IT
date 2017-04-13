<properties
    pageTitle="RBAC: Ruoli predefiniti | Microsoft Azure"
    description="In questo argomento viene incorporato in ruoli per controllo dell'accesso basato sui ruoli (RBAC)."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC: Ruoli predefiniti

Azure basato sui ruoli accesso controllo (RBAC) viene fornito con i ruoli predefiniti seguenti che possono essere assegnati a utenti, gruppi e servizi. Non è possibile modificare le definizioni di ruoli incorporati. Tuttavia, è possibile creare [ruoli personalizzati in Azure RBAC](role-based-access-control-custom-roles.md) per soddisfare le esigenze specifiche dell'organizzazione.

## <a name="roles-in-azure"></a>Ruoli di Azure

Nella tabella seguente vengono fornite brevi descrizioni dei ruoli predefiniti. Fare clic sul nome di ruolo ricoperto dai partecipanti per visualizzare l'elenco dettagliato delle **Azioni** e **notactions** per il ruolo. Proprietà **Azioni** specifica le azioni consentite sulle risorse Azure. Stringhe di azione è possono utilizzare caratteri jolly. La proprietà **notactions** specifica le azioni che sono escluso dalla azioni consentite.

>[AZURE.NOTE] Le definizioni di ruolo Azure evoluzione. In questo articolo è aggiornato come possibili, ma si saprà sempre le ultime definizioni di ruoli in Azure PowerShell. Utilizzare i cmdlet `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions` applicabile.

| Nome del ruolo | Descrizione |
| --------- | ----------- |
| [API Gestione servizio collaboratori](#api-management-service-contributor) | Gestire i servizi di gestione dell'API |
| [Applicazione approfondimenti componente collaboratori](#application-insights-component-contributor) | Possibile gestire i componenti di applicazioni approfondimenti |
| [Operatore di automazione](#automation-operator) | Avviare, interrompere, sospendere e riprendere i processi |
| [BizTalk collaboratori](#biztalk-contributor) | Gestire i servizi BizTalk |
| [ClearDB MySQL DB collaboratori](#cleardb-mysql-db-contributor) | Gestire i database ClearDB MySQL |
| [Per i collaboratori](#contributor) | Consente di gestire tutti gli elementi ad eccezione di access. |
| [Dati Factory collaboratori](#data-factory-contributor) | Creare e gestire dati factory e risorse figlio al loro interno. |
| [Utente esercitazioni DevTest](#devtest-labs-user) | Visualizzare tutti gli elementi e connettersi, inizio, riavvio e arresto macchine virtuali |
| [Zona DNS per i collaboratori](#dns-zone-contributor) | Consente di gestire i record e zone DNS |
| [Account DocumentDB collaboratori](#documentdb-account-contributor) | Gestire gli account DocumentDB |
| [Sistemi intelligente Account collaboratori](#intelligent-systems-account-contributor) | Gestire gli account di sistemi intelligente |
| [Collaboratori di rete](#network-contributor) | Gestire tutte le risorse di rete |
| [Nuovo Relic APM Account collaboratore](#new-relic-apm-account-contributor) | Gestire applicazioni e gestione delle prestazioni dell'applicazione Relic nuovo account |
| [Proprietario](#owner) | Gestire tutti gli elementi, tra cui l'accesso |
| [Utilità per la lettura](#reader) | Visualizzare tutti gli elementi, ma non è possibile apportare modifiche |
| [Redis Cache collaboratori](#redis-cache-contributor) | Possono gestire la cache Redis |
| [Utilità di pianificazione processo raccolte collaboratori](#scheduler-job-collections-contributor) | Gestire raccolte di processo di pianificazione |
| [Collaboratori di servizio di ricerca](#search-service-contributor) | Gestire i servizi di ricerca |
| [Gestione della protezione](#security-manager) | Gestire i componenti di protezione, criteri di sicurezza e macchine virtuali |
| [SQL DB collaboratori](#sql-db-contributor) | Gestire i database SQL, ma non i criteri di sicurezza |
| [Gestione di protezione SQL](#sql-security-manager) | Gestire i criteri relativi alla sicurezza di SQL Server e database |
| [SQL Server collaboratori](#sql-server-contributor) | Gestire SQL Server e database, ma non i criteri di sicurezza |
| [Spazio di archiviazione classica Account collaboratori](#classic-storage-account-contributor) | Gestire gli account di archiviazione classica |
| [Spazio di archiviazione Account collaboratori](#storage-account-contributor) | Gestire gli account di archiviazione |
| [Amministratore di accesso utente](#user-access-administrator) | Gestire l'accesso alle risorse di Azure |
| [Per i collaboratori classica macchina virtuale](#classic-virtual-machine-contributor) | Gestire classiche macchine virtuali, ma non la rete o lo spazio di archiviazione account virtuale a cui sono connessi |
| [Per i collaboratori macchina virtuale](#virtual-machine-contributor) | Gestire macchine virtuali, ma non la rete o lo spazio di archiviazione account virtuale a cui sono connessi |
| [Rete classica collaboratori](#classic-network-contributor) | Possono gestire reti virtuali classiche e gli indirizzi IP riservato |
| [Web piano collaboratori](#web-plan-contributor) | Gestire i piani web |
| [Sito Web per i collaboratori](#website-contributor) | Gestire siti Web, ma non i piani di web a cui sono connessi |

## <a name="role-permissions"></a>Autorizzazioni per i ruoli
Nelle tabelle seguenti vengono descrivano le autorizzazioni specifiche assegnate a ogni ruolo. Può trattarsi di **Azioni**, concedere le autorizzazioni e **NotActions**che limitarli.

### <a name="api-management-service-contributor"></a>API Gestione servizio collaboratori
Gestire i servizi di gestione dell'API

| **Azioni** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Creare e gestire i servizi di gestione dell'API |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="application-insights-component-contributor"></a>Applicazione approfondimenti componente collaboratori
Possibile gestire i componenti di applicazioni approfondimenti

| **Azioni** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.Insights/components/* | Creare e gestire i componenti approfondimenti |
| Microsoft.Insights/webtests/* | Creare e gestire i test web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="automation-operator"></a>Operatore di automazione
Avviare, interrompere, sospendere e riprendere i processi

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Automation/automationAccounts/jobs/read | Leggere l'automazione dei processi di account |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Riprendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Automazione di lettura flussi di lavoro di account |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Sospendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/write | Scrivere automazione processi di account |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/read | Leggere gli account di automazione |
| Microsoft.Automation/automationAccounts/runbooks/read | Leggere runbook di automazione |
| Microsoft.Automation/automationAccounts/schedules/read | Automazione di lettura contabili |
| Microsoft.Automation/automationAccounts/schedules/write | Scrivere automazione contabili |
| Microsoft.Insights/components/* | Creare e gestire i componenti approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="biztalk-contributor"></a>BizTalk collaboratori
Gestire i servizi BizTalk

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.BizTalkServices/BizTalk/* | Creare e gestire servizi BizTalk |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB collaboratori
Gestire i database ClearDB MySQL

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |
| successbricks.cleardb/Databases/* | Creare e gestire database ClearDB MySQL |

### <a name="contributor"></a>Per i collaboratori
Gestire tutti gli elementi ad eccezione di access

| **Azioni** ||
| ------- | ------ |
| * | Creare e gestire le risorse di tutti i tipi |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | Non è possibile eliminare i ruoli e le assegnazioni di ruolo |  
| Microsoft.Authorization/*/Write | Non è possibile creare ruoli e le assegnazioni di ruolo |

### <a name="data-factory-contributor"></a>Dati Factory collaboratori
Creare e gestire dati factory e risorse figlio al loro interno.

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.DataFactory/dataFactories/* | Creare e gestire dati factory e risorse figlio al loro interno. |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="devtest-labs-user"></a>Utente esercitazioni DevTest
Visualizzare tutti gli elementi e connettersi, inizio, riavvio e arresto macchine virtuali

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Compute/availabilitySets/read | Leggere le proprietà del set di disponibilità |
| Microsoft.Compute/virtualMachines/*/read | Leggere le proprietà di una macchina virtuale (dimensioni macchine Virtuali, lo stato di runtime, estensioni macchine Virtuali e così via) |
| Microsoft.Compute/virtualMachines/deallocate/action | Rilasciare macchine virtuali |
| Microsoft.Compute/virtualMachines/read | Leggere le proprietà di una macchina virtuale |
| Microsoft.Compute/virtualMachines/restart/action | Riavviare macchine virtuali |
| Microsoft.Compute/virtualMachines/start/action | Avviare macchine virtuali |
| Microsoft.DevTestLab/*/read | Leggere le proprietà di un ambiente di lavoro |
| Microsoft.DevTestLab/labs/createEnvironment/action | Creare un ambiente di lavoro |
| Microsoft.DevTestLab/labs/formulas/delete | Eliminare le formule |
| Microsoft.DevTestLab/labs/formulas/read | Leggere le formule |
| Microsoft.DevTestLab/labs/formulas/write | Aggiungere o modificare le formule |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Valutare i criteri di laboratorio |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Partecipare a un pool di indirizzi di carico bilanciamento back-end |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Partecipare a un servizio di bilanciamento del carico regola NAT in entrata |
| Microsoft.Network/networkInterfaces/*/read | Leggere le proprietà di un'interfaccia di rete (ad esempio tutto il bilanciamento che l'interfaccia di rete fa parte di) |
| Microsoft.Network/networkInterfaces/join/action | Partecipare a una macchina virtuale all'interfaccia di rete |
| Microsoft.Network/networkInterfaces/read | Leggere le interfacce di rete |
| Microsoft.Network/networkInterfaces/write | Scrivere le interfacce di rete |
| Microsoft.Network/publicIPAddresses/*/read | Leggere le proprietà di un indirizzo IP pubblico |
| Microsoft.Network/publicIPAddresses/join/action | Partecipare a un indirizzo IP pubblico |
| Microsoft.Network/publicIPAddresses/read | Per saperne di indirizzi IP di rete |
| Microsoft.Network/virtualNetworks/subnets/join/action | Partecipare a una rete virtuale |
| Microsoft.Resources/deployments/operations/read | Leggere le operazioni di distribuzione |
| Microsoft.Resources/deployments/read | Distribuzioni di lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Storage/storageAccounts/listKeys/action | Elenco dello spazio di archiviazione account tasti |

### <a name="dns-zone-contributor"></a>Zona DNS per i collaboratori
Gestire i record e DNS zone.

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/\*/leggere | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/\* | Creare e gestire le regole di avviso |
| Microsoft.Network/dnsZones/\* | Creare e gestire i record e zone DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere lo stato delle risorse |
| Microsoft.Resources/deployments/\* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/\* | Creare e gestire i ticket di supporto |


### <a name="documentdb-account-contributor"></a>Account DocumentDB collaboratori
Gestire gli account DocumentDB

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.DocumentDb/databaseAccounts/* | Creare e gestire gli account DocumentDB |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="intelligent-systems-account-contributor"></a>Sistemi intelligente Account collaboratori
Gestire gli account di sistemi intelligente

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.IntelligentSystems/accounts/* | Creare e gestire gli account di sistemi intelligente |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="network-contributor"></a>Collaboratori di rete
Gestire tutte le risorse di rete

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.Network/* | Creare e gestire le reti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="new-relic-apm-account-contributor"></a>Nuovo Relic APM Account collaboratore
Gestire applicazioni e gestione delle prestazioni dell'applicazione Relic nuovo account

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |
| NewRelic.APM/accounts/* | Creare e gestire gli account di gestione delle prestazioni di Relic nuova applicazione |

### <a name="owner"></a>Proprietario
Gestire tutti gli elementi, tra cui l'accesso

| **Azioni** ||
| ------- | ------ |
| * | Creare e gestire le risorse di tutti i tipi |

### <a name="reader"></a>Utilità per la lettura
Visualizzare tutti gli elementi, ma non è possibile apportare modifiche

| **Azioni** ||
| ------- | ------ |
| * / lettura | Leggere le risorse di tutti i tipi, ad eccezione di informazioni riservate. |

### <a name="redis-cache-contributor"></a>Redis Cache collaboratori
Possono gestire la cache Redis

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Cache/redis/* | Creazione e gestione cache Redis |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="scheduler-job-collections-contributor"></a>Utilità di pianificazione processo raccolte collaboratori
Gestire raccolte di processo di pianificazione

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Scheduler/jobcollections/* | Creare e gestire raccolte di processo |
| Microsoft.Support/* | Creare e gestire i ticket di supporto  |

### <a name="search-service-contributor"></a>Collaboratori di servizio di ricerca
Gestire i servizi di ricerca

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Search/searchServices/* | Creare e gestire i servizi di ricerca |
| Microsoft.Support/* | Creare e gestire i ticket di supporto  |

### <a name="security-manager"></a>Gestione della protezione
Gestire i componenti di protezione, criteri di sicurezza e macchine virtuali

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.ClassicCompute/*/read | Leggere le informazioni di configurazione classica calcolare macchine virtuali |
| Microsoft.ClassicCompute/virtualMachines/*/write | Scrivere configurazione per macchine virtuali |
| Microsoft.ClassicNetwork/*/read | Leggere informazioni di configurazione della rete classica  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Security/* | Creare e gestire i criteri e componenti di protezione |
| Microsoft.Support/* | Creare e gestire i ticket di supporto  |

### <a name="sql-db-contributor"></a>SQL DB collaboratori
Gestire i database di SQL ma non i criteri di sicurezza

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Letti ruoli e delle assegnazioni di ruolo |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Sql/servers/databases/* | Creare e gestire database SQL |
| Microsoft.Sql/servers/read | Leggere SQL Server |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Non è possibile modificare i criteri di controllo |
| Microsoft.Sql/servers/databases/auditingSettings/* | Non è possibile modificare le impostazioni di controllo |
| Microsoft.Sql/servers/databases/auditRecords/read  | Non è possibile leggere i record |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Non è possibile modificare i criteri di connessione |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Non è possibile modificare i dati masking criteri |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Non è possibile modificare i criteri degli avvisi di sicurezza |
| Microsoft.Sql/servers/databases/securityMetrics/* | Non è possibile modificare metriche di sicurezza |

### <a name="sql-security-manager"></a>Gestione di protezione SQL
Gestire i criteri relativi alla sicurezza di SQL Server e database

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura Microsoft |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Sql/servers/auditingPolicies/* | Creare e gestire i criteri di controllo di SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Creare e gestire i criteri di controllo di SQL server database |
| Microsoft.Sql/servers/databases/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL server database |
| Microsoft.Sql/servers/databases/auditRecords/read | Record di controllo di lettura |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Creare e gestire i criteri di connessione database SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Creare e gestire dati SQL server database masking criteri |
| Microsoft.Sql/servers/databases/read | Database SQL di lettura |
| Microsoft.Sql/servers/databases/schemas/read | Schemi di database SQL di lettura server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Colonne della tabella lettura SQL server database |
| Microsoft.Sql/servers/databases/schemas/tables/read | Tabelle di database SQL di lettura server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza SQL server database |
| Microsoft.Sql/servers/databases/securityMetrics/* | Creare e gestire le metriche di protezione del database SQL server |
| Microsoft.Sql/servers/read | Leggere SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Creare e gestire i criteri di avviso protezione SQL server |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="sql-server-contributor"></a>SQL Server collaboratori
Gestire SQL Server e database ma non i criteri di sicurezza

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura|
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Sql/servers/* | Creare e gestire SQL Server |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Non è possibile modificare i criteri di controllo di SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Non è possibile modificare le impostazioni di controllo di SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Non è possibile modificare i criteri di controllo di SQL server database |
| Microsoft.Sql/servers/databases/auditingSettings/* | Non è possibile modificare le impostazioni controllo di database SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read  | Non è possibile leggere i record |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Non è possibile modificare i criteri di connessione database SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Non è possibile modificare dati del database SQL server masking criteri |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Non è possibile modificare i criteri degli avvisi di sicurezza SQL server database |
| Microsoft.Sql/servers/databases/securityMetrics/* | Non è possibile modificare le metriche di protezione del database SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Non è possibile modificare i criteri degli avvisi di SQL server protezione |

### <a name="classic-storage-account-contributor"></a>Spazio di archiviazione classica Account collaboratori
Gestire gli account di archiviazione classica

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.ClassicStorage/storageAccounts/* | Creare e gestire gli account di archiviazione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="storage-account-contributor"></a>Spazio di archiviazione Account collaboratori
Possibile gestire gli account di archiviazione, ma non ha accesso alla loro.

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Tutte le autorizzazioni di lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.Insights/diagnosticSettings/* | Gestire le impostazioni di diagnostiche |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Storage/storageAccounts/* | Creare e gestire gli account di archiviazione |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="user-access-administrator"></a>Amministratore di accesso utente
Gestire l'accesso alle risorse di Azure

| **Azioni** ||
| ------- | ------ |
| * / lettura | Leggere le risorse di tutti i tipi, ad eccezione di informazioni riservate. |
| Microsoft.Authorization/* | Gestire l'autorizzazione |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="classic-virtual-machine-contributor"></a>Per i collaboratori classica macchina virtuale
Gestire macchine virtuali classiche ma non il virtuale rete o lo spazio di archiviazione account a cui sono connessi

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.ClassicCompute/domainNames/* | Creare e gestire i nomi di dominio calcolo classica |
| Microsoft.ClassicCompute/virtualMachines/* | Creare e gestire macchine virtuali |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Partecipare a gruppi di sicurezza di rete |
| Microsoft.ClassicNetwork/reservedIps/link/action | Creare un collegamento IP riservato |
| Microsoft.ClassicNetwork/reservedIps/read | Lettura riservato indirizzi IP |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Partecipare a reti virtuali |
| Microsoft.ClassicNetwork/virtualNetworks/read | Leggere le reti virtuali |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Leggere dischi account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/images/read | Leggere le immagini di account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenco dello spazio di archiviazione account tasti |
| Microsoft.ClassicStorage/storageAccounts/read | Leggere gli account di archiviazione classica |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="virtual-machine-contributor"></a>Per i collaboratori macchina virtuale
Gestire macchine virtuali ma non il virtuale rete o lo spazio di archiviazione account a cui sono connessi

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.Compute/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
| Microsoft.Compute/locations/* | Creare e gestire posizioni di calcolo |
| Microsoft.Compute/virtualMachines/* | Creare e gestire macchine virtuali |
| Microsoft.Compute/virtualMachineScaleSets/* | Creare e gestire set scala macchina virtuale |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Partecipare a rete gateway back-end indirizzo pool di applicazioni |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Partecipare a pool di indirizzi di carico bilanciamento back-end |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Partecipare di bilanciamento del carico in ingresso pool NAT |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Partecipare di bilanciamento del carico NAT regole connessioni in entrata |
| Microsoft.Network/loadBalancers/read | Servizi di bilanciamento del carico lettura |
| Microsoft.Network/locations/* | Creare e gestire percorsi di rete |
| Microsoft.Network/networkInterfaces/* | Creare e gestire le interfacce di rete |
| Microsoft.Network/networkSecurityGroups/join/action | Partecipare a gruppi di sicurezza di rete |
| Microsoft.Network/networkSecurityGroups/read | Leggere i gruppi di sicurezza di rete |
| Microsoft.Network/publicIPAddresses/join/action | Partecipare a indirizzi IP di rete |
| Microsoft.Network/publicIPAddresses/read | Per saperne di indirizzi IP di rete |
| Microsoft.Network/virtualNetworks/read | Leggere le reti virtuali |
| Microsoft.Network/virtualNetworks/subnets/join/action | Partecipare a subnet virtuali |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Storage/storageAccounts/listKeys/action | Elenco dello spazio di archiviazione account tasti |
| Microsoft.Storage/storageAccounts/read | Leggere gli account di archiviazione |
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="classic-network-contributor"></a>Rete classica collaboratori
Possono gestire reti virtuali classiche e gli indirizzi IP riservato

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.ClassicNetwork/* | Creare e gestire le reti classiche |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Support/* | Creare e gestire i ticket di supporto |

### <a name="web-plan-contributor"></a>Web piano collaboratori
Gestire i piani web

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Support/* | Creare e gestire i ticket di supporto |
| Microsoft.Web/serverFarms/* | Creare e gestire server farm |

### <a name="website-contributor"></a>Sito Web per i collaboratori
Gestire siti Web, ma non i piani di web a cui sono connessi

| **Azioni** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorizzazione di lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso approfondimenti |
| Microsoft.Insights/components/* | Creare e gestire i componenti approfondimenti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Stato di lettura delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i gruppi di risorse |  
| Microsoft.Support/* | Creare e gestire i ticket di supporto |
| Microsoft.Web/certificates/* | Creare e gestire i certificati di sito Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leggere i siti creati in un nome host |
| Microsoft.Web/serverFarms/join/action | Partecipare a server farm |
| Microsoft.Web/serverFarms/read | Lettura server farm |
| Microsoft.Web/sites/* | Creare e gestire siti Web |

## <a name="see-also"></a>Vedere anche
- [Controllo dell'accesso basato sui ruoli](role-based-access-control-configure.md): Guida introduttiva a RBAC nel portale di Azure.
- [Ruoli personalizzati in Azure RBAC](role-based-access-control-custom-roles.md): informazioni su come creare ruoli personalizzati secondo le proprie esigenze di accesso.
- [Creare un accesso report Cronologia modifiche](role-based-access-control-access-change-history-report.md): tenere traccia di modifica delle assegnazioni di ruolo in RBAC.
- [Risoluzione dei problemi di controllo dell'accesso basato sui ruoli](role-based-access-control-troubleshooting.md): ottenere suggerimenti per la risoluzione di problemi comuni.
