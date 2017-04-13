<properties
    pageTitle="Protezione dei server Azure tramite PowerShell Azure Gestione risorse Azure | Microsoft Azure"
    description="Automatizzare la protezione dei server in Azure con il ripristino del sito di Azure tramite PowerShell e gestione di risorse di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Replicare tra macchine virtuali di Hyper-V in locale e Azure tramite PowerShell e gestione di risorse di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Manager delle risorse](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portale classica](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Panoramica

Il ripristino del sito Azure contribuisce a business continuità ed emergenza strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali in un numero di scenari di distribuzione. Per un elenco completo di scenari di distribuzione, vedere la [Panoramica il ripristino del sito di Azure](site-recovery-overview.md).

PowerShell Azure è un modulo che fornisce i cmdlet per la gestione di Azure tramite Windows PowerShell. Funziona con due tipi di moduli: il modulo di Azure profilo o il modulo di gestione delle risorse di Azure.

Cmdlet di PowerShell di ripristino del sito, disponibile con Azure PowerShell per gestione di risorse di Azure consentono di proteggere e ripristinare i server Azure.

In questo articolo viene descritto come utilizzare Windows PowerShell, insieme a gestione di risorse di Azure per distribuire il ripristino del sito per configurare e gestire la protezione server Azure. Nell'esempio utilizzato in questo articolo viene illustrato come proteggere, esito negativo su e recuperare macchine virtuali di un host Hyper-V in Azure, tramite PowerShell Azure Gestione risorse Azure.

> [AZURE.NOTE] I cmdlet di PowerShell ripristino sito attualmente consentono di configurare le operazioni seguenti: un sito di gestione di macchina virtuale a un altro, un sito di macchina virtuale Manager in Azure e un sito di Hyper-V in Azure.

Non è necessario essere un esperto di PowerShell per usare questo articolo, ma è necessario conoscere i concetti di base, ad esempio moduli, i cmdlet e sessioni. Per ulteriori informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

È inoltre possibile leggere informazioni [sull'Uso di PowerShell Azure Gestione risorse Azure](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Microsoft partner che fanno parte della finestra del programma Cloud soluzione Provider (CSP) è possibile configurare e gestire la protezione dei server dei clienti per rispettivi CSP le sottoscrizioni i clienti (tenant abbonamenti).

## <a name="before-you-start"></a>Prima di iniziare

Verificare di che avere inserito i prerequisiti di:

- Un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). Inoltre, è possibile leggere sulla [gestione di ripristino di Azure siti prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Per informazioni su questa versione e come installarlo, vedere [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- Moduli [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) . È possibile ottenere le versioni più recenti di questi moduli della [raccolta di PowerShell](https://www.powershellgallery.com/)

In questo articolo viene illustrato come usare Powershell Azure Gestione risorse Azure per configurare e gestire la protezione dei server. Nell'esempio utilizzato in questo articolo viene illustrato come proteggere una macchina virtuale in esecuzione su un host Hyper-V, Azure. I prerequisiti seguenti sono specifici per questo esempio. Per un set più completo dei requisiti per i diversi scenari del ripristino del sito, fare riferimento alla documentazione relativa a tale scenario.

- Host Hyper-V in esecuzione Windows Server 2012 R2 o che contiene uno o più macchine virtuali di Microsoft Hyper-V Server 2012 R2.
- Hyper-V Server connessi a Internet, direttamente o tramite un proxy.
- Macchine virtuali che si desidera proteggere devono essere conformi con [Prerequisiti macchina virtuale](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Passaggio 1: Accedere al proprio account Azure


1. Aprire una console di PowerShell ed eseguire questo comando per accedere al proprio account Azure. Il cmdlet viene visualizzata una pagina web che verranno richieste le credenziali dell'account.

        Login-AzureRmAccount

    In alternativa, è possibile includere le credenziali dell'account come parametro per il `Login-AzureRmAccount` cmdlet usando il `-Credential` parametro.

    Se si partner CSP utilizzo per conto di un tenant, specificare il cliente come un tenant, tramite il nome di dominio primario tenantID o tenant.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Un account può contenere più abbonamenti, quindi è necessario associare l'abbonamento da usare con l'account.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Verificare che l'abbonamento sia registrato per l'utilizzo di provider di servizi di Azure per i servizi di recupero e il ripristino del sito, utilizzando i comandi seguenti:

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    Nell'output di questi comandi, se **RegistrationState** è impostata su **registrata**, è possibile procedere al passaggio 2. In caso contrario, è necessario registrare il provider di mancante nell'abbonamento.

    Per registrare il provider di Azure per il ripristino del sito e i servizi di recupero, eseguire i comandi seguenti:

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Verificare che i provider registrati correttamente utilizzando i comandi seguenti: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` e `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Passaggio 2: Configurare l'archivio di servizi di recupero

1. Creare un gruppo di risorse Manager delle risorse Azure in cui è necessario creare l'archivio o utilizzare un gruppo di risorse esistenti. È possibile creare un nuovo gruppo di risorse utilizzando il comando seguente:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    dove la variabile $ResourceGroupName contiene il nome del gruppo di risorse che si desidera creare e la variabile $Geo contiene l'area Azure in cui creare il gruppo di risorse (ad esempio, "Sud Brasile").

    È possibile ottenere un elenco di gruppi di risorse nell'abbonamento tramite il `Get-AzureRmResourceGroup` cmdlet.

2. Creare un nuovo archivio di servizi di ripristino di Windows Azure come segue:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    È possibile recuperare un elenco degli archivi esistenti utilizzando la `Get-AzureRmRecoveryServicesVault` cmdlet.

> [AZURE.NOTE] Se si desidera eseguire operazioni su archivi il ripristino del sito creati tramite il portale classico o il modulo di Azure servizio Gestione PowerShell, è possibile recuperare un elenco di tali archivi utilizzando il `Get-AzureRmSiteRecoveryVault` cmdlet. È consigliabile creare un nuovo archivio di servizi di recupero per tutte le operazioni di nuove. Archivi il ripristino del sito creata in precedenza sono supportati, ma non dispongono di caratteristiche più recenti.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passaggio 3: Impostare il contesto di archivio di servizi di recupero

1.  Impostare il contesto di archivio eseguendo il comando seguente:

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Passaggio 4: Creare un sito di Hyper-V e generare una nuova chiave di registrazione di archivio per il sito.

1. Creare un nuovo sito di Hyper-V come segue:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Questo cmdlet avvia un processo di ripristino del sito per creare il sito e restituisce un oggetto di processo di ripristino del sito. Attendere il processo per il completamento e verificare che il processo è stato completato correttamente.

    È possibile recuperare l'oggetto processo e quindi controllare lo stato corrente del processo, utilizzando il cmdlet Get-AzureRmSiteRecoveryJob.
2. Generare e scaricare una chiave di registrazione per il sito, come indicato di seguito:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copiare il codice scaricato all'host Hyper-V. È necessario il tasto per eseguire la registrazione host Hyper-V al sito.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Passaggio 5: Installare il provider del ripristino del sito di Azure e agente di servizi di recupero Azure sull'host Hyper-V

1. Scaricare il programma di installazione per la versione più recente del provider [Microsoft](https://aka.ms/downloaddra).

2. Esegui il programma di installazione sull'host Hyper-V e al termine dell'installazione di continuare con il passaggio di registrazione.

3. Quando richiesto, immettere la chiave di registrazione sito scaricati e completare la registrazione dell'host di Hyper-V al sito.

4. Verificare che l'host Hyper-V sia registrato al sito tramite il comando seguente:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Passaggio 6: Creare un criterio di replica e associare con il contenitore di protezione

1. Creare un criterio di replica come segue:

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Controllare il processo restituito per assicurarsi che la creazione di criteri di replica ha avuto esito positivo.

    >[AZURE.IMPORTANT] L'account di archiviazione specificato dovrebbe essere presenti nell'area di Azure stesso come l'archivio di servizi di recupero e deve avere abilitata la replica geografico.
    >
    > - Se l'account di archiviazione di ripristino specificato è del tipo di archiviazione di Azure (classico), failover macchine protetto recuperare la macchina Azure IaaS (classico).
    > - Se l'account di archiviazione di ripristino specificato è del tipo di archiviazione di Azure (Azure Manager delle risorse), failover macchine protetto recuperare la macchina Azure IaaS (Manager delle risorse di Azure).

2. Ottenere il contenitore di protezione corrispondente al sito, come indicato di seguito:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Avviare l'associazione del contenitore di protezione con il criterio di replica, come indicato di seguito:

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Attendere il completamento del processo di associazione e assicurarsi che completata correttamente.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Passaggio 7: Attivare la protezione per macchine virtuali

1. Ottenere l'entità di protezione corrispondente a macchine Virtuali che si desidera proteggere, come indicato di seguito:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Iniziare a proteggere il computer virtuale, come indicato di seguito:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] L'account di archiviazione specificato dovrebbe essere presenti nell'area di Azure stesso come l'archivio di servizi di recupero e deve avere abilitata la replica geografico.
    >
    > - Se l'account di archiviazione di ripristino specificato è del tipo di archiviazione di Azure (classico), failover macchine protetto recuperare la macchina Azure IaaS (classico).
    > - Se l'account di archiviazione di ripristino specificato è del tipo di archiviazione di Azure (Azure Manager delle risorse), failover macchine protetto recuperare la macchina Azure IaaS (Manager delle risorse di Azure).

    > Se la macchina virtuale per proteggere ha più di un disco collegati, specificare il disco di sistema operativo utilizzando il parametro *OSDiskName* .

3. Attendere macchine virtuali raggiungere uno stato protetto dopo la replica iniziale. L'operazione può richiedere un po' di tempo, in base a fattori quali la quantità di dati da replicare e la larghezza di banda padre disponibile in Azure. Lo stato di processo e StateDescription vengono aggiornate come indicato di seguito, durante la macchina virtuale raggiungere uno stato protetto.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Aggiornare le proprietà di ripristino, ad esempio le dimensioni di ruolo macchine Virtuali e la rete Azure allegare schede di rete della macchina virtuale per in caso di failover.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Passaggio 8: Eseguire test caso di errore

1. Eseguire un processo di failover test, come indicato di seguito:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Verificare che il test macchine Virtuali venga creata in Azure. (Il processo di failover test è sospeso, dopo aver creato il test macchine Virtuali in Azure. Termine del processo eliminando sfarfallio creato alla riattivazione del processo, come illustrato nel passaggio successivo.)

3. Completare il failover test, come indicato di seguito:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Passaggi successivi

[Per ulteriori](https://msdn.microsoft.com/library/azure/mt637930.aspx) sul ripristino di siti di Azure con i cmdlet di PowerShell di Azure Manager delle risorse.
