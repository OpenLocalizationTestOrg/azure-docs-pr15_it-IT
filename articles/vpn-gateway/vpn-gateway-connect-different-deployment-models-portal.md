<properties 
   pageTitle="Modalità di connessione classiche reti virtuali le reti virtuali nel portale di gestione risorse | Microsoft Azure"
   description="Informazioni su come creare una connessione VPN tra classica VNets e Manager delle risorse VNets tramite il portale e Gateway VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Connettere reti virtuali da diversi modelli di distribuzione nel portale

> [AZURE.SELECTOR]
- [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure è attualmente modelli di gestione delle due: classica e Manager delle risorse (RM). Se è stato utilizzato Azure per un certo tempo, è probabile che sia macchine virtuali di Azure e ruoli istanza in esecuzione in una VNet classica. Il più recente macchine virtuali e istanze del ruolo potrebbero essere in esecuzione in una VNet creati in Gestione risorse. In questo articolo illustra la connessione classica VNets a Manager delle risorse VNets per consentire le risorse presenti nei modelli di distribuzione separata per comunicare con loro tramite una connessione di gateway. 

È possibile creare una connessione tra VNets negli abbonamenti a diversi e in diverse aree geografiche. È inoltre possibile connettersi VNets che già dispongono di connessioni a reti locale, come il gateway che sono stati configurati con è basato su route o dinamico. Per ulteriori informazioni sulle connessioni VNet-VNet, vedere [domande frequenti su VNet a VNet](#faq) alla fine di questo articolo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelli di distribuzione e i metodi per le connessioni VNet-VNet

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Viene aggiornato nella tabella seguente come nuovi articoli e altri strumenti disponibili per la configurazione. Quando un articolo è disponibile, è collega direttamente dalla tabella.<br><br>

**VNet-VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>Peering VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Prima di iniziare

La procedura seguente scorrere le impostazioni necessarie per configurare un gateway dinamico o basate su route per ogni VNet e creare una connessione VPN tra il gateway. Questa configurazione non supporta gateway statico o basate sui criteri. 

In questo articolo è usare il portale classico, il portale di Azure e PowerShell. Non è attualmente possibile creare questa configurazione tramite il portale di Azure.

### <a name="prerequisites"></a>Prerequisiti

 - Entrambi VNets sono già state create.
 - Gli intervalli di indirizzi per il VNets sovrappongono o non si sovrappongono con tutti gli intervalli per le connessioni che il gateway può essere connesso a.
 - Sono stati installati i cmdlet di PowerShell più recente (1.0.2 o versioni successive). Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Verificare che si installa la gestione di servizio (ROSS) e i cmdlet di Manager delle risorse (RM). 

### <a name="values"></a>Impostazioni di esempio

È possibile utilizzare le impostazioni di esempio come riferimento.

**Impostazioni VNet classiche**

Nome VNet = ClassicVNet <br>
Posizione = US ovest <br>
Gli spazi di indirizzi virtuali = 10.0.0.0/24 <br>
Subnet 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome di rete locale = RMVNetLocal <br>

**Impostazioni di gestione risorse VNet**

Nome VNet = RMVNet <br>
Gruppo di risorse = RG1 <br>
Spazi di indirizzi IP virtuali = 192.168.0.0/16 <br>
Subnet 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Posizione = US orientale <br>
Nome del gateway virtuali = RMGateway <br>
Nome del gateway pubblico IP = gwpip <br>
Tipo di gateway = VPN <br>
Tipo di VPN = basate su Route <br>
Gateway di rete locale = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Sezione 1: Configurare le impostazioni di VNet classiche

In questa sezione, viene creata la rete locale e il gateway per il VNet classica. Le istruzioni disponibili in questa sezione utilizzano il portale classico. Attualmente, il portale di Azure non offre tutte le impostazioni che riguardano un VNet classica.

### <a name="part-1---create-a-new-local-network"></a>Parte 1: creare una nuova rete locale

Aprire il [portale classica](https://manage.windowsazure.com) e accedere con l'account Azure.

1. In angolo inferiore sinistro dello schermo, fare clic su **Nuovo** > **Servizi di rete** > **Virtuali** > **Aggiungi rete locale**.

2. Nella finestra di **specificare i dettagli di rete locale** , digitare un nome per il VNet RM si desidera connettersi. Nella casella **indirizzo IP dispositivo VPN (facoltativo)** digitare qualsiasi indirizzo IP pubblico valido. Questo è solo un segnaposto temporaneo. Modificare questo indirizzo IP in un secondo momento. In angolo inferiore destro della finestra, fare clic sulla freccia.
 
3. Nella pagina **specificare lo spazio di indirizzi** nella casella di testo **IP iniziale** , digitare il prefisso di rete e il blocco CIDR per Manager delle risorse VNet si desidera connettersi. Questa impostazione viene utilizzata per specificare lo spazio di indirizzi per inviare a VNet RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Parte 2: associare la rete locale per il VNet

1. Fare clic su **Reti virtuali** nella parte superiore della pagina per passare alla schermata reti virtuali, quindi fare clic per selezionare il VNet classica. Nella pagina per il VNet, fare clic su **Configura** per passare alla pagina di configurazione.

2. Nella sezione connessione di **connettività al sito** , selezionare la casella di controllo **Connetti alla rete locale** . Quindi selezionare la rete locale che è stato creato. Se si dispone di più reti locali che è stato creato, assicurarsi di selezionare quello che è stato creato per rappresentare le Manager delle risorse VNet nell'elenco a discesa.

3. Fare clic su **Salva** nella parte inferiore della pagina.

### <a name="part-3---create-the-gateway"></a>Parte 3: creare il gateway

1. Dopo il salvataggio delle impostazioni, fare clic su **Dashboard** nella parte superiore della pagina per passare alla pagina del Dashboard. Nella parte inferiore della pagina del Dashboard, fare clic su **Creare Gateway**, quindi fare clic su **Routing dinamico**. Fare clic su **Sì** per iniziare a creare il gateway. Un gateway Routing dinamico è necessario per la configurazione.

2. Attendere che il gateway da creare. Può succedere che possono essere necessari più di 45 minuti per completare.

### <a name="ip"></a>Parte 4: visualizzare l'indirizzo IP pubblico gateway

Dopo aver creato il gateway, è possibile visualizzare l'indirizzo IP del gateway nella pagina **Dashboard** . Questo è l'indirizzo IP pubblico del gateway. Copiare o annotare l'indirizzo IP pubblico. Si usa nei passaggi successivi quando si crea la rete locale per la configurazione di Manager delle risorse VNet.


## <a name="creatermgw"></a>Sezione 2: Configurare le impostazioni di gestione risorse VNet

In questa sezione, vengono create gateway virtuali e la rete locale per il Manager delle risorse VNet. Non avviare la procedura seguente fino a dopo avere recuperato l'indirizzo IP pubblico del gateway del VNet classica.

Le schermate sono illustrate alcuni esempi. Assicurarsi di sostituire i valori con uno personalizzato. Se si sta creando questa configurazione come esercizio, vedere questi [valori](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Parte 1: creare una subnet gateway

Prima di connettersi alla rete aziendale virtuale a un gateway, è innanzitutto necessario creare subnet gateway per la rete virtuale a cui si desidera connettersi. Creare una subnet gateway con conteggio CIDR di /28 pollici (/ 27/26, ecc.)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Da un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2: creare un gateway di rete virtuale


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Parte 3 - creare un gateway di rete locale

Il gateway di rete locale' ' in genere fa riferimento alla posizione locale. Comunica Azure quale indirizzo IP intervalli per instradare la posizione e l'indirizzo IP pubblico del dispositivo per tale posizione. Tuttavia, in questo caso, si riferisce all'intervallo di indirizzi e indirizzo IP pubblico associato il VNet classica e al gateway di rete virtuale.

Specificare un nome per il quale Azure può fare riferimento a tale per il gateway di rete locale. Durante la creazione di gateway virtuali, è possibile creare il gateway di rete locale. Per questa configurazione, utilizzare l'indirizzo IP pubblico che è stata assegnata al gateway VNet classica nella [sezione precedente](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>Parte 4: copiare l'indirizzo IP pubblico

Al termine del creazione gateway virtuali, copiare l'indirizzo IP pubblico associato il gateway. Utilizzare quando si configurano le impostazioni di rete locale per il VNet classica. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Sezione 3: Modificare la rete locale per VNet classica

Aprire il [portale classica](https://manage.windowsazure.com).

2. Nel portale classico, scorrere verso il basso sul lato sinistro e fare clic su **reti**. Nella pagina **reti** , fare clic su **Reti locali** nella parte superiore della pagina. 

3. Fare clic per selezionare la rete locale è configurato nella parte 1. Nella parte inferiore della pagina, fare clic su **Modifica**.

4. Nella pagina **specificare i dettagli di rete locale** , sostituire l'indirizzo IP del segnaposto con l'indirizzo IP pubblico per il gateway di gestione risorse creata nella sezione precedente. Fare clic sulla freccia per passare alla sezione successiva. Verificare che lo **Spazio di indirizzi** sia corretto e quindi fare clic sul segno di spunta per accettare le modifiche.

## <a name="connect"></a>Sezione 4: Creare la connessione

In questa sezione, viene creata la connessione tra il VNets. Procedura per l'oggetto è necessario disporre PowerShell. È possibile creare la connessione in entrambi i portali. Assicurarsi di avere scaricato e installato sia classica (ROSS) e i cmdlet di PowerShell Manager delle risorse (RM).


1. Accedere al proprio account Azure nella console di PowerShell. Il seguente cmdlet richiede le credenziali di accesso per il proprio Account Azure. Dopo avere effettuato l'accesso, le impostazioni dell'account vengono scaricate in modo che siano disponibili per Azure PowerShell.

        Login-AzureRmAccount 

    È possibile ottenere un elenco delle sottoscrizioni Azure se si dispone di più di una sottoscrizione.

        Get-AzureRmSubscription

    Specificare l'abbonamento a cui si desidera utilizzare. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Aggiungere l'Account Azure per utilizzare i cmdlet di PowerShell classici. A tale scopo, è possibile utilizzare il comando seguente:

        Add-AzureAccount

3. Impostare la chiave condivisa eseguendo nell'esempio seguente. In questo esempio, `-VNetName` è il nome del VNet classica e `-LocalNetworkSiteName` è il nome specificato per la rete locale quando è stato configurato nel portale di classica. Il `-SharedKey` è un valore che è possibile generare e specificare. Il valore specificato qui deve essere lo stesso valore specificato nel passaggio successivo quando si crea la connessione.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Creare una connessione VPN eseguendo i comandi seguenti:
    
    **Impostare le variabili**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Creare la connessione**<br> Si noti che la `-ConnectionType` ' IPSec ', non 'Vnet2Vnet'. In questo esempio, `-Name` è il nome che si desidera chiamare la connessione. Nell'esempio seguente viene creata una connessione denominata '*rm-connessione classica*.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Verificare la connessione

È possibile verificare la connessione utilizzando il portale classico, il portale di Azure o PowerShell. È possibile utilizzare la procedura seguente per verificare la connessione. Sostituire i valori con uno personalizzato.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>Domande frequenti su VNet-VNet

Visualizzare i dettagli di domande frequenti per ulteriori informazioni sulle connessioni VNet a VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


