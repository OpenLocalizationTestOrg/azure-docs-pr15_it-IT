<properties
    pageTitle="Esportare il modello di gestione di risorse Azure | Microsoft Azure"
    description="Utilizzare Gestione risorse Azure per esportare un modello da un gruppo di risorse esistenti."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="tomfitz"/>

# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Esportare un modello di gestione di risorse Azure da risorse esistenti

Manager delle risorse consente di esportare un modello di Manager delle risorse sulle risorse esistenti nell'abbonamento. Per informazioni sulla sintassi modello o per automatizzare la ridistribuzione della soluzione in base alle esigenze, è possibile utilizzare il modello generato.

È importante tenere presente che esistono diversi modi per esportare un modello:

- È possibile esportare il modello effettivo utilizzato per una distribuzione. Il modello esportato include tutti i parametri e variabili esattamente come appaiono nel modello originale. Questo approccio è utile per la distribuzione di risorse tramite il portale. A questo punto, desiderata per informazioni su come creare il modello per creare le risorse.
- È possibile esportare un modello che rappresenta lo stato corrente del gruppo di risorse. Il modello esportato non è basato su qualsiasi modello usato per la distribuzione. Viene creato un modello di uno snapshot del gruppo di risorse. Numero di valori hardcoded e potrebbe non essere tanti parametri in genere è possibile definire il modello esportato. Questo approccio è utile quando è stato modificato il gruppo di risorse tramite il portale oppure script. A questo punto, è necessario acquisire il gruppo di risorse come modello.

In questo argomento viene entrambi gli approcci.

In questa esercitazione, accedere al portale di Azure, creare un account di archiviazione ed esportare il modello per l'account di archiviazione. Aggiungere una rete virtuale per modificare il gruppo di risorse. Infine, si esporta un nuovo modello che rappresenta lo stato corrente. Anche se in questo articolo è incentrata su un'infrastruttura semplificata, è possibile utilizzare la stessa procedura per esportare un modello per una soluzione più complicata.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Nel [portale di Azure](https://portal.azure.com), selezionare **Nuovo** > **lo spazio di archiviazione** > **account di archiviazione**.

      ![creare lo spazio di archiviazione](./media/resource-manager-export-template/create-storage.png)

2. Creare un account di archiviazione con il nome **dello spazio di archiviazione**, le proprie iniziali e la data. Il nome dell'account di archiviazione deve essere univoco in Azure. Se il nome è già in uso, viene visualizzato un messaggio di errore che indica che il nome è in uso. Provare una variante. Per il gruppo di risorse, creare un nuovo gruppo di risorse e denominarla **ExportGroup**. È possibile utilizzare i valori predefiniti per le altre proprietà. Selezionare **Crea**.

      ![fornire i valori per lo spazio di archiviazione](./media/resource-manager-export-template/provide-storage-values.png)

La distribuzione potrebbe richiedere qualche minuto. Al termine del processo di distribuzione, l'abbonamento contiene l'account di archiviazione.

## <a name="view-a-template-from-deployment-history"></a>Visualizzare un modello dalla cronologia di distribuzione

1. Passare a e il gruppo delle risorse per il nuovo gruppo di risorse. Si noti che e il Mostra il risultato dell'ultima distribuzione. Selezionare il collegamento.

      ![blade gruppo risorse](./media/resource-manager-export-template/resource-group-blade.png)

2. Viene visualizzato una cronologia delle distribuzioni per il gruppo. Nel caso, e l'Elenca probabilmente sola distribuzione. Selezionare la distribuzione.

     ![ultima distribuzione](./media/resource-manager-export-template/last-deployment.png)

3. E il riepilogo della distribuzione. Riepilogo include lo stato di distribuzione e le operazioni e i valori immessi per i parametri. Per visualizzare il modello è stato utilizzato per la distribuzione, selezionare **modello di visualizzazione**.

     ![visualizzare il riepilogo di distribuzione](./media/resource-manager-export-template/deployment-summary.png)

4. Manager delle risorse recupera i file elencati di seguito è:

   1. **Modello** - il modello che definisce l'infrastruttura per la soluzione. Una volta creato l'account di archiviazione tramite il portale, Manager delle risorse utilizzato un modello per la distribuzione e salvato il modello per riferimento futuro.
   2. **Parametri** - un file di parametri che è possibile utilizzare per passare valori durante la distribuzione. Contiene i valori forniti durante la distribuzione prima, ma è possibile modificare uno di questi valori quando si ridistribuisce il modello.
   3. **CLI** - Azure un comando riga interfaccia (CLI) file di script è possibile utilizzare per distribuire il modello.
   4. **PowerShell** - file di script di PowerShell di Azure che è possibile utilizzare per distribuire il modello.
   5. **.NET** - classe A .NET che è possibile utilizzare per distribuire il modello.
   6. **Trascrizione** - classe A trascrizione che è possibile utilizzare per distribuire il modello.

     I file sono disponibili tramite i collegamenti tra e il. Per impostazione predefinita, e il Visualizza il modello.

       ![modello di visualizzazione](./media/resource-manager-export-template/view-template.png)

     Di seguito prestare particolare attenzione al modello. Il modello è simile alla seguente:

        {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parametri": {"nome": {"tipo": "Stringa"}, "accountType": {"tipo": "Stringa"}, "posizione": {"tipo": "Stringa"}, "encryptionEnabled": {"defaultValue": false, "tipo": "Bool"}}, "risorse": [{"tipo": "Microsoft.Storage/storageAccounts", "sku": {"nome": "[parameters('accountType')]"}, "tipo": "Lo spazio di archiviazione", "nome": "[parameters('name')]", "apiVersion": "2016 01 / 01", "posizione": "[parameters('location')]", "proprietà": {"crittografia": {"servizi": {"blob": {"abilitato": "[parameters('encryptionEnabled')]"}}, "keySource": "Microsoft.Storage"}}}]}
 
Questo modello è utilizzato per creare l'account di archiviazione effettivo. Si noti che contiene i parametri che consentono di distribuire diversi tipi di account di archiviazione. Per ulteriori informazioni sulla struttura di un modello, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md). Per l'elenco completo delle funzioni che è possibile usare in un modello, vedere [gestione di risorse di Azure modello funzioni](resource-group-template-functions.md).


## <a name="add-a-virtual-network"></a>Aggiungere una rete virtuale

Il modello scaricato nella sezione precedente rappresentato l'infrastruttura di tale distribuzione originale. Tuttavia, verrà non tenuto in considerazione le modifiche apportate dopo la distribuzione.
Per illustrare il problema, modificare di seguito il gruppo di risorse mediante l'aggiunta di una rete virtuale tramite il portale.

1. Nella finestra e gruppo delle risorse, selezionare **Aggiungi**.

      ![aggiunta di risorse](./media/resource-manager-export-template/add-resource.png)

2. Selezionare **rete virtuale** le risorse disponibili.

      ![Selezionare rete virtuale](./media/resource-manager-export-template/select-vnet.png)

2. Assegnare un nome alla rete aziendale virtuale **VNET**e usare i valori predefiniti per le altre proprietà. Selezionare **Crea**.

      ![Imposta avviso per](./media/resource-manager-export-template/create-vnet.png)

3. Dopo la rete virtuale è distribuito correttamente al gruppo di risorse, osservare nuovamente la cronologia di distribuzione. È ora possibile visualizzare due distribuzioni. Se non viene visualizzata la seconda distribuzione, è necessario chiudere blade il gruppo delle risorse e alla riapertura del documento. Selezionare la distribuzione più recente.

      ![cronologia di distribuzione](./media/resource-manager-export-template/deployment-history.png)

4. Consente di visualizzare il modello per la distribuzione. Si noti che definisca solo la rete virtuale. Non include l'account di archiviazione che è distribuito in precedenza. Non è un modello che rappresenta tutte le risorse nel gruppo di risorse.

## <a name="export-the-template-from-resource-group"></a>Esportare il modello dal gruppo di risorse

Per ottenere lo stato corrente del gruppo di risorse, esportare un modello che mostra un'istantanea del gruppo di risorse.  

> [AZURE.NOTE] Non è possibile esportare un modello per un gruppo di risorse che contiene più di 200 risorse.

1. Per visualizzare il modello per un gruppo di risorse, selezionare **script di automazione**.

      ![gruppo di risorse di esportazione](./media/resource-manager-export-template/export-resource-group.png)

     Non tutti i tipi di risorse supportano la funzione di modello di esportazione. Se il gruppo di risorse contiene solo l'account di archiviazione e di rete virtuale illustrate in questo articolo, non è presente un errore. Tuttavia, se sono stati creati altri tipi di risorse, venga visualizzato un messaggio di errore che indica la presenza di un problema con l'esportazione. Si imparerà a gestire i problemi nella sezione [Correggi i problemi di esportazione](#fix-export-issues) .

      

2. Viene visualizzato nuovamente sei file che è possibile utilizzare per ridistribuire la soluzione, ma questa volta il modello è leggermente diverso. Questo modello include solo due parametri: uno per il nome dell'account di archiviazione e uno per il nome di rete virtuale.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Manager delle risorse non recuperata i modelli utilizzati durante la distribuzione. Se, tuttavia, generato un nuovo modello è in base alla configurazione corrente delle risorse. Ad esempio, il modello imposta il valore di posizione e replica account lo spazio di archiviazione:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Sono disponibili due opzioni per continuare a lavorare con questo modello. È possibile scaricare il modello e lavorare su di esso localmente con un editor di JSON. In alternativa, è possibile salvare il modello a una raccolta e lavorare su di esso tramite il portale.

     Se si ha familiarità con un editor di JSON come [Codice Visual Studio](resource-manager-vs-code.md) o [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), è preferibile scaricare il modello localmente e utilizzare tale editor. Se non è impostato con un editor di JSON, può essere preferibile la modifica del modello tramite il portale. Il resto di questo argomento presuppone che avere salvato il modello a una raccolta nel portale. Tuttavia, si apportare le modifiche nella sintassi della stessa al modello se si lavora in locale con un editor di JSON o tramite il portale.

     Per usare in locale, fare clic **su Download**.

      ![modello di download](./media/resource-manager-export-template/download-template.png)

     Per usare il portale, selezionare **Aggiungi alla raccolta**.

      ![aggiungere alla raccolta](./media/resource-manager-export-template/add-to-library.png)

     Quando si aggiunge un modello alla raccolta, assegnare al modello un nome e una descrizione. Scegliere **Salva**.

     ![impostare i valori di modello](./media/resource-manager-export-template/set-template-values.png)

4. Per visualizzare un modello salvato nella raccolta, selezionare **altri servizi**, digitare **modelli** per filtrare i risultati, selezionare **modelli**.

      ![ricerca di modelli](./media/resource-manager-export-template/find-templates.png)

5. Selezionare il modello con il nome che è stato salvato.

      ![Selezionare modello](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Personalizzare il modello

Il modello esportato funziona correttamente se si desidera creare la stessa account di archiviazione e rete virtuale per ogni distribuzione. Tuttavia, Gestione risorse saranno disponibili opzioni in modo che è possibile distribuire i modelli con maggiore flessibilità. Ad esempio, durante la distribuzione, è consigliabile specificare il tipo di account di archiviazione per creare o i valori da utilizzare per la rete virtuale indirizzo prefisso e un prefisso subnet.

In questa sezione, aggiungere parametri per il modello esportato in modo che è possibile riutilizzare il modello quando si distribuiscono queste risorse in altri ambienti. È inoltre possibile aggiungere alcune caratteristiche per il modello per ridurre la probabilità che si verifichi un errore quando si distribuisce il modello di. Non è di indovinare di un nome univoco per il proprio account di archiviazione. Se, tuttavia, il modello consente di creare un nome univoco. Limitare i valori specificati per il tipo di account di archiviazione a solo le opzioni valide.

1. Selezionare **Modifica** per personalizzare il modello.

     ![Mostra modello](./media/resource-manager-export-template/show-template.png)

1. Selezionare il modello.

     ![Modifica modello](./media/resource-manager-export-template/edit-template.png)

1. Per passare i valori che si desidera specificare durante la distribuzione, sostituire la sezione **parametri** con nuove definizioni di parametro. Osservare i valori di **allowedValues** per **storageAccount_accountType**. Se si accidentalmente fornisce un valore non valido, che è riconosciuto prima dell'avvio della distribuzione. Si noti inoltre che si desidera fornire solo un prefisso per il nome dell'account di archiviazione e il prefisso è limitato a 11 caratteri. Quando si riduce il prefisso a 11 caratteri, assicurarsi che il nome completo non superi il numero massimo di caratteri per un account di archiviazione. Il prefisso consente di applicare una convenzione di denominazione per gli account di archiviazione. Verrà visualizzata come creare un nome univoco per il passaggio successivo.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. La sezione **variabili** del modello è attualmente vuota. Nella sezione **variabili** è creare valori che semplificano la sintassi per il resto del modello. Sostituire in questa sezione con una definizione della nuova variabile. La variabile **storageAccount_name** concatena il prefisso dal parametro in una stringa univoco viene generato in base all'identificatore del gruppo di risorse. Non è indovinare un nome univoco quando viene fornito un valore di parametro.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Per usare i parametri e variabili nelle definizioni delle risorse, sostituire la sezione **risorse** con nuove definizioni di risorse. Si noti che sono leggermente cambiata definizioni di risorse diverso da quello che sono state assegnate le proprietà delle risorse. Le proprietà sono come le proprietà del modello esportato. Si siano assegnando semplicemente proprietà a valori di parametro anziché valori hard-coded. La posizione delle risorse è impostata per usare nella stessa posizione come gruppo di risorse tramite l'espressione **resourceGroup () .location** . La variabile creato per il nome dell'account di archiviazione viene fatto riferimento tramite l'espressione di **variabili** .

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

4. Fare clic su **OK** dopo aver terminato la modifica del modello.

5. Selezionare **Salva** per salvare le modifiche al modello.

     ![Salva modello](./media/resource-manager-export-template/save-template.png)

6. Per distribuire il modello aggiornato, selezionare **Distribuisci**.

     ![distribuire modello](./media/resource-manager-export-template/deploy-template.png)

7. Selezionare un nuovo gruppo di risorse per distribuire le risorse per fornire i valori di parametro.

## <a name="update-the-downloaded-parameters-file"></a>Aggiornare il file scaricato parametri

Se si lavora con i file scaricati (piuttosto che la raccolta portale), è necessario aggiornare il file scaricato parametro. E non corrisponde più i parametri nel modello scelto. Non è necessario usare un file di parametri, ma è possibile semplificare il processo quando si ridistribuisce un ambiente. Utilizzare i valori predefiniti che sono definiti nel modello per molti dei parametri in modo che il file di parametro deve solo due valori.

Sostituire il contenuto del file parameters.json con:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Il file di parametro aggiornato fornisce valori solo per i parametri che non dispongono di un valore predefinito. Quando si desidera un valore diverso da quello predefinito, è possibile fornire i valori per gli altri parametri.

## <a name="fix-export-issues"></a>Risolvere i problemi di esportazione

Non tutti i tipi di risorse supportano la funzione di modello di esportazione. Manager delle risorse in particolare non vengono esportati alcuni tipi di risorsa per impedire l'esposizione dati riservati. Ad esempio, se si dispone di una stringa di connessione nel file di configurazione del sito, probabilmente non desiderato in modo esplicito visualizzarlo in un modello esportato. È possibile risolvere il problema aggiungendo manualmente le risorse mancante nel modello.

> [AZURE.NOTE] Solo si verifichino problemi di esportazione durante l'esportazione da un gruppo di risorse anziché dalla cronologia distribuzione. Se l'ultima distribuzione rappresenti in modo accurato lo stato corrente del gruppo di risorse, è consigliabile esportare il modello dalla cronologia distribuzione anziché dal gruppo di risorse. Esportare solo da un gruppo di risorse quando sono state apportate modifiche al gruppo di risorse che non sono definiti in un unico modello.

Ad esempio, se si esporta un modello per un gruppo di risorse che contiene un'app web, Database SQL e stringa di connessione nel file di configurazione del sito, è visualizzato il messaggio seguente:

![Mostra errore](./media/resource-manager-export-template/show-error.png)

Selezionare il messaggio viene esattamente quali tipi di risorse non sono stati esportati. 
     
![Mostra errore](./media/resource-manager-export-template/show-error-details.png)

In questo argomento viene risoluzioni comuni.

### <a name="connection-string"></a>Stringa di connessione

Nella risorsa siti web, aggiungere una definizione per la stringa di connessione al database:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Estensione del sito Web

Aggiungere una definizione per il codice per l'installazione delle risorse del sito web:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Estensione macchina virtuale

Per esempi delle estensioni macchina virtuale, vedere [Esempi di configurazione estensione macchine Virtuali Windows Azure](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### <a name="virtual-network-gateway"></a>Gateway di rete virtuale

Aggiungere un tipo di risorsa virtuali gateway.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Gateway di rete locale

Aggiungere un tipo di risorsa gateway di rete locale.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Connessione

Aggiungere un tipo di risorsa di connessione.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! Sono state fornite come esportare un modello dalle risorse che è stato creato nel portale.

- È possibile distribuire un modello tramite [PowerShell](resource-group-template-deploy.md), [CLI Azure](resource-group-template-deploy-cli.md)o [API REST](resource-group-template-deploy-rest.md).
- Per informazioni su come esportare un modello tramite PowerShell, vedere [Uso Azure PowerShell Gestione risorse Azure](powershell-azure-resource-manager.md).
- Per informazioni su come esportare un modello tramite CLI Azure, vedere [usare CLI Azure per Mac, Linux e Windows Azure Gestione risorse di](xplat-cli-azure-resource-manager.md).
