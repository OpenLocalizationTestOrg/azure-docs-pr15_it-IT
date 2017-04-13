<properties
   pageTitle="Creare un bilanciamento del carico interno per i servizi cloud nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come creare un bilanciamento del carico interno tramite PowerShell nel modello di distribuzione classica"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Iniziare la creazione di un servizio di bilanciamento del carico interno (classica) per i servizi cloud

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurazione di bilanciamento del carico interno per i servizi cloud

Servizio di bilanciamento del carico interno è supportata per macchine virtuali e servizi cloud. Un endpoint di bilanciamento carico interno creato in un servizio cloud di fuori rete virtuale internazionali sarà accessibile solo all'interno del servizio cloud.

La configurazione di bilanciamento del carico interno deve essere impostato durante la creazione della prima distribuzione nel servizio cloud, come illustrato nell'esempio riportato di seguito.

>[AZURE.IMPORTANT] Per eseguire la procedura seguente solo a una rete virtuale già creati per la distribuzione cloud. È necessario il nome di rete virtuale e il nome di subnet per creare il bilanciamento del carico interno.

### <a name="step-1"></a>Passaggio 1

Aprire il file di configurazione di servizio (. cscfg) per la distribuzione di cloud in Visual Studio e aggiungere la sezione seguente per creare l'interno il bilanciamento del carico sotto l'ultimo "`</Role>`" elemento per la configurazione di rete.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


È possibile aggiungere i valori per il file di configurazione della rete visualizzare l'aspetto. Nell'esempio, si supponga che è stato creato una subnet denominata "test_vnet" con un 10.0.0.0/24 subnet denominata test_subnet e un indirizzo IP statico 10.0.0.4. Bilanciamento del carico verrà denominato testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Per ulteriori informazioni sullo schema di bilanciamento del carico, vedere [aggiunta di bilanciamento del carico](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Passaggio 2


Modificare il file di definizione (.csdef) del servizio per aggiungere i punti finali al bilanciamento del carico interno. Il momento in cui che viene creata un'istanza di ruolo, file di definizione del servizio aggiungerà istanze del ruolo di bilanciamento del carico interno.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

In seguito gli stessi valori dell'esempio precedente, aggiungere i valori per il file di definizione del servizio.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

Il traffico di rete sarà il bilanciamento del carico utilizzando il bilanciamento del carico testLB utilizzando la porta 80 per le richieste in arrivo, inviare a istanze del ruolo di lavoro anche sulla porta 80.


## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione carico bilanciamento utilizzando affinità dell'indirizzo IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)