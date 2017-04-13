<properties
    pageTitle="Archiviare il registro attività Azure | Microsoft Azure"
    description="Informazioni su come archiviare il Registro di attività di Azure per la conservazione a lungo termine in un account di archiviazione."
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>Archiviare il registro attività Azure
In questo articolo, ecco come è possibile utilizzare il portale Azure PowerShell Cmdlets o multipiattaforma CLI per archiviare il [**Registro di Azure attività**](monitoring-overview-activity-logs.md) in un account di archiviazione. Questa opzione è utile se si desidera conservare il Registro di attività più di 90 giorni (con il criterio di conservazione controllo completo) per la verifica, analisi statica o copia di backup. Se è necessario conservare gli eventi per 90 giorni o meno non necessarie configurare l'archiviazione di un account di archiviazione, poiché gli eventi del Registro di attività vengono conservati nella piattaforma Azure per 90 giorni senza abilitare archiviazione.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) a cui è possibile archiviare il Registro di attività. È consigliabile non utilizzare un account di archiviazione esistente che contiene dati di altri, non il monitoraggio in essa contenuti in modo che è possibile controllare meglio accesso ai dati di monitoraggio. Tuttavia, se si desidera archiviare anche registri diagnostici e il formato per un account di archiviazione, può essere utile usare tale account di archiviazione per il Registro di attività anche per mantenere tutti i dati di monitoraggio in una posizione centrale. L'account di archiviazione in uso deve essere un account di archiviazione generale, non un account di archiviazione blob.

## <a name="log-profile"></a>Profilo log
Per archiviare il Registro di attività utilizzando uno dei metodi seguenti, si imposta il **Profilo Log** per una sottoscrizione. Profilo Log definisce il tipo di eventi che vengono archiviati o in streaming e gli output, ovvero hub account e/o evento lo spazio di archiviazione. Definisce inoltre i criteri di conservazione (numero di giorni per cui le) per gli eventi archiviati in un account di archiviazione. Se i criteri di conservazione sono impostato su zero, gli eventi sono archiviati in modo indefinito. In caso contrario, è possibile impostare questa operazione su un valore compreso tra 1 e 2147483647. [è possibile leggere informazioni sul registro dei profili qui](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## <a name="archive-the-activity-log-using-the-portal"></a>Archiviare il Registro di attività tramite il portale
1. Nel portale, fare clic sul collegamento **Registro attività** nella barra di spostamento sinistro. Se non è visibile un collegamento per il registro attività, selezionare il collegamento **Altri servizi** .

    ![Passare al blade registro attività](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Nella parte superiore e il, fare clic su **Esporta**.

    ![Fare clic sul pulsante Esporta](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. In e il che viene visualizzata, selezionare la casella di controllo per **esportare in un account di archiviazione** e selezionare un account di archiviazione.

    ![Configurare un account di archiviazione](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Utilizzando la casella di testo o un dispositivo di scorrimento, definire un numero di giorni per cui mantenere attività eventi nell'account di archiviazione. Se si preferisce che i dati mantenuta nell'account di archiviazione tempo indefinito, impostare questo numero per difetto.
5. Fare clic su **Salva**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiviare il Registro di attività tramite PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Proprietà         | Obbligatorio | Descrizione                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | No       | ID risorsa dell'Account di archiviazione a cui devono essere salvati i registri delle attività.                                                                                                                                                                                                                        |
| Percorsi        | Sì      | Elenco delimitato da virgole delle aree per il quale si desidera raccogliere eventi del Log di attività. È possibile visualizzare un elenco di tutte le aree geografiche [, visitare la pagina](https://azure.microsoft.com/en-us/regions) o tramite [L'API REST di Azure Management](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays  | Sì      | Numero di giorni per cui occorre conservare gli eventi, compreso tra 1 e 2147483647. Il valore zero sono archiviati i registri di tempo indefinito (sempre).                                                                                                                                                                                             |
| Categorie       | Sì      | Elenco delimitato da virgole delle categorie di eventi che devono essere raccolti. Valori possibili sono scrittura, eliminazione e azione.                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>Archiviare il Registro di attività tramite CLI
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Proprietà        | Obbligatorio | Descrizione                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome            | Sì      | Nome del profilo log.                                                                                                                                                                                                                                                                         |
| storageId       | No       | ID risorsa dell'Account di archiviazione a cui devono essere salvati i registri delle attività.                                                                                                                                                                                                                        |
| percorsi       | Sì      | Elenco delimitato da virgole delle aree per il quale si desidera raccogliere eventi del Log di attività. È possibile visualizzare un elenco di tutte le aree geografiche [, visitare la pagina](https://azure.microsoft.com/en-us/regions) o tramite [L'API REST di Azure Management](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Sì      | Numero di giorni per cui occorre conservare gli eventi, compreso tra 1 e 2147483647. Il valore zero verrà archiviati i registri di tempo indefinito (sempre).                                                                                                                                                                                             |
| categorie      | Sì      | Elenco delimitato da virgole delle categorie di eventi che devono essere raccolti. Valori possibili sono scrittura, eliminazione e azione.                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>Schema di archiviazione del registro attività
Dopo avere configurato la archiviazione, un contenitore di archiviazione verrà creato in account di archiviazione non appena si verifica un evento di registro attività. BLOB all'interno del contenitore seguire lo stesso formato del registro attività e i registri diagnostici. La struttura di tali BLOB è:

> Approfondimenti-operativi-registri/name = predefinito/resourceId = / ABBONAMENTI / {ID abbonamento} / y = {anno a quattro cifre numerica} / m = {mese a due cifre numerico} / d = {giorno a due cifre numerico} / h = {due cifre a 24 ore hour}/m=00/PT1H.json

Ad esempio, potrebbe essere un nome blob:

> Insights-OPERATIONAL-Logs/Name=default/ResourceID=/Subscriptions/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON

Ogni blob PT1H.json contiene un blob JSON di eventi che si sono verificati all'interno dell'ora specificato nell'URL blob (ad esempio h = 12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json quando si verificano. Il valore minuti (m = 00) è sempre 00, poiché gli eventi del Registro di attività sono suddivisi in ogni BLOB orarie.

All'interno del file PT1H.json ogni evento è archiviato nella matrice "record", seguire questo formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nome dell'elemento    | Descrizione                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| ora            | Timestamp quando l'evento è stato generato dal servizio di Azure elaborazione della richiesta corrispondente all'evento.    |
| resourceId      | ID risorsa della risorsa interessata.                                                                          |
| Invece   | Nome dell'operazione.                                                                                         |
| categoria        | Categoria dell'azione, ad esempio. Scrittura, lettura, azione.                                                               |
| dell'enumeratore      | Il tipo di risultato, ad esempio. Esito positivo, errore, inizio                                                            |
| resultSignature | Dipende dal tipo di risorsa.                                                                                  |
| durationMs      | Durata dell'operazione di in millisecondi                                                                      |
| callerIpAddress | Indirizzo IP dell'utente che ha eseguito l'operazione, attestazioni UPN o Richiedi SPN in base alla disponibilità.         |
| correlationId   | In genere GUID in formato stringa. Gli eventi che condividono un correlationId appartengono all'azione uber stesso.         |
| identità        | Blob JSON che descrive le autorizzazioni e sulle attestazioni.                                                             |
| autorizzazione   | BLOB di proprietà RBAC dell'evento. In genere include le proprietà "azioni", "ruolo" e "ambito".            |
| livello           | Livello dell'evento. Uno dei seguenti valori: "Critico", "Errore", "Avviso", "Informativo" e "Dettagliato" |
| posizione        | Area geografica in cui si è verificato il percorso (o globale).                                                             |
| proprietà      | Set di `<Key, Value>` coppie di parole (ad esempio dizionario) che descrive i dettagli dell'evento.                             |

> [AZURE.NOTE] La proprietà e l'uso di queste proprietà può variare a seconda della risorsa.

## <a name="next-steps"></a>Passaggi successivi
- [Scaricare BLOB per l'analisi](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Flusso il Registro di attività a un hub di evento](monitoring-stream-activity-logs-event-hubs.md)
- [Per ulteriori informazioni sul Log di attività](monitoring-overview-activity-logs.md)
