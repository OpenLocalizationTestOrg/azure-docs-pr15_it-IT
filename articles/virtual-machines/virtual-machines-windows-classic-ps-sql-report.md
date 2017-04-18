<properties 
    pageTitle="Usare PowerShell per creare una macchina virtuale con un Server di Report in modalità nativa | Microsoft Azure"
    description="In questo argomento descrive e illustra le la distribuzione e la configurazione di un server di report di SQL Server Reporting Services in modalità nativa in una macchina virtuale Azure. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Usare PowerShell per creare una macchina virtuale Azure con un Server di Report in modalità nativa

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

In questo argomento descrive e illustra le la distribuzione e la configurazione di un server di report di SQL Server Reporting Services in modalità nativa in una macchina virtuale Azure. La procedura descritta in questo documento utilizza una combinazione di procedure manuali per creare la macchina virtuale con uno script di Windows PowerShell per configurare Reporting Services nella macchina virtuale. Script di configurazione include l'apertura di una porta firewall per HTTP o HTTPs.

>[AZURE.NOTE] Se non è necessaria **HTTPS** nel server di report, **ignorare il passaggio 2**.
>
>Dopo aver creato la macchina virtuale nel passaggio 1, passare alla sezione utilizza script per configurare il server di report e HTTP. Dopo avere eseguito lo script, è possibile utilizzare il server di report.

## <a name="prerequisites-and-assumptions"></a>Prerequisiti e ipotesi

- **Abbonamento Azure**: verificare il numero di core disponibili nel proprio abbonamento Azure. Se si crea la dimensione consigliata macchine Virtuali di **A3**, è necessario **4** core disponibili. Se si usa una dimensione macchine Virtuali di **A2**, è necessario core disponibili **2** .
    
    - Per verificare il limite di base del proprio abbonamento nel portale di classica Azure, fare clic su impostazioni nel riquadro sinistro e quindi fare clic su uso nel menu superiore.
    
    - Per aumentare la quota di base, contattare il [Supporto di Azure](https://azure.microsoft.com/support/options/). Per informazioni sulle dimensioni macchine Virtuali, vedere [Dimensioni macchina virtuale per Azure](virtual-machines-linux-sizes.md).

- **Gli script di Windows PowerShell**: l'argomento si presuppone una conoscenza base di Windows PowerShell. Per ulteriori informazioni sull'uso di Windows PowerShell, vedere le operazioni seguenti:

    - [Avviare Windows PowerShell in Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Guida introduttiva a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Passaggio 1: Effettuare il provisioning di una macchina virtuale Azure

1. Passare al portale di classica Azure.

1. Fare clic su **macchine virtuali** nel riquadro sinistro.

    ![macchine virtuali di Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Fare clic su **Nuovo**.

    ![pulsante nuovo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Fare clic su **dalla raccolta**.

    ![nuova macchina virtuale dalla raccolta](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Fare clic su **SQL Server 2014 RTM Standard: Windows Server 2012 R2** e quindi fare clic sulla freccia per continuare.

    ![successivo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Se è necessario i dati di Reporting Services basate su funzionalità di sottoscrizione, scegliere **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Per ulteriori informazioni su edizioni di SQL Server e caratteristiche supportate, vedere [Funzionalità supportate dalle edizioni di SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Nella pagina **configurazione della macchina virtuale** , modificare i campi seguenti:
                                    
    - Se sono presenti più di una **Data di rilascio di versione**, selezionare la versione più recente.
    
    - **Nome macchina virtuale**: il nome del computer viene usato anche nella successiva pagina di configurazione come il nome DNS servizio Cloud predefinito. Il nome DNS deve essere univoco attraverso il servizio di Azure. Si consiglia di configurare la macchina virtuale con un nome che viene descritto come quali la macchina virtuale per. Ad esempio ssrsnativecloud.
    
    - **Livello**: Standard
    
    - **Dimensione: A3** è la dimensione di macchine Virtuali consigliata per carichi di lavoro di SQL Server. Se una macchina virtuale viene utilizzata esclusivamente come un server di report, una dimensione macchine Virtuali di A2 è sufficiente a meno che il server di report si verifica un carico di lavoro di grandi dimensioni. Per informazioni sui prezzi macchine Virtuali, vedere [Macchine virtuali di prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/).
    
    - **Nuovo nome utente**: il nome viene creato un amministratore nella macchina virtuale.
    
    - **Nuova Password** e **Conferma**. Questa password viene utilizzata per il nuovo account di amministratore, è consigliabile che utilizzare una password complessa.
    
    - Fare clic su **Avanti**. ![successivo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Nella pagina successiva, modificare i campi seguenti:

    - **Servizio cloud**: selezionare **Crea un nuovo servizio Cloud**.
    
    - **Nome DNS del servizio cloud**: si tratta del nome DNS pubblico del servizio Cloud associato la macchina virtuale. Il nome predefinito è il nome digitato per il nome di macchine Virtuali. Se nei passaggi successivi dell'argomento, si crea un certificato SSL attendibile e quindi viene usato il nome del DNS per il valore di "**rilasciato a**" del certificato.
    
    - **Regione/affinità gruppo/virtuale rete**: scegliere la provincia più vicina agli utenti finali.
    
    - **Account di archiviazione**: usare un account di archiviazione generata automaticamente.
    
    - **Imposta la disponibilità**: Nessuno.
    
    - **Punti finali** Mantenere gli endpoint **Desktop remoto** e di **PowerShell** e quindi aggiungere endpoint HTTP o HTTPS, a seconda dell'ambiente.

        - **HTTP**: le porte predefinite pubblici e privati sono **80**. Si noti che se si utilizza una porta privata diverso da 80, modificare **$HTTPport = 80** dello script http.

        - **HTTPS**: le porte predefinite pubblici e privati sono **443**. Una procedura consigliata consiste nel modificare la porta privata e configurare il firewall e il server di report di utilizzare la porta privata. Per ulteriori informazioni sull'endpoint, vedere [come impostare la comunicazione con una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md). Si noti che se si usa una porta diversa da 443, modificare il parametro **$HTTPsport = 443** dello script HTTPS.
    
    - Fare clic su Avanti. ![successivo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Nell'ultima pagina della procedura guidata, mantenere il valore predefinito di **installare l'agente di macchine Virtuali** selezionato. La procedura descritta in questo argomento non utilizza l'agente di macchine Virtuali, ma se si prevede di mantenere questa macchina virtuale, l'agente di macchine Virtuali e le estensioni consentono di migliorare ha CM.  Per ulteriori informazioni sull'agente di macchine Virtuali, vedere [agente macchine Virtuali ed estensioni-parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Uno dell'annuncio estensioni installate predefinito in esecuzione è l'estensione "BGINFO" visualizzata sul desktop macchine Virtuali, informazioni di sistema, ad esempio IP interno e lo spazio libero.

1. Fare clic su completa. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. Lo **stato** della macchina virtuale Visualizza come **iniziale (Provisioning)** durante il processo di provisioning e viene visualizzato come **in esecuzione** quando la macchina virtuale è stato eseguito il provisioning e pronto per l'utilizzo.

## <a name="step-2-create-a-server-certificate"></a>Passaggio 2: Creare un certificato Server

>[AZURE.NOTE] Se non è necessaria HTTPS nel server di report, è possibile **ignorare il passaggio 2** e passare alla sezione **utilizza script per configurare il server di report e HTTP**. Utilizzare lo script HTTP per configurare rapidamente il server di report e il server di report sarà pronto per l'utilizzo.

Per utilizzare HTTPS nella macchina virtuale, è necessario un certificato SSL attendibile. A seconda dello scenario, è possibile usare uno dei due metodi seguenti:

- Un certificato SSL valido rilasciato da un'autorità di certificazione (CA) e attendibili da Microsoft. Certificati CA sono necessari per distribuire tramite il programma Microsoft Root Certificate. Per ulteriori informazioni su questo programma, vedere [Windows e Windows Phone 8 SSL programma Root Certificate (membro CA)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [Introduzione per il programma Microsoft Root Certificate](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Un certificato autofirmato. Certificati non sono consigliati per gli ambienti di produzione.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Usare un certificato creato da un'autorità di certificazione (CA) attendibile

1. **Richiedere un certificato server per il sito Web da un'autorità di certificazione**. 

    È possibile utilizzare la procedura guidata certificato Server Web per generare un file di richiesta di certificato (Certreq. txt) da inviare a un'autorità di certificazione o per generare una richiesta di un'autorità di certificazione online. Ad esempio, servizi certificati Microsoft in Windows Server 2012. A seconda del livello di garanzia offerto dal certificato server, è più giorni ad alcuni mesi autorità di certificazione per approvare la richiesta e inviare un file di certificato. 

    Per ulteriori informazioni sulla richiesta di certificati di server, vedere le operazioni seguenti: 
    
    - Utilizzare [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Strumenti di sicurezza per amministrare Windows Server 2012.

    [Strumenti di protezione per amministrare Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] Il campo **rilasciato** del certificato SSL attendibile deve essere uguale al **Nome DNS del servizio Cloud** è stato usato per la nuova macchina virtuale.

1. **Installare il certificato server sul server Web**. Il server Web è in questo caso la macchina virtuale che ospita il server di report e il sito Web viene creato nei passaggi successivi quando si configura Reporting Services. Per ulteriori informazioni sull'installazione il certificato server sul server Web tramite il componente aggiuntivo di certificato di MMC, vedere [installare un certificato Server](https://technet.microsoft.com/library/cc740068).
    
    Se si desidera utilizzare lo script incluso in questo argomento, per configurare il server di report, il valore dei certificati **identificazione personale** è necessario come parametro dello script. Vedere la sezione successiva per informazioni dettagliate su come ottenere l'identificazione del certificato digitale.

1. Assegnare il certificato server a server di report. Quando si configura il server di report, l'assegnazione viene completata nella sezione successiva.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Per utilizzare il certificato autofirmato macchine virtuali

Un certificato autofirmato è stato creato la macchina virtuale quando è stato eseguito il provisioning macchina virtuale. Il certificato ha lo stesso nome il nome VM DNS. Per evitare errori del certificato, è necessario che il certificato è attendibile in macchine Virtuali stesso e tutti gli utenti del sito.

1. Per rendere attendibile all'autorità di certificazione del certificato nella macchina virtuale locale, aggiungere il certificato **Autorità di certificazione radice attendibili**. Di seguito è riportato un riepilogo dei passaggi richiesti. Per istruzioni dettagliate su come attendibile autorità di certificazione, vedere [installare un certificato Server](https://technet.microsoft.com/library/cc740068).

    1. Dal portale di classica Azure, selezionare la macchina virtuale e fare clic su Connetti. A seconda della configurazione del browser, potrebbe essere richiesto di salvare un file con estensione rdp per connettere la macchina virtuale.
    
        ![connettersi a macchina virtuale azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Usare il nome utente macchine Virtuali, nome utente e la password che è configurato al momento della creazione della macchina virtuale. 
    
        Nell'immagine seguente, ad esempio, macchine Virtuali è denominato **ssrsnativecloud** e il nome utente è **testuser**.
        
        ![nome di accesso inlcudes macchine virtuali](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Eseguire mmc.exe. Per ulteriori informazioni, vedere [come: visualizzazione dei certificati con MMC](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. Nel menu **File** di applicazione console, aggiungere **lo snap-in** , selezionare **Account Computer** quando richiesto e fare clic su **Avanti**.
    
    1. Selezionare **Computer locale** per gestire e quindi fare clic su **Fine**.
    
    1. Fare clic su **Ok** e quindi espandere la **certificati - personali** nodi e quindi fare clic su **certificati**. Il certificato viene assegnato il nome della macchina virtuale DNS e termina con **cloudapp.net**. Pulsante destro del mouse sul nome di certificato e fare clic su **Copia**.
    
    1. Espandere il nodo **Autorità di certificazione radice attendibili** e quindi rapida **certificati** e quindi fare clic su **Incolla**.
    
    1. Per convalidare, fare doppio clic sul nome del certificato in **Autorità di certificazione attendibile** e verificare che non siano presenti errori e viene visualizzato il certificato. Se si desidera utilizzare lo script HTTPS incluso in questo argomento, per configurare il server di report, il valore dei certificati **identificazione personale** è necessario come parametro dello script. **Per ottenere il valore di identificazione personale**, eseguire le operazioni seguenti. È inoltre disponibile un campione di PowerShell per recuperare l'identificazione personale nella sezione [utilizza script per configurare il server di report e HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Fare doppio clic sul nome del certificato, ad esempio ssrsnativecloud.cloudapp.net.
        
        1. Fare clic sulla scheda **Dettagli** .
        
        1. Fare clic su **identificazione personale**. Il valore dell'identificazione digitale viene visualizzato nel campo dettagli, ad esempio a6 08 3c test f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
        
        1. Copiare l'identificazione digitale e salvare il valore per in un secondo momento o modificare lo script ora.
        
        1. (*) Prima di eseguire lo script, rimuovere gli spazi tra le coppie di valori. Ad esempio l'identificazione personale indicato prima sono a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Assegnare il certificato server a server di report. Quando si configura il server di report, l'assegnazione viene completata nella sezione successiva.

Se si utilizza un certificato SSL autofirmato, il nome del certificato corrisponde già hostname della macchina virtuale. Di conseguenza, il DNS del computer è già registrato globalmente ed è possibile accedervi da qualsiasi client.

## <a name="step-3-configure-the-report-server"></a>Passaggio 3: Configurare il Server di Report

In questa sezione illustra la configurazione della macchina virtuale come un server di report di Reporting Services in modalità nativa. Per configurare il server di report, è possibile utilizzare uno dei metodi seguenti:

- Utilizzare lo script per configurare il server di report

- Utilizzare Gestione configurazione per configurare il Server di Report.

Per informazioni più dettagliate, vedere la sezione [connettere la macchina virtuale e avviare Gestione configurazione Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Autenticazione Nota:** L'autenticazione di Windows è il metodo di autenticazione consigliato ed è l'autenticazione di Reporting Services predefinita. Solo gli utenti che possono essere configurati nella macchina virtuale possono accedere Reporting Services e assegnate ai ruoli di Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Utilizzare script per configurare il server di report e HTTP

Per utilizzare lo script di Windows PowerShell per configurare il server di report, eseguire la procedura seguente. La configurazione include HTTP, non HTTPS:

1. Dal portale di classica Azure, selezionare la macchina virtuale e fare clic su Connetti. A seconda della configurazione del browser, potrebbe essere richiesto di salvare un file con estensione rdp per connettere la macchina virtuale.

    ![connettersi a macchina virtuale azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Usare il nome utente macchine Virtuali, nome utente e la password che è configurato al momento della creazione della macchina virtuale. 

    Nell'immagine seguente, ad esempio, macchine Virtuali è denominato **ssrsnativecloud** e il nome utente è **testuser**.
    
    ![nome di accesso inlcudes macchine virtuali](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. In macchine Virtuali, aprire **Windows PowerShell ISE** con privilegi di amministratore. PowerShell ISE è installato per impostazione predefinita in Windows server 2012. Si consiglia di che utilizzare il ISE invece di una finestra di Windows PowerShell standard in modo che è possibile incollare lo script dell'ISE, modificare lo script e quindi eseguire lo script.

1. In Windows PowerShell ISE, fare clic sul menu **Visualizza** e quindi fare clic su **Mostra riquadro Script**.

1. Copiare il seguente script e incollare lo script nel riquadro dello script di Windows PowerShell ISE.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Se la macchina virtuale è stato creato con una porta HTTP diverso da 80, modificare il parametro $HTTPport = 80.

1. Lo script è attualmente configurato per Reporting Services. Se si desidera eseguire lo script per Reporting Services, modificare la parte di versione del percorso di spazio dei nomi da "v11" sull'istruzione Get-WmiObject.

1. Eseguire lo script.

**Convalida**: per verificare il funzionamento di funzionalità del server di report di base, vedere la sezione [verificare la configurazione](#verify-the-configuration) più avanti in questo argomento.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Utilizzare script per configurare il server di report e HTTPS

Per utilizzare Windows PowerShell per configurare il server di report, eseguire la procedura seguente. La configurazione include HTTPS, non HTTP.

1. Dal portale di classica Azure, selezionare la macchina virtuale e fare clic su Connetti. A seconda della configurazione del browser, potrebbe essere richiesto di salvare un file con estensione rdp per connettere la macchina virtuale.

    ![connettersi a macchina virtuale azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Usare il nome utente macchine Virtuali, nome utente e la password che è configurato al momento della creazione della macchina virtuale. 

    Nell'immagine seguente, ad esempio, macchine Virtuali è denominato **ssrsnativecloud** e il nome utente è **testuser**.

    ![nome di accesso inlcudes macchine virtuali](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. In macchine Virtuali, aprire **Windows PowerShell ISE** con privilegi di amministratore. PowerShell ISE è installato per impostazione predefinita in Windows server 2012. Si consiglia di che utilizzare il ISE invece di una finestra di Windows PowerShell standard in modo che è possibile incollare lo script dell'ISE, modificare lo script e quindi eseguire lo script.

1. Per abilitare gli script in esecuzione, eseguire il comando di Windows PowerShell seguente:

        Set-ExecutionPolicy RemoteSigned

    È quindi possibile eseguire le operazioni seguenti per verificare il criterio:

        Get-ExecutionPolicy

1. In **Windows PowerShell ISE**, fare clic sul menu **Visualizza** e quindi fare clic su **Mostra riquadro Script**.

1. Copiare il seguente script e incollarla nel riquadro dello script di Windows PowerShell ISE.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Modificare il parametro **$certificatehash** lo script:

    - Si tratta di un parametro **obbligatorio** . Se nei passaggi precedenti non è stata salvata il valore di certificato, utilizzare uno dei metodi seguenti per copiare il valore di hash certificato l'identificazione personale dei certificati.:

        In macchine Virtuali, aprire Windows PowerShell ISE ed eseguire il comando seguente:

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        L'output sarà simile al seguente. Se lo script restituisce una riga vuota, la macchina virtuale non dispone di un certificato configurato, ad esempio, [per utilizzare il certificato autofirmato macchine virtuali di](#to-use-the-virtual-machines-self-signed-certificate)vedere la sezione.
    
    OR
    
    - Nella macchina virtuale eseguire mmc.exe e quindi aggiungere **lo snap-in** .
    
    - Sotto il nodo **Autorità di certificazione attendibile** sul nome del certificato. Se si utilizza il certificato autofirmato della macchina virtuale, il certificato viene assegnato il nome della macchina virtuale DNS e termina con **cloudapp.net**.
    
    - Fare clic sulla scheda **Dettagli** .
    
    - Fare clic su **identificazione personale**. Il valore dell'identificazione digitale viene visualizzato nel campo dettagli, ad esempio messa a fuoco automatica 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
    
    - **Prima di eseguire lo script**, rimuovere gli spazi tra le coppie di valori. Ad esempio af1160b64b288d890a8212ff6ba9c3664f319048

1. Modificare il parametro **$httpsport** : 

    - Se si usa la porta 443 per l'endpoint HTTPS, non è necessario aggiornare questo parametro dello script. In caso contrario, usare il valore di porta selezionata quando è stato configurato l'endpoint privato HTTPS nella macchina virtuale.

1. Modificare il parametro **$DNSName** : 

    - Lo script è configurato per un certificato di caratteri jolly $DNSName = "+". Se non si non si desidera configurare per un'associazione di certificato con caratteri jolly, commento $DNSName ="+"e abilitare la riga seguente, il riferimento $DNSNAme completo, # # $DNSName="$server.cloudapp.net".

        Modificare il valore di $DNSName se non si desidera utilizzare virtuale del nome del computer DNS per Reporting Services. Se si utilizza il parametro, il certificato deve anche utilizzare questo nome e aver registrato il nome globalmente su un server DNS.

1. Lo script è attualmente configurato per Reporting Services. Se si desidera eseguire lo script per Reporting Services, modificare la parte di versione del percorso di spazio dei nomi da "v11" sull'istruzione Get-WmiObject.

1. Eseguire lo script.

**Convalida**: per verificare il funzionamento di funzionalità del server di report di base, vedere la sezione [verificare la configurazione](#verify-the-connection) più avanti in questo argomento. Per verificare il certificato associazione aprire un prompt dei comandi con privilegi di amministratore e quindi eseguire il comando seguente:

    netsh http show sslcert

Il risultato includerà le operazioni seguenti:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utilizzare Gestione configurazione per configurare il Server di Report

Se non si desidera eseguire lo script di PowerShell per configurare il server di report, seguire la procedura descritta in questa sezione per utilizzare Gestione configurazione di Reporting Services in modalità nativa per configurare il server di report.

1. Dal portale di classica Azure, selezionare la macchina virtuale e fare clic su Connetti. Usare il nome utente e la password che è configurato al momento della creazione della macchina virtuale.

    ![connettersi a macchina virtuale azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Eseguire Windows update e installare gli aggiornamenti per la macchina virtuale. Se è necessario riavviare la macchina virtuale, riavviare la macchina virtuale e riconnettersi a macchina virtuale dal portale di classica Azure.

1. Dal menu di avvio nella macchina virtuale, digitare **Reporting Services** e aprire **Gestione configurazione Reporting Services**.

1. Lasciare i valori predefiniti per **Il nome del Server** e **Istanza Server di Report**. Fare clic su **Connetti**.

1. Nel riquadro sinistro fare clic su **URL del servizio Web**.

1. Per impostazione predefinita, RS è configurato per la porta HTTP 80 con IP "Assegnato tutti". Per aggiungere HTTPS:

    1. Certificato **SSL**: selezionare il certificato da usare, ad esempio [nome macchina virtuale]. cloudapp.net. Se non esistono certificati sono elencati, vedere la sezione **passaggio 2: creare un certificato Server** per informazioni su come installare e considerare attendibile il certificato nella macchina virtuale.
    
    1. In **Porta SSL**: scegliere 443. Se è configurata l'endpoint privato HTTPS in macchine Virtuali con un'altra porta privata, usare il valore di seguito.
    
    1. Fare clic su **Applica** e attendere il completamento dell'operazione.

1. Nel riquadro sinistro fare clic su **Database**.

    1. Fare clic su **Modifica Databas**e.
    
    1. Fare clic su **Crea un nuovo database del server di report** e quindi fare clic su **Avanti**.
    
    1. Lasciare il **Nome del Server**predefinito: come macchina virtuale assegnare un nome e lasciare il **Tipo di autenticazione** predefinito come **Utente corrente** – **Sicurezza integrata**. Fare clic su **Avanti**.
    
    1. Lasciare il **Nome del Database** come **ReportServer** predefinito e fare clic su **Avanti**.
    
    1. Lasciare il **Tipo di autenticazione** come **Le credenziali del servizio** predefinito e fare clic su **Avanti**.
    
    1. Fare clic su **Avanti** nella pagina **Riepilogo** .
    
    1. Una volta completata la configurazione, fare clic su **Fine**.

1. Nel riquadro sinistro fare clic su **URL gestione Report**. Lasciare il valore predefinito **Directory virtuale** come **report** e fare clic su **Applica**.

1. Fare clic su **Esci** per chiudere la gestione configurazione di Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Passaggio 4: Aprire Windows Firewall la porta

>[AZURE.NOTE] Se si utilizza uno script per configurare il server di report, è possibile ignorare questa sezione. Lo script incluso un passaggio per aprire la porta del firewall. Il valore predefinito è stato porte 80 per HTTP e 443 per HTTPS.

Per connettersi in remoto a gestione Report o il Server di Report sul computer virtuale, è necessario un Endpoint TCP nella macchina virtuale. È necessario aprire la stessa porta nel firewall della macchina virtuale. Quando è stato eseguito il provisioning macchina virtuale è stato creato il punto finale.

In questa sezione fornisce informazioni di base su come aprire la porta del firewall. Per ulteriori informazioni, vedere [configurare un Firewall per accedere al Server Report](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Se lo script è stato utilizzato per configurare il server di report, è possibile ignorare questa sezione. Lo script incluso un passaggio per aprire la porta del firewall.

Se è configurata una porta privata per HTTPS diversa da 443, modificare il seguente script in modo appropriato. Per aprire la porta **443** di Windows Firewall, eseguire le operazioni seguenti:

1. Aprire una finestra di Windows PowerShell con privilegi di amministratore.

1. Se è stata utilizzata una porta diversa da quella 443 quando è stato configurato l'endpoint HTTPS nella macchina virtuale, aggiornare la porta nel comando seguente e quindi eseguire il comando:

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Il comando al termine, **Ok** viene visualizzato il prompt dei comandi.

Per verificare che la porta è aperta, aprire una finestra di Windows PowerShell ed eseguire il comando seguente:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verificare la configurazione

Per verificare se funziona la funzionalità del server di report di base, aprire il browser con privilegi di amministratore e quindi individuare il seguente report server report pubblicitario URL:

- In macchine Virtuali, individuare l'URL del server di report:

        http://localhost/reportserver

- In macchine Virtuali, passare all'URL di gestione di report:

        http://localhost/Reports

- Dal computer locale, individuare il report **remoto** Manager nella macchina virtuale. Aggiornare il nome DNS nell'esempio seguente in base alle esigenze. Quando viene richiesto di immettere una password, utilizzare le credenziali di amministratore creata quando è stato eseguito il provisioning macchina virtuale. Il nome utente è in [dominio]\[nome utente] formato, in cui il dominio è il nome del computer macchine Virtuali, ad esempio ssrsnativecloud\testuser. Se non si utilizza HTTP**S**, rimuovere **s** nell'URL. Vedere la sezione successiva per informazioni sulla creazione di altri utenti su macchine Virtuali.

        https://ssrsnativecloud.cloudapp.net/Reports

- Dal computer locale, individuare l'URL del server di report remoto. Aggiornare il nome DNS nell'esempio seguente in base alle esigenze. Se non si usa HTTPS, rimuovere gli oggetti nell'URL.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Creare utenti e assegnare i ruoli

Dopo la configurazione e verificare il server di report, un'attività amministrativa comune consiste nel creare uno o più utenti e assegnare utenti ai ruoli di Reporting Services. Per ulteriori informazioni, vedere le operazioni seguenti:

- [Creare un account utente locali](https://technet.microsoft.com/library/cc770642.aspx)

- [Concedere l'accesso a un Server di Report (gestione Report)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Creare e gestire le assegnazioni di ruolo](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Per creare e pubblicare report per la macchina virtuale Azure

Nella tabella seguente sono riepilogate alcune delle opzioni disponibili per pubblicare i rapporti esistenti da un computer locale al server di report ospitato in Microsoft Azure Virtual Machine:

- **Script RS.exe**: usare RS.exe script per copiare gli elementi del report da e il server di report esistente per il computer virtuali di Microsoft Azure. Per ulteriori informazioni, vedere la sezione "Modalità nativa alla modalità nativa-macchina virtuale di Microsoft Azure" [nell'esempio Reporting Services rs.exe Script per eseguire la migrazione di contenuto tra server di Report](https://msdn.microsoft.com/library/dn531017.aspx).

- **Generatore report**: la macchina virtuale include fare clic su-una volta sola versione di Generatore Report di Microsoft SQL Server. Per Report Generatore la prima data di inizio del computer virtuale:

    1. Avviare il browser con privilegi di amministratore.
    
    1. Passare alla gestione di report sul computer virtuale e fare clic su **Generatore Report** sulla barra multifunzione.

    Per ulteriori informazioni, vedere [installazione, il processo di disinstallazione e il supporto di Generatore Report](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools: macchine Virtuali**: se creato macchine Virtuali con SQL Server 2012, SQL Server Data Tools è installato nel computer virtuale e può essere utilizzato per creare **Progetti Server di Report** e report nel computer virtuale. SQL Server Data Tools possono pubblicare i report nel server di report sul computer virtuale.
    
    Se la macchina virtuale è stato creato con SQL server 2014, è possibile installare SQL Server dati strumenti - Business Intelligence per visual Studio. Per ulteriori informazioni, vedere le operazioni seguenti:

    - [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

    - [SQL Server Data Tools e SQL Server Business Intelligence (BI SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools: Remote**: nel computer locale, creare un progetto di Reporting Services in SQL Server Data Tools che contiene i report di Reporting Services. Configurare il progetto a cui connettersi l'URL del servizio web.

    ![proprietà del progetto SSDT per progetto SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Utilizza script**: usare script per copiare il contenuto del server di report. Per ulteriori informazioni, vedere [esempio Reporting Services rs.exe Script per eseguire la migrazione di contenuto tra server di Report](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Ridurre il costo se non si usa la macchina virtuale

>[AZURE.NOTE] Per ridurre a icona in base alle tariffe per le macchine virtuali di Azure quando non è in uso, arrestare la macchina virtuale dal portale di classica Azure. Se si utilizzano le opzioni di power Windows all'interno di una macchina virtuale per arrestare la macchina virtuale, ancora addebitate la stessa quantità per la macchina virtuale. Per ridurre in base alle tariffe, è necessario arrestare la macchina virtuale nel portale di classica Azure. Se non è più necessario macchina virtuale, è necessario eliminare la macchina virtuale e i file associati VHD per evitare i costi di spazio di archiviazione. Per ulteriori informazioni, vedere la sezione Domande frequenti su [macchine virtuali di prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/)dettagli.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="resources"></a>Risorse

- Per contenuto simile correlato a una distribuzione server singolo di Business Intelligence di SQL Server e SharePoint 2013, vedere [Usare Windows PowerShell per creare una macchina virtuale Azure con Business Intelligence di SQL Server e SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Per contenuto simile correlato a una distribuzione di più server di Business Intelligence di SQL Server e SharePoint 2013, vedere [Distribuire Business Intelligence di SQL Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/dn321998.aspx).

- Per informazioni generali relative alle distribuzioni di Business Intelligence di SQL Server in macchine virtuali di Azure, vedere [Business Intelligence di SQL Server in macchine virtuali di Azure](virtual-machines-windows-classic-ps-sql-bi.md).

- Per ulteriori informazioni sul costo di Azure calcolare addebiti, vedere la scheda macchine virtuali di [Azure speciale calcolatrice](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Contenuto della community

- Per istruzioni dettagliate su come creare un server di report in modalità Reporting Services nativo senza utilizzare script, vedere [Hosting SQL Reporting Services in Azure Virtual Machine](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Collegamenti ad altre risorse per SQL Server in macchine virtuali di Azure

[SQL Server in macchine virtuali Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md)
