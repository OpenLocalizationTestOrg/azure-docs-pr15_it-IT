<properties
 pageTitle="Guida di riferimento dei cmdlet di PowerShell utilità di pianificazione"
 description="Guida di riferimento dei cmdlet di PowerShell utilità di pianificazione"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>Guida di riferimento dei cmdlet di PowerShell utilità di pianificazione

Nella tabella seguente descrive e collegamenti alla pagina di riferimento di tutti i cmdlet principali nell'utilità di pianificazione di Azure.

Per installare PowerShell Azure e associare all'abbonamento Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). 

Per ulteriori informazioni sui [cmdlet Gestione risorse di Azure](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx), vedere [Uso di PowerShell Azure Gestione risorse Azure](../powershell-azure-resource-manager.md).

|Cmdlet|Descrizione cmdlet|
|---|---|
[Disabilita AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Disattiva un insieme di processo. 
[Abilita AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Abilita la raccolta di un processo.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Ottiene processi utilità di pianificazione.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Ottiene processo raccolte.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Ottiene la cronologia dei processi.
[Nuovo AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Crea un processo HTTP.
[Nuovo AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Crea un insieme di processo.
[Nuovo AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Crea un processo di coda bus del servizio.
[Nuovo AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Crea un processo di argomento bus del servizio.
[Nuovo AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Crea un processo di coda lo spazio di archiviazione. 
[Rimuovi AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Rimuove un processo di pianificazione.  
[Rimuovi AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Rimuove un insieme di processo. 
[Set AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifica di un processo di pianificazione HTTP.
[Set AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifica di un insieme di processo. 
[Set AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifica di un processo di coda bus del servizio.  
[Set AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifica di un processo di argomento bus del servizio. 
[Set AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifica di un processo di coda di archiviazione.   

Per informazioni più dettagliate, è possibile eseguire i cmdlet seguenti: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Vedere anche


 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Azure utilità di pianificazione fondamentali, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Limiti di Azure utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)

 [Azure autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)
