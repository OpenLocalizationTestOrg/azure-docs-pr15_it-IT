<properties
    pageTitle="Flusso il Registro di Azure attività a un evento hub | Microsoft Azure"
    description="Informazioni su come trasmettere il Registro di attività di Azure a un hub di evento."
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
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Flusso il Registro di Azure attività a un hub di evento
[**Registro attività Azure**](./monitoring-overview-activity-logs.md) può essere alterata quasi in tempo reale in qualsiasi applicazione utilizzando l'opzione "Esporta" predefinito nel portale o abilitando l'Id della regola servizio Bus in un profilo di Log tramite il cmdlet di PowerShell Azure o CLI Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Operazioni possibili con il registro attività e gli hub di evento
Ecco alcune modalità è possibile utilizzare la funzionalità di streaming per il registro attività:

- **Flusso di sistemi di registrazione e telemetria di terze parti** – nel tempo, hub evento streaming diventa meccanismo il Registro di attività in SIEMs di terze parti e registrare le soluzioni analitica.
- **Creare una piattaforma di registrazione e di telemetria personalizzato** : se è già una piattaforma di telemetria personalizzata o solo le informazioni sulla creazione di uno, elevata scalabilità pubblicazione sottoscrizione natura dell'evento hub consente di acquisire flessibile registro attività. [Vedere la Guida di Dan Rosanova all'uso di hub di evento in una piattaforma di telemetria di scala globale.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Abilitare lo streaming del registro attività
È possibile abilitare a livello di programmazione o tramite il portale di flusso del registro attività. In entrambi i casi, selezionare un Namespace Bus di servizio e un criterio di accesso condiviso per tale spazio dei nomi e un Hub di evento viene creato nello spazio dei nomi quando si verifica il primo nuovo evento registro attività. Se non si dispone di un Namespace Bus del servizio, è innanzitutto necessario crearne uno. Se sono in precedenza in streaming eventi del Log di attività a Namespace di Bus questo servizio, verrà riutilizzata Hub evento creato in precedenza. Il criterio di accesso condiviso definisce le autorizzazioni che contiene il meccanismo di trasmissione. Oggi, streaming a un hub evento richiede le autorizzazioni di **gestire**, **leggere**e **inviare** . È possibile creare o modificare i criteri di accesso servizio Bus Namespace condivise nel portale di classica sotto la scheda "Configura" per le Namespace Bus di servizio. Per aggiornare il profilo di log registro attività in modo da includere streaming, l'utente che effettua la modifica è necessario disporre dell'autorizzazione di ListKey su tale regola di autorizzazione Bus di servizio.

### <a name="via-azure-portal"></a>Tramite il portale di Azure 
1. Passare a e il **Registro attività** usando il menu sul lato sinistro del portale.

    ![Passare al registro attività nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Fare clic sul pulsante **Esporta** nella parte superiore e il.

    ![Pulsante Esporta nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. In e il che viene visualizzata, è possibile selezionare le aree geografiche per il quale si vogliono eventi di flusso e le Namespace Bus di servizio in cui si desidera un Hub di evento da creare per flussi di questi eventi.

    ![Esportare blade registro attività](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente all'abbonamento.


### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell
Se esiste già un profilo di log, è innanzitutto necessario rimuovere tale profilo.

1. Usare `Get-AzureRmLogProfile` per identificare se esiste un profilo di log
2. In questo caso, utilizzare `Remove-AzureRmLogProfile` per rimuoverla.
3. Usare `Set-AzureRmLogProfile` per creare un profilo:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

L'ID della regola Bus servizio è una stringa con questo formato: {service ID risorsa bus} /authorizationrules/ {chiave name}, ad esempio 

### <a name="via-azure-cli"></a>Tramite CLI Azure
Se esiste già un profilo di log, è innanzitutto necessario rimuovere tale profilo.

1. Usare `azure insights logprofile list` per identificare se esiste un profilo di log
2. In questo caso, utilizzare `azure insights logprofile delete` per rimuoverla.
3. Usare `azure insights logprofile add` per creare un profilo:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

L'ID della regola Bus servizio è una stringa con questo formato: `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Modalità di utilizzo di dati del registro dall'hub di evento
[È disponibile uno schema per il registro attività qui](./monitoring-overview-activity-logs.md). Ogni evento è in una matrice di BLOB JSON denominate "record".

## <a name="next-steps"></a>Passaggi successivi
- [Archiviare il Registro di attività a un account di archiviazione](./monitoring-archive-activity-log.md)
- [Panoramica del Registro di attività Azure](./monitoring-overview-activity-logs.md)
- [Impostare un avviso in base a un evento del Log di attività](./insights-auditlog-to-webhook-email.md)
