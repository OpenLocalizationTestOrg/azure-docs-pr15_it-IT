<properties
   pageTitle="Utilizzare il dashboard device Manager StorSimple | Microsoft Azure"
   description="Descrive il dashboard di dispositivo del servizio di gestione StorSimple e come utilizzarlo per visualizzare metriche di archiviazione e iniziatori connessi e trovare il numero seriale e IQN."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-device-dashboard"></a>Utilizzare il dashboard di dispositivo StorSimple Manager

## <a name="overview"></a>Panoramica

Dashboard di dispositivo StorSimple Manager offre una panoramica delle informazioni per un dispositivo StorSimple specifico, a differenza di dashboard del servizio, che offre informazioni relative a tutti i dispositivi inclusi nella soluzione Microsoft Azure StorSimple.

![Pagina dashboard dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Il dashboard contiene le informazioni seguenti:

- **Area del grafico** , è possibile visualizzare le metriche di archiviazione pertinenti nell'area del grafico nella parte superiore del dashboard. In questo grafico, è possibile visualizzare metrica per le principale spazio di archiviazione totale (la quantità di dati scritti da host al dispositivo) e lo spazio di archiviazione totale cloud consumate dal dispositivo in uso per un periodo di tempo.

     In questo contesto si riferisce alla quantità totale di dati scritti dall'host *principale dello spazio di archiviazione* e possono essere suddivise in base al tipo di volume: *principale dello spazio di archiviazione a più livelli* include sia i dati archiviati in locale e dati a più livelli nel cloud; *primaria localmente aggiunte lo spazio di archiviazione* include solo i dati archiviati in locale. *Spazio di archiviazione cloud*, invece, è una misura dell'importo totale dei dati archiviati nel cloud. Sono inclusi dati a più livelli e backup. Si noti che i dati archiviati nel cloud sono deduplicated e compressa, mentre lo spazio di archiviazione principale indica la quantità di spazio di archiviazione utilizzato prima che i dati sono deduplicated e compressa. (È possibile confrontare questi due numeri per avere un'idea della velocità di compressione). Per entrambi principale e lo spazio di archiviazione cloud, gli importi verrà impostata in configurare la frequenza di verifica. Ad esempio, se si sceglie una frequenza di una settimana, il grafico verrà visualizzato dati per ogni giorno della settimana precedente.

     È possibile configurare il grafico come indicato di seguito:

     - Per visualizzare la quantità di spazio di archiviazione cloud consumata nel tempo, selezionare l'opzione **Utilizzata lo spazio di archiviazione CLOUD** . Per visualizzare lo spazio di archiviazione totale scritte dall'host, selezionare le opzioni **Principale a più livelli dello spazio di archiviazione utilizzato** e **primaria LOCALMENTE aggiunte lo spazio di archiviazione** . Nella figura, entrambe le opzioni sono selezionate. di conseguenza, il grafico mostra gli importi di spazio di archiviazione per cloud e lo spazio di archiviazione principale. Si noti che spazio di archiviazione principale utilizzato prima di installare l'aggiornamento 2 è rappresentato dalla riga **Principale utilizzato lo spazio di archiviazione a più livelli** .
     - Usare il menu a discesa nell'angolo superiore destro del grafico per specificare un periodo di tempo 1 settimana, 1 mese, 3 mesi o anni 1. Si noti che il grafico principale viene aggiornato solo una volta al giorno e pertanto rifletteranno totali del giorno precedente.

     Per ulteriori informazioni, vedere [usare il servizio di gestione di StorSimple per controllare il dispositivo StorSimple](storsimple-monitor-device.md).

- **Panoramica di utilizzo** -nell'area **Panoramica di utilizzo** è possibile visualizzare la quantità di spazio di archiviazione principale utilizzato, la quantità di spazio di archiviazione provisioning e la capacità di archiviazione massima del dispositivo. Esaminando questi numeri di utilizzo per la maggior quantità di spazio di archiviazione è disponibile, è possibile visualizzare a colpo d'occhio se è necessario ottenere ulteriore spazio di archiviazione. Tenere presente che questa panoramica viene aggiornata ogni 15 minuti, a causa della differenza la frequenza di aggiornamento, può visualizzare numeri diversi da quelli visualizzata nell'area del grafico riportati sopra, che viene aggiornato ogni giorno. Per ulteriori informazioni, vedere [usare il servizio di gestione di StorSimple per controllare il dispositivo StorSimple](storsimple-monitor-device.md).


- **Avvisi** : l'area **avvisi** viene presentata una panoramica degli avvisi per il dispositivo. Avvisi raggruppati in base alla gravità e viene fornito un conteggio del numero di avvisi a ogni livello di gravità. Fare clic su gravità degli avvisi per aprire una visualizzazione dell'ambito della scheda avvisi per visualizzare solo gli avvisi di tale livello di gravità per il dispositivo.

- **Processi** : l'area di **processi** illustrato il risultato dell'attività di processo recente. Questa operazione possibile assicurare è che il sistema funziona come previsto oppure è possibile indicare che è necessario adottare misure correttive. Per visualizzare ulteriori informazioni sui processi completati di recente, fare clic su **processi esito positivo nelle ultime 24 ore**.

- Area **occhio** a destra del dashboard vengono fornite informazioni utili, ad esempio modello del dispositivo, il numero di serie, stato, descrizione e numero di volumi.

È anche possibile configurare failover e visualizzare iniziatori connessi nel dashboard di dispositivo.

Le attività comuni che possono essere eseguite in questa pagina sono:

- Visualizzare iniziatori connessi

- Calcolare il numero seriale di dispositivo

- Trovare la destinazione del dispositivo IQN

## <a name="view-connected-initiators"></a>Visualizzare iniziatori connessi

È possibile visualizzare gli iniziatori iSCSI connessi al dispositivo facendo clic sul collegamento **Visualizza connessi iniziatori** disponibile nell'area di **visualizzazione rapida** dei dashboard del dispositivo. Questa pagina per un elenco tabulare di iniziatori che sono stati connessi al dispositivo. Per ogni iniziatore, è possibile visualizzare:

- ISCSI completo nome iSCSI dell'utente connesso.

- Il nome del record di controllo di accesso (ACR) che consente l'iniziatore connesso.

- L'indirizzo IP dell'utente connesso.

- Le interfacce di rete che iniziatore sia connesso a in un dispositivo di archiviazione. Questi può variare da dati 0 a 5 di dati.

- Tutti i volumi iniziatore connesso è autorizzato ad accedere in base alla configurazione ACR corrente.

Se presente iniziatori imprevisti in questo elenco o non è visibile quelli previsti, rivedere la configurazione ACR. Un massimo di 512 iniziatori possibile connettersi al dispositivo.

## <a name="find-the-device-serial-number"></a>Calcolare il numero seriale di dispositivo

Potrebbe essere necessario il numero seriale di dispositivo quando si configura Microsoft percorsi multipli i/o (MPIO) nel dispositivo. Eseguire la procedura seguente per calcolare il numero seriale di dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Per calcolare il numero seriale di dispositivo

1. Passare a **dispositivi** > **Dashboard**.

2. Nel riquadro a destra del dashboard, individuare l'area di **visualizzazione rapida** .

3. Scorrere verso il basso e individuare il numero di serie.

## <a name="find-the-device-target-iqn"></a>Trovare la destinazione del dispositivo IQN

Potrebbe essere necessario il dispositivo di destinazione IQN quando si configura la richiesta di verifica CHAP Handshake Authentication Protocol () in un dispositivo StorSimple. Per trovare la destinazione del dispositivo IQN, procedere come segue.

### <a name="to-find-the-device-target-iqn"></a>Per trovare la destinazione del dispositivo IQN

1. Passare a **dispositivi** > **Dashboard**.

1. Nel riquadro a destra del dashboard, individuare l'area di **visualizzazione rapida** .

1. Scorrere verso il basso e individuare la destinazione IQN.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sui [dashboard del servizio di gestione StorSimple](storsimple-service-dashboard.md).
- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
