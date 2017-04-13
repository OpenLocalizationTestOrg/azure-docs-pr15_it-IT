<properties 
   pageTitle="Creare e configurare un Gateway di applicazioni con bilanciamento del carico interno (ILB) in una rete virtuale | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni per configurare un Gateway di applicazioni Azure con un endpoint di bilanciamento del carico interno"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Creare un Gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB)

> [AZURE.SELECTOR]
- [Procedura classica Azure](application-gateway-ilb.md)
- [Passaggi di Manager delle risorse Powershell](application-gateway-ilb-arm.md)

Gateway di applicazioni possono essere configurati con un indirizzo IP virtuale è connessa a internet o con un punto finale interno non viene esposto su internet, noto anche come endpoint interno carico bilanciamento (ILB). Configurazione del gateway con un ILB è utile per applicazioni line-of-business interne non viene esposte su internet. È utile anche per i servizi/livelli all'interno di un'applicazione di più livelli, che si trova in un limite di protezione non viene esposto su internet, ma richiedono comunque il distribuzione carico circolari, aderenza sessione o terminazione SSL. In questo articolo sono illustrati i passaggi per configurare un gateway di applicazioni con un ILB.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di PowerShell Azure tramite il programma di installazione di piattaforma Web. È possibile scaricare e installare l'ultima versione dalla sezione **Windows PowerShell** della [pagina di Download](https://azure.microsoft.com/downloads/).
2. Verificare di disporre di una rete virtuale di lavoro con subnet validi.
3. Verificare di avere server back-end nella rete virtuale o con un pubblico IP/VIP assegnato.


Per creare un gateway di applicazioni, effettuare le seguenti operazioni nell'ordine elencato. 

1. [Creare un gateway di applicazioni](#create-a-new-application-gateway)
2. [Configurare il gateway](#configure-the-gateway)
3. [Impostare la configurazione di gateway](#set-the-gateway-configuration)
4. [Avviare il gateway](#start-the-gateway)
4. [Verificare il gateway](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Creare un gateway di applicazione:

**Per creare il gateway**, usare il `New-AzureApplicationGateway` cmdlet, sostituire i valori con uno personalizzato. Si noti che la fatturazione per il gateway non non viene avviato. Fatturazione inizia in un passaggio successivo, quando il gateway è stato avviato correttamente.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Per convalidare** che il gateway sia stato creato, è possibile utilizzare il `Get-AzureApplicationGateway` cmdlet. 

Nell'esempio, *Descrizione*, *InstanceCount*e *GatewaySize* sono parametri facoltativi. Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è medio. Le piccole e grandi dimensioni sono altri valori disponibili. *VIP* e *DnsName* vengono visualizzati come vuoto perché il gateway non è ancora stata avviata. Vengono creati dopo il gateway è in esecuzione. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Configurare il gateway

Configurazione di gateway un'applicazione è costituita da più valori. I valori possono essere collegati tra loro per creare la configurazione.
 
I valori sono:

- **Pool di server di back-end:** Elenco di indirizzi IP del server di back-end. Indirizzi IP elencati devono appartenere alla subnet VNet o devono essere un IP/VIP pubblico. 
- **Le impostazioni del pool di server di back-end:** Ogni pool ha impostazioni come porta, il protocollo e affinità basate su cookie. Queste impostazioni sono collegate a un pool e vengono applicate a tutti i server del pool.
- **Frontend porta:** Questa porta è la porta pubblica aperta nel gateway dell'applicazione. Il traffico raggiunge la porta e quindi venga reindirizzato a uno dei server di back-end.
- **Comunicare ascoltatore:** Comunicare ascoltatore dispone di una porta front-end, un protocollo (Http o Https, si tratta maiuscole e minuscole) e il nome del certificato SSL (se la configurazione di SSL trasferire). 
- **Regola:** La regola associa comunicare ascoltatore e al pool di server di back-end e consente di definire quali pool di server di back-end il traffico deve essere indirizzato al quando arriva una particolare comunicare ascoltatore. Attualmente non è supportata solo la regola di *base* . La regola di *base* è distribuzione carico circolari.

È possibile creare la configurazione mediante la creazione di un oggetto di configurazione o tramite un file XML di configurazione. Per creare la configurazione utilizzando un file XML di configurazione, utilizzare l'esempio riportato di seguito.



Tenere presente quanto segue:


- L'elemento *FrontendIPConfigurations* descrive i dettagli ILB rilevanti per la configurazione di Gateway di applicazioni con un ILB. 

- Il *tipo* di Frontend IP deve essere impostata su 'Private'

- *StaticIPAddress* deve essere impostata all'indirizzo IP interno desiderato in cui il gateway riceve il traffico. Si noti che l'elemento *StaticIPAddress* è facoltativo. In caso contrario set, un indirizzo IP interno disponibile da subnet distribuito scelto. 

- Il valore dell'elemento *nome* specificato in *FrontendIPConfiguration* da utilizzare nell'elemento *FrontendIP* del HTTPListener per fare riferimento alla FrontendIPConfiguration.

 **Esempio di configurazione XML**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Impostare la configurazione di gateway

Successivamente, impostare il gateway di applicazione. È possibile utilizzare il `Set-AzureApplicationGatewayConfig` cmdlet con un oggetto di configurazione o con un file XML di configurazione. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Avviare il gateway

Dopo aver configurato il gateway, utilizzare la `Start-AzureApplicationGateway` cmdlet per avviare il gateway. Fatturazione per un gateway di applicazioni inizia dopo l'avvio di gateway. 


> [AZURE.NOTE] Il `Start-AzureApplicationGateway` cmdlet potrebbe richiedere fino a 15-20 minuti. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Verificare lo stato del gateway

Utilizzare la `Get-AzureApplicationGateway` cmdlet per controllare lo stato del gateway. Se *Inizio AzureApplicationGateway* ha avuto esito positivo nel passaggio precedente, lo stato deve essere *in esecuzione*e l'indirizzo Vip e DnsName devono avere voci valide. In questo esempio mostra il cmdlet nella prima riga, seguita dall'output. In questo esempio, il gateway è in esecuzione ed è pronto per scrivere il traffico. 

> [AZURE.NOTE] Il gateway di applicazione è configurato per il traffico all'endpoint ILB configurato 10.0.0.10 in questo esempio.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Passaggi successivi


Per ulteriori informazioni sulle opzioni di bilanciamento di carico in generale, vedere:

- [Bilanciamento del carico Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestore del traffico Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
