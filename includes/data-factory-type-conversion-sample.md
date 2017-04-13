### <a name="type-conversion-sample"></a>Esempio di conversione di tipo
Nell'esempio seguente è per la copia di dati da un Blob in SQL Azure con le conversioni.

Si supponga che il set di dati Blob è in formato CSV e contiene 3 colonne. Uno di essi è una colonna datetime con un formato personalizzato datetime utilizzando nomi francese abbreviati per giorno della settimana.

Definire il set di dati di origine Blob come indicato di seguito insieme definizione dei tipi di colonne.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Assegnato il codice SQL tipo alla tabella delle corrispondenze di tipo .NET sopra si preferisce definire la tabella di SQL Azure con il seguente schema.

| Nome di colonna | Tipo di SQL |
| ----------- | -------- |
| ID utente | bigint |
| nome | testo |
| LastLoginDate | DateTime |

Punto verrà definire il set di dati di SQL Azure come indicato di seguito. Nota: Non è necessario specificare la sezione "struttura" con informazioni sul tipo, dal momento che le informazioni sul tipo è già specificati nell'archivio dati sottostante.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

In questo caso factory dati produrrà automaticamente il tipo di formattare le conversioni tra cui il campo di data e ora con datetime personalizzati utilizzando le impostazioni cultura fr-fr quando si spostano i dati da Blob in SQL Azure.


