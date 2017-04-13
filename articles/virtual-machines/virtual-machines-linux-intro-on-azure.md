<properties
    pageTitle="Introduzione a Linux in Azure | Microsoft Azure"
    description="Informazioni sull'uso di macchine virtuali Linux in Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>Introduzione a Linux su Azure

In questo argomento viene fornita una panoramica di alcuni aspetti dell'utilizzo di macchine virtuali Linux nel cloud Azure. Distribuire una macchina virtuale Linux è un processo semplice utilizzo di un'immagine dalla raccolta.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Autenticazione: I nomi utente, password e chiavi SSH

Quando si crea una macchina virtuale Linux classica nel portale di Azure, viene chiesto di immettere un nome utente, password o una chiave pubblica SSH. La scelta di un nome utente per la distribuzione di una macchina virtuale Linux in Azure è soggetto a vincolo seguente: i nomi degli account di sistema (UID < 100) già presenti nella macchina virtuale non sono ammessi 'root', ad esempio.


 - Vedere [creare una macchina virtuale che eseguono Linux](virtual-machines-linux-quick-create-cli.md)
 - Informazioni [su come utilizzare SSH con Linux su Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Ottenere i privilegi di utente avanzato utilizzando`sudo`

L'account utente specificato durante la distribuzione di istanza macchina virtuale in Azure è un account con privilegi. L'account è configurato con l'agente di Linux Azure in grado di acquisire i privilegi di radice (account utente avanzato) mediante la `sudo` utility. Dopo aver eseguito l'accesso tramite questo account utente, sarà possibile eseguire comandi come radice utilizzando la sintassi di comando

    # sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell radice utilizzando **sudo -s**.

- Vedere [usare radice i privilegi per macchine virtuali Linux in Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Configurazione del firewall

Azure fornisce un filtro di pacchetti in ingresso che limita la connettività a porte specificate nel portale di classica Azure. Per impostazione predefinita, la porta consentita sola è SSH. È possibile aprire l'accesso alle porte aggiuntive sul computer virtuale Linux configurando i punti finali nel portale di classica Azure:

 - Vedere: [come configurare i punti finali a una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md)

Immagini Linux nella raccolta di Azure firewall *iptables* non è attivato per impostazione predefinita. Se lo si desidera, il firewall può essere configurato per specificare altre opzioni di filtro.


## <a name="hostname-changes"></a>Hostname modifiche

Quando si distribuisce inizialmente un'istanza di un'immagine di Linux, è necessario specificare un nome per la macchina virtuale. Dopo la macchina virtuale è in esecuzione, questo hostname viene pubblicato ai server DNS piattaforma in modo che più macchine virtuali collegate tra loro possono eseguire ricerche di indirizzi IP con nome host.

Se si desidera apportare modifiche di hostname dopo una macchina virtuale è stata distribuita, utilizzare il comando

    # sudo hostname <newname>

L'agente di Linux Azure include funzionalità che rileva automaticamente questa modifica del nome e configurare in modo appropriato la macchina virtuale per mantenere la modifica e pubblicazione di questa modifica ai server DNS piattaforma.

 - [Guida di Azure Linux agente utente](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Cloud-inizializzazione
Immagini **Ubuntu** e **CoreOS** utilizzano cloud-inizializzazione su Azure, che offre funzionalità aggiuntive per l'avvio di una macchina virtuale.

 - [Come inserire dati personalizzati](virtual-machines-windows-classic-inject-custom-data.md)
 - [Dati personalizzati e Cloud-inizializzazione in Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Creare partizioni Scambia Azure utilizzando Cloud-inizializzazione](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Come usare CoreOS Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Acquisizione di immagini macchina virtuale

Azure consente di acquisire lo stato di una macchina virtuale esistente in un'immagine può essere utilizzata per distribuire le istanze di macchina virtuale aggiuntiva. L'agente di Linux Azure può essere utilizzato per eseguire il ripristino alcuni di personalizzazione che è stata eseguita durante il processo di provisioning. È possibile seguire la procedura seguente per acquisire una macchina virtuale come immagine:

1. Eseguire **waagent-annullare l'implementazione** per annullare la personalizzazione di provisioning. O **waagent-annullare l'implementazione + utente** facoltativamente, è possibile eliminare l'account utente specificato durante provisioning e tutti i dati associati.

2. Chiudi verso il basso/spegnere la macchina virtuale.

3. Fare clic su *Acquisisci* nel portale di classica Azure o utilizzare gli strumenti di Powershell o CLI per acquisire la macchina virtuale come immagine.

 - Vedere: [come acquisire una macchina virtuale Linux da utilizzare come modello](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Associare dischi

Ogni macchina virtuale dispone di un locale e temporanea *disco di risorsa* associata. Poiché i dati in un disco di risorsa potrebbero non essere permanenti dopo il riavvio, viene spesso utilizzato da applicazioni e processi in esecuzione nel computer virtuale per la memorizzazione temporaneo e **temporanea** dei dati. Viene usato anche per archiviare la pagina o scambiare file per il sistema operativo.

Su Linux, il disco di risorsa è in genere gestito dall'agente di Linux Azure e automaticamente installato per **/mnt/resource** (o **/mnt** su Ubuntu immagini).


>[AZURE.NOTE] Si noti che il disco di risorsa è un disco **temporaneo** e potrebbe essere eliminato e riformattato al riavvio la macchina virtuale.

Su Linux disco dati potrebbe essere denominato dal kernel come `/dev/sdc`, e gli utenti dovranno partizioni, formattare e installare quella risorsa. Questo argomento viene trattato dettagliate nell'esercitazione: [come collegare un disco di dati a una macchina virtuale](virtual-machines-linux-classic-attach-disk.md).

 - **Vedere anche:** [Configurare il Software RAID su Linux](virtual-machines-linux-configure-raid.md)  &  [Configurare LVM in una macchina virtuale Linux in Azure](virtual-machines-linux-configure-lvm.md)

