<properties
    pageTitle="Accesso attivazione chiave Azure AD | Microsoft Azure"
    description="In questo articolo vengono illustrate le procedure consigliate attivazione chiave firma per Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Firma chiave attivazione di Azure Active Directory

In questo argomento viene illustrato cosa occorre sulle chiavi pubbliche utilizzati in Azure Active Directory (Azure Active Directory) per firmare i token di sicurezza. È importante tenere presente che questi attivazione chiavi periodicamente e, in caso di emergenza Infrastructure immediatamente. Tutte le applicazioni che utilizzano Azure Active Directory dovrebbero essere possibile gestire il processo di attivazione chiave o stabilire un processo di attivazione manuale periodici a livello di programmazione. Continuare la lettura per capire come utilizzare i tasti, come valutare l'impatto dell'attivazione all'applicazione e aggiornare l'applicazione o stabilire un processo di attivazione manuale periodici per gestire attivazione chiave se necessario.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Panoramica delle chiavi per la firma in Azure Active Directory

Azure Active Directory utilizza la crittografia chiave pubblica basata su standard per stabilire l'attendibilità tra stesso e le applicazioni di usarlo. In pratica, funziona nel modo seguente: Azure Active Directory utilizza una chiave di firma è costituito da una coppia di chiavi pubblica e privatehttp. Quando un utente accede a un'applicazione che utilizza Azure Active Directory per l'autenticazione, Azure Active Directory crea un token di sicurezza che contiene le informazioni relative all'utente. Questo token è firmato da Azure Active Directory utilizzando la chiave privata prima che venga inviato all'applicazione. Per verificare che il token è valido ed effettivamente originati da Azure Active Directory, l'applicazione deve convalida firma del token utilizzando la chiave pubblica esposta dall'Azure Active Directory contenuto nel [documento di individuazione OpenID connettersi](http://openid.net/specs/openid-connect-discovery-1_0.html) o SAML/WS-Fed [documento di metadati federazione](active-directory-federation-metadata.md)del tenant.

Per motivi di sicurezza, Azure AD accedere chiave rolls periodicamente e, in caso di emergenza potrebbero essere riportati immediatamente. Tutte le applicazioni che si integrano con Azure Active Directory devono essere preparata per gestire un evento di attivazione chiave indipendentemente dalla frequenza può verificarsi. Se non si tenta di utilizzare un tasto scaduto per verificare la firma un token, la richiesta di accesso non viene eseguito.

È sempre più di una chiave valida disponibile in un documento di individuazione OpenID connettersi e il documento di metadati federazione. L'applicazione devono essere pronti a utilizzare i tasti specificati nel documento, poiché un tasto può distribuire breve, un altro potrebbe essere la sostituzione e così via.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Come verificare se l'applicazione avrà effetto e spiega come affrontarlo

Modalità di gestione delle chiavi attivazione l'applicazione dipende da variabili, ad esempio il tipo di applicazione o è stato utilizzato il protocollo di identità e raccolta. Nelle sezioni seguenti valutare se i tipi di più comuni di applicazioni sono interessati dal attivazione chiave e forniscono indicazioni su come aggiornare un'applicazione di supportare attivazione automatica o aggiornare manualmente la chiave.

* [Applicazioni client nativi accesso alle risorse](#nativeclient)
* [Le applicazioni Web / API accesso alle risorse](#webclient)
* [Le applicazioni Web / API la protezione delle risorse e costruite servizi App di Windows Azure](#appservices)
* [Le applicazioni Web / API proteggere le risorse con .NET OWIN OpenID connessione, WS Fed o middleware WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Le applicazioni Web / API proteggere le risorse usando la connessione di .NET Core OpenID o JwtBearerAuthentication middleware](#owincore)
* [Le applicazioni Web / API proteggere le risorse utilizzando il modulo di Active Directory di azure passport Node](#passport)
* [Le applicazioni Web / API la protezione delle risorse e creato con Visual Studio 2015](#vs2015)
* [Applicazioni Web sulla protezione delle risorse e creato con Visual Studio 2013](#vs2013)
* [API Web sulla protezione delle risorse e creato con Visual Studio 2013](#vs2013_webapi)
* [Applicazioni Web sulla protezione delle risorse e creato con Visual Studio 2012](#vs2012)
* [Applicazioni Web sulla protezione delle risorse e creato con Visual Studio 2010, 2008 o tramite Windows Identity Foundation](#vs2010)
* [Le applicazioni Web / API proteggere le risorse usando altre raccolte o l'implementazione manualmente i protocolli supportati](#other)

Questa Guida **non** applicabile per:

* Le applicazioni aggiunte da Azure Active Directory applicazione raccolta (inclusi personalizzato) hanno indicazioni separata in relazione alle chiavi per la firma. [Ulteriori informazioni.](active-directory-sso-certs.md)
* Locale applicazioni pubblicate tramite proxy dell'applicazione non sono necessario preoccuparsi di chiavi per la firma.

### <a name="nativeclient"></a>Applicazioni client nativi accesso alle risorse

Applicazioni che accedono solo alle risorse (ad esempio Microsoft Graph, KeyVault, API di Outlook e altre APIs Microsoft) in genere solo ottenere un token e passarlo al proprietario della risorsa. Dato che non proteggere le risorse, non controllano il token e pertanto non è necessario assicurarsi che sia firmato correttamente.

Applicazioni client nativo, se computer desktop o portatile, rientrano in questa categoria e pertanto non influisce l'attivazione.

### <a name="webclient"></a>Le applicazioni Web / API accesso alle risorse

Applicazioni che accedono solo alle risorse (ad esempio Microsoft Graph, KeyVault, API di Outlook e altre APIs Microsoft) in genere solo ottenere un token e passarlo al proprietario della risorsa. Dato che non proteggere le risorse, non controllano il token e pertanto non è necessario assicurarsi che sia firmato correttamente.

API che utilizzano il flusso solo app e applicazioni Web (credenziali client / certificato client), rientrano in questa categoria e pertanto non influisce l'attivazione.

### <a name="appservices"></a>Le applicazioni Web / API la protezione delle risorse e costruite servizi App di Windows Azure

Autenticazione di Azure i servizi App / funzionalità per le autorizzazioni (EasyAuth) è già presente la logica necessaria per la gestione key attivazione automaticamente.

### <a name="owin"></a>Le applicazioni Web / API proteggere le risorse con .NET OWIN OpenID connessione, WS Fed o middleware WindowsAzureActiveDirectoryBearerAuthentication

Se l'applicazione utilizza il .NET OWIN OpenID connettere, WS Fed o middleware WindowsAzureActiveDirectoryBearerAuthentication, sono già la logica necessaria per la gestione key attivazione automaticamente.

È possibile verificare che l'applicazione utilizza uno di questi riconoscibili per uno dei seguenti frammenti di Startup.cs o Startup.Auth.cs dell'applicazione

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Le applicazioni Web / API proteggere le risorse usando la connessione di .NET Core OpenID o JwtBearerAuthentication middleware

Se l'applicazione utilizza middleware .NET Core OWIN OpenID connettersi o JwtBearerAuthentication, sono già la logica necessaria per la gestione key attivazione automaticamente.

È possibile verificare che l'applicazione utilizza uno di questi riconoscibili per uno dei seguenti frammenti di Startup.cs o Startup.Auth.cs dell'applicazione

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Le applicazioni Web / API proteggere le risorse utilizzando il modulo di Active Directory di azure passport Node

Se l'applicazione utilizza il modulo di Active Directory passport Node, sono già la logica necessaria per la gestione key attivazione automaticamente.

È possibile verificare che l'applicazione Active Directory a passport cercandone il frammento di codice seguente in app.js dell'applicazione

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Le applicazioni Web / API la protezione delle risorse e creato con Visual Studio 2015

Se l'applicazione è stato creato con un modello di applicazione web in Visual Studio 2015 e si seleziona **Account dell'istituto di istruzione e di lavoro** dal menu **Modifica autenticazione** , sono già la logica necessaria per la gestione key attivazione automaticamente. Questa logica incorporata in middleware OWIN OpenID connettersi recupera e memorizza le chiavi dal documento di individuazione OpenID connettersi e periodicamente dati vengono aggiornati.

Se l'autenticazione è stato aggiunto alla soluzione di manualmente, l'applicazione non dispone la logica di attivazione chiave necessarie. Sarà necessario scrivere o seguire i passaggi descritti in [applicazioni Web / API utilizza altre librerie o manualmente l'implementazione i protocolli supportati.](#other).

### <a name="vs2013"></a>Applicazioni Web sulla protezione delle risorse e creato con Visual Studio 2013

Se l'applicazione è stato creato con un modello di applicazione web in Visual Studio 2013 e si seleziona **Account dell'organizzazione** dal menu **Modifica autenticazione** , sono già la logica necessaria per la gestione key attivazione automaticamente. Questa logica memorizza identificatore univoco dell'organizzazione e le informazioni chiave firma in due tabelle di database associate al progetto. È possibile trovare la stringa di connessione per il database in config del progetto.

Se l'autenticazione è stato aggiunto alla soluzione di manualmente, l'applicazione non dispone la logica di attivazione chiave necessarie. Sarà necessario scrivere o seguire i passaggi descritti in [applicazioni Web / API utilizza altre librerie o manualmente l'implementazione i protocolli supportati.](#other).

La procedura seguente consente di verificare il corretto funzionamento della logica dell'applicazione.

1. In Visual Studio 2013, aprire la soluzione e quindi fare clic sulla scheda **Esplora Server** nel riquadro destro.
2. Espandere **connessioni dati**, **DefaultConnection**e **tabelle**. Individuare la tabella **IssuingAuthorityKeys** , pulsante destro del mouse e quindi fare clic su **Mostra dati tabella**.
3. Nella tabella **IssuingAuthorityKeys** verrà almeno una riga, che corrisponde al valore per la chiave. Eliminare tutte le righe della tabella.
4. Pulsante destro del mouse nella tabella **tenant** e quindi fare clic su **Mostra dati tabella**.
5. Nella tabella **tenant** , si verificherà almeno una riga, che corrisponde a un identificatore tenant directory univoca. Eliminare tutte le righe della tabella. Non eliminare le righe nella tabella **IssuingAuthorityKeys** sia **tenant di** tabella, verrà visualizzato un messaggio di errore in fase di esecuzione.
6. Creare ed eseguire l'applicazione. Dopo aver registrato al proprio account, è possibile interrompere l'applicazione.
7. Tornare a **Esplora Server** e controllare i valori nella tabella **IssuingAuthorityKeys** e **tenant** . Si noterà sono stati inseriti automaticamente con le relative informazioni dal documento di metadati federazione.

### <a name="vs2013"></a>API Web sulla protezione delle risorse e creato con Visual Studio 2013

Se si crea un'applicazione API web in Visual Studio 2013, utilizzando il modello API Web e selezionare **Account dell'organizzazione** dal menu **Modifica autenticazione** , si dispone già logica necessaria nell'applicazione.

Se si configura manualmente l'autenticazione, seguire le istruzioni seguenti per informazioni su come configurare l'API Web per aggiornare automaticamente le informazioni chiave.

Frammento di codice riportato di seguito viene illustrato come ottenere le chiavi più recenti dal documento di metadati federazione e quindi utilizzare il [Gestore di Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) per convalidare il token. Il frammento di codice si presuppone che si utilizzerà il proprio meccanismo di memorizzazione nella cache per mantenere la chiave per convalidare i token futuri da Azure Active Directory, sia che si tratti di un database, file di configurazione o ad altri aeroporti.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Applicazioni Web sulla protezione delle risorse e creato con Visual Studio 2012

Se l'applicazione è stata creata in Visual Studio 2012, si usava probabilmente lo strumento di accesso e identità per configurare l'applicazione. È inoltre probabile che si sta utilizzando [La convalida emittente nome del Registro di sistema (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). Il VINR è responsabile della gestione delle informazioni sui provider di identità attendibile (Azure Active Directory) e le chiavi utilizzate per convalidare i token emessi da essi. Il VINR semplifica aggiornare automaticamente le informazioni archiviate in un file config scaricando il documento più recente di metadati federazione associato directory, verificare se la configurazione è aggiornata con il documento più recente e l'aggiornamento dell'applicazione di utilizzare la nuova chiave in base alle esigenze.

Se è stato creato l'applicazione utilizzando le esempi di codice o documentazione di questa procedura dettagliata fornita da Microsoft, la logica di attivazione chiave è già inclusa nel progetto. Si noterà che il codice riportato di seguito esiste già nel progetto. Se l'applicazione non dispone già di questa logica, seguire la procedura seguente per aggiungerla e verificare il corretto funzionamento.

1. In **Esplora soluzioni**, aggiungere un riferimento all'assembly **IdentityModel** per il progetto appropriato.
2. Aprire il file **Global.asax.cs** e aggiungere le seguenti direttive using:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Aggiungere il metodo seguente al file **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Richiamare il metodo **RefreshValidationSettings()** nel metodo **Application_Start()** **Global.asax.cs** come illustrato:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Dopo aver seguito le operazioni seguenti, config dell'applicazione verranno aggiornati con le informazioni più recenti dal documento di metadati federazione, incluse le chiavi più recente. Questo aggiornamento verrà eseguita ogni volta che il pool di applicazioni Ricicla in IIS. Per impostazione predefinita IIS impostata Cestino applicazioni ogni ore 29.

Seguire la procedura seguente per verificare che la logica di attivazione chiave funzioni.

1. Dopo aver verificato che l'applicazione utilizza il codice precedente, aprire il file **config** e individuare il **<issuerNameRegistry>** blocco, in particolare ricerca per le operazioni seguenti alcune righe:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. Nel **<add thumbprint=””>** impostare, modificare il valore di identificazione personale sostituendo qualsiasi carattere con uno diverso. Salvare il file **config** .

3. Creare l'applicazione e quindi eseguirlo. Se non è possibile completare la procedura di accesso, l'applicazione sta aggiornando correttamente il tasto scaricando le informazioni richieste dal documento di metadati federazione della directory. Se si sono verificati problemi di accesso, assicurarsi che le modifiche nell'applicazione siano corrette, leggere l'argomento [Aggiunta Sign-On per l'uso di applicazione Web Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) o scaricare e verifica nell'esempio seguente: [Applicazione Cloud multi-Tenant per Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Le applicazioni Web sulla protezione delle risorse e creato con Visual Studio 2008 o 2010 e Windows Identity Foundation (WIF) versione 1.0 per .NET 3.5

Se è stata creata un'applicazione nella versione 1.0 WIF, non è disponibili per aggiornare automaticamente la configurazione dell'applicazione per l'utilizzo di una nuova chiave.

- *Semplice* Utilizzare gli utensili FedUtil inclusi in SDK WIF, che consente di recuperare il documento di metadati più recente e aggiornare la configurazione.
- Aggiornare l'applicazione per .NET 4.5, che include la versione più recente di WIF nello spazio dei nomi del sistema. È quindi possibile utilizzare [La convalida emittente nome del Registro di sistema (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) per eseguire aggiornamenti automatici della configurazione dell'applicazione.
- Eseguire un effetto di attivazione manuale in base alle istruzioni alla fine di questo documento di istruzioni.

Istruzioni per utilizzare il FedUtil per aggiornare la configurazione di:

1. Verificare di avere installato nel computer di sviluppo per Visual Studio 2008 o 2010 SDK versione 1.0 WIF. Se non si dispone ancora installato, è possibile [scaricarlo da questa posizione](https://www.microsoft.com/en-us/download/details.aspx?id=4451) .
2. In Visual Studio, aprire la soluzione, quindi fare clic sul progetto applicabile e selezionare **Aggiorna metadati federazione**. Se questa opzione non è disponibile, FedUtil e/o la versione 1.0 WIF SDK non completata.
3. Dal prompt dei comandi, selezionare **Aggiorna** per avviare l'aggiornamento i metadati di una federazione. Se si ha accesso all'ambiente di server in cui l'applicazione è ospitata, è possibile utilizzare facoltativamente del FedUtil [automatica dei metadati aggiornare utilità di pianificazione](https://msdn.microsoft.com/library/ee517272.aspx).
4. Fare clic su **Fine** per completare il processo di aggiornamento.

### <a name="other"></a>Le applicazioni Web / API proteggere le risorse usando altre raccolte o l'implementazione manualmente i protocolli supportati

Se si utilizza qualche altra libreria o implementata manualmente i protocolli supportati, è necessario esaminare la raccolta o l'implementazione, per assicurarsi che il tasto viene recuperato dal documento di individuazione OpenID connettersi o il documento di metadati federazione. Per verificare se questa è possibile eseguire una ricerca nel codice o della raccolta per tutte le chiamate fuori per il documento di individuazione OpenID o il documento di metadati federazione.

Se la chiave viene archiviato in un punto o hardcoded nell'applicazione, è possibile manualmente recuperare la chiave e l'aggiornamento è conseguenza per eseguire un effetto di attivazione manuale in base alle istruzioni alla fine di questo documento di istruzioni. **È consiglia migliorare l'applicazione per supportare l'attivazione automatica** utilizzando uno degli approcci contorno in questo articolo per evitare interruzioni future e sovraccarico se aumenta Azure Active Directory è digitazione attivazione o che dispone di un effetto di attivazione fuori banda emergenza.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Come verificare l'applicazione per determinare se avrà effetto

È possibile stabilire se l'applicazione supporta attivazione chiave automatico, scaricare gli script e seguire le istruzioni disponibili in [questo repository GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Come eseguire un effetto di attivazione manuale se non si applicazione supporta l'attivazione automatica

Se l'applicazione non **non** attivazione automatica di supporto, è necessario stabilire un processo che periodicamente esegue il monitoraggio chiavi di firma di Azure Active Directory ed esegue un effetto di attivazione manuale di conseguenza. [Questa GitHub repository](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contiene script e istruzioni su come eseguire questa operazione.
