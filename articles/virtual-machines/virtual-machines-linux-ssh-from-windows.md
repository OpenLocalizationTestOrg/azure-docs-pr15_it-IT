<properties 
    pageTitle="Utilizzare i tasti SSH con Windows per macchine virtuali Linux | Microsoft Azure" 
    description="Informazioni su come generare e utilizzare SSH tasti in un computer Windows per connettersi a una macchina virtuale Linux in Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Come usare SSH tasti con Windows su Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

Quando ci si connette a Linux macchine () in Azure, è necessario utilizzare [la crittografia di chiave pubblica](https://wikipedia.org/wiki/Public-key_cryptography) in modo più sicuro agli utenti di accedere alle VM Linux. Questo processo prevede uno scambio di chiavi pubblico e privatehttp utilizzando il comando shell sicura (SSH) per eseguire l'autenticazione se stessi anziché un nome utente e password. Le password sono soggette a tentativi di attacchi, in particolare in macchine virtuali esposto a Internet, ad esempio server web. In questo articolo viene fornita una panoramica di chiavi SSH e come generare chiavi appropriate in un computer Windows.


## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e chiavi

È possibile registrare in modo sicuro per le VM Linux utilizzando i tasti pubblici e privati:

- **Chiave pubblica** viene inserita la VM Linux o altri servizi che si desiderano utilizzare con crittografia a chiave pubblica.
- La **chiave privata** è quali la presentazione per la VM Linux quando si effettua, per verificare l'identità. Proteggere la chiave privata. Non condividerlo.

Queste chiavi pubbliche e privatehttp possono essere usate su più macchine virtuali e servizi. Non è necessaria una coppia di chiavi per ogni macchine Virtuali o servizio che si desidera accedere. Per informazioni più dettagliate, vedere [crittografia a chiave pubblica](https://wikipedia.org/wiki/Public-key_cryptography).

SSH è un protocollo di connessione crittografata che consente l'accesso sicuro su connessioni non sicure. È il protocollo di connessione predefinito per le macchine virtuali Linux ospitato in Azure. Anche se SSH stesso fornisce una connessione crittografata, uso delle password con le connessioni SSH comunque il macchina virtuale esposto a tentativi di attacchi o di individuazione delle password. Un metodo più sicuro e preferito, di connessione a una macchina virtuale utilizzando SSH è utilizzando le chiavi pubbliche e private, noto anche come SSH tasti.

Se non si desidera utilizzare i tasti di SSH, è possibile comunque accedere nelle macchine virtuali Linux con una password. Se la macchina virtuale non viene esposto a Internet, l'uso delle password può essere sufficiente. Tuttavia, è comunque necessario gestire le password per ogni VM Linux e gestire i criteri password corretti e procedure consigliate, ad esempio la lunghezza minima e aggiornarle regolarmente. L'utilizzo di chiavi SSH riduce la complessità della gestione credenziali individuali tra più macchine virtuali.


## <a name="windows-packages-and-ssh-clients"></a>Pacchetti di Windows e i client SSH

Per connettersi e gestire macchine virtuali Linux in Azure tramite un client **ssh** . Computer Windows non è in genere un client **ssh** installato. Comuni SSH Windows client che è possibile installare sono inclusi in pacchetti seguenti:

- [Operazioni per Windows](https://git-for-windows.github.io/)
- [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] Il più recente di Windows 10 anniversario Update include Bash per Windows. Questa caratteristica consente di eseguire il sottosistema di Windows per l'accesso o Linux utilità, ad esempio un client SSH. Bash per Windows è ancora in fase di sviluppo ed è considerato una versione beta. Per ulteriori informazioni su Bash per Windows, vedere [Bash su Ubuntu su Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>Quali file chiave è necessario creare?

Azure richiede almeno 2048 bit, **ssh rsa** formattare chiavi pubbliche e private. Se si gestiscono risorse Azure usando il modello di distribuzione classica, occorre anche generare un modulo PEM (`.pem` file).

Ecco gli scenari di distribuzione e i tipi di file da utilizzare in ogni:

1. chiavi **SSH rsa** sono necessari per la distribuzione tramite il [portale di Azure](https://portal.azure.com)e distribuzioni Manager delle risorse utilizzando [CLI Azure](../xplat-cli-install.md).
    - Questi tasti sono in genere che necessario tutti maggior parte delle persone.
2. `.pem`file è necessario creare macchine virtuali tramite il [portale classica](https://manage.windowsazure.com). Questi tasti sono supportati anche nelle distribuzioni classica che utilizzano [CLI Azure](../xplat-cli-install.md).
    - È necessario solo creare questi tasti aggiuntivi e certificati se si gestiscono le risorse create utilizzando il modello di distribuzione classica.


## <a name="install-git-for-windows"></a>Installare fra per Windows

Nella sezione precedente elencati diversi pacchetti che includono la `openssl` strumento per Windows. Questo strumento è necessario creare chiavi pubbliche e private. Negli esempi seguenti viene illustrato come installare e usare **Fra per Windows**, anche se è possibile scegliere qualsiasi pacchetto si preferisce. **Operazioni per Windows** consente di accedere ad alcuni strumenti open source software ([SharePoint Server](https://en.wikipedia.org/wiki/Open-source_software)) e utilità che può essere utile mentre si lavora con macchine virtuali Linux.

1. Scaricare e installare **Fra per Windows** da uno dei seguenti percorsi: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Accettare le opzioni predefinite durante il processo di installazione solo se è necessario modificarle.

3. Eseguire **Operazioni Bash** dal **Menu Start** > **fra** > **Fra Bash**. Console di sarà simile alla seguente:

    ![Operazioni per Windows Bash shell](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Creare una chiave privata

1. Nella finestra del **Bash fra** utilizzare `openssl.exe` per creare una chiave privata. Nell'esempio seguente viene creata una chiave denominata `myPrivateKey` e certificato denominato `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    L'output sarà simile alla seguente:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Rispondere alle istruzioni visualizzate per paese nome, percorso, nome dell'organizzazione, e così via.

3. La nuova chiave privata e certificato vengono creati nella directory di lavoro corrente. Per le procedure consigliate, è necessario impostare le autorizzazioni per la chiave privata in modo che solo è possibile accedervi:

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Se occorre anche gestire le risorse classica, convertire il `myCert.pem` a `myCert.cer` (X509 codificato in DER certificato). Eseguire questo passaggio facoltativo solo se è necessario gestire specificamente risorse classica precedenti. 

    Convertire il certificato utilizzando il comando seguente:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Creare una chiave privata per PuTTY

PuTTY è un client SSH comune per Windows. Si può utilizzare qualsiasi client SSH che si desidera. Per utilizzare PuTTY, è necessario creare un'ulteriore tipo di chiave: una chiave privata PuTTY (PPK). Se non si desidera utilizzare PuTTY, ignorare questa sezione.

Nell'esempio seguente viene creato questa chiave privata aggiuntiva specificamente per PuTTY da usare:

1. Utilizzare **Fra Bash** per convertire la chiave privata in una chiave privata RSA riconoscibili PuTTYgen. Nell'esempio seguente viene creata una chiave denominata `myPrivateKey_rsa` da chiave esistente denominata `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Per le procedure consigliate, è necessario impostare le autorizzazioni per la chiave privata in modo che solo è possibile accedervi:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Scaricare ed eseguire PuTTYgen da uno dei seguenti percorsi: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Fare clic sul menu: **File** > **carico una chiave privata**

4. Individuare la chiave privata (`myPrivateKey_rsa` dell'esempio precedente). La directory predefinita quando si avvia **Fra Bash** è `C:\Users\%username%`. Modificare il filtro di file per visualizzare **tutti i file (\*.\*)**:

    ![Caricare la chiave privata esistente in PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Fare clic su **Apri**. Un messaggio indica che la chiave è stata importata correttamente:

    ![Tasto importato correttamente per PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Fare clic su **OK** per chiudere la richiesta.

7. Chiave pubblica viene visualizzata nella parte superiore della finestra di **PuTTYgen** . Copiare e incollare questa chiave pubblica il portale di Azure o il modello di gestione di risorse Azure quando si crea una VM Linux. È anche possibile fare clic su **Salva chiave pubblica** per salvare una copia nel computer in uso:

    ![Salvare un file di chiave pubblica PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    Nell'esempio seguente mostra come da copiare e incollare questa chiave pubblica nel portale di Azure quando si crea una VM Linux. Chiave pubblica in genere è quindi archiviata in `~/.ssh/authorized_keys` nella nuova macchina virtuale.

    ![Utilizzare la chiave pubblica quando si crea una macchina virtuale nel portale di Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. Di nuovo in **PuTTYgen**, fare clic su **Salva chiave privata**:

    ![Salvare il file di chiave privata PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] Un viene richiesto se si desidera continuare senza immettere una frase per trovare il codice. Una frase è simile a una password per la chiave privata associata. Anche se un utente utilizzato per ottenere la chiave privata, vengono comunque non sarebbe autenticati solo la chiave. È inoltre necessario la passphrase. Senza una frase, se un utente ottiene la chiave privata possono registrare qualsiasi macchine Virtuali o servizio che utilizza tale tasto. È consigliabile che creare una frase. Tuttavia, se si dimentica la passphrase, è possibile recuperarlo.

    Se si desidera immettere una frase, fare clic su **No**, immettere una frase nella finestra principale di PuTTYgen e quindi scegliere **Salva chiave privata** . In caso contrario, fare clic su **Sì** per continuare senza fornire la passphrase facoltativa.

8. Immettere un nome e un percorso per salvare il file PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Utilizzare Putty a SSH a un computer Linux

Anche PuTTY è un client SSH comune per Windows. Si può utilizzare qualsiasi client SSH che si desidera. La procedura seguente viene illustrato come utilizzare la chiave privata per eseguire l'autenticazione con le macchine Virtuali Azure utilizzando SSH. I passaggi sono simili in altri client chiave SSH in termini di dover caricare la chiave privata per autenticare la connessione SSH.

1. Download e l'esecuzione putty da uno dei seguenti percorsi: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Specificare il nome host o l'indirizzo IP delle macchine Virtuali dal portale di Azure:

    ![Apri nuova connessione PuTTY](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Prima di selezionare **aperto**, fare clic su **connessione** > **SSH** > scheda**Auth** . Individuare e selezionare la chiave privata:

    ![Selezionare la chiave privata PuTTY per l'autenticazione](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Fare clic su **Apri** per connettere la macchina virtuale
 

## <a name="next-steps"></a>Passaggi successivi
È inoltre possibile generare chiavi pubbliche e private [utilizzando OS X e Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Per ulteriori informazioni su Bash per Windows e illustra i vantaggi derivanti strumenti OSS immediatamente disponibili in un computer Windows, vedere [Bash su Ubuntu su Windows](https://msdn.microsoft.com/commandline/wsl/about).

Nel caso di problemi con l'uso SSH a cui connettersi nelle macchine virtuali Linux, vedere [risolvere i problemi di SSH connessioni a una macchina virtuale Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).