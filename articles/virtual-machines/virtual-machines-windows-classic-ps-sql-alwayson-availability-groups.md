<properties
    pageTitle="Configurare sempre nel gruppo di disponibilità in macchine Virtuali di Azure con PowerShell"
    description="In questa esercitazione utilizza risorse create con il modello di distribuzione classica e PowerShell per creare un sempre nel gruppo di disponibilità in Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Configurare sempre nel gruppo di disponibilità in macchine Virtuali di Azure con PowerShell

> [AZURE.SELECTOR]
- [Manager delle risorse: modello](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Manager delle risorse: manuale](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure macchine () consente agli amministratori di database per implementare inferiore il costo di un sistema di SQL Server di disponibilità. In questa esercitazione viene illustrato come implementare un gruppo di disponibilità con SQL Server sempre in-to-end all'interno di un ambiente Azure. Al termine dell'esercitazione, la soluzione SQL Server sempre attiva in Azure è costituita dagli elementi seguenti:

- Una rete virtuale che contiene più subnet, tra cui front-end e una subnet back-end

- Un controller di dominio con un dominio di Active Directory (AD)

- Due macchine virtuali Server SQL distribuito alla subnet back-end e al dominio di Active Directory

- Un cluster WSFC 3 nodi con il modello di base della maggior parte nodo

- Un gruppo di disponibilità con due icona del commit di un database di disponibilità

Questo scenario scelto per la semplicità, non per l'efficacia dei costi o altri fattori in Azure. Ad esempio, è possibile ridurre il numero di macchine virtuali per un gruppo di replica di due disponibilità per risparmiare in ore di elaborazione di Azure utilizzando controller di dominio come il riferimento di condivisione file di base in un cluster WSFC nodo 2. Questo metodo riduce il conteggio di macchine Virtuali di una parte della configurazione precedente.

In questa esercitazione viene usata per mostrare i passaggi necessari per configurare la soluzione descritta in precedenza senza spiegazione i dettagli di ogni passaggio. Pertanto, anziché con i passaggi di configurazione di grafica, utilizza PowerShell gli script per accedere rapidamente a informazioni dettagliate. Si presuppone che le operazioni seguenti:

- Si dispone già di un account Azure con l'abbonamento macchina virtuale.

- È stato installato i [cmdlet di PowerShell Azure](../powershell-install-configure.md).

- Si dispone già di dubbio gruppi sempre sulla disponibilità per le soluzioni in locale. Per ulteriori informazioni, vedere [Sempre nella disponibilità gruppi (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Connettersi al proprio abbonamento Azure e creare la rete virtuale

1. Nella finestra di PowerShell nel computer locale, importare il modulo di Azure, scaricare un file di Impostazioni pubblicazione nel computer in uso e connettere la sessione di PowerShell all'abbonamento Azure importando le impostazioni di pubblicazione scaricate.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Il comando **Get-AzurePublishgSettingsFile** genera automaticamente una gestione certificati con Azure lo scarica nel computer in uso. Viene aperto automaticamente un browser e viene chiesto di immettere le credenziali dell'account Microsoft per l'abbonamento Azure. Il file scaricato **publishsettings** contiene tutte le informazioni necessarie per gestire l'abbonamento Azure. Dopo aver salvato il file in una directory locale, l'importazione mediante il comando **Importa AzurePublishSettingsFile** .

    >[AZURE.NOTE] Il file publishsettings contiene le credenziali (chiaro) utilizzati per amministrare le sottoscrizioni Azure e i servizi. Procedura consigliata per questo file consiste nel memorizzare temporaneamente di fuori la directory di origine (ad esempio nella cartella Libraries\Documents) e quindi eliminarla una volta completata l'importazione. Un utente non autorizzato nuovo l'accesso al file publishsettings può modificare, creare ed eliminare il provider di servizi Azure.

1. Definire una serie di variabili che verrà utilizzato per creare il cloud infrastruttura IT.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Prestare attenzione alle seguenti per verificare che i comandi vengono eseguita correttamente in un secondo momento:

    - Variabili **$storageAccountName** e **$dcServiceName** devono essere univoci perché sono utilizzati per identificare il cloud account e cloud server di archiviazione, rispettivamente, su internet.

    - Nomi specificati per variabili **$affinityGroupName** e **$virtualNetworkName** sono configurati nel documento di configurazione virtuali che verrà utilizzato in un secondo momento.

    - **$sqlImageName** specifica il nome aggiornato dell'immagine macchina virtuale che contiene SQL Server 2012 Service Pack 1 Enterprise Edition.

    - Per semplicità, **Contoso! 000** è la stessa password utilizzata nel corso dell'esercitazione intera.

1. Creare un gruppo di affinità.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Creare una rete virtuale importando un file di configurazione.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    File di configurazione contiene il seguente documento XML. In breve, specifica una rete virtuale denominata **ContosoNET** nel gruppo affinità chiamato **ContosoAG**e dispone di spazio di indirizzi **10.10.0.0/16** e ha due subnet, **10.10.1.0/24** e **10.10.2.0/24**subnet anteriore e indietro subnet, rispettivamente. Dove è possibile posizionare le applicazioni client, ad esempio Microsoft SharePoint e subnet indietro nel punto in cui viene inserito macchine virtuali di Server SQL anteriore iè. Se si modificano le variabili **$affinityGroupName** e **$virtualNetworkName** in precedenza, è necessario modificare anche i nomi corrispondenti riportata di seguito.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. Creare un account di archiviazione associato al gruppo di affinità creato e impostarla come account di archiviazione corrente nell'abbonamento.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Creare il server controller di dominio in nuovo set di disponibilità e servizio cloud.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Questa serie di comandi reindirizzati eseguire le operazioni seguenti:

    - **Nuovo AzureVMConfig** crea una configurazione macchina virtuale.

    - **Aggiungi AzureProvisioningConfig** restituisce i parametri di configurazione di un server di Windows autonomo.

    - **Aggiungi AzureDataDisk** aggiunge il disco di dati da utilizzare per l'archiviazione dei dati di Active Directory, con la memorizzazione nella cache opzione impostata su Nessuno.

    - **Nuovo AzureVM** crea un nuovo servizio cloud e Crea nuova macchina virtuale Azure nel nuovo servizio cloud.

1. Attendere che la nuova macchina virtuale completamente il provisioning e scaricare il file desktop remoto per la cartella di lavoro. Poiché la nuova macchina virtuale Azure richiede molto tempo a disposizione, mentre il ciclo continua a sondaggio nuova macchina virtuale fino a quando non è pronto per l'utilizzo.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Il server controller di dominio a questo punto è configurato correttamente. Punto, configurare il dominio di Active Directory sul server controller di dominio. Lasciare aperta la finestra di PowerShell nel computer locale. Utilizzare in un secondo momento per creare le macchine virtuali due Server SQL.

## <a name="configure-the-domain-controller"></a>Configurare il Controller di dominio

1. Connettersi al server controller di dominio per avviare il file desktop remoto. Usare AzureAdmin nome utente e la password dell'amministratore del computer **Contoso! 000**, quale specificato quando si crea nuova macchina virtuale.

1. Aprire una finestra di PowerShell in modalità di amministratore.

1. Eseguire le operazioni seguenti **DCPROMO. File EXE** comando per la configurazione del dominio **corp.contoso.com** , con le directory dei dati sull'unità M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Dopo avere completato il comando, la macchina virtuale riavvia automaticamente.

1. Connettersi al server controller di dominio nuovamente avviando il file desktop remoto. In questo caso, accedere come **CORP\Administrator**.

1. Aprire una finestra di PowerShell in modalità di amministratore e importare il modulo di Active Directory PowerShell utilizzando il comando seguente:

        Import-Module ActiveDirectory

1. Eseguire i comandi seguenti per aggiungere tre utenti al dominio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** viene utilizzato per configurare tutti i dati correlati per le istanze di servizio SQL Server, cluster WSFC e il gruppo di disponibilità. **CORP\SQLSvc1** e **CORP\SQLSvc2** vengono utilizzati come account del servizio SQL Server per le macchine virtuali di due SQL Server.

1. Successivamente, eseguire i comandi seguenti per assegnare le autorizzazioni per creare oggetti computer nel dominio **CORP\Install** .

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    GUID specificato sopra è il GUID per il tipo di oggetto computer. L'account **CORP\Install** richiede l'autorizzazione di **Lettura tutte le proprietà** e **Creazione di oggetti Computer** per creare gli oggetti Active diretta per il cluster WSFC. L'autorizzazione di **Lettura tutte le proprietà** è già stato assegnato a CORP\Install per impostazione predefinita, in modo che non è necessaria concedere in modo esplicito. Per ulteriori informazioni sulle autorizzazioni necessarie per creare il cluster WSFC, vedere [Guida dettagliata Failover: configurazione di account in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Dopo aver completato la configurazione di Active Directory e gli oggetti utente, creare macchine virtuali di due SQL Server e per il dominio, aggiungere i.

## <a name="create-the-sql-server-vms"></a>Creare macchine virtuali di Server SQL

1. Continuare a usare la finestra di PowerShell aperto nel computer locale. Definire le variabili aggiuntive seguenti:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    L' indirizzo IP **10.10.0.4** in genere viene assegnato alla macchina virtuale prima di creare subnet **10.10.0.0/16** della rete virtuale Azure. È necessario verificare che questo è l'indirizzo del server controller di dominio eseguendo **IPCONFIG**.

1. Eseguire i comandi reindirizzati seguenti per creare il primo macchine Virtuali del cluster WSFC denominato **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Tenere presente quanto segue riguardanti il comando precedente:

    - **Nuovo AzureVMConfig** crea una configurazione macchina virtuale con il nome del set di disponibilità desiderati. Macchine virtuali successive verranno create con lo stesso nome di set di disponibilità, in modo che in cui vengono aggiunti allo stesso set di disponibilità.

    - **Aggiungi AzureProvisioningConfig** unisce la macchina virtuale per il dominio di Active Directory che è stato creato.

    - **Set AzureSubnet** inserisce la macchina virtuale in subnet indietro.

    - **Nuovo AzureVM** crea un nuovo servizio cloud e Crea nuova macchina virtuale Azure nel nuovo servizio cloud. Il parametro **DnsSettings** specifica che il server DNS per il server di nuovo servizio cloud ha indirizzo IP **10.10.0.4**, che corrisponde all'indirizzo IP del server controller di dominio. Questo parametro è necessario attivare il nuove macchine virtuali nel servizio cloud a partecipare al dominio Active Directory correttamente. Senza questo parametro, è necessario impostare manualmente le impostazioni di IPv4 nelle macchine Virtuali di utilizzare il server controller di dominio come server DNS primario dopo la macchina virtuale viene eseguito il provisioning e quindi aggiungere la macchina virtuale al dominio Active Directory.

1. Eseguire i comandi reindirizzati seguenti per creare il Server SQL macchine virtuali, denominati **ContosoSQL1** e **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Tenere presente quanto segue relative ai comandi sopra:

    - **Nuovo AzureVMConfig** utilizza lo stesso nome di set di disponibilità del server controller di dominio e viene utilizzata l'immagine di SQL Server 2012 Service Pack 1 Enterprise Edition nella raccolta macchina virtuale. Imposta inoltre il disco di sistema operativo a lettura memorizzazione nella cache solo (senza la cache di scrittura). Si consiglia di eseguire la migrazione dei file di database a un disco dati distinto connettere la macchina virtuale e configurarlo con non letto o scrivere la memorizzazione nella cache. Tuttavia, il modo migliore successivo consiste nel rimuovere la cache di scrittura su disco del sistema operativo, poiché non è possibile rimuovere tale cache su disco del sistema operativo.

    - **Aggiungi AzureProvisioningConfig** unisce la macchina virtuale per il dominio di Active Directory che è stato creato.

    - **Set AzureSubnet** inserisce la macchina virtuale in subnet indietro.

    - **Aggiungi AzureEndpoint** aggiunge i punti finali di accesso in modo che le applicazioni client possono accedere le istanze di servizi di SQL Server su internet. Porte diverse vengono fornite al ContosoSQL1 e ContosoSQL2.

    - **Nuovo AzureVM** crea la nuova macchina virtuale di SQL Server lo stesso servizio cloud come ContosoQuorum. Se si desidera che siano nello stesso set di disponibilità, è necessario inserire le macchine virtuali nel servizio cloud stesso.

1. Attendere che ogni macchina virtuale completamente il provisioning e scaricare il file desktop remoto per la cartella di lavoro. Il ciclo ciclicamente le tre nuove macchine virtuali e vengono eseguiti i comandi all'interno delle parentesi graffa principale per ognuno di essi.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    A questo punto viene completato il provisioning macchine virtuali SQL Server e in esecuzione, ma vengono installate con SQL Server con le opzioni predefinite.

## <a name="initialize-the-wsfc-cluster-vms"></a>Inizializzare macchine virtuali di Cluster WSFC

In questa sezione, è necessario modificare i tre server che si userà il cluster WSFC e installazione di SQL Server. In particolare:

- (Tutti i server) È necessario installare la funzionalità **Failover cluster** .

- (Tutti i server) È necessario aggiungere **CORP\Install** come l' **amministratore**del sistema.

- (ContosoSQL1 e ContosoSQL2 solo) È necessario aggiungere **CORP\Install** come ruolo **sysadmin** nel database predefinito.

- (ContosoSQL1 e ContosoSQL2 solo) È necessario aggiungere **Il sistema operativo NT** come account di accesso con le autorizzazioni seguenti:

    - Modificare un gruppo di disponibilità

    - Connettere SQL

    - Visualizzazione dello stato

- (ContosoSQL1 e ContosoSQL2 solo) Il protocollo **TCP** è già abilitato nella macchina virtuale SQL Server. Tuttavia, è comunque necessario aprire il firewall per l'accesso remoto di SQL Server.

A questo punto, si è pronti iniziare. A partire da **ContosoQuorum**, seguire la procedura seguente:

1. Connettersi a **ContosoQuorum** avviando file del desktop remoti. Usare **AzureAdmin** nome utente e la password dell'amministratore del computer **Contoso! 000**, che è specificato quando si creano macchine virtuali.

1. Verificare che il computer si sono aggiunti a **corp.contoso.com**.

1. Attendere che l'installazione di SQL Server completare l'esecuzione delle attività inizializzazione automatizzati prima di continuare.

1. Aprire una finestra di PowerShell in modalità di amministratore.

1. Installare la funzionalità cluster Failover di Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Aggiungere **CORP\Install** come amministratore locale.

        net localgroup administrators "CORP\Install" /Add

1. Disconnettersi da ContosoQuorum. Termine con il server ora.

        logoff.exe

Successivamente, inizializzare **ContosoSQL1** e **ContosoSQL2**. Eseguire la procedura seguente, che sono identici per entrambe le macchine SQL Server.

1. Connettersi a macchine virtuali di Server SQL due avviando file del desktop remoti. Usare **AzureAdmin** nome utente e la password dell'amministratore del computer **Contoso! 000**, che è specificato quando si creano macchine virtuali.

1. Verificare che il computer si sono aggiunti a **corp.contoso.com**.

1. Attendere che l'installazione di SQL Server completare l'esecuzione delle attività inizializzazione automatizzati prima di continuare.

1. Aprire una finestra di PowerShell in modalità di amministratore.

1. Installare la funzionalità cluster Failover di Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Aggiungere **CORP\Install** come amministratore locale

        net localgroup administrators "CORP\Install" /Add

1. Importare il Provider di PowerShell SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Aggiungere **CORP\Install** equivalenti al ruolo sysadmin per l'istanza di SQL Server predefinito.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Aggiungere **Il sistema operativo NT** come account di accesso con le tre autorizzazioni descritte in precedenza.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Aprire il firewall per l'accesso remoto di SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Disconnettersi da entrambe le macchine virtuali.

        logoff.exe

Infine, si è pronti configurare il gruppo di disponibilità. Utilizzare il Provider di PowerShell di SQL Server per eseguire le operazioni sui **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurare il gruppo di disponibilità

1. Connettersi a **ContosoSQL1** nuovamente avviando file del desktop remoti. Invece di accesso con l'account del computer, effettuare l'accesso utilizzando **CORP\Install**.

1. Aprire una finestra di PowerShell in modalità di amministratore.

1. Definire le variabili seguenti:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. Importare il Provider di PowerShell SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Cambiare l'account di servizio SQL Server per ContosoSQL1 a CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Cambiare l'account di servizio SQL Server per ContosoSQL2 a CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Scaricare **CreateAzureFailoverCluster.ps1** da [Creare Cluster WSFC sempre sulla disponibilità per i gruppi di in macchine Virtuali di Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) nella cartella di lavoro locale. Utilizzare questo script che consentono di creare un cluster WSFC funziona. Per informazioni importanti sulla modalità di interazione di WSFC con la rete Azure, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

1. Modificare la cartella di lavoro e creare il cluster WSFC con lo script scaricato.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Abilita sempre in gruppi disponibilità per le istanze di SQL Server predefinito su **ContosoSQL1** e **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Creare una directory di backup e concedere le autorizzazioni per gli account del servizio di SQL Server. Utilizzare questa directory per preparare il database di disponibilità della replica secondario.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Creare un database in **ContosoSQL1** denominata **MyDB1**, eseguire un backup completo e il backup del registro e ripristinarli in **ContosoSQL2** con l'opzione **WITH NORECOVERY** .

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Creare la disponibilità di endpoint di gruppo in macchine virtuali SQL Server e impostare le autorizzazioni appropriate per i punti finali.

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Creare repliche disponibilità.

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. Infine, creare il gruppo di disponibilità e partecipare replica secondaria al gruppo di disponibilità.

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>Passaggi successivi
È stato ora correttamente implementato SQL Server sempre attive mediante la creazione di un gruppo di disponibilità in Azure. Per configurare un comunicare ascoltatore per il gruppo di disponibilità, vedere [configurare un comunicare ascoltatore ILB sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
