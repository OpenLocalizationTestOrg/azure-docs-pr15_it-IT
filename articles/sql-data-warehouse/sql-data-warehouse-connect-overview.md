<properties
   pageTitle="Connettersi a SQL Azure Data Warehouse | Microsoft Azure"
   description="Come trovare il server stringa di connessione e nome per l'archivio di dati di SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>Connettersi a SQL Azure Data Warehouse

In questo articolo consente di connettersi a SQL Data Warehouse per la prima volta.

## <a name="find-your-server-name"></a>Trovare il nome del server

Il primo passo per la connessione a SQL Data Warehouse è sapere come trovare il nome del server.  Ad esempio, il nome del server nell'esempio seguente viene sample.database.windows.net. Per trovare il nome completo del server:

1. Accedere al [portale di Azure][].
2. Fare clic su **database SQL** 
3. Fare clic sul database che si desidera connettersi.
4. Individuare il nome completo del server.

    ![Nome completo del server][1]

## <a name="supported-drivers-and-connection-strings"></a>Driver supportati e stringhe di connessione

Azure SQL Data Warehouse supporta [ADO.NET][], [ODBC][], [PHP][]e [JDBC][]. Fare clic su uno dei driver precedente per trovare l'ultima versione e documentazione. Per generare automaticamente la stringa di connessione per il driver che si sta utilizzando dal portale di Azure, è possibile fare clic su **Mostra le stringhe di connessione di database** dell'esempio precedente.  Seguenti sono riportati alcuni esempi di aspetto una stringa di connessione per ogni driver.

> [AZURE.NOTE] Provare a impostare il timeout della connessione a 300 secondi per consentire la connessione a brevi periodi di indisponibilità.

### <a name="adonet-connection-string-example"></a>Esempio di stringa di connessione ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Esempio di stringa di connessione ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Esempio di stringa di connessione PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Esempio di stringa di connessione JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Impostazioni di connessione

SQL Data Warehouse standardizzano alcune impostazioni durante la connessione e la creazione dell'oggetto. Queste impostazioni non possono essere sovrascritti e includono:

| Impostazione di database       | Valore                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | VIA                           |
| [QUOTED_IDENTIFIERS][] | VIA                           |
| [FORMATO DATA][]         | mdy                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>Passaggi successivi

Per connettersi e query con Visual Studio, vedere [Query con Visual Studio][]. Per ulteriori informazioni sulle opzioni di autenticazione, vedere [autenticazione nell'archivio di dati di SQL Azure][].

<!--Articles-->
[Query con Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticazione di SQL Azure Data warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[FORMATO DATA]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portale di Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


