<properties
pageTitle="Creare un'applicazione di HBase utilizzando Maven e distribuire basato su Windows HDInsight | Microsoft Azure"
description="Informazioni su come utilizzare Maven Apache per creare un'applicazione basato su Java HBase Apache, quindi distribuirlo a un cluster basato su Windows Azure HDInsight."
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
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Usare Maven delle applicazioni Java che utilizzano HBase basato su Windows HDInsight (Hadoop)

Informazioni su come creare e generare un'applicazione di [Apache HBase](http://hbase.apache.org/) Java utilizzando Apache Maven. Usare quindi l'applicazione con Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) è uno strumento di comprensione che consente di creare software, documentazione e report per i progetti di linguaggio e gestione dei progetti software. In questo articolo si imparerà da utilizzare per creare un'applicazione di linguaggio di base che crea, query ed elimina una tabella di HBase in un cluster di Azure HDInsight.

> [AZURE.NOTE] La procedura descritta in questo documento presuppone che si sta utilizzando un cluster basato su Windows HDInsight. Per informazioni sull'utilizzo di un cluster basati su Linux HDInsight, vedere [Utilizzare Maven che utilizzano HBase con HDInsight basati su Linux delle applicazioni Java](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>Requisiti

* [Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive

* [Maven](http://maven.apache.org/)


* [Un cluster basato su Windows HDInsight con HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] La procedura descritta in questo documento è stata testata con le versioni di cluster HDInsight 3.2 e 3.3. I valori predefiniti forniti esempi sono per un cluster 3.3 HDInsight.

##<a name="create-the-project"></a>Creare il progetto

1. Dalla riga di comando nel proprio ambiente di sviluppo, passare al percorso in cui si vuole creare il progetto, ad esempio `cd code\hdinsight`.

2. Utilizzare il comando __mvn__ viene installato con Maven, per generare le pagine di supporto temporaneo per il progetto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Questo comando consente di creare una directory nella posizione corrente con il nome specificato dal parametro __un artifactID__ (in questo esempio,**hbaseapp** ). Questa directory contiene gli elementi seguenti:

    * __POM.XML__: il modello a oggetti progetto ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene informazioni dettagliate su informazioni e configurazione usati per generare il progetto.

    * __src__: la directory che contiene la directory __main\java\com\microsoft\examples__ , verrà creata l'applicazione.

3. Eliminare il file __src\test\java\com\microsoft\examples\apptest.java__ perché non è stato utilizzato in questo esempio.

##<a name="update-the-project-object-model"></a>Aggiornare il modello a oggetti progetto

1. Modificare il file __pom.xml__ e aggiungere il codice seguente all'interno di `<dependencies>` sezione:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    In questa sezione viene Maven che il progetto richiede __hbase client__ versione __1.1.2__. Durante la compilazione, questa dipendenza viene scaricata dal repository dei Maven predefinito. È possibile utilizzare la [Ricerca Repository centrale Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) per altre informazioni su questa dipendenza.

    > [AZURE.IMPORTANT] Il numero di versione deve corrispondere alla versione di HBase viene fornito con il cluster HDInsight. Utilizzare la tabella seguente per calcolare il numero di versione corretta.

  	| Versione cluster HDInsight | Versione HBase da utilizzare |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    Per ulteriori informazioni sulle versioni HDInsight e componenti, vedere [informazioni sui diversi componenti di Hadoop disponibili con HDInsight](hdinsight-component-versioning.md).

2. Se si utilizza un cluster HDInsight 3.3, è necessario aggiungere anche le operazioni seguenti per la `<dependencies>` sezione:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Questa dipendenza verrà caricato i componenti di base phoenix, utilizzato dalla versione Hbase 1.1.x.

2. Aggiungere il codice seguente al file __pom.xml__ . In questa sezione deve trovarsi all'interno di `<project>...</project>` tag nel file, ad esempio tra `</dependencies>` e `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
              </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
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

    Il `<resources>` sezione Configura una risorsa (__conf\hbase site.xml__) che contiene le informazioni di configurazione per HBase.

    > [AZURE.NOTE] È anche possibile impostare i valori di configurazione tramite codice. Vedere i commenti nell'esempio che segue per come eseguire questa operazione __CreateTable__ .

    Questo `<plugins>` sezione Configura il [Plug-in compilatore Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) [Plug](http://maven.apache.org/plugins/maven-shade-plugin/)-in e Maven ombreggiatura. Il compilatore plug-in viene utilizzato per la compilazione della topologia. Per evitare la duplicazione di licenza nel pacchetto VASO compilati dalla Maven viene utilizzata la sfumatura plug-in. Il motivo che viene utilizzato è che i file della licenza duplicati causano un errore in fase di esecuzione su cluster HDInsight. Uso plugin di ombreggiatura maven con la `ApacheLicenseResourceTransformer` questo errore impedisce l'implementazione.

    Il plugin di ombreggiatura maven produce anche un vaso uber (o vaso fat) che contiene tutte le dipendenze necessarie all'applicazione.

3. Salvare il file __pom.xml__ .

4. Creare una nuova directory denominata __conferenza telefonica con__ nella directory __hbaseapp__ . Creare un file denominato __hbase site.xml__nella directory __conferenza telefonica con__ . Utilizzare la seguente come il contenuto del file:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Il file verrà utilizzato per caricare la configurazione di HBase per un cluster di HDInsight.

    > [AZURE.NOTE] Si tratta di un file minime hbase-site.xml e contiene le impostazioni minime bare per il cluster HDInsight.

3. Salvare il file __hbase site.xml__ .

##<a name="create-the-application"></a>Creare l'applicazione

1. Passare alla directory __hbaseapp\src\main\java\com\microsoft\examples__ e rinominare il file app.java in __CreateTable.java__.

2. Aprire il file __CreateTable.java__ e sostituire il contenuto con il codice seguente:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Questa è la classe __CreateTable__ , che consente di creare una tabella denominata __persone__ e inserirvi alcuni utenti predefiniti.

3. Salvare il file __CreateTable.java__ .

4. Creare un nuovo file denominato __SearchByEmail.java__nella directory __hbaseapp\src\main\java\com\microsoft\examples__ . Utilizzare il codice seguente come il contenuto del file:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    La classe __SearchByEmail__ può essere utilizzata per eseguire una query per le righe dall'indirizzo di posta elettronica. Perché utilizza un filtro di espressioni regolari, è possibile fornire una stringa o un'espressione regolare quando si utilizza la classe.

5. Salvare il file __SearchByEmail.java__ .

6. Creare un nuovo file denominato __DeleteTable.java__nella directory __hbaseapp\src\main\hava\com\microsoft\examples__ . Utilizzare il codice seguente come il contenuto del file:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Questa classe è per la pulizia in questo esempio la disattivazione e rilasciando la tabella creata per la classe __CreateTable__ .

7. Salvare il file __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Compilare e creare un pacchetto dell'applicazione

1. Aprire un prompt dei comandi e passare alla directory __hbaseapp__ .

2. Utilizzare il comando seguente per creare un file di VASO che contiene l'applicazione:

        mvn clean package

    Questo pulisce eventuali elementi di compilazione precedente, scarica le dipendenze che non sono stato già installate, quindi compila e del pacchetto dell'applicazione.

3. Dopo avere completato il comando, la directory __hbaseapp\target__ contiene un file denominato __SNAPSHOT.jar di 1.0 hbaseapp__.

    > [AZURE.NOTE] Il file __SNAPSHOT.jar di 1.0 hbaseapp__ è un uber vaso (a volte chiamato un vaso fat) che contiene tutte le dipendenze necessari per eseguire l'applicazione.

##<a name="upload-the-jar-file-and-start-a-job"></a>Caricare il file VASO e avviare un processo

Esistono diversi modi per caricare un file per il cluster HDInsight, come descritto in [Carica dati per i processi di Hadoop in HDInsight](hdinsight-upload-data.md). La procedura seguente usa PowerShell Azure.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Dopo l'installazione e configurazione di PowerShell di Azure, creare un nuovo file denominato __hbase runner.psm1__. Utilizzare la seguente come il contenuto del file:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Il file contiene due moduli:

    * __Aggiungi HDInsightFile__ - utilizzato per caricare file in HDInsight

    * __Inizio HBaseExample__ - utilizzato per eseguire le classi create in precedenza

2. Salvare il file __hbase runner.psm1__ .

3. Aprire una nuova finestra di PowerShell di Azure, passare alla directory __hbaseapp__ e quindi eseguire il comando seguente.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Modificare il percorso al percorso del file __hbase runner.psm1__ creato in precedenza. In questo modo il modulo viene registrata per questa sessione di PowerShell Azure.

2. Usare il comando seguente per caricare __SNAPSHOT.jar di 1.0 hbaseapp__ al cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Sostituire __hdinsightclustername__ con il nome del cluster HDInsight. Il comando Carica __SNAPSHOT.jar di 1.0 hbaseapp__ nella posizione di __esempio/JAR__ nello spazio di memorizzazione principale per il cluster HDInsight.

3. Dopo i file vengono caricati, utilizzare il codice riportato di seguito per creare una tabella utilizzando __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Sostituire __hdinsightclustername__ con il nome del cluster HDInsight.

    Questo comando crea una nuova tabella denominata __persone__ all'interno del cluster HDInsight. Questo comando non visualizzare alcun output nella finestra della console.

2. Per cercare le voci della tabella, usare il comando seguente:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Sostituire __hdinsightclustername__ con il nome del cluster HDInsight.

    Questo comando utilizza la classe **SearchByEmail** per cercare tutte le righe in cui la famiglia di colonna __contactinformation__ e la colonna di __posta elettronica__ contiene la stringa __contoso.com__. Verrà visualizzato i risultati seguenti:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Uso __fabrikam.com__ per il `-emailRegex` valore restituisce gli utenti che hanno __fabrikam.com__ nel campo di posta elettronica. Poiché la ricerca viene implementata utilizzando un filtro basate su espressioni regolare, è inoltre possibile immettere espressioni regolari, ad esempio __^ r__, le voci restituisce nel punto in cui il messaggio inizia con la lettera "r".

##<a name="delete-the-table"></a>Eliminare la tabella

Al termine dell'esempio, utilizzare il seguente comando dalla sessione di PowerShell Azure per eliminare la tabella __persone__ utilizzata in questo esempio:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Sostituire __hdinsightclustername__ con il nome del cluster HDInsight.

##<a name="troubleshooting"></a>Risoluzione dei problemi

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nessun risultato o risultati imprevisti quando si utilizza Start HBaseExample

Utilizzare la `-showErr` parametro per visualizzare l'errore standard (STDERR) che viene visualizzato durante l'esecuzione del processo.
