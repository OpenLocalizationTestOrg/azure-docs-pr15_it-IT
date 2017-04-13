<properties
    pageTitle="Creare un'app web di Azure Marketplace | Microsoft Azure"
    description="Informazioni su come creare una nuova app web WordPress da Azure Marketplace tramite il portale di Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Creare un'app web di Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace rende disponibile un'ampia gamma di applicazioni web popolari sviluppata da Microsoft, società di terze parti e iniziative software Apri origine. Ad esempio, WordPress, Umbraco CMS, Drupal, e così via. Queste applicazioni web si basano su una vasta gamma di Framework comuni, ad esempio [PHP] in questo esempio, [.NET], [Node], [Java]e [Python], per citarne di WordPress. Per creare un'app web di Azure Marketplace l'unico software che è necessario sia il browser da utilizzare per il [Portale di Azure].

In questa esercitazione si apprenderà come:

* Trovare e creare web app in Azure App servizio basato su un modello di Azure Marketplace.
* Configurare le impostazioni di Azure App servizio per la nuova app web.
* Avviare e gestire applicazioni web.

Per questa esercitazione verrà distribuito un sito blog WordPress da Azure Marketplace. Quando è stata completata la procedura descritta in questa esercitazione, è necessario sito WordPress alto e in esecuzione nel cloud.

![Dashboard di esempio WordPress wep app][WordPressDashboard1]

Il sito WordPress che è possibile distribuire in questa esercitazione utilizza MySQL per il database. Se si desidera utilizzare il Database di SQL per il database, vedere [Nami progetto], anch ' esso disponibile tramite Azure Marketplace.

> [AZURE.NOTE]
> Per completare questa esercitazione, è necessario un account di Microsoft Azure. Se non si dispone di un account, è possibile [attivare i vantaggi della propria sottoscrizione Visual Studio] [ activate] o [iscriversi per una versione di valutazione gratuita][free trial].
>
> Se si desidera iniziare a usare il servizio di App Azure prima che si effettua l'iscrizione per un account Azure, accedere al [Servizio di App provare]. Da è possibile creare immediatamente un'app web starter breve nel servizio di App, ovvero non è richiesta alcuna carta di credito e sono non disponibili impegni.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Trovare e creare un'App Web nel servizio di App Azure

1. Accedere al [portale di Azure].

1. Fare clic su **Nuovo**.
    
    ![Creare una nuova risorsa Azure][MarketplaceStart]
    
1. Cercare **WordPress**e quindi fare clic su **WordPress**. (Se si desidera utilizzare Database SQL anziché MySQL, cercare **Progetto Nami**.)

    ![Cercare WordPress del Marketplace][MarketplaceSearch]
    
1. Dopo aver letto la descrizione dell'app WordPress, fare clic su **Crea**.

    ![Creare WordPress web app][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configurare le impostazioni di Azure App servizio per la nuova App Web

1. Dopo aver creato una nuova app web, e l'impostazioni WordPress verrà visualizzato, che verrà utilizzato per completare la procedura seguente:

    ![Configurare le impostazioni di WordPress web app][ConfigStart]

1. Immettere un nome per l'applicazione web nella finestra di **Web app** .

    Questo nome deve essere univoco nel dominio di azurewebsites.net perché l'URL dell'applicazione web sarà *{nome}*. azurewebsites.net. Se il nome che immesso non è univoco, un punto esclamativo rosso visualizzato nella casella di testo.

    ![Configurare il nome dell'app web WordPress][ConfigAppName]

1. Se si dispone di più di una sottoscrizione, scegliere quello che si desidera utilizzare. 

    ![Configurare la sottoscrizione per l'applicazione web][ConfigSubscription]

1. Selezionare un **Gruppo di risorse** o crearne uno nuovo.

    Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione di risorse Azure][ResourceGroups].

    ![Configurare il gruppo di risorse per l'applicazione web][ConfigResourceGroup]

1. Selezionare un **Percorso/del piano di servizio App** o crearne uno nuovo.

    Per ulteriori informazioni sui piani di servizio di App, vedere [Panoramica di piani di servizio App Azure][AzureAppServicePlans]. 

    ![Configurare il piano di servizio per l'applicazione web][ConfigServicePlan]

1. Fare clic su **Database**e quindi fornire i valori necessari per la configurazione del database MySQL in e il **Nuovo MySQL Database** .

    un. Immettere un nuovo nome oppure lasciare il nome predefinito.

    b. Lasciare il **Tipo di Database** impostato su **condiviso**.

    c. Scegliere la posizione stessa quello che scelto per l'applicazione web.

    d. Scegliere un livello prezzo. **Mercurio** , che è gratuita con spazio su disco e connessioni minime - riguarda fitta questa esercitazione.

    e. In e il **Nuovo MySQL Database** , accettare le condizioni legali e quindi fare clic su **OK**. 

    ![Configurare le impostazioni per l'applicazione web][ConfigDatabase]

1. In e il **WordPress** accettare le condizioni legali e quindi fare clic su **Crea**. 

    ![Completare le impostazioni di web app e fare clic su OK][ConfigFinished]

    Servizio App Azure crea app web in genere in meno di un minuto. È possibile controllare lo stato di avanzamento, fare clic su icona della campana nella parte superiore della pagina del portale.

    ![Indicatore di stato][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Avviare e gestire applicazioni web WordPress
    
1. Al termine, la creazione di applicazioni web passare nel portale di Azure al gruppo di risorse in cui è stata creata l'applicazione e si può vedere web app e il database.

    Risorse aggiuntive con l'icona lampadina sono [Applicazione approfondimenti][ApplicationInsights], che offre servizi di monitoraggio per un'app web.

1. In e il **gruppo di risorse** , fare clic sulla linea di app web.

    ![Selezionare un'app web WordPress][WordPressSelect]

1. In e il app Web, fare clic su **Sfoglia**.

    ![Passare all'app web WordPress][WordPressBrowse]

1. Se viene richiesto di selezionare la lingua per il blog di WordPress, selezionare la lingua desiderata e quindi fare clic su **Continua**.

    ![Configurare la lingua per un'app web WordPress][WordPressLanguage]

1. Nella pagina WordPress **benvenuto** , immettere le informazioni di configurazione necessari affinché WordPress e quindi fare clic su **Installa WordPress**.

    ![Configurare le impostazioni di un'applicazione web WordPress][WordPressConfigure]

1. Accedere usando le credenziali creato nella pagina di **benvenuto** .  

1. La pagina Dashboard del sito verrà aperto e visualizzare le informazioni che è specificato.    

    ![Visualizzare il dashboard WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare e distribuire un'app web di esempio da Azure Marketplace.

Per ulteriori informazioni su come usare le App servizio Web App, vedere i collegamenti sul lato sinistro della pagina (per le finestre del browser larghezza) o nella parte superiore della pagina (per le finestre del browser strette).

Per ulteriori informazioni sullo sviluppo WordPress web App in Azure, vedere [Sviluppo WordPress Azure App servizio][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Provare il servizio di App]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portale di Azure]: https://portal.azure.com/
[Progetto Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
