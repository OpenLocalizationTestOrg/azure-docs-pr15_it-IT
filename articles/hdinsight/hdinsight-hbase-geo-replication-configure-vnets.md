<properties 
   pageTitle="Configurare una connessione VPN tra due reti virtuali | Microsoft Azure" 
   description="Informazioni su come configurare una connessione VPN e la risoluzione dei nomi di dominio tra due reti virtuale Azure e su come configurare replica geografico HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Configurare una connessione VPN tra due reti virtuale Azure  

> [AZURE.SELECTOR]
- [Configurare la connettività VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurare il sistema DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurare la replica HBase](hdinsight-hbase-geo-replication.md) 

La connettività a siti virtuali Azure utilizza un gateway VPN per fornire un tunnel protetto tramite Ipsec/IKE. Il VNets possono essere diversi abbonamenti e aree geografiche diverse. È anche possibile combinare VNet per la comunicazione VNet con le configurazioni più siti. Esistono diversi motivi per VNet alla connettività VNet:

- Area cross geografico-ridondanza e geografico presenza 
- Internazionali applicazioni a più livelli con limite di isolamento sicuro 
- Cross abbonamento, le comunicazioni tra organizzazioni di Azure

Per ulteriori informazioni, vedere [configurare un VNet a VNet connessione](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). 

Per visualizzare il video:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

In questa esercitazione fa parte di [serie] [ hdinsight-hbase-replication] sulla creazione di replica di geografico HBase. 

- Configurare una connessione VPN tra due reti virtuale (questa esercitazione)
- [Configurare il DNS per le reti virtuali][hdinsight-hbase-geo-replication-dns]
- [Configurare la replica geografico HBase][hdinsight-hbase-geo-replication]

Il diagramma seguente illustra le reti virtuali due che verrà creato in questa esercitazione:

![Diagramma di rete virtuale replica HDInsight HBase][img-vnet-diagram]
 

##<a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un lavoro utilizzando Azure PowerShell**.

    Prima di eseguire gli script di PowerShell, assicurarsi che si è connessi al proprio abbonamento Azure mediante il seguente cmdlet:

        Add-AzureAccount

    Se si hanno più abbonamenti Azure, utilizzare il cmdlet seguente per impostare l'abbonamento corrente:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Nomi dei servizi Azure e nomi di macchina virtuale devono essere univoci. Il nome utilizzato in questa esercitazione è Contoso-[nome servizio Azure/macchine Virtuali]-[UE / Usa]. Ad esempio Contoso-VNet-dell'Unione europea è la rete virtuale Azure nell'interfaccia di dati Nord Europa; Contoso-DNS-US è il server DNS macchine Virtuali nel centro dati orientale degli Stati Uniti. È necessario sorgere con i propri nomi.
 

##<a name="create-two-azure-vnets"></a>Creare due VNets di Azure



**Per creare una rete virtuale denominata Contoso VNet UE in Nord Europa**

1.  Accedere al [portale classica Azure][azure-portal].
2.  Fare clic su **Nuovo**, **servizi di rete**, **rete virtuale**, **Crea personalizzato**.
3.  Immettere:

    - **Nome**: Contoso-VNet-UE
    - **Posizione**: Europa Nord America

        In questa esercitazione utilizza Europa Nord America e Stati Uniti orientali Data Center. È possibile scegliere il proprio Data Center.
4.  Immettere:

    - **SERVER DNS**: (lasciarlo vuoto) 
    
        Server DNS sarà necessario per la risoluzione dei nomi all'interno di reti virtuali. Per ulteriori informazioni su quando utilizzare forniti Azure risoluzione dei nomi e quando utilizzare i server DNS, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Per istruzioni su come configurare la risoluzione dei nomi tra VNets, vedere [Configurare DNS tra due reti virtuale Azure][hdinsight-hbase-dns].
  
    - **Configurare una connessione VPN punto al sito**: (non selezionato)

        Punto al sito non vale per questo scenario.

    - **Configurare una connessione VPN da sito**: (non selezionato)
    
        Configurare la connessione VPN da sito per la rete virtuale Azure verrà in Data Center del orientale degli Stati Uniti.
5.  Immettere:

    -   **Indirizzo spazio iniziale IP**: 10.1.0.0
    -   **Indirizzo spazio CIDR**: / 16
    -   **Partire da 1 a subnet IP**: 10.1.0.0
    -   **CIDR subnet 1**: / 24

    Lo spazio di indirizzi non può essere sovrapposta con la rete virtuale degli Stati Uniti.  

**Per creare una rete virtuale denominata Contoso VNet UE europei ovest**

- Ripetere l'ultima procedura con i valori seguenti:

    - **Nome**: Contoso-VNet-US
    - **Posizione**: Usa orientale
     
    - **SERVER DNS**: (lasciarlo vuoto)
    - **Configurare una connessione VPN punto al sito**: (non selezionato)
    - **Configurare una connessione VPN da sito**: (non selezionato)
     
    - **Indirizzo spazio iniziale IP**: 10.2.0.0
    - **Indirizzo spazio CIDR**: / 16
    - **Partire da 1 a subnet IP**: 10.2.0.0
    - **CIDR subnet 1**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Configurare una connessione VPN tra due VNets

###<a name="create-local-networks"></a>Creare le reti locale

Quando si crea un VNet alla configurazione VNet, è necessario configurare ogni VNet per identificare tra loro come un sito di rete locale. In questa sezione, è necessario configurare ogni VNet come una rete locale. Reti locali condividano gli spazi di indirizzi IP stesso VNet corrispondente.

![Configurare Azure VPN da sito - reti locali azure][img-vnet-lnet-diagram]


**Per creare una rete locale denominato Contoso-LNet-UE corrispondenti lo spazio di indirizzi di rete dell'Unione europea Contoso VNet**

1. Dal portale classica di Azure, fare clic su **Nuovo**, **Servizi di rete**, **VIRTUALI**, **Aggiungere rete locale**.
3. Immettere:

    - **Nome**: Contoso-LNet-UE
    - **Indirizzo IP del dispositivo VPN**: 192.168.0.1 (questo indirizzo verrà aggiornato in un secondo momento)

        In genere, utilizzare l'indirizzo IP esterno effettivo per un dispositivo VPN. Per VNet per le configurazioni VNet, utilizzare l'indirizzo IP del gateway VPN. Dato che non è creato i gateway VPN per due VNets ancora, si immette un indirizzo IP arbitary, tornare a risolvere il problema.
4.  Immettere:

    - **Indirizzo spazio iniziale IP:** 10.1.0.0
    - **CIDR spazio di indirizzi:** /16
    
    Che deve corrispondere esattamente all'intervallo specificato in precedenza per dell'Unione europea Contoso VNet.

**Per creare una rete locale denominato Contoso-LNet-US corrispondenti lo spazio di indirizzi di rete di Contoso-VNet-US**

- Ripetere l'ultima procedura con i parametri seguenti:

    - **Nome**: Contoso-LNet-US
    - **Indirizzo IP del dispositivo VPN**: 192.168.0.1 (questo indirizzo verrà aggiornato in un secondo momento)
     
    - **Indirizzo spazio iniziale IP**: 10.2.0.0
    - **Indirizzo spazio CIDR**: / 16


###<a name="create-vpn-gateways"></a>Creare gateway VPN

In questa configurazione sono presenti due parti. Prima configurare una connessione di da sito VNet a una rete locale e quindi si crea una connessione VPN routing dinamica. VNet a VNet richiede gateway VPN di Azure con VPN routing dinamico. Azure VPN routing statica non sono supportate.

**Per configurare la connessione al sito dell'Unione europea Contoso VNet Contoso-LNet-US**

1.  Dal portale classica di Azure, fare clic su **reti** nel riquadro sinistro
2.  Fare clic su **Dell'Unione europea Contoso VNet**.
3.  Fare clic sulla scheda **CONFIGUE** .
4.  Selezionare **Connetti alla rete locale**.
5.  Nella **Rete locale**, selezionare **Contoso-LNet-US**.
6.  Fare clic su **Aggiungi subnet di gateway** nella sezione spazi indirizzo virtuali.
7.  Fare clic su **Salva**.
8.  Fare clic su **OK** per confermare.


**Per creare un gateway VPN per dell'Unione europea Contoso VNet**

1.  Dal portale classica di Azure, fare clic sulla scheda **DASHBOARD** .
4.  Fare clic su **Creare GATEWAY** nella parte inferiore della pagina e quindi fare clic su **Routing dinamico**.
5.  Fare clic su **Sì** per confermare. Si noti l'elemento grafico gateway nella pagina cambia a giallo e indica che la creazione di Gateway. In genere bastano circa 15 minuti per creare il gateway.

    Quando lo stato del gateway la connessione, l'indirizzo IP per ogni Gateway saranno visibile nel Dashboard. Prendere nota dell'indirizzo IP corrispondente a ogni VNet, facendo attenzione a non combinarli. Questi sono gli indirizzi IP utilizzati quando si modificano gli indirizzi IP di segnaposto per il dispositivo VPN nelle reti locali.

6.  Creare una copia dell' **Indirizzo IP del GATEWAY**. Si utilizzerà per configurare l'indirizzo IP del gateway VPN per Contoso VNet UE nella sezione successiva.

**Per creare un gateway VPN per dell'Unione europea Contoso VNet**

- Ripetere la procedura ultime due per configurare la connessione da sito Contoso-VNet-US dell'Unione europea Contoso LNet e crea un gateway VPN per Contoso-Vnet-US. Al termine, si avrà l'indirizzo IP del gateway VPN per Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Impostare il dispositivo VPN gli indirizzi IP per reti locali
Nella sezione precedente, creare un gateway VPN per ognuna del VNets. Hanno ottenuto gli indirizzi IP dei gateway VPN. A questo punto è possibile tornare indietro per configurare gli indirizzi IP di rete VPN dispositivo.

**Per configurare l'indirizzo IP del dispositivo VPN per dell'Unione europea Contoso LNet** 

1.  Dal portale classica di Azure, fare clic su **reti** nel riquadro sinistro.
2.  Fare clic su **Reti locali** dall'alto.
3.  Fare clic su **Dell'Unione europea Contoso LNet**e quindi fare clic su **Modifica** nella parte inferiore.
4.  Aggiornare **l'indirizzo IP del dispositivo VPN**.  Questo è l'indirizzo che viene visualizzato nella scheda DASHBOARD dell'Unione europea Contoso VNET.
5.  Scegliere il pulsante destro.
6.  Fare clic sul pulsante di controllo.

**Per configurare l'indirizzo IP del dispositivo VPN per Contoso-LNet-US** 

- Ripetere l'ultima procedura per configurare l'indirizzo IP del dispositivo VPN per Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Impostare le chiavi gateway VNet

Il gateway Vnet utilizzare una chiave condivisa per eseguire l'autenticazione di connessioni tra le reti virtuali. Non è possibile configurare la chiave dal portale classica di Azure. È necessario utilizzare PowerShell o .NET SDK.

**Per impostare i tasti**

1. Dalla workstation, aprire **Windows PowerShell ISE** o console di Windows PowerShell.
2. Aggiornare i parametri in questo script Segui ed eseguirlo:

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Verificare la connessione VPN 

Senza macchine virtuali distribuite per la VNets, è possibile utilizzare il diagramma visual virtuali pagina VNet Dashboard nel portale classica di Azure per controllare lo stato di connessione:

![Virtuali replica HDInsight HBase lo stato della connessione VPN][img-vpn-status]
  



##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono appreso come configurare una connessione VPN tra due reti virtuale Azure. Altri due articoli della serie coprono:

- [Configurare il sistema DNS tra due reti virtuale Azure][hdinsight-hbase-geo-replication-dns]
- [Configurare la replica geografico HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 
