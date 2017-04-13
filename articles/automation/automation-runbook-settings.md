<properties 
   pageTitle="Impostazioni runbook"
   description="Descrive le impostazioni di configurazione per runbook in Azure automazione e come modificarle utilizzando il portale di gestione Azure e di Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Impostazioni runbook

Ogni runbook Azure automazione ha più impostazioni che consentono di identificare e per modificare il comportamento di registrazione. Ognuna di queste impostazioni descritto di seguito seguita da procedure su come modificarle.

## <a name="settings"></a>Impostazioni

### <a name="name-and-description"></a>Nome e una descrizione

Non è possibile modificare il nome di un runbook dopo averlo creato. La descrizione facoltativa e può contenere fino a 512 caratteri.

### <a name="tags"></a>Tag

I contrassegni consentono di assegnare distinte parole o frasi per identificare un runbook. Ad esempio, quando si invia un runbook nella [Raccolta Runbook](https://msdn.microsoft.com/library/dn781422.aspx), specificare particolari tag per identificare le categorie dal runbook dovrebbe essere elencato in. È possibile specificare più tag per un runbook separandole con virgole.

### <a name="logging"></a>Registrazione

Per impostazione predefinita, i record dettagliato e lo stato di avanzamento non vengono scritte alla cronologia processo. È possibile modificare le impostazioni per una particolare runbook per registrare questi record. Per ulteriori informazioni su questi record, vedere [Runbook Output e i messaggi](https://msdn.microsoft.com/library/dn879148.aspx).

## <a name="changing-runbook-settings"></a>Modifica delle impostazioni runbook

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Modifica delle impostazioni di runbook con il portale di gestione di Azure

È possibile modificare le impostazioni per un runbook nel portale di gestione di Azure dalla pagina **Configura** dal runbook.

1. Nel portale di gestione di Azure, selezionare **automazione** e quindi fare clic sul nome di un account di automazione.
1. Selezionare la scheda **runbook** .
1. Fare clic sul nome di un runbook.
1. Selezionare la scheda **Configura** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Modifica delle impostazioni di runbook con Windows PowerShell

È possibile utilizzare il cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) per modificare le impostazioni per un runbook. Se si desidera specificare più contrassegni, è possibile fornire in una matrice o una singola stringa con valori delimitati da virgole per il parametro tag. È possibile ottenere il tag corrente con [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

I comandi di esempio seguente viene illustrato come impostare le proprietà per un runbook. In questo esempio aggiunge tre tag ai tag esistente e specifica che devono essere registrati record dettagliato.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Articoli correlati
- [Output runbook e messaggi](../automation-runbook-output-and-messages) 
- [Creare o importare una Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 