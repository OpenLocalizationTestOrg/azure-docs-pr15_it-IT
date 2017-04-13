<properties
    pageTitle="Rimuovere server e disattivare la protezione | Microsoft Azure" 
    description="In questo articolo descrive come annullare server da un archivio il ripristino del sito e per disattivare la protezione per macchine virtuali e server fisici." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Rimuovere server e disattivare la protezione

Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

## <a name="overview"></a>Panoramica

Questo articolo descrive come annullare la registrazione server dall'archivio il ripristino del sito e su come disattivare la protezione per macchine virtuali protetto tramite il ripristino del sito. 

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="unregister-a-vmm-server"></a>Annullare la registrazione di un server VMM

Si annulla la registrazione di un server VMM da un archivio eliminando il server nella scheda **server** nel portale del ripristino del sito di Azure. Si noti che:

-  **Server connessi VMM**: È consigliabile si annulla la registrazione del server VMM quando è connesso a Azure. In questo modo che le impostazioni nel server VMM in locale e i server VMM associati (server VMM contenenti nuvole mappate alle aree nel server che si desidera eliminare) vengono cancellate correttamente. È consigliabile che rimuovere un server non è connesso solo se si verifica un problema permanente con la connettività.
- **Server VMM non connesso**: se il server VMM non è connesso quando si elimina è necessario eseguire uno script manualmente per eseguire la pulizia. Lo script è disponibile nella [Raccolta Microsoft](http://aka.ms/asr-cleanup-script-vmm). Annotare l'ID di VMM del server per completare il processo di pulizia manuale.
- **Server VMM cluster**: se è necessario annullare la registrazione di un server VMM che viene distribuito in un cluster di eseguire le operazioni seguenti:

    - Se il server di connesso, eliminare il server VMM connesso nella scheda **server** . Per disinstallare il Provider di sul server, accedere ogni servizio cluster e disinstallarle dal Pannello di controllo. Eseguire lo script di pulizia indicato nella sezione precedente in tutti i nodi passivi del cluster di eliminare voci di registrazione.
    - Se non è connesso al server è necessario eseguire lo script di pulizia su tutti i nodi.

### <a name="unregister-an-unconnected-vmm-server"></a>Annullare la registrazione di un server VMM non connesso

Sul server VMM si desidera rimuovere:

1. Annullare la registrazione server VMM dal portale di Azure.
2. Nel server VMM scaricare lo script di pulizia.
3. Aprire PowerShell con Esegui come amministratore per modificare i criteri di esecuzione per l'ambito predefinito (LocalMachine).
4. Seguire le istruzioni dello script. 

Nei server VMM con nuvole vengono combinati con nuvole sul server da rimuovere:

1. Eseguire lo script di pulizia e seguire i passaggi da 2 a 4.
2. Specificare l'ID di VMM per il server VMM che è stato annullato. 
3. Questo script comporta la rimozione di informazioni di registrazione per il Server VMM cloud informazioni di associazione.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Annullare la registrazione di un server di Hyper-V in un sito di Hyper-V

Quando il ripristino del sito di Azure viene distribuito per proteggere le macchine virtuali presenti in un server di Hyper-V in un sito di Hyper-V (con nessun server VMM) è possibile annullare la registrazione di un server di Hyper-V da un archivio come indicato di seguito:

1. Disattivare la protezione per macchine virtuali sul server Hyper-V.
2. Nella scheda **server** nel portale del ripristino del sito di Azure, selezionare il server > Elimina. Il server non deve essere connesso a Azure quando si esegue questa operazione.
3. Eseguire il seguente script per pulire le impostazioni del server e annullare la registrazione dall'archivio di. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Arrestare la protezione una macchina virtuale Hyper-V

Se si desidera interrompere la protezione una macchina virtuale Hyper-V è necessario rimuovere la protezione per renderla. Come si rimuove la protezione a seconda del tipo potrebbe essere necessario pulire le impostazioni di protezione manualmente nel computer. 

### <a name="remove-protection"></a>Rimuovere la protezione

1. Nella scheda **macchine virtuali** di proprietà cloud, selezionare la macchina virtuale > **Rimuovi**.
2. Nella pagina **confermare la rimozione di Virtual Machine** sono disponibili due opzioni:

    - **Disattivare la protezione**, ovvero se si abilita e Salva questa opzione non è più la macchina virtuale verrà protetto tramite il ripristino del sito. Impostazioni di protezione per la macchina virtuale vengono cancellate automaticamente.
    - **Rimuovere l'archivio**, ovvero se si seleziona questa opzione la macchina virtuale verrà rimosso solo dall'archivio il ripristino del sito. Non è possibile applicare le impostazioni di protezione locale per la macchina virtuale. È necessario pulire impostazioni manualmente per rimuovere le impostazioni di protezione e rimuovere la macchina virtuale dalla sottoscrizione Azure e rimuovere le impostazioni di protezione che è necessario eliminarle manualmente seguendo le istruzioni riportate di seguito.

Se si seleziona per eliminare la macchina virtuale e il relativo dischi rigidi verranno rimossi dalla posizione di destinazione.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Pulire impostazioni di protezione manualmente (tra siti VMM)

Se è selezionata l'opzione **Interrompi gestione la macchina virtuale**, pulire manualmente le impostazioni:

1. Nel server primario eseguire lo script dalla console di VMM per pulire le impostazioni per la macchina virtuale principale. Nella console di VMM fare clic sul pulsante di PowerShell per aprire la console di PowerShell VMM. Sostituire SQLVM1 con il nome del computer virtuale.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Nel server VMM secondario, eseguire lo script per pulire le impostazioni per la macchina virtuale secondaria:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. Dopo avere eseguito lo script server VMM secondario, aggiornare macchine virtuali sul server host Hyper-V in modo che il computer virtuale secondario ottiene nuovamente rilevati nella console di VMM.
4. La procedura sopra indicata eliminerà il server VMM replica solo le impostazioni. Se si desidera rimuovere la replica di macchina virtuale per la macchina virtuale. È necessario eseguire i passaggi in entrambe le principale e secondaria seguito macchine virtuali. Eseguire il seguente script per rimuovere la replica e sostituire SQLVM1 con il nome del computer virtuale.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Pulire le impostazioni di protezione manualmente (tra siti VMM locale e Azure)

1. Sul server VMM origine eseguire questo script per pulire le impostazioni per la macchina virtuale primaria:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. La procedura sopra indicata eliminerà il server VMM replica solo le impostazioni. Dopo la rimozione della replica dal server VMM garantire per rimuovere la replica per la macchina virtuale con il server di host Hyper-V con questo script. Sostituire SQLVM1 con il nome del computer virtuale e host01.contoso.com con il nome del server host Hyper-V.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Pulire le impostazioni di protezione manualmente (tra siti Hyper-V e Azure)

1. Nel server di host Hyper-V di origine, per rimuovere la replica per la macchina virtuale usare questo script. Sostituire SQLVM1 con il nome del computer virtuale.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Arrestare la protezione una macchina virtuale VMware o un server fisico

Se si desidera interrompere la protezione una macchina virtuale VMware o un server fisico è necessario rimuovere la protezione per renderla. Come si rimuove la protezione a seconda del tipo potrebbe essere necessario pulire le impostazioni di protezione manualmente nel computer. 

### <a name="remove-protection"></a>Rimuovere la protezione

1. Nella scheda **macchine virtuali** di proprietà cloud, selezionare la macchina virtuale > **Rimuovi**.
2. **Rimuovi macchina virtuale** selezionare una delle opzioni:

    - **Disattivare la protezione (utilizzare per ridimensionare drill-down e il volume di ripristino)**, è possibile solo visualizzare e abilitare questa opzione se si è:
        - **Resized volume macchina virtuale**, ovvero durante il ridimensionamento un volume la macchina virtuale entra nello stato critico. In questo caso selezionare questa opzione. Disabilita protezione conservando i punti di ripristino in Azure. Quando si riattiva la protezione per il computer i dati per il volume ridimensionato saranno trasferiti in Azure.
        - **Eseguire caso di errore**, ovvero dopo avere verificato l'ambiente eseguendo caso di errore da macchine virtuali VMware locale o server fisici in Azure, selezionare questa opzione per iniziare a proteggere nuovamente macchine virtuali locale. Questa opzione Disattiva ogni macchina virtuale e quindi è necessario riattivare la protezione per tali. Si noti che:
            - Disabilitare la macchina virtuale con questa impostazione non ha alcun effetto la macchina virtuale duplicata in Azure.
            - Su non disinstallare il servizio mobilità dalla macchina virtuale.
    
    - **Disattivare la protezione**, ovvero se si abilita e Salva questa opzione il computer non è più possibile protetto dal ripristino del sito. Impostazioni di protezione per il computer verranno cancellate automaticamente.
    - **Rimuovere l'archivio**, ovvero se si seleziona questa opzione la macchina verrà rimosso solo dall'archivio il ripristino del sito. Non è possibile applicare le impostazioni di protezione locale per il computer. Per rimuovere le impostazioni del computer e rimuovere la macchina virtuale i comuni abbonamento ed è necessario pulire le impostazioni per disinstallare il servizio di mobilità.
    
        ![Rimuovere le opzioni](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















