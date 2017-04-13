<properties
    pageTitle="SQL (PaaS) e SQL Server di Database nel cloud in macchine virtuali (IaaS) | Microsoft Azure"
    description="Informazioni su quale opzione di SQL Server cloud rientri applicazione: Database SQL Azure (PaaS) o SQL Server nel cloud in macchine virtuali di Azure."
    services="sql-database, virtual-machines"
    keywords="Cloud di SQL Server, SQL Server nel cloud, database PaaS, cloud SQL Server, DBaaS"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Scegliere un'opzione di SQL Server cloud: Database SQL Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)

Azure disponibili due opzioni per l'hosting carichi di lavoro di SQL Server in Microsoft Azure:

* [Database SQL Azure](https://azure.microsoft.com/services/sql-database/): database A SQL nativi nel cloud, noto anche come una piattaforma come un database del servizio (PaaS) o in un database come servizio (DBaaS) ottimizzate per lo sviluppo di app di software come servizio (SaaS). Offre la compatibilità con la maggior parte delle caratteristiche di SQL Server. Per ulteriori informazioni su PaaS, vedere [Novità PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installato ed è ospitato nel cloud in Windows Server macchine () in esecuzione in Azure, noto anche come un'infrastruttura come servizio (IaaS).
SQL Server in macchine virtuali di Azure ottimizzata per la migrazione della applicazioni di SQL Server esistenti. Tutte le versioni e le edizioni di SQL Server sono disponibili. Offre la compatibilità di 100% con SQL Server, consentendo di ospitare i database quante transazioni tra database necessari e in esecuzione. Offre controllo completo su SQL Server e Windows.

Informazioni su inquadrare ogni opzione all'interno della piattaforma di dati di Microsoft e ottenere assistenza corrispondenti all'opzione più adatta alle esigenze. Se è la priorità risparmi sui costi o amministrazione minimo prima di tutto il resto, in questo articolo consentono di stabilire l'approccio offre rispetto ai requisiti di business che interessano di più.


## <a name="microsofts-data-platform"></a>Dati la piattaforma Microsoft

Uno dei primi aspetti da comprendere qualsiasi discussione di Azure e database di SQL Server locale è che è possibile utilizzare tutto. Dati la piattaforma Microsoft utilizza grazie alla tecnologia di SQL Server e rende disponibili in più computer locale fisica, ambienti cloud privati, ambienti cloud privati ospitata di terze parti e cloud pubblico. SQL Server Azure mchines virtuale consente di soddisfare le esigenze aziendali univoci e diversi tramite una combinazione di locali e cloud ospitato distribuzioni durante l'uso dello stesso set di prodotti server, strumenti di sviluppo e conoscenze in questi ambienti.

   ![Opzioni di SQL Server cloud: database di SQL server in IaaS o SaaS SQL nel cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Come illustrato nella figura, è possibile caratterizzata ogni offerta dal livello di amministrazione che si dispone di infrastruttura (sull'asse X) e dal grado di efficienza dei costi realizzato dal consolidamento del livello di database e automazione (sull'asse Y).

Durante la progettazione di un'applicazione di quattro opzioni di base sono disponibili per l'hosting la parte di SQL Server dell'applicazione:

- SQL Server in macchine fisiche non virtualizzato
- SQL Server in macchine locale virtualizzati (cloud privato)
- SQL Server Azure Virtual Machine (cloud pubblico Microsoft)
- Database SQL Azure (cloud pubblica Microsoft)

Nelle sezioni seguenti verranno informazioni sulla SQL Server nel cloud pubblico Microsoft: Database di SQL Azure e SQL Server in macchine virtuali di Azure. Inoltre, è esplorare vantaggi aziendali comuni per determinare quale opzione adatta per l'applicazione.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Osservare il Database di SQL Azure e SQL Server in macchine virtuali di Azure

**Database SQL Azure** è un relazionale database-come-a-service (DBaaS) ospitato nel cloud Azure compreso nelle categorie di settore di *Software-come-a-servizio* e *Piattaforma-come-a-Service (PaaS)*. [Database SQL](sql-database-technical-overview.md) si basa su standard hardware e software di proprietà, ospitato e gestito da Microsoft. Database SQL, è possibile sviluppare direttamente nel servizio funzionalità e funzionalità incorporate. Quando si usano Database SQL, si in con le opzioni per ridurre verso l'alto o indietro per maggiore potenza senza interruzioni.

**SQL Server in macchine virtuali di Azure (macchine virtuali)** rientra nella categoria di settore *Infrastruttura-come-a-Service (IaaS)* e consente di eseguire SQL Server una macchina virtuale nel cloud. Simile al Database SQL, si basa sull'hardware standard di proprietà, ospitato e gestito da Microsoft. Quando si usano SQL Server in una macchina virtuale, è possibile uno pagare-come si-go per una licenza di SQL Server già incluso in un'immagine di SQL Server o della semplice utilizzare una licenza esistente. È anche possibile scala-crescenti/decrescenti e sospendere o riprendere la macchina virtuale in base alle esigenze.

In generale, queste due opzioni SQL sono ottimizzate per diversi scopi:

- **Database SQL di** ottimizzata per ridurre i costi complessivi per il valore minimo per il provisioning e la gestione di molti database. Riduce i costi di amministrazione in corso poiché non è necessario gestire eventuali software di database o del sistema operativo, macchine virtuali. Non è necessario gestire gli aggiornamenti, disponibilità o [backup](sql-database-automated-backups.md). In generale, Database SQL Azure possono aumentare il numero di database gestita da un unico IT o delle risorse di sviluppo.
- **Esecuzione di SQL Server in macchine virtuali di Azure** ottimizzata per la migrazione di applicazioni esistenti in Azure o estendere le applicazioni locale esistenti nel cloud nelle distribuzioni ibride. Inoltre, è possibile usare SQL Server in un computer virtuale per sviluppare e testare le applicazioni di SQL Server tradizionali. Con SQL Server in macchine virtuali di Azure, si dispone dei diritti amministrativi completi su un'istanza di SQL Server dedicata e una SV basato sul cloud. È una soluzione ideale quando un'organizzazione ha già disponibile per la gestione di macchine virtuali di risorse IT. Queste funzionalità consentono di creare un sistema altamente personalizzato per soddisfare alle prestazioni e i requisiti di disponibilità dell'applicazione.

Nella tabella seguente vengono riepilogate le principali caratteristiche di Database SQL e SQL Server in macchine virtuali di Azure:

|       | Database SQL | SQL Server in una macchina virtuale Azure|
| -------------- | ------------ | ---------------------- |
| **Procedure per:** | Nuove Design cloud applicazioni che esistono vincoli dispongono tempo di sviluppo e marketing. |Applicazioni esistenti che richiedono la migrazione rapida nel cloud con modifiche minime. Un rapido sviluppo test scenari di utilizzo e quando non si desidera acquistare hardware di SQL Server locale non di produzione. |
|| Team che richiedono disponibilità predefinite, il ripristino di emergenza e aggiornamento per il database. |Gruppi che possono configurare e gestire disponibilità, il ripristino di emergenza e patch per SQL Server. Alcune fornite funzionalità automatiche semplificare notevolmente l'operazione. |
||Team che si desidera gestire il sistema operativo e le impostazioni di configurazione.| Se è necessario un ambiente personalizzato con diritti amministrativi completi.|
||Database di 1 TB, o più grandi database che possono essere [orizzontalmente o verticalmente suddiviso](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) utilizzando un modello di scalabilità.|Istanze di SQL Server fino a 64 TB di spazio di archiviazione. L'istanza supporta tutti i database in base alle esigenze. |
||[Le applicazioni di creazione Software-come-a-servizio](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Eseguire la migrazione e la creazione di applicazioni enterprise e ibrida.|
|||||
|**Risorse:**|Non si desidera utilizzare le risorse per la configurazione e la gestione dell'infrastruttura IT, ma desidera focalizzare l'attenzione sul livello di applicazione.|Si dispongono di alcune risorse utili all'IT per la configurazione e gestione. Alcune fornite funzionalità automatiche semplificare notevolmente l'operazione.|
|**Costo totale di proprietà:**|Elimina i costi di hardware e ridurre i costi amministrativi.|Elimina i costi hardware.|
|**Continuità aziendale:**|Oltre alle funzionalità di infrastruttura tolleranza di errore predefiniti, Database SQL Azure offre caratteristiche, ad esempio [backup automatici](sql-database-automated-backups.md), [Ripristinare In un momento](sql-database-recovery-using-backups.md#point-in-time-restore), [Geografico ripristino](sql-database-recovery-using-backups.md#geo-restore)e [Replica di geografico Active](sql-database-geo-replication-overview.md) per aumentare la continuità aziendale. Per ulteriori informazioni, vedere [Panoramica di continuità aziendale di Database SQL](sql-database-business-continuity.md).|SQL Server in macchine virtuali di Azure consente di configurare una soluzione di ripristino di alta disponibilità e per esigenze specifiche del database. Di conseguenza, è possibile installare un sistema altamente ottimizzate per l'applicazione. È possibile testare ed eseguire failover personalmente quando necessario. Per ulteriori informazioni, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Cloud ibrida:**|L'applicazione locale può accedere ai dati nel Database di SQL Azure.|Con SQL Server in macchine virtuali di Azure, è possibile connettere le applicazioni eseguite parzialmente nel cloud e in parte locale. Ad esempio, è possibile estendere la rete locale e il dominio di Active Directory nel cloud tramite [Virtuali Azure](../virtual-network/virtual-networks-overview.md). Inoltre, è possibile memorizzare i file di dati in locale in archiviazione Azure con [File di dati di SQL Server in Azure](http://msdn.microsoft.com/library/dn385720.aspx). Per ulteriori informazioni, vedere [Introduzione al Cloud ibrida di SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx).|
||Supporta [la replica transazione di SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) come sottoscrittore replicare i dati.|Supporta [la replica transazione di SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Disponibilità AlwaysOn](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), servizi di integrazione e la distribuzione dei Log per replicare i dati. Inoltre, tradizionale backup di SQL Server sono completamente supportate|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivazioni business per la scelta di Database SQL Azure o SQL Server in macchine virtuali di Azure

### <a name="cost"></a>Costo

Se si è un avvio strapped per cassa o un team in una società Fondazione opera in base ai vincoli budget limitato, fondi limitato è spesso driver principale per decidere come ospitare i database. In questa sezione illustra la fatturazione e licensing nozioni fondamentali in Azure in queste due opzioni di database relazionale: Database SQL e SQL Server in macchine virtuali di Azure. Anche informazioni sul calcolo del costo totale dell'applicazione.

#### <a name="billing-and-licensing-basics"></a>Fatturazione e gestione delle licenze nozioni di base

**Database SQL** viene venduto ai clienti un servizio, non con una licenza.  [SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) viene venduto con una licenza di inclusa che si paga al minuto. Se si dispone di una licenza esistente, è possibile utilizzare il.  

Attualmente, **Database SQL** è disponibile in diversi livelli di servizio, ognuno dei quali vengono addebitate ogni ora un tasso fisso in base a livello di servizio e si sceglie di prestazioni. Inoltre, vengono addebitate per il traffico Internet in uscita in normali [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/). I livelli di servizio Basic, Standard e Premium sono progettati per offrire prestazioni prevedibili con più livelli di prestazioni in modo che corrispondano requisiti punta dell'applicazione. È possibile modificare tra i livelli di servizio e livelli di prestazioni in modo che corrispondano alle esigenze di velocità variabili dell'applicazione. Se il database ha elevate transazione ed esigenze aziendali per supportare molti utenti contemporaneamente, è consigliabile il livello di servizio Premium. Per informazioni aggiornate sulla corrente livelli di servizio supportati, vedere [Livelli di servizio di Database SQL Azure](sql-database-service-tiers.md). È anche possibile creare [pool di database flessibile](sql-database-elastic-pool.md) per condividere le risorse di prestazioni tra le istanze di database.

**Database SQL**, il software di database è configurato, patch e aggiornato da Microsoft, in modo da ridurre i costi di amministrazione automaticamente. Inoltre, le relative funzionalità di [backup incorporato](sql-database-automated-backups.md) consentono di ottenere significativi risparmi, soprattutto se si dispone di un numero elevato di database.

Con **SQL Server in macchine virtuali di Azure**, è possibile utilizzare una delle immagini di SQL Server forniti piattaforma (che include una licenza) o visualizzare la licenza di SQL Server. Tutte le versioni di SQL Server supportate (2008 R2, 2016, 2014 2012) e le edizioni (sviluppo, Express, Web, Standard, Enterprise) sono disponibili. Inoltre, le versioni porta Your-proprietari-licenza (BYOL) le immagini sono disponibili. Quando tramite il Azure fornito immagini, i costi operativi dipendono dal dimensione memoria virtuale e l'edizione di SQL Server scelto. Indipendentemente dalle dimensioni macchine Virtuali o edizione di SQL Server, pagando costi di licenza di al minuto di SQL Server e Windows Server, oltre il costo di archiviazione Azure per dischi macchine Virtuali. L'opzione di fatturazione al minuto consente di utilizzare SQL Server per come è necessario senza acquisto aggiunta SQL Server licenze. Se si anticipa la propria licenza di SQL Server in Azure, addebitate per Windows Server e solo i costi di archiviazione. Per ulteriori informazioni sulla porta-your-proprietari licenza, vedere [Mobilità licenza tramite Software Assurance su Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Il calcolo del costo totale dell'applicazione

Quando si inizia con una piattaforma cloud, il costo di eseguire l'applicazione include i costi di sviluppo e amministrazione, nonché i costi di assistenza piattaforma cloud pubblico.

Ecco il calcolo del costo dettagliate per l'applicazione in esecuzione nel Database di SQL e SQL Server in macchine virtuali di Azure:

**Quando si utilizza il Database di SQL Azure:**

*Costi di applicazione = costi di amministrazione altamente ridotto a icona + costi di sviluppo software i costi del servizio di Database SQL*

**Quando si usa SQL Server in macchine virtuali di Azure:**

*Costi di applicazione = costo di sviluppo software altamente ridotto a icona i costi di amministrazione i costi di gestione delle licenze di SQL Server e Windows Server + costi di archiviazione Azure*

Per ulteriori informazioni sui prezzi, vedere le risorse seguenti:

- [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prezzi macchina virtuale](https://azure.microsoft.com/pricing/details/virtual-machines/) per [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure prezzi calcolatrice](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Esiste un sottoinsieme di funzionalità in SQL Server che non sono applicabili o non è disponibile con Database di SQL. Per ulteriori informazioni, vedere [limitazioni generale di Database SQL e linee guida](sql-database-general-limitations.md) e [informazioni di Database di SQL in Transact-SQL](sql-database-transact-sql-information.md) . Se si sposta una soluzione di SQL Server esistente nel cloud, vedere [migrazione di un database di SQL Server di Database SQL Azure](sql-database-cloud-migrate.md). Quando si esegue la migrazione di un'applicazione di SQL Server locale esistente al Database SQL, valutare la possibilità di aggiornamento dell'applicazione in modo da sfruttare le funzionalità che offrono servizi cloud. Ad esempio, provare a utilizzo [Del servizio App Web di Azure](https://azure.microsoft.com/services/app-service/web/) o [Servizi Cloud Windows Azure](https://azure.microsoft.com/services/cloud-services/) per ospitare il livello di applicazione per l'aumento dei vantaggi di costo.

### <a name="administration"></a>Amministrazione

Per molte aziende, la decisione di transizione a un servizio cloud è molto sulla possibilità di scaricare complessità dell'amministrazione, poiché viene costo. **Database SQL di**Microsoft Amministra hardware sottostante. Microsoft automaticamente replica tutti i dati per fornire alta disponibilità, configura e aggiornamento del software del database, gestisce il bilanciamento del carico e non failover trasparente se esiste un errore del server. È possibile continuare a gestire il database, ma non è più necessario gestire il motore di database, sistema operativo del server o hardware.  Esempi di elementi che è possibile continuare a gestire includono database e gli account di accesso, indice e ottimizzazione delle query e il controllo e sicurezza.

Con **SQL Server in macchine virtuali di Azure**, è necessario il controllo completo del sistema operativo e configurazione di istanza di SQL Server. Con una macchina virtuale dipende da di stabilire quando aggiornamento o aggiornare il sistema operativo e il software di database e installare software aggiuntivo, ad esempio anti-virus. Alcune funzionalità automatiche sono disponibili per semplificare notevolmente la disponibilità delle patch, backup e alta. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e la configurazione archiviazione. Azure consente di modificare le dimensioni di una macchina virtuale in base alle esigenze. Per informazioni, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure](../virtual-machines/virtual-machines-linux-sizes.md). 

### <a name="service-level-agreement-sla"></a>Contratto di servizio (contratto di servizio)

Per i reparti IT numerosi, i tempi obblighi di un livello di contratto di servizio di riunione è una priorità. In questa sezione, si osserva il contratto di servizio applicato a ogni opzione di hosting del database.

Per i livelli di servizio di **Database SQL di** base, Standard e Premium Microsoft offre una contratto di servizio del 99,99% di disponibilità. Per informazioni aggiornate, vedere [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/sql-database/). Per informazioni aggiornate sulla livelli del servizio di Database SQL e i piani di continuità aziendale supportati, vedere [Livelli di servizio](sql-database-service-tiers.md).

Per l' **esecuzione di SQL Server in macchine virtuali di Azure**, Microsoft offre una disponibilità contratto di servizio di 99,95% che copra solo la macchina virtuale. Questo contratto di servizio non comprende i processi (ad esempio SQL Server) in esecuzione nella macchina virtuale e richiede ospitare almeno due istanze di macchine Virtuali in un set di disponibilità. Per informazioni aggiornate, vedere il [Contratto di servizio macchine Virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Per database disponibilità (HA) all'interno di macchine virtuali, è necessario configurare una delle opzioni di disponibilità supportati in SQL Server, ad esempio [Disponibilità AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Utilizzando l'opzione disponibilità supportati non fornisce un contratto di servizio aggiuntivo, ma consente di ottenere > disponibilità del database 99,99%.

### <a name="market"></a>Tempo di mercato

**Database SQL** è la soluzione ideale per applicazioni progettato cloud quando sono critici produttività sviluppo e rapido time to market. Con la funzionalità di amministratore di database come livello di programmazione, è ideale per gli sviluppatori e progettisti cloud come riduce la necessità di gestire il sistema operativo e il database sottostante. Ad esempio, è possibile utilizzare [l'API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) e [I cmdlet di PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) per automatizzare e gestire le operazioni amministrative per migliaia di database. Caratteristiche quali [Flessibile pool Database](sql-database-elastic-pool.md) consentono di focalizzare l'attenzione sul livello di applicazione e fornire la soluzione del mercato più rapidamente.

**Esecuzione di SQL Server in macchine virtuali di Azure** è perfetto se le applicazioni di nuove o esistenti richiedono grandi database correlate o l'accesso a tutte le funzionalità di SQL Server o Windows. È anche una soluzione ideale quando si desidera eseguire la migrazione esistente locale applicazioni e database in Azure come-è. Poiché non è necessario modificare la presentazione, applicazione e livelli di dati, è risparmiare tempo e budget nella rielaborazione soluzione esistente. Se, tuttavia, è possibile concentrarsi sulla migrazione tutte le soluzioni Azure in eseguire alcune ottimizzazione delle prestazioni necessarie per la piattaforma Azure. Per ulteriori informazioni, vedere [Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Riepilogo

In questo articolo sono stati illustrati Database SQL e SQL Server in Azure macchine () e illustrati i vantaggi aziendali comuni che potrebbero interferire con le regole. Di seguito è un riepilogo dei suggerimenti per da considerare:

Scegliere **Database SQL Azure** se:

- Si creano nuove applicazioni basate su cloud in modo da sfruttare risparmi sui costi e ottimizzazione delle prestazioni che i servizi cloud fornire. Questo approccio offre i vantaggi di un servizio cloud completamente gestita consente inferiore iniziale time-to-market e può fornire l'ottimizzazione dei costi a lungo termine.

- Si desidera che Microsoft operazioni di gestione comuni per i database e richiede maggiore disponibilità contratti di servizio per i database.



Scegliere **SQL Server in macchine virtuali Azure** se:

- Si dispone di applicazioni locale che si desidera eseguire la migrazione o estendere nel cloud, o se si desidera creare applicazioni aziendali superiori a 1 TB. Questo approccio vantaggi della compatibilità SQL 100%, capacità di database di grandi dimensioni, il controllo completo su SQL Server e Windows e sicura connettersi locale. Questo approccio riduce i costi di sviluppo e modifiche delle applicazioni esistenti.

- Si dispongono di risorse IT esistenti e infine possono essere proprietari patch, l'esecuzione di backup e disponibilità di database. Si noti che alcune caratteristiche automatizzati semplificano notevolmente queste operazioni. 


## <a name="next-steps"></a>Passaggi successivi
- Vedere [esercitazione di Database SQL: creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md) per iniziare a utilizzare il Database di SQL.
- Vedere [prezzi di Database SQL] (https://azure.microsoft.com/pricing/details/sql-database/).
- Vedere [provisioning una macchina virtuale di SQL Server Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) per iniziare con SQL Server in macchine virtuali di Azure.
- Vedere [SQL Server in un computer virtuale Azure: percorso formativo:](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
