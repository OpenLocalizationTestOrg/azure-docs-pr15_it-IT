<properties
    pageTitle="Configurare MySQL in un VM Linux | Microsoft Azure "
    description="Informazioni su come installare stack MySQL in un computer virtuale Linux (Ubuntu o Red Hat famiglia OS) in Azure"
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
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>Come installare MySQL in Azure


In questo articolo si imparerà a installare e configurare MySQL in un computer virtuale Azure che eseguono Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Installare MySQL sul computer virtuale

> [AZURE.NOTE] È necessario avere già una macchina virtuale di Microsoft Azure che eseguono Linux per completare questa esercitazione. Vedere l' [esercitazione macchine Virtuali Linux Azure](virtual-machines-linux-quick-create-cli.md) per creare e configurare un VM Linux con `mysqlnode` come nome macchine Virtuali e `azureuser` come utente prima di continuare.

In questo caso, utilizzare 3306 porta come porta MySQL.  

Connettersi a Linux macchine Virtuali creati tramite putty. Se si tratta al primo utilizzo di macchine Virtuali Linux Azure, vedere come utilizzare putty connettersi a un VM Linux [qui](virtual-machines-linux-mac-create-ssh-keys.md).

Pacchetto di archivio verrà usata per installare MySQL5.6 ad esempio in questo articolo. Sono in effetti, MySQL5.6 disponibili ulteriori analisi utilizzo software prestazioni rispetto alla MySQL5.5.  Altre informazioni [di seguito](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Come installare MySQL5.6 in Ubuntu
Verrà usata Linux VM con Ubuntu da Azure qui.

- Passaggio 1: Installare MySQL Server 5.6 passa a `root` utente:

            #[azureuser@mysqlnode:~]sudo su -

    Installare server mysql 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Durante l'installazione, si vedrà un poping finestra di dialogo fino a chiedere di impostare password radice MySQL riportata di seguito ed è necessario impostare la password di seguito.

    ![immagine](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Immettere la password per confermarla.

    ![immagine](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Passaggio 2: Accesso MySQL Server

    Al termine dell'installazione di server MySQL, servizio MySQL verrà avviato automaticamente. È possibile accedere MySQL Server con `root` utente.
    Usare il seguente comando per la password di accesso e l'input.

             #[root@mysqlnode ~]# mysql -uroot -p

- Passaggio 3: Gestire il servizio MySQL in esecuzione

    (a) ottenere lo stato del servizio MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) avviare il servizio MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) arrestare il servizio di MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) riavviare il servizio MySQL

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Come installare MySQL in rosso Hat famiglia di sistemi come CentOS, Linux Oracle
Verrà usata Linux VM con CentOS o Linux Oracle qui.

- Passaggio 1: Aggiungere repository dei MySQL Yum passa a `root` utente:

            #[azureuser@mysqlnode:~]sudo su -

    Scaricare e installare il pacchetto di rilascio MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Passaggio 2: Modificare sotto il file da abilitare l'archivio MySQL per il download del pacchetto MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Aggiorna ogni valore di questo file qui di seguito:

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Passaggio 3: Installare MySQL MySQL archivio installare MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    Verranno installati pacchetto RPM MySQL e tutti i pacchetti correlati.

- Passaggio 4: Gestire il servizio MySQL in esecuzione

    (a) verificare lo stato dei servizi del server MySQL:

           #[root@mysqlnode ~]#service mysqld status

    (b) controllare se il server di porta di MySQL predefinito è in esecuzione:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) avviare il server MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) arrestare il server MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL set per avviare quando l'avvio del sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>Come installare MySQL in SUSE Linux
Verrà usata Linux VM con OpenSUSE qui.

- Passaggio 1: Scaricare e installare MySQL Server

    Passare a `root` utente tramite sotto comando:  

           #sudo su -

    Scaricare e installare MySQL pacchetto:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Passaggio 2: Gestire il servizio MySQL in esecuzione

    (a) controllare lo stato del server MySQL:

           #[root@mysqlnode ~]# rcmysql status

    (b) controllo se la porta predefinita del server MySQL:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) avviare il server MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) arrestare il server MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL set per avviare quando l'avvio del sistema:

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Passaggio successivo
Trovare ulteriori informazioni relative alle MySQL [qui](https://www.mysql.com/)e l'uso.
