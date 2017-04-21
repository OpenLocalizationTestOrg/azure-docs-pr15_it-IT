
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>File di configurazione di esempio per la protezione stringa di connessione


È unsound inserire la stringa di connessione come valori letterali nel codice c#. Si consiglia di inserire la stringa di connessione in un file di configurazione. È possibile modificare la stringa qualsiasi momento senza la necessità di ricompilare.

Supponiamo che il programma compilato c# denominato **ConsoleApplication1.exe**e che questo .exe si trova un **bin\debug\* * directory.

In questo esempio, la maggior parte delle parti la stringa di connessione sono archiviate in un file di configurazione denominato esattamente **ConsoleApplication1.exe.config**. È inoltre necessario che il file di configurazione **bin\debug\**.

In XML del file di configurazione seguente viene visualizzato una stringa di connessione denominata **ConnectionString4NoUserIDNoPassword**. Il codice c# consente di cercare questa stringa.

È necessario modificare i nomi reali in per i segnaposto:

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Per questa figura si è scelto di omettere due parametri:

- ID utente = {your_userName_here};
- Password = {your_password_here};


È possibile includere, ma in alcuni casi è preferibile il programma ottenere i valori dall'input della tastiera dall'utente. Dipende.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
