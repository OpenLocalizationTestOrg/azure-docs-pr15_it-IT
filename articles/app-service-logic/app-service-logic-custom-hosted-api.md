<properties
    pageTitle="Chiamare una API personalizzata nelle App logica"
    description="Tramite l'API personalizzati ospitati in App servizio con logica App"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>Tramite l'API personalizzati ospitati in App servizio con logica App

Anche se logica App offre una vasta gamma di 40 connettori per un'ampia varietà di servizi, può essere necessario chiamare il proprio API personalizzati che è possibile eseguire il codice personalizzato. Uno dei modi più facile e più scalabili per ospitare il proprio web personalizzata dell'API consiste nell'usare il servizio di App. In questo articolo viene descritto come intervenire in qualsiasi web API ospitato in un'app API del servizio di App, online o app per dispositivi mobili.

Per informazioni sulla creazione di API come trigger o azione all'interno di logica App, vedere [questo articolo](app-service-logic-create-api-app.md).

## <a name="deploy-your-web-app"></a>Distribuire un'App Web

Prima di tutto, è necessario distribuire l'API come un'App Web nel servizio di App. Le istruzioni visualizzate coprono distribuzione di base: [creare un'app web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Sebbene sia possibile chiamare in qualsiasi API da un'app per logica, per ottenere risultati migliori è consigliabile che aggiungere metadati Swagger l'integrazione con le operazioni logiche app.  È possibile trovare informazioni dettagliate [sull'aggiunta di swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Impostazione delle API

Affinché la progettazione di applicazioni logica analizzare il Swagger, è importante abilitare CORS e impostare le proprietà APIDefinition dell'app web.  Questo è molto semplice impostare all'interno del portale di Azure.  È sufficiente aprire e l'impostazioni dell'applicazione Web e nella sezione API impostare il 'definizione dell'API' all'URL del file swagger.json (si tratta in genere https://{name}.azurewebsites.net/swagger/docs/v1) e aggiungere un criterio CORS per ' *' per consentire richieste dalle app di logica Designer.

## <a name="calling-into-the-api"></a>Accedono API

Quando all'interno del portale di App logica, se si impostano CORS e le proprietà di definizione dell'API dovrebbe essere possibile facilmente aggiungere azioni personalizzate API all'interno del flusso.  Nella finestra di progettazione è possibile selezionare per esplorare i siti Web abbonamento per elencare i siti Web con un URL swagger definito.  È inoltre possibile utilizzare il protocollo HTTP + Swagger azione per scegliere un swagger e nell'elenco delle azioni disponibili e input.  Infine, è sempre possibile creare una richiesta di qualsiasi API, anche quelli che non hanno o esporre un documento swagger tramite l'azione HTTP.

Se si desidera proteggere l'API, esistono due diversi modi per eseguire questa operazione:

1. Nessuna modifica codice obbligatorio - Azure Active Directory è utilizzabile per proteggere l'API senza codice modifiche o ridistribuzione.
1. Applicare l'autenticazione di base, AAD Auth o certificato di autenticazione nel codice dell'API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Proteggere le chiamate all'API senza una modifica al codice

In questa sezione, sarà necessario creare due applicazioni di Azure Active Directory, uno per l'app logica e uno per un'App Web.  È necessario autenticare chiamate all'App Web usando il capitale di servizio (id client e segreto) associato all'applicazione di AAD per l'app logica. Infine, è necessario includere l'applicazione ID nella definizione dell'app logica.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Parte 1: Impostazione di un'identità di applicazione per l'app logica

Si tratta in quale app logica viene utilizzata per eseguire l'autenticazione in active directory. È solo *necessario* farlo una volta per la directory. Ad esempio, è possibile scegliere di utilizzare la stessa identità per tutte le app di logica, anche se è anche possibile creare identità univoche per app logica se lo si desidera. È possibile eseguire questa operazione nell'interfaccia utente o usare PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Creare l'identità dell'applicazione tramite il portale classico Azure

1. Passare a [Active directory nel portale di classica Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selezionare la directory che usano per un'App Web
2. Fare clic sulla scheda **applicazioni**
3. Fare clic su **Aggiungi** nella barra dei comandi nella parte inferiore della pagina
4. Specificare un nome da utilizzare, fare clic sulla freccia avanti per l'identità
5. Inserire in una stringa univoca formattata come un dominio in due caselle di testo e fare clic sul segno di spunta
6. Fare clic sulla scheda **configurazione** dell'applicazione
7. Copiare l' **ID Client**, verrà utilizzato nell'app logica
8. Nella sezione **chiavi** fare clic su **Seleziona durata** e scegliere anno 1 o 2 anni
9. Fare clic sul pulsante **Salva** nella parte inferiore dello schermo (può essere necessario attendere alcuni secondi)
10. A questo punto occorre copiare la chiave nella casella. Verrà utilizzato anche nell'app logica

#### <a name="create-the-application-identity-using-powershell"></a>Creare l'identità dell'applicazione tramite PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Accertarsi di copiare l' **ID Tenant**, l' **ID dell'applicazione** e la password che è stata utilizzata

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Parte 2: Proteggere un'applicazione Web con un'identità di app AAD

Se è già stata distribuita un'applicazione Web è possibile attivare solo nel portale. In caso contrario, è possibile apportare attivazione dell'autorizzazione a una parte della distribuzione di manager delle risorse di Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Attivare l'autorizzazione nel portale di Azure

1. Passare all'app Web e fare clic su **Impostazioni** nella barra dei comandi.
2. Fare clic su **Autorizzazione/autenticazione**.
3. **Attivarla.**

A questo punto, un'applicazione viene creata automaticamente. ID Client dell'applicazione è necessario per parte 3, in modo che è necessario:

1. Passare a [Active directory nel portale di classica Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selezionare la directory.
2. Cercare l'app nella casella di ricerca
3. Fare clic su di esso nell'elenco
4. Fare clic sulla scheda **Configura**
5. Verrà visualizzato l' **ID Client**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Distribuire un'App Web utilizzando un modello ARM

Prima di tutto, è necessario creare un'applicazione per un'app Web. Dovrebbe essere diversa dall'applicazione che viene utilizzato per l'app logica. Prima di tutto seguire i passaggi indicati nella parte 1, ma ora per l'utilizzo della **home page** e **IdentifierUris** l' effettivo https://**URL** dell'applicazione Web.

>[AZURE.NOTE]Quando si crea l'applicazione per un'app Web, è necessario utilizzare l' [approccio portale classica Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), come il cmdlet di PowerShell non impostare le autorizzazioni necessarie per gli utenti ad accedere a un sito Web.

Dopo avere client ID e ID tenant, includono le seguenti come risorsa sub dell'app Web nel modello di distribuzione:

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Per eseguire una distribuzione automaticamente che distribuisce un vuoto Web app e app logica insieme che utilizzano AAD, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Per il modello completo, vedere [chiamate app logica in un'API personalizzati ospitati in App servizio e protetto da AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Parte 3: Popolare la sezione autorizzazioni nell'app logica

Nella sezione **autorizzazione** dell'azione **HTTP** :`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrizione |
|---------|-------------|
| tipo | Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è ActiveDirectoryOAuth. |
| tenant | Identificatore del tenant utilizzato per identificare il tenant di Active Directory. |
| gruppo di destinatari | Obbligatorio. Risorsa che si è connessi. |
| clientID | Identificatore di client per l'applicazione di Azure Active Directory. |
| segreto | Obbligatorio. Segreto del client che richiede il token. |

Il modello sopra dispone già di questa configurazione, ma se si sta creando app logica direttamente, è necessario includere la sezione autorizzazioni completo.

## <a name="securing-your-api-in-code"></a>Proteggere le API nel codice

### <a name="certificate-auth"></a>Autenticazione certificato

È possibile utilizzare i certificati Client per convalidare le richieste in arrivo a un'applicazione Web. Vedere [How To Configure TLS reciproca Authentication di Web App](../app-service-web/app-service-web-configure-tls-mutual-auth.md) per la procedura impostare il codice.

Nella sezione *autorizzazioni* è necessario fornire: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descrizione |
|---------|-------------|
| tipo | Obbligatorio. Tipo di autenticazione. Per i certificati SSL client il valore deve essere ClientCertificate. |
| PFX | Obbligatorio. Codifica base 64 contenuto del file PFX. |
| password | Obbligatorio. Password per accedere al file PFX. |

### <a name="basic-auth"></a>Autenticazione di base

È possibile utilizzare l'autenticazione di base (ad esempio nome utente e password) per convalidare le richieste in arrivo. È possibile eseguire in qualsiasi lingua di che si creano le app autenticazione di base è un modello comune.

Nella sezione *autorizzazioni* , è necessario fornire: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrizione |
|---------|-------------|
| tipo | Obbligatorio. Tipo di autenticazione. Per l'autenticazione di base, il valore deve essere Basic. |
| nome utente | Obbligatorio. Nome utente per eseguire l'autenticazione. |
| password | Obbligatorio. Password per l'autenticazione. |

### <a name="handle-aad-auth-in-code"></a>Gestire auth AAD nel codice

Per impostazione predefinita, l'autenticazione di Azure Active Directory che consentono nel portale di eseguire un'autorizzazione. Ad esempio, non blocca l'API a un utente specifico o un'app, ma solo in un determinato tenant.

Se si desidera limitare l'API a solo la logica app, ad esempio, nel codice, è possibile estrarre l'intestazione che contiene la JWT e verificare che il chiamante è, rifiutare qualsiasi richiesta che non corrisponda.

Procedere, se si desidera implementare interamente nel codice e non sfruttare la caratteristica portale, è possibile leggere questo articolo: [Utilizzare Active Directory per l'autenticazione in Azure App servizio](../app-service-web/web-sites-authentication-authorization.md).

È comunque necessario seguire la procedura precedente per creare un'identità di applicazione per l'app logica e che consente di chiamare l'API.
