<properties 
   pageTitle="Gestione dei dati di Azure automazione | Microsoft Azure"
   description="In questo articolo contiene più argomenti per la gestione di un ambiente di automazione di Azure.  Attualmente include la conservazione dei dati e il backup di emergenza automazione Azure in Azure automazione."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Gestione dei dati di Azure automazione

In questo articolo contiene più argomenti per la gestione di un ambiente di automazione di Azure.

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in Azure automazione, viene mantenuto per 90 giorni per motivi di controllo prima vengono rimossi definitivamente.  Non è possibile visualizzare o utilizzare la risorsa durante questo periodo.  Questo criterio vale anche per le risorse che appartengono a un account di automazione che viene eliminato.

Automazione Azure automaticamente eliminati e processi 90 giorni per rimuovere definitivamente.

Nella tabella seguente vengono riepilogati i criteri di conservazione per diverse risorse.

|Dati|Criteri|
|:---|:---|
|Account|Rimossi definitivamente 90 giorni dopo l'account viene eliminato da un utente.|
|Risorse|Rimossi definitivamente 90 giorni dopo l'eliminazione di risorsa da un utente o 90 giorni dopo l'account contenente che la risorsa viene eliminata da un utente.|
|Moduli|Rimossi definitivamente 90 giorni dopo il modulo viene eliminato da un utente o 90 giorni dopo l'account contenente che il modulo viene eliminato da un utente.|
|Runbook|Rimossi definitivamente 90 giorni dopo l'eliminazione di risorsa da un utente o 90 giorni dopo l'account contenente che la risorsa viene eliminata da un utente.|
|Processi|Eliminati ed è stato rimossi definitivamente 90 giorni dopo l'ultimo venga modificato. Può trattarsi di dopo il processo completa, viene interrotto o è sospesa.|
|Nodo configurazioni/MOF file| Configurazione di nodo precedente verrà rimossi definitivamente 90 giorni dopo la generazione di una nuova configurazione nodo.|
|DSC nodi| Rimossi definitivamente 90 giorni dopo il nodo viene annullato dall'Account di automazione tramite portal Azure o il cmdlet di [Annullamento della registrazione AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) in Windows PowerShell. I nodi vengono rimosse anche definitivamente 90 giorni dopo l'account contenente che il nodo viene eliminato da un utente. |
|Nodo report| Rimossi definitivamente 90 giorni dopo la generazione di un nuovo report per il nodo|

I criteri di conservazione si applica a tutti gli utenti e attualmente non possono essere personalizzato.

## <a name="backing-up-azure-automation"></a>Backup automazione Azure

Quando si elimina un account di automazione in Microsoft Azure, vengono eliminati tutti gli oggetti nella finestra account inclusi runbook, moduli, configurazioni, impostazioni, processi e risorse. Gli oggetti non possono essere recuperati dopo che l'account viene eliminato.  È possibile usare le informazioni seguenti per eseguire il backup il contenuto del proprio account di automazione prima di eliminarlo. 

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook ai file di script tramite il portale di gestione di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) in Windows PowerShell.  Questi file di script possono essere importati in un altro account di automazione come descritto nella [creazione o importazione di un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare i moduli di integrazione di automazione di Azure.  È necessario assicurarsi che siano disponibili di fuori dell'account di automazione.

### <a name="assets"></a>Risorse

Non è possibile esportare [risorse](https://msdn.microsoft.com/library/dn939988.aspx) da Azure automazione.  Tramite il portale di gestione di Azure, è necessario prestare attenzione ai dettagli di variabili, le credenziali, certificati, le connessioni e le pianificazioni.  È quindi necessario creare manualmente tutte le risorse utilizzate da runbook da importare in un'altra automazione.

È possibile utilizzare [i cmdlet di Azure](https://msdn.microsoft.com/library/dn690262.aspx) per recuperare i dettagli di risorse non crittografati e salvarli per riferimento futuro o creare risorse equivalente in un altro account di automazione.

Non è possibile recuperare il valore di variabili crittografate o il campo password delle credenziali utilizzando i cmdlet.  Se non si conoscono questi valori, è possibile recuperarli da un runbook utilizzando le attività [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) .

Non è possibile esportare i certificati da Azure automazione.  È necessario assicurarsi che tutti i certificati sono disponibili all'esterno di Azure.

### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni ai file di script tramite il portale di gestione di Azure o il cmdlet di [Esportazione AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) in Windows PowerShell. Queste configurazioni possono essere importate e usate in un altro account di automazione.


##<a name="geo-replication-in-azure-automation"></a>Replica geografico in automazione Azure

La replica geografico, standard per gli account Azure automazione, il backup account dati su un'area geografica diversa per la ridondanza. È possibile scegliere un'area principale durante la configurazione dell'account e quindi un'area secondaria venga assegnata automaticamente. Dati secondari, copiati da dell'area principale, vengono continuamente aggiornati in caso di perdita di dati.  

Nella tabella seguente mostra le associazioni di aree disponibili primario e secondario.

|Principale            |Secondario
| ---------------   |----------------
|Sud centrale USA   |America del Nord centrale USA
|Stati Uniti orientali 2          |Centrale USA
|Europa occidentale        |Europa Nord America
|Asia sudorientale    |Asia orientale
|Giappone est         |Giappone ovest

In caso di guasto la perdita dei dati un area principale, Microsoft tenta di recuperarlo. Se non è possibile recuperarla dati principali, quindi viene eseguito geografico failover e verranno notificati clienti interessati informazioni tramite il proprio abbonamento.

