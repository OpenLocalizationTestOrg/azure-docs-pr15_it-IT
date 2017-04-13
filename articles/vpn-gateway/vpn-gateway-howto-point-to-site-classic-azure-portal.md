<properties
   pageTitle="Configurare una connessione di gateway VPN punto da sito a una rete virtuale Azure tramite il portale di Azure | Microsoft Azure"
   description="Garantire una connessione alla rete virtuale Azure mediante la creazione di una connessione di gateway VPN punto al sito tramite il portale di Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurare una connessione punto a sito a un VNet tramite il portale di Azure

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classico: portale Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

In questo articolo illustra la creazione di un VNet con una connessione punto a sito nel modello di distribuzione classica tramite il portale di Azure. Configurazione di un punto al sito (P2S) consente di creare una connessione protetta da un computer client a una rete. Una connessione P2S è utile quando si desidera connettersi il VNet da una posizione remota, ad esempio da casa o una conferenza. In alternativa, quando è necessario solo alcuni client necessari per connettersi a una rete.

Punto al sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per l'uso. Una connessione VPN avviando la connessione dal computer client. Per ulteriori informazioni sulle connessioni punto al sito, vedere le [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Su Gateway VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelli di distribuzione e i metodi per le connessioni P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra le due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flusso di lavoro di base 

![Punto-a--diagramma sito] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "punto al sito")


Nelle sezioni seguenti vengono guidano l'utente tramite la procedura per creare una connessione punto a sito protetta a una rete. 

1. Creare un gateway VPN e una rete virtuale
2. Generare certificati
3. Caricare il file. cer
4. Generare il pacchetto di configurazione di client VPN
5. Configurare il computer client
6. Connettersi a Azure

### <a name="example-settings"></a>Impostazioni di esempio

È possibile utilizzare le impostazioni di esempio seguenti:

- **Nome: VNet1**
- **Spazio di indirizzi: 192.168.0.0/16**
- **Nome subnet: front-end**
- **Intervallo di indirizzi subnet: 192.168.1.0/24**
- **Abbonamento:** Se si dispone di più di una sottoscrizione, verificare che si sta utilizzando quella corretta.
- **Gruppo risorse: TestRG**
- **Posizione: Stati Uniti orientali**
- **Tipo di connessione: punto al sito**
- **Spazio di indirizzi client: 172.16.201.0/24**. Client VPN che si connettono a VNet connessione punto a siti di ricevere un indirizzo IP del pool specificato.
- **GatewaySubnet: 192.168.200.0/24**. Subnet Gateway deve utilizzare il nome "GatewaySubnet".
- **Dimensioni:** Selezionare il gateway SKU che si desidera utilizzare.
- **Il tipo di routing: dinamico**

## <a name="vnetvpn"></a>Sezione 1 - creare un gateway VPN e una rete virtuale

### <a name="createvnet"></a>Parte 1: Creare una rete virtuale

Se non si dispone di una rete virtuale, crearne uno. Schermate sono illustrate alcuni esempi. Assicurarsi di sostituire i valori con uno personalizzato. Per creare un VNet tramite il portale di Azure, utilizzare la procedura seguente: 

1. Da un browser passare al [portale di Azure](http://portal.azure.com) e, se necessario, accedere con l'account Azure.

2. Fare clic su **Nuovo**. Nel campo di **ricerca di marketplace** , digitare "Virtuali". Fare clic per aprire e il **Virtuali** individuare **Virtuali** dall'elenco restituito.

    ![Cercare blade virtuali] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Cercare blade virtuali")

3. Nella parte inferiore della stessa e virtuali, nell'elenco **Selezionare un modello di distribuzione** selezionare **classica**e quindi fare clic su **Crea**.

    ![Selezionare modello di distribuzione] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Selezionare modello di distribuzione")

4. Nella e **virtuali creare** , configurare le impostazioni di VNet. In questo blade si aggiungerà il primo spazio di indirizzi e un intervallo di indirizzi singola subnet. Dopo aver creato il VNet, è possibile tornare indietro e aggiungere gli spazi degli indirizzi e subnet aggiuntive.

    ![Blade virtuali crea] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Blade virtuali crea")

5. Verificare che l' **abbonamento** sia quello corretto. È possibile modificare le sottoscrizioni utilizzando l'elenco a discesa.

6. Fare clic su **gruppo di risorse** e selezionare un gruppo di risorse esistente o crearne uno nuovo, digitare un nome per il nuovo gruppo di risorse. Se si sta creando un nuovo gruppo, assegnare un nome al gruppo di risorse in base ai valori configurazione pianificata. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

7. Selezionare le impostazioni di **posizione** per il VNet. Il percorso determinerà in cui verranno archiviato risorse che si distribuiscono questo VNet.

8. Selezionare **Aggiungi a dashboard** se si desidera essere in grado di trovare facilmente il VNet nel dashboard e quindi fare clic su **Crea**.
    
    ![Aggiungi a dashboard] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Aggiungi a dashboard")


9. Dopo aver fatto clic su Crea, si vedrà un riquadro nel dashboard che rifletteranno lo stato di avanzamento del VNet. Il riquadro cambia mentre viene creato il VNet.

    ![Il riquadro creazione virtuali] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Il riquadro creazione virtuali")

10. Dopo aver creato la rete virtuale, è possibile aggiungere l'indirizzo IP del server DNS per gestire la risoluzione dei nomi. Aprire le impostazioni per la rete virtuale, fare clic su server DNS e aggiungere l'indirizzo IP del server DNS che si desidera utilizzare. Questa impostazione non viene creato un nuovo server DNS. Assicurarsi di aggiungere un server DNS che possono comunicare le risorse.

Dopo aver creata la rete virtuale, si vedrà **che creata** elencata con **lo stato** nella pagina reti nel portale di classica Azure.

### <a name="gateway"></a>Parte 2: Creare un gateway routing dinamico e subnet gateway

In questo passaggio si creerà una subnet gateway e un gateway routing dinamico. Nel portale di Azure per il modello di distribuzione classica Creazione subnet gateway e il gateway può essere gestita tramite le stesse pale di configurazione.

1. Nel portale, passare alla rete virtuale per il quale si desidera creare un gateway.

2. Scegliere **Gateway**e il per la rete virtuale, su e il **Panoramica** , nella sezione connessioni VPN.

    ![Fare clic qui per creare un gateway] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Fare clic qui per creare un gateway")


3. Scegliere **punto al sito e il **Nuova connessione VPN** **.

    ![Tipo di connessione P2S] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "Tipo di connessione P2S")

4. Per lo **Spazio di indirizzi Client**, aggiungere l'indirizzo IP. Questo è l'intervallo da cui i client VPN verranno visualizzato un indirizzo IP durante la connessione. Eliminare l'intervallo di riempimento automatico e aggiungere il proprio.

    ![Spazio di indirizzi client] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Spazio di indirizzi client")

5. Selezionare la casella di controllo **Crea gateway immediatamente** .

    ![Creare gateway immediatamente] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Creare gateway immediatamente")

6. Fare clic su **configurazione di gateway facoltativo** per aprire e il **configurazione del Gateway** .

    ![Fare clic su configurazione di gateway facoltativo] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Fare clic su configurazione di gateway facoltativo")

7. Fare clic su **Configura Subnet obbligatori impostazioni** per aggiungere alla **subnet gateway**. Sebbene sia possibile creare una subnet gateway assuma /29, è consigliabile creare una subnet più grande contenente più indirizzi selezionando almeno /28 o /27. In questo modo per indirizzi sufficiente contenere possibili configurazioni aggiuntive che può essere utile in futuro.

    >[AZURE.IMPORTANT] Quando si utilizzano subnet gateway, evitare di associazione di un gruppo di sicurezza di rete (NSG) alla subnet gateway. Associazione di un gruppo di sicurezza di rete per questa subnet potrebbe causare il gateway VPN smettere di funzionare come previsto. Per ulteriori informazioni sui gruppi di sicurezza di rete, vedere [che cos'è un gruppo di sicurezza della rete?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Aggiungere GatewaySubnet] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Aggiungere GatewaySubnet")

8. Selezionare il gateway **dimensioni**. Questo è il gateway SKU che verrà utilizzato per creare il gateway di rete virtuale. Nel portale di SKU predefinita è di **base**. Per ulteriori informazioni su gateway SKU, vedere [Sulle impostazioni del Gateway VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![dimensioni del gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Selezionare il **Tipo di Routing** per il gateway. Configurazioni P2S richiedono un tipo di routing **dinamico** . Dopo aver configurato il blade, fare clic su **OK** .

    ![Configura un tipo di routing] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configura un tipo di routing")

10. Scegliere **OK** nella parte inferiore della stessa e per iniziare a creare il gateway virtuali e il **Nuova connessione VPN** . Possono essere necessari fino a 45 minuti. 


## <a name="generatecerts"></a>Sezione 2 - generare certificati

I certificati sono utilizzati da Azure per autenticare i client VPN per le connessioni VPN punto al sito. Esportare dati certificato pubblico (non la chiave privata) come file con estensione cer x. 509 da un certificato generati da una soluzione di certificato aziendale o un certificato autofirmato con codifica Base 64. Quindi come importare i dati del certificato pubblico dal certificato principale in Azure. Inoltre, è necessario generare un certificato client dal certificato principale per i client. Ogni client da connettere alla rete virtuale tramite una connessione P2S deve essere installato un certificato client, generato dal certificato principale.

### <a name="cer"></a>Parte 1: Ottenere il file. cer per il certificato


Se si utilizza una soluzione aziendale, è possibile usare catena di certificato esistente. Se non si usa una soluzione CA dell'organizzazione, è possibile creare un certificato autofirmato. Un metodo per la creazione di un certificato autofirmato è makecert.

- Se si usa un sistema di certificato aziendale, ottenere il file. cer per il certificato che si desidera utilizzare. 

- Se non si usa una soluzione di certificato aziendale, è necessario generare un certificato autofirmato. Per la procedura di Windows 10, è possibile fare riferimento [all'utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md).

1. Per ottenere un file CER da un certificato, aprire **certmgr. msc** e individuare il certificato di autenticazione. Pulsante destro del mouse il certificato autofirmato, fare clic su **tutte le attività**e quindi fare clic su **Esporta**. Verrà visualizzata l' **Esportazione guidata certificati**.

2. Nella procedura guidata, fare clic su **Avanti**, selezionare **No, esportare la chiave privata**e quindi fare clic su **Avanti**.

3. Nella pagina **Formato File di esportazione** , selezionare **Base 64 codificato x. 509 (. CER).** Scegliere **Avanti**. 

4. Sul **File da esportare**, **passare** al percorso in cui si vuole esportare il certificato. Per **Nome File**, assegnare un nome di file di certificato. Fare clic su **Avanti**.

5. Fare clic su **Fine** per esportare il certificato.


### <a name="genclientcert"></a>Parte 2: Generare un certificato client

È possibile generare un certificato univoco uno per ogni cliente che si connetterà oppure è possibile utilizzare lo stesso certificato su più client. Il vantaggio di generare i certificati client univoco è la possibilità di revocare un certificato solo se necessario. In caso contrario, se è necessario revocare il certificato per un client che utilizzano lo stesso certificato client, sarà necessario generare e installare nuovi certificati per tutti i client che consente di eseguire l'autenticazione del certificato.

- Se si utilizza una soluzione di certificato aziendale, generare un certificato client con il formato del valore nome comuni 'name@yourdomain.com', piuttosto che il formato 'dominio\nomeutente.'. 

- Se si utilizza un certificato autofirmato, vedere [utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md) per generare un certificato client.

### <a name="exportclientcert"></a>Parte 3: Esportare il certificato client

Installare un certificato client in ogni computer in cui si desidera connettersi alla rete virtuale. È necessario per l'autenticazione certificato client. È possibile automatizzare l'installazione del certificato client oppure è possibile installare manualmente. La procedura seguente guidano l'utente attraverso l'esportazione e installare manualmente il certificato client.

1. Per esportare un certificato client, è possibile utilizzare *certmgr*. Pulsante destro del mouse il certificato client che si desidera esportare, fare clic su **tutte le attività**e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file *. pfx* . Assicurarsi che consente di registrare o ricordare la password (chiave) che è impostato per il certificato.

## <a name="upload"></a>Sezione 3 - caricare il file. cer del certificato radice

Dopo aver creato il gateway, è possibile caricare il file di certificato attendibile in Azure. È possibile caricare file per un massimo di 20 certificati. È non caricare la chiave privata per il certificato in Azure. Al caricamento di file. cer, Azure viene usato per autenticare i client connessi alla rete virtuale.

1. Nella sezione **connessioni VPN** della stessa e per i VNet, fare clic sull'immagine di **client** per connessione VPN Apri il **punto al sito** blade.

    ![Client] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Client")

2. Connessione **punto al sito** blade, fare clic su **Gestisci certificati** per aprire e il **certificati** .<br>

    ![Blade certificati](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. Scegliere **Carica** per aprire e il **certificato di caricare** e il **certificati** .<br>

    ![Caricare blade certificati](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Fare clic sull'immagine di cartella per cercare il file. cer. Selezionare il file, quindi fare clic su **OK**. Aggiornare la pagina per visualizzare il certificato caricato e il **certificati** .

    ![Carica certificato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Sezione 4 - generare il pacchetto di configurazione di client VPN

Per connettersi alla rete virtuale, è anche necessario configurare un client VPN. Il computer client richiede un certificato client e il pacchetto di configurazione di client VPN corretta per la connessione.

Il pacchetto di client VPN contiene informazioni di configurazione per configurare il software client VPN incorporato in Windows. Il pacchetto non viene installato software aggiuntivo. Le impostazioni sono specifiche per la rete virtuale che si desidera connettersi. Per un elenco di sistemi operativi supportati, vedere le connessioni [punto al sito](vpn-gateway-vpn-faq.md#point-to-site-connections) sezione delle domande frequenti Gateway VPN. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Per generare il pacchetto di configurazione di client VPN

1. Nel portale di Azure, in e il **Panoramica** per la VNet in **connessioni VPN**, fare clic sull'immagine di client per connessione VPN Apri il **punto al sito** blade.
2. Nella parte superiore del **punto-a-sito VPN connessione** blade, fare clic sul pacchetto di download che corrisponde al sistema operativo client in cui verrà installato:

 - Per i client a 64 bit, selezionare **Client VPN (64 bit)**.
 - Per i client di versione a 32 bit, selezionare **Client VPN (32 bit)**.

    ![Scaricare il pacchetto di configurazione di client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Verrà visualizzato un messaggio che Azure generi il pacchetto di configurazione di client VPN per la rete virtuale. Dopo alcuni minuti, viene generato il pacchetto e verrà visualizzato un messaggio nel computer locale che è stato scaricato il pacchetto. Salvare il file di pacchetto di configurazione. Questa operazione verrà installato ogni computer client si connetterà virtuali mediante P2S.


## <a name="clientconfiguration"></a>Sezione 5: configurare il computer client

### <a name="part-1-install-the-client-certificate"></a>Parte 1: Installare il certificato client

Ogni computer client deve disporre di un certificato client per l'autenticazione. Quando si installa il certificato client, sarà necessario la password creata quando il certificato client esportato.

1. Copiare il file. pfx nel computer client.
2. Fare doppio clic sul file. pfx per installarlo. Non modificare il percorso di installazione.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Installare il pacchetto di configurazione di client VPN

È possibile utilizzare lo stesso pacchetto di configurazione di client VPN in ogni computer client, purché la versione corrispondente all'architettura per il client.

1. Copiare il file di configurazione in locale nel computer in cui si desidera connettersi alla rete virtuale e fare doppio clic sul file .exe. 

2. Dopo che è installato il pacchetto, è possibile iniziare la connessione VPN. Il pacchetto di configurazione non è firmato da Microsoft. Si desidera firmare il pacchetto di utilizzo del servizio di firma dell'organizzazione oppure accedere se stessi con [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). È possibile utilizzare il pacchetto senza eseguire l'accesso. Tuttavia, se non è stato effettuato il pacchetto, viene visualizzato un avviso quando si installa il pacchetto.

3. Nel computer client, passare alle **Impostazioni di rete** e fare clic su **VPN**. Verrà visualizzata la connessione nell'elenco. Viene visualizzato il nome della rete virtuale che si connetterà e sarà simile alla seguente: 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "Client VPN VNet")

## <a name="connect"></a>Sezione 6 - connettersi a Azure

### <a name="connect-to-your-vnet"></a>Connettere il VNet

1. Per connettere il VNet del computer client, passare alle connessioni VPN e individuare la connessione VPN creato. È denominato lo stesso nome di rete virtuale. Fare clic su **Connetti**. Venga visualizzato un messaggio a comparsa che fa riferimento a utilizzando il certificato. In questo caso, fare clic su **Continua** per utilizzare i privilegi elevati. 

2. Nella pagina di stato di **connessione** , fare clic su **Connetti** per avviare la connessione. Se viene visualizzata una schermata di **Selezione certificato** , verificare che il certificato client con sia quello che si desidera utilizzare per la connessione. In caso contrario, utilizzare la freccia in giù per selezionare il certificato corretto e quindi fare clic su **OK**.

    ![La connessione] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "Connessione client VPN")

3. Stabilire a questo punto la connessione.

    ![Connessione stabilite] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connessione")

### <a name="verify-the-vpn-connection"></a>Verificare la connessione VPN

1. Per verificare che sia attiva la connessione VPN, aprire un prompt dei comandi con privilegi elevato ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto uno degli indirizzi all'interno dell'intervallo di indirizzo di connettività al sito specificato al momento della creazione del VNet. Il risultato dovrebbe essere simile alla seguente:

Esempio:



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere macchine virtuali alla rete virtuale. Informazioni su [come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-windows-classic-createportal.md).