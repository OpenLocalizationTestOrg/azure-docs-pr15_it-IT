<properties
   pageTitle="Creare, avviare o eliminare un gateway di applicazioni | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni per creare, configurare, avviare ed eliminare un gateway di applicazioni Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="create-start-or-delete-an-application-gateway"></a>Creare, avviare o eliminare un gateway di applicazioni

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-gateway-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classica](application-gateway-create-gateway.md)
- [Modelli di Azure Manager delle risorse](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Gateway di applicazione Azure è un bilanciamento del carico di livello 7. Fornisce failover, routing prestazioni richieste HTTP tra server diversi, se sono nel cloud o locale. Gateway di applicazioni offre molte caratteristiche di applicazione recapito Controller (ADC) inclusi bilanciamento del carico HTTP, affinità sessione basata su cookie, trasferire Secure Sockets Layer (SSL), le ricerche dello stato personalizzato, il supporto per più siti e molti altri. Per trovare un elenco completo delle funzionalità supportate, vedere [Applicazione Gateway Panoramica](application-gateway-introduction.md)

In questo articolo sono illustrati i passaggi per creare, configurare, avviare ed eliminare un gateway di applicazioni.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare l'ultima versione dei cmdlet di PowerShell Azure tramite il programma di installazione di piattaforma Web. È possibile scaricare e installare l'ultima versione dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Se si dispone di una rete virtuale esistente, selezionare una subnet vuota esistente oppure creare una nuova subnet nella rete virtuale esistente esclusivo per il gateway di applicazione. Non è possibile distribuire il gateway di applicazione a una rete virtuale diverso da quello delle risorse che si desidera distribuire dietro il gateway di applicazione, a meno che non vnet peering viene utilizzato. Per ulteriori informazioni visitare [Peering Vnet](../virtual-network/virtual-network-peering-overview.md)
3. Verificare di disporre di una rete virtuale di lavoro con una subnet valida. Assicurarsi che nessuna macchine virtuali o distribuzioni cloud utilizza subnet. Il gateway di applicazione deve essere una subnet virtuali autonomamente.
3. I server configurati per utilizzare il gateway di applicazione deve essere presente o relativi endpoint creati con la rete virtuale o a un pubblico IP/VIP assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Che cos'è necessario creare un gateway di applicazione?

Quando si utilizza il comando **Nuovo AzureApplicationGateway** per creare il gateway di applicazione, non è configurato a questo punto e la risorsa appena creata sono configurate tramite XML o un oggetto di configurazione.

I valori sono:

- **Pool di server di back-end:** Elenco di indirizzi IP di server back-end. Indirizzi IP elencati devono appartenere alla subnet virtuali o devono essere un IP/VIP pubblico.
- **Le impostazioni del pool di server di back-end:** Ogni pool ha impostazioni come porta, il protocollo e affinità basate su cookie. Queste impostazioni sono collegate a un pool e vengono applicate a tutti i server del pool.
- **Porta front-end:** Questa porta è la porta pubblica che viene aperta nel gateway dell'applicazione. Il traffico raggiunge la porta e quindi venga reindirizzato a uno dei server back-end.
- **Comunicare ascoltatore:** Comunicare ascoltatore dispone di una porta front-end, un protocollo (Http o Https, questi valori sono maiuscole e minuscole) e il nome del certificato SSL (se la configurazione di SSL trasferire).
- **Regola:** La regola associa comunicare ascoltatore e al pool di server di back-end e consente di definire quali pool di server di back-end il traffico deve essere indirizzato al quando arriva una particolare comunicare ascoltatore.

## <a name="create-an-application-gateway"></a>Creare un gateway di applicazioni

Per creare un gateway di applicazione:

1. Creare una risorsa di gateway dell'applicazione.
2. Creare un file di configurazione XML o un oggetto di configurazione.
3. Eseguire il commit della configurazione per la risorsa gateway applicazione appena creato.

>[AZURE.NOTE] Se è necessario configurare un sondaggio personalizzato per il gateway di applicazione, vedere [creare un gateway di applicazioni con le ricerche personalizzate tramite PowerShell](application-gateway-create-probe-classic-ps.md). Consultare [le ricerche personalizzate e il monitoraggio dell'integrità](application-gateway-probe-overview.md) per altre informazioni.

![Scenario di esempio][scenario]

### <a name="create-an-application-gateway-resource"></a>Creare una risorsa di gateway dell'applicazione

Per creare il gateway, utilizzare il cmdlet **New-AzureApplicationGateway** , sostituendo i valori con il proprio. Fatturazione per il gateway non viene avviato a questo punto. Fatturazione inizia in un passaggio successivo, quando il gateway è stato avviato correttamente.

Nell'esempio seguente viene creato un gateway di applicazione tramite una rete virtuale denominata "testvnet1" e una subnet denominata "subnet-1".


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Descrizione*, *InstanceCount*e *GatewaySize* sono parametri facoltativi.

Per convalidare che il gateway sia stato creato, è possibile utilizzare il cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è medio. È possibile scegliere tra Small, Medium e Large.

*VirtualIPs* e *DnsName* vengono visualizzati come vuoto perché il gateway non è ancora stata avviata. Vengono creati dopo il gateway è in esecuzione.

## <a name="configure-the-application-gateway"></a>Configurare il gateway di applicazione

È possibile configurare il gateway di applicazione tramite XML o un oggetto di configurazione.

## <a name="configure-the-application-gateway-by-using-xml"></a>Configurare il gateway di applicazione tramite XML

Nell'esempio seguente, utilizzare un file XML per configurare le impostazioni di gateway di applicazione e li vincolano alla risorsa gateway dell'applicazione.  

### <a name="step-1"></a>Passaggio 1  

Copiare il testo seguente in blocco note.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Modificare i valori tra parentesi per gli elementi di configurazione. Salvare il file con estensione XML.

>[AZURE.IMPORTANT] L'elemento protocollo Http o Https è maiuscole e minuscole.

Nell'esempio seguente viene illustrato come utilizzare un file di configurazione per impostare il gateway di applicazione. Il carico di esempio distribuisce il traffico HTTP sulla porta pubblico 80 e invia il traffico di rete back-end porte 80 tra due indirizzi IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Passaggio 2

Successivamente, impostare il gateway di applicazione. Utilizzare il cmdlet **Set-AzureApplicationGatewayConfig** con un file XML di configurazione.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurare il gateway di applicazione tramite un oggetto di configurazione

Nell'esempio seguente viene illustrato come configurare il gateway di applicazione utilizzando gli oggetti di configurazione. Tutti gli elementi di configurazione devono essere configurati singolarmente e quindi aggiunto a un oggetto di configurazione del gateway dell'applicazione. Dopo aver creato l'oggetto di configurazione, il comando **Set AzureApplicationGateway** per eseguire il commit della configurazione per la risorsa gateway applicazione creata in precedenza.

>[AZURE.NOTE] Prima di assegnare un valore per ogni oggetto di configurazione, è necessario dichiarare il tipo di oggetto PowerShell utilizza per l'archiviazione. La prima riga per creare i singoli elementi consente di definire quali Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nome oggetto) vengono utilizzati.

### <a name="step-1"></a>Passaggio 1

Creare tutti gli elementi di configurazione singoli.

Creare IP front-end, come illustrato nell'esempio seguente.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Creare la porta front-end, come illustrato nell'esempio seguente.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Creare il pool di server di back-end.

 Definire gli indirizzi IP che vengono aggiunti al pool di server di back-end, come illustrato nell'esempio riportato di seguito.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 L'oggetto $server per sommare i valori per l'oggetto del pool di back-end ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Creare l'impostazione del pool di server di back-end.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Creare comunicare ascoltatore.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Creare la regola.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Passaggio 2

Assegnare tutti gli elementi di configurazione singoli a un oggetto di configurazione di gateway di applicazione ($appgwconfig).

Aggiungere la configurazione IP front-end.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Aggiungere la porta front-end per la configurazione.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Aggiungere la configurazione del pool di server di back-end.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Aggiungere l'impostazione del pool di back-end per la configurazione.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Aggiungere comunicare ascoltatore la configurazione.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Aggiungere la regola per la configurazione.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Passaggio 3

Eseguire il commit l'oggetto di configurazione per la risorsa gateway dell'applicazione tramite **Set AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Avviare il gateway

Dopo aver configurato il gateway, utilizzare il cmdlet **AzureApplicationGateway inizio** per avviare il gateway. Fatturazione per un gateway di applicazioni inizia dopo l'avvio di gateway.

> [AZURE.NOTE] Il cmdlet **Start AzureApplicationGateway** potrebbe richiedere fino a 20 15 minuti.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Verificare lo stato del gateway

Utilizzare il cmdlet **Get-AzureApplicationGateway** per controllare lo stato del gateway. Se **Inizio AzureApplicationGateway** ha esito positivo nel passaggio precedente, *stato* deve essere in esecuzione e *Vip* e *DnsName* devono avere voci valide.

Nell'esempio seguente mostra un gateway applicazioni che è verso l'alto, in esecuzione, e pronto per eseguire il traffico destinati `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Eliminare un gateway di applicazioni

Per eliminare un gateway di applicazione:

1. Utilizzare il cmdlet **AzureApplicationGateway Interrompi** per interrompere il gateway.
2. Utilizzare il cmdlet **AzureApplicationGateway Rimuovi** per rimuovere il gateway.
3. Verificare che il gateway è stata rimossa utilizzando il cmdlet **Get-AzureApplicationGateway** .

Nell'esempio seguente mostra il cmdlet **Stop AzureApplicationGateway** nella prima riga, seguita dall'output.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Dopo aver aggiunto il gateway di applicazione allo stato di interruzione, utilizzare il cmdlet **AzureApplicationGateway Rimuovi** per rimuovere il servizio.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Per verificare che il servizio è stato rimosso, è possibile utilizzare il cmdlet **Get-AzureApplicationGateway** . Questo passaggio non sono necessario.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare offload SSL, vedere [configurare un gateway di applicazioni per SSL trasferire](application-gateway-ssl.md).

Se si vuole configurare un gateway di applicazioni da utilizzare con un sistema di bilanciamento del carico interno, vedere [creare un gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per ulteriori informazioni sulle opzioni di bilanciamento di carico in generale, vedere:

- [Bilanciamento del carico Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestore del traffico Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png