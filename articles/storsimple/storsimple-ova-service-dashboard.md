<properties 
   pageTitle="Dashboard del servizio di gestione StorSimple - matrice virtuale | Microsoft Azure"
   description="Descrive il dashboard del servizio di gestione StorSimple e viene spiegato come utilizzare per monitorare l'integrità della matrice virtuale StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Utilizzare il dashboard di servizio di gestione StorSimple per la matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

La pagina di dashboard del servizio di gestione StorSimple fornisce una visualizzazione di riepilogo delle StorSimple virtuale matrici (noto anche come StorSimple locale dispositivi virtuali o dispositivi virtuali) che sono connessi al servizio Gestione StorSimple evidenziazione quelle che richiedono l'attenzione dell'amministratore di sistema. In questa esercitazione introduce pagina del dashboard, viene illustrato il contenuto di dashboard e la funzione e sono descritte le attività che è possibile eseguire da questa pagina.

![Dashboard del servizio](./media/storsimple-ova-service-dashboard/dashboard1.png)

Dashboard di servizio di gestione StorSimple vengono visualizzate le informazioni seguenti:

- Nell'area del **grafico** nella parte superiore della pagina, è possibile visualizzare le metriche pertinenti per i dispositivi virtuali. È possibile visualizzare l'archiviazione primario usato da tutti i dispositivi virtuali, come lo spazio di archiviazione cloud consumata da dispositivi virtuali per un periodo di tempo. Usare i controlli nell'angolo superiore destro del grafico per specificare l'utilizzo di relativo o assoluto e per vedere una scala 1 settimana, 1 mese, 3 mesi o anni 1 ora. Utilizzare il controllo Aggiorna ![controllo aggiornamento](./media/storsimple-ova-service-dashboard/refresh-control.png) per aggiornare il grafico.

- **Panoramica di utilizzo** Mostra lo spazio di archiviazione principale che è stato effettuato il provisioning e consumata da tutti i dispositivi virtuali rispetto spazio di archiviazione totale disponibile con tutti i dispositivi virtuali. **Provisioned** indica la quantità di spazio di archiviazione che preparato e allocato per l'utilizzo, fa riferimento **utilizzato** per l'utilizzo della condivisioni o volumi come viene visualizzato da iniziatori connessi per i dispositivi virtuali e **capacità max** Mostra la capacità massima di tutti i dispositivi virtuale.

- Nell'area **avvisi** è disponibile uno snapshot di tutti gli avvisi attivi da tutti i dispositivi virtuali, raggruppati in base alla gravità. Scegliere il livello di gravità apre la pagina **avvisi** , nell'ambito per visualizzare gli avvisi. Nella pagina **avvisi** , fare clic su un singolo avviso per visualizzare ulteriori dettagli su tale avviso, inclusi eventuali azioni consigliate. Se è stato risolto il problema, è inoltre possibile cancellare l'avviso.

- L'area di **processi** offre uno snapshot dei processi recenti da tutti i dispositivi virtuali connessi al servizio. Sono disponibili collegamenti che è possibile utilizzare per esaminare i processi attualmente in corso e quelli che ha avuto esito positivo o negativo nelle ultime 24 ore. 

- Area **occhio** a destra della pagina vengono fornite informazioni utili, ad esempio lo stato dei servizi, numero totale di virtuali dispositivi connessi al servizio, posizione del servizio e i dettagli dell'abbonamento a cui è associato al servizio. È inoltre disponibile un collegamento al log operazioni. Fare clic sul collegamento per visualizzare un elenco di tutte le operazioni di servizio StorSimple Manager completata. 

È possibile utilizzare la pagina di dashboard del servizio di gestione di StorSimple per avviare le attività seguenti:

- È possibile ottenere la chiave di registrazione del servizio.
- Visualizzare i registri di operazione.

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio

La chiave di registrazione del servizio è utilizzata per registrare un dispositivo virtuale StorSimple con il servizio di gestione StorSimple, in modo che il dispositivo viene visualizzato nel portale di classica Azure per ulteriori operazioni di gestione. Il tasto viene creato sul primo dispositivo virtuale e condivise con i dispositivi virtuali rimanenti. 

Fare clic su **Chiave di registrazione** (nella parte inferiore della pagina), verrà visualizzata la finestra di dialogo **Chiave di registrazione del servizio** in cui è possibile copiare il codice di registrazione servizio corrente negli Appunti o rigenerare la chiave di registrazione del servizio.

![chiave di registrazione](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Rigenerare il tasto non ha alcun effetto di dispositivi virtuali registrati in precedenza: influisce solo i dispositivi virtuali sono registrati con il servizio dopo il tasto rigenerazione.

Per ulteriori informazioni su come ottenere la chiave di registrazione del servizio, passare a [Ottieni la chiave di registrazione del servizio](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Visualizzare i registri di operazioni

È possibile visualizzare i registri di operazione, selezionando il collegamento log operazione disponibile nel riquadro **visualizzazione rapida** del dashboard. Verrà visualizzata la pagina di servizi di gestione, in cui è possibile filtrare e visualizzare i registri specifici per il servizio di gestione StorSimple.

![Registro operazioni](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare l'interfaccia utente per amministrare la matrice virtuale StorSimple web locale](storsimple-ova-web-ui-admin.md).