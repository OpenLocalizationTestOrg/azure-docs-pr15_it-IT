<properties
   pageTitle="Connettere VNets utilizzando il modello di distribuzione di Manager delle risorse e il portale di Azure | Microsoft Azure"
   description="Creare una connessione di gateway VPN tra VNets tramite Gestione risorse e del portale di Azure."
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
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurare una connessione di VNet a VNet tramite il portale di Azure

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Classico: portale classica](virtual-networks-configure-vnet-to-vnet-connection.md)


In questo articolo sono illustrati i passaggi per creare una connessione tra VNets nel modello di distribuzione Manager delle risorse tramite il portale Azure e Gateway VPN.

Quando si utilizza il portale di Azure per connettere le reti virtuali, il VNets deve essere nello stesso abbonamento. Se le reti virtuali diversi abbonamenti, è possibile collegarle eseguendo la procedura di [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) .

![diagramma V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelli di distribuzione e i metodi per le connessioni VNet-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Nella tabella seguente mostra i metodi per le configurazioni VNet a VNet e modelli di distribuzione attualmente disponibile. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>Peering VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Informazioni sulle connessioni VNet-VNet

La connessione di rete virtuale a un'altra rete virtuale (VNet-VNet) è simile alla connessione un VNet in un punto del sito locale. Entrambi i tipi di connettività per utilizzano un gateway VPN Azure per fornire un tunnel protetto tramite IPsec/IKE. VNets che ci si connette può essere in diverse aree geografiche o in diversi abbonamenti.

È anche possibile combinare VNet a VNet la comunicazione con le configurazioni più siti. Consente stabilire topologie di rete che combinano tra locale la connettività con la connettività di rete tra virtuale, come illustrato nella figura seguente:

![Informazioni sulle connessioni] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informazioni sulle connessioni")

### <a name="why-connect-virtual-networks"></a>Perché la connessione reti virtuali?

È consigliabile connettersi reti virtuali per i motivi seguenti:

- **Area cross geografico-ridondanza e geografico presenza**
    - È possibile impostare il proprio geografico replica o la sincronizzazione con la connettività protetta senza superando il endpoint esposto a Internet.
    - Con Azure il traffico Manager e di bilanciamento del carico, è possibile impostare disponibilità carico di lavoro con la ridondanza geografico in più regioni Azure. Un esempio importante consiste nel configurare SQL sempre attive con i gruppi di disponibilità diffusione in più regioni Azure.

- **Internazionali applicazioni a più livelli con isolamento o confine amministrativo**
    - All'interno dell'area stessa, è possibile impostare applicazioni a più livelli con più reti virtuali collegate a causa di isolamento o requisiti amministrativi.

Per ulteriori informazioni sulle connessioni VNet-VNet, vedere [domande frequenti su VNet a VNet](#faq) alla fine di questo articolo.

### <a name="values"></a>Impostazioni di esempio

Quando si utilizza la procedura seguente come esercizio, è possibile utilizzare i valori di configurazione di esempio. Scopo di esempio, verranno utilizzate più spazi degli indirizzi per ogni VNet. Tuttavia, le configurazioni VNet a VNet non è richiesta più spazi degli indirizzi.

**Valori per TestVNet1:**

- Nome VNet: TestVNet1
- Spazio di indirizzi: 10.11.0.0/16
    - Nome subnet: front-end
    - Intervallo di indirizzi subnet: 10.11.0.0/24
- Gruppo risorse: TestRG1
- Posizione: Stati Uniti orientali
- Spazio di indirizzi: 10.12.0.0/16
    - Nome subnet: back-end
    - Intervallo di indirizzi subnet: 10.12.0.0/24
- Nome del gateway Subnet: GatewaySubnet (crescerà riempimento automatico nel portale)
    - Intervallo di indirizzi di gateway Subnet: 10.11.255.0/27
- Server DNS: Usare l'indirizzo IP del Server DNS
- Nome del Gateway virtuali: TestVNet1GW
- Tipo di gateway: VPN
- Tipo di VPN: basate su Route
- SKU: Selezionare lo SKU di Gateway da usare
- Indirizzo IP pubblico: TestVNet1GWIP
- Valori di connessione:
    - Nome: TestVNet1toTestVNet4
    - Chiave condivisa: È possibile creare manualmente la chiave condivisa. In questo esempio si userà abc123. Importante è che quando si crea la connessione tra il VNets, il valore deve corrispondere.

**Valori per TestVNet4:**

- Nome VNet: TestVNet4
- Spazio di indirizzi: 10.41.0.0/16
    - Nome subnet: front-end
    - Intervallo di indirizzi subnet: 10.41.0.0/24
- Gruppo risorse: TestRG1
- Posizione: Usa ovest
- Spazio di indirizzi: 10.42.0.0/16
    - Nome subnet: back-end
    - Intervallo di indirizzi subnet: 10.42.0.0/24
- Nome GatewaySubnet: GatewaySubnet (crescerà riempimento automatico nel portale)
    - Intervallo di indirizzi GatewaySubnet: 10.41.255.0/27
- Server DNS: Usare l'indirizzo IP del Server DNS
- Nome del Gateway virtuali: TestVNet4GW
- Tipo di gateway: VPN
- Tipo di VPN: basate su Route
- SKU: Selezionare lo SKU di Gateway da usare
- Indirizzo IP pubblico: TestVNet4GWIP
- Valori di connessione:
    - Nome: TestVNet4toTestVNet1
    - Chiave condivisa: È possibile creare manualmente la chiave condivisa. In questo esempio si userà abc123. Importante è che quando si crea la connessione tra il VNets, il valore deve corrispondere.


## <a name="CreatVNet"></a>1. creare e configurare TestVNet1

Se si dispone già di un VNet, verificare che le impostazioni siano compatibili con la struttura del gateway VPN. Prestare particolare attenzione alle eventuali subnet che potrebbero risultare sovrapposti con altre reti. Se si dispone sovrapposti subnet, la connessione non funzionerà correttamente. Se il VNet è configurato con i dati corretti, è possibile iniziare la procedura nella sezione [specificare un server DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. aggiungere spazio aggiuntivo di indirizzi e creare subnet

È possibile aggiungere spazio aggiuntivo di indirizzi e creare subnet dopo aver creato il VNet.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. creare una subnet gateway

Prima di connettersi alla rete aziendale virtuale a un gateway, è innanzitutto necessario creare subnet gateway per la rete virtuale a cui si desidera connettersi. Se possibile, è consigliabile creare una subnet gateway mediante un blocco CIDR di /28 o /27 per fornire indirizzi IP sufficiente per soddisfare requisiti aggiuntivi successive alla configurazione.

Se si sta creando questa configurazione come esercizio, fare riferimento a queste [impostazioni di esempio](#values) quando si creano subnet gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Per creare una subnet gateway

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. specificare un server DNS (facoltativo)

Se si vuole avere la risoluzione dei nomi per macchine virtuali distribuiti nel VNets, è necessario specificare un server DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. creare un gateway di rete virtuale

In questo passaggio, creare il gateway di rete virtuale per la VNet. Questa operazione può richiedere fino a 45 minuti. Se si sta creando questa configurazione come esercizio, è possibile fare riferimento alle [impostazioni di esempio](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. creare e configurare TestVNet4

Dopo aver configurato TestVNet1, creare TestVNet4 ripetendo i passaggi precedenti, sostituendo i valori con quelle di TestVNet4. Non è necessario attendere fino al completamento della creazione prima di configurare TestVNet4 il gateway di rete virtuale per TestVNet1. Se si usano valori personalizzati, assicurarsi che gli spazi indirizzo non si sovrappongono con uno dei VNets che si desidera connettersi.


## <a name="TestVNet1Connection"></a>7. configurare la connessione TestVNet1

Quando il gateway di rete virtuale per TestVNet1 e TestVNet4 è stata completata, è possibile creare la rete virtuale le connessioni gateway. In questa sezione, si verrà creata una connessione da VNet1 a VNet4.

1. In **tutte le risorse**, passare al gateway virtuali per il VNet. Ad esempio **TestVNet1GW**. Fare clic su **TestVNet1GW** per aprire e il gateway virtuali.

    ![Blade di connessioni] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Blade connessioni")

2. Fare clic su **+ Aggiungi** per aprire e **l'Aggiungi connessione** .

3. Nel campo nome e **l'Aggiungi connessione** , digitare un nome per la connessione. Ad esempio **TestVNet1toTestVNet4**.

    ![Nome della connessione] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nome della connessione")

4. Per **tipo di connessione**. Selezionare **VNet-VNet** nell'elenco a discesa.

5. Il valore del campo **primo gateway virtuali** viene compilato automaticamente in quanto si sta creando la connessione da gateway virtuali specificato.

6. Il campo **secondo gateway virtuali** è il gateway virtuali di VNet che si desidera creare una connessione a. Fare clic su **scegliere un altro gateway virtuali** per aprire e il **Selezionare virtuali gateway** .

    ![Aggiungi connessione] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Aggiungere una connessione")

7. Visualizzare i gateway virtuali elencati in questo blade. Si noti che sono elencati solo i gateway virtuali inclusi nell'abbonamento. Se si vuole collegare a un gateway virtuali non incluso nell'abbonamento, utilizzare l' [articolo PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Fare clic su gateway virtuali che si desidera connettersi.
 
9. Nel campo **chiave condivisa** digitare una chiave condivisa per la connessione. È possibile generare o creare manualmente questa chiave. In una connessione al sito, la chiave utilizzata saranno esattamente gli stessi del dispositivo locale e la connessione di rete virtuale gateway. Il concetto è simile, tranne che piuttosto che la connessione a un dispositivo VPN, si connette a un altro gateway virtuali.

    ![Chiave condivisa] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Chiave condivisa")

10. Fare clic su **OK** nella parte inferiore della stessa e per salvare le modifiche.

## <a name="TestVNet4Connection"></a>8. configurare la connessione TestVNet4

Creare una connessione da TestVNet4 a TestVNet1. Utilizzare lo stesso metodo utilizzato per creare la connessione da TestVNet1 a TestVNet4. Assicurarsi di usare la stessa chiave condivisa.

## <a name="VerifyConnection"></a>9. verificare la connessione

Verificare la connessione. Per ogni gateway virtuali, eseguire le operazioni seguenti:

1. Individuare e il per il gateway di rete virtuale. Ad esempio **TestVNet4GW**. 
2. Scegliere **connessioni** per visualizzare e il connessioni per il gateway virtuali e il gateway virtuali.

Visualizzare le connessioni e verificare lo stato. Dopo aver creata la connessione, verrà visualizzata **completato** e **connettività** dei valori di stato.

![È stata eseguita correttamente] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "È stata eseguita correttamente")

Fare doppio clic su ogni connessione separatamente per visualizzare ulteriori informazioni sulla connessione.

![Nozioni di base] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Nozioni di base")

## <a name="faq"></a>Domande frequenti su VNet-VNet

Visualizzare i dettagli di domande frequenti per ulteriori informazioni sulle connessioni VNet a VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Una volta completata la connessione, è possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.
