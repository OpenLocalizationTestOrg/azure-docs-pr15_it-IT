<properties 
    pageTitle="Come consentire agli account di sviluppo con Azure Active Directory in Azure API Management" 
    description="Informazioni su come autorizzare gli utenti che utilizzano Azure Active Directory in Gestione API." 
    services="api-management" 
    documentationCenter="API Management" 
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

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Come consentire agli account di sviluppo con Azure Active Directory in Azure API Management


## <a name="overview"></a>Panoramica
Questa guida viene illustrato come attivare l'accesso al portale per sviluppatori per tutti gli utenti in una o più Active Directory Azure. Questa guida viene inoltre illustrato come gestire i gruppi di utenti di Azure Active Directory mediante l'aggiunta di gruppi esterni che contengono gli utenti di Azure Active Directory.

>Per completare la procedura descritta in questa guida è un server di Azure Active Directory in cui creare un'applicazione.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Come consentire agli account di sviluppo con Azure Active Directory

Per iniziare, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Fare clic su **protezione** dal menu **Gestione API** sul lato sinistro e fare clic su **Identità esterni**.

![Identità esterne][api-management-security-external-identities]

Fare clic su **Azure Active Directory**. Prendere nota di **Reindirizzamento URL** e il parametro Azure Active Directory nel portale classica di Azure.

![Identità esterne][api-management-security-aad-new]

Fare clic sul pulsante **Aggiungi** per creare una nuova applicazione di Azure Active Directory e scegliere **Aggiungi un'applicazione di sviluppo di organizzazione**.

![Aggiungere una nuova applicazione di Azure Active Directory][api-management-new-aad-application-menu]

Immettere un nome per l'applicazione, selezionare **applicazione Web e/o API Web**e fare clic sul pulsante Avanti.

![Nuova applicazione di Azure Active Directory][api-management-new-aad-application-1]

Per **Sign-on URL**immettere l'URL di accesso del portale per sviluppatori. In questo esempio l' **URL di accesso** è `https://aad03.portal.current.int-azure-api.net/signin`. 

Per l' **URL di ID App**, immettere il dominio predefinito o un dominio personalizzato di Azure Active Directory e aggiungere una stringa univoca. In questo esempio viene utilizzato il dominio predefinito di **https://contoso5api.onmicrosoft.com** con del suffisso dell' **/api** specificato.

![Nuove proprietà di applicazione di Azure Active Directory][api-management-new-aad-application-2]

Fare clic sul pulsante di controllo per salvare e creare la nuova applicazione e passare alla scheda **Configura** per configurare la nuova applicazione.

![Creata nuova applicazione di Azure Active Directory][api-management-new-aad-app-created]

Se più Azure Active Directory sta per essere utilizzato per l'applicazione, fare clic su **Sì** per **l'applicazione è multi-tenant**. Il valore predefinito è **No**.

![Applicazione è multi-tenant][api-management-aad-app-multi-tenant]

Copiare l' **URL di reindirizzamento** della sezione di **Azure Active Directory** della scheda **Identità esterni** nel portale di publisher e incollarla nella casella di testo **URL di risposta** . 

![URL di risposta][api-management-aad-reply-url]

Scorrere verso il basso della scheda Configura, selezionare l'elenco a discesa **Autorizzazioni per l'applicazione** e controllare i **dati di directory di lettura**.

![Autorizzazioni per l'applicazione][api-management-aad-app-permissions]

Selezionare l'elenco a discesa **Le autorizzazioni di delega** e selezionare **Abilita accesso e leggere i profili degli utenti**.

![Autorizzazioni delegate][api-management-aad-delegated-permissions]

>Per ulteriori informazioni sull'applicazione e delega le autorizzazioni, vedere [accesso API grafico][].

Copiare l' **Id Client** negli Appunti.

![Id client][api-management-aad-app-client-id]

Tornare al portale di publisher e incollare l' **Id Client** copiata dalla configurazione dell'applicazione Azure Active Directory.

![Id client][api-management-client-id]

Passare alla configurazione di Azure Active Directory e fare clic su **Seleziona durata** elenco a discesa nella sezione **chiavi** e specificare un intervallo. In questo esempio viene usata **1 anno** .

![Chiave][api-management-aad-key-before-save]

Fare clic su **Salva** per salvare la configurazione e visualizzare la chiave. Copiare il codice negli Appunti.

>Prendere nota di questo tasto. Dopo aver chiuso la finestra di configurazione di Azure Active Directory, il tasto non è possibile visualizzare nuovamente.

![Chiave][api-management-aad-key-after-save]

Tornare al portale di publisher e incollare la chiave nella casella di testo **Segreto del Client** .

![Segreto del client][api-management-client-secret]

**È consentito tenant** specifica quali directory dispongano dell'accesso alle API dell'istanza del servizio di gestione delle API. Specificare i domini delle istanze di Azure Active Directory a cui si desidera concedere l'accesso. È possibile separare più domini con caratteri di fine riga, spazi o virgole.

![Tenant consentiti][api-management-client-allowed-tenants]

Nella sezione **Consentito tenant** , è possono specificare più domini. Prima di tutti gli utenti possono accedere da un dominio diverso da quello originale nel punto in cui l'applicazione è stata registrata, un amministratore globale di dominio diverso conceda l'autorizzazione per l'applicazione ai dati di directory di accesso. Per concedere l'autorizzazione, un amministratore globale necessario accedere all'applicazione e fare clic su **accetta**. Nell'esempio seguente `miaoaad.onmicrosoft.com` è stato aggiunto al **Tenant consentiti** e amministratore globale amministratore da tale dominio accesso per la prima volta.

![Autorizzazioni][api-management-permissions-form]

>Se un amministratore non globale tenta di accedere prima di concedere le autorizzazioni da un amministratore globale, il tentativo di accesso ha esito negativo e verrà visualizzato un errore.

Dopo aver specificata la configurazione desiderata, fare clic su **Salva**.

![Salva][api-management-client-allowed-tenants-save]

Una volta le modifiche vengono salvate, gli utenti della specificato Azure Active Directory possono accedere al portale per sviluppatori eseguendo la procedura descritta [nell'accedere al portale per sviluppatori usando un account Azure Active Directory][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Come aggiungere un esterni Azure gruppo di Active Directory

Dopo aver abilitato l'accesso per gli utenti di Azure Active Directory, è possibile aggiungere gruppi di Azure Active Directory nella gestione di API per gestire più facilmente l'associazione degli sviluppatori di gruppo con i prodotti desiderati.

> Per configurare un gruppo di Azure Active Directory esterno, è necessario prima di tutto configurati Azure Active Directory nella scheda identità seguendo la procedura descritta nella sezione precedente. 

Gruppi di Azure Active Directory esterni vengono aggiunti dalla scheda **visibilità** del prodotto per il quale si desidera concedere l'accesso al gruppo. Fare clic su **Products**e quindi fare clic sul nome del prodotto desiderato.

![Configurare prodotto][api-management-configure-product]

Passare alla scheda **visibilità** e fare clic su **Aggiungi gruppi da Azure Active Directory**.

![Aggiunta di gruppi][api-management-add-groups]

Selezionare il **Tenant di Azure Active Directory** nell'elenco a discesa e quindi digitare il nome del gruppo desiderato in **gruppi** da aggiungere una casella di testo.

![Gruppo selezionato][api-management-select-group]

Sono disponibili il nome del gruppo nell'elenco **gruppi** per il Azure Active Directory, come illustrato nell'esempio seguente.

![Elenco di gruppi di Azure Active Directory][api-management-aad-groups-list]

Fare clic su **Aggiungi** per convalidare il nome del gruppo e aggiungere il gruppo. In questo esempio **Contoso 5 sviluppatori** gruppo esterno viene aggiunto. 

![Gruppo aggiunto][api-management-aad-group-added]

Fare clic su **Salva** per salvare la nuova selezione del gruppo.

Una volta un gruppo di Azure Azure Active Directory è stato configurato da un prodotto, è disponibile per essere selezionato nella scheda **visibilità** per gli altri prodotti istanza del servizio di gestione delle API.

Per visualizzare e configurare le proprietà per i gruppi esterni dopo averli aggiunti, fare clic sul nome del gruppo nella scheda **gruppi** .

![Gestire i gruppi][api-management-groups]

Da qui è possibile modificare il **nome** e la **Descrizione** del gruppo.

![Modifica di gruppo][api-management-edit-group]

Gli utenti da configurato Azure Active Directory possono accedere al portale per sviluppatori di visualizzazione e la sottoscrizione a tutti i gruppi per cui hanno visibilità seguendo le istruzioni nella sezione seguente.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Come accedere al portale per sviluppatori usando un account Azure Active Directory

Per accedere al portale per sviluppatori usando un account Azure Active Directory configurato nelle sezioni precedenti, aprire una nuova finestra del browser mediante l' **URL di accesso** dalla configurazione dell'applicazione Active Directory e fare clic su **Azure Active Directory**.

![Portale per sviluppatori][api-management-dev-portal-signin]

Immettere le credenziali di uno degli utenti in Azure Active Directory e fare clic su **Accedi**.

![Accedi][api-management-aad-signin]

Potrebbe essere richiesto con un modulo di registrazione se sono necessarie eventuali informazioni aggiuntive. Compilare il modulo di registrazione e fare clic su **Iscriviti**.

![Registrazione][api-management-complete-registration]

L'utente è connesso a questo punto nel portale per sviluppatori per l'istanza del servizio di gestione dell'API.

![Registrazione completata][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accesso alle API di grafico]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Accedere al portale per sviluppatori usando un account Azure Active Directory]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

