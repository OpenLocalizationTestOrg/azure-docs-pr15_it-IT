<properties
   pageTitle="Uso bene accolta stata configurazione macchina virtuale scala set | Microsoft Azure"
   description="Con scala macchina virtuale imposta con l'estensione DSC Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Con scala macchina virtuale Imposta estensione DSC Azure

Con il gestore di estensione [Azure bene accolta stato configurazione (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) , è possibile utilizzare [Set scala macchina virtuale (VMSS)](virtual-machine-scale-sets-overview.md) . VMSS offre un modo per distribuire e gestire un numero elevato di macchine virtuali e illimitate ridimensionare avanti e indietro in risposta a caricare. DSC viene utilizzato per configurare le macchine virtuali man mano che si sono online che sono in esecuzione il software di produzione.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Differenze tra la distribuzione in macchine Virtuali e VMSS

La struttura di modello sottostante di VMSS è leggermente diversa da una singola macchina virtuale. In particolare, una macchina virtuale singola distribuisce estensioni sotto il nodo "virtualMachines". Esiste una voce di tipo "estensioni" in cui DSC viene aggiunto al modello

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Un nodo VMSS ha una sezione "proprietà" con la "VirtualMachineProfile", "extensionProfile" attributo. DSC viene aggiunto in "estensioni"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Comportamento per VMSS

Il comportamento di VMSS corrisponde a quello per un singolo macchine Virtuali. Quando si crea una nuova macchina virtuale, essa viene automaticamente il provisioning con estensione DSC. Se è necessaria una versione più recente del file WMF dall'estensione, la macchina virtuale Riavvia prima di connettersi. Una volta online, Scarica DSC configurazione ZIP ed effettuare il provisioning nella macchina virtuale. Sono disponibili ulteriori dettagli nella [Azure DSC estensione Panoramica](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Passaggi successivi ##
Esaminare il [modello di gestione risorse di Azure per l'estensione DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Informazioni su come [estensione DSC gestisce in modo sicuro le credenziali](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Per ulteriori informazioni sul gestore estensione DSC Azure, vedere [Introduzione al gestore estensione configurazione dello stato bene accolta Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Per ulteriori informazioni su DSC PowerShell, [visitare l'area documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


