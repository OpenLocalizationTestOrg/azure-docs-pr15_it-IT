<properties 
    pageTitle="Configurazione Python con Azure App servizio Web App" 
    description="In questa esercitazione descrive le opzioni per la creazione e la configurazione di un server Web base applicazione Python compatibile Gateway interfaccia (WSGI) su Azure App servizio Web Apps." 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configurazione Python con Azure App servizio Web App

In questa esercitazione descrive le opzioni per la creazione e la configurazione di un'applicazione di Python conforme di base Web Server Gateway interfaccia (WSGI) su [Azure App servizio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Descrive le caratteristiche aggiuntive di distribuzione di operazioni, ad esempio ambiente virtuale e l'installazione di pacchetto con requirements.txt.


## <a name="bottle-django-or-flask"></a>Bottiglia, Django o pallone?

Azure Marketplace contiene modelli per le strutture bottiglia, Django e pallone. Se si sviluppano applicazioni web prima di Azure App servizio o non si ha familiarità con fra, è consigliabile eseguire una di queste esercitazioni che includono istruzioni dettagliate per la creazione di un'applicazione dalla raccolta utilizzando la distribuzione fra dal Windows o Mac:

- [Creazione di applicazioni web con bottiglia](web-sites-python-create-deploy-bottle-app.md)
- [Creazione di applicazioni web con Django](web-sites-python-create-deploy-django-app.md)
- [Creazione di applicazioni web con pallone](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Creazione di app Web nel portale di Azure

In questa esercitazione si presuppone un abbonamento esistente Azure e accesso al portale di Azure.

Se non si dispone di un'app web esistente, è possibile creare uno dal [Portale di Azure](https://portal.azure.com).  Fare clic sul pulsante nuovo nell'angolo superiore sinistro e quindi fare clic su **Web + Mobile** > **Web app**.

## <a name="git-publishing"></a>Pubblicazione fra

Configurare la pubblicazione dei fra per un'app web appena creato seguendo le istruzioni disponibili in fase di [Distribuzione locale di fra al servizio App Azure](app-service-deploy-local-git.md). In questa esercitazione utilizza fra per creare, gestire e pubblicare l'app web Python al servizio App Azure.

Dopo la pubblicazione fra è impostata, verrà creato e associato a un'app web di un repository fra. URL dell'archivio verrà visualizzata e può essere utilizzato da questo momento per inserire i dati dall'ambiente di sviluppo locale nel cloud. Per pubblicare applicazioni tramite fra, assicurarsi che sia installato anche un client fra e usare le istruzioni fornite per inserire il contenuto di app web al servizio App Azure.


## <a name="application-overview"></a>Panoramica dell'applicazione

Nelle sezioni successive vengono creati i file seguenti. Devono essere inseriti nella radice dell'archivio fra.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Gestore WSGI

WSGI è uno standard di Python descritto da [PEP 3333](http://www.python.org/dev/peps/pep-3333/) definizione di un'interfaccia tra il server web e Python. Offre un'interfaccia standard per la scrittura di varie applicazioni web e Framework utilizzando Python. Framework di web Python popolari utilizzare oggi WSGI. Supporto per tale Framework; Azure consente di App del servizio Web Apps Inoltre, per gli utenti avanzati possono anche creare le proprie come gestore personalizzato segue le linee guida specifica WSGI.

Ecco un esempio di un `app.py` che definisce un gestore personalizzato:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

È possibile eseguire questa applicazione localmente con `python app.py`, quindi passare alla `http://localhost:5555` nel web browser.


## <a name="virtual-environment"></a>Ambiente virtuale

Anche se l'applicazione di esempio precedente non richiede eventuali pacchetti esterni, è probabile che l'applicazione richiederà alcuni.

Per informazioni su come gestire le dipendenze dei pacchetti esterni, distribuzione di Azure fra supporta la creazione di ambienti virtuali.

Quando Azure rileva un requirements.txt nella radice del repository, viene creato automaticamente un ambiente virtuale denominato `env`. In tal caso solo nella distribuzione prima o durante la distribuzione dopo aver selezionato Python runtime ha modificato.

È probabilmente necessario creare un ambiente virtuale localmente per lo sviluppo, ma non includere nel repository fra.


## <a name="package-management"></a>Gestione dei pacchetti

Pacchetti elencati in requirements.txt verranno installati automaticamente nell'ambiente virtuale utilizzando pip. Ciò avviene in ogni distribuzione, ma pip verranno ignorate installazione se è già installato un pacchetto.

Esempio `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Versione Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Esempio `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Config

È necessario creare un file config per specificare come il server di gestire le richieste.

Si noti che se si dispone di un file web.x.y.config del repository, in cui y corrisponde il runtime Python selezionato, quindi Azure vengono copiate automaticamente il file appropriato come config.

Negli esempi seguenti config si basano su uno script proxy ambiente virtuale, descritto nella sezione successiva.  Funzionano con il gestore WSGI utilizzato nell'esempio `app.py` sopra.

Esempio `web.config` per Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Esempio `web.config` per Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Statici devono essere gestiti file dal server web direttamente, senza passano attraverso il codice Python, per migliorare le prestazioni.

Negli esempi precedenti, il percorso del file nel disco deve corrispondere la posizione nell'URL. Ciò significa che una richiesta di `http://pythonapp.azurewebsites.net/static/site.css` serve il file sul disco `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER`è in cui specificare il gestore WSGI. Negli esempi precedenti ha `app.wsgi_app` poiché il gestore è una funzione denominata `wsgi_app` in `app.py` nella cartella radice.

`PYTHONPATH`può essere personalizzato, ma se si installano tutte le dipendenze nell'ambiente virtuale specificando loro di requirements.txt, non è necessario modificarla.


## <a name="virtual-environment-proxy"></a>Ambiente virtuale Proxy

Il seguente script viene utilizzato per recuperare il gestore WSGI, attivare l'ambiente e registrare gli errori virtuali. È progettato per essere generico e usati senza alcuna modifica.

Contenuto di `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Personalizzare la distribuzione fra

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>Risoluzione dei problemi - installazione del pacchetto

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Risoluzione dei problemi - ambiente virtuale

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)





 
