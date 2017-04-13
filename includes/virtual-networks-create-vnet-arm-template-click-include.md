## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Distribuire il modello ARM tramite fare clic su per la distribuzione

È possibile riutilizzare carica di modelli ARM predefinito in un archivio github gestito da Microsoft e aprire alla community di. Questi modelli possono essere distribuiti direttamente dalla github, o scaricati e modificati secondo le proprie esigenze. Per distribuire un modello che crea una VNet con due subnet, eseguire la procedura seguente.

1. Da un browser passare al [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Scorrere verso il basso l'elenco di modelli e fare clic su **101-vnet-2-subnet**. Selezionare il file **README.md** , come illustrato di seguito.

    ![File READEME.md in github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Fare clic su **Distribuisci in Azure**. Se necessario, immettere le credenziali di accesso Azure. 
4. In e il **parametri** , immettere i valori che si desidera utilizzare per creare il nuovo VNet e quindi fare clic su **OK**. La figura seguente mostra i valori per questo scenario.

    ![Parametri del modello ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Fare clic su **gruppo di risorse** e selezionare un gruppo di risorse per aggiungere VNet a o fare clic su **Crea nuovo** per aggiungere il VNet a un nuovo gruppo di risorse. La figura seguente mostra la risorsa impostazioni dei gruppi per un nuovo gruppo di risorse denominato **TestRG**.

    ![Gruppo di risorse](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Se necessario, modificare le impostazioni di **sottoscrizione** e un **percorso** per il VNet.
6. Se non si desidera visualizzare il VNet come un riquadro in **Startboard**, disattivare **Pin per Startboard**.
5. Fare clic su **Leagl termini**, leggere le condizioni e fare clic su **Acquista** per accettare. 
6. Fare clic su **Crea** per creare il VNet.

    ![Riquadro l'invio di distribuzione nel portale di anteprima](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Una volta completata la distribuzione, fare clic su **TestVNet** > **tutte le impostazioni** > **subnet** per visualizzare le proprietà di subnet, come illustrato di seguito.

    ![Creare VNet nel portale di anteprima](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)