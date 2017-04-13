<properties
    pageTitle="Creare un'applicazione di HBase tramite Maven e Java e quindi distribuire basati su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come utilizzare Maven Apache per creare un'applicazione basato su Java HBase Apache, quindi distribuirlo a HDInsight basati su Linux nel cloud Azure."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Utilizzare Maven delle applicazioni Java che utilizzano HBase basati su Linux HDInsight (Hadoop)

Informazioni su come creare e generare un'applicazione di [Apache HBase](http://hbase.apache.org/) Java utilizzando Apache Maven. Usare quindi l'applicazione con un cluster basati su Linux HDInsight.

[Maven](http://maven.apache.org/) è uno strumento di comprensione che consente di creare software, documentazione e report per i progetti di linguaggio e gestione dei progetti software. In questo articolo si apprenderà come usarlo per creare un'applicazione di linguaggio di base che consente di creare, query ed elimina una tabella di HBase in un cluster basati su Linux HDInsight.

> [AZURE.NOTE] La procedura descritta in questo documento presuppone che si sta utilizzando un cluster basati su Linux HDInsight. Per informazioni sull'utilizzo di un cluster basato su Windows HDInsight, vedere [Utilizzare Maven che utilizzano HBase con HDInsight basato su Windows delle applicazioni Java](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>Requisiti

* [Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive

* [Maven](http://maven.apache.org/)

* [Un cluster basati su Linux Azure HDInsight con HBase](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] La procedura descritta in questo documento è stata testata con le versioni di cluster HDInsight 3.2, 3.3 e 3.4. I valori predefiniti forniti esempi sono per un cluster 3.4 HDInsight.

* **Familiarità con SSH e SCP**. Per ulteriori informazioni sull'utilizzo di SSH e SCP con HDInsight, vedere le operazioni seguenti:

    * **Client Linux, Unix o OS X**: vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Client Windows**: vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>Creare il progetto

1. Dalla riga di comando nel proprio ambiente di sviluppo, passare al percorso in cui si vuole creare il progetto, ad esempio `cd code/hdinsight`.

2. Utilizzare il comando __mvn__ viene installato con Maven, per generare le pagine di supporto temporaneo per il progetto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Verrà creata una nuova directory nella directory corrente con il nome specificato dal parametro __un artifactID__ (in questo esempio,**hbaseapp** ). Questa directory conterrà gli elementi seguenti:

    * __POM.XML__: il modello a oggetti progetti ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene informazioni e configurazione dettagli usati per generare il progetto.

    * __src__: la directory che contiene la directory __principali/java/com/microsoft/esempi__ in cui verrà creata l'applicazione.

3. Eliminare il file __src/test/java/com/microsoft/examples/apptest.java__ perché non verranno utilizzata in questo esempio.

##<a name="update-the-project-object-model"></a>Aggiornare il modello a oggetti progetto

1. Modificare il file __pom.xml__ e aggiungere il codice seguente all'interno di `<dependencies>` sezione:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    In questo modo Maven che il progetto richiede __hbase client__ versione __1.1.2__. Durante la compilazione, questo verrà scaricato dal repository Maven predefinito. È possibile utilizzare la [Ricerca Repository centrale Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) per altre informazioni su questa dipendenza.

    > [AZURE.IMPORTANT] Il numero di versione deve corrispondere alla versione di HBase viene fornito con il cluster HDInsight. Utilizzare la tabella seguente per calcolare il numero di versione corretta.

  	| Versione cluster HDInsight | Versione HBase da utilizzare |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 e 3.4 | 1.1.2 |

    Per ulteriori informazioni sulle versioni HDInsight e componenti, vedere [informazioni sui diversi componenti di Hadoop disponibili con HDInsight](hdinsight-component-versioning.md).

2. Se si utilizza un 3.3 HDInsight o cluster 3.4, è necessario aggiungere anche le operazioni seguenti per la `<dependencies>` sezione:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Verrà caricato componenti phoenix core, che sono necessari con Hbase versione 1.1.x.

2. Aggiungere il codice seguente al file __pom.xml__ . Deve essere all'interno di `<project>...</project>` tag nel file, ad esempio tra `</dependencies>` e `</project>`.

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

    Consente di configurare una risorsa (__conferenza telefonica con/hbase-site.xml__), che contiene le informazioni di configurazione per HBase.

    > [AZURE.NOTE] È anche possibile impostare i valori di configurazione tramite codice. Vedere i commenti nell'esempio che segue per come eseguire questa operazione __CreateTable__ .

    In questo modo anche il [Plug-in compilatore Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) [Plug](http://maven.apache.org/plugins/maven-shade-plugin/)-in e Maven ombreggiatura. Il compilatore plug-in viene utilizzato per la compilazione della topologia. Per evitare la duplicazione di licenza nel pacchetto VASO compilati dalla Maven viene utilizzata la sfumatura plug-in. Il motivo che viene utilizzato è che i file della licenza duplicati causano un errore in fase di esecuzione su cluster HDInsight. Uso plugin di ombreggiatura maven con la `ApacheLicenseResourceTransformer` questo errore impedisce l'implementazione.

    Il plugin di ombreggiatura maven produce anche un vaso uber (o fat vaso,) che contiene tutte le dipendenze necessarie all'applicazione.

3. Salvare il file __pom.xml__ .

4. Creare una nuova directory denominata __conferenza telefonica con__ nella directory __hbaseapp__ . Questa verrà utilizzata per contenere le informazioni di configurazione per la connessione a HBase.

5. Utilizzare il comando seguente per copiare la directory di __conferenza telefonica con__ la configurazione di HBase dal server HDInsight. Sostituire **il nome utente** il nome di accesso dell'utente SSH. Sostituire **nome cluster** con il proprio nome cluster HDInsight:

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] Se si utilizza una password per l'account SSH, verrà richiesto di immettere la password. Se è stata utilizzata una chiave SSH con l'account, potrebbe essere necessario utilizzare la `-i` parametro per specificare il percorso del file di chiave. Nell'esempio seguente verrà caricata la chiave privata da `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>Creare l'applicazione

1. Passare alla directory __hbaseapp/src/principali/java/com/microsoft/esempi__ e rinominare il file app.java in __CreateTable.java__.

2. Aprire il file __CreateTable.java__ e sostituire il contenuto con le operazioni seguenti:

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
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. Creare un nuovo file denominato __SearchByEmail.java__nella directory __hbaseapp/src/principali/java/com/microsoft/esempi__ . Utilizzare la seguente come il contenuto del file:

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

6. Creare un nuovo file denominato __DeleteTable.java__nella directory __hbaseapp/src/principali/hava/com/microsoft/esempi__ . Utilizzare la seguente come il contenuto del file:

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

2. Dalla directory __hbaseapp__ , utilizzare il comando seguente per creare un file di VASO che contiene l'applicazione:

        mvn clean package

    Questo pulisce eventuali elementi di compilazione precedente, scarica le dipendenze che non sono stato già installate, quindi compila e del pacchetto dell'applicazione.

3. Dopo avere completato il comando, la directory __hbaseapp/destinazione__ conterrà un file denominato __SNAPSHOT.jar di 1.0 hbaseapp__.

    > [AZURE.NOTE] Il file __SNAPSHOT.jar di 1.0 hbaseapp__ è un uber vaso (a volte chiamato un vaso fat) che contiene tutte le dipendenze necessari per eseguire l'applicazione.

##<a name="upload-the-jar-file-and-run-jobs"></a>Caricare il file VASO ed eseguire i processi

1. Utilizzare le operazioni seguenti per caricare vaso cluster HDInsight. Sostituire **il nome utente** il nome di accesso dell'utente SSH. Sostituire **nome cluster** con il proprio nome cluster HDInsight:

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Questa operazione verrà caricare il file alla home directory per l'account utente SSH.

    > [AZURE.NOTE] Se si utilizza una password per l'account SSH, verrà richiesto di immettere la password. Se è stata utilizzata una chiave SSH con l'account, potrebbe essere necessario utilizzare la `-i` parametro per specificare il percorso del file di chiave. Nell'esempio seguente verrà caricata la chiave privata da `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Utilizzare SSH per connettersi al cluster HDInsight. Sostituire **il nome utente** il nome di accesso dell'utente SSH. Sostituire **nome cluster** con il proprio nome cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se si utilizza una password per l'account SSH, verrà richiesto di immettere la password. Se è stata utilizzata una chiave SSH con l'account, potrebbe essere necessario utilizzare la `-i` parametro per specificare il percorso del file di chiave. Nell'esempio seguente verrà caricata la chiave privata da `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Una volta connessa, utilizzare le operazioni seguenti per creare una nuova tabella HBase mediante l'applicazione di linguaggio:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    Verrà creare una nuova tabella HBase denominata __persone__e inserire i dati.

4. Cercare gli indirizzi di posta elettronica archiviati nella tabella successivamente, utilizzare le operazioni seguenti:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    Verrà visualizzato i risultati seguenti:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>Eliminare la tabella

Al termine dell'esempio, utilizzare il seguente comando dalla sessione di PowerShell Azure per eliminare la tabella __persone__ utilizzata in questo esempio:

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

