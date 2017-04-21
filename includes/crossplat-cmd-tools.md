#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Come usare gli strumenti della riga di comando di Azure per Mac e Linux

Questa guida viene descritto come utilizzare gli strumenti della riga di comando di Azure per Mac e Linux per creare e gestire i servizi di Azure. Gli scenari coperti includono **installazione degli strumenti**, **importare le impostazioni di pubblicazione**, **la creazione e gestione di siti Web di Azure**e **creare e gestire macchine virtuali di Azure**. Per la documentazione della Guida di riferimento completa, vedere [Azure strumento della riga di comando per Mac e la documentazione Linux][reference-docs]. 

##<a name="table-of-contents"></a>Sommario
* [Quali sono gli strumenti della riga di comando di Azure per Mac e Linux](#Overview)
* [Come installare gli strumenti della riga di comando di Azure per Mac e Linux](#Download)
* [Come creare un account Azure](#CreateAccount)
* [Come scaricare e importare le impostazioni di pubblicazione](#Account)
* [Come creare e gestire un sito Web di Azure](#WebSites)
* [Come creare e gestire una macchina virtuale Azure](#VMs)


<h2><a id="Overview"></a>Quali sono gli strumenti della riga di comando di Azure per Mac e Linux</h2>

Gli strumenti della riga di comando di Azure per Mac e Linux sono un set di strumenti della riga di comando per la distribuzione e gestione dei servizi Azure.
 
Le attività supportate includono le seguenti:

* Importare le impostazioni di pubblicazione.
* Creare e gestire siti Web di Azure.
* Creare e gestire macchine virtuali di Azure.

Per un elenco completo di comandi supportati, digitare `azure -help` nella riga di comando dopo l'installazione degli strumenti, o vedere la [documentazione di riferimento][reference-docs].

<h2><a id="Download">Come installare gli strumenti della riga di comando di Azure per Mac e Linux</a></h2>

Di seguito sono riportate informazioni per l'installazione gli strumenti della riga di comando, a seconda del sistema operativo:

* **Mac**: scaricare il [programma di installazione di Azure SDK][mac-installer]. Aprire il file scaricato pkg e completare la procedura di installazione come richiesto.

* **Linux**: installare la versione più recente di [Node] [ nodejs-org] (vedere [Installare Node tramite Gestione pacchetti][install-node-linux]), quindi eseguire il comando seguente:

        npm install azure-cli -g

    **Nota**: potrebbe essere necessario eseguire il comando con privilegi elevati:

        sudo npm install azure-cli -g

* **Windows**: eseguire il programma di installazione di Winows (con estensione msi), che è disponibile qui: [Strumenti di riga di comando Azure][windows-installer].


Per testare l'installazione, digitare `azure` al prompt dei comandi. Se l'installazione è stata completata, verrà visualizzato un elenco di tutti disponibili `azure` comandi.

<h2><a id="CreateAccount"></a>Come creare un account Azure</h2>

Per utilizzare gli strumenti della riga di comando di Azure per Mac e Linux, è necessario un account Azure.

Aprire un web browser e passare alla [http://www.windowsazure.com] [ windowsazuredotcom] e fare clic su **versione di valutazione gratuita** nell'angolo superiore destro.

![Sito Web Azure][Azure Web Site]

Seguire le istruzioni per la creazione di un account.

<h2><a id="Account"></a>Come scaricare e importare le impostazioni di pubblicazione</h2>

Per iniziare, è necessario prima scaricare e importare le impostazioni di pubblicazione. In questo modo sarà possibile utilizzare gli strumenti per creare e gestire i servizi di Windows Azure. Per scaricare le impostazioni di pubblicazione, utilizzare la `account download` comando:

    azure account download

Per aprire il browser predefinito e viene richiesto di accedere al portale di gestione. Dopo l'accesso, il `.publishsettings` file verrà scaricate. Prendere nota in cui è salvato il file.

Successivamente, importare la `.publishsettings` file eseguendo il comando seguente sostituendo `{path to .publishsettings file}` con il percorso il `.publishsettings` file:

    azure account import {path to .publishsettings file}

È possibile rimuovere tutte le informazioni memorizzate dalla <code>import</code> comando utilizzando il <code>account clear</code> comando:

    azure account clear

Per visualizzare un elenco delle opzioni per `account` comandi, utilizzare la `-help` opzione:

    azure account -help

Dopo aver importato le impostazioni di pubblicazione, è necessario eliminare il `.publishsettings` file per motivi di sicurezza.

> [AZURE.NOTE] Quando si importano le impostazioni di pubblicazione, vengono memorizzate le credenziali per l'accesso a abbonamento Azure all'interno del `user` cartella. Il `user` cartella è protetta dal sistema operativo. Tuttavia, si consiglia di eseguire altre operazioni aggiuntive per crittografare il `user` cartella. È possibile farlo nei modi seguenti:    
> 
> - In Windows, modificare le proprietà della cartella o utilizzare BitLocker.
> - Nel Mac, attivare FileVault per la cartella.
> - In Ubuntu, utilizzare la funzionalità di crittografia Home directory. Altre distribuzioni Linux offrono funzionalità equivalenti.

A questo punto si è pronti per da creare e gestire siti Web di Azure e macchine virtuali di Azure.  

<h2><a id="WebSites"></a>Come creare e gestire un sito Web di Azure</h2>

###<a name="create-a-website"></a>Creare un sito Web

Per creare un sito Web di Azure, prima di tutto creare una directory vuota denominata `MySite` e individuare nella directory.

Eseguire il comando seguente:

    azure site create MySite --git

L'output di questo comando conterrà l'URL predefinito per il sito Web appena creato. Il `--git` opzione consente di utilizzare fra per pubblicare il sito Web tramite la creazione di archivi fra in entrambe le directory dell'applicazione locale e nel centro di dati del sito Web. Si noti che se nella cartella locale è già un repository fra, il comando aggiunge una nuova connessione remota all'archivio esistente, che punta al repository nell'interfaccia di dati del sito Web.

Si noti che è possibile eseguire il `azure site create` comando con una delle opzioni seguenti:

* `--location [location name]`. Questa opzione consente di specificare la posizione del data center in cui viene creato il sito Web (ad esempio "Ovest Usa"). Se si omette questa opzione, si promted per scegliere un percorso.
* `--hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.

È quindi possibile aggiungere contenuto alla directory sito Web. Utilizzare il flusso fra regolari (`git add`, `git commit`) per eseguire il commit del contenuto. Usare il comando seguente fra per inserire il contenuto del sito Web in Azure: 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Configurare la pubblicazione da GitHub

Per configurare la pubblicazione continua da un repository GitHub, utilizzare la `--GitHub` opzione durante la creazione di un sito:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se si dispone di un duplicato locale di un repository GitHub o se si dispone di un repository con un solo riferimento remoto a un repository GitHub, questo comando automaticamente pubblicare codice repository GitHub al sito. In poi, eventuali modifiche inserite all'archivio GitHub automaticamente pubblicate al sito.

Quando si configura la pubblicazione da GitHub, ramo predefinito utilizzato è master. Per specificare una diversa diramazione, eseguire il comando seguente dal proprio archivio locale:

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Configurare le impostazioni dell'app

Le impostazioni dell'App sono coppie di valori chiave che sono disponibili per l'applicazione in fase di esecuzione. Quando è impostata per un sito Web di Azure, i valori delle impostazioni app le impostazioni verranno sostituite con la stessa chiave definite nel file config del sito. Per le applicazioni di Node e PHP, le impostazioni dell'app sono disponibili come variabili. Nell'esempio seguente viene illustrato come impostare una coppia di valore di chiave:

    azure site config add <key>=<value> 

Per visualizzare un elenco di tutti coppie chiave/valore, utilizzare le operazioni seguenti:

    azure site config list 

Oppure, se si conosce la chiave e si desidera visualizzare il valore, è possibile usare:

    azure site config get <key> 

Se si desidera modificare il valore di una chiave esistente è necessario deselezionare innanzitutto la chiave esistente e quindi aggiungerlo nuovamente. Il comando Cancella è:

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Elenco e visualizzare i siti

Per elencare i siti Web, utilizzare il comando seguente:

    azure site list

Per ottenere informazioni dettagliate su un sito, utilizzare la `site show` comando. Nell'esempio seguente mostra i dettagli relativi `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Interrompere, avviare o riavviare un sito

Interrompere, avviare o riavviare un sito con il `site stop`, `site start`, o `site restart` comandi:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Eliminare un sito

Infine, è possibile eliminare un sito con il `site delete` comando:

    azure site delete MySite

Si noti che se si esegue una delle sopra comandi da all'interno della cartella in cui è stato eseguito `site create`, non è necessario specificare il nome del sito `MySite` come ultimo parametro.

Per visualizzare un elenco completo dei `site` comandi, utilizzare la `-help` opzione:

    azure site -help 

<h2><a id="VMs"></a>Come creare e gestire una macchina virtuale Azure</h2>

viene creata una macchina virtuale Azure da un'immagine di macchina virtuale (file con estensione vhd) che forniscono o che sono disponibili nella raccolta di immagini. Per visualizzare le immagini che sono disponibili, utilizzare la `vm image list` comando:

    azure vm image list

È possibile eseguire il provisioning e avviare una macchina virtuale da una delle immagini disponibili con la `vm create` comando. Nell'esempio seguente viene illustrato come creare una macchina virtuale Linux (denominata `myVM`) da un'immagine nella raccolta di immagini (CentOS 6.2). Il nome utente principale e la password per la macchina virtuale sono `myusername` e `Mypassw0rd` rispettivamente. (Si noti che la `--location` parametro specifica il centro di dati in cui viene creata la macchina virtuale. Se si omette il `--location` parametro, verrà richiesto di scegliere un percorso.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

È possibile prendere in considerazione passando il `--ssh` contrassegno (Linux) o `--rdp` contrassegno (Windows) `vm create` per consentire le connessioni remote alla macchina virtuale appena creato.

Se si preferisca da effettuare il provisioning di una macchina virtuale da un'immagine personalizzata, è possibile creare un'immagine da un file con estensione vhd con la `vm image create` comando, quindi usare il `vm create` comando per eseguire il provisioning la macchina virtuale. Nell'esempio seguente viene illustrato come creare un'immagine Linux (denominata `myImage`) da un file con estensione vhd locale. (La `--location` parametro specifica i dati in cui è archiviato l'immagine.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Invece di creare un'immagine da un VHD locale, è possibile creare un'immagine da un VHD archiviati in archiviazione Blob Azure. È possibile eseguire questa operazione con la `blob-url` parametro:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Dopo la creazione di un'immagine, è possibile effettuare il provisioning di una macchina virtuale dall'immagine utilizzando `vm create`. Il comando seguente crea una macchina virtuale denominata `myVM` dall'immagine creata in precedenza (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Dopo avere completato il provisioning di una macchina virtuale, può essere necessario creare endpoint per consentire l'accesso remoto al computer virtuale (ad esempio). Nell'esempio seguente viene utilizzato il `vm create endpoint` comando per aprire esterni 22 locale porta e 22 in `myVM`:

    azure vm endpoint create myVM 22 22

È possibile ottenere informazioni dettagliate su una macchina virtuale (inclusi indirizzo IP, nome DNS e le informazioni sull'endpoint) con la `vm show` comando:

    azure vm show myVM

Per arrestare, iniziare, o riavviare il computer virtuale, utilizzare uno dei comandi seguenti:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

E infine per eliminare la macchina virtuale, utilizzare la `vm delete` comando:

    azure vm delete myVM

Per un elenco completo di comandi per creare e gestire macchine virtuali, utilizzare la `-h` opzione:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

