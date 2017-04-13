<properties
   pageTitle="Risoluzione degli errori di estensione macchine Virtuali di Windows | Microsoft Azure"
   description="Informazioni sulla risoluzione dei problemi di estensione macchine Virtuali di Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Risoluzione degli errori di estensione macchine Virtuali di Windows Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visualizzazione dello stato di estensione
Modelli di Azure Manager delle risorse possono essere eseguiti da Azure PowerShell. Dopo il modello viene eseguito, lo stato di estensione può essere visualizzato da Esplora risorse di Azure o degli strumenti della riga di comando.

Ecco un esempio:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Di seguito è riportato l'output:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Risoluzione degli errori di estensione

### <a name="re-running-the-extension-on-the-vm"></a>Rieseguire l'estensione nella macchina virtuale

Se si esegue script nella macchina virtuale utilizzando l'estensione di Script personalizzati, in alcuni casi è possibile eseguire in un messaggio di errore in macchine Virtuali è stato creato correttamente ma lo script non è riuscita. In queste condizioni, il modo consigliato per risolvere il problema consiste nel rimuovere l'estensione ed eseguire di nuovo il modello.
Nota: In futuro, questa funzionalità da essere migliorata per rimuovere la necessità di disinstallazione dell'estensione.


#### <a name="remove-the-extension-from-azure-powershell"></a>Rimuovere l'estensione da Azure PowerShell

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Dopo aver rimosso l'estensione, il modello può essere eseguito nuovamente eseguire gli script nella macchina virtuale.
