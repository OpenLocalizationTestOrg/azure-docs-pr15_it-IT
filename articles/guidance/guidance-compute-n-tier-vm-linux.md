<properties
   pageTitle="Esecuzione macchine virtuali Linux per un'architettura di livelli in Azure | Microsoft Azure"
   description="Come eseguire macchine virtuali Linux per un'architettura di livelli in Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
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

# <a name="running-linux-vms-for-an-n-tier-architecture-on-azure"></a>Esecuzione macchine virtuali Linux per un'architettura di livelli in Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]


> [AZURE.SELECTOR]
- [Esecuzione macchine virtuali Linux per un'architettura di livelli in Azure](guidance-compute-n-tier-vm-linux.md)
- [Esecuzione macchine virtuali di Windows per un'architettura di livelli in Azure](guidance-compute-n-tier-vm.md)

Questo articolo illustra un insieme di procedure per l'esecuzione di Linux macchine () per un'applicazione con un'architettura a più livelli. Si basa sull'articolo [esecuzione di più macchine virtuali in Azure][multi-vm].

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. In questo articolo utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Esistono varianti di architetture a più livelli. Per la maggior parte delle differenze non ha importanza ai fini di questi suggerimenti. In questo articolo si presuppone un'app web di livello 3 tipico:

- **Livello di Web.** Gestisce le richieste in arrivo HTTP. Le risposte vengono restituite tramite questo livello.

- **Livello aziendale.** Processi aziendali implementa e altre funzionalità logica per il sistema.

- **Livello di database.** Consente l'archiviazione di dati permanenti, utilizzando Cassandra Apache disponibilità elevata.

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "elaborazione - su più pagine di livello (Linux).

![[0]][0]

- **Imposta la disponibilità.** Creare un [Set di disponibilità] [ azure-availability-sets] per ogni livello ed effettuare il provisioning di almeno due macchine virtuali di ogni livello. Questo approccio è richiesta per raggiungere il [contratto di servizio] disponibilità[ vm-sla] per macchine virtuali.

- **Subnet.** Creare una subnet distinta per ogni livello. Specificare la maschera di intervallo e subnet indirizzo utilizzando la notazione [CIDR] . 

- **Caricare bilanciamento del carico.** Utilizzare un [servizio di bilanciamento del carico esposto a Internet] [ load-balancer-external] per distribuire il traffico Internet in arrivo per il livello web e un [servizio di bilanciamento del carico interno] [ load-balancer-internal] per distribuire il traffico di rete dal livello web a livello aziendale.

- **Jumpbox**. Un _jumpbox_, detto anche [host di protezione], è una macchina virtuale sulla rete agli amministratori di connettersi alle altre macchine virtuali. Il jumpbox ha un NSG che consente il traffico remoto solo da indirizzi IP pubblici whitelisted. Il NSG devono consentire il traffico protetto shell (SSH).

- **Monitoraggio**. Monitoraggio software, ad esempio [Nagios], [Zabbix]o [Icinga] concedono approfondita tempi di risposta, macchine Virtuali tempi di attività e l'integrità del sistema. Installare il software di monitoraggio in una macchina viene inserita in una subnet gestione separata.

- **NSGs**. Utilizzare i [gruppi di sicurezza di rete] [ nsg] (NSGs) per limitare il traffico di rete all'interno di VNet. Ad esempio, architettura di livello 3 illustrato di seguito, il livello di database non accetta il traffico dal web front end, solo dal livello aziendale e subnet gestione.

- **Database Cassandra Apache**. Fornisce disponibilità al livello dati mediante l'attivazione della replica e failover.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento seguono queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="vnet--subnets"></a>VNet / subnet

Quando si crea la VNet, allocazione di spazio sufficiente indirizzo per subnet che sarà necessario. Specificare la VNet indirizzo intervallo e subnet maschera utilizzando la notazione [CIDR] . Utilizzare uno spazio indirizzo che si trova all'interno di standard [blocchi di indirizzi IP privati][private-ip-space], che sono 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Scegliere un intervallo di indirizzi che si sovrappongono alla rete aziendale in locale, nel caso in cui è necessario configurare un gateway tra la VNet e la rete locale in un secondo momento. Dopo aver creato il VNet, non è possibile modificare l'intervallo di indirizzi.

Progettare subnet ai requisiti di funzionalità e protezione in considerazione. Tutte le macchine virtuali all'interno della stessa livello o ruolo devono essere inseriti nella stessa subnet, che può essere un limite di protezione. Per ulteriori informazioni sulla progettazione VNets e subnet, vedere [pianificare e progettare reti virtuali Azure][plan-network].

Per ogni subnet, specificare lo spazio di indirizzi della subnet in notazione CIDR. Ad esempio, '10.0.0.0/24' Crea un intervallo di 256 indirizzi IP. (Macchine virtuali utilizzabili 251 di questi piani, cinque sono riservati. Vedere [virtuali domande frequenti su][vnet faq].) Assicurarsi che gli intervalli di indirizzi non si sovrappongono tra subnet.

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

Servizio di bilanciamento del carico esterno distribuisce il traffico Internet al livello web. Creare un indirizzo IP pubblico per il servizio di bilanciamento del carico. Vedere [creazione di un servizio di bilanciamento del carico esposto a Internet][lb-external-create].

Servizio di bilanciamento del carico interno distribuisce il traffico di rete dal livello web a livello aziendale. Per fornire il servizio di bilanciamento del carico un indirizzo IP privato, creare una configurazione IP front-end e associarla alla subnet per il livello di business. Visualizzare [informazioni introduttive sulla creazione di un servizio di bilanciamento del carico interno][lb-internal-create].

### <a name="cassandra"></a>Cassandra

È consigliabile [DataStax Enterprise] [ datastax] per produzione utilizzo, ma queste raccomandazioni applicano a qualsiasi edizione Cassandra. Per ulteriori informazioni sull'esecuzione DataStax in Azure, vedere [Guida alla distribuzione aziendale di DataStax per Azure][cassandra-in-azure]. 

Inserire le macchine virtuali per un cluster Cassandra in un set di disponibilità, per garantire che repliche Cassandra distribuite in più domini guasto ed eseguire l'aggiornamento di domini. Per ulteriori informazioni sui domini di errore e l'aggiornamento, vedere [gestire la disponibilità di macchine virtuali][availability-sets-manage]. 

Configurare domini di errore 3 (il valore massimo) per set di disponibilità. 

Configurare 18 aggiornamento domini per il set di disponibilità. Si ottiene così il numero massimo di domini aggiornamento che può comunque essere distribuiti uniformemente domini guasto.   

Configurare i nodi in modalità rack importanti. Eseguire il mapping domini guasto a rack la `cassandra-rackdc.properties` file.

Non è necessario un bilanciamento del carico che precede il cluster. Il client si connette direttamente a un nodo del cluster.

### <a name="jumpbox"></a>Jumpbox

Posizionare il jumpbox in VNet stesso come le altre macchine virtuali, ma in una subnet gestione separata.

Creare un [indirizzo IP pubblico] per la jumpbox.

Utilizzare una dimensione macchine Virtuali piccola per jumpbox, ad esempio A1 Standard.

Configurare NSGs per il livello web, business e subnet livello database per consentire il traffico (SSH) amministrazione passare dalla subnet gestione.

Per proteggere la jumpbox, creare un NSG e applicarlo alla subnet jumpbox. Aggiungere una regola NSG che consente connessioni SSH solo da un set di whitelisted di indirizzi IP pubblici.

La NSG da collegare alla subnet o per jumpbox di rete. In questo caso, è consigliabile allegarlo alla scheda, in modo che il traffico SSH è consentito solo jumpbox, anche se si aggiungono altri macchine virtuali alla stessa subnet.

Non consentono l'accesso SSH da Internet pubblica in macchine virtuali che eseguono il carico di lavoro dell'applicazione. Se, tuttavia, tutti gli accessi SSH a queste macchine virtuali devono provenire tramite il jumpbox. Un amministratore accede alla jumpbox e quindi Registra in altre macchine Virtuali dalla jumpbox. Il jumpbox consente il traffico SSH da Internet, ma solo da note, ma gli indirizzi IP whitelisted.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Inserire ogni livello o ruolo macchine Virtuali in un set di disponibilità separata. Non inserire macchine virtuali da differenti livelli nello stesso set di disponibilità. 

Al livello del database con più macchine virtuali non viene convertito automaticamente in un database di disponibilità. In genere per un database relazionale, è necessario utilizzare replica e failover per ottenere la disponibilità elevata.  

Se è necessario maggiore disponibilità di [Azure contratto di servizio per macchine virtuali] [ vm-sla] fornisce, replicare l'applicazione su due aree e usare gestore del traffico Azure per failover. Per ulteriori informazioni, vedere [Esecuzione macchine virtuali Linux in più aree geografiche per disponibilità][multi-dc].  

## <a name="security-considerations"></a>Considerazioni sulla protezione

Usare le regole NSG per limitare il traffico tra livelli. Architettura di livello 3 sopra, ad esempio, il livello di web non comunica direttamente con il livello di database. Per applicare questa operazione, il livello di database deve bloccare il traffico in arrivo dalla subnet livello web.  

  1. Creare un NSG e associarla alla subnet livello database.

  2. Aggiungere una regola che nega tutto il traffico dalla VNet. (Usare il `VIRTUAL_NETWORK` tag della regola.) 

  3. Aggiungere una regola con priorità alta che consente il traffico in ingresso dalla subnet a livello aziendale. Questa regola esegue l'override regola precedente e consente il livello di business comunicare con il livello di database.

  4. Aggiungere una regola che consenta il traffico in ingresso all'interno della subnet livello database stesso. Questa regola consente la comunicazione tra macchine virtuali nel livello del database, è necessario per la replica di database e failover.

  5. Aggiungere una regola che consenta il traffico SSH dalla subnet jumpbox. Questa regola consente agli amministratori di connettersi a livello del database dalla jumpbox.

  > [AZURE.NOTE] Un NSG con [regole predefinite] [ nsg-rules] che consentono di qualsiasi traffico in ingresso da all'interno di VNet. Queste regole non potranno essere eliminate, ma è possibile eseguirne mediante la creazione di regole con priorità più alta.

È possibile aggiungere un dispositivo virtuale di rete (NVA) per creare una rete Perimetrale tra Internet pubblica e la rete virtuale Azure. NVA è un termine generico per un dispositivo virtuale che può eseguire attività correlate alla rete aziendale, ad esempio firewall, ispezione pacchetti, il controllo, instradamento personalizzato o molte altre operazioni. Per ulteriori informazioni, vedere [implementazione di una rete Perimetrale tra Azure e Internet][dmz].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Servizi di bilanciamento del carico distribuire il traffico di rete per i livelli web e business. Ridimensionare in senso orizzontale mediante l'aggiunta di nuove istanze di macchine Virtuali. Si noti che è possibile ridimensionare i livelli web e business in modo indipendente, in base al caricamento. Per ridurre possibili problemi causati dalla necessità di mantenere affinità client, macchine virtuali nel livello web devono essere senza informazioni sullo stato. Macchine virtuali che ospita la regola business devono essere senza informazioni sullo stato.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Semplificare la gestione dell'intero sistema tramite gli strumenti di amministrazione centralizzata, ad esempio [L'automazione Azure][azure-administration], [Microsoft operazioni Management Suite][operations-management-suite], [Chef][chef], o [Marionetta][puppet]. Questi strumenti è possono consolidare le informazioni di diagnostica e l'integrità acquisite da più macchine virtuali per fornire una panoramica del sistema.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni è disponibile in [Github][github-folder]. L'architettura di riferimento include un livello web, il livello aziendale e un livello di dati.

1. Fare clic sul pulsante.  
[! ["Distribuire Azure"] [1]][2]

2. Una volta aperto il collegamento nel portale di Azure, immettere i seguenti valori: 
  - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-ntier-sql-network-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

3. Selezionare Azure notifica portale per un messaggio di distribuzione è stata completata.

4. I file di parametro includono un hardcoded nomi utente di amministratore e password e si consiglia di immediatamente modificare entrambi su tutte le macchine virtuali. Fare clic su ogni macchina virtuale nel portale di Azure, quindi fare clic su **Reimposta password** e il **supporto + risoluzione dei problemi** . Selezionare **reimpostare la password** nella casella a discesa **modalità** , quindi selezionare un nuovo **nome utente** e **Password**. Fare clic sul pulsante **Aggiorna** per mantenere il nuovo nome utente e la password.

## <a name="next-steps"></a>Passaggi successivi

Per ottenere disponibilità per tale architettura, è consigliabile [distribuire per più paesi][multi-dc].

<!-- links -->

[azure-administration]: ../automation/automation-intro.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[availability-sets-manage]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[host di protezione]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-consistency-usage]: http://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters-linux.md
[multi-vm]: guidance-compute-multi-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[indirizzo IP pubblico]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./media/blueprints/compute-n-tier-linux.png "Architettura a più livelli utilizzando Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier%2Fazuredeploy.json


