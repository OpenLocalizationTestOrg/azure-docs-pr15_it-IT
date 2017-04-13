<properties
   pageTitle="Creare un bilanciamento del carico interno utilizzando CLI Azure nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come creare un bilanciamento del carico interno utilizzando CLI Azure nel modello di distribuzione classica"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Iniziare a creare un interno di bilanciamento del carico (classico) usa CLI Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Per creare un sistema di bilanciamento del carico interno impostare per macchine virtuali

Per creare un set di bilanciamento del carico interno e i server che verranno inviare loro il traffico, è necessario eseguire le operazioni seguenti:

1. Creare un'istanza di interno il bilanciamento del carico da endpoint del traffico in ingresso per eseguire il bilanciamento tra i server di un set di bilanciamento del carico.

1. Aggiungere i punti finali corrispondenti alle macchine virtuali che riceverà il traffico in entrata.

1. Configurare i server che verranno inviato il traffico del carico per inviare il traffico all'indirizzo IP (VIP) virtuale dell'istanza interno il bilanciamento del carico.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Step by step la creazione di un bilanciamento del carico interno utilizzando CLI

Questa guida viene illustrato come creare un bilanciamento del carico interno in base allo scenario precedente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.

2. Eseguire il comando **configurazione azure modalità** per passare alla modalità classica, come illustrato di seguito.

        azure config mode asm

    Output previsto:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Creare endpoint e caricare i set di bilanciamento del carico

Lo scenario presuppone macchine virtuali "DB1" e "DB2" in un servizio cloud denominato "mytestcloud". Entrambe le macchine virtuali usano una rete virtuale denominata personale "testvnet" con subnet "subnet-1".

Questa Guida verrà creato un set di bilanciamento carico interno tramite la porta 1433 come porta privata e 1433 come porta locale.

Si tratta di uno scenario comune nel punto in cui si dispone di macchine virtuali SQL back-end tramite un servizio di bilanciamento del carico interno per garantire che il server di database non esposti direttamente tramite un indirizzo IP pubblico.


### <a name="step-1"></a>Passaggio 1

Creare un set mediante di bilanciamento del carico interno `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parametri utilizzati:

**-r** - nome servizio cloud<BR>
**-n** - nome bilanciamento del carico interno<BR>
**-t** - nome subnet (stessa subnet dalle macchine virtuali che verranno aggiunti al servizio di bilanciamento del carico interno)<BR>
**-a** - (facoltativo) per aggiungere un indirizzo IP statico privato<BR>

Estrarre `azure service internal-load-balancer --help` per ulteriori informazioni.

È possibile controllare le proprietà di bilanciamento carico interno utilizzando il comando `azure service internal-load-balancer list` *nome servizio cloud*.

Qui di seguito un esempio di output:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Passaggio 2

Configurare il set di bilanciamento del carico interno quando si aggiunge il primo punto finale. Associare l'endpoint, macchina virtuale e la porta di verifica per il set di bilanciamento del carico interno in questo passaggio.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parametri utilizzati:

**-k** - porta locale macchina virtuale<BR>
**-t** - verifica porta<BR>
**-r** - protocollo di verifica<BR>
**-e** - intervallo di ricerca in secondi<BR>
**-f** - intervallo di timeout in secondi <BR>
**-i** - nome bilanciamento del carico interno <BR>


## <a name="step-3"></a>Passaggio 3

Verificare la configurazione di bilanciamento carico utilizzando `azure vm show` *nome macchina virtuale*

    azure vm show DB1

L'output sarà:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Creare un endpoint desktop remoto per una macchina virtuale

È possibile creare un endpoint desktop remoto per inoltrare il traffico di rete da una porta pubblica a una porta locale per l'uso di macchina virtuale specifica `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Rimuovere virtual machine dal servizio di bilanciamento del carico

È possibile rimuovere una macchina virtuale da un bilanciamento del carico interno impostata eliminando il punto finale associato. Dopo aver rimosso l'endpoint, la macchina virtuale non appartengono al set più di bilanciamento del carico.

 Nel caso dell'esempio precedente, è possibile rimuovere l'endpoint creato per macchina virtuale "DB1" da "ilbset" di bilanciamento del carico interno utilizzando il comando `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Estrarre `azure vm endpoint --help` per ulteriori informazioni.


## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione carico bilanciamento utilizzando affinità dell'indirizzo IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)