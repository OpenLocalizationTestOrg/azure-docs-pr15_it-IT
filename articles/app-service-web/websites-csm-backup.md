<properties
    pageTitle="RESTO consente di eseguire il backup e ripristinare le applicazioni di servizio di App"
    description="Informazioni su come utilizzare chiamate all'API REST eseguire il backup e ripristinare un'applicazione di servizio App Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>RESTO consente di eseguire il backup e ripristinare le applicazioni di servizio di App

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API REST](websites-csm-backup.md)

[Le applicazioni di servizio App](https://azure.microsoft.com/services/app-service/web/) possibile backup come BLOB di archiviazione Azure. Il backup può inoltre contenere i database dell'applicazione. Se l'app è mai eliminato per errore o se l'app deve essere ripristinata a una versione precedente, possono essere ripristinato da un backup precedente. Backup possono essere eseguiti in qualsiasi momento su richiesta o backup possono essere pianificati intervalli appropriata.

In questo articolo viene spiegato come eseguire il backup e ripristinare un'app con le richieste API REST. Se si desidera creare e gestire app backup graficamente tramite il portale di Azure, vedere [eseguire il backup un'app web nel servizio App Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Guida introduttiva
Per inviare richieste REST, è necessario conoscere l'app **nome** **gruppo di risorse**e **id abbonamento**. Queste informazioni sono disponibili facendo clic sull'app e il **Servizio di App** del [portale di Azure](https://portal.azure.com). Per esempi in questo articolo, è in corso la configurazione il sito Web **backuprestoreapiexamples.azurewebsites.net**. Viene archiviato nel gruppo di risorse WestUS di Web predefinito ed è in esecuzione su un abbonamento con l'ID 00001111-2222-3333-4444-555566667777.

![Informazioni sui siti Web di esempio][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Eseguire il backup e ripristino API REST
Verranno ora illustrate le caratteristiche diversi esempi di come usare l'API REST per eseguire il backup e ripristinare un'app. Include un corpo della richiesta URL e HTTP. L'URL di esempio contiene segnaposto racchiuso tra parentesi graffe, ad esempio {id abbonamento}. Sostituire i segnaposto con le informazioni corrispondenti per l'app. Per riferimento, ecco una spiegazione di ogni segnaposto che compare nell'URL di esempio.

* id abbonamento: ID della sottoscrizione Azure contenente l'app
* risorsa nome gruppo: nome del gruppo di risorse contenente l'app
* nome: nome dell'app
* copia di backup-id: ID del backup app

Per la documentazione completa dell'API, inclusi diversi parametri facoltativi che possono essere inclusi nella richiesta HTTP, vedere [Risorse di Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Eseguire il backup di un'app su richiesta
Per eseguire il backup un'app immediatamente, inviare una richiesta **POST** per **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Ecco cosa l'URL è simile alla tramite il sito Web di esempio. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backup/**

Fornire un oggetto JSON nel corpo della convocazione per specificare l'account di archiviazione da utilizzare per il backup. L'oggetto JSON deve avere una proprietà denominata **storageAccountUrl**, che contiene un [URL SA](../storage/storage-dotnet-shared-access-signature-part-1.md) concessione dell'accesso di scrittura per il contenitore di archiviazione Azure contenente il backup blob. Se si desidera eseguire il backup del database, è necessario fornire anche un elenco contenente i nomi, tipi e le stringhe di connessione di database per eseguire il backup.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Una copia di backup dell'app inizia immediatamente quando si riceve la richiesta. Il processo di backup può richiedere molto tempo. La risposta HTTP contiene un ID che è possibile usare in un'altra richiesta per controllare lo stato del backup. Ecco un esempio del corpo della risposta alla richiesta backup.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Messaggi di errore sono disponibili nella proprietà log della risposta HTTP.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Pianificare backup automatici
Oltre a backup un'app su richiesta, è anche possibile pianificare una copia di backup da eseguire automaticamente.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurare una nuova programmazione di backup automatica
Per impostare una pianificazione di backup, inviare una richiesta di **mettere** a **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Ecco è simile all'URL per il sito Web di esempio. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/config/backup**

Nel corpo della richiesta deve avere un oggetto JSON che specifica la configurazione del backup. Ecco un esempio con tutti i parametri necessari.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

In questo esempio configurato l'app backup automaticamente ogni sette giorni. I parametri **frequencyInterval** e **frequencyUnit** insieme determinare quante volte si verificano i backup. Valori validi per **frequencyUnit** sono **ora** e il **giorno**. Per eseguire il backup un'app ogni 12 ore, ad esempio, è possibile impostare la frequencyInterval a 12 e frequencyUnit all'ora.

Backup precedente vengono automaticamente rimossi dall'account di archiviazione. È possibile controllare l'età di backup possono essere impostando il parametro **retentionPeriodInDays** . Se si vuole avere sempre almeno un backup salvato, indipendentemente dalla modalità precedente, è **keepAtLeastOneBackup** su true.

### <a name="get-the-automatic-backup-schedule"></a>Ottenere la pianificazione di backup automatici
Per ottenere backup della configurazione dell'applicazione, inviare una richiesta **POST** all'URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

L'URL per il sito di esempio è **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Ottenere lo stato di una copia di backup
A seconda delle dimensioni l'app viene eseguita, una copia di backup potrebbe richiedere un po' di tempo per completare. Backup potrebbero anche non riesce, il timeout o parzialmente completata. Per visualizzare lo stato del backup di tutto un'app, inviare una richiesta **GET** all'URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Per visualizzare lo stato di una copia di backup specifico, inviare una richiesta GET per l' URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Ecco è simile all'URL per il sito Web di esempio. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/Backups/1**

Corpo della risposta contiene un oggetto JSON simile a questo esempio.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

Lo stato di una copia di backup è un tipo enumerato. Ecco ogni stato possibili.

* 0 – in corso: il backup è stato avviato ma non è ancora stata completata.
* 1 – non riuscita: Il backup non riuscito.
* 2 – completata: Il backup è stato completato correttamente.
* 3 – TimedOut: Il backup non è stato completato nel tempo e stato annullato.
* 4-creazione: La richiesta di backup in coda ma non è stata avviata.
* 5-ignorata: Il backup non è stata eseguita a causa di una pianificazione di attivazione di un numero eccessivo di backup.
* 6-fallita: Il backup completato, ma alcuni file non eseguiti perché non può essere letta. In genere infatti un blocco esclusivo è stato inserito nel file.
* 7-DeleteInProgress: Il backup è stata richiesta da eliminare, ma non è ancora stato eliminato.
* 8: eliminazione non riuscita: Impossibile eliminare il backup. È possibile che l'URL SA utilizzato per creare il backup è scaduto.
* 9 – eliminato: Il backup è stato eliminato.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Ripristinare un'app da un backup
Se è stato eliminato l'app oppure se si desidera ripristinare l'app a una versione precedente, è possibile ripristinare l'app da un backup. Per richiamare un ripristino, inviare una richiesta **POST** all'URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Ecco è simile all'URL per il sito Web di esempio. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/Backups/1/Restore**

Nel corpo della richiesta, inviare un oggetto JSON che contiene le proprietà per l'operazione di ripristino. Ecco un esempio contenente tutte le proprietà necessarie:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Ripristinare una nuova app
In alcuni casi è consigliabile creare una nuova app quando si ripristina un backup, anziché sovrascrivere un'app già esistente. A tale scopo, modificare l'URL della richiesta in modo che puntino all'app di nuovo per creare e impostare proprietà **sovrascrivere** il JSON su **false**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Eliminare una copia di backup di app
Se si desidera eliminare una copia di backup, inviare una richiesta di **eliminare** l' URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Ecco è simile all'URL per il sito Web di esempio. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/Backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Gestione SA URL del backup
Servizio App Azure tenterà di eliminare il backup dallo spazio di archiviazione di Azure mediante l'URL SA specificato al momento della creazione del backup. Se questo URL SA non è più valido, il backup non può essere eliminato tramite l'API REST. Tuttavia, è possibile aggiornare l'URL di SA associato a una copia di backup mediante l'invio di una richiesta **POST** all'URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Ecco è simile all'URL per il sito Web di esempio. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/Backups/1/List**

Nel corpo della richiesta, inviare un oggetto JSON che contiene il nuovo URL SA. Ecco un esempio.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Per motivi di sicurezza, l'URL di SA associato a una copia di backup non viene restituito quando si invia una richiesta GET per una copia di backup specifico. Se si desidera visualizzare l'URL di SA associato a una copia di backup, inviare una richiesta POST per lo stesso URL sopra. Includere un oggetto JSON vuoto nel corpo della richiesta. La risposta dal server contiene le informazioni del backup, inclusi l'URL di SA.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
