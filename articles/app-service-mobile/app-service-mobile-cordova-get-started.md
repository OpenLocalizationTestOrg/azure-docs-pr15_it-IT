<properties
    pageTitle="Creare un'app Cordova in App per dispositivi mobili Azure App servizio | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a utilizzare back-end Azure app per dispositivi mobili per lo sviluppo di Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, client mobile, javascript," />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Creare un Apache Cordova app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come aggiungere un servizio basato su cloud back-end a un'app per dispositivi mobili Apache Cordova utilizzando un back-end Azure app per dispositivi mobili.  È necessario creare un nuova app per dispositivi mobili di back-end e un semplice _elenco_ app Apache Cordova contenente dati app in Azure.

Esercitazione è un prerequisito per tutte le altre esercitazioni Apache Cordova sull'utilizzo della funzionalità di App Mobile in Azure App servizio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario quanto segue:

* Un PC con [Visual Studio Community 2015] o versione successiva.
* [Strumenti di visual Studio per Apache Cordova].
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).

È anche possibile ignorare Visual Studio e utilizzare la riga di comando Apache Cordova direttamente.  In questo modo al termine dell'esercitazione in un computer Mac.  La compilazione di applicazioni client Apache Cordova dalla riga di comando non rientrano in questa esercitazione.

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuovo back-end Azure app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Guardare un video che mostra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurare project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Scaricare ed eseguire l'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata completata questa esercitazione introduttiva, passare a uno dei seguenti esercitazioni:

* [Aggiungere l'autenticazione] per il Apache Cordova app.
* [Aggiungere le notifiche Push] per il Apache Cordova app.

Ulteriori informazioni sui concetti fondamentali con Azure App servizio.

* [Autenticazione]
* [Notifiche push]

Informazioni su come usare gli SDK.

* [Apache Cordova SDK]
* [Server di ASP.NET SDK]
* [Server Node SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Community di Visual Studio 2015]: http://www.visualstudio.com/
[Strumenti di Visual Studio per Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Aggiungere l'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Aggiungere le notifiche Push]: app-service-mobile-cordova-get-started-push.md
[Autenticazione]: app-service-mobile-auth.md
[Notifiche push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Server di ASP.NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Server Node SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
