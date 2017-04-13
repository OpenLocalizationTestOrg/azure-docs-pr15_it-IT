<properties
    pageTitle="Esercitazione su Factory dati: pipeline di dati prima | Microsoft Azure"
    description="In questa esercitazione Azure Data Factory viene illustrato come creare e pianificare una factory di dati che consente di elaborare dati utilizzando Hive script in un cluster di Hadoop."
    services="data-factory"
    keywords="dati di Azure factory esercitazione, hadoop cluster, hive hadoop"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>Esercitazione: Creare il prima pipeline per elaborare i dati utilizzando Hadoop cluster 
> [AZURE.SELECTOR]
- [Panoramica e i prerequisiti](data-factory-build-your-first-pipeline.md)
- [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modello di Manager delle risorse](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

In questa esercitazione si creano i prima factory Azure dati con una pipeline di dati che elabora i dati eseguendo Hive script in un cluster di Azure HDInsight (Hadoop). 

> [AZURE.NOTE] In questo articolo non fornisce una panoramica di Azure Data Factory. Per una panoramica del servizio, vedere [Introduzione alle Azure Data Factory](data-factory-introduction.md). Vedere [percorso formativo: Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/) per spostarsi tra i contenuti in modo consigliato per informazioni su Factory dati.

## <a name="whats-covered-in-this-tutorial"></a>Che cos'è coperta in questa esercitazione? 
**Azure Data Factory** consente di comporre di **spostamento** e attività di **elaborazione** dei dati come basati sui dati dei flussi di lavoro (denominati anche pipeline di dati). Si imparerà a creare la prima attività pipeline con un'elaborazione dei dati o la trasformazione dei dati, dati. Questa attività utilizza un cluster di HDInsight Hadoop per trasformare e analizzare i log web di esempio.  

In questa esercitazione, attenersi alla procedura seguente:

1.  Creare una **factory di dati**. Una factory dati può contenere uno o più dati pipeline tali dati sposta e processo. 
2.  Creare **servizi collegati**. Creare un servizio collegato da collegare un archivio dati o un servizio di calcolo per la factory di dati. Un archivio di dati, ad esempio lo spazio di archiviazione di Azure contiene dati di input/output delle attività nella pipeline. Un servizio, ad esempio HDInsight Hadoop cluster processi/trasformazioni dati.    
3.  Creare input e output **set di dati**. Un set di dati di input rappresenta l'input per un'attività nella pipeline e un set di dati di output rappresenta l'output per l'attività.
3.  Creare la **pipeline**. Una pipeline può avere una o più attività (esempi: copia attività, attività Hive HDInsight). In questo esempio viene usata l'attività HDInsight Hive uno script che esegue Hive in un cluster di HDInsight Hadoop. Lo script crea una tabella che fa riferimento ai dati di log web non elaborato archiviati in archiviazione blob Azure e quindi suddivide i dati non elaborati per anno e mese.

    Esistono due tipi di attività supportate da Azure dati Factory. Sono: [attività di spostamento dei dati](data-factory-data-movement-activities.md) e [attività di trasformazione dati](data-factory-data-transformation-activities.md). Esiste un solo dati movimento attività, l'attività di copia. In questa esercitazione, non si utilizza l'attività di copia. Per un'esercitazione sull'utilizzo dell'attività Copia, vedere [Esercitazione: copiare i dati da un Azure blob in SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Attività di HDInsight Hive che utilizzare in questa esercitazione è una delle operazioni di trasformazione dati supportate da produttore di dati.  
 
Ecco la **Vista diagramma** della factory di dati di esempio creato in questa esercitazione. 

![Vista diagramma in esercitazione Factory dati](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

In questa esercitazione cartella **inputdata** del contenitore di blob Azure **adfgetstarted** contiene un file denominato input.log. Il file di log contiene voci dei tre mesi: gennaio, febbraio e marzo del 2016. Ecco le righe di esempio per ogni mese nel file di input. 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Quando il file viene elaborato da pipeline con HDInsight Hive attività, l'attività esegue uno script Hive cluster HDInsight che le partizioni dati di input per anno e mese. Lo script crea tre cartelle di output che contengono un file con le voci di ogni mese.  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

Dalle linee di esempio illustrate in precedenza, il primo (con 2016-01 / 01) scritto al file 000000_0 nel mese = 1 cartella. Allo stesso modo, il secondo è scritto al file del mese = 2 cartella e il terzo si scrive il file nel mese = cartella 3.  


## <a name="pre-requisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre i prerequisiti seguenti:

1.  **Abbonamento azure** , se non si dispone di un abbonamento a Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Vedere l'articolo [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) su come è possibile ottenere un account di valutazione gratuito.

2.  **Lo spazio di archiviazione di azure** – si utilizza un account di archiviazione Azure per archiviare i dati in questa esercitazione. Se non si dispone di un account di archiviazione Azure, vedere l'articolo [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) . Dopo aver creato l'account di archiviazione, nota premuto il **nome dell'account** e un **tasto di scelta rapida**. Vedere [visualizzazione, copia e lo spazio di archiviazione rigenera i tasti di scelta](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys). 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>Caricare file allo spazio di archiviazione di Azure per l'esercitazione
Prima di iniziare l'esercitazione, è necessario preparare l'account di archiviazione Azure con file di esempio per l'esercitazione.

1. Caricare file di query Hive (HQL) alla cartella di **script** del contenitore di blob **adfgetstarted** .
2. Caricare file di input nella cartella **inputdata** del contenitore di blob **adfgetstarted** . 

#### <a name="create-hql-script-file"></a>Creare file di script HQL 

1. Avviare **blocco note** e incollare lo script HQL seguente. Questo script Hive crea due tabelle: **WebLogsRaw** e **WebLogsPartitioned**. Selezionare **Salva con nome**dal menu **File** . Passare alla cartella **C:\adfgetstarted** sul disco rigido. Selezionare * *tutti i file (*.*) **per il** Salvare con nome digitare** campo. Immettere **partitionweblogs.hql** per il ****nome del File. Verificare che il **codifica** campo nella parte inferiore della finestra di dialogo è impostato su **ANSI**. In caso contrario, impostarlo su **ANSI * *.  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

In fase di esecuzione attività Hive nella pipeline di dati Factory passa valori la **inputtable** e **partitionedtable** parametri come illustrato nel frammento di codice seguente:  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

**Storageaccountname** è il nome del proprio account di archiviazione Azure.
 
#### <a name="create-a-sample-input-file"></a>Creare un file di input di esempio
Utilizzando il blocco note, creare un file denominato **input.log** in **c:\adfgetstarted** con il contenuto seguente: 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>Caricare file di input e HQL file allo spazio di archiviazione Blob Azure

In questa sezione vengono fornite istruzioni sull'uso dello strumento **AzCopy** per copiare il file input.log e partitionweblogs.hql all'archivio Blob Azure. È possibile utilizzare qualsiasi strumento di propria scelta (ad esempio: [Microsoft Azure archiviazione Explorer](http://storageexplorer.com/), [CloudXPlorer da parte di ClumsyLeaf Software](http://clumsyleaf.com/products/cloudxplorer) per eseguire questa operazione.   
     
1. Scaricare la [versione più recente di **AzCopy**](http://aka.ms/downloadazcopy)o l' [ultima versione di anteprima](http://aka.ms/downloadazcopypr). Vedere l'articolo [come usare AzCopy](../storage/storage-use-azcopy.md) per istruzioni su come utilizzare l'utilità.
2. Passare alla cartella c:\adfgetstarted ed eseguire il comando seguente: 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    Questo comando consente di caricare il file **input.log** account di archiviazione (contenitore**adfgetstarted** e **inputdata** cartella). Sostituire **storageaccountname** con il nome di account di archiviazione e **storageaccesskey** con il tasto di scelta di spazio di archiviazione.

    > [AZURE.NOTE] Questo comando crea un contenitore denominato **adfgetstarted** in archiviazione Blob Azure e copia il file **input.log** nell'unità locale nella cartella **inputdata** nel contenitore. 
    
3. Dopo il file è stato caricato, viene visualizzato l'output simile a quella riportata di seguito da AzCopy.
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. Eseguire il seguente comando per caricare il file **partitionweblogs.hql** nella cartella **script** del contenitore **adfgetstarted** . Ecco il comando: 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

È stato completato i prerequisiti. È possibile creare una factory dati utilizzando uno dei modi indicati di seguito. Fare clic su una delle schede nella parte superiore o i collegamenti seguenti per eseguire l'esercitazione. 

- [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modello di Manager delle risorse](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)