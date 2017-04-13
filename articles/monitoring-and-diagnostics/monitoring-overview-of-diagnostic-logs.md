<properties
    pageTitle="Panoramica dei registri diagnostici Azure | Microsoft Azure"
    description="Informazioni sui registri diagnostici Azure e come è possibile utilizzare per comprendere eventi che si verificano all'interno di una risorsa Azure."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Panoramica dei registri diagnostici Azure
**Registri diagnostici Azure** sono registri emessi da una risorsa che forniscono dati accattivanti e dall'aspetto frequenti sull'operazione di quella risorsa. Il contenuto di questi registri varia in base al tipo di risorsa (ad esempio, Registro di sistema di eventi di Windows è una categoria di Log diagnostici per macchine virtuali e blob, tabella e i registri coda sono categorie di registri diagnostici per gli account di archiviazione) e diversi dal [registro attività (precedentemente noto come Log di controllo o registro operativo)](monitoring-overview-activity-logs.md), che fornisce approfondimenti operazioni eseguite sulle risorse nell'abbonamento. Non tutte le risorse di supporto il nuovo tipo di log diagnostici descritti di seguito. Nell'elenco supportato servizi Mostra quali tipi di risorse di supporto tecnico i nuovi registri diagnostici.

![Logico posizionamento dei registri diagnostici](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Operazioni eseguibili con registri diagnostici
Ecco alcune delle operazioni che eseguibili con log diagnostici:

- Salvarli in un **Account di archiviazione** per la verifica di controllo o manuale. È possibile specificare il tempo di conservazione (in giorni) usare le **Impostazioni di diagnostica**.
- [In flussi a un **Evento hub** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) per acquisizione da un servizio di terze parti o una soluzione analitica personalizzato, ad esempio ottenere informazioni.
- Analizzarli con [OMS Log Analitica](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Impostazioni di diagnostiche
Registri diagnostici per le risorse di calcolo non possono essere configurati procedendo le impostazioni di diagnostica. **Impostazioni di diagnostica** per un controllo di risorsa:

- Registri diagnostici nel punto in cui vengono inviati (Account di archiviazione, hub di eventi e/o OMS Log Analitica).
- Vengono inviate le categorie di Log.
- Per quanto tempo occorre conservare ogni categoria di log in un Account di archiviazione: conservazione pari a zero indica giorni in cui vengono mantenuti per sempre i registri. In caso contrario, questo valore può essere compreso tra 1 e 2147483647. Se vengono impostati i criteri di conservazione ma archiviazione dei log in un Account di archiviazione è disattivata (ad esempio se sono selezionate opzioni evento hub o OMS solo), i criteri di conservazione non hanno alcun effetto.

Queste impostazioni sono configurate facilmente tramite e il diagnostica per una risorsa nel portale di Azure, tramite i comandi PowerShell di Azure e CLI o tramite l' [API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Log diagnostici e i criteri di risorse di elaborazione (ad esempio macchine virtuali o servizio tessuti) usare [un meccanismo distinto per la configurazione e la selezione di output](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Come attivare la raccolta dei registri diagnostici
Insieme di registri diagnostici può essere attivate come parte della creazione di una risorsa o dopo la creazione di una risorsa tramite blade della risorsa nel portale. È anche possibile attivare registri diagnostici in qualsiasi momento usando i comandi PowerShell di Azure o CLI o tramite l'API REST di Azure Monitor.

> [AZURE.TIP] Queste istruzioni potrebbero non essere applicabile direttamente a ogni risorsa. Fare clic sui collegamenti schema nella parte inferiore della pagina per comprendere passaggi speciali applicabili a determinati tipi di risorse.

[Questo articolo descrive come è possibile utilizzare un modello di risorsa per attivare le impostazioni di diagnostica durante la creazione di una risorsa](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Abilitare i registri diagnostici nel portale
È possibile abilitare registri diagnostici nel portale di Azure quando si creano tipi di risorse calcolo consentendo l'estensione di Windows o Linux Azure diagnostica:

1.  Passare a **Nuovo** e scegliere la risorsa che interessa.
2.  Dopo avere configurato le impostazioni di base e selezionando una dimensione in e **l'Impostazioni** , in **monitoraggio**, selezionare **attivato** e scegliere un account di archiviazione in cui si desidera memorizzare i registri diagnostici. Quando si invia diagnostica a un account di archiviazione, addebitate velocità normale per lo spazio di archiviazione e le transazioni.

    ![Abilitare i registri diagnostici durante la creazione di risorse](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Fare clic su **OK** e creare la risorsa.

Per le risorse non calcolo, è possibile abilitare registri diagnostici nel portale di Azure dopo la creazione di una risorsa eseguendo le operazioni seguenti:

1.  Passare a e il per la risorsa e aprire e il **diagnostica** .
2.  Fare clic **su** e selezionare un Account di archiviazione e/o Hub evento.

    ![Attivare i registri diagnostici dopo la creazione di risorse](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  In **log**, selezionare le **Categorie Log** si desidera raccogliere o flusso.
4.  Fare clic su **Salva**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Abilitare i registri diagnostici tramite PowerShell
Per abilitare registri diagnostici tramite i cmdlet di PowerShell Azure, utilizzare i comandi seguenti.

Per abilitare l'archiviazione dei registri diagnostici in un Account di archiviazione, utilizzare questo comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

L'ID dell'Account di archiviazione è l'id di risorsa per l'account di archiviazione che si desidera inviare i log.

Per eseguire lo streaming dei registri diagnostici a un Hub di evento, utilizzare questo comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

L'ID della regola Bus servizio è una stringa con questo formato: `{service bus resource ID}/authorizationrules/{key name}`.

Per abilitare l'invio dei registri diagnostici per un'area di lavoro Log Analitica, utilizzare questo comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] Il parametro WorkspaceId non è disponibile nella versione ottobre. Descritte diventeranno disponibile nella versione novembre.

È possibile ottenere l'ID dell'area di lavoro Analitica Log nel portale di Azure.

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-diagnostic-logs-via-cli"></a>Abilitare i registri diagnostici tramite CLI
Per attivare i registri diagnostici tramite CLI Azure, utilizzare i comandi seguenti:

Per abilitare l'archiviazione dei registri diagnostici in un Account di archiviazione, utilizzare questo comando:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

L'ID dell'Account di archiviazione è l'id di risorsa per l'account di archiviazione che si desidera inviare i log.

Per eseguire lo streaming dei registri diagnostici a un Hub di evento, utilizzare questo comando:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

L'ID della regola Bus servizio è una stringa con questo formato: `{service bus resource ID}/authorizationrules/{key name}`.

Per abilitare l'invio dei registri diagnostici per un'area di lavoro Log Analitica, utilizzare questo comando:

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] Il parametro workspaceId non è disponibile nella versione ottobre. Descritte diventeranno disponibile nella versione novembre.

È possibile ottenere l'ID dell'area di lavoro Analitica Log nel portale di Azure.

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Abilitare i registri diagnostici tramite API REST
Per modificare le impostazioni di diagnostica tramite l'API REST di Azure Monitor, vedere [questo documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gestire le impostazioni di diagnostica nel portale

Per garantire che tutte le risorse siano impostate correttamente con impostazioni di diagnostiche, è possibile passare a e il **monitoraggio** nel portale e aprire e il **Log diagnostici** .

![Blade registri diagnostici nel portale](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Potrebbe essere necessario fare clic su "Altri servizi" per trovare e il monitoraggio.

In questo blade, è possibile visualizzare e filtrare tutte le risorse che supportano registri diagnostici per verificare se hanno diagnostica abilitata e quali account di archiviazione, hub di eventi e/o dell'area di lavoro Analitica Log i registri vengono inviati a.

![Risultati di blade registri diagnostici nel portale](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Fare clic su una risorsa verranno visualizzati tutti i registri che sono stati memorizzati nell'account di archiviazione e sarà disponibile l'opzione per disattivare o modificare le impostazioni di diagnostiche. Fare clic sull'icona di download per scaricare i registri per un periodo di tempo specifico.

![Diagnostica registri blade una risorsa](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Registri diagnostici verranno solo per questa visualizzazione e disponibile per il download se sono state configurate impostazioni di diagnostiche per salvarli in un account di archiviazione.

Facendo clic sul collegamento per **Le impostazioni di diagnostica** e il diagnostica impostazioni, in cui è possibile attivare, disattivare o modificare le impostazioni di diagnostiche per la risorsa selezionata verrà visualizzata.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Servizi supportati e allo schema per registri diagnostici
Lo schema per i registri diagnostici varia a seconda della categoria di log e delle risorse. Di seguito sono i servizi supportati e lo schema.

| Servizio                       | Schema e documenti                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Software di bilanciamento del carico     |    [Analitica del Registro di sistema di bilanciamento del carico Azure (Preview)](../load-balancer/load-balancer-monitor-log.md)             |
|    Gruppi di sicurezza di rete    |    [Registro analitica per i gruppi di sicurezza di rete (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Gateway per applicazioni       |    [Registrazione diagnostica per Gateway di applicazioni](../application-gateway/application-gateway-diagnostics.md)     |
|    Archivio di chiave                  |    [Registrazione di Azure archivio chiave](../key-vault/key-vault-logging.md)                                                 |
|    Ricerca di Azure               |    [Attivazione e utilizzo Analitica il traffico di ricerca](../search/search-traffic-analytics.md)                         |
|    Archivio Lake dati.            |    [Accesso ai registri diagnostici per archivio Lake dati di Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Dati Lake Analitica        |    [Accesso ai registri diagnostici per Azure dati Lake Analitica](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    App logica                 |    Nessuno schema disponibile.                                                                                         |
|    Azure Batch                |    [Registrazione diagnostica di Azure Batch](../batch/batch-diagnostics.md)                                              |
|    Automazione Azure           |    [Registro analitica per l'automazione di Azure](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Hub di evento                  |    Nessuno schema disponibile.                                                                                         |
|    Bus di servizio                |    Nessuno schema disponibile.                                                                                         |
|    Flusso Analitica           |    Nessuno schema disponibile.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log per ogni tipo di risorsa è supportata

|Tipo di risorsa|Categoria|Nome di categoria|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Log di processo|
|Microsoft.Automation/automationAccounts|JobStreams|Flussi di lavoro|
|Microsoft.Batch/batchAccounts|ServiceLog|Registri del servizio|
|Microsoft.DataLakeAnalytics/accounts|Controllo|Log di controllo|
|Microsoft.DataLakeAnalytics/accounts|Richieste di|Registri delle richieste|
|Microsoft.DataLakeStore/accounts|Controllo|Log di controllo|
|Microsoft.DataLakeStore/accounts|Richieste di|Registri delle richieste|
|Microsoft.EventHub/namespaces|ArchiveLogs|Log degli archivi|
|Microsoft.EventHub/namespaces|OperationalLogs|Registri operativi|
|Microsoft.KeyVault/vaults|AuditEvent|Log di controllo|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventi di diagnostica runtime del flusso di lavoro|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Eventi di gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contatore regola gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Eventi del flusso di regola gruppo di sicurezza di rete|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Caricare eventi di avviso di bilanciamento del carico|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stato di caricamento bilanciamento verifica dell'integrità|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log di accesso di applicazione Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registro delle prestazioni Gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Applicazione Gateway Firewall Log|
|Microsoft.Search/searchServices|OperationLogs|Registri operazioni|
|Microsoft.ServerManagement/nodes|RequestLogs|Registri delle richieste|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registri operativi|
|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|Esecuzione|
|Microsoft.StreamAnalytics/streamingjobs|Creazione condivisa|Creazione condivisa|

## <a name="next-steps"></a>Passaggi successivi
- [Registri diagnostici flusso a un **hub di evento**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Modificare le impostazioni di diagnostica tramite l'API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analizzare i registri con OMS Log Analitica](../log-analytics/log-analytics-azure-storage-json.md)
