<properties
    pageTitle="Azure Active B2C di Directory: Personalizzazione dell'interfaccia utente | Microsoft Azure"
    description="Un argomento le caratteristiche di personalizzazione dell'interfaccia utente di Azure Active Directory B2C"
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
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure B2C Directory attiva: Personalizzare l'interfaccia utente di Azure Active Directory B2C (UI)

Esperienza utente è fondamentale in un'applicazione consumer esposto. È la differenza tra un'applicazione efficace e un ottimo e tra consumatori semplicemente attivi e quelli realmente impegnati. Azure Active Directory (Azure Active Directory) B2C consente di personalizzare consumer per l'abbonamento, accesso aggiuntivo (*Vedi nota riportata di seguito*), la modifica dei profili e pagine con controllo pixel perfetto la reimpostazione della password.

> [AZURE.NOTE]
Attualmente account locale accesso pagine, la relativa password accompaning Reimposta le pagine e messaggi di posta elettronica verifica può essere personalizzati solo se si utilizza la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md) e non per i meccanismi descritti in questo articolo.

In questo articolo leggerà su:

- La funzionalità di personalizzazione dell'interfaccia utente di pagina.
- Uno strumento che consente di verificare la funzionalità di personalizzazione dell'interfaccia utente di pagina con il contenuto di esempio.
- Elementi dell'interfaccia utente di base in ogni tipo di pagina.
- Procedure consigliate per esercitare questa caratteristica.

## <a name="the-page-ui-customization-feature"></a>La funzionalità di personalizzazione dell'interfaccia utente di pagina

Con la funzionalità di personalizzazione della pagina dell'interfaccia utente, è possibile personalizzare l'aspetto del consumatore per l'abbonamento, accesso, la reimpostazione della password e la modifica profilo pagine (in base alla configurazione dei [criteri](active-directory-b2c-reference-policies.md)). I consumatori avranno esperienze continua quando lo spostamento tra le pagine applicazione rappresentate in Azure Active Directory B2C.

Diversamente da altri servizi in opzioni dell'interfaccia utente sono limitate o disponibili solo tramite API Azure Active Directory B2C utilizza un approccio moderno (e più semplice) alla personalizzazione dell'interfaccia utente di pagina.

Ecco come funziona: Azure Active Directory B2C viene eseguito codice nel browser del consumatore e utilizza un approccio moderno denominato [Condivisione delle risorse tra origine (CORS)](http://www.w3.org/TR/cors/) per caricare il contenuto da un URL specificato in un criterio. È possibile specificare URL diversi per pagine diverse. Il codice consente di unire elementi di interfaccia utente di Azure Active Directory B2C con il contenuto caricato dall'URL e viene visualizzata la pagina per il consumer. È sufficiente è:

1. Creare il contenuto con HTML5 corretto una `<div id="api"></div>` individuare un elemento (è necessario essere un elemento vuoto) il `<body>`. Segni di questo elemento in cui viene inserito il contenuto di Azure Active Directory B2C.
2. Ospitare il contenuto di un endpoint HTTPS (con CORS consentito).
3. Applicare uno stile gli elementi dell'interfaccia utente che Azure Active Directory B2C inserisce in.

## <a name="test-out-the-ui-customization-feature"></a>Testare la funzionalità di personalizzazione dell'interfaccia utente

Se si vuole provare la funzionalità di personalizzazione dell'interfaccia utente utilizzando il nostro codice HTML e il contenuto CSS, troverete è [uno strumento di supporto semplici](active-directory-b2c-reference-ui-customization-helper-tool.md) che carica e configura l'archiviazione Blob Azure contenuto di esempio.

> [AZURE.NOTE]
È possibile ospitare il contenuto dell'interfaccia utente in un punto qualsiasi: server web, reti CDN, AWS S3, sistemi di condivisione file e così via. Come il contenuto è ospitato su un endpoint HTTPS disponibile al pubblico (con CORS consentito), si è possibile continuare. Si utilizza archiviazione Blob Azure solo a titolo esemplificativo.

### <a name="the-most-basic-html-content-for-testing"></a>Il contenuto HTML di base per il testing

Di seguito viene mostrato il codice HTML più semplice contenuto che è possibile utilizzare per testare questa funzionalità. Utilizzare lo stesso strumento di supporto fornito in precedenza per caricare e configurare il contenuto nella spazio di archiviazione Blob Azure. È quindi possibile verificare che i pulsanti non stilizzati di base e campi del modulo in ogni pagina siano visualizzati e funzionante.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Elementi dell'interfaccia utente di base in ogni tipo di pagina

Nelle sezioni seguenti sono disponibili esempi di frammenti di HTML5 Azure Active Directory B2C unisce il `<div id="api"></div>` elemento si trova nel contenuto. **Non è possibile inserire questi frammenti nel contenuto HTML 5.** Il servizio di Azure Active Directory B2C inseriti in fase di esecuzione. Usare questi esempi per progettare fogli di stile.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Azure Active Directory B2C contenuto inserito "identità provider pagina di selezione"

Questa pagina contiene un elenco di provider di identità che l'utente è possibile scegliere tra durante l'iscrizione o l'accesso. Si tratta di provider di identità di social networking, ad esempio Facebook e Google + o account locali (basato su nome utente o indirizzo di posta elettronica).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Azure Active Directory B2C contenuto inserito "locale pagina dell'account per l'abbonamento"

Questa pagina contiene un modulo per l'abbonamento è necessario compilare durante l'iscrizione per un account locale basato su un indirizzo di posta elettronica o un nome utente dell'utente. Il modulo può contenere controlli inpui diversi, ad esempio input casella di testo, casella password, pulsante di opzione, caselle di riepilogo a selezione singola e selezionare più caselle di controllo.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure Active Directory B2C contenuto inserito "" Social pagina dell'account per l'abbonamento"

Questa pagina contiene un modulo di iscrizione che l'utente deve compilare durante l'iscrizione con un account esistente da un provider di identità di social networking, ad esempio Facebook o Google +. Questa pagina è simile a quello della account locale per l'abbonamento (illustrata nella sezione precedente) fatta eccezione per i campi di immissione della password.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Azure Active Directory B2C contenuto inserito nella "unificato per l'abbonamento o accesso pagina"

Questa pagina gestisce sia per l'abbonamento e accessohttp dei consumatori, in grado di utilizzare il provider di identità di social networking, ad esempio Facebook o Google + o account locali.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Azure Active Directory B2C contenuto inserito nella pagina autenticazione a più fattori""

In questa pagina, gli utenti possono verificare i numeri di telefono (del computer con testo o alla segreteria) durante l'iscrizione o l'accesso.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Azure Active Directory B2C contenuto inserito "" pagina di errore"

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Aspetti da ricordare durante la creazione di contenuti

Se si prevede di utilizzare la funzionalità di personalizzazione dell'interfaccia utente, esaminare le procedure consigliate seguenti:

- Non copiare il contenuto predefinito del B2C Active Directory Azure e tentare di modificarlo. È consigliabile per creare il contenuto di HTML5 da zero e utilizzare contenuto predefinito come riferimento.
- In tutte le pagine (ad eccezione di pagine di errore) per l'accesso, per l'abbonamento e criteri di modifica profilo personale, saranno necessario ignorare i fogli di stile predefinito aggiunti in tali pagine in fogli di stile che si forniscono la <head> frammenti. In tutte le pagine per l'abbonamento o accesso e criteri di reimpostazione Password e le pagine di errore in tutti i criteri, sarà necessario fornire tutti gli stili se stessi.
- Per motivi di sicurezza, non consentono di includere qualsiasi JavaScript nel contenuto. La maggior parte degli elementi devono trovarsi all'esterno della casella. In caso contrario, utilizzare il [VoIP utente](http://feedback.azure.com/forums/169401-azure-active-directory) per richiedere nuove funzionalità.
- Le versioni di browser supportate:
    - Internet Explorer 11
    - Internet Explorer 10
    - Internet Explorer 9 (limitata)
    - Internet Explorer 8 (limitata)
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0
