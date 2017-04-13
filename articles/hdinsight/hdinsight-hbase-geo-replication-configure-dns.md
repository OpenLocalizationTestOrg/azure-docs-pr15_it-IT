<properties 
   pageTitle="Configurare il sistema DNS tra due reti virtuale Azure | Microsoft Azure" 
   description="Informazioni su come configurare una connessione VPN e la risoluzione dei nomi di dominio tra due reti virtuali e su come configurare replica geografico HBase." 
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

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configurare il sistema DNS tra due reti virtuale Azure

> [AZURE.SELECTOR]
- [Configurare la connettività VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurare il sistema DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurare la replica HBase](hdinsight-hbase-geo-replication.md) 


Informazioni su come aggiungere e configurare i server DNS a Azure reti virtuali per gestire la risoluzione dei nomi all'interno di reti virtuali.

In questa esercitazione è la seconda parte della [serie] [ hdinsight-hbase-geo-replication] sulla creazione di replica di geografico HBase:

- [Configurare una connessione VPN tra due reti virtuale][hdinsight-hbase-geo-replication-vnet]
- Configurare il DNS per le reti virtuali (questa esercitazione)
- [Configurare la replica geografico HBase][hdinsight-hbase-geo-replication]


Il diagramma seguente illustra le due reti virtuale è stato creato in [configurare una connessione VPN tra due reti virtuali][hdinsight-hbase-geo-replication-vnet]:

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

- **Azure due virtuali con la connettività VPN**.  Per ulteriori informazioni, vedere [configurare una connessione VPN tra due reti virtuali Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Nomi dei servizi Azure e nomi di macchina virtuale devono essere univoci. Il nome utilizzato in questa esercitazione è Contoso-[nome servizio Azure/macchine Virtuali]-[UE / Usa]. Ad esempio Contoso-VNet-dell'Unione europea è la rete virtuale Azure nell'interfaccia di dati Nord Europa; Stati Uniti di DNS Contoso sono il server DNS macchine Virtuali nell'interfaccia di dati uniti orientali. È necessario sorgere con i propri nomi.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Creazione di Azure macchine virtuali da utilizzare come server DNS

**Per creare una macchina virtuale all'interno di Contoso-VNet-UE, denominato dell'Unione europea Contoso DNS**

1.  Fare clic su **Nuovo**, **calcolare**, **macchina virtuale**, **Da raccolta**.
2.  Scegliere **Windows Server 2012 R2 Data Center**.
3.  Immettere:
    - **Nome macchina virtuale**: Contoso-DNS-UE
    - **Nuovo nome utente**: 
    - **Nuova PASSWORD**: 
4.  Immettere:
    - **Servizio CLOUD**: creare un nuovo servizio cloud
    - **Regione/gruppo AFFINITÀ/virtuale di rete**: (selezionare Contoso VNet UE)
    - **Subnet rete virtuale**: Subnet-1
    - **ACCOUNT di archiviazione**: usare un account generato automaticamente lo spazio di archiviazione
    
        Il nome del servizio cloud sarà lo stesso nome macchina virtuale. In questo caso, che è dell'Unione europea Contoso DNS. Per macchine virtuali successive, è possibile scegliere di utilizzare lo stesso servizio cloud.  Tutte le macchine virtuali sotto lo stesso servizio cloud condividono lo stesso virtuali e suffisso di dominio.

        Account di archiviazione viene utilizzato per archiviare il file di immagine macchina virtuale. 
    - **I punti finali**: (scorrere verso il basso e selezionare **DNS**) 

Dopo aver creata la macchina virtuale, scoprire interno IP e indirizzi IP esterni.

1.  Fare clic su nome macchina virtuale **Dell'Unione europea Contoso DNS**.
2.  Fare clic su **DashBoard**.
3.  Prendere nota:
    - INDIRIZZO IP PUBBLICO VIRTUALE
    - INDIRIZZO IP INTERNO


**Per creare una macchina virtuale all'interno di Contoso-VNet-US, denominata Contoso-DNS-US** 

- Ripetere la stessa procedura per creare una macchina virtuale con i valori seguenti:
    - NOME macchina virtuale: Contoso-DNS-US
    - REGIONE/gruppo AFFINITÀ/virtuale rete: selezionare Contoso-VNET-US
    - SUBNET VIRTUALI: Subnet-1
    - ACCOUNT di archiviazione: Utilizzare un account generato automaticamente lo spazio di archiviazione
    - I punti finali: (selezionare DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Impostare gli indirizzi IP statici per due macchine virtuali

I server DNS richiede indirizzi IP statici.  Questo passaggio non può essere eseguito dal portale classica di Azure. Utilizzare PowerShell Azure.

**Per configurare l'indirizzo IP statico per due macchine virtuali**

1. Aprire Windows PowerShell ISE.
2. Eseguire i cmdlet seguenti.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    Nome è il nome del servizio cloud. Perché il server DNS sulla prima macchina virtuale del servizio cloud, il nome del servizio cloud è lo stesso nome macchina virtuale.

    Potrebbe essere necessario aggiornare nome e il nome in modo che corrispondano i nomi che si dispone.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Aggiungere i Server DNS ruolo due macchine virtuali

**Per aggiungere il ruolo di Server DNS dell'Unione europea Contoso DNS**

1.  Dal portale classica di Azure, fare clic su **macchine virtuali** a sinistra. 
2.  Fare clic su **Dell'Unione europea Contoso DNS**.
3.  Fare clic su **DASHBOARD** nella parte superiore.
4.  Fare clic su **CONNETTI** dal basso e seguire le istruzioni per connettere la macchina virtuale tramite RDP.
2.  All'interno della sessione RDP, fare clic sul pulsante Windows nell'angolo inferiore sinistro per aprire la schermata Start.
3.  Fare clic sul riquadro **Server Manager** .
4.  Fare clic su **Aggiungi ruoli e funzionalità**.
5.  Fare clic su **Avanti**
6.  Selezionare **installazione basato sui ruoli o funzionalità di base**e quindi fare clic su **Avanti**.
7.  Selezionare la macchina virtuale DNS (deve essere evidenziato già) e quindi fare clic su **Avanti**.
8.  Controllare i **Server DNS**.
9.  Fare clic su **Aggiungi funzionalità**e quindi fare clic su **Continua**.
10. Fare clic su **Avanti** tre volte e quindi fare clic su **Installa**. 

**Per aggiungere il ruolo di Server DNS per Contoso-DNS-US**

- Ripetere i passaggi per aggiungere ruolo DNS **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Assegnare i server DNS per le reti virtuali

**Per registrare i due server DNS**

1.  Dal portale classica di Azure, fare clic su **Nuovo**, **Servizi di rete**, **VIRTUALI**, **Eseguire la registrazione al SERVER DNS**.
2.  Immettere:
    - **Nome**: Contoso-DNS-UE
    - **Indirizzo IP del SERVER DNS**: 10.1.0.4: l'indirizzo IP obbligatorie corrispondente all'indirizzo IP di DNS server macchina virtuale.
     
3.  Ripetere la procedura per eseguire la registrazione degli Stati Uniti Contoso DNS con le impostazioni seguenti:
    - **Nome**: Contoso-DNS-US
    - **Indirizzo IP del SERVER DNS**: 10.2.0.4

**Per assegnare i due server DNS a due reti virtuale**

1.  Fare clic su **reti** dal riquadro di sinistra nel portale di classica.
2.  Fare clic su **Dell'Unione europea Contoso VNet**.
3.  Fare clic su **Configura**.
4.  Selezionare **Dell'Unione europea Contoso DNS** nella sezione **dns servers** .
5.  Fare clic su **Salva** nella parte inferiore della pagina e fare clic su **Sì** per confermare.
6.  Ripetere la procedura per assegnare il server DNS **Contoso-DNS-US** per la rete virtuale **Contoso-VNet-US** .

Per aggiornare la configurazione del server DNS, è necessario riavviare tutte le macchine virtuali distribuiti a reti virtuali.

**Per riavviare le macchine virtuali**

1. Dal portale classica di Azure, fare clic su **macchine virtuali** a sinistra.
2. Fare clic su **Dell'Unione europea Contoso DNS**.
3. Fare clic su **Dashboard** nella parte superiore.
4. Scegliere **RIAVVIA** nella parte inferiore.
5. Ripetere la stessa procedura per riavviare **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Configurare l'inoltro condizionale

Il server DNS su ogni virtuali possibile risolvere solo i nomi DNS all'interno della rete virtuale. È necessario configurare un server d'inoltro condizionale in modo che punti al server DNS peer per la risoluzione nella rete virtuale peer.

Per configurare server d'inoltro condizionale, è necessario conoscere i suffissi dei due server DNS del dominio. Suffissi DNS possono essere diversi a seconda della configurazione di servizi Cloud utilizzato per creare le macchine virtuali. Per ogni suffisso DNS nella rete virtuale utilizzato, è necessario aggiungere un server d'inoltro condizionale. 

**Per trovare i suffissi dei due server DNS del dominio**

1. RDP **Dell'Unione europea Contoso DNS**.
2. Aprire console di Windows PowerShell o prompt dei comandi.
3. Eseguire **ipconfig**e prendere nota **suffisso DNS specifici della connessione**.
4. Non chiudere la sessione RDP, è comunque necessario. 
5. Ripetere questi passaggi per individuare il **suffisso DNS specifici della connessione** di **Contoso-DNS-US**.


**Per configurare l'inoltro**
 
1.  Da sessione RDP a **Contoso DNS UE**, premere il tasto Windows in basso a sinistra.
2.  Fare clic su **Strumenti di amministrazione**.
3.  Fare clic su **DNS**.
4.  Nel riquadro a sinistra espandere **l'errore DSN**, **Dell'Unione europea Contoso DNS**.
5.  Immettere le informazioni seguenti:
    - **Dominio DNS**: immettere il suffisso di Contoso-DNS-US. Ad esempio: Contoso-DNS-US.b5.internal.cloudapp.net.
    - **Indirizzi IP dei server master**: immettere 10.2.0.4, indirizzo IP di Contoso-DNS-US.
6.  Premere **INVIO**e quindi fare clic su **OK**.  A questo punto sarà possibile risolvere indirizzo IP del Contoso-DNS-US dell'Unione europea Contoso DNS.
7.  Ripetere i passaggi per aggiungere un server d'inoltro DNS per il servizio DNS sul computer virtuale degli Stati Uniti Contoso DNS con i valori seguenti:
    - **Dominio DNS**: immettere il suffisso dell'UE di DNS Contoso. 
    - **Indirizzi IP dei server master**: immettere 10.2.0.4, indirizzo IP di Contoso-DNS-dell'Unione europea.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Testare la risoluzione dei nomi su reti virtuale

A questo punto è possibile verificare la risoluzione dei nomi di host reti virtuale. Ping è bloccato da firewall per impostazione predefinita.  È possibile usare nslookup per risolvere le macchine virtuali di server DNS (è necessario utilizzare FQDN) nelle reti peer.  


##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono appreso come configurare la risoluzione dei nomi su reti virtuale con connessioni VPN. Altri due articoli della serie coprono:

- [Configurare una connessione VPN tra due reti virtuale Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurare la replica geografico HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
