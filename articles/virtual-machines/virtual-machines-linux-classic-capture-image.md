<properties
    pageTitle="Acquisire un'immagine di una VM Linux | Microsoft Azure"
    description="Informazioni su come acquisire un'immagine di una basati su Linux Azure macchine () creata con il modello di distribuzione classica."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Come acquisire una macchina virtuale Linux classica come immagine

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](virtual-machines-linux-capture-image.md).

In questo articolo viene illustrato come acquisire una macchina virtuale Azure classica che eseguono Linux come un'immagine per creare altre macchine virtuali. Questa immagine include il disco rigido del sistema operativo e dischi dati collegati alla macchina virtuale. Non include la configurazione di rete, pertanto è necessario configurare che quando si creano altre macchine virtuali dall'immagine.

Azure memorizza l'immagine in **immagini**, insieme a tutte le immagini di che aver caricato. Per ulteriori informazioni sulle immagini, vedere [Sulle immagini macchina virtuale di Azure] [].

## <a name="before-you-begin"></a>Prima di iniziare

Questa procedura si presuppone che aver creato una macchina virtuale Azure utilizzando il modello di distribuzione classica e di aver configurato il sistema operativo, tra cui associare qualsiasi disco di dati. Se è necessario creare una macchina virtuale, informazioni, [vedere creare una macchina virtuale Linux] [].


## <a name="capture-the-virtual-machine"></a>Acquisire la macchina virtuale

1. [Connettersi alla macchina virtuale](virtual-machines-linux-mac-create-ssh-keys.md) utilizzando un client SSH preferito.

2. Nella finestra di SSH, digitare il comando seguente. L'output di `waagent` possono variare leggermente a seconda della versione di questa utilità:

    `sudo waagent -deprovision+user`

    Questo comando tenta di eliminare dal sistema e renderli più adatto per un nuovo provisioning del. Questa operazione esegue le operazioni seguenti:

    - Rimuove le chiavi host SSH (se Provisioning.RegenerateSshHostKeyPair è "y" nel file di configurazione)
    - Cancella la configurazione dei server dei nomi in /etc/resolv.conf
    - Rimuove le `root` password utente da/ecc/ombreggiatura (se Provisioning.DeleteRootPassword è "y" nel file di configurazione)
    - Rimuove cache leasing client DHCP
    - Nome host Reimposta a localhost.localdomain
    - Elimina l'ultimo provisioning utente account (ottenuto dal /var/lib/waagent) **e i dati associati**.

    >[AZURE.NOTE] Annullamento dell'implementazione Elimina file e dati a "generica" l'immagine. Solo il seguente comando in un computer virtuale che si desidera acquisire come nuovo modello di immagine. Non garantisce che l'immagine è deselezionata di tutte le informazioni riservate o è adatto per la ridistribuzione a terze parti.


3. Digitare **y** per continuare. È possibile aggiungere il `-force` parametro per evitare questo passaggio di conferma.

4. Digitare **Esci** per chiudere il client SSH.

    >[AZURE.NOTE] La procedura si suppone che già [installato CLI Azure](../xplat-cli-install.md) nel computer client. Tutti i passaggi seguenti possono essere eseguiti anche nel [portale classica Azure] [].

5. Dal computer client aprire CLI Azure e accedere al proprio abbonamento Azure. Per ulteriori informazioni, leggere [connettersi a un abbonamento Azure da CLI Azure](../xplat-cli-connect.md).

6. Verificare che siano in modalità di gestione dei servizi:

    `azure config mode asm`

7. Arrestare la macchina virtuale che è già stato rimosso con deprovisioning nei passaggi precedenti con:

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] È possibile trovare tutte le macchine virtuali create in abbonamento tramite`azure vm list`

8. Quando la macchina virtuale viene interrotto, acquisire un'immagine con il comando:

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Digitare il nome dell'immagine desiderata al posto di _Nome nuova immagine_. Questo comando crea un'immagine del sistema operativo GRG. Il `-t` sottocomando Elimina la macchina virtuale originale.

9.  La nuova immagine è ora disponibile nell'elenco delle immagini che può essere utilizzato per configurare le nuove macchine virtuali. È possibile visualizzarla con il comando:

    `azure vm image list`

    Nel [portale classica Azure] [], viene visualizzato nell'elenco **immagini** .

    ![Acquisizione di immagini completato](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Passaggi successivi
L'immagine è pronto per essere utilizzato per creare macchine virtuali. È possibile utilizzare il comando CLI Azure `azure vm create` e specificare il nome dell'immagine è stato creato. Per informazioni dettagliate sul comando, vedere [utilizzo CLI Azure con il modello di distribuzione classica](../virtual-machines-command-line-tools.md) . In alternativa, è possibile utilizzare il [portale classica Azure] [] per creare una macchina virtuale personalizzata utilizzando il metodo **Dalla raccolta** e selezionare l'immagine che è stato creato. Per ulteriori informazioni, vedere [come creare una macchina virtuale personalizzata] [] .

**Vedere anche:** [Guida di Azure Linux agente utente](virtual-machines-linux-agent-user-guide.md)

[Portale classica Azure]: http://manage.windowsazure.com
[Informazioni sulle immagini macchina virtuale in Azure]: virtual-machines-linux-classic-about-images.md
[Come creare una macchina virtuale personalizzata]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Come creare una macchina virtuale Linux]: virtual-machines-linux-classic-create-custom.md
