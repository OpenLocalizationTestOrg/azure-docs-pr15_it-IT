<properties
    pageTitle="Configurare il criterio SSL e to end SSL con Gateway applicazione | Microsoft Azure"
    description="In questo articolo viene descritto come configurare SSL to end con Gateway applicazione tramite Manager delle risorse PowerShell Azure"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurare il criterio SSL e to end SSL con Gateway applicazione tramite PowerShell

## <a name="overview"></a>Panoramica

Gateway di applicazione supporta la crittografia to end del traffico. A tale scopo, Gateway applicazione chiusura della connessione SSL gateway dell'applicazione. Il gateway quindi applica le regole di routing per il traffico nuovamente la crittografia del pacchetto e inoltra il pacchetto back-end appropriato in base alle regole di routing definite. Qualsiasi risposta dal server web attraversa lo stesso processo di tornare all'utente finale.

Un altro portare in primo piano il gateway di applicazione supporta disabilitazione alcune versioni di protocollo SSL. Gateway di applicazione supporta la disattivazione la versione del protocollo seguenti; TLSv1.0, TLSv1.1 e TLSv1.2.

> [AZURE.NOTE] SSL 2.0 e SSL 3.0 sono disabilitate per impostazione predefinita e non può essere attivata. Non sono considerate non protette e non sono possano essere utilizzate con Gateway di applicazioni

![immagine di uno scenario][scenario]

## <a name="scenario"></a>Scenario:

In questo scenario si imparerà a creare un gateway di applicazione tramite SSL to end tramite PowerShell.

Questo scenario consente di:

- Creare un gruppo di risorse denominato "appgw-rg"
- Creare una rete virtuale denominata "appgwvnet" con un blocco CIDR riservato di 10.0.0.0/16.
- Creare due subnet denominata "appgwsubnet" e "appsubnet".
- Creare un gateway piccola applicazione che supporta la crittografia SSL to end che disabilita determinati protocolli SSL.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare SSL to end con un gateway di applicazioni, è necessario per il gateway un certificato e i certificati sono necessari per il server di back-end. Il certificato di gateway viene utilizzato per crittografare e decrittografare il traffico inviato per l'utilizzo di SSL. Il certificato di gateway deve essere nel formato di scambio di informazioni personali (pfx). Consente il formato di file per la chiave privata da esportare richiesto per eseguire la crittografia e decrittografia del traffico dal gateway di applicazione.

Per la crittografia ssl to end back-end deve essere whitelisted con gateway di applicazioni. In tal caso, caricare il pubblico certificato di back-end al gateway dell'applicazione. In questo modo che il gateway di applicazione comunica solo con le istanze di back-end noti. Ciò protegge ulteriormente la comunicazione to end.

Questo processo viene descritto nei passaggi seguenti:

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

In questa sezione illustra la creazione di un gruppo di risorse, che contiene il gateway di applicazione.

### <a name="step-1"></a>Passaggio 1

Accedere al proprio Account Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Passaggio 2

Selezionare l'abbonamento da utilizzare per questo scenario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Passaggio 3

Creare un gruppo di risorse (salta questo passaggio se si usa un gruppo di risorse esistente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway di applicazione

Nell'esempio seguente viene creata una rete virtuale con due subnet. Una subnet viene utilizzata per tenere il gateway di applicazione. Altre subnet viene usata per back-end ospita l'applicazione web.

### <a name="step-1"></a>Passaggio 1

Assegnare un intervallo di indirizzi per la subnet da utilizzare per il Gateway di applicazione stesso.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Subnet configurata per il gateway di applicazione devono essere ridimensionate correttamente. Un gateway di applicazioni può essere configurato per fino a 10 istanze. Ogni istanza accetta 1 indirizzo IP dalla subnet. Troppo piccolo di una subnet può influire negativamente sulla scalabilità un gateway di applicazioni.

### <a name="step-2"></a>Passaggio 2

Assegnare un intervallo di indirizzi da utilizzare per il pool di indirizzi back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Passaggio 3

Creare una rete virtuale con subnet definita in precedenza.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Passaggio 4

Recuperare le risorse virtuali e le risorse di subnet da utilizzare nei passaggi seguenti:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica da utilizzare per il gateway di applicazione. L'indirizzo IP pubblico viene utilizzato un passaggio successivo.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Applicazione Gateway non supporta l'uso di un indirizzo IP pubblico creato con un'etichetta di dominio definita. È supportato solo un indirizzo IP pubblico con un'etichetta di dominio in modo dinamico creato. Se si richiede un nome descrittivo dns per il gateway di applicazione, è consigliabile utilizzare un record cname come alias.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione del gateway dell'applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway di applicazione. La procedura seguente crea gli elementi di configurazione necessari per una risorsa di gateway dell'applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP gateway un'applicazione, questa impostazione configura quali subnet viene utilizzato il gateway di applicazione. All'avvio di gateway di applicazioni, raccoglie dal subnet configurato un indirizzo IP e indirizza il traffico di rete per gli indirizzi IP nel pool di indirizzi IP di back-end. Tenere presente che ogni istanza accetta un indirizzo IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Passaggio 2

Creare una configurazione IP front-end, questa impostazione esegue il mapping di un indirizzo ip pubblico o privato il front-end del gateway dell'applicazione. La seguente operazione associa l'indirizzo IP pubblico nel passaggio precedente con la configurazione IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Passaggio 3

Configurare il pool di indirizzi IP di back-end con gli indirizzi IP dei server web back-end. Questi indirizzi IP sono gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP seguenti per aggiungere il proprio endpoint indirizzo IP di applicazione.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Un nome di dominio completo (FQDN) è un valore valido al posto di un indirizzo ip per il server di back-end tramite il parametro - BackendFqdns.

### <a name="step-4"></a>Passaggio 4

Configurare la porta IP front-end per l'endpoint IP pubblico. Questa porta è la porta in grado di connettersi agli utenti finali.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Passaggio 5

Configurare il certificato per il gateway di applicazione. Questo certificato viene utilizzato per decrittografare e crittografare nuovamente il traffico nel gateway dell'applicazione.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] In questo esempio consente di configurare il certificato usato per connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compreso tra i caratteri da 4 a 12.

### <a name="step-6"></a>Passaggio 6

Creare comunicare ascoltatore HTTP per il gateway di applicazione. Assegnare il certificato di configurazione, porta e ssl ip front-end da utilizzare.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Passaggio 7

Caricare il certificato da utilizzare per le risorse del pool di back-end ssl abilitato.

> [AZURE.NOTE] La ricerca predefinito Ottiene la chiave pubblica dalla associazione SSL **predefinito** sull'indirizzo IP del back-end e confronta il valore di chiave pubblica che riceve il valore di chiave pubblica che qui. La chiave pubblica recuperata potrebbe non essere necessariamente al sito desiderato in cui il traffico scorrerà **se** si usano le intestazioni host e SNI back-end. In caso di dubbi, visitare https://127.0.0.1/ su back-end per confermare il certificato utilizzato per il binding SSL **predefinito** . Utilizzare la chiave pubblica dalla richiesta di questa sezione. Se si usano le intestazioni host e SNI associazioni HTTPS e non si riceve una risposta e certificato da una richiesta manuale del browser per https://127.0.0.1/ nel back-end, è necessario impostare un'associazione SSL predefinito back-end. Se non, back-end non da saranno whitelisted ricerche avrà esito negativo.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] Il certificato fornito in questo passaggio deve essere la chiave pubblica del certificato pfx presente back-end. Esportare il certificato (non il certificato radice) installato sul server back-end. CER formattare e usarla in questo passaggio. In questo passaggio whitelists back-end con il gateway di applicazione.

### <a name="step-8"></a>Passaggio 8

Configurare le impostazioni di back-end http gateway dell'applicazione. Assegnare il certificato caricato nel passaggio precedente per le impostazioni di http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Passaggio 9

Creare una regola di routing bilanciamento carico che configura il comportamento di bilanciamento del carico. In questo esempio viene creata una regola di base circolari.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Passaggio 10

Configurare la dimensione istanza del gateway dell'applicazione.  Le dimensioni disponibili sono **Standard\_Small**, **Standard\_medio**, e **Standard\_grande**.  Per la capacità, i valori disponibili sono 1 e 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Scopo di verifica, è possibile scegliere un conteggio delle istanze di 1. È importante sapere che qualsiasi conteggio delle istanze in due istanze non è coperto dal contratto di servizio e sono pertanto non è consigliabile. Gateway Small devono essere utilizzate per dev test e non per scopi di produzione.

### <a name="step-11"></a>Passaggio 11

Configurare i criteri SSL da utilizzare nel Gateway dell'applicazione. Gateway di applicazione supporta la possibilità di disabilitare alcune versioni di protocollo SSL.

I valori seguenti sono un elenco delle versioni del protocollo che può essere disattivata.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

Nell'esempio seguente disattiva TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Creare il Gateway di applicazione

Tutti i passaggi precedenti consente di creare il Gateway di applicazione. La creazione del gateway è un processo lungo in esecuzione.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Disattivare SSL protocollo versioni in un Gateway di applicazione esistente

I passaggi precedenti richiedere la creazione di un'applicazione con ssl to end e la disattivazione di alcune versioni di protocollo SSL. Nell'esempio seguente disattiva determinati criteri SSL su un gateway di applicazioni esistente.

### <a name="step-1"></a>Passaggio 1

Recuperare il gateway di applicazione di aggiornamento.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Passaggio 2

Definire un criterio SSL. Nell'esempio seguente, TLSv1.0 e TLSv1.1 sono disabilitati.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Passaggio 3

Infine, aggiornare il gateway. È importante tenere presente che quest'ultimo passaggio è un'attività lunga. Al termine, to end ssl configurato sul gateway dell'applicazione.

    $gw | Set-AzureRmApplicationGateway

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome dell'applicazione gateway DNS

Dopo aver creato il gateway, il passaggio successivo consiste nel configurare front-end per la comunicazione. Quando si usa un indirizzo IP pubblico, applicazione gateway richiede un nome DNS assegnato dinamicamente, ovvero non è descrittivo. Per garantire agli utenti finali possibile raggiungere il gateway di applicazione un record CNAME può essere utilizzato in modo che puntino all'endpoint pubblico del gateway dell'applicazione. [Configurazione di un nome di dominio personalizzato per in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). A tale scopo, recuperare i dettagli del gateway dell'applicazione e il relativo nome IP/DNS associato utilizzando l'elemento PublicIPAddress collegato al gateway dell'applicazione. Nome DNS del gateway dell'applicazione deve essere utilizzato per creare un record CNAME che punta le applicazioni due web per questo nome DNS. L'uso di un record non è consigliabile poiché l'indirizzo VIP cambino al riavvio del gateway dell'applicazione.
    
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

Informazioni sulle rafforzare la protezione delle applicazioni web con Firewall di applicazione Web tramite applicazione Gateway visitando il sito [Web Application Firewall Overview](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
