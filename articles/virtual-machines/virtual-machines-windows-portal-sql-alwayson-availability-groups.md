<properties
    pageTitle="Configurare sempre nel gruppo di disponibilità in macchine Virtuali di Azure automaticamente - Gestione risorse"
    description="Creare un gruppo di disponibilità sempre attive con Azure macchine virtuali in modalità di gestione di risorse Azure. In questa esercitazione principalmente utilizza l'interfaccia utente per creare automaticamente l'intera soluzione."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurare sempre nel gruppo di disponibilità in macchine Virtuali di Azure automaticamente - Gestione risorse

> [AZURE.SELECTOR]
- [Manager delle risorse: modello](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Manager delle risorse: manuale](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

In questa esercitazione-to-end viene illustrato come creare un gruppo di disponibilità di SQL Server con macchine virtuali di gestione di risorse Azure. Nell'esercitazione pale Azure vengono utilizzati per configurare un modello. Si verrà rivedere le impostazioni predefinite, digitare le impostazioni necessarie e aggiornare le pale nel portale di come si scoprirà questa esercitazione.

Al termine dell'esercitazione, la soluzione di gruppo disponibilità di SQL Server in Azure è costituita dagli elementi seguenti:

- Una rete virtuale che contiene più subnet, tra cui front-end e una subnet back-end

- Controller di dominio due con un dominio di Active Directory (AD)

- Due macchine virtuali Server SQL distribuito alla subnet back-end e al dominio di Active Directory

- Un cluster WSFC 3 nodi con il modello di base della maggior parte nodo

- Un gruppo di disponibilità con due icona del commit di un database di disponibilità

Nella figura seguente è una rappresentazione grafica della soluzione.

![Architettura di laboratorio di test per AG in Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Tutte le risorse in questa soluzione appartiene a un gruppo di risorse singola.

In questa esercitazione si presuppone che le operazioni seguenti:

- Si dispone già di un account Azure. Se non si dispone di uno, [iscriversi a un account di prova](http://azure.microsoft.com/pricing/free-trial/).

- Si sa come effettuare il provisioning di una macchina virtuale Server SQL dalla raccolta macchina virtuale utilizzando l'interfaccia grafica. Per ulteriori informazioni, vedere [il Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Si dispone già di una conoscenza approfondita dei gruppi di disponibilità. Per ulteriori informazioni, vedere [sempre in gruppi disponibilità (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se si è interessati all'utilizzo di gruppi di disponibilità con SharePoint, vedere anche [configurare SQL Server 2012 sempre in gruppi di disponibilità per SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

In questa esercitazione si utilizzerà il portale Azure per:

- Selezionare il modello sempre attivo dal portale

- Esaminare le impostazioni del modello e aggiornare alcune impostazioni di configurazione per l'ambiente

- Eseguire il monitoraggio Azure durante la creazione dell'intero ambiente

- Connettersi a uno dei controller di dominio e quindi su uno dei server SQL

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Eseguire il provisioning del cluster dalla raccolta

Azure fornisce un'immagine della raccolta per l'intera soluzione. Per individuare il modello:

1.  Accedere al portale di Azure con il proprio account.
1.  Scegliere portale Azure **+ nuovo.** Il portale verrà aperto e il nuovo.
1.  Nel nuovo blade Cerca **AlwaysOn**.
![Trovare il modello di AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Nei risultati della ricerca individuare **AlwaysOn Cluster di SQL Server**.
![Modello AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  Scegliere **Gestione risorse** **Selezionare un modello di distribuzione** .

### <a name="basics"></a>Nozioni di base

Fare clic su **Nozioni di base** e configurare gli elementi seguenti:

- **Nome utente amministratore** è un account utente con autorizzazioni di amministratore di dominio e un membro del ruolo predefinito del server sysadmin SQL Server in entrambe le istanze di SQL Server. Per questa esercitazione utilizzare **DomainAdmin**.

- **La password** contiene la password per l'account di amministratore di dominio. Utilizzare una password complessa. Confermare la password.

- **Sottoscrizione** è l'abbonamento da Azure verrà distinta per l'esecuzione di tutte le risorse distribuite per il gruppo di disponibilità. È possibile specificare una sottoscrizione diversa se l'account ha più abbonamenti.

- **Gruppo di risorse** è il nome per il gruppo tutte le risorse Azure creati in questa esercitazione apparterrà a. Per questa esercitazione utilizzare **SQL-HA-RG**. Per ulteriori informazioni, vedere (panoramica di gestione di risorse Azure) [- gruppo-overview.md / # risorsa-gruppi di risorse].

- **Posizione** è l'area Azure in cui le risorse per questa esercitazione verranno create. Selezionare un'area di ospitare l'infrastruttura di Azure.

Di seguito è aspetto e il **Nozioni di base** :

![Nozioni di base](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Fare clic su **OK**.

### <a name="domain-and-network-settings"></a>Impostazioni di dominio e di rete

Questo modello di raccolta Azure crea un nuovo dominio con nuovi controller di dominio. Inoltre, crea una nuova rete e due subnet. Il modello non è abilitato creazione dei server in un dominio esistente o virtuali. Il passaggio successivo consiste nel configurare le impostazioni di dominio e di rete.

Impostazioni di **dominio e rete** blade esaminare i valori predefiniti per le impostazioni di dominio e di rete:

- **Nome del dominio radice foresta** è il nome di dominio che verrà utilizzato per il dominio che ospita i cluster. Per l'esercitazione utilizzare **contoso.com**.

- **Nome di rete virtuale** è il nome di rete per la rete virtuale Azure. Per questa esercitazione utilizzare **autohaVNET**.

- **Nome subnet Controller di dominio** è il nome di una parte della rete virtuale che ospita controller di dominio. Per questa esercitazione utilizzare **subnet-1**. Subnet utilizzerà indirizzo prefisso **10.0.0.0/24**.

- **SQL Server subnet nome** è il nome di una parte della rete virtuale che ospita SQL Server e il file di condividere riferimento. Per questa esercitazione utilizzare **subnet 2**. Subnet utilizzerà indirizzo prefisso **10.0.1.0/26**.

Per ulteriori informazioni sulle reti virtuali in [Azure, vedere Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md).  

Le **impostazioni del dominio e rete** dovrebbe risultare analoga alla seguente:

![Impostazioni di dominio e di rete](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessario, è possibile modificare questi valori. Per questa esercitazione si usare i valori predefiniti.

- Rivedere le impostazioni e fare clic su **OK**.

###<a name="availability-group-settings"></a>impostazioni dei gruppi disponibilità

In **impostazioni dei gruppi disponibilità** esaminare i valori predefiniti per il gruppo di disponibilità e comunicare ascoltatore.

- **Nome del gruppo di disponibilità** è il nome di risorsa cluster per il gruppo di disponibilità. Per questa esercitazione utilizzare **ag Contoso**.

- **disponibilità gruppo comunicare ascoltatore nome** viene utilizzato il cluster e il servizio di bilanciamento del carico interno. Client che si connettono a SQL Server possono utilizzare questo nome per la connessione a replica appropriato del database. Per questa esercitazione utilizzare **comunicare ascoltatore Contoso**.

-  **porta di attesa gruppo disponibilità** specifica che utilizzerà la porta TCP comunicare ascoltatore SQL Server. Per questa esercitazione utilizzare la porta predefinita **1433**.

Se necessario, è possibile modificare questi valori. Usare i valori predefiniti per questa esercitazione.  

![impostazioni dei gruppi disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Fare clic su **OK**.

###<a name="vm-size-storage-settings"></a>Dimensioni macchine Virtuali, le impostazioni di archiviazione

**Dimensione memoria virtuale** , le impostazioni di archiviazione scegliere una dimensione di macchina virtuale di SQL Server e rivedere le altre impostazioni.

- **Le dimensioni del computer virtuale di SQL Server** è la dimensione di Azure macchina virtuale per i server SQL. Scegliere una dimensione macchina virtuale appropriata per il carico di lavoro. Se si creano **DS2**questo ambiente per l'esercitazione. Per carichi di lavoro di produzione scegliere una dimensione macchina virtuale in grado di supportare il carico di lavoro. Molti carichi di lavoro di produzione richiederà **DS4** pollici. Il modello verrà creare due macchine virtuali di questa dimensione e installare SQL Server in ognuna di esse. Per ulteriori informazioni, vedere [le dimensioni per macchine virtuali](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure verrà installato SQL Server Enterprise Edition. Il costo dipende l'edizione e le dimensioni di macchina virtuale. Per informazioni dettagliate sui costi correnti, vedere [macchine virtuali di prezzi](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Dimensioni macchina virtuale controller di dominio** è la dimensione di macchina virtuale per i controller di dominio. Per questa esercitazione utilizzare **D2**.

- **Riferimento di condividere file macchina virtuale dimensioni** è la dimensione di macchina virtuale per il controllo di condivisione file. Per questa esercitazione utilizzare **A1**.

- **Account di archiviazione SQL** è il nome dell'account di archiviazione per contenere i dati di SQL Server e dischi del sistema operativo. Per questa esercitazione utilizzare **alwaysonsql01**.

- **Account di archiviazione controller di dominio** è il nome dell'account di archiviazione per i controller di dominio. Per questa esercitazione utilizzare **alwaysondc01**.

- **Dati di SQL Server disco dimensioni** TB è la dimensione del disco di dati di SQL Server in TB. Specificare un numero compreso tra 1 e 4. Questa è la dimensione del disco dati collegati a ciascuna SQL Server. Per questa esercitazione utilizzare **1**.

- **Ottimizzazione dell'archiviazione** imposta le impostazioni di configurazione specifiche dello spazio di archiviazione per le macchine virtuali di SQL Server in base al tipo di carico di lavoro. Tutti i server di SQL in questo scenario utilizzare l'archiviazione premium con Azure disco host cache impostata su sola lettura. Inoltre, è possibile ottimizzare le impostazioni di SQL Server per il carico di lavoro scegliendo uno di questi tre impostazioni:

    - **Carico di lavoro generale** non imposta impostazioni di configurazione specifiche

    - **Elaborazione transazione** imposta flag di traccia 1117 e 1118

    - **Data warehouse** imposta il flag di traccia 1117 e 610

Per questa esercitazione utilizzare **Generale carico di lavoro**.

![Impostazioni di archiviazione di dimensioni macchine Virtuali](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Rivedere le impostazioni e fare clic su **OK**.

####<a name="a-note-about-storage"></a>Una nota sull'archiviazione

Ulteriori ottimizzazioni dipendono dalle dimensioni dei dischi di dati di SQL Server. Per ogni terabyte del disco dati Azure aggiunge un 1 TB premium spazio di archiviazione aggiuntivo (SSD). Quando un server richiede 2 TB o più, il modello consente di creare un pool di archiviazione in ogni SQL Server. Un pool di archiviazione è una forma di virtualizzazione di spazio di archiviazione in più dischi configurati in modo da fornire costi più elevati capacità, adattabilità e le prestazioni.  Il modello crea uno spazio di archiviazione in pool di archiviazione e si presenta come un singolo dati al sistema operativo. Il modello indica il disco del disco di dati di SQL Server. Il modello Ottimizza di saranno molto limitati pool di archiviazione per SQL Server con le impostazioni seguenti:

- Dimensioni strisce sono l'impostazione interleave per il disco virtuale. Per transazione carichi di lavoro si è impostata su 64 KB. Per data warehouse carichi di lavoro l'impostazione è 256 KB.

- Adattabilità è semplice (senza adattabilità).

>[AZURE.NOTE] Lo spazio di archiviazione di Azure premium localmente ridondante e continua tre copie dei dati in una singola regione, in modo che non è necessaria un'ulteriore verifica al pool di archiviazione.

- Numero di colonne è pari al numero di dischi nel pool di archiviazione.

Per ulteriori informazioni sull'archiviazione pool spazio e lo spazio di archiviazione vedere:

- [Panoramica di spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx).

- [Windows Server Backup e pool di archiviazione](http://technet.microsoft.com/library/dn390929.aspx)

Per ulteriori informazioni sulle procedure consigliate per la configurazione di SQL Server, vedere [prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Impostazioni di SQL Server

Impostazioni di **SQL Server** verificare e modificare il prefisso del nome macchine Virtuali di SQL Server, versione di SQL Server, account del servizio SQL Server e la password e automatico SQL patch pianificazione della manutenzione.

- **Prefisso di SQL Server nome** viene utilizzato per creare un nome per ogni SQL Server. Per questa esercitazione utilizzare **ag Contoso**. I nomi di SQL Server saranno *Contoso-ag-0* e *Contoso-ag-1*.

- **Versione di SQL Server** è la versione di SQL Server. Per questa esercitazione usare **SQL Server 2014**. È anche possibile scegliere **SQL Server 2012** o **SQL Server 2016**.

- **Nome utente dell'account del servizio di SQL Server** è il nome di account di dominio per il servizio SQL Server. Per questa esercitazione utilizzare **sqlservice**.

- **La password** contiene la password dell'account del servizio SQL Server.  Utilizzare una password complessa. Confermare la password.

- **Pianificazione della manutenzione SQL automatico patch** identifica il giorno della settimana che Azure automaticamente patch per SQL Server. Per questa esercitazione digitare **domenica**.

- Quando l'aggiornamento automatico, verrà avviata **l'Aggiornamento automatico SQL manutenzione ora di inizio** è l'ora del giorno per l'area geografica Azure.

>[AZURE.NOTE]Finestra di gestione delle patch per ogni macchina virtuale è sfalsata di un'ora. Solo una macchina virtuale è corretto alla volta, per evitare interruzioni dei servizi.

![Impostazioni di SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Rivedere le impostazioni e fare clic su **OK**.

###<a name="summary"></a>Riepilogo

Nella pagina di riepilogo Azure consente di verificare le impostazioni. È anche possibile scaricare il modello. Controllare il riepilogo. Fare clic su **OK**.

###<a name="buy"></a>Acquista

Questo blade finale contiene **termini di utilizzo**e **informativa sulla privacy**. Esaminare le informazioni. Quando si è pronti per Azure iniziare a creare macchine virtuali e tutte le altre risorse richieste per il gruppo di disponibilità, fare clic su **Crea**.

Portale di Azure creerà il gruppo di risorse e tutte le risorse.

##<a name="monitor-deployment"></a>Distribuzione di monitor

Controllare lo stato di distribuzione dal portale di Azure. Un'icona che rappresenta la distribuzione viene automaticamente aggiunta al dashboard di portale Azure.

![Dashboard di Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Connettersi a SQL Server

Eseguono le nuove istanze di SQL Server in macchine virtuali che non dispongono di connessioni a internet. Tuttavia, i controller di dominio dispone di una connessione a internet. Per connettersi a SQL Server con desktop remoto, RDP prima a uno dei controller di dominio. Aprire un secondo RDP a SQL Server controller di dominio.

Per RDP al controller di dominio primario, procedere come segue:

1.  Dal dashboard del portale Azure molto che ha avuto esito positivo della distribuzione.

1.  Fare clic su **risorse**.

1.  In e il **risorse** , fare clic su **Data Center di principale Active Directory** è il nome della macchina virtuale per controller di dominio primario.

1.  Scegliere **Connetti**e il per **Active Directory-principale-controller di dominio** . Il browser verrà chiesto se si desidera aprire o salvare l'oggetto di connessione remota. Fare clic su **Apri**.
![Connettersi a controller di dominio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Connessione desktop remoto** potrebbe segnalare che non è possibile identificare il server di pubblicazione della connessione remota. Fare clic su **Connetti**.

1.  Sicurezza di Windows viene richiesto di immettere le credenziali per connettersi all'indirizzo IP del controller di dominio primario. Fare clic su **Usa un altro account**. Nome **utente** digitare **contoso\DomainAdmin**. Si tratta del conto che scelto per nome utente amministratore. Usare la password complessa scelto quando si configura il modello.

1.  **Desktop remoto** possono avvisare che il computer remoto non può essere autenticato a causa di problemi con il proprio certificato di sicurezza. Verrà visualizzato è il nome del certificato di sicurezza. Se è stata eseguita l'esercitazione il nome sarà **dc.contoso.com di principale Active Directory**. Fare clic su **Sì**.

A questo punto si è connessi a controller di dominio primario. Per RDP a SQL Server, procedere come segue:

1.  Sul controller di dominio, aprire **Connessione Desktop remoto**.

1.  Per **Computer**, digitare il nome di uno dei server SQL. Per questa esercitazione, digitare **0 di SQL Server**.

1.  Usare l'account utente stesso e la password utilizzati per RDP al controller di dominio.

Si è connessi a questo punto RDP a SQL Server. È possibile aprire SQL Server management studio, connettersi all'istanza predefinita di SQL Server e verificare che il gruppo availabilty viene configurato.


