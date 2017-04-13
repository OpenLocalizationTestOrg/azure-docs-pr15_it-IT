<properties
    pageTitle="Attivare la diagnostica nei servizi Cloud Azure tramite PowerShell | Microsoft Azure"
    description="Informazioni su come abilitare diagnostica per i servizi cloud tramite PowerShell"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Attivare la diagnostica nei servizi Cloud Azure tramite PowerShell

È possibile raccogliere dati di diagnostica come registri applicazioni, contatore delle prestazioni e così via da un servizio Cloud utilizzando l'estensione di Azure diagnostica. In questo articolo viene descritto come attivare l'estensione di Azure diagnostica per un servizio Cloud tramite PowerShell.  Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per i prerequisiti necessari per questo articolo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Attivare l'estensione del diagnostica come parte della distribuzione di un servizio Cloud

Questo approccio di corretta per il tipo di integrazione continua di scenari in cui è possibile abilitare l'estensione di diagnostica come parte della distribuzione del servizio cloud. Quando si crea una nuova distribuzione servizio Cloud, è possibile attivare l'estensione di diagnostica passando il parametro *ExtensionConfiguration* al cmdlet [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) . Il parametro *ExtensionConfiguration* accetta una matrice di configurazioni di diagnostica creata utilizzando il cmdlet [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

Nell'esempio seguente mostra come è possibile abilitare diagnostica per un servizio cloud con WebRole e WorkerRole ognuno con una configurazione di diagnostica diversi.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Se il file di configurazione di diagnostica specifica un elemento StorageAccount con un nome di account di archiviazione, il cmdlet New-AzureServiceDiagnosticsExtensionConfig utilizzerà automaticamente l'account di archiviazione. Per questa, l'account di archiviazione deve essere nello stesso abbonamento servizio Cloud in fase di distribuzione.

Da Azure SDK 2.6 in poi pubblicano i file di configurazione di estensione generati da di MSBuild output di destinazione conterrà il nome dell'account di archiviazione in base alla stringa di configurazione di diagnostica specificata nel file di configurazione del servizio (. cscfg). Script riportato di seguito viene illustrato come analizzare i file di configurazione estensione dall'output di destinazione della pubblicazione e configurare l'estensione di diagnostica per ogni ruolo la distribuzione del servizio cloud.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

In linea di Visual Studio viene utilizzato un approccio simile per deploymnts automatico di servizi Cloud con l'estensione di diagnostica. Vedere [AzureCloudDeployment.ps1 pubblica](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) per un esempio completo.

Se non StorageAccount è stato specificato nella configurazione di diagnostica, è necessario passare nel parametro StorageAccountName al cmdlet. Se il parametro StorageAccountName viene specificato, il cmdlet utilizza sempre l'account di archiviazione specificato nel parametro e non quello specificato nel file di configurazione di diagnostica.

Se l'account di archiviazione di diagnostica in una sottoscrizione diversa dal servizio Cloud, è necessario passare in modo esplicito i parametri StorageAccountName e StorageAccountKey al cmdlet. Il parametro StorageAccountKey non è necessario quando l'account di archiviazione di diagnostica nello stesso abbonamento, come il cmdlet possibile automaticamente query e impostare il valore di chiave quando si abilita l'estensione di diagnostica. Tuttavia, se l'account di archiviazione di diagnostica in una sottoscrizione diversa, il cmdlet potrebbe non essere possibile ottenere la chiave automaticamente e si è necessario specificare la chiave tramite il parametro StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Attivare l'estensione del diagnostica in un servizio Cloud esistente

È possibile utilizzare il cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) per attivare o aggiornare la configurazione di diagnostica per un servizio Cloud che è già in esecuzione.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Ottenere la configurazione di estensione diagnostica corrente
Utilizzare il cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) per ottenere la configurazione di diagnostica corrente di un servizio cloud.

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Rimuovere l'estensione di diagnostica
Per disattivare la diagnostica su un servizio cloud è possibile utilizzare il cmdlet [AzureServiceDiagnosticsExtension Rimuovi](https://msdn.microsoft.com/library/azure/mt589183.aspx) .

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Se è abilitata l'estensione di diagnostica utilizzando *Set AzureServiceDiagnosticsExtension* o *Nuovo AzureServiceDiagnosticsExtensionConfig* senza il parametro *ruolo* è possibile rimuovere l'estensione utilizzando *Rimuovi AzureServiceDiagnosticsExtension* senza il parametro *ruolo* . Se il parametro *ruolo* è stato utilizzato quando si abilita l'estensione quindi necessario anche utilizzarlo quando si rimuove l'estensione.

Per rimuovere l'estensione di diagnostica da ogni ruolo singoli:

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori istruzioni sull'uso di diagnostica Windows Azure e altre tecniche per la risoluzione dei problemi, vedere [Attivazione di diagnostica in macchine virtuali e servizi Cloud Windows Azure](cloud-services-dotnet-diagnostics.md).
- Lo [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) illustra le varie opzioni di configurazioni xml per l'estensione di diagnostica.
- Per informazioni su come attivare l'estensione di diagnostica per macchine virtuali, vedere [creazione di un computer Windows virtuale con il monitoraggio e diagnostica utilizzando il modello di gestione risorse di Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
