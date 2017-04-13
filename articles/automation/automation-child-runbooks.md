<properties 
   pageTitle="Runbook figlio in Azure automazione | Microsoft Azure"
   description="Vengono descritti i diversi metodi per avviare un runbook in Azure automazione da un altro runbook e la condivisione di informazioni tra di esse."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Runbook figlio nel modello di automazione Azure

È buona norma in Azure automazione scrivere runbook riutilizzabile, modulare con una funzione distinte che può essere utilizzata da altri runbook. Un runbook padre chiama spesso uno o più runbook figlio per eseguire funzionalità necessarie. Esistono due modi per chiamare un runbook figlio, ognuno con differenze nette che è necessario comprendere in modo che è possibile determinare quale sarà adatto per i diversi scenari.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Richiamare un runbook figlio utilizzando esecuzione all'interno del testo

Per richiamare un runbook all'interno del testo da un'altra runbook, utilizzare il nome dal runbook e fornire i valori per i parametri esattamente come utilizzare un'attività o un cmdlet.  Runbook tutti lo stesso account di automazione disponibili a tutti gli altri utilizzati in questo modo. Runbook padre dovrà attendere runbook figlio completare prima di passare alla riga successiva e un tipo di output viene restituito direttamente all'elemento padre.

Quando si richiama un runbook all'interno del testo, viene eseguito nello stesso processo runbook padre. Non ci sarà alcuna indicazione nella cronologia dei processi dal runbook figlio che è stato eseguito. Le eventuali eccezioni e qualsiasi flusso di output dal runbook figlio sarà associati l'elemento padre. La restituisce come risultato un numero minore di processi e ne semplifica per rilevare e per risolvere i problemi perché le eccezioni generate dal runbook figlio ed eventuali l'output di flusso sono associate al processo padre.

Quando viene pubblicato un runbook, qualsiasi runbook figlio che chiama già deve essere pubblicata. In questo modo automazione Azure consente di creare un'associazione con qualsiasi runbook figlio quando un runbook viene compilato. In caso contrario, runbook padre saranno visibili per pubblicare correttamente, ma viene generata un'eccezione quando viene avviato. In questo caso, è possibile ripubblicare runbook padre per fare riferimento correttamente runbook figlio. Non è necessario ripubblicare runbook padre se le runbook figlio sono state modificate in quanto l'associazione sarà già stata creata.

I parametri di una runbook figlio chiamato all'interno del testo possono essere qualsiasi tipo di dati tra gli oggetti complessi e c'è alcun [serializzazione JSON](automation-starting-a-runbook.md#runbook-parameters) di quanto accade quando si avvia runbook tramite il portale di gestione di Azure o con il cmdlet Start AzureRmAutomationRunbook.


### <a name="runbook-types"></a>Tipi di runbook

Quali tipi di possono chiamare tra loro:

- Un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e [grafica runbook](automation-runbook-types.md#graphical-runbooks) possono chiamare ogni in linea (entrambi sono PowerShell in base a).
- Un [flusso di lavoro di PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks) e del flusso di lavoro con interfaccia grafica di PowerShell runbook possono chiamare ogni in linea (entrambi sono PowerShell flusso di lavoro in base a)
- I tipi di PowerShell e i tipi di PowerShell del flusso di lavoro non è possibile chiamare tra loro all'interno del testo e necessario utilizzare AzureRmAutomationRunbook Start.
    
Quando si pubblica in materia di ordine:

- L'ordine di pubblicazione dei runbook rilevanti solo per grafica e flusso di lavoro di PowerShell PowerShell runbook.


Quando si chiama un runbook figlio grafiche o PowerShell del flusso di lavoro tramite l'esecuzione all'interno del testo, è sufficiente utilizzare il nome dal runbook.  Quando si chiama un runbook figlio PowerShell, è necessario preceduto il proprio nome con *.\\ * Per specificare che lo script si trova nella directory locale. 

### <a name="example"></a>Esempio

Nell'esempio seguente richiama un runbook figlio test che accetta tre parametri, un oggetto complesso, un numero intero e un valore boolean. L'output dal runbook figlio viene assegnato a una variabile.  In questo caso, dal runbook figlio è un runbook PowerShell del flusso di lavoro

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Di seguito è lo stesso esempio in un runbook PowerShell come elemento figlio.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Avviare un runbook figlio utilizzando cmdlet

È possibile utilizzare il cmdlet [AzureRmAutomationRunbook inizio](https://msdn.microsoft.com/library/mt603661.aspx) per avviare un runbook come descritto in [per avviare un runbook con Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Esistono due modi di utilizzo per questo cmdlet.  In una modalità, il cmdlet restituisce l'id di processo non appena viene creato il processo figlio per runbook figlio.  La modalità, quali specificando il **-attendere** parametro, il cmdlet di attesa finché l'elemento figlio al termine del processo e restituirà l'output dal runbook figlio.

Processo da un runbook figlio iniziare con un cmdlet verrà eseguito in un processo separato dal runbook padre. Questo genera più processi di richiamare inline runbook e renderli più difficile tenere traccia. L'elemento padre può avviare più figlio runbook asincrono senza attendere che ognuno di essi al completamento. Per tale stesso tipo di esecuzione in parallelo la chiamata in linea di runbook figlio runbook padre necessario utilizzare la [parola chiave in parallelo](automation-powershell-workflow.md#parallel-processing).

Parametri per una runbook figlio iniziare con un cmdlet vengono forniti come hashtable come descritto nei [Parametri Runbook](automation-starting-a-runbook.md#runbook-parameters). Possono essere utilizzati solo i tipi di dati semplici. Se dal runbook include un parametro a un tipo di dati complessi, quindi deve essere chiamato all'interno del testo.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un runbook figlio con i parametri e quindi attendere il completamento utilizzando Start-AzureRmAutomationRunbook-attendere parametro. Al termine, l'output è acquisita dal runbook figlio.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Confronto dei metodi per chiamare un runbook figlio

Nella tabella seguente sono riepilogate le differenze tra i due metodi per chiamare un runbook dal runbook un'altra.

| | In linea| Cmdlet|
|:---|:---|:---|
|Processo|Figlio runbook eseguire nello stesso processo dell'elemento padre.|Verrà creato un processo separato dal runbook figlio.|
|Esecuzione|Padre runbook attendere runbook figlio completare prima di continuare.|Padre runbook continua non appena si avvia figlio runbook runbook padre *o* aspettare il processo di figlio alla fine.|
|Output|Runbook padre è possibile ottenere output direttamente dal runbook figlio.|Runbook padre deve recuperare l'output dal runbook processo figlio *o* padre runbook direttamente accessibile output dal runbook figlio.|
|Parametri|Valori per i parametri di runbook figlio vengono specificati separatamente e possono usare qualsiasi tipo di dati.|I valori per i parametri di runbook figlio devono essere combinati in una singola hashtable e possono includere solo semplici, matrice e che la serializzazione JSON di sfruttare i tipi di dati di oggetto.|
|Account di automazione|Runbook padre è possibile utilizzare solo figlio runbook nello stesso account di automazione.|Se si dispone di una connessione a tale, padre runbook utilizzabili runbook figlio da qualsiasi account automazione dalla stessa sottoscrizione di Azure e anche una sottoscrizione diversa.|
|Pubblicazione|Runbook figlio devono essere pubblicate prima della pubblicazione runbook padre.|Runbook figlio deve essere pubblicata qualsiasi momento prima dell'avvio runbook padre.|

## <a name="next-steps"></a>Passaggi successivi

- [A partire da un runbook automazione Azure](automation-starting-a-runbook.md)
- [Output runbook e messaggi di automazione di Azure](automation-runbook-output-and-messages.md)
