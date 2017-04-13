<properties 
    pageTitle="Utilizzo di archiviazione Azure con una soluzione di integrazione continuo Jenkins | Microsoft Azure" 
    description="In questa esercitazione viene illustrato come utilizzare il servizio di blob Azure come funge da archivio per creare gli elementi creati da una soluzione di integrazione continuo Jenkins." 
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

# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Utilizzo dello spazio di archiviazione di Azure con una soluzione di integrazione continua Jenkins

## <a name="overview"></a>Panoramica

Le informazioni seguenti viene illustrato come utilizzare archiviazione Blob come un archivio di elementi di compilazione creato da una soluzione Jenkins continua integrazione (CI) o come origine dei file scaricabili per essere utilizzato in un processo di compilazione. Uno degli scenari in cui si otterrebbe questo utile è quando si attiva la codifica in un ambiente di sviluppo agile (con Java o altre lingue) e build in base alle integrazione continua è necessario un archivio di elementi di compilazione in modo che si potrebbe, ad esempio, condividerle con altri membri dell'organizzazione, i clienti o gestire un archivio. Un altro scenario è durante il processo di compilazione richiede altri file, ad esempio le dipendenze per il download come parte di compilazione di input.

In questa esercitazione si utilizzerà il plug-in Azure lo spazio di archiviazione per CI Jenkins resi disponibili da Microsoft.

## <a name="overview-of-jenkins"></a>Panoramica delle Jenkins ##

Jenkins consente l'integrazione continuo di un progetto software consentendo agli sviluppatori di integrare facilmente le proprie modifiche al codice e build hanno automaticamente e frequentemente, quindi aumenta la produttività degli sviluppatori di. Build viene creata una versione e gli elementi di compilazione possono essere caricati in vari repository. In questo argomento illustra come utilizzare archiviazione blob Azure come archivio degli elementi build. Verrà anche visualizzato come scaricare le dipendenze dallo spazio di archiviazione blob Azure.

Sono disponibili ulteriori informazioni su Jenkins in [Soddisfano Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Vantaggi dell'utilizzo del servizio di Blob ##

Vantaggi dell'utilizzo del servizio Blob per ospitare gli elementi di compilazione di sviluppo agile:

- Disponibilità della compilazione elementi e/o dipendenze scaricabili.
- Prestazioni quando la soluzione CI Jenkins carica il compilazione di elementi.
- Prestazioni durante il compilazione di elementi di download dei clienti e partner.
- Controllare i criteri di accesso utente, con una scelta tra accesso anonimo, in base a scadenza condivisa firma accesso privato access e così via.

## <a name="prerequisites"></a>Prerequisiti di ##

Le operazioni seguenti per utilizzare il servizio di Blob con la soluzione Jenkins CI sarà necessario:

- Una soluzione di integrazione continuo Jenkins.

    Se è attualmente non disponibile una soluzione di CI Jenkins, è possibile eseguire una soluzione che CI Jenkins utilizza la seguente tecnica:

    1. In un computer abilitato per Java, scaricare jenkins.war da <http://jenkins-ci.org>.
    2. Al prompt dei comandi che viene aperta la cartella che contiene jenkins.war, eseguire:

        `java -jar jenkins.war`

    3. Nel browser, aprire `http://localhost:8080/`. Verrà aperto il dashboard Jenkins, che verrà utilizzato per installare e configurare il plug-in archiviazione Azure.

        Durante una tipica soluzione CI Jenkins da configurare per l'esecuzione come servizio, esecuzione war Jenkins nella riga di comando sono sufficienti per questa esercitazione.

- Un account Azure. È possibile iscriversi a un account Azure presso <http://www.azure.com>.

- Un account di archiviazione Azure. Se si dispone già di un account di archiviazione, è possibile creare uno seguendo i passaggi al [Crea un Account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

- Avere familiarità con la soluzione CI Jenkins è consigliabile ma non è obbligatorio, come il contenuto seguente utilizzeranno un esempio di base per illustrare i passaggi necessari quando si utilizza il servizio Blob come un archivio per Jenkins CI creare elementi.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Come utilizzare il servizio Blob con Jenkins CI ##

Per utilizzare il servizio Blob con Jenkins, è necessario installare il plug-in archiviazione Azure, configurare il plug-in per usare l'account di archiviazione e quindi si crea un'azione post-compilazione che consente di caricare il compilazione di elementi al proprio account di archiviazione. La procedura seguente sono descritti nelle sezioni seguenti.

## <a name="how-to-install-the-azure-storage-plugin"></a>Come installare il plug-in archiviazione Azure ##

1. Nel dashboard Jenkins, fare clic su **Gestisci Jenkins**.
2. Nella pagina **Gestisci Jenkins** , fare clic su **Gestisci plug-in**.
3. Fare clic sulla scheda **disponibili** .
4. Nella sezione **Uploaders elemento** selezionare **plug-in di Microsoft Azure archiviazione**.
5. Fare clic su **installazione senza riavvio** o **Download e installazione dopo il riavvio**.
6. Riavviare Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Come configurare il plug-in archiviazione Azure per usare l'account di archiviazione ##

1. Nel dashboard Jenkins, fare clic su **Gestisci Jenkins**.
2. Nella pagina **Gestisci Jenkins** , fare clic su **Configurazione sistema**.
3. Nella sezione **Configurazione dell'Account di Microsoft Azure lo spazio di archiviazione** :
    1. Immettere il nome dell'account di archiviazione, che è possibile ottenere dal [Portale di Azure](https://portal.azure.com).
    2. Immettere la chiave account lo spazio di archiviazione, anche ottenuta dal [Portale di Azure](https://portal.azure.com).
    3. Usare il valore predefinito per **URL Endpoint servizio Blob** se si utilizza il cloud Azure pubblico. Se si usa un'area di Azure diversa, utilizzare il punto finale come specificato nel [Portale di Azure](https://portal.azure.com) per l'account di archiviazione. 
    4. Fare clic su **credenziali di spazio di archiviazione di convalida** per convalidare l'account di archiviazione. 
    5. [Facoltativo] Se si dispone di spazio di archiviazione aggiuntivo account che si desidera resi disponibili per il CI Jenkins, fare clic su **aggiungere altri account di archiviazione**.
    6. Fare clic su **Salva** per salvare le impostazioni.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Come creare un'azione post-compilazione che carica gli elementi build al proprio account di archiviazione ##

Per motivi di istruzione, prima di tutto è necessario creare un processo che creare più file, quindi aggiungere nell'azione post-compilazione per caricare i file al proprio account di archiviazione.

1. Nel dashboard Jenkins, fare clic su **Nuovo elemento**.
2. Assegnare un nome processo **MyJob**, fare clic su **Genera un progetto di software gratuiti stile**e fare clic su **OK**.
3. Nella sezione **creare** della configurazione del processo, fare clic su **Aggiungi compilazione passaggio** e scegliere **comando batch eseguire Windows**.
4. **Comando**, utilizzare i comandi seguenti:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Nella sezione **Azioni post-compilazione** della configurazione del processo, fare clic su **Aggiungi azione post-compilazione** e scegliere **Carica gli elementi da archiviazione Blob Azure**.
6. Per **nome dell'account di archiviazione**, selezionare l'account di archiviazione da utilizzare.
7. Per **nome del contenitore**, specificare il nome del contenitore. (Verrà creato il contenitore in già contrario quando vengono caricati gli elementi di compilazione.) È possibile utilizzare le variabili di ambiente, perché questo esempio immetterlo **${JOB_NAME}** come nome del contenitore.

    **Suggerimento**
    
    Sotto il **comando** sezione in cui è stata immessa uno script per **eseguire Windows batch comando** è un collegamento alle variabili di ambiente riconosciuto da Jenkins. Fare clic su tale collegamento per scoprire i nomi delle variabili e le descrizioni. Si noti che le variabili di ambiente contenenti caratteri speciali, ad esempio la variabile di ambiente **BUILD_URL** non sono consentite come nome del contenitore o percorso virtuale comune.

8. Fare clic su **Crea nuovo contenitore pubblico per impostazione predefinita** per questo esempio. (Se si desidera utilizzare un contenitore privato, è necessario creare una firma di accesso condiviso per consentire l'accesso. Questo è lo scopo di questo argomento. Per ulteriori informazioni sulle firme di accesso condiviso in [Uso condiviso accesso firme (SA)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Facoltativo] Fare clic su **Pulisci contenitore prima di caricare** se si desidera che il contenitore la cancellazione del contenuto prima di creare elementi vengono caricati (lasciarla deselezionata se non si desidera eliminare il contenuto del contenitore).
10. Per un **Elenco di elementi da caricare**, immettere * *testo /*. txt**.
11. Per un **percorso virtuale comune per gli elementi caricati**, ai fini di questa esercitazione, immettere **${BUILD\_ID} / ${BUILD\_numero}**.
12. Fare clic su **Salva** per salvare le impostazioni.
13. Nel dashboard Jenkins, fare clic su **Creare ora** per eseguire **MyJob**. Esaminare l'output di console relativi allo stato. Messaggi di stato per lo spazio di archiviazione Azure verranno inclusi nell'output della console quando l'azione post-compilazione inizia a caricare gli elementi di compilazione.
14. Al termine del processo, è possibile esaminare gli elementi di compilazione aprendo il blob pubblica.
    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Fare clic su **archiviazione**.
    3. Fare clic sul nome di account di archiviazione che è stato usato per Jenkins.
    4. Fare clic su **contenitori**.
    5. Fare clic sul contenitore denominato **myjob**, la versione in minuscolo del nome del processo che è stata assegnata al momento della creazione del processo Jenkins. Nomi di contenitore e blob sono minuscoli (maiuscole e minuscole) di spazio di archiviazione Azure. Nell'elenco di BLOB per il contenitore denominato **myjob** risulta **hello.txt** e **date.txt**. Copiare l'URL di uno di questi elementi e aprirlo nel browser. File di testo che è stata caricata verrà visualizzato come elemento genera.

È possibile creare solo un'azione post-compilazione che consente di caricare gli elementi a archiviazione blob Azure per processo. Si noti che l'azione post-compilazione singola per caricare gli elementi a archiviazione blob Azure è possibile specificare diversi file (inclusi i caratteri jolly) e percorsi dei file in utilizzando un punto e virgola come separatore di **Elenco di elementi da caricare** . Se, ad esempio, viene prodotta la compilazione di Jenkins VASO file e TXT nella cartella di **creazione** dell'area di lavoro e si desidera caricare entrambe a archiviazione blob Azure, utilizzare le operazioni seguenti per il valore di **Elenco di elementi da caricare** : **build /\*JAR; build /\*. txt**. È anche possibile utilizzare i due punti doppi sintassi per specificare un percorso da utilizzare all'interno del nome blob. Ad esempio, se si vuole vasetti da ottenere caricata l'utilizzo di **file binari** nel percorso blob e i file TXT per ottenere caricato utilizzando **le notifiche** nel percorso blob, utilizzare le operazioni seguenti per il valore di **Elenco di elementi da caricare** : **creare /\*. jar::binaries; genera /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Come creare un'istruzione di compilazione che scarica dallo spazio di archiviazione blob Azure ##

La procedura seguente viene illustrato come configurare un passaggio di compilazione per scaricare gli elementi da archiviazione blob Azure. Questo potrebbe essere utile se si desidera includere gli elementi nella build, ad esempio JAR tenere in Azure nell'archiviazione blob.

1. Nella sezione **creare** della configurazione del processo, fare clic su **Aggiungi compilazione passaggio** e scegliere **Scarica dallo spazio di archiviazione Blob Azure**.
2. Per **nome dell'account di archiviazione**, selezionare l'account di archiviazione da utilizzare.
3. Per **Nome contenitore**, specificare il nome del contenitore che contiene i BLOB da scaricare. È possibile utilizzare le variabili di ambiente.
4. Per **nome Blob**, specificare il nome blob. È possibile utilizzare le variabili di ambiente. Inoltre, è possibile utilizzare un asterisco come carattere jolly dopo avere specificato le lettere iniziali del nome del blob. Ad esempio **project\* ** specificare tutti i BLOB i cui nomi iniziano con **project**.
5. [Facoltativo] Per **scaricare percorso**, specificare il percorso del computer Jenkins nel punto in cui si desidera scaricare i file dallo spazio di archiviazione blob Azure. Le variabili di ambiente possono anche essere utilizzate. (Se non si specifica un valore per **il Download percorso**, i file dallo spazio di archiviazione blob Azure verranno scaricati dell'area di lavoro del processo.)

Se si dispone di altri elementi che si desidera scaricare dallo spazio di archiviazione blob Azure, è possibile creare ulteriori passaggi.

Dopo aver eseguito una compilazione, è possibile verificare l'output di compilazione cronologia o osservare il percorso di download, per verificare se blob che ci si aspettava sono stati scaricati correttamente.  

## <a name="components-used-by-the-blob-service"></a>Componenti utilizzati dal servizio Blob ##

Di seguito viene fornita una panoramica dei componenti di servizio Blob.

- **Account di archiviazione**: tutti gli accessi allo spazio di archiviazione di Azure viene eseguito tramite un account di archiviazione. Si tratta del livello più alto dello spazio dei nomi per l'accesso a BLOB. Un account può contenere un numero illimitato di contenitori, come le dimensioni totali sono in 100TB.
- **Contenitore**: un contenitore fornisce un raggruppamento di un insieme di BLOB. Tutti i BLOB devono essere in un contenitore. Un account può contenere un numero illimitato di contenitori. Un contenitore consentono di memorizzare un numero illimitato di BLOB.
- **BLOB**: un file di qualsiasi tipo e dimensioni. Esistono due tipi di blob che possono essere archiviati in archiviazione Azure: BLOB di pagina e di blocco. La maggior parte dei file sono BLOB di blocco. Un blob singolo blocco può essere dimensioni fino a 200GB. In questa esercitazione utilizza BLOB di blocco. BLOB di pagine, un altro tipo di blob, può essere fino a 1TB, le dimensioni e sono più efficiente quando gli intervalli di byte in un file vengono modificati frequentemente. Per ulteriori informazioni sui blob, vedere [Understanding blocco BLOB, accodare BLOB e BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Formato URL**: BLOB sono indirizzati tramite il seguente formato URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (Formato precedente si applica a di Azure cloud. Se si usa un'area di Azure diversa, utilizzare il punto finale all'interno del [Portale di Azure](https://portal.azure.com) per determinare l'endpoint dell'URL.)

    Nel formato di riportati sopra, `storageaccount` rappresenta il nome del proprio account di archiviazione, `container_name` rappresenta il nome del contenitore di e `blob_name` rappresenta il nome del blob, rispettivamente. All'interno del nome di contenitore, è possibile creare più percorsi, separati da una barra, **/**. Il nome del contenitore di esempio in questa esercitazione è stata **MyJob**e **${creare\_ID} / ${creare\_numero}** utilizzato per il percorso virtuale comune, risultante BLOB con un URL nel formato seguente:

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Passaggi successivi

- [Soddisfano Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
- [Spazio di archiviazione di Azure SDK per Java](https://github.com/azure/azure-storage-java)
- [Spazio di archiviazione Azure Client SDK riferimento](http://dl.windowsazure.com/storage/javadoc/)
- [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di linguaggio](https://azure.microsoft.com/develop/java/).