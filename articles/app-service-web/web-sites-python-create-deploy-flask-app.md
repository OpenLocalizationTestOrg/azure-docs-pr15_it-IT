<properties 
    pageTitle="Creazione di applicazioni web con pallone in Azure" 
    description="Esercitazione in cui viene presentato all'esecuzione di un'app web Python in Azure." 
    services="app-service\web" 
    documentationCenter="python"
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/20/2016"
    ms.author="huvalo"/>


# <a name="creating-web-apps-with-flask-in-azure"></a>Creazione di applicazioni web con pallone in Azure

In questa esercitazione viene descritto come iniziare a esecuzione Python in [Azure App servizio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).  Web App fornisce limitato gratuita di hosting e distribuzione rapida ed è possibile utilizzare Python!  Man mano che cresce l'app, è possibile passare a pagamento hosting e può inoltre essere integrato con tutti gli altri servizi Azure.

È necessario creare un'applicazione utilizzando il framework web pallone (vedere versioni alternative di questa esercitazione per [Django](web-sites-python-create-deploy-django-app.md) e [bottiglia](web-sites-python-create-deploy-bottle-app.md)).  Si verrà creare il sito Web della raccolta di Azure, configurare la distribuzione fra e duplicare l'archivio locale.  Quindi si verrà eseguito l'applicazione in locale, apportare le modifiche, eseguire il commit e inviarli a Azure.  L'esercitazione viene illustrato come eseguire questa operazione da Windows o Mac o Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="prerequisites"></a>Prerequisiti

- Windows, Mac o Linux
- 2.7 Python o 3.4
- setuptools, pip, virtualenv (solo 2.7 Python)
- Operazioni
- [Python Tools per Visual Studio][] Si noti (PTVS) -: facoltativo

**Nota**: la pubblicazione TFS non è attualmente supportata per i progetti Python.

### <a name="windows"></a>Windows

Se non si dispone già 2.7 Python o 3,4 installati (32 bit), è consigliabile installare [Azure SDK per Python 2.7] o [Azure SDK per Python 3.4] mediante Installazione guidata piattaforma Web.  Consente di installare la versione a 32 bit di Python, setuptools, pip, virtualenv e così via (32 bit Python è ciò che è installato nel computer host Azure).  In alternativa, è possibile ottenere Python da [python.org].

Per operazioni, è consigliabile [Fra per Windows] o [GitHub per Windows].  Se si utilizza Visual Studio, è possibile utilizzare il supporto fra integrato.

È anche consigliabile installare [Python 2.2 Tools per Visual Studio].  Facoltativo, ma se si dispone di [Visual Studio], inclusa la gratuito 2013 di Visual Studio della Community o Visual Studio Express 2013 per Web, quindi verrà visualizzato un ottimo IDE Python.

### <a name="maclinux"></a>Mac o Linux

È necessario disporre di Python e fra già installato, ma accertarsi di avere 2.7 Python o 3.4.


## <a name="web-app-create-on-the-azure-portal"></a>Creare app Web nel portale di Azure

Il primo passaggio per la creazione dell'applicazione consiste nel creare l'applicazione web tramite il [Portale di Azure](https://portal.azure.com). 

1. Accedere al portale di Azure e fare clic sul pulsante **Nuovo** nell'angolo inferiore sinistro. 
2. Fare clic su **Web + Mobile**.
3. Nella casella di ricerca, digitare "python".
4. Nei risultati della ricerca, selezionare **pallone**, quindi fare clic su **Crea**.
5. Configurare la nuova app pallone, ad esempio la creazione di un nuovo piano di servizio di App e un nuovo gruppo di risorse per renderla. Quindi fare clic su **Crea**.
6. Configurare la pubblicazione dei fra per un'app web appena creato seguendo le istruzioni disponibili in fase di [Distribuzione locale di fra al servizio App Azure](app-service-deploy-local-git.md).


## <a name="application-overview"></a>Panoramica dell'applicazione

### <a name="git-repository-contents"></a>Contenuto dell'archivio fra

Ecco una panoramica dei file che sono disponibili nell'archivio di fra iniziale, è necessario duplicare nella sezione successiva.

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Origini principali per l'applicazione.  È costituito da 3 pagine (indice, sul contatto) con un layout master.  Script e in contenuto statico includono avvio, jquery, modernizr e rispondere.

    \runserver.py

Supporto di server di sviluppo locale. Consente di eseguire l'applicazione in locale.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

File di progetto per l'utilizzo con [Python Tools per Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy IIS per gli ambienti virtuali e PTVS debug supporto remoto.

    \requirements.txt

Pacchetti esterni necessari per l'applicazione. Script di distribuzione verrà pip installare i pacchetti elencati in questo file.
 
    \web.2.7.config
    \web.3.4.config

File di configurazione IIS.  Script di distribuzione verrà utilizzate web.x.y.config appropriato e copiare come config.

### <a name="optional-files---customizing-deployment"></a>File facoltativi - distribuzione personalizzazione

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>File facoltativi - Python runtime

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>File aggiuntivi sul server

Alcuni file esistono nel server ma non vengono aggiunti all'archivio fra.  Vengono creati tramite lo script di distribuzione.

    \web.config

File di configurazione IIS.  Creato da web.x.y.config in ogni distribuzione.

    \env\

Ambiente virtuale Python.  Se un ambiente virtuale compatibile non esiste già l'App, creato durante la distribuzione.  Pacchetti elencati in requirements.txt sono pip installate, ma pip verranno ignorate installazione se i pacchetti sono già installati.

Le 3 successivo sezioni viene descritto come procedere con lo sviluppo di app web in ambienti diversi 3:

- Windows con gli strumenti di Python per Visual Studio
- Windows con riga di comando
- Mac o Linux con riga di comando


## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Sviluppo di app Web - Windows - Python Tools per Visual Studio

### <a name="clone-the-repository"></a>Duplicare l'archivio

Prima di tutto duplicare archivio mediante l'URL specificato nel portale di Azure. Per ulteriori informazioni, vedere [Distribuzione fra locale al servizio App Azure](app-service-deploy-local-git.md).

Aprire il file di soluzione (sln) che è incluso nella radice del repository.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### <a name="create-virtual-environment"></a>Creare un ambiente virtuale

A questo punto è necessario creare un ambiente virtuale per lo sviluppo locale.  Fare clic su Seleziona **Python ambienti** **Aggiungere ambiente virtuale**.

- Verificare che sia il nome dell'ambiente `env`.

- Selezionare l'interprete di base.  Assicurarsi di usare la stessa versione di Python che sia selezionata per un'app web (in runtime.txt o e **l'Impostazioni applicazione** di un'app web nel portale di Azure).

- Verificare che sia selezionata l'opzione per scaricare e installare pacchetti.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Fare clic su **Crea**.  Verrà creare l'ambiente virtuale e installato dipendenze elencate in requirements.txt.

### <a name="run-using-development-server"></a>Eseguire tramite il server di sviluppo

Premere F5 per avviare il debug e il web browser verrà aperta automaticamente alla pagina in esecuzione in locale.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

È possibile impostare i punti di interruzione di origini, utilizzare le finestre Espressioni di controllo e così via.  Vedere gli [Strumenti di Python per la documentazione di Visual Studio] per ulteriori informazioni su varie caratteristiche.

### <a name="make-changes"></a>Apportare modifiche

A questo punto è possibile provare ad apportare modifiche a origini dell'applicazione e/o modelli.

Dopo avere verificato le modifiche, eseguire il commit repository fra:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### <a name="install-more-packages"></a>Installare più pacchetti

L'applicazione potrebbe avere dipendenze oltre Python e pallone.

È possibile installare pacchetti aggiuntivi utilizzando pip.  Per installare un pacchetto, il pulsante destro sull'ambiente virtuale e selezionare **Installa pacchetto Python**.

Ad esempio, per installare Azure SDK per Python, che consente di accedere per lo spazio di archiviazione Azure, bus di servizio e altri servizi di Azure, immettere `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Pulsante destro del mouse sull'ambiente virtuale e selezionare **Genera requirements.txt** aggiornare requirements.txt.

Quindi, eseguire il commit delle modifiche requirements.txt all'archivio fra.

### <a name="deploy-to-azure"></a>Distribuire Azure

Per avviare una distribuzione, fare clic su **Sincronizza** o **Push**.  Sincronizzazione indica un push e un pull.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

La distribuzione prima richiederà molto tempo, come verrà creato un ambiente virtuale, pacchetti di installazione e così via.

Non è visualizzato l'avanzamento della distribuzione in Visual Studio.  Se si desidera esaminare l'output, vedere la sezione sulla [risoluzione dei problemi - distribuzione](#troubleshooting-deployment).

Passare all'URL di Azure per visualizzare le modifiche.


## <a name="web-app-development---windows---command-line"></a>Riga di comando sviluppo - Windows - Web app

### <a name="clone-the-repository"></a>Duplicare l'archivio

Prima di tutto duplicare archivio mediante l'URL specificato nel portale di Azure e aggiungere repository Azure come un remoto. Per ulteriori informazioni, vedere [Distribuzione fra locale al servizio App Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Creare un ambiente virtuale

È necessario creare un nuovo ambiente virtuale in fase di sviluppo (non aggiungerlo al repository).  Gli ambienti virtuali in Python non sono rilocabile, in modo che ogni sviluppatore lavorando l'applicazione creerà le proprie in locale.

Assicurarsi di usare la stessa versione di Python che sia selezionata per un'app web (in runtime.txt o e **l'Impostazioni applicazione** di un'app web nel portale di Azure).

Per Python 2.7:

    c:\python27\python.exe -m virtualenv env

Per Python 3.4:

    c:\python34\python.exe -m venv env

Installare eventuali pacchetti esterni necessari per l'applicazione. È possibile utilizzare il file requirements.txt nella radice del repository per installare i pacchetti nell'ambiente virtuale:

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>Eseguire tramite il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il comando seguente:

    env\scripts\python runserver.py

Console di visualizzerà l'URL e ascolta porta server:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Aprire il web browser dell'URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### <a name="make-changes"></a>Apportare modifiche

A questo punto è possibile provare ad apportare modifiche a origini dell'applicazione e/o modelli.

Dopo avere verificato le modifiche, eseguire il commit repository fra:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installare più pacchetti

L'applicazione potrebbe avere dipendenze oltre Python e pallone.

È possibile installare pacchetti aggiuntivi utilizzando pip.  Ad esempio, per installare Azure SDK per Python, che consente di accedere per lo spazio di archiviazione Azure, bus di servizio e altri servizi di Azure, digitare:

    env\scripts\pip install azure

Assicurarsi di aggiornare requirements.txt:

    env\scripts\pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Distribuire Azure

Per avviare una distribuzione, rendere disponibili le modifiche in Azure:

    git push azure master

Verrà visualizzata l'output dello script di distribuzione, tra cui la creazione dell'ambiente virtuale, installazione del pacchetto, la creazione di config.

Passare all'URL di Azure per visualizzare le modifiche.


## <a name="web-app-development---maclinux---command-line"></a>Riga di comando sviluppo - Mac o Linux - Web app

### <a name="clone-the-repository"></a>Duplicare l'archivio

Prima di tutto duplicare archivio mediante l'URL specificato nel portale di Azure e aggiungere repository Azure come un remoto. Per ulteriori informazioni, vedere [Distribuzione fra locale al servizio App Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Creare un ambiente virtuale

È necessario creare un nuovo ambiente virtuale in fase di sviluppo (non aggiungerlo al repository).  Gli ambienti virtuali in Python non sono rilocabile, in modo che ogni sviluppatore lavorando l'applicazione creerà le proprie in locale.

Assicurarsi di usare la stessa versione di Python che sia selezionata per un'app web (in runtime.txt o e **l'Impostazioni applicazione** di un'app web nel portale di Azure).

Per Python 2.7:

    python -m virtualenv env

Per Python 3.4:

    python -m venv env
o pyvenv env

Installare eventuali pacchetti esterni necessari per l'applicazione. È possibile utilizzare il file requirements.txt nella radice del repository per installare i pacchetti nell'ambiente virtuale:

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>Eseguire tramite il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il comando seguente:

    env/bin/python runserver.py

Console di visualizzerà l'URL e ascolta porta server:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Aprire il web browser dell'URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### <a name="make-changes"></a>Apportare modifiche

A questo punto è possibile provare ad apportare modifiche a origini dell'applicazione e/o modelli.

Dopo avere verificato le modifiche, eseguire il commit repository fra:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installare più pacchetti

L'applicazione potrebbe avere dipendenze oltre Python e pallone.

È possibile installare pacchetti aggiuntivi utilizzando pip.  Ad esempio, per installare Azure SDK per Python, che consente di accedere per lo spazio di archiviazione Azure, bus di servizio e altri servizi di Azure, digitare:

    env/bin/pip install azure

Assicurarsi di aggiornare requirements.txt:

    env/bin/pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Distribuire Azure

Per avviare una distribuzione, rendere disponibili le modifiche in Azure:

    git push azure master

Verrà visualizzata l'output dello script di distribuzione, tra cui la creazione dell'ambiente virtuale, installazione del pacchetto, la creazione di config.

Passare all'URL di Azure per visualizzare le modifiche.


## <a name="troubleshooting---package-installation"></a>Risoluzione dei problemi - installazione del pacchetto

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Risoluzione dei problemi - ambiente virtuale

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## <a name="next-steps"></a>Passaggi successivi

Seguire questi collegamenti per ulteriori informazioni sui pallone e Python Tools per Visual Studio: 
 
- [Documentazione pallone]
- [Strumenti di Python per la documentazione di Visual Studio]

Per informazioni sull'utilizzo di archivio tabelle Azure e MongoDB:

- [Pallone e MongoDB su Azure con Python Tools per Visual Studio]
- [Pallone e archiviazione tabelle Azure in Azure con Python Tools per Visual Studio]

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di Python](/develop/python/).

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Pallone e MongoDB su Azure con Python Tools per Visual Studio]: https://github.com/microsoft/ptvs/wiki/Flask-and-MongoDB-on-Azure
[Pallone e archiviazione tabelle Azure in Azure con Python Tools per Visual Studio]: web-sites-python-ptvs-flask-table-storage.md

<!--External Link references-->
[Azure SDK per Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK per Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[Python.org]: http://www.python.org/
[Operazioni per Windows]: http://msysgit.github.io/
[GitHub per Windows]: https://windows.github.com/
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python 2.2 degli strumenti per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Strumenti di Python per la documentazione di Visual Studio]: http://aka.ms/ptvsdocs
[Documentazione pallone]: http://flask.pocoo.org/ 
 
