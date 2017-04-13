<properties
    pageTitle="Utilizzo di servizio di gestione di API per generare le richieste HTTP"
    description="Informazioni su come usare i criteri di richiesta e risposta in Gestione API per chiamare servizi esterni dall'API"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="using-external-services-from-the-azure-api-management-service"></a>Uso di servizi esterni dal servizio di gestione delle API di Azure

I criteri disponibili nel servizio di gestione dell'API di Azure è possono eseguire una vasta gamma di lavoro utile basato sui richiesta in arrivo, la risposta in uscita e informazioni di configurazione di base. Tuttavia, la possibilità di interagire con i servizi esterni dalla gestione API criteri apre molte altre opportunità.

Abbiamo visto in precedenza come è possibile interagire con il [servizio di Azure evento Hub per la registrazione, monitoraggio e analitica](api-management-log-to-eventhub-sample.md). In questo articolo è mostrerà servizio in base a criteri che consentono di interagire con qualsiasi HTTP esterni. Questi criteri possono essere utilizzati per l'attivazione eventi remoti o per il recupero di informazioni che verranno usate per modificare la richiesta e risposta in modo originale.

## <a name="send-one-way-request"></a>Richiesta modo di quello invio
Probabilmente l'interazione esterno più semplice è lo stile fire e dimenticare della richiesta che consente a un servizio esterno ricevere una notifica di un tipo di evento importante. È possibile utilizzare il criterio di flusso di controllo `choose` per rilevare qualsiasi tipo di condizione che si è interessati e, se viene soddisfatta la condizione, è possibile apportare una richiesta HTTP esterna utilizzando il criterio di [richiesta modo di quello invio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Può trattarsi di una richiesta di un sistema di messaggistica come Hipchat o un margine di flessibilità o un messaggio di posta API come SendGrid o MailChimp o per interventi di supporto critico qualcosa di simile a PagerDuty. Tutti i sistemi di messaggistica dispongono APIs HTTP semplice che è possibile richiamare facilmente.

### <a name="alerting-with-slack"></a>Avviso con il margine di flessibilità
Nell'esempio seguente viene illustrato come inviare un messaggio a un margine di flessibilità di chat room se il codice di stato risposta HTTP è maggiore o uguale a 500. Un errore di 500 intervallo indica un problema con il nostro back-end API che il client dell'API non riesce a risolvere se stessi. In genere richiede l'esecuzione di un tipo di intervento da parte.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Margine di flessibilità è il concetto di hook web in ingresso. Quando si configura un latino web in ingresso, il margine di flessibilità genera un URL speciale che consente di eseguire una semplice richiesta POST e per passare a un messaggio nel canale margine di flessibilità. Corpo JSON che verranno create si basa su un formato definito dal margine di flessibilità.

![Margine di flessibilità Web latino](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Trova fire e dimenticare correttamente?
Esistono alcuni compromessi quando si usa uno stile fire e dimenticare della richiesta. Se per qualche motivo la richiesta non riesce, quindi l'errore non verranno segnalato. In questo caso particolare, non sono garantita la complessità della presenza di un errore secondario dei report di sistema e il costo di prestazioni di attesa di una risposta. Per gli scenari in cui è essenziale per la verifica della risposta, quindi il criterio di [richiesta di invio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) è un'opzione migliore.

## <a name="send-request"></a>Richiesta di invio
Il `send-request` consente di criteri tramite un servizio esterno per eseguire funzioni di elaborazione complessa e restituire dati per la gestione dell'API del servizio che possono essere usate per un'ulteriore elaborazione dei criteri.

### <a name="authorizing-reference-tokens"></a>Per l'autorizzazione token di riferimento
Funzione principale della gestione delle API protegge risorse back-end. Se il server di autorizzazione utilizzato tramite l'API crea [token JWT](http://jwt.io/) come parte del flusso di OAuth2, come avviene [Azure Active Directory](../active-directory/active-directory-aadconnect.md) , è possibile utilizzare il `validate-jwt` criteri per verificare la validità del token. Tuttavia, alcuni server autorizzazione creare cosa denominato [token di riferimento](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) che non possono essere verificate senza eseguire una chiamata al server di autorizzazione.

### <a name="standardized-introspection"></a>Introspezione standard
In passato non si è verificato alcun modo standard per la verifica di un token di riferimento con un server di autorizzazione. Tuttavia un recente proposta standard [RFC 7662](https://tools.ietf.org/html/rfc7662) è stato pubblicato dall'IETF che consente di definire come un server di risorse possibile verificare la validità del token.

### <a name="extracting-the-token"></a>L'estrazione del token
Il primo passaggio consiste per estrarre il token dall'intestazione di autorizzazione. Il valore dell'intestazione deve essere formattato con il `Bearer` schema di autorizzazione, quindi fare clic su uno spazio e quindi il token di autorizzazione in base a [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Purtroppo esistono casi in cui viene omesso la combinazione di autorizzazione. Per questa situazione durante l'analisi, abbiamo dividere il valore dell'intestazione su uno spazio e selezionare l'ultima stringa dalla matrice restituita di stringhe. Si tratta di una possibile soluzione per le intestazioni di autorizzazione formato non corretto.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Effettua la richiesta di convalida
Una volta ottenuto il token di autorizzazione, è possibile effettuare la richiesta per convalidare il token. RFC 7662 chiama introspezione questo processo e richiede che si `POST` un modulo HTML per la risorsa introspezione. Il modulo HTML deve contenere almeno una coppia chiave/valore con il tasto `token`. La richiesta al server di autorizzazione deve essere autenticata anche per assicurarsi che non è possibile passare dovuti client non autorizzati per i token validi.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>Verifica la risposta
Il `response-variable-name` attributo viene utilizzato per concedere l'accesso risposta restituito. Il nome definito in questa proprietà può essere utilizzato come chiave nel `context.Variables` accesso al dizionario di `IResponse` oggetto.

Dall'oggetto risposta è possibile recuperare il corpo e RFC 7622 indica che la risposta deve essere un oggetto JSON e deve contenere almeno una proprietà denominata `active` che è un valore boolean. Quando `active` è vera, il token è considerato valido.

### <a name="reporting-failure"></a>Errori
Serve un `<choose>` criteri per rilevare se il token non è valido e in caso affermativo, restituire una risposta 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

In base a [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) che descrive come `bearer` token devono essere utilizzati, è inoltre essere restituito un `WWW-Authenticate` intestazione con la risposta 401. Autenticazione WWW viene usata per indicare un client su come creare una richiesta di correttamente autorizzata. A causa di ampia gamma di approcci possibili con il framework degli OAuth2, è difficile da comunicare tutte le informazioni necessarie. Sono disponibili per attività in corso per consentire [ai client di individuare come correttamente autorizzare richieste a un server di risorse](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Soluzione finale
Applicazione pratica di tutti gli elementi insieme, è visualizzato il seguente criterio:

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Questo è solo uno dei molti esempi di come la `send-request` criterio può essere utilizzato per integrare il processo di richieste e risposte che scorrono attraverso il servizio di gestione dell'API utili servizi esterni.

## <a name="response-composition"></a>Risposta composizione
Il `send-request` criterio può essere utilizzato per il miglioramento di una richiesta di primaria per un sistema di back-end, come illustrato nell'esempio precedente o può essere utilizzato come un Sostituisci completa della chiamata back-end. Questa tecnica è possibile creare facilmente composte risorse che vengono aggregate da più sistemi diversi.

### <a name="building-a-dashboard"></a>Creazione di un dashboard   
Può succedere che si desidera poter per esporre le informazioni che è presente in più sistemi di back-end, ad esempio, per indirizzare un dashboard. Gli indicatori KPI provengono da tutte le diverse back-end, ma si preferisce non accesso diretto agli stessi e sarebbe utile se tutte le informazioni potrebbero essere recuperate in un'unica richiesta. Ad esempio alcune informazioni back-end deve alcune sezionare e anelli e sterilizzare prima di tutto un po'! La possibilità di memorizzare nella cache la risorsa composta sarebbe utile per ridurre il carico di back-end se ne conosca gli utenti hanno gli strumenti di martellamenti il tasto F5 per verificare se le metriche underperforming potrebbero essere modificati.    

### <a name="faking-the-resource"></a>Falsificare la risorsa
Il primo passo per creare la risorsa dashboard consiste nel configurare una nuova operazione nel portale di publisher API gestione. Questo sarà un'operazione di segnaposto utilizzata per configurare le regole di composizione per creare la risorsa dinamica.

![Operazione di dashboard](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Apportare le richieste
Una volta il `dashboard` operazione è stata creata per è possibile configurare un criterio specificamente per tale operazione. 

![Operazione di dashboard](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Il primo passaggio consiste per estrarre i parametri di query da richiesta in arrivo, in modo che è possibile inoltrarle alla nostro back-end. In questo esempio nostro dashboard viene visualizzato informazioni in base a un periodo di tempo un conterrà un `fromDate` e `toDate` parametro. È possibile utilizzare il `set-variable` criteri per estrarre le informazioni dall'URL della richiesta.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Una volta che si dispone di queste informazioni è possibile far richieste a tutti i sistemi di back-end. Ogni richiesta crea un nuovo URL con le informazioni sui parametri e chiama rispettivo server e archivia la risposta in una variabile di contesto.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Queste richieste verranno eseguito in sequenza, non è ideale. In una versione futura verranno introdotte un nuovo criterio denominato `wait` che consentiranno di tutte le richieste di eseguire in parallelo.

### <a name="responding"></a>Blocca

Per creare la risposta composta è possibile utilizzare il criterio [restituito risposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . Il `set-body` elemento è possibile utilizzare un'espressione per creare un nuovo `JObject` con tutte le rappresentazioni componente incorporate come proprietà.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

Criteri completi simile al seguente:

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

Nella configurazione del segnaposto operazione per la risorsa dashboard da memorizzare nella cache per almeno un'ora perché siamo consapevoli natura dei dati è possibile configurare significa che anche se si tratta di un'ora è aggiornata, che ma potrà comunque essere sufficientemente efficace comunicare informazioni importanti per gli utenti.

## <a name="summary"></a>Riepilogo
Servizio di gestione di API Azure fornisce criteri flessibili che possono essere applicati in modo selettivo per il traffico HTTP e consente la composizione dei servizi back-end. Se si vuole migliorare il gateway API con gli avvisi di funzioni, verifica, la funzionalità di convalida o creare nuove risorse composte in base a più servizi back-end, il `send-request` e un mondo di possibilità di aprire criteri correlati.

## <a name="watch-a-video-overview-of-these-policies"></a>Guardare un video introduttivo di questi criteri
Per ulteriori informazioni su [richiesta modo di quello invio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [richiesta di invio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)e criteri di [Invio risposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) in questo articolo illustrati, guardare il video seguente.

> [AZURE.VIDEO send-request-and-return-response-policies]
