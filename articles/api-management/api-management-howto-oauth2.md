<properties 
    pageTitle="Come consentire agli account di sviluppo utilizzando OAuth 2.0 in Azure API Management" 
    description="Informazioni su come autorizzare gli utenti che utilizzano OAuth 2.0 API gestione." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Come consentire agli account di sviluppo utilizzando OAuth 2.0 in Azure API Management

Molte API supportano [OAuth 2.0](http://oauth.net/2/) per proteggere l'API e assicurarsi che solo validi utenti hanno accesso e possono accedere solo alle risorse a cui hanno diritto. Per utilizzare Console di sviluppo interattivi di gestione di Azure API con tali API, il servizio consente di configurare l'istanza del servizio per lavorare con i OAuth 2.0 API abilitato.

## <a name="prerequisites"> </a>Prerequisiti

Questa guida illustra come configurare l'istanza del servizio di gestione dell'API per utilizzare l'autorizzazione OAuth 2.0 per gli account di sviluppo, ma non viene illustrato come configurare un provider di servizi OAuth 2.0. La configurazione per ogni provider OAuth 2.0 è diversa, anche se i passaggi sono simili e le necessarie informazioni usati per la configurazione di OAuth 2.0 nell'istanza del servizio di gestione delle API sono la stessa. In questo argomento viene esempi di utilizzo di Azure Active Directory come un provider di servizi OAuth 2.0.

>[AZURE.NOTE] Per ulteriori informazioni sulla configurazione 2.0 OAuth utilizzando Azure Active Directory, vedere l'esempio [DotNet di GraphAPI Web App][] .

## <a name="step1"> </a>Configurare un server di autorizzazione OAuth 2.0 in Gestione API

Per iniziare, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale di Publisher][api-management-management-console]

>[AZURE.NOTE] Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Fare clic su **protezione** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Aggiungi autorizzazione server** **2.0 OAuth**.

![OAuth 2.0][api-management-oauth2]

Dopo aver fatto clic su **Aggiungi server di autorizzazione**, viene visualizzato il nuovo formato di server autorizzazione.

![Nuovo server][api-management-oauth2-server-1]

Immettere un nome e una descrizione facoltativa nelle caselle **nome** e **Descrizione** . 

>[AZURE.NOTE] Questi campi vengono utilizzati per identificare il server di autorizzazione 2.0 OAuth all'interno dell'istanza di servizio di gestione dell'API corrente e i rispettivi valori non vengono forniti dal server 2.0 OAuth.

Immettere l' **URL della pagina registrazione Client**. Questa pagina è nel punto in cui gli utenti possono creare e gestire gli account e varia a seconda del provider OAuth 2.0 utilizzato. L' **URL della pagina registrazione Client** faccia riferimento alla pagina che gli utenti possono usare per creare e configurare gli account personali per OAuth 2.0 provider che supportano la gestione degli account utente. In alcune organizzazioni non configurare o utilizzare questa funzionalità, anche se il provider di OAuth 2.0 supporta. Se il provider di OAuth 2.0 non dispone di gestione degli utenti di account configurati, inserire un URL segnaposto, ad esempio l'URL dell'azienda o un URL, ad esempio `https://placeholder.contoso.com`.

La sezione successiva del modulo contiene le impostazioni di **codice di autorizzazione concedere tipi di** **URL endpoint di autorizzazione**e **metodo di richiesta di autorizzazione** .

![Nuovo server][api-management-oauth2-server-2]

Specificare il **codice di autorizzazione concedere tipi** selezionando tipi desiderati. Per impostazione predefinita è il **codice di autorizzazione** .

Immettere l' **URL di endpoint di autorizzazione**. Per Azure Active Directory, questo URL sarà simile all'URL seguente, in cui `<client_id>` viene sostituito con l'id client che identifica l'applicazione al server 2.0 OAuth.

    https://login.windows.net/<client_id>/oauth2/authorize

Il **metodo di richiesta di autorizzazione** specifica come la richiesta di autorizzazione viene inviata al server 2.0 OAuth. Per impostazione predefinita **ottenere** sia selezionata.

La sezione successiva è in cui vengono specificati i **Token endpoint URL**, **i metodi di autenticazione Client**, **INVIO metodo token di accesso**e **ambito predefinito** .

![Nuovo server][api-management-oauth2-server-3]

Per un server Azure Active Directory OAuth 2.0 **Token URL endpoint** avrà il seguente formato, in cui `<APPID>` è il formato di `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

L'impostazione predefinita per **i metodi di autenticazione Client** è **base**e **token di accesso invio metodo** è **intestazione di autorizzazione**. Questi valori vengono configurati in questa sezione della maschera, insieme **ambito predefinito**.

La sezione **credenziali Client** contiene **l'ID Client** e il **segreto del Client**, ottenuti durante il processo di creazione e la configurazione del server 2.0 OAuth. Una volta specificati **l'ID Client** e il **segreto del Client** , viene generato **redirect_uri** per il **codice di autorizzazione** . Questo URI viene utilizzato per configurare l'URL di risposta nella configurazione del server 2.0 OAuth.

![Nuovo server][api-management-oauth2-server-4]

Se **il codice di autorizzazione concedere tipi** è impostata su **password del proprietario delle risorse**, la sezione **credenziali password proprietario risorsa** viene utilizzata per specificare le credenziali; in caso contrario è possibile lasciarlo vuoto.

![Nuovo server][api-management-oauth2-server-5]

Una volta completato il modulo, fare clic su **Salva** per salvare la configurazione del server autorizzazione API Management OAuth 2.0. Dopo aver salvata la configurazione del server, è possibile configurare API per utilizzare questa configurazione, come illustrato nella sezione successiva.

## <a name="step2"> </a>Configurare un'API per utilizzare l'autorizzazione utente OAuth 2.0

Fare clic su **API** dal menu **Gestione API** a sinistra, fare clic sul nome dell'API desiderato, fare clic su **sicurezza**e quindi selezionare la casella per **OAuth 2.0**.

![Autorizzazioni utente][api-management-user-authorization]

Selezionare il **server di autorizzazione** desiderato dall'elenco a discesa e fare clic su **Salva**.

![Autorizzazioni utente][api-management-user-authorization-save]

## <a name="step3"> </a>Testare l'autorizzazione dell'utente OAuth 2.0 nel portale per sviluppatori

Dopo aver configurato il server di autorizzazione OAuth 2.0 e configurato l'API per utilizzare il server, è possibile testare passando al portale di sviluppo e la chiamata a un'API.  Fare clic su **portale per sviluppatori** nel menu superiore destro.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore e selezionare **API eco**.

![API eco][api-management-apis-echo-api]

>[AZURE.NOTE] Se si dispone di un solo API configurata o visibile al proprio account, quindi fare clic su API consente di accedere direttamente alle operazioni di tale API.

Selezionare l'operazione di **Ottenere risorse** , fare clic su **Apri la Console**e quindi selezionare **il codice di autorizzazione** dall'elenco a discesa.

![Console aperta][api-management-open-console]

Quando è selezionato **il codice di autorizzazione** , verrà visualizzata una finestra popup con la maschera di accesso del provider OAuth 2.0. In questo esempio la maschera di accesso viene fornita da Azure Active Directory.

>[AZURE.NOTE] Se si dispone di popup disabilitato verrà richiesto per consentire loro dal browser. Dopo aver attivato loro, verrà visualizzate selezionare di nuovo **il codice di autorizzazione** e al modulo di accesso.

![Accedi][api-management-oauth2-signin]

Dopo avere effettuato l'accesso, le **intestazioni di richiesta** dispongono di un `Authorization : Bearer` intestazione che autorizza la richiesta.

![Token intestazione della richiesta][api-management-request-header-token]

A questo punto è possibile configurare i valori desiderati per i parametri rimanenti e inviare la richiesta. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'uso di API Gestione e OAuth 2.0, vedere il video seguente e [articolo](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Guida introduttiva di Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[DotNet di GraphAPI Web App]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

