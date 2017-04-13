<properties
    pageTitle="Creare una scala di macchina virtuale impostata tramite il portale di Azure | Microsoft Azure"
    description="Distribuire insiemi scala Azure nel portale."
    keywords="set di scala macchina virtuale" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Creare una scala di macchina virtuale impostata tramite il portale di Azure

Questa esercitazione viene illustrato come quanto sia semplice creare un Set di scala macchina virtuale in pochi minuti, tramite il portale di Azure. Se non si dispone di un abbonamento a Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Scegliere l'immagine di macchine Virtuali di Marketplace

Dal portale, è possibile distribuire facilmente una scala impostare con CentOS, CoreOS, Debian, Suse aperta, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu Server o le immagini di Windows Server.

Prima di tutto, passare al [portale di Azure](https://portal.azure.com) in un web browser. Fare clic su `New`, cercare `scale set`e quindi selezionare il `Virtual machine scale set` voce:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Creare la macchina virtuale Linux

È ora possibile utilizzare le impostazioni predefinite e creare rapidamente la macchina virtuale.

* Nel `Basics` blade, immettere un nome per il set di scala. Questo nome diventa la base del nome di dominio completo di bilanciamento del carico che precede il set di scala, dunque, verificare che il nome sia univoco tra tutti Azure.

* Selezionare il sistema operativo desiderato digitare, immettere il nome utente desiderato e seleziona autenticazione digitare si preferisce. Se si sceglie una password, deve essere almeno 12 caratteri lungo e tre i seguenti requisiti di complessità quattro soddisfano: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Vedere informazioni sui [requisiti di nome utente e password](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Se si sceglie `SSH public key`, assicurarsi di solo Incolla nella chiave pubblica, non la chiave privata:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Immettere il nome del gruppo di risorse desiderato e la posizione e quindi fare clic su `OK`.

* Nel `Virtual machine scale set service settings` blade: immettere l'etichetta di nome di dominio desiderato (base del FQDN di bilanciamento del carico che precede il set di scala). L'etichetta deve essere univoca in tutti Azure.

* Scegliere l'immagine del disco di sistema operativo desiderato, conteggio delle istanze e le dimensioni del computer.

* Attivare o disattivare autoscale e configurare se attivato:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Nel `Summary` blade, quando la convalida viene eseguita, fare clic su `OK`.

* Infine, nel `Purchase` blade, fare clic su `Purchase` per avviare la scala del set di distribuzione.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Connettersi a una macchina virtuale nel set di scala

Una volta distribuito set di scala, passare al `Inbound NAT Rules` scheda di bilanciamento del carico per il set di scala:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

È possibile connettersi a ogni macchina virtuale in scala set mediante le regole NAT. Ad esempio, per un set di scala di Windows, se esiste una regola NAT nella posta in arrivo porta 50000, è Impossibile connettersi al computer tramite RDP dal `<load-balancer-ip-address>:50000`. Per un set di scala Linux, si connette con il comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire i gruppi di scala di CLI, vedere [la documentazione](./virtual-machine-scale-sets-cli-quick-create.md).

Per informazioni su come distribuire i gruppi di scala di PowerShell, vedere [la documentazione](./virtual-machine-scale-sets-windows-create.md).

Per informazioni su come distribuire insiemi di scala da Visual Studio, vedere [la documentazione](./virtual-machine-scale-sets-vs-create.md).

Per la documentazione generale, estrarre [Imposta pagina Panoramica della documentazione per scala](./virtual-machine-scale-sets-overview.md).

Per informazioni generali, visitare la [pagina principale per i set di scala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

