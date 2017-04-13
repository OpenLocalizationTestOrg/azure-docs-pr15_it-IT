<properties
   pageTitle="Test SAP NetWeaver in macchine virtuali di Microsoft Azure SUSE Linux | Microsoft Azure"
   description="Test NetWeaver SAP in macchine virtuali di Microsoft Azure SUSE Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Esecuzione SAP NetWeaver in macchine virtuali di Microsoft Azure SUSE Linux


In questo articolo vengono illustrati i vari aspetti da considerare quando si esegue SAP NetWeaver a Microsoft Azure SUSE Linux macchine (). Prima del 2016 19 maggio SAP NetWeaver ufficiale è supportato in macchine virtuali Linux SUSE in Azure. Tutte le informazioni relative alle versioni Linux, versioni kernel SAP e così via sono disponibili in SAP nota 1928533 "le applicazioni SAP in Azure: prodotti supportati e dei tipi di Azure macchine Virtuali".
Ulteriori informazioni su SAP in macchine virtuali Linux disponibile qui: [Utilizzando SAP su Linux macchine ()](virtual-machines-linux-sap-get-started.md).


Le informazioni seguenti dovrebbero risultare utili evitare alcuni potenziali problemi.

## <a name="suse-images-on-azure-for-running-sap"></a>Immagini SUSE su Azure per l'esecuzione di SAP

Per l'esecuzione di SAP NetWeaver su Azure, utilizzare solo SUSE Linux Enterprise Server SLES 12 (SPx): Vedere anche nota SAP 1928533. Un'immagine SUSE speciale è in Azure Marketplace ("SLES 11 SP3 per licenza CAL SAP"), ma non è destinato per l'utilizzo generale. Non utilizzare questa immagine perché è riservato per la soluzione [SAP Cloud accessorio raccolta](https://cal.sap.com/) .  

Utilizzare Gestione risorse di Azure per tutti i nuovi test e installazioni in Azure. Per cercare immagini SUSE SLES e versioni tramite PowerShell Azure o interfaccia Azure della riga di comando (CLI), utilizzare i comandi seguenti. È quindi possibile utilizzare l'output, ad esempio, per definire l'immagine del sistema operativo in un modello di JSON per la distribuzione di un nuovo supporto virtuale di SUSE Linux.
Questi comandi PowerShell sono valide per la versione di Azure PowerShell 1.0.1 e versioni successive.

* Cercare editori esistenti, SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Cercare esistente offerte da SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Cercare offerte SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Cercare una versione specifica di uno SKU SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>L'installazione di WALinuxAgent in una macchina virtuale SUSE

L'agente di denominato WALinuxAgent fa parte delle immagini SLES in Azure Marketplace. Per informazioni sull'installazione manualmente (ad esempio durante il caricamento di un sistema operativo SLES virtuale sul disco rigido (disco rigido virtuale) da locale), vedere:

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtual-macchine-linux-approvato-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "enhanced monitoraggio"

SAP "enhanced monitoraggio" è un prerequisito obbligatorio eseguire SAP in Azure. Controllare i dettagli in SAP nota 2191498 "SAP su Linux con Azure: Enhanced monitoraggio".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Connessione dischi Azure dati a una macchina virtuale Linux Azure

È necessario non è mai dischi di dati di Azure montaggio a una macchina virtuale Linux Azure utilizzando l'ID di dispositivo. Utilizzare l'identificatore univoco universale (UUID). Prestare attenzione quando si usa strumenti grafici per dischi di dati di Azure montaggio, ad esempio. Verificare le voci di /etc/fstab..

Il problema con l'ID del dispositivo è che potrebbero essere modificati e quindi la macchina virtuale Azure potrebbe bloccarsi nel processo di avvio. Per ridurre il problema, è possibile aggiungere il parametro nofail in /etc/fstab.. Ma, prestare attenzione alle nofail perché applicazioni possono utilizzare il punto di montaggio come prima e potrebbero scrivere nel file system radice nel caso in cui un disco di dati di Azure esterni non è stato installato durante l'avvio.

L'unica eccezione a montaggio tramite UUID si connette un disco del sistema operativo per la risoluzione dei problemi, come descritto nella sezione seguente.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Risoluzione dei problemi relativi a un VM SUSE che non è più accessibile

Potrebbero verificarsi situazioni in cui una VM SUSE su Azure si blocca nel processo di avvio (ad esempio, con un messaggio di errore relative a montaggio dischi). È possibile verificare il problema utilizzando la funzionalità di diagnostica di avvio per macchine virtuali di Azure v2 nel portale di Azure. Per ulteriori informazioni, vedere [avvio diagnostica] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Un modo per risolvere il problema è per allegare il disco del sistema operativo da macchina virtuale danneggiata a un'altra SUSE VM in Azure. Quindi apportare modifiche ad esempio /etc/fstab. di modifica o rimozione di regole di udev di rete, come descritto nella sezione successiva.

Non c'è è importante tenere presente. Distribuzione di diverse macchine virtuali di SUSE dalla stessa immagine di Azure Marketplace (ad esempio SLES 11 SP4) causa il disco del sistema operativo essere sempre fissato con lo stesso UUID. Di conseguenza, utilizzando l'UUID per connettere un disco del sistema operativo da una macchina diversa distribuita utilizzando la stessa immagine di Azure Marketplace verrà generato due UUID identici. In questo modo problemi e significa che la macchina virtuale per la risoluzione dei problemi in realtà verrà avviato dal disco del sistema operativo sia collegato sia danneggiato anziché quella originale.

Esistono due modi per evitare il problema:

* Utilizzare un'immagine di Azure Marketplace diversa per la risoluzione dei problemi macchina virtuale (ad esempio SLES 11 SPx anziché SLES 12).
* Non allegare disco OS danneggiato da un'altra macchine Virtuali mediante UUID - usare un altro.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Caricamento di una macchina virtuale SUSE locali per Azure

Per una descrizione dei passaggi per caricare una VM SUSE locali in Azure, vedere [preparare una macchina virtuale SLES o openSUSE per Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Se si desidera caricare una macchina virtuale senza il passaggio di annullare l'implementazione alla fine (ad esempio, per mantenere un'installazione SAP esistente, nonché il nome host), controllare quanto segue:

* Assicurarsi che il disco del sistema operativo sia installato utilizzando UUID e non l'ID del dispositivo. La modifica in UUID solo nei /etc/fstab. non è sufficiente per il disco del sistema operativo. Inoltre, non dimenticare di adattare caricatore di avvio mediante YaST o modificando /boot/grub/menu.lst.
* Se si utilizza il formato VHDX per il disco SUSE OS e convertirla in disco rigido virtuale per il caricamento in Azure, è molto probabile che il dispositivo di rete cambierà da eth0 a eth1. Per evitare problemi quando si esegue l'avvio in Azure in un secondo momento, ripristinare eth0 come descritto in [correggere eth0 in duplicato SLES 11 VMware] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Oltre a quella descritta nell'articolo, è consigliabile rimuovere seguente:

   /LIB/udev/Rules.d/75-persistent-NET-Generator.Rules

È anche possibile installare l'agente di Azure Linux (waagent) per evitare potenziali problemi, purché non sono disponibili più schede di rete.

## <a name="deploying-a-suse-vm-on-azure"></a>Distribuzione di una macchina virtuale SUSE su Azure

È consigliabile creare nuove macchine virtuali di SUSE utilizzando i file di modello JSON nel nuovo modello di gestione di risorse Azure. Una volta creato il file del modello JSON, è possibile distribuire la macchina virtuale utilizzando il seguente comando CLI come alternativa PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Per ulteriori informazioni sui file di modello JSON, vedere [Authoring Manager delle risorse di Azure modelli] (risorsa-gruppo-authoring-templates.md) e [Guida introduttiva di Azure modelli] (https://azure.microsoft.com/documentation/templates/).

Per ulteriori informazioni su CLI e gestione di risorse di Azure, vedere [usare CLI Azure per Mac, Linux e Windows Azure Gestione risorse di] (xplat-cli-azure-risorsa-manager.md).

## <a name="sap-license-and-hardware-key"></a>Chiave di licenza e hardware SAP

Per l'abilitazione di Azure SAP ufficiale, un nuovo meccanismo è stata introdotta per calcolare il tasto hardware SAP utilizzato per la licenza SAP. Kernel SAP doveva essere adattate per utilizzare questa. Versioni di kernel SAP ex per Linux non include la modifica di codice. Di conseguenza, in alcuni casi (ad esempio macchine Virtuali di Azure ridimensionamento), la chiave hardware SAP modificata e la licenza SAP è stato non valido. Questo è stato risolto in più recenti vengono di SAP Linux. Per informazioni dettagliate selezionare Nota SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Pacchetto di sapconf SUSE / adm ottimizzati

SUSE fornisce un pacchetto denominato "sapconf" che gestisce un insieme di impostazioni specifiche del SAP. Per ulteriori informazioni su che cosa significa questo pacchetto e su come installare e utilizzare la funzionalità, vedere [utilizzando sapconf per preparare l'ambiente SUSE Linux Enterprise Server per l'esecuzione di sistemi SAP] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [novità sapconf o come preparare SUSE Linux Enterprise Server per l'esecuzione di sistemi SAP?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Nel frattempo è presente un nuovo strumento che sostituisce sapconf - adm ottimizzati Una possibile trovare informazioni dettagliate su questo strumento collegamento due riportato di seguito.

Sono disponibili documentazione SLES sulle adm ottimizzate profilo sap-hana [qui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Sono disponibili i sistemi di regolazione per carichi di lavoro SAP con adm ottimizzate - [qui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) nella capitolo 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>Condivisione NFS in installazioni distribuite SAP

Se si dispone di un'installazione distribuita, ad esempio, in cui si vuole installare il database e i server di applicazioni SAP in macchine virtuali separate, è possibile condividere la directory /sapmnt tramite File System NFS (Network). Se si verificano problemi con la procedura di installazione dopo aver creato la condivisione NFS per /sapmnt, verificare se "no_root_squash" è impostato per la condivisione.

## <a name="logical-volumes"></a>Volumi logici

In passato uno eventualmente una grande quantità logica su più dischi di dati di Azure (ad esempio, per il database SAP) è stata consiglia di utilizzare mdadm come lvm non è stata completamente convalidata ancora in Azure. Per informazioni su come configurare RAID Linux su Azure tramite mdadm, vedere [configurare software RAID su Linux](virtual-machines-linux-configure-raid.md). Nel frattempo alla data di inizio di maggio 2016 anche lvm è completamente supportata in Azure e può essere utilizzato come alternativa alla mdadm. Per ulteriori informazioni sui lvm su Azure, vedere [Configurare LVM in una macchina virtuale Linux in Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Archivio SUSE Azure

Se si dispone di un problema con l'accesso a repository Azure SUSE standard, è possibile utilizzare un comando semplice per reimpostarlo. Questo può accadere se si crea un'immagine del sistema operativo privata in un'area di Azure e quindi copiare l'immagine su un'area diversa nel punto in cui si desidera distribuire nuove macchine virtuali basati su questa immagine del sistema operativo privata. Eseguire il comando seguente all'interno della macchina virtuale:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Desktop Gnome

Se si desidera utilizzare il desktop Gnome per installare un sistema di demo SAP completo all'interno di una singola macchina virtuale - inclusi un'interfaccia grafica SAP, browser e SAP management console: utilizzare questo suggerimento per installarlo in immagini SLES Azure:

   Per SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Per SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Supporto SAP per Oracle su Linux nel cloud

Esiste una restrizione di supporto da Oracle su Linux in ambienti virtualizzati. Anche se non è un argomento specifico di Azure, è importante tenere presente. SAP non supporta Oracle SUSE o Red Hat in un'area pubblica come Azure. Per discutere in questo argomento, contattare direttamente il Oracle.
