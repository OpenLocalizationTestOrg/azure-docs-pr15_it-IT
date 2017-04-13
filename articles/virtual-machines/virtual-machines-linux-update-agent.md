<properties
    pageTitle="Aggiornare l'agente Linux Azure da GitHub | Microsoft Azure"
    description="Informazioni su come aggiornamento Azure Linux agente per la VM Linux in Azure alla versione lateset da Github"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Come aggiornare l'agente di Linux Azure in una macchina virtuale per la versione più recente da GitHub

Per aggiornare l' [Agente Linux Azure](https://github.com/Azure/WALinuxAgent) su una VM Linux in Azure, è necessario avere già:

1. Una VM Linux in esecuzione in Azure.
2. Una connessione a tale VM Linux utilizzando SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Se questa operazione verrà eseguita da un computer Windows, è possibile utilizzare PuTTY a SSH al computer Linux. Per ulteriori informazioni, vedere [come accedere a macchina virtuale in esecuzione Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Approvato Azure Linux distros stato inserito il pacchetto dell'agente Linux Azure nel loro archivi così controllare e installare la versione più recente da tale archivio Distro prima di tutto se possibile.  

Per Ubuntu, digitare:

    #sudo apt-get install walinuxagent

E CentOS, digitare:

    #sudo yum install waagent


Per Oracle Linux, assicurarsi che la `Addons` repository è abilitato. Scegliere di modificare il file `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e modificare la riga `enabled=0` a `enabled=1` in **[ol6_addons]** o **[ol7_addons]** in questo file.

Per installare la versione più recente dell'agente di Linux Azure, digitare:


    #sudo yum install WALinuxAgent

Se non si trova l'archivio di componente aggiuntivo è possibile aggiungere semplicemente le righe alla fine del file .repo secondo la versione Linux Oracle:

Per macchine virtuali Oracle Linux 6:

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Per macchine virtuali Oracle Linux 7:

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Digitare:

    #sudo yum update WALinuxAgent

In genere queste informazioni sono necessarie, ma se per qualche motivo è necessario installare da https://github.com direttamente, utilizzare la procedura seguente.


## <a name="install-wget"></a>Installare wget

Accesso per la macchina virtuale utilizzando SSH.

Installare wget (esistono alcune distros che non installarlo per impostazione predefinita, ad esempio Red Hat, CentOS e Oracle Linux versioni 6.4 e 6.5) digitando `#sudo yum install wget` nella riga di comando.


## <a name="download-the-latest-version"></a>Scaricare la versione più recente

Aprire [la versione di Azure Linux agente in GitHub](https://github.com/Azure/WALinuxAgent/releases) in una pagina web e individuare il numero di versione più recente. (È possibile individuare la versione corrente digitando `#waagent --version`.)

### <a name="for-version-20x-type"></a>Per la versione 2.0, tipo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La riga seguente usa versione 2.0.14 ad esempio:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Per la versione 2.1.x o versioni successive, digitare:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La riga seguente usa versione 2.1.0 ad esempio:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Installare l'agente Linux Azure

### <a name="for-version-20x-use"></a>Per la versione 2.0, usare:

 Apportare waagent eseguibile:

    #chmod +x waagent

 Copiare il file eseguibile nuovo in /usr/sbin /.

  Per la maggior parte delle Linux, usare:

    #sudo cp waagent /usr/sbin

  Per CoreOS, usare:

    #sudo cp waagent /usr/share/oem/bin/

  Se si tratta di una nuova installazione dell'agente di Linux Azure, eseguire:
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Per la versione 2.1.x, usare:

Potrebbe essere necessario installare il pacchetto `setuptools` prima di tutto, vedere [di seguito](https://pypi.python.org/pypi/setuptools). Eseguire quindi:

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

Per la maggior parte delle linux Distros:

    #sudo service waagent restart

Per Ubuntu, usare:

    #sudo service walinuxagent restart

Per CoreOS, usare:

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Verificare la versione dell'agente Linux Azure

    #waagent -version

Per CoreOS, il comando riportato sopra potrebbe non funzionare.

Si noterà che la versione dell'agente di Azure Linux è stata aggiornata alla nuova versione.

Per ulteriori informazioni sulla agente Linux di Azure, vedere [Il file Leggimi agente Linux Azure](https://github.com/Azure/WALinuxAgent).
