<properties 
   pageTitle="Tipi di Runbook automazione Azure"
   description="Vengono descritti i diversi tipi di runbook che è possibile usare in Azure automazione e considerazioni da tenere in considerazione la determinazione del tipo da utilizzare. "
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

# <a name="azure-automation-runbook-types"></a>Tipi di runbook automazione Azure

Automazione Azure supporta quattro tipi di runbook brevemente descritti nella tabella seguente.  Nelle sezioni seguenti forniscono ulteriori informazioni su ogni tipo con alcune considerazioni su quando utilizzare ognuna.


| Tipo |  Descrizione |
|:---|:---|
| [Grafica](#graphical-runbooks) | In base a Windows PowerShell e creati e modificati completamente nell'editor grafico nel portale di Azure. | 
| [Flusso di lavoro di PowerShell con interfaccia grafica](#graphical-runbooks) | In base a flusso di lavoro di Windows PowerShell e creati e modificati completamente nell'editor di grafica nel portale di Azure. 
| [PowerShell](#powershell-runbooks) | Runbook di testo in base a script di Windows PowerShell.
| [Flusso di lavoro di PowerShell](#powershell-workflow-runbooks) | Testo runbook basato su Windows PowerShell flusso di lavoro. |


## <a name="graphical-runbooks"></a>Runbook grafica

Runbook [grafiche](automation-runbook-types.md#graphical-runbooks) e del flusso di lavoro con interfaccia grafica di PowerShell vengono creati e modificati con editor di immagini nel portale di Azure.  È possibile esportarli in un file e quindi importarli in un altro account di automazione, ma non è possibile creare o modificarli con un altro strumento.  Grafica runbook generare il codice di PowerShell, ma è possibile visualizzare o modificare il codice direttamente. Grafica runbook non può essere convertito in uno dei [formati di testo](automation-runbook-types.md), né un runbook di testo può essere convertito in formato grafico. Grafica runbook può essere convertito in grafica del flusso di lavoro di PowerShell runbook durante l'importazione e viceversa.

### <a name="advantages"></a>Vantaggi

- Creare runbook con una conoscenza minima di [PowerShell](automation-powershell-workflow.md).
- Rappresentare visivamente le procedure di gestione.
- Includere altri runbook come figlio runbook per creare i flussi di lavoro di alti livelli.


### <a name="limitations"></a>Limitazioni

- Non è possibile modificare runbook all'esterno di Azure portal.
- Può richiedere un'attività di codice contenente il codice di PowerShell per eseguire logica complessa.
- Non è possibile visualizzare o modificare direttamente il codice di PowerShell creati dal flusso di lavoro con interfaccia grafica. Si noti che è possibile visualizzare il codice creato in tutte le attività di codice.


## <a name="powershell-runbooks"></a>Runbook PowerShell

PowerShell runbook basati su Windows PowerShell.  Modificare direttamente il codice dal runbook utilizzando l'editor di testo nel portale di Azure.  Nell'automazione di Azure, è possibile utilizzare qualsiasi editor di testo non in linea e [importare dal runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) .

### <a name="advantages"></a>Vantaggi

- Implementare tutta la logica complessa con codice di PowerShell senza ulteriori complessità del flusso di lavoro di PowerShell. 
- Runbook avvia più velocemente di quanto runbook grafiche o del flusso di lavoro di PowerShell poiché non è necessario essere compilati prima dell'esecuzione.

### <a name="limitations"></a>Limitazioni

- È necessario avere familiarità con gli script di PowerShell.
- Non è possibile utilizzare [l'elaborazione in parallelo](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
- Non è possibile utilizzare [punti di controllo](automation-powershell-workflow.md#checkpoints) per riprendere runbook in caso di errore.
- Runbook del flusso di lavoro di PowerShell e grafica runbook possono essere inclusi come figlio runbook utilizzando il cmdlet Start AzureAutomationRunbook che consente di creare un nuovo processo.

### <a name="known-issues"></a>Problemi noti
Di seguito sono corrente problemi noti di PowerShell runbook.

- PowerShell runbook non è possibile non è possibile recuperare un formato non crittografato [bene variabile](automation-variables.md) con un valore null.
- PowerShell runbook non è possibile recuperare una [variabile bene](automation-variables.md) con *~* nel nome.
- Get-Process riprodotta a ciclo continuo in una PowerShell runbook potrebbe bloccarsi quando circa 80 iterazioni. 
- Un runbook PowerShell potrebbe non riuscire se tenta di scrittura di dimensioni molto grandi quantità di dati per il flusso di output contemporaneamente.   È in genere possibile risolvere questo problema restituendo solo le informazioni che necessarie quando si lavora con oggetti di grandi dimensioni.  Ad esempio, invece di output simile al *Processo di Get*, è possibile creare solo i campi obbligatori con *Get-Process | Selezionare opzioni, CPU*.

## <a name="powershell-workflow-runbooks"></a>Flusso di lavoro di PowerShell runbook

Flusso di lavoro di PowerShell runbook sono runbook di testo in base a [Flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md).  Modificare direttamente il codice dal runbook utilizzando l'editor di testo nel portale di Azure.  Nell'automazione di Azure, è possibile utilizzare qualsiasi editor di testo non in linea e [importare dal runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) .

### <a name="advantages"></a>Vantaggi

- Implementare tutta la logica complessa con codice di PowerShell del flusso di lavoro.
- Utilizzare [punti di controllo](automation-powershell-workflow.md#checkpoints) per riprendere runbook in caso di errore.
- Utilizzare [l'elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
- Possono includere altri runbook grafica e del flusso di lavoro di PowerShell runbook come figlio runbook per creare i flussi di lavoro di alti livelli.


### <a name="limitations"></a>Limitazioni

- Autore deve avere familiarità con PowerShell del flusso di lavoro.
- Runbook devono affrontare sofisticate di PowerShell flusso di lavoro, ad esempio [deserializzato oggetti](automation-powershell-workflow.md#code-changes).
- Runbook richiede più tempo per iniziare a PowerShell runbook quanto è necessario essere compilati prima dell'esecuzione.
- PowerShell runbook possono essere inclusi come figlio runbook utilizzando il cmdlet Start AzureAutomationRunbook che consente di creare un nuovo processo.


## <a name="considerations"></a>Considerazioni

Tenere in considerazione le considerazioni seguenti per determinare quale tipo per usare per una particolare runbook.

- È possibile convertire runbook dal grafico di tipo testo o viceversa.
- Esistono limitazioni relative all'uso runbook dei diversi tipi come un runbook figlio.  Per ulteriori informazioni, vedere [runbook figlio in Azure automazione](automation-child-runbooks.md) .

  
## <a name="next-steps"></a>Passaggi successivi

- Per maggiori informazioni sulla creazione di grafica runbook, vedere [grafiche creazione condivisa in automazione Azure](automation-graphical-authoring-intro.md)
- Per comprendere le differenze tra PowerShell e PowerShell flussi di lavoro per runbook, vedere [Flusso di lavoro di risorse Windows PowerShell](automation-powershell-workflow.md)
- Per ulteriori informazioni su come creare o importare una Runbook, vedere [creare o importare una Runbook](automation-creating-importing-runbook.md)



