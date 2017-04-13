<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM tramite PowerShell (Manager delle risorse) e il ripristino del sito di Azure | Microsoft Azure"
    description="Replicare macchine virtuali di Hyper-V in cloud VMM utilizzando il ripristino del sito di Azure e PowerShell"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replicare macchine virtuali di Hyper-V in cloud VMM Azure tramite PowerShell e gestione di risorse di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Manager delle risorse](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portale classica](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classico](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Panoramica

Il ripristino del sito Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali in un numero di scenari di distribuzione. Per un elenco completo di scenari di distribuzione, vedere [Panoramica il ripristino del sito di Azure](site-recovery-overview.md).

In questo articolo viene illustrato come usare PowerShell per automatizzare attività da eseguire quando si configura il ripristino del sito di Azure replicare macchine virtuali di Hyper-V in aree System Center VMM allo spazio di archiviazione Azure.

L'articolo include i prerequisiti per lo scenario e Mostra

- Come configurare un archivio di servizi di recupero
- Installare il Provider di ripristino del sito di Azure nel server VMM di origine
- Registrare il server nell'archivio di, aggiungere un account di archiviazione Azure
- Installare l'agente di servizi di ripristino di Windows Azure nel server host Hyper-V
- Configurare le impostazioni di protezione per cloud VMM, che verrà applicata a tutte le macchine virtuali protette
- Attivare la protezione per tali macchine virtuali.
- Testare la sopra fail per assicurarsi che tutto funzioni come previsto.

Se si verificano problemi durante l'impostazione questo scenario, pubblicare domande nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse e classica](../resource-manager-deployment-model.md). In questo articolo è descritta utilizzando il modello di distribuzione di Manager delle risorse.

## <a name="before-you-start"></a>Prima di iniziare

Verificare di che avere inserito i prerequisiti di:

### <a name="azure-prerequisites"></a>Prerequisiti Azure

- È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . Se non si dispone di uno, iniziare con un [account gratuito](https://azure.microsoft.com/free). Inoltre, è possibile leggere sulla [gestione di ripristino di Azure siti prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).
- Se si sta tentando di replica di uno scenario di abbonamento CSP in corso, è necessario un abbonamento CSP. Per ulteriori informazioni sul programma CSP in [come iscriversi al programma CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- È necessario un account di archiviazione (Manager delle risorse) v2 Azure per archiviare i dati replicati in Azure. Attivazione della replica geografico è necessario che l'account. Dovrebbe essere nella stessa regione servizio il ripristino del sito di Azure ed essere associato lo stesso abbonamento o l'abbonamento CSP. Per ulteriori informazioni sulla configurazione di archiviazione Azure, vedere [Introduzione a Microsoft Azure archiviazione](../storage/storage-introduction.md) per riferimento.
- È necessario assicurarsi che macchine virtuali che si desidera proteggere rispettino i [Prerequisiti di Azure macchina virtuale](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Operazioni Livella di macchine Virtuali sono attualmente possibili tramite Powershell. Supporto per le operazioni di ripristino piano livello sarà presto.  Per il momento sono limitate all'esecuzione di passaggi fail solo una granularità 'protetta macchine Virtuali' e non a un livello di pianificazione di ripristino.

### <a name="vmm-prerequisites"></a>Prerequisiti VMM
- È necessario server VMM in System Center 2012 R2.
- Qualsiasi server VMM contenente macchine virtuali che si desidera proteggere deve essere in esecuzione il Provider di ripristino del sito di Azure. Questo è installato durante la distribuzione di Azure sito ripristino.
- È necessario almeno un cloud sul server VMM che si desidera proteggere. Deve contenere nel cloud:
    - Uno o più gruppi host VMM.
    - Uno o più server host Hyper-V o cluster di ogni gruppo host.
    - Uno o più macchine virtuali nel server di origine Hyper-V.
- Ulteriori informazioni sull'impostazione cloud VMM:
    - Per ulteriori su cloud privati VMM [Novità di Cloud privato con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) in [VMM 2012 e il cloud](http://go.microsoft.com/fwlink/?LinkId=324956).
    - Informazioni sulla [configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Dopo che gli elementi dell'infrastruttura cloud sono disponibili ulteriori informazioni sulla creazione di cloud private per [la creazione di un cloud privato in VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) e in [procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Prerequisiti di Hyper-V

- I server host Hyper-V devono essere in esecuzione almeno **Windows Server 2012** con ruolo Hyper-V o **Microsoft Hyper-V Server 2012** e sono stati installati gli aggiornamenti più recenti.
- Se si esegue Hyper-V in una nota cluster tale gestore cluster non viene creati automaticamente se si dispone di un cluster basato su indirizzo IP statico. È necessario configurare manualmente il gestore di cluster. Per
- Per istruzioni, vedere [come configurare Hyper-V Replica gestore](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Tutti i server host Hyper-V e cluster per il quale si desidera gestire protezione deve essere incluso in un'area VMM.

### <a name="network-mapping-prerequisites"></a>Prerequisiti di mapping di rete
Quando si proteggere macchine virtuali di Azure, le mappe di mapping di rete macchina virtuale reti nel server VMM di origine e destinazione Azure reti per poter effettuare le operazioni seguenti:

- Tutti i computer che non sopra nella stessa rete possono connettersi tra loro, indipendentemente dalla quale piano di ripristino si trovano in.
- Se un gateway di rete è impostata sulla destinazione della rete Azure, macchine virtuali di connettersi a altre macchine virtuali locale.
- Se non Configura connessione di rete, solo le macchine virtuali che non tramite lo stesso piano di ripristino saranno in grado di connettersi tra loro dopo fail in Azure.

Se si desidera distribuire connessione di rete è necessario le operazioni seguenti:

- Macchine virtuali che si desidera proteggere nel server VMM di origine deve essere connesso a una rete macchine Virtuali. La rete deve essere collegata a una rete logica associato nel cloud.
- Una rete Azure a cui è possibile connettersi replicate macchine virtuali dopo la risoluzione. Selezionare la rete al momento della risoluzione. La rete dovrebbe essere nella stessa regione come abbonamento il ripristino del sito di Azure.

Ulteriori informazioni sulla connessione di rete in

- [Configurazione di reti logiche in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Configurazione di gateway e reti macchine Virtuali in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Come configurare e monitorare le reti virtuali in Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


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

1. Creare un gruppo di risorse in Gestione risorse di Azure, se non è già disponibile

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Creare un nuovo archivio di servizi di recupero e salvare l'oggetto di archivio di ripristino creato in una variabile (verrà utilizzato in un secondo momento). È inoltre possibile recuperare la creazione di un post di oggetto di un archivio di ripristino utilizzando il cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passaggio 3: Impostare il contesto di archivio di servizi di recupero

1.  Impostare il contesto di archivio eseguendo il seguente comando.

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


## <a name="step-5-create-an-azure-storage-account"></a>Passaggio 5: Creare un account di archiviazione Azure

1. Se non si dispone di un account di archiviazione Azure, creare un account di replica geografico abilitato nella stessa geografico come archivio eseguendo il comando seguente:

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Si noti che l'account di archiviazione deve essere nella stessa regione servizio il ripristino del sito di Azure ed essere associato all'abbonamento stesso.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Passaggio 6: Installare l'agente di servizi di recupero Azure

1. Scaricare l'agente di servizi di ripristino di Windows Azure nel [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e installare ogni server host Hyper-V nella cloud VMM che si desidera proteggere.

2. Eseguire il comando seguente in tutti gli host VMM:

    marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Passaggio 7: Configurare cloud impostazioni di protezione

1.  Creare un criterio di replica di Azure eseguendo il comando seguente:


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Ottenere un contenitore di protezione eseguendo i comandi seguenti:

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Visualizzare i dettagli di criteri a una variabile utilizzando il processo di creazione e menzionare il nome descrittivo dei criteri:

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Avviare l'associazione del contenitore di protezione con il criterio di replica:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Al termine di processo, eseguire il comando seguente:

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Al termine dell'elaborazione il processo, eseguire il comando seguente:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Per verificare il completamento dell'operazione, seguire la procedura descritta in [Monitorare l'attività](#monitor).

## <a name="step-8-configure-network-mapping"></a>Passaggio 8: Configurare il mapping di rete

Prima di iniziare il mapping di rete verificare che macchine virtuali sul server VMM origine connessi a una rete macchine Virtuali. Inoltre, creare uno o più reti virtuali Azure.

Ulteriori informazioni su come creare una rete virtuale tramite Gestione risorse di Azure e PowerShell, in [creare una rete virtuale con una connessione VPN da sito tramite Gestione risorse di Azure e PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Si noti che è possono eseguire il mapping di più reti macchina virtuale a un'unica rete Azure. Se la rete di destinazione dispone di più subnet e una di queste subnet ha lo stesso nome subnet in cui si trova la macchina virtuale di origine, quindi la macchina virtuale duplicata verrà connesso subnet destinazione dopo la risoluzione. Se non vi è alcuna subnet di destinazione con un nome corrispondente, la macchina virtuale verrà connesso prima subnet nella rete.

1. Il primo comando Ottiene server per l'archivio il ripristino del sito di Azure corrente. Il comando Archivia i server il ripristino del sito di Microsoft Azure nella variabile di matrice $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. Il secondo comando Ottiene la rete di ripristino del sito per il primo server nella matrice $Servers. Il comando Archivia le reti nella variabile $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. Il terzo comando Ottiene reti virtuali Azure e tale valore nella variabile $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. Il cmdlet finale consente di creare un mapping tra la rete principale e la macchina virtuale Azure. Il cmdlet specifica la rete primaria come il primo elemento $Networks. Il cmdlet specifica una rete macchina virtuale come il primo elemento $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Passaggio 9: Attivare la protezione per macchine virtuali

Dopo che il server, aree e le reti sono configurate correttamente, è possibile abilitare la protezione per macchine virtuali nel cloud.

 Tenere presente quanto segue:

 - Macchine virtuali deve soddisfare requisiti Azure. Verificare gli aspetti seguenti in [Prerequisiti e il supporto](site-recovery-best-practices.md) della Guida alla pianificazione.

 - Per abilitare la protezione, il sistema operativo e il sistema operativo disco devono essere impostate per la macchina virtuale. Quando si crea una macchina virtuale in VMM tramite un modello di macchina virtuale è possibile impostare la proprietà. È anche possibile impostare queste proprietà per macchine virtuali esistenti nella scheda **Generale** e **Configurazione Hardware** delle proprietà macchina virtuale. Se non si imposta queste proprietà in VMM sarà possibile come configurarli nel portale del ripristino del sito di Azure.


  1. Per abilitare la protezione, eseguire il seguente comando per ottenere il contenitore di protezione:

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Ottenere l'entità di protezione (macchine Virtuali) per eseguire il comando seguente:

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Abilitare DR per la macchina virtuale eseguendo il comando seguente:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Testare la distribuzione

Per testare la distribuzione è possibile eseguire un test fail-over per una singola macchina virtuale, o creare un piano di ripristino composta da più macchine virtuali ed eseguire un test fail-over per il piano. Test fail-over consente di simulare il meccanismo fail over e ripristino di una rete isolata. Si noti che:

- Se si desidera connettersi alla macchina virtuale di Azure con Desktop remoto dopo la sopra fail, abilitare connessione Desktop remoto sul computer virtuale prima di eseguire il test failover.
- Dopo aver fail sopra, si userà un indirizzo IP pubblico per connettere la macchina virtuale in Azure con Desktop remoto. Se si desidera eseguire questa operazione, assicurarsi che non si dispone di alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.

Per verificare il completamento dell'operazione, seguire la procedura descritta in [Monitorare l'attività](#monitor).


### <a name="run-a-test-failover"></a>Eseguire test caso di errore

1.  Avviare il test failover eseguendo il comando seguente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Eseguire pianificato in caso di errore

1. Avviare failover pianificato eseguendo il comando seguente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato

1. Avviare failover non pianificato eseguendo il comando seguente:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


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
