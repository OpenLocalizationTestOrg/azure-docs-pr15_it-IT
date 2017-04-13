<properties
    pageTitle="Ospitare una trascrizione sul sito Web di guide in una VM Linux | Microsoft Azure"
    description="Configurare e ospitare una trascrizione sul sito Web basate su Guide nella Azure utilizzando una macchina virtuale Linux."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Trascrizione in applicazione Web guide su una macchina virtuale Azure

In questa esercitazione viene illustrato come ospitare una trascrizione sul sito Web di guide in Azure utilizzando una macchina virtuale Linux.  

In questa esercitazione convalidata tramite Ubuntu Server 14.04 risultati. Se si usa una distribuzione Linux diversa, potrebbe essere necessario modificare le istruzioni visualizzate per installare Guide.

> [AZURE.IMPORTANT] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse e classica](../../../resource-manager-deployment-model.md).  In questo articolo è descritta utilizzando il modello di distribuzione classica. Si consiglia di più nuove distribuzioni di utilizzare il modello di Manager delle risorse.

## <a name="create-an-azure-vm"></a>Creare una macchina virtuale Azure

Iniziare creando una macchina virtuale Azure con un'immagine di Linux.

Per creare la macchina virtuale, è possibile usare il portale classico Azure o Azure interfaccia riga di comando (CLI).

### <a name="azure-management-portal"></a>Portale di gestione di Azure

1. Accedere al [portale classica Azure](http://manage.windowsazure.com)
2. Fare clic su **Nuovo** > **calcolare** > **macchina virtuale** > **Creazione rapida**. Selezionare un'immagine di Linux.
3. Immettere una password.

Dopo la macchina virtuale viene eseguito il provisioning, fare clic sul nome macchine Virtuali e fare clic su **Dashboard**. Trovare l'endpoint SSH elencato sotto **SSH dettagli**.

### <a name="azure-cli"></a>CLI Azure

Seguire la procedura descritta in [creare macchina virtuale in esecuzione Linux][vm-instructions].

Dopo la macchina virtuale viene eseguito il provisioning, è possibile ottenere l'endpoint SSH eseguendo il comando seguente:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installare trascrizione in Guide

1. Utilizzare SSH per connettere la macchina virtuale.

2. Da sessione SSH, utilizzare i comandi seguenti per installare trascrizione nella macchina virtuale:

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    L'installazione potrebbe richiedere alcuni minuti. Al termine, utilizzare il comando seguente per verificare che sia installato trascrizione:

        ruby -v

    Restituisce la versione di trascrizione è stato installato.

3. Usare il comando seguente per installare Guide:

        sudo gem install rails --no-rdoc --no-ri -V

    Utilizzare--flag rdoc n e - ri no per ignorare l'installazione della documentazione, è più veloce.
    Questo comando probabilmente richiederà molto tempo da eseguire, in modo aggiungendo -V verrà visualizzate le informazioni sullo stato dell'installazione.

## <a name="create-and-run-an-app"></a>Creare ed eseguire un'app

Mentre si è ancora connessi tramite SSH, eseguire i comandi seguenti:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

Il comando [Nuovo](http://guides.rubyonrails.org/command_line.html#rails-new) crea una nuova app Guide. Il comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) avvia il server web WEBrick fornito con guide. (Per l'utilizzo di produzione, probabilmente desiderate utilizzare un server diverso, ad esempio esclusive o passeggeri.)

È necessario vedere output simile al seguente.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Aggiunta di un estremo

1. Passare al [portale classica Azure] [ management-portal] e selezionare la macchina virtuale.

    ![elenco di macchina virtuale][vmlist]

2. Selezionare **i punti finali** nella parte superiore della pagina e quindi fare clic su **Aggiungi ENDPOINT +** nella parte inferiore della pagina.

    ![pagina endpoint][endpoints]

3. Nella finestra di dialogo **Aggiungi ENDPOINT** selezionare "Aggiungi un endpoint autonoma" e fare clic sulla freccia **Avanti** .

    ![finestra di dialogo nuovo endpoint][new-endpoint1]

3. Nella pagina successiva finestra di dialogo immettere le informazioni seguenti:

    * **Nome**: HTTP

    * **Protocollo**: TCP

    * **Porta pubblica**: 80

    * **Porta privata**: 3000

    Verrà creato una pubblica porte 80 che indirizza il traffico alla porta privato di 3000, in cui è in attesa server Guide.

    ![finestra di dialogo nuovo endpoint][new-endpoint]

4. Fare clic sul segno di spunta per salvare il punto finale.

5. Deve essere visualizzato un messaggio che indica **Lo stato di avanzamento di aggiornamento IN**. Una volta questo messaggio scomparirà, l'endpoint è attivo. A questo punto, è possibile testare l'applicazione passando al nome del computer virtuale DNS. Il sito Web dovrebbe essere simile al seguente:

    ![pagina Guide predefinita][default-rails-cloud]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si ha eseguito la maggior parte dei passaggi manualmente. In un ambiente di produzione, si vuole scrivere l'app in un computer di sviluppo e distribuire la macchina virtuale Azure. Inoltre, la maggior parte delle ambienti di produzione ospitano l'applicazione di guide in combinazione con un altro processo server, ad esempio Apache o NginX, quali punti di manipolazione di richiedere il routing a più istanze dell'applicazione Guide e utilizzo risorse statiche. Per ulteriori informazioni, vedere http://rubyonrails.org/deploy/.

Per ulteriori informazioni sulle trascrizione su Guide, visitare la [Trascrizione le guide Guide][rails-guides].

Per usare i servizi di Azure dall'applicazione di trascrizione, vedere:

* [Archiviare i dati non strutturati utilizzando BLOB][blobs]

* [Coppie chiave/valore archivio usando tabelle][tables]

* [Gestire contenuto elevata larghezza di banda di rete di distribuzione del contenuto][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
