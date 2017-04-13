<properties
    pageTitle="Aggiungere l'autenticazione in iOS con App Mobile Azure"
    description="Informazioni su come usare le app Mobile Azure autenticare gli utenti dell'app iOS attraverso una varietà di provider di identità, inclusi AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Aggiungere l'autenticazione per l'app iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In questa esercitazione, aggiungere l'autenticazione del progetto [iOS rapido iniziare] con un provider di identità supportati. In questa esercitazione si basa sull'esercitazione [iOS rapido iniziare] , è necessario completare prima di tutto.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio di App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni per gli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Xcode, premere **eseguire** per avviare l'applicazione. Verrà generata un'eccezione perché l'app tenta di accedere a back-end come un utente autenticato, ma _TodoItem_ tabella ora richiede l'autenticazione.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[Guida introduttiva di iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
