<properties
    pageTitle="Creare un'app di Android in App per dispositivi mobili Azure App servizio | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a utilizzare back-end Azure app per dispositivi mobili per lo sviluppo di Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-android-app"></a>Creare un'app di Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come aggiungere un servizio basato su cloud back-end a un'app per dispositivi mobili Android utilizzando un back-end Azure app per dispositivi mobili.  È necessario creare un nuova app per dispositivi mobili di back-end e un semplice _elenco_ Android app contenente dati app in Azure.

Esercitazione è un prerequisito per tutte le altre esercitazioni Android sull'utilizzo della funzionalità di App Mobile in Azure App servizio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario quanto segue:

* [Strumenti di sviluppo di android](https://developer.android.com/sdk/index.html), che include l'ambiente di sviluppo integrato Studio Android e la piattaforma Android più recente.
* Azure Mobile Android SDK, automaticamente fare riferimento come parte del progetto Guida introduttiva scaricati.
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuovo back-end Azure app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurare project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Scaricare ed eseguire l'app Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
