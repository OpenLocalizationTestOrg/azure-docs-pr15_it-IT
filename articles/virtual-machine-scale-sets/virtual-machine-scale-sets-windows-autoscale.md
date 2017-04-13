<properties
    pageTitle="Imposta scala macchina virtuale di Windows AutoScale | Microsoft Azure"
    description="Impostare il ridimensionamento automatico per un Windows macchina virtuale scala tramite PowerShell Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Adattamento automatico macchine in un set di scala macchina virtuale

Set di scala macchina virtuale semplificano distribuire e gestire macchine virtuali identiche come set. Set di scala offrono un livello di elaborazione altamente scalable e personalizzabile per le applicazioni superscalari e supportano immagini piattaforma Windows, Linux piattaforma immagini, immagini personalizzate e le estensioni. Per ulteriori informazioni sui set di scala, vedere [Set scala macchina virtuale](virtual-machine-scale-sets-overview.md).

In questa esercitazione viene illustrato come creare un set di scala di macchine virtuali di Windows e ridimensionare automaticamente i computer nel set. Creare la scala e impostare il ridimensionamento per creare un modello di gestione risorse di Azure e distribuirlo tramite PowerShell Azure. Per ulteriori informazioni sui modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md). Per ulteriori informazioni sul ridimensionamento automatico del set di scala, vedere [il ridimensionamento automatico e Imposta scala macchina virtuale](virtual-machine-scale-sets-autoscale-overview.md).

In questo articolo è distribuire le risorse e le estensioni seguenti:

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Per ulteriori informazioni sulle risorse Manager delle risorse, vedere [Azure calcolare, rete e il provider di archiviazione in Gestione risorse di Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md).

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: Installare PowerShell Azure

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e accedere a Azure.

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Passaggio 2: Creare un gruppo di risorse e un account di archiviazione

1. **Creare un gruppo di risorse** : tutte le risorse devono essere distribuite a un gruppo di risorse. Utilizzare [AzureRmResourceGroup di nuovo](https://msdn.microsoft.com/library/mt603739.aspx) per creare un gruppo di risorse denominato **vmsstestrg1**.

2. **Creare un account di archiviazione** : questo account di archiviazione è in cui è archiviato il modello. Consente di creare un account di archiviazione denominato **vmsstestsa** [AzureRmStorageAccount di nuovo](https://msdn.microsoft.com/library/mt607148.aspx) .

## <a name="step-3-create-the-template"></a>Passaggio 3: Creare il modello
Un modello di gestione di risorse Azure consente di distribuire e gestire risorse Azure insieme utilizzando una descrizione JSON le risorse e i parametri di distribuzione associato.

1. In un editor, creare il file C:\VMSSTemplate.json e aggiungere la struttura iniziale JSON per supportare il modello.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Parametri non sono sempre necessari, ma consentono di valori di input quando si distribuisce il modello. Aggiungere questi parametri sotto l'elemento padre parametri che è stato aggiunto al modello.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Imposta un nome per il computer virtuale separato che viene utilizzato per accedere alle macchine nella scala.
    - Il nome dell'account di archiviazione in cui è archiviato il modello.
    - Imposta il numero di macchine virtuali di creare inizialmente nella scala.
    - Il nome e la password dell'account administrator in macchine virtuali.
    - Imposta un prefisso del nome per le risorse che vengono creati per supportare la scala.

3. Variabili possono essere utilizzate in un modello per specificare i valori possono cambiare frequentemente o valori che devono essere creati da una combinazione di valori di parametro. Aggiungere queste variabili sotto l'elemento padre di variabili che è stato aggiunto al modello.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
          "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

  - Nomi DNS che vengono utilizzati per le interfacce di rete.
    - I nomi di indirizzi IP e prefissi per la rete virtuale e subnet.
    - I nomi e gli identificatori di rete virtuale caricare bilanciamento e interfacce di rete.
    - Impostare i nomi degli account di archiviazione per gli account associati macchine nella scala.
    - Impostazioni per l'estensione di diagnostica installata nel computer virtuale. Per ulteriori informazioni sull'estensione diagnostica, vedere [creare un computer Windows virtuale con il monitoraggio e diagnostica utilizzando il modello di gestione risorse Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Aggiungere la risorsa di account di archiviazione sotto l'elemento padre di risorse che è stato aggiunto al modello. Questo modello utilizza un ciclo per creare gli account di archiviazione cinque consigliati in cui sono archiviati i dischi del sistema operativo e i dati di diagnostica. Questo set di account supportino fino a 100 macchine virtuali in un set di scala, il valore massimo corrente. Ogni account di archiviazione è denominato con un indicatore di lettera definiti in variabili combinate con il prefisso che forniscono nei parametri per il modello.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Aggiungere la risorsa virtuali. Per ulteriori informazioni, vedere [Provider di risorse di rete](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Aggiungere le risorse indirizzo IP pubbliche utilizzati dal servizio di bilanciamento del carico e l'interfaccia di rete.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Aggiungere la risorsa bilanciamento carico utilizzato per il set di scala. Per ulteriori informazioni, vedere [Azure Gestione risorse di supporto per bilanciamento del carico](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Aggiungere la risorsa dell'interfaccia di rete utilizzato dal computer virtuale separato. Poiché macchine in un set di scala non sono accessibile tramite un indirizzo IP pubblico, un computer virtuale separato viene creato nella stessa rete virtuale per l'accesso in remoto i computer.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Aggiungere il computer virtuale separato nella stessa rete set scala.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"        
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
              ]
            }
          }
        },

10. Aggiungere che la macchina virtuale scala imposta la risorsa e specificare l'estensione di diagnostica installata in tutte le macchine virtuali nel set di scala. Molte delle impostazioni per la risorsa sono simili alla risorsa macchina virtuale. Le principali differenze riguardano l'elemento capacità che specifica il numero di macchine virtuali nel set di scala e upgradePolicy che specifica come gli aggiornamenti effettuati in macchine virtuali. Impostare la scala non è stata creata fino a quando non vengono creati tutti gli account di archiviazione come specificato con l'elemento dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "MicrosoftWindowsServer",
                      "offer": "WindowsServer",
                      "sku": "2012-R2-Datacenter",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Aggiungere la risorsa autoscaleSettings che consente di definire come impostare la scala regola in base sull'uso del processore computer nel set di scala.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Per questa esercitazione, sono importanti questi valori:

    - **metricName** - questo valore è uguale il contatore delle prestazioni definiti nella variabile wadperfcounter. Usa la variabile, l'estensione di diagnostica raccoglie le **processore (totale)\% tempo processore** contatore.
    - **metricResourceUri** - questo valore è l'identificatore di risorsa del set di scala macchina virtuale.
    - **timeGrain** : questo valore è granularità dei valori che sono stati raccolti. In questo modello, è impostato per un minuto.
    - **statistica** : questo valore determina il modo in cui vengono combinate le metriche per adattare l'operazione di ridimensionamento automatico. I valori possibili sono: Media, Min, Max. In questo modello, l'utilizzo della CPU totale medio macchine virtuali verrà raccolte.
    - **finestra** – questo valore è l'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore.
    - **aggregazione temporale** – questo valore determina come devono essere combinati i dati raccolti nel tempo. Il valore predefinito è Media. I valori possibili sono: Media, minimo, massimo, all'ultimo, totale, conteggio.
    - l' **operatore** : questo valore è l'operatore viene utilizzato per confrontare i dati metrici e la soglia. I valori possibili sono: è uguale a, NotEquals, maggiore, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **soglia** : questo valore è un valore che attiva l'azione scala. In questo modello, macchine vengono aggiunti al scala impostata quando è l'utilizzo della CPU medio tra macchine nel set di sul 50%.
    - **direzione** : questo valore determina l'azione che viene eseguita quando si ottiene il valore di soglia. I valori possibili sono aumentare o diminuire. In questo modello, il numero di macchine virtuali nel set di scala viene aumentato se la soglia è supera il 50% di intervallo di tempo definito.
    - **tipo** : questo valore è il tipo di azione che dovrebbe essere eseguita e deve essere impostata su ChangeCount.
    - **valore** : questo valore è il numero di macchine virtuali che vengono aggiunti o rimossi dal gruppo scala. Questo valore deve essere maggiore o uguale a 1. Il valore predefinito è 1. In questo modello, il numero di computer nella scala impostazione aumenta 1 quando viene raggiunta la soglia.
    - **raffreddamento** – questo valore è la quantità di tempo di attesa dopo l'ultima operazione di ridimensionamento, prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra un minuto e una settimana.

12. Salvare il file del modello.    

## <a name="step-4-upload-the-template-to-storage"></a>Passaggio 4: Caricare il modello di archiviazione

È possibile caricare il modello, purché si conosce il nome e una chiave primaria dell'account di archiviazione che è stato creato nel passaggio 1.

1.  Nella finestra di Microsoft Azure PowerShell, impostare una variabile che specifica il nome dell'account di archiviazione che è stato creato nel passaggio 1.

            $storageAccountName = "vmstestsa"

2.  Impostare una variabile che specifica la chiave primaria dell'account di archiviazione.

            $storageAccountKey = "<primary-account-key>"

    È possibile ottenere questo tasto facendo clic sull'icona chiave quando si visualizza la risorsa di account di archiviazione nel portale di Azure.

3.  Creare l'oggetto del contesto di account di archiviazione che viene utilizzato per convalidare operazioni con l'account di archiviazione.

            $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

4.  Creare il contenitore per l'archiviazione del modello.

            $containerName = "templates"
            New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob

5.  Caricare il file del modello per il nuovo contenitore.

            $blobName = "VMSSTemplate.json"
            $fileName = "C:\" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx

## <a name="step-5-deploy-the-template"></a>Passaggio 5: Distribuire il modello

Ora che è stato creato il modello, è possibile iniziare la distribuzione di risorse. Utilizzare questo comando per avviare il processo:

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Quando preme INVIO, viene chiesto di fornire i valori per le variabili che è stata assegnata. Specificare questi valori:

    vmName: vmsstestvm1
      vmSSName: vmsstest1
      instanceCount: 5
      adminUserName: vmadmin1
      adminPassword: VMpass1
      resourcePrefix: vmsstest

Richiede circa 15 minuti per tutte le risorse essere distribuito correttamente.

>[AZURE.NOTE] È anche possibile utilizzare capacità del portale per distribuire le risorse. Usare questo collegamento: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"

## <a name="step-6-monitor-resources"></a>Passaggio 6: Risorse Monitor

È possibile ottenere alcune informazioni sui set di scala macchina virtuale utilizzo di questi metodi:

 - Portale Azure - attualmente è possibile ottenere una quantità di informazioni tramite il portale limitata.
 - [Esplora risorse Azure](https://resources.azure.com/) - questo strumento più appropriato per esplorare lo stato corrente del set di scala. Seguire questo percorso e verrà visualizzata la visualizzazione di istanza del set di scala creato:

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure - PowerShell usare questo comando per ottenere informazioni:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

        Or

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - Connettersi a computer virtuale separato come si farebbe un altro computer e quindi è possibile accedere in remoto macchine virtuali in scala impostare per monitorare singoli processi.

>[AZURE.NOTE] Un API REST completo per ottenere informazioni sui set di scala sono disponibili in [Imposta scala macchina virtuale](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Passaggio 7: Rimuovere le risorse

Poiché vengono addebitate per le risorse utilizzate in Azure, è sempre consigliabile eliminare risorse che non sono più necessari. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse e tutte le risorse vengono automaticamente eliminate.

    Remove-AzureRmResourceGroup -Name vmsstestrg1

Se si desidera mantenere il gruppo di risorse, è possibile eliminare la scala imposta solo.

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"

## <a name="next-steps"></a>Passaggi successivi

- Gestire il set di scala appena creata utilizzando le informazioni contenute in [gestire macchine virtuali in un Set di scala macchina virtuale](virtual-machine-scale-sets-windows-manage.md).
- Altre informazioni sul ridimensionamento verticale consultando [autoscale verticale con scala macchina virtuale set](virtual-machine-scale-sets-vertical-scale-reprovision.md)
- Trovare esempi di Azure Monitor in [Azure Monitor PowerShell quick start esempi](../monitoring-and-diagnostics/insights-powershell-samples.md) di funzionalità di monitoraggio
- Informazioni sulle caratteristiche di notifica di [usare le azioni autoscale per inviare posta elettronica e webhook notifiche di avviso nel Monitor Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Informazioni su come [registri di controllo Usa per l'invio di notifiche di avviso di posta elettronica e webhook nel Monitor Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
