<properties 
    pageTitle="Errore durante il rilevamento di autenticazione" 
    description="La connessione guidata di active directory rilevato un tipo di autenticazione compatibile" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="error-during-authentication-detection"></a>Errore durante il rilevamento di autenticazione

Durante il rilevamento codice autenticazione precedente, la procedura guidata rilevato un tipo di autenticazione non compatibili.   

##<a name="what-is-being-checked"></a>Cosa viene eseguita la verifica?

**Nota:** Per individuare correttamente il codice di autenticazione precedente in un progetto, è necessario compilare il progetto.  Se questo errore e non si dispone di un codice di autenticazione precedente di un progetto, ricompilare e riprovare.

###<a name="project-types"></a>Tipi di progetto

La procedura guidata controlla il tipo di progetto si sviluppano soluzioni in modo che è possibile inserire la logica di autenticazione appropriata nel progetto.  Se è presente un controller che deriva da `ApiController` nel progetto, il progetto verrà considerato un progetto WebAPI.  Se sono presenti solo i controller che derivano da `MVC.Controller` nel progetto, il progetto verrà considerato un progetto MVC.  Qualsiasi altro elemento non è supportato dalla procedura guidata.  Progetti Web Form non sono attualmente supportati.

###<a name="compatible-authentication-code"></a>Codice di autenticazione compatibili

La procedura guidata controlla le impostazioni di autenticazione che sono state configurate in precedenza con la procedura guidata o sono compatibili con la procedura guidata.  Se tutte le impostazioni sono presente, viene considerato un caso rientrante e la creazione guidata verrà aperto e visualizzerà le impostazioni.  Se solo alcune delle impostazioni sono presenti, viene considerato come un caso di errore.

In un progetto MVC, la procedura guidata verifica la presenza di una delle opzioni seguenti derivanti dall'utilizzo precedente della procedura guidata:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Inoltre, la procedura guidata verifica la presenza di una delle opzioni seguenti in un progetto API Web derivanti dall'utilizzo precedente della procedura guidata:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Codice di autenticazione compatibile

Infine, la procedura guidata tenta di rilevare versioni del codice di autenticazione che sono state configurate con le versioni precedenti di Visual Studio. Se si riceve questo errore, significa che il progetto contiene un tipo di autenticazione non compatibili. La procedura guidata rileva i tipi di autenticazione rispetto alle versioni precedenti di Visual Studio seguenti:

* Autenticazione di Windows 
* Singoli account utente 
* Account dell'organizzazione 
 

Per rilevare l'autenticazione di Windows in un progetto MVC, la procedura guidata consente di cercare il `authentication` elemento dal file **config** .

<pre>
    &lt;configurazione&gt;
        &lt;System&gt;
            <span style="background-color: yellow">&lt;modalità di autenticazione = "Windows" /&gt;</span>
        &lt;/System. Web&gt;
    &lt;/Configuration&gt;
</pre>

Per rilevare l'autenticazione di Windows in un progetto API Web, la procedura guidata consente di cercare il `IISExpressWindowsAuthentication` elemento da file con **estensione csproj** del progetto:

<pre>
    &lt;Progetto&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;abilitato&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/progetto        &gt;
</pre>

Per rilevare l'autenticazione di singoli account utente, la procedura guidata consente di cercare l'elemento package dal file **Packages.config** .

<pre>
    &lt;pacchetti&gt;
        <span style="background-color: yellow">&lt;pacchetto id="Microsoft.AspNet.Identity.EntityFramework" versione = "2.1.0" targetFramework = "net45" /&gt;</span>
    &lt;/pacchetti&gt;
</pre>

Per individuare un formato precedente del autenticazione dell'Account dell'organizzazione, la procedura guidata consente di cercare l'elemento seguente da **Web. config**:

<pre>
    &lt;configurazione&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;aggiungere chiave = valore "ida: autenticazione" = "*" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/Configuration&gt;
</pre>

Per modificare il tipo di autenticazione, rimuovere il tipo di autenticazione compatibile ed eseguire nuovamente la procedura guidata.

Per ulteriori informazioni, vedere [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md).