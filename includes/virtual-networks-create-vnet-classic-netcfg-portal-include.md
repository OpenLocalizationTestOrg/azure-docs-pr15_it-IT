## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Come creare un VNet tramite un file di configurazione di rete nel portale di Azure

Azure utilizza un file xml per definire VNets tutti disponibili a un abbonamento. È possibile scaricare questo file e modificarlo per modificare o eliminare VNets esistente e crearne di nuovi. In questo documento verrà informazioni su come scaricare questo file definito come file di configurazione (o netcgf) rete e modificarlo per creare un nuovo VNet. Selezionare lo [schema di configurazione di Azure virtuali](https://msdn.microsoft.com/library/azure/jj157100.aspx) per altre informazioni sui file di configurazione della rete.

Per creare un VNet usando un file di netcfg tramite il portale di Azure, seguire la procedura seguente.

1. In un browser, passare a http://manage.windowsazure.com e, se necessario, accedere con l'account Azure.
2. Scorrere verso il basso nell'elenco dei servizi e fare clic su **reti** come indicato di seguito.

    ![Reti virtuali Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Nella parte inferiore della pagina, fare clic sul pulsante **ESPORTA** come illustrato di seguito.

    ![Pulsante Esporta](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Nella pagina **Esporta configurazione della rete** , selezionare l'abbonamento a cui da che si desidera esportare la configurazione della rete virtuale e fare clic sul pulsante di segno di spunta nell'angolo inferiore sinistro della pagina.
5. Seguire le istruzioni del browser per salvare il file **NetworkConfig.xml** . Non dimenticare di nota nel punto in cui si desidera salvare il file.
6. Aprire il file salvato nel passaggio 5 utilizzando qualsiasi applicazione di editor XML o testo e cercare il **<VirtualNetworkSites>** elemento. Se si dispone di tutte le reti già state create, ogni rete verrà visualizzato come i propri **<VirtualNetworkSite>** elemento.
7. Per creare la rete virtuale descritta in questo scenario, aggiungere il seguente codice XML solo nel **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Salvare il file di configurazione della rete.
9.  Nel portale di Azure, nell'angolo inferiore sinistro della pagina, fare clic su **Nuovo**, quindi fare clic su **Servizi di rete**, quindi fare clic su **VIRTUALI**e quindi fare clic su **Importa configurazione** , come illustrato nella figura seguente.

    ![Configurazione dell'importazione](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Nella pagina **Importa file di configurazione della rete** fare clic su **Sfoglia per FILE...**, quindi passare alla cartella che è stato salvato il file al passaggio 8, selezionare il file e quindi fare clic su **Apri**. La pagina web dovrebbe essere simile alla figura riportata di seguito. Nell'angolo inferiore destro della pagina, fare clic sul pulsante freccia per spostare il passaggio successivo.

    ![Pagina file di configurazione della rete importazione](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   La pagina **creazione rete** visualizzata la voce per il nuovo VNet, come illustrato nella figura seguente.

    ![Creazione della pagina di rete](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Fare clic sul pulsante segno di spunta nell'angolo inferiore destra della pagina per creare il VNet. Dopo alcuni secondi la VNet verrà visualizzato nell'elenco dei VNets disponibili, come illustrato nella figura seguente.

    ![Nuova rete virtuale](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)