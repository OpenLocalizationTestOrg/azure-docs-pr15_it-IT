<properties
    pageTitle="Creare un'app web Node nel servizio di App Azure | Microsoft Azure"
    description="Informazioni su come distribuire un'applicazione di Node in un'app web nel servizio App Azure."
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
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Creare un'app web Node nel servizio di App Azure

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [Node](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - fra](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

In questa esercitazione viene illustrato come creare una semplice applicazione [Node](http://nodejs.org) e distribuire in un' [app web](app-service-web-overview.md) nel [Servizio App Azure](../app-service/app-service-value-prop-what-is.md) tramite [operazioni](http://git-scm.com). Le istruzioni disponibili in questa esercitazione possono essere seguite dal sistema operativo che è in grado di eseguire Node.

Si apprenderanno:

* Come creare un'app web nel servizio App Azure tramite il portale di Azure.
* Come distribuire un'applicazione di Node all'app web premendo all'archivio di operazioni dell'applicazione web.

Applicazione completata scrive una breve stringa "hello world" nel browser.

![Browser verrà visualizzato il messaggio "Hello World".][helloworld-completed]

Per esercitazioni e codice di esempio con applicazioni Node più complesse o per altri argomenti sull'utilizzo Node in Azure, vedere il [Centro per sviluppatori Node](/develop/nodejs/).

> [AZURE.NOTE]
> Per completare questa esercitazione, è necessario un account di Microsoft Azure. Se non si dispone di un account, è possibile [attivare i vantaggi della propria sottoscrizione Visual Studio](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o [iscriversi per una versione di valutazione gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
>
> Se si desidera iniziare a usare il servizio di App Azure prima che si effettua l'iscrizione per un account Azure, accedere al [Servizio di App provare](http://go.microsoft.com/fwlink/?LinkId=523751). Non esiste, è possibile creare immediatamente un'app web starter breve nel servizio di App, ovvero non carta di credito necessari e non impegni.

## <a name="create-a-web-app-and-enable-git-publishing"></a>Creare un'app web e abilitare la pubblicazione fra

Seguire questa procedura per creare un'app web nel servizio di App Azure e abilitare la pubblicazione fra. 

[Fra](http://git-scm.com/) è un sistema di controllo delle versioni distribuito che è possibile utilizzare per distribuire il sito Web Azure. Archiviare il codice che scritto per un'app web in un repository fra locale e si verrà distribuire il codice in Azure premendo a un repository remoto. Questo metodo di distribuzione è una funzionalità di servizio App web apps.  

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic sull'icona **+ Nuovo** nell'angolo superiore sinistra del portale di Azure.

3. Fare clic su **Web + Mobile**e quindi fare clic su **app Web**.

    ![][portal-quick-create]

4. Immettere un nome per l'applicazione web nella finestra di **Web app** .

    Questo nome deve essere univoco nel dominio di azurewebsites.net perché l'URL dell'applicazione web sarà {nome}. azurewebsites.net. Se il nome che immesso non è univoco, un punto esclamativo rosso visualizzato nella casella di testo.

5. Selezionare un **abbonamento**.

6. Selezionare un **Gruppo di risorse** o crearne uno nuovo.

    Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).

7. Selezionare un **Percorso/del piano di servizio App** o crearne uno nuovo.

    Per ulteriori informazioni sui piani di servizio di App, vedere [Panoramica di piani di servizio App Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

8. Fare clic su **Crea**.
   
    ![][portal-quick-create2]

    In breve, in genere minori di un minuto Azure termina creare la nuova app web.

9. Fare clic su **Web App > {la nuova app web}**.

    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)

10. Selezionare la parte di **distribuzione** e il **Web app** .

    ![][deployment-part]

11. Selezionare **Scegli origine** e il **Distribuzione continua**

12. Fare clic su **Archivio fra locale**e quindi fare clic su **OK**.

    ![][setup-git-publishing]

13. Se non è già fatto, impostare le credenziali di distribuzione.

    un. Selezionare e il app Web **Impostazioni > credenziali distribuzione**.

    ![][deployment-credentials]
 
    b. Creare un nome utente e password. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)

14. In e il app Web, fare clic su **Impostazioni**e quindi fare clic su **proprietà**.
 
    Per pubblicare, verrà push a un repository fra remoto. L'URL per l'archivio è elencato in **Fra URL**. Utilizzare questo URL in un secondo momento nell'esercitazione.

    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Creare e testare l'applicazione localmente

In questa sezione, sarà necessario creare un file di **server.js** che contiene una versione leggermente modificata dell'esempio 'Hello World' da [nodejs.org]. Il codice aggiunge process.env.PORT come porta in ascolto durante l'esecuzione in un'app web Azure.

1. Creare una directory denominata *helloworld*.

2. Utilizzare un editor di testo per creare un nuovo file denominato **server.js** nella directory *helloworld* .

2. Copiare il codice seguente nel file **server.js** e quindi salvare il file:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Aprire la riga di comando e usare il comando seguente per avviare l'applicazione web locale.

        node server.js

4. Aprire il web browser e passare a http://localhost:1337. 

    Viene visualizzata una pagina Web che verrà visualizzato "Hello World", come illustrato nella schermata seguente.

    ![Browser verrà visualizzato il messaggio "Hello World".][helloworld-localhost]

## <a name="publish-your-application"></a>Pubblicare l'applicazione

1. Se non è già fatto, installare fra.

    Istruzioni di installazione per la piattaforma in uso, vedere la [pagina di download fra](http://git-scm.com/download).

1. Dalla riga di comando, passare alla directory **helloworld** e immettere il comando seguente per inizializzare un repository fra locale.

        git init


2. Usare i comandi seguenti per aggiungere file di archivio di:

        git add .
        git commit -m "initial commit"

3. Aggiungere un fra remoto per l'inserimento di aggiornamenti per l'applicazione web creata in precedenza, utilizzando il comando seguente:

        git remote add azure [URL for remote repository]

4. Inviare le modifiche apportate ai Azure tramite il comando seguente:

        git push azure master

    Viene richiesto per la password creata in precedenza. L'output è simile al seguente.

        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master

5. Per visualizzare l'app, fare clic **sul pulsante **App Web** part nel portale di Azure** .

    ![Pulsante Sfoglia](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)

    ![Hello world nelle Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Pubblicare le modifiche apportate all'applicazione

1. Aprire il file **server.js** in un editor di testo e modificare 'Hello World\n' a 'Hello Azure\n'. 

2. Salvare il file.

2. Dalla riga di comando, passare alla directory **helloworld** ed eseguire i comandi seguenti:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Viene chiesto di specificare la password nuovamente.

3. Aggiornare la finestra del browser che ci si spostasse all'URL dell'applicazione web.

    ![Una pagina web contenente 'Hello Azure'][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Ripristinare una distribuzione

Da e il **Web app** è possibile fare clic su **Impostazioni > distribuzione continua** per visualizzare la cronologia di distribuzione e il **distribuzioni** . Se è necessario ripristinare una distribuzione precedenti, è possibile selezionarla e quindi scegliere **ridistribuire** in e il **Dettagli di distribuzione** .

## <a name="next-steps"></a>Passaggi successivi

Distribuire un'applicazione di node per un'app web nel servizio App Azure. Per ulteriori informazioni sulle modalità di esecuzione delle applicazioni Node App servizio web App, vedere [Azure App servizio Web Apps: node](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) e [si specifica una versione Node in un'applicazione di Azure](../nodejs-specify-node-version-azure-apps.md).

Node fornisce un insieme di moduli che possono essere usate dalle applicazioni offre un RTF. Per informazioni su come funziona il Web Apps con moduli, vedere [uso Node moduli con le applicazioni di Azure](../nodejs-use-node-modules-azure-apps.md).

Se si verificano problemi con l'applicazione dopo che è stata distribuita in Azure, vedere [come eseguire il debug di un'applicazione di Node nel servizio di Azure App](web-sites-nodejs-debug.md) per informazioni su diagnosi del problema.

In questo articolo viene utilizzato il portale di Azure per creare un'app web. Per eseguire le stesse operazioni, è possibile utilizzare [l'interfaccia di riga di comando di Azure](../xplat-cli-install.md) o [Azure PowerShell](../powershell-install-configure.md) .

Per ulteriori informazioni su come sviluppare applicazioni Node su Azure, vedere il [Centro per sviluppatori Node](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
