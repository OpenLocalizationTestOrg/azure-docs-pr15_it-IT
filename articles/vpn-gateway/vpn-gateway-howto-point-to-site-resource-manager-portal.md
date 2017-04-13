<properties 
   pageTitle="Configurare una connessione di gateway punto to a Site VPN alla rete virtuale utilizzando il modello di distribuzione di Manager delle risorse e il portale di Azure | Microsoft Azure"
   description="Garantire una connessione alla rete virtuale Azure mediante la creazione di una connessione di gateway VPN punto al sito tramite il portale Azure e Manager delle risorse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurare una connessione punto a sito a un VNet tramite il portale di Azure

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classico: portale Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configurazione di un punto al sito (P2S) consente di creare una connessione protetta da un computer client a una rete. Una connessione P2S risulta utile quando si desidera connettersi il VNet da una posizione remota, ad esempio da casa o una conferenza o quando è necessario solo alcuni client necessari per connettersi a una rete. 

Punto al sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per l'uso. Una connessione VPN avviando la connessione dal computer client. Per ulteriori informazioni sulle connessioni punto al sito, vedere [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [pianificazione e progettazione](vpn-gateway-plan-design.md).

In questo articolo illustra la creazione di un VNet con una connessione punto a sito nel modello di distribuzione Manager delle risorse tramite il portale di Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelli di distribuzione e i metodi per le connessioni P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra le due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flusso di lavoro di base 

![Punto-a--diagramma sito] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "punto al sito")

### <a name="example"></a>Esempi di valori

- **Nome: VNet1**
- **Spazio di indirizzi: 192.168.0.0/16**<br>In questo esempio abbiamo utilizzare solo uno spazio indirizzo. È possibile avere più spazio di indirizzi per il VNet.
- **Nome subnet: front-end**
- **Intervallo di indirizzi subnet: 192.168.1.0/24**
- **Abbonamento:** Se si dispone di più di una sottoscrizione, verificare che si sta utilizzando quella corretta.
- **Gruppo risorse: TestRG**
- **Posizione: Stati Uniti orientali**
- **GatewaySubnet: 192.168.200.0/24**
- **Nome del gateway virtuali: VNet1GW**
- **Tipo di gateway: VPN**
- **Tipo di VPN: basate su Route**
- **Indirizzo IP pubblico: VNet1GWpip**
- **Tipo di connessione: punto al sito**
- **Pool di indirizzi client: 172.16.201.0/24**<br>Client VPN che si connettono a VNet connessione punto a siti di ricevere un indirizzo IP dal pool di indirizzi client.

## <a name="before-beginning"></a>Prima di iniziare

- Verificare di avere un abbonamento a Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Parte 1: creare una rete virtuale

Se si sta creando questa configurazione come esercizio, è possibile fare riferimento ai [valori di esempio](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. aggiungere subnet e spazio aggiuntivo di indirizzi

È possibile aggiungere spazio aggiuntivo di indirizzi e subnet per il VNet dopo averlo creato.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. creare una subnet gateway

Prima di connettersi alla rete aziendale virtuale a un gateway, è innanzitutto necessario creare subnet gateway per la rete virtuale a cui si desidera connettersi. Se possibile, è consigliabile creare una subnet gateway mediante un blocco CIDR di /28 o /27 per fornire indirizzi IP sufficiente per soddisfare requisiti aggiuntivi successive alla configurazione.

Screenshot in questa sezione vengono forniti come un esempio di riferimento. Assicurarsi di usare l'intervallo di indirizzi GatewaySubnet corrispondente con i valori necessari per la configurazione.

#### <a name="to-create-a-gateway-subnet"></a>Per creare una subnet gateway

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. specificare un server DNS (facoltativo)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Parte 2: creare un gateway di rete virtuale

Le connessioni punto da sito richiedono le impostazioni seguenti:

- Tipo di gateway: VPN
- Tipo di VPN: basate su Route

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Parte 3 - generare certificati

I certificati sono utilizzati da Azure per autenticare i client VPN per le connessioni VPN punto al sito. Esportare dati certificato pubblico (non la chiave privata) come file con estensione cer x. 509 da un certificato generati da una soluzione di certificato aziendale o un certificato autofirmato con codifica Base 64. Quindi come importare i dati del certificato pubblico dal certificato principale in Azure. Inoltre, è necessario generare un certificato client dal certificato principale per i client. Ogni client da connettere alla rete virtuale tramite una connessione P2S deve essere installato un certificato client, generato dal certificato principale.

### <a name="getcer"></a>1. ottenere il file. cer per il certificato

Se si utilizza una soluzione aziendale, è possibile usare catena di certificato esistente. Se non si usa una soluzione CA dell'organizzazione, è possibile creare un certificato autofirmato. Un metodo per la creazione di un certificato autofirmato è makecert.

- Se si usa un sistema di certificato aziendale, ottenere il file. cer per il certificato che si desidera utilizzare. 

- Se non si usa una soluzione di certificato aziendale, è necessario generare un certificato autofirmato. Per la procedura di Windows 10, è possibile fare riferimento [all'utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md).

1. Per ottenere un file CER da un certificato, aprire **certmgr. msc** e individuare il certificato di autenticazione. Pulsante destro del mouse il certificato autofirmato, fare clic su **tutte le attività**e quindi fare clic su **Esporta**. Verrà visualizzata l' **Esportazione guidata certificati**.

2. Nella procedura guidata, fare clic su **Avanti**, selezionare **No, esportare la chiave privata**e quindi fare clic su **Avanti**.

3. Nella pagina **Formato File di esportazione** , selezionare **Base 64 codificato x. 509 (. CER).** Scegliere **Avanti**. 

4. Sul **File da esportare**, **passare** al percorso in cui si vuole esportare il certificato. Per **Nome File**, assegnare un nome di file di certificato. Fare clic su **Avanti**.

5. Fare clic su **Fine** per esportare il certificato.

### <a name="generateclientcert"></a>2. generare un certificato client

È possibile generare un certificato univoco uno per ogni cliente che si connetterà oppure è possibile utilizzare lo stesso certificato su più client. Il vantaggio di generare i certificati client univoco è la possibilità di revocare un certificato solo se necessario. In caso contrario, se è necessario revocare il certificato per un client che utilizzano lo stesso certificato client, sarà necessario generare e installare nuovi certificati per tutti i client che consente di eseguire l'autenticazione del certificato.

- Se si utilizza una soluzione di certificato aziendale, generare un certificato client con il formato del valore nome comuni 'name@yourdomain.com', piuttosto che il formato 'dominio\nomeutente.'. 

- Se si utilizza un certificato autofirmato, vedere [utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md) per generare un certificato client.

### <a name="exportclientcert"></a>3. esportare il certificato client

È necessario per l'autenticazione certificato client. Dopo aver generato il certificato client, esportarlo. Verrà installato il certificato client che si esporta in un secondo momento in ogni computer client.

1. Per esportare un certificato client, è possibile utilizzare *certmgr*. Pulsante destro del mouse il certificato client che si desidera esportare, fare clic su **tutte le attività**e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file *. pfx* . Assicurarsi che consente di registrare o ricordare la password (chiave) che è impostato per il certificato.

## <a name="addresspool"></a>Parte 4: aggiungere il pool di indirizzi client

1. Dopo aver creato il gateway virtuali, passare alla sezione **Impostazioni** della stessa e gateway virtuali. Nella sezione **Impostazioni** , fare clic su configurazione **punto al sito** per aprire e il **configurazione** .

    ![scegliere blade sito] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "scegliere blade sito")

2. **Pool di indirizzi** è il pool di indirizzi IP da cui i clienti che si connettono riceveranno un indirizzo IP. Aggiungere il pool di indirizzi e quindi fare clic su **Salva**.

    ![pool di indirizzi client] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "pool di indirizzi client")

## <a name="uploadfile"></a>Parte 5: caricare il file. cer del certificato radice

Dopo aver creato il gateway, è possibile caricare il file di certificato attendibile in Azure. È possibile caricare file per un massimo di 20 certificati. È non caricare la chiave privata per il certificato in Azure. Al caricamento di file. cer, Azure viene usato per autenticare i client connessi alla rete virtuale.

1. Passare alla configurazione **al sito di punto** blade. Verranno aggiunti i file. cer nella sezione **autorità di certificazione** della stessa questo e.

    ![scegliere blade sito] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "scegliere blade sito")

2. Assicurarsi che esportato il certificato come file x. 509 (CER) con la codifica Base 64. È necessario esportare in questo formato, in modo che è possibile aprire il certificato con editor di testo.
3. Aprire il certificato con un editor di testo, ad esempio Blocco note. Copiare solo la sezione seguente:

    ![dati del certificato] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "dati del certificato")

4. Incollare i dati del certificato nella sezione **Dati del certificato pubblico** del portale. Inserire il nome del certificato nello spazio dei **nomi** e quindi fare clic su **Salva**. È possibile aggiungere fino a 20 certificati radice attendibili.

    ![Carica certificato] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "Carica certificato")

## <a name="clientconfig"></a>Parte 6 - scaricare e installare il pacchetto di configurazione di client VPN

Client che si connettono a Azure utilizzando P2S deve avere un certificato client e un pacchetto di configurazione di client VPN installato. Pacchetti di configurazione di client VPN sono disponibili per i client di Windows. 

Il pacchetto di client VPN contiene informazioni necessarie per configurare il software client VPN integrato in Windows. La configurazione è specifica per la rete VPN che si desidera connettersi. Il pacchetto non viene installato software aggiuntivo. Vedere le [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) per altre informazioni.

1. Configurazione **al sito di punto** blade, fare clic su **Scarica VPN client** per l'apertura e il **client VPN scaricare** .

    ![Scaricare il client VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Scaricare il client VPN")

2. Selezionare il pacchetto corretto per il client, quindi fare clic su **Scarica**. Per i client a 64 bit, selezionare **AMD64**. Per i client di versione a 32 bit, selezionare **x86**.

3. Installare il pacchetto del computer client. Se viene visualizzato un popup SmartScreen, fare clic su **altre informazioni**, quindi **eseguire comunque** per installare il pacchetto.

4. Nel computer client, passare alle **Impostazioni di rete** e fare clic su **VPN**. Verrà visualizzata la connessione nell'elenco. Verrà visualizzato il nome della rete virtuale che si connette al e ha un aspetto simile a questo esempio: 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "Client VPN")

## <a name="installclientcert"></a>Parte 7 - installare il certificato client

Ogni computer client deve disporre di un certificato client per l'autenticazione. Quando si installa il certificato client, sarà necessario la password creata quando il certificato client esportato.

1. Copiare il file. pfx nel computer client.
2. Fare doppio clic sul file. pfx per installarlo. Non modificare il percorso di installazione.

## <a name="connect"></a>Parte 8 - connettersi a Azure

1. Per connettere il VNet del computer client, passare alle connessioni VPN e individuare la connessione VPN creato. È denominato lo stesso nome di rete virtuale. Fare clic su **Connetti**. Venga visualizzato un messaggio a comparsa che fa riferimento a utilizzando il certificato. In questo caso, fare clic su **Continua** per utilizzare i privilegi elevati. 

2. Nella pagina di stato di **connessione** , fare clic su **Connetti** per avviare la connessione. Se viene visualizzata una schermata di **Selezione certificato** , verificare che il certificato client con sia quello che si desidera utilizzare per la connessione. In caso contrario, utilizzare la freccia in giù per selezionare il certificato corretto e quindi fare clic su **OK**.

    ![Client VPN 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connessione client VPN")

3. Stabilire a questo punto la connessione.

    ![Client VPN 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connessione tramite VPN client 2")

## <a name="verify"></a>Parte 9 - verificare la connessione

1. Per verificare che sia attiva la connessione VPN, aprire un prompt dei comandi con privilegi elevato ed eseguire *ipconfig/all*.

2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto uno degli indirizzi del sito per punto VPN Client indirizzo pool specificato nella configurazione. Il risultato dovrebbe essere simile alla seguente:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Per aggiungere o rimuovere i certificati principali attendibili

È possibile rimuovere di certificazione attendibile comuni. Quando si rimuove un certificato attendibile, i certificati client generati dal certificato principale non saranno non è più possibile connettersi Azure tramite punto al sito. Se si desidera client per la connessione, è necessario installare un nuovo certificato client generati da un certificato considerato attendibile in Azure.

È possibile gestire l'elenco dei certificati revocato sulla configurazione di **al sito di punto** blade. Verrà visualizzata e l'utilizzato per [caricare un certificato attendibile](#uploadfile).

## <a name="revokeclient"></a>Per gestire l'elenco dei certificati client revocato

È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo connettività punto al sito in base a singoli certificati client. Se si rimuove un CER certificato radice da Azure, revocare l'accesso per tutti i certificati client generato/effettuato l'accesso per il certificato revocato. Se si vuole revocare un certificato client specifico, non il documento, è possibile eseguire questa operazione. In questo modo i certificati generati dal certificato principale verranno comunque essere validi. 

Pratica comune consiste nell'usare il certificato per gestire l'accesso a livello del team o dell'organizzazione, durante l'uso di certificati client revocato diffusamente il controllo dell'accesso per singoli utenti.

È possibile gestire l'elenco dei certificati revocato sulla configurazione di **al sito di punto** blade. Verrà visualizzata e l'utilizzato per [caricare un certificato attendibile](#uploadfile).


## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere una macchina virtuale per la rete virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.