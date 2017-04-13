<properties
   pageTitle="Bene accolta configurazione dello stato delle risorse Manager modello | Microsoft Azure"
   description="Definizione di modello Manager delle risorse per bene accolta configurazione dello stato in Azure con esempi e risoluzione dei problemi"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS e lo si desidera configurazione dello stato con i modelli di Manager delle risorse di Azure
In questo articolo viene descritto il modello di Manager delle risorse per il [gestore di estensione bene accolta configurazione dello stato](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Esempio di modello per una macchina virtuale di Windows

Il frammento di codice seguente entra nella sezione delle risorse del modello.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Esempio di modello per VMSS di Windows

Un nodo VMSS ha una sezione "proprietà" con la "VirtualMachineProfile", "extensionProfile" attributo. DSC viene aggiunto in "estensioni". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
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

## <a name="detailed-settings-information"></a>Informazioni sulle impostazioni dettagliate

Schema riportato di seguito è per la parte di impostazioni dell'estensione DSC Azure in un modello di gestione di risorse Azure.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Dettagli
| Nome della proprietà | Tipo | Descrizione |
| --- | --- | --- |
| settings.wmfVersion | stringa | Specifica la versione di Windows Management Framework che deve essere installato su di una macchina virtuale. Impostare questa proprietà su 'ultima' Installa la versione più aggiornata di WMF. I valori possibili correnti solo per questa proprietà sono **'4.0', '5.0', ' 5.0PP' e 'ultima'**. Questi valori possibili sono soggetti a aggiornamenti. Il valore predefinito è 'ultimo.|
| Settings.Configuration.URL | stringa | Specifica il percorso URL da cui scaricare il file zip di configurazione DSC. Se l'URL indicato richiede un token SA per l'accesso, è necessario impostare la proprietà protectedSettings.configurationUrlSasToken il valore del token SA. Questa proprietà è necessaria se settings.configuration.script e/o settings.configuration.function sono definiti. |
| Settings.Configuration.script | stringa | Specifica il nome del file di script che contiene la definizione della configurazione DSC. Questo script deve essere nella cartella radice del file zip scaricato dall'URL specificato dalla proprietà configuration.url. Questa proprietà è necessaria se settings.configuration.url e/o settings.configuration.script sono definiti. |
| Settings.Configuration.Function | stringa | Specifica il nome della configurazione DSC. La configurazione denominata deve contenere lo script definito da configuration.script. Questa proprietà è necessaria se settings.configuration.url e/o settings.configuration.function sono definiti. |
| settings.configurationArguments | Raccolta | Definisce i parametri che si desidera passare alla configurazione DSC. Questa proprietà non è crittografata. |
| settings.configurationData.url | stringa | Specifica l'URL da cui si desidera scaricare il file di dati (.pds1) configurazione da utilizzare come input per la configurazione di DSC. Se l'URL indicato richiede un token SA per l'accesso, è necessario impostare la proprietà protectedSettings.configurationDataUrlSasToken il valore del token SA.|
| settings.privacy.dataEnabled | stringa | Attiva o disattiva l'insieme di telemetria. I valori possibili solo per questa proprietà sono **'Attiva', 'Disattiva', ', o $null**. Uscire da questa proprietà vuoti o null consente di telemetria. Il valore predefinito è '. [Ulteriori informazioni](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Raccolta | Definisce una posizione da cui eseguire il download di file WMF alternativa. [Ulteriori informazioni](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Raccolta | Definisce i parametri che si desidera passare alla configurazione DSC. Questa proprietà è crittografata. |
| protectedSettings.configurationUrlSasToken | stringa | Specifica il token SA per accedere all'URL definito da configuration.url. Questa proprietà è crittografata. |
| protectedSettings.configurationDataUrlSasToken | stringa | Specifica il token SA per accedere all'URL definito da configurationData.url. Questa proprietà è crittografata. |

## <a name="settings-vs-protectedsettings"></a>Impostazioni e ProtectedSettings
Tutte le impostazioni vengono salvate in un file di testo impostazioni nella macchina virtuale.
Proprietà in 'impostazioni' sono proprietà pubbliche poiché non è crittografati nel file di testo impostazioni.
Proprietà in 'protectedSettings' vengono crittografate con un certificato e non vengono visualizzate in formato testo normale nel file nella macchina virtuale.

Se la configurazione richiede le credenziali, possono essere inclusi in protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Esempio

Nell'esempio seguente deriva dalla sezione "Guida introduttiva" della [pagina Panoramica di gestore estensione DSC](virtual-machines-windows-extensions-dsc-overview.md).
In questo esempio utilizza i modelli di Manager delle risorse anziché i cmdlet per distribuire l'estensione. Salvare la configurazione di "IisInstall.ps1", inserirlo in una. File ZIP e caricare il file in un URL accessibile. Questo esempio viene usata archiviazione blob Azure, ma è possibile scaricare. COMPRIMERE i file da un punto qualsiasi.

Nel modello di gestione risorse di Azure, il codice seguente indica la macchina virtuale per scaricare il file corretto ed eseguire la funzione di PowerShell appropriata:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Aggiornamento dal formato precedente
Le impostazioni nel formato precedente (contenente le proprietà pubbliche ModulesUrl, ConfigurationFunction, SasToken o proprietà) automaticamente adattare nel formato corrente ed eseguire esattamente come prima.

Schema riportato di seguito è quali precedente schema delle impostazioni di simile:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Ecco come formato precedente si adatta al formato corrente:

| Nome della proprietà | Equivalente di Schema precedente |
| --- | --- |
| settings.wmfVersion | Impostazioni. WMFVersion |
| Settings.Configuration.URL | Impostazioni. ModulesUrl |
| Settings.Configuration.script | Prima parte delle impostazioni. ConfigurationFunction (prima '\\\\") |
| Settings.Configuration.Function | Seconda parte delle impostazioni. ConfigurationFunction (dopo '\\\\") |
| settings.configurationArguments | Impostazioni. Proprietà |
| settings.configurationData.url | protectedSettings.DataBlobUri (senza token SA) |
| settings.privacy.dataEnabled | Impostazioni. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | Impostazioni. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | Impostazioni. SasToken |
| protectedSettings.configurationDataUrlSasToken | Token sa da protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Risoluzione dei problemi - codice di errore 1100
Codice di errore 1100 indica che si verifica un problema con l'input dell'utente all'articolo estensione DSC.
Il testo di questi errori è variabile e può cambiare.
Ecco alcuni degli errori che possono verificarsi e come è possibile correggerli.

### <a name="invalid-values"></a>Valori non validi
"È Privacy.dataCollection '{0}'. Sono gli unici valori possibili ', 'Attiva' e 'Disattiva' ""WmfVersion è '{0}'. Solo i valori possibili sono... e 'ultima' "

Problema: Non è un valore specificato.

Soluzione: Modificare il valore non valido per un valore valido. Vedere la tabella nella sezione dei dettagli.

### <a name="invalid-url"></a>URL non valido
"È ConfigurationData.url '{0}'. Non si tratta di un URL valido""DataBlobUri è '{0}'. Non si tratta di un URL valido""Configuration.url è '{0}'. Non si tratta di un URL valido"

Problema: Ha fornito che URL non è valido.

Soluzione: Controllare tutti gli URL specificati. Assicurarsi che tutti gli URL risolvono a percorsi validi accessibile l'estensione del computer remoto.

### <a name="invalid-configurationargument-type"></a>Tipo ConfigurationArgument non valido
"Digitare configurationArguments non valido" {0}

Problema: La proprietà ConfigurationArguments non riesce a risolvere a un oggetto Hashtable. 

Soluzione: Verificare la proprietà ConfigurationArguments Hashtable. Seguire il formato specificato nell'esempio precedente. Prestare particolare attenzione ai offerte, virgole e parentesi graffe.

### <a name="duplicate-configurationarguments"></a>Duplicare ConfigurationArguments
"Trovata duplicati argomenti '{0}' in configurationArguments pubblici e protetti"

Problema: ConfigurationArguments nelle impostazioni di pubbliche e ConfigurationArguments in impostazioni protette contengono proprietà con lo stesso nome.

Soluzione: Rimuovere le proprietà duplicati.

### <a name="missing-properties"></a>Proprietà mancanti
"Configuration.function richiede che non viene specificato configuration.url o configuration.module"

"Configuration.url richiede che tale configuration.script viene specificato"

"Configuration.script richiede che tale configuration.url viene specificato"

"Configuration.url richiede che tale configuration.function viene specificato"

"ConfigurationUrlSasToken richiede che tale configuration.url viene specificato"

"ConfigurationDataUrlSasToken richiede che tale configurationData.url viene specificato"

Problema: Una proprietà definita deve un'altra proprietà che è presente.

Soluzioni: 
- Specificare le proprietà mancante.
- Rimuovere la proprietà che deve essere proprietà mancante.


## <a name="next-steps"></a>Passaggi successivi
Informazioni sui set di scala DSC e macchina virtuale in [Uso Imposta scala macchina virtuale con l'estensione DSC Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Trovare ulteriori informazioni sulla [gestione protetta delle credenziali del DSC](virtual-machines-windows-extensions-dsc-credentials.md). 

Per ulteriori informazioni sul gestore estensione DSC Azure, vedere [Introduzione al gestore estensione configurazione dello stato bene accolta Azure](virtual-machines-windows-extensions-dsc-overview.md). 

Per ulteriori informazioni su DSC PowerShell, [visitare l'area documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
