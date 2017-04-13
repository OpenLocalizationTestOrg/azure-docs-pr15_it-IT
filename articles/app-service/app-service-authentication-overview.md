<properties
    pageTitle="Autenticazione e l'autorizzazione in Azure App servizio | Microsoft Azure"
    description="Panoramica dell'autenticazione e riferimento concettuale / autorizzazione portare in primo piano per il servizio di App Azure"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticazione e autorizzazione in servizio App Azure

## <a name="what-is-app-service-authentication--authorization"></a>Che cos'è l'App dei servizi di autenticazione / autorizzazione?

Applicazione servizio di autenticazione / autorizzazione è una funzionalità che offre un modo per l'applicazione effettuare l'accesso agli utenti in modo che non è necessario modificare codice in back-end app. Fornisce un modo semplice per proteggere l'applicazione e lavorare con i dati per utente.

Servizio App utilizza identità federata, in cui sono memorizzati gli account di un provider di identità di terze parti e autentica gli utenti. L'applicazione si basa su informazioni di identità del provider in modo che l'app non è necessario archiviare tali informazioni stesso. Servizio App supporta cinque provider di identità all'esterno della casella: Azure Active Directory, Facebook, Google, Account Microsoft e Twitter. L'app è possibile utilizzare un numero qualsiasi di questi provider di identità per fornire agli utenti con le opzioni per la modalità eseguire l'accesso. Per espandere il supporto incorporato, è possibile integrare un altro provider di identità o [soluzione identità personalizzata][custom-auth].

Se si desidera iniziare subito, vedere uno degli esercitazioni elencate di seguito:

- [Aggiungere l'autenticazione per l'app iOS] [ iOS] (o [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]o [Cordova])
- [Autenticazione dell'utente per App API nel servizio App Azure][apia-user]
- [Guida introduttiva di Azure App servizio - parte 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Funzionamento dell'autenticazione nel servizio di App

Per l'autenticazione utilizzando uno dei provider di identità, è innanzitutto necessario configurare il provider di identità per conoscere l'applicazione. Il provider di identità fornirà quindi ID e informazioni riservate che forniscono al servizio di App. La relazione di attendibilità è stata completata in modo che il servizio di App convalidare asserzioni utente, ad esempio i token di autenticazione, dal provider di identità.

Per accedere a un utente utilizzando uno dei provider elencati, l'utente deve essere reindirizzata a un endpoint accede agli utenti per tale provider. Se i clienti utilizza un web browser, è possibile impostare servizio App indirizzare automaticamente tutti gli utenti non autenticati all'endpoint accede agli utenti. In caso contrario, sarà necessario indirizzare i clienti a `{your App Service base URL}/.auth/login/<provider>`, dove `<provider>` corrisponde a uno dei seguenti valori: aad, facebook, google, microsoft o twitter. Scenari di utilizzo mobile e API sono descritti nelle sezioni più avanti in questo articolo.

Utenti che interagiscono con l'applicazione tramite un web browser avrà un cookie impostato in modo che può rimanere autenticati durante la visualizzazione dell'applicazione. Per altri tipi di client, ad esempio il cellulare, un token di web JSON (JWT), che devono essere presentate nel `X-ZUMO-AUTH` intestazione, verrà inviata al client. Il client App Mobile SDK gestirà questa necessità. In alternativa, un token di identità di Azure Active Directory o un token di accesso possono essere direttamente inclusi nella `Authorization` intestazione come [token del titolare](https://tools.ietf.org/html/rfc6750).

Servizio di App convalida qualsiasi cookie token o che l'applicazione problemi per l'autenticazione degli utenti. Per limitare chi può accedere all'applicazione, vedere la sezione [autorizzazioni](#authorization) più avanti in questo articolo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Autenticazione per dispositivi mobili con un provider di SDK

Dopo che tutto sia configurato nel back-end, è possibile modificare i client mobili di accedere con il servizio di App. Esistono due metodi di seguito:

- Utilizzare un SDK che un provider di identità specificato pubblica per stabilire l'identità e quindi accedere al servizio di App.
- Usare una sola riga di codice in modo che gli utenti può accedere al client App Mobile SDK.

>[AZURE.TIP] La maggior parte delle applicazioni devono utilizzare un provider di SDK per ottenere un'esperienza più coerente all'accesso degli utenti, per usare l'aggiornamento del supporto tecnico e per altri vantaggi che specifica il provider di servizi.

Quando si usa un provider di SDK, gli utenti possono accedere a un'esperienza la più stretta integrazione con sistema operativo che l'app è in esecuzione. Inoltre, offre un token di provider e alcune informazioni utente del client, che rende più facile usare grafico API e personalizzare l'esperienza utente. In alcuni casi nel blog e forum, verrà visualizzato questo noto come "flusso client" o "diretto client flusso" poiché codice sul client accede in utenti e il codice client ha accesso a un token di provider.

Dopo aver ottenuto un token di provider, è necessario che venga inviato al servizio di App per la convalida. Dopo avere servizio App convalidato il token, App servizio crea un nuovo token di servizio App restituito al client. Il client App Mobile SDK è metodi di supporto per la gestione di questo exchange e collegare automaticamente il token a tutte le richieste di back-end dell'applicazione. Gli sviluppatori anche possono mantenere un riferimento al token del provider se richiesto.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticazione mobile senza un provider di SDK

Se non si desidera configurare un provider di SDK, è possibile consentire la caratteristica di App Mobile del servizio di App Azure effettuare l'accesso dell'utente. Il client App Mobile SDK verrà aperta una visualizzazione web per il provider di propria scelta e accedere con l'utente. In alcuni casi nel blog e forum, verrà visualizzato questo definito "il server flusso" o "indirizzati server flusso" perché il server gestisce il processo di firma in utenti e il client SDK non riceve mai token del provider.

Codice per avviare il flusso è incluso nell'esercitazione autenticazione per ogni piattaforma. Al termine del flusso di client SDK dispone di un token di servizio di App e il token automaticamente è associato a tutte le richieste di back-end dell'applicazione.

### <a name="service-to-service-authentication"></a>Servizio di autenticazione

Anche se è possibile concedere l'accesso all'applicazione, è anche possibile attendibile un'altra applicazione di chiamare il proprio API. Ad esempio, potrebbe avere un'app web chiamare un'API in un'altra app web. In questo scenario, utilizzare le credenziali per un account di servizio anziché le credenziali utente per ottenere un token. Un account del servizio è noto anche come un *servizio principale* in termini di Azure Active Directory e l'autenticazione che utilizza un account di questo tipo è noto anche come uno scenario di servizio.

>[AZURE.IMPORTANT] Poiché App per dispositivi mobili eseguite su dispositivi cliente, applicazioni mobili eseguire _non_ conteggio come applicazioni attendibili e non utilizzare un flusso principale del servizio. Se, tuttavia, è necessario utilizzare un flusso di utente che è stata descritta in precedenza.

Per gli scenari di servizio servizio servizio App proteggere l'applicazione tramite Azure Active Directory. L'applicazione chiamata richiede solo fornire un token di autorizzazione principale del servizio di Azure Active Directory ottenute mediante l'aggiunta del client ID e client segreta da Azure Active Directory. Un esempio di questo scenario che usa App API ASP.NET è illustrato nell'esercitazione, [l'autenticazione dell'entità servizio per App API][apia-service].

Se si desidera utilizzare l'autenticazione del servizio di App per gestire uno scenario di servizio, è possibile utilizzare i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [How To Configure TLS reciproca Authentication per Web Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Per informazioni sull'autenticazione di base in ASP.NET, vedere [Filtri di autenticazione in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticazione dell'account del servizio da un'app di logica di servizio App a un'app API è un caso particolare dettaglio nelle [tramite l'API personalizzati ospitati in App servizio con logica App](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Funzionamento di autorizzazione nel servizio di App

È possibile controllare completo richieste che possono accedere all'applicazione. Applicazione servizio di autenticazione / autorizzazione può essere configurati con uno dei seguenti comportamenti:

- Consenti solo le richieste autenticate per raggiungere l'applicazione.

    Se un browser riceve una richiesta anonima, servizio App verrà reindirizzati alla pagina per il provider di identità che si è scelto in modo che gli utenti possano accedere. Se la richiesta proviene da un dispositivo mobile, la risposta restituita è una risposta HTTP _401 non autorizzato_ .

    Con questa opzione non è necessario scrivere codice autenticazione tutto nell'app. Se è necessaria l'autorizzazione più dettagliato, informazioni relative all'utente sono disponibile per il codice.

- Consentire a tutte le richieste di raggiungere l'applicazione, ma convalidare richieste autenticate e passare le informazioni di autenticazione nelle intestazioni HTTP.

    Questa opzione rinvia decisioni relative alle autorizzazioni per il codice dell'applicazione. Offre maggiore flessibilità nella gestione delle richieste anonime, ma è necessario scrivere codice.

- Consentire a tutte le richieste di raggiungere l'applicazione ed non esegue alcuna azione sulle informazioni di autenticazione nella richiesta.

    In questo caso, l'autenticazione / autorizzazione funzionalità è disattivata. Le attività di autenticazione e autorizzazione sono interamente il codice dell'applicazione.

Comportamenti precedenti vengono controllati mediante l'opzione di **azione da eseguire quando non viene autenticato richiesta** nel portale di Azure. Se si sceglie di * *accedere con *nome del provider* **, tutte le richieste devono essere autenticato.** Consentire richiesta (alcuna azione) * * rinvia la decisione al codice, ma fornisce comunque le informazioni di autenticazione. Se si desidera il codice di gestire tutti gli elementi, è possibile disattivare l'autenticazione / funzione di autorizzazione.

## <a name="working-with-user-identities-in-your-application"></a>Utilizzo delle identità utente dell'applicazione

Servizio di App passa informazioni relative agli utenti per l'applicazione tramite le intestazioni di speciale. Richieste esterne proibire queste intestazioni e se presenta solo verrà impostata dall'applicazione servizio di autenticazione / autorizzazione. Di seguito sono riportate alcune intestazioni di esempio:

* X MS-CLIENT-NOME-PRINCIPALE
* X-MS-CLIENT-CAPITALE-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Il codice scritto in qualsiasi lingua o framework è possibile ottenere le informazioni necessarie da queste intestazioni. Per le applicazioni ASP.NET 4.6, **ClaimsPrincipal** viene impostata automaticamente con i valori appropriati.

L'applicazione ottenere anche i dettagli aggiuntivi relativi all'utente tramite un HTTP GET sul `/.auth/me` endpoint dell'applicazione. Un token valido che è incluso con la richiesta, verrà restituito un payload JSON con informazioni dettagliate relative al provider in uso, token del provider sottostante e altre informazioni utente. Il server di App Mobile SDK forniscono metodi di supporto per lavorare con i dati. Per ulteriori informazioni, vedere [come utilizzare Azure Mobile App Node SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)e [il lavoro con il server di back-end .NET SDK per App Mobile Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentazione e risorse aggiuntive

### <a name="identity-providers"></a>Provider di identità
Le esercitazioni seguenti viene illustrato come configurare il servizio di App per l'utilizzo dei provider di autenticazione diverso:

- [Come configurare l'app per l'utilizzo di accesso di Azure Active Directory][AAD]
- [Come configurare l'app per l'utilizzo di accesso di Facebook][Facebook]
- [Come configurare l'app per l'utilizzo di accesso di Google][Google]
- [Come configurare l'app per utilizzare Microsoft Account di accesso][MSA]
- [Come configurare l'app per l'utilizzo di accesso di Twitter][Twitter]

Se si desidera utilizzare un sistema di identità ad eccezione di quelli forniti in questo caso, è inoltre possibile utilizzare l' [Anteprima di supporto dell'autenticazione personalizzata in .NET App Mobile server SDK][custom-auth], che può essere utilizzato nel web apps, App per dispositivi mobili o API app.

### <a name="web-applications"></a>Applicazioni Web
Le esercitazioni seguenti viene illustrato come aggiungere l'autenticazione a un'applicazione web:

- [Guida introduttiva di Azure App servizio - parte 2][web-getstarted]

### <a name="mobile-applications"></a>Applicazioni per dispositivi mobili
Le seguenti esercitazioni viene illustrato come aggiungere l'autenticazione per i client mobili tramite il flusso indirizzati al server:

- [Aggiungere l'autenticazione per l'app iOS][iOS]
- [Aggiungere l'autenticazione per l'app di Android] [Android]
- [Aggiungere l'autenticazione per l'app di Windows] [Windows]
- [Autenticazione aggiungere all'app Xamarin.iOS] [Xamarin.iOS]
- [Autenticazione aggiungere all'app Xamarin.Android] [Xamarin.Android]
- [Autenticazione aggiungere all'app Xamarin.Forms] [Xamarin.Forms]
- [Aggiungere l'autenticazione all'app Cordova] [Cordova]

Se si desidera utilizzare il flusso diretto client per Azure Active Directory, utilizzare le risorse seguenti:

- [Utilizzare Active Directory Authentication Library per iOS][ADAL-iOS]
- [Utilizzare la libreria di autenticazione Active Directory per Android][ADAL-Android]
- [Utilizzare la libreria di autenticazione Active Directory per Windows e Xamarin][ADAL-dotnet]

Se si desidera utilizzare il flusso diretto client per Facebook, usare le risorse seguenti:

- [Utilizzare il SDK di Facebook per iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Se si desidera utilizzare il flusso diretto client per Twitter, usare le risorse seguenti:

- [Usare tessuti Twitter per iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Se si desidera utilizzare il flusso diretto client per Google, usare le risorse seguenti:

- [Utilizzare SDK di accesso di Google per iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>Applicazioni API
Le esercitazioni seguenti viene illustrato come proteggere le app API:

- [Autenticazione dell'utente per App API nel servizio App Azure][apia-user]
- [Autenticazione dell'entità del servizio per API App comprese nel servizio App Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
