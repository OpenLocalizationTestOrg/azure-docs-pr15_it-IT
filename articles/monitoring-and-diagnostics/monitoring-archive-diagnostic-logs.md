<properties
    pageTitle="Archiviare i registri diagnostici Azure | Microsoft Azure"
    description="Informazioni su come archiviare i registri diagnostici Azure per la conservazione a lungo termine in un account di archiviazione."
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
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Archiviare i registri diagnostici Azure
In questo articolo, ecco come è possibile utilizzare il portale Azure PowerShell Cmdlets, CLI o API REST per archiviare i [Registri diagnostici Azure](monitoring-overview-of-diagnostic-logs.md) in un account di archiviazione. Questa opzione è utile se si preferisce mantenere i registri diagnostici con un criterio di conservazione facoltativo per backup, analisi statica o controllo.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) a cui è possibile archiviare i registri diagnostici. È consigliabile non utilizzare un account di archiviazione esistente che contiene dati di altri, non il monitoraggio in essa contenuti in modo che è possibile controllare meglio accesso ai dati di monitoraggio. Tuttavia, se si desidera archiviare anche il registro attività e il formato diagnostico per un account di archiviazione, può essere utile usare tale account di archiviazione per i registri diagnostici anche per mantenere tutti i dati di monitoraggio in una posizione centrale. L'account di archiviazione in uso deve essere un account di archiviazione generale, non un account di archiviazione blob.

## <a name="diagnostic-settings"></a>Impostazioni di diagnostiche
Per archiviare i registri diagnostici utilizzando uno dei seguenti metodi, impostare un' **Impostazione di diagnostica** per una determinata risorsa. Un'impostazione di diagnostica per una risorsa definisce le varie categorie di accesso che si trovano che vengono archiviate o in streaming e output, ovvero hub account e/o evento lo spazio di archiviazione. Definisce inoltre i criteri di conservazione (numero di giorni per cui le) per gli eventi di ogni categoria di log archiviati in un account di archiviazione. Se un criterio di conservazione è impostato su zero, gli eventi per la categoria di log sono archiviati tempo indefinito (vale a dire, per sempre). Criteri di conservazione in caso contrario possono essere qualsiasi numero di giorni compreso tra 1 e 2147483647. [Vengono fornite ulteriori informazioni sulle impostazioni di diagnostiche qui](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archivio registri diagnostici tramite il portale

1. Nel portale, fare clic su in e delle risorse per la risorsa in cui si desidera abilitare archiviazione dei registri diagnostici.
2. Nella sezione **monitoraggio** del menu Impostazioni risorsa selezionare **diagnostica**.

    ![Sezione monitoraggio del menu delle risorse](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Selezionare la casella di controllo per **esportare in Account di archiviazione**, quindi selezionare un account di archiviazione. Facoltativamente, è possibile impostare un numero di giorni per la conservazione di questi registri utilizzando il **conservazione (giorni)** dispositivi di scorrimento. Conservazione di zero giorni archivia i registri di tempo indefinito.

    ![Blade registri diagnostici](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Fare clic su **Salva**.

Registri diagnostici vengono archiviati all'account di archiviazione non appena viene generati nuovi dati dell'evento.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Archivio registri diagnostici tramite i cmdlet di PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Proprietà         | Obbligatorio | Descrizione                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId       | Sì      | ID risorsa della risorsa in cui si desidera impostare un'impostazione di diagnostica.                            |
| StorageAccountId | No       | ID risorsa dell'Account di archiviazione a cui deve essere salvati registri diagnostici.                          |
| Categorie       | No       | Elenco delimitato da virgole di categorie log per abilitare.                                                     |
| Attivato          | Sì      | Valore Boolean che indica se la diagnostica è attivata o disattivate in questa risorsa.                  |
| RetentionEnabled | No       | Valore Boolean che indica se un criterio di conservazione sono abilitati in questa risorsa.                            |
| RetentionInDays  | No       | Numero di giorni per cui gli eventi devono essere mantenuti compreso tra 1 e 2147483647. Il valore zero archivia i registri di tempo indefinito. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Archiviare il Registro di attività tramite CLI multipiattaforma

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Proprietà         | Obbligatorio | Descrizione                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId       | Sì      | ID risorsa della risorsa in cui si desidera impostare un'impostazione di diagnostica.                            |
| storageId        | No       | ID risorsa dell'Account di archiviazione a cui deve essere salvati registri diagnostici.                          |
| categorie       | No       | Elenco delimitato da virgole di categorie log per abilitare.                                                     |
| attivato          | Sì      | Valore Boolean che indica se la diagnostica è attivata o disattivate in questa risorsa.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archivio registri diagnostici tramite l'API REST
Per informazioni su come è possibile impostare un'impostazione di diagnostica tramite l'API REST di Azure Monitor, [è possibile visualizzare questo documento](https://msdn.microsoft.com/library/azure/dn931931.aspx) .

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schema dei registri diagnostici nell'account di archiviazione
Dopo avere configurato la archiviazione, viene creato un contenitore di spazio di archiviazione in account di archiviazione non appena si verifica un evento in una delle categorie log che è stata attivata. BLOB all'interno del contenitore seguire lo stesso formato in più registri diagnostici e registro attività. La struttura di tali BLOB è:

> Approfondimenti - log: {nome della categoria log} / resourceId = / ABBONAMENTI / {ID abbonamento} /RESOURCEGROUPS/ {nome gruppo di risorse} /PROVIDERS/ {nome provider risorsa} / {risorsa digitare} / {nome di risorsa} / y = {anno a quattro cifre numerica} / m = {mese a due cifre numerico} / d = {giorno a due cifre numerico} / h = {due cifre a 24 ore hour}/m=00/PT1H.json

O più semplice

> Approfondimenti - log: {nome della categoria log} / resourceId = / {risorsa Id} / y = {anno a quattro cifre numerica} / m = {mese a due cifre numerico} / d = {giorno a due cifre numerico} / h = {due cifre a 24 ore hour}/m=00/PT1H.json

Ad esempio, potrebbe essere un nome blob:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Ogni blob PT1H.json contiene un blob JSON di eventi che si sono verificati all'interno dell'ora specificato nell'URL blob (ad esempio, h = 12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json quando si verificano. Il valore minuti (m = 00) è sempre 00, poiché la registrazione diagnostica degli eventi sono suddivisi in ogni BLOB orarie.

All'interno del file PT1H.json ogni evento è archiviato nella matrice "record", seguire questo formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nome dell'elemento  | Descrizione                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| ora          | Timestamp quando l'evento è stato generato dal servizio di Azure elaborazione della richiesta corrispondente all'evento. |
| resourceId    | ID risorsa della risorsa interessata.                                                                       |
| Invece | Nome dell'operazione.                                                                                      |
| categoria      | Categoria di log dell'evento.                                                                                  |
| proprietà    | Set di `<Key, Value>` coppie di parole (ad esempio dizionario) che descrive i dettagli dell'evento.                            |

> [AZURE.NOTE] La proprietà e l'uso di queste proprietà può variare a seconda della risorsa.

## <a name="next-steps"></a>Passaggi successivi
- [Scaricare BLOB per l'analisi](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Registri diagnostici flusso a un hub di evento](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Per ulteriori informazioni sui registri diagnostici](monitoring-overview-of-diagnostic-logs.md)
