
<properties
    pageTitle="Il ripristino del sito Azure: Replica Hyper-V macchine virtuali di un unico server VMM | Microsoft Azure"
    description="In questo articolo viene descritto come replicare macchine virtuali Hyper-V quando è sufficiente un singolo server VMM."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Replicare macchine virtuali di Hyper-V in un unico server VMM

Leggere questo articolo per informazioni su come replicare macchine virtuali di Hyper-V in un server di host Hyper-V in un'area VMM quando è sufficiente un singolo server VMM nella distribuzione.

Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: Gestione risorse di Azure e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione. In questo articolo è contenute istruzioni per la configurazione della replica nel portale di Azure.


Se hai domande in questo articolo, è necessario pubblicarli commenti Disqus nella parte inferiore di questo articolo o nel [forum di servizi di ripristino di Windows Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica

Se si desidera replicare macchine virtuali di Hyper-V in host Hyper-V in VMM è sufficiente un singolo server VMM, è possibile [replicare Azure](site-recovery-vmm-to-azure.md), o tra le aree sul server VMM singolo.

È consigliabile replicare Azure perché failover e ripristino non continua durante la replica tra nuvole e sono necessari alcuni passaggi manuali. Se si desidera replicare utilizza solo il server VMM, è possibile eseguire le operazioni seguenti:

- La replica con un server VMM singola autonomo
- La replica con un singolo server VMM distribuiti in un cluster di Windows esteso


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Replicare nei vari siti con un server VMM singola autonomo

![Server VMM virtuale autonomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

In questo scenario distribuire il server VMM singolo come macchina virtuale nel sito principale e replicare questa macchina virtuale in un sito secondario utilizzando il ripristino del sito e Replica Hyper-V.

1. Configurare VMM in una macchina virtuale Hyper-V. È consigliabile che installare l'istanza di SQL Server utilizzata da VMM nella stessa macchina virtuale per risparmiare tempo. Se si desidera utilizzare si verifica un'istanza di SQL Server e un'interruzione del servizio remota, è necessario ripristinare l'istanza prima di poter ripristinare VMM.
2. Assicurarsi che il server VMM contenga almeno due aree configurati. Un'area contiene macchine virtuali da replicare e altri cloud viene utilizzato come posizione secondaria. Deve essere nel cloud che contiene le macchine virtuali da proteggere:

    - Uno o più gruppi host VMM contenente uno o più server host Hyper-V in ogni gruppo host.
    - Macchina virtuale Hyper-V almeno ogni server di host Hyper-V.

3. Creare un archivio di servizi di recupero, generare e scaricare una chiave di registrazione di archivio e registrare il server VMM nell'archivio di. Durante la registrazione è installare il Provider di ripristino del sito di Azure sul server VMM.
4. Impostare una o più aree in VM VMM e aggiungere gli host Hyper-V in queste aree.
3. Configurare le impostazioni di protezione per il cloud. Specificare il nome del server VMM singolo come il percorso di origine e destinazione. Per configurare il mapping di rete, si esegue il mapping della rete macchine Virtuali per il cloud con macchine virtuali che si desidera proteggere alla rete macchine Virtuali per il cloud replica.
4. Abilitare la replica iniziale per le macchine virtuali che si desidera proteggere tramite la rete perché si trovano entrambi nuvole nello stesso server.
4. Nella console di gestione di Hyper-V, abilitare Replica Hyper-V nell'host Hyper-V contenente VM VMM e abilitare la replica nella macchina virtuale. Assicurarsi che non si aggiungono VM VMM a qualsiasi nuvole protetti tramite il ripristino del sito. In questo modo che non sono override delle impostazioni di Replica Hyper-V per il ripristino del sito.
5. Se si desidera creare piani di ripristino, specificare lo stesso server VMM per origine e destinazione.

Seguire le istruzioni fornite in [questo articolo](site-recovery-vmm-to-vmm.md) per creare un archivio, registrare il server e configurare la protezione.

### <a name="what-to-do-in-an-outage"></a>Operazioni da eseguire in un'interruzione del servizio

Se si verifica un'interruzione completa ed è necessario eseguire operazioni dal sito secondario, eseguire le operazioni seguenti:

1.  Nella console di gestione di Hyper-V in sito secondario, eseguire un il controllo non pianificato a esito negativo su VM VMM da primario al sito secondario.
2.  Verificare che la VM VMM sia in esecuzione nel sito secondario.
3.  Nell'archivio di servizi di recupero, eseguire un il controllo non pianificato a esito negativo sul carico di lavoro macchine virtuali da primario per nuvole secondari. Per completare il failover non pianificato delle macchine virtuali, eseguire il commit il failover o selezionare un punto di ripristino diversi in base alle esigenze.
4.  Dopo avere inserito failover non pianificato, gli utenti possono accedere alle risorse carico di lavoro del sito secondario.

Quando il sito principale funziona normalmente nuovamente, eseguire le operazioni seguenti:

1.  Nella console di gestione di Hyper-V, attivare la replica inversa per VM VMM, per avviare la replica da secondaria a primaria.
2.  Nella console di gestione di Hyper-V eseguire pianificato in caso di errore per avere esito negativo indietro VM VMM al sito principale. Eseguire il commit failover per completare l'operazione. Quindi attivare la replica inversa avviare la replica VMM da primario e secondario.
3.  Nell'archivio di servizi di recupero, attivare la replica inversa per il carico di lavoro macchine virtuali, per avviare la loro replica da secondaria a primaria.
4.  Nell'archivio di servizi di recupero eseguire pianificato in caso di errore per avere esito negativo nuovamente il carico di lavoro macchine virtuali al sito principale. Eseguire il commit failover per completare l'operazione. Quindi attivare la replica inversa avviare la replica il carico di lavoro macchine virtuali da primario e secondario.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Replicare nei vari siti con un singolo server VMM in un cluster esteso

![Server VMM virtuale raggruppate](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Invece di distribuzione di un server VMM autonomo come una macchina replica in un sito secondario, è possibile rendere VMM altamente disponibili mediante la distribuzione come una macchina virtuale in un cluster di failover di Windows. In questo modo adattabilità carico di lavoro e protezione dai guasti hardware. Per distribuire con il ripristino del sito VM VMM devono essere distribuite in un cluster di estensione nei vari siti aree geografiche separati. Per procedere come segue:

1. Installare VMM in un computer virtuale in un cluster di failover di Windows e selezionare l'opzione per eseguire il server per la disponibilità elevata durante l'installazione.
2. L'istanza di SQL Server che viene utilizzato da VMM deve essere replicato con disponibilità AlwaysOn di SQL Server in modo che non esiste una replica di database nel sito secondario.
3. Seguire le istruzioni fornite in [questo articolo](site-recovery-vmm-to-vmm.md) per creare un archivio, registrare il server e configurare la protezione. È necessario registrare ogni server VMM cluster nell'archivio di servizi di recupero. A tale scopo, installare il Provider in un nodo attivo e registrare il server VMM. Quindi installare il Provider su altri nodi.

### <a name="what-to-do-in-an-outage"></a>Operazioni da eseguire in un'interruzione del servizio

Quando si verifica un'interruzione del servizio, server VMM e il database di SQL Server corrispondente non riuscite su sono accessibili tramite il sito secondario.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-vmm-to-vmm.md) sulla distribuzione il ripristino del sito dettagliata per VMM a replica VMM.
