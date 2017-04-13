<properties
   pageTitle="Creare una ricerca personalizzata per Gateway applicazione tramite PowerShell nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come creare una ricerca personalizzata per il Gateway di applicazione tramite PowerShell nel modello di distribuzione classica"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Creare una ricerca personalizzata per Azure Application Gateway (classica) tramite PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-probe-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-probe-ps.md)
- [Azure PowerShell classica](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Creare un gateway di applicazioni

Per creare un gateway di applicazione:

1. Creare una risorsa di gateway dell'applicazione.
2. Creare un file di configurazione XML o un oggetto di configurazione.
3. Eseguire il commit della configurazione per la risorsa gateway applicazione appena creato.

### <a name="create-an-application-gateway-resource"></a>Creare una risorsa di gateway dell'applicazione

Per creare il gateway, utilizzare il cmdlet **New-AzureApplicationGateway** , sostituendo i valori con il proprio. Fatturazione per il gateway non viene avviato a questo punto. Fatturazione inizia in un passaggio successivo, quando il gateway è stato avviato correttamente.

Nell'esempio seguente viene creato un gateway di applicazione tramite una rete virtuale denominata "testvnet1" e una subnet denominata "subnet-1".

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Per convalidare che il gateway sia stato creato, è possibile utilizzare il cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è medio. È possibile scegliere tra piccole, medie e grandi dimensioni.

 *VirtualIPs* e *DnsName* vengono visualizzati come vuoto perché il gateway non è ancora stata avviata. Vengono creati dopo il gateway è in esecuzione.

## <a name="configure-an-application-gateway"></a>Configurare un gateway di applicazioni

È possibile configurare il gateway di applicazione tramite XML o un oggetto di configurazione.

## <a name="configure-an-application-gateway-by-using-xml"></a>Configurare un gateway di applicazione tramite XML

Nell'esempio seguente, utilizzare un file XML per configurare le impostazioni di gateway di applicazione e li vincolano alla risorsa gateway dell'applicazione.  

### <a name="step-1"></a>Passaggio 1

Copiare il testo seguente in blocco note.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Modificare i valori tra parentesi per gli elementi di configurazione. Salvare il file con estensione XML.

Nell'esempio seguente viene illustrato come utilizzare un file di configurazione per impostare il gateway di applicazione, il traffico HTTP sulla porta pubblico 80 bilanciamento del carico e inviare il traffico di rete back-end porte 80 tra due indirizzi IP tramite una ricerca personalizzata.

>[AZURE.IMPORTANT] L'elemento protocollo Http o Https è maiuscole e minuscole.

Un nuovo elemento di configurazione \<presenza\> aggiunti per configurare le ricerche personalizzate.

I parametri di configurazione sono:

- **Nome** - nome di riferimento per sondaggio personalizzato.
- **Protocollo** - protocollo utilizzato (i valori possibili sono HTTP o HTTPS).
- **Host** e **percorso** - percorso URL completo che viene richiamato dal gateway applicazione per determinare lo stato dell'istanza. Ad esempio, se si dispone di un sito Web http://contoso.com/, quindi verifica personalizzato configurabile per "http://contoso.com/path/custompath.htm" per i controlli di verifica disporre di una risposta HTTP corretta.
- **Intervallo** - configura i controlli di verifica dell'intervallo in secondi.
- **Timeout** - definisce il timeout di verifica per il controllo di risposta HTTP.
- **UnhealthyThreshold** - il numero di risposte HTTP non riuscite necessari per contrassegnare l'istanza di back-end come *non corretti*.

Il nome del sondaggio viene fatto riferimento nel <BackendHttpSettings> configurazione per assegnare il pool di back-end utilizza le impostazioni di ricerca personalizzata.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Aggiungere una configurazione di ricerca personalizzato per un gateway di applicazioni esistente

La modifica della configurazione del gateway applicazione richiede tre passaggi: ottenere il file di configurazione XML corrente, modificare per avere una verifica personalizzata e configurare il gateway di applicazione con le nuove impostazioni XML.

### <a name="step-1"></a>Passaggio 1

È possibile ottenere il file XML utilizzando get-AzureApplicationGatewayConfig. Consente di esportare la configurazione XML da modificare per aggiungere un'impostazione di verifica.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Passaggio 2

Aprire il file XML in un editor di testo. Aggiungere un `<probe>` sezione dopo `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Nella sezione backendHttpSettings del codice XML, aggiungere il nome di ricerca come illustrato nell'esempio seguente:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Salvare il file XML.

### <a name="step-3"></a>Passaggio 3

Aggiornare la configurazione di gateway dell'applicazione con il nuovo file XML utilizzando **Set AzureApplicationGatewayConfig**. Aggiorna il gateway di applicazione con la nuova configurazione.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare offload Secure Sockets Layer (SSL), vedere [configurare un gateway di applicazioni per SSL trasferire](application-gateway-ssl.md).

Se si vuole configurare un gateway di applicazioni da utilizzare con un sistema di bilanciamento del carico interno, vedere [creare un gateway di applicazioni con un sistema di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).
