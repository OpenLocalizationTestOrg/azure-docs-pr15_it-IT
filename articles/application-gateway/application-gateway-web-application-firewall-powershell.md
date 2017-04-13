<properties
   pageTitle="Configurare il Firewall di applicazione Web sul nuovo o esistente Gateway applicazione | Microsoft Azure"
   description="In questo articolo fornisce indicazioni su come iniziare a utilizzare firewall di applicazione web in un gateway di applicazione nuovo o esistente."
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurare il Firewall di applicazione Web in un Gateway di applicazione nuovo o esistente

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-web-application-firewall-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-web-application-firewall-powershell.md)

Il firewall di applicazione web (WAF) in Azure applicazione Gateway protegge le applicazioni web da attacchi comuni basato sul web come inserimento SQL, attacchi di script e assume il controllo della sessione.

Gateway di applicazione Azure è un bilanciamento del carico di livello 7. Fornisce failover, routing prestazioni richieste HTTP tra server diversi, se sono nel cloud o locale. Applicazione offre molte caratteristiche di applicazione recapito Controller (ADC) inclusi bilanciamento del carico HTTP, affinità sessione basata su cookie, trasferire Secure Sockets Layer (SSL), le ricerche dello stato personalizzato, il supporto per più siti e molti altri. Per trovare un elenco completo delle funzionalità supportate, vedere applicazione Gateway Panoramica

Il seguente articolo mostra come [aggiungere firewall di applicazione web per un gateway di applicazioni esistente](#add-web-application-firewall-to-an-existing-application-gateway) e [creare un gateway di applicazioni che utilizza il firewall di applicazione web](#create-an-application-gateway-with-web-application-firewall).

![immagine di uno scenario][scenario]

## <a name="waf-configuration-differences"></a>Differenze di configurazione WAF

Se è stato consultato l'articolo [creare un Gateway di applicazioni con PowerShell](application-gateway-create-gateway-arm.md), comprendere le impostazioni di SKU per configurare durante la creazione di un gateway di applicazioni. WAF fornisce impostazioni aggiuntive per definire quando si configurano lo SKU di un gateway di applicazioni. Non sono disponibili altre modifiche apportate il gateway di applicazione.

**SKU** - un gateway di applicazione normale senza WAF supporta **Standard\_Small**, **Standard\_medio**, e **Standard\_grande** dimensioni. Con l'introduzione di WAF, sono disponibili due SKU aggiuntive, **WAF\_medio** e **WAF\_grande**. WAF non è supportato in gateway piccola applicazione.

**Livello** - i valori disponibili sono **Standard** o **WAF**. Quando si utilizza il Firewall di applicazione Web, è necessario scelto **WAF** .

**Modalità** - questa impostazione è la modalità di WAF. valori consentiti sono **rilevamento** e **protezione**. Quando WAF sia configurata in modalità di rilevamento, tutti i rischi vengono memorizzati in un file di log. Nella modalità di prevenzione eventi sono ancora eseguito l'accesso ma il pirata informatico riceve una risposta non autorizzato 403 da gateway dell'applicazione.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Aggiungere un gateway di applicazioni esistente firewall applicazione web

Verificare che si usa la versione più recente di Azure PowerShell. Altre informazioni sono disponibili [all'Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere al proprio Account Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Passaggio 2

Selezionare l'abbonamento da utilizzare per questo scenario.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Passaggio 3

Recuperare il gateway che si sta aggiungendo Firewall di applicazione Web per.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Passaggio 4

Configurare lo sku di firewall applicazione web. Le dimensioni disponibili sono **WAF\_grande** e **WAF\_medio**. Quando il firewall di applicazione web viene utilizzato il livello deve essere **WAF**, la capacità deve essere confermata quando si imposta la sku.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Passaggio 5

Configurare le impostazioni di WAF come definito nell'esempio seguente:

Affinché l'impostazione **WafMode** , i valori disponibili sono prevenzione.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Passaggio 6

Aggiornare il gateway di applicazione con le impostazioni definite nel passaggio precedente.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Questo comando Aggiorna il gateway di applicazione con i Firewall dell'applicazione Web. È consigliabile visualizzare [Applicazione Gateway diagnostica](application-gateway-diagnostics.md) per comprendere come visualizzare i registri per il gateway di applicazione. A causa della natura di sicurezza di WAF, i registri necessario rivedere periodicamente per comprendere le condizioni di protezione delle applicazioni web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Creare un Gateway di applicazioni con Firewall di applicazione Web

La procedura seguente vengono illustrate l'intero processo dall'inizio alla fine per la creazione di un Gateway di applicazioni con Firewall di applicazione Web.

Verificare che si usa la versione più recente di Azure PowerShell. Altre informazioni sono disponibili [all'Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere a Azure

    Login-AzureRmAccount

Viene richiesto di eseguire l'autenticazione con le credenziali.

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

### <a name="step-3"></a>Passaggio 3

Scegliere quale delle sottoscrizioni Azure da utilizzare.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Questo percorso viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway di applicazioni utilizza stesso gruppo di risorse.

Nell'esempio precedente, creato un gruppo di risorse denominato "appgw-RG" e un percorso "Ovest Contattaci".

>[AZURE.NOTE] Se è necessario configurare un sondaggio personalizzato per il gateway di applicazione, vedere [creare un gateway di applicazioni con le ricerche personalizzate tramite PowerShell](application-gateway-create-probe-ps.md). Consultare [le ricerche personalizzate e il monitoraggio dell'integrità](application-gateway-probe-overview.md) per altre informazioni.

### <a name="step-5"></a>Passaggio 5

Assegnare un intervallo di indirizzi per la subnet da utilizzare per il Gateway di applicazione stesso.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Una subnet per un'applicazione deve avere un minimo di 28 bit maschera. Questo valore lascia 10 indirizzi disponibili in subnet per le istanze di Gateway di applicazioni. Con una subnet più piccole, potrebbe non essere possibile aggiungere più istanze di gateway applicazione in futuro.

### <a name="step-6"></a>Passaggio 6

Assegnare un intervallo di indirizzi da utilizzare per il pool di indirizzi back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Passaggio 7

Creare una rete virtuale con subnet precedente nel gruppo di risorse creata nel passaggio: [creare il gruppo di risorse](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Passaggio 8

Recuperare le risorse virtuali e le risorse di subnet da utilizzare nei passaggi seguenti:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Passaggio 9

Creare una risorsa IP pubblica da utilizzare per il gateway di applicazione. L'indirizzo IP pubblico viene utilizzata in una delle operazioni seguenti:

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Applicazione Gateway non supporta l'uso di un indirizzo IP pubblico creato con un'etichetta di dominio definita. È supportato solo un indirizzo IP pubblico con un'etichetta di dominio in modo dinamico creato. Se si richiede un nome descrittivo dns per il gateway di applicazione, è consigliabile utilizzare un record cname come alias.

### <a name="step-10"></a>Passaggio 10

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway di applicazione. La procedura seguente crea gli elementi di configurazione necessari per una risorsa di gateway dell'applicazione.

Creare una configurazione IP gateway un'applicazione, in questo modo quali subnet viene utilizzato il Gateway di applicazione. Quando si avvia applicazione Gateway, raccoglie dal subnet configurato un indirizzo IP e indirizza il traffico di rete per gli indirizzi IP nel pool di indirizzi IP di back-end. Tenere presente che ogni istanza accetta un indirizzo IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Passaggio 11

Configurare il pool di indirizzi IP di back-end con gli indirizzi IP dei server web back-end. Questi indirizzi IP sono gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP seguenti per aggiungere il proprio endpoint indirizzo IP di applicazione.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Passaggio 12

Caricare il certificato da utilizzare per le risorse del pool di back-end ssl abilitato.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Passaggio 13

Configurare le impostazioni di back-end http gateway dell'applicazione. Assegnare il certificato caricato nel passaggio precedente per le impostazioni di http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Passaggio 14

Configurare la porta IP front-end per l'endpoint IP pubblico. Questa porta è la porta in grado di connettersi agli utenti finali.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Passaggio 15

Creare una configurazione IP front-end, questa impostazione esegue il mapping di un indirizzo ip pubblico o privato il front-end del gateway dell'applicazione. La seguente operazione associa l'indirizzo IP pubblico nel passaggio precedente con la configurazione IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Passaggio 16

Configurare il certificato per il gateway di applicazione. Questo certificato viene utilizzato per decrittografare e crittografare nuovamente il traffico nel gateway dell'applicazione.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Passaggio 17

Creare comunicare ascoltatore HTTP per il gateway di applicazione. Assegnare il certificato di configurazione, porta e ssl ip front-end da utilizzare.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Passaggio 18

Creare una regola di routing bilanciamento carico che configura il comportamento di bilanciamento del carico. In questo esempio viene creata una regola di base circolari.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Passaggio 19

Configurare la dimensione istanza del gateway dell'applicazione.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  È possibile scegliere tra **WAF\_medio** e **WAF\_grande**, il livello quando si utilizza WAF è sempre **WAF**. Capacità è un numero compreso tra 1 e 10.

### <a name="step-20"></a>Passaggio 20

Configurare la modalità per WAF, i valori accettabili sono **prevenzione** e **rilevamento**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Passaggio 21

Creare un gateway di applicazioni con tutti gli elementi di configurazione dai passaggi precedenti. In questo esempio, il gateway di applicazione si chiama "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

Informazioni su come configurare la registrazione diagnostica per registrare gli eventi che vengono rilevati o impedire con Firewall di applicazione Web visitando [Applicazione Gateway diagnostica](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png