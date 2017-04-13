<properties
    pageTitle="Registro Analitica accede ricerca API REST | Microsoft Azure"
    description="Questa guida fornisce un'esercitazione di base che spiega come è possibile utilizzare la ricerca di Log Analitica API REST in operazioni di gestione famiglia di prodotti (OMS) e vengono forniti esempi che illustrano come usare i comandi."
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


# <a name="log-analytics-log-search-rest-api"></a>Registro Analitica Registro ricerca API REST

Questa guida fornisce un'esercitazione di base che spiega come è possibile utilizzare l'API REST di Log Analitica ricerca in operazioni di gestione famiglia di prodotti (OMS) e vengono forniti esempi che illustrano come usare i comandi. Alcuni degli esempi inclusi in questo articolo fare riferimento a informazioni dettagliate sui operativa, che rappresenta il nome della versione precedente di Log Analitica.

## <a name="overview-of-the-log-search-rest-api"></a>Panoramica della ricerca Log API REST

API REST ricerca Analitica Log è RESTful e sono accessibili tramite l'API di gestione risorse Azure. In questo documento sono disponibili esempi in cui l'API è possibile accedere mediante il [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento di riga di comando Apri origine che semplifica richiamare l'API di gestione risorse Azure. L'utilizzo di PowerShell e ARMClient è una delle numerose opzioni per accedere all'API di ricerca Analitica Log. È inoltre possibile utilizzare il modulo di Azure PowerShell per OperationalInsights che include i cmdlet per l'accesso a ricerca. Con questi strumenti è possibile utilizzare l'API di gestione risorse Azure REST per effettuare chiamate alle aree di lavoro OMS ed eseguire i comandi di ricerca al loro interno. L'API fornirà risultati della ricerca per l'utente nel formato JSON, che consente di utilizzare i risultati della ricerca in molti modi diversi a livello di programmazione.

Gestione risorse Azure può essere utilizzata tramite una [raccolta per .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) , nonché un' [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Esaminare le pagine web collegate per altre informazioni.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Esercitazione all'API REST di Log Analitica ricerca base

### <a name="to-use-the-arm-client"></a>Per utilizzare il Client ARM

1. Installare [Chocolatey](https://chocolatey.org/)un aprire Gestione pacchetto di origine per Windows. Aprire una finestra del prompt dei comandi come amministratore e quindi eseguire il comando seguente:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Installare il ARMClient eseguendo il comando seguente:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Per eseguire una ricerca semplice utilizzando il ARMClient

1. Accedere al proprio account Microsoft o OrgID:

    ```
    armclient login
    ```

    Un account di accesso ha esito positivo sono elencate tutte le sottoscrizioni collegate all'account specificato:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Ottenere le aree di lavoro di operazioni Management Suite:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Una chiamata Get da output tutte le aree di lavoro associati all'abbonamento:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Creare la variabile di ricerca:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Eseguire una ricerca con la nuova variabile di ricerca:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Esempi di riferimento all'API REST di ricerca Analitica accedere
Negli esempi seguenti viene illustrato come utilizzare l'API di ricerca.

### <a name="search---actionread"></a>Ricerca - azione/letto

**Url di esempio:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Richiesta:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
Nella tabella seguente vengono descritte le proprietà disponibili.

|**Proprietà**|**Descrizione**|
|---|---|
|In alto|Numero massimo di risultati da restituire.|
|evidenziare|Contiene parametri pre e post, utilizzati in genere per evidenziare i campi corrispondenti|
|versioni precedenti|Prefissi la stringa specificata per i campi corrispondenti.|
|Inserisci|Accoda la stringa specificata per i campi corrispondenti.|
|query|Query di ricerca utilizzata per raccogliere e restituire i risultati.|
|inizio|Inizio dell'intervallo di tempo desiderato risultati possano essere trovati da.|
|fine|Fine dell'intervallo di tempo desiderato risultati possano essere trovati da.|


**Risposta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Eseguire una ricerca / {ID} - azione/letto

**Richiedere il contenuto di una ricerca salvata:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Se la ricerca restituisce con lo stato 'In sospeso', quindi polling i risultati aggiornati può essere eseguito tramite l'API. Dopo aver min 6, il risultato della ricerca verrà rimosse dalla cache e HTTP eliminato verrà restituito. Se la richiesta di ricerca iniziale restituito immediatamente ' correttamente, non verranno aggiunti alla cache causa questa API restituire HTTP eliminato se eseguire la query. Il contenuto di un risultato HTTP 200 sarà nello stesso formato la richiesta di ricerca iniziale solo con valori aggiornati.

### <a name="saved-searches---rest-only"></a>Ricerche - salvate solo resto

**Richiedere l'elenco delle ricerche salvate:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Metodi supportati: GET inserire eliminare

Metodi di raccolta supportati: GET

Nella tabella seguente vengono descritte le proprietà disponibili.

|Proprietà|Descrizione|
|---|---|
|ID|Identificatore univoco.|
|ETag|**Necessario per Patch**. In ogni scrittura, aggiornati dal server. Valore deve essere uguale al valore corrente archiviato o "*" per l'aggiornamento. 409 restituito per valori precedente o non valido.|
|Properties.query|**Obbligatorio**. La query di ricerca.|
|properties.displayName|**Obbligatorio**. Nome visualizzato definito utente della query. Se modellare come risorsa Azure, il risultato sarà un Tag.|
|Properties.Category|**Obbligatorio**. L'utente definito categoria della query. Se modellare come risorsa Azure il risultato sarà un Tag.|

>[AZURE.NOTE] API di ricerca Analitica Log attualmente restituisce creati dall'utente ricerche quando ricercate ricerche salvate in un'area di lavoro salvate. L'API non restituirà ricerche salvate fornite da solutions al momento. Questa funzionalità verrà aggiunti in un secondo momento.

### <a name="create-saved-searches"></a>Creare ricerche salvate

**Richiesta:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Cancellare ricerche salvate

**Richiesta:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Ricerche per aggiornamenti salvati

 **Richiesta:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadati - JSON solo

Ecco un modo per visualizzare i campi per tutti i tipi di log per i dati raccolti nell'area di lavoro. Ad esempio, se si vuole che si sa se il tipo di evento dispone di un campo denominato Computer, questo è un modo per cercare e confermare.

**Richiesta di campi:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Risposta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

Nella tabella seguente vengono descritte le proprietà disponibili.

|**Proprietà**|**Descrizione**|
|---|---|
|nome|Nome campo.|
|displayName|Nome visualizzato del campo.|
|tipo|Il tipo di valore del campo.|
|facetable|Combinazione di corrente 'indicizzato', ' archiviati ' e 'facet' proprietà.|
|visualizzazione|Proprietà corrente 'Visualizza'. True se il campo è visibile nella ricerca.|
|ownerType|Ridurre a solo i tipi che appartengono a onboarded IP.|


## <a name="optional-parameters"></a>Parametri facoltativi
Le informazioni seguenti vengono illustrati i parametri facoltativi disponibili.

### <a name="highlighting"></a>L'evidenziazione

Il parametro "Evidenziazione" è un parametro facoltativo è possibile utilizzare per richiedere sottosistema ricerca includere un insieme di indicatori nella sua risposta.

Questi indicatori indicano l'inizio e fine testo evidenziato che soddisfa le condizioni illustrate nella query di ricerca.
È possibile specificare gli indicatori di inizio e fine verranno usati dal servizio di ricerca per disporre il termine evidenziato.

**Query di ricerca di esempio**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Risultato di esempio:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Si noti che il risultato precedente contiene un messaggio di errore che è stato il prefisso e aggiunto.

## <a name="computer-groups"></a>Gruppi di computer

Gruppi di computer sono speciali ricerche che restituiscono un insieme di computer.  È possibile usare un gruppo di computer nelle altre query per limitare i risultati per il computer del gruppo.  Un gruppo di computer viene implementato come una ricerca salvata con un tag di gruppo con un valore del Computer.

Di seguito è una risposta di esempio per un gruppo di computer.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Recupero di gruppi di computer

Utilizzare il metodo Get con l'ID di gruppo per recuperare un gruppo di computer.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Creazione o aggiornamento di un gruppo di computer

Utilizzare il metodo di caricamento con univoco salvato ID di ricerca per creare un nuovo gruppo di computer. Se si utilizza un ID di gruppo computer esistente, verrà modificato che uno. Quando si crea un gruppo di computer nella console di OMS, l'ID viene creata dal gruppo e il nome.

La query usata per la definizione del gruppo deve restituire un insieme di computer per il gruppo a funzionare correttamente.  È consigliabile interrompere una query con *| Computer distinti* per assicurarsi che i dati corretti vengono restituiti.

La definizione della ricerca salvata deve includere un tag denominato gruppo con un valore del Computer per la ricerca a essere suddivise in un gruppo di computer.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Eliminazione di gruppi di computer

Utilizzare il metodo Delete con l'ID di gruppo per eliminare un gruppo di computer.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [ricerche dei registri](log-analytics-log-searches.md) creare query utilizzando i campi personalizzati per i criteri.
