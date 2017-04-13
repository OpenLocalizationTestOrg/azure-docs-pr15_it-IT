## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Come creare un VNet nel portale di Azure

Per creare un VNet in base allo scenario sopra, seguire la procedura seguente.

1. In un browser, passare a http://manage.windowsazure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Nuovo** > **Servizi di rete** > **VIRTUALI** > **Creare personalizzato** come illustrato nella figura seguente.

    ![Creare VNet nel portale](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Nella pagina **Dettagli sulla rete virtuale** , digitare il **nome** della VNet, scegliere la **posizione**e quindi fare clic sulla freccia nell'angolo inferiore destro della pagina per passare al passaggio 2. La figura seguente mostra le impostazioni per questo scenario.

    ![Pagina dei dettagli virtuali](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Nella pagina **DNS e la connettività VPN** specificare il nome e indirizzo IP fino a 9 server DNS da utilizzare. Se non si specifica un server DNS, il VNet utilizzerà la risoluzione di risoluzione dei nomi interno fornita da Azure. Per questo scenario, non verrà configurato i server DNS.
5. Se si desidera fornire accesso VPN punto al sito per il VNet, attivare la casella di controllo **Configura una connessione VPN punto al sito** . Se non si configura una connessione VPN punto al sito, è possibile aggiungere per il VNet in qualsiasi momento dopo la creazione. Per questo scenario, non verrà configurato una connessione VPN punto al sito.
6. Se si desidera fornire la connettività VPN da sito tra il VNet e un'altra VNet o la rete locale, attivare la casella di controllo **Configura una connessione VPN da sito** e specificare se si desidera utilizzare per connettersi a **ExpressRoute** o nota e il nome della rete. Se non si configura una connessione VPN da sito, è possibile aggiungere per il VNet in qualsiasi momento dopo la creazione. Per questo scenario, non verrà configurato una connessione VPN da sito.
7. Fare clic sulla freccia nell'angolo inferiore destra della pagina per passare al passaggio 3 che nella figura seguente mostra le impostazioni per questo scenario.

    ![Pagina di connettività server DNS e VPN](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Nella pagina **Virtuale spazi degli indirizzi di rete** , in **IP iniziale**, fare clic su *10.0.0.0* per modificare lo spazio di indirizzi VNet e quindi digitare lo spazio indirizzo iniziale che si desidera utilizzare. Per questo scenario, digitare *192.168.0.0*. 
9. Selezionare il numero di bit per la maschera subnet **CIDR (conteggio indirizzo)** . Per questo scenario, selezionare *16 (65536)*.
10. In **subnet**, fare clic su *Subnet 1* e rinominare la subnet, se necessario. Per questo scenario, rinominarla *front-end*.

    >[AZURE.NOTE] Se si fa clic all'esterno di casella di testo nome per una subnet non sarà possibile modificare il nome se alla subnet. Per correggere questo errore, è necessario rimuovere subnet facendo clic sul pulsante X a destra, quindi aggiungere una nuova subnet, come descritto nel passaggio 13 sotto.

11. In **IP iniziale** per la prima subnet, specificare l'indirizzo IP iniziale per la subnet. Per questo scenario, digitare *192.168.1.0*.
12. Selezionare il numero di bit per la maschera subnet per la prima subnet **CIDR (conteggio indirizzo)** . Per questo scenario, selezionare *24 (256)*.
13. Fare clic su **Aggiungi subnet** per aggiungere una nuova subnet, se necessario. Per questo scenario, aggiungere una subnet e ripetere i passaggi 10-12 per configurare VNet come illustrato nella figura seguente.

    ![Pagina di spazi di indirizzi virtuali](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Fare clic sul pulsante segno di spunta nell'angolo inferiore destra della pagina per creare il VNet. Dopo alcuni secondi la VNet verrà visualizzato nell'elenco dei VNets disponibili, come illustrato nella figura seguente.

    ![Nuova rete virtuale](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)