<properties 
   pageTitle="Come impostare un indirizzo IP privato statico in modalità ARM tramite il portale di Azure | Microsoft Azure"
   description="Informazioni sulle IP privato (DIP) e come possono essere gestiti in modalità ARM tramite il portale di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Come impostare un indirizzo IP statico privato nel portale di Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È inoltre possibile [gestire l'indirizzo IP statico privato nel modello di distribuzione classica](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente si aspettano un ambiente semplice già stato creato. Se si desidera eseguire la procedura indicata in questo documento, creare innanzitutto l'ambiente di testing descritto in [creazione di un vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Come creare una macchina virtuale per la verifica statici indirizzi IP privati

È possibile impostare un indirizzo IP statico privato durante la creazione di una macchina virtuale in modalità di distribuzione Manager delle risorse tramite il portale di Azure. È necessario creare la macchina virtuale prima di tutto, quindi impostare il relativo privato IP statico.

Per creare una macchina virtuale denominata *DNS01* in subnet *front-end* di un VNet denominato *TestVNet*, seguire la procedura seguente.

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Nuovo** > **calcolare** > **Data Center di Windows Server 2012 R2**si noti che l'elenco **Selezionare un modello di distribuzione** **Manager delle risorse**è già visualizzato e quindi fare clic su **Crea**, come illustrato nella figura seguente.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. In e il **Nozioni di base** , immettere il nome della macchina virtuale per creare (*DNS01* in questo scenario), l'account dell'amministratore locale e la password, come illustrato nella figura seguente.

    ![Blade nozioni di base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Verificare che il **percorso** selezionato sia *Centrale USA*, quindi fare clic su **Seleziona esistente** nel **gruppo di risorse**, quindi nuovamente su **gruppo di risorse** , quindi fare clic su *TestRG*e fare clic su **OK**.

    ![Blade nozioni di base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. In e lo **scegliere una dimensione** , selezionare **Standard A1**e quindi fare clic su **Seleziona**.

    ![Scegliere una pala dimensioni](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. In e **l'Impostazioni** , assicurarsi che siano impostate le proprietà seguenti siano impostati con i valori seguenti e quindi fare clic su **OK**.

    -**Account di archiviazione**: *vnetstorage*
    - **Rete**: *TestVNet*
    - **Subnet**: *front-end*

    ![Scegliere una pala dimensioni](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. In e il **Riepilogo** , fare clic su **OK**. Si noti sotto il riquadro visualizzato nel dashboard.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare statico privato indirizzo IP per una macchina virtuale

Per visualizzare le informazioni di indirizzo IP private statiche per la macchina virtuale creata con la procedura indicata sopra, eseguire la procedura seguente.

1. Dal portale di Azure Azure, fare clic su **Esplora tutto** > **macchine virtuali** > **DNS01** > **tutte le impostazioni** > **interfacce di rete** e quindi fare clic su nell'interfaccia di rete solo elencati.

    ![Riquadro macchine Virtuali di distribuzione](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Selezionare **tutte le impostazioni**e **l'interfaccia di rete**  > **indirizzi IP** e notare i valori di **un'assegnazione** e **l'indirizzo IP** .

    ![Riquadro macchine Virtuali di distribuzione](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato a macchine Virtuali creato mediante la procedura riportata sopra, seguire la procedura seguente:

1. Da e **l'indirizzi IP** sopra, fare clic su **statico** in **un'assegnazione**.
2. Digitare *192.168.1.101* per **indirizzo IP**e quindi fare clic su **Salva**.

    ![Creare macchine Virtuali nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Se dopo avere fatto clic su **Salva** si noterà che l'assegnazione è ancora impostato su **dinamico**, significa che l'indirizzo IP digitato è già in uso. Provare a un indirizzo IP diverso.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato da macchine Virtuali creato in precedenza, seguire la procedura riportata di seguito.
    
1. Da e **l'indirizzi IP** sopra, fare clic su **dinamica** in **assegnazione**e quindi fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli indirizzi [IP pubblico riservato](virtual-networks-reserved-public-ip.md) .
- Informazioni sugli indirizzi [IP pubblico (ILPIP) a livello di istanza](virtual-networks-instance-level-public-ip.md) .
- Consultare le [API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).