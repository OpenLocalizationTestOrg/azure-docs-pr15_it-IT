## <a name="invoking-stored-procedure-for-sql-sink"></a>Richiamo stored procedure per elaborare SQL

Quando si copiano dati nel Database SQL Server o Azure SQL di SQL Server, un utente specificato stored procedure può essere configurata e richiamata con altri parametri. 

Una stored procedure possa risultare utili in meccanismi di copia incorporata non possono essere utilizzati. Questo è in genere utilizzato durante l'elaborazione aggiuntivi (unione colonne, la ricerca di valori aggiuntivi, inserimento in più tabelle...), deve essere eseguita prima dell'inserimento finale dei dati di origine della tabella di destinazione. 

È possibile richiamare una stored procedure di scelta. Nell'esempio seguente viene illustrato come utilizzare una stored procedure per eseguire un semplice inserimento in una tabella nel database. 

**Set di dati di output**

In questo esempio, tipo è impostato su: SqlServerTable. Impostarlo su AzureSqlTable da utilizzare con un database SQL Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Definire la sezione SqlSink nell'attività copia JSON come indicato di seguito. Per chiamare una stored procedure durante l'inserimento di dati, le proprietà SqlWriterStoredProcedureName e SqlWriterTableType sono necessarie.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

Nel database, definire stored procedure con lo stesso nome come SqlWriterStoredProcedureName. Gestisce i dati di input dal origine specificato e Inserisci nella tabella di output. Si noti che il nome del parametro della stored procedure deve essere uguale tableName definito nel file JSON tabella.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

Nel database, definire il tipo di tabella con lo stesso nome come SqlWriterTableType. Si noti che lo schema di tipo tabella deve essere uguale a quello schema restituito dai dati di input.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

La funzionalità stored procedure consente di sfruttare [Table-Valued Parameters](https://msdn.microsoft.com/library/bb675163.aspx).