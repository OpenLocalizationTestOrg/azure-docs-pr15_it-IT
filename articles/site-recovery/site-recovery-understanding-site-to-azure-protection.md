<properties
    pageTitle="La replica di Hyper-V con il ripristino del sito di Azure | Microsoft Azure"
    description="Utilizzare questo articolo per comprendere i concetti tecnici che consentono di correttamente installare, configurare e gestire il ripristino del sito di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Replica di Hyper-V con il ripristino del sito di Azure

In questo articolo vengono illustrati i concetti tecnici che consentono di configurare e gestire un sito di Hyper-V o System Center Virtual Machine Manager (VMM) protezione Azure utilizzando il ripristino del sito di Azure correttamente.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Impostazione dell'ambiente di origine per la replica tra locale e Azure

Come parte dell'impostazione di emergenza tra locale e Azure, assicurarsi di scaricare e installare Provider di ripristino del sito di Azure sul server VMM. Installare l'agente di servizi di recupero Azure su ogni host Hyper-V.

![Distribuzione di VMM sito per la replica tra locale e Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Configurare l'ambiente di origine in un'infrastruttura di Hyper-V gestite è simile all'impostazione dell'ambiente di origine in un'infrastruttura gestita VMM. L'unica differenza è che il provider e l'agente siano installati nell'host Hyper-V stesso. Nell'ambiente di VMM, il provider di servizi viene installato sul server VMM e l'agente è installato in host Hyper-V (in caso di replica in Azure).

## <a name="workflows"></a>Flussi di lavoro

### <a name="enable-protection"></a>Attivare la protezione
Dopo aver protetto una macchina virtuale dal portale di Azure o locale, un processo di ripristino del sito denominato **Attiva protezione** Avvia. È possibile monitorare nella scheda **processi** .

!["Attiva protezione" processo nell'elenco dei processi](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

Il processo di **attivare la protezione** verifica i prerequisiti prima di chiamare il metodo [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Questo metodo crea replica in Azure utilizzando input configurati durante la protezione.

Il processo di **attivare la protezione** avvia la replica iniziale locali chiamando il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Questo metodo invia dischi virtuali della macchina virtuale in Azure.

![Dettagli del processo di "Attiva protezione"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Completare la protezione del computer virtuale
Quando viene attivata la replica iniziale non viene eseguita uno [snapshot della macchina virtuale Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) . Dischi rigidi virtuali vengono elaborati uno alla volta fino a quando tutti i dischi vengono caricati in Azure. In genere verrà un po' di tempo alla fine, in base alle dimensioni del disco e la larghezza di banda. Per ottimizzare l'utilizzo di rete, vedere [come gestire locale per l'utilizzo della larghezza di banda di rete di protezione Azure](https://support.microsoft.com/kb/3056159).

Una volta completata la replica iniziale, il processo di **protezione Finalize sul computer virtuale** consente di configurare le impostazioni di rete e post-replica. Mentre è in corso la replica iniziale:

- Tutte le modifiche ai dischi vengono rilevate. 
- Spazio su disco aggiuntive è utilizzata per lo snapshot e i file di Log di Replica Hyper-V (HRL).

Al termine di replica iniziale, viene eliminato snapshot macchine Virtuali di Hyper-V. L'eliminazione genera l'unione delle modifiche ai dati dopo la replica iniziale al disco padre.

![Processo di "Completare la protezione del computer virtuale" nell'elenco dei processi](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Replica delta
Individuazione di replica Replica Hyper-V, che fa parte del motore di replica di Replica di Hyper-V, tiene traccia delle modifiche a un disco rigido virtuale come file di Log di Replica Hyper-V (*.hrl). File HRL sono nella stessa directory dischi associati.

Ogni disco in cui è configurato per la replica è associata a un file HRL. Questo registro viene inviato all'account di archiviazione del cliente dopo il completamento della replica iniziale. Quando un file di log è in transito verso Azure, le modifiche nel server primario vengono registrate in un altro file di log nella stessa directory.

Durante la replica iniziale o delta, è possibile monitorare l'integrità del replica macchine Virtuali nella visualizzazione macchine Virtuali, come indicato in [monitorare l'integrità della replica per macchina virtuale](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Risincronizzazione
Una macchina virtuale viene contrassegnata per la risincronizzazione quando si verifica un errore di replica delta sia iniziale completo della replica è costosa in termini di larghezza di banda di rete o un'ora. Ad esempio, quando pile dimensioni del file HRL fino al 50% della dimensione totale del disco, la macchina virtuale viene contrassegnata per la risincronizzazione. Risincronizzazione riduce la quantità di dati inviati in rete computing checksum dischi macchina virtuale origine e destinazione e inviando solo la differenza.

Al termine della risincronizzazione, dovrebbe riprendere la replica delta normale. È possibile riprendere la risincronizzazione se si verifica un'interruzione di rete o un'altra interruzione.

Per impostazione predefinita, risincronizzazione programmata automaticamente è configurata per l'esecuzione all'esterno di ore lavorative. Se la macchina virtuale deve essere sincronizzati manualmente, selezionare la macchina virtuale dal portale e fare clic su **sincronizzare di nuovo**.

![Risincronizzazione manuale](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Risincronizzazione utilizza un algoritmo di suddivisione in blocchi blocco fixed in file di origine e destinazione sono suddivise in blocchi fissi. Checksum per ogni blocco vengono generate e quindi confrontati per determinare quali blocchi dal sito di origine devono essere applicati alla destinazione.

### <a name="retry-logic"></a>Riprovare logica
Non c'è logica tentativi incorporato per gli errori di replica. Questa logica può essere suddivise in due categorie:

| Categoria                  | Scenari                                    |
|---------------------------|----------------------------------------------|
| Errore irreversibile     | Nessun tentativo viene eseguito. Lo stato di replica di macchina virtuale è **critico**e intervenire amministratore. Alcuni esempi: <ul><li>Catena disco rigido virtuale errata</li><li>Stato non valido per la macchina virtuale replica</li><li>Errore di autenticazione di rete</li><li>Errore di autorizzazione</li><li>Macchina virtuale che non è stato trovato, nel caso di un server autonomo Hyper-V</li></ul>|
| Errore reversibile         | Tentativi verificano ogni intervallo di replica, utilizzando un esponenziale back-off che aumenta l'intervallo dall'inizio del primo tentativo (1, 2, 4, 8, 10 minuti). Se un messaggio di errore persiste, Riprova ogni 30 minuti. Alcuni esempi: <ul><li>Errore di rete</li><li>Spazio su disco insufficiente</li><li>Condizione di memoria insufficiente</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Ciclo di vita di protezione e ripristino macchina virtuale Hyper-V

![Ciclo di vita di protezione e ripristino macchina virtuale Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Altri riferimenti

- [Monitorare e risolvere i problemi di protezione per i siti di Hyper-V e fisici VMware, VMM,](./site-recovery-monitoring-and-troubleshooting.md)
- [Raggiungere per il supporto Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Gli errori comuni del ripristino del sito di Azure e le relative soluzioni](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
