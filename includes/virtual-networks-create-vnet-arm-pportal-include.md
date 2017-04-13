## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Come creare un VNet nel portale di Azure

Per creare un VNet in base allo scenario sopra tramite il portale di Azure anteprima, eseguire la procedura seguente.

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Nuovo** > **rete** > **rete virtuale**, quindi fare clic su **Gestione risorse** dall'elenco **Selezionare un modello di distribuzione** e quindi fare clic su **Crea**, come illustrato nella figura seguente.

    ![Creare VNet nel portale di Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. Nella e **virtuali creare** , configurare le impostazioni di VNet come illustrato nella figura seguente.

    ![Creare blade virtuali](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Fare clic su **gruppo di risorse** e selezionare un gruppo di risorse per aggiungere VNet a o fare clic su **Crea nuovo** per aggiungere il VNet a un nuovo gruppo di risorse. La figura seguente mostra la risorsa impostazioni dei gruppi per un nuovo gruppo di risorse denominato **TestRG**. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](../articles/resource-group-overview.md#resource-groups).

    ![Gruppo di risorse](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Se necessario, modificare le impostazioni di **sottoscrizione** e un **percorso** per il VNet. 

6. Se non si desidera visualizzare il VNet come un riquadro in **Startboard**, disattivare **Pin per Startboard**. 

7. Fare clic su **Crea** e notare il riquadro denominato **virtuali creazione** , come illustrato nella figura seguente.

    ![Creazione di riquadro virtuali](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Attendere VNet da creare, quindi selezionare **tutte le impostazioni**e il **rete virtuale**  > **subnet** > **Aggiungi** come indicato di seguito.

    ![Aggiunta di subnet nel portale di Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Specificare le impostazioni di subnet per subnet *back-end* , come illustrato di seguito e quindi fare clic su **OK**. 

    ![Impostazioni subnet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Viene visualizzato l'elenco di subnet, come illustrato nella figura seguente.

    ![Elenco di subnet nel VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)
