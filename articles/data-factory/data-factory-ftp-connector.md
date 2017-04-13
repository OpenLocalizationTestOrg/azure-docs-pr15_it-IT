<properties 
    pageTitle="Spostare i dati dal server FTP | Microsoft Azure" 
    description="Informazioni su come spostare i dati da un server FTP utilizzando Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Spostare i dati da un server FTP utilizzando Factory di dati di Azure
In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per spostare i dati da un server FTP a un archivio di dati supportati sink. In questo articolo si basa sull'articolo di [attività di spostamento dei dati](data-factory-data-movement-activities.md) che viene fornita una panoramica generale di spostamento con attività di copia e l'elenco degli archivi di dati supportati come origini/sink. 

Dati factory supporta attualmente solo spostare i dati da un server FTP agli altri archivi di dati, ma non per spostare dati da altri archivi di dati in un server FTP. Supporta entrambi locali e cloud server FTP. 

Se si spostano i dati da un server **locale** FTP a un archivio di dati cloud (esempio: archiviazione Blob Azure), installare e usare Gateway di gestione dati. Il Gateway di gestione dati è un agente client installata nel computer locale, che consente di servizi cloud per connettersi alla risorsa locale. Per informazioni dettagliate sul gateway, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md) . Vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per istruzioni dettagliate sulla configurazione del gateway e l'utilizzo. Il gateway è possibile connettersi a un server FTP anche se il server è installato una Azure IaaS macchine (). 

È possibile installare il gateway nello stesso computer locale o la macchina virtuale IaaS Azure come server FTP. Tuttavia, è consigliabile installare il gateway su un computer distinto oppure una macchina virtuale IaaS Azure separata per evitare conflitti di risorse e per migliorare le prestazioni. Quando si installa il gateway su un computer separato, il computer dovrebbe essere possibile accedere al server FTP. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati da un server FTP consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Di seguito sono riportati definizioni JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Esempio: Copiare i dati dal server FTP a blob Azure

In questo esempio viene illustrato come copiare dati da un server FTP a un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

- Servizio collegato del tipo [serverftp](#ftp-linked-service-properties).
- Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un input [set di dati](data-factory-create-datasets.md) di tipo [FileShare](#fileshare-dataset-type-properties).
- Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [FileSystemSource](#ftp-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio dati copiati da un server FTP blob Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

**FTP collegato servizio** In questo esempio viene usata l'autenticazione di base con nome utente e password in formato testo normale. È anche possibile usare uno dei modi seguenti: 

- Autenticazione anonima 
- Autenticazione di base con le credenziali crittografate
- FTP su SSL/TLS (FTP)

Vedere la sezione [FTP collegato servizio](#ftp-linked-service-properties) per diversi tipi di autenticazione è possibile utilizzare. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
    }

**Servizio di archiviazione collegato di Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Set di dati input FTP** Questo set di dati fa riferimento alla cartella FTP `mysharedfolder` e file `test.csv`. La pipeline di copia del file alla destinazione. 

Impostazione "esterni": "true" servizio Factory dati si informa che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **FileSystemSource** e tipo **sink** è impostato su **BlobSink**. 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Proprietà collegate sito FTP

Nella tabella seguente fornisce una descrizione per gli elementi JSON specifici di FTP servizi collegati.

| Proprietà | Descrizione | Obbligatorio | Impostazione predefinita |
| -------- | ----------- | -------- | ------- | 
| tipo | La proprietà di tipo deve essere impostata su serverftp | Sì | &nbsp;
| host | Nome o indirizzo IP del FTP Server | Sì | &nbsp;
| authenticationType | Specificare il tipo di autenticazione | Sì | Base e anonimo |
| nome utente | Utente che ha accesso al server FTP | No | &nbsp;
| password | Password per l'utente (nome utente) | No | &nbsp;
| encryptedCredential | Crittografia delle credenziali per accedere al server FTP | No | &nbsp;
| gatewayName | Nome del gateway Gateway di gestione dati per connettersi a un server FTP locale | No    | &nbsp;
| porta | Porta in cui il server FTP è in attesa | No | 21 |
| enableSsl | Specificare se si desidera utilizzare FTP su canale SSL/TLS. | No | vero | 
| enableServerCertificateValidation | Specificare se si desidera abilitare la convalida del certificato SSL server quando utilizzare FTP su canale SSL/TLS. | No | vero | 

### <a name="using-anonymous-authentication"></a>Utilizza l'autenticazione anonima

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Usando il nome utente e la password in formato testo normale per l'autenticazione di base
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Utilizzo di porta, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Utilizzo di encryptedCredential per l'autenticazione e gateway

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Per informazioni dettagliate sull'impostazione credenziali per un'origine dati FTP locale, vedere [impostare le credenziali e sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Proprietà del tipo di attività FTP copia

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, le proprietà di tipo variano a seconda dei tipi di origini e sink.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Esercitazione attività Copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per istruzioni dettagliate per la creazione di una pipeline con un'attività di copia. 
