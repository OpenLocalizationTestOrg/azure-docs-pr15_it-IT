<properties
    pageTitle="Replicare macchine virtuali VMware Azure utilizzando il ripristino del sito con Azure automazione DSC | Microsoft Azure"
    description="Viene descritto come utilizzare Azure automazione DSC per distribuire automaticamente il servizio di Azure sito ripristino mobilità e Azure agente per macchine virtuali/fisici in Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Replicare macchine virtuali VMware Azure utilizzando il ripristino del sito con Azure automazione DSC

Nel gruppo Gestione operazioni, viene fornito un backup completo e soluzione di ripristino completa che è possibile utilizzare come parte del piano di continuità aziendale.

Si inizia a questo viaggio insieme Hyper-V tramite Replica Hyper-V. Ma è stato espanso per supportare una configurazione eterogenea perché i clienti nelle aree più hypervisor e piattaforme.

Se si esegue VMware carichi di lavoro e/o server fisici oggi, tutti i componenti il ripristino del sito di Azure un server di gestione viene eseguita nel proprio ambiente di gestione della replica di dati e la comunicazione con Azure, al momento Azure rappresenta la destinazione.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Distribuire il servizio sito ripristino mobilità utilizzando DSC di automazione
Iniziamo eseguendo rapida scomposizione che cosa significa questo server di gestione.

Il server di gestione esegue numerosi ruoli del server. Uno di questi ruoli è *configurazione*, che coordinare la comunicazione e gestire i processi di replica e ripristino di dati.

Inoltre, il ruolo di *processo* funge da un gateway di replica. Questo ruolo riceve replica dati dal computer di origine protetta, Ottimizza con la memorizzazione nella cache, compressione e la crittografia e quindi invia a un account di archiviazione Azure. Una delle funzioni per il ruolo di processo è anche per inserire l'installazione del servizio mobilità ai computer protetto ed eseguire il rilevamento automatico delle macchine virtuali VMware.

Se esiste un failback da Azure, il ruolo di *destinazione master* consente di gestire i dati di replica come parte di questa operazione.

Computer protetto, abbiamo si basano sul *servizio mobilità*. Questo componente viene distribuito in ogni computer (VMware VM o server fisico) che si desidera replicare in Azure. Acquisisce scrive dati nel computer e li inoltra al server di gestione (ruolo del processo).

Quando si gestiscono continuità aziendale, è importante conoscere i carichi di lavoro, infrastruttura e i componenti coinvolti. È quindi possibile soddisfare i requisiti per l'obiettivo di tempo di ripristino (RTO) e obiettivo punto di ripristino (rilasciato). Nel contesto, il servizio di mobilità è fondamentale per garantire che i carichi di lavoro protetti come previsto.

In che modo possono, in modo ottimizzato, assicurarsi di avere una configurazione affidabile protetta con l'aiuto di alcuni componenti di operazioni Management Suite?

In questo articolo viene illustrato un esempio di come è possibile utilizzare Azure automazione bene accolta stato configurazione (DSC), insieme al ripristino del sito, per garantire che:

- Il servizio di mobilità e l'agente di macchine Virtuali di Azure vengono distribuiti nel computer Windows in cui si desidera proteggere.
- Il servizio di mobilità e l'agente di macchine Virtuali di Azure siano sempre in esecuzione quando Azure è la destinazione della replica.

## <a name="prerequisites"></a>Prerequisiti

- Un archivio per archiviare le necessarie per l'installazione
- Un archivio per archiviare la passphrase necessaria per eseguire la registrazione con il server di gestione

 > [AZURE.NOTE] Una frase univoca viene generata per ogni management server. Se si desidera distribuire più server di gestione, è necessario assicurarsi che la passphrase corretta archiviata nel file passphrase.txt.

- Windows Management Framework (WMF) 5.0 installato nel computer in cui si desidera attivare per la protezione (un requisito di automazione DSC)

 > [AZURE.NOTE] Se si desidera utilizzare computer DSC per Windows che ha installato 4.0 WMF, vedere la sezione [Utilizzare DSC in ambienti disconnessi](#Use DSC in disconnected environments).

Il servizio di mobilità può essere installato dalla riga di comando e accetta più argomenti. Ecco perché è necessario disporre i file binari (dopo l'estrazione loro dall'installazione) e archiviarle in una posizione in cui è possibile recuperare utilizzando una configurazione DSC.

## <a name="step-1-extract-binaries"></a>Passaggio 1: File binari estratto

1. Per estrarre i file che è necessario per il programma di installazione, passare alla directory seguente nel server di gestione:

    **\Microsoft sito azure Recovery\home\svsystems\pushinstallsvc\repository**

    In questa cartella viene visualizzato un file MSI denominato:

    **Microsoft ASR_UA_version_Windows_GA_date_Release.exe**

    Utilizzare il comando seguente per estrarre il programma di installazione:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Selezionare tutti i file e inviarle a una cartella compressa (zip).

È ora possibile i file binari necessari per automatizzare l'installazione del servizio di mobilità tramite automazione DSC.

### <a name="passphrase"></a>Passphrase

È necessario determinare nel punto in cui si desidera inserire la cartella compressa. È possibile usare un account di archiviazione Azure, come illustrato in seguito, per archiviare la passphrase necessari per la configurazione. L'agente verrà quindi registrato con il server di gestione come parte del processo.

La passphrase ottenuto quando si distribuisce il server di gestione può essere salvata in un file di testo come passphrase.txt.

Posizionare la cartella compressa e la passphrase in un contenitore dedicato all'account di archiviazione Azure.

![Percorso della cartella](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se si preferisce mantenere i file in una condivisione in rete, è possibile eseguire questa operazione. È sufficiente Assicurarsi che la risorsa DSC che verrà utilizzato in un secondo momento acceda e accessibile la configurazione e passphrase.

## <a name="step-2-create-the-dsc-configuration"></a>Passaggio 2: Creare la configurazione di DSC

L'impostazione dipende WMF 5.0. Per il computer applicare la configurazione attraverso l'automazione DSC, WMF 5.0 deve essere presente.

L'ambiente viene utilizzata la configurazione di DSC di esempio seguente:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configurazione verrà eseguire le operazioni seguenti:

- Le variabili che indica la configurazione come ottenere i file binari per il servizio di mobilità e agente macchine Virtuali di Azure, dove ottenere la passphrase e la posizione in cui memorizzare l'output.
- La configurazione importerà la risorsa xPSDesiredStateConfiguration DSC, in modo che è possibile utilizzare `xRemoteFile` per scaricare i file dal repository.
- La configurazione verrà creata una directory in cui si desidera memorizzare i file binari.
- La risorsa archivio verrà estrarre i file dalla cartella compressa.
- Risorsa di installazione verrà installato il servizio mobilità dalla UNIFIEDAGENT. Programma di installazione di EXE con gli argomenti specifici. (Le variabili di creare gli argomenti devono modificate per riflettere l'ambiente).
- Risorsa AzureAgent verrà installato l'agente di macchine Virtuali di Azure, è consigliabile su ogni macchina virtuale in esecuzione in Azure. Agente di macchine Virtuali di Azure consente inoltre di aggiungere estensioni macchina virtuale successiva failover.
- Le risorse servizio garantisce che i servizi di mobilità correlati e i servizi Azure siano sempre in esecuzione.

Salvare la configurazione come **ASRMobilityService**.

>[AZURE.NOTE] Ricordarsi di sostituire CSIP nella configurazione per riflettere il server di gestione effettiva, in modo che l'agente verrà collegato correttamente e utilizzerà la passphrase corretta.

## <a name="step-3-upload-to-automation-dsc"></a>Passaggio 3: Caricare automazione DSC

Perché la configurazione di DSC produttrice importerà un modulo di risorse necessarie DSC (xPSDesiredStateConfiguration), è necessario importare tale funzionalità di automazione prima di caricare la configurazione di DSC.

Accedere al proprio account di automazione, passare alle **risorse** > **moduli**, fare clic su **Sfoglia raccolta**.

Qui è possibile cercare il modulo e importarlo al proprio account.

![Modulo di importazione](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Al termine, passare al computer in cui sono i moduli di gestione di risorse Azure installati e procedere per importare la configurazione DSC appena creata.

### <a name="import-cmdlets"></a>Cmdlet di importazione

In PowerShell, accedere al proprio abbonamento Azure. Modificare i cmdlet per riflettere l'ambiente e raccogliere le informazioni sull'account di automazione in una variabile di tipo:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Caricare la configurazione di automazione DSC utilizzando il seguente cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilare la configurazione di automazione DSC

Successivamente, è necessario compilare la configurazione di automazione DSC, in modo che è possibile iniziare a registrare nodi ad esso. Per ottenere che eseguendo il seguente cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Possono essere necessari alcuni minuti, poiché si distribuisce in pratica la configurazione per il servizio di hosting DSC pull.

Dopo aver compilato la configurazione, è possibile recuperare le informazioni sul processo tramite PowerShell (Get-AzureRmAutomationDscCompilationJob) o tramite il [portale di Azure](https://portal.azure.com/).

![Recuperare il processo](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Si dispone ora correttamente pubblicato e caricamento della configurazione DSC di automazione DSC.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Passaggio 4: Macchine incorporate a DSC di automazione
>[AZURE.NOTE] Uno dei prerequisiti per il completamento di questo scenario è che i computer Windows vengano aggiornati con l'ultima versione di WMF. È possibile scaricare e installare la versione corretta per la piattaforma dall' [Area Download](https://www.microsoft.com/download/details.aspx?id=50395).

A questo punto si creerà un metaconfig per DSC che si applicano ai nodi. Per la corretta con questo valore, è necessario recuperare l'URL dell'endpoint e la chiave primaria per l'account di automazione selezionato in Azure. È possibile trovare questi valori nelle **chiavi** su e il **tutte le impostazioni** per l'account di automazione.

![Valori di chiave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

In questo esempio, si dispone di un server fisico di Windows Server 2012 R2 che si desidera proteggere usando il ripristino del sito.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Verificare la presenza di eventuali operazioni di ridenominazione di file del Registro di sistema sospeso

Prima di iniziare da associare il server endpoint DSC di automazione, è consigliabile controllare gli eventuali file Rinomina operazioni nel Registro di sistema sospeso. Sono potrebbe impedire la configurazione di completamento a causa di un riavvio in sospeso.

Eseguire il cmdlet seguente per verificare che non esiste alcun riavvio in sospeso nel server:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Se Mostra vuota, si sta OK per continuare. In caso contrario, è necessario risolvere questo problema riavviando il server durante una finestra di manutenzione.

Per applicare la configurazione sul server, avviare dell'ambiente di Scripting integrato PowerShell (ISE) ed eseguire il seguente script. Si tratta essenzialmente una configurazione locale di DSC che indichi il motore di Gestione configurazione locale per eseguire la registrazione con il servizio di automazione DSC e recuperare la configurazione specifica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Questa configurazione si verificherà il motore di Gestione configurazione locale di registrarsi con DSC automazione. Viene determinato anche funzionamento il motore, cosa deve fare se esiste una deviazione della configurazione (ApplyAndAutoCorrect) e come procedere con la configurazione se è necessario riavviare.

Dopo aver eseguito questo script, il nodo deve iniziare la registrazione con DSC automazione.

![Registrazione di nodo in corso](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se si torna al portale di Azure, è possibile vedere che il nodo appena registrato ora erano presenti nel portale.

![Nodo registrato nel portale](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Sul server, è possibile eseguire il seguente cmdlet di PowerShell per verificare che il nodo è stato registrato in modo corretto:

```powershell
Get-DscLocalConfigurationManager
```

Dopo la configurazione è stata recapitata e applicata al server, è possibile verificarlo eseguendo il seguente cmdlet:

```powershell
Get-DscConfigurationStatus
```

L'output mostra che il server è estratta correttamente la configurazione:

![Output](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Inoltre, la configurazione del servizio mobilità ha un proprio log sono disponibili in \ProgramData\ASRSetupLogs *SystemDrive*.

Questo è tutto. Si dispone ora correttamente distribuita e registrato il servizio di mobilità nei computer che si desidera proteggere usando il ripristino del sito. DSC verrà assicurarsi che i servizi necessari siano sempre in esecuzione.

![Distribuzione corretta](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Dopo che il server di gestione rileva la corretta distribuzione, è possibile configurare la protezione e abilitare la replica sul computer utilizzando il ripristino del sito.

## <a name="use-dsc-in-disconnected-environments"></a>Utilizzare DSC in ambienti disconnessi

Se il computer non sono connessi a Internet, non è ancora possibile utilizzare DSC per distribuire e configurare il servizio di mobilità in carichi di lavoro che si desidera proteggere.

È possibile creare un'istanza di server pull DSC nel proprio ambiente essenzialmente fornire le stesse capacità ottenuta da DSC automazione. Ovvero i client di ottenere la configurazione (dopo la registrazione) all'endpoint DSC. Tuttavia, è inoltre possibile inserire manualmente configurazione DSC per i computer locale o remoto.

Si noti che in questo esempio, un parametro aggiunto per il nome del computer. I file remoti a questo punto si trovano in una condivisione remota deve essere accessibile dal computer in cui si desidera proteggere. Fine dello script attivarne la configurazione e quindi inizia applicare la configurazione di DSC al computer di destinazione.

### <a name="prerequisites"></a>Prerequisiti

Assicurarsi che sia installato il modulo di PowerShell xPSDesiredStateConfiguration. Per i computer Windows in cui è installato WMF 5.0, è possibile installare il modulo xPSDesiredStateConfiguration eseguendo il seguente cmdlet nei computer di destinazione:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

È anche possibile scaricare e salvare il modulo nel caso in cui è necessario essere distribuito ai computer Windows in cui sono WMF 4.0. Eseguire questo cmdlet in un computer in cui è presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Anche per 4.0 WMF, assicurarsi che il computer sia installato [Windows 8.1 aggiornare KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) .

Al computer Windows in cui sono WMF 4.0 e 5.0 WMF, è possono inserire le seguenti operazioni:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Se si desidera creare il proprio server pull DSC nella rete aziendale per simulare le funzionalità che è possibile ottenere da DSC automazione, vedere [impostazione di un server di pull web DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Facoltativo: Distribuire una configurazione DSC utilizzando un modello di gestione risorse di Azure

In questo articolo è basato su come è possibile creare la propria configurazione DSC per distribuire il servizio di mobilità e l'agente di macchine Virtuali di Azure - automaticamente e assicurarsi che sono in esecuzione nel computer in cui si desidera proteggere. Abbiamo anche un modello di gestione di risorse Azure che consente di distribuire questa configurazione DSC a un account di automazione di Azure nuovo o esistente. Il modello verrà utilizzato parametri di input per creare attività di automazione contenenti le variabili per l'ambiente.

Dopo che si distribuisce il modello, è possibile semplicemente fare riferimento al passaggio 4 in questa guida per incorporata computer.

Il modello verrà eseguire le operazioni seguenti:

1. Usare un account di automazione esistente o crearne uno nuovo
2. Scrivere i parametri di input per:
    - ASRRemoteFile, ossia il percorso in cui sono memorizzati la configurazione del servizio di mobilità
    - ASRPassphrase, ossia il percorso in cui sono memorizzati il file passphrase.txt
    - ASRCSEndpoint: l'indirizzo IP del server di gestione
3. Importare il modulo di PowerShell xPSDesiredStateConfiguration
4. Creare e compilare la configurazione di DSC

Tutti i passaggi precedenti verranno visualizzato nell'ordine corretto, in modo che è possibile iniziare onboarding i computer per la protezione.

Il modello, con le istruzioni per la distribuzione, è disponibile nel [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Distribuire il modello tramite PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito gli agenti di servizi di mobilità, è possibile [abilitare la replica](site-recovery-vmware-to-azure.md#step-6-replicate-applications) per le macchine virtuali.
