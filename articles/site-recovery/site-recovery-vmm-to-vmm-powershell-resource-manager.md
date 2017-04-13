<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM in un sito secondario di VMM tramite PowerShell (Manager delle risorse) | Microsoft Azure"
    description="Descrive come distribuire Azure il ripristino del sito per la gestione di replica, failover e ripristino delle macchine virtuali di Hyper-V in cloud VMM a un sito secondario di VMM tramite PowerShell (Manager delle risorse)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Replicare macchine virtuali di Hyper-V in cloud VMM in un sito secondario di VMM tramite PowerShell (Manager delle risorse)

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-vmm.md)
- [Portale classica](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Manager delle risorse](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Introduzione al ripristino del sito Azure! Usare questo articolo se si desidera replicare macchine virtuali di Hyper-V locale gestito in aree System Center Virtual Machine Manager (VMM) a un sito secondario. 

In questo articolo viene illustrato come usare PowerShell per automatizzare attività da eseguire quando si configura il ripristino del sito di Azure per replicare macchine virtuali di Hyper-V in aree System Center VMM nuvole System Center VMM nel sito secondario.

L'articolo include i prerequisiti per lo scenario e Mostra 

- Come configurare un archivio di servizi di recupero
- Installare il Provider di ripristino del sito di Azure nel server VMM di origine e quello di destinazione VMM
- Registrare i server VMM nell'archivio di
- Configurare i criteri di replica per il Cloud VMM. Le impostazioni della replica il criterio verranno applicate a tutte le macchine virtuali protette 
- Attivare la protezione per le macchine virtuali. 
- Testare il failover delle macchine virtuali singolarmente o come parte di un piano di ripristino per assicurarsi che tutto funzioni come previsto.
- Eseguire un pianificata oppure un failover non pianificato delle macchine virtuali singolarmente o come parte di un piano di ripristino per assicurarsi che tutto funzioni come previsto.

Se si verificano problemi durante l'impostazione questo scenario, pubblicare domande nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: Gestione risorse di Azure e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione. Questo articolo illustra il modello di distribuzione di Manager delle risorse.



## <a name="on-premises-prerequisites"></a>Prerequisiti locale

Ecco cosa occorre nei siti principali e secondarie locale per distribuire questo scenario:

**Prerequisiti** | **Dettagli** 
--- | ---
**VMM** | È consigliabile che distribuire un server VMM nel sito principale e un server VMM il sito secondario.<br/><br/> È inoltre possibile [replicare tra aree in un unico server VMM](site-recovery-single-vmm.md). Per eseguire questa operazione è necessario almeno due aree configurati sul server VMM.<br/><br/> Server VMM deve essere in esecuzione almeno System Center 2012 SP1 con gli aggiornamenti più recenti.<br/><br/> Ogni server VMM deve avere uno o più aree configurati e tutte le aree è necessario impostare il profilo della capacità di Hyper-V. <br/><br/>Nuvole devono contenere uno o più gruppi di host VMM.<br/><br/>Altre informazioni sull'impostazione cloud VMM nella [configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Server VMM devono avere accesso a internet. 
**Hyper-V** | Server Hyper-V deve essere in esecuzione almeno Windows Server 2012 con il ruolo Hyper-V e sono stati installati gli aggiornamenti più recenti.<br/><br/> Un server di Hyper-V deve contenere uno o più macchine virtuali.<br/><br/>  Server host Hyper-V devono essere distribuiti nei gruppi host nel cloud VMM primario e secondario.<br/><br/> Se si esegue Hyper-V in un cluster in Windows Server 2012 R2 è necessario installare [l'aggiornamento 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se si esegue Hyper-V in un cluster di Windows Server 2012 Nota tale gestore cluster non viene creati automaticamente se si dispone di un cluster basato su indirizzo IP statico. È necessario configurare manualmente il gestore di cluster. [Per ulteriori](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Provider** | Durante la distribuzione il ripristino del sito di installare il Provider di ripristino del sito di Azure sui server VMM. Il provider di servizi comunica con il ripristino del sito tramite HTTPS 443 per poter gestire le replica. Replica di dati tra il server di Hyper-V primario e secondario attraverso la rete LAN o una connessione VPN.<br/><br/> Il Provider eseguito sul server VMM richiede l'accesso a questi URL: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Inoltre consentire la comunicazione firewall dal server VMM [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/download/confirmation.aspx?id=41653) e per consentire il protocollo HTTPS (443).

### <a name="network-mapping-prerequisites"></a>Prerequisiti di mapping di rete
Rete mappe di mapping tra le reti VMM VM nei server che VMM primario e secondario per:

- Posizionare in modo ottimale macchine virtuali di replica su host Hyper-V secondario dopo il failover.
- Connettere macchine virtuali di replica reti macchine Virtuali appropriate.
- Se non Configura rete mapping replica macchine virtuali non connesso a qualsiasi rete dopo il failover.
- Se si desidera configurare la rete mapping durante il ripristino del sito distribuzione qui è operazioni che sarà necessario:

    - Assicurarsi di macchine virtuali nel server di origine Hyper-V host connessi a una rete VMM VM. La rete deve essere collegata a una rete logica associato nel cloud.
    - Verificare che il cloud secondario che verrà utilizzato per il ripristino disponga di una rete macchine Virtuali corrispondente configurato. La rete macchine Virtuali deve essere collegata a una rete logica associato con il cloud secondario.


Ulteriori informazioni sulla configurazione reti VMM nel sotto articoli

- [Configurazione di reti logiche in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Configurazione di gateway e reti macchine Virtuali in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[Altre informazioni](site-recovery-network-mapping.md) sulle modalità di connessione di rete.

###<a name="powershell-prerequisites"></a>Prerequisiti di PowerShell
Assicurarsi che sia pronto per l'invio di Azure PowerShell. Se si utilizza già PowerShell, sarà necessario eseguire l'aggiornamento alla versione 0.8.10 o versione successiva. Per informazioni sull'impostazione di PowerShell, vedere la [Guida per installare e configurare Azure PowerShell](../powershell-install-configure.md). Dopo aver impostato e configurato PowerShell, è possibile visualizzare tutti i cmdlet disponibili per il servizio [di seguito](https://msdn.microsoft.com/library/dn850420.aspx). 

Per informazioni sui suggerimenti che consentono di utilizzare i cmdlet, ad esempio come output di input e i valori dei parametri vengono in genere gestiti in Azure PowerShell, vedere la [Guida per iniziare a utilizzare i cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Passaggio 1: Configurare l'abbonamento 

1. Da Azure powershell, accedere al proprio account Azure: utilizzare i cmdlet seguenti
 
        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred 
    

2. È possibile ottenere un elenco delle sottoscrizioni. Questo anche verrà elencati i subscriptionIDs per ognuna delle sottoscrizioni. Si noti verso il basso il subscriptionID della sottoscrizione in cui si desidera creare l'archivio di servizi di recupero    

        Get-AzureRmSubscription 

3. Impostare l'abbonamento in cui l'archivio di servizi di recupero viene creato da menzionare ID abbonamento

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Passaggio 2: Creare un archivio di servizi di recupero 

1. Creare un gruppo di risorse Manager delle risorse Azure se non è già disponibile

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Creare un nuovo archivio di servizi di recupero e salvare l'oggetto di archivio di ripristino creato in una variabile (verrà utilizzato in un secondo momento). È inoltre possibile recuperare la creazione di un post di oggetto di un archivio di ripristino utilizzando il cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passaggio 3: Impostare il contesto di archivio di servizi di recupero

1.  Se si dispone di un archivio già stato creato, eseguire il seguente comando per ottenere l'archivio.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Impostare il contesto di archivio eseguendo il seguente comando.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault



## <a name="step-4-install-the-azure-site-recovery-provider"></a>Passaggio 4: Installare il Provider di ripristino del sito Azure

1.  Nel computer VMM, creare una directory eseguendo il comando seguente:
    
        New-Item c:\ASR -type directory
        
2.  Estrarre i file mediante il provider scaricato eseguendo il seguente comando
    
        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q

    
3.  Installare il provider di utilizzare i comandi seguenti:
    
        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Attendere il completamento dell'installazione.
    
4.  Registrare il server nell'archivio di utilizzando il comando seguente:
    
        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-and-associate-a-replication-policy"></a>Passaggio 5: Creare e associare un criterio di replica

1.  Creare un criterio di replica Hyper-V 2012 R2 eseguendo il comando seguente:

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] Cloud VMM può contenere host Hyper-V in esecuzione diverse versioni di Windows Server (come indicato in prerequisiti Hyper-V), ma il criterio di replica è versione del sistema operativo specifico. Se si dispone di host diversi in esecuzione in versioni diverse del sistema operativo, creare criteri di replica distinta per ogni tipo di versione del sistema operativo. Per ad esempio: se si dispone di cinque hosts eseguono Windows Server 2012 e tre in Windows Server 2012 R2, creare due criteri di replica: uno per ogni tipo di versioni del sistema operativo.

2.  Ottenere il contenitore di protezione principale (Cloud VMM principale) e un contenitore di protezione di ripristino (ripristino VMM Cloud) eseguendo i comandi seguenti:
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Recuperare il criterio creato nel passaggio 1 utilizzando il nome del criterio

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Avviare l'associazione del contenitore di protezione (Cloud VMM) con il criterio di replica:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Attendere il completamento del processo associazione dei criteri. È possibile verificare se il lavoro è completato con frammento di PowerShell riportato di seguito.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Al termine dell'elaborazione il processo, eseguire il comando seguente:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Per verificare il completamento dell'operazione, seguire la procedura descritta in [Monitorare l'attività](#monitor).

## <a name="step-5-configure-network-mapping"></a>Passaggio 5: Configurare il mapping di rete

1. Il primo comando Ottiene server per l'archivio il ripristino del sito di Azure corrente. Il comando Archivia i server il ripristino del sito di Microsoft Azure nella variabile di matrice $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. Il sotto i comandi ottenere la rete di ripristino del sito per il server VMM di origine e quello di destinazione VMM.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] Il server di origine VMM può essere il primo o secondo nella matrice server. Controllare i nomi dei server VMM e ottenere le reti in modo appropriato


4. Il cmdlet finale consente di creare un mapping tra la rete principale e il ripristino. Il cmdlet specifica la rete principale come primo elemento di $PrimaryNetworks e la rete ripristino come il primo elemento $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Passaggio 6: Configurare il mapping di spazio di archiviazione

1. Il seguente comando Ottiene l'elenco di classificazioni di spazio di archiviazione in $storageclassifications variabile.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. Di seguito comandi ottenere la classificazione di origine in $SourceClassificaion classificazione variabile e di destinazione in $TargetClassification variabile. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] Le classificazioni di origine e destinazione possono essere qualsiasi elemento della matrice. Fare riferimento all'output del seguente comando per determinare l'indice di origine e destinazione classificazioni in $storageclassifications in forma di matrice. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Selezionare oggetto - proprietà FriendlyName, Id | Formato tabella


3. Il seguente cmdlet consente di creare un mapping tra la classificazione di origine e la classificazione di destinazione. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Passaggio 7: Attivare la protezione per macchine virtuali

Dopo che il server, aree e le reti sono configurate correttamente, è possibile abilitare la protezione per macchine virtuali nel cloud. 


  1. Per abilitare la protezione, eseguire il seguente comando per ottenere il contenitore di protezione:
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Ottenere l'entità di protezione (macchine Virtuali) per eseguire il comando seguente:
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Abilitare la replica per la macchina virtuale eseguendo il comando seguente:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Testare la distribuzione

Per testare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale, o creare un piano di ripristino composta da più macchine virtuali ed eseguire caso di errore per il piano di test. Test failover consente di simulare il meccanismo failover e ripristino di una rete isolata. 

> [AZURE.NOTE] Nel portale di Azure, è possibile creare un piano di ripristino dell'applicazione.

Per verificare il completamento dell'operazione, seguire la procedura descritta in [Monitorare l'attività](#monitor).


### <a name="run-a-test-failover"></a>Eseguire test caso di errore

1.  Eseguire il seguente cmdlet per accedere alla rete macchine Virtuali a cui si desidera eseguire test di failover nelle macchine virtuali a.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Eseguire test caso di errore di una macchina virtuale eseguendo le operazioni seguenti:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Eseguire test caso di errore di un piano di ripristino eseguendo le operazioni seguenti:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Eseguire pianificato in caso di errore

1. Eseguire pianificato in caso di errore di una macchina virtuale eseguendo le operazioni seguenti:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Eseguire pianificato in caso di errore di un piano di ripristino eseguendo le operazioni seguenti:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato

1. Eseguire un failover non pianificato di una macchina virtuale eseguendo le operazioni seguenti:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2. eseguire un failover non pianificato di un piano di ripristino eseguendo le operazioni seguenti:
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
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

[Per ulteriori](https://msdn.microsoft.com/library/azure/mt637930.aspx) sul ripristino di siti di Azure con i cmdlet di PowerShell di Azure Manager delle risorse.

