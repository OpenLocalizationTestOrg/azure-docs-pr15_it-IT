<properties
    pageTitle="Utilizzare l'estensione CustomScript in una macchina virtuale Linux | Microsoft Azure"
    description="Informazioni su come utilizzare l'estensione CustomScript per distribuire applicazioni in macchine virtuali Linux in Azure creata utilizzando il modello di distribuzione classica."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Distribuire un'app di luce utilizzando l'estensione CustomScript Azure per Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Estensione Microsoft Azure CustomScript per Linux offre un modo per personalizzare il macchine () tramite l'esecuzione di codice non autorizzato scritto in qualsiasi linguaggio di script supportato da macchine Virtuali (ad esempio Python e Bash). In questo modo molto flessibile per automatizzare la distribuzione di applicazioni su più computer.

È possibile distribuire l'estensione CustomScript usando il portale classico Azure, Windows PowerShell o nell'interfaccia della riga di Azure (Azure CLI).

In questo articolo che si userà CLI Azure per distribuire un'applicazione di luce semplice in una VM Ubuntu creata utilizzando il modello di distribuzione classica.

## <a name="prerequisites"></a>Prerequisiti

In questo esempio, è necessario creare due macchine virtuali di Azure con Ubuntu 14.04 o versione successiva. Macchine virtuali sono denominate *macchine virtuali di script* e *luce SV*. Quando si creano macchine virtuali, utilizzare nomi univoci. Viene utilizzato uno per eseguire i comandi CLI e viene utilizzato uno per distribuire l'app luce.

È necessario un account di archiviazione Azure e una chiave per accedervi (è possibile ottenere questo dal portale di classica Azure).

Se occorre assistenza per la creazione di macchine virtuali Linux su Azure consultare [Crea macchina virtuale in esecuzione Linux](virtual-machines-linux-classic-createportal.md).

I comandi di installazione presuppongono Ubuntu, ma è possibile adattare l'installazione per qualsiasi distro Linux supportato.

La macchina virtuale macchine virtuali di script deve disporre di Azure CLI installato con una connessione attiva a Azure. Per assistenza con questo riferimento per [installare e configurare la riga di comando di Azure](../xplat-cli-install.md).

## <a name="upload-a-script"></a>Caricare uno script

Si userà l'estensione CustomScript per eseguire uno script su una macchina virtuale remota per installare stack di luce e creare una pagina PHP. Per accedere allo script ovunque si sarà caricarlo come blob Azure.

### <a name="script-overview"></a>Panoramica di script

Lo script di esempio installa uno stack di luce in Ubuntu (inclusa la configurazione di un'installazione invisibile di MySQL), scrivere un semplice file PHP e avvia Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Caricare script

Salvare lo script come file di testo, ad esempio *install_lamp.sh*e quindi caricarlo allo spazio di archiviazione Azure. È possibile eseguire questa operazione facilmente con Azure CLI. Nell'esempio seguente consente di caricare il file in un contenitore di spazio di archiviazione denominato "script". Se non è presente il contenitore è necessario creare prima di tutto.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Anche creare un file JSON che descrive come scaricare lo script dallo spazio di archiviazione Azure. Salva come *public_config.json* (sostituendo "la risorsa mystorage" con il nome del proprio account di archiviazione):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Distribuire l'estensione

A questo punto è possibile utilizzare il comando successivo per distribuire l'estensione CustomScript Linux macchina virtuale remota utilizzando CLI Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

Il comando precedente Scarica ed esegue lo script *install_lamp.sh* in macchine Virtuali chiamato *macchine virtuali di luce*.

Poiché l'app include un server web, è necessario ricordare di aprire una porta in attesa HTTP nella macchina virtuale remota con il comando successivo.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi

È possibile controllare come anche lo script personalizzato viene eseguito esaminando il file di log nella macchina virtuale remoto. SSH *macchine virtuali di luce* e tail il file di log con il comando successivo.

    cd /var/log/azure/customscript
    tail -f handler.log

Dopo avere eseguito l'estensione CustomScript, è possibile passare alla pagina PHP creata per informazioni. Pagina PHP, ad esempio in questo articolo è *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Risorse aggiuntive

È possibile utilizzare la stessa procedura di base per distribuire applicazioni più complesse. In questo esempio lo script di installazione è stato salvato come blob pubblico in archiviazione Azure. L'opzione più sicura, è possibile archiviare lo script di installazione come blob protetto con una [Firma di accesso sicuro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SA).

Di seguito sono elencate risorse aggiuntive per CLI Azure, Linux e l'estensione CustomScript.

[Automatizzare le attività di personalizzazione di macchine Virtuali Linux con estensione CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Estensioni Azure Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux e Open Source Computing su Azure](virtual-machines-linux-opensource-links.md)
