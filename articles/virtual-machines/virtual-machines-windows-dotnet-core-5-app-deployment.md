<properties
   pageTitle="Automatizzare la distribuzione di applicazione con estensioni macchina virtuale | Microsoft Azure"
   description="Esercitazione DotNet Core Azure macchina virtuale"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Distribuzione di applicazioni con i modelli di Manager delle risorse di Azure

Una volta tutti i requisiti infrastrutturali Azure sono stati identificati e convertiti in un modello di distribuzione, la distribuzione dell'applicazione effettivo deve essere affrontato. La distribuzione delle applicazioni di seguito fa riferimento all'installazione di file binari di applicazione effettiva su Azure risorse. Per l'esempio di archivio di musica, .net Core e IIS devono essere installato e configurato in ogni macchina virtuale. I file binari musica Store è necessario installare nel computer virtuale e il database dell'archivio musica creato in precedenza.

In questo documento dettaglio come macchina virtuale estensioni possono automatizzare la distribuzione di applicazioni e configurazione di macchine virtuali di Azure. Tutte le dipendenze e configurazioni univoche vengono evidenziate. Per risultati ottimali, distribuire un'istanza della soluzione per l'abbonamento Azure e il lavoro con il modello di gestione di risorse Azure. Il modello completato sono disponibili qui: [Musica distribuzione Store in Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Script di configurazione

Estensioni macchina virtuale sono i programmi specializzati eseguire in macchine virtuali per fornire l'automazione configurazione. Estensioni sono disponibili per diversi scopi specifici, ad esempio antivirus, configurazione di registrazione e configurazione di Docker. L'estensione di Script personalizzato può essere utilizzato per eseguire tutti gli script rispetto a una macchina virtuale. Con l'esempio di archivio di musica, in questo caso, l'estensione di script personalizzati per configurare le macchine virtuali di Windows e installare l'applicazione di archiviazione di musica.

Prima di dettaglio dichiarate come estensioni macchina virtuale in un modello di gestione risorse di Azure, esaminare lo script che viene eseguito. Questo script consente di configurare la macchina virtuale di Windows per ospitare l'applicazione dell'archiviazione musica. Quando si esegue, lo script consente di installare tutti necessari software, installare l'applicazione di archiviazione musica dal controllo origine e preparare il database. 

> In questo esempio è dimostrativo.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Estensione di Script macchine Virtuali

Estensioni macchine Virtuali può essere eseguite su una macchina virtuale in fase di compilazione includendo la risorsa estensione nel modello di gestione di risorse Azure. L'estensione può essere aggiunti con la procedura guidata Visual Studio Aggiungi risorsa oppure inserendo un JSON valido nel modello. La risorsa Script estensione è nidificata all'interno della risorsa macchina virtuale; può essere visualizzato nell'esempio seguente.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse- [Macchine Virtuali Script estensione](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Si noti che nel sotto JSON che lo script viene salvato in GitHub. Questo script può anche essere archiviato in archiviazione Blob Azure. Inoltre, i modelli di gestione risorse Azure consentono la stringa di esecuzione di script costruire in modo che i valori dei parametri di modello possono essere utilizzati come parametri per l'esecuzione di script. In questo caso i dati sono disponibili per la distribuzione di modelli e questi valori possono quindi essere usati durante l'esecuzione di script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Per ulteriori informazioni sull'utilizzo dell'estensione di script personalizzati, vedere [estensioni di script personalizzati con i modelli di Manager delle risorse](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Passaggio successivo

<hr>

[Esplorare Azure più modelli di Manager delle risorse](https://github.com/Azure/azure-quickstart-templates)
