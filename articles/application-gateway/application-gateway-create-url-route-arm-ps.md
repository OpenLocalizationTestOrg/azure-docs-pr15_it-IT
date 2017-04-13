<properties
   pageTitle="Creare un gateway di applicazioni usando le regole di routing URL | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni per creare, configurare un gateway di applicazione Azure usando le regole di routing URL"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-using-path-based-routing"></a>Creare un gateway di applicazione tramite routing basato sul percorso 

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-url-route-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-url-route-arm-ps.md)

URL basato sul percorso routing consente di associare route in base al percorso URL della richiesta Http. Controlla se esiste una route a un pool di back-end configurato per gli elenchi di URL nel Gateway di applicazioni e inviare il traffico di rete al pool di back-end definito. Un utilizzo comune per il routing basato su URL è carico per diversi tipi di contenuto per i pool diversi server di back-end.

Routing basato su URL introduce un nuovo tipo di regola al gateway di applicazione. Gateway di applicazione sono disponibili due tipi di regola: base e PathBasedRouting. Tipo di regola base fornisce servizio circolari per i pool di back-end mentre si PathBasedRouting oltre distribuzione circolari, tiene motivo percorso dell'URL della richiesta in considerazione anche la scelta del pool di back-end.

>[AZURE.IMPORTANT] PathPattern: L'elenco dei modelli di percorso per la corrispondenza. Ogni deve iniziare con / e il luogo solo un "\*" consentito sia posizionato alla fine. Esempi validi sono /xyz /xyz* o /xyz/*. Stringa notte matcher percorso non include il testo eventualmente contenuto dopo il primo "?" o "#" e i caratteri non consentiti. 

## <a name="scenario"></a>Scenario:
Nell'esempio seguente Gateway applicazione serve il traffico verso contoso.com con due pool di server di back-end: pool di server video e il server di immagine.

Richieste per http://contoso.com/image* indirizzati al pool di server immagine (pool1) e http://contoso.com/video* sono indirizzate al pool di server video (pool2). Se nessuno dei modelli percorso corrisponde, un pool di server predefinito (pool1) sia selezionato.

![route dell'URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare l'ultima versione dei cmdlet di PowerShell Azure tramite il programma di installazione di piattaforma Web. È possibile scaricare e installare l'ultima versione dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e subnet per Gateway di applicazioni. Assicurarsi che nessuna macchine virtuali o distribuzioni cloud utilizza subnet. Il gateway di applicazione deve essere una subnet virtuali autonomamente.
3. I server aggiunti al pool di back-end da utilizzare il gateway di applicazione deve essere presente o relativi endpoint creato nella rete virtuale o a un pubblico IP/VIP assegnato.



## <a name="what-is-required-to-create-an-application-gateway"></a>Che cos'è necessario creare un gateway di applicazione?


- **Pool di server di back-end:** Elenco di indirizzi IP di server back-end. Indirizzi IP elencati devono appartenere alla subnet virtuali o devono essere un IP/VIP pubblico.
- **Le impostazioni del pool di server di back-end:** Ogni pool ha impostazioni come porta, il protocollo e affinità basate su cookie. Queste impostazioni sono collegate a un pool e vengono applicate a tutti i server del pool.
- **Porta front-end:** Questa porta è la porta pubblica che viene aperta nel gateway dell'applicazione. Il traffico raggiunge la porta e quindi venga reindirizzato a uno dei server back-end.
- **Comunicare ascoltatore:** Comunicare ascoltatore dispone di una porta front-end, un protocollo (Http o Https, si tratta maiuscole e minuscole) e il nome del certificato SSL (se la configurazione di SSL trasferire).
- **Regola:** La regola associa comunicare ascoltatore, il pool di server di back-end e consente di definire quali pool di server di back-end il traffico deve essere indirizzato al quando arriva una particolare comunicare ascoltatore.

## <a name="create-an-application-gateway"></a>Creare un gateway di applicazioni

La differenza tra Azure classica e gestione di risorse di Azure è l'ordine in cui è stato creato il gateway di applicazioni e gli elementi che devono essere configurati.

Gestione risorse di tutti gli elementi che costituiscono un gateway di applicazioni configurati singolarmente e quindi inserire per creare la risorsa gateway dell'applicazione.


Ecco i passaggi necessari per creare un gateway di applicazione:

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, alla subnet e indirizzo IP pubblico per il gateway di applicazione.
3. Creare un oggetto di configurazione del gateway dell'applicazione.
4. Creare una risorsa di gateway dell'applicazione.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Verificare che si usa la versione più recente di Azure PowerShell. Altre informazioni sono disponibili [all'Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere a Azure

    Login-AzureRmAccount

Viene richiesto di eseguire l'autenticazione con le credenziali.<BR>

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

### <a name="step-3"></a>Passaggio 3

Scegliere quale delle sottoscrizioni Azure da utilizzare. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente).

    New-AzureRmResourceGroup -Name appgw-RG -Location "West US"

In alternativa è anche possibile creare tag per un gruppo di risorse per gateway applicazione:
    
    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway di applicazioni utilizzano lo stesso gruppo di risorse.

Nell'esempio precedente, creato un gruppo di risorse denominato "appgw-RG" e un percorso "Ovest Contattaci".

>[AZURE.NOTE] Se è necessario configurare un sondaggio personalizzato per il gateway di applicazione, vedere [creare un gateway di applicazioni con le ricerche personalizzate tramite PowerShell](application-gateway-create-probe-ps.md). Consultare [le ricerche personalizzate e il monitoraggio dell'integrità](application-gateway-probe-overview.md) per altre informazioni.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway di applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale tramite Gestione risorse.

### <a name="step-1"></a>Passaggio 1

Assegnare il 10.0.0.0/24 intervallo indirizzo per la variabile di subnet da utilizzare per creare una rete virtuale.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### <a name="step-2"></a>Passaggio 2

Creare una rete virtuale denominata "appgwvnet" in risorse gruppo "appgw-rg" per l'area geografica US ovest con 10.0.0.0/16 prefisso subnet 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Passaggio 3

Assegnare una variabile di subnet per i passaggi successivi, che consente di creare un gateway di applicazioni.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica "publicIP01" in risorse gruppo "appgw-rg" per l'area geografica US ovest.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Un indirizzo IP viene assegnato al gateway applicazione all'avvio del servizio.

## <a name="create-application-gateway-configuration"></a>Creare la configurazione di gateway applicazione

Tutti gli elementi di configurazione necessario impostare prima di creare il gateway di applicazione. La procedura seguente crea gli elementi di configurazione necessari per una risorsa di gateway dell'applicazione.

### <a name="step-1"></a>Passaggio 1

Creare un'applicazione di configurazione di IP gateway denominato "gatewayIP01". Quando si avvia applicazione Gateway, raccoglie dal subnet configurato un indirizzo IP e indirizzare il traffico di rete per gli indirizzi IP nel pool di indirizzi IP di back-end. Tenere presente che ogni istanza accetta un indirizzo IP.


    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Passaggio 2

Configurare il pool di indirizzi IP di back-end denominate "pool01" e "pool2" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50" per "pool1" e "134.170.186.46, 134.170.189.221,134.170.186.50" per "pool2".


    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

In questo esempio, sono disponibili due pool di back-end per instradare il traffico di rete in base al percorso URL. Un pool riceve il traffico dal percorso URL "/ video" e altri pool di ricevere il traffico da percorso "/ immagine". Sostituire gli indirizzi IP precedenti per aggiungere il proprio endpoint indirizzo IP di applicazione. 

### <a name="step-3"></a>Passaggio 3

Configurare le applicazioni gateway impostazione "poolsetting01" e "poolsetting02" per il traffico di rete bilanciamento del carico nel pool di back-end. In questo esempio, configurare impostazioni pool di back-end diverso per i pool di back-end. Ogni pool di back-end può avere la propria impostazione del pool di back-end.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>Passaggio 4

Configurare IP front-end con endpoint IP pubblico.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>Passaggio 5 

Configurare la porta front-end per un gateway di applicazioni.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### <a name="step-6"></a>Passaggio 6

Configurare comunicare ascoltatore. Questo passaggio consente di configurare comunicare ascoltatore per l'indirizzo IP pubblico e la porta utilizzato per ricevere il traffico di rete in ingresso. 
 
    $listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### <a name="step-7"></a>Passaggio 7 

Configurare percorsi di regole di URL per i pool di back-end. Questo passaggio consente di configurare il relativo percorso utilizzato da gateway di applicazioni per definire il mapping tra il percorso URL e viene assegnato il pool di back-end per gestire il traffico in entrata.

Nell'esempio seguente vengono create due regole: uno per "immagine /" percorso instradare il traffico verso un altro per "video /" percorso instradare il traffico verso "pool2" back-end e back-end "pool1".
    
    $imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

    $videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

La configurazione delle regole percorso mappa configura un pool di indirizzi di back-end predefinito anche se il percorso non corrisponda a una delle regole di percorso predefinito. 

    $urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### <a name="step-8"></a>Passaggio 8

Creare un'impostazione di regola. Questo passaggio consente di configurare il gateway di applicazione per utilizzare il routing basato sul percorso URL.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap

### <a name="step-9"></a>Passaggio 9

Configurare il numero di istanze e le dimensioni del gateway dell'applicazione.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>Creare applicazioni Gateway

Creare un gateway di applicazioni con tutti gli oggetti di configurazione dai passaggi precedenti.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

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

Se si desiderano ulteriori offload Secure Sockets Layer (SSL), vedere [configurare un gateway di applicazioni per SSL trasferire](application-gateway-ssl-arm.md).