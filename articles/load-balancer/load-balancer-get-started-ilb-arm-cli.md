<properties
   pageTitle="Creare un sistema di bilanciamento del carico interno utilizzando CLI Azure in Gestione risorse | Microsoft Azure"
   description="Informazioni su come creare un sistema di bilanciamento del carico interno utilizzando CLI Azure in Gestione risorse"
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Creare un sistema di bilanciamento del carico interno utilizzando CLI Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Distribuire la soluzione utilizzando CLI Azure

La procedura seguente viene illustrato come creare un servizio di bilanciamento del carico esposto a Internet tramite Gestione risorse di Azure con CLI. Azure Gestione risorse di ogni risorsa viene creato e configurato singolarmente e quindi inserire insieme per creare una risorsa.

È necessario creare e configurare i seguenti oggetti per distribuire un bilanciamento del carico:

- **Configurazione IP front-end**: contiene indirizzi IP per il traffico di rete in ingresso
- **Pool di indirizzi back-end**: contiene le interfacce di rete (NIC) che consentono di macchine virtuali di ricevere il traffico di rete di bilanciamento del carico
- **Regole di bilanciamento del carico**: contiene le regole di cui eseguire il mapping di una porta pubblica del servizio di bilanciamento del carico alla porta nel pool di indirizzi di back-end
- **Le regole in entrata NAT**: contiene le regole di cui eseguire il mapping di una porta pubblica del servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi di back-end
- **Esegue la ricerca**: contiene le ricerche integrità utilizzati per verificare la disponibilità delle istanze di macchine virtuali nel pool di indirizzi di back-end

Per ulteriori informazioni, vedere [Gestione di Azure risorse di supporto per bilanciamento del carico](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Impostare CLI per utilizzare Gestione risorse

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../../articles/xplat-cli-install.md). Seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.

2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come indicato di seguito:

        azure config mode arm

    Output previsto:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Creare un sistema di bilanciamento del carico interno passo dopo passo

1. Accedere a Azure.

        azure login

    Quando richiesto, immettere le credenziali di Azure.

2. Modificare gli strumenti di comando in modalità di gestione di risorse Azure.

        azure config mode arm

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Tutte le risorse in Gestione risorse di Azure sono associate a un gruppo di risorse. Se non è fatto ancora, creare un gruppo di risorse.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Creare un set di bilanciamento del carico interno

1. Creare un sistema di bilanciamento del carico interno

    Nello scenario seguente viene creato un gruppo di risorse denominato nrprg nell'area orientale degli Stati Uniti.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Tutte le risorse per un servizi di bilanciamento del carico interno, ad esempio reti virtuali e subnet virtuali, devono essere dello stesso gruppo di risorse e nella stessa regione.

2. Creare un indirizzo IP front-end per il servizio di bilanciamento del carico interno.

    L'indirizzo IP in uso deve essere all'interno dell'intervallo di subnet della rete virtuale.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Creare il pool di indirizzi di back-end.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Dopo avere definito un indirizzo IP front-end e un pool di indirizzi di back-end, è possibile creare regole di bilanciamento carico, gestione regole in entrata NAT e analizza integrità personalizzati.

4. Creare una regola di bilanciamento del carico per il servizio di bilanciamento del carico interno.

    Quando si esegue la procedura precedente, il comando crea una regola di bilanciamento del carico per ascoltare la porta 1433 nel pool front-end e inviare il traffico di rete di bilanciamento del carico al pool di indirizzi di back-end, anche tramite la porta 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Creare le regole in entrata NAT.

    Le regole in entrata NAT vengono utilizzate per creare i punti finali in un bilanciamento del carico che passare a un'istanza di macchina virtuale specifica. I passaggi precedenti creare due regole NAT per desktop remoto.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Creare le ricerche di integrità per il servizio di bilanciamento del carico.

    Una verifica dell'integrità della verifica tutte le istanze di macchina virtuale per assicurarsi che possono inviare il traffico di rete. L'istanza di macchina virtuale con i controlli di verifica viene rimosso dalla bilanciamento del carico finché non si estenda torna online e una verifica del sondaggio determina stato ottimale.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]La piattaforma Microsoft Azure utilizza un indirizzo IPv4 statico, pubblicamente routing per un'ampia varietà di scenari amministrativi. L'indirizzo IP è 168.63.129.16. Questo indirizzo IP non deve essere bloccato da un firewall, poiché potrebbe verificarsi un comportamento imprevisto.
    >Per quanto riguarda bilanciamento del carico interno Azure, l'indirizzo IP viene utilizzato il monitoraggio delle ricerche di bilanciamento del carico per determinare lo stato di integrità per macchine virtuali in un set di bilanciamento del carico. Se un gruppo di sicurezza di rete viene utilizzato per limitare il traffico in macchine virtuali Azure in un set di bilanciamento del carico internamente o viene applicato a una subnet virtuali, verificare che si aggiunge una regola di sicurezza di rete per consentire il traffico da 168.63.129.16.

## <a name="create-nics"></a>Creare schede di rete

È necessario creare NIC (o modificare quelli esistenti), associarli regole NAT, le regole di bilanciamento carico e le ricerche.

1. Creare una scheda di rete denominato *essere kg-nic1*e quindi associarla alla regola NAT *rdp1* e il pool di indirizzi di back-end *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Creare una scheda di rete denominato *essere kg-nic2*e quindi associarla alla regola NAT *rdp2* e il pool di indirizzi di back-end *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Creare una macchina virtuale denominata *DB1*e quindi associarlo NIC denominato *essere kg-nic1*. Viene creato un account di archiviazione chiamato *web1nrp* prima che venga eseguito il comando seguente:

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Macchine virtuali in un servizio di bilanciamento del carico devono essere nello stesso set di disponibilità. Usare `azure availset create` per creare una disponibilità set.

4. Creare una macchine () denominata *DB2*e quindi associarlo NIC denominato *essere kg-nic2*. È stato creato un account di archiviazione denominato *web1nrp* prima di eseguire il comando seguente.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Eliminare un bilanciamento del carico

Per rimuovere un bilanciamento del carico, utilizzare il comando seguente:

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione carico di bilanciamento del carico utilizzando affinità dell'indirizzo IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
