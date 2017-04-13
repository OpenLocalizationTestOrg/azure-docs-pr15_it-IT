<properties
    pageTitle="Registrazione di Azure archivio chiave | Microsoft Azure"
    description="Utilizzare questa esercitazione per un'introduzione a Azure chiave archivio registrazione."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Registrazione di Azure archivio chiave #
Azure chiave archivio è disponibile nella maggior parte delle aree geografiche. Per ulteriori informazioni, vedere [chiave archivio prezzi pagina](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduzione  
Dopo la creazione di uno o più archivi chiavi, è probabile che che si desidera eseguire il monitoraggio delle come e quando i tasti archivi sono accessibile e l'autore. È possibile eseguire questa operazione abilitando la registrazione per archivio chiave, che consente di salvare le informazioni in un account di archiviazione Azure che fornisce. Un nuovo contenitore denominato **auditevent di registri approfondimenti** viene creato automaticamente per il proprio account di archiviazione specificato ed è possibile utilizzare lo stesso account di archiviazione per la raccolta dei registri per più archivi di chiave.

È possibile accedere al massimo le informazioni di registrazione, 10 minuti dopo la chiave archivio operazione. Nella maggior parte dei casi, sarà più rapido rispetto a quella.  Sia gestione dei registri nell'account di archiviazione:

- Utilizzare i metodi di controllo accesso Azure standard per proteggere i registri limitando chi può accedere.
- Eliminare i registri che non si desidera mantenere nell'account di archiviazione.

Utilizzare questa esercitazione per iniziare a utilizzare la registrazione, per creare l'account di archiviazione, attivare la registrazione e interpretare le informazioni di registrazione raccolte di Azure chiave archivio.  


>[AZURE.NOTE]  In questa esercitazione non include istruzioni per la procedura creare archivi chiavi, le chiavi o informazioni riservate. Per informazioni, vedere [Guida introduttiva di Azure chiave archivio](key-vault-get-started.md). In alternativa, per interfaccia della riga di comando multipiattaforma istruzioni, vedere [questa esercitazione equivalente](key-vault-manage-with-cli.md).
>
>Al momento non è possibile configurare Azure chiave archivio nel portale di Azure. Utilizzare queste istruzioni di PowerShell Azure.

I registri raccolti sono visualizzabili tramite analitica Log dal gruppo di gestione di operazioni. Per ulteriori informazioni, vedere [soluzione Azure chiave archivio (Preview) in Log Analitica](../log-analytics/log-analytics-azure-key-vault.md).

Per informazioni generali su Azure chiave archivio, vedere [Novità Azure chiave archivio?](key-vault-whatis.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

- Archivio di chiave esistente che è stato utilizzato.  
- PowerShell Azure, **versione minima di 1.0.1**. Per installare PowerShell Azure e associare all'abbonamento Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Se già stato installato PowerShell Azure e non conoscono la versione dalla console di PowerShell di Azure, digitare `(Get-Module azure -ListAvailable).Version`.  
- Spazio di archiviazione sufficiente su Azure per i registri di archivio di chiave.


## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Avviare una sessione di PowerShell Azure e accedere al proprio account Azure con il comando seguente:  

    Login-AzureRmAccount

Nella finestra del browser a comparsa, immettere il nome utente dell'account Azure e la password. PowerShell Azure otterrà tutte le sottoscrizioni che sono associati a questo account e per impostazione predefinita, viene utilizzato il primo.

Se si hanno più abbonamenti, è necessario specificare uno specifico utilizzato per creare l'archivio di chiave Azure. Digitare quanto segue per visualizzare le sottoscrizioni per l'account:

    Get-AzureRmSubscription

Per specificare l'abbonamento a cui è associato l'archivio chiave che effettuerà l'accesso, digitare:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Per ulteriori informazioni sulla configurazione di Azure PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Creare un nuovo account di archiviazione per i registri ##

Sebbene sia possibile usare un account esistente di spazio di archiviazione per i registri, si verrà creare un nuovo account di spazio di archiviazione dedicate a registri chiave archivio. Per maggiore comodità per quando è necessario specificarlo in un secondo momento, si verranno memorizzata i dettagli in una variabile denominata **sa**.

Per ulteriori semplificare la gestione, si userà anche stesso gruppo di risorse a quello che contiene il nostro archivio chiave. [Esercitazione introduttiva](key-vault-get-started.md), questo gruppo di risorse è denominato **ContosoResourceGroup** ed è necessario continuare a usare il percorso Asia orientale. Sostituire i valori per il proprio come applicabili:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Se si decide di usare un account di archiviazione esistente, è necessario utilizzare lo stesso abbonamento come l'archivio chiave e utilizzi il modello di distribuzione di Manager delle risorse, anziché il modello di distribuzione classica.

## <a id="identify"></a>Identificare l'archivio di chiave per i registri ##

Nelle esercitazioni iniziare il recupero, il nome di archivio chiave era **ContosoKeyVault**, in modo che si continuerà a utilizzare tale nome e memorizzare i dettagli in una variabile denominata **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Attivare la registrazione ##

Per attivare la registrazione di archivio di chiave, si userà il cmdlet Set-AzureRmDiagnosticSetting, insieme a variabili creato per il nuovo account di archiviazione e la chiave archivio. È anche necessario impostare il **-abilitato** contrassegnare a **$true** e impostare la categoria a AuditEvent (la categoria sola per la registrazione di archivio di chiave):


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

L'output per questa include:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Ciò significa che è ora abilitata per l'archivio chiave, il salvataggio delle informazioni al proprio account di archiviazione.

È facoltativamente possibile impostare criteri di conservazione per i registri in modo che i registri precedenti saranno eliminati automaticamente. Ad esempio, impostare i criteri di conservazione utilizzando contrassegno **- RetentionEnabled** **$true** e **- RetentionInDays** parametro impostato **90** , in modo che i registri 90 giorni saranno eliminati automaticamente.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Informazioni di connessione:

- Tutte le richieste API REST autenticate connessi, che include le richieste non riuscite in seguito le autorizzazioni di accesso, errori del sistema o richieste non valide.
- Operazioni sulla chiave archivio stesso, che include la creazione, eliminazione e impostazione di criteri di accesso archivio chiave, e aggiornamento degli attributi di archivio chiave come tag.
- Operazioni su chiavi e informazioni riservate nell'archivio di chiave, che include la creazione, modifica o eliminazione di queste chiavi o informazioni riservate; operazioni, ad esempio segno, verificare, crittografare, decrittografare, a capo e annullare la disposizione del tasti, ottenere informazioni riservate, elenco tasti e informazioni riservate e le relative versioni.
- Richieste non autenticate che restituiscono una risposta 401. Ad esempio, le richieste di non dispone di un token del titolare, o non corretti o scaduto, o con un token non valido.  


## <a id="access"></a>I registri di accesso ##

Archivio chiave log sono archiviate nel contenitore di **informazioni dettagliate sui registri-auditevent** nell'account di archiviazione che è specificato. Per visualizzare tutti i BLOB in questo contenitore, digitare:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

L'output sarà simile sarà simile alla seguente:

**Contenitore Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Nome**

**----**

**resourceId = / ABBONAMENTI/361DA5D4-A47A-79 C 4-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/provider/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId = / ABBONAMENTI/361DA5D4-A47A-79 C 4-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/provider/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

* * resourceId = / ABBONAMENTI/361DA5D4-A47A-79 C 4-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/provider/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Come si vede da questo output, i BLOB seguono una convenzione di denominazione: **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

I valori di data e ora utilizzano ora UTC.

Perché lo stesso account di archiviazione può essere utilizzato per raccogliere i registri di più risorse, l'ID risorsa completo del nome blob è molto utile per accedere o scaricare solo i BLOB desiderati. Ma prima di procedere è prima di tutto illustreremo come scaricare tutti i BLOB.

Prima di tutto creare una cartella per scaricare i BLOB. Per esempio:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Ottenere un elenco di tutti i BLOB:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Inviare l'elenco tramite 'Get-AzureStorageBlobContent"per scaricare i BLOB nella cartella di destinazione:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Quando si esegue il comando secondo la **/** delimitatore nei nomi blob creare una struttura di cartelle completo all'interno della cartella di destinazione e la struttura verrà usata per scaricare e archiviare i BLOB come file.

Per scaricare in modo selettivo BLOB, usare i caratteri jolly. Per esempio:

- Se si hanno più archivi di chiave e si desidera scaricare dei registri per un solo archivio chiave, denominata CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Se si dispone di più gruppi di risorse e si desidera scaricare dei registri per solo un gruppo di risorse, utilizzare `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Se si desidera scaricare tutti i log per il mese di gennaio 2016, utilizzare `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

A questo punto si è pronti iniziare esamina novità nei log. Ma prima di passare a cui due altri parametri per Get-AzureRmDiagnosticSetting che potrebbe essere necessario conoscere:

- Per eseguire query lo stato delle impostazioni di diagnostiche per la risorsa archivio chiave:`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Per disattivare la registrazione per la risorsa archivio chiave:`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretare i registri di archivio di chiave ##

Ogni BLOB vengono memorizzati come testo formattato come blob JSON. Si tratta di una voce del log di esempio esecuzione `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


La tabella seguente elenca i nomi dei campi e le descrizioni.


| Nome campo        | Descrizione |
| ------------- |-------------|
| ora      | Data e l'ora UTC ().|
| resourceId      | ID risorsa Manager delle risorse Azure. Per i registri di archivio di chiave, è sempre l'ID di risorsa chiave archivio.|
| Invece      | Nome dell'operazione, come descritto nella tabella seguente.|
| operationVersion      | Questa è la versione di API REST richiesta dal client.|
| categoria      | Per i registri di archivio di chiave, AuditEvent è il valore singolo, disponibile.|
| dell'enumeratore      | Risultato della richiesta API REST.|
| resultSignature      | Stato HTTP.|
| resultDescription     | Descrizione aggiuntiva il risultato, se disponibile.|
| durationMs      | Tempo che necessario per rispondere alla richiesta di API REST, in millisecondi. Questa operazione non include la latenza di rete, in modo che il tempo che misurare sul lato client potrebbe non corrispondere a questo momento.|
| callerIpAddress      | Indirizzo IP del client che ha effettuato la richiesta.|
| correlationId      | Un GUID facoltativo che il cliente può passare per correlare i log lato client con i log lato servizio (chiave archivio).|
| identità      | Identità del token presentato quando si effettua la richiesta di API REST. Si tratta in genere "utente", "principale di servizio" o una combinazione "utente + appId" come nel caso di una richiesta da un cmdlet di PowerShell Azure.|
| proprietà      | In questo campo conterrà informazioni diverse in base all'operazione (invece). Nella maggior parte dei casi, contiene informazioni del client (la stringa agente utente passata dal client), la richiesta API REST esatta URI e codice di stato HTTP. Inoltre, quando viene restituito un oggetto in seguito a una richiesta (ad esempio KeyCreate o VaultGet) anche conterrà la chiave URI (come "id"), URI archivio o URI segreto.|




I valori dei campi **invece** sono in formato ObjectVerb. Per esempio:

- Tutte le operazioni di archivio chiave hanno la ' archivio`<action>`' formato, ad esempio `VaultGet` e `VaultCreate`.

- Tutte le operazioni chiave hanno la ' chiave`<action>`' formato, ad esempio `KeySign` e `KeyList`.

- Tutte le operazioni segrete hanno la ' segreto`<action>`' formato, ad esempio `SecretGet` e `SecretListVersions`.

Nella tabella seguente elenca gli invece e comando API REST corrispondente.

| Invece        | Comando API REST |
| ------------- |-------------|
| Autenticazione      | Tramite endpoint di Azure Active Directory|
| VaultGet      | [Ottenere informazioni su un archivio di chiave](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Creare o aggiornare un archivio di chiave](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Eliminare un archivio di chiave](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Aggiornare un archivio di chiave](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Elenco di tutti gli archivi chiavi in un gruppo di risorse](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Creare una chiave](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Ottenere informazioni su una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importare una chiave in un archivio](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Copia di backup una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Eliminare una chiave](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Ripristinare una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Accedere con un tasto](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Verificare con una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Disporre di una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Annullare l'inserimento di una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Crittografa con una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Decrittografare con una chiave](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Aggiornare una chiave](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Elenco di tasti in un archivio](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [Elencare le versioni di una chiave](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Creare un segreto](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Ottenere segreta](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Aggiornare un segreto](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Eliminare un segreto](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Elenco di informazioni riservate in un archivio](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Elenco versioni di un segreto](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Passaggi successivi ##

Per un'esercitazione che utilizza Azure chiave archivio in un'applicazione web, vedere [Usare Azure chiave archivio da un'applicazione Web](key-vault-use-from-web-application.md).

Per la programmazione di riferimenti, vedere [Guida per gli sviluppatori di Azure chiave archivio](key-vault-developers-guide.md).

Per un elenco dei cmdlet di Azure PowerShell 1.0 per l'archivio di chiave di Azure, vedere [Cmdlet di archivio di Azure chiave](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Per l'esercitazione sulla rotazione delle chiavi e il controllo del Registro di Azure chiave archivio, vedere [come configurazione archivio chiave con rotazione to end chiave e il controllo](key-vault-key-rotation-log-monitoring.md).
