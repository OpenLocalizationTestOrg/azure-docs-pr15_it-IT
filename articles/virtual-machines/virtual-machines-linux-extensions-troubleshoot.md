<properties
   pageTitle="Risoluzione degli errori di estensione Linux VM | Microsoft Azure"
   description="Informazioni sulla risoluzione degli errori di estensione macchine Virtuali Linux Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Risoluzione degli errori di estensione macchine Virtuali Linux Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visualizzazione dello stato di estensione
Modelli di Azure Manager delle risorse possono essere eseguiti da CLI Azure. Dopo il modello viene eseguito, lo stato di estensione può essere visualizzato da Esplora risorse di Azure o degli strumenti della riga di comando.

Ecco un esempio:

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Risoluzione degli errori di Extenson:

### <a name="re-running-the-extension-on-the-vm"></a>Rieseguire l'estensione nella macchina virtuale

Se si esegue script nella macchina virtuale utilizzando l'estensione di Script personalizzati, in alcuni casi è possibile eseguire in un messaggio di errore in macchine Virtuali è stato creato correttamente ma lo script non è riuscita. In queste condizioni, il modo consigliato per risolvere il problema consiste nel rimuovere l'estensione ed eseguire di nuovo il modello.
Nota: In futuro, questa funzionalità da essere migliorata per rimuovere la necessità di disinstallazione dell'estensione.

#### <a name="remove-the-extension-from-azure-cli"></a>Rimuovere l'estensione dal CLI Azure

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Dove "nome publsher" corrisponde al tipo di estensione dall'output di "macchine virtuali azure get-istanza visualizzazione" e nome è il nome della risorsa estensione dal modello

Dopo aver rimosso l'estensione, il modello può essere eseguito nuovamente eseguire gli script nella macchina virtuale.
