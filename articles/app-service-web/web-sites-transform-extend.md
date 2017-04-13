<properties
    pageTitle="Azure App servizio web app avanzate config ed estensioni"
    description="Utilizzare le dichiarazioni Transformation(XDT) documento XML per trasformare il file applicationHost nell'app web di servizio App Azure e aggiungere le estensioni private per attivare le azioni di amministrazione personalizzato."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Azure App servizio web app avanzate config ed estensioni

Utilizzando le dichiarazioni di [Trasformazione di documento XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), è possibile trasformare il file [applicationHost](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) nell'app web di Azure App servizio. È anche possibile utilizzare dichiarazioni XDT per aggiungere estensioni private per attivare le azioni di amministrazione app web personalizzata. In questo articolo include un'estensione esempio PHP Manager web app che consente la gestione delle impostazioni di PHP mediante un'interfaccia web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configurazione avanzata tramite applicationHost
La piattaforma servizi App offre flessibilità e controllo per la configurazione dell'applicazione web. Anche se il file di configurazione IIS applicationHost standard non è disponibile per la modifica diretta nel servizio di App, la piattaforma supporta un modello di trasformazione applicationHost dichiarativo basato su XML documento trasformazione (XDT).

Per sfruttare questa funzionalità di trasformazione, si crea un file ApplicationHost.xdt XDT contenuto e la posizione nella radice del sito (d:\home\site) nella [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Potrebbe essere necessario riavviare l'App Web per le modifiche abbiano effetto.

Nell'esempio seguente viene applicationHost.xdt viene illustrato come aggiungere una nuova variabile di ambiente personalizzato a un'applicazione web che utilizza PHP 5.4.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


Un file di log con lo stato di trasformazione e dettagli è disponibile dalla radice FTP in LogFiles\Transform.

Per esempi aggiuntivi, vedere [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Nota**<br />
Elementi dall'elenco dei moduli in `system.webServer` non possono essere rimossi o riordinati, ma è possibile aggiunte all'elenco.


##<a id="extend"></a>Estendere un'applicazione web

###<a id="overview"></a>Panoramica delle estensioni di app web privato

Servizio di App supporta le estensioni app web come punto di estensibilità per le attività amministrative. In realtà, alcune funzionalità di piattaforma del servizio di App sono implementata come estensioni preinstallate. Mentre le estensioni di piattaforma preinstallato non possono essere modificate, è possibile creare e configurare le estensioni private per un'app web. Questa funzionalità si basa anche nelle dichiarazioni XDT. I passaggi principali per la creazione di un'estensione app web privato sono i seguenti:

1. Estensione app **contenuto**Web: creare applicazioni web supportate dal servizio di App
2. App estensione **dichiarazione**Web: creare un file ApplicationHost.xdt
3. Estensione app **distribuzione**Web: inserire contenuto nella cartella SiteExtensions in`root`

Collegamenti interni per l'applicazione web devono puntare a relativo al percorso applicazione specificato nel file ApplicationHost.xdt. Qualsiasi modifica al file ApplicationHost.xdt richiede un Cestino di app web.

**Nota**: ulteriori informazioni su questi elementi chiave sono disponibili in [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Un esempio dettagliato è inclusa per illustrare i passaggi per la creazione e abilitare un'estensione app web privato. Il codice sorgente per il gestore PHP dell'esempio che segue può essere scaricato dal [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Esempio di estensione Web app: PHP Manager

Gestione PHP è un'estensione app web che consente agli amministratori di app visualizzare e configurare le impostazioni della PHP utilizzando un'interfaccia web anziché dover modificare file ini PHP direttamente facilmente web. File di configurazione comuni per PHP includono file PHP memorizzato nella cartella programmi e. user.ini file che si trova nella cartella principale dell'applicazione web. Poiché il file PHP non modificabile direttamente sulla piattaforma App servizio, l'estensione PHP Manager utilizza il. user.ini file da applicare le modifiche alle impostazioni.

####<a id="PHPwebapp"></a>L'applicazione web PHP Manager

Di seguito è la home page della distribuzione PHP Manager:

![TransformSitePHPUI][TransformSitePHPUI]

Come si può notare, un'estensione app web è analoga a un'applicazione web regolari, ma con un altro file ApplicationHost.xdt inseriti nella cartella radice di web app (ulteriori dettagli relativi al file ApplicationHost.xdt sono disponibili nella sezione seguente di questo articolo).

L'estensione PHP Manager è stato creato utilizzando il modello di applicazione Web Visual Studio ASP.NET MVC 4. Visualizzazione persone seguite da Esplora soluzioni viene illustrata la struttura dell'estensione PHP Manager.

![TransformSiteSolEx][TransformSiteSolEx]

La logica solo speciale necessaria per i/o file consiste nell'indicare la posizione nella directory wwwroot dell'app web. Come illustrato nell'esempio seguente, l'ambiente variabile "HOME" indica percorso radice dell'applicazione web e aggiungendo "site\wwwroot" è possibile creare il percorso wwwroot:

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Dopo aver ottenuto il percorso della directory, è possibile utilizzare operazioni i/o file standard per leggere e scrivere i file.

Un punto di prestare attenzione con estensioni app web di quanto concerne la gestione dei collegamenti interni.  Se si dispone di tutti i collegamenti nei file HTML che forniscono percorsi assoluti ai collegamenti interni in un'applicazione web, è necessario che i collegamenti sono contrassegnati con il nome di estensione come il radice. Questa operazione è necessaria in quanto la cartella principale per l'estensione è "/`[your-extension-name]`/" non è solo "/", pertanto qualsiasi interni i collegamenti, devono essere aggiornati di conseguenza. Si supponga ad esempio che il codice contiene un collegamento per le operazioni seguenti:

`"<a href="/Home/Settings">PHP Settings</a>"`

Quando il collegamento fa parte di un'estensione app web, il collegamento deve essere nel formato seguente:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Per risolvere questo requisito tramite utilizzando solo i percorsi relativi all'interno dell'applicazione web o in caso di applicazioni ASP.NET, il `@Html.ActionLink` metodo che consente di creare collegamenti appropriati automaticamente.

####<a id="XDT"></a>Il file applicationHost.xdt

Il codice per l'estensione app web viene inserito in %HOME%\SiteExtensions\[l'estensione]. È necessario chiamare questo radice estensione.  

Per registrare l'estensione di app web con il file applicationHost, è necessario inserire un file denominato ApplicationHost.xdt nella radice estensione. Il contenuto del file ApplicationHost.xdt sarà simile alla seguente:

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

Il nome che selezionare il nome di estensione deve avere lo stesso nome della cartella radice di estensione.

Questa operazione con l'effetto dell'aggiunta di un nuovo percorso di applicazione per la `system.applicationHost` elenco dei siti nel sito servizi. Il sito di servizi è il punto finale di amministrazione sito con le credenziali di accesso specifico. Che contiene l'URL `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Distribuzione di un'estensione Web app

Per installare l'estensione app web, è possibile utilizzare FTP per copiare tutti i file dell'applicazione web per la `\SiteExtensions\[your-extension-name]` cartella dell'applicazione web in cui si desidera installare l'estensione.  Assicurarsi di copiare il file ApplicationHost.xdt in questa posizione anche. Riavviare l'applicazione web per abilitare l'estensione.

Dovrebbe essere possibile visualizzare l'estensione app web in:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Si noti che l'URL è simile a quello l'URL per l'app web, ad eccezione del fatto che utilizza HTTPS e contiene ".scm".

È possibile disattivare tutte le estensioni private (non preinstallate) per un'app web durante lo sviluppo e indagini aggiungendo le impostazioni di un app insieme al tasto `WEBSITE_PRIVATE_EXTENSIONS` e un valore di `0`.

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
