<properties
    pageTitle="Creare un IoT Hub tramite l'API REST | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare l'API REST per creare un IoT Hub."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Esercitazione: Creare un hub IoT in un programma c# e le API REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile utilizzare il [provider di risorse IoT Hub API REST] [ lnk-rest-api] per creare e gestire gli hub di Azure IoT a livello di programmazione. In questa esercitazione viene illustrato come utilizzare il provider di risorse IoT Hub API REST per creare un hub IoT da un programma c#.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse di Azure e classica](../resource-manager-deployment-model.md).  In questo articolo è descritta utilizzando il modello di distribuzione di Manager delle risorse di Azure.

Per completare questa esercitazione, è necessario quanto segue:

- Microsoft Visual Studio 2015.
- Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] o versione successiva.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparare il progetto di Visual Studio

1. In Visual Studio, creare un progetto di Visual c# Windows utilizzando il modello di progetto **Applicazione Console** . Nome del progetto **CreateIoTHubREST**.

2. In Esplora risorse, pulsante destro del mouse sul progetto e quindi fare clic su **Gestisci pacchetti NuGet**.

3. In Gestione Nuget pacchetti verificare **incluso preliminare**e cercare **Microsoft.Azure.Management.ResourceManager**. Fare clic su **Installa**, le modifiche apportate **Revisione** fare clic su **OK**, quindi fare clic sui **accetto** per accettare le licenze.

4. In Gestione Nuget pacchetti cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Fare clic su **Installa**, le modifiche apportate **Revisione** fare clic su **OK**, quindi fare clic sui **accetto** per accettare la licenza.

6. In Program.cs, sostituire le istruzioni esistenti **utilizzando** le opzioni seguenti:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. In Program.cs, aggiungere le variabili statiche seguente sostituendo i valori di segnaposto. Nota **dell'ID applicazione**, **SubscriptionId**, **TenantId**e **la Password** è stato in precedenza in questa esercitazione. **Nome gruppo di risorse** è il nome del gruppo di risorse che si utilizza quando si crea hub IoT, può essere un gruppo di risorse esistenti o uno nuovo. **IoT Hub nome** è il nome dell'IoT Hub si crea, ad esempio **MyIoTHub** (questo nome deve essere univoco globale, in modo che deve includere il nome o le iniziali). **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Consente di creare un hub IoT API REST

Utilizzare l' [API REST Hub IoT] [ lnk-rest-api] per creare un hub IoT nel gruppo di risorse. È anche possibile utilizzare API REST per apportare modifiche a un hub IoT esistente.

1. Aggiungere il metodo seguente Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Aggiungere il codice seguente al metodo **CreateIoTHub** per creare un oggetto **HttpClient** con il token di autenticazione nelle intestazioni di:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Aggiungere il codice seguente al metodo **CreateIoTHub** per descrivere hub IoT per creare e generare una rappresentazione in formato JSON (per l'elenco corrente delle aree che supportano l'IoT Hub vedere [Lo stato di Azure][lnk-status]):

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Aggiungere il codice seguente al metodo **CreateIoTHub** per inviare la richiesta di resto in Azure, controllare la risposta e recuperare l'URL è possibile utilizzare per controllare lo stato dell'attività di distribuzione:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Aggiungere il codice seguente alla fine del metodo **CreateIoTHub** per usare l'indirizzo **asyncStatusUri** recuperato nel passaggio precedente in attesa per la distribuzione completare:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Aggiungere il codice seguente alla fine del metodo **CreateIoTHub** per recuperare le chiavi dell'hub IoT creati dall'utente e stamparle alla console:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Completare ed eseguire l'applicazione

È ora possibile completare l'applicazione tramite il metodo **CreateIoTHub** prima di generare ed eseguirlo.

1. Aggiungere il codice seguente alla fine del metodo **principali** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Fare clic su **creare** e quindi **compilare soluzione**. Correggere gli errori.

3. Fare clic su **Debug** e quindi **Avviare il debug** per eseguire l'applicazione. Possono richiedere alcuni minuti per la distribuzione per l'esecuzione.

4. È possibile verificare che l'applicazione aggiunto il nuovo hub IoT visitando il [portale di Azure] [ lnk-azure-portal] e visualizzazione dell'elenco delle risorse o utilizzando il cmdlet di PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] In questo esempio di applicazione aggiunge un S1 Standard IoT Hub per il quale vengono addebitate. Al termine, è possibile eliminare l'hub IoT tramite il [portale di Azure] [ lnk-azure-portal] oppure utilizzare il cmdlet di PowerShell **Rimuovi AzureRmResource** al termine.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT tramite l'API REST, può essere necessario esplorare ulteriormente:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
