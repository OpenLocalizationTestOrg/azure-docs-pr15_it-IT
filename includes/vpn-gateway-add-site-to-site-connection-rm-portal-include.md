1. Individuare il gateway virtuali e fare clic su **tutte le impostazioni** per aprire e **l'Impostazioni** .

2. Scegliere e **l'Impostazioni** , fare clic su **connessioni**e quindi fare clic su **Aggiungi** nella parte superiore e il per aprire e **l'Aggiungi connessione** .

    ![Creare una connessione a siti](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. In e il **componente connessione** **nome** la connessione. 

4. Per **tipo di connessione**, selezionare **Site-to-site(IPSec)**.

5. Per **il gateway di rete virtuali**, il valore è fisso perché si esegue la connessione da questo gateway.

6. Per **il gateway di rete locale**, fare clic su **Scegli un gateway di rete locale** e selezionare il gateway di rete locale che si desidera utilizzare. 

7. Per **Chiave condivisa**, il valore deve corrispondere al valore che si sta utilizzando del dispositivo VPN locale. Se il dispositivo VPN sulla rete locale non fornisce una chiave condivisa, è possibile creare uno e immetterlo qui e nel dispositivo locale. Importante è che entrambe corrispondano.

8. I valori rimanenti per la **sottoscrizione**, **Gruppo di risorse**e **posizione** sono corretti.

9. Fare clic su **OK** per creare la connessione. Verrà visualizzata *La creazione di connessione* flash sullo schermo.

10. Una volta completata la connessione, è necessario visualizzarlo vengono visualizzati in e il **connessioni** per il Gateway.

    ![Creare una connessione a siti](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

