<properties 
   pageTitle="Come connettersi classiche reti virtuali a Gestione risorse reti virtuali tramite PowerShell | Microsoft Azure"
   description="Informazioni su come creare una connessione VPN tra classica VNets e Manager delle risorse VNets utilizzando Gateway VPN e PowerShell"
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
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Connettere reti virtuali da diversi modelli di distribuzione tramite PowerShell

> [AZURE.SELECTOR]
- [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure è attualmente modelli di gestione delle due: classica e Manager delle risorse (RM). Se è stato utilizzato Azure per un certo tempo, è probabile che sia macchine virtuali di Azure e ruoli istanza in esecuzione in una VNet classica. Il più recente macchine virtuali e istanze del ruolo potrebbero essere in esecuzione in una VNet creati in Gestione risorse. In questo articolo illustra la connessione classica VNets a Manager delle risorse VNets per consentire le risorse presenti nei modelli di distribuzione separata per comunicare con loro tramite una connessione di gateway.

È possibile creare una connessione tra VNets negli abbonamenti a diversi e in diverse aree geografiche. È inoltre possibile connettersi VNets che già dispongono di connessioni a reti locale, come il gateway che sono stati configurati con è basato su route o dinamico. Per ulteriori informazioni sulle connessioni VNet-VNet, vedere [domande frequenti su VNet a VNet](#faq) alla fine di questo articolo.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Modelli di distribuzione e i metodi per la connessione VNets in diversi modelli di distribuzione

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Viene aggiornato nella tabella seguente come nuovi articoli e altri strumenti disponibili per la configurazione. Quando un articolo è disponibile, è collega direttamente dalla tabella.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>Peering VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Prima di iniziare

La procedura seguente scorrere le impostazioni necessarie per configurare un gateway dinamico o basate su route per ogni VNet e creare una connessione VPN tra il gateway. Questa configurazione non supporta gateway statico o basate sui criteri.

### <a name="prerequisites"></a>Prerequisiti di

 - Entrambi VNets sono già state create.
 - Gli intervalli di indirizzi per il VNets sovrappongono o non si sovrappongono con tutti gli intervalli per le connessioni che il gateway può essere connesso a.
 - Sono stati installati i cmdlet di PowerShell più recente (1.0.2 o versioni successive). Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Verificare che si installa la gestione di servizio (ROSS) e i cmdlet di Manager delle risorse (RM). 

### <a name="exampleref"></a>Impostazioni di esempio

È possibile utilizzare le impostazioni di esempio come riferimento.

**Impostazioni VNet classiche**

Nome VNet = ClassicVNet <br>
Posizione = US ovest <br>
Gli spazi di indirizzi virtuali = 10.0.0.0/24 <br>
Subnet 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome di rete locale = RMVNetLocal <br>
GatewayType = DynamicRouting

**Impostazioni di gestione risorse VNet**

Nome VNet = RMVNet <br>
Gruppo di risorse = RG1 <br>
Spazi di indirizzi IP virtuali = 192.168.0.0/16 <br>
Subnet 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Posizione = US orientale <br>
Nome del gateway pubblico IP = gwpip <br>
Gateway di rete locale = ClassicVNetLocal <br>
Nome Gateway di rete virtuale = RMGateway <br>
Configurazione di indirizzi IP di gateway = gwipconfig


## <a name="createsmgw"></a>Sezione 1 - configurare VNet classica

### <a name="part-1---download-your-network-configuration-file"></a>Parte 1: scaricare il file di configurazione della rete

1. Accedere al proprio account Azure nella console di PowerShell con diritti elevati. Il seguente cmdlet richiede le credenziali di accesso per il proprio Account Azure. Dopo avere effettuato l'accesso, il download delle impostazioni dell'account, in modo che siano disponibili per Azure PowerShell. Si utilizzeranno i cmdlet di PowerShell ROSS per completare questa parte della configurazione.

        Add-AzureAccount

2. Esportare il file di configurazione della rete Azure eseguendo il comando seguente. È possibile modificare il percorso del file da esportare in un percorso diverso, se necessario. Modificare il file verrà importarlo in Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Aprire il file con estensione XML che è stato scaricato per modificarlo. Per un esempio di file di configurazione della rete, vedere lo [Schema di configurazione di rete](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Parte 2 - verificare la subnet gateway

Nell'elemento **VirtualNetworkSites** , aggiungere una subnet gateway il VNet se non ne è stata creata. Quando si utilizzano i file di configurazione della rete, subnet gateway deve essere denominata "GatewaySubnet" o Azure non sia riconosciuta e usarla come subnet gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Esempio:**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Parte 3: aggiungere il sito di rete locale

Il sito di rete locale che è aggiungere rappresenta VNet RM a cui si desidera connettersi. Potrebbe essere necessario aggiungere un elemento **LocalNetworkSites** al file se non esiste già. Nella configurazione, a questo punto la VPNGatewayAddress può essere qualsiasi indirizzo IP pubblico perché non è ancora creato il gateway per Manager delle risorse VNet. Una volta abbiamo creare il gateway, si sostituita questo indirizzo IP segnaposto con l'indirizzo IP pubblico corretto assegnato al gateway RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>Parte 4 - associare il VNet al sito di rete locale

In questa sezione è specificare il sito di rete locale che si desidera connettere VNet a. In questo caso è il Manager delle risorse VNet che si è fatto riferimento in precedenza. Assicurarsi che i nomi corrispondenti. Questo passaggio non viene creato un gateway. Specifica la rete locale che si connetterà il gateway.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Parte 5: salvare il file e caricare

Salvare il file, quindi si importeranno in Azure eseguendo il comando seguente. Assicurarsi di modificare il percorso del file in base alle esigenze per l'ambiente.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Verrà visualizzata sarà simile a questo risultato che mostra che l'importazione completata.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Parte 6: creare il gateway 

È possibile creare il gateway VNet tramite il portale classico o tramite PowerShell.

Consente di creare un gateway routing dinamico nell'esempio seguente:

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

È possibile verificare lo stato del gateway usando il `Get-AzureVNetGateway` cmdlet.


## <a name="creatermgw"></a>Sezione 2: Configurare il gateway VNet RM

Per creare un gateway VPN per VNet RM, seguire le istruzioni seguenti. Non avviare la procedura fino a dopo avere recuperato l'indirizzo IP pubblico del gateway del VNet classica. 

1. **Accesso al proprio account Azure** nella console di PowerShell. Il seguente cmdlet richiede le credenziali di accesso per il proprio Account Azure. Dopo avere effettuato l'accesso, le impostazioni dell'account vengono scaricate in modo che siano disponibili per Azure PowerShell.

        Login-AzureRmAccount 

    È possibile ottenere un elenco delle sottoscrizioni Azure se si dispone di più di una sottoscrizione.

        Get-AzureRmSubscription

    Specificare l'abbonamento a cui si desidera utilizzare. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Creare un gateway di rete locale**. In una rete virtuale il gateway di rete locale indica in genere la posizione dell'utente locale. In questo caso, il gateway di rete locale fa riferimento al VNet classica. Assegnare un nome per il quale Azure possa fare riferimento a tale e anche specificare il prefisso di spazio di indirizzi. Azure utilizza il prefisso dell'indirizzo IP specificato per identificare il tipo di traffico per inviare la posizione dell'utente locale. Se è necessario modificare le informazioni visualizzate in seguito, prima di creare il gateway, è possibile modificare i valori ed eseguire nuovamente l'esempio.<br><br>
    - `-Name`è il nome da assegnare per fare riferimento al gateway di rete locale.<br>
    - `-AddressPrefix`è lo spazio di indirizzi per il VNet classica.<br>
    - `-GatewayIpAddress`è l'indirizzo IP pubblico del gateway del VNet classica. Assicurarsi di modificare nell'esempio seguente viene in base all'indirizzo IP corretto.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Richiedere un indirizzo IP pubblico** da allocare per il gateway di rete virtuale per Manager delle risorse VNet. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. L'indirizzo IP dinamicamente allocata per il gateway di rete virtuale. Tuttavia, ciò significa che verrà modificato l'indirizzo IP. Solo le modifiche di indirizzi IP di gateway virtuali è quando il gateway viene eliminato e ricreato. Non è possibile modificare tra il ridimensionamento, reimpostare o altri interni/aggiornamenti di manutenzione del gateway.<br>In questo passaggio, è inoltre possibile impostare variabile che viene utilizzata in un passaggio successivo.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Verificare che la rete virtuale dispone di una subnet gateway**. Se è presente alcuna subnet gateway, aggiungerne uno. Assicurarsi che la subnet gateway denominata *GatewaySubnet*.

5. **Recuperare la subnet** utilizzato per il gateway eseguendo il comando seguente. In questo passaggio, è inoltre possibile impostare una variabile da utilizzare nel passaggio successivo.
    - `-Name`è il nome del VNet Manager delle risorse.
    - `-ResourceGroupName`è il gruppo di risorse è associato il VNet. Subnet gateway deve essere già presente per questo VNet e deve essere denominata *GatewaySubnet* per il corretto funzionamento.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Creare la configurazione di indirizzi IP di gateway**. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Nell'esempio seguente consente di creare la configurazione del gateway.<br>In questo passaggio, la `-SubnetId` e `-PublicIpAddressId` parametri devono essere passati la proprietà id dalla subnet e gli oggetti indirizzo IP, rispettivamente. Non è possibile utilizzare una stringa semplice. Queste variabili sono impostate nel passaggio per richiedere un indirizzo IP pubblico e il passaggio per recuperare la subnet.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Creare il gateway di gestione risorse virtuali** eseguendo il comando seguente. Il `-VpnType` deve essere *RouteBased*. È possibile richiedere 45 minuti per questa opzione per completare.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Copiare l'indirizzo IP pubblico** una volta il gateway VPN è stata creata. Utilizzare quando si configurano le impostazioni di rete locale per il VNet classica. È possibile utilizzare il cmdlet seguente per recuperare l'indirizzo IP pubblico. L'indirizzo IP pubblico è elencato il ritorno come *indirizzo IP*.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Sezione 3: Modificare la rete locale per VNet classica

È possibile eseguire questo passaggio o mediante esportando il file di configurazione della rete, modifica, quindi salvare e importazione del file di nuovo in Azure. In alternativa, è possibile modificare questa impostazione nel portale di classica. 

### <a name="to-modify-in-the-portal"></a>Per modificare nel portale

1. Aprire il [portale classica](https://manage.windowsazure.com).

2. Nel portale classico, scorrere verso il basso sul lato sinistro e fare clic su **reti**. Nella pagina **reti** , fare clic su **Reti locali** nella parte superiore della pagina. 

3. Nella pagina **Reti locale** , fare clic per selezionare la rete locale configurato nella parte 1, quindi passare alla parte inferiore della pagina e fare clic su **Modifica**.

4. Nella pagina **specificare i dettagli di rete locale** , sostituire l'indirizzo IP del segnaposto con l'indirizzo IP pubblico per il gateway di gestione risorse creata nella sezione precedente. Fare clic sulla freccia per passare alla sezione successiva. Verificare che lo **Spazio di indirizzi** sia corretto e quindi fare clic sul segno di spunta per accettare le modifiche.

### <a name="to-modify-using-the-network-configuration-file"></a>Per modificare utilizzando il file di configurazione della rete

1. Esportare il file di configurazione della rete.
2. In un editor di testo, modificare l'indirizzo IP del gateway VPN.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Salvare le modifiche e importare il file modificato Azure.


## <a name="connect"></a>Sezione 4: Creare una connessione tra il gateway

Creazione di una connessione tra il gateway richiede PowerShell. Potrebbe essere necessario aggiungere l'Account Azure per utilizzare i cmdlet di PowerShell classici. A tale scopo, è possibile utilizzare il cmdlet seguente: 
        
    Add-AzureAccount

1. **Impostare la chiave condivisa** , eseguire il comando seguente. In questo esempio, `-VNetName` è il nome del VNet classica e `-LocalNetworkSiteName` è il nome specificato per la rete locale quando è stato configurato nel portale di classica. Il `-SharedKey` è un valore che è possibile generare e specificare. Il valore specificato qui deve essere lo stesso valore specificato nel passaggio successivo quando si crea la connessione. Invio di questo esempio deve essere visualizzata **stato: completato**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Creare la connessione VPN eseguendo i comandi seguenti.

    **Impostare le variabili**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Creare la connessione**<br> Si noti che la `-ConnectionType` IPSec, non Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. È possibile visualizzare la connessione in uno dei due portale o tramite il `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Domande frequenti su VNet-VNet

Visualizzare i dettagli di domande frequenti per ulteriori informazioni sulle connessioni VNet a VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


