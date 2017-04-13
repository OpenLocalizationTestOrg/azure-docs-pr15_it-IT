<properties
    pageTitle="Distribuire luce in un computer virtuale Linux | Microsoft Azure"
    description="Informazioni su come installare stack di luce in una VM Linux"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Distribuire luce Stack di Azure
In questo articolo verrà descritto come distribuire un server web Apache, MySQL e PHP (stack luce) in Azure. È necessario un Account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e [Azure CLI](../xplat-cli-install.md) è [collegato al proprio account Azure](../xplat-cli-connect.md).

Esistono due metodi per l'installazione di luce contenute in questo articolo:

## <a name="quick-command-summary"></a>Comando rapido riepilogo

1) Distribuire luce nella nuova macchina virtuale

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Distribuire luce in macchine Virtuali esistenti

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Distribuire luce nella nuova procedura dettagliata macchine Virtuali

È possibile iniziare creando un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) che conterrà la macchina virtuale:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Per creare la macchina virtuale stesso, è possibile utilizzare un modello di gestione risorse Azure già scritte trovato [qui nella GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Verrà visualizzata una risposta che richiede una serie di dati più:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

È stata creata una VM Linux con luce in cui è già installato. Se si desidera, è possibile verificare l'installazione del passaggio per difetto a [verificare luce correttamente installato].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Distribuire luce nella procedura dettagliata macchine Virtuali esistenti

Se occorre assistenza per la creazione di una VM Linux è possibile sede [qui per imparare a creare un VM Linux] (. / virtual-machines-linux-quick-create-cli.md). Successivamente, sarà necessario SSH in VM Linux. Se occorre assistenza per la creazione di una chiave SSH è possibile testa [qui per imparare a creare una chiave SSH in Linux/Mac] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Se è già disponibile una chiave SSH, accedere SSH e direttamente nelle VM Linux con `ssh username@uniqueDNS`.

Ora che sta lavorando all'interno del VM Linux, esamineremo installazione stack luce nelle distribuzioni basate su Debian. I comandi esatti potrebbero essere diversi per altri distros Linux.

#### <a name="installing-on-debianubuntu"></a>L'installazione su Debian/Ubuntu

Sarà necessario seguenti pacchetti installati: `apache2`, `mysql-server`, `php5`, e `php5-mysql`. È possibile installare questi direttamente cattura questi pacchetti o utilizzando Tasksel. Istruzioni per entrambe le opzioni sono elencate di seguito.
Prima di installare sarà necessario scaricare e aggiornare gli elenchi di pacchetto.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Singoli pacchetti
Utilizzo di apt get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Utilizzo Tasksel
In alternativa è possibile scaricare Tasksel, uno strumento Debian/Ubuntu che consente di installare più pacchetti correlati come "attività" coordinati nel sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Dopo l'esecuzione di una delle opzioni precedenti soddisfa verrà richiesto di installare questi pacchetti e un numero di altre dipendenze. Premere "y" e "invio' per continuare e seguire altre istruzioni per impostare una password amministrativa per MySQL. Verrà installato con le estensioni PHP necessari minime necessari per utilizzare PHP con MySQL. 

![][1]

Eseguire il comando seguente per visualizzare altre estensioni PHP sono disponibili come pacchetti:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Creare documenti info.php

Questo punto dovrebbe essere possibile verificare quale versione di Apache, MySQL e PHP avere dalla riga di comando digitando `apache2 -v`, `mysql -v`, o `php -v`.

Se si desidera verificare ulteriormente, è possibile creare una pagina di informazioni PHP veloce per visualizzare in un browser. Creare un nuovo file con aspetti editor di testo con questo comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

Nell'editor di testo aspetti GNU, aggiungere le righe seguenti:

    <?php
    phpinfo();
    ?>

Salvare e chiudere l'editor di testo.

Riavviare Apache con questo comando per tutte le nuove installazioni avrà effetto.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Verificare luce installato

A questo punto è possibile controllare la pagina relativa alle informazioni PHP che appena creato nel browser facendo clic su http://youruniqueDNS/info.php, dovrebbe essere simile alla seguente.

![][2]

È possibile verificare il funzionamento dell'installazione Apache visualizzando la pagina predefinita di Apache2 Ubuntu facendo clic su si http://youruniqueDNS/. Verrà visualizzato simile al seguente.

![][3]

È stata completata la configurazione solo uno stack di luce in macchine Virtuali il Azure!

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione Ubuntu Nell'Elenco luce:

- [https://help.Ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
