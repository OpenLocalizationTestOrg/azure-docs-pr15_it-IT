<properties
    pageTitle="Dov'è il progetto WebApi (Visual Studio Azure Active Directory connesso servizio) | Microsoft Azure "
    description="Descrivere cosa accade al progetto MVC WebApi si connette a Azure Active Directory utilizzando Visual Studio"
  services="active-directory"
    documentationCenter=""
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

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Dov'è il progetto WebApi (Visual Studio Azure Active Directory connesso servizio)

> [AZURE.SELECTOR]
> - [Guida introduttiva](vs-active-directory-webapi-getting-started.md)
> - [Cos'è successo](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>Sono stati aggiunti riferimenti

###<a name="nuget-package-references"></a>Riferimenti del pacchetto NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>Riferimenti .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>Modifiche al codice

###<a name="code-files-were-added-to-your-project"></a>File di codice sono stati aggiunti al progetto

Una classe di avvio di autenticazione, **App_Start/Startup.Auth.cs** è stata aggiunta al progetto che contiene la logica di avvio per l'autenticazione di Azure Active Directory.

###<a name="startup-code-was-added-to-your-project"></a>Codice di avvio è stata aggiunta al progetto

Se si dispone già di un corso di avvio di un progetto, il metodo di **configurazione** è stato aggiornato per includere una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe di avvio è stata aggiunta al progetto.


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Il file App o Web. config contenga nuovi valori di configurazione.

Sono state aggiunte le seguenti voci di configurazione.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>È stata creata un'App di Azure Active Directory

Applicazione Azure Active Directory è stata creata nella cartella selezionata nella procedura guidata.

[Ulteriori informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se è selezionata, *disattivare l'autenticazione di singoli account utente*, le altre modifiche apportate a un progetto?
Riferimenti del pacchetto NuGet sono stati rimossi e i file sono state rimosse e backup. In base allo stato del progetto, potrebbe essere necessario manualmente rimuovere riferimenti aggiuntivi o un file o modificare codice in base alle esigenze.

###<a name="nuget-package-references-removed-for-those-present"></a>Riferimenti del pacchetto NuGet rimossi (per quelli presenti)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>Codice di file sottoposti a backup e rimosso (per quelli presenti)

Ognuno dei file seguenti è stato eseguito il backup e rimosso dal progetto. File di backup si trovano in una cartella di 'Backup' nella radice della directory del progetto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>Backup dei file di codice (per quelli presenti)

Ognuno dei seguenti file di backup prima vengono sostituiti. File di backup si trovano in una cartella di 'Backup' nella radice della directory del progetto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se è selezionata *Lettura directory dati*, le altre modifiche apportate a un progetto?

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Altre modifiche apportate all'App o Web. config

Le voci di configurazione aggiuntive seguenti sono state aggiunte.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>L'App di Azure Active Directory è stato aggiornato
L'App di Azure Active Directory è stato aggiornato per includere l'autorizzazione di *Lettura directory dati* e un tasto aggiuntivo è stato creato che è stato utilizzato come *ida: Password* nel `web.config` file.

[Ulteriori informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
