1. Nel portale di **tutte le risorse**, fare clic su **Add +**. Nella finestra di **tutti gli elementi** blade ricerca, digitare **gateway di rete locale**, quindi fare clic per eseguire una ricerca. Viene restituito un elenco. Fare clic su **gateway rete locale** per aprire e il, quindi fare clic su **Crea** per aprire e il **gateway di rete locale crea** .

    ![creare il gateway di rete locale](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. In **blade di gateway crea rete locale**, specificare un **nome** per l'oggetto di gateway di rete locale.
 
3. Specificare un valido pubblico **indirizzo IP** del dispositivo VPN o gateway virtuali a cui si desidera connettersi.<br>Se la rete locale rappresenta una posizione locale, questa è l'indirizzo IP pubblico del dispositivo VPN che si desidera connettersi. Essa non può essere NAT e deve essere raggiungibile da Azure.<br>Se la rete locale rappresenta un altro VNet, specificare l'indirizzo IP pubblico assegnato al gateway virtuali per tale VNet.<br>

4. **Spazio di indirizzi** fa riferimento a intervalli di indirizzi della rete che rappresenta la rete locale. È possibile aggiungere più intervalli di spazio di indirizzi. Assicurarsi che gli intervalli che è possibile specificare non si sovrappongono con intervalli di altre reti che si desidera connettersi.
 
5. Per l' **abbonamento**, verificare che l'abbonamento corretto venga visualizzato.

6. Per **Gruppo di risorse**, selezionare il gruppo di risorse che si desidera utilizzare. È possibile creare un nuovo gruppo di risorse o selezionare uno che è già stato creato.

7. **Percorso**, selezionare il percorso creata in questo oggetto. È consigliabile selezionare nella stessa posizione in cui si trova il VNet, ma non è necessario farlo.

8. Fare clic su **Crea** per creare il gateway di rete locale.
