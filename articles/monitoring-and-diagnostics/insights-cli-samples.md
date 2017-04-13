<properties
    pageTitle="Esempi di Guida introduttiva di Azure CLI Monitor. | Microsoft Azure"
    description="Comandi CLI di esempio per le caratteristiche di Azure Monitor. Monitor Azure è un servizio di Microsoft Azure che consente di inviare notifiche, chiamare URL web in base ai valori di dati di telemetria configurato e servizi Cloud autoScale, macchine virtuali e Web Apps."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Esempi di introduttive Monitor multipiattaforma CLI Azure

In questo articolo illustra esempio interfaccia riga di comando (CLI) comandi consentono di accedere a caratteristiche di Azure Monitor. Monitor Azure consente per servizi Cloud AutoScale, macchine virtuali e Web App e per inviare le notifiche di avviso o di chiamarlo URL web in base ai valori di dati di telemetria configurato.

>[AZURE.NOTE] Monitor Azure è il nuovo nome per ciò che era definito "Azure approfondimenti" fino a 25 ° settembre 2016. Tuttavia, gli spazi dei nomi e quindi comandi riportati di seguito contengono ancora "che".


## <a name="prerequisites"></a>Prerequisiti

Se non ha ancora installato CLI Azure, vedere [installare CLI Azure](../xplat-cli-install.md). Se si ha familiarità con CLI Azure, è possibile leggere altre informazioni all'indirizzo [usare CLI Azure per Mac, Linux e Windows Azure Gestione risorse di](../xplat-cli-azure-resource-manager.md).


In Windows, installare npm dal [sito Web Node](https://nodejs.org/). Dopo aver completato l'installazione con CMD.exe con privilegi di Esegui come amministratore, eseguire le operazioni seguenti dalla cartella in cui è installato npm:

```console
npm install azure-cli --global
```

Passare a qualsiasi/percorso della cartella desiderata e digitare nella riga di comando:

```console
azure help
```

## <a name="log-in-to-azure"></a>Accedere a Azure

Il primo passaggio consiste nel accedere al proprio account Azure.

```console
azure login
```

Dopo aver eseguito questo comando, è necessario accedere tramite le istruzioni sullo schermo. In seguito, viene visualizzato l'Account, TenantId e predefinito ID abbonamento. Tutti i comandi lavorare nel contesto di abbonamento predefinito.

Per visualizzare i dettagli dell'abbonamento corrente, utilizzare il comando seguente.

```console
azure account show
```

Per modificare il contesto di lavoro a un abbonamento diverso, utilizzare il comando seguente.

```console
azure account set "subscription ID or subscription name"
```

Per utilizzare i comandi di gestione risorse di Azure e Azure Monitor, è necessario essere in modalità di gestione di risorse Azure.

```console
azure config mode arm
```

Per visualizzare un elenco di tutti i comandi di Azure Monitor supportati, eseguire le operazioni seguenti.

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>Visualizza i registri di controllo per una sottoscrizione

Per visualizzare un elenco dei registri di controllo, eseguire le operazioni seguenti.

```console
azure insights logs list [options]
```

Eseguire le operazioni seguenti per visualizzare tutte le opzioni disponibili.

```console
azure insights logs list -help
```

Ecco un esempio ai registri di elenco da un resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Esempio ai registri elenco dal chiamante

```console
azure insights logs list --caller "myname@company.com"
```

Esempio di elenco registri da chiamante sui tipi di risorse, all'interno di una data di inizio e fine

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Lavorare con gli avvisi
È possibile utilizzare le informazioni nella sezione per lavorare con gli avvisi.

### <a name="get-alert-rules-in-a-resource-group"></a>Ottenere le regole di avviso in un gruppo di risorse

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Creare una regola di avviso metrica

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Creare una regola di avviso log

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Creare una regola di avviso webtest

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Eliminare una regola di avviso

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Profili di log
Usare le informazioni in questa sezione per lavorare con i profili di log.

### <a name="get-a-log-profile"></a>Ottenere un profilo di log

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Aggiungere un profilo log senza criteri di conservazione

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Rimuovere un profilo di log

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Aggiungere un profilo di log con criteri di conservazione

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Aggiungere un profilo di log con criteri di conservazione ed EventHub

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostica
Usare le informazioni in questa sezione per modificare le impostazioni di diagnostiche.

### <a name="get-a-diagnostic-setting"></a>Ottenere un'impostazione di diagnostica

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Disabilitare un'impostazione di diagnostica

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Attivare un'impostazione diagnostica senza criteri di conservazione

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>AutoScale
Usare le informazioni in questa sezione per modificare le impostazioni di autoscale. È necessario modificare questi esempi.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Ottieni impostazioni autoscale per un gruppo di risorse

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Ottieni impostazioni autoscale in base al nome di un gruppo di risorse

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Configurare le impostazioni auotoscale

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
