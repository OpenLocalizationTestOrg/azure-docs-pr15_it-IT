<properties 
   pageTitle="Come impostare un indirizzo IP privato statico in modalità classica tramite il portale di Azure | Microsoft Azure"
   description="Informazioni sulle statico IP privato e come possono essere gestiti in modalità classica tramite il portale di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Come impostare un indirizzo IP statico privato (classica) nel portale di Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione Manager delle risorse](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente si aspettano un ambiente semplice già stato creato. Se si desidera eseguire la procedura indicata in questo documento, creare innanzitutto l'ambiente di testing descritto in [creazione di un vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale
Per creare una macchina virtuale denominata *DNS01* in subnet *front-end* di un VNet denominato *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Nuovo** > **calcolare** > **Data Center di Windows Server 2012 R2**si noti che l'elenco **Selezionare un modello di distribuzione** è già visualizzato **classica**e quindi fare clic su **Crea**.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. In e il **Creare macchine Virtuali** , immettere il nome della macchina virtuale per creare (*DNS01* in questo scenario), l'account dell'amministratore locale e la password.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Fare clic su **Configurazione facoltativa** > **rete** > **Virtuali**, quindi fare clic su **TestVNet**. Se **TestVNet** non è disponibile, verificare che si sta usando la posizione *Centrale USA* e aver creato l'ambiente di testing descritto all'inizio di questo articolo.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. In e il **rete** , assicurarsi che la subnet attualmente selezionata è *front-end*, quindi fare clic su **indirizzi IP**, selezionare **Assegnazione indirizzo IP** **statico**e quindi immettere *192.168.1.101* per **Indirizzo IP** come indicato di seguito.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Fare clic su **OK** in e **l'indirizzi IP** , quindi fare clic su **OK** in e il **rete** e fare clic su **OK** in e il **config facoltativo** .
7. In e il **Creare macchine Virtuali** , fare clic su **Crea**. Si noti sotto il riquadro visualizzato nel dashboard.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare statico privato indirizzo IP per una macchina virtuale

Per visualizzare le informazioni di indirizzo IP private statiche per la macchina virtuale creata con la procedura indicata sopra, eseguire la procedura seguente.

1. Dal portale di Azure Azure, fare clic su **Esplora tutto** > **(classica) macchine virtuali di** > **DNS01** > **tutte le impostazioni** > **indirizzi IP** e osservare l'assegnazione di indirizzi IP e indirizzi IP come indicato di seguito.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato da macchine Virtuali creato in precedenza, seguire la procedura seguente.
    
1. Da e **l'indirizzi IP** sopra, fare clic su **dinamico** a destra dell' **assegnazione di indirizzi IP**, quindi fare clic su **Salva**e quindi fare clic su **Sì**.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato a macchine Virtuali creato mediante la procedura riportata sopra, seguire la procedura seguente:

1. Da e **l'indirizzi IP** sopra, fare clic su **statica** a destra **dell'assegnazione di indirizzi IP**.
2. Digitare *192.168.1.101* per **indirizzo IP**, quindi fare clic su **Salva**e quindi fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli indirizzi [IP pubblico riservato](virtual-networks-reserved-public-ip.md) .
- Informazioni sugli indirizzi [IP pubblico (ILPIP) a livello di istanza](virtual-networks-instance-level-public-ip.md) .
- Consultare le [API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).