<properties
    pageTitle="Come installare automaticamente il connettore Proxy di Azure Active Directory applicazione | Microsoft Azure"
    description="Viene descritto come eseguire un'installazione automatica di Azure Active Directory applicazione Proxy Connector per fornire l'accesso remoto sicuro alle App locale."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Come installare automaticamente il connettore Proxy di Azure Active Directory dell'applicazione

Si desidera inviare uno script di installazione in più server di Windows o in Windows Server che non dispongono di interfaccia utente abilitato. In questo argomento viene illustrato come creare uno script di Windows PowerShell che consente l'installazione automatica installare e registrare il connettore Proxy di Azure Active Directory dell'applicazione.

## <a name="enabling-access"></a>Abilitazione dell'accesso
Proxy applicazione funziona mediante l'installazione di un servizio di Windows Server sottile denominato il connettore all'interno della rete. Per il connettore Proxy di applicazione di lavoro che deve essere registrata con la directory di Azure Active Directory mediante un amministratore globale e una password. In genere questo viene immessa durante l'installazione di connettore nella finestra di dialogo. In alternativa, è possibile utilizzare Windows PowerShell per creare un oggetto di credenziali per immettere le informazioni di registrazione oppure è possibile creare il proprio token e usarlo per immettere le informazioni di registrazione.

## <a name="step-1--install-the-connector-without-registration"></a>Passaggio 1: Installare il connettore senza registrazione


Installare il file MSI connettore senza registrare il connettore come indicato di seguito:


1. Aprire un prompt dei comandi.
2. Eseguire il seguente comando in cui l'opzione /q significa installazione automatica - l'installazione non chiederà di accettare il contratto di licenza utente finale.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Passaggio 2: Registrare il connettore con Azure Active Directory
Questo risultato può essere ottenuto utilizzando uno dei metodi seguenti:


- Registrare il connettore usando un oggetto di credenziali Windows PowerShell
- Registrare il connettore utilizzando un token creato in modalità offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrare il connettore usando un oggetto di credenziali Windows PowerShell


1. Creare l'oggetto di Windows PowerShell credenziali eseguendo le operazioni seguenti, in cui "<username>"e"<password>" deve essere sostituito con il nome utente e la password per la directory:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Passare a **C:\Programmi\Microsoft c:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire script usando l'oggetto credenziali PowerShell che è stato creato, in cui $cred è il nome di PowerShell credenziali oggetto creato:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>Registrare il connettore utilizzando un token creato in modalità offline

1. Creare un token offline utilizzando la classe AuthenticationContext con i valori indicati nel frammento di codice:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Dopo avere creato il token di creare una SecureString utilizzando il token di: <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Eseguire il seguente comando di Windows PowerShell, dove SecureToken è il nome del token creato in precedenza e tenantID è GUID del tenant: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>Vedere anche

- [Abilitare Proxy di applicazione per Azure Active Directory](active-directory-application-proxy-enable.md)
- [Pubblicare le applicazioni che utilizzano il nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)
- [Risoluzione dei problemi che si verificano con Proxy dell'applicazione](active-directory-application-proxy-troubleshoot.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
