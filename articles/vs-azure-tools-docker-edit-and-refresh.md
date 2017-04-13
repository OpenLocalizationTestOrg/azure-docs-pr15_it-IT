<properties
   pageTitle="Il debug App in un contenitore Docker locale | Microsoft Azure"
   description="Informazioni su come modificare un'app che è in esecuzione in un contenitore Docker locale, aggiornare il contenitore mediante modifica e aggiornamento e impostare i punti di interruzione di debug"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Il debug App in un contenitore Docker locale

## <a name="overview"></a>Panoramica
Visual Studio Tools per Docker offre un modo coerenza per lo sviluppo di una e convalidare l'applicazione in locale in un contenitore Linux Docker.
Non è necessario riavviare il contenitore ogni volta che si applica un codice di modifica.
In questo articolo verrà illustrato come utilizzare la caratteristica "Modifica e aggiornamento" per avviare un'applicazione Web ASP.NET Core in un contenitore Docker locale, apportare le modifiche necessarie e quindi aggiornare il browser per visualizzare le modifiche.
Verrà anche visualizzato è illustrato come impostare i punti di interruzione per il debug.

> [AZURE.NOTE] Supporto del contenitore di Windows sarà presto nelle versioni future

## <a name="prerequisites"></a>Prerequisiti
È necessario installare gli strumenti seguenti.

- [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Installare [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Per eseguire contenitori Docker in locale, è necessario un client docker locale.
È possibile utilizzare rilasciato [Docker della casella degli strumenti](https://www.docker.com/products/overview#/docker_toolbox) che richiede di Hyper-V in modo disabilitato oppure è possibile utilizzare [Docker per Windows Beta](https://beta.docker.com) che utilizza Hyper-V e richiede Windows 10.

In casella degli strumenti Docker, è necessario [configurare il client Docker](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. creare un'app web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. aggiungere supporto Docker

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. modificare il codice e aggiornamento

Per scorrere rapidamente le modifiche, è possibile avviare l'applicazione di un contenitore e continuare ad apportare modifiche, visualizzarle come si farebbe con IIS Express.

1. Impostare la configurazione della soluzione `Debug` e premere ** &lt;CTRL + F5 >** per creare l'immagine docker ed eseguirlo in locale.

    Dopo l'immagine contenitore è stato creato ed è in esecuzione in un contenitore Docker, Visual Studio verrà avviato l'app Web nel browser predefinito.
    Se si utilizza il browser Microsoft Edge o in caso contrario sono presenti errori, vedere la sezione [risoluzione dei problemi](vs-azure-tools-docker-troubleshooting-docker-errors.md) .

1. Passare alla pagina dettagli, in cui verranno apportare le modifiche.

1. Tornare a Visual Studio e aprire `Views\Home\About.cshtml`.

1. Aggiungere il contenuto HTML seguente alla fine del file e salvare le modifiche.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  La visualizzazione della finestra di output, una volta completata la compilazione di .NET e vedere le righe, tornare al browser e aggiornare la pagina dettagli.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  Le modifiche sono state applicate!

## <a name="4-debug-with-breakpoints"></a>4. eseguire il debug con punti di interruzione

Spesso, le modifiche saranno necessario ulteriormente ispezione, sfruttare le funzionalità di debug di Visual Studio.

1.  Tornare a Visual Studio e aprire`Controllers\HomeController.cs`

1.  Sostituire il contenuto del metodo About() con le operazioni seguenti:

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Impostare un punto di interruzione a sinistra della virgola il `string message`... riga.

1.  Premere ** &lt;F5 >** per avviare il debug.

1.  Passare alla pagina dettagli per l'interruzione.

1.  Passare a Visual Studio per visualizzare il punto di interruzione ed esaminare il valore del messaggio.

    ![][2]

##<a name="summary"></a>Riepilogo

Con [Visual Studio 2015 Tools per Docker](https://aka.ms/DockerToolsForVS), è possibile ottenere la produttività dell'uso in locale, realismo produzione di sviluppo in un contenitore Docker.

## <a name="troubleshooting"></a>Risoluzione dei problemi

[Risoluzione dei problemi di sviluppo Docker Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Ulteriori informazioni, vedere Docker con Visual Studio, Windows e Azure

- [Docker Tools per Visual Studio](http://aka.ms/dockertoolsforvs) - sviluppo del codice .NET Core in un contenitore
- [Docker Tools per Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - compilare e distribuire contenitori docker
- [Docker Tools per Visual Studio codice](http://aka.ms/dockertoolsforvscode) - servizi di lingua per la modifica di file docker, con ulteriori scenari e2e disponibile a breve
- [Informazioni sul contenitore di Windows](http://aka.ms/containers), Windows Server e le informazioni sul Server aspetti
- [Il servizio contenitore Azure](https://azure.microsoft.com/services/container-service/) - [il contenuto del servizio contenitore Azure](http://aka.ms/AzureContainerService)
-    Per altri esempi di utilizzo di Docker, vedere [utilizzo di Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) da [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connetti [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Per altre guide rapide dal demo HealthClinic.biz, vedere [Guide rapide strumenti di sviluppo di Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Vari strumenti Docker

[Alcuni strumenti docker grande (blog di Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Articoli ottimale

[Introduzione a Microservices dalla NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentazioni

- [Davide Lasker: VS Live Las Vegas 2016 - e2e Docker](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduzione a ASP.NET di base @ creare 2016 - nel punto in cui è in Demo](https://channel9.msdn.com/Events/Build/2016/B810)
- [Sviluppo di applicazioni .NET in contenitori, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
