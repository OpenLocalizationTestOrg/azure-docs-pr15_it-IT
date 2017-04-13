<properties
    pageTitle="Creazione o importazione runbook di automazione di Azure"
    description="In questo articolo viene descritto come creare un nuovo runbook in Azure automazione o importare uno da un file."
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
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Creazione o importazione runbook di automazione di Azure

È possibile aggiungere un runbook per automazione di Azure, è possibile [crearne uno nuovo](#creating-a-new-runbook) o importando un runbook esistente da un file o dalla [Raccolta Runbook](automation-runbook-gallery.md). In questo articolo fornisce informazioni sulla creazione e l'importazione runbook da un file.  È possibile ottenere tutti i dettagli sull'accesso ai moduli in [raccolte Runbook e modulo per l'automazione di Azure](automation-runbook-gallery.md)e runbook community.

## <a name="creating-a-new-runbook"></a>Creazione di un nuovo runbook

È possibile creare un nuovo runbook in Azure automazione utilizzando uno dei portali Azure o Windows PowerShell. Dopo aver creato dal runbook, è possibile modificarlo utilizzando le informazioni [Del flusso di lavoro di apprendimento PowerShell](automation-powershell-workflow.md) e [grafica creazione condivisa in Azure automazione](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Per creare un nuovo runbook automazione Azure con il portale di Azure classica

È possibile utilizzare solo con [PowerShell del flusso di lavoro runbook](automation-runbook-types.md#powershell-workflow-runbooks) nel portale di Azure.

1. Nel portale di Azure classica fare clic su, **New**, **App Services**, **automazione**, **Runbook**, **Creazione rapida**.
2. Immettere le informazioni richieste e quindi fare clic su **Crea**. Il nome runbook deve iniziare con una lettera e può avere lettere, numeri, caratteri di sottolineatura e trattini.
3. Se si desidera modificare dal runbook a questo punto, fare clic su **Modifica Runbook**. In caso contrario, fare clic su **OK**.
4. Il nuovo runbook verranno visualizzati nella scheda **runbook** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Per creare un nuovo runbook automazione Azure con il portale di Azure

1. Nel portale di Azure, aprire l'account di automazione.
2. Fare clic sul riquadro **runbook** per aprire l'elenco di runbook.
3. Fare clic sul pulsante **Aggiungi un runbook** e quindi **Crea un nuovo runbook**.
2. Digitare un **nome** per il runbook e selezionare il [tipo](automation-runbook-types.md). Il nome runbook deve iniziare con una lettera e può avere lettere, numeri, caratteri di sottolineatura e trattini.
3. Fare clic su **Crea** per creare dal runbook e aprire l'editor.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Per creare un nuovo runbook automazione Azure con Windows PowerShell

È possibile utilizzare il cmdlet [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) per creare un vuoto [runbook PowerShell del flusso di lavoro](automation-runbook-types.md#powershell-workflow-runbooks). È possibile specificare il **nome** parametro per creare un runbook vuota che è possibile modificare in un secondo momento oppure è possibile specificare il parametro **Path** per importare un file runbook. Parametro di **tipo** deve essere inclusi anche per specificare uno dei quattro tipi di runbook.

I comandi di esempio seguente viene illustrato come creare un nuovo runbook vuoto.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>L'importazione di un runbook da un file in automazione Azure

È possibile creare un nuovo runbook in Azure automazione importando uno script di PowerShell o PowerShell del flusso di lavoro (con estensione ps1) o un runbook grafica esportato (.graphrunbook).  È necessario specificare il [tipo di runbook](automation-runbook-types.md) creato dall'importazione tenendo conto delle considerazioni seguenti.

- Un file di .graphrunbook può essere importato solo in un nuovo [grafico runbook](automation-runbook-types.md#graphical-runbooks)e runbook grafica può essere creato solo da un file .graphrunbook.
- Un file ps1 contenente un flusso di lavoro di PowerShell può essere importato solo in un [flusso di lavoro di PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Se il file contiene più flussi di lavoro di PowerShell, l'importazione avrà esito negativo. È necessario salvare ogni flusso di lavoro in un file distinto e importare ogni separatamente.
- Un file ps1 che non contiene un flusso di lavoro può essere importato in un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) o un [flusso di lavoro di PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Se viene importato in un flusso di lavoro di PowerShell runbook, quindi convertito in un flusso di lavoro e i commenti verranno inclusi nel runbook specificando le modifiche apportate.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Per importare un runbook da un file con il portale di Azure classica
È possibile utilizzare la procedura seguente per importare un file di script in Azure automazione.  Si noti che è possibile importare un file ps1 solo in un runbook PowerShell del flusso di lavoro tramite il portale.  Per altri tipi, è necessario utilizzare il portale di Azure.

1. Nel portale di gestione di Azure, selezionare **automazione** e quindi selezionare un Account di automazione.
2. Fare clic su **Importa**.
3. Fare clic su **Sfoglia per File** e individuare il file di script da importare.
4. Se si desidera modificare dal runbook a questo punto, fare clic su **Modifica Runbook**. In caso contrario, fare clic su OK.
5. Nuovo runbook compariranno nella scheda **runbook** per l'Account di automazione.
6. È necessario [pubblicare dal runbook](#publishing-a-runbook) prima di poter eseguire.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Per importare un runbook da un file con il portale di Azure
È possibile utilizzare la procedura seguente per importare un file di script in Azure automazione.  

>[AZURE.NOTE] Si noti che è possibile importare un file ps1 solo in un runbook PowerShell del flusso di lavoro tramite il portale.

1. Nel portale di Azure, aprire l'account di automazione.
2. Fare clic sul riquadro **runbook** per aprire l'elenco di runbook.
3. Fare clic sul pulsante **Aggiungi un runbook** e quindi **importazione**.
4. Fare clic su **file Runbook** per selezionare il file da importare
2. Se il campo **nome** è attivato, quindi è possibile modificarlo.  Il nome runbook deve iniziare con una lettera e può avere lettere, numeri, caratteri di sottolineatura e trattini.
3. Il [tipo di runbook](automation-runbook-types.md) verrà selezionato automaticamente, ma è possibile modificare il tipo dopo avere accettato le restrizioni applicabili in considerazione. 
3. Nuovo runbook verrà visualizzato nell'elenco dei runbook per l'Account di automazione.
4. È necessario [pubblicare dal runbook](#publishing-a-runbook) prima di poter eseguire.

>[AZURE.NOTE] Dopo aver importato un runbook grafica o un grafico runbook del flusso di lavoro PowerShell, è possibile convertire in altro tipo se si desidera. Non è possibile convertire in testo.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Per importare un runbook da un file di script con Windows PowerShell

È possibile utilizzare il cmdlet [AzureRMAutomationRunbook Importa](https://msdn.microsoft.com/library/mt603735.aspx) per importare un file di script come bozza runbook PowerShell del flusso di lavoro. Se dal runbook esiste già, l'importazione non riuscirà se non si utilizza il *-forza* parametro. 

I comandi di esempio seguente viene illustrato come importare un file di script in un runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Pubblicazione di un runbook

Quando si creano o si importa un nuovo runbook, è necessario pubblicare prima di poter eseguire.  Ogni runbook automazione una bozza e una versione pubblicata. Solo la versione pubblicata è disponibile per l'esecuzione e può essere modificata solo la versione bozza. La versione pubblicata non viene influenzata dalle modifiche alla versione bozza. Quando la versione bozza deve essere disponibili, quindi è pubblicarlo che sovrascrivere la versione pubblicata con la versione bozza.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Per pubblicare un runbook tramite il portale di Azure classica

1. Aprire dal runbook nel portale di Azure classica.
1. Nella parte superiore dello schermo, fare clic su **Modifica**.
1. Nella parte inferiore dello schermo, fare clic su **pubblica** e quindi su **Sì** per il messaggio di verifica.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Per pubblicare un runbook tramite il portale di Azure

1. Aprire dal runbook nel portale di Azure.
1. Fare clic sul pulsante **Modifica** .
1. Clic sul pulsante **pubblica** e quindi **Sì** per il messaggio di verifica.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Per pubblicare un runbook usando Windows PowerShell

È possibile utilizzare il cmdlet [AzureRmAutomationRunbook pubblica](https://msdn.microsoft.com/library/mt603705.aspx) per pubblicare un runbook con Windows PowerShell. I comandi di esempio seguente viene illustrato come pubblicare un esempio di runbook.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come è possibile trarre vantaggio dal Runbook e raccolta modulo PowerShell, vedere [le raccolte di Runbook e modulo per l'automazione di Azure](automation-runbook-gallery.md)
- Per ulteriori informazioni sulla modifica runbook PowerShell e PowerShell del flusso di lavoro con un editor di testo, vedere [Modifica runbook testuali in automazione Azure](automation-edit-textual-runbook.md)
- Per maggiori informazioni sulla creazione di grafica runbook, vedere [grafiche creazione condivisa in automazione Azure](automation-graphical-authoring-intro.md)
