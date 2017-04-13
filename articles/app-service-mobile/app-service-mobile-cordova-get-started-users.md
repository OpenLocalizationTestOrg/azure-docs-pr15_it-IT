<properties
    pageTitle="Aggiungere l'autenticazione in Apache Cordova con App Mobile | Servizio App Azure"
    description="Informazioni su come utilizzare App Mobile in Azure App servizio per l'autenticazione degli utenti dell'app Apache Cordova tramite un'ampia gamma di provider di identità, inclusi Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Aggiungere l'autenticazione all'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Riepilogo

In questa esercitazione, aggiungere l'autenticazione al progetto elenco attività Guida introduttiva su Apache Cordova tramite un provider di identità supportati. In questa esercitazione si basa sull'esercitazione [Introduzione App Mobile] , è necessario completare prima di tutto.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio di App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Guardare un video che mostra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Limitare le autorizzazioni per gli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A questo punto, è possibile verificare che l'accesso anonimo per il back-end è stata disattivata. In Visual Studio, aprire il progetto creato quando si è completata l'esercitazione [Introduzione App Mobile], quindi eseguire l'applicazione in **Google Android emulatore** e verificare che venga visualizzato un errore imprevisto di connessione dopo l'avvio dell'applicazione.

Successivamente, sarà necessario aggiornare l'app per l'autenticazione degli utenti prima di richiedere risorse di back-end App Mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Aprire il progetto in **Visual Studio**, quindi aprire il `www/index.html` file per la modifica.

2. Individuare il `Content-Security-Policy` metatag nella sezione intestazione.  È necessario aggiungere l'host OAuth all'elenco delle origini consentite.

  	| Provider               | Nome del Provider SDK | OAuth Host                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | aAd               | https://Login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.Google.com |
  	| Microsoft              | MicrosoftAccount  | https://Login.Live.com      |
  	| Twitter                | Twitter           | https://API.Twitter.com     |

    Un esempio contenuto-criteri di sicurezza (implementato per Azure Active Directory) è la seguente:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    È necessario sostituire `https://login.windows.net` con l'host OAuth nella tabella seguente.  Per ulteriori informazioni su questo metatag, consultare la [documentazione di criteri di sicurezza del contenuto] .

    Si noti che alcuni provider di autenticazione non richiedono che Criteri di sicurezza contenuto cambia quando utilizzato nei dispositivi mobili appropriati.  Nessuna modifica criteri di sicurezza del contenuto, ad esempio, è necessaria quando si usa l'autenticazione di Google in un dispositivo Android.

3. Aprire la `www/js/index.js` per la modifica di file, individuare il `onDeviceReady()` metodo, e sotto la creazione di client codice aggiungere quanto segue:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Si noti che questo codice sostituisce il codice esistente che crea il riferimento alla tabella e aggiorna l'interfaccia utente.

    Il metodo Login () avvia l'autenticazione con il provider. Il metodo Login () è una funzione asincrono che restituisce un suggerimento JavaScript.  Il resto dell'inizializzazione viene posizionato in risposta promise in modo che non viene eseguita finché non viene completato il metodo Login ().

4. Nel codice appena aggiunto, sostituire `SDK_Provider_Name` con il nome del provider di accesso. Ad esempio, per Azure Active Directory, utilizzare `client.login('aad')`.

4. Eseguire il progetto.  Al termine dell'inizializzazione il progetto, l'applicazione verrà visualizzata la pagina di accesso OAuth per il provider di autenticazione scelto.

##<a name="next-steps"></a>Passaggi successivi

* Informazioni su ulteriori [Informazioni sull'autenticazione] con il servizio di App Azure.
* Continuare l'esercitazione aggiungendo [Delle notifiche Push] Apache Cordova all'applicazione in uso.

Informazioni su come usare gli SDK.

* [Apache Cordova SDK]
* [Server di ASP.NET SDK]
* [Server Node SDK]

<!-- URLs. -->
[Guida introduttiva a App Mobile]: app-service-mobile-cordova-get-started.md
[Documentazione di criteri di sicurezza del contenuto]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notifiche push]: app-service-mobile-cordova-get-started-push.md
[Informazioni sull'autenticazione]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[Server di ASP.NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Server Node SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
