<properties
    pageTitle="Serializzare dati con Microsoft Avro Library | Microsoft Azure"
    description="Informazioni su come Azure HDInsight utilizza Avro per serializzare dati."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializzare dati Hadoop con Microsoft Avro Library

In questo argomento viene illustrato come utilizzare la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Libreria di Avro</a> per serializzare oggetti e altre strutture di dati nei flussi per mantenere le a memoria, un database o un file, nonché come deserializzare in modo da recuperare gli oggetti originali.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
Il sistema di serializzazione Apache Avro dati per l'ambiente Microsoft.NET di implementazione nella <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Raccolta Avro Microsoft</a> . Apache Avro fornisce un formato interscambio dati binari compatta per la serializzazione. Utilizza <a href="http://www.json.org" target="_blank">JSON</a> per definire uno schema indipendente dalla lingua che copra l'interoperabilità lingua. Dati serializzati in una lingua possono essere letti in un altro. C, C++, c#, Java, PHP, Python e trascrizione sono attualmente supportati. Sono disponibili informazioni dettagliate sul formato <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro specifica</a>. Si noti che la versione corrente di Microsoft Avro Library non supporta la procedura remota (RPC) parte di questa specifica delle chiamate.

Rappresentazione serializzata di un oggetto nel sistema di Avro è costituito da due parti: schema e il valore effettivo. Lo schema Avro descrive il modello di dati indipendente dalla lingua dei dati serializzati con JSON. È presente side-by-side con una rappresentazione binaria dei dati. Con lo schema separato dalla rappresentazione binaria consente a tutti gli oggetti devono essere scritti con senza costi generali per valore, apportare piccole serializzazione veloce e la rappresentazione.

##<a name="the-hadoop-scenario"></a>Lo scenario Hadoop
Il formato di serializzazione Apache Avro usato in altri ambienti Apache Hadoop e Azure HDInsight. Avro offre un modo comodo per rappresentare le strutture di dati complessi all'interno di un processo Hadoop MapReduce. Il formato di file Avro (file contenitore di oggetto Avro) è stato progettato per supportare il modello di programmazione MapReduce distribuito. La caratteristica che consente la distribuzione è che i file siano "splittable" nel senso che sia possibile qualsiasi punto di un file di ricerca e avviare la lettura da un particolare blocco.

##<a name="serialization-in-avro-library"></a>Serializzazione nella raccolta Avro
La raccolta di .NET per Avro in due modi diversi di serializzare oggetti:

- **riflesso** - schema di JSON per i tipi di viene compilato automaticamente dai dati di contratto attributi dei tipi di .NET da serializzare.
- **record generico** - JSON A schema in modo esplicito è specificato in un record rappresentato dalla classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) quando nessun tipo .NET è presente per descrivere lo schema per i dati da serializzare.

Quando si conosce lo schema di dati per la scrittura e l'utilità per la lettura del flusso, è possono inviare i dati senza lo schema. In casi quando viene utilizzato un file di Avro oggetto contenitore, lo schema è archiviato all'interno del file. È possibile specificare, ad esempio il codec utilizzato per la compressione dei dati, gli altri parametri. Gli scenari seguenti sono descritti in modo più dettagliato e illustrati negli esempi di codice riportata di seguito.


## <a name="install-avro-library"></a>Installare Avro libreria

Di seguito sono necessari prima di installare la raccolta:

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 o versioni successive)

Si noti che la dipendenza Newtonsoft.Json.dll viene scaricata automaticamente con l'installazione di Microsoft Avro Library. Procedura per l'oggetto è disponibile nella sezione seguente.


Microsoft Avro Library viene distribuito come pacchetto NuGet che può essere installato da Visual Studio tramite la procedura seguente:

1. Selezionare la scheda **progetto** -> **Gestisci pacchetti di NuGet**
2. Cercare "Microsoft.Hadoop.Avro" nella casella di **Ricerca Online** .
3. Fare clic sul pulsante **Installa** accanto a **Microsoft Azure HDInsight Avro raccolta**.

Si noti che la Newtonsoft.Json.dll (> = 6.0.4) dipendenza scaricato anche automaticamente con Microsoft Avro Library.

È consigliabile visitare la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page di Microsoft Avro Library</a> per leggere le note sulla versione corrente.


Il codice sorgente Microsoft Avro Library è disponibile nella <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page di Microsoft Avro Library</a>.

##<a name="compile-schemas-using-avro-library"></a>Compilare utilizzando Avro raccolta schemi

Microsoft Avro Library contiene un'utilità per la generazione di codice che consente di creare tipi c# automaticamente in base allo schema JSON definito in precedenza. L'utilità per la generazione di codice non viene distribuito come un eseguibile binario, ma può essere compilato facilmente tramite la procedura seguente:

1. Scaricare il file con estensione zip con la versione più recente del codice sorgente HDInsight SDK da <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK per Hadoop</a>. Fare clic sull'icona di **Download** , non sulla scheda **download** .

2. Estrarre SDK HDInsight in una directory sul computer con .NET Framework 4 installato e connesso a Internet per il download dei pacchetti NuGet dipendenza necessarie. Di seguito si presuppone che il codice sorgente viene estratto per C:\SDK.

3. Passare alla cartella C:\SDK\src\Microsoft.Hadoop.Avro.Tools ed eseguire bat. (Il file verrà chiamato MSBuild dalla distribuzione di versione a 32 bit di .NET Framework. Se si vuole usare la versione a 64 bit, modificare bat, seguire i commenti all'interno del file.) Assicurarsi che la compilazione venga eseguita correttamente. (In alcuni sistemi MSBuild potrebbe generare avvisi. Questi avvisi non interessano l'utilità, purché non siano presenti errori di compilazione.)

4. L'utilità compilato è disponibile in C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Per acquisire familiarità con la sintassi della riga di comando, eseguire il comando seguente dalla cartella in cui si trova l'utilità per la generazione di codice:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Per verificare l'utilità, è possibile generare classi c# dal file di schema JSON di esempio fornito con il codice sorgente. Eseguire il comando seguente:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Questa operazione serve a produrre due c# file nella cartella corrente: SensorData.cs e Location.cs.

Per comprendere la logica che utilizza l'utilità per la generazione di codice durante la conversione di schema JSON in tipi di c#, vedere il file GenerationVerification.feature nella C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Si noti che gli spazi dei nomi sono estratti dallo schema JSON, utilizzando la logica descritta nel file indicato nel paragrafo precedente. Gli spazi dei nomi estratte dallo schema hanno la precedenza su qualsiasi elemento viene fornito con il parametro /n nella riga di comando utilità. Se si vuole ignorare gli spazi dei nomi contenuti lo schema, utilizzare il parametro /nf. Ad esempio, per modificare tutti gli spazi dei nomi dal SampleJSONSchema.avsc a my.own.nspace, eseguire il comando seguente:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Esempi
Sei esempi forniti in questo argomento viene illustrato diversi scenari supportati da Microsoft Avro Library. Microsoft Avro Library è progettato per funzionare con qualsiasi flusso. In questi esempi, i dati vengono modificati tramite flussi di memoria invece di flussi di file o database per la semplicità e la coerenza. L'approccio adottato in un ambiente di produzione dipenderà i requisiti dello scenario, origine dati e volume, i vincoli delle prestazioni e altri fattori.

I primi due esempi viene illustrato come serializzare e deserializzare dati nei buffer di flusso della memoria mediante riflesso e record generico. Lo schema di questi due casi verrà considerato uguale a essere condivisi tra i lettori e writer fuori banda.

Negli esempi terzi e quarto viene illustrato come serializzare e deserializzare dati sull'utilizzo di Avro oggetto contenitore. Quando vengono memorizzati in un file contenitore Avro, lo schema viene sempre memorizzato è perché lo schema deve essere condivisa per la deserializzazione.

Esempio contenente i primi quattro esempi di può essere scaricato dal sito <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">esempi di codice Azure</a> .

Nell'esempio quinto viene illustrato come utilizzare un codec di compressione personalizzata per i file di Avro oggetto contenitore. Un campione contenente il codice per questo esempio può essere scaricato dal sito <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">esempi di codice Azure</a> .

Nell'esempio sesto viene illustrato come utilizzare serializzazione Avro per caricare dati in archiviazione Blob Azure e quindi analizzare utilizzando Hive con un cluster di HDInsight (Hadoop). Possibile scaricarlo dal sito di <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">esempi di codice Azure</a> .

Qui sono disponibili collegamenti a sei esempi descritti nell'argomento:

 * <a href="#Scenario1">**Serializzazione con riflesso**</a> - schema di JSON per i tipi di serializzare viene automaticamente creato dai dati di attributi contratto.
 * <a href="#Scenario2">**Serializzazione con record generico**</a> - schema di JSON è specificato in modo esplicito in un record alcun tipo di .NET non è disponibile per riflesso.
 * <a href="#Scenario3">**Serializzazione mediante file contenitore oggetto con riflesso**</a> - schema JSON il automaticamente creato e condiviso con i dati serializzati tramite un file di Avro oggetto contenitore.
 * <a href="#Scenario4">**Serializzazione mediante file contenitore oggetto con record generico**</a> - schema JSON il in modo esplicito specificato prima della serializzazione e condivise con i dati tramite un file di Avro oggetto contenitore.
 * <a href="#Scenario5">**Serializzazione mediante file contenitore oggetto con un codec di compressione personalizzata**</a> - nell'esempio viene illustrato come creare un file di contenitore oggetto Avro con un'implementazione .NET personalizzata del codec di compressione dati Deflate.
 * <a href="#Scenario6">**Utilizzo Avro per caricare i dati per il servizio di Microsoft Azure HDInsight**</a> - nell'esempio illustrato come serializzazione Avro interagisce con il servizio HDInsight. Per eseguire questo esempio sono necessari un abbonamento attivo a Azure e l'accesso a un cluster di Azure HDInsight.

###<a name="Scenario1"></a>Esempio 1: Serializzazione con riflesso

Lo schema JSON per i tipi di può essere automaticamente creato da Microsoft Avro Library tramite riflesso dai dati di attributi di contratto degli oggetti c# da serializzare. Microsoft Avro Library crea un [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) per identificare i campi da serializzare.

In questo esempio, gli oggetti (una classe **SensorData** con una struttura di **posizione** membro) vengono serializzati in un flusso di memoria e questo flusso di oggetti, a sua volta. Il risultato viene quindi confrontato l'istanza iniziale per verificare che l'oggetto **SensorData** recuperato è identico all'originale.

Lo schema in questo esempio viene considerato essere condivisi tra i lettori e writer, in modo che il formato del contenitore oggetto Avro non è necessario. Per un esempio di come serializzare e deserializzare dati nei buffer di memoria utilizzando riflesso con il formato del contenitore oggetto quando lo schema deve essere condiviso con i dati, vedere <a href="#Scenario3">serializzazione mediante file contenitore oggetto con riflesso</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Esempio 2: Serializzazione a un record generico

Uno schema JSON può essere specificato in modo esplicito in un record generico quando riflesso non può essere utilizzato in quanto i dati non possono essere rappresentati tramite le classi .NET con un contratto di dati. Questo metodo è generalmente più lento dell'utilizzo riflesso. In tal caso, lo schema per i dati possono essere dinamici, ad esempio, non essere noto in fase di compilazione. Dati rappresentati come file con valori delimitati da virgole (CSV) il cui schema è sconosciuto fino a quando non viene trasformato in formato Avro in fase di esecuzione sono illustrato un esempio di questo tipo di scenario dinamico.

In questo esempio viene illustrato come creare e utilizzare un [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) per specificare esplicitamente uno schema JSON, come popolare con i dati e quindi come serializzare e deserializzarla. Il risultato viene quindi confrontato l'istanza iniziale per verificare che il record recuperato è identico all'originale.

Lo schema in questo esempio viene considerato essere condivisi tra i lettori e writer, in modo che il formato del contenitore oggetto Avro non è necessario. Per un esempio di come serializzare e deserializzare dati nei buffer di memoria utilizzando un record generico con il formato del contenitore oggetto quando lo schema deve essere incluso con i dati serializzati, vedere l'esempio <a href="#Scenario4">serializzazione mediante file contenitore oggetto con record generico</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Esempio 3: Serializzazione utilizzando file contenitore oggetto e serializzazione con riflesso

In questo esempio è simile a uno scenario di <a href="#Scenario1">esempio prima</a>, in cui lo schema viene specificato in modo implicito con riflesso. La differenza è che qui, lo schema non presuppone che sia nota per il lettore che deserializza essa. Gli oggetti **SensorData** da serializzare e lo schema specificato in modo implicito vengono memorizzati in un file di contenitore oggetto Avro rappresentato dalla classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

I dati sono serializzati in questo esempio con [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) e deserializzati con [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Il risultato viene quindi confrontato con le istanze iniziale per garantire l'identità.

I dati nel file contenitore oggetto viene compresso tramite il valore predefinito [**Deflate**] [ deflate-100] codec di compressione da .NET Framework 4. Vedere l' <a href="#Scenario5">esempio quinta</a> in questo argomento per informazioni su come usare una versione più recente e superiore di [**Deflate**] [ deflate-110] codec di compressione disponibile in .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Esempio 4: Serializzazione con file contenitore oggetto e serializzazione record generico

In questo esempio è simile a uno scenario di <a href="#Scenario2">secondo esempio</a>, in cui lo schema viene specificato in modo esplicito con JSON. La differenza è che qui, lo schema non presuppone che sia nota per il lettore che deserializza essa.

Il set di dati di test è raccolti in un elenco di oggetti [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) tramite uno schema JSON definito in modo esplicito e quindi archiviato in un file contenitore oggetto rappresentato dalla classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Questo file contenitore crea un writer che viene utilizzato per serializzare i dati non compressi, in un flusso di memoria quindi salvato in un file. Il parametro [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) utilizzato per la creazione del lettore specifica questi dati non verranno compressi.

I dati sono quindi leggere il file e deserializzati in un insieme di oggetti. Questa raccolta viene confrontata con l'elenco iniziale dei record Avro per confermare che sono identici.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Esempio 5: Serializzazione utilizzo dei file contenitore di oggetto con un codec di compressione personalizzata

Nell'esempio quinto viene illustrato come utilizzare un codec di compressione personalizzata per i file di Avro oggetto contenitore. Un campione contenente il codice per questo esempio può essere scaricato dal sito [esempi di codice Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) .

La [Specifica Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) consente l'uso di un codec di compressione facoltativo (oltre alle impostazioni predefinite di **Null** e **Deflate** ). In questo esempio non è l'implementazione di un codec completamente nuovo, ad esempio Snappy (indicate come sui codec supportati facoltativo nella [Specifica Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Viene illustrato come utilizzare l'implementazione di .NET Framework 4.5 di [**Deflate**] [ deflate-110] codec che fornisce un algoritmo di compressione migliore in base alla libreria di compressione [zlib](http://zlib.net/) rispetto alla versione di .NET Framework 4 predefinito.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Esempio 6: Utilizzo di Avro per caricare i dati per il servizio di Microsoft Azure HDInsight

L'esempio sesto illustra alcune tecniche di programmazione correlati a interagire con il servizio di Azure HDInsight. Un campione contenente il codice per questo esempio può essere scaricato dal sito [esempi di codice Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) .

Nell'esempio vengono eseguite le operazioni seguenti:

* Si connette a un cluster di servizio HDInsight esistente.
* Serializza più file CSV e consente di caricare il risultato archiviazione Blob Azure. (File CSV distribuiti insieme a campione e rappresentano un estratto i dati cronologici AMEX azionario distribuiti da [Infochimps](http://www.infochimps.com/) per il periodo 1970 2010. Nell'esempio legge i dati del file CSV, converte i record in istanze della classe **azionario** e quindi serializza loro utilizzando riflesso. Definizione di tipo per quotazioni azionarie viene creata da uno schema JSON tramite l'utilità per la generazione di codice Microsoft Avro Library.
* Crea una nuova tabella esterna denominata **scorte** Hive e i collegamenti per i dati caricato nel passaggio precedente.
* Esegue una query utilizzando Hive sulla tabella **scorte** .

Inoltre, nell'esempio vengono eseguite una procedura di pulizia prima e dopo l'esecuzione di operazioni principali. Durante la pulizia, vengono rimossi tutti i dati correlati Blob Azure e le cartelle e si elimina la tabella Hive. È inoltre possibile richiamare la procedura di pulizia dalla riga di comando di esempio.

Nell'esempio sono previsti i prerequisiti seguenti:

* Un abbonamento attivo a Microsoft Azure e il relativo ID abbonamento.
* Certificato di gestione di sottoscrizione con la chiave privata corrispondente. Il certificato deve essere installato nell'archivio privato utente corrente nel computer usato per eseguire l'esempio.
* Un cluster HDInsight attivo.
* Un account di archiviazione Azure collegato al cluster HDInsight da prerequisito precedente, con la chiave primaria o secondaria accesso corrispondente.

Tutte le informazioni dai prerequisiti devono essere immesse nel file di configurazione di esempio prima di eseguita l'esempio. Esistono due modi per eseguire questa operazione:

* Modificare il file di App nella directory radice dell'esempio e quindi compilare l'esempio
* Creare innanzitutto il campione e quindi modifica AvroHDISample.exe.config nella directory di generazione

In entrambi i casi tutte le modifiche devono essere eseguite nel **<appSettings>** sezione Impostazioni. Seguire i commenti nel file.
Nell'esempio viene avviata dalla riga di comando eseguendo il comando seguente (in caso contrario, nel punto in cui il file con estensione zip con l'esempio stato considerato come estrarre C:\AvroHDISample; se usare il percorso del file pertinenti):

    AvroHDISample run C:\AvroHDISample\Data

Per pulire il cluster, eseguire il comando seguente:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
