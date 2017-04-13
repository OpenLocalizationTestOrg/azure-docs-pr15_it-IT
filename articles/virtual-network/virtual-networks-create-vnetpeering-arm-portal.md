<properties
   pageTitle="Creare VNet Peering tramite il portale di Azure | Microsoft Azure"
   description="Informazioni su come creare una rete virtuale tramite il portale di Azure in Gestione risorse."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Creare una rete virtuale peering tramite il portale di Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un VNet peering basata sullo scenario sopra tramite il portale di Azure, eseguire la procedura seguente.

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Per stabilire VNET peering, è necessario creare due collegamenti, uno per ogni direzione tra due VNets. È possibile creare innanzitutto collegamento di peering VNET per VNET1 a VNET2. Nel portale, fare clic su **Sfoglia** > **scegliere reti virtuali**

    ![Creare VNet peering nel portale di Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. In blade reti virtuali, scegliere VNET1, fare clic su Peerings, quindi fare clic su Aggiungi

    ![Scegliere peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. In e aggiungere Peering l'assegnare un nome di collegamento peering LinkToVnet2, scegliere l'abbonamento e il peer virtuale VNET2 di rete, fare clic su OK.

    ![Collegamento a VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Dopo aver creato il collegamento di peering VNET. È possibile visualizzare lo stato del collegamento come segue:

    ![Lo stato dei collegamenti](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Creare il collegamento di peering VNET per VNET2 a VNET1. In blade reti virtuali, scegliere VNET2, fare clic su Peerings, quindi fare clic su Aggiungi

    ![Peer da altri VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. In e l'aggiungere Peering, assegnare un nome di collegamento peering LinkToVnet1, scegliere la sottoscrizione e peer virtuali, fare clic su OK.

    ![Creazione di riquadro virtuali](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Dopo aver creato il collegamento di peering VNET. È possibile visualizzare lo stato del collegamento come segue:

    ![Stato dei collegamenti finale](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Controllare lo stato per LinkToVnet2 o ora impostato su connesso anche.  

    ![Stato dei collegamenti finale 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] VNET peering viene stabilito solo se entrambi i collegamenti sono connessi.

Esistono alcune proprietà configurabili per ogni collegamento:

|Opzione|Descrizione|Impostazione predefinita|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Spazio di VNet Peer devono essere inclusi come parte del Tag Virtual_network se indirizzo|Sì|
|AllowForwardedTraffic|Se il traffico non provenienti da un VNet peered viene accettato o eliminato|No|
|AllowGatewayTransit|Consente al peer VNet verrà utilizzato il gateway VNet|No|
|UseRemoteGateways|Usare gateway VNet del peer. Peer VNet deve avere configurato un gateway e AllowGatewayTransit sia selezionata. È possibile utilizzare questa opzione se si è configurato un gateway|No|

Ogni collegamento VNet peering ha un insieme di proprietà di sopra. Dal portale, fare clic sul collegamento di Peering VNet e modificare le opzioni disponibili, fare clic su Salva per verificare l'effetto di modifica.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. In questo esempio verrà usata due sottoscrizioni A e B e due utenti successivamente e potrebbero verificarsi con privilegi per gli abbonamenti rispettivamente
3. Nel portale, fare clic su Sfoglia, scegliere reti virtuali. Fare clic sul VNET e fare clic su Aggiungi.

    ![Scenario 2 Sfoglia](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. Scegliere e l'accesso Aggiungi, selezionare un ruolo e scegliere rete collaboratori, fare clic su aggiungere utenti, digitare il simbolo potrebbero verificarsi in nome e fare clic su OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Non si tratta di un requisito peering è possibile stabilire anche se gli utenti singolarmente generano peering richieste per i rispettivi Vnets come le richieste corrispondono. Aggiunta utente con privilegi di altri VNet come utenti nel VNet locale, è facile installazione nel portale.

5. Quindi accedere al portale Azure con potrebbero verificarsi utente privilegi per SubscriptionB. Seguire sopra i passaggi per aggiungere l'utente come collaboratori di rete.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] È possibile disconnettersi e accedere a entrambe le sessioni utente nel browser per garantire che l'autorizzazione è configurata correttamente.

6. Accesso al portale come utente, passare a e il VNET3, fare clic su Peering, selezionare ' verificare che l'ID risorsa "casella di controllo e digitare la risorsa ID per VNET5 in formato.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![ID risorsa](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Accedere al portale di come utente b e seguire sopra passaggio per creare un collegamento peering da VNET5 a VNet3.

    ![ID risorsa 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Verranno stabilite peering e qualsiasi macchina virtuale nel VNet3 dovrebbe essere possibile comunicare con qualsiasi macchina virtuale nel VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Come primo passaggio, collegamenti peering VNET da HubVnet a VNET1. Si noti che consentire il traffico inoltrato non è impostato per il collegamento.

    ![Peering base](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Passaggio successivo, è possono creare collegamenti peering da VNET1 a HubVnet. Si noti che sia selezionata l'opzione di traffico Consenti inoltro delle chiamate.

    ![Peering base](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Dopo avere stabilito peering, è possibile fare riferimento al presente [articolo](virtual-network-create-udr-arm-ps.md) e definire Route(UDR) definiti dall'utente per reindirizzare il traffico VNet1 tramite un dispositivo virtuale per usare le funzionalità. Quando si specifica l'indirizzo Hop successivo nella route, è possibile impostare l'indirizzo IP del dispositivo virtuale in peer VNet HubVNet


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.

2. Per stabilire VNET peering in questo scenario, è necessario creare solo un collegamento dalla rete virtuale in Gestione risorse Azure a quella classica. Vale a dire da **VNET1** a **VNET2**. Nel portale, fare clic su **Sfoglia** > scegliere **Reti virtuali**

3. Scegliere **VNET1**e il reti virtuali. Fare clic su **Peerings**, quindi fare clic su **Aggiungi**.

4. Nella finestra e aggiungere Peering nome del collegamento. Di seguito viene chiamato **LinkToVNet2**. In dettagli Peer selezionare **classica**.

5. Scegliere la sottoscrizione e peer virtuali **VNET2**. Fare clic su OK.

    ![Collegamento Vnet1 a Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Dopo aver creato il collegamento di peering VNet, le reti virtuali due sono peered e sarà possibile vedere le risorse seguenti:

    ![Verifica della connessione peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Rimuovere VNet Peering

1.  In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2.  Passare alla blade virtuali, fare clic su Peerings, fare clic sul collegamento che si desidera rimuovere, fare clic sul pulsante Elimina.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Quando si rimuove un collegamento VNET peering, lo stato del collegamento peer andrà alla disconnesso.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. In questo stato è non è possibile ricreare il collegamento fino a quando non viene modificato lo stato di collegamento peer a segnalazione. È consigliabile che rimuovere i collegamenti di entrambi prima di ricreare la VNET peering.
