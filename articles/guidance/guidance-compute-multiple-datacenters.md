<properties
   pageTitle="Macchine virtuali di Windows in esecuzione in più aree geografiche per disponibilità | Fare riferimento a architettura | Microsoft Azure"
   description="Informazioni su come distribuire macchine virtuali in più aree in Azure per disponibilità e adattabilità."
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
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>Macchine virtuali di Windows in esecuzione in più aree di disponibilità

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Esecuzione di macchine virtuali Linux in più aree di disponibilità](guidance-compute-multiple-datacenters-linux.md)
- [Macchine virtuali di Windows in esecuzione in più aree di disponibilità](guidance-compute-multiple-datacenters.md)

In questo articolo, è consigliabile un insieme di procedure consigliate per l'esecuzione di Windows macchine () in più aree di Azure, per ottenere la disponibilità e un'infrastruttura di ripristino di emergenza efficaci.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource groups] e classica. In questo articolo utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Un'architettura più aree può fornire maggiore disponibilità della distribuzione a una singola regione. Se l'area principale viene applicata a un'interruzione internazionali, è possibile utilizzare [Il traffico Manager] [ traffic-manager] venga reindirizzato all'area secondario. Questa architettura consente anche se si verifica un errore di un singolo sottosistema dell'applicazione. 

Vi sono diversi approcci per ottenere una disponibilità elevata fra centri dati:

- Attiva/passiva con standby attivo. Traffico in un'area, mentre altri attesa in standby. Macchine virtuali nell'area secondario sono allocato e in esecuzione in qualsiasi momento.

- Attiva/passiva con standby fredda. Lo stesso, ma macchine virtuali nell'area secondario non vengono allocare fino a quando non necessarie per failover. Questo approccio costa meno per l'esecuzione, ma in genere avrà più in basso di tempo in caso di errore.

- Attivo/attivo. Entrambe aree geografiche sono attive e le richieste sono il bilanciamento del carico tra di esse. Se non è disponibile un centro dati, viene accettata dalla rotazione. 

Questa architettura è incentrata su attivo/passivo con standby attivo, con il traffico Manager per il failover. Si noti che è possibile distribuire un numero limitato di macchine virtuali per standby attivo e quindi scalabilità in base alle esigenze.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente si basa all'architettura illustrata nella [affidabilità aggiunta a un'architettura a più livelli in Azure](guidance-compute-n-tier-vm.md).

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "elaborazione - su più pagine area (Windows).

[! [0]][0]

- **Aree principali e secondarie**. Questa architettura due aree vengono utilizzate per raggiungere una maggiore disponibilità. Una è l'area principale. Durante il normale funzionamento, il traffico di rete è indirizzato all'area principale. Ma se che non è disponibile, il traffico viene instradato all'area secondario.

- ** [Gestore del traffico azure] [ traffic-manager] ** indirizza le richieste in arrivo per l'area principale. Se tale area è disponibile, il traffico Manager ha esito negativo all'area secondario. Per ulteriori informazioni, vedere la sezione [Configurazione il traffico Manager](#configuring-traffic-manager).

- **Gruppi di risorse**. Creare diversi [gruppi di risorse] [ resource groups] per l'area principale, secondaria regione e per il traffico Manager. In questo modo della flessibilità necessaria per gestire ogni regione come un'unica raccolta di risorse. Ad esempio, è Impossibile ridistribuire una regione, senza chiudere l'altro valore. [Collegare i gruppi di risorse][resource-group-links], in modo che è possibile eseguire una query per elencare tutte le risorse per l'applicazione.

- **VNets**. Creare un VNet distinta per ogni area. Assicurarsi che gli spazi indirizzo non si sovrappongono. 

- **SQL Server sempre sul gruppo di disponibilità**. Se si utilizza SQL Server, è consigliabile [Sempre nella Availabilty gruppi SQL] [ sql-always-on] disponibilità elevata. Creare un gruppo di disponibilità singola che include le istanze di SQL Server in entrambe le aree geografiche. Per ulteriori informazioni, vedere la sezione [la configurazione del gruppo di disponibilità SQL Server sempre attiva](#configuring-the-sql-server-alwayson-availability-group ).

> [AZURE.NOTE] Valutare anche la possibilità di [Database SQL Azure][azure-sql-db], che fornisce un database relazionale come un servizio cloud. Database SQL, non è necessario configurare un gruppo di disponibilità o gestire failover.  

- **Gateway VPN**: creare un [gateway VPN] [ vpn-gateway] in ogni VNet e configurare una [connessione VNet-VNet][vnet-to-vnet], per consentire il traffico di rete tra due VNets. Questo è necessario per il gruppo di disponibilità SQL sempre attiva.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento seguono queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="regional-pairing"></a>Associazione internazionali

Ogni area Azure è associata a un'altra area geography stesso. In generale, scegliere aree la stessa coppia internazionali (ad esempio, USA orientale 2 e centrale Contattaci). Vantaggi dell'operazione:

- Se c'è un'ampia interruzione, ripristino di almeno un'area fuori ciascuna coppia ha la priorità.

- Aggiornamenti di sistemi di Azure pianificate sono distribuiti alle aree accoppiate in sequenza, per ridurre al minimo il tempo di inattività possibili.

- Coppie di parole risiedono geography stesso, per soddisfare requisiti di residenza dei dati.

Tuttavia, assicurarsi che entrambe aree geografiche supportano tutti i servizi Azure necessari per l'applicazione (vedere [servizi per area geografica][services-by-region]). Per ulteriori informazioni sulle coppie internazionali, vedere [Business continuità e ripristino di emergenza (BCDR): aree accoppiati Azure][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configurazione di traffico Manager

Tenere presente quanto segue quando si configura il traffico manager per lo scenario:

- **Routing.** Gestore del traffico supporta diversi [algoritmi di routing][tm-routing]. Per lo scenario descritto in questo articolo, utilizzare _la priorità_ routing (noto in precedenza come _failover_ routing). Con questa impostazione, il traffico Manager invia tutte le richieste all'area principale, a meno che l'area principale assume la forma non è raggiungibile. A questo punto, automaticamente ha esito negativo all'area secondario. Vedere [configurare Failover routing metodo][tm-configure-failover].

- **Verifica dell'integrità.** Gestione del traffico utilizza un HTTP (o HTTPS) [verifica] [ tm-monitoring] per controllare la disponibilità di ogni area. La ricerca verifica la presenza di una risposta HTTP 200 per un percorso URL specificato. Ottimale, creare un endpoint che segnala l'integrità generale dell'applicazione e utilizzare l'endpoint per la verifica dell'integrità. In caso contrario, il sondaggio potrebbe segnalare un endpoint "Integro" errore critiche parti dell'applicazione sono effettivamente. Per ulteriori informazioni, vedere [Integrità Endpoint monitoraggio motivo][health-endpoint-monitoring-pattern].   

Quando il traffico Manager ha esito negativo su, non esiste un periodo di tempo quando i client non riesce a raggiungere l'applicazione, che può essere diversi minuti. Due fattori la durata totale:

- Verifica dell'integrità deve rilevare che il centro dati principale è diventato non è raggiungibile.

- I server DNS è necessario aggiornare i record DNS memorizzati nella cache per l'indirizzo IP, che dipende il DNS time to live (TTL). Il valore TTL predefinito è 300 secondi (5 minuti), ma è possibile configurare questo valore quando si crea il profilo di gestore del traffico.

Per informazioni dettagliate, vedere [Su Monitoraggio gestore del traffico][tm-monitoring]. 

Se il traffico Manager non riesce sopra, è consigliabile eseguire un failback manuale, anziché automaticamente non funziona indietro. Verificare che tutti i sottosistemi applicazione siano corretti prima di tutto. In caso contrario, è possibile creare una situazione in cui l'applicazione capovolge avanti e indietro tra data center.

Per impostazione predefinita, il traffico Manager viene eseguito automaticamente il nuovo. Per evitare questo problema, ridurre manualmente la priorità dell'area principale dopo un evento di failover. Si supponga ad esempio, l'area principale è priorità 1 e secondaria priorità 2. Dopo aver caso di errore, impostare l'area principale a livello di priorità 3, per evitare che questa opzione. Quando si è pronti tornare alla modalità, aggiornare la priorità a 1.

Comando CLI Azure Aggiorna la priorità:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

È possibile evitare Flip-flop disattivare temporaneamente il punto finale:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```

A seconda della causa del caso di errore, potrebbe essere necessario redploy le risorse in un'area. Prima dell'errore indietro, eseguire un test di conformità operativi. Il test deve verificare operazioni come:

- Macchine virtuali sono configurate correttamente. (Tutto il software necessario è installato, IIS è in esecuzione e così via).

- Applicazione sottosistemi sono corretti. 

- Funzionale. (Ad esempio, il livello di database è raggiungibile dal livello web).

### <a name="sql-server-always-on-configuration"></a>SQL Server sempre nella configurazione

SQL Server sempre in gruppi di disponibilità richiedono un controller di dominio. Tutti i nodi di gruppo della disponibilità devono essere nello stesso dominio di Active Directory. I seguenti punti di forniscono indicazioni relative alle come configurare un gruppo di disponibilità SQL Server sempre in:

- Almeno, posizionare due controller di dominio in ogni area. 

- Assegnare un indirizzo IP statico ogni controller di dominio.

- Creare una connessione VNet-VNet per attivare le comunicazioni tra il VNets.

- Per ogni VNet, aggiungere gli indirizzi IP dei controller di dominio (da entrambe aree geografiche) per l'elenco dei server DNS. È possibile usare il comando CLI seguente. Per ulteriori informazioni, vedere [server di gestione DNS utilizzati da una rete virtuale (VNet)][vnet-dns].

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- Creare un [Cluster di Windows Server Failover] [ wsfc] cluster (WSFC) che include le istanze di SQL Server in entrambe le aree geografiche. 

- Creare un gruppo SQL Server sempre sulla disponibilità che include le istanze di SQL Server in entrambe le aree principali e secondarie. Per i passaggi descritti, vedere [Estensione sempre nel gruppo di disponibilità al Data Center di Azure remoto (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) . 

- Inserire la replica primaria nell'area principale.

- Inserire una o più repliche secondarie nell'area principale. Configurare questi per usare l'icona del commit con failover automatico.

- Inserire una o più repliche secondarie nell'area secondario. Configurare questi per usare commit *asincrono* , per motivi di prestazioni. (In caso contrario, tutte le transazioni SQL necessario aspettare che in un round trip in rete all'area secondaria.) 

> [AZURE.NOTE] Repliche commit asincrono non cedere il controllo automatico. 

Per ulteriori informazioni, vedere [Esecuzione macchine virtuali di Windows per un'architettura di livelli in Azure](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Con un'app livelli complessa, potrebbe non necessario replicare l'intera applicazione nell'area secondario. Se, tuttavia, si potrebbe replicare solo un sottosistema critico necessari per supportare la continuità aziendale.

Gestore del traffico è un punto di errore possibili nel sistema. Se il servizio non riesce, i client non è possibile accedere all'applicazione durante il periodo di inattività. Rivedere il [Contratto di servizio di gestione di traffico][tm-sla]e determinare se con il traffico Manager solo soddisfa esigenze aziendali per la disponibilità elevata. In caso contrario, è possibile aggiungere un'altra soluzione di gestione di traffico un failback. Se il servizio di gestione di traffico Azure non riesce, modificare i record CNAME in DNS in modo che puntino a di altro servizio di gestione di traffico. (Questa operazione deve essere eseguita manualmente, e l'applicazione non sarà disponibile fino a quando non vengono distribuite le modifiche DNS) 

Per il cluster di SQL Server, sono disponibili due scenari di failover da considerare:

1. Non è Nessuna replica SQL nell'area principale. Ad esempio, questa potrebbe avvenire durante un periodo di inattività internazionali. In questo caso, è necessario manualmente esito negativo sopra il gruppo di disponibilità SQL, anche se il traffico Manager automaticamente ha esito negativo nel front-end. Seguire i passaggi descritti in [eseguire forzata manuale caso di errore di un gruppo di disponibilità di SQL Server](https://msdn.microsoft.com/library/ff877957.aspx), che viene descritto come eseguire forzata caso di errore con SQL Server Management Studio, Transact-SQL o PowerShell in SQL Server 2016. 

    > [AZURE.WARNING] Con failover forzato, non c'è il rischio di perdita di dati. Dopo l'area principale è in linea, creare uno snapshot del database e utilizzare [tablediff] per trovare le differenze.

2. Gestore del traffico ha esito negativo all'area secondario, ma la replica SQL principale è ancora disponibile. Ad esempio, il livello front-end può avere esito negativo, senza influire sulle macchine virtuali SQL. In questo caso, il traffico Internet viene inviato all'area secondario e tale area può effettuare la connessione alla replica di SQL principale. Tuttavia, si verificherà incremento di latenza, perché le connessioni SQL vengono inseriti in aree geografiche. In questo caso, è necessario eseguire manuale caso di errore come indicato di seguito: 

    - Passare temporaneamente una replica SQL nell'area secondaria a *icona del* commit. In questo modo non possono essere la perdita di dati durante il failover.

    - Non su tale replica SQL. 
    
    - Quando non si riesce tornare all'area principale, ripristinare l'impostazione commit asincrono. 

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Quando si aggiorna la distribuzione, aggiornare una regione alla volta, per ridurre il rischio di un errore globale da una configurazione non corretta o un errore nell'applicazione.

Testare la flessibilità del sistema di errori. Ecco alcuni scenari di errore comuni per verificare:

- Chiudere le istanze di macchine Virtuali.

- Risorse di pressione, ad esempio CPU e memoria.

- Rete disconnettere/ritardo.

- Un arresto anomalo processi.

- Scadenza dei certificati.

- Consente di simulare errori hardware.

- Arrestare il servizio DNS sul controller di dominio.

Misurare i tempi di ripristino e verificare che soddisfano le esigenze aziendali. Verificare combinazioni di gestione degli errori, nonché.

## <a name="next-steps"></a>Passaggi successivi

Questa serie è basato su distribuzioni cloud pure. Scenari aziendali richiedono spesso una rete ibrido connette una rete locale con una rete virtuale Azure. Per informazioni su come creare una rete di distribuzione ibrida, vedere [implementazione di un'architettura di rete ibrido con Azure e VPN locale][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc.png "Architettura di rete altamente disponibile per le applicazioni di Azure a più livelli"