<properties
    pageTitle="Distribuzione continua al servizio App Azure | Microsoft Azure"
    description="Informazioni su come attivare la distribuzione continua al servizio App Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua al servizio App Azure

In questa esercitazione viene illustrato come configurare un flusso di lavoro di distribuzione continua per l'applicazione di [Servizio App Azure] . Integrazione del servizio di App con BitBucket, GitHub e Visual Studio Team Services (VSTS) consente un flusso di lavoro di distribuzione continua in Azure recupera negli aggiornamenti più recenti dal progetto pubblicato in uno di questi servizi. Distribuzione continua è un'ottima soluzione per i progetti in più e contributi frequenti vengono integrati.

## <a name="overview"></a>Attivare la distribuzione continua

Per attivare la distribuzione continua, 

1. Pubblicare i contenuti di app all'archivio che verrà utilizzato per la distribuzione continua.  
    Per ulteriori informazioni sulla pubblicazione di un progetto in questi servizi, vedere [creare un repo (GitHub)], [creare un repo (BitBucket)]e [iniziare a utilizzare VSTS].

2. In blade menu dell'applicazione nel [portale di Azure], fare clic su **distribuzione delle APP > Opzioni di distribuzione**. Fare clic su **Scegli origine**e quindi selezionare l'origine di distribuzione.  

    ![](./media/app-service-continuous-deployment/cd_options.png)
    
    > [AZURE.NOTE] Per configurare un VSTS account per la distribuzione del servizio di App, vedere questa [esercitazione](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
    
3. Eseguire il flusso di lavoro di autorizzazione. 

4. Scegliere il progetto e diramazione di distribuzione da e **l'origine di distribuzione** . Al termine, fare clic su **OK**.
  
    ![](./media/app-service-continuous-deployment/github_option.png)

    > [AZURE.NOTE] Quando si abilita distribuzione continua con GitHub o BitBucket, verranno visualizzati i progetti pubblici e privati.

    Servizio App crea un'associazione con repository selezionato, recupera dal ramo specificato per il file e mantiene un duplicato il repository per l'applicazione di servizio di App. Quando si configura la distribuzione continua VSTS dal portale di Azure, l'integrazione di utilizza il servizio di App [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki), che già consente di automatizzare le attività e di distribuzione con ogni `git push`. Non è necessario configurare separatamente la distribuzione continua in VSTS. Dopo avere completato questa procedura, e il app **Opzioni di distribuzione** viene visualizzata una distribuzione attiva che indica la distribuzione ha avuto esito positivo.

5. Per verificare che l'app viene distribuito correttamente, fare clic sull' **URL** nella parte superiore della stessa e dell'applicazione nel portale di Azure. 

6. Per verificare che la distribuzione continua è in corso dal repository di propria scelta, inserire una modifica all'archivio. L'app è necessario aggiornare per riflettere le modifiche tra breve termine push per l'archivio. È possibile verificare che è estratta l'aggiornamento e **l'Opzioni di distribuzione** dell'app.

## <a name="VSsolution"></a>Distribuzione continua di una soluzione di Visual Studio 

Inserimento di una soluzione Visual Studio al servizio App Azure è semplice come inserendo un file index semplice. Il processo di distribuzione del servizio di App semplifica tutti i dettagli, incluso il ripristino delle dipendenze NuGet e la creazione di file binari dell'applicazione. È possibile seguire le procedure consigliate di origine controllo di gestione codice solo nel repository fra e fare in modo che la distribuzione del servizio di App del resto.

La procedura per l'inserimento della soluzione di Visual Studio al servizio di App è gli stessi visualizzati nella [sezione precedente](#overview), purché si configura la soluzione e repository come indicato di seguito:

-   Utilizzare l'opzione di controllo dell'origine di Visual Studio per generare un `.gitignore` file come aggiungere l'immagine sotto o manualmente una `.gitignore` file principale dell'archivio con contenuto simile al seguente [esempio .gitignore](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore). 

    ![](./media/app-service-continuous-deployment/VS_source_control.png)
 
-   Aggiungere la struttura di directory della soluzione intera per l'archivio con sln nella radice archivio.

Dopo aver impostare il repository come descritto e configurato l'app in Azure per la pubblicazione continua da uno dei repository fra online, è possibile sviluppare l'applicazione ASP.NET in locale in Visual Studio e continuamente distribuire il codice premendo le modifiche desiderate per l'archivio online di operazioni.

## <a name="disableCD"></a>Disabilitare la distribuzione continua

Per disabilitare la distribuzione continua, 

1. In blade menu dell'applicazione nel [portale di Azure], fare clic su **distribuzione delle APP > Opzioni di distribuzione**. Quindi fare clic su **Disconnetti** e **l'Opzioni di distribuzione** .

    ![](./media/app-service-continuous-deployment/cd_disconnect.png)    

2. Dopo la risposta **Sì** nel messaggio di conferma, è possibile tornare al blade dell'app e fare clic su **distribuzione delle APP > Opzioni di distribuzione** se si desidera configurare la pubblicazione da un'altra origine.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Come esaminare i problemi comuni relativi distribuzione continua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Come usare PowerShell per Azure]
* [Come usare gli strumenti della riga di comando di Azure per Mac e Linux]
* [Documentazione fra]
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

[Servizio App Azure]: https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/ 
[Portale di Azure]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Come usare PowerShell per Azure]: ../articles/powershell-install-configure.md
[Come usare gli strumenti della riga di comando di Azure per Mac e Linux]: ../articles/xplat-cli-install.md
[Documentazione fra]: http://git-scm.com/documentation

[Creare un repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Creare un repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Guida introduttiva a VSTS]: https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md
