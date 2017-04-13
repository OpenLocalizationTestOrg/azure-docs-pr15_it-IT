<properties
   pageTitle="Creare comunicare ascoltatore per il gruppo availabilty AlwaysOn per SQL Server in macchine virtuali di Azure"
   description="Istruzioni dettagliate per la creazione di un comunicare ascoltatore per un gruppo di availabilty AlwaysOn per SQL Server in macchine virtuali di Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure

In questo argomento viene illustrato come creare un bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn di SQL Server in macchine virtuali Azure in esecuzione in modello di gestione delle risorse. Un gruppo di disponibilità AlwaysOn richiede un sistema di bilanciamento del carico quando le istanze di SQL Server in macchine virtuali di Azure. Bilanciamento del carico contiene l'indirizzo IP per comunicare ascoltatore gruppo disponibilità. Se un gruppo di disponibilità si estende su più aree, un servizio di bilanciamento del carico ogni area.

Per completare questa attività, è necessario disporre di un gruppo di disponibilità di SQL Server AlwaysOn distribuito su Azure macchine virtuali nel modello di gestione delle risorse. Entrambe le macchine virtuali di SQL Server deve appartenere allo stesso set di disponibilità. È possibile utilizzare il [modello di Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) per creare automaticamente il gruppo di disponibilità AlwaysOn in Gestione risorse Azure. Questo modello crea automaticamente il servizio di bilanciamento del carico interno dell'utente. 

Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

In questo argomento è necessario che i gruppi di disponibilità sono già configurati.  

Argomenti correlati includono:

 - [Configurare la disponibilità AlwaysOn in macchine Virtuali Azure (grafica)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurare una connessione VNet a VNet tramite Gestione risorse di Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Procedura

Spostandosi all'interno del documento verranno creare e configurare un bilanciamento del carico nel portale di Azure. Dopo che è stata completata, sarà necessario configurare il cluster per usare l'indirizzo IP dal servizio di bilanciamento del carico per comunicare ascoltatore gruppo di disponibilità AlwaysOn.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Creare e configurare il servizio di bilanciamento del carico nel portale di Azure

In questa parte dell'attività è la procedura seguente nel portale di Azure:

1. Creare il servizio di bilanciamento del carico e configurare l'indirizzo IP

1. Configurare il pool di back-end

1. Creare la ricerca 

1. Impostare le regole di bilanciamento del carico

>[AZURE.NOTE] Se SQL Server in aree geografiche e gruppi di risorse diversi, viene eseguita tutti i passaggi due volte, una volta in ogni gruppo di risorse.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. creare bilanciamento del carico e configurare l'indirizzo IP

Il primo passaggio consiste nel creare bilanciamento del carico. Nel portale di Azure, aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. Nel gruppo di risorse, fare clic su **Aggiungi**.

- Ricerca di **bilanciamento del carico**. Nei risultati della ricerca selezionare **Bilanciamento del carico**, pubblicato da **Microsoft**.

- In e il **Servizio di bilanciamento del carico** , fare clic su **Crea**.

- In **Crea bilanciamento del carico**, configurare il bilanciamento del carico come descritto di seguito:

| Impostazione | Valore |
| ----- | ----- |
| **Nome** | Nome di testo che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
| **Schema** | **Interno** |
| **Rete virtuale** | Scegliere la rete virtuale di SQL Server.   |
| **Subnet**  | Scegliere subnet presenti SQL Server. |
| **Abbonamento** | Se si hanno più abbonamenti, possono essere visualizzati in questo campo. Selezionare l'abbonamento a cui si desidera associare a questa risorsa. In genere è la stessa sottoscrizione come tutte le risorse per il gruppo di disponibilità.  |
| **Gruppo di risorse** | Scegliere il gruppo di risorse di SQL Server. | 
| **Posizione** | Scegliere la posizione di Azure SQL Server disponibili in. |

- Fare clic su **Crea**. 

Azure consente di creare il bilanciamento del carico configurato in precedenza. Bilanciamento del carico appartiene a una rete specifica, alla subnet, gruppo di risorse e posizione. Al termine di Azure, verificare le impostazioni di bilanciamento del carico in Azure. 

Configurare l'indirizzo IP di bilanciamento carico.  

- In e **le impostazioni** di bilanciamento del carico il carico, fare clic su **indirizzo IP**. E **l'indirizzo IP** indica che si tratta di un bilanciamento del carico privato nella stessa rete virtuale per SQL Server. 

- Configurare le impostazioni seguenti: 

| Impostazione | Valore |
| ----- | ----- |
| **Subnet** | Scegliere subnet presenti SQL Server. |
| **Assegnazione** | **Statica** |
| **Indirizzo IP** | Digitare un indirizzo IP virtuale inutilizzato dalla subnet.  |

- Salvare le impostazioni.

Bilanciamento del carico è ora disponibile un indirizzo IP. Registrare l'indirizzo IP. Utilizzare questo indirizzo IP quando si crea un comunicare ascoltatore sul cluster. In uno script di PowerShell più avanti in questo articolo, usare l'indirizzo per la `$ILBIP` variabile.



## <a name="2-configure-the-backend-pool"></a>2. configurare il pool di back-end

Il passaggio successivo consiste nel creare un pool di indirizzi back-end. Azure chiama il pool di indirizzi back-end *pool di back-end*. In questo caso, il pool di back-end è gli indirizzi dei due SQL Server a un gruppo di disponibilità. 

- Nel gruppo di risorse, fare clic sul bilanciamento del carico che è stato creato. 

- In **Impostazioni**, fare clic su **pool di back-end**.

- Nel **pool di indirizzi back-end**, fare clic su **Aggiungi** per creare un pool di indirizzi back-end. 

- In **Aggiungi back-end pool** sotto il **nome**, digitare un nome per il pool di back-end.

- In **macchine virtuali** fare clic su **+ Aggiungi una macchina virtuale**. 

- In **macchine virtuali scegliere** fare clic su **scegliere un set di disponibilità** e specificare che la disponibilità set che a cui appartiene la macchine virtuali di SQL Server.

- Dopo aver scelto il set di disponibilità, fare clic su **Scegli macchine virtuali**. Fare clic su due macchine virtuali che ospitano le istanze di SQL Server del gruppo di disponibilità. Fare clic su **Seleziona**. 

- Fare clic su **OK** per chiudere le pale per **macchine virtuali scegliere**e **aggiungere back-end pool**. 

Azure consente di aggiornare le impostazioni per il pool di indirizzi back-end. Set di disponibilità è ora disponibile un pool di due SQL Server.

## <a name="3-create-a-probe"></a>3. creare un sondaggio

Il passaggio successivo consiste nel creare una ricerca. La ricerca consente di definire come Azure verificherà che di SQL Server appartiene comunicare ascoltatore gruppo disponibilità. Azure verrà esaminare il servizio in base all'indirizzo IP su una porta che è possibile definire quando si crea la ricerca.

- In e **le impostazioni** di bilanciamento del carico il carico, fare clic su **Analizza**. 

- Scegliere **Aggiungi**e **Analizza** il.

- Configurare la ricerca e il **componente verifica** . Per configurare la ricerca, utilizzare i valori seguenti:

| Impostazione | Valore |
| ----- | ----- |
| **Nome** | Nome di testo che rappresenta la ricerca. Ad esempio **SQLAlwaysOnEndPointProbe**. |
| **Protocollo** | **TCP** |
| **Porta** | È possibile utilizzare qualsiasi porta disponibile. Ad esempio *59999*.    |
| **Intervallo**  | *5* | 
| **Soglia non corretti**  | *2* | 

- Fare clic su **OK**. 

>[AZURE.NOTE] Assicurarsi che la porta specificata sia aperta sul firewall di entrambi i server SQL. Entrambi i server richiedono una regola in entrata per la porta TCP in uso. Per ulteriori informazioni, vedere [aggiunta o Modifica regola Firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

Azure consente di creare la ricerca. Azure verrà utilizzato per testare il sondaggio, SQL Server è comunicare ascoltatore per il gruppo di disponibilità

## <a name="4-set-the-load-balancing-rules"></a>4. impostare regole di bilanciamento del carico

Impostare le regole di bilanciamento del carico. Le regole di bilanciamento del carico configurare come il bilanciamento del carico indirizza il traffico a SQL Server. Per questo servizio di bilanciamento del carico si consentirà diretto server restituito perché solo uno dei due SQL Server sarà mai proprietario della risorsa di comunicare ascoltatore gruppo disponibilità alla volta.

- Scegliere **regole di bilanciamento del carico**e **le impostazioni** di bilanciamento del carico il carico. 

- In e il **bilanciamento del carico regole** , fare clic su **Aggiungi**.

- Consente di configurare una regola di bilanciamento del carico e il **componente caricare le regole di bilanciamento del carico** . Usare le impostazioni seguenti: 

| Impostazione | Valore |
| ----- | ----- |
| **Nome** | Nome di testo che rappresenta le regole di bilanciamento del carico. Ad esempio **SQLAlwaysOnEndPointListener**. |
| **Protocollo** | **TCP** |
| **Porta** | *1433*   |
| **Porta di back-end** | *1433*. si noti che questa verrà disabilitata poiché questa regola utilizza **IP mobile (restituito diretto server)**.   |
| **Presenza** | Usare il nome del sondaggio creato per il servizio di bilanciamento del carico. |
| **Persistenza sessione**  | **Nessuno** | 
| **Tempo di inattività (minuti)**  | *4* | 
| **Mobile IP (diretto server restituito)**  | **Attivato** | 

 >[AZURE.NOTE] Potrebbe essere necessario scorrere verso il basso nella e per visualizzare tutte le impostazioni.

- Fare clic su **OK**. 

- Azure consente di configurare il bilanciamento del carico regola. Bilanciamento del carico è ora configurato per instradare il traffico a SQL Server che ospita comunicare ascoltatore per il gruppo di disponibilità. 

A questo punto al gruppo di risorse è un servizio di bilanciamento del carico, la connessione a entrambi i computer SQL Server. Bilanciamento del carico contiene anche un indirizzo IP per comunicare ascoltatore gruppo di disponibilità AlwaysOn di SQL Server in modo che entrambi i computer possono rispondere a richieste per i gruppi di disponibilità.

>[AZURE.NOTE] Se il server di SQL in due aree separate, ripetere i passaggi nell'area altri. Ogni area richiede un sistema di bilanciamento del carico. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurare il cluster per utilizzare l'indirizzo IP di bilanciamento carico 

Il passaggio successivo consiste nel configurare comunicare ascoltatore sul cluster e visualizzare comunicare ascoltatore online. A tale scopo, eseguire le operazioni seguenti: 

1. Creare comunicare ascoltatore gruppo disponibilità in del cluster 

1. Porta comunicare ascoltatore online

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. creare il comunicare ascoltatore gruppo disponibilità del cluster

In questo passaggio è creare manualmente comunicare ascoltatore gruppo disponibilità in Gestione Cluster di Failover e SQL Server Management Studio (Express).

- Utilizzare RDP per connettere la macchina virtuale Azure che ospita la replica principale. 

- Aprire Gestione Cluster di Failover.

- Selezionare il nodo **reti** e prendere nota del nome di rete cluster. Questo nome verrà utilizzato nel `$ClusterNetworkName` variabili dello script di PowerShell.

- Espandere il nome del cluster e quindi fare clic su **ruoli**.

- Nel riquadro di **ruoli** , pulsante destro del mouse sul nome del gruppo disponibilità e quindi selezionare **Aggiungi risorsa** > **Punto di accesso Client**.

- Nella casella **nome** , creare un nome per il nuovo comunicare ascoltatore, quindi fare doppio clic su **Avanti** e quindi fare clic su **Fine**. Non visualizzare comunicare ascoltatore o la risorsa online a questo punto.

 >[AZURE.NOTE] Il nome del nuovo comunicare ascoltatore è il nome di rete utilizzate dalle applicazioni per connettersi al database nel gruppo di disponibilità di SQL Server.

- Fare clic sulla scheda **risorse** , quindi espandere il punto di accesso Client appena creato. Pulsante destro del mouse la risorsa IP e scegliere Proprietà. Prendere nota del nome dell'indirizzo IP. Questo nome in verrà utilizzato il `$IPResourceName` variabili dello script di PowerShell.

- In **Indirizzo IP** fare clic su **Indirizzo IP statico** e impostare l'indirizzo IP statico per lo stesso indirizzo utilizzato quando si imposta l'indirizzo IP di bilanciamento carico nel portale di Azure. Abilita NetBIOS per questo indirizzo e fare clic su **OK**. Ripetere questo passaggio per ogni risorsa IP se la soluzione si estende su più VNets Azure. 

- In nodo cluster che ospita attualmente la replica principale, aprire una finestra di PowerShell ISE e incollare i comandi seguenti in un nuovo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Aggiornare le variabili ed eseguire lo script di PowerShell per configurare l'indirizzo IP e porte per comunicare ascoltatore nuovo.

 >[AZURE.NOTE] Se il server SQL in aree separate, è necessario eseguire due volte lo script di PowerShell. La prima volta usare il nome di rete cluster, nome risorsa IP cluster e caricare indirizzo IP di bilanciamento del primo gruppo di risorse. La seconda volta usare il nome di rete cluster, nome risorsa IP cluster e caricare indirizzo IP di bilanciamento del secondo gruppo di risorse.

Il cluster è ora disponibile una risorsa di comunicare ascoltatore gruppo disponibilità.

## <a name="2-bring-the-listener-online"></a>2. porta comunicare ascoltatore online

Risorsa di comunicare ascoltatore gruppo disponibilità configurato, è possibile trasferire anche comunicare ascoltatore online in modo che le applicazioni possono connettersi ai database nel gruppo di disponibilità con comunicare ascoltatore.

- Passare a gestione Cluster di Failover. Espandere **i ruoli** e quindi selezionare il gruppo di disponibilità. Nella scheda **risorse** , pulsante destro del mouse sul nome di comunicare ascoltatore e scegliere **proprietà**.

- Fare clic sulla scheda **relazioni** . Se sono presenti più risorse nell'elenco, verificare che gli indirizzi IP o meno e, dipendenze. Fare clic su **OK**.

- Pulsante destro del mouse sul nome di comunicare ascoltatore e fare clic su **Connetti**.


- Una volta comunicare ascoltatore sia connesso a Internet, nella scheda **risorse** destro del mouse sul gruppo di disponibilità e fare clic su **proprietà**.

- Creare una dipendenza sulla risorsa nome comunicare ascoltatore (non nome risorse indirizzo IP). Fare clic su **OK**.


- Avviare SQL Server Management Studio e connettersi a replica primaria.


- Passare alla **disponibilità AlwaysOn alta** | **disponibilità gruppi** | **gruppo di disponibilità listener**. 


- Viene visualizzato il nome di comunicare ascoltatore creato in Gestione Cluster di Failover. Pulsante destro del mouse sul nome di comunicare ascoltatore e scegliere **proprietà**.


- Nella casella **porta** specificare il numero di porta per comunicare ascoltatore gruppo disponibilità utilizzando $EndpointPort utilizzato in precedenza (1433 era l'impostazione predefinita), quindi fare clic su **OK**.

Ora è un gruppo di disponibilità AlwaysOn di SQL Server in macchine virtuali Azure in esecuzione in modalità di gestione delle risorse. 

## <a name="test-the-connection-to-the-listener"></a>Verificare la connessione a comunicare ascoltatore

Per verificare la connessione:

1. RDP a SQL Server nella stessa rete virtuale, ma non dispone di replica. Può trattarsi di altri SQL Server del cluster.

1. L'utilità **sqlcmd** per verificare la connessione. Ad esempio, il seguente script stabilisce una connessione **sqlcmd** replica primaria tramite comunicare ascoltatore con l'autenticazione di Windows:

        sqlcmd -S <listenerName> -E

Connettersi a qualsiasi istanza di SQL Server ospita la replica primaria automaticamente la connessione SQLCMD. 

## <a name="guidelines-and-limitations"></a>Linee guida e le limitazioni

Tenere presente le seguenti linee guida sulla comunicare ascoltatore gruppo disponibilità in Azure uso interno bilanciamento del carico:

- Sola comunicare ascoltatore gruppo interno disponibilità è supportata per ogni servizio cloud perché comunicare ascoltatore è configurato per il servizio di bilanciamento del carico e non esiste un solo di bilanciamento del carico interno. È possibile creare multipe listener esterni. 

- Con un sistema di bilanciamento del carico interno solo accedere comunicare ascoltatore all'interno della stessa rete virtuale.
 
