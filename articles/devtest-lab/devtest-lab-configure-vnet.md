<properties
    pageTitle="Configurare una rete virtuale in Azure DevTest esercitazioni | Microsoft Azure"
    description="Informazioni su come configurare una rete virtuale esistente e subnet e usarli in una macchina virtuale con Azure DevTest esercitazioni"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="tarcher"/>

# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurare una rete virtuale in Azure DevTest esercitazioni

Come illustrato nell'articolo, [aggiungere una macchina virtuale con gli elementi da un ambiente di lavoro](devtest-lab-add-vm-with-artifacts.md), quando si crea una macchina virtuale in un ambiente di lavoro, è possibile specificare una rete virtuale configurata. Uno scenario per eseguire questa operazione è se è necessario accedere alle risorse di corpnet nelle macchine virtuali tramite la rete virtuale che è stata configurata con ExpressRoute o VPN da sito. Nelle sezioni seguenti viene illustrato come aggiungere rete virtuale esistente nelle impostazioni di rete virtuale del laboratorio in modo che sia disponibile al momento della creazione di macchine virtuali.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurare una rete virtuale per un ambiente di lavoro tramite il portale di Azure
Nella procedura seguente viene aggiunta una rete virtuale esistente (subnet e) per un ambiente di lavoro in modo che può essere utilizzato durante la creazione di una macchina virtuale nello stesso laboratorio. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi**e quindi selezionare **Esercitazioni DevTest** dall'elenco.

1. Selezionare il laboratorio desiderato dall'elenco di esercitazioni. 

1. In blade del laboratorio, selezionare **la configurazione**.

1. Blade di **configurazione** dell'ambiente di test, selezionare **reti virtuali**.

1. In e il **reti virtuali** è visualizzato un elenco delle reti virtuali configurato per l'esercitazione corrente, nonché la rete virtuale predefinita creata per l'ambiente. 

1. Selezionare **+ Aggiungi**.

    ![Aggiungere una rete virtuale esistente per l'esercitazione](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
    
1. Scegliere **[selezionare virtuali]**e il **rete virtuale** .

    ![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
    
1. Scegliere la rete virtuale desiderata e il **virtuali Scegli** . E il Mostra tutte le reti virtuali sotto l'area stessa nella sottoscrizione come laboratorio.  

1. Dopo aver selezionato una rete virtuale, vengono restituiti a e il **rete virtuale** e campi diversi vengono abilitati.  

    ![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Specificare una descrizione per la rete virtuale / combinazione laboratorio.

1. Per consentire una subnet da utilizzare in laboratorio la creazione di macchine Virtuali, selezionare **La creazione di macchina virtuale IN uso**.

1. Per consentire indirizzi IP in una subnet, selezionare **Consenti IP pubblico**.

1. Nel campo **Massimo macchine VIRTUALI PER utente** , specificare macchine virtuali massime per ogni utente per ogni subnet. Se si desidera un numero illimitato di macchine virtuali, lasciare vuoto questo campo.

1. Selezionare **Salva**.

1. Dopo avere configurato la rete virtuale, possono essere selezionato durante la creazione di una macchina virtuale. Per informazioni su come creare una macchina virtuale e specificare una rete virtuale, consultare l'articolo [aggiungere una macchina virtuale con gli elementi da un ambiente di lavoro](devtest-lab-add-vm-with-artifacts.md). 

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto la rete virtuale desiderata per l'ambiente, il passaggio successivo consiste [nell'aggiungere una macchina virtuale per l'esercitazione](devtest-lab-add-vm-with-artifacts.md).