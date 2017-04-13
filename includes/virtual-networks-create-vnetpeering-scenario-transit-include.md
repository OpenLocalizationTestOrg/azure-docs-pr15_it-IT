## <a name="service-chaining---transit-through-peered-vnet"></a>Servizio Chaining - transito VNet peered

Anche se l'utilizzo di route di sistema facilita il traffico automaticamente per la distribuzione, esistono casi in cui si desidera controllare il routing di pacchetti tramite un dispositivo virtuale.
In questo scenario, sono disponibili due VNets in una sottoscrizione, HubVNet e VNet1 come descritto nella figura seguente. Distribuire rete virtuale Appliance(NVA) in VNet HubVNet. Dopo aver stabilito VNet peering tra HubVNet e VNet1, è possibile impostare i percorsi definiti dall'utente e specificare il passaggio successivo per NVA nel HubVNet.

![Transito NVA](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Per semplicità, si presume qui VNets tutti nello stesso abbonamento. Ma funziona anche per gli scenari di abbonamento a croce.

La proprietà della chiave per abilitare il routing transito è il parametro "Consentire il traffico inoltrato". In questo modo accettare e inviare il traffico da/verso la NVA VNet peered.  
