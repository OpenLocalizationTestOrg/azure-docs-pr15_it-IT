<properties 
    pageTitle="Utilizzare l'emulatore Azure dello spazio di archiviazione per sviluppo e la verifica | Microsoft Azure" 
    description="Emulatore archiviazione Azure offre un ambiente di sviluppo locale gratuito per lo sviluppo e testing archiviazione Azure. Informazioni sulle emulatore lo spazio di archiviazione, tra cui la modalità di autenticazione richieste, come connettersi all'emulatore dall'applicazione in uso e come usare lo strumento della riga di comando." 
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
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utilizzare l'emulatore Azure dello spazio di archiviazione per lo sviluppo e test

## <a name="overview"></a>Panoramica

Emulatore di spazio di archiviazione di Microsoft Azure offre un ambiente locale che emula i servizi Blob Azure, coda e una tabella in fase di sviluppo. Utilizzo dell'emulatore lo spazio di archiviazione, è possibile verificare l'applicazione Servizi di archiviazione in locale, senza creare una sottoscrizione Azure o incorrere sui costi previsti. Quando si è soddisfatti funzionamento dell'applicazione nell'emulatore, è possibile passare all'uso di un account Azure dello spazio di archiviazione nel cloud.

> [AZURE.NOTE] Emulator lo spazio di archiviazione è disponibile come parte di [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). È anche possibile installare emulatore lo spazio di archiviazione tramite il [programma di installazione autonomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Per configurare l'emulatore lo spazio di archiviazione, è necessario disporre dei privilegi di amministratore nel computer.
> 
> Solo in Windows viene eseguito attualmente emulatore lo spazio di archiviazione.
>  
> Si noti che dati creati in una versione dell'emulatore lo spazio di archiviazione non necessariamente accessibile quando si utilizza una versione diversa. Se è necessario mantenere i dati a lungo termine, si consiglia di memorizzare i dati in un account di archiviazione Azure anziché nell'emulatore di spazio di archiviazione.

## <a name="how-the-storage-emulator-works"></a>Come funziona il emulatore lo spazio di archiviazione
 
Emulatore lo spazio di archiviazione utilizza un'istanza di Microsoft SQL Server locale e nel file system locale per emulare i servizi di archiviazione Azure. Per impostazione predefinita, l'emulatore lo spazio di archiviazione utilizza un database di Microsoft SQL Server 2012 Express LocalDB.  È possibile scegliere di configurare l'emulatore lo spazio di archiviazione per accedere a un'istanza di SQL Server locale anziché l'istanza di LocalDB. Per ulteriori informazioni, vedere [Start e inizializzare l'emulatore lo spazio di archiviazione](#start-and-initialize-the-storage-emulator) di sotto.

È possibile installare SQL Server Management Studio Express per gestire l'installazione di LocalDB. Emulatore lo spazio di archiviazione si connette a SQL Server o LocalDB mediante l'autenticazione di Windows. 

Alcune differenze nelle funzionalità esistono tra i servizi di archiviazione Azure ed emulatore lo spazio di archiviazione. Per ulteriori informazioni su queste differenze, vedere [differenze tra l'emulatore lo spazio di archiviazione e l'archiviazione di Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>Richieste di autenticazione contro emulatore lo spazio di archiviazione

Come con Azure lo spazio di archiviazione nel cloud, ogni richiesta è eseguire emulatore lo spazio di archiviazione deve essere autenticato, a meno che non si tratta di una richiesta anonima. È possibile autenticare le richieste di contro emulatore lo spazio di archiviazione utilizza l'autenticazione chiave condivisa o con una firma di accesso condiviso (SA).

### <a name="authentication-with-shared-key-credentials"></a>Autenticazione con le credenziali di chiave condivisa

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Per ulteriori informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di Azure lo spazio di archiviazione](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Autenticazione con una firma di accesso condiviso 

Alcune librerie di client di archiviazione Azure, ad esempio la raccolta Xamarin supportano solo l'autenticazione con un token di firme (SA) accesso condiviso. È necessario creare il token SA mediante un'applicazione che supporta l'autenticazione chiave condivisa o strumento. Un modo semplice per generare il token di SA consiste nell'utilizzare Azure PowerShell:

1. Se non già stato fatto, installare Azure PowerShell. È consigliabile utilizzare la versione più recente dei cmdlet di PowerShell Azure. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md#Install) per le istruzioni di installazione.

2. Aprire Azure PowerShell ed eseguire i comandi seguenti. Ricordarsi di sostituire *ACCOUNT_NAME* e *ACCOUNT_KEY = =* con le proprie credenziali. Sostituire *CONTAINER_NAME* con un nome di propria scelta.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

La firma di accesso condiviso risultante URI per il nuovo contenitore dovrebbe essere simile al seguente:

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La firma di accesso condiviso creata con questo esempio è valida per un giorno. La firma concede l'accesso completo (ad esempio, lettura, scrittura, eliminazione, elenco) a BLOB all'interno del contenitore.

Per ulteriori informazioni sulle firme di accesso condiviso, vedere [Utilizzo condiviso accesso firme (SA)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Avviare e inizializzare l'emulatore lo spazio di archiviazione

Per avviare l'emulatore archiviazione Azure, selezionare il pulsante Start o premere il tasto Windows. Iniziare a digitare **Emulatore lo spazio di archiviazione di Azure**e selezionare l'emulatore dall'elenco delle applicazioni. 

Quando l'emulatore è in esecuzione, è visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows.

Quando si avvia l'emulatore lo spazio di archiviazione, verrà visualizzata una finestra della riga di comando. È possibile utilizzare questa finestra della riga di comando per avviare e arrestare l'emulatore lo spazio di archiviazione così come cancellare dati, ottenere lo stato corrente e inizializzare l'emulatore. Per ulteriori informazioni, vedere [Lo spazio di archiviazione emulatore della riga di comando degli strumenti riferimento](#storage-emulator-command-line-tool-reference).

Quando si chiude la finestra della riga di comando, emulatore archiviazione resta in esecuzione. Per visualizzare nuovamente la riga di comando, seguire le istruzioni indicate come se partire emulatore lo spazio di archiviazione.

Alla prima esecuzione di emulatore lo spazio di archiviazione dell'ambiente locale dello spazio di archiviazione è inizializzato automaticamente. Il processo di inizializzazione crea un database in LocalDB e riserva porte HTTP per ogni servizio di archiviazione locale. 

Emulatore lo spazio di archiviazione è installato per impostazione predefinita sulla c:\Programmi\Microsoft (x86) \Microsoft SDKs\Azure\Storage emulatore directory. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inizializzare emulatore lo spazio di archiviazione per l'utilizzo di un altro database SQL

È possibile utilizzare lo strumento della riga di comando di archiviazione emulatore inizializzare emulatore lo spazio di archiviazione in modo che puntino a un'istanza di database SQL LocalDB diversa da quella predefinita. Si noti che è necessario eseguire lo strumento della riga di comando con privilegi di amministratore per inizializzare il database back-end per l'emulatore lo spazio di archiviazione:

1. Fare clic sul pulsante **Start** oppure premere il tasto **Windows** . Iniziare a digitare `Azure Storage Emulator` e selezionarlo quando viene visualizzato per visualizzare lo strumento della riga di comando emulatore lo spazio di archiviazione.
2. Nella finestra del prompt dei comandi digitare il comando seguente, in cui `<SQLServerInstance>` è il nome dell'istanza di SQL Server. Per utilizzare LocalDb, specificare `(localdb)\v11.0` dell'istanza di SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    È anche possibile utilizzare il comando seguente, che indirizza l'emulatore per utilizzare l'istanza di SQL Server predefinito:

        AzureStorageEmulator init /server .\\ 

    In alternativa, è possibile utilizzare il comando seguente, che consente di inizializzare nuovamente il database all'istanza LocalDB predefinita:

        AzureStorageEmulator init /forceCreate 

Per ulteriori informazioni su questi comandi, vedere [Lo spazio di archiviazione emulatore della riga di comando degli strumenti riferimento](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Risorse indirizzamento nell'emulatore di spazio di archiviazione

Gli endpoint del servizio per l'emulatore lo spazio di archiviazione sono diversi da quelle di un account di archiviazione Azure. La differenza è che il computer locale non esegue la risoluzione dei nomi di dominio, in modo che i punti finali emulatore lo spazio di archiviazione richiedono un indirizzo locale piuttosto che un nome di dominio.

Quando si indirizza una risorsa in un account di archiviazione Azure, utilizzare il seguente schema, in cui il nome dell'account fa parte del nome host URI e la risorsa viene indirizzata fa parte del percorso URI:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Ad esempio, il seguente URI è un indirizzo valido per un blob in un account di archiviazione Azure:

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Nell'emulatore di spazio di archiviazione, poiché il computer locale non esegue la risoluzione dei nomi di dominio, il nome dell'account fa parte del percorso URI anziché il nome host. Utilizzare il seguente schema per una risorsa in esecuzione nell'emulatore lo spazio di archiviazione:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Ad esempio, il seguente indirizzo abituato per l'accesso a un blob di emulatore lo spazio di archiviazione:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Gli endpoint del servizio per l'emulatore lo spazio di archiviazione sono:

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Indirizzare account secondario con GRS RA

A partire da versione 3.1, l'account di emulatore lo spazio di archiviazione supporta la replica geografico ridondanti di accesso in lettura (RA GRS). Per le risorse di archiviazione nel cloud e nell'emulatore locale, è possibile accedere posizione secondaria aggiungendo - secondario al nome dell'account. Ad esempio, il seguente indirizzo abituato per l'accesso a un blob tramite secondario sola lettura nell'emulatore lo spazio di archiviazione:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Per l'accesso a quello secondario con emulatore lo spazio di archiviazione, utilizzare la libreria di Client lo spazio di archiviazione per .NET 3.2 o versioni successive. Vedere la [Libreria Client di Microsoft Azure lo spazio di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) per informazioni dettagliate.

## <a name="storage-emulator-command-line-tool-reference"></a>Guida di riferimento dello strumento della riga di comando emulatore lo spazio di archiviazione

A partire da versione 3.0, quando si avvia l'emulatore lo spazio di archiviazione, verrà visualizzata una finestra della riga di comando popup. Utilizzare la finestra della riga di comando per avviare e arrestare l'emulatore anche per eseguire una query per stato ed eseguire altre operazioni.

> [AZURE.NOTE] Se si dispone di Microsoft Azure calcolare emulatore installato, verrà visualizzata un'icona nella barra delle applicazioni quando si avvia l'emulatore lo spazio di archiviazione. Pulsante destro del mouse sull'icona per visualizzare un menu, che offre un modo grafico per avviare e arrestare l'emulatore lo spazio di archiviazione.

### <a name="command-line-syntax"></a>Nella sintassi della riga di comando

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Opzioni

Per visualizzare nell'elenco di opzioni, digitare `/help` al prompt dei comandi.

| Opzione | Descrizione                                                    | Comando                                                                                                 | Argomenti                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Inizio**  | Verrà avviata l'emulatore lo spazio di archiviazione.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-inprocess*: avviare l'emulatore del processo corrente anziché creare un nuovo processo.                          |
| **Interrompi**   | Arresta emulatore lo spazio di archiviazione.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **Stato** | Consente di stampare lo stato dell'emulatore lo spazio di archiviazione.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Cancella**  | Consente di cancellare i dati in tutti i servizi specificati nella riga di comando. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *BLOB*: Cancella blob di dati. <br/>*coda*: Cancella dati coda. <br/>*tabella*: Cancella dati della tabella. <br/>*tutti*: cancella tutti i dati in tutti i servizi. |
| **Inizializzazione**   | Esegue l'inizializzazione monouso per configurare l'emulatore.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-Nomeserver\nomeistanza server*: Specifica il server che ospita l'istanza SQL. <br/>*instanceName - sqlinstance*: Specifica il nome dell'istanza di SQL da utilizzare per l'istanza di server predefinito. <br/>*-forcecreate*: impone la creazione di database SQL, anche se è già presente. <br/>*-inprocess*: esegue l'inizializzazione nel processo corrente anziché la generazione di un nuovo processo. È necessario avviare il processo corrente con autorizzazioni elevate per eseguire l'inizializzazione.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Differenze tra l'emulatore lo spazio di archiviazione e l'archiviazione di Azure

Poiché l'emulatore lo spazio di archiviazione è un ambiente emulata in esecuzione in un'istanza locale di SQL, esistono alcune differenze di funzionalità tra l'emulatore e un account Azure dello spazio di archiviazione nel cloud:

- Emulatore lo spazio di archiviazione supporta un singolo account fisso e una chiave di autenticazione conosciuti.

- Emulatore lo spazio di archiviazione non è un servizio di archiviazione scalable e non supporta un numero elevato di client simultanee.

- Come descritto in [risorse di indirizzi in emulatore lo spazio di archiviazione](#addressing-resources-in-the-storage-emulator), le risorse sono trattate in modo diverso nelle emulatore lo spazio di archiviazione rispetto a un account di archiviazione Azure. Questa differenza è che la risoluzione dei nomi di dominio è disponibile nel cloud, ma non nel computer locale.

- A partire da versione 3.1, l'account di emulatore lo spazio di archiviazione supporta la replica geografico ridondanti di accesso in lettura (RA GRS). Nell'emulatore, tutti gli account hanno RA-GRS abilitato e non viene mai qualsiasi ritardo tra i principali e secondarie riproduzioni. Operazioni di ottenere Blob servizio Stat, ottenere coda servizio stat e ottenere statistiche servizio tabella sono supportate per l'account secondario e restituirà il valore della `LastSyncTime` elemento risposta come l'ora corrente in base al database SQL sottostante.

    Per l'accesso a quello secondario con emulatore lo spazio di archiviazione, utilizzare la libreria di Client lo spazio di archiviazione per .NET 3.2 o versioni successive. Vedere la [Libreria Client di Microsoft Azure lo spazio di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) per informazioni dettagliate.

- Il servizio di File e gli endpoint del servizio di protocollo piccole e medie imprese non sono attualmente supportati in emulatore lo spazio di archiviazione.

- Emulatore archiviazione restituisce un errore di VersionNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida) se si utilizza una versione di servizi di archiviazione che non è ancora supportata dalla versione di emulatore in uso.

### <a name="differences-for-blob-storage"></a>Differenze di archiviazione Blob 

Le differenze seguenti si applicano a archiviazione Blob nell'emulatore:

- Spazio di archiviazione emulatore solo supporta blob dimensioni fino a 2 GB.

- Un'operazione di inserire Blob potrebbe riuscire rispetto a un blob esistenti nell'emulatore di spazio di archiviazione e con un leasing attivo, anche se l'ID di leasing non è stata specificata come parte della richiesta. 

- Accodare i dati Blob operazioni non sono supportate dall'emulatore. Se si tenta di un'operazione su un blob Accodamento restituisce un errore di FeatureNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida).

### <a name="differences-for-table-storage"></a>Differenze per lo spazio di archiviazione tabella 

Le differenze seguenti si applicano all'archivio tabelle nell'emulatore:

- Proprietà relative alla data nel servizio tabella emulatore lo spazio di archiviazione supportano solo nell'intervallo supportato da SQL Server 2005 (*ad esempio*, sono necessari per entro il 1 ° gennaio 1753). Tutte le date precedenti al 1 ° gennaio 1753 vengono modificate a questo valore. La precisione delle date è limitata alla precisione di SQL Server 2005, vale a dire che le date siano precise a 1/300th di secondo.

- Emulatore lo spazio di archiviazione supporta valori di proprietà chiave di chiave e riga partizione meno di 512 byte ogni. Inoltre, le dimensioni totali del nome dell'account, nome tabella e i nomi delle proprietà chiave insieme non possono superare i 900 byte.

- Dimensione totale di una riga in una tabella in emulatore lo spazio di archiviazione è limitata a meno di 1 MB.

- Nell'emulatore di spazio di archiviazione, digitare le proprietà dei dati `Edm.Guid` o `Edm.Binary` supporta solo il `Equal (eq)` e `NotEqual (ne)` operatori di confronto in query filtrare stringhe.

### <a name="differences-for-queue-storage"></a>Differenze per lo spazio di archiviazione di coda

Non esistono differenze specifici per lo spazio di archiviazione coda nell'emulatore.

## <a name="storage-emulator-release-notes"></a>Note sulla versione di emulatore lo spazio di archiviazione

### <a name="version-45"></a>Versione 4.5

- Correzione di un problema che ha causato inizializzazione e l'installazione di emulatore lo spazio di archiviazione di non riuscire se il database di backup è stato rinominato.

### <a name="version-44"></a>Versione 4.4

- Emulatore lo spazio di archiviazione ora supporta versione 2015-12-11 di servizi di archiviazione in endpoint del servizio Blob, coda e una tabella.

- Simultanea dell'emulatore lo spazio di archiviazione dei dati blob diventa più efficiente quando si lavora con un numero elevato di BLOB.

- Correzione di un problema che ha causato contenitore ACL XML da convalidare in modo leggermente diverso da come indica il servizio di archiviazione.

- Correzione di un problema che talvolta causa max e min valori DateTime nel fuso orario non corretto.

### <a name="version-43"></a>Versione 4.3

- Emulatore lo spazio di archiviazione ora supporta versione 2015-07-08 dei servizi di archiviazione in endpoint del servizio Blob, coda e una tabella.

### <a name="version-42"></a>Versione 4.2

- Emulatore lo spazio di archiviazione supporta ora la versione 04/2015/05 servizi lo spazio di archiviazione su endpoint servizio Blob, coda e una tabella.

### <a name="version-41"></a>Versione 4.1

- Emulatore lo spazio di archiviazione supporta ora versione 2015-02-21 i servizi di archiviazione Blob, coda e una tabella endpoint del servizio, ad eccezione delle nuove caratteristiche di accodare Blob. 

- Emulatore lo spazio di archiviazione ora restituisce un messaggio di errore significativo se si utilizza una versione di servizi di archiviazione che non è ancora supportata da tale versione dell'emulatore. È consigliabile usare la versione più recente dell'emulatore. Se si verifica un errore di VersionNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida), scaricare la versione più recente dell'emulatore lo spazio di archiviazione.

- Un errore in cui un competizione condizione causati tabella entità dati fissa non essere corretti durante le operazioni di merge concorrenti.

### <a name="version-40"></a>Versione 4.0

- Emulatore archiviazione eseguibile è stato rinominato in *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versione 3.2

- Emulatore lo spazio di archiviazione supporta ora la versione 2014-02-14 dei servizi di archiviazione su endpoint servizio Blob, coda e una tabella. Si noti che gli endpoint del servizio di File non sono attualmente supportati in emulatore lo spazio di archiviazione. Per ulteriori informazioni sulla versione 2014-02-14, vedere [controllo delle versioni per i servizi di archiviazione Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) .

### <a name="version-31"></a>Versione 3.1

- Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS) è ora supportato nell'emulatore lo spazio di archiviazione. La Guida Blob servizio Stat, ottenere coda servizio stat e ottenere tabella servizio Stat API sono supportate per l'account secondario e restituirà il valore dell'elemento di risposta LastSyncTime come l'ora corrente in base al database SQL sottostante. Per l'accesso a quello secondario con emulatore lo spazio di archiviazione, utilizzare la libreria di Client lo spazio di archiviazione per .NET 3.2 o versioni successive. Per informazioni dettagliate, vedere la libreria di Client di Microsoft Azure lo spazio di archiviazione per riferimento .NET.

### <a name="version-30"></a>Versione 3.0

- Non è più disponibile emulatore archiviazione Azure nello stesso pacchetto emulatore di calcolo.

- L'interfaccia utente grafica emulatore di spazio di archiviazione è deprecata a favore un'interfaccia della riga di comando script. Per informazioni sull'interfaccia della riga di comando, vedere lo spazio di archiviazione emulatore della riga di comando degli strumenti riferimento. L'interfaccia grafica continueranno a essere presenti nella versione 3.0, ma è possibile accedervi solo durante l'installazione di emulatore calcolare destro del mouse sull'icona della barra delle applicazioni del sistema e selezionando Mostra lo spazio di archiviazione emulatore dell'interfaccia utente.

- Versione 2013-08-15 dei servizi di archiviazione Azure a questo punto è completamente supportata. (In precedenza questa versione è stata supportata solo per lo spazio di archiviazione Emulator versione 2.2.1 Preview.)
