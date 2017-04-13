<properties
   pageTitle="Integrazione di Azure log domande frequenti su | Microsoft Azure"
   description="In questo argomento fornisce le risposte alle domande sull'integrazione di Azure log."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Integrazione di Azure log frequenti domande frequenti

In questo argomento vengono fornite le risposte alle domande sull'integrazione di Azure log, un servizio che consente di integrare i sistemi di informazioni sulla protezione e gestione di evento (SIEM) locale registri elaborati dalle risorse Azure. Questa integrazione fornisce un dashboard unificato per tutte le attività, in locale o nel cloud, in modo da aggregare, correlare, analizzare e un avviso per gli eventi di protezione associati alle applicazioni.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Come è possibile visualizzare gli account di archiviazione da cui l'integrazione di Azure log è il pull e registri macchine Virtuali di Azure?

Eseguire il comando **azlog elenco di origine**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Come è possibile aggiornare la configurazione del proxy?

Se le impostazioni proxy non consente l'accesso di archiviazione Azure direttamente, aprire **AZLOG. EXE. CONFIGURAZIONE** file in **c:\Programmi\Microsoft c:\Programmi\Microsoft Azure Log integrazione**. Aggiornare il file in modo da includere la sezione **defaultProxy** con l'indirizzo proxy dell'organizzazione. Al termine dell'aggiornamento, arrestare e riavviare il servizio usando i comandi **azlog Interrompi netto** e **azlog start netto**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Come è possibile visualizzare le informazioni di sottoscrizione di eventi di Windows?

Aggiungere il **subscriptionid** al nome descrittivo durante l'aggiunta dell'origine.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

L'evento XML è i metadati come illustrato di seguito, incluso l'id di sottoscrizione.

![Evento XML][1]

## <a name="error-messages"></a>Messaggi di errore

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Quando si esegue il comando **azlog createazureid**, perché viene visualizzato l'errore seguente?

Errore:

  *Impossibile creare l'applicazione AAD - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37-motivo = 'Non consentito' - messaggio = "Privilegi insufficienti per completare l'operazione".*

**Azlog createazureid** tenta di creare un'entità servizio in tutti i tenant di Azure Active Directory per le sottoscrizioni in cui l'account di accesso Azure ha accesso a. Se l'accesso Azure viene solo un utente Guest nel tenant Azure Active Directory, il comando non riesce con "Privilegi insufficienti per completare l'operazione". Richiedere amministratore Tenant di aggiungere il proprio account come utente nel tenant.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Durante l'esecuzione di comando **azlog autorizzare**, perché visualizzato l'errore seguente?

Errore:

  *Avviso creazione di assegnazione di ruolo - AuthorizationFailed: il client janedo@microsoft.com' con oggetto id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' non dispone di autorizzazioni per eseguire l'operazione 'Microsoft.Authorization/roleAssignments/write' ambito '/ abbonamenti/70 d 95299-d689-4c 97-b971-0d8ff0000000'.*

Comando **Azlog autorizzare** assegna il ruolo di lettore al servizio di Azure Active Directory principale, creato con **Azlog createazureid**, per gli abbonamenti forniti. Se l'account di accesso Azure non è un amministratore condivisa o un proprietario della sottoscrizione, non riesce con messaggio di errore "Autorizzazione non riuscita". Controllo dell'accesso basato sui ruoli Azure (RBAC) del proprietario o CO-amministratore è necessario per completare l'operazione.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Nel punto in cui è possibile trovare la definizione delle proprietà in log di controllo?

Vedere:

- [Controllare le operazioni con Gestione risorse](../resource-group-audit.md)
- [Elenco degli eventi di gestione di un abbonamento in API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Dove reperibili ulteriori informazioni sugli avvisi di Centro protezione di Azure

Vedere [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Come è possibile modificare informazioni raccolte con diagnostica macchine Virtuali?

Per informazioni dettagliate su come ottenere, modifica, vedere [Usare PowerShell per attivare la diagnostica in un computer virtuale che esegue Windows Azure](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) e impostare il Azure diagnostica di Windows *(tampone)* configurazione. Di seguito è riportato un esempio:

### <a name="get-the-wad-config"></a>Ottenere la configurazione di tampone

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modificare la configurazione di tampone

Nell'esempio seguente è una configurazione in cui solo EventID 4624 ed EventId 4625 sono stati raccolti dal registro eventi di protezione. Microsoft Antimalware eventi sono stati raccolti dal registro eventi di sistema. Vedere [utilizzo degli eventi] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) per informazioni dettagliate sull'utilizzo di espressioni XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Impostare la configurazione di tampone

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Dopo aver apportato le modifiche, selezionare l'account di archiviazione per garantire che sono stati raccolti gli eventi corretti.

Se hai domande sull'integrazione di Log di Azure, inviare un messaggio di posta elettronica a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
