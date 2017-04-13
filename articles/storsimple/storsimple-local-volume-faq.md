<properties 
   pageTitle="StorSimple localmente aggiunte volumi domande frequenti su | Microsoft Azure"
   description="Vengono fornite le risposte alle domande frequenti sui volumi StorSimple localmente aggiunte."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localmente aggiunte volumi: domande frequenti domande frequenti

## <a name="overview"></a>Panoramica

Di seguito sono domande e risposte che potrebbe essere quando si crea un volume StorSimple localmente aggiunte, convertire un volume a più livelli in un volume locale bloccato e viceversa, o eseguire il backup e ripristinare un volume locale bloccato.

Domande e risposte sono organizzate in categorie seguenti

- Creazione di un volume locale bloccato
- Backup di un locale bloccata
- Conversione di un volume a più livelli in un volume locale bloccato
- Ripristino di un volume locale bloccato
- Errore in un volume locale bloccato

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Domande sulla creazione di un volume locale bloccato

**Q.** Che cos'è la dimensione massima di un volume locale bloccata che è possibile creare nei dispositivi 8000 serie?

**A** è possibile eseguire il provisioning volumi localmente bloccati fino a 8,5 TB o volumi a più livelli fino a 200 TB sul dispositivo 8100. Sul dispositivo 8600 più grande, è possibile eseguire il provisioning volumi localmente bloccati fino a 22,5 TB o volumi a più livelli fino a 500 TB.

**Q.** L'aggiornamento recente dispositivo 8100 a 2 di aggiornamento e quando si tenta di creare un volume locale bloccato, la dimensione massima disponibile è solo 6 TB e non 8,5 TB. Perché è possibile creare un volume TB 8,5?

**Risposte** è possibile effettuare il provisioning volumi localmente bloccati fino a 8.5 OR TB a più livelli volumi fino a 200 TB sul dispositivo 8100. Se il dispositivo ha già livelli volumi e quindi lo spazio disponibile per la creazione di un volume locale bloccato sarà proporzionalmente inferiore a questo limite massimo. Ad esempio, se sono già state acquisite 100 TB di volumi a più livelli in un dispositivo 8100 (che è la metà della capacità a più livelli), quindi la dimensione massima di un volume locale che è possibile creare sul dispositivo 8100 verrà essere conseguentemente ridotte a 4 TB (circa la metà della massima localmente aggiunte capacità del volume).

Quanto spazio locale nel dispositivo viene utilizzato per ospitare il working set di volumi a più livelli, viene ridotta dello spazio disponibile per la creazione di un volume locale bloccato se il dispositivo è a più livelli di volumi. Invece la creazione di un volume locale bloccato in modo proporzionale riduce lo spazio disponibile per indicare i volumi a più livelli. La tabella seguente riepiloga le capacità a più livelli disponibile nei dispositivi 8100 e 8600 quando vengono creati volumi localmente bloccati.

|Capacità di provisioning volumi localmente bloccata|Capacità disponibile effettuare il provisioning per indicare i volumi a più livelli - 8100|Capacità disponibile effettuare il provisioning per indicare i volumi a più livelli - 8600|
|-----|------|------|
|0 | 200 TB | 500 TB |
|1 TB | 176.5 TB | 477.8 TB|
|4 TB | 105.9 TB | 411.1 TB |
|8.5 TB | TB 0 | 311.1 TB|
|10 TB | ND | 277.8 TB |
|15 TB | ND | 166.7 TB |
|22.5 TB | ND | TB 0 |


**Q.** Perché è la creazione del volume localmente bloccata un'operazione di esecuzione prolungata? 

**RISPOSTE.** Locale bloccati volumi thickly vengono effettuato il provisioning. Per creare spazio su livelli locali del dispositivo, alcuni dati di volumi a più livelli esistenti potrebbero essere inseriti nel cloud durante il processo di provisioning. E poiché questo dipende dalle dimensioni del volume viene effettuato il provisioning, i dati presenti nel dispositivo e la larghezza di banda disponibile nel cloud, il tempo necessario per creare un volume locale può essere diverse ore.

**Q.** Quanto tempo è necessario per creare un volume locale bloccato?

**RISPOSTE.** Poiché volumi localmente bloccati thickly vengono effettuato il provisioning, alcuni dati esistenti da volumi a più livelli potrebbero inseriti nel cloud durante il processo di provisioning. Di conseguenza, il tempo necessario per creare un volume locale bloccato dipende da vari fattori, tra cui la dimensione del volume, i dati nel dispositivo e la larghezza di banda disponibile. In un dispositivo appena installato con nessun volume tempo per la creazione di un volume locale bloccato è circa 10 minuti per terabyte di dati. Tuttavia, la creazione di volumi locali può richiedere alcune ore in base a fattori descritto in precedenza in un dispositivo in uso.

**Q.** Si desidera creare un volume locale bloccato. Esistono delle procedure consigliate, che è necessario essere a conoscenza?

**RISPOSTE.** Locale bloccati volumi sono adatti per carichi di lavoro che richiedono garanzie locale dei dati in qualsiasi momento e sono riservate nel cloud latenza. Nel valutare l'utilizzo della volumi locali per tutti i carichi di lavoro, tenere presente quanto segue:

- Locale bloccati volumi thickly vengono effettuato il provisioning e la creazione di volumi locali impatto dello spazio disponibile per indicare i volumi a più livelli. Di conseguenza, è consigliabile iniziare con volumi di dimensioni inferiori e scalabilità man mano che aumenta il requisito lo spazio di archiviazione.

- Il provisioning di volumi locali è un'operazione lunga che prevede l'inserimento di dati esistenti da volumi a più livelli nel cloud. Di conseguenza, possono verificarsi prestazioni ridotte su questi volumi.

- Il provisioning di volumi locali è un'operazione molto tempo. Il tempo effettivo coinvolti dipende da vari fattori: le dimensioni del volume viene effettuato il provisioning, dati sul dispositivo e larghezza di banda disponibile. Se non dispone di backup i volumi esistenti nel cloud, la creazione del volume è lenta. È consigliabile che si istantanee cloud dei volumi esistenti prima effettuare il provisioning di un volume locale.
 
- È possibile convertire i volumi esistenti a più livelli in volumi bloccati in locale e la conversione comporta il provisioning di spazio sul dispositivo per il volume localmente bloccato risultante (oltre alle arrestare dati a più livelli [eventuale] dal cloud). Anche questa è un'operazione di esecuzione prolungata che varia in base a fattori che già state indicato in precedenza. È consigliabile eseguire il backup dei volumi esistenti prima della conversione durante il processo sarà anche più lento se volumi esistenti non sono backup. Il dispositivo potrebbe anche verificarsi prestazioni ridotte durante il processo.
    
Ulteriori informazioni su come [creare un volume locale bloccato](storsimple-manage-volumes-u2.md#add-a-volume)

**Q.** È possibile creare più volumi localmente bloccati allo stesso tempo?

**RISPOSTE.** Sì, ma eventuali processi di creazione e l'espansione volume localmente bloccata vengono elaborate in sequenza.

Thickly viene completato il provisioning volumi localmente aggiunto e si richiede la creazione di spazio locale nel dispositivo (questo può comportare i dati esistenti di volumi a più livelli devono essere inserito nel cloud durante il processo di provisioning). Pertanto, se non è un processo di provisioning in corso, altri processi di creazione volume locale vengono accodate fino a tale termine.

Allo stesso modo, se è l'espansione un volume locale esistente o un volume a più livelli viene convertito in un volume locale bloccato, la creazione di un nuovo volume localmente bloccata è accodata fino al completamento del processo precedente. Espandere la dimensione di un volume locale bloccata a altro comporta l'espansione dello spazio locale esistente per tale volume. Conversione da una a più livelli in locale aggiunte volume comporta anche la creazione di spazio locale per risultante localmente aggiunte volume. In entrambi i queste operazioni, creazione o espansione spazio locale è un valore long in esecuzione processo.

È possibile visualizzare questi processi nella pagina **dei processi** del servizio di Azure StorSimple Manager. Il processo attivamente elaborate viene costantemente aggiornato per riflettere lo stato di avanzamento di provisioning spazio. I processi di volume localmente bloccata rimanente viene contrassegnato come è in esecuzione, ma si blocca lo stato di avanzamento e hanno scelto nell'ordine che sono state accodate.

**Q.** Eliminato un volume locale bloccato. Perché non è disponibile lo spazio recuperato applicato dello spazio disponibile quando si tenta di creare un nuovo volume? 

**RISPOSTE.** Se si elimina un volume locale bloccato, lo spazio disponibile per i nuovi volumi potrebbe non essere aggiornato immediatamente. Il servizio di gestione StorSimple Aggiorna spazio locale disponibile circa ogni ora. È consigliabile che attendere un'ora prima che si tenta di creare il nuovo volume.

**Q.** Volumi localmente bloccati sono supportati sul dispositivo cloud?

**RISPOSTE.** Volumi localmente bloccati non sono supportati sul dispositivo cloud (dispositivi 8010 e 8020 definiti in precedenza il dispositivo virtuale StorSimple).

**Q.** È possibile utilizzare i cmdlet di PowerShell Azure per creare e gestire i volumi localmente bloccati? 

**RISPOSTE.** No, non è possibile creare volumi localmente bloccati tramite i cmdlet di PowerShell Azure (qualsiasi volume creato tramite PowerShell Azure livelli). È anche consigliabile non utilizzare i cmdlet di PowerShell Azure per modificare le proprietà di un volume locale bloccata come avrà effetto indesiderato di modificare il tipo di volume fino a più livelli.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Domande sui backup di un volume locale bloccato

**Q.** Sono istantanee locale di volumi localmente bloccati supportati?

**RISPOSTE.** Sì, è possibile eseguire locale istantanee dei volumi localmente bloccati. Tuttavia, è consigliabile regolarmente backup di un volume locale bloccato snapshot cloud per garantire che i dati siano protette in eventualità di emergenza.

**Q.** Sono disponibili le linee guida per la gestione delle istantanee locale per indicare i volumi localmente bloccati?

**RISPOSTE.** Snapshot locale frequenti insieme a un valore elevato di varianza di dati del volume localmente bloccata potrebbero causare spazio locale nel dispositivo consumate rapidamente e produrre dati dai volumi a più livelli vengano applicati nel cloud. È pertanto consigliabile che è ridurre il numero di snapshot locale.

**Q.** Si riceve un avviso per indicare che è possono Invalidate personale locale istantanee volumi localmente bloccati. Quando può verificarsi?

**RISPOSTE.** Snapshot locale frequenti insieme a un valore elevato di varianza di dati del volume localmente bloccata potrebbero causare spazio locale nel dispositivo devono essere utilizzate rapidamente. Se vengono utilizzati i livelli locali del dispositivo, un'interruzione del servizio cloud "Extended" può comportare il dispositivo si riempie e scrive in arrivo per il volume possono comportare la convalida delle istantanee (come nessuno spazio disponibile per aggiornare le istantanee per fare riferimento ai blocchi di versioni precedenti di dati che sono stati sovrascritti). In questo caso in cui scrive il volume continueranno a essere rappresentate, ma potrebbero non essere valide istantanee locale. Non esiste alcun effetto lo snapshot cloud esistenti.

L'avviso di avviso è per ricevere una notifica che tale situazione può verificarsi e assicurarsi che lo stesso indirizzo in modo tempestivo rivedere le pianificazioni istantanee locale a meno frequenti istantanee locale o eliminazione di versioni precedenti snapshot locale che non sono più necessari.

Se vengono invalidate istantanee locale, verrà visualizzato un avviso di informazioni indicante che sono state invalidate snapshot locale per criteri di backup specifico insieme all'elenco di data e ora di snapshot locale che sono state invalidate. Questi snapshot saranno eliminati automaticamente e non sarà possibile visualizzarli nella pagina **Cataloghi di Backup** nel portale di classica Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Domande sulla conversione di un volume a più livelli in un volume locale bloccato

**Q.** Sta osservando alcune lentezza sul dispositivo durante la conversione di un volume a più livelli in un volume locale bloccato. Cosa è dovuto? 

**RISPOSTE.** Il processo di conversione prevede due passaggi: 

  1. Il provisioning di spazio sul dispositivo per la breve-a--convertire localmente aggiunte volume.
  2. Il download di qualsiasi tipo di dati a più livelli dal cloud per garantire locale garantisce.

Entrambe le operazioni sono lunghe che esegue le operazioni che dipendono le dimensioni del volume da convertire dati sul dispositivo e larghezza di banda disponibile. Come alcuni dati di volumi a più livelli esistenti potrebbero soppressione nel cloud come parte del processo di provisioning, il dispositivo potrebbe verificarsi prestazioni ridotte durante questo periodo. Inoltre, il processo di conversione può essere più lento se:

- Volumi esistenti non backup sono stato nel cloud; in modo che è consigliabile che eseguire il backup dei volumi prima di avviare una conversione.

- Criteri di limitazione della larghezza di banda siano stati applicati, che potrebbe limitare la larghezza di banda disponibile nel cloud; è pertanto consigliabile che si usa un 40 Mbps dedicato o altre connessione al cloud.

- Il processo di conversione può richiedere alcune ore a causa di vari fattori sopra; di conseguenza, si consiglia di eseguire questa operazione durante le ore non picchi o festivi per evitare l'impatto sugli utenti finali.

Ulteriori informazioni su come [convertire un volume a più livelli in un volume locale bloccato](storsimple-manage-volumes-u2.md#change-the-volume-type)

**Q.** È possibile annullare l'operazione di conversione volume?

**RISPOSTE.** No, non è possibile sul Annulla l'operazione di conversione di una volta avviata. Come descritto nella domanda precedente, tenere presente gli eventuali problemi di prestazioni che potrebbero verificarsi durante il processo e seguire le procedure consigliate elencate sopra, quando si pianifica la conversione.

**Q.** Cosa succede al volume se l'operazione di conversione non riesce?

**RISPOSTE.** Conversione del volume potrebbe non riuscire a causa di problemi di connettività del cloud. Il dispositivo potrebbe interrompersi alla fine del processo di conversione dopo una serie di tentativi di importare dati a più livelli di nel cloud. In questo caso, il tipo di volume continuerà a essere il tipo di volume di origine prima della conversione, e:

- Verrà generato un avviso per la notifica dell'errore di conversione volume. Ulteriori informazioni su [avvisi correlati a volumi localmente bloccati](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Se si converte una a più livelli in un volume locale bloccato, il volume continuerà a presentano proprietà di un volume a più livelli come dati potrebbero ancora trovarsi nel cloud. Si consiglia di risolvere i problemi di connettività e quindi ripetere l'operazione di conversione.
 
- Allo stesso modo, quando la conversione da un localmente aggiunto a un volume a più livelli non riesce, anche se il volume verrà contrassegnato come volume localmente bloccato, essa funzionerà come volume a più livelli (perché dati Impossibile siano stati inseriti vuoti nel cloud). Tuttavia, continuerà a occupi spazio livelli locali del dispositivo. Questo spazio non sarà disponibile per gli altri volumi localmente bloccate. Si consiglia di ripetere l'operazione per garantire che è stata completata la conversione del volume e possa essere recuperato spazio locale nel dispositivo.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Domande sul ripristino di un volume locale bloccato

**Q.** Sono inclusi in locale volumi ripristinati immediatamente?

**RISPOSTE.** Sì, in locale bloccati volumi vengono ripristinati immediatamente. Non appena le informazioni sui metadati per il volume vengono spostati dal cloud come parte dell'operazione di ripristino, il volume è in linea e sono accessibili dall'host. Tuttavia, locale garanzie per il volume di dati non sono presenti fino a quando tutti i dati è stato scaricato dal cloud che si verifichino ridotte le prestazioni in questi volumi per la durata dell'operazione di ripristino.

**Q.** Quanto tempo è necessario ripristinare un volume locale bloccato?

**RISPOSTE.** Locale bloccati volumi vengono ripristinati immediatamente e in linea non appena le informazioni sui metadati volume viene recuperati dal cloud, mentre i dati del volume continuano a essere scaricati in background. Questa quest'ultima parte dell'operazione di ripristino - tornando garanzie locale per i dati del volume - è un'operazione lunga e potrebbe richiedere alcune ore per tutti i dati da rendere locale nuovamente. Il tempo necessario per completare lo stesso dipende da vari fattori, ad esempio le dimensioni del volume da ripristinare e la larghezza di banda disponibile. Tempo aggiuntivo è stato eliminato il volume originale da ripristinare, verrà visualizzato automaticamente per creare spazio locale nel dispositivo come parte dell'operazione di ripristino.

**Q.** È necessario ripristinare il volume localmente bloccato esistente a uno snapshot precedente (prese quando è stata a più livelli il volume). Il volume verrà ripristinato come a più livelli in questo caso?

**RISPOSTE.** No, il volume verrà ripristinato come volume localmente bloccato. Anche se le date di snapshot in ora quando il volume è stata a più livelli, durante il ripristino volumi esistenti, StorSimple utilizza sempre il tipo di volume sul disco attualmente esistente.

**Q.** Esteso il volume localmente bloccato recente, ma è necessario ripristinare i dati in un intervallo di tempo durante il volume di dimensioni inferiore a questo punto. Verrà Ripristina ridimensionare il volume corrente e sarà necessario aumentare le dimensioni del volume dopo il completamento del ripristino?

**RISPOSTE.** Sì, il ripristino verrà ridimensionata il volume e sarà necessario aumentare le dimensioni del volume dopo il completamento del ripristino.

**Q.** È possibile modificare il tipo di un volume durante il ripristino?

**A.** No, non è possibile modificare il tipo di volume durante il ripristino.

- Volumi che sono stati eliminati verranno ripristinati come tipo archiviato nello snapshot.

- Volumi esistenti vengono ripristinati in base al tipo corrente, indipendentemente dal tipo archiviato in snapshot (fare riferimento alle due domande precedente).
 
**Q.** È necessario ripristinare il volume bloccato in locale, ma si è scelto un punto non corretto in snapshot ora. È possibile annullare l'operazione di ripristino corrente?

**RISPOSTE.** Sì, è possibile annullare un'operazione di ripristino in corso. Lo stato del volume verrà ripristinato lo stato all'inizio del ripristino. Tuttavia, qualsiasi scrive che sono state apportate al volume durante il ripristino andranno perse.

**Q.** Avviata un'operazione di ripristino su uno dei volumi localmente aggiunto e viene visualizzato uno snapshot nel catalogo personale backlog non raccogliere di nuovo la creazione. Che cos'è questo utilizzato per?

**RISPOSTE.** Si tratta istantanea temporanea creata prima dell'operazione di ripristino e viene utilizzata per il ripristino nel caso in cui il ripristino viene annullato o ha esito negativo. Non eliminare questo snapshot. essa sarà eliminato automaticamente quando il ripristino. Questo problema può verificarsi se il processo di ripristino solo in locale sia bloccato volumi o una combinazione di volumi localmente bloccati e a più livelli. Se il processo di ripristino include solo i volumi a più livelli, quindi il problema non si verificherà.

**Q.** È possibile duplicare un volume locale bloccato?

**RISPOSTE.** Sì, è possibile. Tuttavia, il volume localmente bloccato verrà duplicato come volume a più livelli per impostazione predefinita. Ulteriori informazioni su come [duplicato un volume locale bloccato](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Domande sull'esito negativo su un volume locale bloccato

**Q.** È necessario avere esito negativo su dispositivo a un altro dispositivo fisico. I volumi localmente bloccati Impossibile eseguire più aggiunte in locale o a più livelli?

**RISPOSTE.** A seconda della versione del software del dispositivo di destinazione, verranno eseguiti su volumi localmente bloccati come:

- In locale aggiunte se il dispositivo di destinazione è in esecuzione 8000 StorSimple serie aggiornare 2
- A più livelli se il dispositivo di destinazione è in esecuzione StorSimple 8000 serie aggiornare 1. x
- Livelli di se il dispositivo di destinazione è accessorio cloud (versione aggiornamento 2 o aggiornamento di 1. x)

Ulteriori informazioni sui [failover e DR di localmente aggiunte volumi tra le versioni](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Q.** Locale bloccati volumi vengono ripristinati istantaneamente durante il ripristino di emergenza (DR)?

**RISPOSTE.** Sì, in locale bloccati volumi vengono ripristinati istantaneamente durante il failover. Non appena le informazioni sui metadati per il volume vengono spostati dal cloud come parte dell'operazione di failover, il volume è in linea sul dispositivo e sono accessibili dall'host. Nel frattempo, i dati del volume continuerà a scaricare in background e può avere prestazioni ridotte su questi volumi per la durata del failover.

**Q.** Visualizzare il lavoro failover completato, come è possibile tenere traccia dello stato di avanzamento del volume localmente bloccata da ripristinare sul dispositivo?

**RISPOSTE.** Durante l'operazione di failover, il processo di failover viene contrassegnato come completare una sola volta tutti i volumi nel set di failover sono stati istantaneamente ripristinati e in linea sul dispositivo. Sono inclusi i volumi localmente bloccati non riusciti del percorso. Tuttavia, garanzie locale dei dati saranno disponibili solo dopo aver scaricato tutti i dati per il volume. È possibile avanzamento per ogni volume localmente bloccata non riuscito da monitoraggio processi di ripristino corrispondenti vengono create come parte del failover. Questi processi di ripristino singoli verranno creati solo per indicare i volumi localmente bloccati.

**Q.** È possibile modificare il tipo di un volume durante il failover?

**RISPOSTE.** No, non è possibile modificare il tipo di volume durante caso di errore. Se hanno esito negativo su in un altro dispositivo fisico che esegue StorSimple 8000 serie aggiornamento 2, i volumi verranno eseguiti su in base al tipo di volume memorizzato nello snapshot. In caso di problemi su qualsiasi altra versione di dispositivo, consultare la domanda di sopra del tipo di volume dopo caso di errore.

**Q.** È possibile non su un contenitore di volume con volumi localmente bloccati all'accessorio cloud?

**RISPOSTE.** Sì, è possibile. I volumi localmente bloccati verranno eseguiti su come volumi a più livelli. Ulteriori informazioni sui [failover e DR di localmente aggiunte volumi tra le versioni](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


