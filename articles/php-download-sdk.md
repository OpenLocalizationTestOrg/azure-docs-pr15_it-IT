<properties
    pageTitle="Scaricare il SDK Azure per PHP"
    description="Informazioni su come scaricare e installare Azure SDK per PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Scaricare il SDK Azure per PHP

## <a name="overview"></a>Panoramica

Azure SDK per PHP include componenti che consentono di sviluppare, distribuire e gestire applicazioni PHP per Azure. In particolare, Azure SDK per PHP include le operazioni seguenti:

* **Raccolte di client di PHP per Azure**. Queste librerie offrono un'interfaccia per l'accesso alle caratteristiche di Azure, ad esempio servizi di gestione dati e servizi cloud.  
* **L'interfaccia della riga di comando Azure per Mac, Linux e Windows (Azure CLI)**. Si tratta di un insieme di comandi per la distribuzione e gestione dei servizi Azure, ad esempio siti Web di Azure e macchine virtuali di Azure. Il lavoro Azure CLI su qualsiasi piattaforma, inclusi Mac, Linux e Windows.
* **Azure PowerShell (solo Windows)**. Si tratta di una serie di cmdlet di PowerShell per la distribuzione e gestione dei servizi di Windows Azure, ad esempio servizi Cloud e macchine virtuali.
* **Emulatori Azure (solo Windows)**. Emulatori di elaborazione e di archiviazione sono emulatori locali dei servizi cloud e dei servizi di gestione dei dati che consentono di verificare un'applicazione in locale. Emulatori Azure eseguire solo in Windows.

Nelle sezioni seguenti descrivono come scaricare e installare componenti descritti in precedenza.

Le istruzioni fornite in questo argomento si suppone che [PHP] [ install-php] installato.

> [AZURE.NOTE] È necessario disporre PHP 5,5 o versioni successive di utilizzare le librerie di client PHP per Azure.

##<a name="php-client-libraries-for-azure"></a>Raccolte di client PHP per Azure

Librerie Client PHP per Azure offrono un'interfaccia per l'accesso alle caratteristiche di Azure, ad esempio servizi di gestione dati e servizi da un sistema operativo cloud. Queste librerie possono essere installate tramite il compositore.

Per informazioni su come usare le raccolte di Client PHP per Azure, vedere [come utilizzare il servizio Blob][blob-service], [come utilizzare il servizio di tabella] [ table-service] e [su come utilizzare il servizio di coda][queue-service].

###<a name="install-via-composer"></a>Installare tramite compositore

1. [Installare fra][install-git].


    > [AZURE.NOTE] In Windows, sarà necessario anche aggiungere operazioni eseguibili variabile di ambiente PATH.

2. Creare un file denominato **composer.json** nella radice del progetto e aggiungervi il codice seguente:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Scaricare ** [composer.phar] [ composer-phar] ** nella radice del progetto.

4. Aprire un prompt dei comandi ed eseguire questa operazione nella radice del progetto

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>PowerShell Azure e Azure emulatori

PowerShell Azure è un insieme di cmdlet di PowerShell per la distribuzione e gestione dei servizi di Windows Azure (ad esempio servizi Cloud e macchine virtuali). Emulatori Azure sono emulatori dei servizi cloud e dei servizi di gestione dei dati che consentono di verificare un'applicazione in locale. Questi componenti sono supportati solo Windows.

Il modo consigliato per installare PowerShell Azure ed emulatori Azure consiste nell'utilizzare [Installazione guidata piattaforma Web Microsoft][download-wpi]. Si noti che è anche possibile scegliere di installare altri componenti di sviluppo, ad esempio PHP, SQL Server, i Drivers di Microsoft SQL Server per PHP e WebMatrix.

Per informazioni su come usare PowerShell di Azure, vedere [come usare PowerShell Azure][powershell-tools].

##<a name="azure-cli"></a>CLI Azure

CLI Azure è un insieme di comandi per la distribuzione e gestione dei servizi Azure, ad esempio siti Web di Azure e macchine virtuali di Azure. Per informazioni sull'installazione CLI Azure, vedere [installare CLI Azure](xplat-cli-install.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
