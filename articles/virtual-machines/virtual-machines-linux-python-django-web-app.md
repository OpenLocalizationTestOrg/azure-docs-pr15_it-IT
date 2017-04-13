<properties 
    pageTitle="Python web app con Django su Linux | Microsoft Azure" 
    description="Informazioni su come pubblicare un'applicazione web basata su Django in Azure utilizzando una macchina virtuale Linux." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Applicazione web Hello World Django su una VM Linux

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac o Linux](virtual-machines-linux-python-django-web-app.md)

<br>

In questa esercitazione viene illustrato come ospitare un sito Web basate su Django in Microsoft Azure utilizzando una macchina virtuale Linux. In questa esercitazione si presuppone che nessun già esperienza nell'uso di Azure. Al termine dell'esecuzione di questa Guida, si avrà un'applicazione basata su Django alto e in esecuzione nel cloud.

Si apprenderà come:

* Impostare una macchina virtuale Azure all'host Django. Mentre in questa esercitazione viene illustrato come eseguire questa operazione in **Linux**, lo stesso potrebbe essere eseguito anche con una macchina virtuale Server Windows ospitato in Azure. 
* Creare una nuova applicazione Django da Linux.

Seguendo questa esercitazione si creerà una semplice web applicazione. L'applicazione è ospitata in un computer virtuale Azure.

Schermata dell'applicazione completata è inferiore a:

![Una finestra del browser world pagina hello su Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Creazione e configurazione di una macchina virtuale Azure all'host Django

1. Seguire le istruzioni indicato [di seguito](virtual-machines-linux-quick-create-portal.md) per creare una macchina virtuale Azure della distribuzione *Ubuntu Server 14.04 risultati* .  Se si preferisce, è possibile scegliere autenticazione tramite password invece di chiave pubblica SSH.

1. Modificare il gruppo di sicurezza di rete per consentire il traffico http in ingresso alla porta 80 seguendo le istruzioni riportate [di seguito](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Per impostazione predefinita, la nuova macchina virtuale non è disponibile un nome di dominio completo (FQDN).  È possibile crearne uno seguendo le istruzioni riportate [di seguito](virtual-machines-linux-portal-create-fqdn.md).  Questo passaggio è facoltativo per completare questa esercitazione.

## <a id="setup"> </a>La configurazione dell'ambiente di sviluppo

**Nota:** Se è necessario installare Python o si desidera utilizzare le raccolte di Client, vedere la [Guida all'installazione di Python](../python-how-to-install.md).

Il supporto virtuale Linux Ubuntu include già 2.7 Python preinstallato, ma non è necessario Apache o django installato.  Seguire questa procedura per connettere la macchina virtuale e installare Apache e django.

1.  Avviare una nuova finestra **terminale** .
    
1.  Immettere il comando seguente per connettere la macchina virtuale Azure.  Se è stato creato un nome di dominio completo, è possibile connettersi tramite l'indirizzo IP pubblico visualizzato nella macchina virtuale riepilogo nel portale di classica Azure.

        $ ssh yourusername@yourVmUrl

1.  Immettere i comandi seguenti per installare django:

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Immettere il comando seguente per installare Apache con mod wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Creare una nuova applicazione Django

1.  Aprire la finestra di **terminale** usato nella sezione precedente per ssh in di una macchina virtuale.
    
1.  Immettere i comandi seguenti per creare un nuovo progetto Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    Lo script **django admin.py** genera una struttura di base per i siti Web basate su Django:
    -   **HelloWorld/Manage.py** consente di avviare l'hosting e arrestare ospitare il sito Web basate su Django
    -   **HelloWorld/HelloWorld/Settings.py** contiene le impostazioni di Django per l'applicazione.
    -   **HelloWorld/HelloWorld/URLs.py** contiene il codice di mapping tra ogni url e la relativa visualizzazione.

1.  Creare un nuovo file denominato **views.py** nella directory **/var/www/helloworld/helloworld** . Che conterrà la visualizzazione che esegue il rendering della pagina "hello world". Avviare l'editor e immettere le operazioni seguenti:
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Sostituire il contenuto del file **urls.py** con le operazioni seguenti:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>La configurazione di Apache

1.  Creare un file di configurazione dall'host virtuale di Apache **/etc/apache2/sites-available/helloworld.conf**. Impostare il contenuto al seguente e sostituire *yourVmName* con il nome effettivo del computer in uso (ad esempio *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Abilitare il sito con il comando seguente:

        $ sudo a2ensite helloworld

1.  Riavviare Apache con il comando seguente:

        $ sudo service apache2 reload

1.  Infine, caricare la pagina web nel browser:

    ![Una finestra del browser world pagina hello su Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Arrestare la macchina virtuale Azure

Al termine di questa esercitazione, arresto e/o Rimuovi appena creata Azure macchina virtuale per liberare risorse per altre esercitazioni ed evitare i costi di utilizzo Azure.
