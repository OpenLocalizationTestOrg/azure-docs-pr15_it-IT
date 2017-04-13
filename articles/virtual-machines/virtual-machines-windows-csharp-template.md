<properties
    pageTitle="Distribuire una macchina virtuale utilizzando c# e un modello di gestione risorse | Microsoft Azure"
    description="Informazioni su come utilizzare c# e un modello di Manager delle risorse per distribuire una macchina virtuale Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Distribuire una macchina virtuale Azure utilizzando c# e un modello di Manager delle risorse

Utilizzando i modelli e i gruppi di risorse, è in grado di gestire tutte le risorse insieme che supportano l'applicazione. In questo articolo viene illustrato come utilizzare Visual Studio e c# per configurare l'autenticazione, creare un modello e quindi distribuirle Azure utilizzando il modello è stato creato.

È necessario innanzitutto per assicurarsi che avrebbe potuto questi passaggi di configurazione:

- Installare [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verificare l'installazione di [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Ottenere un [token di autenticazione](../resource-group-authenticate-service-principal.md)
- Creare un gruppo di risorse con [PowerShell di Azure](../resource-group-template-deploy.md), [CLI Azure](../resource-group-template-deploy-cli.md)o [portale Azure](../resource-group-template-deploy-portal.md).

Bastano circa 30 minuti per eseguire la procedura seguente.
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Passaggio 1: Creare il progetto di Visual Studio, il file del modello e il file di parametri

### <a name="create-the-template-file"></a>Creare il file del modello

Un modello di gestione di risorse Azure consente di distribuire e gestire le risorse Azure insieme. Il modello è una descrizione JSON le risorse e i parametri di distribuzione associato.

In Visual Studio, eseguire le operazioni seguenti:

1. Fare clic su **File** > **nuova** > **progetto**.

2. Nei **modelli** > **c#**, selezionare **Applicazione Console**, immettere il nome e il percorso del progetto e quindi fare clic su **OK**.

3. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni fare clic su **Aggiungi** > **Nuovo elemento**.

4. Fare clic su Web, selezionare il File JSON, immettere *VirtualMachineTemplate.json* per il nome e quindi fare clic su **Aggiungi**.

5. Apertura e chiusura tra parentesi quadre del file VirtualMachineTemplate.json, aggiungere l'elemento dello schema obbligatori e l'elemento contentVersion necessari:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Parametri](../resource-group-authoring-templates.md#parameters) non sono sempre necessari, ma consentono di valori di input quando si distribuisce il modello. Aggiungere l'elemento parametri e gli elementi figlio dopo l'elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [Variabili](../resource-group-authoring-templates.md#variables) possono essere utilizzate in un modello per specificare i valori possono cambiare frequentemente o valori che devono essere creati da una combinazione di valori di parametro. Aggiungere l'elemento variabili dopo la sezione parametri:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. [Risorse](../resource-group-authoring-templates.md#resources) , ad esempio la macchina virtuale, la rete virtuale con l'account di archiviazione sono definiti successivo nel modello. Aggiungere la sezione di risorse dopo la sezione variabili:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. Salvare il file del modello creato.

### <a name="create-the-parameters-file"></a>Creare il file di parametri

Per specificare i valori per i parametri di risorse che sono stati definiti nel modello, si crea un file di parametri che contiene i valori vengono utilizzati quando si distribuisce il modello. In Visual Studio, eseguire le operazioni seguenti:

1. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni fare clic su **Aggiungi** > **Nuovo elemento**.

2. Fare clic su Web, selezionare il File JSON, immettere *Parameters.json* per il nome e quindi fare clic su **Aggiungi**.

3. Aprire il file parameters.json e quindi aggiungere il contenuto JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] In questo articolo viene creata una macchina virtuale in esecuzione una versione del sistema operativo Windows Server. Per ulteriori informazioni sulla selezione altre immagini, vedere [Naviga e selezionare macchina virtuale Azure immagini con Windows PowerShell e CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Salvare il file di parametri che è stato creato.

## <a name="step-2-install-the-libraries"></a>Passaggio 2: Installare le raccolte

Pacchetti NuGet rappresentano il modo più semplice per installare le librerie che è necessario per completare questa esercitazione. È necessario la raccolta di gestione delle risorse di Azure e Azure Active Directory Authentication Library per creare le risorse. Per ottenere le raccolte in Visual Studio, eseguire le operazioni seguenti:

1. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni, fare clic su **Gestisci pacchetti NuGet**e quindi fare clic su Sfoglia.

2. Tipo, *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library e quindi seguire le istruzioni per installare il pacchetto.

4. Nella parte superiore della pagina, selezionare **Includi definitiva**. Tipo *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca, fare clic su **Installa** per le raccolte di gestione delle risorse di Microsoft Azure e quindi seguire le istruzioni per installare il pacchetto.

A questo punto si è pronti per iniziare a usare le raccolte per creare l'applicazione.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Passaggio 3: Creare le credenziali utilizzati per eseguire l'autenticazione richieste

L'applicazione di Azure Active Directory viene creata ed è installata la raccolta di autenticazione. Ora è formattare le informazioni sull'applicazione in credenziali utilizzate per autenticare le richieste di Manager delle risorse Azure.

1. Aprire il file Program.cs per il progetto che è stato creato e quindi aggiungere queste istruzioni nella parte superiore del file using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  Aggiungere questo metodo per la classe di programma per ottenere il token necessari per creare le credenziali:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    Sostituire {id client} con l'identificatore di Azure Active Directory applicazione, {client segreto} con il tasto di scelta dell'applicazione di Active Directory e {tenant id} con l'identificatore tenant per l'abbonamento. È possibile trovare l'id tenant eseguendo Get-AzureRmSubscription. È possibile trovare il tasto di scelta tramite il portale di Azure.

3. Per creare le credenziali, aggiungere il codice per il metodo Main nel file Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Salvare il file Program.cs.

## <a name="step-4-deploy-the-template"></a>Passaggio 4: Distribuire il modello

In questo passaggio si utilizza il gruppo di risorse creato in precedenza, ma è anche possibile creare un gruppo di risorse mediante [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e le classi [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) .

1. Aggiungere variabili per il metodo principali della classe di programma per specificare i nomi delle risorse creato in precedenza, il nome di distribuzione e l'identificatore di sottoscrizione:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Sostituire il valore di nomegruppo con il nome del gruppo di risorse. Sostituire il valore di deploymentName con il nome che si desidera utilizzare per la distribuzione. È possibile trovare l'identificatore di sottoscrizione eseguendo Get-AzureRmSubscription.

2. Aggiungere questo metodo per la classe di programma per distribuire le risorse al gruppo di risorse utilizzando il modello definito:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Se si desidera distribuire il modello da un account di archiviazione, è possibile sostituire la proprietà del modello con la proprietà TemplateLink.

3. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Passaggio 5: Eliminare le risorse

Poiché vengono addebitate per le risorse utilizzate in Azure, è sempre consigliabile eliminare risorse che non sono più necessari. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. Eliminare il gruppo di risorse e tutte le risorse vengono automaticamente eliminate.

1.  Per eliminare il gruppo di risorse, aggiungere questo metodo alla classe Program:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>Passaggio 6: Eseguire l'applicazione console

1.  Per eseguire l'applicazione console, fare clic su **Start** in Visual Studio, effettuare l'accesso a Azure Active Directory utilizzando le stesse credenziali utilizzate con l'abbonamento.

2.  Una volta visualizzato lo stato accettato, premere **INVIO** .

    Richiede circa 5 minuti per questa applicazione console al termine dell'esecuzione dall'inizio alla fine. Prima di premere INVIO per avviare l'eliminazione di risorse, si potrebbe richiedere alcuni minuti per verificare la creazione delle risorse nel portale di Azure prima di eliminare.

3. Per visualizzare lo stato delle risorse, visitare il log di controllo nel portale di Azure:

    ![Esplorare i log di controllo nel portale di Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, è un passaggio successivo per esaminare [le distribuzioni di risoluzione dei problemi delle risorse gruppo il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Informazioni su come gestire la macchina virtuale creata consultando [macchine virtuali di gestione tramite Gestione risorse di Azure e PowerShell](virtual-machines-windows-csharp-manage.md).
