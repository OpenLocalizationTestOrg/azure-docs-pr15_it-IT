<properties
    pageTitle="Le applicazioni che utilizzano le regole di accesso condizionale di Azure Active Directory | Microsoft Azure"
    description="Controllo di accesso condizionato, Azure Active Directory controlla condizioni specifiche durante l'autenticazione dell'utente e per consentire l'accesso dell'applicazione."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Applicazioni che utilizzano le regole di accesso condizionale di Azure Active Directory

Le regole di accesso condizionale sono supportate in Azure Active Directory (Azure Active Directory)-applicazioni connesse, pre-integrata software federato come un servizio applicazioni, che utilizzano password single sign-on (SSO), applicazioni line-of-business e le applicazioni che utilizzano Proxy di applicazione Azure Active Directory. Per un elenco dettagliato delle applicazioni di cui è possibile utilizzare access condizionale, vedere [servizi attivati con accesso condizionale](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Accesso condizionato funziona sia con le applicazioni desktop e portatili che usa l'autenticazione moderno. In questo articolo sono illustrate come condizionale works access nelle applicazioni desktop e portatili.

È possibile utilizzare pagine di accesso Azure Active Directory nelle applicazioni che utilizzano l'autenticazione moderno. Con una pagina di accesso, un utente è richiesto per l'autenticazione a più fattori. Verrà visualizzato un messaggio se l'accesso dell'utente è bloccato. Autenticazione moderno è necessaria per il dispositivo per eseguire l'autenticazione con Azure Active Directory, in modo che i criteri basati su dispositivo accesso condizionale vengono valutati.

È importante sapere quali applicazioni possono utilizzare le regole di accesso condizionale e i passaggi che potrebbe essere necessario eseguire per proteggere altri punti di ingresso dell'applicazione.

## <a name="applications-that-use-modern-authentication"></a>Applicazioni che utilizzano l'autenticazione moderno

> [AZURE.NOTE] Se si dispone di un criterio di accesso condizionale in Azure Active Directory con un equivalente in Office 365, configurare entrambi i criteri di accesso condizionale. Questo da applicare, ad esempio, ai criteri di accesso condizionale per Exchange Online o SharePoint Online.

Le applicazioni seguenti supportano l'accesso condizionale per Office 365 e altre applicazioni di servizio Azure Active Directory connessi:

| Servizio di destinazione  | Piattaforma  | Applicazione                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Exchange Online di Office 365 | Windows 10|App di posta elettronica/calendario/contatti, Outlook 2016, Outlook 2013 (con l'autenticazione moderno), Skype for Business (con l'autenticazione moderno)|
|Exchange Online di Office 365| Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (con l'autenticazione moderno), Skype for Business (con l'autenticazione moderno)|
|Exchange Online di Office 365|iOS, Android|  App per dispositivi mobili di Outlook|
|Exchange Online di Office 365|Mac OS X| Outlook 2016 per l'autenticazione a più fattori e il percorso di sola lettura. supporto dei criteri basate sul dispositivo in futuro, Skype per il supporto di Business in futuro|
|Office 365 SharePoint Online|Windows 10| Le applicazioni di Office 2016, Office universale apps, Office 2013 (con l'autenticazione moderno), OneDrive per il supporto Business app (Client di sincronizzazione successiva generazione o NGSC) previste in futuro, il supporto di gruppi di Office in futuro, il supporto per app di SharePoint in futuro|
|Office 365 SharePoint Online|Windows 8.1, Windows 7|Applicazioni di Office 2016, Office 2013 (con l'autenticazione moderno), app OneDrive for Business (client di sincronizzazione di Groove)|
|Office 365 SharePoint Online|iOS, Android|  App di Office mobile |
|Office 365 SharePoint Online|Mac OS X| App di Office 2016 per l'autenticazione a più fattori e il percorso di sola lettura. supporto dei criteri basate sul dispositivo in futuro|
|Office 365 Yammer|Windows 10, iOS e Android | App Yammer di Office|
|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS e Android | App di Dynamics CRM|
|Servizio di ottenere informazioni|Windows 10, Windows 8.1, Windows 7, iOS e Android | Ottenere informazioni app|
|Servizio App Remote Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X |App Remote Azure|
|Qualsiasi servizio app App personali|IOS e Android|Qualsiasi servizio app App personali |


## <a name="applications-that-do-not-use-modern-authentication"></a>Applicazioni che non utilizzano l'autenticazione moderno

Attualmente, è necessario utilizzare altri metodi per bloccare l'accesso alle applicazioni che non utilizza l'autenticazione moderno. Le regole di accesso per le applicazioni che non usa l'autenticazione moderno non vengono applicate da access condizionale. Ciò principalmente è importante per l'accesso di Exchange e SharePoint. La maggior parte delle versioni precedenti delle applicazioni utilizzano precedenti protocolli di controllo di accesso.

### <a name="control-access-in-office-365-sharepoint-online"></a>Controllare l'accesso in Office 365 SharePoint Online
È possibile disabilitare i protocolli legacy per l'accesso a SharePoint utilizzando il cmdlet Set-SPOTenant. Utilizzare questo cmdlet per impedire ai client di Office che utilizzano i protocolli non moderno autenticazione dell'accesso alle risorse di SharePoint Online.

**Comando di esempio**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Controllare l'accesso in Exchange Online di Office 365

Exchange sono disponibili due categorie principali di protocolli. Esaminare le opzioni seguenti e quindi selezionare il criterio più adatto per la propria organizzazione.

-   **Exchange ActiveSync**. Per impostazione predefinita, i criteri di accesso condizionale per l'autenticazione a più fattori e la posizione non vengono applicati per Exchange ActiveSync. È necessario proteggere l'accesso a questi servizi mediante la configurazione dei criteri di Exchange ActiveSync direttamente o bloccando Exchange ActiveSync mediante le regole di Active Directory Federation Services (ADFS).
-   **I protocolli legacy**. È possibile bloccare i protocolli legacy con ADFS. Questo blocca l'accesso al client meno recenti di Office, ad esempio Office 2013 senza attivata l'autenticazione moderno e versioni precedenti di Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>Utilizzare ADFS per bloccare protocollo precedente

È possibile utilizzare le seguenti regole di esempio per impedire l'accesso a livello di ADFS protocollo precedente. Scegliere tra due configurazioni comuni.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Opzione 1: Consentono di Exchange ActiveSync e legacy App, ma solo in una rete intranet

Per applicare le regole seguenti tre per AD FS basarsi protezione di terze parti per piattaforma identità di Microsoft Office 365, il traffico di Exchange ActiveSync e browser e il traffico di autenticazione moderno, hanno accesso. App legacy vengono bloccati dalla rete extranet.

##### <a name="rule-1"></a>Regola 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regola 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regola 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Opzione 2: Consentire a Exchange ActiveSync e bloccare le applicazioni legacy

Per applicare le regole seguenti tre per AD FS basarsi protezione di terze parti per piattaforma identità di Microsoft Office 365, il traffico di Exchange ActiveSync e browser e il traffico di autenticazione moderno, hanno accesso. App legacy vengono bloccati da qualsiasi posizione.

##### <a name="rule-1"></a>Regola 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regola 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regola 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
