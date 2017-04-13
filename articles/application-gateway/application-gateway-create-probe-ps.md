<properties
   pageTitle="Creare una ricerca personalizzata per Gateway applicazione tramite PowerShell in Gestione risorse | Microsoft Azure"
   description="Informazioni su come creare una ricerca personalizzata per il Gateway di applicazione tramite PowerShell in Gestione risorse"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Creare una ricerca personalizzata per il Gateway di applicazioni Azure tramite PowerShell per gestione di risorse di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-probe-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-probe-ps.md)
- [Azure PowerShell classica](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](application-gateway-create-probe-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Passaggio 1

Consente di eseguire l'autenticazione AzureRmAccount Login.

    Login-AzureRmAccount

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

### <a name="step-3"></a>Passaggio 3

Scegliere quale delle sottoscrizioni Azure da utilizzare. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Questo percorso viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway di applicazioni utilizzano lo stesso gruppo di risorse.

Nell'esempio precedente, creato un gruppo di risorse denominato "appgw-RG" e un percorso "Ovest Contattaci".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway di applicazione

La procedura seguente per creare una rete virtuale subnet per il gateway di applicazione.

### <a name="step-1"></a>Passaggio 1


Assegnare il 10.0.0.0/24 intervallo indirizzo a una variabile di subnet da utilizzare per creare una rete virtuale.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Passaggio 2

Creare una rete virtuale denominata "appgwvnet" in risorse gruppo "appgw-rg" per l'area geografica US ovest con 10.0.0.0/16 prefisso subnet 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Passaggio 3

Assegnare una variabile subnet per i passaggi successivi, quale creare un gateway di applicazioni.

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end


Creare una risorsa IP pubblica "publicIP01" in risorse gruppo "appgw-rg" per l'area geografica US ovest.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Creare un oggetto di configurazione del gateway dell'applicazione con una ricerca personalizzata

Impostare tutti gli elementi di configurazione prima di creare il gateway di applicazione. La procedura seguente crea gli elementi di configurazione necessari per una risorsa di gateway dell'applicazione.

### <a name="step-1"></a>Passaggio 1

Creare un'applicazione di configurazione di IP gateway denominato "gatewayIP01". Quando si avvia applicazione Gateway, raccoglie dal subnet configurato un indirizzo IP e indirizzare il traffico di rete per gli indirizzi IP nel pool di indirizzi IP di back-end. Tenere presente che ogni istanza accetta un indirizzo IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Passaggio 2


Configurare il pool di indirizzi IP di back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". I valori sono gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP per aggiungere il proprio endpoint indirizzo IP di applicazione.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>Passaggio 3


Verifica personalizzato è configurato in questo passaggio.

I parametri vengono utilizzati:

- **Intervallo** - configura i controlli di verifica dell'intervallo in secondi.
- **Timeout** - definisce il timeout di verifica per il controllo di risposta HTTP.
- **Nome dell'host e il percorso** - percorso URL completo richiamato dal Gateway di applicazioni per determinare lo stato dell'istanza. Ad esempio, se si dispone di un sito Web http://contoso.com/, quindi verifica personalizzato configurabile per "http://contoso.com/path/custompath.htm" per i controlli di verifica disporre di una risposta HTTP corretta.
- **UnhealthyThreshold** - il numero di risposte HTTP non riuscite necessari per contrassegnare l'istanza di back-end come *non corretti*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>Passaggio 4

Configurare le applicazioni gateway impostazione "poolsetting01" per il traffico nel pool di back-end. Questo passaggio è installato anche una configurazione di timeout per la risposta di back-end del pool a una richiesta di gateway di applicazione. Quando una risposta di back-end raggiunge un limite di timeout, Gateway applicazione Annulla la richiesta. Questo valore è diverso da un timeout verifica solo per la risposta di back-end per verificare la presenza di controlli.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>Passaggio 5

Configurare la porta IP front-end denominata "frontendport01" per l'endpoint IP pubblico.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>Passaggio 6

Creare la configurazione IP front-end denominata "fipconfig01" e associare l'indirizzo IP pubblico la configurazione IP front-end.


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>Passaggio 7

Creare comunicare ascoltatore nome "listener01" e associare la porta front-end per la configurazione IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>Passaggio 8

Creare la regola di routing bilanciamento carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>Passaggio 9

Configurare la dimensione istanza del gateway dell'applicazione.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è medio. È possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Creare un gateway di applicazioni utilizzando AzureRmApplicationGateway nuovo

Creare un gateway di applicazioni con tutti gli elementi di configurazione dalla procedura indicata sopra. In questo esempio, il gateway di applicazione si chiama "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Aggiungere una ricerca per un gateway di applicazioni esistente

Si dispone di quattro passaggi per aggiungere una ricerca personalizzata per un gateway di applicazioni esistente.

### <a name="step-1"></a>Passaggio 1

Caricare la risorsa gateway dell'applicazione in una variabile di PowerShell utilizzando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Passaggio 2

Aggiungere una ricerca per la configurazione del gateway esistente.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


Nell'esempio, la ricerca personalizzata è configurata per verificare la presenza di contoso.com/path/custompath.htm percorso URL ogni 30 secondi. Un limite di timeout di 120 secondi è configurato con un numero massimo di 8 richieste di verifica non riuscito.

### <a name="step-3"></a>Passaggio 3

Aggiungere la ricerca per la configurazione di impostazione del pool di back-end e timeout utilizzando **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>Passaggio 4

Salvare la configurazione per il gateway di applicazione tramite **Set AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Rimuovere una ricerca da un gateway di applicazioni esistente

Ecco la procedura per rimuovere una ricerca personalizzata da un gateway di applicazioni esistente.

### <a name="step-1"></a>Passaggio 1

Caricare la risorsa gateway dell'applicazione in una variabile di PowerShell utilizzando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>Passaggio 2

Rimuovere la configurazione di ricerca da gateway applicazione utilizzando **AzureRmApplicationGatewayProbeConfig Rimuovi**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>Passaggio 3

Aggiornare l'impostazione del pool di back-end per rimuovere la ricerca e l'impostazione di timeout tramite **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Passaggio 4

Salvare la configurazione per il gateway di applicazione tramite **Set AzureRmApplicationGateway**. 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

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

Informazioni su come configurare SSL scaricando visitando [Configurare trasferire SSL](application-gateway-ssl-arm.md)

