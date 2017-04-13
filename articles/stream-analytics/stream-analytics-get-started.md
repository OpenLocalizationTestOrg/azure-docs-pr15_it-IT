<properties
    pageTitle="Guida introduttiva a flusso Analitica: rilevamento frodi in tempo reale | Microsoft Azure"
    description="Informazioni su come creare una soluzione di rilevamento frodi in tempo reale con flusso Analitica. Utilizzare un hub di evento per l'elaborazione di eventi in tempo reale."
    keywords="rilevamento delle anomalie in, rilevamento frodi, rilevamento di anomalie in tempo reale"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Introduzione all'utilizzo di Azure flusso Analitica: rilevamento frodi in tempo reale

Informazioni su come creare una soluzione-to-end per il rilevamento di frodi in tempo reale con Azure flusso Analitica. Visualizzare gli eventi a un hub evento Azure, scrivere query flusso Analitica per l'aggregazione o l'avviso e inviare i risultati un sink di output per ottenere informazioni sui dati con elaborazione in tempo reale. Rilevamento delle anomalie in tempo reale delle telecomunicazioni è coperto ma la tecnica di esempio è ideale per altri tipi di rilevamento di frode, ad esempio gli scenari di furto di carta di credito o identità.

Flusso Analitica è un servizio completamente gestito fornendo elaborazione dell'evento complesse bassa latenza elevata disponibilità, scalable sopra il flusso di dati nel cloud. Per ulteriori informazioni, vedere [Introduzione alle Azure flusso Analitica](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: Telecomunicazioni e Image del rilevamento delle frodi in tempo reale

Una società di telecomunicazione ha una grande quantità di dati per le chiamate in arrivo. La società deve seguenti con i dati seguenti:
* Confronta questi dati per difetto a una quantità gestibile e ottenere informazioni approfondite sul utilizzo al cliente nel tempo e aree geografiche.
* Consente di individuare frodi Image (più chiamate provenienti dalla stessa identità intorno all'ora, ma in posizioni diverse aree geografiche) in tempo reale in modo che rispondano facilmente tramite la notifica clienti o arrestare il servizio di sistema.

In canonici scenari di Internet di elementi (IoT) sono grandi quantità di dati di telemetria o sensore viene generata – e clienti li riunisce o un avviso tramite alterazioni in tempo reale.

## <a name="prerequisites"></a>Prerequisiti di

- Scaricare [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'area Download Microsoft 
- Facoltativo: Codice sorgente del generatore di evento da [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Creare un hub di Azure evento di input e Consumer gruppo

L'applicazione di esempio genererà eventi e inviarli a un'istanza di evento Hub per l'elaborazione in tempo reale. Ulteriori informazioni sull'hub di evento nella [documentazione Bus di servizio Azure](/documentation/services/service-bus/)servizio Bus evento hub sono il metodo di acquisizione di eventi per Analitica flusso preferito.

Per creare un Hub di evento:

1.  Nel [portale di Azure](https://manage.windowsazure.com/) fare clic su **Nuovo** > **Servizi App** > **Servizio Bus** > **Evento Hub** > **Creazione rapida**. Specificare un nome, l'area geografica e nomi di nuovo o esistenti per creare un nuovo evento Hub.  
2.  Si consiglia, ogni processo Analitica flusso contiene informazioni interessanti di un singolo gruppo Consumer Hub di evento. Abbiamo illustrano le fasi del processo di creazione di un gruppo di Consumer riportata di seguito ed è possibile [ottenere ulteriori informazioni sui gruppi di consumatori](https://msdn.microsoft.com/library/azure/dn836025.aspx). Per creare un gruppo di Consumer, passare Hub evento appena creato e fare clic sulla scheda **Gruppi di consumatori** , quindi fare clic su **Crea** nella parte inferiore della pagina e specificare un nome per il gruppo Consumer.
3.  Per concedere l'accesso all'evento Hub, sarà necessario creare un criterio di accesso condiviso.  Fare clic sulla scheda **Configura** dell'Hub evento.
4.  In **Criteri di accesso condivisi**, creare un nuovo criterio di **gestire** le autorizzazioni.

    ![Condividere criteri di accesso in cui è possibile creare un criterio di gestire le autorizzazioni.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  Fare clic su **Salva** nella parte inferiore della pagina.
6.  Passare al **Dashboard** e fare clic su **Informazioni di connessione** nella parte inferiore della pagina, quindi copiarlo e salvare le informazioni di connessione.

## <a name="configure-and-start-event-generator-application"></a>Configurare e avviare applicazioni di generatore di evento

Vengono fornite un'applicazione client che generano metadati chiamata in arrivo di esempio e push a Hub di evento. Seguire la procedura seguente per configurare l'applicazione.  

1.  Scaricare il [file TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Quindi decomprimere in una directory.

    **Nota**: Windows potrebbe bloccare il file zip scaricati. Fare clic con il pulsante destro del file e scegliere Proprietà. Se il messaggio "il file proviene da un altro computer e potrebbe essere bloccato per proteggere il computer." quindi selezionare la casella "Sblocca" e fare clic su Applica sul file zip.

2.  Sostituire i valori Microsoft.ServiceBus.ConnectionString ed EventHubName in **telcodatagen.exe.config** con il nome e la stringa di connessione Hub evento.

    **Nota**: la stringa di connessione copiato le posizioni portale Azure il nome della connessione alla fine. Assicurarsi di rimuovere la "; EntityPath =<value>"dal tasto = campo.

3.  Avviare l'applicazione. L'utilizzo consiste nel modo seguente:

   telcodatagen.exe [#NumCDRsPerHour] [Image scheda frode probabilità] [#DurationHours]

Nell'esempio seguente genererà gli eventi di 1000 con una probabilità di 20% di frode complessivamente nel corso di 2 ore.

    telcodatagen.exe 1000 .2 2

Verranno visualizzati record inviato all'Hub di evento. Alcuni campi chiave che verranno utilizzate in questa applicazione di rilevamento frodi in tempo reale sono definiti di seguito:

| Record | Definizione |
| ------------- | ------------- |
| CallrecTime | Ora di inizio timestamp per la chiamata. |
| SwitchNum | Telefonico utilizzato per eseguire la connessione. |
| CallingNum | Numero di telefono del chiamante. |
| CallingIMSI | Identità sottoscrittore cellulare internazionale (IMSI).  Identificatore univoco del chiamante. |
| CalledNum | Numero di telefono del destinatario della chiamata Visualizza. |
| CalledIMSI | Identità sottoscrittore cellulare internazionale (IMSI).  Identificatore univoco del destinatario chiamata. |


## <a name="create-stream-analytics-job"></a>Creare Analitica flusso processo
Ora che si dispone di un flusso di eventi di telecomunicazione, è possibile impostare un processo Analitica flusso analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Effettuare il provisioning di un processo di flusso Analitica

1.  Nel portale di Azure, fare clic su **Nuovo > servizi dati > flusso Analitica > Creazione rapida**.
2.  Specificare i valori seguenti e quindi fare clic su **Crea flusso Analitica processo**:

    * **Nome del processo**: immettere un nome di processo.

    * **L'area geografica**: selezionare l'area in cui si vuole eseguire il processo. È consigliabile inserire il processo e l'hub di evento nella stessa regione per garantire prestazioni migliori e per garantire che si verrà non acquistati trasferire dati tra aree geografiche.

    * **Account di archiviazione**: scegliere l'account di archiviazione Azure che si desidera utilizzare per archiviare i dati di monitoraggio per tutti i processi di flusso Analitica in esecuzione all'interno di quest'area. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.

3.  Fare clic su **Flusso Analitica** nel riquadro a sinistra per visualizzare un elenco di processi flusso Analitica.

    ![Icona di flusso Analitica servizio](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  Verrà visualizzato il nuovo processo con lo stato **creato**. Si noti che viene disabilitato dal pulsante **Start** nella parte inferiore della pagina. È necessario configurare il processo input, output e query prima di iniziare il processo.

### <a name="specify-job-input"></a>Specificare l'input di processo
1.  Il processo di flusso Analitica fare clic su **input** nella parte superiore della pagina e quindi fare clic su **Aggiungi Input**. Nella finestra di dialogo visualizzata consentirà numerosi passaggi per configurare l'input.
2.  Selezionare **flusso di dati**e quindi fare clic sul pulsante a destro.
3.  Selezionare **Hub evento**e quindi scegliere il pulsante destro.
4.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **Alias Input**: immettere un nome descrittivo per il processo di input, ad esempio *CallStream*. Si noti che si userà il nome della query in un secondo momento.
    * **Hub di evento**: se l'Hub di evento creato nello stesso abbonamento il processo di flusso Analitica, selezionare lo spazio dei nomi di hub evento.

    Se l'hub di evento in una sottoscrizione diversa, selezionare **Usa evento Hub da un'altra sottoscrizione** e immettere manualmente le informazioni per ** **Servizio Bus Namespace**evento Hub Name**, **Nome del criterio Hub evento**, **Chiave dei criteri di evento Hub**e **Conteggio partizione Hub eventi**.

    * **Nome dell'evento Hub**: selezionare il nome dell'evento Hub.

    * **Nome del criterio Hub evento**: selezionare il criterio di evento hub creato in precedenza in questa esercitazione.

    * **Gruppo di eventi Hub Consumer**: specificare il gruppo di Consumer creata in precedenza in questa esercitazione.
5.  Scegliere il pulsante destro.
6.  Specificare i valori seguenti:

    * **Formato serializzatore evento**: JSON
    * **Codifica**: UTF8
7.  Fare clic sul pulsante di controllo per aggiungere questa origine e verificare che flusso Analitica è possibile connettersi all'hub di evento.

### <a name="specify-job-query"></a>Specificare query processo

Flusso Analitica supporta un modello di query semplice e dichiarativo per descrivere le trasformazioni per l'elaborazione in tempo reale. Per ulteriori informazioni sull'utilizzo del linguaggio, vedere [Riferimenti per Azure flusso Analitica Query Language](https://msdn.microsoft.com/library/dn834998.aspx). In questa esercitazione consente di creare e verificare diverse query per il flusso in tempo reale di dati di chiamata.

#### <a name="optional-sample-input-data"></a>Facoltativi: Dati di input di esempio
Per convalidare la query sui dati effettivo del processo, è possibile utilizzare la caratteristica di **Dati di esempio** per estrarre eventi dal flusso di e creare un. File di JSON degli eventi di verifica.  La procedura seguente mostra come eseguire questa operazione e sono disponibili anche un file di esempio [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) a scopo di testing.

1.  Selezionare l'evento Hub di input e fare clic su **Dati di esempio** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata, specificare un' **Ora di inizio** per avviare la raccolta di dati e una **durata** per la quantità di dati per l'utilizzo.
3.  Fare clic sul pulsante di controllo per iniziare a dati di esempio dell'input.  Può essere necessaria un o due minuti per il file di dati da creare.  Una volta completata la procedura, fare clic su **Dettagli** , scaricare e salvare la. File JSON generato.

    ![Scaricare e salvare i dati trasformati in un file JSON](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>Query pass-through

Se si desidera archiviare tutti gli eventi, è possibile utilizzare una query pass-through per leggere tutti i campi payload del messaggio o all'evento. Per iniziare, eseguire una query pass-through semplice che tutti i campi di un evento i progetti.

1.  Fare clic su **Query** nella parte superiore della pagina processo flusso Analitica.
2.  Aggiungere quanto segue all'editor di codice:

        SELECT * FROM CallStream

    > Assicurarsi che il nome dell'origine di input corrisponda al nome dell'input specificato in precedenza.

3.  Fare clic su **prova** in editor di query.
4.  Fornire un file di test, uno che è stato creato mediante la procedura precedente o utilizzare [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5.  Fare clic sul pulsante di controllo e visualizzare i risultati visualizzati sotto la definizione della query.

    ![Risultati della definizione di query](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Proiezione di colonna

È ora necessario ridurre i campi restituiti da un set più piccolo.

1.  Modificare la query nell'editor di codice per:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![Output nell'editor di query.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Conteggio di posta in arrivo effettua una chiamata tramite l'area geografica: finestra lo scambio di aggregazione

Per confrontare le quantità che le chiamate in arrivo per l'area geografica è necessario sfruttare le un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio delle chiamate in arrivo raggruppati per SwitchNum 5 secondi.

1.  Modificare la query nell'editor di codice per:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Questa query utilizza la parola chiave **Timestamp By** per specificare un campo di data e ora nel carico utilizzato nel calcolo temporale. Se questo campo non è stato specificato, l'operazione di finestre verrà eseguita utilizzando il tempo di che ogni evento ricevuto hub evento. Vedere ["Arrivo Vs applicazione tempo" in Analitica flusso riferimenti del linguaggio di Query](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Si noti che è possibile accedere timestamp per la fine di ogni finestra utilizzando la proprietà **System.Timestamp** .

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![Risultati della query per Timestand da](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Rilevamento di frode Image con un self-Join

Per individuare l'utilizzo potenzialmente falsi vengono illustrate per le chiamate provenienti da parte dell'utente stesso, ma in un'altra posizione in meno di 5 secondi.  Si [Partecipa](https://msdn.microsoft.com/library/azure/dn835026.aspx) il flusso di eventi di chiamata con per verificare la presenza di questi casi.

1.  Modificare la query nell'editor di codice per:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![Risultati della query di un join](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Creare sink di output

Ora che sono state definite un flusso di eventi, un Hub di eventi di input per acquisire eventi e una query per eseguire una trasformazione sul flusso, il passo successivo consiste per definire un sink di output per il processo.  Creiamo eventi per il comportamento fraudolento a archiviazione Blob.

Seguire la procedura seguente per creare un contenitore per lo spazio di archiviazione Blob se non si dispone già uno.

1.  Usare un account di archiviazione esistente oppure creare un nuovo account di archiviazione, fare clic su **Nuovo > servizi dati > archiviazione > Creazione rapida** chat room e seguire le istruzioni.
2.  Selezionare l'account di archiviazione, fare clic su **contenitori** nella parte superiore della pagina e quindi fare clic su **Aggiungi**.
3.  Specificare un **nome** per il contenitore e impostare l' **accesso** a della.

## <a name="specify-job-output"></a>Specificare l'output di processo

1.  Il processo di flusso Analitica fare clic su **OUTPUT** nella parte superiore della pagina e quindi fare clic su **Aggiungi OUTPUT**. Nella finestra di dialogo visualizzata consentirà numerosi passaggi per configurare l'output.
2.  Selezionare **Archiviazione BLOB**e quindi fare clic sul pulsante a destro.
3.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **ALIAS di OUTPUT**: immettere un nome descrittivo per l'output di processo.
    * **Abbonamento**: se lo spazio di archiviazione Blob creato nella stessa sottoscrizione il processo di flusso Analitica, selezionare **Usa Account di archiviazione dall'abbonamento**. Se lo spazio di archiviazione è attiva una sottoscrizione diversa, selezionare **Usa Account di archiviazione dall'abbonamento a un altro** e immettere manualmente le informazioni per **ACCOUNT di archiviazione**, **Chiave ACCOUNT lo spazio di archiviazione**, **contenitore**.
    * **ACCOUNT di archiviazione**: selezionare il nome dell'account di archiviazione.
    * **Contenitore**: selezionare il nome del contenitore.
    * **PREFISSO FILENAME**: tipo di un prefisso di file da utilizzare durante la scrittura di output blob.

4.  Scegliere il pulsante destro.
5.  Specificare i valori seguenti:

    * **Formato SERIALIZZATORE evento**: JSON
    * **Codifica**: UTF8

6.  Fare clic sul pulsante di controllo per aggiungere questa origine e per verificare che Analitica flusso è possibile connettersi all'account di archiviazione.

## <a name="start-job-for-real-time-processing"></a>Avvia processo per l'elaborazione in tempo reale

Dopo aver specificati tutti un processo input, query e output, si è pronti avviare il processo di flusso Analitica per il rilevamento di frodi in tempo reale.

1.  Dal **DASHBOARD**di processo, fare clic su **Avvia** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata, selezionare **l'ora di inizio processo** e quindi fare clic sul pulsante di controllo nella parte inferiore della finestra di dialogo. Lo stato del processo cambierà a **partire da** e poco sposterà sia in **esecuzione**.

## <a name="view-fraud-detection-output"></a>Visualizzare l'output rilevamento frodi

Utilizzare uno strumento come [Soluzioni di archiviazione di Azure](https://azurestorageexplorer.codeplex.com/) o [Esplora Azure](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare gli eventi fraudolenti man mano che vengono scritte l'output in tempo reale.  

![Rilevamento frodi: eventi fraudolenti visualizzati in tempo reale](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Ottenere supporto
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
