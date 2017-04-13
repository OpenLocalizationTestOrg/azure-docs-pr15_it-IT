<properties
    pageTitle="Azure B2C Directory attiva: Framework criteri Extensible | Microsoft Azure"
    description="Un argomento in framework extensible dei criteri di Azure Active Directory B2C e su come creare diversi tipi di criteri"
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
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure B2C Directory attiva: Framework criteri Extensible

## <a name="the-basics"></a>Nozioni fondamentali

Framework extensible dei criteri di Azure Active Directory (Azure Active Directory) B2C è l'intensità del centro del servizio. Criteri descrivono consumer esperienze identità, ad esempio per l'abbonamento, accesso o profilo modifica. Ad esempio, un criterio per l'abbonamento consente di controllare il comportamento del configurando le impostazioni seguenti:

- Tipi di account (account di social networking, ad esempio Facebook o account locale, ad esempio indirizzo di posta elettronica) che possono essere utilizzati per iscriversi per l'applicazione consumer.
- Attributi (ad esempio nome, CAP e dimensioni attacco) devono essere raccolti dal consumer durante l'iscrizione.
- Utilizzo di autenticazione a più fattori.
- Il e aspetto di tutte le pagine per l'abbonamento.
- Informazioni (manifesti come attestazioni un token) che viene visualizzato quando il criterio al termine dell'esecuzione.

È possibile creare più criteri di tipo diverso nel tenant e utilizzare nelle applicazioni in base alle esigenze. Criteri possono essere riutilizzati tra le applicazioni. In questo modo agli sviluppatori di definire e modificare consumer identità esperienze con minime o nessuna modifica al codice.

Criteri sono disponibili per l'utilizzo tramite un'interfaccia semplice per sviluppatori. L'applicazione attiva un criterio utilizzando una richiesta di autenticazione HTTP standard (passando un parametro di criteri nella richiesta di) e riceve un token personalizzato come risposta. Ad esempio, l'unica differenza tra le richieste di richiamare un criterio di abbonamento e quelli richiamare un criterio di accesso è il nome del criterio utilizzato nel parametro stringa query "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Per ulteriori informazioni su framework dei criteri, vedere questo [post di blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Creazione di un criterio per l'abbonamento

Per abilitare l'iscrizione sull'applicazione, sarà necessario creare un criterio di iscrizione. Questo criterio descrive le esperienze consumatori verranno elaborata durante l'iscrizione e il contenuto di token che l'applicazione riceverà per iscrizioni esito negativo.

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri per l'abbonamento**.
3. Fare clic su **Add +** nella parte superiore e il.
4. Il **nome** determina il nome dei criteri per l'abbonamento utilizzato dall'applicazione. Ad esempio, immettere "SiUp".
5. Fare clic su **provider di identità** e selezionare "L'iscrizione di posta elettronica". Facoltativamente, è possibile selezionare il provider di identità di social networking, anche se è già configurato. Fare clic su **OK**.
6. Fare clic su **attributi per l'abbonamento**. Scegliere qui gli attributi che si desidera raccogliere dal consumer durante l'iscrizione. Selezionare ad esempio, "Paese/area geografica", "Nome" e "Il codice postale". Fare clic su **OK**.
7. Fare clic **sulle attestazioni applicazione**. Scegliere qui sulle attestazioni che si desidera vengano restituiti in token inviati all'applicazione dopo un'esperienza di iscrizione completata. Selezionare ad esempio, "Nome", "Provider di identità", "Codice postale", "Utente è le novità" e "ID dell'utente".
8. Fare clic su **Crea**. Si noti che il criterio appena creato viene visualizzato come "**B2C_1_SiUp**" (la **B2C\_1\_ ** frammento verrà automaticamente aggiunti) in e il **criteri per l'abbonamento** .
9. Aprire il criterio, fare clic su "**B2C_1_SiUp**".
10. Selezionare "Contoso B2C app" nell'elenco a discesa **applicazioni** e `https://localhost:44321/` nel **URL di risposta / reindirizzare URI** elenco a discesa.
11. Fare clic su **Esegui**. Verrà aperta una nuova scheda del browser e sarà possibile eseguire mediante l'esperienza utente di iscrizione per l'applicazione.

    > [AZURE.NOTE]
    Bastano in un minuto per la creazione dei criteri e gli aggiornamenti per rendere effettive.

## <a name="create-a-sign-in-policy"></a>Creazione di un criterio di accesso

Per abilitare l'accesso in ingresso sull'applicazione, sarà necessario creare un criterio di accesso. Questo criterio descrive le esperienze consumatori verranno elaborata durante l'accesso e immettere il contenuto di token che verrà visualizzato l'applicazione su componenti aggiuntivi di accesso ha esito positivo.

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri di accesso**.
3. Fare clic su **Add +** nella parte superiore e il.
4. Il **nome** determina il nome del criterio di accesso utilizzato dall'applicazione. Ad esempio, immettere "SiIn".
5. Fare clic su **provider di identità** e selezionare "Locale Account di accesso". Facoltativamente, è possibile selezionare il provider di identità di social networking, anche se è già configurato. Fare clic su **OK**.
6. Fare clic **sulle attestazioni applicazione**. Scegliere qui sulle attestazioni che si desidera vengano restituiti in token inviati all'applicazione dopo un'esperienza di accesso ha esito positivo. Selezionare ad esempio, "Nome", "Provider di identità", "Il codice postale" e "ID dell'utente". Fare clic su **OK**.
7. Fare clic su **Crea**. Si noti che il criterio appena creato viene visualizzato come "**B2C_1_SiIn**" (la **B2C\_1\_ ** frammento verrà automaticamente aggiunti) in e il **criteri di accesso** .
8. Aprire il criterio, fare clic su "**B2C_1_SiIn**".
9. Selezionare "Contoso B2C app" nell'elenco a discesa **applicazioni** e `https://localhost:44321/` nel **URL di risposta / reindirizzare URI** elenco a discesa.
10. Fare clic su **Esegui**. Verrà aperta una nuova scheda del browser e sarà possibile eseguire mediante esperienza utente di accesso all'interno dell'applicazione.

    > [AZURE.NOTE]
    Bastano in un minuto per la creazione dei criteri e gli aggiornamenti per rendere effettive.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creazione di un criterio per l'abbonamento o accesso

Questo criterio consente di gestire entrambe le esperienze di abbonamento e accesso consumer con una singola configurazione. Gli utenti sono dirette verso il basso il percorso verso destra (per l'abbonamento o accesso) in base al contesto. Viene inoltre il contenuto di token che l'applicazione riceverà al momento della iscrizioni ha esito positivo o componenti aggiuntivi di accesso.  Un esempio di codice per i criteri per l'abbonamento o accesso è [disponibile qui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri per l'abbonamento o di accesso**.
3. Fare clic su **Add +** nella parte superiore e il.
4. Il **nome** determina il nome dei criteri per l'abbonamento utilizzato dall'applicazione. Ad esempio, immettere "SiUpIn".
5. Fare clic su **provider di identità** e selezionare "L'iscrizione di posta elettronica". Facoltativamente, è possibile selezionare il provider di identità di social networking, anche se è già configurato. Fare clic su **OK**.
6. Fare clic su **attributi per l'abbonamento**. Scegliere qui gli attributi che si desidera raccogliere dal consumer durante l'iscrizione. Selezionare ad esempio, "Paese/area geografica", "Nome" e "Il codice postale". Fare clic su **OK**.
7. Fare clic **sulle attestazioni applicazione**. Scegliere qui sulle attestazioni che si desidera vengano restituiti in token inviati all'applicazione dopo un'esperienza esito negativo per l'abbonamento o accesso. Selezionare ad esempio, "Nome", "Provider di identità", "Codice postale", "Utente è le novità" e "ID dell'utente".
8. Fare clic su **Crea**. Si noti che il criterio appena creato viene visualizzato come "**B2C_1_SiUpIn**" (la **B2C\_1\_ ** frammento verrà automaticamente aggiunti) in e il **criteri per l'abbonamento o di accesso** .
9. Aprire il criterio, fare clic su "**B2C_1_SiUpIn**".
10. Selezionare "Contoso B2C app" nell'elenco a discesa **applicazioni** e `https://localhost:44321/` nel **URL di risposta / reindirizzare URI** elenco a discesa.
11. Fare clic su **Esegui**. Verrà aperta una nuova scheda del browser e sarà possibile eseguire mediante l'esperienza utente per l'abbonamento o accesso in base alla configurazione.

    > [AZURE.NOTE]
    Bastano in un minuto per la creazione dei criteri e gli aggiornamenti per rendere effettive.

## <a name="create-a-profile-editing-policy"></a>Creare un profilo Modifica criteri

Per abilitare la modifica nell'applicazione del profilo, sarà necessario creare un profilo Modifica criteri. Questo criterio descrive le esperienze consumatori verranno elaborata durante la modifica del profilo e il contenuto di token che l'applicazione riceverà il corretto completamento.

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **profilo Modifica criteri**.
3. Fare clic su **Add +** nella parte superiore e il.
4. Il **nome** determina il nome del criterio utilizzato dall'applicazione di modifica del profilo. Ad esempio, immettere "SiPe".
5. Fare clic su **provider di identità** e selezionare "Indirizzo di posta elettronica". Facoltativamente, è possibile selezionare il provider di identità di social networking, anche se è già configurato. Fare clic su **OK**.
6. Fare clic su **attributi del profilo**. Scegliere qui gli attributi che può visualizzare e modificare il consumer. Selezionare ad esempio, "Paese/area geografica", "Nome" e "Il codice postale". Fare clic su **OK**.
7. Fare clic **sulle attestazioni applicazione**. Scegliere qui sulle attestazioni che si desidera vengano restituiti in token inviati all'applicazione dopo un modifica esperienza del profilo corretto. Selezionare ad esempio, "Nome" e "Il codice postale".
8. Fare clic su **Crea**. Si noti che il criterio appena creato viene visualizzato come "**B2C_1_SiPe**" (la **B2C\_1\_ ** frammento verrà automaticamente aggiunti) in e il **profilo Modifica criteri** .
9. Aprire il criterio, fare clic su "**B2C_1_SiPe**".
10. Selezionare "Contoso B2C app" nell'elenco a discesa **applicazioni** e `https://localhost:44321/` nel **URL di risposta / reindirizzare URI** elenco a discesa.
11. Fare clic su **Esegui**. Verrà aperta una nuova scheda del browser e sarà possibile eseguire mediante l'esperienza utente dell'applicazione di modifica del profilo.

    > [AZURE.NOTE]
    Bastano in un minuto per la creazione dei criteri e gli aggiornamenti per rendere effettive.
    
## <a name="create-a-password-reset-policy"></a>Creare un criterio di reimpostazione password

Per abilitare la reimpostazione dell'applicazione della password diffusamente, sarà necessario creare un criterio di reimpostazione password. Nota che la password a livello di tenant Reimposta l'opzione specificato [qui](active-directory-b2c-reference-sspr.md) è ancora disponibile per i criteri di accesso. Questo criterio descrive le esperienze che gli utenti verranno attraversano durante la reimpostazione della password e immettere il contenuto di token che l'applicazione riceverà il corretto completamento.

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri la reimpostazione della Password**.
3. Fare clic su **Add +** nella parte superiore e il.
4. Il **nome** determina che la reimpostazione della password nome del criterio utilizzato dall'applicazione. Ad esempio, immettere "SSPR".
5. Fare clic su **provider di identità** e selezionare "Reimpostare la password tramite l'indirizzo di posta elettronica". Fare clic su **OK**.
6. Fare clic **sulle attestazioni applicazione**. Scegliere qui sulle attestazioni che si desidera vengano restituiti nei token inviato all'applicazione dopo la reimpostazione della password corretta esperienza. Selezionare ad esempio, "ID oggetto dell'utente".
7. Fare clic su **Crea**. Si noti che il criterio appena creato viene visualizzato come "**B2C_1_SSPR**" (la **B2C\_1\_ ** frammento verrà automaticamente aggiunti) in e il **criteri la reimpostazione della Password** .
8. Aprire il criterio, fare clic su "**B2C_1_SSPR**".
9. Selezionare "Contoso B2C app" nell'elenco a discesa **applicazioni** e `https://localhost:44321/` nel **URL di risposta / reindirizzare URI** elenco a discesa.
10. Fare clic su **Esegui**. Verrà aperta una nuova scheda del browser e sarà possibile eseguire mediante l'esperienza utente di reimpostazione password nell'applicazione.

    > [AZURE.NOTE]
    Bastano in un minuto per la creazione dei criteri e gli aggiornamenti per rendere effettive.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Token, sessione e configurazione di single sign-on](active-directory-b2c-token-session-sso.md).
