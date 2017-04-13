<properties
   pageTitle="Utilizzo delle identità basato sulle attestazioni nelle applicazioni multi-tenant | Microsoft Azure"
   description="Come utilizzare azioni per la convalida emittente e l'autorizzazione"
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

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Utilizzo delle identità basata sulle attestazioni nelle applicazioni multi-tenant

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

In questo articolo fa [parte di una serie]. È inoltre disponibile un' [applicazione di esempio] completa relativa a questa serie.

## <a name="claims-in-azure-ad"></a>Sulle attestazioni in Azure Active Directory

Quando un utente accede, Azure Active Directory invia token ID che contiene una serie di attestazioni relative all'utente. Una richiesta di rimborso è semplicemente un'informazione, espresso come una coppia chiave/valore. Ad esempio `email` = `bob@contoso.com`.  Reclami dispone di un emittente &mdash; in questo caso, Azure Active Directory &mdash; che è l'entità che autentica l'utente e crea i reclami. Si ritiene attendibile crediti perché si ritiene attendibile autorità. (Al contrario, se non si considera attendibile autorità, non considerare attendibili i crediti!)

Alto livello:

1.  Autenticazione dell'utente.
2.  Il IDP invia una serie di attestazioni.
3.  L'app Normalizza o integra crediti (facoltativi).
4.  L'app Usa le attestazioni per prendere decisioni sulle autorizzazioni.

In OpenID connettersi, insieme ai reclami che viene visualizzato viene controllata dal [parametro ambito] della richiesta di autenticazione. Problemi di un numero limitato di attestazioni tramite OpenID connettere; tuttavia Azure Active Directory vedere [supportati Token e tipi di attestazione]. Se si desiderano ulteriori informazioni sull'utente, è necessario usare l'API di Azure Active Directory grafico.

Ecco alcuni dei crediti da AAD un'app potrebbe in genere interessano:

Tipo di token ID attestazione |    Descrizione
-----------------------|--------------
AUD | Chi è stato rilasciato il token per. Questo sarà l'ID client. dell'applicazione In genere, non è necessario preoccuparsi di questa domanda, perché il middleware convalida automaticamente. Esempio:`"91464657-d17a-4327-91f3-2ed99386406f"`
gruppi   | Elenco dei gruppi AAD di cui l'utente è un membro. Esempio:`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | [Emittente] del token OIDC. Esempio:`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
nome    | Nome visualizzato dell'utente. Esempio:`"Alice A."`
OID | Identificatore di oggetto per l'utente in AAD. Questo valore è l'identificatore non modificabile e non riutilizzabile dell'utente. Utilizzare questo valore, non di posta elettronica, come un identificatore univoco per gli utenti. gli indirizzi di posta elettronica possono cambiare. Se si utilizza l'API di Azure Active Directory grafico nell'app, ID oggetto è il valore utilizzato per le informazioni sul profilo di query. Esempio:`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
ruoli   | Elenco di ruoli di app per l'utente. Esempio:`["SurveyCreator"]`
TID | ID tenant. Questo valore è un identificatore univoco per il tenant di Azure Active Directory. Esempio:`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique_name | Un nome visualizzato leggibile umano dell'utente. Esempio:`"alice@contoso.com"`
UPN | Nome dell'entità utente. Esempio:`"alice@contoso.com"`

Questa tabella sono inclusi i tipi di domanda come vengono visualizzati in token ID. ASP.NET Core 1.0 middleware OpenID connettersi viene convertito in alcuni dei tipi di domanda quando compila l'insieme di reclami per l'utente principale:

-   OID >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   TID >`http://schemas.microsoft.com/identity/claims/tenantid`
-   unique_name >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Trasformazioni delle attestazioni

Durante il flusso di autenticazione desiderato per la modifica delle attestazioni ottenuta dal IDP. In ASP.NET Core 1.0, è possibile eseguire trasformazione delle attestazioni all'interno dell'evento **AuthenticationValidated** da middleware OpenID connettersi. (Vedere [eventi di autenticazione]).

Reclami aggiunte durante **AuthenticationValidated** sono archiviate nel cookie di autenticazione della sessione. Sono non accedere nuovamente inseriti di Azure Active Directory.

Ecco alcuni esempi di trasformazione delle attestazioni:

-   **Normalizzazione reclami**o rendendo sulle attestazioni coerenti per gli utenti. Questa operazione è particolarmente importante se si desidera ottenere sulle attestazioni da più IDPs, che potrebbe utilizzare tipi di attestazioni diversi per le informazioni simili.
Ad esempio, Azure Active Directory invia una richiesta di rimborso "upn" che contiene posta elettronica dell'utente. Altri IDPs può inviare una richiesta di rimborso "posta elettronica". Il codice seguente converte Richiedi "upn" in una richiesta di rimborso "posta elettronica":

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Aggiungere **i valori predefiniti attestazione** per crediti che non sono presenti &mdash; , ad esempio, l'assegnazione di un utente a un ruolo predefinito. In alcuni casi questo può semplificare la logica di autorizzazione.
- Aggiungere **tipi di attestazione personalizzata** con le informazioni specifiche di un'applicazione all'utente. Ad esempio, è possibile archiviare alcune informazioni sull'utente in un database. È possibile aggiungere un'attestazione personalizzata con queste informazioni al ticket di autenticazione. Credito verrà archiviato in cookie, pertanto è necessario iniziare dal database una volta per sessione di accesso. Mano, è evitare di creare troppo grandi cookie, in modo da tenere in considerazione compromesso tra dimensione cookie e ricerche nel database.   

Dopo avere inserito il flusso di autenticazione, le attestazioni sono disponibili in `HttpContext.User`. A questo punto, è necessario considerare come un insieme di sola lettura &mdash; ad esempio, utilizzarli per prendere decisioni sulle autorizzazioni.

## <a name="issuer-validation"></a>Convalida emittente
In OpenID connettersi, la richiesta di emittente ("iss") identifica IDP che ha rilasciato token ID. Parte del flusso di autenticazione OIDC consiste nel verificare che la richiesta di emittente corrisponda autorità effettivo. Middleware OIDC viene gestita automaticamente.

In Azure Active Directory, il valore emittente sia univoco per ogni tenant di Active Directory (`https://sts.windows.net/<tenantID>`). Pertanto, un'applicazione deve eseguire un ulteriore controllo per assicurarsi che autorità rappresenta un tenant che è possibile accedere all'app.

Per un'applicazione singola tenant, è possibile controllare solo che autorità è il proprio tenant. Infatti, middleware OIDC responsabile automaticamente per impostazione predefinita. In un'app multi-tenant, è necessario consentire più emittenti corrispondente ai tenant diverso. Ecco un approccio generale da usare:

-   Nelle opzioni di middleware OIDC impostare **ValidateIssuer** su false. Questa opzione disattivi il controllo automatico.
-   Quando si iscrive un tenant, archiviare il tenant e autorità nell'utente DB.
-   Ogni volta che un utente accede, cercare autorità nel database. Se non è stato trovato autorità, significa che tenant non ha effettuato l'iscrizione. È possibile reindirizzarli a una pagina di iscrizione.
-  È anche possibile blacklist determinati tenant; ad esempio, per i clienti che non pagare il proprio abbonamento.

Per informazioni più dettagliate, vedere [per l'abbonamento e onboarding in un'applicazione multi-tenant del tenant][signup].

## <a name="using-claims-for-authorization"></a>Uso delle attestazioni di autorizzazione

Con sulle attestazioni, identità dell'utente non è più un'entità su. Ad esempio, un utente può avere un indirizzo di posta elettronica, il numero di telefono, compleanno, sesso e così via. Forse IDP dell'utente archivia le informazioni. Ma quando si esegue l'autenticazione dell'utente, in genere si riceverà un sottoinsieme di questi elementi come reclami. In questo modello, l'identità dell'utente è semplicemente un'aggregazione dei reclami. Quando si apportano decisioni relative alle autorizzazioni relative a un utente, si presenterà per insiemi specifici ai reclami. In altre parole, la domanda "Anche utente X operazione Y" diventa infine "dispone di X attestazione utente Z".

Ecco alcuni modelli di base per il controllo delle attestazioni.

-  Per verificare che l'utente ha una domanda specifica con un valore specifico:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Questo codice viene verificato se l'utente ha una domanda di ruolo con il valore "Amministratore". Gestisce correttamente le maiuscole/minuscole nel punto in cui l'utente ha Richiedi alcun ruolo o più delle attestazioni ruolo.

    La classe **ClaimType** definisce costanti per i tipi di domanda comune. Tuttavia, è possibile utilizzare qualsiasi valore stringa per il tipo di domanda.

-   Per ottenere un singolo valore di un tipo di attestazione, quando si prevede che si verifichi almeno un valore:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Per ottenere tutti i valori di un tipo di domanda:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Per ulteriori informazioni, vedere [autorizzazione basato sui ruoli e basate sulle risorse nelle applicazioni multi-tenant][authorization].

## <a name="next-steps"></a>Passaggi successivi

- Articolo successivo in questa serie: [per l'abbonamento e onboarding in un'applicazione multi-tenant del tenant][signup]
- Ulteriori informazioni [sull'autorizzazione basata sulle attestazioni] nella documentazione ASP.NET Core 1.0

<!-- Links -->
[parte di una serie]: guidance-multitenant-identity.md
[parametro di ambito]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Token supportati e tipi di attestazione]: ../active-directory/active-directory-token-and-claims.md
[emittente]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Eventi di autenticazione]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Autorizzazione basata sulle attestazioni]: https://docs.asp.net/en/latest/security/authorization/claims.html
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
