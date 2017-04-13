<properties 
   pageTitle="La connessione remota al dispositivo StorSimple | Microsoft Azure"
   description="Viene illustrato come configurare il dispositivo per la gestione remota e come connettersi a Windows PowerShell per StorSimple tramite HTTP o HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>La connessione remota al dispositivo StorSimple

## <a name="overview"></a>Panoramica

È possibile utilizzare Windows PowerShell remota per connettersi al dispositivo StorSimple. Quando ci si connette in questo modo, non verrà visualizzato un menu. (È visualizzato un menu solo se si utilizza la console seriale sul dispositivo per la connessione). Con Windows PowerShell remota, connettersi a uno specifico spazio di esecuzione. È inoltre possibile specificare la lingua di visualizzazione. 

Per ulteriori informazioni sull'uso di servizi remoti di Windows PowerShell per gestire il dispositivo, passare a [Usare Windows PowerShell per StorSimple amministrare il dispositivo StorSimple](storsimple-windows-powershell-administration.md).

In questa esercitazione viene illustrato come configurare il dispositivo per la gestione remota e quindi come connettersi a Windows PowerShell per StorSimple. È possibile utilizzare HTTP o HTTPS per connettersi tramite Windows PowerShell remota. Tuttavia, per decidere come connettersi a Windows PowerShell per StorSimple, tenere presente quanto segue: 

- La connessione direttamente alla console seriale dispositivo è sicura, ma che si connettono a console seriale switch di rete non è. Prestare attenzione dei rischi per la sicurezza quando ci si connette alla console seriale dispositivo su parametri di rete. 

- Connessione tramite una sessione HTTP potrebbe offrire maggiore sicurezza rispetto alla connessione tramite la console seriale sulla rete. Anche se non è il metodo più sicuro, è accettabile su reti attendibili. 

- La connessione tramite una sessione HTTPS con un certificato autofirmato è più sicura e l'opzione consigliata.

È possibile connettersi in remoto all'interfaccia di Windows PowerShell. Accesso remoto al dispositivo StorSimple tramite l'interfaccia di Windows PowerShell disabilitata per impostazione predefinita. È necessario attivare la gestione remota sul dispositivo prima di tutto e quindi sul client usato per accedere a un dispositivo.

I passaggi descritti in questo articolo sono stati eseguiti in un sistema di host che eseguono Windows Server 2012 R2.

## <a name="connect-through-http"></a>Connettersi tramite HTTP

La connessione a Windows PowerShell per StorSimple tramite una sessione HTTP offre maggiore sicurezza rispetto alla connessione tramite la console seriale del dispositivo StorSimple. Anche se non è il metodo più sicuro, è accettabile su reti attendibili.

È possibile utilizzare il portale classico Azure o console seriale per configurare la gestione remota. Selezionare una delle procedure seguenti:

- [Utilizzare il portale classico Azure per abilitare la gestione remota su HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Utilizzare la console seriale per abilitare la gestione remota su HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Dopo aver attivato Gestione remota, utilizzare la procedura seguente per preparare il client per una connessione remota.

- [Preparare il client per la connessione remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Utilizzare il portale classico Azure per abilitare la gestione remota su HTTP 

Nel portale di classica Azure per abilitare la gestione remota su HTTP, procedere come segue.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Per attivare la gestione remota tramite il portale classica Azure

1. Accedere ai **dispositivi** > **Configura** del dispositivo.

2. Scorrere fino alla sezione **Gestione remota** .

3. Impostare **Consenti gestione remota** su **Sì**.

4. A questo punto è possibile connettersi tramite HTTP. (Il valore predefinito è di connettersi tramite HTTPS). Verificare che HTTP sia selezionata.

    >[AZURE.NOTE] Connessione su HTTP è accettabile solo su reti attendibili.

6. Fare clic su **Salva** nella parte inferiore della pagina.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilizzare la console seriale per abilitare la gestione remota su HTTP

Nella console seriale di dispositivo per abilitare la gestione remota, procedere come segue.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Per attivare la gestione remota tramite la console seriale dispositivo

1. Nel menu console seriali, selezionare l'opzione 1. Per ulteriori informazioni sull'utilizzo di console seriale nel dispositivo, passare a [connettersi a Windows PowerShell per StorSimple tramite console seriale dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Al prompt dei comandi digitare:`Enable-HcsRemoteManagement –AllowHttp`

3. Riceverà notifica vulnerabilità della protezione tramite HTTP per connettersi al dispositivo. Quando richiesto, confermare digitando **Y**.

4. Verificare che HTTP sia abilitata digitando:`Get-HcsSystem`

5. Verificare che il campo **RemoteManagementMode** Mostra **HttpsAndHttpEnabled**. L'illustrazione seguente mostra queste impostazioni in PuTTY.

     ![Seriale HTTPS che HTTP attivata](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparare il client per la connessione remota

Eseguire la procedura seguente sul client per abilitare la gestione remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Per preparare il client per la connessione remota

1. Avviare una sessione di PowerShell di Windows come amministratore.

2. Digitare il comando seguente per aggiungere l'indirizzo IP del dispositivo StorSimple all'elenco di host attendibili del client: 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Sostituire <*device_ip*> con l'indirizzo IP del dispositivo; Per esempio: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Digitare il comando seguente per salvare le credenziali di dispositivo in una variabile di tipo: 

     *$cred = get-Credential*

4. Nella finestra di dialogo che viene visualizzato:

    1. Digitare il nome utente in questo formato: *device_ip\SSAdmin*.
    2. Digitare la password di amministratore di dispositivo che è stata impostata quando il dispositivo è stato configurato con la configurazione guidata. La password predefinita è *Password1*.

7. Avviare una sessione di Windows PowerShell sul dispositivo digitando questo comando:

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Per creare una sessione di Windows PowerShell per l'utilizzo con il dispositivo virtuale StorSimple, aggiungere il `–Port` parametro e specificare la porta pubblica configurati in remoto per dispositivo virtuale StorSimple.

     A questo punto, è necessario disporre di una sessione di Windows PowerShell remota attiva al dispositivo.

    ![Gestione remota di PowerShell tramite HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Connettersi tramite HTTPS

Connettersi a Windows PowerShell per StorSimple tramite una sessione HTTPS è il metodo più sicuro e consigliato della connessione remota al dispositivo Microsoft Azure StorSimple. Le procedure seguenti illustrano come configurare i computer client e console seriali in modo che è possibile utilizzare HTTPS per connettersi a Windows PowerShell per StorSimple.

È possibile utilizzare il portale classico Azure o console seriale per configurare la gestione remota. Selezionare una delle procedure seguenti:

- [Utilizzare il portale classico Azure per abilitare la gestione remota su HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Utilizzare la console seriale per abilitare la gestione remota su HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Dopo aver attivato Gestione remota, usare le procedure seguenti per preparare l'ambiente host per una gestione remota e connettere il dispositivo dall'host remoto.

- [Preparare l'host per la gestione remota](#prepare-the-host-for-remote-management)

- [Connettere il dispositivo dall'host remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Utilizzare il portale classico Azure per abilitare la gestione remota su HTTPS

Nel portale Azure classico per abilitare la gestione remota su HTTPS, procedere come segue.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Per attivare la gestione remota su HTTPS dal portale di classica Azure

1. Accedere ai **dispositivi** > **Configura** del dispositivo.

2. Scorrere fino alla sezione **Gestione remota** .

3. Impostare **Consenti gestione remota** su **Sì**.

4. A questo punto è possibile connettersi tramite HTTPS. (Il valore predefinito è di connettersi tramite HTTPS). Assicurarsi che sia selezionata HTTPS. 

5. Fare clic su **Scarica certificato di gestione remota**. Specificare un percorso per salvare il file. È necessario installare il certificato del computer client o host che verrà utilizzato per connettersi al dispositivo.

6. Fare clic su **Salva** nella parte inferiore della pagina.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilizzare la console seriale per abilitare la gestione remota su HTTPS

Nella console seriale di dispositivo per abilitare la gestione remota, procedere come segue.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Per attivare la gestione remota tramite la console seriale dispositivo

1. Nel menu console seriali, selezionare l'opzione 1. Per ulteriori informazioni sull'utilizzo di console seriale nel dispositivo, passare a [connettersi a Windows PowerShell per StorSimple tramite console seriale dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Al prompt dei comandi digitare: 

     `Enable-HcsRemoteManagement`

    Questa operazione deve abilitare HTTPS nel dispositivo.

3. Verificare che sia stato attivato HTTPS digitando: 

     `Get-HcsSystem`

    Assicurarsi che il campo **RemoteManagementMode** Mostra **HttpsEnabled**. L'illustrazione seguente mostra queste impostazioni in PuTTY.

     ![HTTPS seriale abilitata](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. Dall'output di `Get-HcsSystem`, copiare il numero seriale del dispositivo e salvarla per successivi utilizzi.

    >[AZURE.NOTE] Il numero seriale associato al nome CN del certificato.

5. Ottenere un certificato di gestione remota digitando: 
 
     `Get-HcsRemoteManagementCert`

    Verrà visualizzato un certificato simile al seguente.

    ![Ottenere il certificato di gestione remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copiare le informazioni del certificato **---inizio certificato---** **---** interrompere certificato---un editor di testo, ad esempio Blocco note e salvarlo come file. cer. (File verrà copiato questo per l'host remoto quando si prepara l'host.)

    >[AZURE.NOTE] Per generare un nuovo certificato, utilizzare la `Set-HcsRemoteManagementCert` cmdlet.

### <a name="prepare-the-host-for-remote-management"></a>Preparare l'host per la gestione remota

Per preparare il computer host per una connessione remota che utilizza una sessione HTTPS, eseguire le procedure seguenti:

- [Importare il file. cer nell'archivio radice del client o host remoto](#to-import-the-certificate-on-the-remote-host).

- [Aggiungere i numeri seriali di dispositivo per il file hosts sull'host remoto](#to-add-device-serial-numbers-to-the-remote-host).

Ognuna di queste procedure descritto di seguito.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Per importare il certificato dell'host remoto

1. Fare clic sul file CER e scegliere **Installa certificato**. Verrà avviata l'importazione guidata certificati.

    ![Importazione guidata certificati 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. **Posizione di archiviazione**, selezionare **Computer locale**e quindi fare clic su **Avanti**.

3. Selezionare **Mettere tutti i certificati nel seguente archivio**e quindi fare clic su **Sfoglia**. Passare all'archivio principale dell'host remoto e quindi fare clic su **Avanti**.

    ![Importazione guidata certificati 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Fare clic su **Fine**. Viene visualizzato un messaggio che indica che l'importazione è stata stabilita.

    ![Importazione guidata certificati 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Per aggiungere i numeri seriali dispositivo all'host remoto

1. Avviare Blocco note come amministratore e quindi aprire il file hosts in \Windows\System32\Drivers\etc.

2. Aggiungere le seguenti tre voci nel file hosts: **indirizzo IP dati 0**, **indirizzo IP fisso Controller 0**e **indirizzo IP fisso 1 Controller**.

3. Immettere il numero seriale dispositivo salvata in precedenza. Il mapping all'indirizzo IP come illustrato nell'immagine seguente. Per Controller 0 e 1 Controller, aggiungere **Controller0** e **Controller1** alla fine del numero di serie (nome NC).

    ![Aggiunta di CN nome al file hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Salvare il file hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Connettere il dispositivo dall'host remoto

Utilizzare Windows PowerShell e SSL per immettere una sessione di SSAdmin sul dispositivo da un client o host remoto. La sessione SSAdmin cartine all'opzione 1 nel menu [console seriale](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) del dispositivo.

Nel computer da cui si desidera effettuare la connessione remota di Windows PowerShell, procedere come segue.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Per immettere una sessione di SSAdmin sul dispositivo con Windows PowerShell e SSL

1. Avviare una sessione di PowerShell di Windows come amministratore.

2. Aggiungere l'indirizzo IP del dispositivo host attendibili del client digitando:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Dove <*device_ip*> è l'indirizzo IP del dispositivo; Per esempio: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Creare nuove credenziali digitando: 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Dove <*IP del dispositivo destinazione*> è l'indirizzo IP del dati 0 del dispositivo; ad esempio, **10.126.173.90** come illustrato nell'immagine precedente del file hosts. Inoltre, fornire la password dell'amministratore del dispositivo.

4. Creare una sessione digitando:

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Per il parametro - ComputerName nel cmdlet, fornire il <*numero seriale, del dispositivo di destinazione*>. Questo numero di serie è stato mappato all'indirizzo IP di dati 0 nel file hosts sull'host remoto; ad esempio, **SHX0991003G44MT** come illustrato nell'immagine seguente.

5. Tipo: 

     `Enter-PSSession $session`

6. Sarà necessario attendere alcuni minuti e quindi verrà connesso il dispositivo tramite HTTPS su SSL. Verrà visualizzato un messaggio che indica che si è connessi al dispositivo.

    ![PowerShell remota con HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni [sull'uso di Windows PowerShell per gestire il dispositivo StorSimple](storsimple-windows-powershell-administration.md).

- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
