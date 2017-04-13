<properties 
    pageTitle="Monitorare e gestire i processi di flusso Analitica con PowerShell | Microsoft Azure" 
    description="Informazioni su come usare PowerShell Azure e i cmdlet per monitorare e gestire i processi di flusso Analitica." 
    keywords="powershell Azure, i cmdlet di powershell azure, comando di powershell e gli script di powershell" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorare e gestire i processi di flusso Analitica con i cmdlet di PowerShell di Azure

Informazioni su come monitorare e gestire le risorse Analitica di flusso con i cmdlet di PowerShell Azure e gli script di powershell che eseguono attività di base flusso Analitica.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Prerequisiti per l'esecuzione dei cmdlet di PowerShell Azure per Analitica flusso

 - Creare un gruppo di risorse Azure nell'abbonamento. Di seguito è riportato uno script di PowerShell Azure. Per informazioni di Azure PowerShell, vedere [installare e configurare Azure PowerShell](../powershell-install-configure.md);  

Azure PowerShell 0.9.8:  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Processi di Analitica flusso creati a livello di programmazione non dispone di monitoraggio attivata per impostazione predefinita.  È possibile attivare manualmente il monitoraggio nel portale di Azure, passare alla pagina di monitoraggio del processo e fare clic sul pulsante Abilita o è possibile eseguire questa operazione a livello di programmazione, eseguire le operazioni che si trova in [Azure flusso Analitica - Monitor flusso Analitica processi a livello di programmazione](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlet di PowerShell Azure per Analitica flusso
Utilizzare i cmdlet di PowerShell Azure seguenti per monitorare e gestire i processi di Azure flusso Analitica. Si noti che Azure PowerShell ha diverse versioni. 
**Negli esempi elencati il primo comando riguarda Azure PowerShell 0.9.8, il secondo comando riguarda Azure PowerShell 1.0.** I comandi di Azure PowerShell 1.0 avrà sempre "AzureRM" nel comando.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Elenca tutti i processi di flusso Analitica definiti nell'abbonamento Azure o gruppo di risorse specificato o ottiene informazioni sul processo su un processo specifico all'interno di un gruppo di risorse.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Questo comando di PowerShell restituisce informazioni su tutti i processi di flusso Analitica nella sottoscrizione Azure.

**Esempio 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Questo comando di PowerShell restituisce informazioni su tutti i processi di flusso Analitica nel gruppo di risorse StreamAnalytics-predefinito centrale US.

**Esempio 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Questo comando di PowerShell restituisce informazioni sul processo di flusso Analitica StreamingJob nel gruppo di risorse StreamAnalytics-predefinito centrale US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Elenca tutti gli input definiti in un processo di flusso Analitica specificato o ottiene informazioni relative a un input specifico.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Questo comando di PowerShell restituisce informazioni su tutti gli input definito nel processo StreamingJob.

**Esempio 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Questo comando di PowerShell restituisce informazioni sull'input denominata EntryStream definita nel processo StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Elenco di tutti gli output sono definiti in un processo di flusso Analitica specificato o ottiene informazioni output specifico.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Questo comando di PowerShell restituisce informazioni sull'output definito nel processo StreamingJob.

**Esempio 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Questo comando di PowerShell restituisce informazioni sull'output denominata Output definito nel processo StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Ottiene informazioni la quota di streaming unità in un'area specifica.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Questo comando di PowerShell restituisce le informazioni sulla quota e utilizzo di unità flusso nell'area centrale USA.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Ottiene informazioni su una trasformazione specifica definita in un processo di flusso Analitica.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Questo comando di PowerShell restituisce le informazioni sulla trasformazione denominata StreamingJob nel processo StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Nuovo AzureStreamAnalyticsInput | Nuovo AzureRMStreamAnalyticsInput
Crea un nuovo input all'interno di un processo Analitica flusso o aggiorna input specificato esistente.
  
Nel file .json o riga di comando, è possibile specificare il nome dell'input. Se sono specificati entrambi, il nome nella riga di comando deve essere uguale a quello nel file.

Se si specifica un input già esistente e non si specifica il vigore parametro –, verrà richiesto di sostituire l'input esistente o meno il cmdlet.

Se si specifica – forzare parametro e specificare un esistente input nome, l'input verrà sostituita senza conferma.

Per informazioni dettagliate sulla struttura di file JSON e contenuto, fare riferimento alla [Creazione di Input (Azure flusso Analitica)] [ msdn-rest-api-create-stream-analytics-input] sezione della [raccolta di flusso Analitica gestione resto API riferimento][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Questo comando di PowerShell crea un nuovo input dal file Input.json. Se è già definito input esistente con il nome specificato nel file di definizione di input, verrà richiesto il cmdlet per sostituirlo o meno.

**Esempio 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Questo comando di PowerShell crea un nuovo input nel processo chiamato EntryStream. Se è già definito input esistente con questo nome, il cmdlet verrà chiesto di sostituirlo o meno.

**Esempio 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Questo comando di PowerShell sostituisce la definizione di origine di input esistente denominata EntryStream con la definizione del file.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Nuovo AzureStreamAnalyticsJob | Nuovo AzureRMStreamAnalyticsJob
Crea un nuovo processo flusso Analitica in Microsoft Azure o aggiorna la definizione di un processo specificato esistente.

Nel file .json o riga di comando, è possibile specificare il nome del processo. Se sono specificati entrambi, il nome nella riga di comando deve essere uguale a quello nel file.

Se si specifica un nome di processo che esiste già e non si specifica il vigore parametro –, verrà richiesto di sostituire un processo esistente o meno il cmdlet.

Se si specifica – forzare parametro e specificare un nome di processo esistente, la definizione del processo verrà sostituita senza conferma.

Per informazioni dettagliate sulla struttura di file JSON e contenuto, fare riferimento al [Crea flusso Analitica processo] [ msdn-rest-api-create-stream-analytics-job] sezione della [raccolta di flusso Analitica gestione resto API riferimento][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Questo comando di PowerShell crea un nuovo processo dalla definizione nel JobDefinition.json. Se un processo esistente con il nome specificato nel file di definizione di processo è già stato definito, verrà richiesto il cmdlet per la sostituzione o meno.

**Esempio 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Questo comando di PowerShell sostituisce la definizione del processo per StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Nuovo AzureStreamAnalyticsOutput | Nuovo AzureRMStreamAnalyticsOutput
Crea un nuovo output all'interno di un processo di flusso Analitica o aggiorna un output esistente.  

Nel file .json o riga di comando, è possibile specificare il nome dell'output. Se sono specificati entrambi, il nome nella riga di comando deve essere uguale a quello nel file.

Se si specifica un output già esistente e non si specifica il vigore parametro –, verrà richiesto di sostituire l'output esistente o meno il cmdlet.

Se si specifica – forzare parametro e specificare un esistente output nome, l'output verrà sostituito senza conferma.

Per informazioni dettagliate sulla struttura di file JSON e contenuto, fare riferimento alla [Creazione di Output (Azure flusso Analitica)] [ msdn-rest-api-create-stream-analytics-output] sezione della [raccolta di flusso Analitica gestione resto API riferimento][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Questo comando di PowerShell crea un nuovo output denominato "output" nel processo StreamingJob. Se un output esistente con questo nome è già definito, verrà richiesto il cmdlet per la sostituzione o meno.

**Esempio 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Questo comando di PowerShell sostituisce la definizione di "output" nel processo StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Nuovo AzureStreamAnalyticsTransformation | Nuovo AzureRMStreamAnalyticsTransformation
Crea una nuova trasformazione all'interno di un processo di flusso Analitica o aggiorna la trasformazione esistente.
  
Nel file .json o riga di comando, è possibile specificare il nome della trasformazione. Se sono specificati entrambi, il nome nella riga di comando deve essere uguale a quello nel file.

Se si specifica una trasformazione già esistente e non si specifica il vigore parametro –, verrà richiesto di sostituire la trasformazione esistente o meno il cmdlet.

Se si specifica – forzare parametro e specificare un nome di trasformazione esistente, la trasformazione verrà sostituita senza conferma.

Per informazioni dettagliate sulla struttura di file JSON e contenuto, fare riferimento alla [Creazione di trasformazione (Azure flusso Analitica)] [ msdn-rest-api-create-stream-analytics-transformation] sezione della [raccolta di flusso Analitica gestione resto API riferimento][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Questo comando di PowerShell crea una nuova trasformazione denominata StreamingJobTransform nel processo StreamingJob. Se una trasformazione esistente è già stata definita con questo nome, il cmdlet verrà richiesto di sostituire o meno.

**Esempio 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Questo comando di PowerShell sostituisce la definizione di StreamingJobTransform nel processo StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Rimuovi AzureStreamAnalyticsInput | Rimuovi AzureRMStreamAnalyticsInput
Elimina in modo asincrono un input specifico da un processo di flusso Analitica in Microsoft Azure.  
Se si specifica il vigore parametro –, verrà eliminata l'input senza conferma.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Questo comando di PowerShell rimuove EventStream input nel processo StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Rimuovi AzureStreamAnalyticsJob | Rimuovi AzureRMStreamAnalyticsJob
Elimina in modo asincrono un processo Analitica flusso specifico in Microsoft Azure.  
Se si specifica il vigore parametro –, il processo verrà eliminato senza conferma.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Questo comando di PowerShell rimuove il processo StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Rimuovi AzureStreamAnalyticsOutput | Rimuovi AzureRMStreamAnalyticsOutput
Elimina in modo asincrono output specifico da un processo di flusso Analitica in Microsoft Azure.  
Se si specifica il vigore parametro –, verrà eliminata l'output senza conferma.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Rimuove questo comando di PowerShell l'output di Output nel processo StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Inizio AzureStreamAnalyticsJob | Inizio AzureRMStreamAnalyticsJob
In modo asincrono distribuisce e avvia un processo del flusso Analitica in Microsoft Azure.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Il comando PowerShell avvia il processo StreamingJob con un'ora di inizio output personalizzato impostato su 12 dicembre 2012 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Interrompi AzureStreamAnalyticsJob | Interrompi AzureRMStreamAnalyticsJob
In modo asincrono viene interrotto il processo di flusso Analitica da in esecuzione in Microsoft Azure e deselezionare Assegna risorse che sono stati che sono stati utilizzati. La definizione del processo e i metadati rimarranno disponibili all'interno di abbonamento tramite il portale di Azure e una gestione API, in modo che il processo può essere modificato e si riavvia. Non ti per un processo viene interrotto in.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Questo comando di PowerShell interrompe il processo StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test AzureStreamAnalyticsInput | Test AzureRMStreamAnalyticsInput
Verifica la possibilità di flusso Analitica per connettersi a un input specificato.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Questo comando di PowerShell verifica lo stato di connessione di input EntryStream in StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test AzureStreamAnalyticsOutput | Test AzureRMStreamAnalyticsOutput
Verifica la possibilità di flusso Analitica per connettersi a un output specificato.

**Esempio 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Questo test dei comandi PowerShell lo stato di connessione di output di Output in StreamingJob.  

## <a name="get-support"></a>Ottenere supporto
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
