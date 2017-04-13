<properties
   pageTitle="Esecuzione macchine virtuali di Windows per un'architettura di livelli | Fare riferimento a architettura | Microsoft Azure"
   description="Come implementare un'architettura multilivello su Azure, pagamento particolare attenzione alle disponibilità, sicurezza, scalabilità e gestibilità sicurezza."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>Esecuzione macchine virtuali di Windows per un'architettura di livelli in Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Esecuzione macchine virtuali Linux per un'architettura di livelli in Azure](guidance-compute-n-tier-vm-linux.md)
- [Esecuzione macchine virtuali di Windows per un'architettura di livelli in Azure](guidance-compute-n-tier-vm.md)

Questo articolo illustra un insieme di procedure per l'esecuzione di Windows macchine () per un'applicazione con un'architettura a più livelli. Si basa sull'articolo [esecuzione di più macchine virtuali in Azure][multi-vm].

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. In questo articolo utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Esistono varianti di architetture a più livelli. Per la maggior parte delle differenze non ha importanza ai fini di questi suggerimenti. In questo articolo si presuppone un'app web di livello 3 tipico:

- **Livello di Web.** Gestisce le richieste in arrivo HTTP. Le risposte vengono restituite tramite questo livello.

- **Livello aziendale.** Processi aziendali implementa e altre funzionalità logica per il sistema.

- **Livello di database.** Consente di archiviazione dei dati permanenti, utilizzo di [SQL Server sempre nella disponibilità gruppi] [ sql-alwayson] disponibilità elevata.

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "elaborazione - su più pagine di livello (Windows).

![[0]][0]

- **Imposta la disponibilità.** Creare un [Set di disponibilità] [ azure-availability-sets] per ogni livello ed effettuare il provisioning di almeno due macchine virtuali di ogni livello. Questo approccio è richiesta per raggiungere il [contratto di servizio] disponibilità[ vm-sla] per macchine virtuali.

- **Subnet.** Creare una subnet distinta per ogni livello. Specificare la maschera di intervallo e subnet indirizzo utilizzando la notazione [CIDR] . 

- **Caricare bilanciamento del carico.** Utilizzare un [servizio di bilanciamento del carico esposto a Internet] [ load-balancer-external] per distribuire il traffico Internet in arrivo per il livello web e un [servizio di bilanciamento del carico interno] [ load-balancer-internal] per distribuire il traffico di rete dal livello web a livello aziendale.

- **Jumpbox**. Un _jumpbox_, detto anche [host di protezione], è una macchina virtuale sulla rete agli amministratori di connettersi alle altre macchine virtuali. Il jumpbox ha un NSG che consente il traffico remoto solo da indirizzi IP pubblici whitelisted. Il NSG devono consentire il traffico desktop remoto (RDP).

- **Monitoraggio**. Monitoraggio software, ad esempio [Nagios], [Zabbix]o [Icinga] concedono approfondita tempi di risposta, macchine Virtuali tempi di attività e l'integrità del sistema. Installare il software di monitoraggio in una macchina viene inserita in una subnet gestione separata.

- **NSGs**. Utilizzare i [gruppi di sicurezza di rete] [ nsg] (NSGs) per limitare il traffico di rete all'interno di VNet. Ad esempio, architettura di livello 3 illustrato di seguito, il livello di database non accetta il traffico dal web front end, solo dal livello aziendale e subnet gestione.

- **Sempre nel gruppo di disponibilità di SQL Server.** Fornisce disponibilità al livello dati mediante l'attivazione della replica e failover.

- **Server (AD DS) servizi di dominio active Directory**. Servizi di dominio Active Directory (AD DS) vengono archiviati i dati di directory e si gestiscono le comunicazioni tra gli utenti e domini, inclusi i processi di accesso, l'autenticazione e le ricerche nella directory. Un controller di dominio Active Directory è un server che esegue Active Directory. Prima di Windows Server 2016 sempre in gruppi disponibilità deve essere associati a un dominio. In questo modo disponibilità gruppi dipendono dalla tecnologia Windows Server Failover Cluster (WSFC). Windows Server 2016 introdotta la possibilità di creare un Cluster di Failover senza Active Directory. Per ulteriori informazioni, vedere [Novità di Failover cluster in Windows Server 2016][wsfc-whats-new]

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento seguono queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="vnet--subnets"></a>VNet / subnet

Quando si crea la VNet, allocazione di spazio sufficiente indirizzo per subnet che sarà necessario. Specificare la VNet indirizzo intervallo e subnet maschera utilizzando la notazione [CIDR] . Utilizzare uno spazio indirizzo che si trova all'interno di standard [blocchi di indirizzi IP privati][private-ip-space], che sono 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Scegliere un intervallo di indirizzi che si sovrappongono alla rete aziendale in locale, nel caso in cui è necessario configurare un gateway tra la VNet e la rete locale in un secondo momento. Dopo aver creato il VNet, non è possibile modificare l'intervallo di indirizzi.

Progettare subnet ai requisiti di funzionalità e protezione in considerazione. Tutte le macchine virtuali all'interno della stessa livello o ruolo devono essere inseriti nella stessa subnet, che può essere un limite di protezione. Per ulteriori informazioni sulla progettazione VNets e subnet, vedere [pianificare e progettare reti virtuali Azure][plan-network].

Per ogni subnet, specificare lo spazio di indirizzi della subnet in notazione CIDR. Ad esempio, '10.0.0.0/24' Crea un intervallo di 256 indirizzi IP. (Macchine virtuali utilizzabili 251 di questi piani, cinque sono riservati. Vedere [virtuali domande frequenti su][vnet faq].) Assicurarsi che gli intervalli di indirizzi non si sovrappongono tra subnet.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Usare le regole NSG per limitare il traffico tra livelli. Architettura di livello 3 sopra, ad esempio, il livello di web non comunica direttamente con il livello di database. Per applicare questa operazione, il livello di database deve bloccare il traffico in arrivo dalla subnet livello web.  

  1. Creare un NSG e associarla alla subnet livello database.

  2. Aggiungere una regola che nega tutto il traffico dalla VNet. (Usare il `VIRTUAL_NETWORK` tag della regola.) 

  3. Aggiungere una regola con priorità alta che consente il traffico in ingresso dalla subnet a livello aziendale. Questa regola esegue l'override regola precedente e consente il livello di business comunicare con il livello di database.

  4. Aggiungere una regola che consenta il traffico in ingresso all'interno della subnet livello database stesso. Questa regola consente la comunicazione tra macchine virtuali nel livello del database, è necessario per la replica di database e failover.

  5. Aggiungere una regola che consenta il traffico RDP dalla subnet jumpbox. Questa regola consente agli amministratori di connettersi a livello del database dalla jumpbox.

  > [AZURE.NOTE] Un NSG con [regole predefinite] [ nsg-rules] che consentono di qualsiasi traffico in ingresso da all'interno di VNet. Queste regole non potranno essere eliminate, ma è possibile eseguirne mediante la creazione di regole con priorità più alta.

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

Servizio di bilanciamento del carico esterno distribuisce il traffico Internet al livello web. Creare un indirizzo IP pubblico per il servizio di bilanciamento del carico. Vedere [creazione di un servizio di bilanciamento del carico esposto a Internet][lb-external-create].

Servizio di bilanciamento del carico interno distribuisce il traffico di rete dal livello web a livello aziendale. Per fornire il servizio di bilanciamento del carico un indirizzo IP privato, creare una configurazione IP front-end e associarla alla subnet per il livello di business. Visualizzare [informazioni introduttive sulla creazione di un servizio di bilanciamento del carico interno][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>SQL Server sempre in gruppi di disponibilità

È consigliabile [Sempre in gruppi disponibilità] [ sql-alwayson] disponibilità elevata SQL Server. Sempre in gruppi disponibilità richiedono un controller di dominio. Tutti i nodi di gruppo della disponibilità devono essere nello stesso dominio di Active Directory.

Altri livelli connettersi al database tramite un [comunicare ascoltatore gruppo disponibilità][sql-alwayson-listeners]. Comunicare ascoltatore consente ai client SQL per la connessione senza conoscere il nome dell'istanza fisico di SQL Server. Macchine virtuali che accesso al database deve essere aggiunto al dominio. Il client (in questo caso, un altro livello) utilizza DNS per risolvere il nome di rete virtuale di comunicare ascoltatore negli indirizzi IP.

Configurare SQL Server sempre come indicato di seguito:

- Creare un cluster di Windows Server Failover cluster (WSFC) e un gruppo di disponibilità SQL Server sempre attiva. Per ulteriori informazioni, vedere [Introduzione a sempre in gruppi disponibilità][sql-alwayson-getting-started].

- Creare un sistema di bilanciamento del carico interno con un indirizzo IP statico privato.

- Creare un comunicare ascoltatore gruppo disponibilità e mappare nome DNS di comunicare ascoltatore all'indirizzo IP di un servizio di bilanciamento del carico interno. 

- Creare una regola di bilanciamento del carico per di SQL Server in attesa (porta TCP 1433 per impostazione predefinita). La regola di bilanciamento del carico necessario abilitare _IP mobile_, detto anche diretto Server restituito. In questo modo la macchina virtuale rispondere direttamente al client, che consente una connessione diretta a replica primaria.

    > [AZURE.NOTE] Quando è abilitata la mobile IP, il numero di porta front-end deve essere uguale al numero di porta back-end della regola di bilanciamento del carico.

Quando un client SQL tentativo di connessione, bilanciamento del carico indirizza la richiesta di connessione per la replica principale corrente. Se esiste un caso di errore a un'altra replica, bilanciamento del carico indirizza automaticamente le successive richieste per la nuova replica principale. Per ulteriori informazioni, vedere [configurare Bilanciamento del carico per SQL sempre in][sql-alwayson-ilb].

Durante il caso di errore, vengono chiuse le connessioni esistenti. Dopo il failover, verranno reindirizzate nuove connessioni per la nuova replica principale.

Se l'app rende molte più legge di scrittura, è possibile trasferire una parte delle query di sola lettura per una replica secondaria. Vedere [utilizzando un comunicare ascoltatore a cui connettersi secondario sola lettura Replica (sola lettura Routing)][sql-alwayson-read-only-routing].

Testare la distribuzione per [caso di errore manuale][sql-alwayson-force-failover].

### <a name="jumpbox"></a>Jumpbox

Non consentono l'accesso RDP da Internet pubblica in macchine virtuali che eseguono il carico di lavoro dell'applicazione. Se, tuttavia, tutti gli accessi RDP/SSH a queste macchine virtuali devono provenire tramite il jumpbox. Un amministratore accede alla jumpbox e quindi Registra in altre macchine Virtuali dalla jumpbox. Il jumpbox consente il traffico RDP da Internet, ma solo da note, ma gli indirizzi IP whitelisted.

Posizionare il jumpbox in VNet stesso come le altre macchine virtuali, ma in una subnet gestione separata.

Creare un [indirizzo IP pubblico] per la jumpbox.

Utilizzare una dimensione macchine Virtuali piccola per jumpbox, ad esempio A1 Standard.

Configurare NSGs per il livello web, business e subnet livello database per consentire il traffico (RDP) amministrazione passare dalla subnet gestione.

Per proteggere la jumpbox, creare un NSG e applicarlo alla subnet jumpbox. Aggiungere una regola NSG che consente connessioni RDP solo da un set di whitelisted di indirizzi IP.

La NSG da collegare alla subnet o per jumpbox di rete. In questo caso, è consigliabile allegarlo alla scheda, in modo che il traffico RDP è consentito solo jumpbox, anche se si aggiungono altri macchine virtuali alla stessa subnet.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Inserire ogni livello o ruolo macchine Virtuali in un set di disponibilità separata. Non inserire macchine virtuali da differenti livelli nello stesso set di disponibilità. 

Al livello del database con più macchine virtuali non viene convertito automaticamente in un database di disponibilità. In genere per un database relazionale, è necessario utilizzare replica e failover per ottenere la disponibilità elevata. Per SQL Server, è consigliabile usare [Sempre in gruppi disponibilità][sql-alwayson]. 

Se è necessario maggiore disponibilità di [Azure contratto di servizio per macchine virtuali] [ vm-sla] fornisce, replicare l'applicazione su due aree e usare gestore del traffico Azure per failover. Per ulteriori informazioni, vedere [Macchine virtuali di Windows in esecuzione in più aree geografiche per disponibilità][multi-dc].   

## <a name="security-considerations"></a>Considerazioni sulla protezione

Crittografare dati inattivi. Utilizzare [Azure chiave archivio] [ azure-key-vault] per gestire le chiavi di crittografia di database. Archivio chiave consentono di memorizzare chiavi di crittografia nei moduli di sicurezza hardware (HSM). Per ulteriori informazioni, vedere [Configurare l'integrazione di archivio di Azure chiave per SQL Server in macchine virtuali di Azure] [ sql-keyvault] è anche consigliabile per memorizzare informazioni riservate applicazione, ad esempio stringhe di connessione di database, nell'archivio di chiave.

È possibile aggiungere un dispositivo virtuale di rete (NVA) per creare una rete Perimetrale tra Internet pubblica e la rete virtuale Azure. NVA è un termine generico per un dispositivo virtuale che può eseguire attività correlate alla rete aziendale, ad esempio firewall, ispezione pacchetti, il controllo, instradamento personalizzato o molte altre operazioni. Per ulteriori informazioni, vedere [implementazione di una rete Perimetrale tra Azure e Internet][dmz].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Servizi di bilanciamento del carico distribuire il traffico di rete per i livelli web e business. Ridimensionare in senso orizzontale mediante l'aggiunta di nuove istanze di macchine Virtuali. Si noti che è possibile ridimensionare i livelli web e business in modo indipendente, in base al caricamento. Per ridurre possibili problemi causati dalla necessità di mantenere affinità client, macchine virtuali nel livello web devono essere senza informazioni sullo stato. Macchine virtuali che ospita la regola business devono essere senza informazioni sullo stato.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Semplificare la gestione dell'intero sistema tramite gli strumenti di amministrazione centralizzata, ad esempio [L'automazione Azure][azure-administration], [Microsoft operazioni Management Suite][operations-management-suite], [Chef][chef], o [Marionetta][puppet]. Questi strumenti è possono consolidare le informazioni di diagnostica e l'integrità acquisite da più macchine virtuali per fornire una panoramica del sistema.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni è disponibile in [Github][github-folder]. L'architettura di riferimento include un livello web, il livello aziendale, un livello di dati, nonché controller di dominio Active Directory e macchine Virtuali di jumpbox.

È possibile distribuire l'architettura di riferimento seguendo le indicazioni seguenti: 

1. Fare clic sul pulsante.  
[! ["Distribuire Azure"] [1]][2]

2. Una volta aperto il collegamento nel portale di Azure, immettere i seguenti valori: 
  - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-ntier-sql-network-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

3. Selezionare Azure notifica portale per un messaggio di distribuzione è stata completata.

4. Fare clic sul pulsante.  
[! ["Distribuire Azure"] [1]][3]

5. Una volta aperto il collegamento nel portale di Azure, immettere i seguenti valori: 
  - Il nome del **gruppo di risorse** è già definito nel file di parametri, pertanto selezionare **Usa esistente** e immettere `ra-ntier-sql-workload-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

6. Selezionare Azure notifica portale per un messaggio di distribuzione è stata completata.

7. Fare clic sul pulsante.  
[! ["Distribuire Azure"] [1]][4]

8. Una volta aperto il collegamento nel portale di Azure, immettere i seguenti valori: 
  - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-ntier-sql-network-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

9. Selezionare Azure notifica portale per un messaggio di distribuzione è stata completata.

10. I file di parametro includono un hardcoded nomi utente di amministratore e password e si consiglia di immediatamente modificare entrambi su tutte le macchine virtuali. Fare clic su ogni macchina virtuale nel portale di Azure, quindi fare clic su **Reimposta password** e il **supporto + risoluzione dei problemi** . Selezionare **reimpostare la password** nella casella a discesa **modalità** , quindi selezionare un nuovo **nome utente** e **Password**. Fare clic sul pulsante **Aggiorna** per mantenere il nuovo nome utente e la password. 

Per informazioni su altri modi per distribuire l'architettura di riferimento, vedere il file Leggimi in [macchine virtuali di singola indicazioni] [ github-folder] Github cartella. 

## <a name="next-steps"></a>Passaggi successivi

Per ottenere disponibilità per tale architettura, è consigliabile [distribuire per più paesi][multi-dc].

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[host di protezione]: https://en.wikipedia.org/wiki/Bastion_host
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[indirizzo IP pubblico]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "Architettura a più livelli utilizzando Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json