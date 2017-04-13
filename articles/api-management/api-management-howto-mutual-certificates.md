<properties 
    pageTitle="Come proteggere i servizi di back-end tramite client autenticazione certificato in Azure API Management" 
    description="Informazioni su come proteggere i servizi di back-end tramite autenticazione certificato client in Azure API Management." 
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

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Come proteggere i servizi di back-end tramite client autenticazione certificato in Azure API Management

Gestione dell'API consente di proteggere l'accesso al servizio di back-end di un'API utilizzando i certificati client. Questa guida viene illustrato come gestire i certificati nel portale di publisher API e come configurare un'API per utilizzare un certificato per accedere a un servizio di back-end.

Per informazioni sulla gestione dei certificati tramite l'API REST di gestione API, vedere [il certificato API REST di Azure API Gestione entità][].

## <a name="prerequisites"> </a>Prerequisiti

Questa guida viene illustrato come configurare l'istanza del servizio Gestione API per l'utilizzo di autenticazione certificato client per accedere al servizio di back-end per un'API. Prima di eseguire la procedura descritta in questo argomento, necessario il servizio di back-end configurato per l'autenticazione certificato client ([per configurare l'autenticazione certificato nei siti Web di Azure fare riferimento al presente articolo][]) e di avere accesso per il certificato e la password per il certificato per il caricamento nel portale di publisher API gestione.

## <a name="step1"> </a>Caricare un certificato client

Per iniziare, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale pubblicazione API][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Fare clic su **protezione** dal menu **Gestione API** sul lato sinistro e fare clic su **certificati Client**.

![Certificati client][api-management-security-client-certificates]

Per caricare un nuovo certificato, fare clic su **Carica certificato**.

![Carica certificato][api-management-upload-certificate]

Selezionare il certificato e quindi immettere la password per il certificato.

>Il certificato deve essere in formato **pfx** . Certificati consentiti.

![Carica certificato][api-management-upload-certificate-form]

Fare clic su **Carica** per caricare il certificato.

>La password del certificato viene convalidata al momento. Se non è corretto viene visualizzato un messaggio di errore.

![Certificato caricato][api-management-certificate-uploaded]

Dopo il certificato di caricamento, viene visualizzato nella scheda **certificati Client** . Se si dispongono di più certificati, verificare annotare l'oggetto o gli ultimi quattro caratteri di identificazione personale, che vengono utilizzati per selezionare il certificato quando si configura una API per utilizzare i certificati, come illustrato nella sezione [Configura un'API di utilizzare un certificato client per l'autenticazione del gateway][] seguente.

>Per disattivare la convalida della catena di certificato quando si usa, ad esempio un certificato autofirmato, seguire i passaggi descritti in questo [elemento](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)di domande frequenti.

## <a name="step1a"> </a>Eliminare un certificato client

Per eliminare un certificato, fare clic su **Elimina** accanto il certificato desiderato.

![Eliminare certificato][api-management-certificate-delete]

Fare clic su **Sì, Elimina** per confermare.

![Conferma eliminazione][api-management-confirm-delete]

Se il certificato è in uso da un'API, viene visualizzata una schermata di avviso. Per eliminare il certificato è innanzitutto necessario rimuovere il certificato da qualsiasi API che sono configurati per l'utilizzo.

![Conferma eliminazione][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurare un'API per usare un certificato client per l'autenticazione del gateway

Fare clic su **API** dal menu **Gestione API** a sinistra, fare clic sul nome dell'API desiderato e fare clic sulla scheda **sicurezza** .

![Protezione API][api-management-api-security]

Selezionare **i certificati Client** dall'elenco a discesa **con le credenziali** .

![Certificati client][api-management-mutual-certificates]

Selezionare il certificato desiderato dall'elenco a discesa **certificato Client** . Se sono presenti più certificati è possibile visualizzare l'oggetto o gli ultimi quattro caratteri di identificazione personale come indicato nella sezione precedente per determinare il certificato corretto.

![Seleziona certificato][api-management-select-certificate]

Fare clic su **Salva** per salvare le modifiche di configurazione all'API.

>Questa modifica ha effettiva immediato e le chiamate alle operazioni di tale API utilizzerà il certificato di autenticazione per il server di back-end.

![Salvare le modifiche apportate all'API][api-management-save-api]

>Quando è stato specificato un certificato per l'autenticazione di gateway per il servizio di back-end di un'API, diventa parte dei criteri per tale API e possono essere visualizzato nell'editor criteri.

![Criteri dei certificati][api-management-certificate-policy]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su altri modi per proteggere il servizio di back-end, ad esempio base o condivisa segreta autenticazione HTTP, vedere il video seguente.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Guida introduttiva di Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance

[Entità certificato API REST di gestione API Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Per configurare l'autenticazione certificato nei siti Web di Azure fare riferimento al presente articolo]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurare un'API per usare un certificato client per l'autenticazione del gateway]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
