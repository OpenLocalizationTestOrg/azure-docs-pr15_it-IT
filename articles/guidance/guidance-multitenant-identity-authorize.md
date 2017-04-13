<properties
   pageTitle="Autorizzazione nelle applicazioni multi-tenant | Microsoft Azure"
   description="Come eseguire l'autorizzazione in un'applicazione multi-tenant"
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
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Autorizzazione basato sui ruoli e basate sulle risorse nelle applicazioni multi-tenant

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

In questo articolo fa [parte di una serie]. È inoltre disponibile un' [applicazione di esempio] completa relativa a questa serie.

L' [implementazione di riferimento] è un'applicazione ASP.NET Core 1.0. In questo articolo vengono illustrate due opzioni generali di autorizzazione, con l'autorizzazione API fornite in ASP.NET Core 1.0.

-   **Autorizzazione basato sui ruoli**. L'autorizzazione di un'azione in base ai ruoli assegnati a un utente. Ad esempio, alcune azioni richiedono un ruolo di amministratore.
-   **Autorizzazione basate sulle risorse**. L'autorizzazione di un'azione in base a una determinata risorsa. Ad esempio, ogni risorsa dispone di un proprietario. Il proprietario può eliminare la risorsa. non è possibile ad altri utenti.

Una tipica app verranno utilizzare una combinazione di entrambi. Ad esempio, per eliminare una risorsa, l'utente deve essere la risorsa proprietario _o_ amministratore.


## <a name="role-based-authorization"></a>Autorizzazione basato sui ruoli

[Sondaggi Tailspin] [ Tailspin] applicazione definisce i ruoli seguenti:

- Amministratore. Possono eseguire tutte le operazioni CRUD su qualsiasi sondaggio appartenente a tenant.
- Autore. Creare nuovi sondaggi
- Utilità per la lettura. Leggere indagini che appartengono al tenant

Ruoli applicano agli _utenti_ dell'applicazione. Nell'applicazione di sondaggi, un utente è un amministratore, creatore o reader.

Per informazioni su come definire e gestire i ruoli, vedere [ruoli applicazione].

Indipendentemente da come si gestiscono i ruoli, il codice di autorizzazione sarà simile. Principali ASP.NET 1.0 introduce astrazione denominata [criteri di autorizzazione][policies]. Con questa caratteristica, definire i criteri di autorizzazione nel codice e quindi applicare i criteri per le azioni del controller. Il criterio viene separato dal controller.

### <a name="create-policies"></a>Creare criteri

Per definire un criterio, prima di tutto creare una classe implementata `IAuthorizationRequirement`. È facile derivano da `AuthorizationHandler`. Nel `Handle` metodo, esaminare i contenuti pertinenti claim(s).

Ecco un esempio dall'applicazione Tailspin sondaggi:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Vedere [SurveyCreatorRequirement.cs]

Questa classe definisce il requisito di un utente creare un nuovo sondaggio. L'utente deve essere ruolo SurveyAdmin o SurveyCreator.

In una classe di avvio, definire un criterio denominato che include uno o più requisiti. Se sono presenti più requisiti, l'utente deve soddisfare _ogni_ requisito di autorizzazione. Il codice seguente definisce due criteri:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Vedere [Startup.cs]

Questo codice imposta anche lo schema di autenticazione, che indica ad ASP.NET quali middleware autenticazione deve essere eseguita se si verifica un errore di autorizzazione. In questo caso, si specifica middleware autenticazione cookie, poiché il middleware autenticazione cookie possibile reindirizzare l'utente a una pagina di "Accesso negato". La posizione della pagina di accesso negato sia impostata l'opzione AccessDeniedPath per middleware cookie; vedere [configurazione middleware l'autenticazione].

### <a name="authorize-controller-actions"></a>Autorizzare le azioni del controller

Infine, per autorizzare un'azione in un controller MVC, impostare il criterio `Authorize` attributo:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Nelle versioni precedenti di ASP.NET, impostare la proprietà **ruoli** sull'attributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Questo è ancora supportato in ASP.NET Core 1.0, ma presenta alcuni svantaggi rispetto ai criteri di autorizzazione:

-   Si presuppone che un tipo di attestazione specifico. Criteri di controllare per qualsiasi tipo di domanda. Ruoli sono solo un tipo di domanda.
-   Il nome del ruolo è hardcoded nell'attributo. Con i criteri della logica dell'autorizzazione è tutto in un'unica posizione, rendendo più semplice per aggiornare o per caricarli anche dalle impostazioni di configurazione.
-   Grazie ai criteri più complessi decisioni relative alle autorizzazioni (ad esempio, l'età > = 21) che non può essere espressa in base all'appartenenza ruolo semplice.

## <a name="resource-based-authorization"></a>Autorizzazione alle risorse in base a

_Risorse in base a autorizzazione_ si verifica quando l'autorizzazione dipende da una risorsa specifica interessate da un'operazione. Nell'applicazione Tailspin sondaggi ogni sondaggio ha un proprietario e collaboratori zero-a-molti.

-   Il proprietario può leggere, aggiornare, eliminare, pubblicare e annullare la pubblicazione del sondaggio.
-   Il proprietario può assegnare ai collaboratori al sondaggio.
-   Collaboratori possano leggere e aggiornare il sondaggio.

Si noti che "proprietario" e "Collaboratore" non sono ruoli applicazione. vengono memorizzati per un sondaggio, nel database dell'applicazione. Per verificare se un utente può eliminare un sondaggio, ad esempio, l'app viene verificato se l'utente è proprietario di tale sondaggio.

In ASP.NET Core 1.0 implementare l'autorizzazione basate sulle risorse, che derivano da **AuthorizationHandler** e si esegue l'override il metodo di **gestire** .

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Si noti che questa classe tipizzata per gli oggetti di un sondaggio.  Eseguire la registrazione della classe per DI all'avvio:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Per eseguire i controlli di autorizzazione, utilizzare l'interfaccia **IAuthorizationService** , è possibile inserire in controller di. Il codice riportato di seguito viene verificato se un utente può leggere un sondaggio:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Poiché vengono passati un `Survey` oggetto, questo verrà richiamare il `SurveyAuthorizationHandler`.

Nel codice autorizzazione un approccio ottima consiste nel aggrega tutte le autorizzazioni dell'utente basato sui ruoli e basate sulle risorse, quindi controllo imposta l'aggregazione contro l'operazione desiderata.
Ecco un esempio dall'app sondaggi. L'applicazione definisce diversi tipi di autorizzazione:

- Amministratore
- Per i collaboratori
- Autore
- Proprietario
- Utilità per la lettura

L'applicazione definisce anche un insieme di operazioni possibili in sondaggi:

- Creare
- Lettura
- Aggiornamento
- Elimina
- Pubblicare
- Unpublsh

Il codice seguente viene creato un elenco delle autorizzazioni per un utente specifico e sondaggio. Si noti che questo codice esamina ruoli app dell'utente e i campi proprietario/collaboratori del sondaggio.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Vedere [SurveyAuthorizationHandler.cs].

In un'applicazione multi-tenant, è necessario assicurarsi che le autorizzazioni non "perdita" ai dati del tenant. Nell'applicazione di sondaggi, l'autorizzazione per i collaboratori è consentito attraverso tenant &mdash; è possibile assegnare un utente da un altro tenant come un contriubutor. Gli altri tipi di autorizzazione sono limitati a risorse che appartengono a tenant dell'utente. Per applicare questo requisito, il codice di verifica ID tenant prima di concedere l'autorizzazione. (La `TenantId` campo assegnato quando viene creato il sondaggio.)

Il passaggio successivo consiste nel selezionare l'operazione (lettura, aggiornamento, eliminazione e così via) con le autorizzazioni. L'app sondaggi implementato questo passaggio tramite una tabella di ricerca delle funzioni:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Passaggi successivi

- Articolo successivo in questa serie: [protezione di un back-end web API in un'applicazione multi-tenant][web-api]
- Per ulteriori informazioni sull'autorizzazione delle risorse in base a ASP.NET 1.0 principali, vedere [Autorizzazione delle risorse in base a][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte di una serie]: guidance-multitenant-identity.md
[Ruoli applicazione]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[implementazione di riferimento]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configurazione middleware autenticazione]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
