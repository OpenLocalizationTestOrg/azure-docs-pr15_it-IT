<properties
   pageTitle="Script personalizzati in macchine virtuali Linux | Microsoft Azure"
   description="Automatizzare le attività di configurazione Linux VM utilizzando l'estensione di Script personalizzati"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Con l'estensione di Script personalizzati Azure macchine virtuali Linux

L'estensione di Script personalizzati download ed esegue script in macchine virtuali di Azure. Questa estensione è utile per la configurazione di distribuzione post, installazione del software o qualsiasi altra configurazione / attività di gestione. Script possono essere scaricati dal server di archiviazione Azure o altra posizione internet accessibili o forniti all'articolo estensione fase di esecuzione. L'estensione di Script personalizzato si integra con i modelli di gestione di risorse Azure e può essere eseguito anche tramite CLI Azure, PowerShell, portale Azure o l'API REST di Azure macchina virtuale.

Il documento viene illustrato come utilizzare l'estensione di Script personalizzati da CLI Azure e un modello di gestione di risorse Azure e anche dei dettagli di procedure di risoluzione dei problemi su sistemi Linux.

## <a name="extension-configuration"></a>Configurazione dell'estensione

La configurazione dell'estensione di Script personalizzati specifica come percorso di script e il comando da eseguire. Questa configurazione può essere creata in file di configurazione, specificato nella riga di comando, o in un modello di gestione di risorse Azure. Dati riservati possono essere archiviati in una configurazione protetta, che verrà crittografata e solo decrittografare macchina virtuale. Configurazione protetta è utile quando il comando di esecuzione include informazioni riservate, ad esempio una password.

### <a name="public-configuration"></a>Configurazione pubblica

Schema:

- **commandToExecute**: (stringa obbligatoria,) eseguire lo script punto di ingresso
- **fileUris**: (facoltativo, matrice di stringhe) gli URL di file da scaricare.
- **timestamp** (facoltativo, integer) utilizzare questo campo solo per impostare un trigger rieseguire dello script modificando valore del campo.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configurazione protetta

Schema:

- **commandToExecute**: (facoltativo, string) eseguire lo script punto di ingresso. Utilizzare questo campo se il comando contiene informazioni riservate, ad esempio le password.
- **storageAccountName**: (facoltativo, string) il nome dell'account di archiviazione. Se si specificano credenziali lo spazio di archiviazione, fileUris tutti deve essere URL per BLOB Azure.
- **storageAccountKey**: (facoltativo, string) il tasto di scelta di account di archiviazione.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>CLI Azure

Quando si usa CLI Azure per eseguire l'estensione di Script personalizzati, creare un file di configurazione o file contenenti almeno uri file e il comando di esecuzione di script.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Facoltativamente, il comando può essere eseguito tramite il `--public-config` e `--private-config` opzione, che consente la configurazione da specificare durante l'esecuzione e senza un file di configurazione separato.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Esempi CLI Azure

**Esempio 1** - configurazione pubblica con file di script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Esempio 2** : configurazione pubblica con alcun file di script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Esempio 3** - un file di configurazione pubblico viene utilizzata per specificare il file di script URI viene utilizzato un file di configurazione protetta per specificare il comando da eseguire.

File di configurazione pubblico:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

File di configurazione protetta:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Modello di Manager delle risorse

L'estensione di Script personalizzati Azure può essere eseguito in fase di distribuzione macchina virtuale utilizzando un modello di Manager delle risorse. A tale scopo, aggiungere JSON formattato correttamente per il modello di distribuzione.

### <a name="resource-manager-examples"></a>Esempi di Manager delle risorse

**Esempio 1** - configurazione pubblica.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Esempio 2** : comando nella configurazione protetta.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Vedere .net Core musica Store Demo per un esempio di completamento - [Musica Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando viene eseguita l'estensione di Script personalizzati, lo script viene creato o scaricato in una directory simile all'esempio seguente. L'output del comando viene salvata anche in questa directory `stdout` e `stderr` file.

```none
/var/lib/azure/custom-script/download/0/
```

L'estensione di Script Azure produce un file di log, sono disponibili qui.

```none
/var/log/azure/customscript/handler.log
```

Lo stato di esecuzione dell'estensione di Script personalizzati può essere recuperato anche con CLI Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

L'output è simile alla seguente:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre estensioni Script macchine Virtuali, vedere [Panoramica di Azure Script estensione per Linux](./virtual-machines-linux-extensions-features.md).