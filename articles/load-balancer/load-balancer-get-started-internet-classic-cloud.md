<properties
   pageTitle="Iniziare a creare un Internet affiancate di bilanciamento del carico distribuzione classica modello con i servizi cloud | Microsoft Azure"
   description="Informazioni su come creare un sistema di bilanciamento del carico nel modello di distribuzione classico per i servizi cloud è connessa a Internet"
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
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Informazioni introduttive sulla creazione di un servizio di bilanciamento del carico per i servizi cloud è connessa a Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [imparare a creare un servizio di bilanciamento del carico Gestione risorse di Azure è connessa a Internet](load-balancer-get-started-internet-arm-cli.md).

Servizi cloud vengono configurati automaticamente con un bilanciamento del carico e possono essere personalizzati tramite il modello dei servizi.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Creare un utilizzando il file di definizione del servizio di bilanciamento del carico

È possibile sfruttare Azure SDK per .NET 2,5 aggiornare il servizio cloud. Impostazioni endpoint per i servizi cloud vengono eseguite nel file .csdef [definizione del servizio](https://msdn.microsoft.com/library/azure/gg557553.aspx).

Nell'esempio seguente viene illustrato come configurare un file di servicedefinition.csdef per una distribuzione cloud:

Controllo snipet per il file .csdef generato da una distribuzione cloud, è possibile vedere endpoint esterno configurato per utilizzare porte HTTP sulla porta 10000, 10001 e 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Controllare lo stato di integrità di bilanciamento carico per i servizi cloud


Di seguito è illustrato un esempio di una verifica dell'integrità:

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

Bilanciamento del carico combina le informazioni dell'endpoint e le informazioni di verifica per creare un URL in forma di http://{DIP di VM}:80/Probe.aspx che può essere utilizzato per lo stato del servizio di query.

Il servizio rileva le ricerche periodiche dallo stesso indirizzo IP. Verrà visualizzata la richiesta di verifica dell'integrità provenienti dall'host del nodo in cui viene eseguita la macchina virtuale.
Per rispondere con un codice di stato HTTP 200 per il bilanciamento del carico in modo che il servizio sia integro il servizio. Qualsiasi altro codice di stato HTTP (ad esempio 503) consente di accedere direttamente la macchina virtuale dalla rotazione.

La definizione di verifica controlla anche la frequenza della ricerca. In questo caso precedente, il servizio di bilanciamento del carico è sondaggio endpoint ogni 5 secondi. Se non si riceve nessuna risposta positiva per 10 secondi (due intervalli di verifica), si presuppone che la ricerca verso il basso e la macchina virtuale viene accettata dalla rotazione. Analogamente, se il servizio non è compresa nel rotazione e si riceve una risposta affermativa, il servizio è ripristinare rotazione immediatamente. Se il servizio è fluttuanti tra corretti e non corretti, bilanciamento del carico può decidere di ritardare l'introduzione ripetizione del servizio di rotazione di fino a quando non è stato integro per un numero di ricerche.

Selezionare lo schema di definizione del servizio per la [verifica dell'integrità](https://msdn.microsoft.com/library/azure/jj151530.aspx) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

