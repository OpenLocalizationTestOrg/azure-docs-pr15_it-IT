<properties 
    pageTitle="Per iniziare con Azure Active Directory e i servizi di Visual Studio connessi (progetti MVC) | Microsoft Azure" 
    description="Come iniziare a utilizzare Azure Active Directory in progetti MVC dopo la connessione a o la creazione di un Azure Active Directory utilizzando Visual Studio connessi servizi" 
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

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Guida introduttiva di Azure Active Directory e Visual Studio connessi servizi (MVC progetti)

> [AZURE.SELECTOR]
> - [Guida introduttiva](vs-active-directory-dotnet-getting-started.md)
> - [Cos'è successo](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Richiedere l'autenticazione per controller di accesso 

Tutti i controller di un progetto sono state decorare con l'attributo di **autorizzazione** . Questo attributo richiede all'utente di essere autenticati prima di accedere a questi controller. Per consentire al controller di consentito l'accesso anonimo, rimuovere l'attributo dal controller. Se si desidera impostare le autorizzazioni per una maggiore precisione, applicare l'attributo a ogni metodo che richiede l'autorizzazione anziché l'applicazione per la classe controller.
 
##<a name="adding-signin--signout-controls"></a>Aggiunta di accesso / SignOut controlli 

Per aggiungere un controlli/SignOut di accedere alla propria visualizzazione, è possibile utilizzare la visualizzazione parziale **_LoginPartial.cshtml** per aggiungere la funzionalità di una visualizzazione. Ecco un esempio di funzionalità aggiunto alla visualizzazione standard **cshtml** . (Si noti l'ultimo elemento div con barra di spostamento e compressione di classe):

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[Ulteriori informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 
