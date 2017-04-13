Per creare un VNet nel modello di distribuzione Manager delle risorse tramite il portale di Azure, seguire la procedura seguente. Le immagini sono illustrate alcuni esempi. Assicurarsi di sostituire i valori con uno personalizzato. Per ulteriori informazioni sull'uso di reti virtuali, vedere [Panoramica di rete virtuale](../articles/virtual-network/virtual-networks-overview.md).

1. Da un browser passare al [portale di Azure](http://portal.azure.com) e, se necessario, accedere con l'account Azure.

2. Fare clic su **Nuovo**. Nel campo di **ricerca di marketplace** , digitare "Virtuali". Fare clic per aprire e il **Virtuali** individuare **Virtuali** dall'elenco restituito.

    ![Individuare virtuali blade di risorse] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Blade risorse virtuali di ricerca")

3. Nella parte inferiore della stessa e virtuali, nell'elenco **Selezionare un modello di distribuzione** selezionare **Manager delle risorse**e quindi fare clic su **Crea**.


    ![Selezionare Gestione risorse] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Selezionare Gestione risorse")

4. Nella e **virtuali creare** , configurare le impostazioni di VNet. Quando si riempie nei campi, il punto esclamativo rosso diventa un segno di spunta verde quando sono validi i caratteri immessi nel campo.

    ![Convalida dei campi] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Convalida dei campi")

5. E il **Crea rete virtuale** è simile all'esempio seguente. È possibile che i valori che sono riempimento automatico. In caso affermativo, sostituire i valori con uno personalizzato.

    ![Blade virtuali crea] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Blade virtuali crea")

6. **Nome**: immettere il nome per la rete virtuale.

7. **Spazio di indirizzi**: immettere lo spazio di indirizzi. Se si dispone di più spazi degli indirizzi per aggiungere, aggiungere il primo spazio di indirizzi. È possibile aggiungere gli spazi indirizzo aggiuntivo in un secondo momento, dopo aver creato il VNet.
 
8. **Nome subnet**: aggiungere il nome di subnet e subnet indirizzi di intervalli. È possibile aggiungere subnet aggiuntive in un secondo momento, dopo aver creato il VNet.

10. **Abbonamento**: verificare che l'abbonamento elencato sia quello corretto. È possibile modificare le sottoscrizioni utilizzando l'elenco a discesa.

11. **Gruppo risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo, digitare un nome per il nuovo gruppo di risorse. Se si sta creando un nuovo gruppo, assegnare un nome al gruppo di risorse in base ai valori configurazione pianificata. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](resource-group-overview.md#resource-groups).

12. **Posizione**: selezionare il percorso per il VNet. La posizione determina in cui verranno archiviato risorse che si distribuiscono questo VNet.

13. Selezionare **Aggiungi a dashboard** se si desidera essere in grado di trovare facilmente il VNet nel dashboard e quindi fare clic su **Crea**.
    
    ![Aggiungi a dashboard] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "Aggiungi a dashboard")

14. Dopo aver fatto clic su **Crea**, si vedrà un riquadro nel dashboard che rifletteranno lo stato di avanzamento del VNet. Il riquadro cambia mentre viene creato il VNet.

    ![Creazione virtuali affiancate] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Creazione virtuali affiancate")