<properties 
   pageTitle="Limiti di sistema StorSimple | Microsoft Azure"
   description="Descrive i limiti di sistema e le dimensioni consigliate per le connessioni e componenti della serie 8000 StorSimple."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="storsimple-system-limits"></a>Limiti di sistema StorSimple

## <a name="overview"></a>Panoramica

StorSimple fornisce scalable e flessibile dello spazio di archiviazione per i Data Center. Tuttavia, esistono alcune limitazioni da tenere in considerazione durante la pianificazione, distribuisce e utilizzare la soluzione StorSimple. Nella tabella seguente descrive i limiti e vengono forniti alcuni suggerimenti in modo che è possibile sfruttare al meglio la soluzione StorSimple.

| Limite identificatore | Limite | Commenti |
|----------------- | ------|--------- |
| Numero massimo di credenziali dell'account di archiviazione | 64 | |
| Numero massimo di contenitori volume | 64 | |
| Numero massimo di volumi | 255 | |
| Numero massimo di volumi localmente bloccati | 32 | |
| Numero massimo di pianificazioni per il modello di larghezza di banda | 168 | Pianificazione per ogni ora, ogni giorno della settimana (24 * 7). |
| Dimensione massima di un volume a più livelli in dispositivi fisici | 64 TB per 8100 e 8600 | 8100 e 8600 sono dispositivi fisici. |
| Dimensione massima di un volume a più livelli in dispositivi virtuali Azure | 30 TB per 8010 <br></br> 64 TB per 8020 | 8010 e 8020 sono dispositivi virtuali di Azure che utilizzano lo spazio di archiviazione Standard e lo spazio di archiviazione Premium rispettivamente. |
| Dimensione massima di un volume localmente bloccato su dispositivi fisici | 8,5 TB per 8100 <br></br> 22,5 TB per 8600 | 8100 e 8600 sono dispositivi fisici. |
| Numero massimo di connessioni iSCSI | 512 | |
| Numero massimo di connessioni iSCSI da iniziatori | 512 | |
| Numero massimo di record di controllo di accesso al dispositivo | 64 | |
| Numero massimo di volumi per criteri di backup | 24 | |
| Numero massimo di backup conservate pianificazione (in un criterio di backup) | 64 | |
| Numero massimo di pianificazioni per criteri di backup | 10 | |
| Numero massimo di snapshot di qualsiasi tipo che possono essere conservate volume | 256 | Questo numero include le istantanee locali e cloud istantanee. |
| Numero massimo di istantanee possono essere presenti in qualsiasi dispositivo | 10.000 | |
| Numero massimo di volumi che possono essere elaborati in parallelo per il backup, ripristino o duplicare | 16 |<ul><li>Se sono presenti più di 16 volumi, vengono elaborati in sequenza come bande orarie elaborazione diventano disponibili.</li><li>Nuovi backup di un duplicato o un volume a più livelli ripristinato non può verificarsi fino al completamento dell'operazione. Per un volume locale, tuttavia, l'esecuzione di backup sono consentiti dopo il volume sia online.</li></ul>|
| Ripristino e Clona recuperare ora per indicare i volumi a più livelli | < 2 minuti | <ul><li>Il volume viene reso disponibile all'interno di 2 minuti dell'operazione di ripristino o duplicato, indipendentemente dalle dimensioni del volume.</li><li>Prestazioni del volume inizialmente potrebbero essere più lenta rispetto al normale come la maggior parte dei dati e metadati rimarrà nel cloud. Le prestazioni possono aumentare come flussi di dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale per il download dei metadati dipende dalle dimensioni allocata volume. Il dispositivo in background il tasso di 5 minuti per TB di dati del volume allocato automaticamente messa metadati. Questa frequenza potrebbe incidono della larghezza di banda Internet nel cloud.</li><li>L'operazione di ripristino o duplicato è stata completata quando tutti i metadati sono attivato il dispositivo.</li><li>Non è possibile eseguire le operazioni di backup finché il ripristino o duplicato operazione è completa.|
| Ripristinare recuperare ora per indicare i volumi localmente bloccati | < 2 minuti | <ul><li>Il volume è disponibile all'interno di 2 minuti dell'operazione di ripristino, indipendentemente dalle dimensioni del volume.</li><li>Prestazioni del volume inizialmente potrebbero essere più lenta rispetto al normale come la maggior parte dei dati e metadati rimarrà nel cloud. Le prestazioni possono aumentare come flussi di dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale per il download dei metadati dipende dalle dimensioni allocato volume. Il dispositivo in background il tasso di 5 minuti per TB di dati del volume allocato automaticamente messa metadati. Questa frequenza potrebbe incidono della larghezza di banda Internet nel cloud.</li><li>Diversamente da quanto succede volumi a più livelli, per indicare i volumi localmente bloccati, i dati del volume anche viene scaricati in locale nel dispositivo. L'operazione di ripristino è stata completata quando tutti i dati del volume è stato attivato il dispositivo.</li><li>Le operazioni di ripristino potrebbero essere lunga. Il tempo totale per completare il ripristino dipenderà le dimensioni del volume locale provisioning, la larghezza di banda Internet e i dati esistenti nel dispositivo. Operazioni di backup del volume localmente bloccata consentite durante l'operazione di ripristino.|
|Frequenza di elaborazione per snapshot cloud| 15 minuti/TB | <ul><li>Tempo minimo per rendere il cloud snapshot pronto per il caricamento, per TB di dati allocato volume di backup. </li><li> Ora snapshot cloud totale viene calcolata aggiungendo questa volta snapshot tempi di caricamento, che dipende dalla larghezza di banda Internet nel cloud.
| Velocità di lettura/scrittura numero massimo di client (quando served dal livello SSD) * | 920/720 MB/s con un'unica interfaccia di rete GbE 10 | Fino a 2 x con MPIO e due interfacce di rete. |
| Velocità di lettura/scrittura numero massimo di client (quando served dal livello del disco rigido) * | 120/250 MB/s |
| Numero massimo di client lettura/scrittura effettiva (quando served dal livello cloud)* per l'aggiornamento 3 e versioni successive** | 40/60 MB/s per a più livelli volumi<br><br>60/80 MB/s per a più livelli volumi con l'opzione archiviazione selezionata durante la creazione del volume | Velocità di lettura dipende dal client generare e mantenere sufficienti antero-posteriore coda i/o. <br><br>Velocità ottenuto dipende la velocità di un account di archiviazione sottostante utilizzato. | 

& #42; Massima produttività per ogni tipo è stato misurato al 100% in lettura e scrittura al 100% scenari. Velocità effettiva può essere inferiore e dipende i/o crearne uno e condizioni di rete.

& #42; & #42; I numeri delle prestazioni prima dell'aggiornamento 3 possono essere inferiori.

## <a name="next-steps"></a>Passaggi successivi

Verificare i [requisiti di sistema StorSimple](storsimple-system-requirements.md). 
