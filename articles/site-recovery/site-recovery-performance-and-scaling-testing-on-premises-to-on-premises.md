<properties
    pageTitle="Test delle prestazioni e la scala di risultati per locale per la replica di Hyper-V in locale con il ripristino del sito | Microsoft Azure"
    description="In questo articolo vengono fornite informazioni sulle prestazioni di verifica degli locale per la replica locale mediante il ripristino del sito di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Prestazioni testare e ridimensionare i risultati per locale per la replica di Hyper-V in locale con il ripristino del sito

È possibile utilizzare il ripristino del sito di Microsoft Azure per progettare e gestire la replica di macchine virtuali e server fisici in Azure o a un Data Center secondario. In questo articolo fornisce i risultati della verifica delle prestazioni che abbiamo quando replica macchine virtuali Hyper-V tra due locale Data Center.



## <a name="overview"></a>Panoramica

Lo scopo di testing era per esaminare come il ripristino del sito di Azure esegue durante la replica stabile. Stabile replica si verifica quando macchine virtuali di avere completato la replica iniziale e si stanno sincronizzando le modifiche delta. È importante migliorare le prestazioni utilizzando stabile perché è stato in cui la maggior parte delle macchine virtuali rimangono a meno che non si verificano interruzioni impreviste.


La distribuzione di test è costituita da due siti locale con un server VMM in ogni sito. La distribuzione di test è tipica di una distribuzione di office office/ramo testa con sede funge da sito principale e la filiale come sito secondario o ripristino.

### <a name="what-we-did"></a>È stato fatto

Ecco cosa è del test passare:

1. Creare macchine virtuali di utilizzare i modelli VMM.

1. Iniziata virtuali e metriche delle prestazioni previsto acquisizione oltre 12 ore.

1. Creato nuvole server VMM principale e ripristino.

1. Protezione cloud configurato nel ripristino del sito di Azure, incluso il mapping di aree di origine e ripristino.

1. Attivazione di protezione per macchine virtuali e consentono agli utenti di completare la replica iniziale.

1. Il tempo di attesa un paio di ore per la stabilizzazione di sistema.

1. Acquisire dati sulle prestazioni 12 ore, garantire che tutte le macchine virtuali rimasti in uno stato di replica prevista per tali 12 ore.

1. Misurare delta tra le metriche di prestazioni previsto e le metriche di prestazioni di replica.

## <a name="test-deployment-results"></a>Distribuzione risultati dei test

### <a name="primary-server-performance"></a>Prestazioni del server primario

- Replica Hyper-V in modo asincrono tiene traccia delle modifiche in un file di log con il sovraccarico di archiviazione minimo nel server primario.

- Replica Hyper-V utilizza cache di memoria manutenzione per ridurre il sovraccarico IOPS per tenerne traccia. Archivia scrive VHDX in memoria e li inserisce nel file di log prima dell'ora che il log viene inviato al sito di ripristino. Un disco svuotamento anche in caso di scrittura raggiunto un limite predefinito.

- Il grafico seguente mostra il sovraccarico IOPS stabile per la replica. È possibile vedere che IOPS sovraccarico a causa di replica è circa 5% più basso.

![Risultati principali](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Replica Hyper-V utilizza memoria nel server primario per ottimizzare le prestazioni del disco. Come mostrato nell'immagine seguente, la memoria sovraccarico in tutti i server cluster primario è marginale. Memoria sovraccarico illustrata corrisponde alla percentuale di memoria utilizzata da replica confrontate con quelle di memoria installata nel server Hyper-V.

![Risultati principali](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Replica Hyper-V sono minimo sovraccarico della CPU. Come illustrato nel grafico, il sovraccarico di replica è nell'intervallo di 2-3%.

![Risultati principali](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Prestazioni del server secondario (ripristino)

Replica Hyper-V utilizza una piccola quantità di memoria nel server di ripristino per ottimizzare il numero di operazioni di archiviazione. Il grafico riepiloga l'utilizzo della memoria nel server di ripristino. Memoria sovraccarico illustrata corrisponde alla percentuale di memoria utilizzata da replica confrontate con quelle di memoria installata nel server Hyper-V.

![Risultati secondari](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

La quantità di operazioni i/o nel sito di ripristino è una funzione del numero di operazioni di scrittura nel sito principale. Di seguito esaminare totale delle operazioni di nel sito di ripristino rispetto ad totale delle operazioni di operazioni e scrittura nel sito principale. I grafici indicano che il totale IOPS nel sito di ripristino

- Circa 1,5 volte la scrittura IOPS sul server principale.

- Circa il 37% del totale IOPS nel sito principale.

![Risultati secondari](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Risultati secondari](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Effetto di replica sull'utilizzo della rete

Una media di 275 MB al secondo della larghezza di banda di rete è stata utilizzata tra il nodo principale e ripristino (la compressione) su una larghezza di banda esistente di 5 GB al secondo.

![Utilizzo della rete di risultati](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Effetto di replica sulle prestazioni macchina virtuale

Importante è l'impatto di replica carichi di lavoro di produzione in esecuzione in macchine virtuali. Se il sito principale adeguatamente è stato effettuato il provisioning della replica, sui carichi di lavoro non devono essere qualsiasi impatto. Lightweight della Replica Hyper-V verifica meccanismo assicura che carichi di lavoro in esecuzione in macchine virtuali non sono interessati durante la replica di stato stabile. Come illustrato nei grafici seguenti.

Questo grafico mostra IOPS eseguita da macchine virtuali in esecuzione diversi carichi di lavoro prima e dopo la replica è stata abilitata. È possibile osservare che non vi è alcuna differenza tra i due.

![Risultati di effetto replica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

Nel grafico seguente mostra la velocità di macchine virtuali in esecuzione diversi carichi di lavoro prima e dopo la replica è stata abilitata. È possibile osservare che la replica non ha alcun effetto significativa.

![Effetti di replica di risultati](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Conclusioni

I risultati mostrano chiaramente che il ripristino del sito di Azure, unitamente Hyper-V Replica scale anche con minimo sovraccarico per un cluster di grandi dimensioni.  Il ripristino del sito Azure fornisce installazione semplice, replica, gestione e monitoraggio. Replica Hyper-V fornisce infrastruttura necessaria per il completamento della replica proporzioni dei caratteri. Per la pianificazione di una distribuzione ottimale è consigliabile che avere scaricato la [Pianificazione delle capacità di Replica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Dettagli sull'ambiente di test

### <a name="primary-site"></a>Sito principale

- Nel sito principale è disponibile un cluster che contiene cinque server Hyper-V 470 macchine virtuali in esecuzione.

- Macchine virtuali eseguire carichi di lavoro diverse, è comunque protetto il ripristino del sito di Azure.

- Spazio di archiviazione per il nodo cluster viene fornita da un SAN iSCSI. Modello: Hitachi HUS130.

- Ogni server cluster sono quattro schede di rete (NIC) di un GB/s ogni.

- Due delle schede di rete sono connessi a una rete privata iSCSI e due connessi a una rete aziendale esterna. Una delle reti esterne è riservata solo comunicazioni cluster.

![Requisiti hardware principale](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Server|RAM|Modello|Processore|Numero di processori|SCHEDA DI RETE|Software|
|---|---|---|---|---|---|---|
|Server di Hyper-V in cluster: <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25|128ESTLAB HOST25 sono 256|Dell™ PowerEdge™ R820|Intel (r) Xeon(R) CPU E5 4620 0 @ 2.20GHz|4|Si GB/s x 4|Windows Server Data Center 2012 R2 (x64) + ruolo Hyper-V|
|Server VMM|2|||2|1 GB/s|Windows Server Database 2012 R2 (x64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Sito secondario (ripristino)

- Il sito secondario ha un cluster di failover sei nodi.

- Spazio di archiviazione per il nodo cluster viene fornita da un SAN iSCSI. Modello: Hitachi HUS130.

![Specifiche hardware principale](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Server|RAM|Modello|Processore|Numero di processori|SCHEDA DI RETE|Software|
|---|---|---|---|---|---|---|
|Server di Hyper-V in cluster: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell™ PowerEdge™ R720|Intel (r) Xeon(R) CPU E5 2630 0 @ 2.30GHz|2|Si GB/s x 4|Windows Server Data Center 2012 R2 (x64) + ruolo Hyper-V|
|ESTLAB HOST17|128|Dell™ PowerEdge™ R820|Intel (r) Xeon(R) CPU E5 4620 0 @ 2.20GHz|4||Windows Server Data Center 2012 R2 (x64) + ruolo Hyper-V|
|ESTLAB HOST24|256|Dell™ PowerEdge™ R820|Intel (r) Xeon(R) CPU E5 4620 0 @ 2.20GHz|2||Windows Server Data Center 2012 R2 (x64) + ruolo Hyper-V|
|Server VMM|2|||2|1 GB/s|Windows Server Database 2012 R2 (x64) + VMM 2012 R2|

### <a name="server-workloads"></a>Carichi di lavoro server

- Scopo di testing abbiamo scelto carichi di lavoro utilizzati in scenari aziendali cliente.

- Serve [IOMeter](http://www.iometer.org) con la caratteristica di carico di lavoro riepilogata nella tabella per simulazione.

- Tutti i profili di IOMeter sono impostati per scrivere byte casuali per simulare relativo al caso peggiore scrivere motivi per carichi di lavoro.

|Carico di lavoro|Dimensione i/o (KB)|% Access|% Lettura|Operazioni in sospeso|Modello i/o|
|---|---|---|---|---|---|
|File Server|48163264|60 20 %5 %5% 10%|80 80% 80% 80% 80%|88888|Tutti 100% casuale|
|SQL Server (volume 1) SQL Server (volume 2)|864|100 100%|70 %0%|88|100% random100% sequenziali|
|Exchange|32|100%|67%|8|casuale al 100%|
|Workstation/VDI|464|66% 34%|95 70%|11|Entrambi casuale di 100%|
|Server Web File|4864|33 34% 33%|95 95% 95%|888|Tutto il 75% casuale|

### <a name="virtual-machine-configuration"></a>Configurazione della macchina virtuale

- 470 macchine virtuali di cluster principale.

- Tutte le macchine virtuali con disco VHDX.

- Macchine virtuali in esecuzione carichi di lavoro riepilogati nella tabella. Tutti sono stati creati con i modelli VMM.

|Carico di lavoro|# Macchine virtuali|(GB) di RAM minima|Massimo RAM (GB)|Dimensione del disco logico (GB) per macchine Virtuali|Massimo IOPS|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Server di Exchange|71|1|4|552|10|
|File Server|50|1|2|552|22|
|VDI|149|,5|1|80|6|
|Server Web|149|,5|1|80|6|
|TOTALE|470|||96.83 TB|4108|

### <a name="azure-site-recovery-settings"></a>Impostazioni di Azure il ripristino del sito

- Azure il ripristino del sito è stato configurato per locale di protezione locale

- Il server VMM include quattro aree configurati, che contiene i server di cluster Hyper-V e le macchine virtuali.

|Cloud VMM principale|Protette macchine virtuali nel cloud|Frequenza di replica|Punti di ripristino aggiuntive|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 minuti|Nessuno|
|PrimaryCloudRpo30s|47|30 secondi|Nessuno|
|PrimaryCloudRpo30sArp1|47|30 secondi|1|
|PrimaryCloudRpo5m|235|5 min.|Nessuno|

### <a name="performance-metrics"></a>Dati sulle prestazioni

Nella tabella sono riepilogate le metriche delle prestazioni e contatori che sono stati misurati nella distribuzione.

|Unità di misura metriche|Contatore|
|---|---|
|CPU|\Processor(_Total)\% tempo processore|
|Memoria disponibile|\Memory\Available MB|
|IOPS|\PhysicalDisk ( Total) \Disk trasferimenti/sec|
|Macchine Virtuali operazioni di lettura (IOPS) / sec|Dispositivo di archiviazione virtuale \Hyper-V (<VHD>) \Read operazioni/Sec|
|Operazioni di scrittura (IOPS) macchine Virtuali/sec|Dispositivo di archiviazione virtuale \Hyper-V (<VHD>) \Write operazioni/S|
|Macchine Virtuali velocità di lettura|Dispositivo di archiviazione virtuale \Hyper-V (<VHD>) \Read byte/sec|
|Velocità di scrittura macchine Virtuali|Dispositivo di archiviazione virtuale \Hyper-V (<VHD>) \Write byte/sec|


## <a name="next-steps"></a>Passaggi successivi

- [Configurare la protezione tra due siti VMM locale](site-recovery-vmm-to-vmm.md)
