<properties 
   pageTitle="Flusso di lavoro di apprendimento PowerShell"
   description="In questo articolo è destinato come una lezione rapida autori familiari con PowerShell per comprendere le differenze specifiche PowerShell e PowerShell del flusso di lavoro."
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
   ms.date="09/12/2016"
   ms.author="bwren" />

# <a name="learning-windows-powershell-workflow"></a>Flusso di lavoro di risorse Windows PowerShell

Runbook in Azure automazione sono implementato come i flussi di lavoro di Windows PowerShell.  Un flusso di lavoro di Windows PowerShell è simile a uno script di Windows PowerShell ma presenta alcune differenze significative che possono essere confusione a un nuovo utente.  In questo articolo è destinato agli utenti già familiari con PowerShell e illustrata brevemente concetti richiedono se si converte uno script di PowerShell in un flusso di lavoro di PowerShell per l'utilizzo in un runbook.  

Un flusso di lavoro è una sequenza di passaggi programmati, connessi che eseguono attività lunga o Richiedi coordinazione di più passaggi in più dispositivi o nodi gestiti. I vantaggi di un flusso di lavoro su un normale script includono la possibilità di contemporaneamente esegua un'azione su più dispositivi e la possibilità di recupero automatico dagli errori. Un flusso di lavoro di Windows PowerShell è uno script di Windows PowerShell che utilizza Windows Workflow Foundation. Mentre il flusso di lavoro è scritto con sintassi di Windows PowerShell e avviata da Windows PowerShell, viene elaborato da Windows Workflow Foundation.

Per informazioni dettagliate sugli argomenti in questo articolo, vedere [Introduzione a Windows PowerShell flusso di lavoro](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Tipi di runbook

Esistono tre tipi di runbook di automazione di Azure, *Del flusso di lavoro di PowerShell*, *PowerShell* e *grafica*.  Per definire il tipo di runbook quando si crea dal runbook e non è possibile convertire un runbook a altro tipo dopo averla creata.

Runbook del flusso di lavoro di PowerShell e PowerShell runbook per gli utenti che si preferiscono utilizzare direttamente il codice di PowerShell uno utilizza l'editor di testo in Azure automazione oppure un editor non in linea, ad esempio PowerShell ISE. Se si sta creando un runbook PowerShell del flusso di lavoro, è necessario conoscere le informazioni in questo articolo. 

Grafica runbook consentono di creare un runbook utilizza le stesse attività cmdlet ma un'interfaccia grafica che nasconde la complessità del flusso di lavoro di PowerShell sottostante.  Concetti illustrati in questo articolo, ad esempio punti di controllo e l'esecuzione in parallelo anche per runbook grafica, ma non sarà necessario preoccuparsi di sintassi dettagliata. 

## <a name="basic-structure-of-a-workflow"></a>Struttura di base di un flusso di lavoro

Il primo passo per la conversione di uno script di PowerShell in un flusso di lavoro di PowerShell è che la contiene la parola chiave **flusso di lavoro** .  Un flusso di lavoro inizia con la parola chiave **flusso di lavoro** seguita dal corpo dello script racchiuso tra parentesi graffe. Il nome del flusso di lavoro segue la parola chiave **flusso di lavoro** illustrata la sintassi seguente. 

    Workflow Test-Workflow
    {
       <Commands>
    }

Il nome del flusso di lavoro deve corrispondere al nome runbook automazione. Se viene importato dal runbook, il nome del file deve corrispondere al nome del flusso di lavoro e deve terminare con ps1.

Per aggiungere parametri al flusso di lavoro, utilizzare la parola chiave **parametri** esattamente come farebbe con uno script. 

## <a name="code-changes"></a>Modifiche al codice

Codice di flusso di lavoro di PowerShell quasi identica a codice di script di PowerShell ad eccezione di alcune modifiche significative.  Le sezioni seguenti descrivono le modifiche necessarie per creare uno script di PowerShell per l'esecuzione in un flusso di lavoro.

### <a name="activities"></a>Attività

Un'attività è un'attività specifica in un flusso di lavoro. Come uno script è composta da uno o più comandi, una o più attività che vengono eseguite in una sequenza è costituito da un flusso di lavoro. Flusso di lavoro di Windows PowerShell converte automaticamente molti dei cmdlet di Windows PowerShell alle attività quando viene eseguito un flusso di lavoro. Quando si specifica uno di questi cmdlet nel runbook, l'attività corrispondente all'esecuzione effettiva da Windows Workflow Foundation. Per questi cmdlet senza un'attività corrispondente, il flusso di lavoro di Windows PowerShell viene eseguita automaticamente il cmdlet all'interno di un'attività [InlineScript è impostato su](#inlinescript) . Esiste una serie di cmdlet esclusi e non può essere utilizzato in un flusso di lavoro, a meno che non si inclusi in modo esplicito in un blocco di InlineScript è impostato su. Per ulteriori informazioni su questi concetti, vedere [Uso attività nei flussi di lavoro di Script](http://technet.microsoft.com/library/jj574194.aspx).

Attività flusso di lavoro condividono un insieme di parametri comuni per configurare il funzionamento. Per informazioni dettagliate sui parametri comuni del flusso di lavoro, vedere [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parametri di posizione

È possibile utilizzare i parametri di posizione con le attività e i cmdlet di un flusso di lavoro.  Tutto ciò significa è che è necessario utilizzare i nomi dei parametri.

Si consideri ad esempio il codice seguente che prende tutti i servizi in esecuzione.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Se si tenta di eseguire il codice stesso in un flusso di lavoro, viene visualizzato un messaggio come "insieme di parametri non può essere risolto utilizzando parametri denominati specificato."  Per risolvere il problema, è sufficiente specificare il nome del parametro come illustrato di seguito.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Oggetti deserializzati

Gli oggetti nei flussi di lavoro sono deserializzati.  Ciò significa che le relative proprietà sono ancora disponibili, ma non i metodi.  Si consideri ad esempio il codice seguente di PowerShell che arresta un servizio utilizzando il metodo Stop dell'oggetto servizio.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Se si tenta di eseguire questa operazione in un flusso di lavoro, viene visualizzato un messaggio di errore che indica "chiamata al metodo non è supportata in un flusso di lavoro di Windows PowerShell".  

Un'opzione possibile consiste per disporre il queste due righe di codice in un blocco di [InlineScript è impostato su](#InlineScript) nel qual caso $Service è un oggetto di servizio all'interno del blocco. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

È inoltre possibile utilizzare un altro cmdlet che effettua la stessa funzionalità del metodo, se disponibile.  In caso di un campione, il cmdlet Interrompi servizio fornisce la stessa funzionalità del metodo di tabulazione ed è possibile utilizzare le operazioni seguenti per un flusso di lavoro.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript è impostato su

L'attività **InlineScript è impostato su** è utile quando è necessario eseguire uno o più comandi come tradizionale script di PowerShell invece del flusso di lavoro di PowerShell.  Mentre i comandi di un flusso di lavoro vengono inviati a Windows Workflow Foundation per l'elaborazione, comandi in un blocco di InlineScript è impostato su vengono elaborati da Windows PowerShell. 

InlineScript è impostato su utilizza la sintassi illustrata di seguito.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

È possibile tornare output da un InlineScript è impostato su assegnando l'output a una variabile. Nell'esempio seguente si arresta un servizio e quindi restituisce il nome del servizio.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


È possibile passare valori in un blocco di InlineScript è impostato su, ma è necessario utilizzare modificatore di ambito **$Using** .  Nell'esempio seguente è identico all'esempio precedente, ad eccezione che il nome del servizio viene fornito da una variabile. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Mentre InlineScript è impostato su attività può essere importante in alcuni flussi di lavoro, non supportano costrutti del flusso di lavoro e deve essere utilizzati solo quando necessario per i motivi seguenti:

- Non è possibile utilizzare [punti di controllo](#Checkpoints) all'interno di un blocco di InlineScript è impostato su. Se si verifica un errore all'interno del blocco, è necessario riprendere dall'inizio dell'intervallo.
- Non è possibile utilizzare l' [esecuzione in parallelo](#parallel-execution) all'interno di un InlineScriptBlock.
- Scalabilità del flusso di lavoro viene applicata a InlineScript è impostato su dal momento che contiene la sessione di Windows PowerShell per l'intera lunghezza del blocco InlineScript è impostato su.

Per ulteriori informazioni sull'uso di InlineScript è impostato su, vedere [Esecuzione di comandi di Windows PowerShell in un flusso di lavoro](http://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Elaborazione in parallelo

Uno dei vantaggi dei flussi di lavoro di Windows PowerShell è la possibilità di eseguire un insieme di comandi in parallelo anziché in sequenza come con uno script tradizionale. 

È possibile utilizzare la parola chiave **in parallelo** per creare un blocco di script con più comandi che verranno eseguite contemporaneamente. Utilizza la sintassi illustrata di seguito. In questo caso, Activity1 e Activity2 verrà avviato nello stesso momento. Activity3 inizierà solo dopo avere completato Activity1 e Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Ad esempio, considerare i seguenti comandi PowerShell che copiare più file in una destinazione di rete.  Questi comandi vengono eseguiti in sequenza, in modo che un file deve finire il copia prima viene avviato il successivo.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Il flusso di lavoro seguente esegue questi comandi in parallelo, in modo che tutti gli iniziare la copia nello stesso momento.  Solo dopo che sono tutti copiato completamente il messaggio di completamento è disponibile.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


È possibile utilizzare il **ForEach-parallela** costrutto l'elaborazione dei comandi per ogni elemento in una raccolta contemporaneamente. Gli elementi nella raccolta vengono eseguiti in parallelo durante l'esecuzione in sequenza di comandi nel blocco di script. Utilizza la sintassi illustrata di seguito. In questo caso, verrà avviato Activity1 contemporaneamente per tutti gli elementi nella raccolta. Per ogni elemento Activity2 inizierà dopo Activity1 è stata completata. Activity3 inizierà solo dopo Activity1 e Activity2 completato per tutti gli elementi.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Nell'esempio seguente è simile all'esempio precedente la copia dei file in parallelo.  In questo caso, verrà visualizzato un messaggio per ogni file dopo averli copiati.  Solo dopo che sono tutti copiato completamente è finale visualizzato il messaggio.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  Non è consigliabile eseguire runbook figlio in parallelo poiché questo stato visualizzato per offrire risultati origini non affidabili.  L'output dal runbook figlio a volte non verrà visualizzati e le impostazioni di un elemento figlio runbook possono influire sulle altre runbook figlio in parallelo 


## <a name="checkpoints"></a>Punti di controllo

Un *punto di controllo* è uno snapshot dello stato corrente del flusso di lavoro che include il valore corrente per le variabili e l'output generato a tale punto. Se un flusso di lavoro termina con errore o è sospesa, quindi alla successiva esecuzione inizierà dalla relativa verifica ultima anziché l'inizio di worfklow.  È possibile impostare un punto di controllo in un flusso di lavoro con l'attività di **Verifica del flusso di lavoro** .

Nel codice di esempio seguente viene generata un'eccezione dopo Activity2 causa il flusso di lavoro terminare. Quando si esegue nuovamente il flusso di lavoro, viene avviato eseguendo Activity2 poiché si tratta di subito dopo l'ultimo punto di controllo imposta.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

È consigliabile impostare punti di controllo in un flusso di lavoro dopo le attività che possono essere soggetto a eccezione e non devono essere ripetuto ripresa se il flusso di lavoro. Ad esempio, il flusso di lavoro può creare una macchina virtuale. È possibile impostare un punto di controllo prima e dopo i comandi necessari per creare la macchina virtuale. Se la creazione non riesce, quindi i comandi ripetuti se il flusso di lavoro viene avviato nuovamente. Se l'il worfklow ha esito negativo dopo avere stabilito la creazione, quindi la macchina virtuale non verrà creata nuovamente quando il flusso di lavoro viene riattivata.

Nell'esempio seguente copia più file in un percorso di rete e imposta una verifica dopo ogni file.  Se il percorso di rete viene perso, il flusso di lavoro terminerà errore.  Quando viene avviato nuovamente, verrà ripresa alla verifica ultimo il che significa che solo i file che sono già stati copiati verranno ignorati.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Poiché le credenziali username non vengono mantenute dopo la chiamata attività [Del flusso di lavoro sospensione](https://technet.microsoft.com/library/jj733586.aspx) o dopo l'ultimo punto di controllo, è necessario configurare le credenziali su null e quindi recuperarli nuovamente dall'archivio di risorse dopo la chiamata a **Sospensione flusso di lavoro** o il punto di controllo.  In caso contrario, si che venga visualizzato il messaggio di errore seguente: non è possibile riprendere *il processo del flusso di lavoro, è perché persistenza dati potrebbero non essere completamente salvati o salvati persistenza dati sono danneggiati. È necessario riavviare il flusso di lavoro.*

Lo stesso codice seguente viene illustrato come gestire questo in runbook il flusso di lavoro di PowerShell.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Questo non è necessario se per l'autenticazione si usa un account Esegui come configurato con un servizio principale.  

Per ulteriori informazioni sui punti di controllo, vedere [Aggiunta di punti di controllo a un flusso di lavoro di Script](http://technet.microsoft.com/library/jj574114.aspx).


## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a utilizzare runbook del flusso di lavoro di PowerShell, vedere [il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md) 
