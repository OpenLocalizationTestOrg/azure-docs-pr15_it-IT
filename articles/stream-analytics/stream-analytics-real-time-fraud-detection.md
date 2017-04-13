<properties
    pageTitle="Analitica flusso: Rilevamento di frodi in tempo reale | Microsoft Azure"
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

Informazioni su come creare una soluzione-to-end per il rilevamento di frodi in tempo reale con Azure flusso Analitica. Importare eventi Azure evento hub, scrivere query flusso Analitica per l'aggregazione o l'avviso e inviare i risultati a un sink di output per ottenere informazioni dettagliate sui dati con elaborazione in tempo reale. Rilevamento di anomalie in tempo reale per telecomunicazioni spiegazioni, ma la tecnica di esempio è ideale per altri tipi di rilevamento di frode, ad esempio gli scenari di furto di carta di credito o identità.

Flusso Analitica è un servizio completamente gestito che consente l'elaborazione di bassa latenza, elevata disponibilità, scalabilità e complesse evento sopra il flusso di dati nel cloud. Per ulteriori informazioni, vedere [Introduzione alle Azure flusso Analitica](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: Telecomunicazioni e Image del rilevamento delle frodi in tempo reale

Una società di telecomunicazione ha una grande quantità di dati per le chiamate in arrivo. La società deve seguenti con i dati seguenti:

* Ridurre i dati a una quantità gestibile e ottenere informazioni approfondite sul utilizzo al cliente nel tempo e attraverso aree geografiche.
* Consente di individuare frodi Image (più chiamate dalla stessa identità intorno all'ora, ma in posizioni diverse aree geografiche) in tempo reale in modo che la società facilmente rispondere mediante la notifica clienti o arrestare il servizio di sistema.

Scenari Internet di elementi (IoT) canonici dispone di una tonnellata di telemetria o dati dai sensori. I clienti desiderano aggregare i dati o ricevere avvisi relativi alterazioni in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'area Download Microsoft
- Facoltativo: Codice sorgente del generatore di evento da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Creare il gruppo di input e consumer Azure evento hub

L'applicazione di esempio genererà eventi e inviarli a un'istanza di evento hub per l'elaborazione in tempo reale. Servizio Bus evento hub sono il metodo di acquisizione di eventi per Analitica flusso preferito. Si può acquisiscono informazioni sull'hub di evento nella [documentazione Bus di servizio Azure](/documentation/services/service-bus/).

Per creare un hub di evento:

1.  Nel [portale di Azure](https://manage.windowsazure.com/), fare clic su **Nuovo** > **Servizi APP** > **Servizio BUS** > **Evento HUB** > **Creazione rapida**. Specificare un nome, l'area geografica e nomi di nuovo o esistenti per creare un nuovo hub di evento.  
2.  Come si consiglia di ogni processo Analitica flusso contiene informazioni interessanti da un gruppo di consumer hub singolo evento. Abbiamo illustrano le fasi del processo di creazione di un gruppo di consumer in un secondo momento. [Ulteriori informazioni sui gruppi di consumatori](https://msdn.microsoft.com/library/azure/dn836025.aspx). Per creare un gruppo di consumer, passare a hub eventi appena creato, fare clic sulla scheda **Gruppi di consumatori** , fare clic su **Crea** nella parte inferiore della pagina e quindi specificare un nome per il gruppo consumer.
3.  Per concedere l'accesso all'hub di evento, sarà necessario creare un criterio di accesso condiviso. Fare clic sulla scheda **Configura** dell'hub evento.
4.  In **Criteri di accesso condivisi**, creare un nuovo criterio dotato di autorizzazioni di **Gestione** .

    ![Condividere criteri di accesso in cui è possibile creare un criterio di gestire le autorizzazioni.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Fare clic su **Salva** nella parte inferiore della pagina.
6.  Passare a **Dashboard**, fare clic su **Informazioni di connessione** nella parte inferiore della pagina, quindi copiarlo e salvare le informazioni di connessione.

## <a name="configure-and-start-the-event-generator-application"></a>Configurare e avviare l'applicazione di generatore di evento

Vengono fornite un'applicazione client che generano metadati chiamata in arrivo di esempio e push a un hub di evento. Utilizzare la procedura seguente per impostare questa applicazione.  

1.  Scaricare il [file TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)e decomprimere in una directory.

    > [AZURE.NOTE] Windows potrebbe bloccare il file zip scaricati. Pulsante destro del mouse sul file e scegliere **proprietà**. Se viene visualizzato il messaggio "il file proviene da un altro computer e potrebbe essere bloccato per proteggere il computer", selezionare la casella **Sblocca** e quindi fare clic su Applica sul file zip.

2.  Sostituire i valori Microsoft.ServiceBus.ConnectionString ed EventHubName in telcodatagen.exe.config con il nome e la stringa di connessione hub evento.

    La stringa di connessione che sono stati copiati dal portale di Azure inserisce il nome della connessione alla fine. Assicurarsi di rimuovere "; EntityPath =<value>"dalla" aggiungere chiave = "campo.

3.  Avviare l'applicazione. L'utilizzo consiste nel modo seguente:

    telcodatagen.exe [#NumCDRsPerHour] [Image scheda frode probabilità] [#DurationHours]

Nell'esempio seguente genererà 1.000 eventi con una probabilità di 20% di frode complessivamente nel corso di due ore.

    telcodatagen.exe 1000 .2 2

Verranno visualizzati record inviato all'hub di evento. Alcuni campi chiave che verranno utilizzate in questa applicazione di rilevamento frodi in tempo reale sono definiti di seguito:

| Record | Definizione |
| ------------- | ------------- |
| CallrecTime | Ora di inizio timestamp per la chiamata. |
| SwitchNum | Parametro di telefono utilizzato per eseguire la connessione. |
| CallingNum | Numero di telefono del chiamante. |
| CallingIMSI | Identità sottoscrittore cellulare internazionale (IMSI).  Identificatore univoco del chiamante. |
| CalledNum | Numero di telefono del destinatario della chiamata Visualizza. |
| CalledIMSI | Identità sottoscrittore cellulare internazionale (IMSI).  Identificatore univoco del destinatario chiamata. |


## <a name="create-a-stream-analytics-job"></a>Creare un processo di flusso Analitica
Ora che si dispone di un flusso di eventi di telecomunicazione, è possibile impostare un processo Analitica flusso analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Effettuare il provisioning di un processo di flusso Analitica

1.  Nel portale di Azure fare clic su **Nuovo** > **Servizi dati** > **ANALYTICS flusso** > **Creazione rapida**.
2.  Specificare i valori seguenti e quindi fare clic su **Crea flusso ANALYTICS processo**:

    * **Nome del processo**: immettere un nome di processo.

    * **L'area geografica**: selezionare l'area in cui si vuole eseguire il processo. È consigliabile inserire il processo e l'hub di evento nella stessa regione per garantire prestazioni migliori e per garantire che si verrà non acquistati trasferire dati tra aree geografiche.

    * **ACCOUNT di archiviazione**: scegliere l'account di archiviazione Azure che si desidera utilizzare per archiviare i dati di monitoraggio per tutti i processi di flusso Analitica che vengono eseguiti in quest'area. Viene offerta la possibilità di scegliere un account di archiviazione esistente o crearne uno nuovo.

3.  Fare clic su **Analisi flusso** nel riquadro a sinistra per visualizzare un elenco di processi flusso Analitica.

    ![Icona di flusso Analitica servizio](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    Verrà visualizzato il nuovo processo con lo stato **creato**. Si noti che viene disabilitato dal pulsante **START** nella parte inferiore della pagina. È necessario configurare il processo input, output e query prima di iniziare il processo.

### <a name="specify-job-input"></a>Specificare l'input di processo
1.  Il processo di flusso Analitica, fare clic su **input** nella parte superiore della pagina e quindi fare clic su **Aggiungi INPUT**. Nella finestra di dialogo che consente di aprire consentono all'utente diversi passaggi per configurare l'input.
2.  Fare clic su **flusso di dati**e quindi fare clic sul pulsante a destro.
3.  Fare clic su **HUB evento**e quindi fare clic sul pulsante a destro.
4.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **ALIAS di INPUT**: immettere un nome descrittivo, ad esempio *CallStream*, per il processo. Si noti che si userà il nome della query in un secondo momento.

    * **HUB di evento**: se hub evento creato nella stessa sottoscrizione il processo di flusso Analitica, selezionare lo spazio dei nomi di hub evento.

        Se l'hub di evento in una sottoscrizione diversa, selezionare **Usa evento Hub da un'altra sottoscrizione**e immettere manualmente le informazioni ** **Dello spazio dei nomi di servizio BUS**evento HUB NAME**, **Nome del criterio HUB evento**, **Chiave dei criteri di evento HUB**e **Conteggio partizione HUB eventi**.

    * **Nome dell'evento HUB**: selezionare il nome dell'hub evento.

    * **Nome del criterio HUB evento**: selezionare il criterio di hub di eventi che è stato creato in precedenza in questa esercitazione.

    * **Gruppo di eventi HUB CONSUMER**: digitare il nome del gruppo consumer creata in precedenza in questa esercitazione.

5.  Scegliere il pulsante destro.
6.  Specificare i valori seguenti:

    * **Formato SERIALIZZATORE evento**: JSON
    * **Codifica**: UTF8
7.  Fare clic sul pulsante **controllo** per aggiungere questa origine e verificare che flusso Analitica è possibile connettersi all'hub di evento.

### <a name="specify-job-query"></a>Specificare query processo

Flusso Analitica supporta un modello di query semplice e dichiarativo che descrive le trasformazioni per l'elaborazione in tempo reale. Per ulteriori informazioni sull'utilizzo del linguaggio, vedere [Riferimenti per Azure flusso Analitica Query Language](https://msdn.microsoft.com/library/dn834998.aspx). In questa esercitazione consente di creare e verificare più query per il flusso in tempo reale di dati di chiamata.

#### <a name="optional-sample-input-data"></a>Facoltativi: Dati di input di esempio
Per convalidare la query sui dati effettivo del processo, è possibile utilizzare la caratteristica di **Dati di esempio** per estrarre eventi dal flusso di e creare un. File di JSON degli eventi di verifica.  La procedura seguente viene illustrato come eseguire questa operazione. Sono disponibili anche un file di esempio [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) a scopo di testing.

1.  Selezionare i dati immessi hub evento e quindi fare clic su **Dati di esempio** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata, specificare un' **ora di inizio** per avviare la raccolta di dati e una **durata** per la quantità di dati per l'utilizzo.
3.  Fare clic sul pulsante **controllo** per iniziare a dati di esempio dell'input.  Può essere necessaria un o due minuti per il file di dati da creare.  Al termine del processo, fare clic su **Dettagli**, scaricare la generato. JSON file e salvarlo.

    ![Scaricare e salvare i dati trasformati in un file JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Query pass-through

Se si desidera archiviare tutti gli eventi, è possibile utilizzare una query pass-through per leggere tutti i campi payload del messaggio o all'evento. Per iniziare, eseguire una query pass-through semplice che tutti i campi di un evento i progetti.

1.  Fare clic su **QUERY** nella parte superiore della pagina processo flusso Analitica.
2.  Aggiungere quanto segue all'editor di codice:

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Assicurarsi che il nome dell'origine di input corrisponda al nome dell'input specificato in precedenza.

3.  Fare clic su **prova** in editor di query.
4.  Fornire un file di test. Usare uno che è stato creato mediante la procedura precedente, oppure [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Fare clic sul pulsante di **controllo** e visualizzare i risultati visualizzati sotto la definizione della query.

    ![Risultati della definizione di query](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Proiezione di colonna

È ora necessario ridurre campi restituiti a un set di dimensioni inferiore.

1.  Modificare la query nell'editor di codice per:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![Output nell'editor di query.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Conteggio di posta in arrivo effettua una chiamata tramite l'area geografica: finestra lo scambio di aggregazione

Per confrontare il numero di chiamate in arrivo per l'area geografica, si userà un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio delle chiamate in arrivo raggruppati per **SwitchNum** ogni cinque secondi.

1.  Modificare la query nell'editor di codice per:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Questa query utilizza la parola chiave **Timestamp By** per specificare un campo di data e ora nel carico utilizzato nel calcolo temporale. Se questo campo non è stato specificato, verrà eseguita l'operazione di finestre utilizzando l'ora di ogni evento ricevuto nell'hub evento. Vedere ["Arrivo Vs applicazione tempo" in Analitica flusso riferimenti del linguaggio di Query](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Si noti che è possibile accedere timestamp per la fine di ogni finestra utilizzando la proprietà **System.Timestamp** .

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![Risultati della query per Timestand da](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Rilevamento di frode Image con un self-Join

Per individuare l'utilizzo potenzialmente falsi, vengono illustrate per le chiamate che derivano dall'utente stesso, ma in un'altra posizione in meno di 5 secondi.  Si [Partecipa](https://msdn.microsoft.com/library/azure/dn835026.aspx) il flusso di eventi di chiamata con per verificare la presenza di questi casi.

1.  Modificare la query nell'editor di codice per:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![Risultati della query di un join](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Creare sink di output

Ora che sono state definite un flusso di eventi, un hub di eventi di input per acquisire eventi e una query per eseguire una trasformazione sul flusso, il passo successivo consiste per definire un sink di output per il processo. Creiamo eventi per il comportamento fraudolento a archiviazione Blob Azure.

Utilizzare la procedura seguente per creare un contenitore per lo spazio di archiviazione Blob, se non si dispone già uno.

1.  Usare un account di archiviazione esistente oppure creare un nuovo account di archiviazione, fare clic su **Nuovo > servizi dati > archiviazione > Creazione rapida**e seguire le istruzioni.
2.  Selezionare l'account di archiviazione, fare clic su **contenitori** nella parte superiore della pagina e quindi fare clic su **Aggiungi**.
3.  Specificare un **nome** per il contenitore e impostare l' **accesso** a **Della**.

## <a name="specify-job-output"></a>Specificare l'output di processo

1.  Il processo di flusso Analitica, fare clic su **OUTPUT** nella parte superiore della pagina e quindi fare clic su **Aggiungi OUTPUT**. Nella finestra di dialogo che consente di aprire consentono all'utente diversi passaggi per configurare l'output.
2.  Fare clic su **Archiviazione BLOB**e quindi fare clic sul pulsante a destro.
3.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **ALIAS di OUTPUT**: immettere un nome descrittivo per l'output di processo.
    * **Abbonamento**: se lo spazio di archiviazione Blob creato nella stessa sottoscrizione il processo di flusso Analitica, fare clic su **Usa Account di archiviazione dall'abbonamento**. Se lo spazio di archiviazione è attiva una sottoscrizione diversa, fare clic su **Usa Account di archiviazione dall'abbonamento a un altro**e immettere manualmente le informazioni per **l'ACCOUNT di archiviazione**, **Chiave ACCOUNT lo spazio di archiviazione**e **contenitore**.
    * **ACCOUNT di archiviazione**: selezionare il nome dell'account di archiviazione.
    * **Contenitore**: selezionare il nome del contenitore.
    * **PREFISSO FILENAME**: digitare un prefisso di file da utilizzare durante la scrittura di output blob.

4.  Scegliere il pulsante destro.
5.  Specificare i valori seguenti:

    * **Formato SERIALIZZATORE evento**: JSON
    * **Codifica**: UTF8

6.  Fare clic sul pulsante **controllo** per aggiungere questa origine e per verificare che Analitica flusso è possibile connettersi all'account di archiviazione.

## <a name="start-job-for-real-time-processing"></a>Avvia processo per l'elaborazione in tempo reale

Perché un processo input, query e output sono tutti è state specificate, si è pronti avviare il processo di flusso Analitica per il rilevamento di frodi in tempo reale.

1.  Dal **DASHBOARD**di processo, fare clic su **Avvia** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata, fare clic su **ora di inizio processo**e quindi fare clic sul pulsante **controllo** nella parte inferiore della finestra di dialogo. Lo stato del processo cambierà a **partire da** e poco cambiano in **esecuzione**.

## <a name="view-fraud-detection-output"></a>Visualizzare l'output rilevamento frodi

Utilizzare uno strumento come [Soluzioni di archiviazione di Azure](http://storageexplorer.com/) o [Esplora Azure](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare gli eventi fraudolenti man mano che vengono scritte l'output in tempo reale.  

![Rilevamento frodi: eventi fraudolenti visualizzati in tempo reale](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Ottenere supporto
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
