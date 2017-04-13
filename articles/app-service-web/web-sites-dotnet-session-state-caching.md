<properties 
    pageTitle="Stato sessione con Azure Redis cache nel servizio App Azure" 
    description="Informazioni su come utilizzare il servizio Cache Azure per supportare la memorizzazione nella cache di ASP.NET sessione stato." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Stato sessione con Azure Redis cache nel servizio App Azure


In questo argomento viene illustrato come utilizzare il servizio Cache Redis Azure per lo stato della sessione.

Se l'applicazione web ASP.NET utilizza lo stato della sessione, sarà necessario configurare un provider di stato sessione esterni (il servizio Cache Redis o un provider di stato della sessione di SQL Server). Se si usa lo stato della sessione e non un provider di servizi esterno, sarà limitato a un'istanza di un'applicazione web. Il servizio Cache Redis è più veloce e più semplice da abilitare.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>Creare la Cache
Seguire [queste istruzioni](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) per creare la cache.

##<a id="configureproject"></a>Aggiungere il pacchetto RedisSessionStateProvider NuGet a un'applicazione web
Installare il NuGet `RedisSessionStateProvider` pacchetto.  Utilizzare il comando seguente per installare dalla console di gestione di pacchetto (**Strumenti** > **Gestione pacchetti NuGet** > **Console di gestione pacchetti**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Per installare da **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci pacchetti NugGet per soluzione**, cercare `RedisSessionStateProvider`.

Per ulteriori informazioni vedere la [pagina NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [configurare il client della cache](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modificare il File config
Oltre a rendere riferimenti per Cache, il pacchetto NuGet aggiunge stub voci in *config* . 

1. Aprire il *Web. config* e trovare l'elemento **sessionState** .

1. Immettere i valori per `host`, `accessKey`, `port` (porta SSL deve essere 6380) e impostare `SSL` a `true`. Questi valori possono essere ottenuti da e il [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) per l'istanza della cache. Per ulteriori informazioni, vedere [connettersi alla cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Si noti che la porta SSL non è disattivata per impostazione predefinita per nuove cache. Per ulteriori informazioni sull'attivazione della porta non SSL, vedere la sezione di [Accesso a porte](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) nella sezione [Configura una cache nella Cache Redis Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx) . Di seguito viene illustrato le modifiche al file *config* , in particolare le modifiche alla *porta*, *host*, accessKey*, e *ssl *.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>Utilizzare l'oggetto sessione nel codice
Il passaggio finale consiste nell'iniziare usando l'oggetto sessione nel codice ASP.NET. Aggiungere oggetti sessione utilizzando il metodo **Session.Add** . Questo metodo utilizza coppie di parole chiave valore per archiviare gli elementi nella cache di stato della sessione.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Il codice seguente recupera il valore dallo stato sessione.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Utilizzare la Cache Redis agli oggetti cache nelle applicazioni web. Per ulteriori informazioni, vedere [app filmato MVC con Azure Redis Cache 15 minuti](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Per ulteriori informazioni su come utilizzare lo stato sessione ASP.NET, vedere [Panoramica di stato sessione ASP.NET][].

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Da [Zaffaroni RTF](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [Panoramica di stato sessione ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
