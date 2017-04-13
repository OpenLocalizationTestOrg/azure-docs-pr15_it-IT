<properties
    pageTitle="Guida introduttiva a Azure flusso Analitica per elaborare i dati da dispositivi IoT. | Microsoft Azure"
    description="Flussi IoT sensore tag e i dati con analitica flusso ed elaborazione dei dati in tempo reale"
    keywords="soluzione IOT, Guida introduttiva a iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Guida introduttiva a Azure flusso Analitica per elaborare i dati da dispositivi IoT

In questa esercitazione si imparerà a creare una logica di elaborazione del flusso per raccogliere dati da dispositivi Internet di elementi (IoT). Un caso di utilizzo di Internet di elementi (IoT) reali, verrà usata per dimostrare come compilare la soluzione rapidamente ed economico.

## <a name="prerequisites"></a>Prerequisiti

-   [Abbonamento Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Progettazione query e dati file di esempio scaricabili da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scenario:

Contoso è una società nell'area automazione industriali, è completamente automatizzato il processo di produzione. Macchina in questo stabilimento di produzione ha sensori che sono in grado di creazione di flussi di dati in tempo reale. In questo scenario, un responsabile di planimetrie di produzione richiede in tempo reale informazioni dettagliate sui dati sensore cercare modelli di eseguire azioni su di essi. Flusso Analitica Query Language (SAQL) sui dati sensore verrà usata per trovare i modelli interessanti dal flusso di posta in arrivo di dati.

Di seguito vengono generati i dati da un dispositivo di tag sensore strumenti Texas.

![Tag sensore strumenti Texas](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

Payload dei dati nel formato JSON e avrà l'aspetto seguente:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

In uno scenario reale, è possibile avere centinaia di questi sensori generazione di eventi come flusso. Se possibile, un dispositivo gateway da eseguire il codice per inviare questi eventi di [Azure evento hub](https://azure.microsoft.com/services/event-hubs/) o [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/). Il processo di flusso Analitica da acquisire questi eventi dall'hub evento ed eseguire query analitica in tempo reale con i flussi. Quindi, è possibile inviare i risultati a una delle [uscite è supportato](stream-analytics-define-outputs.md).

Per facilitare l'utilizzo, questa Guida introduttiva fornisce un file di dati di esempio, viene acquisito da dispositivi tag sensore reali. È possibile eseguire query sui dati di esempio e vedere i risultati. Nelle esercitazioni successive, verrà informazioni su come connettere il processo di input e genera e distribuirle al servizio di Azure.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di flusso Analitica

1. Nel [portale di Azure](http://portal.azure.com), fare clic sul segno più e quindi digitare **Le analisi di flusso** nella finestra di testo a destra. Selezionare quindi **Analitica flusso del processo** nell'elenco dei risultati.

    ![Creare un nuovo processo Analitica flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Immettere un nome univoco e verificare che l'abbonamento sia quello corretto per il lavoro. Quindi creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione.

3. Selezionare un percorso per il lavoro. Per la velocità di elaborazione e riduzione dei costi dei dati è consigliabile trasferimento selezionando nella stessa posizione come il gruppo di risorse e l'account di archiviazione desiderato.

    ![Creare un nuovo processo flusso Analitica dettagli](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] È consigliabile creare l'account di archiviazione solo una volta per ogni area geografica. Questo spazio di archiviazione verrà condivisa tra tutti i processi di flusso Analitica creata in quell'area.

4. Selezionare la casella per posizionare il processo nel dashboard e quindi fare clic su **Crea**.

    ![creazione di processi in corso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Verrà visualizzata una "Distribuzione avviata..." visualizzato nell'angolo superiore destro della finestra del browser. Non appena verrà modificato in una finestra completata come illustrato di seguito.

    ![creazione di processi in corso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Creare una query di Azure flusso Analitica

Dopo aver creato il lavoro è ora per aprirlo e creare una query. È possibile accedere facilmente il lavoro facendo clic sul riquadro per renderla.

![Riquadro processo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Nel riquadro **Della topologia di processo** fare clic su casella di **ricerca** per passare all'Editor di Query. Editor di **QUERY** consente di immettere una query T-SQL che esegue la trasformazione sopra i dati dell'evento in arrivo.

![Casella di ricerca](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Query: Archiviare i dati non elaborati

La forma più semplice di query è una query pass-through che consente di archiviare tutti i dati di input per l'output designato. Scaricare il file di dati di esempio da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) in un percorso nel computer in uso. 

1. Incollare la query dal file PassThrough.txt. 

    ![Flusso di input di test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Scegliere i puntini di sospensione accanto all'input e selezionare la casella di **caricare dati di esempio da file** .

    ![Flusso di input di test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. Verrà visualizzato un riquadro a destra di conseguenza, in selezionare il file di dati HelloWorldASA InputStream.json dal percorso di download e fare clic su **OK** nella parte inferiore del riquadro.

    ![Flusso di input di test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. Quindi fare clic su a forma di ingranaggio **testare** nella parte superiore sinistra dell'area della finestra ed elaborare della query test il set di dati di esempio. Durante l'elaborazione è stata completata, sotto la query verrà aperta una finestra di risultati.

    ![Risultati dei test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Query: Filtrare i dati in base a una condizione

Utilizzare filtrare i risultati in base a una condizione. Si desidera visualizzare i risultati solo per gli eventi che deriva dalla "sensorA". La query è nel file Filtering.txt.

![Il filtro di un flusso di dati](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Si noti che la query distinzione tra maiuscole e confronta un valore stringa. Fare clic su a forma di ingranaggio **Test** nuovamente per eseguire la query. La query deve restituire 389 righe fuori 1860 eventi.

![Secondo i risultati di output del test di query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Query: Avviso attivare un flusso di lavoro di business

Questo punto, eseguire la query più dettagliate. Per ogni tipo di sensore, si desidera eseguire il monitoraggio temperatura media per ogni 30 secondi finestra e visualizzare i risultati solo se la temperatura media è superiore al 100 gradi. Si verrà scrivere la query seguente e quindi fare clic su **Test** per visualizzare i risultati. La query è nel file ThresholdAlerting.txt.

![query del filtro 30 secondi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Verrà visualizzata risultati che contengono 245 solo i nomi dei sensori e le righe in cui la media temperata è maggiore di 100. La query raggruppa il flusso di eventi da **dspl**, ossia il nome del sensore sulla **Finestra lo scambio** di 30 secondi. Query temporali deve specificare come si desidera ora allo stato di avanzamento. Tramite la clausola **TIMESTAMP BY** specificati nella colonna **OUTPUTTIME** per associare volte a tutti i calcoli temporali. Per informazioni dettagliate, vedere gli articoli MSDN sulle [Gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [le finestre](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Query: Rilevare assenza di eventi

Come è possibile scrivere una query per trovare la mancanza di eventi di input? Di seguito individuare l'ultima volta che un sensore inviati dati e quindi non è stata inviata eventi per il minuto successivo. La query è nel file AbsenseOfEvent.txt.

![Rilevare assenza di eventi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Di seguito viene utilizzato un **LEFT OUTER** join per lo stesso flusso di dati (self-join). Per un **INNER** join, viene restituito un risultato solo quando viene trovata una corrispondenza.  Per un **LEFT OUTER** join, se un evento dal lato sinistro del join non corrispondente, viene restituita una riga che include NULL per tutte le colonne del lato destro. Questa tecnica è molto utile per trovare l'assenza di eventi. Vedere la documentazione di MSDN per ulteriori informazioni su [Partecipa](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusioni

Lo scopo di questa esercitazione è per dimostrare come scrivere query flusso Analitica Query Language diverse e visualizzare i risultati nel browser. Tuttavia, si è ancora. È possibile eseguire molte altre con Analitica flusso. Flusso Analitica supporta una varietà di input e genera e anche possibile utilizzare funzioni di apprendimento Azure per renderlo uno strumento efficace per l'analisi dei flussi di dati. È possibile iniziare a esplorare più sulle flusso Analitica utilizzando la [mappa di apprendimento](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Per ulteriori informazioni su come scrivere query, leggere l'articolo informazioni sui [modelli di query comuni](./stream-analytics-stream-analytics-query-patterns.md).
