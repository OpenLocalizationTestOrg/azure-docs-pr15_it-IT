<properties
   pageTitle="Risolvere i problemi comuni distribuzione Azure | Microsoft Azure"
   description="Descrive come risolvere gli errori comuni quando si distribuiscono risorse Azure Gestione risorse di Azure."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="Errore di distribuzione, distribuzione di azure, la distribuzione di azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Risolvere i problemi comuni distribuzione Azure Gestione risorse Azure

In questo argomento descrive come è possibile risolvere alcuni comuni errori di distribuzione Azure che possono verificarsi. Se si desiderano maggiori informazioni sulla causa del problema con la distribuzione, vedere [le operazioni di distribuzione di visualizzazione](resource-manager-troubleshoot-deployments-portal.md) e quindi tornare in questo articolo per informazioni su come risolvere l'errore. Le sezioni di questo argomento dell'elenco viene visualizzato il codice di errore.

## <a name="invalid-template"></a>Modello non valido

Questo errore può verificarsi da vari tipi di errori. 

### <a name="syntax-error"></a>Errore di sintassi

Se si riceve un messaggio di errore che indica la convalida modello non è riuscita, potrebbe essere un problema di sintassi nel modello scelto. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Questo errore è facile perché le espressioni del modello possono essere più complesse. Ad esempio, l'assegnazione del nome seguenti per un account di archiviazione contiene una coppia di parentesi quadre, tre funzioni, tre serie di parentesi, un set di tra virgolette singole e una proprietà:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Se non si fornisce la sintassi corrispondente, il modello restituisce un valore diverso da quello si intende.

Quando si riceve questo tipo di errore, esaminare attentamente la sintassi delle espressioni. È consigliabile utilizzare un editor di JSON come [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Codice Visual Studio](resource-manager-vs-code.md), che possono segnalare errori di sintassi. 

### <a name="incorrect-segment-lengths"></a>Lunghezza segmento non corretto

Un altro modello non valido errore si verifica quando il nome della risorsa non è presente nel formato corretto.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Una risorsa di livello principale deve avere un segmento di meno del nome di in tipo di risorsa. Ogni segmento si distingue da una barra. Nell'esempio seguente, il tipo di sono presenti due segmenti e il nome contiene un segmento, in modo che si tratta di un **valore valido**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Ma nell'esempio seguente viene **nome non valido** perché contiene lo stesso numero di segmenti come tipo di.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Per le risorse figlio, il tipo e il nome ha lo stesso numero di segmenti. Questo numero di segmenti senso perché il nome completo e il tipo per l'elemento figlio include il nome dell'elemento padre e il tipo. Di conseguenza, il nome completo ha ancora un meno segmento rispetto al tipo completo. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Ottenere i segmenti destra può essere difficile con tipi di Manager delle risorse che vengono applicati tra i provider di risorse. Ad esempio, l'applicazione di un blocco di risorsa in un sito web richiede un tipo con quattro lati. Di conseguenza, il nome è tre segmenti:

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Indice di copia non è previsto

Questo errore **InvalidTemplate** quando l'elemento **Copia** applicato a una parte del modello che non supportano questo elemento. È possibile applicare l'elemento copia solo a un tipo di risorsa. Copia non è possibile applicare a una proprietà all'interno di un tipo di risorsa. Ad esempio, si applica copia a una macchina virtuale, ma non è possibile applicare ai dischi del sistema operativo per una macchina virtuale. In alcuni casi, è possibile convertire una risorsa figlio a una risorsa padre per creare un ciclo di copia. Per ulteriori informazioni sull'utilizzo di copia, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

### <a name="parameter-is-not-valid"></a>Parametro non è valido

Se il modello specifica valori consentiti per un parametro e specificare un valore non corrisponde a uno di questi valori, viene visualizzato un messaggio simile al seguente:

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Doppia verificare i valori consentiti nel modello e fornire una durante l'installazione.

## <a name="not-found-or-resource-not-found"></a>Non trovato (o delle risorse non trovato)

Quando il modello include il nome di una risorsa che non può essere risolti, viene visualizzato un messaggio di errore simile a:

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Se si sta tentando di distribuire la risorsa mancante nel modello, verificare se è necessario aggiungere una dipendenza. Manager delle risorse consente di ottimizzare la distribuzione mediante la creazione di risorse in parallelo, se possibile. Se una risorsa deve essere distribuita dopo un'altra risorsa, è necessario utilizzare l'elemento **dependsOn** nel modello scelto per creare una relazione in un'altra risorsa. Quando si distribuisce un'app web, deve essere presente il piano di servizio di App. Se non è stato specificato che l'applicazione web dipende dal piano di servizio di App, Gestione risorse crea entrambe le risorse nello stesso momento. Viene visualizzato un messaggio di errore che indica che la risorsa di piano di servizio App non viene trovata, poiché non esiste ancora quando si tenta di impostare una proprietà per il web app. Impedire l'errore mediante l'impostazione della dipendenza in web app.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
È inoltre possibile visualizzare questo messaggio di errore quando la risorsa è disponibile in un gruppo di risorse diverso da quello distribuito per. In questo caso, utilizzare la [funzione resourceId](resource-group-template-functions.md#resourceid) per ottenere il nome completo della risorsa.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Se si tenta di utilizzare le funzioni [listKeys](resource-group-template-functions.md#listkeys) o [riferimento](resource-group-template-functions.md#referenc) a una risorsa che non può essere risolti, viene visualizzato l'errore seguente:

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Cercare un'espressione che include la funzione di **riferimento** . Verificare che i valori dei parametri siano corrette.

## <a name="storage-account-already-exists-or-already-taken"></a>Spazio di archiviazione account esiste già (o già utilizzato)

Per gli account di archiviazione, è necessario specificare un nome per la risorsa sia univoco tra Azure. Se non si specifica un nome univoco, viene visualizzato un errore:

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

È possibile creare un nome univoco concatenando la convenzione di denominazione con il risultato della funzione [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Se si distribuisce un account di archiviazione con lo stesso nome di un account di archiviazione esistente nell'abbonamento, ma specificare un percorso diverso, si riceve un messaggio di errore che indica che l'account di archiviazione già esistente in un percorso diverso. Eliminare l'account di archiviazione esistente o specificare nella stessa posizione come account di archiviazione esistente.

## <a name="account-name-invalid"></a>Nome dell'account non valido

Viene visualizzato l'errore **AccountNameInvalid** quando si tenta di assegnare un nome che include un account di archiviazione di caratteri consentiti. Nomi degli account di archiviazione deve essere compreso tra 3 e 24 caratteri e utilizzare numeri e lettere minuscole solo.

## <a name="no-registered-provider-found"></a>Nessun provider registrato trovato

Quando si distribuisce risorse, potrebbe essere visualizzato il seguente codice di errore e il messaggio:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Questo errore viene visualizzato per uno dei tre motivi:

1. Percorso non è supportata per il tipo di risorsa
2. Versione dell'API non è supportata per il tipo di risorsa
3. Non è stato registrato il provider di risorse per l'abbonamento

Il messaggio di errore deve fornire i suggerimenti per i percorsi supportati e le versioni di API. È possibile modificare il modello a uno dei valori suggeriti. La maggior parte dei provider sono registrate automaticamente tramite il portale di Azure o la riga di comando in uso, ma non tutti. Se non è stato utilizzato un provider di risorse specifico prima, potrebbe essere necessario eseguire la registrazione del provider. È possibile scoprire ulteriori informazioni su provider di risorse tramite PowerShell o CLI Azure.

### <a name="powershell"></a>PowerShell

Per visualizzare lo stato di registrazione, utilizzare **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Per registrare un provider di servizi, utilizzare **Registro AzureRmResourceProvider** e fornire il nome del provider di risorse che si desidera registrare.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Per ottenere i percorsi supportati per un determinato tipo di risorsa, usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Per ottenere le versioni di API supportate per un determinato tipo di risorsa, usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>CLI Azure

Per verificare se è registrato il provider di servizi, utilizzare la `azure provider list` comando.

    azure provider list

Per registrare un provider di risorse, utilizzare la `azure provider register` comando e specificare *dello spazio dei nomi* per eseguire la registrazione.

    azure provider register Microsoft.Cdn

Per visualizzare le posizioni supportati e le versioni di API per un provider di risorse, usare:

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Operazione non consentita

Potrebbe essere problemi quando distribuzione supera una quota, che può essere per ogni gruppo di risorse, le sottoscrizioni, account e altri ambiti. L'abbonamento, ad esempio, può essere configurato per limitare il numero di core per un'area. Se si tenta di distribuire una macchina virtuale con più core rispetto alla quantità consentita, verrà visualizzato un messaggio di errore indicante che la quota superata.
Per informazioni quota completa, vedere [sottoscrizione Azure e limiti del servizio, le quote e vincoli](azure-subscription-service-limits.md).

Per esaminare le quote dell'abbonamento per core, è possibile utilizzare il `azure vm list-usage` comando CLI Azure. Nell'esempio seguente illustra che è "4" la quota di base per un account di valutazione gratuita:

    azure vm list-usage

Che restituisce:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Se si distribuisce un modello che consente di creare più di quattro core nell'area occidentale degli Stati Uniti, viene visualizzato un errore simile a quello di distribuzione:

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

O in PowerShell, è possibile utilizzare il cmdlet **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Che restituisce:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

In questi casi, è necessario accedere al portale di e file un problema di supporto per generare la quota per l'area geografica in cui si desidera distribuire.

> [AZURE.NOTE] Tenere presente che per i gruppi di risorse, la quota per ogni regione singoli, non per l'intero abbonamento. Se si desidera distribuire 30 core negli Stati Uniti ovest, è necessario richiedere 30 core Manager delle risorse negli Stati Uniti ovest. Se si desidera distribuire 30 core tutte le aree a cui si dispone dell'accesso, è necessario richiedere per 30 core Manager delle risorse in tutte le aree.

## <a name="invalid-content-link"></a>Collegamento contenuto non valido

Quando si riceve il messaggio di errore:

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

È probabile che tentativo di creare un collegamento a un modello nidificato non è disponibile. Verificare l'URI fornito per il modello annidato. Se il modello è presente in un account di archiviazione, assicurarsi che l'URI non è accessibile. Potrebbe essere necessario passare un token di SA. Per ulteriori informazioni, vedere [uso dei modelli collegati Gestione risorse Azure](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Autorizzazione non riuscita

Venga visualizzato un messaggio di errore durante la distribuzione perché l'account o il servizio principale si tenta di distribuire le risorse non dispone dell'accesso per eseguire queste azioni. Azure Active Directory consente o all'amministratore per controllare quali identità sono accessibili le risorse con un elevato grado di precisione. Ad esempio, se l'account viene assegnato il ruolo di lettore, non si possono creare le risorse. In questo caso, viene visualizzato un messaggio di errore che indica che l'autorizzazione non riuscita.

Per ulteriori informazioni sul controllo dell'accesso basato sui ruoli, vedere [Il controllo dell'accesso Azure Role-Based](./active-directory/role-based-access-control-configure.md).

Oltre al controllo dell'accesso basato sui ruoli, le azioni di distribuzione possono essere limitate dai criteri dell'abbonamento. Tramite i criteri, l'amministratore può applicare convenzioni in tutte le risorse distribuite nella sottoscrizione. Ad esempio, un amministratore può richiedere che viene fornito un valore specifico tag per un tipo di risorsa. Se non si soddisfare i requisiti dei criteri, verrà visualizzato un messaggio di errore durante l'installazione. Per ulteriori informazioni sui criteri, vedere [Utilizzare criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Risoluzione dei problemi macchine virtuali

| Errore | Articoli |
| -------- | ----------- |
| Errori di estensione script personalizzato | [Errori di estensione macchine Virtuali di Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />o<br />[Errori di estensione Linux VM](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Immagine del sistema operativo errori di provisioning | [Nuovi errori macchine Virtuali di Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />o<br />[Nuovi errori Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Errori di allocazione | [Errori di allocazione macchine Virtuali di Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />o<br />[Errori di allocazione Linux VM](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Secure Shell (SSH) errori durante il tentativo di connessione | [Proteggere le connessioni di Shell per Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| La connessione all'applicazione in esecuzione in macchine Virtuali di errori | [Applicazione in esecuzione in macchine Virtuali di Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />o<br />[Applicazione in esecuzione su un VM Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Errori di connessione Desktop remoto | [Connessioni Desktop remoto per macchine Virtuali di Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Errori di connessione risolti per la ridistribuzione | [Ridistribuire macchina virtuale al nuovo livello di nodo Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Errori di servizio cloud | [Problemi di distribuzione del servizio cloud](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Risoluzione dei problemi di altri servizi

Nella tabella seguente non è un elenco completo di risoluzione dei problemi di argomenti per Azure. Se, tuttavia, incentrata sui problemi relativi alla distribuzione o la configurazione delle risorse. Se è necessaria assistenza risoluzione dei problemi in fase di esecuzione con una risorsa, vedere la documentazione per tale servizio Azure.

| Servizio | Articolo |
| -------- | -------- |
| Automazione | [Suggerimenti sulla risoluzione dei problemi per gli errori comuni di automazione di Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Pila di Azure | [Risoluzione dei problemi di Microsoft Azure Stack](./azure-stack/azure-stack-troubleshooting.md) |
| Pila di Azure | [Web Apps e Stack Azure](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Dati Factory | [Risoluzione dei problemi di Factory dati](./data-factory/data-factory-troubleshoot.md) |
| Infrastruttura di servizio | [Risolvere i problemi comuni quando si distribuisce servizi su Azure servizio infrastruttura](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Ripristino del sito | [Monitorare e risolvere i problemi di protezione per macchine virtuali e server fisici](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Spazio di archiviazione | [Monitorare, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Risoluzione dei problemi di distribuzione StorSimple dispositivo](./storsimple/storsimple-troubleshoot-deployment.md) |
| Database SQL | [Risoluzione dei problemi di connessione al Database SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse | [Risoluzione dei problemi di SQL Azure Data Warehouse](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Comprendere quando una distribuzione è pronta

Gestione risorse di Azure segnala esito positivo in una distribuzione quando tutti i provider restituiscono dalla distribuzione correttamente. Tuttavia, il messaggio non necessariamente significa che il gruppo di risorse è "attivo e pronto per gli utenti." Una distribuzione, ad esempio, potrebbe essere necessario scaricare gli aggiornamenti, posticipare il modello non risorse o installare ebraico. Manager delle risorse non riconosce i contenuti pertinenti quando queste attività completate perché non sono le attività che tiene traccia di un provider di servizi. In questi casi, può essere prima che le risorse siano pronte all'uso reali. Di conseguenza, è necessario attendere che lo stato di distribuzione ha avuto esito positivo prima che la distribuzione può essere utilizzata.

È possibile impedire Azure reporting successo di distribuzione, tuttavia, tramite la creazione di uno script personalizzato per il modello personalizzato. Lo script sa come eseguire il monitoraggio dell'intera distribuzione per conformità di sistema e restituisce correttamente solo quando gli utenti possono interagire con la distribuzione intera. Se si desidera verificare che l'estensione è l'ultimo venga eseguita, utilizzare la proprietà **dependsOn** nel modello scelto.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su il controllo delle azioni, vedere [operazioni di controllo Gestione risorse](resource-group-audit.md).
- Per informazioni su azioni per determinare gli errori durante l'installazione, vedere [le operazioni di distribuzione di visualizzazione](resource-manager-troubleshoot-deployments-portal.md).
