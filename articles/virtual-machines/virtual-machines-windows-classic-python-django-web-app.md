<properties
    pageTitle="Python web app con Django | Microsoft Azure"
    description="In questa esercitazione si come pubblicare un sito Web Django basate su Azure utilizzando una macchina virtuale Data Center di Windows Server 2012 R2 utilizzando il modello di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Applicazione web Hello World Django su una macchina virtuale Server di Windows

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac o Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

In questa esercitazione viene illustrato come ospitare un sito Web basate su Django in Microsoft Azure utilizzando una macchina virtuale di Windows Server. In questa esercitazione si presuppone che nessun già esperienza nell'uso di Azure. Al termine di questa esercitazione, è un'applicazione basata su Django alto e in esecuzione nel cloud.

Si apprenderà come:

* Configurare una macchina virtuale Azure all'host Django. Mentre in questa esercitazione viene illustrato come eseguire questa operazione in Windows Server, lo stesso potrebbe essere eseguito anche con Linux macchine Virtuali ospitato in Azure.
* Creare una nuova applicazione Django da Windows.

Seguendo questa esercitazione si creerà una semplice web applicazione. L'applicazione è ospitata in un computer virtuale Azure.

Schermata dell'applicazione completata viene visualizzato accanto.

![Una finestra del browser world pagina hello su Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Creazione e configurazione di una macchina virtuale Azure all'host Django

1. Seguire le istruzioni indicato [di seguito](virtual-machines-windows-classic-tutorial.md) per creare una macchina virtuale Azure della distribuzione Data Center di Windows Server 2012 R2.

1. Indicare Azure per indirizzare il traffico 80 porta dal web alla porta 80 macchina virtuale:
 - Passare alla macchina virtuale appena creato nel portale di classica Azure e fare clic sulla scheda **i punti finali** .
 - Fare clic sul pulsante **Aggiungi** nella parte inferiore dello schermo.
    ![aggiungere endpoint](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Aprire l'il protocollo **TCP's** **pubblico porta 80** come **privato porta 80**.
![][port80]
1. Dalla scheda **DASHBOARD** , fare clic su **CONNETTI** per utilizzare **Desktop remoto** per accedere in remoto la macchina virtuale Azure appena creata.  

**Nota importante:** Tutte le istruzioni seguenti presuppongono che connesso al computer virtuale correttamente e rilascio comandi sono piuttosto che nel computer locale.

## <a id="setup"> </a>WFastCGI Python, Django, l'installazione

**Nota:** Per scaricare con Internet Explorer potrebbe essere necessario configurare le impostazioni di Internet Explorer ESC (Start/amministrativo strumenti/Server Manager/locale Server, quindi fare clic su **Sicurezza avanzata di Internet Explorer**, impostare su No).

1. Installare l'ultima 2.7 Python o 3.4 da [python.org][].
1. Installare i pacchetti wfastcgi e django utilizzando pip.

    Per Python 2.7, usare il comando seguente.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Per Python 3.4, usare il comando seguente.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Installazione di IIS con FastCGI

1. Installare IIS con il supporto di FastCGI.  Questa operazione potrebbe richiedere alcuni minuti per l'esecuzione.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Creare una nuova applicazione Django

1.  Da *C:\inetpub\wwwroot*, immettere il comando seguente per creare un nuovo progetto Django:

    Per Python 2.7, usare il comando seguente.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Per Python 3.4, usare il comando seguente.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Il risultato del comando nuovo AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  Il comando **django amministratore** genera una struttura di base per i siti Web basate su Django:

  -   **helloworld\manage.py** consente di avviare l'hosting e arrestare ospitare il sito Web basate su Django
  -   **helloworld\helloworld\settings.py** contiene le impostazioni di Django per l'applicazione.
  -   **helloworld\helloworld\urls.py** contiene il codice di mapping tra ogni url e la relativa visualizzazione.

1.  Creare un nuovo file denominato **views.py** nella directory *C:\inetpub\wwwroot\helloworld\helloworld* . Che conterrà la visualizzazione che esegue il rendering della pagina "hello world". Avviare l'editor e immettere le operazioni seguenti:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Sostituire il contenuto del file urls.py con le operazioni seguenti.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Configurazione di IIS

1. Sbloccare la sezione gestori di applicationHost globale.  In questo modo sarà l'utilizzo del gestore python nel file Web. config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Abilitare WFastCGI.  Verrà aggiunta un'applicazione di applicationHost globale che fa riferimento all'interprete Python eseguibile e lo script wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Creare un file config in *C:\inetpub\wwwroot\helloworld*.  Il valore di `scriptProcessor` attributo deve corrispondere l'output del passaggio precedente.  Vedere la pagina per [wfastcgi][] su pypi per altre impostazioni wfastcgi attivato.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Aggiornare il percorso di IIS sito Web predefinito in modo che punti alla cartella del progetto django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Infine, caricare la pagina web nel browser.

![Una finestra del browser world pagina hello su Azure][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Arrestare la macchina virtuale Azure

Al termine di questa esercitazione, arrestare e/o rimuovere la macchina virtuale Azure appena creata per liberare risorse per altre esercitazioni ed evitare i costi di utilizzo Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
