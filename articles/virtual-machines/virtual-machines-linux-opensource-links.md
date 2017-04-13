<properties
    pageTitle="Linux e Open Source Computing in Azure | Microsoft Azure"
    description="Elenca Linux e Apri origine Computing articoli su Azure, incluso l'uso di Linux base, alcuni concetti fondamentali sui è in esecuzione o il caricamento delle immagini Linux Azure e altro contenuto sulle tecnologie specifiche e le ottimizzazioni."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux e open source computing su Azure

Trovare documentazione per creare e gestire basati su Linux macchine virtuali nel modello di distribuzione classica.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Guida introduttiva
- [Introduzione per Linux su Azure](virtual-machines-linux-intro-on-azure.md)
- [Domande frequenti sulle macchine virtuali di Azure creato con il modello di distribuzione classica](virtual-machines-linux-classic-faq.md)
- [Informazioni sulle immagini per macchine virtuali](virtual-machines-linux-classic-about-images.md)
- [Caricare la propria immagine Distro](virtual-machines-linux-classic-create-upload-vhd.md) (e anche istruzioni usando una [Distribuzione Azure-Endorsed](virtual-machines-linux-endorsed-distros.md))
- [Accedere a un macchine Virtuali Linux tramite il portale classico Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Configurare

- [Installare Azure interfaccia della riga (comando Azure)](../xplat-cli-install.md)


## <a name="tutorials"></a>Esercitazioni

- [Installare Stack di luce in un computer virtuale Linux in Azure](virtual-machines-linux-create-lamp-stack.md)
- [Trascrizione in applicazione Web guide su una macchina virtuale Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Procedura: installare Apache Qpid Proton-C per AMQP e Bus di servizio](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Database
- [Ottimizzare le prestazioni di MySQL su Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [MySQL cluster](virtual-machines-linux-classic-mysql-cluster.md)
- [Esecuzione Cassandra con Linux su Azure e accedervi da Node](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Creare un cluster più Master di MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [Guida introduttiva a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Eseguire NAMD con Microsoft HPC Pack su Linux nodi di calcolo in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [Utilizzando l'estensione di macchine Virtuali Docker comuni della riga di comando interfaccia (CLI Azure)](virtual-machines-linux-classic-cli-use-docker.md)
- [Utilizzando l'estensione di macchine Virtuali Docker dal portale di Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Come usare docker computer Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Procedura: MySQL cluster](virtual-machines-linux-classic-mysql-cluster.md)
- [Procedura: node e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Procedura: installare ed eseguire MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Procedura: utilizzare CoreOS su Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Pianificazione
- [Implementazione di servizi di infrastruttura Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Selezionare i nomi utente Linux](virtual-machines-linux-usernames.md)
- [Come configurare la disponibilità di una set per macchine virtuali nel modello di distribuzione classica](virtual-machines-linux-classic-configure-availability.md)
- [Come pianificare la manutenzione pianificata in macchine virtuali di Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gestire la disponibilità di macchine virtuali](virtual-machines-linux-manage-availability.md)
- [Manutenzione pianificata per macchine virtuali Linux in Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Distribuzione
- [Creare una macchina virtuale personalizzata che eseguono Linux](virtual-machines-linux-classic-createportal.md)
- [Nozioni fondamentali: l'acquisizione di un VM Linux per creare un modello](virtual-machines-linux-classic-capture-image.md)
- [Informazioni per le distribuzioni Non approvato](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Gestione

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Come reimpostare la Password o proprietà SSH per Linux](virtual-machines-linux-classic-reset-access.md)
- [Utilizzo principale](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Risorse Azure

- [Agente di Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Caratteristiche e le estensioni macchine Virtuali di azure](virtual-machines-windows-extensions-features.md)
- [Inserire dati personalizzati in una macchina virtuale da usare con Cloud-inizializzazione](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Spazio di archiviazione

- [Associare un disco dati a una macchina virtuale Linux](virtual-machines-linux-classic-attach-disk.md)
- [Scollegare un disco di dati da una macchina virtuale Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Rete
- [Come configurare i punti finali in un computer virtuale classico in Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Risoluzione dei problemi
- [Risolvere i problemi di connessioni Secure Shell (SSH) in un computer virtuale Azure basato su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Risoluzione dei problemi di distribuzione classica alla creazione di una nuova macchina virtuale Linux in Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Risoluzione dei problemi di distribuzione classica con il riavvio o ridimensionamento di una macchina virtuale Linux esistente in Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Guida di riferimento

- [Azure comandi CLI in modalità di gestione dei servizi di Azure (asm)](../virtual-machines-command-line-tools.md)
- [Gestione dei servizi Azure API REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Collegamenti generale
I collegamenti seguenti sono per blog di Microsoft, Technet pagine e siti esterni piuttosto che documentazione Azure.com come illustrato in precedenza. Azure e tutto il mondo elaborazione Apri origine vengono rapido movimento destinazioni, è quasi certo che i collegamenti seguenti siano non è aggiornato, *Nonostante* il fatto che le facciamo buona continuamente e aggiungere argomenti più recenti e rimuovere quelli non aggiornati. Se sono state perse uno, possiamo fonti di informazioni nei commenti o inviare una richiesta di pull per il nostro [GitHub repo](https://github.com/Azure/azure-content/).

- [In esecuzione ASP.NET 5 sull'utilizzo di contenitori Docker Linux](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Come distribuire un'immagine di macchine Virtuali CentOS da OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infrastruttura di aggiornamento SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server per una raccolta di accessorio Cloud SAP](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Creazione di disponibilità Linux su Azure in 12 passaggi](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatizzare il Provisioning di Linux su Azure con Azure CLI e Node, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS su Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [Esecuzione FreeBSD in Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Facile distribuire FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Distribuzione di un'immagine FreeBSD personalizzate](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV per Linux File Server](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [8 database NoSql Apri origine per Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): Esperienze con CouchDb su Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Esecuzione CouchDB-come-a-Service con Node, CORS e noioso](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis in Windows nel servizio di Cache Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Annuncio Provider dello stato sessione ASP.NET per versione Preview Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ ora disponibile in Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Dati
- [Installazione di Hadoop in macchine virtuali Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Database relazionale
- [Architettura di disponibilità elevata MySQL in Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Installazione di Postgres con corosync, pg_bouncer utilizzando ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux HPC high performance computing)

- [Modello Guida introduttiva: ruotare un cluster di SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (e [il post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modello di Guida introduttiva: creare un cluster HPC con Linux nodi di calcolo](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>Attrezzi, gestione e ottimizzazione

Come mondo degli attrezzi, gestione e ottimizzazione è molto ampio e modificare rapidamente, è necessario prendere in considerazione l'elenco sotto un punto di partenza.

- [Video: Azure virtuali: uso Chef, Marionetta e Docker per la gestione delle macchine virtuali Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guida completa alla distribuzione di cluster Kubernetes automatica con CoreOS e Intessitura](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualizzatore Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Secondario Jenkins plug-in per Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [GitHub repo: archiviazione Jenkins plug-in per Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terze parti: Plug-in per Azure Hudson secondario](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terze parti: Spazio di archiviazione Hudson plug-in per Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Video: Che cos'è Chef e come funziona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Video: Come utilizzare l'automazione Azure con macchine virtuali Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: Come eseguire DSC Powershell per Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Docker Client DSC](https://github.com/anweiss/DockerClientDSC)

- [Plug-in chi per Azure](https://github.com/msopentech/packer-azure)
