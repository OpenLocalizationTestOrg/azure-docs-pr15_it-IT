<properties
   pageTitle="Creare e configurare un gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB) utilizzando Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni per creare, configurare, avviare ed eliminare un gateway di applicazioni Azure con bilanciamento del carico interno (ILB) per gestione di risorse di Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Creare un gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB) tramite Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Procedura classica Azure](application-gateway-ilb.md)
- [Passaggi di Manager delle risorse PowerShell](application-gateway-ilb-arm.md)

Gateway di applicazioni Azure possono essere configurati con un indirizzo VIP esposto a Internet o con un endpoint interno che non viene esposto su Internet, noto anche come un endpoint di bilanciamento del carico (ILB) carico interno. Configurazione del gateway con un ILB è utile per applicazioni line-of-business interne che non viene esposto su Internet. È utile anche per i servizi e i livelli all'interno di un'applicazione a più livelli che si trovano in un limite di protezione che non viene esposto su Internet, ma richiedono comunque il circolari caricano distribuzione, aderenza sessione o terminazione Secure Sockets Layer (SSL).

In questo articolo sono illustrati i passaggi per configurare un gateway di applicazioni con un ILB.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare l'ultima versione dei cmdlet di PowerShell Azure tramite il programma di installazione di piattaforma Web. È possibile scaricare e installare l'ultima versione dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per Gateway di applicazioni. Assicurarsi che nessuna macchine virtuali o distribuzioni cloud utilizza subnet. Applicazione Gateway deve essere da solo in una subnet virtuali.
3. I server configurati per utilizzare il gateway di applicazione deve essere presente o relativi endpoint creati con la rete virtuale o a un pubblico IP/VIP assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Che cos'è necessario creare un gateway di applicazione?


- **Pool di server di back-end:** Elenco di indirizzi IP di server back-end. Indirizzi IP elencati devono appartenere alla rete virtuale ma in un'altra per il gateway di applicazione o devono essere un IP/VIP pubblico.
- **Le impostazioni del pool di server di back-end:** Ogni pool ha impostazioni come porta, il protocollo e affinità basate su cookie. Queste impostazioni sono collegate a un pool e vengono applicate a tutti i server del pool.
- **Porta front-end:** Questa porta è la porta pubblica che viene aperta nel gateway dell'applicazione. Il traffico raggiunge la porta e quindi venga reindirizzato a uno dei server back-end.
- **Comunicare ascoltatore:** Comunicare ascoltatore dispone di una porta front-end, un protocollo (Http o Https, si tratta maiuscole e minuscole) e il nome del certificato SSL (se la configurazione di SSL trasferire).
- **Regola:** La regola associa comunicare ascoltatore e al pool di server di back-end e consente di definire quali pool di server di back-end il traffico deve essere indirizzato al quando arriva una particolare comunicare ascoltatore. Attualmente non è supportata solo la regola di *base* . La regola di *base* è distribuzione carico circolari.



## <a name="create-an-application-gateway"></a>Creare un gateway di applicazioni

La differenza tra Azure classica e gestione di risorse di Azure è l'ordine in cui è stato creato il gateway di applicazioni e gli elementi che devono essere configurati.
Gestione risorse di tutti gli elementi che costituiscono un gateway di applicazioni viene configurato singolarmente e quindi inserire per creare la risorsa gateway dell'applicazione.


Ecco i passaggi necessari per creare un gateway di applicazione:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale e una subnet per il gateway di applicazione
3. Creare un oggetto di configurazione del gateway dell'applicazione
4. Creare una risorsa di gateway dell'applicazione


## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di cambiare la modalità di PowerShell per utilizzare i cmdlet Gestione risorse Azure. Altre informazioni sono disponibili [all'Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

    Login-AzureRmAccount

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

Viene richiesto di eseguire l'autenticazione con le credenziali.<BR>

### <a name="step-3"></a>Passaggio 3

Scegliere quale delle sottoscrizioni Azure da utilizzare. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway di applicazioni utilizza stesso gruppo di risorse.

Nell'esempio precedente, creato un gruppo di risorse denominato "appgw-rg" e un percorso "Ovest Contattaci".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway di applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale tramite Gestione risorse:

### <a name="step-1"></a>Passaggio 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Per l'assegnazione di 10.0.0.0/24 l'intervallo indirizzo a una variabile di subnet da utilizzare per creare una rete virtuale.

### <a name="step-2"></a>Passaggio 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

In questo modo si crea una rete virtuale denominata "appgwvnet" in risorse gruppo "appgw-rg" per l'area geografica US ovest con 10.0.0.0/16 prefisso subnet 10.0.0.0/24.

### <a name="step-3"></a>Passaggio 3

    $subnet = $vnet.subnets[0]

L'oggetto subnet viene assegnato alla variabile $subnet per i passaggi successivi.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione del gateway dell'applicazione

### <a name="step-1"></a>Passaggio 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

In questo modo si crea una configurazione IP del gateway applicazione denominata "gatewayIP01". Quando si avvia applicazione Gateway, raccoglie dal subnet configurato un indirizzo IP e indirizzare il traffico di rete per gli indirizzi IP nel pool di indirizzi IP di back-end. Tenere presente che ogni istanza accetta un indirizzo IP.


### <a name="step-2"></a>Passaggio 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

In questo modo il pool di indirizzi IP di back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Si tratta gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP per aggiungere il proprio endpoint indirizzo IP di applicazione.

### <a name="step-3"></a>Passaggio 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Consente di configurare il traffico di rete gateway impostazione "poolsetting01" per il carico bilanciato applicazione nel pool di back-end.

### <a name="step-4"></a>Passaggio 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Consente di configurare la porta IP front-end denominata "frontendport01" per il ILB.

### <a name="step-5"></a>Passaggio 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Si crea la configurazione IP front-end denominata "fipconfig01" e associa a un indirizzo IP privato da subnet virtuali corrente.

### <a name="step-6"></a>Passaggio 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Verrà creata comunicare ascoltatore denominata "listener01" e associa la porta front-end per la configurazione IP front-end.

### <a name="step-7"></a>Passaggio 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Verrà creata la regola di routing bilanciamento carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

### <a name="step-8"></a>Passaggio 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Consente di configurare le dimensioni di istanza del gateway dell'applicazione.

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è medio. È possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creare un gateway di applicazioni utilizzando AzureApplicationGateway nuovo

Crea un gateway di applicazioni con tutti gli elementi di configurazione dalla procedura indicata sopra. In questo esempio, il gateway di applicazione si chiama "appgwtest".


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Consente di creare un gateway di applicazioni con tutti gli elementi di configurazione dalla procedura indicata sopra. Nell'esempio, il gateway di applicazione è denominato "appgwtest".


## <a name="delete-an-application-gateway"></a>Eliminare un gateway di applicazioni

Per eliminare un gateway di applicazioni, sarà necessario eseguire le operazioni seguenti nell'ordine:

1. Utilizzare il cmdlet **AzureRmApplicationGateway Interrompi** per interrompere il gateway.
2. Utilizzare il cmdlet **AzureRmApplicationGateway Rimuovi** per rimuovere il gateway.
3. Verificare che il gateway è stata rimossa utilizzando il cmdlet **Get-AzureApplicationGateway** .


### <a name="step-1"></a>Passaggio 1

Ottenere l'oggetto di gateway di applicazione e associare a una variabile di tipo "$getgw".

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Passaggio 2

Utilizzare **AzureRmApplicationGateway Interrompi** per interrompere il gateway di applicazione. In questo esempio mostra il cmdlet **Stop AzureRmApplicationGateway** nella prima riga, seguita dall'output.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Dopo aver aggiunto il gateway di applicazione allo stato di interruzione, utilizzare il cmdlet **AzureRmApplicationGateway Rimuovi** per rimuovere il servizio.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Il **-forzare** parametro può essere utilizzato per eliminare il messaggio di conferma Rimuovi.


Per verificare che il servizio è stato rimosso, è possibile utilizzare il cmdlet **Get-AzureRmApplicationGateway** . Questo passaggio non sono necessario.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare offload SSL, vedere [configurare un gateway di applicazioni per SSL trasferire](application-gateway-ssl.md).

Se si vuole configurare un gateway di applicazioni da utilizzare con un ILB, vedere [creare un gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per ulteriori informazioni sulle opzioni di bilanciamento di carico in generale, vedere:

- [Bilanciamento del carico Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestore del traffico Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
