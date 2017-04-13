<properties
    pageTitle="Dov'è il progetto MVC (Visual Studio Azure Active Directory connesso servizio) | Microsoft Azure "
    description="Descrivere cosa accade al progetto MVC quando ci si connette a Azure Active Directory mediante i servizi di Visual Studio connessi"
    services="active-directory"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Dov'è il progetto MVC (Visual Studio Azure Active Directory connesso servizio)?

> [AZURE.SELECTOR]
> - [Guida introduttiva](vs-active-directory-dotnet-getting-started.md)
> - [Cos'è successo](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>Sono stati aggiunti riferimenti

### <a name="nuget-package-references"></a>Riferimenti del pacchetto NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Riferimenti .NET

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Codice è stato aggiunto

### <a name="code-files-were-added-to-your-project"></a>File di codice sono stati aggiunti al progetto

Una classe di avvio di autenticazione, **App_Start/Startup.Auth.cs** è stata aggiunta al progetto che contiene la logica di avvio per l'autenticazione di Azure Active Directory. Inoltre, è stata aggiunta una classe controller, Controllers/AccountController.cs che contiene i metodi **SignIn()** e **SignOut()** . Infine, di una visualizzazione parziale **Views/Shared/_LoginPartial.cshtml** è stata aggiunta contenente un collegamento all'azione per accedere/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Codice di avvio è stata aggiunta al progetto

Se si dispone già di un corso di avvio di un progetto, il metodo di **configurazione** è stato aggiornato per includere una chiamata a **ConfigureAuth(app)**. In caso contrario, una classe di avvio è stata aggiunta al progetto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>L'App o Web. config con nuovi valori di configurazione

Sono state aggiunte le seguenti voci di configurazione.


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>È stata creata un'App di Azure Active Directory (AD)
Applicazione Azure Active Directory è stata creata nella cartella selezionata nella procedura guidata.

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se è selezionata, *disattivare l'autenticazione di singoli account utente*, le altre modifiche apportate a un progetto?
Riferimenti del pacchetto NuGet sono stati rimossi e i file sono state rimosse e backup. In base allo stato del progetto, potrebbe essere necessario manualmente rimuovere riferimenti aggiuntivi o un file o modificare codice in base alle esigenze.

### <a name="nuget-package-references-removed-for-those-present"></a>Riferimenti del pacchetto NuGet rimossi (per quelli presenti)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Codice di file sottoposti a backup e rimosso (per quelli presenti)

Ognuno dei file seguenti è stato eseguito il backup e rimosso dal progetto. File di backup si trovano in una cartella di 'Backup' nella radice della directory del progetto.

- **App_Start\IdentityConfig.cs**
- **Controllers\ManageController.cs**
- **Models\IdentityModels.cs**
- **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Backup dei file di codice (per quelli presenti)

Ognuno dei seguenti file di backup prima vengono sostituiti. File di backup si trovano in una cartella di 'Backup' nella radice della directory del progetto.

- **Startup.cs**
- **App_Start\Startup.auth.cs**
- **Controllers\AccountController.cs**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se è selezionata *Lettura directory dati*, le altre modifiche apportate a un progetto?

Riferimenti aggiuntivi sono stati aggiunti.

###<a name="additional-nuget-package-references"></a>Riferimenti pacchetto NuGet aggiuntivi

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>Riferimenti .NET aggiuntivi

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>File di codice aggiuntivi sono stati aggiunti al progetto

Due file sono stati aggiunti per supportare la memorizzazione nella cache token: **Models\ADALTokenCache.cs** e **Models\ApplicationDbContext.cs**.  Per illustrare l'accesso alle informazioni del profilo utente tramite grafico Azure API sono stati aggiunti un controller aggiuntivo e visualizzazione.  Questi file sono **Controllers\UserProfileController.cs** e **Views\UserProfile\Index.cshtml**.

###<a name="additional-startup-code-was-added-to-your-project"></a>Codice di avvio aggiuntivo è stato aggiunto al progetto

Nel file **startup.auth.cs** è stato aggiunto un nuovo oggetto **OpenIdConnectAuthenticationNotifications** al membro **le notifiche** di **OpenIdConnectAuthenticationOptions**.  Si tratta consente di ricevere il codice OAuth di exchange per un token di accesso.

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Altre modifiche apportate all'App o Web. config

Le voci di configurazione aggiuntive seguenti sono state aggiunte.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Sono state aggiunte le seguenti sezioni di configurazione e la stringa di connessione.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>L'App di Azure Active Directory è stato aggiornato
L'App di Azure Active Directory è stato aggiornato per includere l'autorizzazione di *Lettura directory dati* e un tasto aggiuntivo è stato creato che è stato utilizzato come *ida: ClientSecret* in **config** .

[Ulteriori informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
