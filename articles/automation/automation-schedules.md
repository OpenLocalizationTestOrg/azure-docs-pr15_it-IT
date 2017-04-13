<properties 
   pageTitle="Le pianificazioni di automazione Azure | Microsoft Azure"
   description="Le pianificazioni di automazione vengono utilizzate per pianificare runbook in Azure automazione per l'avvio automatico. In questo articolo viene descritto come creare e gestire una pianificazione in modo che è possibile avviare automaticamente una runbook in un determinato momento o in una pianificazione ricorrente."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="mgoedtel" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Pianificazione runbook di automazione di Azure

Per pianificare un runbook di automazione di Azure per avviare un'ora specifica, viene collegata a una o più pianificazioni. Una pianificazione può essere configurata alla pianificazione di una sola volta o in un ripresenta ogni ora fase o giornaliera per runbook nel portale di classica Azure e per runbook nel portale di Azure, è possibile inoltre pianificare loro per settimanale, mensile, determinati giorni della settimana o giorni del mese o un determinato giorno del mese.  Un runbook può essere collegato a più pianificazioni e una pianificazione può includere più runbook collegato ad esso.

>[AZURE.NOTE]  Le pianificazioni attualmente non supporta le configurazioni di Azure automazione DSC.

## <a name="windows-powershell-cmdlets"></a>Cmdlet di Windows PowerShell

I cmdlet descritti nella tabella seguente vengono utilizzati per creare e gestire le pianificazioni con Windows PowerShell in Azure automazione. Vengono forniti come parte del [modulo PowerShell per Azure](../powershell-install-configure.md).

|Cmdlet|Descrizione|
|:---|:---|
|**Cmdlet per la gestione risorse di Azure**||
|[Get-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603733.aspx)|Recupera una programmazione.|
|[Nuovo AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx)|Crea una nuova programmazione.|
|[Rimuovi AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603691.aspx)|Rimuove una pianificazione.|
|[Set AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx)|Imposta le proprietà di una pianificazione esistente.|
|[Get-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt619406.aspx)|Recupera programmata runbook.|
|[Registro AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx)|Associa un runbook a una programmazione.|
|[Annullare la registrazione AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603844.aspx)|Dissociates runbook da una pianificazione.|
|**Cmdlet per la gestione dei servizi Azure**||
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Recupera una programmazione.|
|[Nuovo AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Crea una nuova programmazione.|
|[Rimuovi AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Rimuove una pianificazione.|
|[Set AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Imposta le proprietà di una pianificazione esistente.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Recupera programmata runbook.|
|[Registro AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Associa un runbook a una programmazione.|
|[Annullare la registrazione AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Dissociates runbook da una pianificazione.|

## <a name="creating-a-schedule"></a>Creazione di una pianificazione

È possibile creare una nuova pianificazione per runbook nel portale di Azure, nel portale di classica o con Windows PowerShell. È inoltre possibile creare una nuova programmazione quando si collega un runbook a una programmazione tramite il portale classico o Azure Azure di.

>[AZURE.NOTE] Quando si associa una programmazione con un runbook, automazione archivia le versioni correnti dei moduli nell'account e li collega a tale programmazione.  Questo significa che se si dispone di un modulo con la versione 1.0 nell'account quando si è creato una pianificazione e quindi aggiornare il modulo alla versione 2.0, la pianificazione continuerà a utilizzare 1.0.  Per usare la versione del modulo aggiornato, è necessario creare una nuova programmazione. 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Per creare una nuova programmazione nel portale di Azure

1. Nel portale di Azure, dal proprio account di automazione, fare clic sul riquadro **attività** per aprire e il **risorse** .
2. Fare clic sul riquadro **le pianificazioni** per aprire e il **pianificazioni** .
3. Fare clic su **Aggiungi una pianificazione** nella parte superiore e il.
4. Digitare un **nome** e facoltativamente una **Descrizione** per la nuova pianificazione e il **nuova pianificazione** .
5. Selezionare se la programmazione verrà eseguita una sola volta o in una pianificazione ricorrente selezionando **una sola volta** o **ricorrenza**.  Se si seleziona **una volta** specificare un' **ora di inizio** e quindi fare clic su **Crea**.  Se si seleziona **ricorrenza**, specificare l' **ora di inizio** e la frequenza con la frequenza con cui si desidera dal runbook per ripetere - **ora**, **giorno**, **settimana**o **mese**.  Se si seleziona **settimana** o **mese** dall'elenco a discesa, verrà visualizzata l' **opzione di ricorrenza** in e il e seleziona, verrà visualizzato e **l'opzione ricorrenza** e se si seleziona **settimana**, è possibile selezionare il giorno della settimana.  Se si seleziona **mese**, è possibile scegliere **giorni settimana** o giorni specifici del mese nel calendario e infine eseguire che si desidera eseguire l'ultimo giorno del mese o meno e quindi fare clic su **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Per creare una nuova programmazione nel portale di classica Azure

1. Nel portale di Azure classico selezionare automazione e quindi scegliere il nome di un account di automazione.
1. Selezionare la scheda **attività** .
1. Nella parte inferiore della finestra, fare clic su **Aggiungi impostazione**.
1. Fare clic su **Aggiungi pianificazione**.
1. Digitare un **nome** e facoltativamente una **Descrizione** per la nuova pianificazione schedule.your eseguiranno **Una sola volta**, **oraria**, **giornaliera**, **settimanale**o **mensile**.
1. Specificare un' **Ora di inizio** e altre opzioni in base al tipo di pianificazione è stata selezionata.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Per creare una nuova programmazione con Windows PowerShell

È possibile utilizzare il cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) per creare una nuova programmazione in Azure automazione per runbook classica o cmdlet [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) per runbook nel portale di Azure. È necessario specificare l'ora di inizio per la pianificazione e la frequenza che deve essere eseguita.

I comandi di esempio seguente viene illustrato come creare una pianificazione per il 15 e 30 di ogni mese utilizzando un cmdlet Gestione risorse Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

I comandi di esempio seguente viene illustrato come creare una nuova programmazione che viene eseguita ogni giorno 3:30 PM in 20 gennaio 2015 a partire da un cmdlet di gestione dei servizi di Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Collegare un runbook una pianificazione

Un runbook può essere collegato a più pianificazioni e una pianificazione può includere più runbook collegato ad esso. Se un runbook include parametri, è possibile fornire i valori per loro. È necessario fornire i valori per tutti i parametri obbligatori e può fornire i valori per tutti i parametri facoltativi.  Questi valori verranno usati ogni volta che viene avviato dal runbook per la pianificazione.  È possibile allegare runbook stessi a un'altra pianificazione e specificare i valori dei parametri diversi.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Collegare una programmazione runbook con il portale di Azure

1. Nel portale di Azure, dal proprio account di automazione, fare clic sul riquadro **runbook** per aprire e il **runbook** .
2. Fare clic sul nome dal runbook per la programmazione.
3. Se dal runbook non è collegato a una programmazione, quindi sarà possibile l'opzione per creare una nuova programmazione o un collegamento a una pianificazione esistente.  
4. Se dal runbook include parametri, è possibile selezionare l'opzione **Modifica impostazioni (impostazione predefinita: Azure) di esecuzione** ed e il **parametri** è suddivise in cui è possibile immettere le informazioni di conseguenza.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Collegare una programmazione runbook con il portale classico Azure

1. Nel portale di Azure classico selezionare **automazione** e quindi fare clic sul nome di un account di automazione.
2. Selezionare la scheda **runbook** .
3. Fare clic sul nome dal runbook per la programmazione.
4. Fare clic sulla scheda **programmazione** .
5. Se dal runbook non è collegato a una programmazione, quindi sarà possibile l'opzione di **collegamento a una nuova programmazione** o un **collegamento a una pianificazione esistente**.  Se dal runbook è collegato a una programmazione, fare clic sul **collegamento** nella parte inferiore della finestra per accedere a queste opzioni.
6. Se dal runbook include parametri, verrà richiesto per i rispettivi valori.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Collegare una programmazione runbook con Windows PowerShell

Collegare una pianificazione di un runbook classica o cmdlet [Registro AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) per runbook nel portale di Azure, è possibile usare il [Registro AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) .  È possibile specificare i valori dei parametri di runbook con il parametro parametri. Per ulteriori informazioni su come specificare i valori dei parametri, vedere [avvio Runbook di automazione di Azure](automation-starting-a-runbook.md) .

I comandi di esempio seguente viene illustrato come creare un collegamento a un runbook utilizzando un cmdlet Gestione risorse Azure con i parametri una programmazione.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
I comandi di esempio seguente viene illustrato come creare un collegamento una pianificazione utilizzando un cmdlet di gestione dei servizi di Azure con parametri.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>La disattivazione di una programmazione

Quando si disattiva una pianificazione, qualsiasi runbook collegato ad esso non verrà eseguito su tale pianificazione. È possibile disabilitare una pianificazione o impostare una scadenza per le pianificazioni con una frequenza quando si crea manualmente. Quando viene raggiunta la scadenza, la pianificazione verrà disabilitata.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Per disabilitare una pianificazione dal portale di Azure

1. Nel portale di Azure, dal proprio account di automazione, fare clic sul riquadro **attività** per aprire e il **risorse** .
2. Fare clic sul riquadro **le pianificazioni** per aprire e il **pianificazioni** .
2. Fare clic sul nome di una pianificazione per aprire e il dettagli.
3. Modificare **abilitato** su **No**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Per disabilitare una pianificazione dal portale di classica Azure

È possibile disabilitare una programmazione nel portale di classica Azure dalla pagina dei dettagli della pianificazione per la programmazione.

1. Nel portale di Azure classico selezionare automazione e quindi fare clic sul nome di un account di automazione.
1. Selezionare la scheda attività.
1. Fare clic sul nome di una pianificazione per aprire la pagina di dettaglio.
2. Modificare **abilitato** su **No**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Per disabilitare una programmazione con Windows PowerShell

È possibile utilizzare il cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) per modificare le proprietà di una pianificazione esistente per un runbook classico o il cmdlet [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) per runbook nel portale di Azure. Per disattivare la pianificazione, specificare **false** per il parametro **IsEnabled** .

I comandi di esempio seguente viene illustrato come disabilitare una pianificazione per un runbook utilizzando un cmdlet Gestione risorse Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

I comandi di esempio seguente viene illustrato come disabilitare una pianificazione utilizzando il cmdlet di gestione dei servizi di Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a utilizzare runbook in Azure automazione, vedere [avvio Runbook di automazione di Azure](automation-starting-a-runbook.md) 