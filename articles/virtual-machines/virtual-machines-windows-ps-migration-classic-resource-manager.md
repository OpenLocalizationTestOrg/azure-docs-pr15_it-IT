<properties
    pageTitle="Eseguire la migrazione di Manager delle risorse con PowerShell | Microsoft Azure"
    description="Questo articolo sono illustrati tramite la migrazione delle risorse IaaS piattaforma supportata da classico per gestione di risorse di Azure utilizzando i comandi di PowerShell di Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Eseguire la migrazione di risorse IaaS da classico per gestione di risorse di Azure tramite PowerShell Azure

Questa procedura viene illustrato come utilizzare i comandi di Azure PowerShell per eseguire la migrazione dell'infrastruttura come una risorsa di servizio (IaaS) dal modello di distribuzione classico per il modello di distribuzione di Manager delle risorse di Azure. 

Se si desidera, è possibile anche eseguire la migrazione risorse utilizzando l' [interfaccia riga di comando Azure Azure CLI ()](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Per informazioni generali su scenari di migrazione supportati, vedere informazioni sulla [migrazione piattaforma supportata delle risorse IaaS da classico per gestione di risorse di Azure](virtual-machines-windows-migration-classic-resource-manager.md). 
- Per istruzioni dettagliate e una procedura dettagliata della migrazione, vedere [approfondimento tecnico su piattaforma supportata la migrazione da classico per gestione di risorse di Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## <a name="step-1-plan-for-migration"></a>Passaggio 1: Pianificare la migrazione

Ecco alcune procedure che è consigliabile durante la valutazione della migrazione IaaS delle risorse da classica di Manager delle risorse:

- Leggere [le configurazioni e caratteristiche supportate e non supportate](virtual-machines-windows-migration-classic-resource-manager.md). Se si dispone di macchine virtuali che utilizzano configurazioni non supportate o funzionalità, è consigliabile attendere configurazione/caratteristiche supportate da presentare. In alternativa, se adatto alle proprie esigenze, rimuovere tale funzionalità o uscire da tale configurazione per consentire la migrazione.
-   Se si dispone di automatizzato script per distribuire l'infrastruttura e applicazioni oggi, provare a creare una configurazione di test simili usando gli script per la migrazione. In alternativa, è possibile impostare ambienti di esempio tramite il portale di Azure.

>[AZURE.IMPORTANT]Gateway di rete virtuale (a siti, Azure ExpressRoute, gateway di applicazioni, punto al sito) non sono attualmente supportati per la migrazione da classico a Manager delle risorse. Per eseguire la migrazione di una rete virtuale classica con un gateway, rimuovere il gateway prima di eseguire un'operazione di Commit per spostare la rete (è possibile eseguire il passaggio Prepara senza eliminare il gateway). Dopo aver completato la migrazione, riconnettere il gateway di gestione di risorse di Azure.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Passaggio 2: Installare la versione più recente di PowerShell di Azure

Sono disponibili due opzioni principali per installare Azure PowerShell: [PowerShell raccolta](https://www.powershellgallery.com/profiles/azure-sdk/) o [Installazione guidata piattaforma Web (WebPI)](http://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. Raccolta di PowerShell di ricezione degli aggiornamenti in modo continuo. In questo articolo si basa su Azure PowerShell versione 2.1.0.

Per istruzioni sull'installazione, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Passaggio 3: Configurare l'abbonamento e iscriversi per la migrazione

Innanzitutto, avviare un prompt dei comandi PowerShell. Per la migrazione, è necessario configurare l'ambiente per entrambi classica e Manager delle risorse.

Accedere al proprio account per il modello di Manager delle risorse.

```powershell
    Login-AzureRmAccount
```

Ottenere abbonamenti disponibili tramite il comando seguente:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Impostare l'abbonamento Azure per la sessione corrente. In questo esempio viene impostato sul nome dell'abbonamento predefinito **Iscrizione Azure**. Sostituire il nome di sottoscrizione di esempio con uno personalizzato. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] La registrazione è un unico passaggio, ma è necessario eseguire una volta prima di eseguire la migrazione. Senza eseguire la registrazione, viene visualizzato il messaggio di errore seguente: 

>   *BadRequest: Sottoscrizione non è registrato per la migrazione.* 

Registrare con il provider di risorse di migrazione utilizzando il comando seguente:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Attendere cinque minuti per la registrazione completare. È possibile controllare lo stato di approvazione utilizzando il comando seguente:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verificare che sia RegistrationState `Registered` prima di procedere. 

Accedere al proprio account per il modello classico.

```powershell
    Add-AzureAccount
```

Ottenere le sottoscrizioni disponibili tramite il comando seguente:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Impostare l'abbonamento Azure per la sessione corrente. In questo esempio imposta l'abbonamento predefinito su **Azure iscrizione**. Sostituire il nome di sottoscrizione di esempio con uno personalizzato. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passaggio 4: Verificare che sia sufficiente core macchina virtuale di Azure Manager delle risorse nell'area Azure della distribuzione corrente o VNET

È possibile utilizzare il seguente comando di PowerShell per controllare il numero di core che nel gestore di risorse di Azure. Per ulteriori informazioni sugli obiettivi di base, vedere [limiti e la gestione di risorse di Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

In questo esempio controlla la disponibilità nell'area **Occidentale degli Stati Uniti** . Sostituire il nome dell'area di esempio con il proprio. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Passaggio 5: Eseguire comandi per eseguire la migrazione delle risorse IaaS

>[AZURE.NOTE] Tutte le operazioni descritte di seguito sono idempotenti. Se si dispone di un problema diverso da una caratteristica non supportata o un errore di configurazione, è consigliabile riprovare Prepara, interrompere o eseguire il commit operazione. La piattaforma tenta l'azione.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Eseguire la migrazione di macchine virtuali in un servizio cloud (non in una rete virtuale)

Ottenere l'elenco dei servizi cloud usando il comando seguente e quindi selezionare il servizio cloud che si desidera eseguire la migrazione. Se si trovano in una rete virtuale macchine virtuali nel servizio cloud o hanno web o lavoro ruoli, il comando restituisce un messaggio di errore.

```powershell
    Get-AzureService | ft Servicename
```

È possibile ottenere il nome di distribuzione per il servizio cloud. In questo esempio, il nome del servizio è **Servizio**. Sostituire il nome del servizio di esempio con il proprio nome di servizio. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Preparare le macchine virtuali nel servizio cloud per la migrazione. Sono disponibili due opzioni tra cui scegliere.

* **Opzione 1. Eseguire la migrazione di macchine virtuali a una rete virtuale creata piattaforma**

    Prima di tutto, verificare se è possibile eseguire la migrazione di servizio cloud usando i comandi seguenti:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Comando Visualizza gli avvisi e gli errori che impediscono la migrazione. Se la convalida ha esito positivo, è possibile spostare in con il passaggio **Prepara** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Opzione 2. Eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Manager delle risorse**

    In questo esempio imposta il nome del gruppo di risorse **myResourceGroup**, il nome di rete virtuale per **myVirtualNetwork** e il nome della subnet da **mySubNet**. Sostituire i nomi nell'esempio con i nomi dei propri risorse.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Prima di tutto, verificare se è possibile eseguire la migrazione di servizio cloud usando il comando seguente:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Comando Visualizza gli avvisi e gli errori che impediscono la migrazione. Se la convalida ha esito positivo, è possibile procedere con il passaggio Prepara seguente:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Dopo l'operazione di preparazione stabilito con una delle opzioni precedenti, eseguire una query lo stato della migrazione di macchine virtuali. Assicurarsi che siano nel `Prepared` stato.

In questo esempio imposta il nome di macchine Virtuali a **myVM**. Sostituire il nome di esempio con il proprio nome macchina virtuale.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Verificare la configurazione per le risorse preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si desidera tornare allo stato precedente, utilizzare il comando seguente:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se la configurazione preparata è soddisfacente, è possibile spostarsi in avanti ed eseguire il commit le risorse usando il comando seguente:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Eseguire la migrazione di macchine virtuali in una rete virtuale

Per eseguire la migrazione macchine virtuali in una rete virtuale, eseguire la migrazione della rete. Macchine virtuali automaticamente la migrazione di rete. Selezionare la rete virtuale che si desidera eseguire la migrazione. 

In questo esempio imposta il nome di rete virtuale per **myVnet**. Sostituire il nome di rete virtuale di esempio con uno personalizzato. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Se la rete virtuale contiene web o ruoli di lavoro o macchine virtuali con configurazioni non supportate, viene visualizzato un messaggio di errore di convalida.

Prima di tutto, verificare se è possibile eseguire la migrazione di rete virtuale utilizzando il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Comando Visualizza gli avvisi e gli errori che impediscono la migrazione. Se la convalida ha esito positivo, è possibile procedere con il passaggio Prepara seguente:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verificare la configurazione per le macchine virtuali preparate tramite PowerShell Azure o il portale di Azure. Se non si è pronti per la migrazione e si desidera tornare allo stato precedente, utilizzare il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se la configurazione preparata è soddisfacente, è possibile spostarsi in avanti ed eseguire il commit le risorse usando il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Eseguire la migrazione di un account di archiviazione

Dopo aver completato la migrazione di macchine virtuali, è consigliabile eseguire la migrazione gli account di archiviazione.

Preparare ogni account di archiviazione per la migrazione utilizzando il comando seguente. In questo esempio, il nome dell'account di archiviazione è **myStorageAccount**. Sostituire il nome di esempio con il nome dell'account di archiviazione. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Verificare la configurazione dell'account di archiviazione preparato tramite PowerShell Azure o il portale di Azure. Se non si è pronti per la migrazione e si desidera tornare allo stato precedente, utilizzare il comando seguente:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Se la configurazione preparata è soddisfacente, è possibile spostarsi in avanti ed eseguire il commit le risorse usando il comando seguente:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla migrazione, vedere informazioni sulla [migrazione piattaforma supportata delle risorse IaaS da classico per gestione di risorse di Azure](virtual-machines-windows-migration-classic-resource-manager.md).
- Per eseguire la migrazione di altre risorse di rete per Gestione risorse tramite PowerShell di Azure, usare una procedura simile con [AzureNetworkSecurityGroup Sposta](https://msdn.microsoft.com/library/mt786729.aspx), [Sposta AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)e [AzureRouteTable Sposta](https://msdn.microsoft.com/library/mt786718.aspx).
- Per gli script Apri origine che è possibile utilizzare per eseguire la migrazione di Azure risorse da classica di Manager delle risorse, vedere [Strumenti di Community per la migrazione a Gestione risorse di Azure migrazione](virtual-machines-windows-migration-scripts.md)
