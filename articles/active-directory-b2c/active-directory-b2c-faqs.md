<properties
    pageTitle="B2C Azure Active Directory: Domande frequenti | Microsoft Azure"
    description="Domande frequenti su Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: domande frequenti

Questa pagina fornisce le risposte alle domande frequenti su B2C Azure Active Directory (Azure Active Directory). Mantenere il controllo per gli aggiornamenti.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>È possibile usare le funzionalità di Azure Active Directory B2C nel tenant Azure Active Directory esistente, in base ai dipendenti?

Caratteristiche di Azure Active Directory B2C attualmente non possono essere attivati nel tenant di Azure Active Directory esistente. È consigliabile creare un tenant separato per utilizzare le funzionalità di Azure Active Directory B2C per gestire i clienti.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>È possibile usare Azure Active Directory B2C per fornire accesso social (Facebook e Google +) in Office 365?

Azure Active Directory B2C non è possibile usare con Microsoft Office 365. In generale, non può essere utilizzato per l'autenticazione a qualsiasi App SaaS (Office 365, Salesforce, giorno e così via). Offre una gestione identità e accesso solo per consumer esposto applicazioni web e dispositivi mobili e non si applica a scenari dipendente o un partner.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Quali sono gli account locali in Azure Active Directory B2C? Quali sono le differenze da account aziendale o dell'istituto di istruzione in Azure Active Directory?

In un tenant di Azure Active Directory, tutti gli utenti nel tenant (ad eccezione di utenti con account Microsoft esistenti) firma con un indirizzo di posta elettronica del modulo `<xyz>@<tenant domain>`, dove `<tenant domain>` corrisponde a uno dei domini verificati in tenant o iniziale `<...>.onmicrosoft.com` dominio. Questo tipo di account è un account aziendale o dell'istituto di istruzione.

In un tenant di Azure Active Directory B2C, la maggior parte delle App vuole che l'utente di accedere con un indirizzo di posta elettronica non autorizzato (ad esempio joe@comcast.net, bob@gmail.com, sarah@contoso.com, o jim@live.com). Questo tipo di account è un account locale. Oggi, è supportato anche i nomi utente non autorizzato (stringhe semplice) come account locali (ad esempio, Fausto, Davide, Sara o jim). È possibile scegliere uno di questi due tipi di account locale nel servizio di Azure Active Directory B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Il provider di identità di social networking è supportata ora? Quelle che si prevede di supportare in futuro?

Attualmente sono supportate Facebook, Google +, LinkedIn e Amazon. Verrà aggiunto il supporto per gli altri provider di identità di social networking popolari in base alle esigenze dei clienti.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>È possibile configurare gli ambiti per raccogliere ulteriori informazioni sui consumatori da diversi provider di identità di social networking?

No, questa caratteristica è disponibile la Guida di orientamento. Gli ambiti predefinito utilizzati per il set supportato dei provider di identità di social networking sono:

- Facebook: posta elettronica
- Google +: posta elettronica
- Account Microsoft: openid profili di posta elettronica
- Amazon: profilo
- LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Applicazione è necessario essere eseguito su Azure per lavorare con Azure Active Directory B2C?

No, è possibile ospitare l'applicazione in un punto qualsiasi (nel cloud o locale). È necessario interagire con Azure Active Directory B2C è la possibilità di inviare e ricevere richieste su endpoint accessibile al pubblico.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Si dispone di più tenant di Azure Active Directory B2C. Come gestiscono le nel portale di Azure?

Ogni tenant di Azure Active Directory B2C è il proprio blade caratteristiche B2C nel portale di Azure. Vedere [Azure Active Directory B2C: registrare l'applicazione](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) per scoprire come è possibile passare a blade di caratteristiche del tenant specifico B2C nel portale di Azure. Passaggio tra B2C di Azure Active Directory nel portale di Azure non consente di mantenere le caratteristiche di B2C blade aprire nella maggior parte dei browser.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Come personalizzare verifica messaggi di posta elettronica (il contenuto e la "da:" campo) inviate dai Azure Active Directory B2C?

Per personalizzare il contenuto dei messaggi di posta elettronica verifica, è possibile utilizzare la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md) . In particolare, è possono personalizzare questi due elementi di posta elettronica:

- **Logo di intestazione**: visualizzata nell'angolo inferiore destro.
- **Colore di sfondo**: visualizzata nella parte superiore.

    ![Schermata di un messaggio di posta elettronica verifica personalizzata](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma di posta elettronica contiene il nome del tenant B2C fornita al momento della creazione prima di tutto il tenant B2C. È possibile modificare il nome queste istruzioni:

- Accedere al [portale classica Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione.
- Passare al tenant di B2C.
- Fare clic sulla scheda **Configura** .
- Modificare il campo **nome** della sezione **proprietà della Directory** .
- Fare clic su **Salva** nella parte inferiore della pagina.

Attualmente non è possibile modificare la "da:" campo nel messaggio di posta elettronica. Se si è interessati in questa funzionalità e personalizzare completamente il corpo del messaggio di posta elettronica verifica, votare la caratteristica [Uservoicehttp](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Come è possibile migrare il nome utente, password e profili del database di Azure Active Directory B2C?

È possibile utilizzare l'API di Azure Active Directory grafico per scrivere lo strumento di migrazione. Vedere l' [esempio di grafico API](active-directory-b2c-devquickstarts-graph-dotnet.md) per informazioni dettagliate. Vengono fornite diverse opzioni di migrazione e strumenti-the-pronte in futuro.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quali criteri password viene utilizzato per gli account locali in Azure Active Directory B2C?

Il criterio di password Azure Active Directory B2C per gli account locali è in base ai criteri di Azure Active Directory. Azure AD B2C dell'abbonamento, per l'abbonamento o accesso e la password Reimposta criteri ottimali la forza della funzione "sicuro" password e non scadono le password. Leggere il [criterio di password di Azure Active Directory](https://msdn.microsoft.com/library/azure/jj943764.aspx) per altri dettagli.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>È possibile usare Azure AD Connect per eseguire la migrazione delle identità consumer che sono archiviate nel mio locale di Active Directory per Azure Active Directory B2C?

No, Azure AD Connect non è progettato per funzionare con Azure Active Directory B2C. Vengono fornite diverse opzioni di migrazione e strumenti-the-pronte in futuro.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure Active Directory B2C funziona con i sistemi, ad esempio Microsoft Dynamics CRM?

Non è attualmente. Integrazione di questi sistemi sia la Guida di orientamento.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Indica Azure Active Directory B2C con SharePoint locale 2016 o versioni precedenti?

Non è attualmente. Azure Active Directory B2C non è disponibile il supporto per i token SAML 1.1 che portali e applicazioni e-commerce basate sulla necessità di SharePoint locale. Si noti che Azure Active Directory B2C non destinato a SharePoint esterni partner scenario di condivisione della; In alternativa, vedere [B2B di Azure Active Directory](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>È consigliabile usare Azure Active Directory B2C o B2B per gestire le identità esterne?

Leggere questo articolo sulle [identità esterne](../active-directory/active-directory-b2b-compare-external-identities.md) per altre informazioni sull'applicazione le caratteristiche appropriate per gli scenari di identità esterni.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quali report e funzionalità di controllo fornisce da Azure Active Directory B2C? Sono gli stessi visualizzati nei Azure Active Directory Premium?

No, Azure Active Directory B2C supporta lo stesso set di report come Azure Active Directory Premium. Rilascio di Azure Active Directory B2C controllo API breve e dei report di base.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>È possibile localizzare l'interfaccia utente di pagine rappresentate in Azure Active Directory B2C? Quali lingue sono supportate?

Al momento Azure Active Directory B2C ottimizzata per l'inglese solo. Si prevede di distribuire le funzionalità di localizzazione presto.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>È possibile usare il proprio URL le pagine di abbonamento e accesso rappresentate in Azure Active Directory B2C? Ad esempio, è possibile modificare l'URL da login.microsoftonline.com a login.contoso.com?

Non è attualmente. Questa funzione è attivata la Guida di orientamento. Si noti inoltre che verifica del dominio nella scheda **domini** del tenant nel portale di classica Azure non verrà eseguita.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Come è possibile eliminare il tenant di Azure Active Directory B2C?

Seguire questa procedura per eliminare il tenant di Azure Active Directory B2C:

- Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
- Passare a pale **tutti i criteri** , **i provider di identità** e le **applicazioni**ed eliminare tutte le voci in ognuna di esse.
- Accedere al [portale classica Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione. (Questo è lo stesso lavoro o dell'istituto di istruzione account o lo stesso account Microsoft usato per effettuare l'iscrizione per Azure).
- Passare all'articolo estensione Active Directory a sinistra e scegliere il tenant B2C.
- Fare clic sulla scheda **utenti** .
- Selezionare ogni utente attiva (l'utente attualmente connessi come, ad esempio, l'amministratore della sottoscrizione esclusioni). Fare clic su **Elimina** nella parte inferiore della pagina e fare clic su **Sì** quando richiesto.
- Fare clic sulla scheda **applicazioni** .
- Selezionare **applicazioni appartiene alla società** nel campo **Mostra** e fare clic sul segno di spunta.
- Verrà visualizzata un'applicazione denominata **b2c-estensioni-app** elencate di seguito. Fare clic su **Elimina** nella parte inferiore della pagina e fare clic su **Sì** quando richiesto.
- Passare di nuovo l'estensione di Active Directory e selezionare il tenant B2C.
- Fare clic su **Elimina** nella parte inferiore della pagina. Seguire le istruzioni visualizzate per completare il processo.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>È possibile ottenere Azure Active Directory B2C come parte della famiglia di prodotti mobilità Enterprise?

No, Azure Active Directory B2C è un servizio di Azure uso prepagato e non fa parte della famiglia di prodotti mobilità dell'organizzazione.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Come segnalano problemi di Azure Active Directory B2C?

Visualizzare [File supportano le richieste di Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="more-information"></a>Ulteriori informazioni

È anche consigliabile rivedere [i vincoli, restrizioni e limitazioni del servizio](active-directory-b2c-limitations.md)corrente.
