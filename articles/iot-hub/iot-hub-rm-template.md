<properties
    pageTitle="Creare un IoT Hub utilizzando un modello ARM e c# | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a utilizzare i modelli di gestione di risorse Azure per creare un IoT Hub con un programma c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Creare un hub IoT tramite un programma c# con un modello di gestione risorse di Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile utilizzare Gestione risorse di Azure per creare e gestire gli hub di Azure IoT a livello di programmazione. In questa esercitazione viene illustrato come utilizzare un modello di gestione di risorse Azure per creare un hub IoT da un programma c#.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse di Azure e classica](../resource-manager-deployment-model.md).  In questo articolo è descritta utilizzando il modello di distribuzione di Manager delle risorse di Azure.

Per completare questa esercitazione, è necessario quanto segue:

- Microsoft Visual Studio 2015.
- Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.
- Un [account di archiviazione di Azure] [ lnk-storage-account] nel punto in cui è possibile archiviare i file di modello Manager delle risorse di Azure.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] o versione successiva.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparare il progetto di Visual Studio

1. In Visual Studio, creare un progetto di Visual c# Windows utilizzando il modello di progetto **Applicazione Console** . Nome del progetto **CreateIoTHub**.

2. In Esplora risorse, pulsante destro del mouse sul progetto e quindi fare clic su **Gestisci pacchetti NuGet**.

3. In Gestione Nuget pacchetti verificare **incluso preliminare**e cercare **Microsoft.Azure.Management.ResourceManager**. Fare clic su **Installa**, le modifiche apportate **Revisione** fare clic su **OK**, quindi fare clic sui **accetto** per accettare le licenze.

4. In Gestione Nuget pacchetti cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Fare clic su **Installa**, le modifiche apportate **Revisione** fare clic su **OK**, quindi fare clic sui **accetto** per accettare la licenza.

5. In Program.cs, sostituire le istruzioni esistenti **utilizzando** le opzioni seguenti:

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. In Program.cs, aggiungere le variabili statiche seguente sostituendo i valori di segnaposto. Nota **dell'ID applicazione**, **SubscriptionId**, **TenantId**e **la Password** è stato in precedenza in questa esercitazione. **Nome dell'account di archiviazione di Azure** è il nome dell'account di archiviazione di Azure in cui si archiviano i file di modello Manager delle risorse di Azure. **Nome gruppo di risorse** è il nome del gruppo di risorse che si utilizza quando si crea IoT Hub, può essere un gruppo di risorse esistenti o uno nuovo. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Invio di un modello di gestione di risorse Azure per creare un hub IoT

Usare un file di modello e un parametro JSON per creare un hub IoT nel gruppo di risorse. È anche possibile usare un modello di gestione di risorse Azure per apportare modifiche a un hub IoT esistente.

1. In Esplora risorse, pulsante destro del mouse sul progetto, fare clic su **Aggiungi**e quindi fare clic su **Nuovo elemento**. Aggiungere un file JSON denominato **template.json** al progetto.

2. Sostituire il contenuto di **template.json** con la definizione di risorse seguenti per aggiungere un hub IoT standard all'area **Orientale degli Stati Uniti** (per l'elenco corrente delle aree geografiche che supportano IoT Hub vedere [Lo stato di Azure][lnk-status]):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. In Esplora risorse, pulsante destro del mouse sul progetto, fare clic su **Aggiungi**e quindi fare clic su **Nuovo elemento**. Aggiungere un file JSON denominato **parameters.json** al progetto.

4. Sostituire il contenuto di **parameters.json** con le seguenti informazioni sui parametri imposta ad esempio un nome per il nuovo hub IoT **{le proprie iniziali} mynewiothub**. Si noti che il nome dell'hub IoT deve essere univoco in modo che deve includere il nome o le iniziali):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. In **Esplora Server**, connettersi al proprio abbonamento Azure e account Azure lo spazio di archiviazione di creare un contenitore di **modelli**. Nel pannello **proprietà** , impostare le autorizzazioni di **accesso in lettura pubblico** per il contenitore di **modelli** su **Blob**.

6. In **Esplora Server**, pulsante destro del mouse sul contenitore **modelli** e quindi fare clic su **Visualizzazione Blob contenitore**. Fare clic sul pulsante **Carica Blob** , selezionare le due file, **parameters.json** e **templates.json**e quindi fare clic su **Apri** per caricare i file JSON al contenitore di **modelli** . Gli URL delle BLOB contenente i dati JSON sono:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Aggiungere il metodo seguente Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Aggiungere il codice seguente al metodo **CreateIoTHub** per inviare i file di modello e un parametro a Gestione risorse Azure:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Aggiungere il metodo **CreateIoTHub** che consente di visualizzare lo stato e i tasti per il nuovo hub IoT il codice seguente:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Completare ed eseguire l'applicazione

È ora possibile completare l'applicazione tramite il metodo **CreateIoTHub** prima di generare ed eseguirlo.

1. Aggiungere il codice seguente alla fine del metodo **principali** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Fare clic su **creare** e quindi **compilare soluzione**. Correggere gli errori.

3. Fare clic su **Debug** e quindi **Avviare il debug** per eseguire l'applicazione. Possono richiedere alcuni minuti per la distribuzione per l'esecuzione.

4. È possibile verificare che l'applicazione aggiunto il nuovo hub IoT visitando il [portale di Azure] [ lnk-azure-portal] e visualizzazione dell'elenco delle risorse o utilizzando il cmdlet di PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] In questo esempio di applicazione aggiunge un S1 Standard IoT Hub per il quale vengono addebitate. È possibile eliminare l'hub IoT tramite il [portale di Azure] [ lnk-azure-portal] oppure utilizzare il cmdlet di PowerShell **Rimuovi AzureRmResource** al termine.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT utilizzando un modello di gestione di risorse Azure con un programma c#, è consigliabile esaminare ulteriormente:

- Informazioni sulle funzionalità del [provider di risorse IoT Hub API REST][lnk-rest-api].
- Panoramica di [Gestione risorse di Azure] [ lnk-azure-rm-overview] per ulteriori informazioni sulle funzionalità di gestione di risorse di Azure.

Per ulteriori informazioni sullo sviluppo per IoT Hub, vedere gli articoli seguenti:

- [Introduzione a C SDK][lnk-c-sdk]
- [Hub IoT SDK][lnk-sdks]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
