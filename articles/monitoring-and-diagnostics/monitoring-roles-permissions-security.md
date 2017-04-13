<properties
    pageTitle="Guida introduttiva a ruoli, autorizzazioni e sicurezza con Azure Monitor | Microsoft Azure"
    description="Informazioni su come usare Azure Monitor ruoli predefiniti e le autorizzazioni per limitare l'accesso a monitoraggio delle risorse."
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
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Guida introduttiva a ruoli, autorizzazioni e sicurezza con Azure Monitor

Molti team necessario strettamente regolano l'accesso ai dati e le impostazioni di monitoraggio. Ad esempio, se si dispongono di membri del team che lavorano esclusivamente su monitoraggio (tecnici, attrezzi ingegneri) o se si usa un provider di servizi gestiti, si desidera concedere l'accesso a solo il monitoraggio dei dati limitando la possibilità di creare, modificare o eliminare le risorse. In questo articolo viene illustrato come applicare un ruolo RBAC monitoraggio incorporato a un utente in Azure o creare il proprio ruolo personalizzato per un utente a cui concedere autorizzazioni limitate monitoraggio rapidamente. Quindi, vengono illustrate considerazioni sulla protezione per le risorse correlate al Monitor di Azure e come è possibile limitare l'accesso ai dati che contengono.

## <a name="built-in-monitoring-roles"></a>Ruoli di monitoraggio incorporati

Ruoli predefiniti del Monitor Azure sono progettati per limitare l'accesso alle risorse in un abbonamento pur continuando i responsabili per il monitoraggio dell'infrastruttura per ottenere e configurare i dati necessari. Monitor Azure offre due ruoli della casella: monitoraggio delle risposte utilità per la lettura e un collaboratore monitoraggio.

### <a name="monitoring-reader"></a>Monitoraggio delle utilità per la lettura

Utenti assegnati al ruolo di monitoraggio lettore possono visualizzare tutti i dati di monitoraggio in una sottoscrizione, ma non è possibile modificare tutte le risorse o modificare le impostazioni relative al monitoraggio delle risorse. Questo ruolo è adatto per gli utenti di un'organizzazione, ad esempio supporto o operazioni tecnici che devono essere in grado di:

- Visualizzare i dashboard monitoraggio nel portale e creare Dashboard personali monitoraggio privato.
- Query per metriche tramite [API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [i cmdlet di PowerShell](insights-powershell-samples.md)o [multipiattaforma CLI](insights-cli-samples.md).
- Il Log di attività tramite il portale, API REST di Azure Monitor, i cmdlet di PowerShell o multipiattaforma CLI della query.
- Visualizzare le [impostazioni di diagnostiche](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) per una risorsa.
- Visualizzare il [profilo log](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) per una sottoscrizione.
- Visualizza impostazioni autoscale.
- Visualizzazione degli avvisi attività e le impostazioni.
- Accedere a informazioni dettagliate sui applicazione dati e visualizzare dati AI Analitica.
- Ricerca dei dati dell'area di lavoro Log Analitica (OMS) inclusi i dati di utilizzo per l'area di lavoro.
- Visualizzare i gruppi di gestione dei Log Analitica (OMS).
- Recuperare lo schema di ricerca Log Analitica (OMS).
- Elencare i pacchetti di business intelligence Log Analitica (OMS).
- Recuperare ed eseguire Log Analitica (OMS) ricerche salvate.
- Recuperare la configurazione di spazio di archiviazione Analitica Log (OMS).

> [AZURE.NOTE] Questo ruolo non concessione dell'accesso in lettura ai dati di registro che sono stata trasmessi a un hub di evento o archiviati in un account di archiviazione. Per informazioni sulla configurazione dell'accesso a queste risorse, [vedere di seguito](#security-considerations-for-monitoring-data) .

### <a name="monitoring-contributor"></a>Monitoraggio collaboratori

Gli utenti assegnati al ruolo di collaboratore monitoraggio possono visualizzare tutti i dati di monitoraggio di un abbonamento e creare o modificare le impostazioni di monitoraggio, ma non è possibile modificare altre risorse. Questo ruolo è un superset del ruolo Lettore di monitoraggio e appropriato per i membri del team monitoraggio di un'organizzazione o gestito provider di servizi, oltre alle autorizzazioni riportati sopra, anche necessario essere in grado di:

- Pubblicare dashboard monitoraggio come dashboard condiviso.
- Configurare le [impostazioni di diagnostiche](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) per un resource.*
- Impostare il [profilo log](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) per un subscription.*
- Impostare avvisi attività e le impostazioni.
- Creare approfondimenti applicazione web test e componenti.
- Elenco Log Analitica (OMS) dell'area di lavoro condivisa tasti.
- Attivare o disattivare i pacchetti di business intelligence Log Analitica (OMS).
- Creare ed eliminare ed eseguire Log Analitica (OMS) ricerche salvate.
- Creare ed eliminare la configurazione archiviazione Analitica Log (OMS).

* utente anche separatamente deve disporre dell'autorizzazione ListKeys sulla risorsa di destinazione (spazio di archiviazione account o un evento hub spazio dei nomi) per impostare un profilo log o l'impostazione di diagnostica.

> [AZURE.NOTE] Questo ruolo non concessione dell'accesso in lettura ai dati di registro che sono stata trasmessi a un hub di evento o archiviati in un account di archiviazione. Per informazioni sulla configurazione dell'accesso a queste risorse, [vedere di seguito](#security-considerations-for-monitoring-data) .

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitoraggio personalizzato RBAC ruoli e autorizzazioni
Se i ruoli predefiniti sopra non soddisfano esigenze del team, è possibile [creare un ruolo RBAC personalizzato](../active-directory/role-based-access-control-custom-roles.md) con autorizzazioni più granulare. Di seguito sono le operazioni di Azure Monitor RBAC comuni con le relative descrizioni.

| Operazione                                                   | Descrizione                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft.Insights/AlertRules/[Read, scrittura, eliminazione]         | Regole di avviso di lettura/scrittura/eliminazione.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Elenco interventi (cronologia della regola di avviso avviata) per le regole di avviso. Si applica solo al portale.                                              |
| Microsoft.Insights/AutoscaleSettings/[Read, scrittura, eliminazione]  | Impostazioni di lettura/scrittura/eliminazione autoscale.                                                                                                                     |
| Microsoft.Insights/DiagnosticSettings/[Read, scrittura, eliminazione] | Impostazioni di diagnostiche lettura/scrittura/eliminazione.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Questa autorizzazione è necessaria che gli utenti che hanno l'esigenza di accesso ai registri attività tramite il portale.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | Elenco attività registro eventi (gestione) in una sottoscrizione. Questa autorizzazione è applicabile al livello di programmazione e portale l'accesso a registro attività. |
| Microsoft.Insights/LogDefinitions/Read                      | Questa autorizzazione è necessaria che gli utenti che hanno l'esigenza di accesso ai registri attività tramite il portale.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Leggere le definizioni metriche (elenco di tipi di metriche disponibili per una risorsa).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Leggere le metriche per una risorsa.                                                                                                                              |

> [AZURE.NOTE] L'accesso agli avvisi, le impostazioni di diagnostiche e metriche per una risorsa, è necessario che gli utenti abbiano accesso in lettura per il tipo di risorsa e l'ambito di quella risorsa. La creazione ("scrittura") un profilo impostazione o log diagnostico che consente di archiviare un account di archiviazione o flussi a un hub evento richiede all'utente di anche disporre dell'autorizzazione ListKeys per la risorsa di destinazione.

Ad esempio utilizzando la tabella precedente, è possibile creare un ruolo RBAC personalizzato per un "attività lettura Log" alla seguente:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerazioni sulla protezione per il monitoraggio dei dati
Monitoraggio dei dati, in particolare file di log, ovvero possono contenere informazioni riservate, ad esempio nomi utente o gli indirizzi IP. Monitoraggio dei dati da Azure disponibile nelle tre forme base:
1. Registro attività, che descrive tutte le azioni di piano di controllo nella sottoscrizione Azure.
2. Registri diagnostici, sono registri emessi da una risorsa.
3. Metrica, vengono creata da risorse.

Tutti e tre questi tipi di dati archiviati in un account di archiviazione o in streaming hub di evento, che sono risorse Azure generiche. Poiché si tratta di risorse generiche, creazione, eliminazione e accedervi è un'operazione con privilegi riservata in genere l'amministratore. Si consiglia di utilizzare le seguenti consigliate per le risorse relative a monitoraggio per evitare che uso errato:

- Usare un account di archiviazione dedicato per controllare i dati. Se è necessario separare dati in più account di archiviazione, mai condividere l'utilizzo di un account di archiviazione tra il monitoraggio e i dati non monitoraggio, come questo possono fornire inavvertitamente coloro che necessitano di accedere a monitoraggio dei dati (ad esempio. SIEM di terze parti) accesso a non monitoraggio dei dati.
- Utilizzare un dedicato Bus di servizio o evento Hub spazio dei nomi in tutte le impostazioni di diagnostiche per lo stesso motivo come illustrato in precedenza.
- Limitare l'accesso per gli account di archiviazione correlati monitoraggio o evento hub salvandole in un gruppo di risorse separato e [utilizzare l'ambito](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) dei ruoli monitoraggio per limitare l'accesso a tale gruppo di risorse.
- Non concedere l'autorizzazione di ListKeys per gli account di archiviazione o hub di evento nell'ambito di un abbonamento non è mai quando un utente deve solo accesso ai dati di monitoraggio. Ma è possibile ottenere le autorizzazioni per l'utente in una risorsa o il gruppo (se si dispone di un gruppo di risorse monitoraggio dedicato) ambito.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitazione dell'accesso per gli account di archiviazione correlati monitoraggio
Quando un utente o un'applicazione necessita di accesso per il monitoraggio dei dati in un account di archiviazione, è necessario [generare un SA Account](https://msdn.microsoft.com/library/azure/mt584140.aspx) in account di archiviazione che contiene dati monitoraggio a livello di servizio di accesso in sola lettura a archiviazione blob. In PowerShell, questa potrebbe essere simile:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

È possibile quindi assegnare il token all'entità che deve leggere da tale archivio account e i dati possibile dell'elenco e leggere tutti i BLOB di account di archiviazione.

In alternativa, se è necessario controllare questa autorizzazione con RBAC, è possibile concedere l'entità dell'autorizzazione Microsoft.Storage/storageAccounts/listkeys/action quell'account archiviazione specifico. Questa operazione è necessaria per gli utenti che devono avere la possibilità di impostare un'impostazione di diagnostica o Registra profilo personale per archiviare un account di archiviazione. Ad esempio, è possibile creare il seguente ruolo RBAC personalizzato per un utente o applicazione che deve solo leggere da un account di archiviazione:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] L'autorizzazione ListKeys consente all'utente di elencare i tasti di account di archiviazione primario e secondario. Questi tasti concedono all'utente tutti firmati autorizzazioni (lettura, scrittura, creare BLOB, eliminare BLOB e così via) tra tutti firmato servizi (blob, coda, tabella, file) in quell'account lo spazio di archiviazione. È consigliabile utilizzare un SA Account descritte in precedenza laddove possibile.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitazione dell'accesso a un hub di eventi correlati al monitoraggio
Un modello simile può essere seguito con gli hub di evento, ma è necessario creare una regola di autorizzazione ascolto dedicata. Se si desidera concedere l'accesso a un'applicazione che non si dovrà ascoltare hub eventi correlati al monitoraggio, eseguire le operazioni seguenti:

1. Creare un criterio di accesso condiviso negli hub di eventi che sono stati creati per il flusso di dati di monitoraggio con solo ascolto attestazioni. Può essere eseguita nel portale. Ad esempio, si potrebbe chiamarlo "monitoringReadOnly". Se possibile, è conferire tale tasto direttamente al consumatore e ignorare il passaggio successivo.
2. Se il consumer deve essere in grado di accedere ad-hoc chiave, concedere all'utente l'azione ListKeys per tale hub evento. È anche necessario per gli utenti che devono avere la possibilità di impostare un'impostazione di diagnostica o Registra profilo flusso a un hub di evento. Ad esempio, è possibile creare una regola RBAC:

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su RBAC e le autorizzazioni in Gestione risorse](../active-directory/role-based-access-control-what-is.md)
- [Panoramica di monitoraggio di Azure](monitoring-overview.md)
