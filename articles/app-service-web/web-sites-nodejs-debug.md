<properties
    pageTitle="Come eseguire il debug di un'app web Node nel servizio App Azure"
    description="Informazioni su come eseguire il debug di un'app web Node in Azure App servizio."
    tags="azure-portal"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Come eseguire il debug di un'app web Node nel servizio App Azure

Azure offre diagnostica per agevolare il debug di applicazioni Node ospitate in [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. In questo articolo verranno fornite informazioni su come abilitare la registrazione di stdout e stderr, visualizzare informazioni sugli errori nel browser e come scaricare e visualizzare i file di log.

Diagnostica per le applicazioni Node Azure viene fornita da [IISNode]. Mentre in questo articolo vengono illustrate le impostazioni più comuni per raccogliere informazioni di diagnostica, non fornisce un riferimento completo per l'utilizzo di IISNode. Per ulteriori informazioni sull'utilizzo di IISNode, vedere il [File Leggimi IISNode] su GitHub.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Attivare la registrazione

Per impostazione predefinita, un'app web di servizio App acquisisce solo informazioni diagnostiche relative a distribuzioni, ad esempio quando si distribuisce un'app web usando fra. Queste informazioni sono utile se si sono verificati problemi durante la distribuzione, ad esempio un errore durante l'installazione di un modulo di cui fa riferimento **package.json**, o se si utilizza uno script di distribuzione personalizzata.

Per abilitare la registrazione dei flussi di stdout e stderr, è necessario creare un file **IISNode.yml** nella radice dell'applicazione Node e aggiungere quanto segue:

    loggingEnabled: true

In questo modo la registrazione di stderr e stdout dall'applicazione di Node.

Il file **IISNode.yml** utilizzabile anche per controllare se nel browser vengono restituiti errori descrittivi o sviluppo quando si verifica un errore. Per abilitare gli errori di sviluppo, aggiungere la riga seguente al file **IISNode.yml** :

    devErrorsEnabled: true

Dopo questa opzione è attivata, IISNode restituirà ultima 64 KB delle informazioni inviate a stderr invece di un errore descrittivo, ad esempio "si è verificato un errore interno del server".

> [AZURE.NOTE] Mentre devErrorsEnabled è utile quando la diagnosi problemi durante lo sviluppo, abilitazione in un ambiente di produzione può provocare errori di sviluppo inviati agli utenti finali.

Se il file **IISNode.yml** non esiste già all'interno dell'applicazione, è necessario riavviare l'applicazione web dopo la pubblicazione dell'applicazione aggiornata. Se è sufficiente modificare le impostazioni di un file **IISNode.yml** esistente che in precedenza è stato pubblicato non è necessario riavviare.

> [AZURE.NOTE] Se un'app web è stata creata con la scheda strumenti della riga di comando Azure o i cmdlet di PowerShell di Azure, verrà creato automaticamente un file di **IISNode.yml** predefinito.

Per riavviare l'applicazione web, selezionare l'app web nel [Portale di Azure](https://portal.azure.com)e quindi fare clic su pulsante **riavviare** :

![Riavviare pulsante][restart-button]

Se gli strumenti della riga di comando di Azure sono installati nel proprio ambiente di sviluppo, è possibile utilizzare il comando seguente per riavviare l'applicazione web:

    azure site restart [sitename]

> [AZURE.NOTE] Mentre loggingEnabled e devErrorsEnabled sono i più comunemente usati IISNode.yml opzioni di configurazione per acquisire informazioni diagnostiche, IISNode.yml può essere utilizzato per configurare una vasta gamma di opzioni per l'ambiente di hosting. Per un elenco completo delle opzioni di configurazione, vedere il file [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>Accesso ai registri

Registri diagnostici accessibili in tre modi; Utilizzando il protocollo FTP (File Transfer), il download di un archivio Zip o come un live aggiornato flusso del log (noto anche come coda). Archivio Zip dei file di log o visualizzandolo flusso live richiedono gli strumenti della riga di comando di Azure. È possibile installarli utilizzando il comando seguente:

    npm install azure-cli -g

Dopo aver installato, gli strumenti sono accessibili tramite il comando "azure". Gli strumenti della riga prima di tutto devono essere configurati per usare l'abbonamento Azure. Per informazioni su come eseguire questa operazione, vedere la **come scaricare e importare le impostazioni di pubblicazione** sezione dell'articolo [come gli strumenti di utilizzo di Azure della riga di comando](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Per accedere alle informazioni di diagnostiche tramite FTP, visitare il [Portale di Azure](https://portal.azure.com), selezionare un'applicazione web e quindi selezionare il **DASHBOARD**. Nella sezione **collegamenti rapidi** collegamenti **FTP registri diagnostici** e **Invia registri diagnostici** forniscono l'accesso ai registri mediante il protocollo FTP.

> [AZURE.NOTE] Se non è stato già configurato nome utente e password per FTP o la distribuzione, è possibile eseguire questa operazione dalla pagina di gestione **delle Guide rapide** selezionando **configurare le credenziali di distribuzione**.

URL FTP restituito nel dashboard riguarda la directory **registri** che conterrà la sottodirectory seguenti:

* [Metodo di distribuzione](web-sites-deploy.md) , se si utilizza un metodo di distribuzione, ad esempio fra, una directory con lo stesso nome verrà creata e conterrà le informazioni relative alle distribuzioni.

* nodejs - Stdout e stderr le informazioni acquisite da tutte le istanze dell'applicazione (quando loggingEnabled è vera.)

###<a name="zip-archive"></a>Archivio ZIP

Per scaricare un archivio Zip di log diagnostici, usare il comando seguente dagli strumenti della riga di comando di Azure:

    azure site log download [sitename]

Questa operazione verrà scaricato un **diagnostics.zip** nella directory corrente. Questo archivio contiene la struttura di directory seguente:

* distribuzioni - un file di log delle informazioni sulle distribuzioni dell'applicazione

* File di registro

    * [Metodo di distribuzione](web-sites-deploy.md) , se si utilizza un metodo di distribuzione, ad esempio fra, una directory con lo stesso nome verrà creata e conterrà le informazioni relative alle distribuzioni.

    * nodejs - Stdout e stderr le informazioni acquisite da tutte le istanze dell'applicazione (quando loggingEnabled è vera.)

###<a name="live-stream-tail"></a>Flusso in diretta (coda)

Per visualizzare un flusso di informazioni diagnostiche log in diretta, utilizzare il comando seguente dagli strumenti della riga di comando di Azure:

    azure site log tail [sitename]

Viene restituito un flusso di eventi che vengono aggiornati quando si verificano nel server. Questo flusso restituirà informazioni sulla distribuzione, nonché informazioni stdout e stderr (quando loggingEnabled è vera.)

<a id="nextsteps"></a>
## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come attivare e accedere alle informazioni di diagnostica per Azure. Mentre queste informazioni sono utili per informazioni sui problemi con l'applicazione, potrebbe puntare a un problema con un modulo in uso o la versione di Node usati dalle App servizio Web App è diversa rispetto a quello usato nel proprio ambiente di distribuzione.

Per informazioni nell'utilizzo di moduli in Azure, vedere [Utilizzo dei moduli Node con le applicazioni di Azure](../nodejs-use-node-modules-azure-apps.md).

Per informazioni su come specificare una versione node per l'applicazione, vedere [impostazione di una versione Node in un'applicazione di Azure].

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori Node](/develop/nodejs/).

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

[IISNode]: https://github.com/tjanczuk/iisnode
[File Leggimi IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Specifica di una versione Node in un'applicazione di Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
