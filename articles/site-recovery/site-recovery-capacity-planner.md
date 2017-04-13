<properties
    pageTitle="Pianificare la capacità per la protezione delle macchine virtuali e server fisici nel ripristino del sito di Azure | Microsoft Azure"
    description="Il ripristino del sito Azure coordinate replica failover e ripristino di macchine virtuali e server fisici disponibile in locale in Azure o a un sito secondario in locale." 
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
    ms.workload="storage-backup-recovery" 
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Pianificare la capacità per la protezione delle macchine virtuali e server fisici nel ripristino del sito di Azure

Lo strumento di pianificazione delle capacità di ripristino di Azure sito contribuisce di stabilire ai propri requisiti di capacità per la protezione delle macchine virtuali di Hyper-V, macchine virtuali VMware e server fisici Windows/Linux con il ripristino del sito di Azure.


## <a name="overview"></a>Panoramica

Utilizzare pianificazione capacità di ripristino del sito per analizzare i carichi di lavoro e un ambiente di origine e individuare la larghezza di banda della, le risorse del server che è necessario nel percorso di origine e le risorse (macchine virtuali e lo spazio di archiviazione e così via), è necessario nel percorso di destinazione. 

È possibile eseguire lo strumento in due modi:

- **Pianificazione rapido**: esecuzione dello strumento in questa modalità per ottenere previsioni di rete e server in base a un numero medio di macchine virtuali, dischi, lo spazio di archiviazione e di modifica dei.
- **Pianificazione dettagliata**: eseguire lo strumento in questa modalità e fornire dettagli di ogni carico di lavoro a livello di macchine Virtuali. Analizzare la compatibilità di macchine Virtuali e ottenere previsioni di rete e server.

## <a name="before-you-start"></a>Prima di iniziare

Prima di eseguire lo strumento:

1. Raccogliere informazioni sull'ambiente, tra cui macchine virtuali, dischi per macchine Virtuali, lo spazio di archiviazione per disco.
2. Identificare le modifiche (varianza) giornaliere per i dati replicati. Per procedere come segue:

    - Se si sta replicare macchine virtuali di Hyper-V quindi scaricare lo [strumento di pianificazione capacità di Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) per ottenere il tasso di modifica. [Altre informazioni](site-recovery-capacity-planning-for-hyper-v-replication.md) su questo strumento. È consigliabile che eseguire questo strumento più di una settimana per acquisire medie.
    - Se si sta replicare macchine virtuali VMware, utilizzare la [capacità di vSphere pianificazione accessorio](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) per determinare il tasso di varianza del.
    - Se si sta replicare server fisici è necessario stimare manualmente.

## <a name="run-the-quick-planner"></a>Eseguire la pianificazione rapida
1.  Scaricare e aprire lo strumento di [Pianificazione delle capacità di Azure sito ripristino](http://aka.ms/asr-capacity-planner-excel) . È necessario eseguire le macro in modo selezionare questa opzione per abilitare la modifica e abilitare il contenuto quando richiesto. 
2.  In **Selezionare un tipo di pianificazione** selezionare **Pianificazione rapido** dalla casella di riepilogo.

    ![Guida introduttiva](./media/site-recovery-capacity-planner/getting-started.png)

3.  Nel foglio di lavoro **Di pianificazione capacità** immettere le informazioni richieste. È necessario compilare tutti i campi cerchiati in rosso nella schermata seguente.

    - In **Selezionare il proprio scenario** scegliere **Hyper-V in Azure** o **VMware/fisici in Azure**.
    - In **dati giornaliera media modificare tasso (%)** inserire le informazioni vengono raccolte utilizzando lo [strumento di pianificazione capacità di Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) o [accessorio vSphere pianificazione](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **Compressione** si applica solo alla compressione disponibile durante la replica macchine virtuali VMware o server fisici in Azure. Abbiamo stimare 30% o più, ma è possibile modificare l'impostazione in base alle esigenze. Per la replica macchine virtuali di Hyper-V in Azure compressione è possibile utilizzare un dispositivo di terze parti, ad esempio Riverbed. 
    -  **Criteri di conservazione** introdotta specificare quanto tempo occorre conservare repliche. Se si sta replicare VMware o server fisici il valore di input in giorni. Se si sta la replica Hyper-V specificare il tempo in ore.
    -  In **numero di ore in cui la replica iniziale per il batch di macchine virtuali devono essere completate** e il **numero di macchine virtuali per il batch di replica iniziale** è immettere le impostazioni usate per calcolare i requisiti di replica iniziale.  Quando il ripristino del sito viene distribuito l'intero set di dati iniziale deve essere caricato. 

    ![Ingressi](./media/site-recovery-capacity-planner/inputs.png)

2.  Dopo aver caricato i valori per l'ambiente di origine, output visualizzato include:

    - **Larghezza di banda necessari per la replica delta** (MB/sec). Larghezza di banda di rete per la replica delta viene calcolata nella media giornaliera modifica dei dati.
    - **Larghezza di banda necessari per la replica iniziale** (MB/sec). Larghezza di banda della replica iniziale viene calcolata sui valori replica iniziale in che si inseriscono. 
    - **Spazio di archiviazione necessario (GB)** è necessario lo spazio di archiviazione Azure totale.
    - **Totale IOPS per gli account di archiviazione standard** viene calcolata in base 8 K IOPS unità dimensione negli account di spazio di archiviazione totale standard.  Per la pianificazione rapido che il numero viene calcolato in base a tutti i dischi macchine virtuali di origine e dati giornaliera modificare frequenza. Per la pianificazione dettagliata che il numero viene calcolato in base a numero totale di macchine virtuali che sono mappate standard macchine virtuali di Azure e dati cambia tasso in tali macchine virtuali. 
    - **Numero di account di archiviazione standard** fornisce il numero totale di account di archiviazione standard necessario per proteggere le macchine virtuali. Si noti che un account di archiviazione standard può contenere fino a 20.000 IOPS nelle macchine virtuali in un archivio standard e 500 IOPS massimo supportato per disco. 
    - **Numero di dischi blob necessari** restituisce il numero di dischi creati in archiviazione Azure.
    - **Numero di account di archiviazione premium necessari** fornisce il numero totale di premium gli account di archiviazione necessario per proteggere le macchine virtuali. Si noti che una fonte macchine Virtuali con IOPS elevato (maggiore di 20000) è necessario un account di archiviazione premium. Un account di archiviazione premium può contenere fino a 80000 IOPS.
    - 256 KB IOPS unità dimensioni gli account di archiviazione totale premium **Totale IOPS archiviazione premium su** calcolata.  Per la pianificazione rapido che il numero viene calcolato in base a tutti i dischi macchine virtuali di origine e dati giornaliera tasso modificato. Per la pianificazione dettagliata il numero viene calcolato in base al numero totale di macchine virtuali che sono mappate premium macchine Virtuali di Azure (Active Directory e GS delle serie) e i dati impostare frequenza su tali macchine virtuali. 
    - **Numero di server di configurazione necessari** mostra quanti server di configurazione sono necessari per la distribuzione (1)
    - **Numero di server aggiuntivi process necessari** Mostra se sono necessari oltre il server di processo che per impostazione predefinita è configurato sul server di configurazione server processo aggiuntivi.
    - **spazio di archiviazione aggiuntivo 100% sulla fonte** Mostra se è necessaria ulteriore spazio di archiviazione nella posizione di origine.
            
    ![Output](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Eseguire la pianificazione dettagliata


1.  Scaricare e aprire lo strumento di [Pianificazione delle capacità di Azure sito ripristino](http://aka.ms/asr-capacity-planner-excel) . È necessario eseguire le macro in modo selezionare questa opzione per abilitare la modifica e abilitare il contenuto quando richiesto. 
2.  In **Selezionare un tipo di pianificazione** selezionare **Pianificazione dettagliate** dalla casella di riepilogo.

    ![Guida introduttiva](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  Nel foglio di lavoro **Qualifica carico di lavoro** immettere le informazioni richieste. È necessario compilare tutti i campi contrassegnati.

    - In **Core** specificare il numero totale di core su un server di origine.
    - In **assegnazione di memoria in MB** specificare la dimensione di RAM di un server di origine. 
    - **Numero di schede** specificare il numero di schede di rete in un server di origine. 
    -  In **spazio di archiviazione totale (in GB)** specificare le dimensioni totali dello spazio di archiviazione macchine Virtuali. Ad esempio se il server di origine è 3 dischi 500 GB, dimensioni di spazio di archiviazione totale è 1500 GB.
    -  Numero **di dischi collegati** specificare il numero totale di dischi di un server di origine.
    -  Specificare l'utilizzo medio nel **disco rigido la capacità di utilizzo** .
    -  In **ogni giorno modificare tasso (%)** specificare che i dati giornalieri modificare tasso di un server di origine.
    -  **Mappatura di Azure** dimensioni uno immettere dimensioni macchine Virtuali di Azure che si desidera eseguire il mapping. Se non si desidera eseguire questa operazione manualmente fare clic su**Calcolare macchine virtuali IaaS**. Si noti che se un'impostazione manuale di input e quindi fare clic su macchine virtuali IaaS calcolare l'impostazione manuale potrebbe sovrascritta perché il processo di elaborazione identifica automaticamente quello più adatto alle dimensioni di macchine Virtuali di Azure.

    ![Qualifica carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Se fa clic su **Macchine virtuali IaaS calcolare** qui è risultato:

    - Convalida input obbligatorio.
    - Calcola IOPS e vengono suggerite macchina virtuale Azure migliore aize corrispondenza per ogni macchine virtuali che sia idoneo per la replica in Azure. Se non è possibile rilevare una dimensione appropriata di macchine Virtuali di Azure che viene visualizzato un errore. Ad esempio, se il numero di dischi 65 allegati in un errore è problemi dopo la dimensione massima macchine Virtuali di Azure è 64.
    - Suggerisce un account di archiviazione che può essere usato per una macchina virtuale Azure.
    - Calcola il numero totale di spazio di archiviazione standard account e premium lo spazio di archiviazione necessario per il carico di lavoro. Scorrere verso il basso a destra per visualizzare gli account di archiviazione che può essere utilizzato per un server di origine e tipo di archiviazione Azure
    - Completa e ordina il resto della tabella in base al tipo di memorizzazione richiesta (standard o premium) assegnato per una macchina virtuale e il numero di dischi collegati. Per tutte le macchine virtuali che soddisfano i requisiti per il backup di Azure, colonna A (è macchine Virtuali qualificati?) viene visualizzato Sì. Se non è possibile eseguire una macchina virtuale fino a Azure un errore viene visualizzato.

Colonne AA a UA vengono restituiti e forniscono informazioni per ogni macchina virtuale.

![Qualifica carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Esempio
Ad esempio, per sei macchine virtuali con i valori visualizzati nella tabella, lo strumento calcola e assegna la migliore corrispondenza macchine Virtuali di Azure e i requisiti di spazio di archiviazione Azure.

![Qualifica carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- Nell'output per l'esempio tenere presente quanto segue:
    
    - La prima colonna è una colonna di convalida per macchine virtuali, dischi e varianza.
    - Per cinque macchine virtuali sono necessari due account di archiviazione standard e account di archiviazione uno premium. 
    -  VM3 non idonei per la protezione poiché uno o più dischi sono più di 1 TB.
    -  VM1 e VM2 possono utilizzare il primo account che lo spazio di archiviazione standard
    -  VM4 possibile utilizzare il secondo account di archiviazione standard.
    -  È necessario un account di archiviazione premium VM5 e VM6 e possono utilizzare un singolo account.

    >[AZURE.NOTE]  IOPS allo spazio di memorizzazione standard e premium vengono calcolati a livello di macchine Virtuali e non a livello di disco. Una macchina virtuale standard è possibile gestire fino a 500 IOPS disco. Se è superiore a 500 IOPS per un disco, è necessario archiviazione premium. Tuttavia se IOPS per un disco sono più di 500 ma IOPS per dischi macchine Virtuali entro i limiti di macchine Virtuali di Azure standard supporto (size macchine Virtuali, numero di dischi, numero di memoria schede, CPU,) quindi della visualizzazione pianificazione sceglie uno standard di macchine Virtuali e non di dominio Active Directory o GS serie. È necessario aggiornare manualmente la cella di dimensioni Azure mapping appropriato serie DS o GS macchine Virtuali.

5. Dopo aver tutti i dettagli, fare clic su **Invia dati per lo strumento di pianificazione** per aprire la **Pianificazione delle capacità** carichi di lavoro sono evidenziate per indicare se si è idonei per la protezione o meno.


### <a name="submit-data-in-the-capacity-planner"></a>Inviare i dati nella pianificazione capacità

1.  Quando si apre il foglio di lavoro di **Pianificazione delle capacità** viene popolata in base alle impostazioni specificati. La parola "carico di lavoro viene visualizzata nella cella di **origine di input infrastruttura** a indicare che il foglio di lavoro di **Carico di lavoro qualifiche** input. 
2.  Se si desidera apportare modifiche è necessario modificare il foglio di lavoro **Qualifiche carico di lavoro** e fare clic su Invia dati per lo strumento di pianificazione nuovamente.  

    ![Pianificazione della capacità](./media/site-recovery-capacity-planner/capacity-planner.png)


