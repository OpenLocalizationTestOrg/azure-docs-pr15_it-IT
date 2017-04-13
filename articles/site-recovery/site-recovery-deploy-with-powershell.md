<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM utilizzando il ripristino del sito di Azure e PowerShell | Microsoft Azure"
    description="Informazioni su come automatizzare la replica di macchine virtuali di Hyper-V in cloud VMM utilizzando il ripristino del sito e PowerShell."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Replicare macchine virtuali di Hyper-V in cloud VMM Azure tramite Powershell - classico

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Manager delle risorse](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portale classica](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classico](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Panoramica

Il ripristino del sito Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali in un numero di scenari di distribuzione. Per un elenco completo di scenari di distribuzione, vedere [Panoramica il ripristino del sito di Azure](site-recovery-overview.md).

In questo articolo viene illustrato come usare PowerShell per automatizzare attività da eseguire quando si configura il ripristino del sito di Azure replicare macchine virtuali di Hyper-V in aree System Center VMM allo spazio di archiviazione Azure.

L'articolo include i prerequisiti per lo scenario e viene illustrato come configurare un archivio il ripristino del sito, installare il Provider di ripristino del sito di Azure nel server VMM di origine, registrare il server nell'archivio di, aggiungere un account di archiviazione Azure, installare l'agente di servizi di ripristino di Windows Azure nel server host Hyper-V, configurare le impostazioni di protezione per cloud VMM applicati a tutte le macchine virtuali protette e quindi attivare la protezione per tali macchine virtuali. Completare il processo verificando failover per assicurarsi che tutto funzioni come previsto.

Se si verificano problemi durante l'impostazione questo scenario, pubblicare domande nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse e classica](../resource-manager-deployment-model.md). In questo articolo è descritta utilizzando il modello di distribuzione classica.



## <a name="before-you-start"></a>Prima di iniziare

Verificare di che avere inserito i prerequisiti di:

### <a name="azure-prerequisites"></a>Prerequisiti Azure

- È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- È necessario un account di archiviazione Azure per archiviare i dati replicati. Attivazione della replica geografico è necessario che l'account. Dovrebbe essere nella stessa regione come archivio il ripristino del sito di Azure ed essere associato all'abbonamento stesso. [Altre informazioni sull'archiviazione Azure](../storage/storage-introduction.md).
- È necessario assicurarsi che macchine virtuali che si desidera proteggere rispettino [Prerequisiti Azure macchina virtuale](site-recovery-best-practices.md#virtual-machines).

### <a name="vmm-prerequisites"></a>Prerequisiti VMM
- È necessario server VMM in System Center 2012 R2.
- È necessario almeno un cloud sul server VMM che si desidera proteggere. Deve contenere nel cloud:
    - Uno o più gruppi host VMM.
    - Uno o più server host Hyper-V o cluster di ogni gruppo host.
    - Uno o più macchine virtuali nel server di origine Hyper-V.

### <a name="hyper-v-prerequisites"></a>Prerequisiti di Hyper-V

- I server host Hyper-V devono essere in esecuzione almeno **Windows Server 2012** con ruolo Hyper-V o **Microsoft Hyper-V Server 2012** e sono stati installati gli aggiornamenti più recenti.
- Se si esegue Hyper-V in una nota cluster tale gestore cluster non viene creati automaticamente se si dispone di un cluster basato su indirizzo IP statico. È necessario configurare manualmente il gestore di cluster. A tale scopo, in Server Manager > Gestione Cluster di Failover, connettersi al cluster, fare clic su **Configura ruolo** e scegliere **Gestore di Replica Hyper-V** nella schermata **Selezionare ruolo** della creazione guidata disponibilità.
- Tutti i server host Hyper-V e cluster per il quale si desidera gestire protezione deve essere incluso in un'area VMM.

### <a name="network-mapping-prerequisites"></a>Prerequisiti di mapping di rete
Quando si proteggere macchine virtuali nelle mappe di rete Azure mapping tra le reti macchine Virtuali nel server VMM di origine e destinazione Azure reti per abilitare le operazioni seguenti:

- Tutti i computer che non sopra nella stessa rete possono connettersi tra loro, indipendentemente dalla quale piano di ripristino si trovano in.
- Se un gateway di rete è impostata sulla destinazione della rete Azure, macchine virtuali di connettersi a altre macchine virtuali locale.
- Se non Configura rete mapping solo macchine virtuali che non tramite lo stesso piano di ripristino saranno in grado di connettersi tra loro dopo il controllo a Azure.

Se si desidera distribuire connessione di rete è necessario le operazioni seguenti:

- Macchine virtuali che si desidera proteggere nel server VMM di origine deve essere connesso a una rete macchine Virtuali. La rete deve essere collegata a una rete logica associato nel cloud.
- Una rete Azure a cui è possibile connettersi replicate macchine virtuali dopo il failover. Selezionare la rete in fase di failover. La rete dovrebbe essere nella stessa regione come abbonamento il ripristino del sito di Azure.
- [Altre informazioni](site-recovery-network-mapping.md) sulla connessione di rete:

###<a name="powershell-prerequisites"></a>Prerequisiti di PowerShell
Assicurarsi che sia pronto per l'invio di Azure PowerShell. Se si utilizza già PowerShell, sarà necessario eseguire l'aggiornamento alla versione 0.8.10 o versione successiva. Per informazioni sull'impostazione di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Dopo aver impostato e configurato PowerShell, è possibile visualizzare tutti i cmdlet disponibili per il servizio [di seguito](https://msdn.microsoft.com/library/dn850420.aspx).

Per informazioni sui suggerimenti che consentono di utilizzare i cmdlet, ad esempio come output di input e i valori dei parametri vengono in genere gestiti in Azure PowerShell, vedere [Iniziare a utilizzare i cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Passaggio 1: Configurare l'abbonamento

In PowerShell, eseguire i cmdlet:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Sostituire gli elementi all'interno di "< >" con le informazioni specifiche.

## <a name="step-2-create-a-site-recovery-vault"></a>Passaggio 2: Creare un archivio il ripristino del sito

In PowerShell, sostituire gli elementi all'interno di "< >" con le informazioni specifiche ed eseguire questi comandi:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Passaggio 3: Generare una chiave di registrazione archivio

Generare una chiave di registrazione nell'archivio di. Dopo aver scaricato il Provider di ripristino del sito di Azure e installarlo nel server VMM, è necessario utilizzare questo tasto per registrare il server VMM nell'archivio di.

1.  Ottenere il file di archivio impostazione e impostare il contesto:

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Impostare il contesto di archivio eseguendo i comandi seguenti:

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Passaggio 4: Installare il Provider di ripristino del sito Azure

1.  Nel computer VMM, creare una directory eseguendo il comando seguente:

    ```

    pushd C:\ASR\

    ```

2.  Estrarre i file mediante il provider scaricato eseguendo il seguente comando

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Installare il provider di utilizzare i comandi seguenti:

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Attendere il completamento dell'installazione.

4.  Registrare il server nell'archivio di utilizzando il comando seguente:

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Passaggio 5: Creare un account di archiviazione Azure

Se non si dispone di un account di archiviazione Azure, creare un account di attivazione della replica geografico eseguendo il comando seguente:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Si noti che l'account di archiviazione deve essere nella stessa regione servizio il ripristino del sito di Azure ed essere associato all'abbonamento stesso.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Passaggio 6: Installare l'agente di servizi di recupero Azure

Dal portale di Azure, installare l'agente di servizi di ripristino di Windows Azure ogni server host Hyper-V nella cloud VMM che si desidera proteggere.

Eseguire il comando seguente in tutti gli host VMM:

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Passaggio 7: Configurare cloud impostazioni di protezione

1.  Creare un profilo di protezione cloud in Azure eseguendo il comando seguente:

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Ottenere un contenitore di protezione eseguendo i comandi seguenti:

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Avviare l'associazione del contenitore di protezione con il cloud:

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Al termine di processo, eseguire il comando seguente:


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Al termine dell'elaborazione il processo, eseguire il comando seguente:


        Do
        {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

        if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)



Per verificare il completamento dell'operazione, seguire la procedura descritta in [Monitorare l'attività](#monitor).

## <a name="step-8-configure-network-mapping"></a>Passaggio 8: Configurare il mapping di rete
Prima di iniziare il mapping di rete verificare che macchine virtuali sul server VMM origine connessi a una rete macchine Virtuali. Inoltre, creare uno o più reti virtuali Azure. Si noti che è possono eseguire il mapping di più reti macchine Virtuali a un'unica rete Azure.

Si noti che, se la rete di destinazione ha più subnet e una di queste subnet ha lo stesso nome subnet in cui si trova la macchina virtuale di origine e quindi la macchina virtuale duplicata verrà connesso a tale subnet destinazione dopo il failover. Se non è una subnet di destinazione con un nome corrispondente, la macchina virtuale verrà connesso prima subnet nella rete.

Il primo comando Ottiene server per l'archivio il ripristino del sito di Azure corrente. Il comando Archivia i server il ripristino del sito di Microsoft Azure nella variabile di matrice $Servers.

    $Servers = Get-AzureSiteRecoveryServer


Il secondo comando Ottiene la rete di ripristino del sito per il primo server nella matrice $Servers. Il comando Archivia le reti nella variabile $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

Il comando terzo Ottiene i propri abbonamenti Azure utilizzando il cmdlet Get-AzureSubscription e il valore viene archiviato nella variabile $Subscriptions.

    $Subscriptions = Get-AzureSubscription



Il comando quarto Ottiene Azure reti virtuali utilizzando il cmdlet Get-AzureVNetSite e tale valore nella variabile $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



Il cmdlet finale consente di creare un mapping tra la rete principale e la macchina virtuale Azure. Il cmdlet specifica la rete primaria come il primo elemento $Networks. Il cmdlet specifica una rete macchina virtuale come il primo elemento $AzureVmNetworks utilizzando il relativo ID. Il comando include l'ID di abbonamento Azure.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Passaggio 9: Attivare la protezione per macchine virtuali

Dopo aver server, nuvole e reti sono configurate correttamente, è possibile abilitare la protezione per macchine virtuali nel cloud. Tenere presente quanto segue:

Macchine virtuali deve soddisfare [i prerequisiti di Azure macchina virtuale](site-recovery-best-practices.md#virtual-machines).

Per abilitare la protezione del sistema operativo e il sistema operativo disco devono essere impostate per la macchina virtuale. Quando si crea una macchina virtuale in VMM tramite un modello di macchina virtuale è possibile impostare la proprietà. È anche possibile impostare queste proprietà per macchine virtuali esistenti nella scheda **Generale** e **Configurazione Hardware** delle proprietà macchina virtuale. Se non si imposta queste proprietà in VMM sarà possibile come configurarli nel portale del ripristino del sito di Azure.



1.  Per abilitare la protezione, eseguire il seguente comando per ottenere il contenitore di protezione:

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Ottenere l'entità di protezione (macchine Virtuali) per eseguire il comando seguente:


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Abilitare DR per la macchina virtuale eseguendo il comando seguente:



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Testare la distribuzione

Per testare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale, o creare un piano di ripristino composta da più macchine virtuali ed eseguire caso di errore per il piano di test. Test failover consente di simulare il meccanismo failover e ripristino di una rete isolata. Si noti che:

- Se si desidera connettere la macchina virtuale in Azure con Desktop remoto dopo il failover, abilitare connessione Desktop remoto sul computer virtuale prima di eseguire il test failover.
- Dopo il failover, si userà un indirizzo IP pubblico per connettersi alla macchina virtuale di Azure con Desktop remoto. Se si desidera eseguire questa operazione, assicurarsi che non si dispone di alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.

Per verificare il completamento dell'operazione, seguire la procedura descritta in [Monitorare l'attività](#monitor).

### <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Creare un file XML come modello per il piano di ripristino utilizzando i dati seguenti e quindi salvarla come "C:\RPTemplatePath.xml".
2. Modificare il nodo RecoveryPlan Id, nome, PrimaryServerId e SecondaryServerId.
3. Modificare il nodo ProtectionEntity PrimaryProtectionEntityId (vmid da VMM).
4. È possibile aggiungere ulteriori macchine virtuali mediante l'aggiunta di più nodi ProtectionEntity.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Inserire i dati nel modello:


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Creare il RecoveryPlan:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Eseguire test caso di errore

1.  Ottenere l'oggetto RecoveryPlan eseguendo il comando seguente:

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Avviare il test failover eseguendo il comando seguente:


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


## <a name=monitor></a>Monitorare l'attività

Usare i comandi seguenti per monitorare l'attività. Si noti che è necessario aspettare che tra processi per il completamento dell'elaborazione.


    Do
    {
            $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
            Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
            if($job -eq $null -or $job.StateDescription -ne "Completed")
            {
                $isJobLeftForProcessing = $true;
            }

        if($isJobLeftForProcessing)
            {
                Start-Sleep -Seconds 60
            }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Passaggi successivi

[Per ulteriori](https://msdn.microsoft.com/library/dn850420.aspx) sui cmdlet di PowerShell di ripristino di Azure sito. </a>.
