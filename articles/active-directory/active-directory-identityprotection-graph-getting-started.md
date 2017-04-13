<properties
    pageTitle="Introduzione a Microsoft Graph e Azure Active Directory identità protezione | Microsoft Azure"
    description="Fornisce un'introduzione alle query Microsoft Graph per un elenco di eventi di rischio e le relative informazioni da Azure Active Directory."
    services="active-directory"
    keywords="la protezione dell'identità di Azure active directory, rischio, vulnerabilità, criteri di sicurezza, Microsoft Graph"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Iniziare a utilizzare la protezione dell'identità Azure Active Directory e Microsoft Graph

Microsoft Graph è endpoint API unificata Microsoft e la home page di [Azure Active Directory la protezione dell'identità](active-directory-identityprotection.md) dell'API. L'API prima **identityRiskEvents**consente di eseguire query di Microsoft Graph per un elenco di [eventi di rischio](active-directory-identityprotection-risk-events-types.md) e le relative informazioni. In questo articolo viene fornita un'introduzione query questa API. Per una profondità in Introduzione, documentazione completa e accesso a Esplora aree di grafico, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/).

Esistono tre passaggi per l'accesso ai dati di protezione dell'identità tramite Microsoft Graph:

1. Aggiungere un'applicazione con una chiave privata client. 

2. Usare questo segreto e alcuni altri tipi di informazioni per l'autenticazione a Microsoft Graph, in cui si riceve un token di autenticazione. 

3. Utilizzare questo token per richiedere all'endpoint API e tornare identità la protezione dei dati.

Prima di iniziare, è necessario:

- Privilegi di amministratore per creare l'applicazione in Azure Active Directory
- Il nome del dominio del tenant (ad esempio contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Aggiungere un'applicazione con una chiave privata client


1. [Accedere](https://manage.windowsazure.com) al portale classico Azure come amministratore. 

1. Nel riquadro di spostamento sinistra scegliere **Active Directory**. 

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Nel menu nella parte superiore, fare clic su **applicazioni**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. Nella finestra di dialogo **inviare commenti e suggerimenti sull'applicazione** , procedere come segue:

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    un. Nella casella di testo **nome** digitare un nome per l'applicazione (ad esempio: applicazione API evento di rischio AADIP).

    b. In **tipo**selezionare **Web API applicazione e/o Web**.

    c. Fare clic su **Avanti**.


5. Nella finestra di dialogo **proprietà di App** , procedere come segue:

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    un. Nella casella di testo **URL Sign-On** digitare `http://localhost`.

    b. Nella casella di testo **App ID URI** digitare `http://localhost`.

    c. Fare clic su **completa**.


È possibile configurare l'applicazione.

![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere l'autorizzazione di applicazione per utilizzare l'API


1. Nella pagina dell'applicazione nel menu nella parte superiore, fare clic su **Configura**. 

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Nella sezione **autorizzazioni ad altre applicazioni** , fare clic su **Aggiungi applicazione**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. Nella finestra di dialogo **autorizzazioni per le altre applicazioni** , procedere come segue:

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    un. Selezionare **Microsoft Graph**.

    b. Fare clic su **completa**.


1. Fare clic su **le autorizzazioni delle applicazioni: 0**e quindi selezionare **leggere le informazioni sugli eventi di rischio identità**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Fare clic su **Salva** nella parte inferiore della pagina.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Ottenere un tasto di scelta

1. Nella pagina dell'applicazione nella sezione **chiavi** selezionare 1 anno come durata.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Fare clic su **Salva** nella parte inferiore della pagina.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. Nella sezione tasti, copiare il valore della chiave appena creato e quindi incollarlo in un percorso sicuro.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Se si perde la chiave, sarà necessario tornare in questa sezione e creare una nuova chiave. Mantenere questo tasto un segreto: tutti gli utenti che è possibile accedere ai dati.


1. Nella sezione **proprietà** copiare l' **ID Client**e quindi incollarlo in un percorso sicuro. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Eseguire l'autenticazione a Microsoft Graph e l'API di eventi di rischio identità della query

A questo punto, è necessario avere:

- ID acquirente copiato in precedenza

- Il tasto copiato in precedenza

- Il nome del dominio del tenant


Per eseguire l'autenticazione, inviare una richiesta di post per `https://login.microsoft.com` con i parametri seguenti nel corpo:

- grant_type: "**client_credentials**"

- risorsa: "**https://graph.microsoft.com**"

- client_id:<your client ID>

- client_secret:<your key>


> [AZURE.NOTE] È necessario fornire i valori per **client_id** e il parametro **client_secret** .

In caso contrario, viene restituito un token di autenticazione.  
Per chiamare l'API, creare un'intestazione con il parametro seguente:

    `Authorization`=”<token_type> <access_token>"


Durante l'autenticazione, è possibile trovare il tipo di token e token di accesso in token restituito.

Invia questa intestazione come una richiesta per l'URL dell'API seguente:`https://graph.microsoft.com/beta/identityRiskEvents`

La risposta, in caso contrario, è un insieme di eventi di rischio identità e dati associati in formato OData JSON, che può essere analizzato e gestito secondo necessità.

Ecco il codice di esempio per l'autenticazione e la chiamata dell'API tramite Powershell.  
Aggiungere l'ID client principali e dominio del tenant.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Passaggi successivi

Congratulazioni, la prima chiamata a Microsoft Graph appena apportate!  
A questo punto è possibile richiedere eventi di rischio identità e utilizzare i dati per adattarlo.

Per ulteriori informazioni su Microsoft Graph e modalità di compilazione di applicazioni tramite l'API di grafico, controllare la [documentazione](https://graph.microsoft.io/docs) e molto più nel [sito di Microsoft Graph](https://graph.microsoft.io/). Inoltre, assicurarsi di inserire un segnalibro della pagina di [protezione di Azure Active Directory identità API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) in cui sono elencate tutte le API di protezione identità disponibili nel grafico. Mentre si aggiungono nuove modalità per lavorare con protezione identità mediante l'API, sarà possibile visualizzarle nella pagina.


## <a name="additional-resources"></a>Risorse aggiuntive

- [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md)

- [Tipi di eventi di rischio rilevati da Azure Active Directory identità protezione](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Panoramica di Microsoft Graph](https://graph.microsoft.io/docs)

- [Radice del servizio di Azure Active Directory identità di protezione](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
