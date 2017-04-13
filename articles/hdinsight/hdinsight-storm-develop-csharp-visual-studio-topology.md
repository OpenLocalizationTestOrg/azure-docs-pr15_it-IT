<properties
   pageTitle="Topologie Apache eccesso con Visual Studio e c# | Microsoft Azure"
   description="Informazioni su come creare topologie eccesso in c# mediante la creazione di una topologia di conteggio word semplice in Visual Studio utilizzando gli strumenti di HDInsight per Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/27/2016"
   ms.author="larryfr"/>

# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Sviluppare c# topologie per eccesso Apache in HDInsight mediante Hadoop tools per Visual Studio

Informazioni su come creare una topologia c# eccesso tramite gli strumenti di HDInsight per Visual Studio. In questa esercitazione il processo di creazione di un nuovo progetto eccesso in Visual Studio, verifica locale e distribuirlo in un eccesso Apache cluster HDInsight.

Verrà anche informazioni su come creare topologie ibrido che utilizzano c# e componenti Java.

> [AZURE.IMPORTANT] Durante la procedura descritta in questo documento si basa su un ambiente di sviluppo di Windows con Visual Studio, è possibile inviare il progetto compilato cluster HDInsight basato su Windows o Linux. Solo i cluster basati su Linux creati dopo il supporto di 28/10/2016 topologie SCP.NET.
>
> Per utilizzare una topologia c# con un cluster basato su Linux, è necessario aggiornare il pacchetto Microsoft.SCP.Net.SDK NuGet usato dal progetto alla versione 0.10.0.6 o versione successiva. La versione del pacchetto deve corrispondere anche la versione principale di eccesso installato HDInsight. Ad esempio eccesso nelle versioni HDInsight 3.3 e 3.4 utilizzare eccesso versione 0.10.x, mentre HDInsight 3.5 utilizza eccesso 1.0.
> 
> C# topologie nei cluster basati su Linux devono usare .NET 4.5 e usare Mono da eseguire in cluster HDInsight. Maggior parte delle caratteristiche funzionano, tuttavia è necessario archiviare il documento [Mono compatibilità](http://www.mono-project.com/docs/about-mono/compatibility/) per le possibili incompatibilità.

## <a name="prerequisites"></a>Prerequisiti

- Uno dei seguenti versioni di Visual Studio

    - Visual Studio 2012 con [aggiornamento 4](http://www.microsoft.com/download/details.aspx?id=39305)

    - Visual Studio 2013 con [aggiornamento 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

    - Visual Studio 2015 o [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

- Azure SDK 2.9.5 o versioni successive

- HDInsight Tools per Visual Studio: [iniziare a utilizzare HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) per installare e configurare gli strumenti di HDInsight per Visual Studio.

    > [AZURE.NOTE] HDInsight Tools per Visual Studio non sono supportate in Visual Studio Express

-   Eccesso Apache cluster HDInsight: [Guida introduttiva a eccesso Apache su HDInsight](hdinsight-apache-storm-tutorial-get-started.md) per la procedura per creare un cluster.

## <a name="templates"></a>Modelli

Gli strumenti di HDInsight per Visual Studio forniscono modelli seguenti:

| Tipo di progetto | Di seguito viene |
| ------------ | ------------- |
| Applicazione eccesso | Un progetto di topologia eccesso vuoto |
| Esempio di Writer eccesso SQL Azure | Come scrivere a Database SQL Azure |
| Esempio di lettore di DocumentDB eccesso | Come leggere da Azure DocumentDB |
| Esempio di DocumentDB Writer eccesso | Come scrivere DocumentDB Azure |
| Esempio di lettore di EventHub eccesso | Come leggere da Azure evento hub |
| Esempio di EventHub Writer eccesso | Come scrivere a un hub evento Azure |
| Esempio di lettore di HBase eccesso | Come leggere da HBase sui HDInsight cluster |
| Esempio di HBase Writer eccesso | Come scrivere la HBase su HDInsight cluster |
| Eccesso ibrida | Come usare un componente Java |
| Esempio di eccesso | Una topologia di conteggio di base in word |

> [AZURE.NOTE] Gli esempi di utilità per la lettura e scrittura HBase utilizzano l'API REST HBase per comunicare con un HBase in HDInsight cluster, non l'API di linguaggio HBase.

Nei passaggi descritti in questo documento, si verrà utilizzato il tipo di progetto applicazione eccesso base per creare una nuova topologia.

## <a name="create-a-c-topology"></a>Crea una topologia c#

1. Se non è già installato la versione più recente degli strumenti HDInsight per Visual Studio, vedere [Introduzione all'utilizzo HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Aprire Visual Studio, selezionare **il File** > **file**e quindi **progetto**.

3. Nella schermata **Nuovo progetto** espandere **installati** > **modelli**e selezionare **HDInsight**. Nell'elenco di modelli, selezionare **Applicazione eccesso**. Nella parte inferiore dello schermo, immettere **WordCount** come il nome dell'applicazione.

    ![immagine](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Dopo aver creato il progetto, è necessario disporre i file seguenti:

    - **Program.cs**: consente di definire la topologia per il progetto. Si noti che una topologia predefiniti che è costituito da un beccuccio e uno bulloni viene creata per impostazione predefinita.

    - **Spout.cs**: un beccuccio di esempio che genera numeri casuali.

    - **BOLT.cs**: un bulloni di esempio che tiene un conteggio dei numeri emessi dal beccuccio.

    Durante la creazione del progetto, la più recente [SCP.NET pacchetti](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) verranno scaricati da NuGet.

    [AZURE.INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

Nelle sezioni successive verrà modificato il progetto in un'applicazione WordCount base.

### <a name="implement-the-spout"></a>Implementare beccuccio

1. Aprire **Spout.cs**. Spouts vengono utilizzati per leggere i dati in una topologia da un'origine esterna. Componenti principali per beccuccio sono:

    - **NextTuple**: chiamata da eccesso quando beccuccio è consentito creare nuove Tuple.

    - **ACK** (solo topologia transazione): gestisce riconoscimenti da altri componenti della topologia per tuple inviate da questo beccuccio. Riconoscimento di una tupla consente beccuccio fonti di informazioni che è stata elaborata correttamente dai componenti downstream.

    - **Esito negativo** (solo topologia transazione): gestisce tuple che sono esito negativo per l'elaborazione di altri componenti della topologia. In questo modo la possibilità di creare nuovamente tupla in modo che possa essere elaborato nuovamente.

2. Sostituire il contenuto della classe **Spout** con le operazioni seguenti. In questo modo si crea un beccuccio che genera in modo casuale una frase nella topologia di.

        private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
            // Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

        // Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }
    
    Richiedere alcuni minuti per leggere i commenti per comprendere che cosa significa questo codice.

### <a name="implement-the-bolts"></a>Implementare il bulloni

1. Eliminare il file **Bolt.cs** esistente dal progetto.

2. In **Esplora soluzioni**fare clic sul progetto e selezionare **Aggiungi** > **nuovo elemento**. Nell'elenco selezionare **Eccesso bulloni**e immettere **Splitter.cs** come nome. Ripetere l'operazione per creare un secondo bullone denominato **Counter.cs**.

    - **Splitter.cs**: implementa bulloni che divide frasi in singole parole e crea un nuovo flusso di parole.

    - **Counter.cs**: implementa bulloni che conta di ogni parola e genera un nuovo flusso di parole e il conteggio di ogni parola.

    > [AZURE.NOTE] Questi bulloni semplicemente leggere e scrivere flussi, ma è possibile usare anche un bulloni per comunicare con origini, ad esempio un database o servizio.

3. Aprire **Splitter.cs**. Si noterà che dispone di uno dei metodi per impostazione predefinita: **Execute**. Questo comando si chiama quando il bulloni riceve una tupla per l'elaborazione. In questo caso, è possibile leggere ed elaborare tuple in arrivo e generare tuple in uscita.

4. Sostituire il contenuto della classe di **divisione** con il codice seguente:

        private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentence from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

    Richiedere alcuni minuti per leggere i commenti per comprendere che cosa significa questo codice.

5. Aprire **Counter.cs** e sostituire il contenuto della classe con le operazioni seguenti:

        private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
            Context.Logger.Info("Execute exit");
        }

    Richiedere alcuni minuti per leggere i commenti per comprendere che cosa significa questo codice.

### <a name="define-the-topology"></a>Definire la topologia

Spouts e bulloni vengono disposte in un grafico, che definisce il flusso dei dati tra i componenti. Per questa topologia, il grafico è:

![immagine della disposizione componenti](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Frasi vengono generate da beccuccio, distribuiti in istanze di bulloni barra di divisione. Bulloni divisione suddivide le frasi in parole, distribuite bulloni contatore.

Poiché il conteggio parole viene mantenuto localmente nell'istanza del contatore, si desidera assicurarsi che parole specifiche flusso per la stessa istanza di bulloni contatore, in modo che è prevista una sola istanza tenere traccia di una parola specifica. Ma per bulloni divisione veramente non è importante quali bulloni riceve la frase, in modo che si desidera semplicemente carico frasi equilibrio tra le istanze.

Aprire **Program.cs**. Il metodo importante è **GetTopologyBuilder**, che viene utilizzato per definire la topologia in cui viene inviata per eccesso. Sostituire il contenuto di **GetTopologyBuilder** con il codice riportato di seguito per implementare la topologia descritta in precedenza:

        // Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

Richiedere alcuni minuti per leggere i commenti per comprendere che cosa significa questo codice.

## <a name="submit-the-topology"></a>Inviare la topologia

1. In **Esplora soluzioni**fare clic sul progetto e selezionare **Invia per eccesso in HDInsight**.

    > [AZURE.NOTE] Se richiesto, immettere le credenziali di accesso per l'abbonamento Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene l'eccesso cluster HDInsight.

2. Selezionare l'eccesso cluster HDInsight dall'elenco a discesa **Eccesso Cluster** e quindi selezionare **Invia**. È possibile controllare se la presentazione viene eseguita correttamente utilizzando la finestra di **Output** .

3. Quando la topologia è stata inviata, dovrebbero essere visualizzate **Topologie eccesso** per il cluster. Selezionare la topologia **WordCount** dall'elenco per visualizzare informazioni sulla topologia di esecuzione.

    > [AZURE.NOTE] È inoltre possibile visualizzare **Il topologie** da **Esplora Server**: espandere **Azure** > **HDInsight**rapida eccesso cluster HDInsight e quindi selezionare **Visualizza eccesso topologie**.

    Utilizzare i collegamenti per il spouts o bulloni per visualizzare informazioni su questi componenti. Verrà aperta una nuova finestra per ogni elemento selezionato.

4. Fare clic su **interruzione** per interrompere la topologia dalla visualizzazione **Riepilogo della topologia** .

    > [AZURE.NOTE] Topologie eccesso continuano a eseguire fino a quando non vengono disattivati o il cluster viene eliminato.

## <a name="transactional-topology"></a>Topologia transazione

La topologia precedente è meno. I componenti all'interno della topologia non implementano le funzionalità per la riproduzione dei messaggi se si verifica un errore di elaborazione da un componente della topologia. Per un esempio di topologia transazione, creare un nuovo progetto e selezionare **Il campione** come tipo di progetto.

Topologie transazione implementano le operazioni seguenti per supportare la riproduzione dei dati:

- **Memorizzazione nella cache dei metadati**: beccuccio necessario archiviare metadati relativi dati creati in modo che i dati possono essere recuperati e generati nuovamente se si verifica un errore. Poiché i dati emessi dal campione sono piccoli, i dati non elaborati per ogni tupla sono archiviati in un dizionario per la riproduzione.

- **ACK**: ogni bulloni nella topologia di possono chiamare `this.ctx.Ack(tuple)` ack correttamente ha elaborato una tupla. Quando tutti i bulloni hanno riconosciuto tupla, il `Ack` del beccuccio viene richiamato. In questo modo beccuccio rimuovere i dati memorizzati nella cache per la riproduzione in quanto i dati è stati elaborati completamente.

- **Errore**: ogni bulloni possono chiamare `this.ctx.Fail(tuple)` per indicare che non è riuscita elaborazione di una tupla. L'errore viene propagato alla `Fail` metodo del beccuccio, in cui è possibile riprodurre della tupla tramite metadati memorizzati nella cache.

- **ID sequenza**: quando si crea una tupla, è possibile specificare un ID sequenza. Deve trattarsi di un valore che identifica la tupla per l'elaborazione di riproduzione (Ack e Fail). Ad esempio, beccuccio nel progetto di **Esempio eccesso** utilizza le operazioni seguenti durante la creazione di dati:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Si crea una nuova tupla contiene una frase al flusso predefinito con il valore di ID sequenza contenuto in **lastSeqId**. In questo esempio **lastSeqId** viene incrementato semplicemente per ogni tupla creato.

Come illustrato nel progetto di **Esempio eccesso** , la transazione o meno un componente può essere impostato in fase di esecuzione in base alla configurazione.

## <a name="hybrid-topology"></a>Topologia ibrido

HDInsight tools per Visual Studio può anche essere utilizzato per creare topologie ibrida, in cui alcuni componenti sono c# e altri linguaggio.

Per un esempio di topologia ibrida, creare un nuovo progetto e selezionare **Il ibrida**. Consente di creare un campione completamente commento che contiene diverse topologie in cui vengono illustrate le operazioni seguenti:

- **Linguaggio spout** e **c# bulloni**: definiti in **HybridTopology_javaSpout_csharpBolt**

    - Una versione transazione sia definita nel **HybridTopologyTx_javaSpout_csharpBolt**

- **C# spout** e **bulloni linguaggio**: definiti in **HybridTopology_csharpSpout_javaBolt**

    - Una versione transazione sia definita nel **HybridTopologyTx_csharpSpout_javaBolt**

        > [AZURE.NOTE] Questa versione viene inoltre illustrato come utilizzare codice Clojure da un file di testo come un componente Java.

Per passare tra la topologia utilizzato quando il progetto viene inviato, è sufficiente spostare il `[Active(true)]` istruzione per la topologia si desidera utilizzare prima di inviarla al cluster.

> [AZURE.NOTE] Tutti i file di linguaggio necessari vengono forniti come parte del progetto nella cartella **JavaDependency** .

Tenere presente quanto segue durante la creazione e invio di una topologia ibrido:

- **JavaComponentConstructor** deve essere utilizzato per creare una nuova istanza della classe Java per un beccuccio o bulloni.

- **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** deve essere utilizzato per serializzare dati o rimuovendoli componenti Java dagli oggetti Java in JSON.

- Quando si invia la topologia nel server, è necessario utilizzare l'opzione **configurazioni aggiuntive** per specificare i **percorsi dei File di linguaggio**. Il percorso specificato dovrebbe essere la directory che contiene i file VASO che contengono le classi Java.

### <a name="azure-event-hubs"></a>Hub evento Azure

Versione SCP.Net 0.9.4.203 introduce una nuova classe e metodo specificamente per l'utilizzo di evento Hub Spout (linguaggio beccuccio che legge dall'Hub evento.) Quando si crea una topologia che utilizza questo beccuccio, utilizzare i metodi seguenti:

- Classe **EventHubSpoutConfig** : crea un oggetto che contiene la configurazione per il componente beccuccio

- Metodo **TopologyBuilder.SetEventHubSpout** : aggiunge il componente evento Hub Spout la topologia

> [AZURE.NOTE] Mentre questi semplificare l'utilizzo con l'Hub di evento Spout più altri componenti di linguaggio, è comunque necessario utilizzare la CustomizedInteropJSONSerializer serializzare dati prodotti dalla beccuccio.

## <a id="configurationmanager"></a>Uso ConfigurationManager

Non usare ConfigurationManager per recuperare i valori di configurazione da bulloni e spout componenti. Questa operazione, verrà visualizzata un'eccezione puntatore null. Se, tuttavia, la configurazione per il progetto viene passata nella topologia di eccesso come una coppia di chiave/valore nel contesto della topologia. Ogni componente che si basa sui valori di configurazione necessario recuperarli da contesto durante l'inizializzazione.

Il codice seguente viene illustrato come recuperare questi valori:

    public class MyComponent : ISCPBolt
    {
        // To hold configuration information loaded from context
        Configuration configuration;
        ...
        public MyComponent(Context ctx, Dictionary<string, Object> parms)
        {
            // Save a copy of the context for this component instance
            this.ctx = ctx;
            // If it exists, load the configuration for the component
            if(parms.ContainsKey(Constants.USER_CONFIG))
            {
                this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
            }
            // Retrieve the value of "Foo" from configuration
            var foo = this.configuration.AppSettings.Settings["Foo"].Value;
        }
        ...
    }

Se si utilizza un `Get` per restituire un'istanza del componente, è necessario assicurarsi che ha superato entrambe le `Context` e `Dictionary<string, Object>` parametri del costruttore. Nell'esempio seguente è una base `Get` metodo che passa correttamente questi valori:

    public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new MyComponent(ctx, parms);
    }

## <a name="how-to-update-scpnet"></a>Come aggiornare SCP.NET

Versioni recenti di SCP.NET supportano l'aggiornamento del pacchetto tramite NuGet. Quando è disponibile un nuovo aggiornamento, si riceverà una notifica di aggiornamento. Per controllare manualmente un aggiornamento, eseguire le operazioni seguenti:

1. In **Esplora soluzioni**fare clic sul progetto e selezionare **Gestisci pacchetti NuGet**.

2. Selezionare **gli aggiornamenti**Gestione pacchetti. Se è disponibile un aggiornamento, tale sconto sarà elencato. Fare clic sul pulsante di **aggiornamento** per il pacchetto di installazione.

> [AZURE.IMPORTANT] Se il progetto è stato creato con una delle versioni precedenti di SCP.NET che non utilizza NuGet aggiornamenti pacchetto, è necessario eseguire la procedura seguente per l'aggiornamento alla nuova versione:
>
> 1. In **Esplora soluzioni**fare clic sul progetto e selezionare **Gestisci pacchetti NuGet**.
> 2. Usare il campo di **ricerca** , cercare e aggiungere **Microsoft.SCP.Net.SDK** al progetto.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="null-pointer-exceptions"></a>Puntatore null eccezioni

Quando si utilizza una topologia c# con un cluster HDInsight basati su Linux, bullone e spout componenti che utilizzano ConfigurationManager per leggere le impostazioni di configurazione in fase di esecuzione potrebbero restituire eccezioni puntatore null. Infatti, la configurazione del dominio caricato non è stato inviato da assembly che contiene il progetto.

La configurazione per il progetto passata nella topologia di eccesso come una coppia di chiave/valore nel contesto della topologia e può essere recuperata dall'oggetto dizionario passato ai componenti quando vengono inizializzati.

Nell'esempio seguente viene caricamento i valori di configurazione dal contesto della topologia, vedere la sezione [ConfigurationManager](#configurationmanager) di questo documento.

### <a name="systemtypeloadexception"></a>TypeLoadException

Quando si utilizza una topologia c# con un cluster HDInsight basati su Linux, è possibile riscontrare l'errore seguente:

    System.TypeLoadException: Failure has occurred while loading a type.

Questo in genere si verifica quando si utilizza un file binario che non è compatibile con la versione di .NET supportati mono.

Per i cluster basati su Linux HDInsight, assicurarsi che il progetto utilizza file binari compilati per .NET 4.5.


### <a name="test-a-topology-locally"></a>Verificare una topologia localmente

Per quanto sia semplice distribuire una topologia a un cluster, in alcuni casi, potrebbe essere necessario verificare una topologia in locale. Utilizzare la procedura seguente per eseguire e testare la topologia di esempio in questa esercitazione in locale nel proprio ambiente di sviluppo.

> [AZURE.WARNING] Test locale funziona solo per topologie sola di base, c#. Non utilizzare locale prova per topologie ibrido o topologie che utilizzano più flussi, come vengono visualizzati errori.

1. In **Esplora soluzioni**fare clic sul progetto e scegliere **proprietà**. Nelle proprietà del progetto, modificare il **tipo di Output** **all'Applicazione Console**.

    ![tipo di output](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

    > [AZURE.NOTE] È necessario modificare il **tipo di Output** alla **Libreria di classi** prima di distribuire la topologia a un cluster.

2. In **Esplora soluzioni**fare clic sul progetto, quindi selezionare **Aggiungi** > **Nuovo elemento**. Selezionare **classe** e immettere **LocalTest.cs** come il nome della classe. Infine, fare clic su **Aggiungi**.

3. Aprire **LocalTest.cs** e aggiungere la seguente istruzione **using** nella parte superiore:

        using Microsoft.SCP;

4. Utilizzare il contenuto della classe **LocalTest** le operazioni seguenti:

        // Drives the topology components
        public void RunTestCase()
        {
            // An empty dictionary for use when creating components
            Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

            #region Test the spout
            {
                Console.WriteLine("Starting spout");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext spoutCtx = LocalContext.Get();
                // Get a new instance of the spout, using the local context
                Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

                // Emit 10 tuples
                for (int i = 0; i < 10; i++)
                {
                    sentences.NextTuple(emptyDictionary);
                }
                // Use LocalContext to persist the data stream to file
                spoutCtx.WriteMsgQueueToFile("sentences.txt");
                Console.WriteLine("Spout finished");
            }
            #endregion

            #region Test the splitter bolt
            {
                Console.WriteLine("Starting splitter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext splitterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


                // Set the data stream to the data created by the spout
                splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    splitter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                splitterCtx.WriteMsgQueueToFile("splitter.txt");
                Console.WriteLine("Splitter bolt finished");
            }
            #endregion

            #region Test the counter bolt
            {
                Console.WriteLine("Starting counter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext counterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Counter counter = Counter.Get(counterCtx, emptyDictionary);

                // Set the data stream to the data created by splitter bolt
                counterCtx.ReadFromFileToMsgQueue("splitter.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    counter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                counterCtx.WriteMsgQueueToFile("counter.txt");
                Console.WriteLine("Counter bolt finished");
            }
            #endregion
        }

    Richiedere alcuni minuti per leggere i commenti del codice. Questo codice utilizza **LocalContext** per eseguire i componenti nell'ambiente di sviluppo e mantiene il flusso di dati tra i componenti per i file di testo nell'unità locale.

5. Aprire **Program.cs** e aggiungere il metodo **Main** quanto segue:

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
        // Create test instance
        LocalTest tests = new LocalTest();
        // Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

6. Salvare le modifiche, quindi fare clic su **F5** o selezionare **Debug** > **Avvia debug** per avviare il progetto. Una finestra della console dovrà essere visualizzato e registrare lo stato come lo stato di avanzamento di test. Quando **che test termine** viene visualizzata, premere un tasto per chiudere la finestra.

7. Utilizzare **Esplora** risorse per individuare la directory contenente il progetto, ad esempio **C:\Users\<your_user_name > \Documents\Visual 2013\Projects\WordCount\WordCount Studio**. In questa directory, aprire **Bin**e quindi fare clic su **Debug**. Verrà visualizzato i file di testo creati durante l'esecuzione di test: sentences.txt, counter.txt e splitter.txt. Aprire ogni file di testo ed esaminare i dati.

    > [AZURE.NOTE] Dati di tipo stringa viene mantenuti come una matrice di valori decimali in questi file. Ad esempio \[[97,103,111]] in **splitter.txt** file è la parola "e".

Anche se il test di una parola base contare locale è un'operazione semplice, che il valore effettivo è disponibile quando si dispone di una topologia complessa che comunica con origini dati esterne o l'esecuzione di analisi di dati complessi. Quando si lavora in un progetto, potrebbe essere necessario impostare interruzione e passaggio tramite il codice nei componenti per isolare problemi.

> [AZURE.NOTE] Assicurarsi di impostare il **tipo di progetto** alla **Libreria di classi** prima di distribuire in eccesso cluster HDInsight.

### <a name="log-information"></a>Informazioni del log

È possibile registrare informazioni facilmente dai componenti della topologia utilizzando `Context.Logger`. Ad esempio, le operazioni seguenti creerà una voce del log informativo:

    Context.Logger.Info("Component started");

Informazioni registrate possono essere visualizzate da **Registro del servizio Hadoop**, che si trovano in **Esplora Server**. Espandere la voce per l'eccesso cluster HDInsight, quindi espandere **Hadoop servizio Log**. Infine, selezionare il file di log da visualizzare.

> [AZURE.NOTE] I log sono archiviati in account di archiviazione di Azure che viene utilizzato il cluster. Se si tratta di una sottoscrizione diversa da quella che si è connessi a con Visual Studio, è necessario effettuare l'abbonamento che contiene l'account di archiviazione per visualizzare queste informazioni.

### <a name="view-error-information"></a>Informazioni sugli errori di visualizzazione

Per visualizzare gli errori che si sono verificati in una topologia in esecuzione, utilizzare la procedura seguente:

1. Da **Esplora Server**mouse eccesso cluster HDInsight e scegliere **visualizzazione eccesso topologie**.

2. Per **Spout** e **bulloni**, la colonna **Ultimo errore** includerà informazioni sull'ultimo errore si è verificato.

3. Selezionare **l'Id Spout** o **Bulloni** per il componente che contiene un errore nell'elenco. Nella pagina dettagli che viene visualizzata, ulteriori informazioni sull'errore verranno elencati nella sezione **errori** nella parte inferiore della pagina.

4. Per ottenere ulteriori informazioni, selezionare una **porta** nella sezione **esecutori** della pagina per visualizzare il log di lavoro eccesso per ultimi minuti.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato illustrato come sviluppare e distribuire topologie eccesso dagli strumenti HDInsight per Visual Studio, informazioni su come per [elaborare gli eventi da Azure evento Hub con eccesso su HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Per un esempio di una topologia c# che divide il flusso di dati in più flussi, vedere [esempio c# eccesso](https://github.com/Blackmist/csharp-storm-example).

Per scoprire ulteriori informazioni sulla creazione di c# topologie, visitare [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Per altre modalità per lavorare con HDInsight e altre eccesso su campioni HDinsight, vedere le operazioni seguenti:

**Microsoft SCP.NET**

* [Guida di programmazione SCP](hdinsight-storm-scp-programming-guide.md)

**Eccesso Apache in HDInsight**

- [Distribuire e monitorare topologie con eccesso Apache in HDInsight](hdinsight-storm-deploy-monitor-topology.md)

- [Esempi di topologie per eccesso in HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop in HDInsight**

- [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

- [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

- [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

- [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started.md)
