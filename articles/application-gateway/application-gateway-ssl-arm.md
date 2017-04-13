<properties
   pageTitle="Configurare un gateway di applicazioni per offload SSL utilizzando Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagine include istruzioni per creare un gateway di applicazioni con SSL trasferire tramite Gestione risorse di Azure"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurare un gateway di applicazioni per offload SSL utilizzando Gestione risorse di Azure

> [AZURE.SELECTOR]
-[Portale di Azure](application-gateway-ssl-portal.md)
-[Azure Manager delle risorse PowerShell](application-gateway-ssl-arm.md)
-[Azure PowerShell classica](application-gateway-ssl.md)

 Azure Application Gateway può essere configurato per terminare la sessione di Secure Sockets Layer (SSL) gateway per evitare i costi attività di decrittografia SSL per verificarsi presso web farm. Offload SSL anche semplifica la gestione dell'applicazione web server front-end.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare l'ultima versione dei cmdlet di PowerShell Azure tramite il programma di installazione di piattaforma Web. È possibile scaricare e installare l'ultima versione dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Per creare una rete virtuale e una subnet per il gateway di applicazione. Assicurarsi che nessuna macchine virtuali o distribuzioni cloud utilizza subnet. Applicazione Gateway deve essere da solo in una subnet virtuali.
3. I server configurati per utilizzare il gateway di applicazione deve essere presente o relativi endpoint creati con la rete virtuale o a un pubblico IP/VIP assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Che cos'è necessario creare un gateway di applicazione?


- **Pool di server di back-end:** Elenco di indirizzi IP di server back-end. Indirizzi IP elencati devono appartenere alla subnet virtuali o devono essere un IP/VIP pubblico.
- **Le impostazioni del pool di server di back-end:** Ogni pool ha impostazioni come porta, il protocollo e affinità basate su cookie. Queste impostazioni sono collegate a un pool e vengono applicate a tutti i server del pool.
- **Porta front-end:** Questa porta è la porta pubblica che viene aperta nel gateway dell'applicazione. Il traffico raggiunge la porta e quindi venga reindirizzato a uno dei server back-end.
- **Comunicare ascoltatore:** Comunicare ascoltatore dispone di una porta front-end, un protocollo (Http o Https, tali impostazioni sono maiuscole e minuscole) e il nome del certificato SSL (se la configurazione di SSL trasferire).
- **Regola:** La regola associa comunicare ascoltatore e al pool di server di back-end e consente di definire quali pool di server di back-end il traffico deve essere indirizzato al quando arriva una particolare comunicare ascoltatore. Attualmente non è supportata solo la regola di *base* . La regola di *base* è distribuzione carico circolari.

**Note sulla configurazione aggiuntive**

Per la configurazione dei certificati SSL, il protocollo **HttpListener** dovrebbe essere *Https* (maiuscole/minuscole). L'elemento **SslCertificate** viene aggiunto a **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere aggiornata alla porta 443.

**Per abilitare affinità basate su cookie**: è possibile configurare un gateway di applicazioni per assicurare che una richiesta da una sessione client sia sempre diretti in macchine Virtuali stessa web farm. Questo scenario è possibile inserimento di un cookie sessione che consente al gateway indirizzare il traffico in modo appropriato. Per attivare affinità basate su cookie, impostare **CookieBasedAffinity** su *attivato* nell'elemento **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Creare un gateway di applicazioni

La differenza tra l'utilizzo di Azure classico modello di distribuzione e Gestione risorse di Azure è l'ordine in cui si crea un gateway di applicazioni e gli elementi che devono essere configurati.

Gestione risorse di tutti i componenti di un gateway di applicazioni sono configurati singolarmente e quindi inserire per creare una risorsa di gateway dell'applicazione.


Ecco i passaggi necessari per creare un gateway di applicazione:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare virtuali, subnet e indirizzo IP pubblico per il gateway di applicazione
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

Creare un gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Questa impostazione viene usata come posizione predefinita per le risorse in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway di applicazioni utilizza stesso gruppo di risorse.

Nell'esempio precedente, creato un gruppo di risorse denominato "appgw-RG" e un percorso "Ovest Contattaci".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway di applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale tramite Gestione risorse:

### <a name="step-1"></a>Passaggio 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

In questo esempio assegna la 10.0.0.0/24 intervallo indirizzo a una variabile di subnet da utilizzare per creare una rete virtuale.

### <a name="step-2"></a>Passaggio 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

In questo esempio viene creata una rete virtuale denominata "appgwvnet" in risorse gruppo "appgw-rg" per l'area geografica US ovest con 10.0.0.0/16 prefisso subnet 10.0.0.0/24.

### <a name="step-3"></a>Passaggio 3

    $subnet = $vnet.Subnets[0]

In questo esempio l'oggetto subnet viene assegnato alla variabile $subnet per i passaggi successivi.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

In questo esempio viene creata una risorsa IP pubblica "publicIP01" in risorse gruppo "appgw-rg" per l'area geografica US ovest.


## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione del gateway dell'applicazione

### <a name="step-1"></a>Passaggio 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

In questo esempio viene creata una configurazione IP del gateway applicazione denominata "gatewayIP01". Quando si avvia applicazione Gateway, raccoglie dal subnet configurato un indirizzo IP e indirizzare il traffico di rete per gli indirizzi IP nel pool di indirizzi IP di back-end. Tenere presente che ogni istanza accetta un indirizzo IP.

### <a name="step-2"></a>Passaggio 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

In questo esempio consente di configurare il pool di indirizzi IP di back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50." I valori sono gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP dell'esempio precedente con gli indirizzi IP dell'endpoint di applicazione web.

### <a name="step-3"></a>Passaggio 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

In questo esempio Configura applicazione gateway impostazione "poolsetting01" per il traffico di rete bilanciamento del carico nel pool di back-end.

### <a name="step-4"></a>Passaggio 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

In questo esempio consente di configurare la porta IP front-end denominata "frontendport01" per l'endpoint IP pubblico.

### <a name="step-5"></a>Passaggio 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

In questo esempio consente di configurare il certificato usato per connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compreso tra i caratteri da 4 a 12.

### <a name="step-6"></a>Passaggio 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

In questo esempio viene creata la configurazione IP front-end denominata "fipconfig01" e associa l'indirizzo IP pubblico la configurazione IP front-end.

### <a name="step-7"></a>Passaggio 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


In questo esempio viene creato il nome di comunicare ascoltatore "listener01" e associa la porta front-end e configurazione di IP front-end.

### <a name="step-8"></a>Passaggio 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

In questo esempio viene creata la regola di routing bilanciamento carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

### <a name="step-9"></a>Passaggio 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

In questo esempio consente di configurare le dimensioni di istanza del gateway dell'applicazione.

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è medio. È possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creare un gateway di applicazioni utilizzando AzureApplicationGateway nuovo

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

In questo esempio viene creato un gateway di applicazioni con tutti gli elementi di configurazione dai passaggi precedenti. Nell'esempio, il gateway di applicazione è denominato "appgwtest".

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome dell'applicazione gateway DNS

Dopo aver creato il gateway, il passaggio successivo consiste nel configurare front-end per la comunicazione. Quando si usa un indirizzo IP pubblico, applicazione gateway richiede un nome DNS assegnato dinamicamente, ovvero non è descrittivo. Per garantire agli utenti finali possibile raggiungere il gateway di applicazione un record CNAME può essere utilizzato in modo che puntino all'endpoint pubblico del gateway dell'applicazione. [Configurazione di un nome di dominio personalizzato per in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). A tale scopo, recuperare i dettagli del gateway dell'applicazione e il relativo nome IP/DNS associato utilizzando l'elemento PublicIPAddress collegato al gateway dell'applicazione. Nome DNS del gateway dell'applicazione deve essere utilizzato per creare un record CNAME che punta le applicazioni due web per questo nome DNS. L'uso di un record non è consigliabile poiché l'indirizzo VIP può cambiare al riavvio del gateway dell'applicazione.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Passaggi successivi

Se si vuole configurare un gateway di applicazioni da utilizzare con un sistema di bilanciamento del carico interno (ILB), vedere [creare un gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per ulteriori informazioni sulle opzioni di bilanciamento di carico in generale, vedere:

- [Bilanciamento del carico Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestore del traffico Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
