<properties
    pageTitle="Panoramica del registro attività Azure | Microsoft Azure"
    description="Informazioni sulle novità registro attività Azure e come è possibile utilizzare per comprendere eventi che si verificano all'interno di abbonamento Azure."
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
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Panoramica del registro attività Azure
**Registro attività Azure** è un file di log che fornisce approfondimenti operazioni eseguite sulle risorse nell'abbonamento. Registro attività era nota come "I log di controllo" o "Registri operativi", dal momento che riporta gli eventi di piano di controllo per le proprie sottoscrizioni. Utilizzando il registro attività, è possibile determinare il ' cosa, chi e il momento "per le operazioni (inserire, POST, DELETE) prelevate per le risorse di abbonamento di scrittura. È inoltre possibile conoscere lo stato dell'operazione e altre proprietà pertinenti. Registro attività non include le operazioni di lettura (GET).

Registro attività è diverso dal [Log diagnostici](./monitoring-overview-of-diagnostic-logs.md), quali sono tutti i log emessi da una risorsa. Questi registri forniscono dati sull'operazione di quella risorsa anziché su operazioni su quella risorsa.

È possibile recuperare gli eventi dal Registro di attività tramite il portale di Azure, CLI, i cmdlet di PowerShell e API REST di Azure Monitor.

Consente di visualizzare il [video Introduzione a registro attività](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>Operazioni eseguibili con registro attività
Ecco alcune delle operazioni che eseguibili con registro attività:

- Progettazione query e viste nel **portale di Azure**.
- Eseguire una query tramite API REST, Cmdlet di PowerShell o CLI.
- [Creare un avviso di posta elettronica o webhook che attiva disattivare un evento del Log di attività.](./insights-auditlog-to-webhook-email.md)
- [Salvarla in un **Account di archiviazione** per l'archiviazione o manuale ispezione](./monitoring-archive-activity-log.md). È possibile specificare il tempo di conservazione (in giorni) utilizzando **I profili di Log**.
- Analizzare in ottenere informazioni tramite il [**pacchetto di contenuti di ottenere informazioni**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
- [Inoltrarla a un **Evento Hub** ](./monitoring-stream-activity-logs-event-hubs.md) per acquisizione da un servizio di terze parti o una soluzione analitica personalizzato, ad esempio ottenere informazioni.

## <a name="export-the-activity-log-with-log-profiles"></a>Esportare il Registro di attività con i profili di Log
Un **Profilo Log** consente di controllare la modalità di esportazione del Registro di attività. Utilizzando un profilo di Log, è possibile configurare:

- Registro attività in cui devono essere inviato (Account di archiviazione o evento hub)
- Devono essere inviate le categorie di eventi (scrittura, Elimina azione)
- Quali aree (posizioni) devono essere esportati
- Per quanto tempo occorre conservare il registro attività in un Account di archiviazione: conservazione di zero giorni indica che i registri vengono mantenuti per sempre. In caso contrario, che può essere qualsiasi numero di giorni compreso tra 1 e 2147483647. Se vengono impostati i criteri di conservazione ma archiviazione dei log in un Account di archiviazione è disattivato (ad esempio, se solo si selezionano opzioni evento hub o OMS), i criteri di conservazione non hanno effetto.

Queste impostazioni possono essere configurate tramite l'opzione "Esporta" in e il registro attività nel portale. Sono inoltre può essere configurato a livello di programmazione [tramite l'API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), i cmdlet di PowerShell o CLI. Una sottoscrizione può contenere solo un profilo di log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurare profili log tramite il portale di Azure
È possibile trasmettere il Registro di attività a un Hub di evento o archiviarli in un Account di archiviazione utilizzando l'opzione "Esporta" nel portale di Azure.

1. Passare a e il **Registro attività** usando il menu sul lato sinistro del portale.

    ![Passare al registro attività nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Fare clic sul pulsante **Esporta** nella parte superiore e il.

    ![Pulsante Esporta nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. In e il che viene visualizzata, è possibile selezionare:  
    - aree geografiche per il quale si desidera esportare gli eventi
    - Account di archiviazione a cui si desidera salvare gli eventi
    - il numero di giorni che si desidera mantenere questi eventi in archiviazione. Un'impostazione di giorni che vanno 0 mantiene sempre i registri.
    - Namespace Bus di servizio in cui si desidera un Hub di evento da creare per flussi di questi eventi.

    ![Esportare blade registro attività](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente all'abbonamento.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurare profili log utilizzando i cmdlet di PowerShell Azure
#### <a name="get-existing-log-profile"></a>Ottenere profilo log esistente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Aggiungere un profilo di log
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Proprietà         | Obbligatorio | Descrizione   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome             | Sì      | Nome del profilo log.                                 |
| StorageAccountId | No       | ID risorsa dell'Account di archiviazione a cui deve essere salvato il registro attività.                         |
| serviceBusRuleId | No       | Servizio ID regola di Bus per lo spazio dei nomi di servizio Bus in che si vuole hub evento creato. È una stringa con questo formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Percorsi        | Sì      | Elenco delimitato da virgole delle aree per il quale si desidera raccogliere eventi del Log di attività.              |
| RetentionInDays  | Sì      | Numero di giorni per cui occorre conservare gli eventi, compreso tra 1 e 2147483647. Il valore zero sono archiviati i registri di tempo indefinito (sempre). |
| Categorie       | No       | Elenco delimitato da virgole delle categorie di eventi che devono essere raccolti. Valori possibili sono scrittura, eliminazione e azione.                                 |

#### <a name="remove-a-log-profile"></a>Rimuovere un profilo di log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurare profili log utilizzando CLI multipiattaforma e su Azure
#### <a name="get-existing-log-profile"></a>Ottenere profilo log esistente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
Il `name` proprietà deve essere il nome del proprio profilo di log.

#### <a name="add-a-log-profile"></a>Aggiungere un profilo di log
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Proprietà         | Obbligatorio | Descrizione   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome             | Sì      | Nome del profilo log.                                 |
| storageId        | No       | ID risorsa dell'Account di archiviazione a cui deve essere salvato il registro attività.                         |
| serviceBusRuleId | No       | Servizio ID regola di Bus per lo spazio dei nomi di servizio Bus in che si vuole hub evento creato. È una stringa con questo formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| percorsi        | Sì      | Elenco delimitato da virgole delle aree per il quale si desidera raccogliere eventi del Log di attività.              |
| retentionInDays  | Sì      | Numero di giorni per cui occorre conservare gli eventi, compreso tra 1 e 2147483647. Il valore zero sono archiviati i registri di tempo indefinito (sempre).     |
| categorie       | No       | Elenco delimitato da virgole delle categorie di eventi che devono essere raccolti. Valori possibili sono scrittura, eliminazione e azione.                                 |

#### <a name="remove-a-log-profile"></a>Rimuovere un profilo di log
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Schema degli eventi
Ogni evento nel Log delle attività ha un blob JSON simile a questo esempio:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nome dell'elemento         | Descrizione             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorizzazione        | BLOB di proprietà RBAC dell'evento. In genere include le proprietà "azioni", "ruolo" e "ambito".|
| chiamante               | Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazioni UPN o Richiedi SPN in base alla disponibilità.|
| canali             | Uno dei seguenti valori: "Amministratore", "Operazione"|
| correlationId        | In genere GUID in formato stringa. Gli eventi che condividono un correlationId appartengono all'azione uber stesso.   |
| Descrizione          | Descrizione testo statico di un evento.                              |
| eventDataId          | Identificatore univoco dell'evento.    |
| eventSource          | Nome del servizio Azure o infrastruttura che ha generato questo evento.    |
| httpRequest          | BLOB che descrive la richiesta Http. In genere include "clientRequestId", "clientIpAddress" e "metodo" (HTTP. INSERIRE, ad esempio).                            |
| livello                | Livello dell'evento. Uno dei seguenti valori: "Critico", "Errore", "Avviso", "Informativo" e "Dettagliato"  |
| resourceGroupName    | Nome del gruppo di risorse per la risorsa interessata.               |
| resourceProviderName | Nome del provider di risorse per la risorsa interessata             |
| resourceUri          | Id risorsa della risorsa interessata.                               |
| operationId          | GUID condivisi tra gli eventi che corrispondono a una singola operazione.         |
| Invece        | Nome dell'operazione.  |
| proprietà           | Set di `<Key, Value>` coppie (dizionario) che descrive i dettagli dell'evento.                                |
| stato               | Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: iniziato, In corso, completato, non riuscito, attivo, risolto.                                |
| stato secondario            | In genere il codice di stato HTTP del resto corrispondente chiamata, ma può essere anche altre stringhe che descrive un secondario, ad esempio questi valori comuni: OK (codice di stato HTTP: 200), creato (codice di stato HTTP: 201), accettato (codice di stato HTTP: 202), non il contenuto (codice di stato HTTP: 204), richiesta non valida (codice di stato HTTP: 400), non viene trovato (codice di stato HTTP: 404), conflitto (codice di stato HTTP : 409), errore interno del Server (codice di stato HTTP: 500), servizio non è disponibile (codice di stato HTTP: 503), Timeout Gateway (codice di stato HTTP: 504). |
| eventTimestamp       | Timestamp quando l'evento è stato generato dal servizio di Azure elaborazione della richiesta corrispondente all'evento.     |
| submissionTimestamp  | Timestamp quando l'evento è diventato disponibile per le query.             |
| subscriptionId       | ID abbonamento Azure.  |
| nextLink             | Token di continuazione per recuperare il set di risultati successivo quando sono suddivise in più risposte. In genere necessari quando sono presenti più di 200 record.     |

## <a name="next-steps"></a>Passaggi successivi
- [Ulteriori informazioni sul Log di attività (in precedenza i log di controllo)](../resource-group-audit.md)
- [Flusso il Registro di Azure attività a un hub di evento](./monitoring-stream-activity-logs-event-hubs.md)
