<properties
   pageTitle="Iniziare a creare un Internet affiancate di bilanciamento del carico nel modello di distribuzione classica usa CLI Azure | Microsoft Azure"
   description="Informazioni su come creare un sistema di bilanciamento del carico nel modello di distribuzione classica usa CLI Azure è connessa a Internet"
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

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Iniziare a creare un Internet affiancate di bilanciamento del carico (classico) in CLI Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [imparare a creare un servizio di bilanciamento del carico Gestione risorse di Azure è connessa a Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Step by step la creazione di un servizio di bilanciamento del carico usa CLI è connessa a Internet

Questa guida viene illustrato come creare un Internet di bilanciamento del carico in base allo scenario precedente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.

2. Eseguire il comando **configurazione azure modalità** per passare alla modalità classica, come illustrato di seguito.

        azure config mode asm

    Output previsto:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Creare endpoint e caricare i set di bilanciamento del carico

Lo scenario presuppone macchine virtuali "web1" e "web2" sono stati creati.
Questa Guida verrà creato un set di bilanciamento carico utilizzando le porte 80 come porta pubblica e porte 80 come porta locale. Una porta verifica anche è configurata per porte 80 e denominata carico bilanciamento set "lbset".


### <a name="step-1"></a>Passaggio 1

Creare il primo endpoint e bilanciamento del carico set mediante `azure network vm endpoint create` per macchina virtuale "web1".

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Parametri utilizzati:

**-k** - porta locale macchina virtuale<br>
**-o** - protocollo<BR>
**-t** - verifica porta<BR>
**-b** - nome di bilanciamento del carico<BR>

## <a name="step-2"></a>Passaggio 2

Aggiungere una seconda macchina virtuale "web2" per il set di bilanciamento del carico.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Passaggio 3

Verificare la configurazione di bilanciamento carico utilizzando `azure vm show` .

    azure vm show web1

L'output sarà:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Creare un endpoint desktop remoto per una macchina virtuale

È possibile creare un endpoint desktop remoto per inoltrare il traffico di rete da una porta pubblica a una porta locale per l'uso di macchina virtuale specifica `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Rimuovere virtual machine dal servizio di bilanciamento del carico

È necessario eliminare il punto finale associato il bilanciamento del carico impostare dal computer virtuale. Dopo aver rimosso l'endpoint, la macchina virtuale non appartiene per il set più di bilanciamento del carico.

 Nel caso dell'esempio precedente, è possibile rimuovere l'endpoint creato per la macchina virtuale "web1" da "lbset" con il comando di bilanciamento del carico `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] È possibile esplorare altre opzioni per gestire i punti finali utilizzando il comando`azure vm endpoint --help`


## <a name="next-steps"></a>Passaggi successivi

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

