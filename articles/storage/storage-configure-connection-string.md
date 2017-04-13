<properties 
    pageTitle="Configurare una stringa di connessione allo spazio di archiviazione Azure | Microsoft Azure"
    description="Configurare una stringa di connessione a un account di archiviazione Azure. Una stringa di connessione include le informazioni necessarie per eseguire l'autenticazione di accesso a un account di archiviazione dall'applicazione in fase di esecuzione."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="configure-azure-storage-connection-strings"></a>Configurare le stringhe di connessione di archiviazione Azure

## <a name="overview"></a>Panoramica

Una stringa di connessione include le informazioni di autenticazione necessarie per accedere ai dati in un account di archiviazione Azure dall'applicazione in fase di esecuzione. È possibile configurare una stringa di connessione:

- Connettersi all'emulatore di archiviazione Azure.
- Accedere a un account di archiviazione in Azure.
- Accedere alle risorse specificate in Azure tramite una firma di accesso condiviso (SA).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Archiviare la stringa di connessione

L'applicazione sarà necessario accedere la stringa di connessione in fase di esecuzione per l'autenticazione richieste allo spazio di archiviazione Azure. Si dispone esistono diverse opzioni per l'archiviazione la stringa di connessione:

- Per un'applicazione in esecuzione sul desktop o in un dispositivo, è possibile archiviare la stringa di connessione in un `app.config `file o una `web.config` file. Aggiungere la stringa di connessione nella sezione **AppSettings** .
- Per un'applicazione in esecuzione in un servizio cloud Azure, è possibile memorizzare la stringa di connessione nel [file di schema (. cscfg) di configurazione del servizio Azure](https://msdn.microsoft.com/library/ee758710.aspx). Aggiungere la stringa di connessione nella sezione **ConfigurationSettings** del file di configurazione del servizio.
- È anche possibile utilizzare la stringa di connessione direttamente nel codice. Per la maggior parte dei casi, tuttavia, si consiglia archiviare la stringa di configurazione in un file di configurazione.

Archiviare la stringa di connessione all'interno di un file di configurazione rende più facile aggiornare la stringa di connessione per spostarsi tra emulatore lo spazio di archiviazione e di un account di archiviazione Azure nel cloud. È sufficiente modificare la stringa di connessione in modo che puntino all'ambiente di destinazione.

È possibile utilizzare la classe di [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) per accedere la stringa di connessione in fase di esecuzione indipendentemente da dove l'applicazione è in esecuzione.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Creare una stringa di connessione all'emulatore di spazio di archiviazione

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Per ulteriori informazioni sull'emulatore lo spazio di archiviazione, vedere [usare l'emulatore di spazio di archiviazione Azure di sviluppo e la verifica](storage-use-emulator.md) .

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Creare una stringa di connessione a un account di archiviazione Azure

Per creare una stringa di connessione all'account di archiviazione Azure, utilizzare il formato di stringa di connessione riportata di seguito. Indicare se si desidera connettersi all'account di archiviazione tramite HTTPS (scelta consigliata) o HTTP, sostituire `myAccountName` con il nome di account di archiviazione e sostituisci `myAccountKey` con il tasto di scelta account:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ad esempio, la stringa di connessione sarà simile alla stringa di connessione di esempio seguente:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Spazio di archiviazione Azure supporta HTTP e HTTPS in una stringa di connessione. Tuttavia, utilizzando HTTPS consiglia.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Creare una stringa di connessione utilizzando una firma di accesso condiviso

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Creazione di una stringa di connessione a un endpoint esplicito dello spazio di archiviazione

È possibile specificare gli endpoint del servizio in modo esplicito nella stringa di connessione invece di usare endpoint predefiniti. Per creare una stringa di connessione che specifica un endpoint esplicito, specificare l'endpoint del servizio di completamento per ogni servizio, incluse le specifiche del protocollo HTTPS (scelta consigliata) o HTTP, nel formato seguente:

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Uno scenario in cui si desidera specificare un endpoint esplicito è se è stata mappata l'endpoint di spazio di archiviazione Blob a un dominio personalizzato. In questo caso, è possibile specificare l'endpoint personalizzato per l'archiviazione Blob nella stringa di connessione e se si desidera specificare endpoint predefiniti per il servizio di altri se li usa l'applicazione.

Ecco alcuni esempi di stringhe di connessione valida che specifica un endpoint esplicito per il servizio Blob:

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

Viene utilizzato il valore di endpoint elencato nella stringa di connessione per creare la richiesta URI al servizio Blob e stabilisce il modulo di qualsiasi URI restituito al codice.

Si noti che se si sceglie di omettere un endpoint del servizio dalla stringa di connessione, quindi non sarà possibile usare la stringa di connessione per accedere ai dati in tale servizio dal codice.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Creazione di una stringa di connessione con un suffisso endpoint

Per creare una stringa di connessione per il servizio di archiviazione in aree o istanze con suffissi endpoint diverso, ad esempio per Azure Cina o Governance Azure, utilizzare il seguente formato stringa di connessione. Indicare se si desidera connettersi all'account di archiviazione tramite HTTP o HTTPS, sostituire `myAccountName` con il nome del proprio account di archiviazione, sostituire `myAccountKey` con il tasto di scelta account e sostituisci `mySuffix` con suffisso URI:


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


Ad esempio, la stringa di connessione dovrebbe essere simile alla stringa di connessione seguenti:

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>Analisi di una stringa di connessione

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Passaggi successivi

- [Utilizzare l'emulatore Azure dello spazio di archiviazione per lo sviluppo e test](storage-use-emulator.md)
- [Elenchi di cartelle di archiviazione Azure](storage-explorers.md)
- [Uso delle firme accesso condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md)
