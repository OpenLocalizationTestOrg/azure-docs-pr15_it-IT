<properties 
   pageTitle="Guida introduttiva a archivio Lake API REST | Microsoft Azure" 
   description="Utilizzare le API REST WebHDFS per eseguire operazioni dati Lake archivio" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Guida introduttiva a archivio Lake Azure utilizzando API REST

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

In questo articolo verrà descritto come utilizzare API REST WebHDFS e le API REST di dati Lake Store per eseguire la gestione degli account, nonché le operazioni di file System Azure dati Lake archivio. Archivio Lake dati di Azure espone relativa API REST per le operazioni di gestione di account. Tuttavia, poiché archivio Lake dati è compatibile con ecosistema HDFS e Hadoop, sono supportate con API REST WebHDFS per le operazioni di file System.

>[AZURE.NOTE] Per informazioni dettagliate sulle API REST supporto per dati Lake Store, vedere [Riferimento all'API di Azure dati Lake archivio resto](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Creare un'applicazione di Azure Active Directory**. Utilizzare l'applicazione di Azure Active Directory per autenticare l'applicazione di archivio di dati Lake con Azure Active Directory. Sono disponibili diversi metodi per l'autenticazione con Azure Active Directory, che sono **al servizio**o **l'autenticazione degli utenti finali** . Per ulteriori informazioni su come eseguire l'autenticazione e istruzioni, vedere [autentica con dati Lake Store con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [voltare](http://curl.haxx.se/). In questo articolo viene utilizzata curvatura per illustrare effettuare chiamate all'API REST rispetto a un account di archivio di dati Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione mediante Azure Active Directory?

È possibile utilizzare due metodi per eseguire l'autenticazione mediante Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticazione dell'utente finale (interattivo)

In questo scenario, l'applicazione richiede all'utente di eseguire l'accesso e tutte le operazioni vengono eseguite nel contesto dell'utente. Eseguire i passaggi seguenti per l'autenticazione interattivo.

1. Tramite l'applicazione, si viene reindirizzati all'utente l'URL seguente:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REINDIRIZZAMENTO URI > deve essere codificato per l'utilizzo in un URL. Pertanto, per https://localhost, utilizzare `https%3A%2F%2Flocalhost`)

    Per questa esercitazione, è possibile sostituire i valori di segnaposto nell'URL sopra e incollarla nella barra degli indirizzi del web browser. Si verrà reindirizzati per l'autenticazione mediante l'account di accesso Azure. Una volta riesce ad accedere, la risposta viene visualizzata nella barra degli indirizzi del browser. La risposta saranno nel formato seguente:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Acquisire il codice di autorizzazione dalla risposta. Per questa esercitazione, è possibile copiare il codice di autorizzazione dalla barra degli indirizzi del browser e passare nella richiesta POST all'endpoint token a come illustrato di seguito:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] In questo caso, il \<REINDIRIZZA URI > necessario non può essere codificato.

3. La risposta è un oggetto JSON che contiene un token di accesso (ad esempio, `"access_token": "<ACCESS_TOKEN>"`) e un token di aggiornamento (ad esempio, `"refresh_token": "<REFRESH_TOKEN>"`). L'applicazione utilizza il token di accesso quando si accede a Azure Lake archivio dati e il token di aggiornamento per ottenere un altro token di accesso quando scade un token di accesso.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando scade il token di accesso, è possibile richiedere un nuovo token di accesso utilizzando il token di aggiornamento, come illustrato di seguito:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Per ulteriori informazioni sull'autenticazione utente interattiva, vedere [il codice di autorizzazione concedere flusso](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Servizio di autenticazione (non interattiva)

In questo scenario, l'applicazione fornisce il proprio le credenziali per eseguire le operazioni. A tale scopo, è necessario inviare una richiesta POST simile a quello illustrato di seguito. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

L'output della richiesta includerà un token di autenticazione (identificato da `access-token` nell'output riportata di seguito) che passerà successivamente con le chiamate API REST. Salvare questo token di autenticazione in un file di testo. è necessario più avanti in questo articolo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

In questo articolo viene utilizzato il **non interattiva** approccio. Per ulteriori informazioni sulle modalità interattiva (chiamate a servizio), vedere [servizio alle chiamate del servizio utilizzando le credenziali](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Creare un account di archivio di dati Lake

Questa operazione è basata su API REST chiamata definito [qui](https://msdn.microsoft.com/library/mt694078.aspx).

Il comando seguente latino. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Il comando precedente, sostituire \< `REDACTED` \> con il token di autorizzazione recuperato in precedenza. Il contenuto di richiesta per questo comando è contenuto nel file **input.json** fornito per il `-d` parametro precedente. Il contenuto del file input.json simile al seguente:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Creare cartelle in un account di archivio di dati Lake

Questa operazione si basa sull'API REST WebHDFS chiamata definito [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Il comando seguente latino. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Il comando precedente, sostituire \< `REDACTED` \> con il token di autorizzazione recuperato in precedenza. Questo comando crea una directory denominata **mytempdir** nella cartella radice del proprio account archivio Lake dati.

Se l'operazione ha esito positivo, verrà visualizzata una risposta al seguente:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Elenco cartelle in un account di archivio di dati Lake

Questa operazione si basa sull'API REST WebHDFS chiamata definito [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Il comando seguente latino. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Il comando precedente, sostituire \< `REDACTED` \> con il token di autorizzazione recuperato in precedenza.

Se l'operazione ha esito positivo, verrà visualizzata una risposta al seguente:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Caricare dati in un account di archivio di dati Lake

Questa operazione si basa sull'API REST WebHDFS chiamata definito [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Caricare dati tramite l'API REST WebHDFS è un processo in due passaggi, come illustrato di seguito.

1. Inviare una richiesta HTTP inserire senza inviare i dati del file da caricare. Il comando seguente, sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    L'output per questo comando essere conterrà un URL di reindirizzamento temporaneo, come illustrato di seguito.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. A questo punto è necessario inviare un'altra richiesta HTTP inserire l'URL elencato per la proprietà **posizione** nella risposta. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    L'output sarà simile alla seguente:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Leggere i dati da un account di archivio di dati Lake

Questa operazione si basa sull'API REST WebHDFS chiamata definito [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Si leggono dati da un archivio di dati Lake account è un processo in due passaggi.

* È innanzitutto inviare una richiesta GET contro l'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Viene restituito un percorso per inviare la richiesta GET successiva.
* È quindi inviare la richiesta GET l'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Verranno visualizzati il contenuto del file.

Tuttavia, poiché non c'è alcuna differenza tra il primo e il secondo passaggio per i parametri di input, è possibile utilizzare il `-L` parametro per inviare la richiesta di prima. `-L`opzione essenzialmente unisce uno due richieste e consentono di rendere curvatura ripetere la richiesta nella nuova posizione. Infine, l'output di tutte le chiamate di richiesta è visualizzato, come illustrato di seguito. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Verrà visualizzato un output simile al seguente:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Rinominare un file in un account di archivio di dati Lake

Questa operazione si basa sull'API REST WebHDFS chiamata definito [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilizzare il comando seguente curvatura per rinominare un file. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Verrà visualizzato un output simile al seguente:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Eliminare un file da un account di archivio di dati Lake

Questa operazione si basa sull'API REST WebHDFS chiamata definito [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Usare il seguente comando curvatura per eliminare un file. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Verrà visualizzato un output simile al seguente:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Eliminare un account di archivio di dati Lake

Questa operazione è basata su API REST chiamata definito [qui](https://msdn.microsoft.com/library/mt694075.aspx).

Il comando seguente curvatura per eliminare un account di archivio di dati Lake. Sostituire ** \<yourstorename >** con il proprio nome archivio Lake dati.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Verrà visualizzato un output simile al seguente:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Vedere anche

- [Aprire le applicazioni di origine dati compatibile con archivio Lake dati di Azure](data-lake-store-compatible-oss-other-applications.md)
 
