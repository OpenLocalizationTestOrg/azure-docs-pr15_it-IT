<properties
pageTitle="Utilizzare una linguaggio definite dall'utente funzione con Hive in HDInsight | Microsoft Azure"
description="Informazioni su come creare e utilizzare una linguaggio definite dall'utente funzione da Hive in HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilizzare un linguaggio utente con Hive in HDInsight

Hive è ideale per l'utilizzo di dati in HDInsight, ma a volte è necessario un più generale allo scopo di lingua. Hive consente di creare funzioni definite dall'utente (utente) con una vasta gamma di linguaggi di programmazione. In questo documento si imparerà a utilizzare un utente di linguaggio dall'Hive.

## <a name="requirements"></a>Requisiti

* Una sottoscrizione di Azure

* Un cluster di HDInsight (Windows o basati su Linux)

    > [AZURE.NOTE] La maggior parte dei passaggi descritti in questo documento lavorerà su entrambi i tipi cluster; Tuttavia, i passaggi necessari per caricare utente compilato il cluster ed eseguirlo sono specifici di cluster basati su Linux. Vengono forniti collegamenti a informazioni che possono essere utilizzate con i cluster basato su Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o versioni successive (o un equivalente, ad esempio OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Un editor di testo o IDE Java

    > [AZURE.IMPORTANT] Se si utilizza un server basati su Linux HDInsight, ma si creano file Python su un client di Windows, è necessario usare un editor che utilizza una nuova riga come terminazioni di riga. Se non si è certi che l'editor utilizza nuova riga o CRLF, vedere la sezione [risoluzione dei problemi](#troubleshooting) per istruzioni sulla rimozione carattere CR utilizzando le utilità cluster HDInsight.

## <a name="create-an-example-udf"></a>Creare un utente di esempio

1. Dalla riga di comando, utilizzare le operazioni seguenti per creare un nuovo progetto Maven:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Se si utilizza PowerShell, è necessario inserire virgolette i parametri. Ad esempio `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Verrà creato una nuova directory denominata __exampleudf__, che contiene il progetto Maven.

2. Dopo aver creato il progetto, eliminare la directory __exampleudf/src/test__ creata come parte del progetto. verrà non utilizzato per questo esempio.

3. Aprire __exampleudf/pom.xml__e sostituire le attuali `<dependencies>` voce con le operazioni seguenti:

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Queste voci specifichino la versione di Hadoop e Hive inclusa HDInsight 3.3 e 3.4 cluster. È possibile trovare informazioni sulle versioni di Hadoop e Hive fornito con HDInsight dal documento di [controllo delle versioni componente HDInsight](hdinsight-component-versioning.md) .

    Aggiungere un `<build>` sezione prima di `</project>` riga alla fine del file. In questa sezione deve contenere le operazioni seguenti:

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
                        <filters>
                            <filter>
                                <artifact>*:*</artifact>
                                <excludes>
                                    <exclude>META-INF/*.SF</exclude>
                                    <exclude>META-INF/*.DSA</exclude>
                                    <exclude>META-INF/*.RSA</exclude>
                                </excludes>
                            </filter>
                        </filters>
                    </configuration>
                    <executions>
                        <execution>
                            <phase>package</phase>
                            <goals>
                                <goal>shade</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    
    Queste voci definiscono la modalità di compilazione del progetto. In particolare, la versione di linguaggio che utilizza il progetto e modalità di compilazione di un uberjar per la distribuzione al cluster.

    Una volta apportate le modifiche, salvare il file.

4. Rinominare __exampleudf/src/main/java/com/microsoft/examples/App.java__ in __ExampleUDF.java__e quindi aprire il file nell'editor.

5. Sostituire il contenuto del file __ExampleUDF.java__ con il seguente, quindi salvare il file.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Questo implementata un'utente accetta un valore stringa che restituisce la versione in minuscolo della stringa.

## <a name="build-and-install-the-udf"></a>Creare e installare l'utente

1. Utilizzare il comando seguente per compilare e creare un pacchetto l'utente:

        mvn compile package

    Verrà compilare e quindi creare un pacchetto l'utente in __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Utilizzare la `scp` comando in cui copiare il file cluster HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Sostituire __myuser__ con l'account utente SSH per il cluster. Sostituire __miocluster__ con il nome del cluster. Se è stata usata una password per proteggere account SSH, verrà richiesto di immettere la password. Se si utilizza un certificato, potrebbe essere necessario utilizzare la `-i` parametro per specificare il file di chiave privato.

3. Connettersi al cluster utilizzando SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere i documenti seguenti.

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Da sessione SSH, copiare il file vaso allo spazio di archiviazione HDInsight.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Usare l'utente dall'Hive

1. Utilizzare le operazioni seguenti per avviare il client Beeline della sessione SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Questo comando si presuppone che è stata usata l'impostazione predefinita __dell'amministratore__ per l'account di accesso per il cluster.

2. Dopo aver concluso il `jdbc:hive2://localhost:10001/>` richiesto, immettere le operazioni seguenti per aggiungere l'utente a Hive ed esporre come una funzione.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Utilizzare l'utente per convertire valori recuperati da una tabella in minuscolo stringhe.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Questo sarà selezionare la piattaforma del dispositivo (Android, Windows, iOS, e così via) dalla tabella, convertire la stringa in lettere minuscole e quindi visualizzarli. L'output apparirà simile al seguente.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Passaggi successivi

Per altre modalità per lavorare con Hive, vedere [Usare Hive con HDInsight](hdinsight-use-hive.md).

Per ulteriori informazioni sulle funzioni Hive User-Defined, vedere la sezione [Hive operatori e funzioni definite dall'utente](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) di wiki Hive in apache.org.
