<properties 
   pageTitle="Monitorare il dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare il servizio di gestione di StorSimple per eseguire il monitoraggio delle prestazioni, la capacità di utilizzo, velocità di rete e prestazioni dispositivo i/o."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Utilizzare il servizio di gestione di StorSimple per controllare il dispositivo StorSimple 

## <a name="overview"></a>Panoramica

È possibile utilizzare il servizio di gestione di StorSimple per monitorare dispositivi specifici all'interno della soluzione StorSimple. È possibile creare grafici personalizzati basati sui / o le prestazioni, la capacità di utilizzo, velocità di rete e metriche delle prestazioni dispositivo. 

Per visualizzare le informazioni di monitoraggio di un dispositivo specifico, nel portale di classica Azure, selezionare il servizio di gestione StorSimple. Fare clic sulla scheda **Monitor** e quindi selezionare nell'elenco dei dispositivi. La pagina **Monitor** contiene le informazioni seguenti.

## <a name="io-performance"></a>Prestazioni i/o 

**Prestazioni i/o** tracce metriche correlati al numero di leggere e scrivere operazioni tra le interfacce iniziatore iSCSI nel server host e il dispositivo o il dispositivo e nel cloud. Queste prestazioni possono essere misurate per un volume specifico, un contenitore volume specifico o tutti i contenitori di volume.

Il grafico seguente mostra i/o per l'iniziatore al dispositivo per tutti i volumi per un dispositivo di produzione. Metrica tracciata vengono letti e scrittura byte al secondo, legge e scrittura operazioni IO al secondo, leggere e scrittura latenza.

![Prestazioni di IO da iniziatore al dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Per la stessa periferica delle operazioni di vengono tracciate per i dati dal dispositivo nel cloud per tutti i contenitori di volume. Nel dispositivo, i dati sono solo nel livello lineare e non sono stati inseriti vuoti nel cloud. Non sono presenti operazioni di lettura e scrittura si verifichi dal dispositivo nel cloud. Pertanto, picchi nel grafico sono in un intervallo di 5 minuti che corrisponde alla frequenza con cui sia selezionata l'heartbeat tra il dispositivo e il servizio. 

![Prestazioni di IO dal dispositivo nel cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Per la stessa periferica uno snapshot cloud intrapresa per i dati del volume iniziando 2:00 pm. Questo condotto alla visualizzazione dati che passano dal dispositivo nel cloud. Lettura scrittura sono stati resi disponibili nel cloud in questa durata. Il grafico IO Mostra un picco in vari criteri di misurazione corrispondente all'ora durante la creazione dello snapshot. 

![Prestazioni di IO per dispositivo per il cloud dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Utilizzo della capacità 

**La capacità di utilizzo** di tiene traccia delle metriche relativi alla quantità di spazio di archiviazione di dati utilizzato per i volumi, contenitori volume o dispositivo. È possibile creare report basati sull'utilizzo della capacità dello spazio di archiviazione principale, lo spazio di archiviazione cloud o lo spazio di archiviazione di dispositivo. Utilizzo della capacità può essere misurato in un volume specifico, un contenitore volume specifico o tutti i contenitori di volume.


Cloud principale, e capacità di archiviazione dispositivo possono essere descritti come indicato di seguito:

###<a name="primary-storage-capacity-utilization"></a>Utilizzo della capacità di archiviazione principale
 
Questi grafici mostrano la quantità di dati scritti volumi StorSimple prima che i dati sono deduplicated e compressa. È possibile visualizzare l'utilizzo di memoria principale da tutti i volumi o per un singolo volume.

Quando si visualizza i grafici di utilizzo capacità di archiviazione principale volume per tutti i volumi rispetto a ognuno dei singoli volumi e riepilogare i dati principali in entrambi i casi, è possibile mancata corrispondenza tra i due numeri. I totali delle primari su tutti i volumi potrebbero non aggiungere il totale dei dati principali dei singoli volumi. È possibile che sia una delle operazioni seguenti:

- **Dati di snapshot inclusi per tutti i volumi**: questo problema si verifica solo se è in esecuzione una versione precedente a 3 di aggiornamento. I dati primari visualizzati per tutti i volumi sono la somma dei dati primario per ogni volume e i dati di snapshot. I dati primari visualizzati per un determinato volume corrisponde alla quantità di dati allocati sul volume (e non includano i dati di snapshot volume corrispondenti).

    Questa operazione può essere spiegata tramite l'equazione seguente:

    *Dati primario (tutti i volumi) = somma (dati principali (volume i) e dimensioni dei dati istantanea (volume i))*
    
    *dove, dati primario (volume i) = dimensione dei dati principali allocati per volume i*
 
    Se le istantanee vengono eliminate tramite il servizio, l'eliminazione viene eseguita in modo asincrono in background. Può richiedere del tempo per la dimensione del volume dati aggiornati dopo l'eliminazione di snapshot. 

    Se in esecuzione aggiornamento 3 o versione successiva, i dati dello snapshot non è incluso nei dati del volume. E l'utilizzo principale viene calcolata nel modo seguente:

    * Dati principale (tutti i volumi) = somma (dati primario (volume i)
    
    *dove, dati primario (volume i) = dimensioni dei dati principali allocati per volume i*
 
- **Volumi con monitoraggio disabilitato inclusi in tutti i volumi**: se si dispone di volumi in un dispositivo per il quale monitoraggio è disattivato, i dati di monitoraggio per i singoli volumi non sarà disponibili nei grafici. Tuttavia, i dati per tutti i volumi il grafico includono volumi per il quale monitoraggio è disattivato. 
 
- **Eliminare volumi con live backup associato inclusi per tutti i volumi**: se vengono eliminati volumi contenenti dati dello snapshot ma istantanee associate sono ancora presenti, quindi venga visualizzato un non corrispondente.

- **Volumi eliminati inclusi per tutti i volumi**: In alcuni casi, volumi precedenti possono esistere anche se questi sono stati eliminati. Non è visibile l'effetto dell'eliminazione e il dispositivo risulti inferiore capacità disponibile. È necessario contattare il supporto Microsoft per rimuovere i volumi.

I grafici seguenti mostrano l'utilizzo della capacità di archiviazione principale di un dispositivo StorSimple prima e dopo un'istantanea cloud. Come si tratta solo i dati del volume, uno snapshot cloud non modificare lo spazio di archiviazione principale. Come si può notare, il grafico viene visualizzata alcuna differenza utilizzo capacità principale in seguito a un'istantanea cloud. Snapshot cloud avviato circa 2:00 PM sul dispositivo.

![Utilizzo della capacità principale prima snapshot cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilizzo della capacità primaria dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Se si esegue aggiornare 2 o versioni successive, è possibile suddividere l'utilizzo della capacità di archiviazione principale volume singolo, tutti i volumi, tutti i volumi a più livelli e tutti i volumi localmente bloccati come illustrato di seguito. Suddivisione per tutti i volumi localmente bloccati consentono di determinare rapidamente la quantità di livello locale viene utilizzata.

![Utilizzo della capacità principale per tutti i volumi localmente bloccati](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Utilizzo della capacità di archiviazione cloud

Questi grafici mostrano la quantità di spazio di archiviazione cloud utilizzato. Questi dati vengono deduplicated e compressa. Questo importo include snapshot cloud che possono contenere dati che non sono riflesse in qualsiasi volume principale e conservati ai fini della conservazione legacy o necessari. È possibile confrontare primario e cloud figure consumo dello spazio di archiviazione per avere un'idea della velocità di riduzione dei dati, anche se il numero non saranno esatto. I grafici seguenti mostrano l'utilizzo di capacità di archiviazione cloud di un dispositivo StorSimple prima e dopo un'istantanea cloud. Snapshot cloud avviata circa 2:00 pm sul dispositivo ed è possibile visualizzare l'utilizzo della capacità cloud riportata di seguito nello stesso momento crescenti da MB 5.73 a 4.04 GB.

![Utilizzo della capacità cloud prima snapshot cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilizzo della capacità cloud dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Utilizzo della capacità di archiviazione dispositivo

Questi grafici mostrano l'utilizzo totale per il dispositivo, sarà spazio di archiviazione più primaria perché include il livello di lineare SSD. Questo livello contiene una quantità di dati presente anche per il dispositivo di altri livelli. La capacità nel livello lineare SSD passa in modo che quando arriva nuovi dati, i vecchi dati viene spostati al livello del disco rigido (momento in cui è deduplicated e compressi) e successivamente nel cloud.

Nel tempo, la capacità principale di utilizzo e l'utilizzo delle capacità dispositivo è probabile che aumenterà insieme fino all'inizio dei dati in livelli al cloud. A questo punto, l'utilizzo della capacità di dispositivo probabilmente inizieranno a zona piatta, ma aumenta l'utilizzo della capacità principale durante la scrittura di altri dati.

I grafici seguenti mostrano l'utilizzo della capacità di archiviazione principale di un dispositivo StorSimple prima e dopo un'istantanea cloud. Guida introduttiva snapshot cloud 2:00 PM e l'utilizzo della capacità di dispositivo avviato riducendo in quel momento. L'utilizzo della capacità di archiviazione dispositivo arrestato da GB 11.58 a 7.48 GB. Indica che è probabile che i dati non compressi nel livello SSD lineare è stata deduplicated compresso e spostati nel livello del disco rigido. Si noti che se il dispositivo ha già una grande quantità di dati nei livelli SSD sia il disco rigido, potrebbe non essere visualizzato questo Riduci. In questo esempio, il dispositivo ha una piccola quantità di dati.

![Utilizzo della capacità di dispositivo prima snapshot cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilizzo della capacità di dispositivo dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Velocità di rete

**Velocità di rete** tiene traccia delle metriche relativi alla quantità di dati trasferiti dalle interfacce di rete iniziatore iSCSI nel server host e il dispositivo e tra il dispositivo e nel cloud. È possibile controllare la metrica, per ognuna delle interfacce di rete iSCSI nel dispositivo.

I grafici seguenti mostrano la velocità di rete per i dati 0 4 di dati, entrambi 1 GbE interfacce di rete e nel dispositivo. In questo caso, dati 0 è stato abilitato al cloud mentre 4 dati è stata abilitata iSCSI. È possibile visualizzare l'in entrata e il traffico in uscita per il dispositivo StorSimple. Linea piatta nel grafico a partire dal 3:24 pm è dovuti al fatto che viene raccogliere dati ogni 5 minuti e deve essere ignorati. 

![Velocità di rete per Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Velocità di rete per Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Prestazioni dispositivo 

**Prestazioni dispositivo** tiene traccia delle metriche relative alle prestazioni del dispositivo. Il diagramma seguente mostra le statistiche di utilizzo della CPU per un dispositivo di produzione.

![Utilizzo della CPU per dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare il dashboard di dispositivo del servizio di gestione StorSimple](storsimple-device-dashboard.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
