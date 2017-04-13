<properties
    pageTitle="Distribuire risorse Azure utilizzando c# | Microsoft Azure"
    description="Informazioni su come utilizzare c# e gestione di risorse di Azure per creare le risorse di Microsoft Azure."
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
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>Distribuire risorse Azure usando C# 

In questo articolo viene illustrato come creare risorse Azure utilizzando c#.

È necessario innanzitutto per assicurarsi di che aver completato le operazioni seguenti:

- Installare [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verificare l'installazione di [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Ottenere un [token di autenticazione](../resource-group-authenticate-service-principal.md)

Bastano circa 30 minuti per eseguire la procedura seguente.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Passaggio 1: Creare un progetto di Visual Studio e installare le raccolte

Pacchetti NuGet rappresentano il modo più semplice per installare le librerie che è necessario per completare questa esercitazione. Per ottenere le relative raccolte che è necessario in Visual Studio, eseguire le operazioni seguenti:

1. Fare clic su **File** > **nuova** > **progetto**.

2. Nei **modelli** > **c#**, selezionare **Applicazione Console**, immettere il nome e il percorso del progetto e quindi fare clic su **OK**.

3. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni e quindi fare clic su **Gestisci pacchetti NuGet**.

4. Tipo, *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library e quindi seguire le istruzioni per installare il pacchetto.

5. Nella parte superiore della pagina, selezionare **Includi definitiva**. Tipo *Microsoft.Azure.Management.Compute* nella casella di ricerca, fare clic su **Installa** per le raccolte di .NET calcolare e quindi seguire le istruzioni per installare il pacchetto.

6. Tipo *Microsoft.Azure.Management.Network* nella casella di ricerca, fare clic su **Installa** per le raccolte di .NET di rete e quindi seguire le istruzioni per installare il pacchetto.

7. Tipo *Microsoft.Azure.Management.Storage* nella casella di ricerca, fare clic su **Installa** per le raccolte di .NET lo spazio di archiviazione e quindi seguire le istruzioni per installare il pacchetto.

8. Digitare *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca, fare clic su **Installa** per le raccolte di gestione delle risorse.

A questo punto si è pronti per iniziare a usare le raccolte per creare l'applicazione.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Passaggio 2: Creare le credenziali utilizzate per autenticare richieste

A questo punto è formattare le informazioni sull'applicazione che è stato creato in credenziali utilizzate per autenticare le richieste di gestione di risorse di Azure.

1. Aprire il file Program.cs per il progetto che è stato creato e quindi aggiungere queste istruzioni nella parte superiore del file using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Per creare il token che è necessario, aggiungere questo metodo alla classe Program:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    Sostituire {id client} con l'identificatore di Azure Active Directory applicazione, {client segreto} con il tasto di scelta dell'applicazione di Active Directory e {tenant id} con l'identificatore tenant per l'abbonamento. È possibile trovare l'id tenant eseguendo Get-AzureRmSubscription. È possibile trovare il tasto di scelta tramite il portale di Azure.

3. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice per il metodo Main nel file Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Salvare il file Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Passaggio 3: Registrare il provider di risorse e creare le risorse

### <a name="register-the-providers-and-create-a-resource-group"></a>Registrare il provider di servizi e creare un gruppo di risorse

Tutte le risorse devono essere contenute in un gruppo di risorse. Prima di aggiungere risorse a un gruppo, è necessario che sia registrato l'abbonamento con il provider di risorse.

1. Aggiungere variabili per il metodo principali della classe di programma per specificare i nomi che si desidera utilizzare per le risorse:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Sostituire tutti i valori delle variabili con i nomi e un identificatore che si desidera utilizzare. È possibile trovare l'identificatore di sottoscrizione eseguendo Get-AzureRmSubscription.

2. Per creare il gruppo di risorse e registrare i provider, aggiungere questo metodo alla classe Program:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice per il metodo principali:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

È necessario un [account di archiviazione](../storage/storage-create-storage-account.md) per archiviare il file disco rigido virtuale creato per la macchina virtuale.

1. Per creare l'account di archiviazione, aggiungere questo metodo alla classe Program:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice al metodo principali della classe Program:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico

Un indirizzo IP pubblico è necessario per comunicare con il computer virtuale.

1. Per creare l'indirizzo IP pubblico della macchina virtuale, aggiungere questo metodo alla classe Program:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice al metodo principale della classe Program:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

In una rete virtuale deve essere una macchina virtuale che viene creata con il modello di distribuzione di Manager delle risorse.

1. Per creare una subnet e una rete virtuale, aggiungere questo metodo alla classe Program:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice al metodo principali della classe Program:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete

Una macchina virtuale deve un'interfaccia di rete per comunicare in rete virtuale.

1. Per creare un'interfaccia di rete, aggiungere questo metodo alla classe Program:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice al metodo principali della classe Program:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>Creare un set di disponibilità

Set di disponibilità semplificano gestire la manutenzione di macchine virtuali utilizzate dall'applicazione.

1. Per creare il set di disponibilità, aggiungere questo metodo alla classe Program:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice al metodo principali della classe Program:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Ora che è stato creato tutte le risorse di supporto, è possibile creare una macchina virtuale.

1. Per creare la macchina virtuale, aggiungere questo metodo alla classe Program:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] Questa esercitazione viene creata una macchina virtuale in esecuzione una versione del sistema operativo Windows Server. Per ulteriori informazioni sulla selezione altre immagini, vedere [Naviga e selezionare macchina virtuale Azure immagini con Windows PowerShell e CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Per chiamare il metodo aggiunto in precedenza, aggiungere il codice al metodo Main:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>Passaggio 4: Eliminare le risorse

Poiché vengono addebitate per le risorse utilizzate in Azure, è sempre consigliabile eliminare risorse che non sono più necessari. Se si desidera eliminare le macchine virtuali e tutte le risorse di supporto, è necessario eseguire è eliminare il gruppo di risorse.

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

2.  Per chiamare il metodo aggiunto in precedenza, aggiungere il codice per il metodo principali:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>Passaggio 5: Eseguire l'applicazione console

1. Per eseguire l'applicazione console, fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

2. Premere **INVIO** dopo ogni codice di stato verrà restituiti a creare ogni risorsa. Dopo aver creata la macchina virtuale, eseguire il passaggio successivo prima di premere INVIO per eliminare tutte le risorse.

    Richiede circa 5 minuti per questa applicazione console al termine dell'esecuzione dall'inizio alla fine. Prima di premere INVIO per avviare l'eliminazione di risorse, si potrebbe richiedere alcuni minuti per verificare la creazione delle risorse nel portale di Azure prima di eliminare.

3. Per visualizzare lo stato delle risorse, visitare il log di controllo nel portale di Azure:

    ![Esplorare i log di controllo nel portale di Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>Passaggi successivi

- Vantaggi offerti da un modello per creare una macchina virtuale in base alle informazioni [Distribuisci una macchina virtuale Azure](virtual-machines-windows-csharp-template.md)utilizzando c# e un modello di Manager delle risorse.
- Informazioni su come gestire la macchina virtuale creata consultando [macchine virtuali di gestione tramite Gestione risorse di Azure e PowerShell](virtual-machines-windows-csharp-manage.md).
