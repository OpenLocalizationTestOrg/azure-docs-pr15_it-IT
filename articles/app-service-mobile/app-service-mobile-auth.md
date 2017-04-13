<properties
    pageTitle="Autenticazione e autorizzazione in App per dispositivi mobili Azure | Microsoft Azure"
    description="Panoramica dell'autenticazione e riferimento concettuale / autorizzazione portare in primo piano per le App per dispositivi mobili Azure"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticazione e autorizzazione in Azure App per dispositivi mobili

## <a name="what-is-app-service-authentication--authorization"></a>Che cos'è l'App dei servizi di autenticazione / autorizzazione?

> [AZURE.NOTE] In questo argomento verrà migrato a un consolidati [App dei servizi di autenticazione / autorizzazione](../app-service/app-service-authentication-overview.md) argomento che copre Web, mobili e le API app.

Applicazione servizio di autenticazione / autorizzazione è una funzionalità che consente l'applicazione per l'accesso degli utenti con le modifiche senza codice obbligatorio in back-end app. Fornisce un modo semplice per proteggere l'applicazione e lavorare con i dati per utente.

Servizio App utilizza identità federata, in cui sono archiviati gli account di un **provider di identità** di terze 3o ("IDP") e autentica gli utenti e l'applicazione viene utilizzata l'identità anziché il proprio. Servizio App supporta cinque provider di identità all'esterno della casella: _Azure Active Directory_, _Facebook_, _Google_, _Account Microsoft_e _Twitter_. È anche possibile espandere il supporto per le app mediante l'integrazione di un altro provider di identità o soluzione identità personalizzata.

L'app può utilizzare un numero qualsiasi di questi provider di identità, pertanto è possibile offrire agli utenti finali con opzioni per la modalità all'accesso.

Se si desidera iniziare subito, vedere uno degli esercitazioni elencate di seguito:

- [Aggiungere l'autenticazione per l'app iOS]
- [Aggiungere l'autenticazione all'app Xamarin.iOS]
- [Aggiungere l'autenticazione all'app Xamarin.Android]
- [Aggiungere l'autenticazione per l'app di Windows]

## <a name="how-authentication-works"></a>Funzionamento dell'autenticazione

Per l'autenticazione utilizzando uno dei provider di identità, è innanzitutto necessario configurare il provider di identità per conoscere l'applicazione. Il provider di identità quindi fornirà con ID e informazioni riservate che forniscono all'applicazione. Questo completa la relazione di attendibilità e consente di convalidare identità fornita a tale servizio App.

Negli argomenti seguenti sono riportate le operazioni seguenti:

- [Come configurare l'app per l'utilizzo di accesso di Azure Active Directory]
- [Come configurare l'app per l'utilizzo di accesso di Facebook]
- [Come configurare l'app per l'utilizzo di accesso di Google]
- [Come configurare l'app per utilizzare Microsoft Account di accesso]
- [Come configurare l'app per l'utilizzo di accesso di Twitter]

Dopo che tutto sia configurato nel back-end, è possibile modificare il client agli utenti di accedere. Esistono due metodi di seguito:

- Usa una sola riga di codice, è consigliabile App Mobile client SDK l'accesso agli utenti.
- Utilizzare un SDK pubblicato da un provider di identità specificata per stabilire l'identità e quindi accedere al servizio di App.

>[AZURE.TIP] La maggior parte delle applicazioni devono utilizzare un provider SDK per ottenere un'esperienza di accesso più nativo-sensazione e per sfruttare il supporto di aggiornamento e altri vantaggi specifiche del provider.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Funzionamento dell'autenticazione senza un provider di SDK

Se non si desidera configurare un provider di SDK, è possibile consentire App Mobile eseguire l'accesso. Il client App Mobile SDK verrà aperta una visualizzazione web per il provider di propria scelta e completato l'accesso in. In alcuni casi sui blog e forum verrà visualizzato questo noto come "flusso dei server" o "indirizzati server flusso" dopo il server gestisce l'account di accesso e il client SDK non riceve mai token del provider.

Il codice necessario per avviare il flusso è contemplato nell'esercitazione autenticazione per ogni piattaforma. Al termine del flusso di client SDK dispone di un token di servizio di App e il token automaticamente è associato a tutte le richieste di back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Funzionamento dell'autenticazione con un provider di SDK

Utilizzo di un provider di SDK consente l'esperienza di log interagire meglio con la piattaforma del sistema operativo che è in esecuzione l'app. Inoltre, offre un token di provider e alcune informazioni utente del client, che rende più facile usare grafico API e personalizzare l'esperienza utente. In alcuni casi nel blog e forum verrà visualizzato questo noto come "flusso client" o "diretto client flusso" dal codice sul client gestisce l'account di accesso e il codice client ha accesso a un token di provider.

Dopo aver ottenuto un token di provider, è necessario essere inviato al servizio di App per la convalida. Al termine del flusso di client SDK dispone di un token di servizio di App e il token automaticamente è associato a tutte le richieste di back-end. Lo sviluppatore anche possibile mantenere un riferimento al token del provider se richiesto.

## <a name="how-authorization-works"></a>Funzionamento di autorizzazione

Applicazione servizio di autenticazione / autorizzazione espone selezionare diverse opzioni per **l'azione da eseguire quando richiesta non viene autenticato**. Prima che il codice riceve una richiesta specifica, è possibile utilizzare App servizio controllare se la richiesta viene autenticata e se non, rifiutarlo e si tenta di richiedere all'utente di effettuare l'accesso prima di riprovare.

È possibile avere non autenticati reindirizzare richieste a uno dei provider di identità. In un web browser, l'operazione effettivamente da richiedere all'utente di una nuova pagina. Tuttavia, in questo modo non è possibile reindirizzare i client mobili e le risposte non autenticate riceveranno una risposta HTTP _401 non autorizzato_ . Pertanto la prima richiesta che il client effettua deve essere sempre per l'endpoint di accesso e quindi è possibile effettuare chiamate ad altre API. Se si tenta di chiamare un'altra API prima di accesso, il client verrà visualizzato un errore.

Se si desidera ottenere maggiore controllo su quali endpoint richiedono l'autenticazione, è anche possibile scegliere "alcuna azione (consentire richiesta)" per le richieste non autenticate. In questo caso, tutte le decisioni di autenticazione posticipate al codice dell'applicazione. In questo modo è possibile consentire l'accesso a utenti specifici in base alle regole di autorizzazione personalizzato.

## <a name="documentation"></a>Documentazione

Le esercitazioni seguenti viene illustrato come aggiungere l'autenticazione per i client mobili mediante il servizio di App:

- [Aggiungere l'autenticazione per l'app iOS]
- [Aggiungere l'autenticazione all'app Xamarin.iOS]
- [Aggiungere l'autenticazione all'app Xamarin.Android]
- [Aggiungere l'autenticazione per l'app di Windows]

Le esercitazioni seguenti viene illustrato come configurare il servizio di App per sfruttare i provider di autenticazione diverso:

- [Come configurare l'app per l'utilizzo di accesso di Azure Active Directory]
- [Come configurare l'app per l'utilizzo di accesso di Facebook]
- [Come configurare l'app per l'utilizzo di accesso di Google]
- [Come configurare l'app per utilizzare Microsoft Account di accesso]
- [Come configurare l'app per l'utilizzo di accesso di Twitter]

Se si desidera utilizzare un sistema di identità ad eccezione di quelli forniti in questo caso, è possibile sfruttare [visualizzare in anteprima il supporto di autenticazione personalizzato .NET Server SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Aggiungere l'autenticazione per l'app iOS]: app-service-mobile-ios-get-started-users.md
[Aggiungere l'autenticazione all'app Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Aggiungere l'autenticazione all'app Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Aggiungere l'autenticazione per l'app di Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Come configurare l'app per l'utilizzo di accesso di Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Come configurare l'app per l'utilizzo di accesso di Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Come configurare l'app per l'utilizzo di accesso di Google]: app-service-mobile-how-to-configure-google-authentication.md
[Come configurare l'app per utilizzare Microsoft Account di accesso]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Come configurare l'app per l'utilizzo di accesso di Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
