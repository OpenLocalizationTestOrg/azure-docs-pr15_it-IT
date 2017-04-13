<properties 
    pageTitle="Modifica runbook testuali in automazione Azure"
    description="Questo articolo offre diverse procedure per l'utilizzo di PowerShell e flusso di lavoro di PowerShell runbook in Azure automazione utilizzando l'editor di testo."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Modifica runbook testuali in automazione Azure

Editor di testo in Azure automazione è utilizzabile per modificare [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbook PowerShell del flusso di lavoro](automation-runbook-types.md#powershell-workflow-runbooks). Ha le caratteristiche tipiche di altri editor di codice, ad esempio intellisense e colori con altre caratteristiche speciali per aiutare l'accesso alle risorse comuni runbook.  In questo articolo vengono fornite procedure dettagliate per l'esecuzione di diverse funzioni all'editor.

L'editor di testo include una caratteristica per inserire un runbook codice per le attività, risorse e runbook figlio. Invece di digitare il codice manualmente, è possibile selezionare da un elenco delle risorse disponibili e avere il codice inserito dal runbook.

Ogni runbook Azure automazione sono disponibili due versioni, Published e bozza. Modificare la versione bozza dal runbook e quindi pubblicarlo in modo che può essere eseguita. La versione pubblicata non può essere modificata. Per ulteriori informazioni, vedere [pubblicazione un runbook](automation-creating-importing-runbook.md#publishing-a-runbook) .

Per lavorare con [Runbook grafici](automation-runbook-types.md#graphical-runbooks), vedere [grafiche creazione condivisa in Azure automazione](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Per modificare un runbook con il portale di Azure

Utilizzare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nel portale di Azure, selezionare il proprio account di automazione.
2. Fare clic sul riquadro **runbook** per aprire l'elenco di runbook.
3. Fare clic sul nome runbook che si desidera modificare e quindi fare clic sul pulsante **Modifica** .
6. Eseguire il processo di modifica necessari.
7. Dopo aver completato le modifiche, fare clic su **Salva** .
8. Se si vuole la versione più recente bozza dal runbook per la pubblicazione, fare clic su **pubblica** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Per inserire un cmdlet un runbook

2. Nell'area di lavoro dell'editor di testo, posizionare il cursore nel punto in cui si desidera inserire il cmdlet.
3. Espandere il nodo **cmdlet** nel controllo raccolta. 
3. Espandere il modulo contenente il cmdlet che si desidera utilizzare.
4. Scelta rapida il cmdlet per inserire e selezionare **Aggiungi all'area di lavoro**.  Se il cmdlet ha più di un parametro impostata, verrà aggiunto il set predefinito.  È anche possibile espandere il cmdlet per selezionare un set di parametri diversi.
4. Viene inserito il codice per il cmdlet con l'intero elenco di parametri.
5. Specificare un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <> per tutti i parametri richiesti.  Rimuovere i parametri che non è necessario.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Per inserire il codice per un runbook figlio un runbook

2. Nell'area di lavoro dell'editor di testo, posizionare il cursore nel punto in cui si desidera inserire il codice per [runbook figlio](automation-child-runbooks.md).
3. Espandere il nodo **runbook** nel controllo raccolta. 
3. Scelta rapida dal runbook per inserire e selezionare **Aggiungi all'area di lavoro**.
4. Il codice per runbook figlio viene inserito con tutti i segnaposto per i parametri runbook.
5. Sostituire i segnaposto con i valori appropriati per ogni parametro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Per inserire una risorsa in un runbook

2. Nell'area di lavoro dell'editor di testo, posizionare il cursore nel punto in cui si desidera inserire il codice per runbook figlio.
3. Espandere il nodo di **risorse** del controllo di una raccolta. 
4. Espandere il nodo per il tipo di risorsa desiderato.
3. Destra, fare clic su risorse per inserire e selezionare **Aggiungi all'area di lavoro**.  Per [beni variabile](automation-variables.md), selezionare **Aggiungi "ottenere variabile" su tela** o **aggiungere "Imposta variabile" in tela** a seconda che si vuole ottenere o impostare la variabile.
4. Il codice per la risorsa viene inserito dal runbook.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Per modificare un runbook con il portale di Azure

Utilizzare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nel portale di Azure, selezionare **automazione** e quindi fare clic sul nome di un account di automazione.
2. Selezionare la scheda **runbook** .
3. Fare clic sul nome runbook che si desidera modificare e quindi selezionare la scheda **autore** .
5. Fare clic sul pulsante **Modifica** nella parte inferiore dello schermo.
6. Eseguire il processo di modifica necessari.
7. Dopo aver completato le modifiche, fare clic su **Salva** .
8. Se si vuole la versione più recente bozza dal runbook per la pubblicazione, fare clic su **pubblica** .

### <a name="to-insert-an-activity-into-a-runbook"></a>Per inserire un'attività in un Runbook

1. Nell'area di lavoro dell'editor di testo, posizionare il cursore nel punto in cui si desidera inserire l'attività.
1. Nella parte inferiore dello schermo, fare clic su **Inserisci** e quindi su **attività**.
1. Nella colonna **Modulo Integration** , selezionare il modulo che contiene l'attività.
1. Nel riquadro **attività** selezionare un'attività.
1. Nella colonna **Descrizione** prendere nota della descrizione dell'attività. Facoltativamente, è possibile fare clic su Visualizza dettagliate della Guida per aprire la Guida per l'attività nel browser.
1. Fare clic sulla freccia a destra.  Se l'attività ha parametri, saranno elencati per le informazioni.
1. Fare clic sul pulsante di controllo.  Eseguire l'attività codice verrà inserita dal runbook.
1. Se l'attività richiede parametri, specificare un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Per inserire il codice per un runbook figlio un runbook

1. Nell'area di lavoro dell'editor di testo, posizionare il cursore nel punto in cui si desidera inserire [runbook figlio](automation-child-runbooks.md).
2. Nella parte inferiore dello schermo, fare clic su **Inserisci** e quindi **Runbook**.
3. Selezionare dal runbook per inserire indicati nella colonna centrale e fare clic sulla freccia a destra.
4. Se dal runbook include parametri, saranno elencati per le informazioni.
5. Fare clic sul pulsante di controllo.  Codice per l'esecuzione dal runbook selezionato verrà inserita runbook corrente.
7. Se dal runbook richiede parametri, specificare un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Per inserire una risorsa in un runbook

1. Nell'area di lavoro dell'editor di testo, posizionare il cursore nel punto in cui si desidera inserire l'attività per recuperare la risorsa.
1. Nella parte inferiore dello schermo, fare clic su **Inserisci** e quindi su **Impostazioni**.
1. Nella colonna **Azione di impostazione** , selezionare l'azione desiderata.
1. Selezionare le risorse disponibili nella colonna centrale.
1. Fare clic sul pulsante di controllo.  Codice per ottenere o impostare la risorsa verrà inserita dal runbook.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Per modificare un runbook Azure automazione usando Windows PowerShell

Per modificare un runbook con Windows PowerShell, utilizzare l'editor preferito e salvarlo in un file ps1. È possibile utilizzare il cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) per recuperare il contenuto della runbook quindi cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) per sostituire dal runbook bozza esistente con quello modificato.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Per recuperare il contenuto di un Runbook usando Windows PowerShell

I comandi di esempio seguente viene illustrato come recuperare lo script per un runbook e salvarla in un file di script. In questo esempio viene recuperata la versione bozza. È inoltre possibile recuperare la versione pubblicata dal runbook anche se questa versione non può essere modificata.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Per modificare il contenuto di un Runbook usando Windows PowerShell

I comandi di esempio seguente viene illustrato come sostituire il contenuto di un runbook con il contenuto di un file di script. Si noti che questa è la stessa procedura di esempio come [importare un runbook da un file di script con Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Articoli correlati

- [Creazione o importazione runbook di automazione di Azure](automation-creating-importing-runbook.md)
- [Formazione del flusso di lavoro di PowerShell](automation-powershell-workflow.md)
- [Creazione condivisa in Azure automazione con interfaccia grafica](automation-graphical-authoring-intro.md)
- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Pianificazioni](automation-schedules.md)
- [Variabili](automation-variables.md)