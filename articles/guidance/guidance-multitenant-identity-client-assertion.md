<properties
   pageTitle="Utilizzo di asserzione client per ottenere i token di accesso da Azure Active Directory | Microsoft Azure"
   description="Informazioni su come usare asserzione client per ottenere i token di accesso da Azure Active Directory."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="using-client-assertion-to-get-access-tokens-from-azure-ad"></a>Utilizzo di asserzione client per ottenere i token di accesso da Azure Active Directory

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

In questo articolo fa [parte di una serie]. È inoltre disponibile un' [applicazione di esempio] completa relativa a questa serie.

## <a name="background"></a>Sfondo

Quando si utilizza il flusso di codice di autorizzazione o ibrido flusso OpenID connettersi, il client Scambia un codice di autorizzazione per un token di accesso. Durante questo passaggio, il client deve autenticarsi al server.

![Segreto del client](media/guidance-multitenant-identity/client-secret.png)

È possibile autenticare i client consiste nell'utilizzare un segreto del client. Ecco come [Sondaggi Tailspin] [ Surveys] applicazione è configurata per impostazione predefinita.

Ecco una richiesta di esempio dal client al IDP, richiedendo un token di accesso. Nota la `client_secret` parametro.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

Il segreto è una stringa, pertanto è necessario assicurarsi di non perdere il valore. La procedura consigliata è mantenere il segreto del client fuori controllo del codice sorgente. Se si distribuisce Azure, archiviare il segreto in un' [impostazione di app][configure-web-app].

Tuttavia, chiunque abbia accesso alla sottoscrizione Azure possa visualizzare le impostazioni di app. Inoltre, che è sempre per archiviare informazioni riservate nel controllo del codice sorgente (ad esempio gli script di distribuzione), condividerli tramite posta elettronica e così via.

Per rafforzare la sicurezza, è possibile utilizzare [asserzione client] anziché un segreto del client. Con asserzione client, il client utilizza un certificato x. 509 per provare che la richiesta token proviene dal client. Il certificato client viene installato sul server web. In genere, sarà più semplice limitare l'accesso al certificato, quelle per assicurarsi che nessuno inavvertitamente, vengono visualizzate una segreto del client. Per ulteriori informazioni sulla configurazione dei certificati in un'app web, vedere [Uso dei certificati nelle applicazioni di siti Web di Azure][using-certs-in-websites]

Ecco una richiesta di token utilizzando asserzione client:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Si noti che la `client_secret` parametro non è più utilizzato. Invece di `client_assertion` parametro contiene un token JWT che è stato effettuato l'accesso con il certificato client. Il `client_assertion_type` parametro specifica il tipo di asserzione &mdash; in questo caso, token JWT. Il server convalida token JWT. Se il token JWT non è valido, la richiesta token restituisce un errore.

> [AZURE.NOTE] Certificati x. 509 non sono sola sotto forma di asserzione client; Abbiamo focalizzare l'attenzione su di esso qui supportato da Azure Active Directory.

## <a name="using-client-assertion-in-the-surveys-application"></a>Utilizzo di asserzione client nell'applicazione di sondaggi

In questa sezione illustra come configurare l'applicazione Tailspin sondaggi per utilizzare asserzione client. In questa procedura, verrà generato un certificato autofirmato è adatto per lo sviluppo, ma non per l'utilizzo di produzione.

1. Esecuzione di script di PowerShell [/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] come indicato di seguito:

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Per la `Subject` parametro, immettere un nome di qualsiasi tipo, ad esempio "surveysapp". Lo script genera un certificato autofirmato e archiviati nell'archivio certificati "utente corrente/personale".

2. L'output dello script è un frammento JSON. Aggiungere questo manifesto dell'applicazione di web app, come indicato di seguito:

    1. Accedere al [portale di gestione Azure] [ azure-management-portal] e passare alla directory Azure Active Directory.

    2. Fare clic su **applicazioni**.

    3. Selezionare l'applicazione di sondaggi.

    4.  Fare clic su **Gestisci manifesto** e scegliere **Scarica manifesto**.

    5.  Aprire il file JSON manifesto in un editor di testo. Incollare l'output di script nel `keyCredentials` proprietà. Dovrebbe essere simile al seguente:

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.  Salvare le modifiche al file JSON.

    7.  Tornare al portale. Fare clic su **Gestisci manifesto** > **Caricare manifesto** e caricare il file JSON.

3. Eseguire il seguente comando per ottenere l'identificazione del certificato digitale.

    ```
    certutil -store -user my [subject]
    ```

    dove `[subject]` è il valore specificato per oggetto dello script di PowerShell. L'identificazione personale viene elencato in "Certificato Hash(sha1)". Rimuovere gli spazi tra i numeri esadecimali.

4. Aggiornare le informazioni riservate app. In Esplora soluzioni fare clic sul progetto Tailspin.Surveys.Web e selezionare **Gestione delle informazioni riservate dell'utente**. Aggiungere una voce per "Asimmetrica" in "AzureAd", come illustrato di seguito:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    È necessario impostare `ValidationRequired` su false, perché il certificato non è stato firmato da un'autorità CA radice. In produzione, usare un certificato firmato da un'autorità CA e impostare `ValidationRequired` su true.

    Eliminare anche la voce relativa `ClientSecret`, perché non è necessaria con asserzione client.

5. In Startup.cs, individuare il codice che registra il `ICredentialService`. Rimuovere il commento che utilizza `CertificateCredentialService`e commento la riga che utilizza `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

In fase di esecuzione dell'applicazione web legge il certificato dall'archivio certificati. Il certificato deve essere installato nello stesso computer con l'app web.

## <a name="next-steps"></a>Passaggi successivi

- Articolo successivo in questa serie: [Uso Azure chiave archivio per proteggere le informazioni riservate applicazione][key vault]


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[asserzione client]: https://tools.ietf.org/html/rfc7521
[key vault]: guidance-multitenant-identity-keyvault.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[parte di una serie]: guidance-multitenant-identity.md
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
