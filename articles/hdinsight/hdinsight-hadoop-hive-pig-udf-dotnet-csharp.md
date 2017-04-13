<properties
    pageTitle="Usare c# con Hive e maialino in Hadoop in HDInsight | Microsoft Azure"
    description="Informazioni su come utilizzare c# a funzioni definite dall'utente (utente) con Hive e maialino streaming in Azure HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="larryfr"/>


#<a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Utilizzare le funzioni definite dall'utente di c# con Hive e maialino streaming sul Hadoop in HDInsight

Hive e maialino sono ideali per l'utilizzo di dati di Azure HDInsight, ma a volte è necessario un linguaggio più generale. Hive e maialino consentono di chiamare codice esterno tramite le funzioni definite dall'utente (funzioni definite dall'utente) o trasmissione.

In questo documento per informazioni su come utilizzare c# con Hive e maialino.

##<a name="prerequisites"></a>Prerequisiti

* Windows 7 o versione successiva.

* Visual Studio con le versioni seguenti:

    * Visual Studio 2012 Professional/Premium/Ultimate con [aggiornamento 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 Community/Professional/Premium/Ultimate con [aggiornamento 4](https://www.microsoft.com/download/details.aspx?id=44921)

    * Visual Studio 2015

* Hadoop cluster HDInsight, vedere [disposizione un cluster di HDInsight](hdinsight-provision-clusters.md) per la procedura per creare un cluster

* Hadoop Tools per Visual Studio. Vedere [Introduzione all'utilizzo di HDInsight Hadoop Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) per la procedura di installazione e configurazione gli strumenti.

##<a name="net-on-hdinsight"></a>.NET su HDInsight

Il .NET common language runtime (CLR) e Framework vengono installati per impostazione predefinita nei cluster HDInsight basato su Windows. In questo modo è possibile utilizzare le applicazioni c# con Hive e maialino streaming (dati vengono passati tra Hive/maialino e l'applicazione c# tramite stdout/stdin).

> [AZURE.NOTE] Attualmente non è disponibile alcun supporto per l'esecuzione di funzioni definite dall'utente di .NET Framework nei cluster basati su Linux HDInsight. 

##<a name="net-and-streaming"></a>.NET e streaming

Streaming comporta l'Hive e maialino passare dati a un'applicazione esterna su stdout e ricevere i risultati sopra stdin. Per le applicazioni c#, questa viene eseguita più facilmente tramite `Console.ReadLine()` e `Console.WriteLine()`.

Poiché Hive e maialino necessaria richiamare l'applicazione in fase di esecuzione, il modello **Applicazione Console** da utilizzare per i progetti c#.

##<a name="hive-and-c35"></a>Hive e C e 35 #;

###<a name="create-the-c-project"></a>Creare il progetto c#

1. Aprire Visual Studio e creare una nuova soluzione. Per il tipo di progetto, selezionare **Applicazione Console**e assegnare al nuovo progetto **HiveCSharp**.

2. Sostituire il contenuto di **Program.cs** con le operazioni seguenti:

        using System;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;

        namespace HiveCSharp
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Parse the string, trimming line feeds
                        // and splitting fields at tabs
                        line = line.TrimEnd('\n');
                        string[] field = line.Split('\t');
                        string phoneLabel = field[1] + ' ' + field[2];
                        // Emit new data to stdout, delimited by tabs
                        Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                    }
                }
                /// <summary>
                /// Returns an MD5 hash for the given string
                /// </summary>
                /// <param name="input">string value</param>
                /// <returns>an MD5 hash</returns>
                static string GetMD5Hash(string input)
                {
                    // Step 1, calculate MD5 hash from input
                    MD5 md5 = System.Security.Cryptography.MD5.Create();
                    byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                    byte[] hash = md5.ComputeHash(inputBytes);

                    // Step 2, convert byte array to hex string
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < hash.Length; i++)
                    {
                        sb.Append(hash[i].ToString("x2"));
                    }
                    return sb.ToString();
                }
            }
        }

3. Compilare il progetto.

###<a name="upload-to-storage"></a>Caricare allo spazio di archiviazione

1. In Visual Studio, aprire **Esplora Server**.

3. Espandere **Azure**e quindi espandere **HDInsight**.

4. Se richiesto, immettere le credenziali di Azure abbonamento e quindi fare clic su **Accedi**.

5. Espandere il cluster HDInsight che si desidera distribuire per questa applicazione e quindi espandere **Account predefinito di spazio di archiviazione**.

    ![Esplora server con l'account di archiviazione per il cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. Fare doppio clic sul **Contenitore predefinito** per il cluster. Si aprirà una nuova finestra che viene visualizzato il contenuto del contenitore predefinito.

7. Fare clic sull'icona di caricamento e quindi passare alla cartella **bin\debug** per il progetto **HiveCSharp** . Infine, selezionare il file **HiveCSharp.exe** e fare clic su **Ok**.

    ![icona di upload](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. Una volta completato il caricamento, sarà possibile usare l'applicazione di una query Hive.

###<a name="hive-query"></a>Query hive

1. In Visual Studio, aprire **Esplora Server**.

2. Espandere **Azure**e quindi espandere **HDInsight**.

5. Rapida cluster che è stato distribuito l'applicazione **HiveCSharp** a e quindi selezionare **scrivere una Query Hive**.

6. Utilizzare le operazioni seguenti per la query Hive:

        add file wasbs:///HiveCSharp.exe;

        SELECT TRANSFORM (clientid, devicemake, devicemodel)
        USING 'HiveCSharp.exe' AS
        (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

    Consente di selezionare il `clientid`, `devicemake`, e `devicemodel` campi da `hivesampletable`e passa i campi all'applicazione di HiveCSharp.exe. La query prevede l'applicazione per restituire tre campi, vengono memorizzati come `clientid`, `phoneLabel`, e `phoneHash`. La query prevede anche trovare HiveCSharp.exe nella radice del contenitore di spazio di archiviazione predefinito (`add file wasbs:///HiveCSharp.exe`).

5. Fare clic su **Invia** per inviare il processo al cluster HDInsight. Verrà aperta la finestra di **Riepilogo Hive** .

6. Fare clic su **Aggiorna** per aggiornare il riepilogo fino a quando non viene modificato **Lo stato dei processi** **completato**. Per visualizzare l'output di processo, fare clic su **Output processo**.

##<a name="pig-and-c35"></a>Maialino e C e 35 #;

###<a name="create-the-c-project"></a>Creare il progetto c#

1. Aprire Visual Studio e creare una nuova soluzione. Per il tipo di progetto, selezionare **Applicazione Console**e assegnare al nuovo progetto **PigUDF**.

2. Sostituire il contenuto del file **Program.cs** con le operazioni seguenti:

        using System;

        namespace PigUDF
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Fix formatting on lines that begin with an exception
                        if(line.StartsWith("java.lang.Exception"))
                        {
                            // Trim the error info off the beginning and add a note to the end of the line
                            line = line.Remove(0, 21) + " - java.lang.Exception";
                        }
                        // Split the fields apart at tab characters
                        string[] field = line.Split('\t');
                        // Put fields back together for writing
                        Console.WriteLine(String.Join("\t",field));
                    }
                }
            }
        }

    Questa applicazione analizza le righe inviate da maialino e riformattato righe che iniziano con `java.lang.Exception`.

3. Salvare **Program.cs**e quindi compilare il progetto.

###<a name="upload-the-application"></a>Caricare l'applicazione

1. Maialino streaming prevede l'applicazione locale nel file system cluster. Attivare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [connessione a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Una volta connessa, copiare **PigUDF.exe** dalla directory **bin/debug** per il progetto PigUDF sul computer locale e incollarla nella directory **% PIG_HOME %** nel cluster.

###<a name="use-the-application-from-pig-latin"></a>Usare l'applicazione da maialino latino

1. Iniziare la riga di comando Hadoop sessione Desktop remoto utilizzando l'icona **della riga di comando Hadoop** sul desktop.

2. Per avviare la riga di comando maialino, utilizzare le operazioni seguenti:

        cd %PIG_HOME%
        bin\pig

    Verrà visualizzata con una `grunt>` prompt dei comandi.

3. Immettere le operazioni seguenti per eseguire un semplice processo di maialino tramite l'applicazione di .NET Framework:

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Il `DEFINE` istruzione consente di creare un alias di `streamer` Applications pigudf.exe e `SHIP` distribuisce tra i nodi del cluster. In un secondo momento, `streamer` viene utilizzata con la `STREAM` operatore per elaborare le singole righe contenute nel registro e restituire i dati come una serie di colonne.

> [AZURE.NOTE] Il nome dell'applicazione che viene utilizzato per lo streaming deve essere racchiuso il \` (apice inverso) carattere quando alias, e ' (virgoletta singola) quando si utilizza `SHIP`.

3. Dopo aver immesso l'ultima riga, deve iniziare il processo. Alla fine verrà restituito output simile al seguente:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##<a name="summary"></a>Riepilogo

In questo documento sono state come usare un'applicazione di .NET Framework da Hive e maialino HDInsight. Per informazioni su come usare Python con Hive e maialino, vedere [Utilizzare Python con Hive e maialino in HDInsight](hdinsight-python.md).

Per altri modi per usare maialino e Hive e per informazioni sull'uso di MapReduce, vedere le operazioni seguenti:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)
