<properties
   pageTitle="Creare una rete Internet affiancate di bilanciamento del carico in Gestione risorse utilizzando CLI Azure | Microsoft Azure"
   description="Informazioni su come creare un sistema di bilanciamento del carico in Gestione risorse utilizzando CLI Azure è connessa a Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Creazione di un bilanciamento del carico interno utilizzando CLI Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È inoltre possibile [imparare a creare un Internet affiancate di bilanciamento del carico mediante la distribuzione classica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Distribuire la soluzione utilizzando CLI Azure

La procedura seguente viene illustrato come creare un servizio di bilanciamento del carico Gestione risorse Azure con CLI è connessa a Internet. Gestione risorse Azure ogni risorsa viene creato e configurato singolarmente, quindi inserire insieme per creare una risorsa.

È necessario creare e configurare i seguenti oggetti per distribuire un bilanciamento del carico:

- Configurazione IP front-end - contiene indirizzi IP per il traffico di rete in ingresso.
- Pool di indirizzi di back-end - contiene le interfacce di rete (NIC) per le macchine virtuali di ricevere il traffico di rete di bilanciamento del carico.
- Regole di bilanciamento del carico - contiene le regole di mapping di una porta pubblica del servizio di bilanciamento del carico alla porta nel pool di indirizzi di back-end.
- In ingresso regole NAT - contiene le regole di mapping di una porta pubblica del servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi di back-end.
- Esegue la ricerca - contiene le ricerche integrità utilizzate per verificare la disponibilità delle istanze di macchine virtuali nel pool di indirizzi di back-end.

Per ulteriori informazioni, vedere [Gestione di Azure risorse di supporto per bilanciamento del carico](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Impostare CLI per utilizzare Gestione risorse

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.

2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Output previsto:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Creare una rete virtuale e un indirizzo IP pubblico per il pool front-end IP

1. Creare una rete virtuale (VNet) denominata *NRPVnet* nella posizione degli Stati Uniti orientali con un gruppo di risorse denominato *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Creare una subnet denominata *NRPVnetSubnet* con un blocco CIDR di 10.0.0.0/24 in *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Creare un indirizzo IP pubblico denominato *NRPPublicIP* che verrà utilizzato da un pool front-end IP con nome DNS *loadbalancernrp.eastus.cloudapp.azure.com*. Il comando seguente viene utilizzato il tipo di allocazione statica e il tempo di inattività di 4 minuti.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]Bilanciamento del carico utilizzerà l'etichetta di dominio dell'indirizzo IP pubblico come il nome di dominio completo. Una modifica da classica distribuzione, che utilizza cloud assistenza come il bilanciamento del carico dominio nome (completo).
    >In questo esempio il FQDN è *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Creare un bilanciamento del carico

Il comando seguente crea un bilanciamento del carico denominata *NRPlb* nel gruppo di risorse *NRPRG* negli *Stati Uniti orientali* Azure posizione.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Creare un pool di indirizzi IP front-end e un pool di indirizzi back-end

In questo esempio viene illustrato come creare il pool di indirizzi IP front-end che riceve il traffico di rete in ingresso nel sistema di bilanciamento del carico e al pool di indirizzi IP back-end nel punto in cui il pool front-end invia il traffico di rete di bilanciamento del carico.

1. Creare un pool front-end IP associazione l'indirizzo IP pubblico creato nel passaggio precedente e il servizio di bilanciamento del carico.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configurare un pool di indirizzi di back-end utilizzato per ricevere il traffico in ingresso dal pool di IP front-end.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Creare regole kg, regole NAT e verifica

In questo esempio viene creato gli elementi seguenti.

- una regola NAT per tradurre tutto il traffico in ingresso sulla porta 21 alla porta 22<sup>1</sup>
- una regola NAT per tradurre tutto il traffico in ingresso sulla porta 23 alla porta 22
- una regola di bilanciamento carico riconciliare tutto il traffico in ingresso sulla porta 80 alla porta 80 sugli indirizzi nel pool di back-end.
- una regola di verifica per controllare lo stato di integrità di una pagina denominata *HealthProbe.aspx*.

<sup>1</sup> le regole NAT associati a un'istanza di macchina virtuale specifica supporto bilanciamento del carico. Il traffico di rete in arrivo sulla porta 21 viene inviato a una specifica macchina virtuale sulla porta 22 associata a questa regola NAT. È necessario specificare un protocollo (UDP o TCP) per una regola NAT. Entrambi i protocolli non assegnare la stessa porta.

1. Creare le regole NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Creare una regola di bilanciamento del carico.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Creare una verifica dell'integrità.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Verificare le impostazioni.

        azure network lb show nrprg nrplb

    Output previsto:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Creare schede di rete

È necessario creare NIC (o modificare quelli esistenti), associarli regole NAT, le regole di bilanciamento carico e le ricerche.

1. Creare una scheda di rete denominato *essere kg-nic1*e associarla alla regola NAT *rdp1* e il pool di indirizzi di back-end *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Output previsto:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Creare una scheda di rete denominato *essere kg-nic2*e associarla alla regola NAT *rdp2* e il pool di indirizzi di back-end *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Creare una macchine () denominata *web1*e associarlo NIC denominato *essere kg-nic1*. È stato creato un account di archiviazione denominato *web1nrp* prima di eseguire il comando seguente.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Macchine virtuali in un servizio di bilanciamento del carico devono essere nello stesso set di disponibilità. Usare `azure availset create` per creare una disponibilità set.

    L'output dovrebbe essere simile al seguente:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] Messaggio che **si tratta di una scheda di rete senza publicIP configurato** è previsto dopo NIC creati per la connessione a Internet utilizzando l'indirizzo IP pubblico bilanciamento di carico di bilanciamento del carico.

    Poiché il *kg-nic1 essere* NIC è associata alla regola NAT *rdp1* , è possibile connettersi a *web1* utilizzando RDP tramite porta 3441 di bilanciamento del carico.

4. Creare una macchine () denominata *web2*e associarlo NIC denominato *essere kg-nic2*. È stato creato un account di archiviazione denominato *web1nrp* prima di eseguire il comando seguente.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Aggiornare un servizio di bilanciamento del carico esistente

È possibile aggiungere regole di fare riferimento a un servizio di bilanciamento del carico esistente. Nell'esempio successivo, una nuova regola di bilanciamento carico viene aggiunto a un bilanciamento del carico esistente **NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Eliminare un bilanciamento del carico

Utilizzare il comando seguente per rimuovere un bilanciamento del carico:

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Passaggi successivi

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
