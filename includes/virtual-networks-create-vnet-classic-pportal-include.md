## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Come creare un VNet classica nel portale di Azure

Per creare un VNet classica in base allo scenario sopra, seguire la procedura seguente.

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Nuovo** > **rete** > **rete virtuale**si noti che l'elenco **Selezionare un modello di distribuzione** è già visualizzato **classica**e quindi fare clic su **Crea**, come illustrato nella figura seguente.

    ![Creare VNet nel portale di Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Scegliere e il **rete virtuale** , digitare il **nome** della VNet e quindi fare clic su **spazio di indirizzi**. Configurare le impostazioni di spazio indirizzo per la VNet e la prima subnet, quindi fare clic su **OK**. La figura seguente mostra le impostazioni di blocco CIDR per questo scenario.

    ![Blade di spazio di indirizzi](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse per aggiungere VNet a o fare clic su **Crea nuovo gruppo di risorse** per aggiungere il VNet a un nuovo gruppo di risorse. La figura seguente mostra la risorsa impostazioni dei gruppi per un nuovo gruppo di risorse denominato **TestRG**. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Creare blade gruppo risorse](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Se necessario, modificare le impostazioni di **sottoscrizione** e un **percorso** per il VNet. 

6. Se non si desidera visualizzare il VNet come un riquadro in **Startboard**, disattivare **Pin per Startboard**. 

7. Fare clic su **Crea** e notare il riquadro denominato **virtuali creazione** , come illustrato nella figura seguente.

    ![Creare VNet nel portale](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Attendere che VNet la creazione e quando viene visualizzato il riquadro riportata di seguito, fare clic per aggiungere altre subnet.

    ![Creare VNet nel portale](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Verrà visualizzata la **configurazione** per il VNet, come illustrato di seguito. 

    ![Creare VNet nel portale](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Fare clic su **subnet** > **Aggiungi**, quindi digitare un **nome** e specificare un **intervallo di indirizzi (blocco CIDR)** per subnet e quindi fare clic su **OK**. La figura seguente mostra le impostazioni per lo scenario corrente.

    ![Creare VNet nel portale di Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)