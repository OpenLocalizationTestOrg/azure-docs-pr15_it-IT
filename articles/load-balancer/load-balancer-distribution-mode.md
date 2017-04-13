<properties
   pageTitle="Configurare la modalità di distribuzione di bilanciamento del carico | Microsoft Azure"
   description="Come configurare la modalità di distribuzione carico Azure bilanciamento per supportare affinità dell'indirizzo IP di origine"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurare la modalità di distribuzione per bilanciamento del carico

## <a name="hash-based-distribution-mode"></a>Modalità di distribuzione basato su hash

Algoritmo di distribuzione predefinito è una tupla 5 (IP, porta di origine di origine IP di destinazione, porta di destinazione, protocollo tipo) hash mappare il traffico ai server disponibili. Fornisce aderenza solo all'interno di una sessione di trasporto. Pacchetti nella stessa sessione saranno indirizzati alla stessa istanza di IP dispone Data Center dietro l'endpoint di bilanciamento del carico. Quando il client avvia una nuova sessione dalla stessa IP di origine, porta di origine viene modificata e provoca il traffico passare a un altro endpoint DIP.

![servizio di bilanciamento del carico hash in base a](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1-5-tupla distribuzione

## <a name="source-ip-affinity-mode"></a>Modalità di affinità IP di origine

Si dispone di un'altra modalità di distribuzione chiamata affinità dell'indirizzo IP di origine (noto anche come affinità sessione o affinità dell'indirizzo IP client). Azure di bilanciamento del carico possono essere configurato per utilizzare una tupla 2 (indirizzo IP di origine; destinazione IP) o una tupla di 3 (IP di origine, IP di destinazione, Protocol) per mappare il traffico al server disponibili. Utilizzando affinità dell'indirizzo IP di origine, le connessioni avviate dallo stesso computer client passa allo stesso endpoint DIP.

Il diagramma seguente illustra la configurazione di una tupla con 2. Si noti come tupla 2 viene eseguito il bilanciamento del carico per la macchina virtuale 1 (VM1) che quindi il backup VM2 e VM3.

![affinità sessione](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figura 2-2-tupla distribuzione

Affinità dell'indirizzo IP di origine è stato risolto un problemi di compatibilità tra il servizio di bilanciamento del carico Azure e Gateway Desktop remoto (RD). A questo punto è possibile creare una farm di gateway desktop remoto in un servizio cloud singola.

Scenario relativo al caso di usare un'altra è caricamento di file multimediali in cui il caricamento di dati avviene tramite UDP ma il piano di controllo viene ottenuto tramite TCP:

- Un client avvia una sessione TCP all'indirizzo pubblico bilanciamento del carico prima di tutto, ottiene indirizzati a un specifico DIP, il canale rimanga attivo per monitorare lo stato di connessione
- Una nuova sessione UDP dallo stesso computer client viene avviata allo stesso endpoint pubblica di bilanciamento del carico, il ci si aspetta che la connessione anche indirizzata allo stesso endpoint DIP durante la connessione TCP precedente in modo da caricare file multimediali può essere eseguita in alta velocità mantenendo anche un canale di controllo tramite TCP.

>[AZURE.NOTE] Quando viene modificato un set di bilanciamento del carico, rimozione o aggiunta di una macchina virtuale, la distribuzione di richieste client viene ricalcolata. Non può dipendere connessioni nuove da esistente client arrivare sullo stesso server. Inoltre, usando l'indirizzo IP di origine in modalità di distribuzione affinità può causare una distribuzione disuguale di traffico. Client che eseguono dietro i proxy possono essere visualizzati come in un'applicazione client univoco.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configurazione delle impostazioni affinità IP di origine per bilanciamento del carico

Per macchine virtuali, è possibile utilizzare PowerShell per modificare le impostazioni di timeout:

Aggiungere un endpoint Azure a una macchina virtuale e impostare la modalità di distribuzione carico bilanciamento

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

LoadBalancerDistribution possono essere impostate sourceIP per 2-tupla (indirizzo IP di origine; destinazione IP) bilanciamento del carico, sourceIPProtocol per il bilanciamento del carico di 3-tupla (origine, IP di destinazione, indirizzo IP) o nessuno se si desidera che il comportamento predefinito di bilanciamento del carico tupla 5.

Utilizzare le operazioni seguenti per recuperare una endpoint carica bilanciamento distribuzione modalità configurazione:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Se l'elemento LoadBalancerDistribution non è presente il bilanciamento del carico Azure utilizza l'algoritmo di 5 tupla predefinito.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Impostare la modalità di distribuzione su un set di endpoint di bilanciamento del carico

Se i punti finali fanno parte di un set di endpoint di bilanciamento del carico, è necessario impostare la modalità di distribuzione per il set di endpoint di bilanciamento del carico:

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configurazione del servizio cloud per modificare la modalità di distribuzione

Per aggiornare il servizio Cloud, è possibile sfruttare Azure SDK per .NET 2.5 (a essere rilasciati novembre). Impostazioni endpoint per i servizi Cloud vengono eseguite nel .csdef. Per aggiornare la modalità di distribuzione carico bilanciamento per una distribuzione di servizi Cloud, è necessario un aggiornamento di distribuzione.
Ecco un esempio di modifiche .csdef per accedere a Impostazioni endpoint:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="api-example"></a>Esempio di API

È possibile configurare la distribuzione bilanciamento del carico tramite l'API di gestione di servizio. Assicurarsi di aggiungere la `x-ms-version` intestazione è impostata su versione `2014-09-01` o versione successiva.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Aggiornare la configurazione del set bilanciamento del carico specificato in una distribuzione

#### <a name="request-example"></a>Richiesta di esempio

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Il valore di LoadBalancerDistribution può essere sourceIP per 2 tupla affinità, sourceIPProtocol per 3 tupla affinità o Nessuno (ad alcuna affinità. ad esempio 5-tupla)

#### <a name="response"></a>Risposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di bilanciamento carico interno](load-balancer-internal-overview.md)

[Per iniziare la configurazione di un servizio di bilanciamento del carico è connessa a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
