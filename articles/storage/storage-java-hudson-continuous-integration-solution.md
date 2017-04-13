<properties
    pageTitle="Come utilizzare Hudson con archiviazione Blob | Microsoft Azure"
    description="In questo articolo viene descritto come utilizzare Hudson a archiviazione Blob Azure come un archivio per gli elementi di compilazione."
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Con una soluzione di integrazione continua Hudson lo spazio di archiviazione di Azure

## <a name="overview"></a>Panoramica

Le informazioni seguenti viene illustrato come utilizzare archiviazione Blob come un archivio di elementi di compilazione creato da una soluzione Hudson continua integrazione (CI) o come origine dei file scaricabili per essere utilizzato in un processo di compilazione. Uno degli scenari in cui si otterrebbe questo utile è quando si attiva la codifica in un ambiente di sviluppo agile (con Java o altre lingue) e build in base alle integrazione continua è necessario un archivio di elementi di compilazione in modo che si potrebbe, ad esempio, condividerle con altri membri dell'organizzazione, i clienti o gestire un archivio.  Un altro scenario è durante il processo di compilazione richiede altri file, ad esempio le dipendenze per il download come parte di compilazione di input.

In questa esercitazione si utilizzerà il plug-in archiviazione Azure per CI Hudson resi disponibili da Microsoft.

## <a name="introduction-to-hudson"></a>Introduzione a Hudson ##

Hudson consente l'integrazione continua di un progetto software consentendo agli sviluppatori di integrare facilmente le proprie modifiche al codice e build hanno automaticamente e frequentemente, quindi aumenta la produttività degli sviluppatori. Build viene creata una versione e gli elementi di compilazione possono essere caricati in vari repository. Questo articolo viene illustrato come utilizzare archiviazione Blob Azure come archivio di elementi genera. Verrà anche visualizzato come scaricare dipendenze dallo spazio di archiviazione Blob Azure.

Sono disponibili ulteriori informazioni su Hudson in [Soddisfano Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Vantaggi dell'utilizzo del servizio di Blob ##

Vantaggi dell'utilizzo del servizio Blob per ospitare gli elementi di compilazione di sviluppo agile:

- Disponibilità della compilazione elementi e/o dipendenze scaricabili.
- Prestazioni quando la soluzione CI Hudson carica il compilazione di elementi.
- Prestazioni durante il compilazione di elementi di download dei clienti e partner.
- Controllare i criteri di accesso utente, con una scelta tra accesso anonimo, in base a scadenza condivisa firma accesso privato access e così via.

## <a name="prerequisites"></a>Prerequisiti di ##

Le operazioni seguenti per utilizzare il servizio Blob con la soluzione Hudson CI sarà necessario:

- Una soluzione di integrazione continua Hudson.

    Se è attualmente non disponibile una soluzione Hudson CI, è possibile eseguire una soluzione CI Hudson tecnica seguenti:

    1. In un computer abilitato per Java scaricare WAR Hudson da <http://hudson-ci.org/>.
    2. Al prompt dei comandi che viene aperta la cartella che contiene WAR Hudson, eseguire WAR Hudson. Ad esempio, se è stato scaricato versione 3.1.2:

        `java -jar hudson-3.1.2.war`

    3. Nel browser, aprire `http://localhost:8080/`. Verrà aperto il dashboard Hudson.

    4. Al primo utilizzo di Hudson, completare la configurazione iniziale presso `http://localhost:8080/`.

    5. Dopo aver completare la configurazione iniziale, annullare l'istanza di il Hudson in esecuzione, ricominciare WAR Hudson e riaprire il dashboard Hudson `http://localhost:8080/`, che verrà utilizzato per installare e configurare il plug-in archiviazione Azure.

        Durante una tipica soluzione CI Hudson da configurare per l'esecuzione come servizio, esecuzione war Hudson nella riga di comando sono sufficienti per questa esercitazione.

- Un account Azure. È possibile iscriversi a un account Azure presso <http://www.azure.com>.

- Un account di archiviazione Azure. Se si dispone già di un account di archiviazione, è possibile creare uno seguendo i passaggi al [Crea un Account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

- Avere familiarità con la soluzione CI Hudson è consigliabile ma non è obbligatorio, come il seguente contenuto utilizzeranno un esempio di base per illustrare i passaggi necessari quando si utilizza il servizio Blob come un archivio per Hudson CI creare elementi.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Come utilizzare il servizio Blob con Hudson CI ##

Per utilizzare il servizio Blob con Hudson, è necessario installare il plug-in archiviazione Azure, configurare il plug-in per usare l'account di archiviazione e quindi si crea un'azione post-compilazione che consente di caricare il compilazione di elementi al proprio account di archiviazione. La procedura seguente sono descritti nelle sezioni seguenti.

## <a name="how-to-install-the-azure-storage-plugin"></a>Come installare il plug-in archiviazione Azure ##

1. Nel dashboard Hudson, fare clic su **Gestisci Hudson**.
2. Nella pagina **Gestisci Hudson** fare clic su **Gestisci plug-in**.
3. Fare clic sulla scheda **disponibili** .
4. Fare clic su **altri utenti**.
5. Nella sezione **Uploaders elemento** selezionare **plug-in di Microsoft Azure archiviazione**.
6. Fare clic su **Installa**.
7. Al termine dell'installazione, riavviare Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Come configurare il plug-in archiviazione Azure per usare l'account di archiviazione ##

1. Nel dashboard Hudson, fare clic su **Gestisci Hudson**.
2. Nella pagina **Gestisci Hudson** fare clic su **Configurazione sistema**.
3. Nella sezione **Configurazione dell'Account di Microsoft Azure lo spazio di archiviazione** :

    un. Immettere il nome dell'account di archiviazione, che è possibile ottenere dal [Portale di Azure](https://portal.azure.com).

    b. Immettere la chiave account lo spazio di archiviazione, anche ottenuta dal [Portale di Azure](https://portal.azure.com).

    c. Usare il valore predefinito per **URL Endpoint servizio Blob** se si utilizza il cloud Azure pubblico. Se si usa un'area di Azure diversa, utilizzare il punto finale come specificato nel [Portale di Azure](https://portal.azure.com) per l'account di archiviazione.

    d. Fare clic su **credenziali di spazio di archiviazione di convalida** per convalidare l'account di archiviazione.

    e. [Facoltativo] Se si dispone di spazio di archiviazione aggiuntivo account che si desidera resi disponibili per il CI Hudson, fare clic su **aggiungere altri account di archiviazione**.

    f. Fare clic su **Salva** per salvare le impostazioni.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Come creare un'azione post-compilazione che carica gli elementi build al proprio account di archiviazione ##

Per motivi di istruzione, prima di tutto è necessario creare un processo che creare più file, quindi aggiungere nell'azione post-compilazione per caricare i file al proprio account di archiviazione.

1. Nel dashboard Hudson, fare clic su **Nuovo processo**.
2. Assegnare un nome processo **MyJob**, fare clic su **Genera un processo software gratuiti stile**e quindi fare clic su **OK**.
3. Nella sezione **creare** della configurazione del processo, fare clic su **Aggiungi compilazione passaggio** e scegliere **comando batch eseguire Windows**.
4. **Comando**, utilizzare i comandi seguenti:

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. Nella sezione **Azioni post-compilazione** della configurazione del processo, fare clic su **Carica gli elementi da archiviazione Blob di Microsoft Azure**.
6. Per **Nome dell'Account di archiviazione**, selezionare l'account di archiviazione da utilizzare.
7. Per **Nome del contenitore**, specificare il nome del contenitore. (Verrà creato il contenitore in già contrario quando vengono caricati gli elementi di compilazione.) È possibile utilizzare le variabili di ambiente, perché questo esempio immetterlo **${JOB_NAME}** come nome del contenitore.

    **Suggerimento**

    Sotto il **comando** sezione in cui è stata immessa uno script per **eseguire Windows batch comando** è un collegamento alle variabili di ambiente riconosciuto da Hudson. Fare clic su tale collegamento per scoprire i nomi delle variabili e le descrizioni. Si noti che le variabili di ambiente contenenti caratteri speciali, ad esempio la variabile di ambiente **BUILD_URL** non sono consentite come nome del contenitore o percorso virtuale comune.

8. Fare clic su **Crea nuovo contenitore pubblico per impostazione predefinita** per questo esempio. (Se si desidera utilizzare un contenitore privato, è necessario creare una firma di accesso condiviso per consentire l'accesso. Questo è lo scopo di questo articolo. Per ulteriori informazioni sulle firme di accesso condiviso in [Uso condiviso accesso firme (SA)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Facoltativo] Fare clic su **Pulisci contenitore prima di caricare** se si desidera che il contenitore la cancellazione del contenuto prima di creare elementi vengono caricati (lasciarla deselezionata se non si desidera eliminare il contenuto del contenitore).
10. Per un **Elenco di elementi da caricare**, immettere * *testo /*. txt**.
11. Per un **percorso virtuale comune per gli elementi caricati**, immettere **${creare\_ID} / ${creare\_numero}**.
12. Fare clic su **Salva** per salvare le impostazioni.
13. Nel dashboard Hudson fare clic su **Creare ora** per eseguire **MyJob**. Esaminare l'output di console relativi allo stato. Messaggi di stato per l'archiviazione di Azure verranno inclusi nell'output della console quando l'operazione di post-compilazione inizia a caricare gli elementi di compilazione.
14. Al termine del processo, è possibile esaminare gli elementi di compilazione aprendo il blob pubblica.

    un. Accedere al [portale di Azure](https://portal.azure.com).

    b. Fare clic su **archiviazione**.

    c. Fare clic sul nome di account di archiviazione che è stato usato per Hudson.

    d. Fare clic su **contenitori**.

    e. Fare clic sul contenitore denominato **myjob**, la versione in minuscolo del nome del processo che è stata assegnata al momento della creazione del processo Hudson. Nomi di contenitore e blob sono minuscoli (maiuscole e minuscole) in archiviazione Azure. Nell'elenco di BLOB per il contenitore denominato **myjob** risulta **hello.txt** e **date.txt**. Copiare l'URL di uno di questi elementi e aprirlo nel browser. File di testo che è stata caricata verrà visualizzato come elemento genera.

È possibile creare solo un'azione post-compilazione che consente di caricare gli elementi a archiviazione Blob Azure per processo. Si noti che l'azione post-compilazione singola per caricare gli elementi in archiviazione Blob Azure è possibile specificare diversi file (inclusi i caratteri jolly) e percorsi dei file in utilizzando un punto e virgola come separatore di **Elenco di elementi da caricare** . Se, ad esempio, viene prodotta la compilazione Hudson VASO file e TXT nella cartella di **compilazione** dell'area di lavoro e si desidera caricare entrambe a archiviazione Blob Azure, utilizzare le operazioni seguenti per il valore di **Elenco di elementi da caricare** : **creare /\*JAR; generatori /\*. txt**. È anche possibile utilizzare i due punti doppi sintassi per specificare un percorso da utilizzare all'interno del nome blob. Ad esempio, se si vuole vasetti da ottenere caricata l'utilizzo di **file binari** nel percorso blob e i file TXT per ottenere caricato utilizzando **le notifiche** nel percorso blob, utilizzare le operazioni seguenti per il valore di **Elenco di elementi da caricare** : **creare /\*. jar::binaries; genera /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Come creare un'istruzione di compilazione che scarica dallo spazio di archiviazione Blob Azure ##

La procedura seguente viene illustrato come configurare un passaggio di compilazione per scaricare gli elementi dallo spazio di archiviazione Blob Azure. Questo sarebbe utile se si desidera includere gli elementi nella build di, ad esempio JAR mantenere nell'archiviazione Blob Azure.

1. Nella sezione **creare** della configurazione del processo, fare clic su **Aggiungi compilazione passaggio** e scegliere **Scarica dallo spazio di archiviazione Blob Azure**.
2. Per **nome dell'account di archiviazione**, selezionare l'account di archiviazione da utilizzare.
3. Per **Nome contenitore**, specificare il nome del contenitore che contiene i BLOB da scaricare. È possibile utilizzare le variabili di ambiente.
4. Per **nome Blob**, specificare il nome blob. È possibile utilizzare le variabili di ambiente. Inoltre, è possibile utilizzare un asterisco come carattere jolly dopo avere specificato le lettere iniziali del nome del blob. Ad esempio **project\* ** specificare tutti i BLOB i cui nomi iniziano con **project**.
5. [Facoltativo] Per **scaricare percorso**, specificare il percorso del computer Hudson nel punto in cui si desidera scaricare i file dallo spazio di archiviazione Blob Azure. Le variabili di ambiente possono anche essere utilizzate. (Se non si specifica un valore per **il Download percorso**, i file dallo spazio di archiviazione Blob Azure verranno scaricati dell'area di lavoro del processo.)

Se si dispone di altri elementi che si desidera scaricare dallo spazio di archiviazione Blob Azure, è possibile creare ulteriori passaggi.

Dopo aver eseguito una compilazione, è possibile verificare l'output di compilazione cronologia o osservare il percorso di download, per verificare se blob che ci si aspettava sono stati scaricati correttamente.

## <a name="components-used-by-the-blob-service"></a>Componenti utilizzati dal servizio Blob ##

Di seguito viene fornita una panoramica dei componenti di servizio Blob.

- **Account di archiviazione**: tutti gli accessi allo spazio di archiviazione di Azure viene eseguito tramite un account di archiviazione. Si tratta del livello più alto dello spazio dei nomi per l'accesso a BLOB. Un account può contenere un numero illimitato di contenitori, come le dimensioni totali sono in 100 TB.
- **Contenitore**: un contenitore fornisce un raggruppamento di un insieme di BLOB. Tutti i BLOB devono essere in un contenitore. Un account può contenere un numero illimitato di contenitori. Un contenitore consentono di memorizzare un numero illimitato di BLOB.
- **BLOB**: un file di qualsiasi tipo e dimensioni. Esistono due tipi di blob che possono essere archiviati in archiviazione Azure: BLOB di pagina e di blocco. La maggior parte dei file sono BLOB di blocco. Un blob singolo blocco può essere dimensioni fino a 200 GB. In questa esercitazione utilizza BLOB di blocco. BLOB di pagine, un altro tipo di blob, può essere fino a 1 TB, le dimensioni e sono più efficiente quando gli intervalli di byte in un file vengono modificati frequentemente. Per ulteriori informazioni sui blob, vedere [Understanding blocco BLOB, accodare BLOB e BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Formato URL**: BLOB sono indirizzati tramite il seguente formato URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (Formato precedente si applica a di Azure cloud. Se si usa un'area di Azure diversa, utilizzare il punto finale all'interno del [Portale di Azure](https://portal.azure.com) per determinare l'endpoint dell'URL.)

    Nel formato di riportati sopra, `storageaccount` rappresenta il nome del proprio account di archiviazione, `container_name` rappresenta il nome del contenitore di e `blob_name` rappresenta il nome del blob, rispettivamente. All'interno del nome di contenitore, è possibile creare più percorsi, separati da una barra, **/**. Il nome del contenitore di esempio in questa esercitazione è stata **MyJob**e **${creare\_ID} / ${creare\_numero}** utilizzato per il percorso virtuale comune, risultante BLOB con un URL nel formato seguente:

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passaggi successivi

- [Soddisfano Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [Spazio di archiviazione di Azure SDK per Java](https://github.com/azure/azure-storage-java)
- [Spazio di archiviazione Azure Client SDK riferimento](http://dl.windowsazure.com/storage/javadoc/)
- [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di linguaggio](https://azure.microsoft.com/develop/java/).