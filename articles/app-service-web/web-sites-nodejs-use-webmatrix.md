<properties 
    pageTitle="Compilare e distribuire un'app web Node Azure con WebMatrix" 
    description="Esercitazione che illustra come utilizzare WebMatrix per sviluppare un'applicazione Node e distribuirlo a Azure App servizio Web Apps." 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Compilare e distribuire un'app web Node Azure con WebMatrix

In questa esercitazione viene illustrato come utilizzare WebMatrix per sviluppare un'applicazione Node e distribuirlo a [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix è uno strumento di sviluppo web gratuito da Microsoft contenente tutte le informazioni che necessarie per lo sviluppo di app web o sito Web. WebMatrix include diverse caratteristiche che rendono più facile da usare Node inclusi meno il completamento del codice, modelli predefiniti e il supporto di editor per Jade e CoffeeScript. Ulteriori informazioni su [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Al termine dell'esecuzione di questa Guida, si avrà un'app web Node in esecuzione in Azure App servizio.
 
Schermata dell'applicazione completata è inferiore a:

![Sito Web nodo Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="sign-into-azure"></a>Eseguire l'accesso a Azure

Seguire questa procedura per creare un'app web nel servizio di App Azure.

1. Barra di avvio WebMatrix
2. Se questa è la prima volta che è stato usato WebMatrix, verrà richiesto per accedere a Azure.  In caso contrario, è possibile fare clic sul pulsante **Accedi** e scegliere **Aggiungi Account**.  Selezionare questa opzione per **l'accesso** con l'Account Microsoft.

    ![Aggiungi Account][addaccount]

3. Se dispone di iscrizione per un account Azure, possono accedere con l'Account Microsoft:

    ![Eseguire l'accesso a Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Creare un sito tramite un modello incorporato per Azure

1. Nella schermata start fare clic sul pulsante **Nuovo** e scegliere **Raccolta modelli** per creare un nuovo sito dalla raccolta modelli:

    ![Nuovo sito dalla raccolta modelli][sitefromtemplate]

2. Nella finestra di dialogo **dal modello di sito** , selezionare **nodo** , quindi selezionare **Sito Express**. Infine, fare clic su **Avanti**. Se manca prerequisiti per il modello di **Sito Express** , verrà richiesto di installarli.

    ![Selezionare modello express][webmatrix-templates]

3. Se l'utente ha effettuato Azure, ora è possibile creare un'app web di servizio di App per il sito locale.  Scegliere un nome univoco e selezionare Data Center in cui si desidera app web App servizio da creare: 

    ![Creare siti in Azure][nodesitefromtemplateazure]
    
4. Al termine WebMatrix creazione sito locale e creare il servizio di App web app, l'IDE WebMatrix viene visualizzato.

    ![WebMatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Pubblicare l'applicazione in Azure

1. In WebMatrix, fare clic su **pubblica** dalla barra multifunzione **Home** per visualizzare la finestra di dialogo **Anteprima pubblicazione** del sito.

    ![Anteprima pubblicazione][webmatrix-node-publishpreview]

2. Fare clic su **Continua**. Una volta completata la pubblicazione, l'URL per il servizio di App web app è visualizzato nella parte inferiore dell'IDE WebMatrix

    ![pubblicazione completata][webmatrix-publish-complete]

3. Fare clic sul collegamento per aprire il servizio di App web app nel browser.

    ![Esprimere web app][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Modificare e ripubblicare l'applicazione.

È possibile facilmente modificare e ripubblicare l'applicazione. In questo caso, si renderà una semplice modifica sul titolo nel file **index.jade** e ripubblicare l'applicazione.

1. In WebMatrix, selezionare **i file**e quindi espandere la cartella di **visualizzazioni** . Aprire il file **index.jade** facendo doppio clic su esso.

    ![WebMatrix visualizzazione index.jade][webmatrix-modify-index]

2. Modificare la riga del paragrafo per le operazioni seguenti:

        p Welcome to #{title} with WebMatrix on Azure!

3. Salvare le modifiche e quindi fare clic sull'icona di pubblicazione. Infine, fare clic su **Continua** nella finestra di dialogo **Anteprima pubblicazione** e attendere che l'aggiornamento da pubblicare.

    ![Anteprima pubblicazione][webmatrix-republish]

4. Al termine di pubblicazione, utilizzare il collegamento nuovamente il processo di pubblicazione termine per visualizzare l'app web App servizio aggiornata.

    ![Nodo Azure web app][webmatrix-node-completed]

##<a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle versioni di Node forniti con Azure e su come specificare la versione da utilizzare con l'applicazione, vedere [impostazione di una versione Node in un'applicazione di Azure](../nodejs-specify-node-version-azure-apps.md).

Se si verificano problemi con l'applicazione dopo che è stata distribuita in Azure, vedere [come eseguire il debug di un'app web Node nel servizio di Azure App](web-sites-nodejs-debug.md) per informazioni su diagnosi del problema.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 