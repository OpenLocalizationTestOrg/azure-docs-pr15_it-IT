1. Nel portale, passare al **Nuovo**. Digitare "Gateway rete virtuale" nella ricerca. Individuare **il gateway di rete virtuale** nella ricerca mittente e fare clic sulla voce. Verrà aperta e il **gateway virtuali crea** .
2. Fare clic su **Crea** nella parte inferiore della stessa e **gateway di rete virtuale** . Verrà aperto e il **gateway virtuali crea** . Inserire i valori per il gateway di rete virtuale.

    ![Campi di blade create virtuali gateway] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Campi di blade create virtuali gateway")

3. **Nome**: nome del gateway. Questa operazione non è diverso da quello di denominazione subnet un gateway. È il nome dell'oggetto gateway che si sta creando.

4. **Tipo di gateway**: selezionare **VPN**. Gateway VPN utilizzare il tipo di gateway virtuali **VPN**. 

5. **Tipo di VPN**: selezionare il tipo VPN specificata per la configurazione. La maggior parte delle configurazioni richiedono un tipo VPN con la distribuzione.

6. **SKU**: selezionare il gateway SKU nell'elenco a discesa. SKU elencato nella casella di riepilogo dipendono dal tipo VPN selezionate.

7. **Posizione**: correggere il campo **posizione** in modo che puntino nella posizione in cui si trova la rete virtuale.
 
8. Scegliere la rete virtuale a cui si desidera aggiungere il gateway. Fare clic su **rete virtuale** per aprire e lo **scegliere una rete virtuale** . Selezionare il VNet. Se non viene visualizzato il VNet, assicurarsi che il campo **posizione** fa riferimento all'area in cui si trova la rete virtuale.

9. Scegliere un indirizzo IP pubblico. Fare clic su **indirizzo IP pubblico** per aprire e lo **scegliere indirizzo IP pubblico** . Fare clic su **+ Crea nuova** per aprire **creazione blade di indirizzo IP pubblico**. Immettere un nome per l'indirizzo IP pubblico. Questo blade crea un oggetto di indirizzo IP pubblico in cui un indirizzo IP pubblico verrà dinamicamente assegnato.<br>Fare clic su **OK** per salvare le modifiche apportate in questo blade.

10. **Abbonamento**: verificare che sia selezionata la sottoscrizione corretta.

11. **Gruppo risorse**: questa impostazione dipende dalla rete virtuale selezionato. 

12. Non regolare la **posizione** dopo avere specificato le impostazioni precedenti.

13. Verificare le impostazioni. Se si desidera che il gateway del dashboard, è possibile selezionare **Aggiungi a dashboard** nella parte inferiore della stessa e.

14. Fare clic su **Crea** per iniziare a creare il gateway. Le impostazioni verranno convalidate e verrà visualizzato "distribuzione virtuale gateway di rete" riquadro nel dashboard. Creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

    ![Distribuzione virtuale gateway di rete] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Distribuzione virtuale gateway di rete")

11. Dopo aver creato il gateway, è possibile visualizzare l'indirizzo IP assegnati a tale esaminando virtuali nel portale. Il gateway verrà visualizzato come un dispositivo connesso. È possibile scegliere il dispositivo collegato (il gateway virtuali) per visualizzare ulteriori informazioni.



