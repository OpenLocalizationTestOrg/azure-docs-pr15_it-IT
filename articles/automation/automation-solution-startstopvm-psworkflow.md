<properties 
    pageTitle="Avviare e arrestare macchine virtuali con Azure automazione - flusso di lavoro di PowerShell | Microsoft Azure"
    description="Versione grafica dello scenario di automazione di Azure inclusi runbook per avviare e arrestare macchine virtuali classiche."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Azure scenario di automazione - avviare e arrestare macchine virtuali

Questo scenario di automazione di Azure include runbook per avviare e arrestare macchine virtuali classiche.  È possibile utilizzare questo scenario per una delle operazioni seguenti:  

- Utilizzare runbook senza alcuna modifica nel proprio ambiente. 
- Modificare runbook per eseguire le funzionalità personalizzate.  
- Chiamare il runbook da un'altra runbook come parte di una soluzione completa. 
- Utilizzare la runbook come esercitazioni per scoprire runbook authoring concetti. 

> [AZURE.SELECTOR]
- [Grafica](automation-solution-startstopvm-graphical.md)
- [Flusso di lavoro di PowerShell](automation-solution-startstopvm-psworkflow.md)

Questa è la versione di runbook PowerShell del flusso di lavoro di questo scenario. È inoltre disponibile tramite [runbook grafica](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Guida lo scenario

In questo scenario è composto da due runbook PowerShell del flusso di lavoro che è possibile scaricare uno dei collegamenti seguenti.  Vedere la [versione con interfaccia grafica](automation-solution-startstopvm-graphical.md) di questo scenario per i collegamenti a runbook grafica.

| Runbook | Collegamento | Tipo | Descrizione |
|:---|:---|:---|:---|
| Inizio AzureVMs | [Avviare Azure macchine virtuali classiche](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Flusso di lavoro di PowerShell | Avvia tutte le macchine virtuali classiche in un subscriptionor Azure tutte le macchine virtuali con un nome di servizio specifico. |
| Interrompi AzureVMs | [Interrompere Azure macchine virtuali classiche](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Flusso di lavoro di PowerShell | Si interrompe tutte le macchine virtuali in un account di automazione o tutte le macchine virtuali con un nome di servizio specifico.  |


## <a name="installing-and-configuring-the-scenario"></a>Installare e configurare lo scenario

### <a name="1-install-the-runbooks"></a>1. installare il runbook

Dopo avere scaricato la runbook, è possibile importarle con la procedura per l'importazione di [un Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. rivedere la descrizione e requisiti
I runbook includono testo di Guida commenti che include una descrizione e risorse necessarie.  È anche possibile ottenere le stesse informazioni in questo articolo. 

### <a name="3-configure-assets"></a>3. configurare risorse
I runbook richiedono le risorse seguenti che è necessario creare e popolare con i valori appropriati.

| Tipo di risorsa | Nome bene | Descrizione |
|:---|:---|:---|:---|
| Credenziali | AzureCredential | Contiene le credenziali per un account che dispone di autorizzazioni per avviare e arrestare macchine virtuali nella sottoscrizione Azure.  In alternativa, è possibile specificare un'altra risorsa credenziali nel parametro dell'attività **AzureAccount Aggiungi** **credenziali** . |
| Variabile | AzureSubscriptionId | Contiene l'ID di sottoscrizione dell'abbonamento Azure. |

## <a name="using-the-scenario"></a>Utilizzo dello scenario

### <a name="parameters"></a>Parametri

Ogni runbook sono i seguenti parametri.  È necessario fornire i valori per tutti i parametri obbligatori e può facoltativamente fornire i valori per gli altri parametri in base ai propri requisiti.

| Parametro | Tipo | Obbligatorio | Descrizione |
|:---|:---|:---|:---|
| Nome | stringa | No | Se viene fornito un valore, quindi tutte le macchine virtuali con lo stesso nome servizio avvio o interruzione.  Se viene specificato alcun valore, tutte le macchine virtuali classiche nella sottoscrizione Azure sono avviate o interrotto. |
| AzureSubscriptionIdAssetName | stringa | No | Contiene il nome della [variabile risorse](#installing-and-configuring-the-scenario) che contiene l'ID di sottoscrizione dell'abbonamento Azure.  Se non si specifica un valore, viene utilizzato *AzureSubscriptionId* .  |
| AzureCredentialAssetName | stringa | No | Contiene il nome del [bene credenziali](#installing-and-configuring-the-scenario) che contiene le credenziali per runbook da usare.  Se non si specifica un valore, viene utilizzato *AzureCredential* .  |

### <a name="starting-the-runbooks"></a>Avviare il runbook

È possibile usare uno dei metodi in [avvio runbook in Azure automazione](automation-starting-a-runbook.md) per avviare entrambi i runbook in questo scenario.

I comandi di esempio seguente viene utilizzato Windows PowerShell per eseguire **StartAzureVMs** per iniziare a tutte le macchine virtuali con il nome del servizio *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Output

I runbook verranno [un messaggio di output](automation-runbook-output-and-messages.md) per ogni macchina virtuale che indica l'istruzione avviare o arrestare è stato inviato correttamente o meno.  È possibile cercare una stringa specifica nell'output per determinare il risultato di ogni runbook.  Nella tabella seguente sono elencate le stringhe di output possibili.

| Runbook | Condizione | Messaggio |
|:---|:---|:---|
| Inizio AzureVMs | Sono già in esecuzione  | MyVM è già in esecuzione |
| Inizio AzureVMs | Richiesta di avvio per la macchina virtuale inviata correttamente | È stato avviato MyVM |
| Inizio AzureVMs | Richiesta di avvio per la macchina virtuale non è riuscita  | Impossibile avviare MyVM |
| Interrompi AzureVMs | Macchina virtuale è già stato interrotto  | MyVM è già stato interrotto |
| Interrompi AzureVMs | Interrompere la richiesta per la macchina virtuale inviata correttamente | MyVM è stata interrotta |
| Interrompi AzureVMs | Richiesta di tabulazione per macchina virtuale non riuscita  | Impossibile arrestare MyVM |

Ad esempio, il frammento di codice seguenti da un runbook tenta di avviare tutte le macchine virtuali con il nome del servizio *MyServiceName*.  Se uno qualsiasi degli errori di richieste inizio, è possono eseguire le azioni di errore. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Descrizione dettagliata

Di seguito è una descrizione dettagliata di runbook in questo scenario.  È possibile utilizzare queste informazioni per personalizzare il runbook o semplicemente per imparare dai loro per la modifica dei propri scenari di automazione.

### <a name="parameters"></a>Parametri

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Il flusso di lavoro viene avviato per ottenere i valori per i [parametri di input](#using-the-scenario).  Se non vengono forniti i nomi delle risorse vengono utilizzati i nomi predefiniti.

### <a name="output"></a>Output

    # Returns strings with status messages
    [OutputType([String])]

Questa riga dichiara che l'output dal runbook sarà una stringa.  Non se è necessario, ma è una procedura consigliata per quando dal runbook viene utilizzato come un [figlio runbook](automation-child-runbooks.md) in modo che un runbook padre conoscano il tipo di output aspettarsi.

### <a name="authentication"></a>Autenticazione

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Le righe successive impostano le [credenziali](automation-configuring.md#configuring-authentication-to-azure-resources) e Azure abbonamento che verrà utilizzato per il resto dal runbook.
Prima di tutto è utilizzare **Get-AutomationPSCredential** per ottenere la risorsa che contiene le credenziali di accesso per avviare e arrestare macchine virtuali nella sottoscrizione Azure. **Aggiungi AzureAccount** utilizza quindi questa risorsa per impostare le credenziali.  L'output viene assegnato a una variabile fittizia in modo che non è inclusa nell'output runbook.  

Variabile bene con l'ID di abbonamento verrà recuperato con **Get-AutomationVariable** e la sottoscrizione set con **Selezionare AzureSubscription**.

### <a name="get-vms"></a>Ottenere macchine virtuali

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** viene utilizzato per recuperare le macchine virtuali che dal runbook sarà compatibili con.  Se un valore nella variabile di input di **nome** , vengono recuperate solo le macchine virtuali con lo stesso nome del servizio.  Se **nome** è vuoto, vengono recuperati tutti i computer virtuali.

### <a name="startstop-virtual-machines-and-send-output"></a>Inizio/fine macchine virtuali e inviare l'output

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

Il passaggio successivo di righe tramite ogni macchina virtuale.  Prima di tutto l' **alimentazione** della macchina virtuale è verificata per determinare se è già in esecuzione o arrestato, a seconda dal runbook.  Se è già in stato di destinazione, viene inviato un messaggio di output e le estremità runbook.  In caso contrario, quindi **AzureVM Start** o **Stop AzureVM** viene utilizzato per tentare di avviare o arrestare la macchina virtuale con il risultato della richiesta archiviato a una variabile.  Quindi inviato un messaggio per generare l'output che specifica se la richiesta per avviare o arrestare è stata inviata.


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'uso di runbook figlio, vedere [runbook figlio in Azure automazione](automation-child-runbooks.md) 
- Per ulteriori informazioni sui messaggi di output durante l'esecuzione di runbook e la registrazione per semplificare la soluzione, vedere [Runbook output e i messaggi di automazione di Azure](automation-runbook-output-and-messages.md)
