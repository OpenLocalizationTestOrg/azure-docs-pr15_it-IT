<properties
    pageTitle="Servizi di dominio Active Directory Azure: Creare o selezionare una rete virtuale | Microsoft Azure"
    description="Guida introduttiva di servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Creare o selezionare una rete virtuale per i servizi di dominio Active Directory di Azure

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Linee guida per selezionare una rete virtuale Azure
> [AZURE.NOTE] **Prima di iniziare**: fare riferimento alla [rete considerazioni per i servizi di dominio di Azure Active Directory](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Attività 2: Creare una rete virtuale Azure
L'attività di configurazione successivo consiste nel creare una rete virtuale Azure e una subnet all'interno di essa. Attivare servizi di dominio Active Directory di Azure subnet all'interno della rete virtuale. Se si dispone già di una rete virtuale esistente che si preferisce utilizzare, è possibile ignorare questo passaggio.

> [AZURE.NOTE] Assicurarsi che la rete virtuale Azure si creano o si sceglie di utilizzare con i servizi di dominio Active Directory Azure appartiene a un'area di Azure supportate da servizi di dominio Active Directory Azure. Vedere la pagina [servizi Azure dall'area](https://azure.microsoft.com/regions/#services/) fonti di informazioni sulle aree di Azure in cui è disponibili Azure servizi di dominio Active Directory.

Nota verso il basso il nome della rete virtuale in modo da selezionare destra virtuali quando si abilita servizi di dominio Active Directory di Azure in un passaggio di configurazione successivi.

Effettuare le seguenti operazioni di configurazione per creare una rete virtuale Azure in cui si desidera attivare servizi di dominio Active Directory Azure.

1. Passare al **portale classica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selezionare il nodo **reti** nel riquadro sinistro.

    ![Nodo reti](./media/active-directory-domain-services-getting-started/networks-node.png)

3. Nel riquadro attività nella parte inferiore della pagina, fare clic su **Nuovo** .

    ![Nodo reti virtuali](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Il nodo di **Servizi di rete** , selezionare **Rete virtuale**.

5. Fare clic su **Creazione rapida** per creare una rete virtuale.

    ![Creare virtuali - rapido](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Specificare un **nome** per la rete virtuale. È anche possibile configurare lo **spazio indirizzo** o il **numero massimo macchine Virtuali** per la rete. È possibile lasciare le impostazioni del **server DNS** impostata su 'Nessuno' per ora. È possibile aggiornare il server DNS impostazione dopo il dominio di Azure Active Directory attiva servizi.

7. Assicurarsi di selezionare un'area di Azure supportata nell'elenco a discesa **posizione** . Vedere la pagina [servizi Azure dall'area](https://azure.microsoft.com/regions/#services/) fonti di informazioni sulle aree di Azure in cui è disponibili Azure servizi di dominio Active Directory.

8. Per creare la rete virtuale, fare clic sul pulsante **Crea una rete virtuale** .

    ![Creare una rete virtuale per i servizi di dominio di Azure Active Directory.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Dopo aver creata la rete virtuale, selezionare la rete virtuale e fare clic sulla scheda **Configura** .

    ![Creare una subnet](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Passare alla sezione **spazi di indirizzi virtuali** . Fare clic su **Aggiungi subnet** e specificare una subnet con il nome **AaddsSubnet**. Fare clic su **Salva** per creare la subnet.

    ![Creare una subnet di servizi di dominio Active Directory Azure.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Attività 3 - Abilita i servizi di dominio Azure
L'attività di configurazione successivo è per [attivare servizi di dominio Active Directory Azure](active-directory-ds-getting-started-enableaadds.md).
