<properties
    pageTitle="Come proteggere un back-end API Web con Azure Active Directory e gestione delle API"
    description="Informazioni su come proteggere un back-end API Web con Azure Active Directory e gestione di API." 
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

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Come proteggere un back-end API Web con Azure Active Directory e gestione delle API

Il video seguente viene illustrato come creare un back-end API Web e proteggere tramite protocollo OAuth 2.0 con Azure Active Directory e gestione di API.  In questo articolo offre una panoramica e informazioni aggiuntive per i passaggi del video. In questo video 24 minuti illustra le procedure per:

-   Creare un back-end API Web e proteggere con AAD - partire da 1:30
-   Importare l'API Gestione API - iniziando 7:10
-   Configurare il portale per sviluppatori per chiamare l'API - partire dal 9:09
-   Configurare un'applicazione desktop per chiamare l'API - 18:08 partire
-   Configurare un criterio di convalida JWT per pre-autorizzare le richieste - iniziando 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Creare una directory di Azure Active Directory

Per proteggere l'API Web backup con Azure Active Directory prima di tutto è necessario un un tenant AAD. In questo video viene utilizzato un tenant denominato **APIMDemo** . Per creare un tenant AAD, accesso al [Portale classica Azure](https://manage.windowsazure.com) e fare clic su **Nuovo**->**Servizi App**->**Active Directory**->**Directory**->**Creare personalizzata**. 

![Azure Active Directory][api-management-create-aad-menu]

In questo esempio viene creata una directory denominata **APIMDemo** con il dominio predefinito denominato **DemoAPIM.onmicrosoft.com**. Questa directory viene utilizzata in tutto il video.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Creare un servizio Web API protetto da Azure Active Directory

In questo passaggio, un back-end API Web viene creato utilizzando Visual Studio 2013. Questo passaggio del video inizia da 1:30. Per creare API Web back-end progetto in Visual Studio, fare clic su **File**->**Nuovo**->**progetto**e scegliere **Applicazione Web ASP.NET** dall'elenco di modelli **Web** . In questo video il progetto è denominato **APIMAADDemo**. Fare clic su **OK** per creare il progetto. 

![Visual Studio][api-management-new-web-app]

Fare clic su **API Web** **Selezionare un elenco di modello** per creare un progetto API Web. Per configurare l'autenticazione di Directory Azure fare clic su **Autenticazione di modifica**.

![Nuovo progetto][api-management-new-project]

Fare clic su **Account dell'organizzazione**e specificare il **dominio** del tenant di AAD. In questo esempio il dominio è **DemoAPIM.onmicrosoft.com**. È possibile ottenere il dominio della directory dalla scheda **domini** della directory.

![Domini][api-management-aad-domains]

Configurare le impostazioni desiderate nella finestra di dialogo **Modifica autenticazione** e fare clic su **OK**.

![Autenticazione di modifica][api-management-change-authentication]

Quando si fa clic su **OK** Visual Studio tenterà di registrare l'applicazione con la directory di Azure Active Directory e potrebbe essere richiesto di accedere da Visual Studio. Accedere con un account amministrativo per la directory.

![Accedere a Visual Studio][api-management-sign-in-vidual-studio]

Per configurare il progetto come una selezionare la casella di controllo API Web Azure per **Host nel cloud** e quindi fare clic su **OK**.

![Nuovo progetto][api-management-new-project-cloud]

Potrebbe essere richiesto di accedere a Azure e configurarlo Web App.

![Configurare][api-management-configure-web-app]

In questo esempio viene specificato un nuovo **piano di servizio App** denominato **APIMAADDemo** .

Fare clic su **OK** per configurare l'applicazione Web e creare il progetto.

## <a name="add-the-code-to-the-web-api-project"></a>Aggiungere il codice al progetto API Web

Il passaggio successivo nel video aggiunta il codice al progetto API Web. Questo passaggio inizia in corrispondenza di 4:35.

API Web in questo esempio offre un servizio di calcolo di base utilizzando un modello e un controller. Per aggiungere il modello per il servizio, scegliere **Aggiungi**, **classe**rapida dei **modelli** in **Esplora soluzioni** . Denominare la classe `CalcInput` e fare clic su **Aggiungi**.

Aggiungere quanto segue `using` istruzione nella parte superiore di `CalcInput.cs` file.

    using Newtonsoft.Json;

 Sostituire la classe generata con il codice riportato di seguito.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Pulsante destro del mouse **controller** in **Esplora soluzioni** e scegliere **Aggiungi**->**Controller**. Scegliere **Web API 2 Controller - vuoto** e fare clic su **Aggiungi**. Digitare **CalcController** per il nome del Controller e fare clic su **Aggiungi**.

![Aggiungere Controller][api-management-add-controller]

Aggiungere quanto segue `using` istruzione nella parte superiore di `CalcController.cs` file.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Sostituire la classe controller generato con il codice riportato di seguito. Questo codice viene implementato il `Add`, `Subtract`, `Multiply`, e `Divide` operazioni di base API di calcolatrice.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Premere **F6** per creare e verificare che la soluzione.

## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto Azure

In questo passaggio Visual Studio viene pubblicato in Azure. Questo passaggio del video inizia 5:45.

Per pubblicare il progetto in Azure, fare clic sul progetto **APIMAADDemo** in Visual Studio e scegliere **pubblica**. Mantenere le impostazioni predefinite nella finestra di dialogo **Pubblica sito Web** e fare clic su **pubblica**.

![Pubblicazione sul Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Concedere le autorizzazioni per l'applicazione di servizio back-end di Azure Active Directory

Nella directory di Azure Active Directory come parte del processo di configurazione e pubblicazione del progetto API Web viene creata una nuova applicazione del servizio di back-end. In questo passaggio del video, iniziando 6:13 autorizzazioni concesse al back-end API Web.

![Applicazione][api-management-aad-backend-app]

Fare clic sul nome dell'applicazione per configurare le autorizzazioni necessarie. Passare alla scheda **Configura** e scorrere fino alla sezione **autorizzazioni per le altre applicazioni** . Fare clic su **Autorizzazioni per l'applicazione** elenco a discesa accanto a **Windows** **Azure Active Directory**, selezionare la casella relativa **Lettura directory dati**e fare clic su **Salva**.

![Aggiungi autorizzazioni][api-management-aad-add-permissions]

>[AZURE.NOTE] Se **Windows** **Azure Active Directory** non è elencato nella casella autorizzazioni per le altre applicazioni, fare clic su **Aggiungi applicazione** e aggiungere dall'elenco.

Prendere nota dell' **App Id URI** per l'utilizzo in un passaggio successivo quando un'applicazione di Azure Active Directory è configurata per il portale di gestione di API per sviluppatori.

![Id applicazione URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importare Web API API Gestione

API sono configurate dal portale di publisher API, è possibile accedere tramite il portale classica Azure. Per accedere al portale di publisher, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Gestisci l'API prima][] .

![Portale di Publisher][api-management-management-console]

Operazioni possono essere [aggiunti alle API manualmente](api-management-howto-add-operations.md)o possono essere importati. In questo video operazioni vengono importate nel formato Swagger partendo da 6:40.

Creare un file denominato `calcapi.json` con seguente contenuto e salvarlo nel computer in uso. Assicurarsi che la `host` attributo punti per il back-end API Web. In questo esempio `"host": "apimaaddemo.azurewebsites.net"` viene utilizzato.

{"swagger": "2.0", "informazioni": {"titolo": "Calcolatrice", "descrizione": "Errore su HTTP!", "versione": "1.0"}, "host": "apimaaddemo.azurewebsites.net", "basePath": "/ api", "schemi": ["http"], "percorsi": {"/ aggiungere? un = {} e b = {b}": {"Ottieni": {"descrizione": "Risponde con una somma di due numeri.", "operationId": "Aggiungi due numeri interi", "parametri": [{"nome": "a", "in": "query", "descrizione": "primo operando. Valore predefinito è <code>51</code>. ","obbligatori": true,"predefinito":"51","enumerazione": ["51"]}, {"nome":"b","in":"query","descrizione":"secondo operando. Valore predefinito è <code>49</code>. ","obbligatori": true,"predefinito":"49","enumerazione": ["49"]}],"risposte": {}}}," / sub?a = {un} & b = {b} ": {"Ottieni": {"descrizione":"Risponde con una differenza tra due numeri.","operationId":"Sottrai due numeri interi","parametri": [{"nome":"a","in":"query","descrizione":"primo operando. Valore predefinito è <code>100</code>. ","obbligatori": true,"predefinito":"100","enumerazione": ["100"]}, {"nome":"b","in":"query","descrizione":"secondo operando. Valore predefinito è <code>50</code>. ","obbligatori": true,"predefinito":"50","enumerazione": ["50"]}],"risposte": {}}}," / div?a = {un} & b = {b} ": {"Ottieni": {"descrizione":"Risponde con un quoziente di due numeri.","operationId":"Dividere due numeri interi","parametri": [{"nome":"a","in":"query","descrizione":"primo operando. Valore predefinito è <code>100</code>. ","obbligatori": true,"predefinito":"100","enumerazione": ["100"]}, {"nome":"b","in":"query","descrizione":"secondo operando. Valore predefinito è <code>20</code>. ","obbligatori": true,"predefinito":"20","enumerazione": ["20"]}],"risposte": {}}}," / mul?a = {un} & b = {b} ": {"Ottieni": {"descrizione":"Risponde a un prodotto di due numeri.","operationId":"Moltiplicare due numeri interi","parametri": [{"nome":"a","in":"query","descrizione":"primo operando. Valore predefinito è <code>20</code>. ","obbligatori": true,"predefinito":"20","enumerazione": ["20"]}, {"nome":"b","in":"query","descrizione":"secondo operando. Valore predefinito è <code>5</code>. ","obbligatori": true,"predefinito":"5";"enumerazione": ["5"]}],"risposte": {}}}}}

Per importare la calcolatrice API, fare clic su **API** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Importa API**.

![Pulsante Importa API][api-management-import-api]

Eseguire i passaggi seguenti per configurare la calcolatrice API.

1. Fare clic su **da file**, quindi individuare il `calculator.json` file salvato e fare clic sul pulsante di opzione **Swagger** .
2. Digitare nella casella di testo **URL API Web suffisso** **calc** .
3. Fare clic sulla casella **prodotti (facoltativi)** e scegliere **Starter**.
4. Fare clic su **Salva** per importare l'API.

![Aggiungi nuova API][api-management-import-new-api]

Una volta importato l'API, verrà visualizzata la pagina Riepilogo per l'API nel portale di publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Chiamare l'API ha avuto esito negativo dal portale per sviluppatori

A questo punto, l'API importato in Gestione API, ma non è ancora chiamare con esito positivo dal portale di sviluppo perché il servizio di back-end è protetto con l'autenticazione di Azure Active Directory. Come illustrato nel video partendo da 7:40 procedendo come segue.

Fare clic su **portale per sviluppatori** dalla parte superiore destra del portale di publisher.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** e fare clic su **Calcolatrice** API.

![Portale per sviluppatori][api-management-dev-portal-apis]

Fare clic su **Provalo**.

![Provare a][api-management-dev-portal-try-it]

Fare clic su **Invia** e prendere nota di stato della risposta della **401 non autorizzato**.

![Invia][api-management-dev-portal-send-401]

La richiesta non è autorizzata perché back-end API è protetto da Azure Active Directory. Prima di chiamare correttamente l'API sviluppatore del portale deve essere configurato per autorizzare gli sviluppatori che utilizzano OAuth 2.0. Questo processo viene descritto nelle sezioni seguenti.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Eseguire la registrazione del portale per sviluppatori di un'applicazione AAD

Il primo passaggio della configurazione del portale per sviluppatori per autorizzare gli sviluppatori che utilizzano OAuth 2.0 è per registrare il portale per sviluppatori di un'applicazione AAD. Come illustrato iniziando 8:27 nel video.

Spostarsi nel tenant di Azure Active Directory dal primo passaggio del video, in questo esempio **APIMDemo** e passare alla scheda **applicazioni** .

![Nuova applicazione][api-management-aad-new-application-devportal]

Fare clic sul pulsante **Aggiungi** per creare una nuova applicazione di Azure Active Directory e scegliere **Aggiungi un'applicazione di sviluppo di organizzazione**.

![Nuova applicazione][api-management-new-aad-application-menu]

Scegliere **applicazione Web e/o API Web**, immettere un nome e fare clic sulla freccia avanti. In questo esempio viene utilizzato **APIMDeveloperPortal** .

![Nuova applicazione][api-management-aad-new-application-devportal-1]

Per **l'accesso URL** immettere l'URL del servizio di gestione delle API e aggiungere `/signin`. In questo esempio viene utilizzato **https://contoso5.portal.azure-api.net/signin **.

Per **App Id URL** immettere l'URL del servizio di gestione delle API e aggiungere alcuni caratteri univoci. Può trattarsi di qualsiasi carattere desiderati e in questo esempio viene utilizzato **https://contoso5.portal.azure-api.net/dp** . Quando vengono configurati desiderate **proprietà App** , fare clic sul segno di spunta per creare l'applicazione.

![Nuova applicazione][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurare un server di autorizzazione API Management OAuth 2.0

Il passaggio successivo consiste nel configurare un server di autorizzazione OAuth 2.0 API gestione. Questo passaggio viene illustrato il video a partire dal 9:43.

Fare clic su **protezione** dal menu Gestione API sul lato sinistro e quindi fare clic su **Aggiungi autorizzazione** server **2.0 OAuth**.

![Aggiungere il server di autorizzazione][api-management-add-authorization-server]

Immettere un nome e una descrizione facoltativa nelle caselle **nome** e **Descrizione** . Questi campi vengono utilizzati per identificare il server di autorizzazione 2.0 OAuth all'interno dell'istanza di servizio di gestione delle API. In questo esempio viene usata **demo server autorizzazione** . In seguito quando si specifica un server 2.0 OAuth da utilizzare per l'autenticazione per un'API, selezionare il nome.

Per l' **URL della pagina registrazione Client** immettere un valore di segnaposto, ad esempio `http://localhost`.  L' **URL della pagina registrazione Client** faccia riferimento alla pagina che gli utenti possono usare per creare e configurare gli account personali per OAuth 2.0 provider che supportano la gestione degli account utente. In questo esempio gli utenti non creare e configurare i propri account in modo che venga usato un segnaposto.

![Aggiungere server di autorizzazione][api-management-add-authorization-server-1]

Successivamente specificare **autorizzazione endpoint URL** e **Token URL endpoint**.

![Server di autorizzazione][api-management-add-authorization-server-1a]

Questi valori possono essere recuperati dalla pagina **App endpoint** dell'applicazione AAD creata per il portale per sviluppatori. In access i punti finali passare alla scheda **Configura** per l'applicazione AAD e fare clic su **endpoint di visualizzazione**.

![Applicazione][api-management-aad-devportal-application]

![Visualizza i punti finali][api-management-aad-view-endpoints]

Copiare l' **endpoint di autorizzazione OAuth 2.0** e incollarla nella casella di testo **URL endpoint di autorizzazione** .

![Aggiungere il server di autorizzazione][api-management-add-authorization-server-2]

Copiare l' **endpoint token OAuth 2.0** e incollarla nella casella di testo **URL endpoint Token** .

![Aggiungere il server di autorizzazione][api-management-add-authorization-server-2a]

Oltre a incollare nell'endpoint del token, aggiungere un parametro aggiuntivo corpo denominato **risorsa** e per l'utilizzo di valore **App Id URI** dall'applicazione AAD per il servizio di back-end che è stato creato quando la pubblicazione del progetto di Visual Studio.

![Id applicazione URI][api-management-aad-sso-uri]

Successivamente, specificare le credenziali del client. Queste sono le credenziali per la risorsa che si desidera accedere, in questo caso il servizio di back-end.

![Credenziali client][api-management-client-credentials]

Per ottenere l' **Id Client**, passare alla scheda **configurazione** dell'applicazione AAD per il servizio di back-end e copiare l' **Id Client**.

Fare clic su **Segreto del Client** **Selezionare durata** elenco a discesa nella sezione **chiavi** e specificare un intervallo. In questo esempio viene utilizzato un anno.

![ID client][api-management-aad-client-id]

Fare clic su **Salva** per salvare la configurazione e visualizzare la chiave. 

>[AZURE.IMPORTANT] Prendere nota di questo tasto. Dopo aver chiuso la finestra di configurazione di Azure Active Directory, il tasto non è possibile visualizzare nuovamente.

Copiare negli Appunti il tasto, tornare al portale di publisher, incollare la chiave nella casella di testo **Segreto del Client** e fare clic su **Salva**.

![Aggiungere il server di autorizzazione][api-management-add-authorization-server-3]

Immediatamente dopo le credenziali del client su concessione di codice di autorizzazione. Copiare il codice di autorizzazione e tornare all'applicazione del portale per sviluppatori di Azure Active Directory configurare pagina e incollare l'autorizzazione concedere nel campo **URL di risposta** e fare di nuovo clic su **Salva** .

![URL di risposta][api-management-aad-reply-url]

Il passaggio successivo consiste nel configurare le autorizzazioni per l'applicazione AAD del portale per sviluppatori. Fare clic su **Autorizzazioni per l'applicazione** e selezionare la casella relativa **Lettura directory dati**. Fare clic su **Salva** per salvare la modifica e quindi fare clic su **Aggiungi applicazione**.

![Aggiungi autorizzazioni][api-management-add-devportal-permissions]

Fare clic sull'icona di ricerca, digitare **APIM** a partire dalla casella, selezionare **APIMAADDemo**e fare clic sul segno di spunta per salvare.

![Aggiungi autorizzazioni][api-management-aad-add-app-permissions]

Fare clic su **Autorizzazioni di delega** per **APIMAADDemo** e selezionare la casella relativa **APIMAADDemo Access**e fare clic su **Salva**. In questo modo lo sviluppatore dell'applicazione del portale per accedere al servizio di back-end.

![Aggiungi autorizzazioni][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Attivare l'autorizzazione utente OAuth 2.0 per l'API calcolatrice

Dopo avere configurato il server OAuth 2.0, è possibile specificare le impostazioni di sicurezza per l'API. Questo passaggio viene illustrato il video iniziando 14:30.

Fare clic su **API** nel menu a sinistra e fare clic su **Calcolatrice** per visualizzare e configurare le impostazioni.

![Calcolatrice API][api-management-calc-api]

Passare alla scheda **protezione** , selezionare la casella di controllo **OAuth 2.0** , selezionare il server di autorizzazione desiderato dall'elenco a discesa **server di autorizzazione** e fare clic su **Salva**.

![Calcolatrice API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Chiamare correttamente l'API calcolatrice dal portale per sviluppatori

Dopo avere configurato l'autorizzazione OAuth 2.0 sull'API, le operazioni possono essere chiamate correttamente dall'interfaccia di sviluppo. Questo passaggio viene illustrato il video inizia con 15:00.

Tornare all'operazione di **aggiungere due numeri interi** del servizio di calcolatrice nel portale di sviluppo e fare clic su **Provalo**. Notare il nuovo elemento nella sezione **autorizzazione** corrisponde al server di autorizzazione che appena aggiunto.

![Calcolatrice API][api-management-calc-authorization-server]

Selezionare **il codice di autorizzazione** dall'elenco a discesa autorizzazione e immettere le credenziali dell'account da utilizzare. Se sono già eseguito l'accesso con l'account che potrebbe non essere richiesto.

![Calcolatrice API][api-management-devportal-authorization-code]

Fare clic su **Invia** e prendere nota di **stato risposta** dei **200 OK** e i risultati dell'operazione di contenuti risposta.

![Calcolatrice API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurare un'applicazione desktop per chiamare l'API

La procedura successiva nel video inizia 16:30 e configura un'applicazione desktop semplice per chiamare l'API. Il primo passaggio consiste nel registrare l'applicazione desktop in Azure Active Directory e consentire l'accesso alla directory e al servizio di back-end. 18:25 esiste una dimostrazione dell'applicazione desktop richiamare un'operazione su Calcolatrice API.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurare un criterio di convalida JWT per pre-autorizzare le richieste

La procedura finale del video inizia 20:48 e viene illustrato come utilizzare il criterio di [Convalidare JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) per pre-autorizzare le richieste per convalidare i token di accesso di ogni richiesta in arrivo. Se la richiesta non è convalidata in base ai criteri di convalidare JWT, la convocazione è bloccata da Gestione API e non passare a back-end.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Per un'altra dimostrazione di configurazione e utilizzo di questo criterio, vedere [Cloud coprire episodio 177: altre funzionalità di gestione dell'API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e invece 13:50. Passaggio rapido in 15:00 per vedere i criteri configurati nell'editor criteri e quindi su 18:50 per una dimostrazione della chiamata un'operazione dal portale di sviluppo con e senza il token di autorizzazione necessaria.

## <a name="next-steps"></a>Passaggi successivi
-   Guardare [i video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sulla gestione delle API.
-   Per altri modi proteggere il servizio di back-end, vedere [l'autenticazione certificato reciproca](api-management-howto-mutual-certificates.md) e la [connessione tramite VPN o ExpressRoute](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Gestire la prima API]: api-management-get-started.md
