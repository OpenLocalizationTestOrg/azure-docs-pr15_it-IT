<properties
    pageTitle="Analisi di valutazione in Twitter in tempo reale con Analitica flusso | Microsoft Azure"
    description="Informazioni su come usare Analitica flusso per l'analisi di valutazione in Twitter in tempo reale. Istruzioni dettagliate per i dati in un dashboard live da generazione di un evento."
    keywords="analisi delle tendenze twitter in tempo reale, analisi di valutazione in, analisi di social networking, esempio di analisi di tendenza"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi di social networking: analisi di valutazione in Twitter in tempo reale in Azure flusso Analitica

Informazioni su come creare una soluzione di analisi di valutazione in per analitica di social networking da importare eventi Twitter in tempo reale in Azure evento hub. È possibile scrivere una query di Azure flusso Analitica per analizzare i dati. Si verrà quindi archiviare i risultati di analisi successiva o utilizzare un dashboard e [Power BI](https://powerbi.com/) per fornire informazioni dettagliate in tempo reale.

Strumenti di social networking analitica identifichi organizzazioni tendenza argomenti, vale a dire oggetti e tempestivo con una quantità elevata di post nel social networking. Analisi di valutazione in, l'acronimo di *data mining parere*, utilizza strumenti analitica social network per determinare tempestivo verso un prodotto, l'idea e così via. Analisi delle tendenze Twitter in tempo reale sono un ottimo esempio perché il modello di sottoscrizione hashtag consente di ascoltare le parole chiave specifiche e sviluppare analisi di valutazione in sul feed.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Uno scenario: Analisi di valutazione in tempo reale

Una società che ha un sito Web media notizie massimo vuole ricevere un vantaggio rispetto alla relativa concorrenti grazie a contenuto del sito immediatamente relative ai lettori. La società utilizza analisi di social networking sugli argomenti che riguardano i lettori eseguendo analisi di valutazione in tempo reale su Twitter dati. In particolare, per identificare gli argomenti della tendenze in tempo reale su Twitter, la società ha bisogno in tempo reale analitica sui volume tweet valutazione in per argomenti più importanti. Pertanto, in sostanza, la necessità è un motore di analitica analisi di valutazione in basato su questo social network feed.

## <a name="prerequisites"></a>Prerequisiti di
-   Twitter account e [token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) dall'area Download Microsoft
-   Facoltativo: Codice sorgente per client twitter da [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Creare un gruppo di consumer e un hub di eventi di input

L'applicazione di esempio genererà eventi e inviarli a un'istanza di evento hub (un hub evento, in breve). Hub evento Bus di servizio sono il metodo di acquisizione di eventi per Analitica flusso preferito. Vedere la documentazione hub di evento nella [documentazione del servizio Bus](/documentation/services/service-bus/).

Utilizzare la procedura seguente per creare un hub di evento.

1.  Nel portale di Azure fare clic su **Nuovo** > **Servizi APP** > **Servizio BUS** > **Evento HUB** > **Creazione rapida**e specificare un nome, l'area geografica e spazio dei nomi del nuovo o esistente.  
2.  Come ottimale, ogni processo Analitica flusso contiene informazioni interessanti di un singolo gruppo consumer hub evento. Abbiamo illustrano le fasi del processo di creazione di un gruppo di consumer in un secondo momento. Sono disponibili ulteriori informazioni sui gruppi di consumer vederepanoramica [Azure evento hub](https://msdn.microsoft.com/library/azure/dn836025.aspx). Per creare un gruppo di consumer, passare a hub eventi appena creato, fare clic sulla scheda **Gruppi di consumatori** , fare clic su **Crea** nella parte inferiore della pagina e quindi specificare un nome per il gruppo consumer.
3.  Per concedere l'accesso all'hub di evento, sarà necessario creare un criterio di accesso condiviso. Fare clic sulla scheda **Configura** dell'hub evento.
4.  In **Criteri di accesso condivisi**, creare un nuovo criterio di **gestire** le autorizzazioni.

    ![Condividere criteri di accesso in cui è possibile creare un criterio di gestire le autorizzazioni.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Fare clic su **Salva** nella parte inferiore della pagina.
6.  Passare a **DASHBOARD**, fare clic su **Informazioni di connessione** nella parte inferiore della pagina, quindi copiarlo e salvare le informazioni di connessione. (Utilizzare l'icona di copia che viene visualizzata sotto l'icona di ricerca).

## <a name="configure-and-start-the-twitter-client-application"></a>Configurare e avviare l'applicazione client Twitter

Vengono fornite un'applicazione client che si connetterà Twitter dati tramite [API Streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi Tweet su un set di parametri di argomenti. Lo strumento di Apri origine [Sentiment140](http://help.sentiment140.com/) viene utilizzato per assegnare il valore di valutazione in a ogni tweet.

- 0 = negativo
- 2 = indipendente
- 4 = positivo

Quindi, gli eventi Tweet vengono inviati a hub evento.  

Seguire questa procedura per configurare l'applicazione:

1.  [Scaricare la soluzione TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Aprire TwitterClient.exe.config e sostituire oauth_consumer_key, oauth_consumer_secret, oauth_token e oauth_token_secret con i token di Twitter contenenti i valori.  

    [Passaggi per generare un token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Si noti che è necessario creare un'applicazione vuota per generare un token.  
3.  Sostituire i valori EventHubConnectionString ed EventHubName in TwitterClient.exe.config con la stringa di connessione e il nome dell'hub di evento. La stringa di connessione che sono stati copiati in precedenza offre la stringa di connessione e il nome dell'hub di evento, in modo da separarli e inserire ogni nel campo corretto. Si consideri ad esempio la stringa di connessione seguenti:

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    Il file TwitterClient.exe.config deve contenere le impostazioni come illustrato nell'esempio seguente:

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    È importante tenere presente che il testo "EntityPath =" indica __non__ vengono visualizzati nel valore EventHubName.

4.  *Facoltativo:* Regolare le parole chiave da cercare.  Per impostazione predefinita, questa applicazione consente di cercare "Azure, Skype, XBox, Microsoft, Seattle".  È possibile modificare i valori per **twitter_keywords** in TwitterClient.exe.config, se lo si desidera.
5.  Eseguire TwitterClient.exe per avviare l'applicazione. Verranno visualizzati gli eventi Tweet con i valori **CreatedAt**, **l'argomento**e **SentimentScore** inviati all'hub di evento.

    ![Analisi di valutazione in: valori SentimentScore inviati a un hub di evento.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Creare un processo di flusso Analitica

Ora che gli eventi Tweet sono streaming in tempo reale da Twitter, è possibile impostare un processo di flusso Analitica analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Effettuare il provisioning di un processo di flusso Analitica

1.  Nel [portale di Azure](https://manage.windowsazure.com/), fare clic su **Nuovo** > **Servizi dati** > **ANALYTICS flusso** > **Creazione rapida**.
2.  Specificare i valori seguenti e quindi fare clic su **Crea flusso ANALYTICS processo**:

    * **Nome del processo**: immettere un nome di processo.
    * **L'area geografica**: selezionare l'area in cui si vuole eseguire il processo. È consigliabile inserire il processo e l'hub di evento nella stessa regione per garantire prestazioni migliori e per garantire che si verrà non acquistati trasferire dati tra aree geografiche.
    * **ACCOUNT di archiviazione**: scegliere l'account di archiviazione Azure che si desidera utilizzare per archiviare i dati di monitoraggio per tutti i processi di flusso Analitica che vengono eseguiti in quest'area. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.

3.  Fare clic su **Analisi flusso** nel riquadro a sinistra per visualizzare un elenco di processi flusso Analitica.  
    ![Icona di flusso Analitica servizio](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    Verrà visualizzato il nuovo processo con lo stato **creato**. Si noti che viene disabilitato dal pulsante **START** nella parte inferiore della pagina. È necessario configurare il processo input, output e query prima di iniziare il processo.


### <a name="specify-job-input"></a>Specificare l'input di processo
1.  Il processo di flusso Analitica, fare clic su **input** nella parte superiore della pagina e quindi fare clic su **Aggiungi INPUT**. Nella finestra di dialogo visualizzata consentirà numerosi passaggi per configurare l'input.
2.  Fare clic su **flusso di dati**e quindi fare clic sul pulsante a destro.
3.  Fare clic su **HUB evento**e quindi fare clic sul pulsante a destro.
4.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **ALIAS INPUT**: immettere un nome descrittivo per il processo di input, ad esempio *TwitterStream*. Si noti che si utilizzerà il nome della query in un secondo momento.
    **HUB di evento**: se l'hub di evento creato nello stesso abbonamento il processo di flusso Analitica, selezionare lo spazio dei nomi di hub evento.

        Se l'hub di evento in una sottoscrizione diversa, fare clic su **Usa evento Hub da un'altra sottoscrizione**e quindi immettere manualmente le informazioni **Dello spazio dei nomi di servizio BUS**, **Evento HUB nome**, **Nome del criterio HUB evento**, **Chiave dei criteri di evento HUB**e **Conteggio partizione HUB eventi**.

    * **Nome dell'evento HUB**: selezionare il nome dell'hub evento.

    * **Nome del criterio HUB evento**: selezionare il criterio di hub di eventi che è stato creato in precedenza in questa esercitazione.

    * **Gruppo di eventi HUB CONSUMER**: digitare il nome del gruppo consumer creata in precedenza in questa esercitazione.
5.  Scegliere il pulsante destro.
6.  Specificare i valori seguenti:

    * **Formato SERIALIZZATORE evento**: JSON
    * **Codifica**: UTF8

7.  Fare clic sul pulsante **controllo** per aggiungere questa origine e verificare che flusso Analitica è possibile connettersi all'hub di evento.

### <a name="specify-job-query"></a>Specificare query processo

Flusso Analitica supporta un modello di query semplice e dichiarativo che descrive le trasformazioni. Per ulteriori informazioni sull'utilizzo del linguaggio, vedere [Riferimenti per Azure flusso Analitica Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx).  In questa esercitazione consente di creare e verificare più query sui dati di Twitter.

#### <a name="sample-data-input"></a>Input di dati di esempio

Per convalidare la query sui dati effettivo del processo, è possibile utilizzare la caratteristica di **Dati di esempio** per estrarre eventi dal flusso di e creare un file di .json degli eventi di verifica.

1.  Selezionare i dati immessi hub evento e quindi fare clic su **Dati di esempio** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata, specificare un' **ora di inizio** per avviare la raccolta di dati e una **durata** per la quantità di dati per l'utilizzo.
3.  Fare clic sul pulsante **Dettagli** e quindi fare clic sul collegamento **fare clic qui** per scaricare e salvare il file .json generato.

#### <a name="pass-through-query"></a>Query pass-through
Per iniziare, si esegue una query pass-through semplice che tutti i campi di un evento i progetti.

1.  Fare clic su **QUERY** nella parte superiore della pagina processo flusso Analitica.
2.  Nell'editor di codice, sostituire il modello di query iniziale con le operazioni seguenti:

        SELECT * FROM TwitterStream

    Assicurarsi che il nome dell'origine di input corrisponda al nome dell'input specificato in precedenza.

3.  Fare clic su **prova** in editor di query.
4.  Passare al file .json di esempio.
5.  Fare clic sul pulsante di **controllo** e visualizzare i risultati sotto la definizione della query.

    ![Risultati visualizzati sotto la definizione di query](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Conteggio di tweet per argomento: finestra lo scambio di aggregazione

Per confrontare il numero di menzioni tra gli argomenti, si userà un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il numero di menzioni per argomento ogni cinque secondi.

1.  Modificare la query nell'editor di codice per:

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Questa query utilizza la parola chiave **TIMESTAMP BY** per specificare un campo di data e ora nel carico utilizzato nel calcolo temporale. Se questo campo non è stato specificato, verrà eseguita l'operazione di finestre utilizzando l'ora di ogni evento ricevuto nell'hub evento.  Ulteriori informazioni nella sezione "Arrivo Vs applicazione tempo" di [Riferimento delle Query flusso Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Questa query consente di accedere anche un timestamp per la fine di ogni finestra utilizzando la proprietà **System.Timestamp** .

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

#### <a name="identify-trending-topics-sliding-window"></a>Identificare gli argomenti della tendenze: finestra scorrevole

Per identificare gli argomenti della tendenze, vengono illustrate degli argomenti che superano un valore di soglia per menzioni in un determinato periodo di tempo. Ai fini di questa esercitazione, verrà cercare agli argomenti a cui sono indicati in più di 20 ore negli ultimi cinque secondi utilizzando un [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Modificare la query nell'editor di codice a: selezionare System.Timestamp come ora, argomento conteggio (*) come menzioni da TwitterStream TIMESTAMP da CreatedAt gruppo da SLIDINGWINDOW(s, 5), argomento VERIFICANO CONTARE (*) > 20

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.

    ![La variabile di output della query finestra](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Conteggio di valutazione in e menzioni: finestra lo scambio di aggregazione
La query finale che si verificherà utilizza **TumblingWindow** per ottenere il numero di menzioni, Media, minimo, massimo e deviazione standard di punteggio di valutazione in per ogni argomento ogni cinque secondi.

1.  Modificare la query nell'editor di codice per:

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Fare clic su **Esegui nuovamente** in editor di query per visualizzare i risultati della query.
3.  Questa è la query che verrà utilizzato per il dashboard.  Fare clic su **Salva** nella parte inferiore della pagina.


## <a name="create-output-sink"></a>Creare sink di output

Ora che sono state definite un flusso di eventi, un hub di eventi di input per acquisire eventi e una query per eseguire una trasformazione sul flusso, il passo successivo consiste per definire un sink di output per il processo.  È necessario scrivere gli eventi tweet aggregato dalla query processo a archiviazione Blob Azure.  È anche possibile push i risultati al Database SQL Azure, archiviazione tabella Azure, o hub di evento, a seconda dell'applicazione specifica esigenze.

Utilizzare la procedura seguente per creare un contenitore per l'archiviazione Blob, se non si dispone già uno:

1.  Usare un account di archiviazione esistente oppure creare un nuovo account di archiviazione, fare clic su **Nuovo** > **Servizi dati** > **lo spazio di archiviazione** > **Creazione rapida**e quindi seguire le istruzioni sullo schermo.
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

## <a name="start-job"></a>Processo di avvio

Perché un processo input, query e output sono tutti è state specificate, si è pronti avviare il processo di flusso Analitica.

1.  Dal **DASHBOARD**di processo, fare clic su **Avvia** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata, fare clic su **ora di inizio processo**e quindi fare clic sul pulsante **controllo** nella parte inferiore della finestra di dialogo. Lo stato del processo cambierà a **partire da** e poco cambiano in **esecuzione**.


## <a name="view-output-for-sentiment-analysis"></a>Visualizzare l'output per l'analisi di valutazione in

Dopo il processo è in esecuzione e il flusso di Twitter in tempo reale di elaborazione, scegliere come si desidera visualizzare l'output per l'analisi di valutazione in. Utilizzare uno strumento come [Soluzioni di archiviazione di Azure](https://azurestorageexplorer.codeplex.com/) o [Esplora Azure](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare l'output di processo in tempo reale. Da qui è possibile usare [Power BI](https://powerbi.com/) per estendere l'applicazione per includere un dashboard personalizzato simile a quella nella schermata seguente.

![Analisi di social networking: output flusso Analitica valutazione in dell'analisi (data mining parere) in un dashboard di Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Ottenere supporto
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
