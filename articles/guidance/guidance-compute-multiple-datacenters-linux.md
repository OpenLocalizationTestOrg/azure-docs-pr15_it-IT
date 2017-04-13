<properties
   pageTitle="Esecuzione di macchine virtuali Linux in più aree geografiche per disponibilità | Fare riferimento a architettura | Microsoft Azure"
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

# <a name="running-linux-vms-in-multiple-regions-for-high-availability"></a>Esecuzione di macchine virtuali Linux in più aree di disponibilità

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Esecuzione di macchine virtuali Linux in più aree di disponibilità](guidance-compute-multiple-datacenters-linux.md)
- [Macchine virtuali di Windows in esecuzione in più aree di disponibilità](guidance-compute-multiple-datacenters.md)

In questo articolo, è consigliabile un insieme di procedure consigliate per l'esecuzione Linux macchine () in più aree di Azure, per ottenere la disponibilità e un'infrastruttura di ripristino di emergenza efficaci.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource groups] e classica. In questo articolo utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Un'architettura più aree può fornire maggiore disponibilità della distribuzione a una singola regione. Se l'area principale viene applicata a un'interruzione internazionali, è possibile utilizzare [Il traffico Manager] [ traffic-manager] venga reindirizzato all'area secondario. Questa architettura consente anche se si verifica un errore di un singolo sottosistema dell'applicazione.

Vi sono diversi approcci per ottenere una disponibilità elevata fra centri dati:   
  
- Attiva/passiva con standby attivo. Traffico in un'area, mentre altri attesa in standby. Macchine virtuali nell'area secondario sono allocato e in esecuzione in qualsiasi momento.

- Attiva/passiva con standby fredda. Lo stesso, ma macchine virtuali nell'area secondario non vengono allocare fino a quando non necessarie per failover. Questo approccio costa meno per l'esecuzione, ma in genere avrà più in basso di tempo in caso di errore.

- Attivo/attivo. Entrambe aree geografiche sono attive e le richieste sono il bilanciamento del carico tra di esse. Se non è disponibile un centro dati, viene accettata dalla rotazione.

Questa architettura è incentrata su attivo/passivo con standby attivo, con il traffico Manager per il failover. Si noti che è possibile distribuire un numero limitato di macchine virtuali per standby attivo e quindi scalabilità in base alle esigenze.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente si basa all'architettura illustrata nella [affidabilità aggiunta a un'architettura a più livelli in Azure](guidance-compute-n-tier-vm-linux.md). 

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "elaborazione - su più pagine area (Linux).

![[0]][0]

- **Aree principali e secondarie**. Questa architettura due aree vengono utilizzate per raggiungere una maggiore disponibilità. Una è l'area principale. Durante il normale funzionamento, il traffico di rete è indirizzato all'area principale. Ma se che non è disponibile, il traffico viene instradato all'area secondario.

- ** [Gestore del traffico azure] [ traffic-manager] ** indirizza le richieste in arrivo per l'area principale. Se tale area è disponibile, il traffico Manager ha esito negativo all'area secondario. Per ulteriori informazioni, vedere la sezione [Configurazione il traffico Manager](#configuring-traffic-manager).

- **Gruppi di risorse**. Creare diversi [gruppi di risorse] [ resource groups] per l'area principale, secondaria regione e per il traffico Manager. In questo modo della flessibilità necessaria per gestire ogni regione come un'unica raccolta di risorse. Ad esempio, è Impossibile ridistribuire una regione, senza chiudere l'altro valore. [Collegare i gruppi di risorse][resource-group-links], in modo che è possibile eseguire una query per elencare tutte le risorse per l'applicazione.

- **VNets**. Creare un VNet distinta per ogni area. Assicurarsi che gli spazi indirizzo non si sovrappongono.

- **Cassandra Apache** distribuito nei dati interfacce tra Azure aree geografiche. Centri dati Cassandra vengono distribuiti in diverse aree geografiche Azure disponibilità elevata. All'interno di ogni regione, nodi configurati in modalità presente rack con errore ed eseguire l'aggiornamento dei domini, per adattabilità all'interno dell'area.

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

### <a name="cassandra-deployment-across-multiple-regions"></a>Distribuzione Cassandra su più aree

Centri dati Cassandra sono divisioni di carichi di lavoro: un gruppo di nodi correlati configurati in un cluster di separazione di replica e il carico di lavoro.

È consigliabile [DataStax Enterprise] [ datastax] per produzione per l'uso. Per ulteriori informazioni sull'esecuzione DataStax in Azure, vedere [Guida alla distribuzione aziendale di DataStax per Azure][cassandra-in-azure]. Le seguenti indicazioni generali applicano a qualsiasi edizione Cassandra.

- Assegnare un indirizzo IP pubblico a ogni nodo. In questo modo i cluster di comunicare attraverso aree utilizzando l'infrastruttura di Azure dorsale, offrendo ad alta velocità effettiva basso costo.

- Proteggere i nodi utilizzando il firewall appropriato e le configurazioni NSG, consentendo il traffico solo da e verso host noti, inclusi i client e altri nodi del cluster. Si noti che Cassandra utilizza porte diverse per la comunicazione, OpsCenter, motori e così via. Per l'utilizzo di porta in Cassandra, vedere [configurazione firewall porta accesso][cassandra-ports].

- Utilizzare la crittografia SSL per tutti i [client al nodo] [ ssl-client-node] e [a nodi] [ ssl-node-node] le comunicazioni.

- In un'area, seguire le istruzioni in [consigli Cassandra](guidance-compute-n-tier-vm-linux.md#cassandra-recommendations).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Con un'app livelli complessa, potrebbe non necessario replicare l'intera applicazione nell'area secondario. Se, tuttavia, si potrebbe replicare solo un sottosistema critico necessari per supportare la continuità aziendale.

Gestore del traffico è un punto di errore possibili nel sistema. Se il servizio non riesce, i client non è possibile accedere all'applicazione durante il periodo di inattività. Rivedere il [Contratto di servizio di gestione di traffico][tm-sla]e determinare se con il traffico Manager solo soddisfa esigenze aziendali per la disponibilità elevata. In caso contrario, è possibile aggiungere un'altra soluzione di gestione di traffico un failback. Se il servizio di gestione di traffico Azure non riesce, modificare i record CNAME in DNS in modo che puntino a di altro servizio di gestione di traffico. (Questa operazione deve essere eseguita manualmente, e l'applicazione non sarà disponibile fino a quando non vengono distribuite le modifiche DNS)

Per cluster Cassandra gli scenari di failover da prendere in considerazione dipendono i livelli di coerenza utilizzati mediante l'applicazione, come il numero di repliche utilizzato. Per i livelli la coerenza e l'uso in Cassandra, vedere [la coerenza dei dati di configurazione] [ cassandra-consistency] e [Cassandra: quanti nodi sono parlati a con base?][cassandra-consistency-usage] Disponibilità dei dati in Cassandra dipende dal livello di coerenza utilizzato dall'applicazione e il meccanismo di replica. Per la replica in Cassandra, vedere [Replica di dati in spiegazione database NoSQL][cassandra-replication].

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
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: http://docs.datastax.com/en/latest-dse/datastax_enterprise/sec/secConfFirePort.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
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
[0]: ./media/blueprints/compute-multi-dc-linux.png "Architettura di rete altamente disponibile per le applicazioni di Azure a più livelli"
