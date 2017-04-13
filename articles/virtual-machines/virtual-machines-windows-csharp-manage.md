<properties
    pageTitle="Gestire macchine virtuali mediante Gestione risorse di Azure e c# | Microsoft Azure"
    description="Gestire macchine virtuali mediante Gestione risorse di Azure e c#."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Gestire macchine virtuali di Azure tramite Gestione risorse di Azure e C#  

Le attività in questo articolo viene illustrato come gestire macchine virtuali, ad esempio avvio, arresto e l'aggiornamento. Una macchina virtuale deve essere presente in un gruppo di risorse per completare le attività in questo articolo.

Per completare le attività in questo articolo, è necessario:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un token di autenticazione](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>Creare un progetto di Visual Studio e installare pacchetti

Pacchetti NuGet modi più semplice per installare le librerie che è necessario completare le attività in questo articolo. Le raccolte in cui si installa per questo articolo sono Azure Active Directory Authentication Library e calcolare libreria Provider di risorse. Completare la procedura seguente per ottenere le relative raccolte in Visual Studio:

1. Fare clic su **File** > **nuova** > **progetto**.

2. Nei **modelli** > **c#**, selezionare **Applicazione Console**, immettere il nome e il percorso del progetto e quindi fare clic su **OK**.

3. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni e quindi fare clic su **Gestisci pacchetti NuGet**.

4. Tipo, *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library e quindi seguire le istruzioni per installare il pacchetto.

5. Nella parte superiore della pagina, selezionare **Includi definitiva**. Tipo *Microsoft.Azure.Management.Compute* nella casella di ricerca, fare clic su **Installa** per le raccolte di .NET calcolare e quindi seguire le istruzioni per installare il pacchetto.

A questo punto si è pronti per iniziare a usare le raccolte per gestire le macchine virtuali.

## <a name="set-up-the-project"></a>Impostare il progetto

Ora che l'applicazione viene creata e le librerie vengono installate, si crea un token utilizzando le informazioni sull'applicazione. Questo token viene utilizzato per autenticare le richieste di gestione di risorse di Azure.

1. Aprire il file Program.cs per il progetto che è stato creato e quindi aggiungere queste istruzioni nella parte superiore del file using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Aggiungere variabili per il metodo principali della classe programma per specificare il nome del gruppo di risorse e il nome del computer virtuale e l'identificatore di sottoscrizione:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";

    È possibile trovare l'identificatore di sottoscrizione eseguendo Get-AzureRmSubscription.
    
3. Per ottenere il token necessarie per creare le credenziali, aggiungere questo metodo per la classe Program:

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
    
4. Per creare le credenziali, aggiungere il codice per il metodo principale in Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Salvare il file Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Visualizzare informazioni su una macchina virtuale

1. Aggiungere questo metodo alla classe Program nel progetto creato in precedenza:

        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
          
        }

2. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Salvare il file Program.cs.

4. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

    Quando si esegue questo metodo, verrà visualizzato un elemento come in questo esempio:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Interrompere una macchina virtuale

È possibile interrompere una macchina virtuale in due modi. È possibile interrompere una macchina virtuale e mantenere tutte le relative impostazioni, ma continuare a verrà addebitato o è possibile interrompere una macchina virtuale e renderla nuovamente disponibile. Quando una macchina virtuale operazione, tutte le risorse associate sono anche le estremità DEALLOCATE e di fatturazione per renderla.

1. Commento codice precedentemente aggiunto al metodo Main, tranne il codice per ottenere le credenziali.

2. Aggiungere questo metodo alla classe Program:

        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }

    Se si desidera rilasciare la macchina virtuale, modificare la chiamata spegnimento per questo codice:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

    Verrà visualizzato lo stato della modifica macchina virtuale su arrestato. Se è stato eseguito il metodo che chiamante Deallocate, lo stato è interrotto (rilasciato).

## <a name="start-a-virtual-machine"></a>Avviare una macchina virtuale

1. Commento codice precedentemente aggiunto al metodo Main, tranne il codice per ottenere le credenziali.

2. Aggiungere questo metodo alla classe Program:

        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }

3. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

    Verrà visualizzato lo stato della macchina virtuale modificare in esecuzione.

## <a name="restart-a-running-virtual-machine"></a>Riavviare una macchina virtuale attiva

1. Commento codice precedentemente aggiunto al metodo Main, tranne il codice per ottenere le credenziali.

2. Aggiungere questo metodo alla classe Program:

        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }

3. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

## <a name="resize-a-virtual-machine"></a>Ridimensionare una macchina virtuale

In questo esempio viene illustrato come modificare le dimensioni di una macchina virtuale in esecuzione.

1. Commento codice precedentemente aggiunto al metodo Main, tranne il codice per ottenere le credenziali.

2. Aggiungere questo metodo alla classe Program:

        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Per chiamare il metodo appena aggiunto, aggiungere il codice al metodo Main:

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

    Verrà visualizzata la dimensione della modifica macchina virtuale a Standard_A1.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Aggiungere un disco dati a una macchina virtuale

In questo esempio viene illustrato come aggiungere un disco dati a una macchina virtuale in esecuzione.

1. Commento codice precedentemente aggiunto al metodo Main, tranne il codice per ottenere le credenziali.

2. Aggiungere questo metodo alla classe Program:

        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

## <a name="delete-a-virtual-machine"></a>Eliminare una macchina virtuale

1. Commento codice precedentemente aggiunto al metodo Main, tranne il codice per ottenere le credenziali.

2. Aggiungere questo metodo alla classe Program:

        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }

3. Per chiamare il metodo appena aggiunto, aggiungere il codice per il metodo principali:

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Salvare il file Program.cs.

5. Fare clic su **Start** in Visual Studio e quindi eseguire l'accesso a Azure Active Directory utilizzando gli stessi nome utente e password utilizzate con l'abbonamento.

## <a name="next-steps"></a>Passaggi successivi

Se si sono verificati problemi con una distribuzione, potrebbe osservare [le distribuzioni di risoluzione dei problemi delle risorse gruppo il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
