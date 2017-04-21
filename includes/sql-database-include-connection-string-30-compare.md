
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Confronto tra la stringa di connessione


Nella tabella seguente contiene un confronto di stringhe di connessione che il programma c# deve connettersi a SQL Server locale e il Database di SQL Azure nel cloud. Le differenze sono in grassetto.


| Stringa di connessione per<br/>Database SQL Azure | Stringa di connessione per<br/>Microsoft SQL Server |
| :-- | :-- |
| Server =**tcp:**{your_serverName_here}**. database.windows.net,1433**;<br/>ID utente = {your_loginName_here}**@{your_serverName_here}**;<br/>Password = {your_password_here};<br/>**Database = {your_databaseName_here};**<br/>**Timeout della connessione = 30**;<br/>**Crittografare = True**;<br/>**TrustServerCertificate = False**; | Server = {your_serverName_here};<br/>ID utente = {your_loginName_here};<br/>Password = {your_password_here}; |


Il **Database =** è facoltativa per SQL Server, ma è necessario per Database SQL.


[Proprietà SqlConnectionStringBuilder ADO .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - vengono illustrati tutti i parametri in modo dettagliato.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
