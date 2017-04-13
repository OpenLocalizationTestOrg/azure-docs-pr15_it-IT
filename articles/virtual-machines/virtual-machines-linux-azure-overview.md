 <properties
   pageTitle="Azure e Linux | Microsoft Azure"
   description="Vengono descritti i servizi di Azure calcolare, lo spazio di archiviazione e reti con macchine virtuali Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure e Linux

Microsoft Azure è un insieme crescente di pubblico integrata tra cui i database analitica, macchine virtuali, per dispositivi mobili, reti, lo spazio di archiviazione, servizi cloud e web, ideale per le soluzioni di hosting.  Microsoft Azure fornisce una piattaforma di elaborazione scalable che consente di pagamento per ciò che si utilizza, quando si desidera - senza dover investire in locale hardware.  Azure è pronto quando si lavora di scalare le soluzioni e su qualsiasi scala è necessario per soddisfare le esigenze dei clienti.

Se si ha familiarità con le varie caratteristiche di Amazon AWS, è possibile esaminare il AWS vs Azure [documento di definizione di mapping](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Aree
Risorse di Microsoft Azure vengono distribuite tra più aree geografiche in tutto il mondo.  "Area" rappresenta più data center in un'unica area geografica.  Prima del 1 ° gennaio 2016, sono inclusi: 8 America, 2 europei, 6 nell'area Asia Pacifico, 2 in Cina e 3 in India.  Se si desidera un elenco completo di tutte le aree di Azure, vengono conservati che un elenco esistente e appena annunciato aree geografiche.

- [Aree Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilità
Affinché la distribuzione ottenere il nostro 99.95 contratto di servizio macchine Virtuali, è necessario distribuire due o più macchine virtuali in esecuzione il carico di lavoro all'interno di una disponibilità impostate. In questo modo nelle macchine virtuali sono distribuite in più domini guasto il data center, nonché distribuite in host con windows manutenzione diversi. Intero [Contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) viene garantita disponibilità di Azure nel suo insieme.

## <a name="azure-virtual-machines--instances"></a>Azure macchine virtuali e istanze
Microsoft Azure supporta l'esecuzione di un numero di popolari distribuzioni Linux fornito e gestito da un numero di partner.  Si noterà distribuzioni, ad esempio Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e altre funzionalità in Azure Marketplace. Si lavora attivamente varie comunità Linux per aggiungere anche altre caratteristiche all'elenco di [che Azure approvato Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Se il distro Linux preferito desiderato non è attualmente presenta nella raccolta, è possibile "portare il proprio Linux" macchine Virtuali mediante [la creazione e caricamento di un disco rigido virtuale di Linux in Azure](virtual-machines-linux-create-upload-generic.md).

Macchine virtuali di Azure consente di distribuire un'ampia gamma di soluzioni informatiche, in modo agile. È possibile distribuire praticamente qualsiasi carico di lavoro e qualsiasi lingua in qualsiasi sistema operativo - Windows, Linux, o un oggetto personalizzato creato uno da uno qualsiasi del nostro elenco crescente dei partner. Non ancora visualizzato che sta cercando?  Niente paura: è anche possibile utilizzare le proprie immagini di locale.

## <a name="vm-sizes"></a>Dimensioni di macchine Virtuali
Quando si distribuisce una macchina virtuale in Azure, si sta per selezionare una dimensione di memoria virtuale all'interno di una della serie di dimensione che può essere il carico di lavoro. Le dimensioni influisce anche sui capacità di alimentazione, memoria e spazio di archiviazione di elaborazione della macchina virtuale. Sono effettuata in base alla quantità di tempo macchina virtuale è in esecuzione e che utilizzano le risorse assegnate. Un elenco completo delle [dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).

Ecco alcune linee guida fondamentali per la selezione di una dimensione di memoria virtuale da una delle serie (A, D, DS, G e GS).

* Macchine virtuali di serie sono il valore prezzo macchine virtuali di base per gli scenari di sviluppo/Test e light carichi di lavoro. Sono ampiamente disponibile in tutti i paesi e possono connettersi e utilizzare tutte le risorse standard disponibili in macchine virtuali.
* Le dimensioni di una serie (A8 - A11) sono configurazioni complesse calcolo speciale adatte alle applicazioni cluster di elaborazione massime prestazioni.
* Macchine virtuali di serie D sono progettate per l'esecuzione di applicazioni che richiedono più grande potenza e prestazioni del disco temporaneo. Macchine virtuali di serie D forniscono processori più veloci, un rapporto di memoria a core superiore e un'unità (SSD) per il disco temporaneo.
* Serie di Dv2 è l'ultima versione della serie D, caratteristiche più potente CPU. La CPU serie Dv2 è circa 35% più veloce la CPU serie D. Si basa sull'ultima generazione 2,4 GHz Intel Xeon® E5-2673 v3 processore (Haskell) e con Intel Turbo Preamplificazione tecnologia 2.0, può essere eseguita fino a 3,2 GHz. La serie di Dv2 ha le stesse configurazioni di memoria e disco la serie D.
* Macchine virtuali di serie G offrono la maggior parte della memoria ed eseguire sugli host che dispongono di processori della famiglia Intel Xeon E5 V3.

Nota: Serie DS e macchine virtuali di serie GS hanno accesso allo spazio di archiviazione Premium: il nostro SSD backup delle prestazioni ad alta, bassa latenza spazio di archiviazione per carichi di lavoro in modo intensivo i/o. Spazio di archiviazione Premium è disponibile in alcune aree geografiche. Per informazioni dettagliate, vedere:

- [Spazio di archiviazione Premium: Spazio di archiviazione High-performance per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automazione
Per ottenere una lingua attrezzi corretto, tutta l'infrastruttura deve contenere codice.  Quando tutti l'infrastruttura di cui si trova nel codice che possono essere facilmente ricreata (Phoenix server).  Azure funziona con l'automazione principale utensili come Ansible, Chef, SaltStack e Marionetta.  Azure è installato anche il proprio utensili per l'automazione:

- [Modelli di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [VMAccess Azure](virtual-machines-linux-using-vmaccess-extension.md)

Azure attuare il supporto per [cloud-inizializzazione](http://cloud-init.io/) attraverso la maggior parte dei Linux Distros che supportano questa opzione.  Attualmente macchine virtuali Ubuntu del Canonical vengono distribuite con cloud-inizializzazione attivata per impostazione predefinita.  RedHats RHEL, CentOS e Fedora supporto cloud-inizializzazione, tuttavia le immagini Azure gestito da Red Hat non è installato cloud-inizializzazione.  Per usare una famiglia di Red Hat OS cloud-inizializzazione, è necessario creare un'immagine personalizzata con cloud-inizializzazione installato.

- [Utilizzo di cloud-inizializzazione in macchine virtuali Linux Azure](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Quote
Ogni abbonamento Azure sono previsti limiti di quota predefiniti che potrebbero avere un impatto della distribuzione di un numero elevato di macchine virtuali per il progetto. Il limite corrente alla scala cronologica per abbonamento è 20 macchine virtuali per ogni area geografica.  Limiti di quota possono essere generati da un ticket di supporto che richiede un aumento del limite di archiviazione.  Per ulteriori informazioni sui limiti di quota:

- [Limiti di servizio di abbonamento Azure](../azure-subscription-service-limits.md)


## <a name="partners"></a>Partner

Microsoft collabora con i partner per assicurarsi che le immagini disponibili vengono aggiornate e ottimizzate per un tempo di esecuzione Azure.  Per ulteriori informazioni su partner selezionare pagine marketplace.

- [Linux nelle distribuzioni approvato Azure](virtual-machines-linux-endorsed-distros.md)

Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canonico - [Azure Marketplace - Ubuntu Server 16.04 risultati](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - 8 Debian "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (stabile)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Raccolta Bitnami per Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - controller di dominio Mesosphere sistema operativo in Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - servizio contenitore Azure con sciame Docker](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins piattaforma](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Guida di configurazione in Azure
Per iniziare a utilizzare Azure è necessario un account Azure, CLI Azure installato e una coppia di chiavi pubbliche e privatehttp SSH.

## <a name="sign-up-for-an-account"></a>Iscriversi a un account
Il primo passaggio con il Cloud Azure è di iscriversi a un account Azure.  Passare alla pagina di [Iscrizione Account Azure](https://azure.microsoft.com/pricing/free-trial/) per iniziare.

## <a name="install-the-cli"></a>Installare CLI
Con il nuovo account Azure, è possibile iniziare immediatamente tramite il portale di Azure, ossia un riquadro amministratore basato sul web.  Per gestire il Cloud Azure attraverso la riga di comando, installare il `azure-cli`.  Installare [Azure CLI ](../xplat-cli-install.md)sulla workstation Mac o Linux.

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
A questo punto si dispone di un account Azure, il portale web Azure e CLI Azure.  Il passaggio successivo consiste nel creare una coppia di chiavi SSH utilizzato per SSH in Linux senza utilizzare una password.  [Creare SSH tasti su Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md) per abilitare gli accessi minore di password e maggiore sicurezza.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Guida introduttiva a Linux in Microsoft Azure
Con l'installazione e configurazione account Azure CLI Azure installato e chiavi SSH create si è pronti a questo punto iniziare a creare un'infrastruttura cloud Azure.  La prima attività consiste nel creare un paio di macchine virtuali.

## <a name="create-a-vm-using-the-cli"></a>Creare una macchina virtuale utilizzando CLI
La creazione di una VM Linux utilizzando CLI è un modo rapido per distribuire una macchina virtuale senza uscire da terminale in uso.  Tutti gli elementi che è possibile specificare nel portale web è disponibili tramite un contrassegno della riga di comando o un parametro.  

- [Creare una VM Linux utilizzando CLI](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Creare una macchina virtuale nel portale
Creazione di una VM Linux nel portale di Azure web è possibile facilmente quindi tra le varie opzioni per accedere a una distribuzione.  Invece di usare contrassegni della riga di comando o parametri, in grado di visualizzare un layout web pile delle varie impostazioni e opzioni.  Tutti gli elementi disponibili tramite l'interfaccia della riga di comando è disponibili anche nel portale.

- [Creare una VM Linux tramite il portale](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Accesso tramite SSH senza una password
La macchina virtuale è in esecuzione in Azure e si è pronti per l'accesso.  Uso delle password per l'accesso tramite SSH è protetta e richiedere molto tempo.  Utilizzo dei tasti SSH è il modo più sicuro e anche il modo più rapido per l'accesso.  Quando si creano Linux VM tramite il portale o CLI, sono disponibili due opzioni di autenticazione.  Se si sceglie una password per SSH, Azure consente di configurare la macchina virtuale per consentire l'accesso tramite password.  Se si è scelto di usare una chiave pubblica SSH, Azure consente di configurare la macchina virtuale per consentire solo gli account di accesso tramite chiavi SSH e disattiva gli accessi password. Per proteggere le VM Linux consentendo solo accessi chiavi SSH, utilizzare l'opzione di chiave pubblica SSH durante la creazione di macchine Virtuali nel portale o CLI.

- [Disattivare le password SSH sulle VM Linux configurando SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Componenti di Azure correlati

## <a name="storage"></a>Spazio di archiviazione

- [Introduzione all'archiviazione di Microsoft Azure](../storage/storage-introduction.md)

- [Aggiungere un disco una VM Linux utilizzando cli azure](virtual-machines-linux-add-disk.md)

- [Come collegare un disco dati a una VM Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Rete

- [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md)

- [Indirizzi IP in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Apertura delle porte per una VM Linux in Azure](virtual-machines-linux-nsg-quickstart.md)

- [Creare un nome di dominio completo nel portale di Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Contenitori

- [Macchine virtuali e i contenitori di Azure](virtual-machines-linux-containers.md)

- [Introduzione contenitore servizio Azure](../container-service/container-service-intro.md)

- [Distribuire un cluster di servizio contenitore Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Passaggi successivi

È ora disponibile una panoramica di Linux in Azure.  Il passaggio successivo consiste per iniziare a creare macchine virtuali alcuni.

- [Creare una VM Linux su Azure tramite il portale](virtual-machines-linux-quick-create-portal.md)

- [Creare una VM Linux su Azure tramite CLI](virtual-machines-linux-quick-create-cli.md)
