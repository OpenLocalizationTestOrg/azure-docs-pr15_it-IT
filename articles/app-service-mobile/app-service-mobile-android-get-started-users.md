<properties
    pageTitle="Aggiungere l'autenticazione in Android con App Mobile | Servizio App Azure"
    description="Informazioni su come utilizzare App Mobile in Azure App servizio per l'autenticazione degli utenti dell'app Android attraverso una varietà di provider di identità, inclusi Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Aggiungere l'autenticazione per l'app di Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Riepilogo

In questa esercitazione si aggiunge l'autenticazione il progetto di Guida introduttiva elenco attività Android tramite un provider di identità supportati. In questa esercitazione si basa sull'esercitazione [Introduzione App Mobile] , è necessario completare prima di tutto.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio di App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni per gli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Android Studio, aprire il progetto è completata previsto con l'esercitazione [Introduzione App Mobile]. Dal menu **Esegui** fare clic su **Esegui app** e verificare che viene generata un'eccezione non gestita con un codice di stato di 401 (non autorizzato) dopo l'avvio dell'applicazione.

     Questa eccezione si verifica perché l'app tenta di accedere a back-end come un utente autenticato, ma la tabella _TodoItem_ ora richiede l'autenticazione.

Sarà quindi aggiornare l'app per l'autenticazione degli utenti prima di richiedere risorse di back-end App Mobile.

## <a name="add-authentication-to-the-app"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Token di autenticazione cache sul client

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Passaggi successivi

Ora che è stata completata questa esercitazione l'autenticazione di base, è consigliabile continuare a uno dei seguenti esercitazioni:

+ [Aggiungere le notifiche push per l'app di Android](app-service-mobile-android-get-started-push.md) Informazioni su come configurare il back-end App Mobile per l'utilizzo di Azure notifica hub per inviare le notifiche push.

+ [Attivare la sincronizzazione offline per un'app di Android](app-service-mobile-android-get-started-offline-data.md) Informazioni su come aggiungere l'app utilizzando un back-end App Mobile supporto della modalità offline. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Guida introduttiva a App Mobile]: app-service-mobile-android-get-started.md
